---
title: 'Schnellstart: Gewinnen von Erkenntnissen zu Bildern mit der REST-API für die visuelle Bing-Suche und Node.js'
titleSuffix: Azure Cognitive Services
description: Es wird beschrieben, wie Sie ein Bild in die API für die visuelle Bing-Suche hochladen und Erkenntnisse dazu gewinnen.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 5fca4e960b449988a0e77b2ecc2d0a9c8ca1988f
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53741470"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Schnellstart: Gewinnen von Erkenntnissen zu Bildern mit der REST-API für die visuelle Bing-Suche und Node.js

Verwenden Sie diese Schnellstartanleitung, um die API für die visuelle Bing-Suche zum ersten Mal aufzurufen und die Suchergebnisse anzuzeigen. Mit dieser einfachen JavaScript-Anwendung wird ein Bild in die API hochgeladen, und es werden die zurückgegebenen Informationen angezeigt. Diese Anwendung ist zwar in JavaScript geschrieben, aber die API ist ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist.

Beim Hochladen eines lokalen Bilds müssen die Formulardaten den Content-Disposition-Header enthalten. Der `name`-Parameter muss auf „image“ festgelegt werden. Der `filename`-Parameter kann auf eine beliebige Zeichenfolge festgelegt werden. Der Inhalt des Formulars sind die Binärdaten des Bilds. Sie können eine maximale Bildgröße von 1 MB hochladen.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Voraussetzungen

* [Node.js](https://nodejs.org/en/download/)
* Anforderungsmodul für JavaScript
    * Sie können dieses Modul mit `npm install request` installieren.
* Formulardatenmodul
    * Sie können dieses Modul mit `npm install form-data` installieren.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="initialize-the-application"></a>Initialisieren der Anwendung

1. Erstellen Sie eine neue JavaScript-Datei in Ihrer bevorzugten IDE oder Ihrem bevorzugten Editor, und legen Sie die folgenden Anforderungen fest:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Erstellen Sie Variablen für Ihren API-Endpunkt, den Abonnementschlüssel und den Pfad zu Ihrem Bild.

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Erstellen Sie eine Funktion mit dem Namen `requestCallback()`, um die Antwort der API auszugeben.

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Erstellen und Senden der Suchanforderung

1. Erstellen Sie mit `FormData()` ein neues Formulardatenelement, und fügen Sie Ihren Bildpfad an, indem Sie `fs.createReadStream()` verwenden.
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Verwenden Sie die Anforderungsbibliothek zum Hochladen des Bilds, indem Sie `requestCallback()` aufrufen, um die Antwort auszugeben. Achten Sie darauf, dass Sie dem Anforderungsheader Ihren Abonnementschlüssel hinzufügen. 

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Erstellen einer benutzerdefinierten Suchwebseite](../tutorial-bing-visual-search-single-page-app.md)