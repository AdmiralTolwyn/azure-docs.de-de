---
title: "Azure-Schnellstart – Übertragen von Objekten nach/aus Azure Blob Storage mit .NET | Microsoft-Dokumentation"
description: "Hier lernen Sie schnell, wie Sie mit .NET Objekte nach/aus Azure Blob Storage übertragen."
services: storage
documentationcenter: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 12/04/2017
ms.author: tamram
ms.openlocfilehash: 5020f070a8eb9215f175fc3ff3a905cff28ce37f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-net"></a>Übertragen von Objekten nach/aus Azure Blob Storage mit .NET

In diesem Schnellstart erfahren Sie, wie Sie die .NET-Clientbibliothek für Azure Storage zum Hochladen, Herunterladen und Auflisten von Blockblobs in einem Container verwenden.

## <a name="prerequisites"></a>Voraussetzungen

So führen Sie diesen Schnellstart durch:

* Installieren von .NET Core 2.0 für [Linux](/dotnet/core/linux-prerequisites?tabs=netcore2x) oder [Windows](/dotnet/core/windows-prerequisites?tabs=netcore2x)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Herunterladen der Beispielanwendung

Die in diesem Schnellstart verwendete Beispielanwendung ist eine einfache Konsolenanwendung. 

Verwenden Sie [Git](https://git-scm.com/), um eine Kopie der Anwendung in Ihre Entwicklungsumgebung herunterzuladen. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Mit diesem Befehl wird das Repository in Ihren lokalen Git-Ordner geklont. Suchen Sie zum Öffnen der Visual Studio-Projektmappe nach dem Ordner „storage-blobs-dotnet-quickstart“ öffnen Sie ihn, und doppelklicken Sie auf „storage-blobs-dotnet-quickstart.sln“. 

## <a name="configure-your-storage-connection-string"></a>Konfigurieren der Speicherverbindungszeichenfolge

Sie müssen in der Anwendung die Verbindungszeichenfolge für das Speicherkonto angeben. Es wird empfohlen, diese Verbindungszeichenfolge in einer Umgebungsvariablen auf dem lokalen Computer zu speichern, auf dem die Anwendung ausgeführt wird. Befolgen Sie je nach Betriebssystem die Schritte für eines der unten angegebenen Beispiele, um die Umgebungsvariable zu erstellen.  Ersetzen Sie \<yourconnectionstring\> durch Ihre Verbindungszeichenfolge.

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```
### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

## <a name="run-the-sample"></a>Ausführen des Beispiels

Mit diesem Beispiel wird eine Testdatei in „Dokumente“ erstellt, diese wird in Blob Storage hochgeladen, die Blobs im Container werden aufgelistet, und dann wird die Datei mit einem neuen Namen heruntergeladen, damit Sie die alte und neue Datei vergleichen können. 

Navigieren Sie zu Ihrem Anwendungsverzeichnis, und führen Sie die Anwendung mit dem Befehl `dotnet run` aus.

```
dotnet run
```

Die Ausgabe sieht in etwa wie im folgenden Beispiel aus:

```
Azure Blob storage quick start sample
Temp file = /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Uploading to Blob storage as blob 'QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt'
List blobs in container.
https://mystorageaccount.blob.core.windows.net/quickstartblobs/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Downloading blob to /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374_DOWNLOADED.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

Wenn Sie eine beliebige Taste drücken, um den Vorgang fortzusetzen, werden der Speichercontainer und die Dateien gelöscht. Überprüfen Sie im Ordner „Eigene Dateien“, ob die beiden Dateien vorhanden sind, bevor Sie fortfahren. Sie können diese öffnen und prüfen, ob sie identisch sind. Kopieren Sie die URL für das Blob aus dem Konsolenfenster, und fügen Sie sie in einem Browser ein, um den Inhalt der Datei in Blob Storage anzuzeigen.

Sie können zum Anzeigen der Dateien in Blob Storage auch ein Tool, z.B. den [Azure Storage-Explorer](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), verwenden. Der Azure Storage-Explorer ist ein kostenloses plattformübergreifendes Tool, das Ihnen den Zugriff auf die Speicherkontoinformationen ermöglicht. 

Nachdem Sie die Dateien erfolgreich überprüft haben, drücken Sie eine beliebige Taste, um die Demo zu beenden und die Testdateien zu löschen. Da Sie jetzt wissen, welche Aktionen das Beispiel ausführt, öffnen Sie die Datei „Program.cs“, um den Code zu betrachten. 

## <a name="understand-the-sample-code"></a>Grundlagen des Beispielcodes

Als Nächstes gehen wir schrittweise durch den Beispielcode, damit Sie verstehen können, wie er funktioniert.

### <a name="get-references-to-the-storage-objects"></a>Abrufen von Verweisen auf die Speicherobjekte

Zunächst müssen die Verweise auf die Objekte erstellt werden, die zum Zugreifen auf und Verwalten von Blob Storage verwendet werden. Diese Objekte bauen aufeinander auf, und jedes wird vom jeweils nächsten in der Liste verwendet.

* Erstellen Sie eine Instanz des [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)-Objekts, das auf das Speicherkonto verweist.

* Erstellen Sie eine Instanz des [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)-Objekts, das auf den Blob-Dienst im Speicherkonto verweist.

* Erstellen Sie eine Instanz des [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)-Objekts, das den Container darstellt, auf den Sie zugreifen. Container werden zum Organisieren der Blobs verwendet, so wie Sie auf Ihrem Computer Ordner zum Organisieren von Dateien verwenden.

Sobald Sie über den [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet) verfügen, können Sie eine Instanz des [CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)-Objekts erstellen, das auf das gewünschte Blob verweist, und einen Upload-, Download- oder Kopiervorgang sowie weitere Vorgänge ausführen.

> [!IMPORTANT]
> Die Containernamen müssen klein geschrieben werden. Weitere Informationen zu Container- und Blobnamen finden Sie unter [Benennen von Containern, Blobs und Metadaten und Verweisen auf diese](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

In diesem Abschnitt erstellen Sie eine Instanz der Objekte und einen neuen Container und legen dann Berechtigungen für den Container fest, damit die Blobs öffentlich sind und einfach mit einer URL auf sie zugegriffen werden kann. Der Name des Containers lautet **quickstartblobs**.

In diesem Beispiel wird [CreateIfNotExists](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexists?view=azure-dotnet) verwendet, da bei jedem Ausführen des Beispiels ein neuer Container erstellt werden soll. In einer Produktionsumgebung, in der Sie in der gesamten Anwendung den gleichen Container verwenden, empfiehlt es sich hingegen, **CreateIfNotExists** nur einmal aufzurufen. Alternativ können Sie den Container im Voraus erstellen, damit er nicht im Code erstellt werden muss.

```csharp
// Load the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");
if (storageConnectionString == null)
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable name 'storageconnectionstring' with the actual storage " +
        "connection string as a value.");
}
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Hochladen von Blobs in den Container

Blob Storage unterstützt Block-, Anfüge- und Seitenblobs. Blockblobs werden am häufigsten verwendet und auch in diesem Schnellstart verwendet.

Um eine Datei in ein Blob hochzuladen, rufen Sie einen Verweis auf das Blob im Zielcontainer ab. Nachdem Sie den Blobverweis abgerufen haben, können Sie mithilfe von [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync) Daten in das Blockblob hochladen. Wenn noch kein Blob vorhanden ist, wird durch diesen Vorgang eines erstellt. Andernfalls wird das vorhandene Blob überschrieben.

Der Beispielcode erstellt eine lokale Datei für den Upload und Download. Dabei wird die hochzuladende Datei als **fileAndPath** und der Name des Blob in **localFileName** gespeichert. Im folgenden Beispiel wird die Datei in einen Container mit dem Namen **quickstartblobs** hochgeladen.

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the location where the blob is going to go, then upload the file.
// Upload the file you created, use localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

Sie können für Blob Storage mehrere Uploadmethoden verwenden. Wenn Sie z.B. über einen Speicherdatenstrom verfügen, können Sie statt „UploadFromFileAsync“ die Methode „UploadFromStreamAsync“ verwenden.

Blockblobs können jede Art von Text oder Binärdateien darstellen. Seitenblobs werden in erster Linie für die VHD-Dateien verwendet, die IaaS-VMs zugrunde liegen. Anfügeblobs dienen der Protokollierung und können z.B. verwendet werden, um beim Schreiben in eine Datei zusätzliche Daten hinzuzufügen. Die meisten Objekte, die in Blob Storage gespeichert werden, sind allerdings Blockblobs.

### <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs in einem Container

Mit [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync) können Sie eine Liste von Dateien im Container abrufen. Der folgende Code ruft die Liste der Blobs ab und durchläuft sie, um die URIs der gefundenen Blobs anzuzeigen. Sie können den URI aus dem Befehlsfenster kopieren und in einen Browser einfügen, um die Datei anzuzeigen.

Bei 5.000 oder weniger Blobs im Container werden in einem einzigen Aufruf von „ListBlobsSegmentedAsync“ alle Blobnamen abgerufen. Wenn der Container mehr als 5.000 Blobs enthält, wird die Liste in Gruppen von 5.000 Blobnamen abgerufen, bis alle Blobnamen abgerufen wurden. Somit werden beim ersten Aufruf dieser API die ersten 5.000 Blobnamen und ein Fortsetzungstoken zurückgegeben. Beim zweiten Aufruf geben Sie das Token an, und die nächste Gruppe von Blobnamen wird abgerufen usw., bis das Fortsetzungstoken NULL ist, was bedeutet, dass alle Blobnamen abgerufen wurden.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null);

```

### <a name="download-blobs"></a>Herunterladen von Blobs

Laden Sie mit [CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync) Blobs auf den lokalen Datenträger herunter.

Der folgende Code lädt das im vorherigen Abschnitt hochgeladene Blob herunter und fügt dem Blobnamen das Suffix „_DOWNLOADED“ hinzu, damit auf dem lokalen Datenträger beide Dateien angezeigt werden.

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Schnellstart hochgeladenen Blobs nicht mehr benötigen, können Sie mit [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync) den gesamten Container löschen. Löschen Sie auch die erstellten Dateien, wenn sie nicht mehr benötigt werden.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the sample files, example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie mit .NET Dateien zwischen einem lokalen Datenträger und Azure Blob Storage übertragen. Weitere Informationen zum Arbeiten mit Blob Storage finden Sie in der exemplarischen Vorgehensweise zu Blob Storage.

> [!div class="nextstepaction"]
> [Gewusst wie: Blob Storage-Vorgänge](storage-dotnet-how-to-use-blobs.md)

In unserer Liste mit [Azure Storage-Beispielen, die .NET verwenden](../common/storage-samples-dotnet.md) finden Sie weitere Azure Storage-Codebeispiele, die Sie herunterladen und ausführen können.

Weitere Informationen zum Storage-Explorer und Blobs finden Sie unter [Verwalten von Azure Blob Storage-Ressourcen mit dem Speicher-Explorer (Vorschau)](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
