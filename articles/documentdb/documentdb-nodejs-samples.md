---
title: "NoSQL Node.js-Beispiele für DocumentDB | Microsoft Docs"
description: "Auf GitHub finden Sie NoSQL Node.js-Beispiele für gängige Aufgaben in DocumentDB, darunter CRUD-Vorgänge für JSON-Dokumente in NoSQL-Datenbanken."
keywords: Node.js-Beispiele
services: documentdb
author: moderakh
manager: jhubbard
editor: monicar
documentationcenter: nodejs
ms.assetid: d87d97be-47a5-4928-8d46-a541fbb33213
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2017
ms.author: moderakh
translationtype: Human Translation
ms.sourcegitcommit: 6d3872dfa5187ea4e0570fdfd0ed3804f461af3f
ms.openlocfilehash: bc3350a094361817f68e31de6561d050a3295f61


---
# <a name="documentdb-nodejs-examples"></a>Node.js-Beispiele für DocumentDB
> [!div class="op_single_selector"]
> * [.NET-Beispiele](documentdb-dotnet-samples.md)
> * [Node.js-Beispiele](documentdb-nodejs-samples.md)
> * [Python-Beispiele](documentdb-python-samples.md)
> * [Katalog mit Azure-Codebeispielen](https://azure.microsoft.com/documentation/samples/?service=documentdb)
> 
> 

Beispiellösungen, in denen CRUD-Vorgänge und andere gängige Vorgänge für Azure DocumentDB-Ressourcen ausgeführt werden, finden Sie im GitHub-Repository [azure-documentdb-nodejs](https://github.com/Azure/azure-documentdb-node/tree/master/samples) . Dieser Artikel enthält Folgendes:

* Links zu den Aufgaben in den einzelnen Node.js-Beispielprojektdateien.
* Links zum zugehörigen API-Referenzinhalt.

**Voraussetzungen**

1. Sie benötigen ein Azure-Konto, um diese Node.js-Beispiele zu verwenden:
   * Sie können [ein Azure-Konto kostenlos erstellen](https://azure.microsoft.com/pricing/free-trial/): Sie erhalten ein Guthaben, das Sie zum Ausprobieren zahlungspflichtiger Azure-Dienste nutzen können, und Sie können das Konto selbst dann behalten und die kostenlosen Azure-Dienste wie Websites nutzen, wenn das Guthaben aufgebraucht ist. Ihre Kreditkarte wird nur dann belastet, wenn Sie Ihre Einstellungen explizit ändern und mit einer Zahlung einverstanden sind.
     * Sie können Ihre [Vorteile für Visual Studio-Abonnenten aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): Ihr Visual Studio-Abonnement beinhaltet ein monatliches Guthaben, das Sie für zahlungspflichtige Azure-Dienste nutzen können.
2. Sie benötigen zudem das [Node.js SDK](documentdb-sdk-node.md).
   
   > [!NOTE]
   > Jedes Beispiel ist eigenständig mit eigener Einrichtung und Bereinigung. Dementsprechend richten die Beispiele mehrere Aufrufe an [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection). Bei jeder Ausführung dieses Vorgangs wird Ihrem Abonnement 1 Stunde Nutzung gemäß Leistungsebene der erstellten Sammlung in Rechnung gestellt.
   > 
   > 

## <a name="database-examples"></a>Datenbankbeispiele
Die Datei [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DatabaseManagement/app.js) im Projekt [DatabaseManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DatabaseManagement) zeigt, wie die folgenden Aufgaben ausgeführt werden.

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer Datenbank](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) |[DocumentClient.createDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDatabase) |
| [Abfragen eines Kontos nach einer Datenbank](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) |[DocumentClient.queryDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDatabases) |
| [Lesen einer Datenbank nach ID](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) |[DocumentClient.readDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase) |
| [Auflisten von Datenbanken für ein Konto](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) |[DocumentClient.readDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabases) |
| [Löschen einer Datenbank](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) |[DocumentClient.deleteDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDatabase) |

## <a name="collection-examples"></a>Sammlungsbeispiele
Die Datei [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/CollectionManagement/app.js) im Projekt [CollectionManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/CollectionManagement) zeigt, wie die folgenden Aufgaben ausgeführt werden.

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer Sammlung](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) |[DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection) |
| [Lesen einer Liste aller Sammlungen in einer Datenbank](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) |[DocumentClient.readCollections](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollections) |
| [Abrufen einer Sammlung nach _self](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) |[DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection) |
| [Abrufen einer Auflistung nach ID](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) |[DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection) |
| [Abrufen der Leistungsebene einer Sammlung](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) |[DocumentQueryable.queryOffers](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryOffers) |
| [Ändern der Leistungsebene einer Sammlung](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) |[DocumentClient.replaceOffer](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceOffer) |
| [Löschen einer Sammlung](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) |[DocumentClient.deleteCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteCollection) |

## <a name="document-examples"></a>Dokumentbeispiele
Die Datei [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentManagement/app.js) im Projekt [DocumentManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DocumentManagement) zeigt, wie die folgenden Aufgaben ausgeführt werden.

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen eines Dokuments](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) |[DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDocument) |
| [Lesen des Dokumentfeeds für eine Sammlung](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) |[DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument) |
| [Lesen eines Dokuments nach ID](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) |[DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument) |
| [Lesen eines Dokuments, nur wenn das Dokument geändert wurde](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L79-L107) |[DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Abfragen von Dokumenten](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) |[DocumentClient.queryDocuments](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocuments) |
| [Ersetzen eines Dokuments](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) |[DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument) |
| [Ersetzen eines Dokuments mit bedingter ETag-Überprüfung](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L147-L164) |[DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Löschen eines Dokuments](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) |[DocumentClient.deleteDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDocument) |

