---
title: "Indizieren einer Azure Cosmos DB SQL API-Datenquelle für Azure Search | Microsoft-Dokumentation"
description: In diesem Artikel wird beschrieben, wie Sie einen Azure Search-Indexer mit einer Azure Cosmos DB SQL API-Datenquelle erstellen.
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: search
ms.date: 01/08/2018
ms.author: eugenesh
robot: noindex
ms.openlocfilehash: e449f13adcd1a3651e1cac852b23f21d0227038a
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2018
---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>Verbinden von Cosmos DB mit Azure Search mithilfe von Indexern

[Azure Cosmos DB](../cosmos-db/introduction.md) ist eine global verteilte Datenbank von Microsoft mit mehreren Modellen. Mit seiner [SQL-API](../cosmos-db/sql-api-introduction.md) stellt Azure Cosmos DB umfassende und vertraute SQL-Abfragefunktionen mit einheitlich kurzen Wartezeiten für schemalose JSON-Daten bereit. Azure Search und die SQL-API sind nahtlos integriert. Sie können JSON-Dokumente direkt per Pull in einen Azure Search-Index einfügen. Verwenden Sie dazu einen [Azure Search-Indexer](search-indexer-overview.md), der speziell für Azure Cosmos DB SQL API entwickelt wurde. 

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Konfigurieren Sie Azure Search für die Verwendung einer Azure Cosmos DB SQL API-Datenbank als Datenquelle. Geben Sie optional eine Abfrage an, um eine Teilmenge auswählen.
> * Erstellen Sie einen Suchindex mit JSON-kompatiblen Datentypen.
> * Konfigurieren Sie einen Indexer für eine bedarfsgesteuerte und wiederholte Indizierung.
> * Aktualisieren Sie den Index schrittweise basierend auf Änderungen an den zugrunde liegenden Daten.

> [!NOTE]
> Azure Cosmos DB SQL API ist die nächste Generation von DocumentDB. Obwohl der Produktname geändert wurde, ist die `documentdb`-Syntax in den Azure Search-Indexern immer noch aus Gründen der Abwärtskompatibilität sowohl in den Azure Search-APIs als auch auf den Portalseiten vorhanden. Wenn Sie Indexer konfigurieren, stellen Sie sicher, dass Sie die `documentdb`-Syntax wie in diesem Artikel beschrieben angeben.

<a name="supportedAPIs"></a>

## <a name="supported-api-types"></a>Unterstützte API-Typen

Obwohl Azure Cosmos DB eine Vielzahl von Datenmodellen und APIs unterstützt, erstreckt sich die Indexer-Unterstützung nur auf die SQL-API. 

Unterstützung für zusätzliche APIs ist in Kürze verfügbar. Um uns zu helfen, Prioritäten zu setzen, welche zuerst unterstützt werden sollen, werfen Sie einen Blick auf die User Voice-Website:

