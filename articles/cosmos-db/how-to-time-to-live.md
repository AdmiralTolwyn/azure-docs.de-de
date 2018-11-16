---
title: Konfigurieren und Verwalten der Gültigkeitsdauer in Azure Cosmos DB
description: Konfigurieren und Verwalten der Gültigkeitsdauer in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/14/2018
ms.author: mjbrown
ms.openlocfilehash: 25824b9959a4fef536ca9fe247f29fc9aaece1f7
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626465"
---
# <a name="how-to-configure-time-to-live-in-azure-cosmos-db"></a>Konfigurieren der Gültigkeitsdauer in Azure Cosmos DB

In Azure Cosmos DB können Sie die Gültigkeitsdauer (Time to Live, TTL) auf der Containerebene konfigurieren oder auf der Elementebene außer Kraft setzen, nachdem Sie sie für den Container festgelegt haben. Die Gültigkeitsdauer für einen Container kann über das Azure-Portal oder mithilfe der sprachspezifischen SDKs konfiguriert werden. Die Außerkraftsetzung der Gültigkeitsdauer auf der Elementebene kann mithilfe der SDKs konfiguriert werden.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Aktivieren der Gültigkeitsdauer für einen Container über das Azure-Portal

Gehen Sie wie folgt vor, um die Gültigkeitsdauer für einen Container ohne Ablauf zu aktivieren. Aktivieren Sie diese Option, um die Außerkraftsetzung der Gültigkeitsdauer auf der Elementebene zu ermöglichen. Sie können auch eine Gültigkeitsdauer festlegen, indem Sie einen Sekundenwert festlegen, der nicht Null ist.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Erstellen Sie ein neues Azure Cosmos-Konto, oder wählen Sie ein bereits vorhandenes Konto aus.

3. Öffnen Sie den Bereich **Daten-Explorer**.

4. Wählen Sie einen vorhandenen Container aus, erweitern Sie ihn, und ändern Sie die folgenden Werte:

   * Öffnen Sie das Fenster **Scale & Settings** (Skalierung und Einstellungen).
   * Suchen Sie unter **Einstellung** nach **Gültigkeitsdauer**.
   * Wählen Sie **Ein (Standard)** aus, oder wählen Sie **Ein** aus, und legen Sie einen Wert für die Gültigkeitsdauer fest.
   * Klicken Sie zum Speichern der Änderungen auf **Speichern**.

   ![Konfigurieren der Gültigkeitsdauer über das Azure-Portal](./media/how-to-time-to-live/how-to-time-to-live-portal.png)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Aktivieren der Gültigkeitsdauer für einen Container per SDK

### <a id="dotnet-enable-noexpiry"></a>.NET SDK

```csharp
// Create a new collection with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Festlegen der Gültigkeitsdauer für einen Container per SDK

### <a id="dotnet-enable-withexpiry"></a>.NET SDK

Wenn Sie die Gültigkeitsdauer für einen Container festlegen möchten, müssen Sie eine positive Zahl ungleich Null angeben, die den Zeitraum in Sekunden angibt. Auf der Grundlage des konfigurierten Werts für die Gültigkeitsdauer werden alle Elemente im Container nach dem Zeitstempel (`_ts`) der letzten Änderung des Elements gelöscht.

```csharp
// Create a new collection with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

## <a name="set-time-to-live-on-an-item"></a>Festlegen der Gültigkeitsdauer für ein Element

Zusätzlich zu einer Standardgültigkeitsdauer für einen Container können Sie auch eine Gültigkeitsdauer für ein Element festlegen. Wenn Sie eine Gültigkeitsdauer auf der Elementebene festlegen, setzt diese die Standardgültigkeitsdauer des Elements in diesem Container außer Kraft.

* Wenn Sie die Gültigkeitsdauer für ein Element festlegen möchten, müssen Sie mit einer positiven Zahl ungleich Null den Zeitraum angeben, nach dem das Element ablaufen soll, und zwar in Sekunden ab dem Zeitstempel (`_ts`) der letzten Änderung des Elements.

* Wenn das Element über kein Feld für die Gültigkeitsdauer verfügt, gilt automatisch die für den Container festgelegte Gültigkeitsdauer.

* Wird die Gültigkeitsdauer auf der Containerebene deaktiviert, wird das elementspezifische Feld für die Gültigkeitsdauer ignoriert, bis die Gültigkeitsdauer für den Container wieder aktiviert wird.

### <a id="dotnet-set-ttl-item"></a>.NET SDK

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? TimeToLive { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    TimeToLive = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

## <a name="reset-time-to-live"></a>Zurücksetzen der Gültigkeitsdauer

Sie können die Gültigkeitsdauer für ein Element zurücksetzen, indem Sie einen Schreib- oder Aktualisierungsvorgang für das Element ausführen. Der Schreib- oder Aktualisierungsvorgang legt den Zeitstempel (`_ts`) auf die aktuelle Zeit fest, und die Gültigkeitsdauer für den Ablauf des Elements beginnt von vorn. Wenn Sie die Gültigkeitsdauer eines Elements ändern möchten, können Sie das Feld genau wie jedes andere Feld aktualisieren.

### <a id="dotnet-extend-ttl-item"></a>.NET SDK

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.TimeToLive = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="turn-off-time-to-live"></a>Ausschalten der Gültigkeitsdauer

Wenn für ein Element eine Gültigkeitsdauer festgelegt wurde, das Element aber nicht mehr ablaufen soll, können Sie das Element abrufen, das Feld für die Gültigkeitsdauer entfernen und das Element anschließend auf dem Server ersetzen. Wenn das Feld für die Gültigkeitsdauer aus dem Element entfernt wird, gilt für das Element wieder die dem Container zugewiesene Standardgültigkeitsdauer. Legen Sie den Wert für die Gültigkeitsdauer auf „-1“ fest, wenn das Element nicht ablaufen und nicht die für den Container festgelegte Gültigkeitsdauer erben soll.

### <a id="dotnet-turn-off-ttl-item"></a>.NET SDK

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.TimeToLive = null; // inherit the default TTL of the collection

response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="disable-time-to-live"></a>Deaktivieren der Gültigkeitsdauer

Wenn Sie die Gültigkeitsdauer für einen Container deaktivieren und verhindern möchten, dass der Hintergrundprozess nach abgelaufenen Elementen sucht, muss die Eigenschaft `DefaultTimeToLive` für den Container gelöscht werden. Das Löschen dieser Eigenschaft ist nicht das Gleiche wie das Festlegen der Eigenschaft auf „-1“. Wenn Sie die Eigenschaft auf „-1“ festlegen, laufen neue Elemente, die dem Container hinzugefügt werden, nicht ab, und der Wert kann für bestimmte Elemente im Container außer Kraft gesetzt werden. Wenn Sie die Eigenschaft für die Gültigkeitsdauer aus dem Container entfernen, laufen die Elemente auch dann ab, wenn der vorherige Standardwert für die Gültigkeitsdauer für sie explizit außer Kraft gesetzt wurde.

### <a id="dotnet-disable-ttl"></a>.NET SDK

```csharp
// Get the collection, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Gültigkeitsdauer finden Sie im folgenden Artikel:

* [Gültigkeitsdauer](time-to-live.md)