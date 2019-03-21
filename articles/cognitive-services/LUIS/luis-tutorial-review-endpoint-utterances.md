---
title: Überprüfung von Endpunktäußerungen
titleSuffix: Azure Cognitive Services
description: Verbessern Sie App-Vorhersagen, indem Sie die über den LUIS-HTTP-Endpunkt erhaltenen Äußerungen, bei denen LUIS unsicher ist, überprüfen bzw. korrigieren. Bei einigen Äußerungen kann eine Überprüfung hinsichtlich der Absicht und bei anderen eine Überprüfung hinsichtlich der Entität erforderlich sein.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: diberry
ms.openlocfilehash: 118ac858103776e880e7304199279a7d50ad71b1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58112278"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Tutorial: Beheben unsicherer Vorhersagen durch Überprüfung von Endpunktäußerungen
In diesem Tutorial verbessern Sie App-Vorhersagen, indem Sie die über den LUIS-HTTPS-Endpunkt erhaltenen Äußerungen, bei denen LUIS unsicher ist, überprüfen bzw. korrigieren. Bei einigen Äußerungen kann eine Überprüfung hinsichtlich der Absicht und bei anderen eine Überprüfung hinsichtlich der Entität erforderlich sein. Sie sollten Endpunktäußerungen regelmäßig im Rahmen der geplanten LUIS-Wartung überprüfen. 

Dieser Überprüfungsprozess ist eine der Möglichkeiten, die für LUIS zum Erlernen Ihrer App-Domäne zur Verfügung stehen. LUIS wählt die Äußerungen aus, die in der Überprüfungsliste angezeigt werden. Für diese Liste gilt Folgendes:

* Sie gilt spezifisch für die App.
* Sie soll die Vorhersagegenauigkeit der App verbessern. 
* Sie sollte in regelmäßigen Abständen überprüft werden. 

Indem Sie die Endpunktäußerungen überprüfen, verifizieren bzw. korrigieren Sie die vorhergesagte Absicht der Äußerung. Außerdem bezeichnen Sie benutzerdefinierte Entitäten, die nicht oder falsch vorhergesagt wurden. 

**In diesem Tutorial lernen Sie Folgendes:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importieren der Beispiel-App
> * Überprüfen von Endpunktäußerungen
> * Aktualisieren der Ausdrucksliste
> * Trainieren der App
> * App veröffentlichen
> * Abfragen des App-Endpunkts zum Anzeigen der LUIS-JSON-Antwort

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importieren der Beispiel-App

Fahren Sie mit der im letzten Tutorial erstellten App mit dem Namen **HumanResources** fort. 

Führen Sie die folgenden Schritte aus:

1.  Laden Sie die [App-JSON-Datei](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json) herunter, und speichern Sie sie.

1. Importieren Sie den JSON-Code in eine neue App.

1. Klonen Sie die Version von der Registerkarte **Versionen** aus dem Abschnitt **Verwalten**, und geben Sie ihr den Namen `review`. Durch Klonen können Sie ohne Auswirkungen auf die ursprüngliche Version mit verschiedenen Features von LUIS experimentieren. Da der Versionsname als Teil der URL-Route verwendet wird, darf er keine Zeichen enthalten, die in einer URL ungültig sind.

1. Trainieren und veröffentlichen Sie die neue App.

1. Verwenden Sie den Endpunkt, um die folgenden Äußerungen hinzuzufügen. Dies ist entweder mit einem [Skript](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js) oder über den Endpunkt in einem Browser möglich. Fügen Sie die folgenden Äußerungen hinzu:

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

    Falls Sie über alle Versionen der App für die gesamte Tutorialreihe verfügen, sind Sie vielleicht überrascht, dass sich die Liste **Review endpoint utterances** (Endpunktäußerungen überprüfen) nicht je nach Version ändert. Es muss nur ein Pool mit Äußerungen überprüft werden. Dies gilt unabhängig davon, welche Version Sie aktiv bearbeiten oder welche Version der App auf dem Endpunkt veröffentlicht wurde. 

