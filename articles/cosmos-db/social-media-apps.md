---
title: 'Azure Cosmos DB-Entwurfsmuster: Social Media Apps | Microsoft-Dokumentation'
description: Erfahren Sie mehr über ein Entwurfsmuster für soziale Netzwerke durch die Nutzung des flexiblen Speichers von Azure Cosmos DB und anderen Azure-Diensten.
keywords: Social Media Apps
services: cosmos-db
author: ealsur
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2017
ms.author: maquaran
ms.openlocfilehash: f03b2f3d295ed7d3986c45ecb80078190a2cd935
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34613882"
---
# <a name="going-social-with-azure-cosmos-db"></a>Soziale Medien mit Azure Cosmos DB
Da wir in einer hochgradig vernetzten Welt leben, werden wir irgendwann Teil **sozialer Netzwerke**. Wir verwenden diese Netzwerke, um mit Freunden, Kollegen und der Familie in Kontakt zu bleiben oder auch, um das, was uns bewegt, mit Menschen mit den gleichen Interessen zu teilen.

Als Ingenieur oder Entwickler haben Sie sich möglicherweise gefragt, wie diese Netzwerke unsere Daten speichern und verknüpfen. Möglicherweise wurden Sie sogar bereits mit dem Entwurf und der Erstellung eines neuen sozialen Netzwerks für einen bestimmten Nischenmarkt beauftragt. Spätestens dann stellt sich die große Frage: Wie werden all diese Daten gespeichert?

Nehmen wir an, dass wir ein nagelneues soziales Netzwerk erschaffen, in dem unsere Benutzer Artikel mit den dazugehörigen Medien wie Bilder, Videos oder sogar Musik posten können. Die Benutzer sollen Beiträge darüber hinaus kommentieren und durch Punktvergabe bewerten können. Über einen Feed auf der Hauptseite sollen Benutzer Beiträge lesen und mit ihnen interagieren können. Das klingt (anfänglich) nicht unbedingt komplex, der Einfachheit halber hören wir aber an dieser Stelle auf. (Wir könnten uns weiter mit von Beziehungen beeinflussten, benutzerdefinierten Feeds beschäftigen, aber das geht über das Ziel dieses Artikels hinaus).

Wie und wo speichern wir also die Daten?

