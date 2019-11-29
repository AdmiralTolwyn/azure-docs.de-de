---
title: 'Ändern von Daten: LUIS'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Daten vor der Vorhersage in Language Understanding Intelligent Service (LUIS) geändert werden können.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: a199821c4db7fd8131ec54700b8c999dfe604a6e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74222021"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Ändern von Äußerungsdaten vor oder während der Vorhersage
LUIS bietet Möglichkeiten zum Bearbeiten einer Äußerung vor oder während der Vorhersage. Dazu gehören eine [Korrektur der Rechtschreibung](luis-tutorial-bing-spellcheck.md) und das Beheben von Zeitzonenproblemen für die vordefinierte Entität [datetimeV2](luis-reference-prebuilt-datetimev2.md). 

## <a name="correct-spelling-errors-in-utterance"></a>Korrigieren von Rechtschreibfehlern in Äußerungen

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


LUIS verwendet die [Bing-Rechtschreibprüfungs-API V7](../Bing-Spell-Check/overview.md) um Rechtschreibfehler in Äußerungen zu korrigieren. LUIS benötigt den diesem Dienst zugewiesenen Schlüssel. Erstellen Sie den Schlüssel, und fügen Sie den Schlüssel dann als Parameter der Abfragezeichenfolge an den [Endpunkt](https://go.microsoft.com/fwlink/?linkid=2092356) hinzu. 

<!--
You can also correct spelling errors in the **Test** panel by [entering the key](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key is kept as a session variable in the browser for the Test panel. Add the key to the Test panel in each browser session you want spelling corrected. 

Usage of the key in the test panel and at the endpoint count toward the [key usage](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) quota. LUIS implements Bing Spell Check limits for text length. 

-->

Der Endpunkt benötigt zwei Parameter, damit Rechtschreibkorrekturen funktionieren:

|Parameter|Wert|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|[Bing-Rechtschreibprüfungs-API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) – Endpunktschlüssel|

Wenn die [Bing-Rechtschreibprüfungs-API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) einen Fehler erkennt, werden die ursprüngliche Äußerung und die korrigierte Äußerung zusammen mit Vorhersagen vom Endpunkt zurückgegeben.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 – Antwort für Vorhersageendpunkt](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 – Antwort für Vorhersageendpunkt](#tab/V3)
 
```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * * 

### <a name="list-of-allowed-words"></a>Liste zulässiger Wörter
Die in LUIS verwendete Bing-Rechtschreibprüfungs-API unterstützt keine Liste mit Wörtern, die bei den Änderungen durch die Rechtschreibprüfung ignoriert werden sollen. Wenn Sie eine Liste für Wörter oder Akronyme benötigen, verarbeiten Sie die Äußerung im Client, bevor Sie die Äußerung zur Absichtsvorhersage an LUIS senden.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Ändern der Zeitzone von vordefinierten datetimeV2-Entitäten
Wenn eine LUIS-App die vordefinierte Entität [datetimeV2](luis-reference-prebuilt-datetimev2.md) verwendet, kann ein datetime-Wert in der Vorhersageantwort zurückgegeben werden. Der richtige datetime-Wert für die Rückgaben wird anhand der Zeitzone der Anforderung bestimmt. Wenn die Anforderung von einem Bot oder einer anderen zentralen Anwendung vor dem Empfang durch LUIS stammt, korrigieren Sie die von LUIS verwendete Zeitzone. 

### <a name="endpoint-querystring-parameter"></a>QueryString-Parameter für den Endpunkt
Sie korrigieren die Zeitzone, indem Sie am [Endpunkt](https://go.microsoft.com/fwlink/?linkid=2092356) mithilfe des `timezoneOffset`-Parameters die Zeitzone des Benutzers hinzufügen. Der Wert von `timezoneOffset` sollte die positive oder negative Zahl in Minuten sein, um die die Zeit geändert werden soll.  

|Parameter|Wert|
|--|--|
|`timezoneOffset`|Positive oder negative Zahl, in Minuten|

### <a name="daylight-savings-example"></a>Sommerzeitbeispiel
Wenn Sie den zurückgegebenen vordefinierten datetimeV2-Wert für die Anpassung an die Sommerzeit benötigen, sollten Sie den QueryString-Parameter `timezoneOffset` mit einem +/-Wert in Minuten für die Abfrage an den [Endpunkt](https://go.microsoft.com/fwlink/?linkid=2092356) verwenden.

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2 – Anforderung für Vorhersageendpunkt](#tab/V2)

Hinzufügen von 60 Minuten: 

https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{App-ID}?q=Turn the lights on?**timezoneOffset=60**&verbose={Boolean}&spellCheck={Boolean}&staging={Boolean}&bing-spell-check-subscription-key={Zeichenfolge}&log={Boolean}

Entfernen von 60 Minuten: 

https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{App-ID}?q=Turn the lights on?**timezoneOffset=-60**&verbose={Boolean}&spellCheck={Boolean}&staging={Boolean}&bing-spell-check-subscription-key={Zeichenfolge}&log={Boolean}

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 – Anforderung für Vorhersageendpunkt](#tab/V3)

Hinzufügen von 60 Minuten:

https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

Entfernen von 60 Minuten: 

https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=-60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

Erfahren Sie mehr über den [V3-Vorhersageendpunkt](luis-migration-api-v3.md).

* * * 

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C#-Code zur Bestimmung des richtigen Werts für timezoneOffset
Der folgende C#-Code verwendet die [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples)-Methode der [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo)-Klasse, um das richtige `timezoneOffset` basierend auf der Systemzeit zu bestimmen:

```CSharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Korrigieren Sie Rechtschreibfehler mithilfe dieses Tutorials.](luis-tutorial-bing-spellcheck.md)
