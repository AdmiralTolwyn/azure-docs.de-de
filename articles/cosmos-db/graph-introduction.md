---
title: Einführung in die Graph-APIs von Azure Cosmos DB | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie anhand der Graph-Abfragesprache Gremlin von Apache TinkerPop umfangreiche Diagramme mit niedrigen Latenzen durch Azure Cosmos DB speichern, abfragen und traversieren können.
services: cosmos-db
author: LuisBosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: overview
ms.date: 01/05/2017
ms.author: lbosq
ms.openlocfilehash: ee6e3adc3300178164b83ee1f8dc2ab307eec45b
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081211"
---
# <a name="introduction-to-azure-cosmos-db-graph-api"></a>Einführung in die Graph-API von Azure Cosmos DB

[Azure Cosmos DB](introduction.md) ist der global verteilte Datenbankdienst von Microsoft mit mehreren Modellen für unternehmenskritische Anwendungen. Azure Cosmos DB bietet die folgenden Features, die alle durch [branchenführende SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/) abgedeckt sind:

* [Globale, sofort einsatzbereite Verteilung](distribute-data-globally.md)
* [Flexible Skalierung für Durchsatz und Speicher](partition-data.md) weltweit
* Einstellige Latenzzeiten im Millisekundenbereich im 99. Perzentil
* [Fünf wohl definierte Konsistenzebenen](consistency-levels.md)
* Garantierte Hochverfügbarkeit 

Azure Cosmos DB [indiziert automatisch Daten](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), sodass Sie sich nicht mit der Schema- und Indexverwaltung befassen müssen. Es unterstützt mehrere Datenmodelle – Dokumente, Schlüsselwerte, Diagramme und spaltenorientierte Datenmodelle.

Es wird empfohlen, sich das folgende Video anzusehen, in dem Kirill Gavrylyuk Ihnen die ersten Schritte mit Graphen in Azure Cosmos DB erläutert:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Graphs-with-Azure-Cosmos-DB-Gremlin-API/player]
> 
> 

Die Graph-API von Azure Cosmos DB bietet:

- Graph-Modellierung
- Traversal-APIs
- Globale, sofort einsatzbereite Verteilung
- Flexible Skalierung von Speicher und Durchsatz mit Leselatenzen von weniger als 10 ms und weniger als 15 ms beim 99. Perzentil
- Automatische Indizierung mit sofortiger Abfrageverfügbarkeit
- Einstellbare Konsistenzebenen
- Umfassende SLAs – einschließlich einer SLA mit einer Verfügbarkeit von 99,99 Prozent für alle Konten mit einer einzelnen Region und für alle Konten mit mehreren Regionen und gelockerter Konsistenz sowie einer Leseverfügbarkeit von 99,999 Prozent für alle Datenbankkonten mit mehreren Regionen.

