---
title: 'Schnellstart: Abrufen von Antworten aus einer Wissensdatenbank – REST, Node.js – QnA Maker'
titlesuffix: Azure Cognitive Services
description: In diesem Node.js-REST-basierten Schnellstart wird Schritt für Schritt erläutert, wie Sie programmgesteuert eine Antwort auf eine Frage aus einer Wissensdatenbank abrufen.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 11/19/2018
ms.author: diberry
ms.openlocfilehash: 9a3db996429bbbd0883643e2b7c6622dcff5480f
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977798"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>Abrufen von Antworten auf Fragen aus einer Wissensdatenbank mit Node.js

In diesem Schnellstart wird Schritt für Schritt erläutert, wie Sie programmgesteuert eine Antwort auf eine Frage aus einer veröffentlichten QnA Maker-Wissensdatenbank abrufen. QnA Maker extrahiert automatisch Fragen und Antworten aus teilweise strukturiertem Inhalt (z.B. häufig gestellten Fragen) von [Datenquellen](../Concepts/data-sources-supported.md). Die Frage wird im JSON-Format im Textkörper der API-Anforderung gesendet. 

## <a name="prerequisites"></a>Voraussetzungen

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Sie benötigen einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md). Wählen Sie zum Abrufen Ihres Schlüssels im Azure-Dashboard für Ihre QnA Maker-Ressource unter **Ressourcenverwaltung** die Option **Schlüssel** aus. 
* Einstellungen auf der Seite **Veröffentlichen**. Wenn Sie keine veröffentlichte Wissensdatenbank haben, erstellen Sie eine leere Wissensdatenbank. Importieren Sie anschließend auf der Seite **Einstellungen** eine Wissensdatenbank, und veröffentlichen Sie sie. Sie können [diese einfache Wissensdatenbank](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv) herunterladen und verwenden. 

    Zu den Einstellungen auf der Seite „Veröffentlichen“ zählen der Wert für die POST-Route, der Hostwert und der EndpointKey-Wert. 

    ![Veröffentlichungseinstellungen](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Der Code für diesen Schnellstart ist im Repository unter [https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/get-answer) verfügbar. 

## <a name="create-a-nodejs-file"></a>Erstellen einer Node.js-Datei

Öffnen Sie Visual Studio Code, und erstellen Sie eine neue Datei mit dem Namen `get-answer.js`. 

## <a name="add-the-required-dependencies"></a>Hinzufügen der erforderlichen Abhängigkeiten

Fügen Sie dem Projekt am Anfang der Datei `get-answer.js` die erforderlichen Abhängigkeiten hinzu:

[!code-nodejs[Add the required dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=1-4 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Hinzufügen der erforderlichen Konstanten

Fügen Sie anschließend die erforderlichen Konstanten für den Zugriff auf QnA Maker hinzu. Diese Werte werden auf der Seite **Veröffentlichen** angezeigt, nachdem Sie die Wissensdatenbank veröffentlicht haben. 

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=6-22 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Hinzufügen einer POST-Anforderung zum Senden einer Frage und Abrufen der Antwort

Der folgende Code sendet eine HTTPS-Anforderung an die QnA Maker-API, um die Frage an die Wissensdatenbank zu senden, und empfängt die Antwort:

[!code-nodejs[Add a POST request to send question to knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=24-49 "Add a POST request to send question to knowledge base")]

Der Wert des `Authorization`-Headers enthält die Zeichenfolge `EndpointKey `. 

## <a name="install-the-dependencies"></a>Installieren der Abhängigkeiten

Installieren Sie die Abhängigkeiten über die Befehlszeile:

```bash
npm install request request-promise
```

## <a name="run-the-program"></a>Ausführen des Programms

Führen Sie das Programm über die Befehlszeile aus. Das Programm sendet die Anforderung automatisch an die QnA Maker-API und zeigt die Antwort im Konsolenfenster an.

Führen Sie die Datei aus:

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz für QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)