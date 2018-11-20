---
title: 'Schnellstart: Erkennen von Gesichtern in einem Bild mit der Azure-REST-API und cURL'
titleSuffix: Azure Cognitive Services
description: In diesem Schnellstart verwenden Sie die Azure-Gesichtserkennungs-REST-API mit cURL, um Gesichter in einem Bild zu erkennen.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: a9e3b4713e11b5f01ea8343471aa33a327210338
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578036"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Schnellstart: Erkennen von Gesichtern in einem Bild mit der Gesichtserkennungs-REST-API und cURL

In diesem Schnellstart verwenden Sie die Azure-Gesichtserkennungs-REST-API mit cURL, um menschliche Gesichter in einem Bild zu erkennen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 

## <a name="prerequisites"></a>Voraussetzungen

- Ein Abonnementschlüssel für die Gesichtserkennungs-API. Über die Seite [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/?api=face-api) können Sie einen Abonnementschlüssel für eine kostenlose Testversion abrufen. Gehen Sie andernfalls wie unter [Schnellstart: Erstellen eines Cognitive Services-Kontos im Azure-Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) beschrieben vor, um den Gesichtserkennungs-API-Dienst zu abonnieren und Ihren Schlüssel zu erhalten.

## <a name="write-the-command"></a>Schreiben des Befehls
 
Mit ungefähr folgendem Befehl rufen Sie die Gesichtserkennungs-API auf und rufen Gesichtsattributdaten aus einem Bild ab. Kopieren Sie den Code zunächst in einen Text-Editor. Sie müssen bestimmte Teile des Befehls ändern, bevor Sie ihn ausführen können.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="subscription-key"></a>Abonnementschlüssel
Ersetzen Sie `<Subscription Key>` durch Ihren gültigen Abonnementschlüssel für die Gesichtserkennungs-API.

### <a name="face-endpoint-url"></a>Endpunkt-URL der Gesichtserkennungs-API

Die URL `https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect` gibt den Endpunkt der Azure-Gesichtserkennungs-API an, der abgefragt werden soll. Sie müssen den ersten Teil dieser URL entsprechend der Region Ihres Abonnementschlüssels ändern (es sei denn, die URL ist bereits korrekt).

### <a name="url-query-string"></a>URL-Abfragezeichenfolge

Die Abfragezeichenfolge der Endpunkt-URL der Gesichtserkennungs-API gibt an, welche Gesichtsattribute abgerufen werden. Sie können diese Zeichenfolge je nach beabsichtigter Verwendung ändern.

```
?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise
```

### <a name="image-source-url"></a>URL der Bildquelle
Die Quell-URL gibt das Bild an, das als Eingabe verwendet werden soll. Sie können den Wert ändern, um auf ein beliebiges Bild zu verweisen, das Sie analysieren möchten.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Führen Sie den folgenden Befehl aus:

Wenn Sie die gewünschten Änderungen vorgenommen haben, öffnen Sie eine Eingabeaufforderung, und geben Sie den neuen Befehl ein. Die Gesichtserkennungsinformationen sollten daraufhin als JSON-Daten im Konsolenfenster angezeigt werden. Beispiel: 

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie einen cURL-Befehl geschrieben, der die Azure-Gesichtserkennungs-API aufruft, um Gesichter in einem Bild zu erkennen und ihre Attribute zurückzugeben. Sehen Sie sich als Nächstes die Referenzdokumentation zur Gesichtserkennungs-API an, um mehr zu erfahren.

> [!div class="nextstepaction"]
> [Gesichtserkennungs-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)