Viele von Ihnen haben möglicherweise bereits Erfahrungen mit SQL-Datenbanken gesammelt, oder kennen zumindest das Konzept der [relationalen Datenmodellierung](https://en.wikipedia.org/wiki/Relational_model) und könnten daher versucht sein, so etwas Ähnliches zu zeichnen:

![Diagramm zur Veranschaulichung eines relativen relationalen Modells](./media/social-media-apps/social-media-apps-sql.png) 

Eine vollkommen normale und ordentliche Datenstruktur – die nicht skalierbar ist. 

Nicht falsch verstehen, ich habe mein ganzes Leben lang mit SQL-Datenbanken gearbeitet. Sie sind großartig, aber wie jedes Muster, jede Praxis und Softwareplattform eignen sie sich nicht uneingeschränkt für jedes Szenario.

Warum ist SQL in diesem Szenario nicht die beste Option? Sehen wir uns die Struktur eines einzelnen Beitrags an. Wenn ich den Beitrag auf einer Website oder in einer Anwendung anzeigen möchte, muss ich eine Abfrage mit ... 8 Tabellenjoins (!) ausführen, um nur einen einzigen Beitrag anzuzeigen. Stellen Sie sich nun einen Stream von Beiträgen vor, die dynamisch geladen und angezeigt werden, und Sie werden das Problem begreifen.

Wir könnten für unsere Sache natürlich eine gigantische SQL-Instanz mit ausreichend Leistung, um Tausende von Abfragen mit so vielen Verknüpfungen zu bearbeiten, verwenden. Aber, offen gestanden, warum sollten wir, wenn wir eine einfachere Lösung zur Hand haben?

## <a name="the-nosql-road"></a>Der Weg mit NoSQL
Dieser Artikel gibt eine Einführung in die Modellierung Ihrer Daten für soziale Plattformen mit der NoSQL-Datenbank [Azure Cosmos-DB](https://azure.microsoft.com/services/cosmos-db/) auf kostengünstige Weise. Dabei werden auch andere Azure Cosmos DB-Features wie die [Gremlin Graph-API](../cosmos-db/graph-introduction.md) eingesetzt. Mithilfe eines [NoSQL](https://en.wikipedia.org/wiki/NoSQL)-Ansatzes, der Datenspeicherung im JSON-Format und der [Denormalisierung](https://en.wikipedia.org/wiki/Denormalization) kann unser zuvor komplizierter Beitrag in ein einziges [Dokument](https://en.wikipedia.org/wiki/Document-oriented_database) transformiert werden:


    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

Und dies erfolgt mit einer einzigen Abfrage und keinerlei Joins. Diese Lösung ist sehr viel unkomplizierter und erfordert auch von Budgetseite weniger Ressourcen, um ein besseres Ergebnis zu erzielen.

Azure Cosmos DB stellt sicher, dass alle Eigenschaften mit der automatischen Indizierung indiziert werden. Diese kann sogar [angepasst](indexing-policies.md) werden. Der schemafreie Ansatz ermöglicht es uns, Dokumente mit verschiedenen und dynamischen Strukturen zu speichern. Wenn den Beiträgen morgen eine Liste von Kategorien oder Hashtags zugeordnet werden soll, wird Cosmos DB die neuen Dokumente mit den hinzugefügten Attributen bearbeiten, ohne zusätzlichen Arbeitsaufwand von unserer Seite.

Kommentare zu einem Beitrag können schlicht als weitere Beiträge mit einer übergeordneten Eigenschaft behandelt werden (was unsere Objektzuordnung vereinfacht). 

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

Des Weiteren können alle sozialen Interaktionen in einem separaten Objekt als Zähler gespeichert werden:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Die Erstellung von Feeds ist nunmehr bloß eine Frage der Erstellung von Dokumenten, die eine Liste von Post-IDs mit einer bestimmten Relevanzordnung aufnehmen können:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Wir könnten einen Stream mit den „neuesten Meldungen“ erstellen, in dem Beiträge nach Erstellungsdatum sortiert sind, oder einen Stream der „beliebtesten Beiträge“ mit den meisten Likes in den letzten 24 Stunden. Wir könnten sogar einen benutzerdefinierten Stream für jeden Benutzer einbauen, der auf einer Logik wie „Follower“ und „Interessen“ basiert und trotzdem immer noch eine Liste von Beiträgen wäre. Relevant ist an dieser Stelle, wie diese Listen erstellt werden. Die Leseleistung bleibt davon unberührt. Sobald wir eine dieser Listen erhalten, schicken wir mit dem [IN-Operator](sql-api-sql-query.md#WhereClause) eine einzelne Abfrage an Cosmos DB, um Seiten von Beiträgen gleichzeitig abzurufen.

Die Feeddatenströme könnten mithilfe von [Azure App Services](https://azure.microsoft.com/services/app-service/)-Hintergrundprozessen erstellt werden: [WebJobs](../app-service/web-sites-create-web-jobs.md). Nach der Erstellung eines Beitrags kann die Hintergrundverarbeitung mit [Azure Storage](https://azure.microsoft.com/services/storage/)-[Warteschlangen](../storage/queues/storage-dotnet-how-to-use-queues.md) und WebJobs mithilfe des [Azure WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) ausgelöst werden. Diese Funktionen implementieren die Verteilung der Beiträge innerhalb der Streams, basierend auf unserer eigenen benutzerdefinierten Logik. 

Punkte und Likes zu einem Beitrag können mithilfe dieser Technik verzögert verarbeitet werden, um eine letztendlich konsistente Umgebung zu erstellen.

Follower sind komplizierter. Cosmos DB beinhaltet eine Beschränkung für die maximale Dokumentgröße. Zudem kann das Lesen und Schreiben umfangreicher Dokumente die Skalierbarkeit Ihrer Anwendung beeinträchtigen. Daher sollten Sie gegebenenfalls erwägen, Follower als Dokument mit dieser Struktur zu speichern:

    {
        "id":"234d-sd23-rrf2-552d",
        "followersOf": "dse4-qwe2-ert4-aad2",
        "followers":[
            "ewr5-232d-tyrg-iuo2",
            "qejh-2345-sdf1-ytg5",
            //...
            "uie0-4tyg-3456-rwjh"
        ]
    }

Das mag bei einem Benutzer mit ein paar Tausend Followern funktionieren. Doch wenn sich ein Prominenter zu uns gesellt, wird bei diesem Ansatz eine umfangreiche Dokumentgröße und letztlich möglicherweise die Größenbeschränkung für Dokumente erreicht.

Um dieses Problem zu lösen, können wir einen kombinierten Ansatz wählen. Die Anzahl der Follower können wir als Teil des Dokuments mit der Benutzerstatistik speichern:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

Das Diagramm der Follower kann mithilfe der [Gremlin Graph-API](../cosmos-db/graph-introduction.md) von Azure Cosmos DB gespeichert werden, um [Scheitelpunkte](http://mathworld.wolfram.com/GraphVertex.html) für jeden Benutzer und [Kanten](http://mathworld.wolfram.com/GraphEdge.html), die „A-folgt-B“-Beziehungen verwalten, zu erstellen. Mithilfe der Graph-API können Sie nicht nur die Follower eines bestimmten Benutzers abrufen, sondern auch komplexere Abfragen erstellen, um auch gemeinsame Personen vorzuschlagen. Wenn wir dem Diagramm die bei Benutzern beliebten Inhaltskategorien hinzufügen, können wir weitere Funktionen einbinden, z.B. die intelligente Inhaltsermittlung, das Vorschlagen von Inhalten oder das Suchen von Personen, mit denen wir viele Gemeinsamkeit aufweisen.

Das Dokument „User Statistics“ kann weiterhin zum Erstellen von Karten auf der Benutzeroberfläche oder für schnelle Profilvorschauen verwendet werden.

## <a name="the-ladder-pattern-and-data-duplication"></a>Das „Leiter-Muster“ und Datenduplizierung
Wie Sie vielleicht bemerkt haben, kommt ein Benutzer in dem JSON-Dokument, das auf einen Beitrag verweist, mehrfach vor. Natürlich haben Sie mit der Annahme recht, dass die Informationen, die einen Benutzer darstellen, aufgrund der Denormalisierung an mehr als einem Ort vorhanden sein können.

Um schnellere Abfragen zu ermöglichen, verursachen wir also Datenduplizierung. Das Problem dieses Seiteneffekts besteht darin, dass eine Änderung der Benutzerdaten das Auffinden aller Aktivitäten, die dieser Benutzer je ausgeführt hat, und ihre Aktualisierung notwendig macht. Das klingt nicht besonders praktisch, nicht wahr?

Wir werden es lösen, indem wir die Schlüsselattribute eines Benutzers identifizieren, die wir in unserer Anwendung für jede Aktivität anzeigen. Wenn bei der visuellen Darstellung eines Beitrags in unserer Anwendung nur der Name und das Bild des Erstellers angezeigt wird, warum sollten wir dann alle Daten des Benutzers im createdBy-Attribut speichern? Wenn bei jedem Kommentar allein das Bild des Benutzers angezeigt wird, benötigen wir seine restlichen Informationen eigentlich nicht. Hier kommt das ins Spiel, was ich „Leiter-Muster“ nenne.

Nehmen wir die Benutzerinformationen als Beispiel:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

Anhand dieser Informationen können wir schnell erkennen, welche Informationen wichtig sind und welche nicht. Dadurch können wir sie „leiterhaft“ hierarchisieren:

![Diagramm eines Leiter-Musters](./media/social-media-apps/social-media-apps-ladder.png)

Die oberste Ebene ist der sogenannte UserChunk, die minimale Information, die einen Benutzer identifiziert und die für die Datenduplizierung verwendet wird. Durch die Reduzierung der doppelt vorhandenen Daten auf ausschließlich jene Informationen, die wir auch „zeigen“, minimieren wir die Gefahr massiver Aktualisierungen.

Die mittlere Ebene heißt „der Benutzer“. Dabei handelt es sich um die gesamten Daten, die für die meisten leistungsabhängigen Abfragen an Cosmos DB verwendet werden: die wichtigsten Daten also, auf die am häufigsten zugegriffen wird. Diese Informationen enthalten auch den UserChunk.

Die umfangreichste Ebene ist der „Erweiterte Benutzer“. Sie enthält alle wichtigen Benutzerinformationen und andere Daten, die nicht schnell gelesen werden müssen oder nur sporadisch verwendet werden (z.B. beim Anmeldeprozess). Diese Daten können außerhalb von Cosmos DB, in Azure SQL-Datenbanken oder Azure Storage-Tabellen gespeichert werden.

Warum empfiehlt es sich, die Benutzerinformationen aufzuteilen und sogar an verschiedenen Orten zu speichern? Weil die Abfragen von der Leistungsseite aus betrachtet immer kostenaufwendiger werden, je größer die Dokumente sind. Achten Sie auf schlanke Dokumente, die die entscheidenden Informationen für all Ihre leistungsabhängigen Abfragen für Ihr soziales Netzwerk enthalten. Speichern Sie alle weiteren Informationen für mögliche Szenarios wie eine vollständige Profilbearbeitung, Anmeldungen, sogar für Data Mining für eine Nutzungsanalyse und Big Data-Initiativen. Dabei ist nicht relevant, ob die Datensammlung für das Data Mining langsamer ist, weil es auf einer Azure SQL-Datenbank ausgeführt wird. Was zählt, ist eine schnelle und schlanke Funktionsweise für unsere Benutzer. Ein in Cosmos DB gespeicherter Benutzer würde wie folgt aussehen:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"@john"
    }

Und ein Beitrag würde wie folgt aussehen:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

Wenn eine Änderung für eines der Attribute der Chunks anfällt, sind die betroffenen Dokumente mithilfe von Abfragen, die auf die indizierten Attribute verweisen (SELECT * FROM posts p WHERE p.createdBy.id == “edited_user_id”), leicht auffindbar und die Chunks ebenso einfach zu aktualisieren.

## <a name="the-search-box"></a>Das Suchfeld
Benutzer generieren (glücklicherweise) viel Inhalt. Sie sollten daher auch in der Lage sein, Inhalte zu suchen, die nicht direkt in Ihrem Stream erscheinen, da Sie vielleicht dem jeweiligen Ersteller nicht folgen oder einen eigenen, sechs Monate alten Post suchen.

Dank und aufgrund von Azure Cosmos DB können wir mit [Azure Search](https://azure.microsoft.com/services/search/) mühelos binnen weniger Minuten und ohne eine einzige Zeile Code (bis auf den Suchprozess und die Benutzeroberfläche natürlich) ein Suchmodul implementieren.

Warum ist das so einfach?

Azure Search implementiert sogenannte [Indexer](https://msdn.microsoft.com/library/azure/dn946891.aspx). Das sind Hintergrundprozesse, die sich in Ihre Datenrepositorys einklinken und Ihre Objekte automatisch den Indizes hinzufügen, sie aktualisieren oder daraus entfernen. Unterstützt werden [Azure SQL-Datenbank-Indexer](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [Azure-Blobindexer](../search/search-howto-indexing-azure-blob-storage.md) und auch [Azure Cosmos DB-Indexer](../search/search-howto-index-documentdb.md). Die Übertragung von Informationen von Cosmos DB an Azure Search ist unkompliziert, da Informationen in beiden Fällen im JSON-Format gespeichert werden. Wir müssen lediglich [unseren Index erstellen](../search/search-create-index-portal.md) und angeben, welche Attribute aus unseren Dokumenten indiziert werden sollen. Bereits wenige Minuten später (abhängig von der Datengröße) stehen alle unsere Inhalte für die Suche zur Verfügung, für die beste Search-as-a-Service-Lösung in der Cloudinfrastruktur. 

Weitere Informationen zu Azure Search finden Sie im [Hitchhiker’s Guide to Search](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/)(Per Anhalter durch Azure Search).

## <a name="the-underlying-knowledge"></a>Die verborgenen Informationen
Nachdem all diese täglich wachsenden Inhalte gespeichert wurden, stellen Sie sich womöglich die Frage: Wie kann ich den Informationsstrom meiner Benutzer verwenden?

Die Antwort ist einfach: Lassen Sie ihn arbeiten und lernen Sie von ihm.

Doch, was können wir von ihm lernen? Einige einfache Beispiele sind die [Stimmmungsanalyse](https://en.wikipedia.org/wiki/Sentiment_analysis), die Empfehlung von Inhalten auf Grundlage der Interessen eines Benutzers oder sogar ein automatischer Inhaltsmoderator, der sicherstellt, dass alle in unserem sozialen Netzwerk veröffentlichten Inhalte familiengeeignet sind.

Nun, da ich Ihr Interesse geweckt habe, glauben Sie wahrscheinlich, dass Sie eine Promotion in Mathematik benötigen, um einfachen Datenbanken und Dateien diese Muster und Informationen zu entlocken. Aber da liegen Sie falsch.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), ein Teil der [Cortana Intelligence Suite](https://www.microsoft.com/en/server-cloud/cortana-analytics-suite/overview.aspx), ist ein vollständig verwalteter Clouddienst, mit dem Sie Workflows mithilfe von Algorithmen in einer einfachen Drag &amp; Drop-Schnittstelle erstellen, Ihre eigenen Algorithmen in [R](https://en.wikipedia.org/wiki/R_\(programming_language\)) programmieren oder einige der bereits erstellten und einsatzbereiten APIs verwenden können (z. B. [Text Analytics](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Content Moderator](https://www.microsoft.com/moderator) oder [Recommendations](https://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2)).

Um diese Machine Learning-Szenarien zu realisieren, können wir [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) zum Erfassen der Informationen aus verschiedenen Quellen und [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) verwenden, um die Informationen zu verarbeiten und eine Ausgabe zu generieren, die von Azure Machine Learning verarbeitet werden kann.

Eine andere Option ist die Verwendung von [Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services) zum Analysieren unserer Benutzerinhalte. Wir können sie nicht nur besser verstehen (durch die Analyse ihrer Texte mit der [Textanalyse-API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), sondern auch unerwünschte oder nicht jugendfreie Inhalte erkennen und mithilfe der [Maschinelles Sehen-API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api) entsprechend agieren. Die Cognitive Services bieten zahlreiche Standardlösungen, für deren Nutzung keinerlei Machine Learning-Kenntnisse erforderlich sind.

## <a name="a-planet-scale-social-experience"></a>Eine soziale Erfahrung im globalen Maßstab
Es gibt ein letztes, aber wichtiges Thema, das angesprochen werden muss : **Skalierbarkeit**. Beim Entwerfen einer Architektur ist es wichtig, dass jede Komponente unabhängig skalierbar sein muss, entweder, weil wir mehr Daten verarbeiten müssen oder weil wir einen geografisch größeren Bereich abdecken möchten (oder beides!). Glücklicherweise gestaltet sich eine so komplexe Aufgabe mit Cosmos DB ähnlich einfach wie der **schlüsselfertige** Bezug eines Hauses.

Cosmos DB unterstützt ohne Konfiguration [dynamische Partitionierung](https://azure.microsoft.com/blog/10-things-to-know-about-documentdb-partitioned-collections/) durch automatisches Erstellen von Partitionen auf der Grundlage eines angegebenen **Partitionsschlüssels** (der als eins der Attribute in Ihren Dokumenten definiert ist). Das Definieren des richtigen Partitionsschlüssels muss zur Entwurfszeit unter Berücksichtigung der verfügbaren [bewährten Methoden](../cosmos-db/partition-data.md#designing-for-partitioning) erfolgen; im Fall von sozialen Anwendungen muss Ihre Partitionierungsstrategie an der Art Ihrer Abfragen (Lesevorgänge sollten möglichst innerhalb der gleichen Partition erfolgen) und Schreibvorgänge (Vermeidung von „Hot Spots“ durch Verteilen von Schreibvorgängen auf mehrere Partitionen) ausgerichtet sein. Einige Optionen dafür sind: Partitionen auf der Grundlage eines temporalen Schlüssels (Tag/Monat/Woche), nach Inhaltskategorie, nach geografischer Region, nach Benutzer; es hängt wirklich ganz davon ab, wie Sie die Daten abfragen und in Ihrer sozialen Anwendung anzeigen. 

Ein erwähnenswerter interessanter Punkt ist, dass Cosmos DB Ihre Abfragen (einschließlich [Aggregaten](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) transparent über alle Ihre Partitionen ausführt; bei wachsendem Datenbestand brauchen Sie keinerlei Logik hinzuzufügen.

Im Lauf der Zeit werden der Datenverkehr und der Ressourcenverbrauch (in [RUs](request-units.md) (Request Units, Anforderungseinheiten) gemessen) irgendwann zunehmen. Mit wachsender Benutzerbasis erfolgen mehr Lese- und Schreibvorgänge, und Ihre Benutzer werden mehr Inhalte erstellen und lesen; die Möglichkeit zum **Skalieren des Durchsatzes** ist essenziell. Das Erhöhen Ihrer RUs ist sehr einfach, es kann mit einigen Klicks im Azure-Portal oder durch [Ausgeben von Befehlen über die API](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer) erfolgen.

![Vertikales Skalieren und Definieren eines Partitionsschlüssels](./media/social-media-apps/social-media-apps-scaling.png)

Was geschieht, wenn sich die Situation positiv entwickelt, und Benutzer aus einer anderen Region, einem anderen Land oder von einem anderen Kontinent auf Ihre Plattform aufmerksam werden und beginnen, sie zu verwenden – welche Überraschung!

Aber Moment mal... Sie stellen bald fest, dass die Benutzerfreundlichkeit Ihrer Plattform für sie nicht optimal ist; sie sind zu weit von Ihrer Betriebsregion entfernt, sodass unmögliche Wartezeiten auftreten, und Sie möchten diese Benutzer nicht verlieren. Wenn es doch nur eine einfache Möglichkeit zum **Erweitern Ihrer globalen Reichweite** gäbe... aber es gibt sie!

Mithilfe von Cosmos DB können Sie [Ihre Daten global replizieren](../cosmos-db/tutorial-global-distribution-sql-api.md), das auch noch transparent und mit nur ein paar Mausklicks; und die Auswahl der verfügbaren Regionen erfolgt automatisch anhand des [Clientcodes](../cosmos-db/tutorial-global-distribution-sql-api.md). Dies bedeutet zugleich, dass Sie mit [mehreren Failoverregionen](regional-failover.md) arbeiten können. 

Wenn Sie Ihre Daten global replizieren, müssen Sie zugleich sicherstellen, dass dies allen Ihren Kunden zugute kommt. Wenn Sie ein Web-Front-End verwenden oder von mobilen Clients aus auf APIs zugreifen, können Sie [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) bereitstellen und Ihre Azure App Service-Instanz in allen gewünschten Regionen klonen. Verwenden Sie eine Leistungskonfiguration zur Unterstützung Ihrer erweiterten globalen Abdeckung. Wenn Ihre Clients auf Ihr Front-End oder Ihre APIs zugreifen, werden sie zum nächstgelegenen App Service weitergeleitet, der seinerseits eine Verbindung mit dem lokalen Cosmos DB-Replikat herstellt.

![Hinzufügen von globaler Abdeckung zu Ihrer sozialen Plattform](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Zusammenfassung
Dieser Artikel soll die alternative, vollständige Erstellung sozialer Netzwerke in Azure beleuchten. Diese bietet kostengünstige Dienste und hervorragende Ergebnisse, da sie die Verwendung einer mehrstufigen Speicherlösung und „Leiter“-Datenverteilung begünstigt.

![Interaktionsdiagramm zwischen Azure-Diensten für soziale Netzwerke](./media/social-media-apps/social-media-apps-azure-solution.png)

In Wahrheit gibt es keine Patentlösung für diese Art von Szenarios. Vielmehr sind es die Synergieeffekte aus einer Kombination hervorragender Dienste, die zu einem großartigen Nutzererlebnis verhelfen: die Geschwindigkeit und die Freiheit von Azure Cosmos DB, die eine hervorragende soziale Anwendung ermöglichen, die Intelligenz hinter einer erstklassigen Suchlösung wie Azure Search, die Flexibilität von Azure App Services, nicht nur sprachunabhängige Anwendungen zu hosten, sondern sogar leistungsfähige Hintergrundprozesse. Dazu kommen der erweiterbare Azure Storage, die Azure SQL-Datenbank zum Speichern riesiger Datenmengen und die analytische Leistungsfähigkeit von Azure Machine Learning zur Gewinnung von Wissen und Intelligenz, was als Feedback für unsere Prozesse dienen und uns helfen kann, den richtigen Benutzern die richtigen Inhalte zur Verfügung zu stellen.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu den Anwendungsfällen für Cosmos DB finden Sie unter [Häufige Cosmos DB-Anwendungsfälle](use-cases.md).
