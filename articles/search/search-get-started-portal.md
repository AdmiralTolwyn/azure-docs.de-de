---
title: Azure Search-Tutorial zur Indizierung, Abfrage und Filterung über das Portal | Microsoft-Dokumentation
description: Generieren Sie über das Azure-Portal auf der Grundlage vordefinierter Beispieldaten einen Index in Azure Search. Machen Sie sich unter anderem mit Volltextsuche, Filtern, Facets, Fuzzysuche und Geosuche vertraut.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: heidist
ms.openlocfilehash: 9ee88b254131b40fdf1e01b771afa92127734e18
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2018
---
# <a name="create-query-and-filter-an-azure-search-index-in-the-portal"></a>Erstellen, Abfragen und Filtern eines Azure Search-Index im Portal

Generieren Sie über das Azure-Portal mithilfe des **Datenimport-Assistenten** auf der Grundlage eines vordefinierten Beispieldatasets im Handumdrehen einen Azure Search-Index. Verwenden Sie den **Suchexplorer**, um sich mit Volltextsuche, Filtern, Facets, Fuzzysuche und Geosuche vertraut zu machen.  

Diese codefreie Einführung erleichtert Ihnen den Einstieg mit vordefinierten Daten, sodass Sie umgehend interessante Abfragen schreiben können. Die Portaltools sind kein Ersatz für Code, aber sie können für die folgenden Aufgaben nützlich sein:

+ Praktisches Lernen mit minimaler Vorbereitung
+ Erstellen eines Indexprototyps vor dem Schreiben von Code in **Daten importieren**
+ Testen von Abfragen und Parsersyntax im **Suchexplorer**
+ Anzeigen eines vorhandenen, für Ihren Dienst veröffentlichten Index und Untersuchen der dazugehörigen Attribute

**Voraussichtliche Dauer:** Etwa 15 Minuten (länger, falls noch eine Konto- oder Dienstregistrierung erforderlich ist). 

Oder setzen Sie sich anhand einer [codebasierten Einführung in die Programmierung von Azure Search in .NET](search-howto-dotnet-sdk.md) detailliert mit der Thematik auseinander.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorials wird vorausgesetzt, dass Sie über ein [Azure-Abonnement](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) und über einen [Azure Search-Dienst](search-create-service-portal.md) verfügen. 

