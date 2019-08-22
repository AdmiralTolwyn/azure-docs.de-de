---
title: Arbeiten mit Suchergebnisse – Azure Search
description: Strukturieren und sortieren Sie Suchergebnisse, erfahren Sie die Dokumentanzahl, und fügen Sie den Suchergebnissen in Azure Search eine Inhaltsnavigation hinzu.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 9fa2baf64dbb35d85c55635d7522075d61bfc17d
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647702"
---
# <a name="how-to-work-with-search-results-in-azure-search"></a>Arbeiten mit Suchergebnissen in Azure Search
Dieser Artikel enthält Anleitungen dazu, wie Standardelemente einer Seite mit Suchergebnissen implementiert werden, z.B. Gesamtanzahl, Dokumentabruf, Sortierreihenfolge und Navigation. Seitenbezogene Optionen, die Daten oder Informationen zu Ihren Suchergebnissen beitragen, werden über die [Dokument durchsuchen](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)-Anforderungen angegeben, die an den Azure Search-Dienst gesendet werden. 

In der REST-API enthalten Anforderungen einen GET-Befehl, Pfad- und Abfrageparameter, denen der Dienst entnimmt, was angefordert wird und wie die Antwort zu formulieren ist. Im .NET-SDK ist die entsprechende API die [DocumentSearchResult-Klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Einige Codebeispiele beinhalten eine Web-Front-End-Schnittstelle, die Sie hier finden: [Demo-App „New York City Jobs“](https://azjobsdemo.azurewebsites.net/) und [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Eine gültige Anforderung umfasst eine Reihe von Elementen, z. B. Dienst-URL und  Pfad, HTTP-Verb, `api-version` und so weiter. Aus Platzgründen wurden die Beispiele verkürzt, um nur die Syntax hervorzuheben, die für die Paginierung wichtig sind. Weitere Informationen zur Anforderungssyntax finden Sie unter [Azure Search-Dienst-REST-API](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Gesamtanzahl der Treffer und die Seitenanzahl

Grundsätzlich werden praktisch auf allen Suchseiten die Gesamtanzahl der von einer Abfrage zurückgegebenen Ergebnisse angezeigt und anschließend die Ergebnisse in kleineren Segmenten zurückgegeben.

![][1]

In Azure Search verwenden Sie die Parameter `$count`, `$top` und `$skip` zur Rückgabe dieser Werte. Das folgende Beispiel enthält eine Beispielanforderung für die Gesamtzahl der Treffer in einem Index namens „online-catalog“, die als `@odata.count` zurückgegeben wird:

    GET /indexes/online-catalog/docs?$count=true

Dokumenten in Gruppen von jeweils 15 abrufen und ab der ersten Seite auch die Gesamtanzahl der Treffer anzeigen:

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

Zur Paginierung der Ergebnisse sind sowohl `$top` als auch `$skip` erforderlich, wobei `$top` angibt, wie viele Elemente in einer Gruppe zurückgegeben werden, und `$skip` angibt, wie viele Elemente übersprungen werden sollen. Im folgenden Beispiel werden auf jeder Seite jeweils die nächsten 15 Elemente angezeigt, wobei die Inkrementschritte durch den Parameter `$skip` angegeben werden.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Layout

Auf einer Seite mit Suchergebnissen empfiehlt es sich, eine Miniaturansicht, eine Teilmenge von Feldern und einen Link zu einer vollständigen Produktseite anzuzeigen.

 ![][2]

In Azure Search verwenden Sie `$select` und eine [Search-API-Anforderung](https://docs.microsoft.com/rest/api/searchservice/search-documents), um diese Umgebung zu implementieren.

So wird eine Teilmenge von Feldern für ein gekacheltes Layout zurückzugeben:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

Bild- und Mediendateien können nicht direkt durchsucht werden und sollten auf einer anderen Speicherplattform gespeichert werden, z.B. Azure Blob Storage, um die Kosten zu senken. Definieren Sie im Index und in den Dokumenten ein Feld, das die URL-Adresse des externen Inhalts enthält. Sie können das Feld dann als Bildverweis verwenden. Die URL zum Bild sollte im Dokument enthalten sein.

Zum Abrufen der Seite mit einer Produktbeschreibung für ein **onClick** -Ereignis verwenden Sie [Dokument suchen](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) , um den Schlüssel des abzurufenden Dokuments zu übergeben. Der Schlüssel hat den Datentyp `Edm.String`. In diesem Beispiel lautet er *246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Sortieren nach Relevanz, Bewertung oder Preis

Oft wird für die Sortierreihenfolge standardmäßig Relevanz festgelegt, aber es ist üblich, alternative Sortierreihenfolgen einfach zur Verfügung zu stellen, damit die Benutzer die vorhandenen Ergebnisse schnell in einer anderen Reihenfolge sortieren können.

 ![][3]

In Azure Search basiert die Sortierung auf dem `$orderby`-Ausdruck für alle Felder, die als `"Sortable": true.` indiziert werden. Eine `$orderby`-Klausel ist ein OData-Ausdruck. Informationen zur Syntax finden Sie unter [OData-Ausdruckssyntax für Filter und Sortierklauseln](query-odata-filter-orderby-syntax.md).

Die Relevanz ist eng mit Bewertungsprofilen verknüpft. Sie können die Standardbewertung verwenden, bei der die Reihenfolge der Ergebnisse anhand von Textanalyse und Statistiken festgelegt wird, wobei Dokumente mit mehr oder höheren Übereinstimmungen mit einem Suchbegriff eine höhere Punktzahl erhalten.

Alternative Sortierreihenfolgen werden in der Regel **onClick** -Ereignissen zugeordnet, die eine Methode aufrufen, welche die Sortierreihenfolge erstellt. Angenommen, das folgende Seitenelement sei gegeben:

 ![][4]

Sie erstellen dann eine Methode, die die ausgewählten Sortieroption als Eingabe akzeptiert und eine sortierte Liste für die Kriterien zurückgibt, die mit dieser Option verknüpft sind.

 ![][5]

> [!NOTE]
> Die Standardbewertung ist zwar für viele Szenarien ausreichend ist, aber es wird empfohlen, stattdessen die Relevanz anhand eines benutzerdefinierten Bewertungsprofil zu ermitteln. Ein benutzerdefiniertes Bewertungsprofil bietet Ihnen eine Möglichkeit, Elementen, die für Ihr Unternehmen sinnvoller sind, eine höhere Priorität zuzuordnen. Weitere Informationen finden Sie unter [Hinzufügen von Bewertungsprofilen zu einem Suchindex](index-add-scoring-profiles.md).
>

## <a name="faceted-navigation"></a>Facettennavigation

Ergebnisseiten bieten üblicherweise eine Suchnavigation, die sich häufig am seitlichen oder oberen Rand der Seite befindet. In Azure Search ermöglicht die Facettennavigation eine selbstgesteuerte Suche, die auf vordefinierten Filtern basiert. Nähere Informationen finden Sie unter [Facettennavigation in Azure Search](search-faceted-navigation.md) .

## <a name="filters-at-the-page-level"></a>Filter auf Seitenebene

Wenn Ihr Lösungsentwurf dedizierte Suchseiten für bestimmte Inhaltstypen enthält (z.B. eine Anwendung für den Online-Einzelhandel mit einer Liste von Abteilungen am oberen Rand der Seite), dann können Sie einen [Filterausdruck](search-filters.md) zusammen mit einem **onClick**-Ereignis einfügen, um eine Seite in einem vorab gefilterten Zustand zu öffnen.

Sie können einen Filter mit oder ohne Suchbegriff senden. Beispielsweise wird mit der folgenden Anforderung nach Markenname gefiltert, wobei nur die dem Filter entsprechenden Dokumente zurückgegeben werden.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

Weitere Informationen zu `$filter`-Ausdrücken finden Sie unter [Dokumente durchsuchen (Azure Search-API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

## <a name="see-also"></a>Siehe auch

- [REST-API für Azure Suchdienst](https://docs.microsoft.com/rest/api/searchservice)
- [Indexvorgänge](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Dokumentvorgänge](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Facettennavigation in Azure Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
