---
title: Festlegen und Abrufen von Objekteigenschaften und Metadaten in Azure Storage | Microsoft-Dokumentation
description: Speichern Sie benutzerdefinierten Metadaten für Objekte in Azure Storage, legen Sie Systemeigenschaften fest, und rufen Sie diese ab.
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 036f9006-273e-400b-844b-3329045e9e1f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2018
ms.author: tamram
ms.openlocfilehash: d2c95139d42f42e43e2fa6e587d5b1b13afdf1e9
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112442"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Festlegen und Abrufen von Eigenschaften und Metadaten

Objekte in Azure Storage unterstützen zusätzlich zu den Daten, die sie enthalten, Systemeigenschaften und benutzerdefinierte Metadaten. In diesem Artikel wird das Verwalten von Systemeigenschaften und benutzerdefinierten Metadaten mithilfe der [Azure Storage-Clientbibliothek für .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) beschrieben.

* **Systemeigenschaften**: Systemeigenschaften sind in jeder Speicherressource vorhanden. Einige davon können gelesen oder festgelegt werden, während andere schreibgeschützt sind. Darüber hinaus entsprechen einige Systemeigenschaften bestimmten HTTP-Standardheadern. Diese Eigenschaften werden für Sie von den Azure Storage-Clientbibliotheken verwaltet.

* **Benutzerdefinierte Metadaten**: Benutzerdefinierte Metadaten bestehen aus einem oder mehreren Name-Wert-Paaren, die Sie für eine Azure Storage-Ressource angeben. Sie können Metadaten verwenden, um weitere Werte zusammen mit einer Ressource zu speichern. Metadatenwerte sind nur für Ihre eigenen Zwecke bestimmt und wirken sich nicht auf das Verhalten der Ressource aus.

Das Abrufen von Eigenschafts- und Metadatenwerten einer Speicherressource ist ein zweistufiger Prozess. Bevor Sie diese Werte lesen können, müssen Sie sie explizit durch Aufrufen der **FetchAttributesAsync** oder **FetchAttributesAsync**-Methode abrufen. Eine Ausnahme ist, wenn Sie die **Exists**- oder **ExistsAsync**-Methode für eine Ressource aufrufen. Wenn Sie eine der dieser Methoden aufrufen, ruft Azure Storage im Hintergrund als Teil des Aufrufs der **Exists**-Methode die entsprechende **FetchAttributes**-Methode auf.

> [!IMPORTANT]
> Wenn Sie feststellen, dass Eigenschaften- oder Metadatenwerte für eine Speicherressource nicht ausgefüllt wurden, überprüfen Sie, ob Ihr Code die **FetchAttributes**- oder **FetchAttributesAsync**-Methode aufruft.
>
> Name-Wert-Paare für Metadaten können nur ASCII-Zeichen enthalten. Name-Wert-Paare für Metadaten sind gültige HTTP-Header und müssen daher allen Einschränkungen für HTTP-Header entsprechen. Es wird empfohlen, für Namen und Werte mit Nicht-ASCII-Zeichen die URL- oder Base64-Codierung zu wählen.
>

## <a name="setting-and-retrieving-properties"></a>Festlegen und Abrufen von Eigenschaften
Zum Abrufen von Eigenschaftswerten rufen Sie die **FetchAttributesAsync**-Methode für das Blob oder den Container auf, um die Eigenschaften aufzufüllen, und lesen anschließend die Werte.

Um Eigenschaften für ein Objekt festzulegen, geben Sie den Eigenschaftswert an und rufen dann die **SetProperties** -Methode auf.

Das folgende Codebeispiel erstellt einen Container und schreibt dann einige der zugehörigen Eigenschaftswerte in ein Konsolenfenster.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
await container.FetchAttributesAsync();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>Festlegen und Abrufen von Metadaten
Sie können Metadaten als ein oder mehrere Name-Wert-Paare für eine Blob- oder Containerressource angeben. Fügen Sie zum Festlegen von Metadaten Name-Wert-Paare zur **Metadaten**-Sammlung der Ressource hinzu, und rufen Sie dann die **SetMetadata** oder **SetMetadataAsync**-Methode auf, um die Werte für den Dienst zu speichern.

> [!NOTE]
> Der Name der Metadaten muss den Benennungskonventionen für C#-Bezeichner entsprechen.
>
>

Das folgende Codebeispiel legt die Metadaten für einen Container fest. Mittels der **Hinzufügen** -Methode der Sammlung wird ein Wert festgelegt. Der andere Wert wird mit der impliziten Schlüssel-Wert-Syntax festgelegt. Beide eignen sich hierzu.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    // Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    // Set the container's metadata.
    await container.SetMetadataAsync();
}
```

Zum Abrufen von Metadaten rufen Sie die **FetchAttributes**- oder **FetchAttributesAsync**-Methode für das Blob oder den Container auf, um die **Metadaten**-Sammlung zu füllen, und lesen anschließend die Werte wie im unten stehenden Beispiel gezeigt.

```csharp
public static async Task ListContainerMetadataAsync(CloudBlobContainer container)
{
    // Fetch container attributes in order to populate the container's properties and metadata.
    await container.FetchAttributesAsync();

    // Enumerate the container's metadata.
    Console.WriteLine("Container metadata:");
    foreach (var metadataItem in container.Metadata)
    {
        Console.WriteLine("\tKey: {0}", metadataItem.Key);
        Console.WriteLine("\tValue: {0}", metadataItem.Value);
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
* [Azure Storage-Clientbibliothek für .NET-Referenz](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [Azure Storage-Clientbibliothek für .NET-NuGet-Paket](https://www.nuget.org/packages/WindowsAzure.Storage/)