## <a name="review-endpoint-utterances"></a>Überprüfen von Endpunktäußerungen

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Wählen Sie im linken Navigationsbereich die Option **Review endpoint utterances** (Endpunktäußerungen überprüfen). Die Liste wird für die Absicht **ApplyForJob** gefiltert. 

    [![Screenshot: Schaltfläche „Review endpoint utterances“ (Endpunktäußerungen überprüfen) im linken Navigationsbereich](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png#lightbox)

1. Schalten Sie **Entities view** (Entitätsansicht) um, um die bezeichneten Entitäten anzuzeigen. 
    
    [![Screenshot: „Review endpoint utterances“ (Endpunktäußerungen überprüfen) mit hervorgehobenem Umschalter „Entities view“ (Entitätsansicht)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png#lightbox)

    |Äußerung|Richtige Absicht|Fehlende Entitäten|
    |:--|:--|:--|
    |I'm looking for a job with Natural Language Processing (Ich suche nach einem Job im Bereich Verarbeitung natürlicher Sprache)|GetJobInfo|Job – „Natural Language Process“|

    Diese Äußerung weist nicht die richtige Absicht auf und hat eine Bewertung von weniger als 50%. Die Absicht **ApplyForJob** verfügt über 21 Äußerungen, während für **GetJobInformation** nur sieben Äußerungen vorhanden sind. Zusätzlich zum richtigen Zuordnen der Endpunktäußerung sollten der Absicht **GetJobInformation** weitere Äußerungen hinzugefügt werden. Diese Übung können Sie selbstständig durchführen. Alle Absichten, mit Ausnahme von **None** (Keine), sollten ungefähr über die gleiche Anzahl von Beispieläußerungen verfügen. Die Absicht **None** (Keine) sollte etwa 10% der gesamten Äußerungen in der App aufweisen. 

1. Wählen Sie für die Absicht `I'm looking for a job with Natual Language Processing` die richtige Absicht **GetJobInformation** in der Spalte **Aligned intent** (Zugeordnete Absicht) aus. 

    [![Screenshot: „Review endpoint utterances“ (Endpunktäußerungen überprüfen) mit Zuordnung der Äußerung zur Absicht](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

1. In derselben Äußerung lautet die Entität für `Natural Language Processing` „keyPhrase“. Dies sollte stattdessen eine **Job**-Entität sein. Wählen Sie `Natural Language Processing` und dann in der Liste die **Job**-Entität aus.

    [![Screenshot: „Review endpoint utterances“ (Endpunktäußerungen überprüfen) – Bezeichnen der Entität in der Äußerung](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

1. Wählen Sie in derselben Zeile den Kreis mit dem Häkchen in der Spalte **Add to aligned intent** (Zugeordneter Absicht hinzufügen). 

    [![Screenshot: Abschließen der Äußerungszuordnung in der Absicht](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    Mit dieser Aktion wird die Äußerung aus **Review endpoint utterances** (Endpunktäußerungen überprüfen) in die Absicht **GetJobInformation** verschoben. Die Endpunktäußerung ist jetzt eine Beispieläußerung für diese Absicht. 

1. Überprüfen Sie die restlichen Äußerungen dieser Absicht, bezeichnen Sie die Äußerungen, und korrigieren Sie die zugeordnete Absicht (**Aligned intent**), falls dies erforderlich ist.

1. Wenn alle Äußerungen korrekt sind, können Sie das Kontrollkästchen in den einzelnen Zeilen aktivieren und dann die Option **Add selected** (Ausgewählte hinzufügen) wählen, um die Äußerungen richtig auszurichten. 

    [![Screenshot: Abschließen der restlichen Äußerungen in Bezug auf die zugeordnete Absicht](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

1. Die Liste sollte diese Äußerungen nicht mehr enthalten. Falls weitere Äußerungen angezeigt werden, können Sie die Liste weiter durcharbeiten, Absichten korrigieren und alle fehlenden Entitäten bezeichnen, bis die Liste leer ist. 

1. Wählen Sie in der Liste „Filter“ die nächste Absicht aus, und fahren Sie dann mit dem Korrigieren von Äußerungen und dem Bezeichnen von Entitäten fort. Beachten Sie, dass der letzte Schritt für eine Absicht jeweils entweder das Auswählen von **Add to aligned intent** (Zugeordneter Absicht hinzufügen) in der Zeile mit der Äußerung oder das Aktivieren des Kontrollkästchens für jede Absicht und Auswählen von **Add selected** (Ausgewählte auswählen) oberhalb der Tabelle ist.

    Setzen Sie diese Vorgehensweise fort, bis alle Absichten und Entitäten in der Filterliste eine leere Liste aufweisen. Dies ist eine sehr kleine App. Der Überprüfungsprozess dauert nur wenige Minuten. 

## <a name="update-phrase-list"></a>Aktualisieren der Ausdrucksliste
Halten Sie die Ausdrucksliste mit allen neu ermittelten Jobnamen auf dem aktuellen Stand. 

1. Wählen Sie im linken Navigationsbereich die Option **Phrase lists** (Ausdruckslisten).

2. Wählen Sie die Ausdrucksliste **Jobs** aus.

3. Fügen Sie `Natural Language Processing` als Wert hinzu, und wählen Sie anschließend **Save** (Speichern). 

## <a name="train"></a>Trainieren

LUIS ist erst über die Änderungen informiert, nachdem das Trainieren durchgeführt wurde. 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Veröffentlichen

Nach dem Importieren dieser App müssen Sie die Option **Sentiment analysis** (Standpunktanalyse) wählen.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Abrufen von Absicht und Entitäten von einem Endpunkt

Probieren Sie es mit einer Äußerung, die nicht weit von der korrigierten Äußerung abweicht. 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Geben Sie in der Adressleiste am Ende der URL `Are there any natural language processing jobs in my department right now?` ein. Der letzte Parameter der Abfragezeichenfolge lautet `q` (für die Abfrage (**query**) der Äußerung). 

   ```json
   {
    "query": "are there any natural language processing jobs in my department right now?",
    "topScoringIntent": {
      "intent": "GetJobInformation",
      "score": 0.9247605
    },
    "intents": [
      {
        "intent": "GetJobInformation",
        "score": 0.9247605
      },
      {
        "intent": "ApplyForJob",
        "score": 0.129989788
      },
      {
        "intent": "FindForm",
        "score": 0.006438211
      },
      {
        "intent": "EmployeeFeedback",
        "score": 0.00408575451
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00194211153
      },
      {
        "intent": "None",
        "score": 0.00166400627
      },
      {
        "intent": "Utilities.Help",
        "score": 0.00118593348
      },
      {
        "intent": "MoveEmployee",
        "score": 0.0007885918
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.0006373631
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0005980781
      },
      {
        "intent": "Utilities.Confirm",
        "score": 3.719905E-05
      }
    ],
    "entities": [
      {
        "entity": "right now",
        "type": "builtin.datetimeV2.datetime",
        "startIndex": 64,
        "endIndex": 72,
        "resolution": {
          "values": [
            {
              "timex": "PRESENT_REF",
              "type": "datetime",
              "value": "2018-07-05 15:23:18"
            }
          ]
        }
      },
      {
        "entity": "natural language processing",
        "type": "Job",
        "startIndex": 14,
        "endIndex": 40,
        "score": 0.9869922
      },
      {
        "entity": "natural language processing jobs",
        "type": "builtin.keyPhrase",
        "startIndex": 14,
        "endIndex": 45
      },
      {
        "entity": "department",
        "type": "builtin.keyPhrase",
        "startIndex": 53,
        "endIndex": 62
      }
    ],
    "sentimentAnalysis": {
      "label": "positive",
      "score": 0.8251864
    }
   }
   }
   ```

   Die richtige Absicht wurde mit einer hohen Bewertung vorhergesagt, und die **Job**-Entität wurde als `natural language processing` ermittelt. 

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>Kann die Überprüfung durch das Hinzufügen von weiteren Äußerungen ersetzt werden? 
Sie fragen sich vielleicht, warum nicht einfach weitere Beispieläußerungen hinzugefügt werden. Was ist der Zweck der Überprüfung von Endpunktäußerungen? Bei einer LUIS-App in der Praxis stammen die Endpunktäußerungen von Benutzern mit einer Wortwahl und einer Zusammensetzung, die Sie noch nicht verwendet haben. Wenn Sie die gleiche Wortwahl und Zusammensetzung verwendet hätten, würde die ursprüngliche Vorhersage einen höheren Prozentsatz aufweisen. 

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Warum ist die oberste Absicht in der Liste mit den Äußerungen enthalten? 
Einige Endpunktäußerungen weisen in der Überprüfungsliste einen hohen Vorhersagewert auf. Sie müssen diese Äußerungen trotzdem überprüfen und verifizieren. Sie sind in der Liste enthalten, weil die nächsthöhere Absicht über eine Bewertung verfügt hat, die zu nah an der Bewertung der obersten Absicht gelegen hat. Es sollte ein Unterschied von ungefähr 15 % zwischen den beiden am höchsten bewerteten Absichten bestehen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie am Endpunkt ausgegebene Äußerungen überprüft, bei denen LUIS unsicher war. Sobald diese Äußerungen überprüft und als Beispieläußerungen unter die richtigen Absichten verschoben wurden, weist LUIS eine verbesserte Vorhersagegenauigkeit auf.

> [!div class="nextstepaction"]
> [Verwenden von Mustern](luis-tutorial-pattern.md)
