---
title: Bekannte Probleme mit Azure Data Lake Storage Gen2 | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Einschränkungen und bekannten Probleme mit Azure Data Lake Storage Gen2.
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: normesta
ms.openlocfilehash: 75e0aa0847d44df40a4823d98460b011addab4d7
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305751"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Bekannte Probleme mit Azure Data Lake Storage Gen2

In diesem Artikel werden die Funktionen und Tools aufgelistet, die noch nicht oder nur teilweise für Speicherkonten unterstützt werden, die einen hierarchischen Namespace haben (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>Blob Storage-APIs

Blob Storage-APIs sind deaktiviert, um Probleme bei der Funktionsfähigkeit von Features zu vermeiden, die durch die fehlende Interoperabilität zwischen Blob Storage-APIs und Azure Data Lake Storage Gen2-APIs entstehen können.

> [!NOTE]
> Wenn Sie sich für die öffentliche Vorschauversion des Multiprotokollzugriffs von Data Lake Storage registrieren, können Blob-APIs und Data Lake Storage Gen2-APIs für dieselben Daten genutzt werden. Weitere Informationen finden Sie unter [Multi-protocol access on Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md) (Multiprotokollzugriff unter Azure Data Lake Storage).

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>Vorgehensweise bei vorhandenen Tools, Anwendungen und Diensten

Wenn hierfür jeweils Blob-APIs verwendet werden und Sie diese für alle Inhalte nutzen möchten, die Sie in Ihr Konto hochladen, haben Sie zwei Möglichkeiten.

* **Option 1**: Aktivieren Sie keinen hierarchischen Namespace in Ihrem Blobspeicherkonto, bevor für die Blob-APIs Interoperabilität mit Azure Data Lake Gen2-APIs besteht. Die Verwendung eines Speicherkontos ohne einen hierarchischen Namespace bedeutet, dass Sie keinen Zugriff auf Data Lake Storage Gen2-spezifische Features wie Zugriffssteuerungslisten (ACLs) für das Verzeichnis- und Dateisystem haben.

* **Option 2**: Registrieren Sie sich für die öffentliche Vorschauversion des [Multiprotokollzugriffs unter Data Lake Storage](data-lake-storage-multi-protocol-access.md). Tools und Anwendungen, die Blob-APIs aufrufen, und Blobspeicherfeatures, z. B. Diagnoseprotokolle, können für Konten verwendet werden, die über einen hierarchischen Namespace verfügen.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Vorgehensweise, wenn Sie Blob-APIs zum Laden von Daten verwendet haben, bevor diese deaktiviert wurden

Falls Sie diese APIs vor der Deaktivierung zum Laden von Daten verwendet haben und produktionsbedingt Zugriff auf diese Daten benötigen, wenden Sie sich mit folgenden Informationen an den Microsoft-Support:

> [!div class="checklist"]
> * Abonnement-ID (die GUID, nicht der Name)
> * Speicherkontoname(n)
> * Ist Ihre Produktion direkt betroffen, und falls ja, für welche Speicherkonten?
> * Müssen die Daten in ein anderes Speicherkonto kopiert werden, und falls ja, warum? (Diese Frage ist auch relevant, wenn Ihre Produktion nicht direkt betroffen ist.)

Unter diesen Umständen können wir den Zugriff auf die Blob-API für einen begrenzten Zeitraum wiederherstellen, damit Sie diese Daten in ein Speicherkonto kopieren können, bei dem das Feature „hierarchischer Namespace“ nicht aktiviert ist.

### <a name="issues-and-limitations-with-using-blob-apis-on-accounts-that-have-a-hierarchical-namespace"></a>Probleme und Einschränkungen bei der Verwendung von Blob-APIs für Konten, die über einen hierarchischen Namespace verfügen

Wenn Sie sich für die öffentliche Vorschauversion des Multiprotokollzugriffs von Data Lake Storage registrieren, können Blob-APIs und Data Lake Storage Gen2-APIs für dieselben Daten genutzt werden.

In diesem Abschnitt werden Probleme und Einschränkungen bei der Verwendung von Blob-APIs und Data Lake Storage Gen2-APIs für dieselben Daten beschrieben.

Diese Blob-Rest-APIs werden nicht unterstützt:

* [Put Blob (Page)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Put Page](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Get Page Ranges](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Incremental Copy Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Put Page from URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Put Blob (Append)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Append Block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Append Block from URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

* Es ist nicht möglich, sowohl Blob-APIs als auch Data Lake Storage-APIs zu verwenden, um in dieselbe Instanz einer Datei zu schreiben.

* Wenn Sie in eine Datei schreiben, indem Sie Data Lake Storage Gen2-APIs verwenden, sind die Blöcke dieser Datei für Aufrufe der [Get Block List](https://docs.microsoft.comrest/api/storageservices/get-block-list)-Blob-API nicht sichtbar.

* Sie können eine Datei überschreiben, indem Sie entweder Data Lake Storage Gen2-APIs oder Blob-APIs verwenden. Dies wirkt sich nicht auf die Dateieigenschaften aus.

* Wenn Sie den Vorgang [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) verwenden, ohne ein Trennzeichen anzugeben, enthalten die Ergebnisse sowohl Verzeichnisse als auch Blobs.

  Wenn Sie sich für Trennzeichen entscheiden, sollten Sie nur einen Schrägstrich (`/`) verwenden. Dies ist das einzige Trennzeichen, das unterstützt wird.

* Wenn Sie die [Delete Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob)-API zum Löschen eines Verzeichnisses verwenden, wird es nur gelöscht, sofern es leer ist.

  Dies bedeutet, dass Sie die Blob-API zum Löschen von Verzeichnissen nicht rekursiv verwenden können.

## <a name="issues-with-unmanaged-virtual-machine-vm-disks"></a>Probleme mit nicht verwalteten VM-Datenträgern

Nicht verwaltete VM-Datenträger werden für Konten, die über einen hierarchischen Namespace verfügen, nicht unterstützt. Wenn Sie einen hierarchischen Namespace für ein Speicherkonto aktivieren möchten, sollten Sie verwaltete VM-Datenträger in einem Speicherkonto anordnen, für das die Funktion für hierarchische Namespaces nicht aktiviert ist.


## <a name="support-for-other-blob-storage-features"></a>Unterstützung für andere Blobspeicherfeatures

In der folgenden Tabelle werden die Funktionen und Tools aufgelistet, die noch nicht oder nur teilweise für Speicherkonten unterstützt werden, die einen hierarchischen Namespace haben (Azure Data Lake Storage Gen2).

| Feature/Tool    | Weitere Informationen    |
|--------|-----------|
| **APIs für Data Lake Storage Gen2-Speicherkonten** | Teilweise unterstützt <br><br>Multiprotokollzugriff unter Data Lake Storage befindet sich derzeit in der öffentlichen Vorschauphase. In dieser Vorschauversion können Sie Blob-APIs in den .NET, Java und Python SDKs mit Konten nutzen, die über einen hierarchischen Namespace verfügen.  Die SDKs enthalten noch keine APIs, die Ihnen das Interagieren mit Verzeichnissen oder das Festlegen von Zugriffssteuerungslisten (ACLs) ermöglichen. Für diese Funktionen können Sie Data Lake Storage Gen2-**REST**-APIs verwenden. |
| **AzCopy** | Versionsspezifische Unterstützung <br><br>Verwenden Sie nur die neueste Version von AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Frühere Versionen von AzCopy wie z. B. AzCopy v8.1 werden nicht unterstützt.|
| **Richtlinien für die Azure Blob Storage-Lebenszyklusverwaltung** | Wird nur unterstützt, wenn Sie sich für die Vorschauversion des [Multiprotokollzugriffs unter Data Lake Storage](data-lake-storage-multi-protocol-access.md) registrieren. Von der Vorschauversion werden nur die Zugriffsebenen „Kalt“ und „Archiv“ unterstützt. Das Löschen von Blobmomentaufnahmen wird noch nicht unterstützt. |
| **Azure Content Delivery Network (CDN)** | Noch nicht unterstützt|
| **Azure Search** |Noch nicht unterstützt|
| **Azure Storage-Explorer** | Versionsspezifische Unterstützung <br><br>Verwenden Sie nur Version `1.6.0` oder höher. <br>Version `1.6.0` steht zum [kostenlosen Download](https://azure.microsoft.com/features/storage-explorer/) zur Verfügung.|
| **Blobcontainer-ACLs** |Noch nicht unterstützt|
| **blobfuse** |Noch nicht unterstützt|
| **Benutzerdefinierte Domänen** |Noch nicht unterstützt|
| **Dateisystem-Explorer** | Eingeschränkte Unterstützung |
| **Unveränderlicher Speicher** |Noch nicht unterstützt <br><br>Durch unveränderlichen Speicher können Sie Daten in einem [WORM-Zustand (Write Once, Read Many)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) speichern.|
| **Objektebenen** |Die Ebenen „Kalt“ und „Archiv“ werden nur unterstützt, wenn Sie sich für die Vorschauversion des [Multiprotokollzugriffs unter Data Lake Storage](data-lake-storage-multi-protocol-access.md) registrieren. <br><br> Alle anderen Zugriffsebenen werden noch nicht unterstützt.|
| **PowerShell- und CLI-Unterstützung** | Eingeschränkte Funktionalität <br><br>Verwaltungsvorgänge, z. B. das Erstellen eines Kontos, werden unterstützt. Vorgänge auf Datenebene, z. B. das Hoch- und Herunterladen von Dateien, befinden sich in der öffentlichen Vorschauphase (im Rahmen des [Multiprotokollzugriffs unter Data Lake Storage](data-lake-storage-multi-protocol-access.md)). Das Verwenden von Verzeichnissen und Festlegen von Zugriffssteuerungslisten (ACLs) wird noch nicht unterstützt. |
| **Statische Websites** |Noch nicht unterstützt <br><br>Insbesondere die Möglichkeit, Dateien an [statische Websites](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website) zu senden.|
| **Drittanbieteranwendungen** | Eingeschränkte Unterstützung <br><br>Drittanbieteranwendungen, die REST-APIs verwenden, funktionieren auch weiterhin, wenn Sie sie mit Data Lake Storage Gen2 verwenden. <br>Anwendungen, die Blob-APIs aufrufen, sollten funktionieren, wenn Sie sich für die öffentliche Vorschauversion des [Multiprotokollzugriffs unter Data Lake Storage](data-lake-storage-multi-protocol-access.md) registrieren. 
| **Versionsverwaltungsfunktionen** |Noch nicht unterstützt <br><br>Dazu zählen [Momentaufnahmen](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) und [vorläufiges Löschen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).|


