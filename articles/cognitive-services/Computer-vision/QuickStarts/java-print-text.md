---
title: 'Schnellstart: Extrahieren von gedrucktem Text – REST, Java'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung extrahieren Sie gedruckten Text aus einem Bild, indem Sie die Maschinelles Sehen-API mit Java verwenden.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 57beb5e5dd3a77e4d8de9b631c4dc5556b2f18f5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878989"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-rest-api-and-java-in-computer-vision"></a>Schnellstart: Extrahieren von gedrucktem Text (OCR) mithilfe der REST-API und Java in der Maschinelles Sehen-API

In dieser Schnellstartanleitung extrahieren Sie mittels optischer Zeichenerkennung (Optical Character Recognition, OCR) gedruckten Text aus einem Bild, indem Sie die REST-API von Maschinelles Sehen verwenden. Mit der [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)-Methode können Sie gedruckten Text in einem Bild erkennen und erkannte Zeichen als computerlesbare Zeichenfolge extrahieren.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Die [Java&trade;-Plattform, Standard Edition Development Kit 7 oder 8](https://aka.ms/azure-jdks) (JDK 7 oder 8), muss installiert sein.
- Sie benötigen einen Abonnementschlüssel für Maschinelles Sehen. Informationen zum Beziehen eines Abonnementschlüssels finden Sie unter [Gewusst wie: Beziehen von Abonnementschlüsseln](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample-application"></a>Erstellen und Ausführen der Beispielanwendung

Führen Sie zum Erstellen und Ausführen des Beispiels die folgenden Schritte aus:

1. Erstellen Sie in Ihrer bevorzugten IDE oder in Ihrem bevorzugten Editor ein neues Java-Projekt. Wenn die Option verfügbar ist, erstellen Sie das Java-Projekt anhand einer Befehlszeilen-Anwendungsvorlage.
1. Importieren Sie die folgenden Bibliotheken in Ihr Java-Projekt. Wenn Sie Maven verwenden, werden die Maven-Koordinaten für jede Bibliothek bereitgestellt.
   - [Apache-HTTP-Client](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.5)
   - [Apache-HTTP-Core](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.9)
   - [JSON-Bibliothek](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. Fügen Sie der Datei, die die öffentliche Klasse `Main` für Ihr Projekt enthält, die folgenden `import`-Anweisungen hinzu.  

   ```java
   import java.net.URI;
   import org.apache.http.HttpEntity;
   import org.apache.http.HttpResponse;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.json.JSONObject;
   ```

1. Ersetzen Sie die öffentliche Klasse `Main` durch den folgenden Code, und nehmen Sie dann die folgenden Änderungen am Code vor, falls dies erforderlich ist:
   1. Ersetzen Sie den `subscriptionKey`-Wert durch Ihren Abonnementschlüssel.
   1. Ersetzen Sie den Wert von `uriBase` durch die Endpunkt-URL für die [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)-Methode in der Azure-Region, in der Sie Ihre Abonnementschlüssel bezogen haben, falls erforderlich.
   1. Ersetzen Sie optional den Wert von `imageToAnalyze` durch die URL eines anderen Bilds, aus dem gedruckter Text extrahiert werden soll.
1. Speichern Sie die Änderungen, und erstellen Sie dann das Java-Projekt.
1. Wenn Sie eine IDE verwenden, führen Sie `Main` aus. Öffnen Sie andernfalls ein Eingabeaufforderungsfenster, und verwenden Sie dann den `java`-Befehl, um die kompilierte Klasse auszuführen. Beispiel: `java Main`.

```java
public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the "westus" region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    private static final String uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/ocr";

    private static final String imageToAnalyze =
        "https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/" +
            "Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder uriBuilder = new URIBuilder(uriBase);

            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation", "true");

            // Request parameters.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("REST Response:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="examine-the-response"></a>Untersuchen der Antwort

Eine erfolgreiche Antwort wird im JSON-Format zurückgegeben. Die Beispielanwendung analysiert eine Antwort und zeigt diese bei erfolgreicher Ausführung im Konsolenfenster an, ähnlich wie im folgenden Beispiel:

```json
REST Response:

{
  "orientation": "Up",
  "regions": [{
    "boundingBox": "21,16,304,451",
    "lines": [
      {
        "boundingBox": "28,16,288,41",
        "words": [{
          "boundingBox": "28,16,288,41",
          "text": "NOTHING"
        }]
      },
      {
        "boundingBox": "27,66,283,52",
        "words": [{
          "boundingBox": "27,66,283,52",
          "text": "EXISTS"
        }]
      },
      {
        "boundingBox": "27,128,292,49",
        "words": [{
          "boundingBox": "27,128,292,49",
          "text": "EXCEPT"
        }]
      },
      {
        "boundingBox": "24,188,292,54",
        "words": [{
          "boundingBox": "24,188,292,54",
          "text": "ATOMS"
        }]
      },
      {
        "boundingBox": "22,253,297,32",
        "words": [
          {
            "boundingBox": "22,253,105,32",
            "text": "AND"
          },
          {
            "boundingBox": "144,253,175,32",
            "text": "EMPTY"
          }
        ]
      },
      {
        "boundingBox": "21,298,304,60",
        "words": [{
          "boundingBox": "21,298,304,60",
          "text": "SPACE."
        }]
      },
      {
        "boundingBox": "26,387,294,37",
        "words": [
          {
            "boundingBox": "26,387,210,37",
            "text": "Everything"
          },
          {
            "boundingBox": "249,389,71,27",
            "text": "else"
          }
        ]
      },
      {
        "boundingBox": "127,431,198,36",
        "words": [
          {
            "boundingBox": "127,431,31,29",
            "text": "is"
          },
          {
            "boundingBox": "172,431,153,36",
            "text": "opinion."
          }
        ]
      }
    ]
  }],
  "textAngle": 0,
  "language": "en"
}
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das Java-Projekt nicht mehr benötigen, löschen Sie es (einschließlich der kompilierten Klasse und der importierten Bibliotheken).

## <a name="next-steps"></a>Nächste Schritte

Lernen Sie eine einfache Java Swing-Anwendung kennen, die maschinelles Sehen verwendet, um eine optische Zeichenerkennung (Optical Character Recognition, OCR) durchzuführen, intelligent zugeschnittene Miniaturansichten zu erstellen sowie visuelle Merkmale (einschließlich Gesichter) in einem Bild zu erkennen, zu kategorisieren, zu markieren und zu beschreiben. Um schnell mit der Maschinelles Sehen-API zu experimentieren, probieren Sie die [Open API-Testkonsole](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console) aus.

> [!div class="nextstepaction"]
> [Computer Vision API Java Tutorial](../Tutorials/java-tutorial.md) (Java-Tutorial zur Maschinelles Sehen-API)
