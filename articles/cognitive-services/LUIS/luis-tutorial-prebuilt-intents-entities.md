---
title: 'Vordefinierte Absichten und Entitäten: LUIS'
titleSuffix: Azure Cognitive Services
description: Fügen Sie in diesem Tutorial einer App vordefinierte Absichten und Entitäten hinzu, um schnell zu Absichtsvorhersage und Datenextraktion zu gelangen. Sie müssen Äußerungen nicht mit vordefinierten Entitäten bezeichnen. Die Entität wird automatisch erkannt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 4697bad15a374bed0de08b7cabc5aceaad7f1259
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876708"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Tutorial: Identifizieren häufiger Absichten und Entitäten

Fügen Sie in diesem Tutorial der Personal-App aus dem Tutorial vordefinierte Absichten und Entitäten hinzu, um schnell Absichtsvorhersagen und extrahierte Daten zu erhalten. Sie müssen Äußerungen nicht mit vordefinierten Entitäten markieren, da die Entität automatisch erkannt wird.

Vordefinierte Modelle (Domänen, Absichten und Entitäten) unterstützen Sie bei der schnellen Erstellung Ihres Modells.

**In diesem Tutorial lernen Sie Folgendes:**

> [!div class="checklist"]
> * Erstellen einer neuen App
> * Hinzufügen vordefinierter Absichten 
> * Hinzufügen vordefinierter Entitäten 
> * Trainieren 
> * Veröffentlichen 
> * Abrufen von Absichten und Entitäten vom Endpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Erstellen einer neuen App

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>Fügen Sie vordefinierte Absichten für allgemeine Benutzerabsichten hinzu.

LUIS enthält mehrere vordefinierte Absichten für allgemeine Benutzerabsichten.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Wählen Sie **Add prebuilt domain intent** (Vordefinierte Domänenabsicht hinzufügen) aus. 

1. Suchen Sie nach `Utilities`. 

1. Wählen Sie alle Absichten und anschließend **Fertig** aus. Diese Absichten sind nützlich, um zu bestimmen, wo sich der Benutzer in der Konversation befindet und was er tun möchte. 

## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>Hinzufügen von vordefinierten Entitäten, um die Extraktion allgemeiner Datentypen zu unterstützen

LUIS enthält mehrere vordefinierte Entitäten für das Extrahieren allgemeiner Daten. 

1. Wählen Sie im linken Navigationsmenü die Option **Entities** (Entitäten) aus.

1. Wählen Sie die Schaltfläche **Add prebuilt entity** (Vordefinierte Entität hinzufügen).

1. Wählen Sie in der Liste der vordefinierten Entitäten die folgenden Entitäten und dann **Fertig** aus:

   * **[PersonName](luis-reference-prebuilt-person.md)** 
   * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

     ![Screenshot: Auswahl von „number“ im Dialogfeld mit den vordefinierten Entitäten](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

     Diese Entitäten helfen Ihnen dabei, Ihrer Clientanwendung Namens- und Ortserkennung hinzuzufügen.

## <a name="add-example-utterances-to-the-none-intent"></a>Hinzufügen von Beispieläußerungen zur Absicht „None“ 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trainieren der App, um die Absichtsänderungen testen zu können 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Veröffentlichen der App, damit das trainierte Modell über den Endpunkt abgefragt werden kann

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Abrufen von Absicht und Entitätsvorhersage vom Endpunkt

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Wechseln Sie an das Ende der URL in der Adressleiste des Browsers, und geben Sie `I want to cancel my trip to Seattle to see Bob Smith` ein. Der letzte Parameter der Abfragezeichenfolge lautet `q` (für die Abfrage (**query**) der Äußerung). 

    ```json
    {
      "query": "I want to cancel my trip to Seattle to see Bob Smith.",
      "topScoringIntent": {
        "intent": "Utilities.ReadAloud",
        "score": 0.100361854
      },
      "intents": [
        {
          "intent": "Utilities.ReadAloud",
          "score": 0.100361854
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.08102781
        },
        {
          "intent": "Utilities.SelectNone",
          "score": 0.0398852825
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0277276486
        },
        {
          "intent": "Utilities.SelectItem",
          "score": 0.0220712926
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0145813478
        },
        {
          "intent": "None",
          "score": 0.012434179
        },
        {
          "intent": "Utilities.Escalate",
          "score": 0.0122632384
        },
        {
          "intent": "Utilities.ShowNext",
          "score": 0.008534077
        },
        {
          "intent": "Utilities.ShowPrevious",
          "score": 0.00547111453
        },
        {
          "intent": "Utilities.SelectAny",
          "score": 0.00152912608
        },
        {
          "intent": "Utilities.Repeat",
          "score": 0.0005556819
        },
        {
          "intent": "Utilities.FinishTask",
          "score": 0.000169488427
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.000149565312
        },
        {
          "intent": "Utilities.GoBack",
          "score": 0.000141017343
        },
        {
          "intent": "Utilities.Reject",
          "score": 6.27324E-06
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        },
        {
          "entity": "bob smith",
          "type": "builtin.personName",
          "startIndex": 43,
          "endIndex": 51
        }
      ]
    }
    ```

    Das Ergebnis hat die Absicht Utilities.Cancel mit einer Konfidenz von 80 % vorhergesagt und die Daten für Stadt und Personennamen extrahiert. 


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Verwandte Informationen

Weitere Informationen zu vordefinierten Modellen:

* [Vordefinierte Domänen](luis-reference-prebuilt-domains.md): Dies sind allgemeine Domänen, die den Gesamtaufwand für das Erstellen von LUIS-Apps verringern.
* Vordefinierte Absichten: Dies sind die einzelnen Absichten der allgemeinen Domänen. Sie können Absichten einzeln hinzufügen, statt eine gesamte Domäne hinzuzufügen.
* [Vorkonfigurierte Entitäten](luis-prebuilt-entities.md): Dies sind allgemeine Datentypen, die für die meisten LUIS-Apps nützlich sind.

Weitere Informationen zur Arbeit mit Ihrer LUIS-App:

* [Informationen zum Trainieren](luis-how-to-train.md)
* [Informationen zum Veröffentlichen](luis-how-to-publish-app.md)
* [Informationen zum Testen im LUIS-Portal](luis-interactive-test.md)

## <a name="next-steps"></a>Nächste Schritte

Durch das Hinzufügen vordefinierter Absichten und Entitäten kann die Clientanwendung allgemeine Benutzerabsichten bestimmen und allgemeine Datentypen extrahieren.  

> [!div class="nextstepaction"]
> [Hinzufügen einer Entität vom Typ „Regulärer Ausdruck“ zur App](luis-quickstart-intents-regex-entity.md)

