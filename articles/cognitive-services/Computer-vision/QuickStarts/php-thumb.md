---
title: 'Schnellstart: Generieren einer Miniaturansicht – REST, PHP'
titleSuffix: Azure Cognitive Services
description: In diesem Schnellstart generieren Sie eine Miniaturansicht von einem Bild, indem Sie die Maschinelles Sehen-API mit PHP verwenden.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 1527f57a1d300f730222d100bc9caf2001098fea
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579344"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-php-in-computer-vision"></a>Schnellstart: Generieren einer Miniaturansicht mit der REST-API und PHP in der Maschinelles Sehen-API

In diesem Schnellstart generieren Sie eine Miniaturansicht von einem Bild, indem Sie die REST-API von Maschinelles Sehen verwenden. Mit der [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)-Methode können Sie eine Miniaturansicht von einem Bild generieren. Sie geben die Höhe und Breite an. Diese kann sich vom Seitenverhältnis des Eingabebilds unterscheiden. Für das maschinelle Sehen wird die intelligente Zuschneidefunktion verwendet, um den gewünschten Bereich zu identifizieren und basierend auf dieser Region Zuschneidekoordinaten zu generieren.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- [PHP](https://secure.php.net/downloads.php) muss installiert sein.
- [PEAR](https://pear.php.net) muss installiert sein.
- Sie benötigen einen Abonnementschlüssel für Maschinelles Sehen. Informationen zum Beziehen eines Abonnementschlüssels finden Sie unter [Gewusst wie: Beziehen von Abonnementschlüsseln](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Erstellen und Ausführen des Beispiels

Führen Sie zum Erstellen und Ausführen des Beispiels die folgenden Schritte aus:

1. Installieren Sie das PHP5-Paket [`HTTP_Request2`](https://pear.php.net/package/HTTP_Request2).
   1. Öffnen Sie ein Eingabeaufforderungsfenster als Administrator.
   1. Führen Sie den folgenden Befehl aus:

      ```console
      pear install HTTP_Request2
      ```

   1. Wenn das Paket erfolgreich installiert wurde, schließen Sie das Eingabeaufforderungsfenster.

1. Kopieren Sie den folgenden Code, und fügen Sie ihn in einen Text-Editor ein.
1. Nehmen Sie bei Bedarf die folgenden Änderungen im Code vor:
    1. Ersetzen Sie den `subscriptionKey`-Wert durch Ihren Abonnementschlüssel.
    1. Ersetzen Sie den Wert von `uriBase` durch die Endpunkt-URL für die Methode [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) in der Azure-Region, in der Sie Ihre Abonnementschlüssel bezogen haben, falls erforderlich.
    1. Ersetzen Sie den Wert von `imageUrl` optional durch die URL eines anderen Bilds, für das Sie eine Miniaturansicht generieren möchten.
1. Speichern Sie den Code als Datei mit der Erweiterung `.php`. Beispiel: `get-thumbnail.php`.
1. Öffnen Sie ein Browserfenster mit PHP-Unterstützung.
1. Ziehen Sie die Datei in das Browserfenster, und legen Sie sie dort ab.

```php
<html>
<head>
    <title>Get Thumbnail Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'generateThumbnail');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'width' => '100',  // Width of the thumbnail.
    'height' => '100', // Height of the thumbnail.
    'smartCropping' => 'true',
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="examine-the-response"></a>Untersuchen der Antwort

Eine erfolgreiche Antwort wird als Binärdaten zurückgegeben, die die Bilddaten für die Miniaturansicht darstellen. Wenn bei der Anforderung ein Fehler auftritt, wird die Antwort im Browserfenster angezeigt. Die Antwort für die fehlerhafte Anforderung enthält einen Fehlercode und eine Meldung, damit festgestellt werden kann, was nicht funktioniert hat.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Datei nicht mehr benötigen, löschen Sie sie, und deinstallieren Sie dann das PHP5-Paket `HTTP_Request2`. Führen Sie die folgenden Schritte durch, um das Paket zu deinstallieren:

1. Öffnen Sie ein Eingabeaufforderungsfenster als Administrator.
2. Führen Sie den folgenden Befehl aus:

   ```console
   pear uninstall HTTP_Request2
   ```

3. Wenn das Paket erfolgreich deinstalliert wurde, schließen Sie das Eingabeaufforderungsfenster.

## <a name="next-steps"></a>Nächste Schritte

Erkunden Sie die Maschinelles Sehen-API, die zum Analysieren von Bildern, Erkennen von Prominenten und Sehenswürdigkeiten, Erstellen von Miniaturansichten und Extrahieren von gedrucktem sowie handschriftlichem Text verwendet wird. Wenn Sie schnell mit Ihren Experimenten mit der Maschinelles Sehen-API beginnen möchten, können Sie die [Open API-Testkonsole](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console) verwenden.

> [!div class="nextstepaction"]
> [Erkunden der Maschinelles Sehen-API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