Zum Abfragen von Azure Cosmos DB können Sie die Graphdurchlauf-Sprache [Apache TinkerPop](http://tinkerpop.apache.org) oder [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) verwenden.

Dieser Artikel enthält eine Übersicht über die Graph-API von Azure Cosmos DB und erläutert, wie Sie diese zum Speichern von umfangreichen Diagrammen mit Milliarden von Vertices und Edges verwenden können. Sie können die Diagramme mit einer Latenz im Millisekundenbereich abfragen und die Diagrammstruktur und das Schema entwickeln.

## <a name="graph-database"></a>Diagrammdatenbank
Daten sind in der Praxis naturgemäß vernetzt. Bei der konventionellen Datenmodellierung liegt der Schwerpunkt auf Entitäten. Bei vielen Anwendungen besteht auch die Notwendigkeit, sowohl Entitäten als auch Beziehungen natürlich zu modellieren.

Ein [Diagramm](http://mathworld.wolfram.com/Graph.html) ist eine Struktur aus [Vertices](http://mathworld.wolfram.com/GraphVertex.html) und [Edges](http://mathworld.wolfram.com/GraphEdge.html). Sowohl Vertices als auch Edges können eine beliebige Anzahl von Eigenschaften aufweisen. Als Vertices werden diskrete Objekte wie etwa eine Person, ein Ort oder ein Ereignis bezeichnet. Edges bezeichnen Beziehungen zwischen Vertices. Beispielsweise könnte eine Person eine andere Person kennen, an einem Ereignis beteiligt sein und sich vor Kurzem an einem Ort befunden haben. Eigenschaften geben Informationen zu den Vertices und Edges an. Zu Eigenschaften zählen beispielsweise Name und Alter eines Vertex sowie ein Edge, der einen Zeitstempel und/oder eine Gewichtung aufweist. Dieses Modell wird offiziell als [Eigenschaftsdiagramm](http://tinkerpop.apache.org/docs/current/reference/#intro) bezeichnet. Azure Cosmos DB unterstützt das Eigenschaftsdiagrammmodell.

Das folgende Beispieldiagramm stellt beispielhaft die Beziehungen zwischen Personen, Mobilgeräten, Interessen und Betriebssystemen dar:

![Beispieldatenbank mit Personen, Geräten und Interessen](./media/graph-introduction/sample-graph.png)

Diagramme sind nützlich, um sich einen Überblick über eine große Bandbreite an Datasets in Wissenschaft, Technologie und Wirtschaft zu verschaffen. Mithilfe von Diagrammdatenbanken können Diagramme natürlich modelliert und gespeichert werden, sodass sie sich in zahlreichen Szenarien nützlich einsetzen lassen. Bei Diagrammdatenbanken handelt es sich in der Regel um NoSQL-Datenbanken, da diese Anwendungsfälle häufig auch Schemaflexibilität und schnelle Iterationen erfordern.

Diagramme bieten eine neuartige und leistungsstarke Datenmodellierungsmethode. Dies allein ist jedoch kein ausreichender Grund für die Verwendung einer Diagrammdatenbank. Bei vielen Anwendungsfällen und -mustern im Zusammenhang mit Diagrammtraversierungen übertreffen Diagramme konventionelle SQL- und NoSQL-Datenbanken um ein Vielfaches. Dieser Leistungsunterschied macht sich umso deutlicher bemerkbar, wenn mehr als eine Beziehung (z.B. Freunde von Freunden) traversiert wird.

Sie können die von Diagrammdatenbanken bereitgestellte Funktion für schnelle Traversierungen mit Diagrammalgorithmen (z.B. Tiefensuche, Breitensuche, Dijkstra-Algorithmus) kombinieren, um Probleme in verschiedenen Domänen wie sozialen Netzwerken, Inhaltsverwaltung, Geodaten und Empfehlungen zu beheben.

## <a name="planet-scale-graphs-with-azure-cosmos-db"></a>Weltweite Diagramme mit Azure Cosmos DB
Azure Cosmos DB ist eine vollständig verwaltete Diagrammdatenbank, die eine globale Verteilung, flexible Skalierung von Speicher und Durchsatz, automatische Indizierung und Abfrage sowie einstellbare Konsistenzebenen bietet und den TinkerPop-Standard unterstützt.

![Architektur von Azure Cosmos DB-Diagrammen](./media/graph-introduction/cosmosdb-graph-architecture.png)

Azure Cosmos DB bietet im Vergleich zu anderen Diagrammdatenbanken auf dem Markt folgende einzigartige Features:

* Flexibel skalierbarer Durchsatz und Speicher

 Diagramme müssen in der Praxis über die Kapazität eines einzelnen Servers hinweg skaliert werden. Mit Azure Cosmos DB können Sie Ihre Diagramme nahtlos über mehrere Server hinweg skalieren. Zudem können Sie den Durchsatz Ihres Diagramms unabhängig basierend auf Ihren Zugriffsmustern skalieren. Azure Cosmos DB unterstützt Diagrammdatenbanken, die auf eine nahezu unbegrenzte Speichergröße und einen nahezu unbegrenzten bereitgestellten Durchsatz skaliert werden können.

* Replikation in mehreren Regionen

 Azure Cosmos DB repliziert Ihre Diagrammdaten transparent in allen Regionen, die Sie Ihrem Konto zugeordnet haben. Die Replikation ermöglicht es Ihnen, Anwendungen zu entwickeln, die globalen Zugriff auf Daten erfordern. In den Bereichen Konsistenz, Verfügbarkeit und Leistung und den entsprechenden Garantien gibt es dabei gewisse Nachteile. Azure Cosmos DB bietet ein transparentes regionales Failover mit Multi-Homing-APIs. Sie können Durchsatz und Speicher weltweit flexibel skalieren.

* Schnelle Abfragen und Traversierungen mit der vertrauten Gremlin-Syntax

 Speichern Sie heterogene Vertices und Edges, und führen Sie Abfragen dieser Dokumente über eine vertraute Gremlin-Syntax durch. Azure Cosmos DB nutzt eine sperrfreie, protokollstrukturierte Indizierungstechnologie für die gleichzeitige Ausführung zahlreicher Vorgänge, um sämtliche Inhalte automatisch zu indizieren. Diese Funktion ermöglicht umfassende Echtzeitabfragen und -traversierungen, ohne Schemahinweise, sekundäre Indizes oder Ansichten festlegen zu müssen. Mehr erfahren Sie unter [Abfragegraphen mithilfe von Gremlin](gremlin-support.md).

* Vollständige Verwaltung

 Sie müssen sich nicht mehr mit der Verwaltung von Datenbanken und Rechenressourcen befassen. Dank des vollständig verwalteten Microsoft Azure-Diensts müssen Sie sich nicht mit der Verwaltung virtueller Computer, der Bereitstellung und Konfiguration von Software, der Skalierung oder mit komplexen Datenebenenupgrades herumschlagen. Alle Diagramme werden automatisch gesichert und vor regionalen Ausfällen geschützt. Sie können einfach Azure Cosmos DB-Konten hinzufügen und nach Bedarf Kapazitäten bereitstellen. Dies ermöglicht es Ihnen, sich auf Ihre Anwendung zu konzentrieren, ohne sich mit dem Betrieb und der Verwaltung der Datenbank aufhalten zu müssen.

* Automatische Indizierung

 Alle Eigenschaften in Knoten und Edges im Graphen werden von Azure Cosmos DB automatisch indiziert, ohne dass ein Schema oder die Erstellung sekundärer Indizes erwartet oder gefordert wird.

* Kompatibilität mit Apache TinkerPop

 Azure Cosmos DB unterstützt systemintern den Open-Source-Standard Apache TinkerPop und kann in andere TinkerPop-fähige Diagrammsysteme integriert werden. So können Sie mühelos Migrationen von einer anderen Diagrammdatenbank wie Titan oder Neo4j durchführen oder Azure Cosmos DB mit Graphanalyse-Frameworks wie Apache Spark GraphX verwenden.

* Einstellbare Konsistenzebenen

 Die Konsistenz kann über fünf klar definierte Ebenen abgestimmt werden, um für ein ausgewogenes Verhältnis zwischen Konsistenz und Leistung zu sorgen. Für Abfragen und Lesevorgänge bietet Azure Cosmos DB fünf verschiedene Konsistenzebenen – „stark“, „begrenzte Veraltung“, „Sitzung“, „Präfixkonsistenz“ und „letztlich“. Mit diesen granularen, wohldefinierten Konsistenzebenen können fundierte Kompromisse zwischen Konsistenz, Verfügbarkeit und Latenz geschlossen werden. Weitere Informationen finden Sie unter [Einstellbare Datenkonsistenzebenen in Azure Cosmos DB](consistency-levels.md).

Azure Cosmos DB bietet zudem die Möglichkeit, mehrere Modelle wie Dokumente und Diagramme in denselben Containern bzw. Datenbanken zu verwenden. Sie können eine Dokumentsammlung verwenden, um Diagrammdaten zusammen mit Dokumenten zu speichern. Sowohl mit SQL-Abfragen über JSON als auch mit Gremlin Abfragen können Sie dieselben Daten wie ein Diagramm abfragen.

## <a name="get-started"></a>Erste Schritte
Sie können die Azure-Befehlszeilenschnittstelle (CLI), Azure PowerShell oder das Azure-Portal mit Unterstützung für die Graph-API verwenden, um Azure Cosmos DB-Konten zu erstellen. Nach der Erstellung von Konten wird im Azure-Portal ein Dienstendpunkt wie `https://<youraccount>.gremlin.cosmosdb.azure.com` bereitgestellt, der ein WebSocket-Front-End für Gremlin bietet. Um eine Verbindung mit diesem Endpunkt herzustellen und Anwendungen in Java, Node.js oder einem beliebigen Gremlin-Clienttreiber zu erstellen, können Sie Ihre TinkerPop-kompatiblen Tools wie die [Gremin-Konsole](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) konfigurieren.

In der folgenden Tabelle werden gängige Gremlin-Treiber aufgeführt, die Sie für Azure Cosmos DB verwenden können:

| Download | Dokumentation | Erste Schritte |
| --- | --- | --- |
| [.NET](http://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [Gremlin.NET auf GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Erstellen von Graph mithilfe von .NET](create-graph-dotnet.md) |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](http://tinkerpop.apache.org/javadocs/current/full/) | [Erstellen von Graph mithilfe von Java](create-graph-java.md) |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript auf GitHub](https://github.com/jbmusso/gremlin-javascript) | [Erstellen von Graph mithilfe von Node.js](create-graph-nodejs.md) |
| [Python](http://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python auf GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Erstellen von Graph mithilfe von Python](create-graph-python.md) |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP auf GitHub](https://github.com/PommeVerte/gremlin-php) | [Erstellen von Graph mithilfe von PHP](create-graph-php.md) |
| [Gremlin-Konsole](https://tinkerpop.apache.org/downloads.html) | [TinkerPop-Dokumente](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Erstellen von Graph mithilfe der Gremlin-Konsole](create-graph-gremlin-console.md) |

## <a name="scenarios-for-graph-support-of-azure-cosmos-db"></a>Szenarien für die Diagrammunterstützung von Azure Cosmos DB
Im Folgenden werden einige Szenarien vorgestellt, in denen die Diagrammunterstützung von Azure Cosmos DB verwendet werden kann:

* Soziale Netzwerke

 Durch die Kombination von Daten über Ihre Kunden und deren Interaktionen mit anderen Personen können Sie individuelle Erlebnisse schaffen, das Kundenverhalten vorhersagen oder Personen mit ähnlichen Interessen vernetzen. Azure Cosmos DB kann zum Verwalten sozialer Netzwerke sowie zum Nachverfolgen von Kundenpräferenzen und -daten verwendet werden.

* Empfehlungs-Engines

 Dieses Szenario kommt häufig im Einzelhandel vor. Durch die Kombination von Informationen zu Produkten, Benutzern und Benutzerinteraktionen (z.B. Einkäufe, Surfverhalten oder Bewertungen eines Artikels) können Sie benutzerdefinierte Empfehlungen erstellen. Die geringe Latenz, flexible Skalierung und native Diagrammunterstützung von Azure Cosmos DB sind für die Modellierung solcher Interaktionen ideal.

* Geodaten

 Zahlreiche Anwendungen in den Bereichen Telekommunikation, Logistik und Reiseplanung müssen einen bestimmten Ort in einer bestimmten Region oder die kürzeste bzw. optimale Route zwischen zwei Orten finden. Azure Cosmos DB ist die ideale Lösung für derartige Probleme.

* Internet der Dinge

 Durch die Modellierung von Netzwerken und Verbindungen zwischen IoT-Geräten als Graphen können Sie sich eine bessere Übersicht über den Status Ihrer Geräte und Ressourcen verschaffen. Sie können auch herausfinden, inwiefern sich Änderungen an einem Teil des Netzwerks möglicherweise auf andere Teile auswirken können.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Diagrammunterstützung in Azure Cosmos DB finden Sie durch folgende Ressourcen:

* Erste Schritte durch das [Tutorial zum Azure Cosmos DB-Diagramm](create-graph-dotnet.md)
* Erfahren Sie mehr über das [Abfragen von Graphen in Azure Cosmos DB mithilfe von Gremlin](gremlin-support.md).
