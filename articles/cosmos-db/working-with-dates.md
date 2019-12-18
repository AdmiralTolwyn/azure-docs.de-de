---
title: Arbeiten mit Datumsangaben in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie DataTime-Objekte in Azure Cosmos DB speichern, indizieren und abfragen.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: 162b1a4ad089e75f4ad953a339b9b4c15e245a70
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74869680"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Arbeiten mit Datumsangaben in Azure Cosmos DB
Azure Cosmos DB bietet Schemaflexibilität und eine umfassende Indizierung über ein natives [JSON](https://www.json.org)-Datenmodell. Alle Azure Cosmos DB-Ressourcen, z.B. Datenbanken, Container, Dokumente und gespeicherte Prozeduren, werden als JSON-Dokumente modelliert und gespeichert. Aufgrund der erforderlichen Portierbarkeit unterstützt JSON (ebenso wie Azure Cosmos DB) nur einen kleinen Satz grundlegender Typen: String, Number, Boolean, Array, Object und Null. JSON ist jedoch flexibel und ermöglicht es Entwicklern und Frameworks, komplexere Daten darzustellen, indem deren Grundtypen verwendet und als Objekte oder Arrays zusammengestellt werden. 

Zusätzlich zu den grundlegenden Typen benötigen viele Anwendungen den DateTime-Typ, um Daten und Zeitstempel darzustellen. Dieser Artikel beschreibt, wie Entwickler Datumsangaben in Azure Cosmos DB über das .NET SDK speichern, abrufen und abfragen können.

## <a name="storing-datetimes"></a>Speichern von DateTime-Werten

Azure Cosmos DB unterstützt JSON-Typen wie „String“, „Number“, „Boolean“, „NULL“, „Array“ und „Object“. Ein DateTime-Typ wird nicht direkt unterstützt. Derzeit unterstützt Azure Cosmos DB keine Lokalisierung von Daten. Daher müssen Sie DateTime-Werte als Zeichenfolgen speichern. Das empfohlene Format für DateTime-Zeichenfolgen in Azure Cosmos DB ist `YYYY-MM-DDThh:mm:ss.sssZ`, das dem ISO 8601 UTC Standard folgt. Es wird empfohlen, alle Datumsangaben in Azure Cosmos DB als UTC-Angabe zu speichern. Die Konvertierung der Datumszeichenfolgen in diesem Format ermöglicht das lexikografische Sortieren der Daten. Wenn Datumsangaben in einem anderen Format als UTC gespeichert werden, muss die Logik auf Clientseite behandelt werden. Um einen lokalen DateTime-Wert in UTC zu konvertieren, muss das Offset bekannt und als Eigenschaft im JSON-Format gespeichert sein. Außerdem kann der Client das Offset verwenden, um den DateTime-Wert im UTC-Format zu berechnen.

Die meisten Anwendungen können die standardmäßige Zeichenfolgendarstellung aus folgenden Gründen für DateTime verwenden:

* Zeichenfolgen können verglichen werden, und die relative Reihenfolge der DateTime-Werte wird beibehalten, wenn diese in Zeichenfolgen umgewandelt werden. 
* Für diesen Ansatz sind weder benutzerdefinierter Code noch Attribute für die JSON-Konvertierung erforderlich.
* Die als JSON gespeicherten Daten sind visuell lesbar.
* Bei dieser Vorgehensweise können Sie den Azure Cosmos DB-Index für eine schnelle Abfrageleistung nutzen.

Der folgende Codeausschnitt beispielsweise speichert ein `Order`-Objekt mit zwei DateTime-Eigenschaften – `ShipDate` und `OrderDate` – als Dokument mit dem .NET SDK:

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

Dieses Dokument wird folgendermaßen in Azure Cosmos DB gespeichert:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

Alternativ dazu können Sie DateTime-Werte auch als UNIX-Zeitstempel speichern, also als Zahl, die die Anzahl der seit dem 1. Januar 1970 verstrichenen Sekunden darstellt. Die interne Timestamp-Eigenschaft von Azure Cosmos DB (`_ts`) folgt diesem Ansatz. Sie können die [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx)-Klasse verwenden, um DateTime-Werte als Zahlen zu serialisieren. 

## <a name="indexing-datetimes-for-range-queries"></a>Indizieren von DateTime-Werten für Bereichsabfragen
Bereichsabfragen werden bei DateTime-Werten häufig ausgeführt. Wenn Sie z.B. alle seit gestern erstellten oder in den letzten fünf Minuten ausgelieferten Aufträge finden möchten, müssen Sie Bereichsabfragen ausführen. Um diese Abfragen effizient auszuführen, müssen Sie Ihre Sammlung für die Bereichsindizierung von Zeichenfolgen konfigurieren.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

Weitere Informationen zum Konfigurieren von Indizierungsrichtlinien finden Sie unter [Azure Cosmos DB-Indizierungsrichtlinien](index-policy.md).

## <a name="querying-datetimes-in-linq"></a>Abfragen von DateTime-Werten in LINQ
Das .NET SDK für SQL unterstützt automatisch die Abfrage von Daten, die über LINQ in Azure Cosmos DB gespeichert sind. Der folgende Codeausschnitt zeigt z.B. eine LINQ-Abfrage, die Aufträge filtert, die in den letzten drei Tagen ausgeliefert wurden.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

Weitere Informationen über die SQL-Abfragesprache von Azure Cosmos DB und den LINQ-Anbieter finden Sie unter [Abfragen von Cosmos DB](how-to-sql-query.md).

In diesem Artikel wurde erläutert, wie DateTime-Werte in Azure Cosmos DB gespeichert, indiziert und abgefragt werden.

## <a name="next-steps"></a>Nächste Schritte
* Herunterladen und Ausführen der [Codebeispiele auf GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Weitere Informationen zu [SQL-Abfragen](how-to-sql-query.md)
* Weitere Informationen zu den [Indizierungsrichtlinien von Azure Cosmos DB](index-policy.md)
