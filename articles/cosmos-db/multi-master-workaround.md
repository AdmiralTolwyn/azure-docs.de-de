---
title: Ausführen von Schreib- und Lesevorgängen in mehreren Regionen durch Auswahl des richtigen Partitionsschlüssel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Anwendungsarchitekturen mit lokalen Lese- und Schreibvorgängen über mehrere geografische Regionen hinweg mit Azure Cosmos DB entwerfen, indem Sie einen Partitionsschlüssel auswählen.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: conceptual
ms.date: 06/6/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 18f036a259bbec98382927ad1d9e8f654b56850b
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850360"
---
# <a name="perform-multi-region-writes-and-reads-by-choosing-the-right-partitioning-key"></a>Ausführen von Schreib- und Lesevorgängen in mehreren Regionen durch Auswahl des richtigen Partitionsschlüssel

Azure Cosmos DB unterstützt die sofort einsetzbare [globale Replikation](distribute-data-globally.md), bei der Sie Daten mit kürzer Wartezeit beim Zugriff an allen Orten der Workload auf mehrere Regionen verteilen können. Dieses Modell wird häufig für Herausgeber-/Verbraucherworkloads verwendet, bei denen sich ein „Writer“ in einer bestimmten geografischen Region und global verteilte Leser in anderen Regionen (Leseregionen) befinden. 

Sie können auch die Unterstützung für die globale Replikation von Cosmos DB nutzen, um Anwendungen zu erstellen, bei denen Writer und Leser global verteilt sind. In diesem Dokument wird ein Muster beschrieben, das den lokalen Schreib- und Lesezugriff für verteilte Writer per Azure Cosmos DB ermöglicht.

## <a id="ExampleScenario"></a>Inhaltsveröffentlichung – Beispielszenario
Wir sehen uns ein Szenario aus der Praxis an, um zu beschreiben, wie Sie mit Cosmos DB global verteilte Lese-/Schreibmuster mit mehreren Regionen bzw. Mastern verwenden können. Stellen Sie sich eine Plattform für die Inhaltsveröffentlichung vor, die auf Azure Cosmos DB basiert. Hier sind einige Anforderungen aufgeführt, die von dieser Plattform erfüllt werden müssen, um sowohl für Herausgeber als auch für Verbraucher für eine hohe Benutzerfreundlichkeit zu sorgen.

* Autoren und Abonnenten sind weltweit verteilt 
* Autoren müssen Artikel in ihrer lokalen (nächstgelegenen) Region veröffentlichen (schreiben)
* Autoren verfügen für ihre Artikel über Leser/Abonnenten, die weltweit verteilt sind. 
* Abonnenten sollten eine Benachrichtigung erhalten, wenn neue Artikel veröffentlicht werden.
* Abonnenten müssen Artikel aus ihrer lokalen Region lesen können. Außerdem sollte es möglich sein, Rezensionen für diese Artikel hinzuzufügen. 
* Alle Benutzer, einschließlich des Autors der Artikel, sollten alle Rezensionen anzeigen können, die an Artikel einer lokalen Region angefügt sind. 

Wenn wir von Millionen von Verbrauchern und Herausgebern mit Milliarden von Artikeln ausgehen, wird schnell klar, dass wir die Skalierungsprobleme lösen und den lokalen Zugriff sicherstellen müssen. Wie bei den meisten Problemen mit der Skalierbarkeit liegt die Lösung in einer guten Partitionierungsstrategie. Als Nächstes sehen wir uns an, wie Artikel, Rezensionen und Benachrichtigungen als Dokumente modelliert werden, Azure Cosmos DB-Konten konfiguriert werden und eine Datenzugriffsschicht implementiert wird. 

Weitere Informationen zur Partitionierung und zu Partitionsschlüsseln finden Sie unter [Partitionieren und Skalieren von Daten in Azure Cosmos DB](partition-data.md).

## <a id="ModelingNotifications"></a>Modellieren von Benachrichtigungen
Benachrichtigungen sind Datenfeeds für einen bestimmten Benutzer. Daher gelten die Zugriffsmuster für Benachrichtigungsdokumente immer im Kontext eines einzelnen Benutzers. Beispielsweise „posten Sie eine Benachrichtigung für einen Benutzer“ oder „rufen alle Benachrichtigungen für einen bestimmten Benutzer ab“. Die optimale Wahl eines Partitionierungsschlüssels für diesen Typ wäre also `UserId`.

    class Notification 
    { 
        // Unique ID for Notification. 
        public string Id { get; set; }

        // The user Id for which notification is addressed to. 
        public string UserId { get; set; }

        // The partition Key for the resource. 
        public string PartitionKey 
        { 
            get 
            { 
                return this.UserId; 
            }
        }

        // Subscription for which this notification is raised. 
        public string SubscriptionFilter { get; set; }

        // Subject of the notification. 
        public string ArticleId { get; set; } 
    }