* [Unterstützung für Tabellen-API-Datenquellen](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)
* [Unterstützung für Graph-API-Datenquellen](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4)
* [Unterstützung für MongoDB-API-Datenquellen](https://feedback.azure.com/forums/263029-azure-search/suggestions/18861421-documentdb-indexer-should-be-able-to-index-mongodb)
* [Unterstützung für Apache Cassandra-API-Datenquellen](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu)

## <a name="prerequisites"></a>Voraussetzungen

Zum Einrichten eines Azure Cosmos DB-Indexers benötigen Sie einen [Azure Search-Dienst](search-create-service-portal.md). Erstellen Sie außerdem einen Index, eine Datenquelle und schließlich den Indexer. Sie können diese Objekte mit dem [Portal](search-import-data-portal.md), dem [.NET SDK](/dotnet/api/microsoft.azure.search) oder der [REST-API](/rest/api/searchservice/) für alle Nicht-.NET-Sprachen erstellen. 

Wenn Sie sich für das Portal entscheiden, führt Sie der [Datenimport-Assistent](search-import-data-portal.md) durch die Erstellung aller dieser Ressourcen samt Index.

> [!TIP]
> Starten Sie über das Azure Cosmos DB-Dashboard den **Datenimport-Assistenten**, um die Indizierung für diese Datenquelle zu vereinfachen. Navigieren Sie im linken Navigationsbereich zu **Sammlungen** > **Azure Search hinzufügen**, um mit dem Vorgang zu beginnen.

<a name="Concepts"></a>

## <a name="azure-search-indexer-concepts"></a>Azure Search Indexer-Konzepte
Azure Search unterstützt die Erstellung und Verwaltung von Datenquellen (einschließlich Azure Cosmos DB SQL API) und von Indexern, die für diese Datenquellen ausgeführt werden.

Eine **Datenquelle** gibt die zu indizierenden Daten, Anmeldeinformationen und Richtlinien für das Bestimmen von Änderungen in den Daten an (z.B. geänderte oder gelöschte Dokumente in Ihrer Sammlung). Die Datenquelle wird als unabhängige Ressource definiert, sodass sie von mehreren Indexern verwendet werden kann.

Ein **Indexer** beschreibt, wie die Daten von der Datenquelle in einen Zielsuchindex fließen. Ein Indexer ermöglicht folgende Vorgänge:

* Eine einmalige Kopie der Daten zum Auffüllen eines Indexes ausführen.
* Einen Index mit Änderungen an der Datenquelle nach einem Zeitplan synchronisieren. Der Zeitplan ist Teil der Indexer-Definition.
* Bedarfs-Updates für einen Index je nach Notwendigkeit abrufen.

<a name="CreateDataSource"></a>

## <a name="step-1-create-a-data-source"></a>Schritt 1: Erstellen einer Datenquelle
Führen Sie einen POST aus, um eine Datenquelle zu erstellen:

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

Der Anforderungstext umfasst die Datenquellendefinition, welche die folgenden Felder enthalten sollte:

* **Name**: Wählen Sie einen beliebigen Namen für Ihre Datenbank.
* **type**: Muss `documentdb` lauten.
* **Anmeldeinformationen**:
  
  * **ConnectionString**: Erforderlich. Geben Sie die Verbindungsinformationen für Ihre Azure Cosmos DB-Datenbank im folgenden Format an: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`
* **Container**:
  
  * **Name**: Erforderlich. Geben Sie die ID der zu indizierenden Datenbanksammlung an.
  * **Abfrage**: Optional. Sie können eine Abfrage spezifizieren, um ein beliebiges JSON-Dokument in ein Flatfile-Schema zu reduzieren, welches Azure Search indizieren kann.
* **dataChangeDetectionPolicy**: Empfohlen. Weitere Informationen finden Sie im Abschnitt [Indizieren von geänderten Dokumenten](#DataChangeDetectionPolicy).
* **dataDeletionDetectionPolicy**: Optional. Weitere Informationen finden Sie im Abschnitt [Indizieren von gelöschten Dokumenten](#DataDeletionDetectionPolicy).

### <a name="using-queries-to-shape-indexed-data"></a>Verwenden von Abfragen zum Formen indizierter Daten
Sie können eine SQL-Abfrage angeben, um geschachtelte Eigenschaften oder Arrays zu vereinfachen, JSON-Eigenschaften zu projizieren und die zu indizierenden Daten zu filtern. 

Beispieldokument:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }

Abfrage zur Filterung:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Vereinfachen der Abfrage:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Abfrage zur Projektion:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Abfrage zum Vereinfachen eines Arrays:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>Schritt 2: Erstellen eines Index
Erstellen Sie einen Azure Search-Zielindex, wenn Sie bislang noch über keinen verfügen. Sie können einen Index über die [Benutzeroberfläche des Azure-Portals](search-create-index-portal.md), die [REST-API zum Erstellen von Indizes](/rest/api/searchservice/create-index) und die [Index-Klasse](/dotnet/api/microsoft.azure.search.models.index) erstellen.

Anhand des folgenden Beispiels wird ein Index mit einer ID und einem Beschreibungsfeld erstellt:

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Stellen Sie sicher, dass das Schema des Ziel-Indexes mit dem Schema der JSON-Quelldokumente oder mit der Ausgabe Ihrer benutzerdefinierten Abfrageprojektion kompatibel ist.

> [!NOTE]
> Für partitionierte Sammlungen ist der Standarddokumentschlüssel die Azure Cosmos DB-Eigenschaft `_rid`, die in Azure Search in `rid` umbenannt wird. Darüber hinaus enthalten die `_rid`-Werte von Azure Cosmos DB Zeichen, die in Azure Search-Schlüsseln ungültig sind. Deshalb sind die `_rid`-Werte Base64-codiert.
> 
> 

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Zuordnung zwischen JSON-Datentypen und Azure Search-Datentypen
| JSON-Datentyp | Kompatible Feldtypen im Zielindex |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Zahlen, die wie Ganzzahlen aussehen |Edm.Int32, Edm.Int64, Edm.String |
| Zahlen, die wie Gleitkommas aussehen |Edm.Double, Edm.String |
| Zeichenfolge |Edm.String |
| Arrays primitiver Typen, z.B. ["a", "b", "c"] |Collection(Edm.String) |
| Zeichenfolgen, die wie Datumsangaben aussehen |Edm.DateTimeOffset, Edm.String |
| GeoJSON-Objekte, z.B. { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Andere JSON-Objekte |N/V |

<a name="CreateIndexer"></a>

## <a name="step-3-create-an-indexer"></a>Schritt 3: Erstellen eines Indexers

Nach der Erstellung von Index und Datenquelle können Sie den Indexer erstellen:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Dieser Indexer wird alle zwei Stunden ausgeführt (das Planungsintervall ist auf „PT2H“ festgelegt). Um einen Indexer alle 30 Minuten auszuführen, legen Sie das Intervall auf „PT30M“ fest. Das kürzeste unterstützte Intervall beträgt fünf Minuten. Der Zeitplan ist optional. Ohne Zeitplan wird ein Indexer nur einmal bei seiner Erstellung ausgeführt. Allerdings können Sie ein Indexer bei Bedarf jederzeit ausführen.   

Weitere Informationen zur API zum Erstellen eines Indexers finden Sie unter [Erstellen eines Indexers](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>Ausführen des Indexers nach Bedarf
Zusätzlich zur Ausführung in regelmäßigen Abständen nach einem Zeitplan kann ein Indexer auch nach Bedarf ausgeführt werden:

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2016-09-01
    api-key: [Search service admin key]

> [!NOTE]
> Wenn die Ausführungs-API erfolgreich ausgeführt wurde, wurde der Indexeraufruf geplant, die eigentliche Verarbeitung erfolgt jedoch asynchron. 

Sie können den indexerstatus im Portal oder mithilfe der im Folgenden beschriebenen API zum Abrufen des Indexer-Status überwachen. 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>Abrufen des Indexer-Status
Sie können den Status und den Ausführungsverlauf eines Indexers abrufen:

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2016-09-01
    api-key: [Search service admin key]

Die Antwort enthält den Gesamtstatus des Indexers, den letzten (oder laufenden) Aufruf des Indexers sowie den Verlauf der letzten Indexeraufrufe.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Der Ausführungsverlauf enthält bis zu 50 der jüngsten abgeschlossenen Ausführungen. Diese sind in umgekehrter chronologischer Reihenfolge sortiert (somit ist die neueste Ausführung als Erstes in der Antwort aufgelistet).

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>Indizieren von geänderten Dokumenten
Die Richtlinie zum Erkennen von Datenänderungen dient einer effizienten Identifizierung geänderter Datenelemente. Derzeit ist die einzige unterstützte Richtlinie die `High Water Mark`-Richtlinie, die die `_ts`-Eigenschaft (Zeitstempel) verwendet, die von Azure Cosmos DB bereitgestellt wird. Diese wird wie folgt angegeben:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Wenn Sie diese Richtlinie verwenden, wird dringend empfohlen sicherzustellen, dass der Indexer eine hohe Leistung erzielt. 

Wenn Sie eine benutzerdefinierte Abfrage verwenden, stellen sicher, dass die `_ts`Eigenschaft von der Abfrage projiziert wird.

<a name="IncrementalProgress"></a>
### <a name="incremental-progress-and-custom-queries"></a>Inkrementeller Status und benutzerdefinierte Abfragen
Der inkrementelle Status während der Indizierung stellt sicher, dass der Indexer bei der nächsten Ausführung an der gleichen Stelle fortgesetzt werden kann und nicht die gesamte Sammlung von vorn indizieren muss, wenn er durch vorübergehende Fehler oder das Erreichen des Ausführungszeitlimits unterbrochen wird. Dies ist besonders beim Indizieren umfangreicher Sammlungen wichtig. 

Stellen Sie zum Aktivieren des inkrementellen Status bei Verwendung einer benutzerdefinierten Abfrage sicher, dass die Abfrage die Ergebnisse nach der Spalte `_ts` sortiert. Dadurch können regelmäßige-Prüfpunkte erstellt werden, die von Azure Search im Falle eines Fehlers zur Bereitstellung des inkrementellen Status verwendet werden.   

In bestimmten Fällen kann Azure Search unter Umständen nicht erkennen, dass die Abfrage nach `_ts` sortiert ist. (Dies kann sogar vorkommen, wenn die Abfrage eine `ORDER BY [collection alias]._ts`-Klausel enthält.) Mithilfe der Konfigurationseigenschaft `assumeOrderByHighWaterMarkColumn` können Sie Azure Search mitteilen, dass die Ergebnisse sortiert sind. Erstellen oder aktualisieren Sie Ihren Indexer wie folgt, um diesen Hinweis anzugeben: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>
## <a name="indexing-deleted-documents"></a>Indizieren von gelöschten Dokumenten
Wenn Zeilen aus der Quelltabelle gelöscht werden, möchten Sie diese Zeilen in der Regel auch aus dem Suchindex löschen. Die Richtlinie zum Erkennen von Datenlöschungen dient einer effizienten Identifizierung gelöschter Datenelemente. Zurzeit ist `Soft Delete` die einzige unterstützte Richtlinie (die Löschung wird durch ein bestimmtes Kennzeichen markiert). Diese wird folgendermaßen festgelegt:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Wenn Sie eine benutzerdefinierte Abfrage verwenden, stellen Sie sicher, dass die Eigenschaft, auf die `softDeleteColumnName` verweist, von der Abfrage projiziert wird.

Im folgenden Beispiel wird eine Datenquelle mit einer Richtlinie zum vorläufigen Löschen erstellt:

    POST https://[Search service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>Nächste Schritte
Glückwunsch! Sie haben gelernt, wie Sie Azure Cosmos DB mit Azure Search integrieren können, indem Sie einen Indexer verwenden, um Dokumente in einem SQL-Datenmodell zu durchforsten und hochzuladen.

* Weitere Informationen zu Azure Cosmos DB finden Sie auf der [Seite über den Azure Cosmos DB-Dienst](https://azure.microsoft.com/services/cosmos-db/).
* Weitere Informationen zu Azure Search finden Sie auf der [Seite des Search-Diensts](https://azure.microsoft.com/services/search/).
