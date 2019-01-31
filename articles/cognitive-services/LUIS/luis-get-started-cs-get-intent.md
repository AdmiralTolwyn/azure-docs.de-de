---
title: Abrufen der Absicht, C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: In dieser C#-Schnellstartanleitung bestimmen Sie mithilfe einer verfügbaren öffentlichen LUIS-App aus Konversationstext die Absicht eines Benutzers.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 81a05ed3e981c2a35221830a16b5859ee5c1bd4f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225515"
---
# <a name="quickstart-get-intent-using-c"></a>Schnellstart: Abrufen der Absicht mit C#

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Voraussetzungen

* [Visual Studio Community 2017 Edition](https://visualstudio.microsoft.com/vs/community/)
* Programmiersprache C# (in VS Community 2017 enthalten)
* ID der öffentlichen App: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Abrufen des LUIS-Schlüssels

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Abrufen der Absicht im Browser

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Programmgesteuertes Abrufen der Absicht

Verwenden Sie C# zum Abfragen der GET-[API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) des Vorhersageendpunkts, um die gleichen Ergebnisse wie im vorherigen Abschnitt im Browserfenster zu erhalten. 

1. Erstellen Sie eine neue Konsolenanwendung in Visual Studio. 

    ![Erstellen einer neuen Konsolenanwendung in Visual Studio](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. Klicken Sie im Visual Studio-Projekt im Projektmappen-Explorer auf **Verweis hinzufügen**, und wählen Sie dann auf der Registerkarte „Assemblys“ den Eintrag **System.Web** aus.

    ![Wählen Sie „Verweis hinzufügen“ und dann „System.Web“ auf der Registerkarte „Assemblys“ aus.](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Überschreiben Sie „Program.cs“ mit dem folgenden Code:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Ersetzen Sie den Wert von `YOUR_KEY` durch Ihren LUIS-Schlüssel.

5. Erstellen Sie die Konsolenanwendung, und führen Sie sie aus. Es wird das gleiche JSON-Ergebnis wie zuvor im Browserfenster angezeigt.

    ![Konsolenfenster mit JSON-Ergebnis aus LUIS](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>LUIS-Schlüssel

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Schließen Sie nach Beendigung dieser Schnellstartanleitung das Visual Studio-Projekt, und entfernen Sie das Projektverzeichnis aus dem Dateisystem. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen von Äußerungen und Trainieren mit C#](luis-get-started-cs-add-utterance.md)
