---
title: 'Azure Cosmos DB: Entwickeln mit der SQL-API in .NET | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie mit der SQL-API von Azure Cosmos DB unter Verwendung von .NET entwickeln können.
services: cosmos-db
documentationcenter: ''
author: rafats
manager: kfile
editor: ''
tags: ''
ms.assetid: ''
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/10/2017
ms.author: rafats
ms.custom: mvc
ms.openlocfilehash: 528832473d68fa90e6383873b1e0491f5abe09c7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cosmos-db-develop-with-the-sql-api-in-net"></a>Azure Cosmos DB: Entwickeln mit der SQL-API in .NET

Azure Cosmos DB ist der global verteilte Microsoft-Datenbankdienst mit mehreren Modellen. Sie können schnell Dokument-, Schlüssel/Wert- und Graph-Datenbanken erstellen und abfragen und dabei stets die Vorteile der globalen Verteilung und der horizontalen Skalierung nutzen, die Azure Cosmos DB zugrunde liegen.

In diesem Tutorial erfahren Sie, wie Sie ein Azure Cosmos DB-Konto im Azure-Portal erstellen und dann mit einem [Partitionsschlüssel](sql-api-partition-data.md#partition-keys) unter Verwendung der [SQL .NET-API](sql-api-introduction.md) eine Dokumentdatenbank und eine Sammlung erstellen. Durch die Definition eines Partitionsschlüssels beim Erstellen einer Sammlung wird Ihre Anwendung auf müheloses Skalieren bei einem Wachstum des Datenbestands vorbereitet.

Dieses Tutorial behandelt die Ausführung folgender Aufgaben mithilfe der [SQL .NET-API](sql-api-sdk-dotnet.md):

> [!div class="checklist"]
> * Erstellen eines Azure Cosmos DB-Kontos
> * Erstellen einer Datenbank und einer Sammlung mit einem Partitionsschlüssel
> * Erstellen von JSON-Dokumenten
> * Aktualisieren eines Dokuments
> * Abfragen partitionierter Sammlungen
> * Ausführen gespeicherter Prozeduren
> * Löschen eines Dokuments
> * Löschen einer Datenbank

## <a name="prerequisites"></a>Voraussetzungen
Vergewissern Sie sich zunächst, dass Sie über Folgendes verfügen:

* Zugriff auf ein Azure Cosmos DB-Konto

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

  Sie können sich auch für ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) registrieren und ein eigenes Azure-Abonnement verwenden. Danach können Sie [ein Azure Cosmos DB-Konto erstellen](create-sql-api-dotnet.md#create-a-database-account).

* Falls Sie Visual Studio 2017 noch nicht installiert haben, können Sie die **kostenlose** [Visual Studio 2017 Community-Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden. Aktivieren Sie beim Setup von Visual Studio die Option **Azure-Entwicklung**.


> [!TIP]
> * Falls Sie den Azure Cosmos DB-Emulator verwenden möchten, führen Sie die Schritte unter [Azure Cosmos DB-Emulator](local-emulator.md) aus, um den Emulator einzurichten.
>
>


## <a id="SetupVS"></a>Einrichten Ihrer Visual Studio-Projektmappe
1. Öffnen Sie auf Ihrem Computer **Visual Studio**.
2. Wählen Sie im Menü **Datei** die Option **Neu** und anschließend **Projekt** aus.
3. Wählen Sie im Dialogfeld **Neues Projekt** die Option **Vorlagen** / **Visual C#** / **Konsolen-App (.NET Framework)** aus, geben Sie Ihrem Projekt einen Namen, und klicken Sie dann auf **OK**.
   ![Screenshot des Fensters „Neues Projekt“](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-new-project-2.png)

4. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die neue Konsolenanwendung, die sich unter Ihrer Visual Studio-Projektmappe befindet, und klicken Sie anschließend auf **NuGet-Pakete verwalten...**.

    ![Screenshot des Kontextmenüs für das Projekt](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges.png)
5. Klicken Sie auf der Registerkarte **NuGet** auf **Durchsuchen**, und geben Sie in das Suchfeld **documentdb** ein.
<!---stopped here--->
6. Suchen Sie in den Ergebnissen nach **Microsoft.Azure.DocumentDB**, und klicken Sie auf **Installieren**.
   Die Paket-ID für die Azure Cosmos DB-Clientbibliothek lautet [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).
   ![Screenshot des NuGet-Menüs zum Suchen nach dem Azure Cosmos DB-Client-SDK](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges-2.png)

    Wenn Sie eine Meldung im Zusammenhang mit der Überprüfung von Änderungen an der Projektmappe erhalten, klicken Sie auf **OK**. Wenn Sie eine Meldung im Zusammenhang mit der Akzeptierung der Lizenz erhalten, klicken Sie auf **Ich stimme zu**.

## <a id="Connect"></a>Hinzufügen von Verweisen zum Projekt
In den restlichen Schritten dieses Tutorials finden Sie die SQL-API-Codeausschnitte, die zum Erstellen und Aktualisieren der Azure Cosmos DB-Ressourcen in Ihrem Projekt erforderlich sind.

Fügen Sie zuerst diese Verweise Ihrer Anwendung hinzu.
<!---These aren't added by default when you install the pkg?--->

```csharp
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

## <a id="add-references"></a>Herstellen einer Verbindung mit Ihrer App

Als Nächstes fügen Sie diese beiden Konstanten und Ihre *client*-Variable der Anwendung hinzu.

```csharp
private const string EndpointUrl = "<your endpoint URL>";
private const string PrimaryKey = "<your primary key>";
private DocumentClient client;
```

Gehen Sie dann zurück zum [Azure-Portal](https://portal.azure.com), um Ihre Endpunkt-URL und den Primärschlüssel abzurufen. Die Endpunkt-URL und der Primärschlüssel sind erforderlich, damit Ihre Anwendung weiß, womit die Verbindung hergestellt werden soll, und damit Azure Cosmos DB weiß, dass die Verbindung Ihrer Anwendung vertrauenswürdig ist.

Navigieren Sie im Azure-Portal zu Ihrem Azure Cosmos DB-Konto. Klicken Sie im Menü auf der linken Seite auf **Schlüssel** und anschließend auf **Lese-/Schreibschlüssel**.

Kopieren Sie den URI vom Portal, und fügen Sie ihn über `<your endpoint URL>` in die Datei „program.cs“ ein. Kopieren Sie anschließend den PRIMÄRSCHLÜSSEL aus dem Portal, und fügen Sie ihn über `<your primary key>`ein. Achten Sie darauf, dass Sie `<` und `>` aus Ihren Werten entfernen.

![Screenshot des Azure-Portals, das vom NoSQL-Tutorial zum Erstellen einer C#-Konsolenanwendung verwendet wird. Zeigt ein Azure Cosmos DB-Konto, wobei die SCHLÜSSEL im Abschnitt des Azure Cosmos DB-Kontos und die Werte für URI und PRIMÄRSCHLÜSSEL im Abschnitt „Schlüssel“ hervorgehoben sind.](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-keys.png)

## <a id="instantiate"></a>Instanziieren des DocumentClient

Erstellen Sie jetzt eine neue Instanz des **DocumentClient**.

```csharp
DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
```

## <a id="create-database"></a>Erstellen einer Datenbank

Als Nächstes erstellen Sie eine Azure Cosmos DB-[Datenbank](sql-api-resources.md#databases) mithilfe der [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx)- oder [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx)-Methode der **DocumentClient**-Klasse aus dem [SQL .NET-SDK](sql-api-sdk-dotnet.md). Eine Datenbank ist ein logischer Container für JSON-Dokumentspeicher, der auf Sammlungen aufgeteilt ist.

```csharp
await client.CreateDatabaseAsync(new Database { Id = "db" });
```
## <a name="decide-on-a-partition-key"></a>Entscheiden über einen Partitionsschlüssel

Sammlungen sind Container zum Speichern von Dokumenten. Sie sind logische Ressourcen und können [eine oder mehrere physische Partitionen](partition-data.md) umfassen. Ein [Partitionsschlüssel](sql-api-partition-data.md) ist eine Eigenschaft (oder ein Pfad) in Ihren Dokumenten, der zum Verteilen Ihrer Daten auf die Server oder Partitionen verwendet wird. Alle Dokumente mit gleichem Partitionsschlüssel werden in der gleichen Partition gespeichert.

Das Bestimmen eines Partitionsschlüssels ist eine wichtige Entscheidung, die Sie vornehmen, bevor Sie eine Sammlung erstellen. Partitionsschlüssel sind eine Eigenschaft (oder ein Pfad) in Ihren Dokumenten, die von Azure Cosmos DB verwendet werden kann, um Ihre Daten auf mehrere Server oder Partitionen zu verteilen. Cosmos DB erstellt einen Hashwert für den Partitionsschlüsselwert und verwendet das Hashergebnis, um zu ermitteln, in welcher Partition das Dokument gespeichert werden soll. Alle Dokumente mit gleichem Partitionsschlüssel werden in der gleichen Partition gespeichert, und Partitionsschlüssel können nicht geändert werden, sobald eine Sammlung erstellt ist.

Für dieses Tutorial müssen Sie den Partitionsschlüssel auf `/deviceId` festlegen, damit alle Daten für ein einzelnes Gerät in einer einzelnen Partition gespeichert werden. Sie möchten einen Partitionsschlüssel auswählen, der eine große Anzahl von Werten hat, von denen jeder gleich häufig verwendet wird, um sicherzustellen, dass Cosmos DB einen Lastenausgleich durchführen kann, wenn Ihr Datenbestand wächst, und um den gesamten Durchsatz der Sammlung zu erreichen.

Weitere Informationen zur Partitionierung finden Sie unter [Partitionierung, Partitionsschlüssel und Skalierung in DocumentDB](partition-data.md).

## <a id="CreateColl"></a>Erstellen einer Sammlung

Mit dem Partitionsschlüssel (`/deviceId`) können Sie mithilfe der [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx)- oder [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx)-Methode der **DocumentClient**-Klasse eine [Sammlung](sql-api-resources.md#collections) erstellen. Eine Sammlung ist ein Container für JSON-Dokumente und jegliche zugehörige JavaScript-Anwendungslogik.

> [!WARNING]
> Das Erstellen einer Sammlung hat Auswirkungen auf die Preise, da Sie Durchsatz für die Anwendung zur Kommunikation mit Azure Cosmos DB reservieren. Weitere Informationen finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

```csharp
// Collection for device telemetry. Here the JSON property deviceId is used  
// as the partition key to spread across partitions. Configured for 2500 RU/s  
// throughput and an indexing policy that supports sorting against any  
// number or string property. .
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 2500 });
```

Auf diese Weise wird ein REST-API-Aufruf in Azure Cosmos DB durchgeführt, und der Dienst stellt eine Anzahl von Partitionen basierend auf dem angeforderten Durchsatz bereit. Wenn Sie eine höhere Leistung benötigen, können Sie den Durchsatz einer Sammlung oder mehrerer Sammlungen mit dem SDK oder im [Azure-Portal](set-throughput.md) ändern.

## <a id="CreateDoc"></a>Erstellen von JSON-Dokumenten
Wir fügen nun einige JSON-Dokumente in Azure Cosmos DB ein. Ein [Dokument](sql-api-resources.md#documents) kann mithilfe der [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx)-Methode der **DocumentClient**-Klasse erstellt werden. Dokumente sind benutzerdefinierter (beliebiger) JSON-Inhalt. Diese Beispielklasse enthält eine Geräteanzeige sowie einen Aufruf von CreateDocumentAsync, um ein neues Gerät mit Lesevorgang einer Sammlung hinzuzufügen.

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted
// as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```
## <a name="read-data"></a>Lesen von Daten

