---
title: Indizierung von CSV-Blobs mit Azure Search-Blobindexer | Microsoft Docs
description: Erfahren Sie, wie Sie CSV-Blobs mit Azure Search indizieren können.
author: chaosrealm
manager: jlembicz
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 12/28/2017
ms.author: eugenesh
ms.openlocfilehash: f4c1238b5ff03d9fc52238660134fde774b3474e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/18/2018
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indizierung von CSV-Blobs mit Azure Search-Blobindexer
In der Standardeinstellung analysiert der [Azure Search-Blobindexer](search-howto-indexing-azure-blob-storage.md) durch Trennzeichen getrennte Blobs als ein einzelnes Textsegment. Bei Blobs mit CSV-Daten sollen die einzelnen Zeilen im Blob jedoch häufig als separates Dokument behandelt werden. Beispiel: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Es wird empfohlen, diesen durch Trennzeichen getrennten Text als zwei Dokumente zu analysieren. Dabei sollte jedes Dokument die Felder „id“, „datePublished“ und „tags“ enthalten.

In diesem Artikel erfahren Sie, wie Sie CSV-Blobs mit einem Azure Search-Blobindexer analysieren. 

> [!IMPORTANT]
> Diese Funktion befindet sich derzeit in der Vorschauphase. Sie ist nur im Rahmen der REST-API unter der Version **2015-02-28-Preview**verfügbar. Beachten Sie hierbei, dass Vorschau-APIs für Tests und Evaluierungen bestimmt sind und nicht in Produktionsumgebungen eingesetzt werden sollten. 
> 
> 

## <a name="setting-up-csv-indexing"></a>Einrichten der CSV-Indizierung
Erstellen oder aktualisieren Sie zum Indizieren von CSV-Blobs eine Indexerdefinition mit dem `delimitedText` -Analysemodus:  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Weitere Informationen zur API zum Erstellen eines Indexers finden Sie unter [Erstellen eines Indexers](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

`firstLineContainsHeaders` gibt an, dass die erste (nicht leere) Zeile der einzelnen Blobs Header enthält.
Wenn Blobs am Anfang keine Headerzeile enthalten, sollten die Header in der Indexerkonfiguration angegeben werden: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Sie können das Trennzeichen mithilfe der Konfigurationseinstellung `delimitedTextDelimiter` anpassen. Beispiel: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Derzeit wird nur die UTF-8-Codierung unterstützt. Wenn Sie Unterstützung für andere Codierungen benötigen, informieren Sie uns auf [unserer UserVoice-Website](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Bei Verwendung des Analysemodus für durch Trennzeichen getrennten Text betrachtet Azure Search alle Blobs in Ihrer Datenquelle als CSV-Blobs. Wenn Sie eine Mischung aus CSV- und Nicht-CSV-Blobs in der gleichen Datenquelle unterstützen müssen, informieren Sie uns auf [unserer UserVoice-Website](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Beispiele für Anforderungen
Dies alles wird an vollständigen Nutzlastbeispielen demonstriert. 

Datenquelle: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexer:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Helfen Sie uns bei der Verbesserung von Azure Search
Teilen Sie uns auf unserer [UserVoice-Website](https://feedback.azure.com/forums/263029-azure-search/)mit, wenn Sie sich Features wünschen oder Verbesserungsvorschläge haben.

