---
title: 'Schnellstart: Generieren einer Miniaturansicht – REST, JavaScript – Maschinelles Sehen'
titleSuffix: Azure Cognitive Services
description: In diesem Schnellstart generieren Sie eine Miniaturansicht von einem Bild, indem Sie die Maschinelles Sehen-API mit JavaScript verwenden.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: ef239b2ceb9205f1984e6e64275fa0aca0fdf58f
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852740"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-javascript-in-computer-vision"></a>Schnellstart: Generieren einer Miniaturansicht mit der REST-API und JavaScript in der Maschinelles Sehen-API

In diesem Schnellstart generieren Sie eine Miniaturansicht von einem Bild, indem Sie die REST-API von Maschinelles Sehen verwenden. Mit der [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)-Methode können Sie eine Miniaturansicht von einem Bild generieren. Sie geben die Höhe und Breite an. Diese kann sich vom Seitenverhältnis des Eingabebilds unterscheiden. Für das maschinelle Sehen wird die intelligente Zuschneidefunktion verwendet, um die gewünschte Region zu identifizieren und basierend auf dieser Region Zuschneidekoordinaten zu generieren.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen einen Abonnementschlüssel für maschinelles Sehen. Informationen zum Beziehen eines Abonnementschlüssels finden Sie unter [Gewusst wie: Beziehen von Abonnementschlüsseln](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Erstellen und Ausführen des Beispiels

Führen Sie zum Erstellen und Ausführen des Beispiels die folgenden Schritte aus:

1. Kopieren Sie den folgenden Code in einen Text-Editor.
1. Nehmen Sie bei Bedarf die folgenden Änderungen im Code vor:
    1. Ersetzen Sie den `subscriptionKey`-Wert durch Ihren Abonnementschlüssel.
    1. Ersetzen Sie den Wert von `uriBase` durch die Endpunkt-URL für die Methode [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) in der Azure-Region, in der Sie Ihre Abonnementschlüssel bezogen haben, falls erforderlich.
    1. Ersetzen Sie optional den Wert des Attributs `value` für das Steuerelement `inputImage` durch die URL eines anderen Bilds, das Sie analysieren möchten.
1. Speichern Sie den Code als Datei mit der Erweiterung `.html`. Beispiel: `get-thumbnail.html`.
1. Öffnen Sie ein Browserfenster.
1. Ziehen Sie die Datei in das Browserfenster, und legen Sie sie dort ab.
1. Wenn die Webseite im Browser angezeigt wird, klicken Sie auf die Schaltfläche **Miniaturansicht generieren**.

```html
<!DOCTYPE html>
<html>
<head>
    <title>Thumbnail Sample</title>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace <Subscription Key> with your valid subscription key.
        var subscriptionKey = "<Subscription Key>";

        // You must use the same Azure region in your REST API method as you used to
        // get your subscription keys. For example, if you got your subscription keys
        // from the West US region, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the "westus" region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        var uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail";

        // Request parameters.
        var params = "?width=100&height=150&smartCropping=true";

        // Display the source image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        // Prepare the REST API call:

        // Create the HTTP Request object.
        var xhr = new XMLHttpRequest();

        // Identify the request as a POST, with the URL and parameters.
        xhr.open("POST", uriBase + params);

        // Add the request headers.
        xhr.setRequestHeader("Content-Type","application/json");
        xhr.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

        // Set the response type to "blob" for the thumbnail image data.
        xhr.responseType = "blob";

        // Process the result of the REST API call.
        xhr.onreadystatechange = function(e) {
            if(xhr.readyState === XMLHttpRequest.DONE) {

                // Thumbnail successfully created.
                if (xhr.status === 200) {
                    // Show response headers.
                    var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                    document.getElementById("responseTextArea").value =
                        JSON.stringify(xhr.getAllResponseHeaders(), null, 2);

                    // Show thumbnail image.
                    var urlCreator = window.URL || window.webkitURL;
                    var imageUrl = urlCreator.createObjectURL(this.response);
                    document.querySelector("#thumbnailImage").src = imageUrl;
                } else {
                    // Display the error message. The error message is the response
                    // body as a JSON string. The code in this code block extracts
                    // the JSON string from the blob response.
                    var reader = new FileReader();

                    // This event fires after the blob has been read.
                    reader.addEventListener('loadend', (e) => {
                        document.getElementById("responseTextArea").value =
                            JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                    });

                    // Start reading the blob as text.
                    reader.readAsText(xhr.response);
                }
            }
        }

        // Make the REST API call.
        xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
    };
</script>

<h1>Generate thumbnail image:</h1>
Enter the URL to an image to use in creating a thumbnail image,
then click the <strong>Generate thumbnail</strong> button.
<br><br>
Image for thumbnail:
<input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg" />
<button onclick="processImage()">Generate thumbnail</button>
<br><br>
<div id="wrapper" style="width:1160px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
    <div id="thumbnailDiv" style="width:140px; display:table-cell;">
        Thumbnail:
        <br><br>
        <img id="thumbnailImage" />
    </div>
</div>
</body>
</html>
```

## <a name="examine-the-response"></a>Untersuchen der Antwort

Eine erfolgreiche Antwort wird als Binärdaten zurückgegeben, die die Bilddaten für die Miniaturansicht darstellen. Wenn die Anforderung erfolgreich ist, wird die Miniaturansicht anhand der Binärdaten in der Antwort generiert und im Browserfenster angezeigt. Wenn bei der Anforderung ein Fehler auftritt, wird die Antwort im Konsolenfenster angezeigt. Die Antwort für die fehlerhafte Anforderung enthält einen Fehlercode und eine Meldung, damit festgestellt werden kann, was nicht funktioniert hat.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Datei nicht mehr benötigen, löschen Sie sie.

## <a name="next-steps"></a>Nächste Schritte

Lernen Sie eine JavaScript-Anwendung kennen, die maschinelles Sehen verwendet, um eine optische Zeichenerkennung (Optical Character Recognition, OCR) durchzuführen, intelligent zugeschnittene Miniaturansichten zu erstellen sowie visuelle Merkmale (einschließlich Gesichter) in einem Bild zu erkennen, zu kategorisieren, zu markieren und zu beschreiben. Um schnell mit der Maschinelles Sehen-API zu experimentieren, probieren Sie die [Open API-Testkonsole](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console) aus.

> [!div class="nextstepaction"]
> [Tutorial zur Maschinelles Sehen-API mit JavaScript](../Tutorials/javascript-tutorial.md)
