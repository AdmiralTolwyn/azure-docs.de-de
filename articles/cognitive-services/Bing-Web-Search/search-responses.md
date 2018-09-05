---
title: Antworten der Bing-Websuche-API | Microsoft-Dokumentation
titleSuffix: Microsoft Cognitive Services
description: Informationen zu Antworttypen und Antworten der Bing-Websuche-API.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 8/13/2018
ms.author: erhopf
ms.openlocfilehash: 13e9792f3d5765047dabb4cdef59e85a47a69aba
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889369"
---
# <a name="bing-web-search-responses"></a>Antworten der Bing-Websuche  

Wenn Sie eine Suchanforderung an die Bing-Websuche senden, wird im Antworttext ein [`SearchResponse`](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse)-Objekt zurückgegeben. Das Objekt enthält jeweils ein Feld für jede Antwort, die nach Bewertung von Bing für die Abfrage relevant ist. In diesem Beispiel wird ein Antwortobjekt gezeigt, bei dem Bing alle Antworten zurückgegeben hat:

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

Normalerweise wird bei der Bing-Websuche eine Teilmenge der Antworten zurückgegeben. Wenn der Abfrageausdruck beispielsweise *sailing dinghies* war, kann die Antwort `webPages`, `images` und `rankingResponse` enthalten. Sofern Sie nicht [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#responsefilter) zum Herausfiltern von Webseiten verwendet haben, umfasst die Antwort immer `webpages`- und `rankingResponse`-Antworten.

## <a name="webpages-answer"></a>Antwort zu Webseiten

Die Antwort vom Typ [webPages](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) enthält eine Liste mit Links zu Webseiten, die nach Bewertung der Bing-Websuche für die Abfrage relevant sind. Jede [Webseite](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webpage) in der Liste beinhaltet den Namen der Seite, die URL, die Anzeige-URL, eine kurze Beschreibung des Inhalts und das Datum, an dem Bing den Inhalt gefunden hat.

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

Verwenden Sie `name` und `url`, um einen Hyperlink zu erstellen, über den der Benutzer zu der Webseite geleitet wird.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Bildantwort

Die Antwort vom Typ [images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) enthält eine Liste mit Bildern, die nach Einschätzung von Bing für die Abfrage relevant sind. Jedes [Bild](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) in der Liste enthält die URL, die Größe, die Abmessungen und das Codierungsformat des Bilds. Darüber hinaus enthält das Bildobjekt die URL einer Miniaturansicht des Bilds sowie die Abmessungen der Miniaturansicht.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

Abhängig vom Gerät des Benutzers zeigen Sie normalerweise eine Teilmenge der Miniaturansichten an, mit einer Möglichkeit für den Benutzer, die restlichen Bilder anzuzeigen.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

Die Miniaturansicht kann erweitert werden, wenn der Benutzer mit dem Cursor darauf zeigt. Wenn das Bild erweitert wird, muss die Quelle angegeben werden. Hierzu können Sie beispielsweise den Host aus `hostPageDisplayUrl` extrahieren und unter dem Bild anzeigen. Informationen zum Ändern der Größe der Miniaturansicht finden Sie unter [Resizing and cropping thumbnail images](./resize-and-crop-thumbnails.md) (Ändern der Größe und Zuschneiden von Miniaturansichten).

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Wenn der Benutzer auf die Miniaturansicht klickt, verwenden Sie `webSearchUrl`, um den Benutzer zu der Bing-Suchergebnisseite für Bilder weiterzuleiten, die eine Collage der Bilder enthält.

Ausführliche Informationen zu der Bildantwort und den Bildern finden Sie unter [Bing-Bildersuche-API](../bing-image-search/search-the-web.md).

## <a name="related-searches-answer"></a>Antwort zu verwandten Suchvorgängen

Die Antwort vom Typ [relatedSearches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse-relatedsearches) enthält eine Liste der am häufigsten von anderen Benutzern durchgeführten verwandten Abfragen. Jede [Abfrage](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query_obj) in der Liste beinhaltet eine Abfragezeichenfolge (`text`), eine Abfragezeichenfolge mit Treffermarkierungszeichen (`displayText`) und eine URL (`webSearchUrl`) zu der Bing-Suchergebnisseite für die Abfrage.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Verwenden Sie die Abfragezeichenfolge `displayText` und die URL `webSearchUrl`, um einen Hyperlink zu erstellen, über den der Benutzer zu der Bing-Suchergebnisseite für die verwandte Abfrage geleitet wird. Außerdem können Sie die Abfragezeichenfolge `text` in Ihrer eigenen Abfrage mit der Websuche-API verwenden und die Ergebnisse selbst anzeigen.

Informationen zur Verwendung der Treffermarkierungen in `displayText` finden Sie unter [Treffermarkierung](./hit-highlighting.md).

Nachfolgend sehen Sie ein Beispiel für die Verwendung verwandter Abfragen in Bing.com.

![Beispiel für verwandte Suchvorgänge in Bing](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Antwort zu Videos

Die Antwort vom Typ [videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) enthält eine Liste mit Videos, die nach Einschätzung von Bing für die Abfrage relevant sind. Jedes [Video](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video) in der Liste enthält die URL, die Dauer, die Abmessungen und das Codierungsformat des Videos. Darüber hinaus enthält das Videoobjekt die URL einer Miniaturansicht des Videos sowie die Abmessungen der Miniaturansicht.

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

Abhängig vom Gerät des Benutzers zeigen Sie normalerweise eine Teilmenge der Videos an, mit einer Möglichkeit für den Benutzer, die restlichen Videos anzuzeigen. Sie zeigen z.B. eine Miniaturansicht des Videos mit der Länge, der Beschreibung (Name) und der Quelle (Herausgeber) des Videos an.

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

Wenn der Benutzer auf die Miniaturansicht zeigt, können Sie `motionThumbnailUrl` verwenden, um eine Miniaturansichtversion des Videos wiederzugeben. Vergessen Sie bei der Anzeige der bewegten Miniaturansicht nicht, die Quelle anzugeben.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Wenn der Benutzer auf die Miniaturansicht klickt, stehen folgende Optionen zum Anzeigen des Videos zur Verfügung:

- Verwenden Sie `hostPageUrl`, um das Video auf der Website des Hosts anzuzeigen (z.B. YouTube).
- Verwenden Sie `webSearchUrl`, um das Video im Bing-Videobrowser anzuzeigen.
- Verwenden Sie `embedHtml`, um das Video in Ihrer Benutzeroberfläche einzubetten.

Ausführliche Informationen zur Videoantwort und zu Videos finden Sie unter [Bing-Videosuche-API](../bing-video-search/search-the-web.md).

## <a name="news-answer"></a>Antwort zu Nachrichtenartikeln

Die Antwort vom Typ [news](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news) enthält eine Liste mit Nachrichtenartikeln, die nach Einschätzung von Bing für die Abfrage relevant sind. Jeder [Nachrichtenartikel](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) in der Liste beinhaltet den Namen des Artikels, eine Beschreibung und die URL zu dem Artikel auf der Website des Hosts. Wenn der Artikel ein Bild enthält, umfasst das Objekt eine Miniaturansicht des Bilds.

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

Abhängig vom Gerät des Benutzers zeigen Sie normalerweise eine Teilmenge der Nachrichtenartikel an, mit einer Möglichkeit für den Benutzer, die restlichen Artikel anzuzeigen. Verwenden Sie `name` und `url`, um einen Hyperlink zu erstellen, über den der Benutzer zu dem Nachrichtenartikel auf der Website des Hosts geleitet wird. Wenn der Artikel ein Bild enthält, machen Sie das Bild mithilfe von `url` klickbar. Vergessen Sie nicht, mit `provider` die Quelle anzugeben.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Ausführliche Informationen zu der News-Antwort und zu Nachrichtenartikeln finden Sie unter [News-Suche-API](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Antwort zu Berechnungen

Wenn der Benutzer einen mathematischen Ausdruck oder eine Abfrage zur Einheitenumrechnung eingibt, enthält die Antwort möglicherweise eine Antwort vom Typ [Computation](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#computation). Die Antwort vom Typ `computation` enthält den normalisierten Ausdruck und das zugehörige Ergebnis.

Eine Abfrage zur Einheitenumrechnung ist eine Abfrage, bei der eine Einheit in eine andere umgerechnet wird. Beispiel: *Wieviel Fuß sind 10 Meter?* oder *Wie viele Teelöffel sind 1/4 Tasse?*

Im folgenden Beispiel wird die Antwort vom Typ `computation` für *Wieviel Fuß sind 10 Meter?* gezeigt.

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

Nachfolgend finden Sie Beispiele für mathematische Abfragen und die jeweils entsprechenden Antworten vom Typ `computation`.

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

Ein mathematischer Ausdruck kann folgende Symbole enthalten:

|Symbol|BESCHREIBUNG|
|------------|-----------------|
|+|Addition|
|-|Subtraktion|
|/|Division|
|*|Multiplikation|
|^|Potenz|
|!|Fakultät|
|.|DECIMAL|
|()|Prioritätsgruppierung|
|[]|Funktion|

Ein mathematischer Ausdruck kann folgende Konstanten enthalten:

|Symbol|BESCHREIBUNG|
|------------|-----------------|
|Pi|3,14159...|
|Grad|Grad|
|i|Imaginäre Zahl|
|e|e, 2,71828...|
|GoldenRatio|Goldener Schnitt, 1,61803...|

Ein mathematischer Ausdruck kann folgende Funktionen enthalten:

|Symbol|BESCHREIBUNG|
|------------|-----------------|
|Sqrt|Quadratwurzel|
|Sin[x], Cos[x], Tan[x]<br />Csc[x], Sec[x], Cot[x]|Trigonometrische Funktionen (mit Argumenten im Bogenmaß)|
|ArcSin[x], ArcCos[x], ArcTan[x]<br />ArcCsc[x], ArcSec[x], ArcCot[x]|Inverse trigonometrische Funktionen (Ergebnisse im Bogenmaß)|
|Exp[x], E^x|Exponentialfunktion|
|Log[x]|Natürlicher Logarithmus|
|Sinh[x], Cosh[x], Tanh[x]<br />Csch[x], Sech[x], Coth[x]|Hyperbolische Funktionen|
|ArcSinh[x], ArcCosh[x], ArcTanh[x]<br />ArcCsch[x], ArcSech[x], ArcCoth[x]|Inverse hyperbolische Funktionen|

Mathematische Ausdrücke, die Variablen enthalten (z.B. 4x + 6 = 18, wobei x die Variable ist), werden nicht unterstützt.

## <a name="timezone-answer"></a>Antwort zur Zeitzone

Wenn der Benutzer eine Abfrage zu Uhrzeit oder Datum eingibt, kann die Antwort eine Antwort vom Typ [TimeZone](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#timezone) enthalten. Diese Antwort unterstützt implizite oder explizite Abfragen. Bei einer impliziten Abfrage, z.B. *Wie spät ist es?*, wird die Ortszeit basierend auf dem Standort des Benutzers zurückgegeben. Bei einer expliziten Abfrage, z.B. *Wie spät ist es in Seattle?*, wird die Ortszeit für Seattle, Washington, USA zurückgegeben.

Die Antwort vom Typ `timeZone` beinhaltet den Namen des Standorts, das aktuelle UTC-Datum und die aktuelle UTC-Zeit am angegebenen Standort sowie die UTC-Abweichung. Wenn sich die Standortgrenzen in mehreren Zeitzonen befinden, enthält die Antwort das aktuelle UTC-Datum und die aktuelle UTC-Zeit aller Zeitzonen innerhalb der Grenzen. Da der US-Bundesstaat Florida in zwei Zeitzonen liegt, enthält die Antwort beispielsweise das lokale Datum und die Ortszeit beider Zeitzonen.  

Wenn mit der Abfrage die Uhrzeit einer Region oder eines Lands angefordert wird, ermittelt Bing den Hauptort innerhalb der geografischen Grenzen des Standorts und gibt diesen im Feld `primaryCityTime` zurück. Wenn die geografischen Grenzen mehrere Zeitzonen umfassen, werden die übrigen Zeitzonen im Feld `otherCityTimes` zurückgegeben.

Nachfolgend finden Sie Beispielabfragen, die eine Antwort vom Typ `timeZone` zurückgeben.

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>Antwort zu Rechtschreibvorschlägen

Wenn Bing feststellt, dass der Benutzer möglicherweise einen abweichenden Ausdruck suchen wollte, enthält die Antwort ein [SpellSuggestions](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#spellsuggestions)-Objekt. Wenn der Benutzer z.B. nach *carlos pen* sucht, geht Bing davon aus, dass der Benutzer stattdessen möglicherweise nach „Carlos Pena“ suchen wollte (basierend auf der früheren Suche nach *carlos pen* durch andere Benutzer). Nachfolgend finden Sie ein Beispiel für eine Antwort zur Rechtschreibung.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

Das folgende Beispiel zeigt, wie der Rechtschreibvorschlag in Bing verwendet wird.

![Beispiel für Rechtschreibvorschlag in Bing](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Nächste Schritte  

* Sehen Sie sich die Dokumentation zur [Anforderungsbegrenzung](throttling-requests.md) an.  

## <a name="see-also"></a>Weitere Informationen  

* [Referenz zur Bing-Websuche-API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
