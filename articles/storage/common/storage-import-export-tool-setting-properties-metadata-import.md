---
title: Festlegen von Eigenschaften und Metadaten mithilfe von Azure Import/Export | Microsoft Docs
description: Erfahren Sie, wie Sie beim Ausführen des Azure Import/Export-Tools zur Vorbereitung der Laufwerke Eigenschaften und Metadaten angeben, die für die Zielblobs festgelegt werden sollen.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 1ba6d157402fae0c7d7bf841d2b4e4f6b1ee1084
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23059405"
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Festlegen von Eigenschaften und Metadaten im Rahmen des Importprozesses

Wenn Sie das Microsoft Azure Import/Export-Tool zum Vorbereiten der Laufwerke ausführen, können Sie Eigenschaften und Metadaten angeben, die für die Zielblobs festgelegt werden sollen. Folgen Sie diesen Schritten:

1.  Erstellen Sie zum Festlegen von Blobeigenschaften auf dem lokalen Computer eine Textdatei mit Eigenschaftsnamen und -werten.
2.  Erstellen Sie zum Festlegen von Blobmetadaten auf dem lokalen Computer eine Textdatei mit Metadatennamen und -werten.
3.  Übergeben Sie im Rahmen des Vorgangs `PrepImport` den vollständigen Pfad zu einer oder beiden Dateien an das Azure Import/Export-Tool.

> [!NOTE]
>  Wenn Sie bei einer Kopiersitzung eine Eigenschaften- oder Metadatendatei angeben, werden diese Eigenschaften bzw. Metadaten für jedes Blob festgelegt, das im Rahmen dieser Kopiersitzung importiert wird. Wenn Sie für einige der importierten Blobs andere Eigenschaften oder Metadaten angeben möchten, müssen Sie eine separate Kopiersitzung mit anderen Eigenschaften- oder Metadatendateien erstellen.

## <a name="specify-blob-properties-in-a-text-file"></a>Festlegen von Blobeigenschaften in einer Textdatei

Zum Festlegen der Blobeigenschaften erstellen Sie eine lokale Textdatei, und fügen Sie XML hinzu, das die Eigenschaftsnamen als Elemente und die Eigenschaftswerte als Werte angibt. Hier sehen Sie ein Beispiel, in dem einige Eigenschaftswerte angegeben werden:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

Speichern Sie die Datei an einem lokalen Speicherort wie `C:\WAImportExport\ImportProperties.txt`.

## <a name="specify-blob-metadata-in-a-text-file"></a>Festlegen von Blobmetadaten in einer Textdatei

Zum Festlegen von Blobmetadaten erstellen Sie eine lokale Textdatei, in der die Metadatennamen als Elemente und Metadatenwerte als Werte angegeben werden. Hier sehen Sie ein Beispiel, in dem einige Metadatenwerte angegeben werden:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Speichern Sie die Datei an einem lokalen Speicherort wie `C:\WAImportExport\ImportMetadata.txt`.

## <a name="add-the-path-to-properties-and-metadata-files-in-datasetcsv"></a>Fügen Sie den Pfad den Eigenschaften und Metadatendateien in „dataset.csv“ hinzu.

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,https://mystorageaccount.blob.core.windows.net/video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,https://mystorageaccount.blob.core.windows.net/photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,https://mystorageaccount.blob.core.windows.net/music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

## <a name="next-steps"></a>Nächste Schritte

* [Format der Metadaten- und Eigenschaftendatei des Import/Export-Diensts](../storage-import-export-file-format-metadata-and-properties.md)