Wenn Sie nicht sofort einen Dienst bereitstellen möchten, können Sie sich eine sechsminütige Demo der Schritte in diesem Tutorial ansehen (etwa ab der dritten Minute [dieses Übersichtsvideos für Azure Search](https://channel9.msdn.com/Events/Connect/2016/138)).

## <a name="find-your-service"></a>Suchen nach dem Dienst
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Öffnen Sie das Service-Dashboard des Azure-Suchdiensts. Falls Sie die Dienstkachel nicht an Ihr Dashboard angeheftet haben, finden Sie Ihren Dienst wie folgt: 
   
   * Klicken Sie auf der Navigationsleiste im linken Navigationsbereich auf **Alle Dienste**.
   * Geben Sie in das Suchfeld den Suchbegriff *Suche* ein, um eine Liste mit Suchdiensten für Ihr Abonnement zu erhalten. Ihr Dienst sollte in der Liste angezeigt werden. 

## <a name="check-for-space"></a>Überprüfen des Speicherplatzes
Viele Kunden beginnen mit dem kostenlosen Dienst (Free). Diese Version ist auf drei Indizes, drei Datenquellen und drei Indexer beschränkt. Stellen Sie sicher, dass Sie über ausreichend Platz für zusätzliche Elemente verfügen, bevor Sie beginnen. In diesem Tutorial wird jeweils eine Instanz von jedem Objekt erstellt. 

> [!TIP] 
> Auf den Kacheln des Service-Dashboards sehen Sie, über wie viele Indizes, Indexer und Datenquellen Sie bereits verfügen. Die Indexerkachel zeigt Erfolgs- und Fehlerindikatoren. Klicken Sie auf die Kachel, um die Indexeranzahl anzuzeigen. 
>
> ![Kacheln für Indexer und Datenquellen][1]
>

## <a name="create-index"></a> Erstellen eines Index und Laden von Daten
Suchabfragen durchlaufen einen *Index* mit durchsuchbaren Daten, Metadaten und Konstrukten, die zur Optimierung bestimmter Suchverhaltensweisen verwendet werden.

Um diese Aufgabe über das Portal durchführen zu können, verwenden wir ein integriertes Dataset, das über den **Datenimport-Assistenten** mit einem Indexer durchforstet werden kann. 

#### <a name="step-1-start-the-import-data-wizard"></a>Schritt 1: Starten des Datenimport-Assistenten
1. Klicken Sie auf dem Dashboard des Azure Search-Diensts auf der Befehlsleiste auf **Daten importieren** , um einen Assistenten zu starten, mit dem ein Index erstellt und aufgefüllt wird.
   
    ![Befehl zum Importieren von Daten][2]

2. Klicken Sie im Assistenten auf **Datenquelle** > **Beispiele** > **realestate-us-sample**. Diese Datenquelle ist mit einem Namen, einem Typ und Verbindungsinformationen vorkonfiguriert. Nach der Erstellung wird sie zu einer vorhandenen Datenquelle, die in anderen Importvorgängen wiederverwendet werden kann.

    ![Auswählen des Beispieldatasets][9]

3. Klicken Sie auf **OK**, um es zu verwenden.

#### <a name="step-2-define-the-index"></a>Schritt 2: Definieren des Index
Indizes werden in der Regel manuell und mithilfe von Code erstellt. Der Assistent kann jedoch einen Index für jede Datenquelle erstellen, die er durchforsten kann. Für einen Index sind mindestens ein Name und eine Felderauflistung erforderlich, wobei ein Feld als Dokumentschlüssel gekennzeichnet sein muss, um die einzelnen Dokumente eindeutig zu identifizieren.

Felder besitzen Datentypen und Attribute. Die Kontrollkästchen im oberen Bereich sind *Indexattribute*, mit denen die Verwendung des Felds gesteuert wird. 

* **Abrufbar** bedeutet, dass es in der Liste mit den Suchergebnissen angezeigt wird. Sie können einzelne Felder kennzeichnen, indem Sie dieses Kontrollkästchen deaktivieren, um sie aus den Suchergebnissen auszuschließen, z. B. wenn Felder nur in Filterausdrücken verwendet werden. 
* Mit **Filterbar**, **Sortierbar** und **Facettenreich** wird angegeben, ob ein Feld in einer Filter-, Sortier- oder Facettennavigationsstruktur verwendet werden kann. 
* **Durchsuchbar** bedeutet, dass ein Feld in die Volltextsuche einbezogen wird. Zeichenfolgen sind durchsuchbar. Numerische Felder und boolesche Felder werden häufig als „Nicht durchsuchbar“ markiert. 

Standardmäßig durchsucht der Assistent die Datenquelle nach eindeutigen Bezeichnern als Grundlage für das Schlüsselfeld. Zeichenfolgen werden durch Attribute als abrufbar und durchsuchbar gekennzeichnet. Ganze Zahlen werden durch Attribute als abrufbar, filterbar, sortierbar und facettierbar gekennzeichnet.

  ![Generierter Immobilienindex][3]

Klicken Sie auf **OK**, um den Index zu erstellen.

#### <a name="step-3-define-the-indexer"></a>Schritt 3: Definieren des Indexers
Klicken Sie im Assistenten **Daten importieren** auf **Indexer** > **Name**, und geben Sie einen Namen für den Indexer ein. 

Mit diesem Objekt wird ein ausführbarer Prozess definiert. Sie könnten zwar auch einen wiederkehrenden Zeitplan einrichten, in diesem Tutorial verwenden Sie allerdings die Standardoption, um den Indexer nach dem Klicken auf **OK** umgehend einmalig auszuführen.  

  ![Immobilienindexer][8]

## <a name="check-progress"></a>Überprüfen des Status
Wechseln Sie zum Überwachen des Datenimports zurück zum Dashboard des Diensts, scrollen Sie nach unten, und doppelklicken Sie auf die Kachel **Indexer**, um die Liste mit den Indexern zu öffnen. Der neu erstellte Indexer sollte in der Liste angezeigt werden. Der Status sollte „In Bearbeitung“ oder „Erfolgreich“ lauten, und die Anzahl indizierter Dokumente sollte angegeben sein.

   ![Statusmeldung des Indexers][4]

## <a name="query-index"></a> Abfragen des Index
Sie verfügen jetzt über einen Suchindex, der bereit für Abfragen ist. **Suchexplorer** ist ein in das Portal integriertes Abfragetool. Es stellt ein Suchfeld bereit, mit dem Sie überprüfen können, ob die Suchergebnisse Ihren Erwartungen entsprechen. 

> [!TIP]
> Die folgenden Schritte werden im [Übersichtsvideo für Azure Search](https://channel9.msdn.com/Events/Connect/2016/138) ab 6:08 vorgeführt.
>

1. Klicken Sie auf der Befehlsleiste auf **Suchexplorer** .

   ![Suchexplorerbefehl][5]

2. Klicken Sie auf der Befehlsleiste auf **Index ändern**, um zu *realestate-us-sample* zu wechseln.

   ![Index- und API-Befehle][6]

3. Klicken Sie auf der Befehlsleiste auf **API-Version festlegen**, um die verfügbaren REST-APIs anzuzeigen. Über Vorschau-APIs erhalten Sie Zugriff auf neue Features, die noch nicht für die Allgemeinheit veröffentlicht wurden. Verwenden Sie für die Abfragen weiter unten die allgemein verfügbare Version (2017-11-11), sofern nichts anderes angegeben ist. 

    > [!NOTE]
    > [Azure Search-REST-API](https://docs.microsoft.com/rest/api/searchservice/search-documents) und die [.NET-Bibliothek](search-howto-dotnet-sdk.md#core-scenarios) sind zwar vollständig gleichwertig, der **Suchexplorer** kann jedoch nur REST-Aufrufe verarbeiten. Er akzeptiert sowohl Syntax für [einfache Abfragen](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) als auch für [vollständige Lucene-Abfrageparser](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) sowie alle Suchparameter, die bei Vorgängen vom Typ [Dokument durchsuchen](https://docs.microsoft.com/rest/api/searchservice/search-documents) zur Verfügung stehen.
    > 

4. Geben Sie über die Suchleiste die folgenden Abfragezeichenfolgen ein, und klicken Sie auf **Suchen**.

  ![Beispiel für eine Suchabfrage][7]

**`search=seattle`**

+ Der Parameter **search** dient zum Eingeben eines Schlüsselworts für die Volltextsuche. In diesem Fall werden Angebote in King County (Washington) zurückgeben, die *Seattle* in einem beliebigen durchsuchbaren Feld des Dokuments enthalten. 

+ Der **Suchexplorer** gibt Ergebnisse im JSON-Format zurück. Dieses Format ist sehr ausführlich und in Dokumenten mit einer dichten Struktur nur schwer lesbar. Abhängig von Ihren Dokumenten müssen Sie unter Umständen Code schreiben, um die Suchergebnisse zu verarbeiten und wichtige Elemente zu extrahieren. 

+ Dokumente bestehen aus allen Feldern, die im Index als abrufbar gekennzeichnet sind. Klicken Sie zum Anzeigen von Indexattributen im Portal auf der Kachel **Indizes** auf *realestate-us-sample*.

**`search=seattle&$count=true&$top=100`**

+ Das Symbol **&** dient zum Anfügen von Suchparametern. Diese können in beliebiger Reihenfolge angegeben werden. 

+  Der Parameter **$count=true** gibt die Gesamtanzahl aller zurückgegebenen Dokumente zurück. Zur Überprüfung von Filterabfragen können Sie von **$count=true** gemeldete Änderungen überwachen. 

+ **$top=100** gibt von allen Dokumenten die 100 Dokumente mit dem höchsten Rang zurück. Standardmäßig gibt Azure Search die ersten 50 der besten Treffer zurück. Die Menge kann mithilfe von **$top** erhöht oder verringert werden.


## <a name="filter-query"></a> Filtern der Abfrage

Filter werden in Suchanfragen eingefügt, wenn Sie den Parameter **$filter** anfügen. 

**`search=seattle&$filter=beds gt 3`**

+ Der Parameter **$filter** gibt Ergebnisse zurück, die den angegebenen Kriterien entsprechen. In diesem Fall: mehr als drei Schlafzimmer. 

+ Bei der Filtersyntax handelt es sich um eine OData-Konstruktion. Weitere Informationen finden Sie unter [OData Expression Syntax for Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (OData-Ausdruckssyntax für Azure Search).

## <a name="facet-query"></a> Facettieren der Abfrage

Facettenfilter werden in Suchanfragen eingebunden. Sie können den Parameter „facet“ verwenden, um eine aggregierte Anzahl von Dokumenten zurückzugeben, die mit einem von Ihnen angegebenen Facetwert übereinstimmen. 

**`search=*&facet=city&$top=2`**

+ **search=*** ist eine leere Suche. Bei einer leeren Suche wird alles durchsucht. Eine leere Abfrage kann beispielsweise übermittelt werden, um den vollständigen Satz von Dokumenten zu filtern oder zu facettieren – etwa, wenn eine Faceting-Navigationsstruktur alle Städte im Index enthalten soll.

+  **facet** gibt eine Navigationsstruktur zurück, die Sie an ein Benutzeroberflächenelement übergeben können. Er gibt Kategorien und eine Anzahl zurück. In diesem Fall basieren die Kategorien auf der Anzahl von Städten. In Azure Search steht zwar keine Aggregation zur Verfügung, mit `facet` lässt sich jedoch eine Art Aggregation erreichen, die eine Anzahl von Dokumenten in den einzelnen Kategorien liefert.

+ **$top=2** gibt zwei Dokumente zurück und zeigt, dass Sie mithilfe von `top` die Ergebnisse sowohl verringern als auch erhöhen können.

**`search=seattle&facet=beds`**

+ Bei dieser Abfrage handelt es sich um ein Facet für Betten bei einer Textsuche nach *Seattle*. Der Ausdruck *beds* kann als Facet angegeben werden, da das Feld im Index als filterbar und facettierbar markiert ist und die enthaltenen Werte (numerische Werte von 1 bis 5) für die Kategorisierung von Angeboten in Gruppen (Angebote mit drei Schlafzimmern, vier Schlafzimmern...) geeignet ist. 

+ Nur filterbare Felder können facettiert werden. In den Ergebnissen können nur abrufbare Felder zurückgegeben werden.

## <a name="highlight-query"></a> Hinzufügen der Treffermarkierung

Bei Treffermarkierungen wird Text, der dem Schlüsselwort entspricht, mit einer Formatierung versehen (sofern Treffer in einem bestimmten Feld gefunden werden). Falls Ihr Suchbegriff Teil einer umfangreicheren Beschreibung ist, können Sie ihn mithilfe einer Treffermarkierung hervorheben. 

**`search=granite countertops&highlight=description`**

+ In diesem Beispiel ist der formatierte Ausdruck *granite countertops* im Beschreibungsfeld leichter zu erkennen.

**`search=mice&highlight=description`**

+ Die Volltextsuche sucht nach Wortformen mit ähnlicher Semantik. In diesem Fall wird bei Häusern mit einer Mäuseplage in den Ergebnissen einer Suche nach dem Schlüsselwort „mice“ der Text „mouse“ hervorgehoben. Dank linguistischer Analyse können in den Ergebnissen unterschiedliche Formen des gleichen Worts erscheinen. 

+ Azure Search unterstützt 56 Analysen von Lucene und Microsoft. Standardmäßig verwendet Azure Search die Standardanalyse von Lucene. 

## <a name="fuzzy-search"></a> Verwenden der Fuzzysuche

Für falsch geschriebene Wörter wie *samamish* für das Samammish-Plateau in der Seattle-Region werden bei einer typischen Suche keine Treffer zurückgegeben. Zur Kompensierung von Schreibfehlern können Sie eine Fuzzysuche verwenden, wie im nächsten Beispiel beschrieben.

**`search=samamish`**

+ In diesem Beispiel ist ein Ort in der Region Seattle falsch geschrieben.

**`search=samamish~&queryType=full`**

+ Eine Fuzzysuche wird aktiviert, wenn Sie das Symbol **~** angeben und den Parser für die vollständige Abfrage verwenden, der die **~**-Syntax interpretiert und richtig analysiert. 

+ Die Fuzzysuche ist verfügbar, wenn Sie sich für die Verwendung des Parsers für vollständige Abfragen entscheiden (durch Festlegen von **queryType=full**). Weitere Informationen zu möglichen Abfrageszenarien mit dem Parser für vollständige Abfragen finden Sie unter [Lucene query syntax in Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) (Lucene-Abfragesyntax in Azure Search).

+ Ohne Angabe von **queryType** wird der standardmäßige Parser für einfache Abfragen verwendet. Der Parser für einfache Abfragen ist zwar schneller, Fuzzysuche, reguläre Ausdrücke, NEAR-Suche und andere erweiterte Abfragetypen stehen jedoch nur bei Verwendung der vollständigen Syntax zur Verfügung. 

## <a name="geo-search"></a> Ausprobieren der Geosuche

Die Geosuche wird über den [Datentyp „edm.GeographyPoint“](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) für ein Feld mit Koordinaten unterstützt. Die Geosuche ist ein Filtertyp und wird in der [OData-Ausdruckssyntax für Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) angegeben. 

**`search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`**

+ Die Beispielabfrage filtert alle Ergebnisse nach Positionsdaten und gibt Ergebnisse zurück, die weniger als fünf Kilometer von einem bestimmten Punkt (angegeben als Koordinaten mit Breiten-und Längengrad) entfernt sind. Durch Hinzufügen von **$count** sehen Sie, wie viele Ergebnisse zurückgegeben werden, wenn Sie die Entfernung oder die Koordinaten ändern. 

+ Die Geosuche ist hilfreich, wenn Ihre Suchanwendung über eine Umgebungssuche oder Kartennavigation verfügt. Es ist allerdings keine Volltextsuche. Falls eine Suche nach Städte- oder Ländernamen möglich sein muss, fügen Sie zusätzlich zu den Koordinaten Felder mit Städte- oder Ländernamen hinzu.

## <a name="next-steps"></a>Nächste Schritte

+ Ändern Sie beliebige der soeben erstellten Objekte. Nachdem Sie den Assistenten einmal ausgeführt haben, können Sie einen Schritt zurück machen und einzelne Komponenten anzeigen oder ändern: Index, Indexer oder Datenquelle. Einige Bearbeitungen, z. B. das Ändern des Felddatentyps, sind für den Index nicht zulässig, aber die meisten Eigenschaften und Einstellungen können geändert werden.

  Klicken Sie zum Anzeigen der einzelnen Komponenten auf dem Dashboard auf die Kachel **Index**, **Indexer** oder **Datenquellen**, um eine Liste mit vorhandenen Objekten anzuzeigen. Weitere Informationen zu Indexbearbeitungen, die keine Neuerstellung erfordern, finden Sie unter [Update Index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/update-index) (Aktualisieren des Index (Azure Search-REST-API)).

+ Probieren Sie die Tools und Schritte mit anderen Datenquellen aus. Das Beispieldataset `realestate-us-sample` stammt aus einer Azure SQL-Datenbank, die Azure Search durchforsten kann. Neben Azure SQL-Datenbank kann Azure Search auch Azure Table Storage, Blob Storage, SQL Server auf einem virtuellen Azure-Computer sowie Azure Cosmos DB durchforsten und einen Index von darin enthaltenen flachen Datenstrukturen ableiten. Alle diese Datenquellen werden im Assistenten unterstützt. Im Code können Sie einen Index problemlos mithilfe eines *Indexers* auffüllen.

+ Alle Indexer-fremden Datenquellen werden über ein Pushmodell unterstützt, bei dem Ihr Code neue und geänderte Rowsets per Push im JSON-Format an Ihren Index übermittelt. Weitere Informationen finden Sie unter [Add, Update or Delete Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) (Hinzufügen, Aktualisieren oder Löschen von Dokumenten in Azure Search).

Informationen zu anderen Features, die in diesem Artikel erwähnt werden, finden Sie unter den folgenden Links:

* [Indexer in Azure Search](search-indexer-overview.md)
* [Create Index (enthält eine ausführliche Erklärung der Indexattribute)](https://docs.microsoft.com/rest/api/searchservice/create-index)
* [Suchexplorer](search-explorer.md)
* [Search Documents (enthält Beispiele für Abfragesyntax)](https://docs.microsoft.com/rest/api/searchservice/search-documents)


<!--Image references-->
[1]: ./media/search-get-started-portal/tiles-indexers-datasources2.png
[2]: ./media/search-get-started-portal/import-data-cmd2.png
[3]: ./media/search-get-started-portal/realestateindex2.png
[4]: ./media/search-get-started-portal/indexers-inprogress2.png
[5]: ./media/search-get-started-portal/search-explorer-cmd2.png
[6]: ./media/search-get-started-portal/search-explorer-changeindex-se2.png
[7]: ./media/search-get-started-portal/search-explorer-query2.png
[8]: ./media/search-get-started-portal/realestate-indexer2.png
[9]: ./media/search-get-started-portal/import-datasource-sample2.png