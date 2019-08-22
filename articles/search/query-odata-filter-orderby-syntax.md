---
title: OData-Sprachübersicht – Azure Search
description: OData-Sprachübersicht für das Filtern, Auswählen und Sortieren für Azure Search-Abfragen.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
manager: nitinme
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 0bd446b0ffa97a758f68a0f85889b13da6e3d8b0
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650032"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-search"></a>OData-Sprachübersicht für `$filter`, `$orderby` und `$select` in Azure Search

Azure Search unterstützt eine Teilmenge der OData-Ausdruckssyntax für **$filter**-, **$orderby**- und **$select**-Ausdrücke. Filterausdrücke werden während der Analyse der Abfrage ausgewertet. Sie beschränken die Suche auf bestimmte Felder oder fügen Übereinstimmungskriterien hinzu, die während Indexscans verwendet werden. Sortierausdrücke werden als Nachverarbeitungsschritt auf ein Resultset angewandt, um die zurückgegebenen Dokumente zu sortieren. Auswahlausdrücke bestimmen, welche Felder des Dokuments in das Resultset eingefügt werden. Die Syntax dieser Ausdrücke unterscheidet sich von der [einfachen](query-simple-syntax.md) oder [vollständigen](query-lucene-syntax.md) Abfragesyntax, die für die **Suchparameter** verwendet wird, auch wenn es bei der Syntax Überschneidungen beim Verweisen auf Felder gibt.

Dieser Artikel enthält eine Übersicht über die OData-Ausdruckssprache, die in den Filter-, Sortier- und Auswahlausdrücken verwendet wird. Die Sprache wird „von unten nach oben“, d. h. beginnend mit den grundlegenden Elementen beschrieben. Die Syntax der obersten Ebene für jeden Parameter wird in einem separaten Artikel beschrieben:

- [$filter-Syntax](search-query-odata-filter.md)
- [$orderby-Syntax](search-query-odata-orderby.md)
- [$select-Syntax](search-query-odata-select.md)

OData-Ausdrücke reichen von einfach bis sehr komplexen Ausdrücken, sie enthalten aber alle gemeinsame Elemente. Die grundlegendsten Teile eines OData-Ausdrucks in Azure Search sind:

- **Feldpfade** für Verweise auf bestimmte Felder des Index
- **Konstanten** als Literalwerte eines bestimmten Datentyps

