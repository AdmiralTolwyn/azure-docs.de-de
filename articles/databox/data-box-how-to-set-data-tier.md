---
title: Senden von Daten an eine Blockblob-Ebene (heiß, kalt oder Archiv) mit Azure Data Box | Microsoft-Dokumentation in Daten
description: Hier wird beschrieben, wie Daten mit Azure Data Box an eine geeignete Blockblob-Speicherebene wie die heiße oder kalte Ebene bzw. die Archivebene gesendet werden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/10/2019
ms.author: alkohli
ms.openlocfilehash: bb1d6c5bd51fcfe35127c2f6d8dd6a80b727c45f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517146"
---
# <a name="use-azure-data-box-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Senden von Daten an eine geeignete Azure Storage-Blobebene mit Azure Data Box

Azure Data Box verschiebt große Datenmengen in Azure, indem Ihnen ein proprietäres Speichergerät zugeschickt wird. Sie füllen das Gerät mit Daten und senden es zurück. Die Daten aus Data Box werden in eine Standardebene hochgeladen, die dem Speicherkonto zugeordnet ist. Sie können die Daten dann in eine andere Speicherebene verschieben.

In diesem Artikel wird beschrieben, wie die von Data Box hochgeladenen Daten in eine heiße, eine kalte oder eine Archivblobebene verschoben werden können.  

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Auswählen der richtigen Speicherebene für Ihre Daten

Azure Storage ermöglicht das Speichern von Daten auf möglichst kostengünstige Art und Weise in drei verschiedenen Ebenen: der heißen Ebene, der kalten Ebene oder der Archivebene. Die heiße Speicherebene ist für die Speicherung von Daten optimiert, auf die häufig zugegriffen wird. Für Speicher der heißen Ebene fallen höhere Speicherkosten als für Speicher der kalten und der Archivebene an, aber gleichzeitig auch die niedrigsten Zugriffskosten.

Die kalte Speicherebene ist für selten verwendete Daten vorgesehen, die mindestens 30 Tage lang gespeichert werden müssen. Die Speicherkosten für die kalte Ebene sind geringer als für die heiße Speicherebene, die Datenzugriffsgebühren sind verglichen mit der heißen Ebene jedoch höher.

Die Azure-Archivebene ist offline und ermöglicht die niedrigsten Speicherkosten, ist gleichzeitig aber mit den höchsten Zugriffskosten verbunden. Diese Ebene ist für Daten vorgesehen, die mindestens 180 Tage im Archivspeicher bleiben. Details zu den einzelnen Ebenen und zum Preismodell finden Sie unter [Vergleich der Speicherebenen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).

Die Daten aus Data Box werden in eine Speicherebene hochgeladen, die dem Speicherkonto zugeordnet ist. Beim Erstellen eines Speicherkontos können Sie die heiße oder die kalte Zugriffsebene festlegen. Je nach dem Zugriffsmuster Ihrer Workload und den Kosten können Sie diese Daten aus der Standardebene in eine andere Speicherebene verschieben.

Sie können das Tiering für Ihre Objektspeicherdaten nur in Blob Storage-Konten oder GPv2-Konten (universelle Konten der Version 2) durchführen. Für GPv1-Konten (General Purpose v1) wird das Tiering nicht unterstützt. Zur Auswahl der richtigen Speicherebene für Ihre Daten überprüfen Sie die ausführlichen Überlegungen in [Azure Blob Storage: Speicherebenen „Premium“ (Vorschauversion), „Heiß“, „Kalt“ und „Archiv“](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).

## <a name="set-a-default-blob-tier"></a>Festlegen einer Standardblobebene

Die Standardblobebene wird angegeben, wenn das Speicherkonto im Azure-Portal erstellt wird. Sobald „GPv2“ oder „Blobspeicher“ als Speichertyp ausgewählt wurde, kann das Zugriffsebenenattribut festgelegt werden. Die heiße Ebene ist standardmäßig ausgewählt.

Die Ebenen können nicht festgelegt werden, wenn Sie beim Bestellen einer Data Box versuchen, ein neues Konto zu erstellen. Nachdem das Konto erstellt wurde, können Sie das Konto im Portal ändern, um die standardmäßige Zugriffsebene festzulegen.

Alternativ erstellen Sie zuerst ein Speicherkonto mit dem festgelegten Zugriffsebenenattribut. Wählen Sie beim Erstellen der Data Box-Bestellung das vorhandene Speicherkonto aus. Weitere Informationen zum Festlegen der Standardblobebene während der Erstellung des Speicherkontos finden Sie unter [Erstellen eines Speicherkontos: Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Verschieben von Daten in eine Nicht-Standardebene

Sobald die Daten aus Data Box in die Standardebene hochgeladen sind, können Sie sie in eine Nicht-Standardebene verschieben. Zum Verschieben dieser Daten in eine Nicht-Standardebene gibt es zwei Möglichkeiten.

- **Azure Blob Storage-Lebenszyklusverwaltung**: Sie können einen richtlinienbasierten Ansatz verwenden, um Daten automatisch in die entsprechenden Zugriffsebenen zu übertragen oder am Ende ihres Lebenszyklus ablaufen zu lassen. Weitere Informationen finden Sie unter [Verwalten des Azure Blob Storage-Lebenszyklus](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts).
- **Skripterstellung**: Sie können einen skriptbasierten Ansatz über Azure PowerShell verwenden, um Blobebenentiering zu aktivieren. Dazu rufen Sie den `SetBlobTier`-Vorgang auf, um die Ebene für das Blob festzulegen.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Festlegen der Blobebene mithilfe von Azure PowerShell

Nachfolgend wird beschrieben, wie Sie die Blobebene mithilfe eines Azure PowerShell-Skripts auf „Archiv“ festlegen können.

1. Öffnen Sie eine Windows PowerShell-Sitzung mit erhöhten Rechten. Stellen Sie sicher, dass Sie PowerShell 5.0 oder höher ausführen. Geben Sie Folgendes ein: 

   `$PSVersionTable.PSVersion`     

2. Melden Sie sich bei Azure PowerShell an. 

   `Login-AzureRmAccount`  

3. Definieren Sie die Variablen für das Speicherkonto, den Zugriffsschlüssel, den Container und den Speicherkontext.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Rufen Sie die Blobs im Container ab.

    `$blobs = Get-AzureStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Legen Sie die Ebene für alle Blobs im Container auf „Archiv“ fest.

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    Nachfolgend sehen Sie eine Beispielausgabe:

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzureRmAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzureStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > Wenn die Daten bei der Erfassung archiviert werden sollen, legen Sie die heiße Ebene als Standard für das Konto fest. Wenn die Standardebene „Kalt “ ist, fällt eine Sondergebühr für frühzeitiges Löschen (30 Tage) an, wenn die Daten sofort in die Archivebene verschoben werden.

## <a name="next-steps"></a>Nächste Schritte

-  Erfahren Sie mehr zur Behandlung der [gängigen Datentiering-Szenarien mithilfe von Regeln zur Lebenszyklusverwaltung](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts#examples).