In diesem Schritt lesen wir das Dokument mithilfe der ReadDocumentAsync-Methode unter Angabe seines Partitionsschlüssels und seiner ID. Beachten Sie, dass der Lesevorgang einen Wert für „PartitionKey“ enthält (entsprechend dem Anforderungsheader `x-ms-documentdb-partitionkey` in der REST-API).

```csharp
// Read document. Needs the partition key and the Id to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;
```

## <a name="update-data"></a>Aktualisieren von Daten

Jetzt aktualisieren wir einige Daten mit der ReplaceDocumentAsync-Methode.

```csharp
// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  reading);
```

## <a name="delete-data"></a>Löschen von Daten

In diesem Schritt löschen wir das Dokument mithilfe der DeleteDocumentAsync-Methode unter Angabe seines Partitionsschlüssels und seiner ID.

```csharp
// Delete a document. The partition key is required.
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```
## <a name="query-partitioned-collections"></a>Abfragen partitionierter Sammlungen

Beim Abfragen von Daten in partitionierten Sammlungen leitet Azure Cosmos DB die Abfrage automatisch an die Partitionen weiter, die den im Filter angegebenen Partitionsschlüsselwerten entsprechen (sofern vorhanden). Diese Abfrage wird z.B. nur an die Partition weitergeleitet, die den Partitionsschlüssel „XMS-0001“ enthält.

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