## <a name="indexing-examples"></a>Indizierungsbeispiele
Die Datei [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/IndexManagement/app.js) im Projekt [IndexManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/IndexManagement) zeigt, wie die folgenden Aufgaben ausgeführt werden.

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer Sammlung mit Standardindizierung](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L657-L701) |[DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection) |
| [Manuelles Indizieren eines bestimmten Dokuments](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) |[RequestOptions.indexingDirective: 'include'](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Manuelles Ausschließen eines bestimmten Dokuments aus dem Index](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) |[RequestOptions.indexingDirective: 'exclude'](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Verwenden der verzögerten Indizierung für den Massenimport oder das Lesen umfangreicher Sammlungen](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) |[IndexingMode.Lazy](http://azure.github.io/azure-documentdb-node/global.html#IndexingMode) |
| [Einschließen bestimmter Pfade eines Dokuments in die Indizierung](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) |[IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy) |
| [Ausschließen bestimmter Pfade von der Indizierung](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) |[IndexingPolicy.ExcludedPath](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy) |
| [Zulassen einer Überprüfung eines Zeichenfolgenpfads während eines Bereichsvorgangs](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347) |[FeedOptions.EnableScanInQuery](http://azure.github.io/azure-documentdb-node/global.html#FeedOptions) |
| [Erstellen eines Bereichsindexes in einem Zeichenfolgenpfad](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) |[IndexKind.Range](http://azure.github.io/azure-documentdb-node/global.html#IndexKind), [IndexingPolicy](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy), [DocumentClient.queryDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocument) |
| [Erstellen einer Sammlung mit indexPolicy-Standardwert und anschließende Onlineaktualisierung](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) |[DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)<br> [DocumentClient.replaceCollection#replaceCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html) |

Weitere Informationen zur Indizierung finden Sie unter [Indizierungsrichtlinien für DocumentDB](documentdb-indexing-policies.md).

## <a name="server-side-programming-examples"></a>Serverseitige Programmierbeispiele
Die Datei [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/ServerSideScripts/app.js) im Projekt [ServerSideScripts](https://github.com/Azure/azure-documentdb-node/tree/master/samples/ServerSideScripts) zeigt, wie die folgenden Aufgaben ausgeführt werden.

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer gespeicherten Prozedur](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) |[DocumentClient.createStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createStoredProcedure) |
| [Ausführen einer gespeicherten Prozedur](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) |[DocumentClient.executeStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#executeStoredProcedure) |

Weitere Informationen zur serverseitigen Programmierung finden Sie unter [DocumentDB-serverseitige Programmierung: gespeicherte Prozeduren, Datenbanktrigger und benutzerdefinierte Funktionen](documentdb-programming.md).

## <a name="partitioning-examples"></a>Partitionierungsbeispiele
Die Datei [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/Partitioning/app.js) im Projekt [Partitioning](https://github.com/Azure/azure-documentdb-node/tree/master/samples/Partitioning) zeigt, wie die folgenden Aufgaben ausgeführt werden.

| Aufgabe | API-Referenz |
| --- | --- |
| [Verwenden eines HashPartitionResolver-Elements](https://github.com/Azure/azure-documentdb-node/blob/ce0fc3c4e70b0279091a1e03620a668d93a14fc2/samples/Partitioning/app.js#L53-L103) |[HashPartitionResolver](http://azure.github.io/azure-documentdb-node/HashPartitionResolver.html) |

Weitere Informationen zur Partitionierung von Daten in DocumentDB finden Sie unter [Partitionieren und Skalieren von Daten in DocumentDB](documentdb-partition-data.md).




<!--HONumber=Jan17_HO3-->