> [!NOTE]
> Die Terminologie in Azure Search unterscheidet sich vom [OData-Standard](https://www.odata.org/documentation/) in verschiedener Hinsicht. Was in Azure Search als **Feld** bezeichnet wird, ist in OData eine **Eigenschaft**. Dasselbe gilt auch für **Feldpfad** bzw. **Eigenschaftspfad**. Ein **Index** mit **Dokumenten** in Azure Search wird in OData allgemeiner als eine **Entitätenmenge** mit **Entitäten** bezeichnet. In dieser Referenz wird die Azure Search-Terminologie verwendet.

## <a name="field-paths"></a>Feldpfade

Die folgende EBNF ([Erweiterte Backus-Naur-Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiert die Grammatik der Feldpfade.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Ein interaktives Syntaxdiagramm ist ebenfalls verfügbar:

> [!div class="nextstepaction"]
> [OData-Syntaxdiagramm für Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Die vollständige EBNF finden Sie in der [Referenz zur OData-Ausdruckssyntax für Azure Search](search-query-odata-syntax-reference.md).

Ein Feldpfad besteht aus einem oder mehreren **Bezeichnern**, die durch Schrägstriche voneinander getrennt sind. Jeder Bezeichner ist eine Folge von Zeichen, die mit einem ASCII-Buchstaben oder einem Unterstrich beginnen muss und nur ASCII-Buchstaben, Ziffern oder Unterstriche enthalten darf. Es können Groß- und Kleinbuchstaben verwendet werden.

Ein Bezeichner kann entweder auf den Namen eines Felds oder auf eine **Bereichsvariable** im Kontext eines [Sammlungsausdruck](search-query-odata-collection-operators.md) (`any` oder `all`) in einem Filter verweisen. Eine Bereichsvariable ist wie eine Schleifenvariable, die das aktuelle Element der Sammlung darstellt. Für komplexe Sammlungen stellt die Variable ein Objekt dar. Deshalb können Sie Feldpfade verwenden, um auf untergeordnete Felder in der Variable zu verweisen. Dies ist vergleichbar mit der Punktnotation in vielen Programmiersprachen.

Die folgende Tabelle enthält Beispiele für Feldpfade:

| Feldpfad | BESCHREIBUNG |
| --- | --- |
| `HotelName` | Verweist auf ein Feld der obersten Ebene des Indexes |
| `Address/City` | Verweist auf das untergeordnete Feld `City` eines komplexen Felds im Index; `Address` ist in diesem Beispiel vom Typ `Edm.ComplexType` |
| `Rooms/Type` | Verweist auf das untergeordnete Feld `Type` eines komplexen Sammlungsfelds im Index; `Rooms` ist in diesem Beispiel vom Typ `Collection(Edm.ComplexType)` |
| `Stores/Address/Country` | Verweist auf das untergeordnete Feld `Country` des untergeordneten Felds `Address` eines komplexen Sammlungsfelds im Index; `Stores` ist in diesem Beispiel vom Typ `Collection(Edm.ComplexType)` und `Address` vom Typ `Edm.ComplexType` |
| `room/Type` | Verweist auf das untergeordnete Feld `Type` der Bereichsvariable `room`, z. B. im Filterausdruck `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Verweist auf das untergeordnete Feld `Country` des untergeordneten Felds `Address` der Bereichsvariable `store`, z. B. im Filterausdruck `Stores/any(store: store/Address/Country eq 'Canada')` |

Die Bedeutung eines Feldpfads variiert je nach Kontext. In Filtern verweist ein Feldpfad auf den Wert einer *Einzelinstanz* eines Felds im aktuellen Dokument. In anderen Kontexten, wie z. B. **$orderby**, **$select** oder bei der [feldbezogenen Suche in der vollständigen Lucene-Syntax](query-lucene-syntax.md#bkmk_fields), verweist ein Feldpfad auf das Feld selbst. Dieser Unterschied hat Konsequenzen für die Verwendung von Feldpfaden in Filtern.

Betrachten Sie den Feldpfad `Address/City`. In einem Filter verweist dieser auf eine einzelne Stadt im aktuellen Dokument, z. B. „San Francisco“. Im Gegensatz dazu verweist `Rooms/Type` auf das untergeordnete Feld `Type` für viele Zimmer (z. B. „standard“ für das erste Zimmer, „deluxe“ für das zweite Zimmer usw.). Da `Rooms/Type` nicht auf eine *Einzelinstanz* des untergeordneten Felds `Type` verweist, ist eine direkte Verwendung in einem Filter nicht möglich. Um stattdessen nach dem Zimmertyp zu filtern, verwenden Sie einen [Lambdaausdruck](search-query-odata-collection-operators.md) mit einer Bereichsvariable, wie im Folgenden gezeigt:

    Rooms/any(room: room/Type eq 'deluxe')

In diesem Beispiel ist die Bereichsvariable `room` im Feldpfad `room/Type` enthalten. Auf diese Weise verweist `room/Type` auf den Typ des aktuellen Zimmers im aktuellen Dokument. Dies ist eine einzelne Instanz des untergeordneten Felds `Type` und kann damit direkt im Filter verwendet werden.

### <a name="using-field-paths"></a>Verwenden von Feldpfaden

Feldpfade werden in zahlreichen Parametern der [Azure Search-API](https://docs.microsoft.com/rest/api/searchservice/) verwendet. Die folgende Tabelle enthält alle Stellen, an denen sie verwendet werden können, sowie einige Einschränkungen für ihre Nutzung:

| API | Parametername | Einschränkungen |
| --- | --- | --- |
| [Erstellen](https://docs.microsoft.com/rest/api/searchservice/create-index) oder [Aktualisieren](https://docs.microsoft.com/rest/api/searchservice/update-index) des Index | `suggesters/sourceFields` | Keine |
| [Erstellen](https://docs.microsoft.com/rest/api/searchservice/create-index) oder [Aktualisieren](https://docs.microsoft.com/rest/api/searchservice/update-index) des Index | `scoringProfiles/text/weights` | Kann nur auf **durchsuchbare** Felder verweisen |
| [Erstellen](https://docs.microsoft.com/rest/api/searchservice/create-index) oder [Aktualisieren](https://docs.microsoft.com/rest/api/searchservice/update-index) des Index | `scoringProfiles/functions/fieldName` | Kann nur auf **filterbare** Felder verweisen |
| [Suchen,](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search`, wenn `queryType` den Wert `full` hat | Kann nur auf **durchsuchbare** Felder verweisen |
| [Suchen,](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Kann nur auf **facettierbare** Felder verweisen |
| [Suchen,](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Kann nur auf **durchsuchbare** Felder verweisen |
| [Suchen,](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Kann nur auf **durchsuchbare** Felder verweisen |
| [Vorschlagen](https://docs.microsoft.com/rest/api/searchservice/suggestions) und [AutoVervollständigen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Kann nur auf Felder verweisen, die Teil einer [Vorschlagsfunktion](index-add-suggesters.md) sind |
| [Suchen](https://docs.microsoft.com/rest/api/searchservice/search-documents), [Vorschlagen](https://docs.microsoft.com/rest/api/searchservice/suggestions) und [AutoVervollständigen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Kann nur auf **filterbare** Felder verweisen |
| [Suchen](https://docs.microsoft.com/rest/api/searchservice/search-documents) und [Vorschlagen](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Kann nur auf **sortierbare** Felder verweisen |
| [Suchen](https://docs.microsoft.com/rest/api/searchservice/search-documents), [Vorschlagen](https://docs.microsoft.com/rest/api/searchservice/suggestions) und [Nachschlagen](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Kann nur auf **abrufbare** Felder verweisen |

## <a name="constants"></a>Konstanten

Konstanten in OData sind Literalwerte eines bestimmten [Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)-Typs (EDM). Eine Liste der in Azure Search unterstützten Typen finden Sie unter [Unterstützte Datentypen](https://docs.microsoft.com/rest/api/searchservice/supported-data-types). Konstanten von Sammlungstypen werden nicht unterstützt.

Die folgende Tabelle zeigt Beispiele für Konstanten für jeden der von Azure Search unterstützten Datentypen:

| Datentyp | Beispielkonstanten |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

Die folgende EBNF ([erweiterte Backus-Naur-Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiert die Grammatik für die meisten Konstanten in der obigen Tabelle. Die Grammatik für geografische Datentypen finden Sie unter [Geografische OData-Funktionen in Azure Search](search-query-odata-geo-spatial-functions.md).

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

Ein interaktives Syntaxdiagramm ist ebenfalls verfügbar:

> [!div class="nextstepaction"]
> [OData-Syntaxdiagramm für Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Die vollständige EBNF finden Sie in der [Referenz zur OData-Ausdruckssyntax für Azure Search](search-query-odata-syntax-reference.md).

## <a name="building-expressions-from-field-paths-and-constants"></a>Erstellen von Ausdrücken aus Feldpfaden und Konstanten

Feldpfade und Konstanten sind der grundlegendste Teil jedes OData-Ausdrucks, sie sind aber selbst bereits vollständige Ausdrücke. Tatsächlich ist der **$select**-Parameter in Azure Search eine durch Trennzeichen getrennte Liste der Feldpfade, und **$orderby** ist nicht viel komplizierter als **$select**. Wenn Ihr Index ein Feld vom Typ `Edm.Boolean` enthält, können Sie sogar einen Filter schreiben, der lediglich der Pfad dieses Felds ist. Die Konstanten `true` und `false` sind ebenfalls gültige Filter.

Allerdings benötigen Sie in den meisten Fällen komplexere Ausdrücke, die auf mehr als ein Feld und eine Konstante verweisen. Diese Ausdrücke werden je nach Parameter auf verschiedene Arten erstellt.

Die folgende EBNF ([erweiterte Backus-Naur-Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiert die Grammatik der Parameter **$filter**, **$orderby** und **$select**. Diese basieren auf einfacheren Ausdrücken, die auf die Feldpfade und Konstanten verweisen:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Ein interaktives Syntaxdiagramm ist ebenfalls verfügbar:

> [!div class="nextstepaction"]
> [OData-Syntaxdiagramm für Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Die vollständige EBNF finden Sie in der [Referenz zur OData-Ausdruckssyntax für Azure Search](search-query-odata-syntax-reference.md).

Die Parameter **$orderby** und **$select** sind durch Trennzeichen getrennte Listen mit einfachen Ausdrücken. Der **$filter**-Parameter ist ein boolescher Ausdruck, der sich aus einfacheren untergeordneten Ausdrücken zusammensetzt. Diese untergeordneten Ausdrücke werden mithilfe logischer Operatoren wie z. B. [`and`, `or` und `not`](search-query-odata-logical-operators.md), Vergleichsoperatoren wie [`eq`, `lt`, `gt` usw.](search-query-odata-comparison-operators.md) und Sammlungsoperatoren wie [`any` und `all`](search-query-odata-collection-operators.md) kombiniert.

In den folgenden Artikeln werden die Parameter **$filter**, **$orderby** und **$select** ausführlicher besprochen:

- [OData-Syntax von „$filter“ in Azure Search](search-query-odata-filter.md)
- [OData-Syntax von „$orderby“ in Azure Search](search-query-odata-orderby.md)
- [OData-Syntax von „$select“ in Azure Search](search-query-odata-select.md)

## <a name="see-also"></a>Weitere Informationen  

- [Facettennavigation in Azure Search](search-faceted-navigation.md)
- [Filter in Azure Search](search-filters.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Suchen nach Dokumenten: REST-API für den Azure Search-Dienst)
- [Lucene-Abfragesyntax](query-lucene-syntax.md)
- [Simple query syntax in Azure Search](query-simple-syntax.md) (Einfache Abfragesyntax in Azure Search)
