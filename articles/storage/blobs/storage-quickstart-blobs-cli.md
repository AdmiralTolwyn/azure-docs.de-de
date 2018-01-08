---
title: "Azure-Schnellstart – Übertragen von Objekten nach/aus Azure Blob Storage mit der Azure-Befehlszeilenschnittstelle | Microsoft-Dokumentation"
description: "Hier lernen Sie schnell, wie Sie mit der Azure-Befehlszeilenschnittstelle Objekte nach/aus Azure Blob Storage übertragen."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/19/2017
ms.author: tamram
ms.openlocfilehash: 7313df35baadf7aa6d476f44b113dc60e6845f4b
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/18/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-the-azure-cli"></a>Übertragen von Objekten nach/aus Azure Blob Storage mit der Azure-Befehlszeilenschnittstelle

Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. In diesem Schnellstart wird erläutert, wie Sie mit der Azure-Befehlszeilenschnittstelle Daten in Azure Blob Storage hochladen und daraus herunterladen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Schnellstart die Azure CLI-Version 2.0.4 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0](/cli/azure/install-azure-cli) Informationen dazu.

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Erstellen eines Containers

Blobs werden immer in einen Container hochgeladen. Sie können Gruppen von Blobs ähnlich wie Dateien in Ordnern auf Ihrem Computer organisieren.

Erstellen Sie mit dem Befehl [az storage container create](/cli/azure/storage/container#create) einen Container zum Speichern von Blobs.

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Hochladen eines Blobs

Blobspeicher unterstützt Block-, Anfüge- und Seitenblobs. Die meisten Dateien, die in Blob Storage gespeichert werden, werden als Blockblobs gespeichert. Anfügeblobs werden verwendet, wenn Daten einem vorhandenen Blob hinzugefügt werden müssen, ohne den vorhandenen Inhalt zu ändern, z.B. bei der Protokollierung. Seitenblobs sind die Grundlage für VHD-Dateien von virtuellen IaaS-Computern.

Erstellen Sie zunächst eine Datei für den Upload in ein Blob.
Verwenden Sie bei Verwendung von Azure Cloud Shell Folgendes, um eine Datei zu erstellen: `vi helloworld`. Wenn die Datei geöffnet wird, drücken Sie **EINFG**, geben Sie „Hello world“ ein, drücken Sie **ESC**, geben Sie `:x` ein, und drücken Sie die **EINGABETASTE**.

In diesem Beispiel laden Sie mithilfe des Befehls [az storage blob upload](/cli/azure/storage/blob#upload) ein Blob in den Container hoch, den Sie im letzten Schritt erstellt haben.

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Wenn Sie mithilfe der zuvor beschriebenen Methode eine Datei in Azure Cloud Shell erstellt haben, können Sie stattdessen den folgenden CLI-Befehl verwenden. (Beachten Sie, dass Sie keinen Pfad angeben müssen, da die Datei im Basisverzeichnis erstellt wurde. Normalerweise muss ein Pfad angegeben werden.)

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name helloworld
    --file helloworld
```

Dabei wird das Blob erstellt, falls es nicht vorhanden ist, oder überschrieben, falls es bereits vorhanden ist. Laden Sie beliebig viele Dateien hoch, bevor Sie fortfahren.

Wenn Sie mehrere Dateien gleichzeitig hochladen möchten, können Sie den Befehl [az storage blob upload-batch](/cli/azure/storage/blob#upload-batch) verwenden.

## <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs in einem Container

Sie können die Blobs im Container mit dem Befehl [az storage blob list](/cli/azure/storage/blob#list) auflisten.

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Herunterladen eines Blobs

Mit dem Befehl [az storage blob download](/cli/azure/storage/blob#download) können Sie das zuvor hochgeladene Blob herunterladen.

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Übertragen von Daten mit AzCopy

Das Dienstprogramm [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ist eine weitere Option für eine leistungsstarke, skriptfähige Datenübertragung für Azure Storage. Sie können AzCopy zum Übertragen von Daten in und aus Blob-, Datei- und Tabellenspeicher verwenden.

Als kurzes Beispiel sehen Sie hier den AzCopy-Befehl für das Hochladen der Datei *myfile.txt* in den Container *mystoragecontainer*.

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://mystorageaccount.blob.core.windows.net/mystoragecontainer \
    --dest-key <storage-account-access-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen in der Ressourcengruppe, einschließlich des in dieser Schnellstartanleitung erstellten Speicherkontos, nicht mehr benötigen, löschen Sie die Ressourcengruppe mit dem Befehl [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie Dateien zwischen lokalen Festplatten und einem Container in Azure Blob Storage übertragen. Weitere Informationen zum Arbeiten mit Blobs in Azure Storage finden Sie im Tutorial zum Arbeiten mit Azure Blob Storage.

> [!div class="nextstepaction"]
> [Gewusst wie: Blob Storage-Vorgänge mit der Azure-Befehlszeilenschnittstelle](storage-how-to-use-blobs-cli.md)