## <a id="ModelingSubscriptions"></a>Modellieren von Abonnements
Abonnements können für verschiedene Kriterien erstellt werden, z.B. eine bestimmte Kategorie von gewünschten Artikeln oder einen bestimmten Herausgeber. Daher ist `SubscriptionFilter` eine gute Wahl für den Partitionsschlüssel.

    class Subscriptions 
    { 
        // Unique ID for Subscription 
        public string Id { get; set; }

        // Subscription source. Could be Author | Category etc. 
        public string SubscriptionFilter { get; set; }

        // subscribing User. 
        public string UserId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.SubscriptionFilter; 
            } 
        } 
    }

## <a id="ModelingArticles"></a>Modellieren von Artikeln
Nachdem ein Artikel über Benachrichtigungen identifiziert wurde, basieren die nachfolgenden Abfragen normalerweise auf der `Article.Id`. Die Wahl von `Article.Id` als Partitionsschlüssel ermöglicht daher die beste Verteilung für die Speicherung von Artikeln in einer Azure Cosmos DB-Sammlung. 

    class Article 
    { 
        // Unique ID for Article 
        public string Id { get; set; }
        
        public string PartitionKey 
        { 
            get 
            { 
                return this.Id; 
            } 
        }
        
        // Author of the article
        public string Author { get; set; }

        // Category/genre of the article
        public string Category { get; set; }

        // Tags associated with the article
        public string[] Tags { get; set; }

        // Title of the article
        public string Title { get; set; }
        
        //... 
    }

## <a id="ModelingReviews"></a>Modellieren von Rezensionen
Wie Artikel auch, werden Rezensionen meist in Zusammenhang mit einem Artikel geschrieben und gelesen. Wenn Sie `ArticleId` als Partitionsschlüssel wählen, erzielen Sie die beste Verteilung und einen effizienten Zugriff für die Rezensionen eines Artikels. 

    class Review 
    { 
        // Unique ID for Review 
        public string Id { get; set; }

        // Article Id of the review 
        public string ArticleId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.ArticleId; 
            } 
        }
        
        //Reviewer Id 
        public string UserId { get; set; }
        public string ReviewText { get; set; }
        
        public int Rating { get; set; } }
    }

## <a id="DataAccessMethods"></a>Datenzugriffsschicht-Methoden
Nun sehen wir uns die wichtigsten Methoden für den Datenzugriff an, die wir implementieren müssen. Dies ist die Liste mit den Methoden, die für `ContentPublishDatabase` benötigt werden:

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a id="Architecture"></a>Azure DB Cosmos-Kontokonfiguration
Zum Sicherstellen von lokalen Lese- und Schreibvorgängen müssen wir die Daten nicht nur nach dem Partitionsschlüssel partitionieren, sondern auch basierend auf dem geografischen Zugriffsmuster für die Regionen. Das Modell basiert darauf, dass für jede Region ein Azure Cosmos DB-Datenbankkonto mit Georeplikation verwendet wird. Bei zwei Regionen lautet die Einrichtung für Schreibvorgänge in mehreren Regionen beispielsweise wie folgt:

| Kontoname | Schreibregion | Leseregion |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

Im folgenden Diagramm ist dargestellt, wie Lese- und Schreibvorgänge mit dieser Einrichtung in einer typischen Anwendung durchgeführt werden:

![Azure Cosmos DB-Architektur mit mehreren Mastern](./media/multi-master-workaround/multi-master.png)

Dieser Codeausschnitt zeigt, wie Sie die Clients auf einer Datenzugriffsschicht für die Region `West US` initialisieren.
    
    ConnectionPolicy writeClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    writeClientPolicy.PreferredLocations.Add(LocationNames.WestUS);
    writeClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

    DocumentClient writeClient = new DocumentClient(
        new Uri("https://contentpubdatabase-usa.documents.azure.com"), 
        writeRegionAuthKey,
        writeClientPolicy);

    ConnectionPolicy readClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    readClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);
    readClientPolicy.PreferredLocations.Add(LocationNames.WestUS);

    DocumentClient readClient = new DocumentClient(
        new Uri("https://contentpubdatabase-europe.documents.azure.com"),
        readRegionAuthKey,
        readClientPolicy);

Bei der obigen Einrichtung kann die Datenzugriffsschicht alle Schreibvorgänge basierend auf der jeweiligen Bereitstellung an das lokale Konto weiterleiten. Lesevorgänge werden durchgeführt, indem aus beiden Konten gelesen wird, um die globale Übersicht über die Daten zu erhalten. Dieser Ansatz kann beliebig auf die erforderliche Anzahl von Regionen erweitert werden. Als Beispiel ist hier eine Einrichtung mit drei geografischen Regionen angegeben:

| Kontoname | Schreibregion | Leseregion 1 | Leseregion 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a id="DataAccessImplementation"></a>Implementierung der Datenzugriffsschicht
Wir sehen uns nun die Implementierung der Datenzugriffsschicht (DAL) für eine Anwendung mit zwei Schreibregionen an. Für die DAL müssen die folgenden Schritte implementiert werden:

* Erstellen Sie für jedes Konto mehrere Instanzen von `DocumentClient`. Bei zwei Regionen weist jede DAL-Instanz ein `writeClient`- und ein `readClient`-Element auf. 
* Konfigurieren Sie die Endpunkte für `writeclient` und `readClient` basierend auf der bereitgestellten Region der Anwendung. Für die in `West US` bereitgestellte DAL wird beispielsweise `contentpubdatabase-usa.documents.azure.com` zum Durchführen von Schreibvorgängen verwendet. Für die in `NorthEurope` bereitgestellte DAL wird `contentpubdatabase-europ.documents.azure.com` für Schreibvorgänge verwendet.

Bei der obigen Einrichtung können die Datenzugriffsmethoden implementiert werden. Für Schreibvorgänge erfolgt die Weiterleitung an das entsprechende `writeClient`-Element.

    public async Task CreateSubscriptionAsync(string userId, string category)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Subscriptions
        {
            UserId = userId,
            SubscriptionFilter = category
        });
    }

    public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Review
        {
            UserId = userId,
            ArticleId = articleId,
            ReviewText = reviewText,
            Rating = rating
        });
    }

Zum Lesen von Benachrichtigungen und Rezensionen müssen Sie aus beiden Regionen lesen und die Ergebnisse wie im folgenden Codeausschnitt verknüpfen:

    public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId)
    {
        IDocumentQuery<Notification> writeAccountNotification = (
            from notification in this.writeClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();
        
        IDocumentQuery<Notification> readAccountNotification = (
            from notification in this.readClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();

        List<Notification> notifications = new List<Notification>();

        while (writeAccountNotification.HasMoreResults || readAccountNotification.HasMoreResults)
        {
            IList<Task<FeedResponse<Notification>>> results = new List<Task<FeedResponse<Notification>>>();

            if (writeAccountNotification.HasMoreResults)
            {
                results.Add(writeAccountNotification.ExecuteNextAsync<Notification>());
            }

            if (readAccountNotification.HasMoreResults)
            {
                results.Add(readAccountNotification.ExecuteNextAsync<Notification>());
            }

            IList<FeedResponse<Notification>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Notification> feed in notificationFeedResult)
            {
                notifications.AddRange(feed);
            }
        }
        return notifications;
    }

    public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId)
    {
        IDocumentQuery<Review> writeAccountReviews = (
            from review in this.writeClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();
        
        IDocumentQuery<Review> readAccountReviews = (
            from review in this.readClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();

        List<Review> reviews = new List<Review>();
        
        while (writeAccountReviews.HasMoreResults || readAccountReviews.HasMoreResults)
        {
            IList<Task<FeedResponse<Review>>> results = new List<Task<FeedResponse<Review>>>();

            if (writeAccountReviews.HasMoreResults)
            {
                results.Add(writeAccountReviews.ExecuteNextAsync<Review>());
            }

            if (readAccountReviews.HasMoreResults)
            {
                results.Add(readAccountReviews.ExecuteNextAsync<Review>());
            }

            IList<FeedResponse<Review>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Review> feed in notificationFeedResult)
            {
                reviews.AddRange(feed);
            }
        }

        return reviews;
    }

Indem Sie einen guten Partitionierungsschlüssel und eine Partitionierung wählen, die auf einem statischen Konto basiert, können Sie also lokale Schreib- und Lesevorgänge in mehreren Regionen mit Azure Cosmos DB erreichen.

## <a id="NextSteps"></a>Nächste Schritte
In diesem Artikel wurde beschrieben, wie Sie mit Azure Cosmos DB global verteilte Lese-/Schreibmuster für mehrere Regionen verwenden können, und als Beispiel wurde die Inhaltsveröffentlichung genutzt.

* Erfahren Sie, wie Azure Cosmos DB die [globale Verteilung](distribute-data-globally.md) unterstützt.
* Informieren Sie sich über [automatische und manuelle Failover in Azure Cosmos DB](regional-failover.md).
* Erfahren Sie mehr über die [globale Konsistenz bei Azure Cosmos DB](consistency-levels.md).
* Entwickeln mit mehreren Regionen mit der [SQL-API für Azure Cosmos DB](tutorial-global-distribution-sql-api.md)
* Entwickeln Sie mit mehreren Regionen mit der [MongoDB-API für Azure Cosmos DB](tutorial-global-distribution-MongoDB.md).
* Entwickeln Sie mit mehreren Regionen mit der [Tabellen-API für Azure Cosmos DB](tutorial-global-distribution-table.md).
