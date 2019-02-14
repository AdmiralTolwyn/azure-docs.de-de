---
title: Vordefinierte Ordinal-Entität
titleSuffix: Azure
description: In diesem Artikel erhalten Sie Informationen zur vorgefertigten Ordinalentität in Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: f6762c74dbe6b16791e597464584b91e696220f1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863449"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Vordefinierte Ordinal-Entität für eine LUIS-App
Eine Ordnungszahl ist eine numerische Darstellung eines Objekts in einer Menge: `first`, `second`, `third`. Da diese Entität bereits trainiert wurde, müssen Sie den Anwendungsabsichten keine Beispieläußerungen mit Ordnungszahlen hinzufügen. Die Ordinalentität wird in [vielen Kulturen](luis-reference-prebuilt-entities.md) unterstützt. 

## <a name="types-of-ordinal"></a>Ordinaltypen
Die Entität „ordinal“ wird über das GitHub-Repository [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) verwaltet.

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Auflösung der vorgefertigten Ordinalentität
Im folgenden Beispiel wird die Auflösung der Entität **builtin.ordinal** veranschaulicht.

```json
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den [Prozentsatz](luis-reference-prebuilt-percentage.md)-, [Telefonnummer](luis-reference-prebuilt-phonenumber.md)- und [Temperaturentitäten](luis-reference-prebuilt-temperature.md). 
