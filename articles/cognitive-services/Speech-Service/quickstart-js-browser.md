---
title: 'Schnellstart: Erkennen von Sprache in JavaScript in einem Browser mit dem Spracherkennungsdienst-SDK'
titleSuffix: Azure Cognitive Services
description: Hier finden Sie Informationen zum Erkennen von Sprache in JavaScript in einem Browser mit dem Spracherkennungsdienst-SDK.
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: fmegen
ms.openlocfilehash: 1e844f83c715dd8ac78a3340628988625e95a4ba
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218439"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-speech-service-sdk"></a>Schnellstart: Erkennen von Sprache in JavaScript in einem Browser mit dem Spracherkennungsdienst-SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Dieser Artikel enthält Informationen zur Erstellung einer Website mithilfe der JavaScript-Bindung des Cognitive Services Speech SDK zum Umwandeln von Sprache in Text.
Die Anwendung basiert auf dem Microsoft Cognitive Services Speech SDK. ([Version 1.1.0 herunterladen](https://aka.ms/csspeech/jsbrowserpackage))

## <a name="prerequisites"></a>Voraussetzungen

* Ein Abonnementschlüssel für den Spracherkennungsdienst. Weitere Informationen finden Sie unter [Kostenloses Testen des Spracherkennungsdiensts](get-started.md).
* Ein PC oder Mac mit einem funktionierenden Mikrofon
* Ein Texteditor
* Eine aktuelle Version von Chrome oder Microsoft Edge
* Optional einen Webserver, der das Hosten von PHP-Skripts unterstützt

## <a name="create-a-new-website-folder"></a>Erstellen eines neuen Websiteordners

Erstellen Sie einen neuen leeren Ordner. Wenn Sie das Beispiel auf einem Webserver hosten möchten, stellen Sie sicher, dass der Webserver auf den Ordner zugreifen kann.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Entpacken des Speech SDK für JavaScript in diesen Ordner

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Laden Sie das Speech SDK als [ZIP-Paket](https://aka.ms/csspeech/jsbrowserpackage) herunter, und entpacken Sie es in den neu erstellten Ordner. Dabei sollten zwei Dateien entpackt werden, nämlich `microsoft.cognitiveservices.speech.sdk.bundle.js` und `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Die zweite Datei ist optional und wird bei Bedarf zur Unterstützung beim Debuggen im SDK-Code verwendet.

## <a name="create-an-indexhtml-page"></a>Erstellen einer Seite „index.html“

Erstellen Sie eine neue Datei im Ordner `index.html`, und öffnen Sie diese Datei mit einem Text-Editor.

1. Erstellen Sie das folgende HTML-Gerüst:

  ```html
  <html>
  <head>
      <title>Microsoft Cognitive Service Speech SDK JavaScript Quickstart</title>
  </head>
  <body>
    <!-- UI code goes here -->

    <!-- SDK reference goes here -->

    <!-- Optional authorization token request goes here -->

    <!-- Sample code goes here -->
  </body>
  </html>
  ```

1. Fügen Sie den folgenden Benutzeroberflächencode unter dem ersten Kommentar in Ihrer Datei hinzu:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. Hinzufügen eines Verweises auf das Speech SDK

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. Verknüpfen Sie die Handler für die Erkennungsschaltfläche, das Erkennungsergebnis und die mit dem Abonnement verknüpften Felder, die durch den Benutzeroberflächencode definiert werden:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>Erstellen der Tokenquelle (optional)

Wenn Sie die Webseite auf einem Webserver hosten möchten, können Sie optional eine Tokenquelle für die Demoanwendung bereitstellen.
Auf diese Weise verlässt Ihr Abonnementschlüssel nie den Server, während Benutzer gleichzeitig die Sprachfunktionen verwenden können, ohne selbst Autorisierungscode eingeben zu müssen.

1. Erstellen Sie eine neue Datei mit dem Namen `token.php`. In diesem Beispiel wird davon ausgegangen, dass der Webserver die PHP-Skriptsprache unterstützt. Geben Sie den folgenden Code ein:

  [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. Bearbeiten Sie die Datei `index.html`, und fügen Sie folgenden Code in Ihre Datei ein:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> Autorisierungstoken haben nur eine begrenzte Lebensdauer.
> Dieses vereinfachte Beispiel zeigt nicht, wie Autorisierungstoken automatisch aktualisiert werden. Als Benutzer können Sie die Seite manuell laden oder F5 drücken, um sie zu aktualisieren.

## <a name="build-and-run-the-sample-locally"></a>Erstellen und lokales Ausführen des Beispiels

Um die App zu starten, doppelklicken Sie auf die Datei „index.html“, oder öffnen Sie die Datei „index.html“ mit Ihren bevorzugten Browser. Es wird eine einfache Oberfläche angezeigt, auf der Sie Ihren Abonnementschlüssel und die [Region](regions.md) eingeben und eine Erkennung über das Mikrofon auslösen können.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Erstellen und Ausführen des Beispiels über einen Webserver

Starten Sie Ihre App, öffnen Sie Ihren bevorzugten Browser, und verweisen Sie in diesem auf die öffentliche URL, unter der Sie den Ordner hosten. Geben Sie Ihre [Region](regions.md) ein, und lösen Sie über das Mikrofon eine Erkennung aus. Wenn dies konfiguriert wurde, wird ein Token aus der Tokenquelle abgerufen.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Suchen Sie nach diesem Beispiel im Ordner `quickstart/js-browser`.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Abrufen der Beispiele](speech-sdk.md#get-the-samples)