Die folgende Abfrage verfügt nicht über einen Filter für den Partitionsschlüssel (DeviceId) und wird an alle Partitionen verteilt, wo sie auf dem Partitionsindex ausgeführt wird. Beachten Sie, dass Sie „EnableCrossPartitionQuery“ (`x-ms-documentdb-query-enablecrosspartition` in der REST-API) angeben müssen, damit das SDK eine partitionsübergreifende Abfrage ausführen kann.

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

## <a name="parallel-query-execution"></a>Ausführung paralleler Abfragen
Ab Azure Cosmos DB SQL SDK 1.9.0 werden Optionen zur parallelen Ausführung von Abfragen unterstützt. Dadurch können Sie Abfragen mit niedriger Latenz auf partitionierte Sammlungen anwenden, auch wenn eine große Anzahl von Partitionen berücksichtigt werden muss. Die folgende Abfrage ist z.B. so konfiguriert, dass sie partitionsübergreifend parallel ausgeführt wird.

```csharp
// Cross-partition Order By queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Sie können die parallele Ausführung von Abfragen verwalten, indem Sie die folgenden Parameter optimieren:

* Durch Festlegen von `MaxDegreeOfParallelism` können Sie den Grad der Parallelität steuern, d.h. die maximale Anzahl gleichzeitiger Verbindungen mit den Partitionen der Sammlung. Wenn Sie diesen Parameter auf „-1“ festlegen, wird der Grad der Parallelität vom SDK verwaltet. Falls `MaxDegreeOfParallelism` nicht angegeben oder auf 0 (Standardwert) festgelegt wurde, besteht eine einzelne Netzwerkverbindung mit den Partitionen der Sammlung.
* Durch Festlegen von `MaxBufferedItemCount` können Sie eine Abstimmung zwischen Abfragelatenz und Speicherauslastung auf Clientseite ermöglichen. Wenn Sie diesen Parameter weglassen oder auf „-1“ festlegen, wird die Anzahl von Elementen, die während der Ausführung paralleler Abfragen gepuffert werden, vom SDK verwaltet.

Bei gleichem Zustand der Sammlung gibt eine parallele Abfrage Ergebnisse in der gleichen Reihenfolge wie bei einer seriellen Ausführung zurück. Beim Durchführen einer partitionsübergreifenden Abfrage mit Sortierung (ORDER BY und/oder TOP) führt das SQL SDK die Abfrage parallel auf den Partitionen durch und führt teilweise sortierte Ergebnisse auf Clientseite zusammen, um global sortierte Ergebnisse zu generieren.

## <a name="execute-stored-procedures"></a>Ausführen gespeicherter Prozeduren
Abschließend können Sie Ihrem Projekt den folgenden Code hinzufügen, um atomische Transaktionen für Dokumente mit der gleichen Geräte-ID auszuführen – beispielsweise, wenn Sie Aggregate oder den aktuellen Status eines Geräts in einem einzelnen Dokument verwalten.

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") },
    "XMS-001-FE24C");
```

