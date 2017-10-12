---
title: Verwenden des Azure-Blobspeichers (Objektspeicher) mit Java | Microsoft Docs
description: Speichern Sie nicht strukturierte Daten in der Cloud mit Azure Blob Storage (Objektspeicher).
services: storage
documentationcenter: java
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 2e223b38-92de-4c2f-9254-346374545d32
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 91ef09916dbb587305572ea640fb4408ea9aebb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-blob-storage-from-java"></a>Verwenden des Blob-Speichers mit Java
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>Übersicht
Der Azure-BLOB-Speicher ist ein Dienst, bei dem unstrukturierte Daten in der Cloud als Objekte/Blobs gespeichert werden. In Blob Storage können alle Arten von Text- oder Binärdaten gespeichert werden, z. B. ein Dokument, eine Mediendatei oder ein Installer einer Anwendung. Der Blobspeicher wird auch als Objektspeicher bezeichnet.

In diesem Artikel wird die Durchführung gängiger Szenarien mit dem Microsoft Azure-Blob-Speicher demonstriert. Die Beispiele wurden in Java geschrieben und verwenden das [Azure Storage-SDK für Java][Azure Storage SDK for Java]. Die behandelten Szenarien umfassen das **Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs. Weitere Informationen zu Blobs finden Sie im Abschnitt [Nächste Schritte](#Next-Steps) .

> [!NOTE]
> Ein SDK steht für Entwickler zur Verfügung, die Azure Storage auf Android-Geräten verwenden. Weitere Informationen finden Sie unter [Azure Storage-SDK für Android][Azure Storage SDK for Android].
>
>

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Erstellen einer Java-Anwendung
In diesem Artikel werden Sie Speicherfunktionen verwenden, die lokal in einer Java-Anwendung oder in Code ausgeführt werden können, der in einer Webrolle oder in einer Workerrolle in Azure ausgeführt wird.

Dafür müssen Sie das Java Development Kit (JDK) installieren und ein Azure Storage-Konto in Ihrem Azure-Abonnement erstellen. Sobald Sie dies erledigt haben, müssen Sie sicherstellen, dass Ihr Entwicklungssystem die minimalen Anforderungen und Abhängigkeiten erfüllt, die im Repository [Azure Storage-SDK für Java][Azure Storage SDK for Java] auf GitHub aufgelistet sind. Wenn Ihr System diese Anforderungen erfüllt, können Sie die Anweisungen für das Herunterladen und Installieren der Azure Storage-Bibliotheken für Java auf Ihr System von diesem Repository befolgen. Sobald Sie diese Aufgaben abgeschlossen haben, können Sie eine Java-Anwendung erstellen, die die Beispiele in diesem Artikel verwendet.

## <a name="configure-your-application-to-access-blob-storage"></a>Konfigurieren der Anwendung für den Zugriff auf Blob-Speicher
Fügen Sie die folgenden "import"-Anweisungen am Anfang der Java-Datei hinzu, um die Stellen anzugeben, an denen Azure-Speicher-APIs auf Blobs zugreifen sollen:

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Einrichten einer Azure-Speicherverbindungszeichenfolge
Ein Azure-Speicherclient verwendet eine Speicherverbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen für den Zugriff auf Datenverwaltungsdienste. Bei der Ausführung in einer Clientanwendung muss die Speicherverbindungszeichenfolge in dem unten gezeigten Format angegeben werden. Dabei müssen der Name Ihres Speicherkontos und der primäre Zugriffsschlüssel für das im [Azure-Portal](https://portal.azure.com) aufgeführte Speicherkonto als Werte für *AccountName* und *AccountKey* eingegeben werden. Das folgende Beispiel zeigt, wie Sie ein statisches Feld für die Verbindungszeichenfolge deklarieren.

```java
// Define the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

In einer Anwendung, die in einer Microsoft Azure-Rolle ausgeführt wird, kann diese Zeichenfolge in der Dienstkonfigurationsdatei *ServiceConfiguration.cscfg* gespeichert werden. Der Zugriff darauf kann dann durch Aufruf der Methode **RoleEnvironment.getConfigurationSettings** erfolgen. Das folgende Beispiel zeigt, wie die Verbindungszeichenfolge von einem **Setting**-Element mit der Bezeichnung *StorageConnectionString* in der Dienstkonfigurationsdatei abgerufen wird.

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

In den folgenden Beispielen wird davon ausgegangen, dass Sie eine dieser zwei Methoden verwendet haben, um die Speicherverbindungszeichenfolge abzurufen.

## <a name="create-a-container"></a>Erstellen eines Containers
Ein **CloudBlobClient** -Objekt ermöglicht den Abruf von Referenzobjekten für Container und Blobs. Mit dem folgenden Code wird ein **CloudBlobClient** -Objekt erstellt.

> [!NOTE]
> Es gibt zusätzliche Möglichkeiten zum Erstellen von **CloudStorageAccount**-Objekten. Weitere Informationen finden Sie unter **CloudStorageAccount** in der [Azure Storage-Client-SDK-Referenz].
>
>

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Mithilfe des **CloudBlobClient**-Objekts können Sie einen Verweis auf den Container abrufen, den Sie verwenden möchten. Falls der Container nicht vorhanden ist, können Sie ihn mit der **createIfNotExists**-Methode erstellen. Andernfalls gibt die Methode den vorhandenen Container zurück. Standardmäßig ist der neue Container privat, sodass Sie Ihren Speicherzugriffsschlüssel wie zuvor angeben müssen, um Blobs aus diesem Container herunterzuladen.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Get a reference to a container.
    // The container name must be lower case
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create the container if it does not exist.
    container.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

### <a name="optional-configure-a-container-for-public-access"></a>Optional: Konfigurieren eines Containers für den öffentlichen Zugriff
Die Berechtigungen eines Containers werden standardmäßig für den privaten Zugriff konfiguriert. Sie können die Berechtigungen eines Containers jedoch einfach konfigurieren, um allen Benutzern im Internet einen öffentlichen Schreibzugriff zu gewähren:

```java
// Create a permissions object.
BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

// Include public access in the permissions object.
containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

// Set the permissions on the container.
container.uploadPermissions(containerPermissions);
```

## <a name="upload-a-blob-into-a-container"></a>Hochladen eines Blobs in einen Container
Rufen Sie einen Containerverweis ab und verwenden Sie diesen zum Abrufen eines Blob-Verweises, um eine Datei in ein Blob hochzuladen. Sobald Sie über einen Blob-Verweis verfügen, können Sie jeden Datenstrom hochladen, indem Sie die upload-Methode für den Blob-Verweis aufrufen. Bei diesem Vorgang wird das Blob erstellt, falls es nicht vorhanden ist, oder überschrieben, falls es vorhanden ist. Das folgende Codebeispiel zeigt diesen Vorgang. Dabei wird davon ausgegangen, dass der Container bereits erstellt wurde.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Define the path to a local file.
    final String filePath = "C:\\myimages\\myimage.jpg";

    // Create or overwrite the "myimage.jpg" blob with contents from a local file.
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    File source = new File(filePath);
    blob.upload(new FileInputStream(source), source.length());
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs in einem Container
Um die Blobs in einem Container aufzulisten, müssen Sie zuerst einen Containerverweis abrufen, ebenso wie beim Hochladen eines Blobs. Sie können die **listBlobs**-Methode des Containers mit einer **for**-Schleife verwenden. Der folgende Code gibt den URI der einzelnen Blobs in einem Container in der Konsole aus.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop over blobs within the container and output the URI to each of them.
    for (ListBlobItem blobItem : container.listBlobs()) {
        System.out.println(blobItem.getUri());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Beachten Sie, dass Sie bei der Benennung von BLOBs Pfadinformationen im Namen verwenden können. Dadurch entsteht eine virtuelle Verzeichnisstruktur, die Sie wie ein herkömmliches Dateisystem organisieren und durchlaufen können. Beachten Sie, dass nur die Verzeichnisstruktur virtuell ist – die einzigen Ressourcen, die im Blob-Speicher verfügbar sind, sind Container und Blobs. Die Clientbibliothek bietet jedoch ein **CloudBlobDirectory** -Objekt, das auf ein virtuelles Verzeichnis verweist und das Arbeiten mit BLOBs vereinfacht, die auf diese Weise organisiert sind.

Beispiel: Sie verfügen über einen Container mit der Bezeichnung "photos", in den Sie Blobs mit der Bezeichnung "rootphoto1", "2010/photo1", "2010/photo2" und "2011/photo1" hochladen. Hierdurch werden die virtuellen Verzeichnisse "2010" und "2011" im Container "photos" erstellt. Wenn Sie **listBlobs** für den Container „photos“ aufrufen, enthält die zurückgegebene Sammlung **CloudBlobDirectory**- und **CloudBlob**-Objekte, die die Verzeichnisse und Blobs auf der obersten Ebene darstellen. In diesem Fall werden die Verzeichnisse "2010" und "2011" sowie das Foto "rootphoto1" zurückgegeben. Mit dem **instanceof** -Operator können Sie diese Objekte unterscheiden.

Optional können Sie Parameter an die **listBlobs**-Methode übergeben, indem Sie den **useFlatBlobListing**-Parameter auf „true“ festlegen. Dies führt dazu, dass jedes Blob unabhängig vom Verzeichnis zurückgegeben wird. Weitere Informationen finden Sie unter **CloudBlobContainer.listBlobs** in der [Azure Storage-Client-SDK-Referenz].

## <a name="download-a-blob"></a>Herunterladen eines Blobs
Führen Sie zum Herunterladen von Blobs die gleichen Schritte wie zum Hochladen eines Blobs aus, um einen Blob-Verweis abzurufen. Im Uploadbeispiel haben Sie die upload-Methode für das Blob-Objekt aufgerufen. Im folgenden Beispiel rufen Sie die download-Methode auf, um den Blob-Inhalt an ein Datenstromobjekt wie z. B. **FileOutputStream** zu übertragen, das Sie verwenden können, um das Blob in einer lokalen Datei zu speichern.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop through each blob item in the container.
    for (ListBlobItem blobItem : container.listBlobs()) {
        // If the item is a blob, not a virtual directory.
        if (blobItem instanceof CloudBlob) {
            // Download the item and save it to a file with the same name.
            CloudBlob blob = (CloudBlob) blobItem;
            blob.download(new FileOutputStream("C:\\mydownloads\\" + blob.getName()));
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-blob"></a>Löschen eines Blobs
Zum Löschen eines Blobs rufen Sie einen Blobverweis ab und rufen dann **deleteIfExists**auf.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Retrieve reference to a blob named "myimage.jpg".
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

    // Delete the blob.
    blob.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-blob-container"></a>Löschen eines Blob-Containers
Zum Löschen eines BLOB-Containers rufen Sie einen BLOB-Containerverweis ab und dann **deleteIfExists**auf.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Delete the blob container.
    container.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich nun mit den Grundlagen des Blob-Speichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

* [Azure Storage-SDK für Java][Azure Storage SDK for Java]
* [Azure Storage-Client-SDK-Referenz][Azure Storage-Client-SDK-Referenz]
* [Azure Storage-REST-API][Azure Storage REST API]
* [Azure Storage-Teamblog][Azure Storage Team Blog]

Weitere Informationen finden Sie auch unter [Azure für Java-Entwickler](/java/azure).

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage-Client-SDK-Referenz]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