Das ist schon alles! Dies sind die Hauptkomponenten einer Azure Cosmos DB-Anwendung, die einen Partitionsschlüssel zum effizienten Skalieren der Partitionen übergreifenden Datenverteilung verwendet.  

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese App nicht weiterhin verwenden, löschen Sie im Azure-Portal sämtliche Ressourcen, die mit diesem Tutorial erstellt wurden. Führen Sie dazu folgende Schritte aus:

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Ressourcengruppen**, und klicken Sie auf den eindeutigen Namen der erstellten Ressource.
2. Klicken Sie auf der Seite mit Ihrer Ressourcengruppe auf **Löschen**, geben Sie im Textfeld den Namen der zu löschenden Ressource ein, und klicken Sie dann auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die folgenden Aufgaben ausgeführt:

> [!div class="checklist"]
> * Erstellen eines Azure Cosmos DB-Kontos
> * Erstellen einer Datenbank und einer Sammlung mit einem Partitionsschlüssel
> * Erstellen von JSON-Dokumenten
> * Aktualisieren eines Dokuments
> * Abfragen partitionierter Sammlungen
> * Ausführen einer gespeicherten Prozedur
> * Löschen eines Dokuments
> * Löschen einer Datenbank

Sie können jetzt mit dem nächsten Tutorial fortfahren und zusätzliche Daten in Ihr Cosmos DB-Konto importieren.

> [!div class="nextstepaction"]
> [Importieren von Daten in Azure Cosmos DB](import-data.md)
