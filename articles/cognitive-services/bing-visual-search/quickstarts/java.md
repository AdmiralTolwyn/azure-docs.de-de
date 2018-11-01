---
title: 'Schnellstart: Erstellen einer Abfrage für die visuelle Suche, Java – Visuelle Bing-Suche'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie ein Bild in die API für die visuelle Bing-Suche hochladen und dadurch Informationen zu diesem Bild erhalten.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: f54914b846c6a001a9fb10d938a038e390abf6bf
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50416442"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-java"></a>Schnellstart: Ihre erste Abfrage für die visuelle Bing-Suche in Java

Die API für die visuelle Bing-Suche gibt Informationen zu einem von Ihnen bereitgestellten Bild zurück. Sie können ein Bild über die Bild-URL, ein Erkenntnistoken oder durch Hochladen des Bilds bereitstellen. Informationen zu diesen Optionen finden Sie unter [Was ist die API für die visuelle Bing-Suche?](../overview.md). In diesem Artikel wird gezeigt, wie Sie ein Bild hochladen. Das Hochladen eines Bilds ist besonders in Szenarien mit einem mobilen Gerät nützlich, wenn Sie eine bekannte Sehenswürdigkeit fotografiert haben und Informationen dazu erhalten möchten. Die Informationen können z.B. Wissenswertes zur Sehenswürdigkeit beinhalten. 

Wenn Sie ein lokales Bild hochladen, müssen Sie die folgenden Formulardaten in den Text der POST-Anforderung einfügen. Die Formulardaten müssen den Header „Content-Disposition“ enthalten. Der `name`-Parameter muss auf „image“ festgelegt werden. Der `filename`-Parameter kann auf eine beliebige Zeichenfolge festgelegt werden. Der Inhalt des Formulars sind die Binärdaten des Bilds. Sie können eine maximale Bildgröße von 1 MB hochladen. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

In diesem Artikel wird eine einfache Konsolenanwendung gezeigt, die eine Anforderung an die API für die visuelle Bing-Suche sendet und die Suchergebnisse im JSON-Format anzeigt. Die Anwendung ist zwar in Java geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit jeder Programmiersprache kompatibel ist, die HTTP-Anforderung stellen und JSON analysieren kann. 


## <a name="prerequisites"></a>Voraussetzungen

Zum Kompilieren und Ausführen des Codes benötigen Sie [JDK 7 oder 8](https://aka.ms/azure-jdks). Sie können auch eine Java-Entwicklungsumgebung verwenden, ein Text-Editor ist jedoch ausreichend.

Für diese Schnellstartanleitung können Sie den Schlüssel eines [kostenlosen Testabonnements](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) oder eines kostenpflichtigen Abonnements verwenden.

## <a name="running-the-application"></a>Ausführen der Anwendung

Im Folgenden wird veranschaulicht, wie Sie das Bild mithilfe von MultipartEntityBuilder in Java hochladen.

Führen Sie die folgenden Schritte aus, um eine Anwendung auszuführen:

1. Laden Sie die [gson-Bibliothek](https://github.com/google/gson) herunter, oder installieren Sie diese. Sie können sie auch über Maven erhalten.
2. Erstellen Sie in Ihrer bevorzugten IDE oder in Ihrem bevorzugten Editor ein neues Java-Projekt.
3. Fügen Sie den bereitgestellten Code in eine Datei namens `VisualSearch.java` ein.
4. Ersetzen Sie den `subscriptionKey`-Wert durch Ihren Abonnementschlüssel.
4. Ersetzen Sie den `imagePath`-Wert durch den Pfad des Bilds, das Sie hochladen möchten.
5. Führen Sie das Programm aus.


```java
package uploadimage;

import java.util.*;
import java.io.*;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// http://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class UploadImage2 {

    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere";
    static String imagePath = "<pathtoyourimagetouploadgoeshere>";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addBinaryBody("image", new File(imagePath))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    
}
```

## <a name="next-steps"></a>Nächste Schritte

[Abrufen von Informationen zu einem Bild mithilfe eines Erkenntnistokens](../use-insights-token.md)  
[Tutorial zum Bildupload für die visuelle Bing-Suche](../tutorial-visual-search-image-upload.md)
[Tutorial zu einer Single-Page-App für die visuelle Bing-Suche](../tutorial-bing-visual-search-single-page-app.md)  
[Übersicht über die visuelle Bing-Suche](../overview.md)  
[Testen](https://aka.ms/bingvisualsearchtryforfree)  
[Abrufen eines Zugriffsschlüssels für eine kostenlose Testversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Referenz zur API für die visuelle Bing-Suche](https://aka.ms/bingvisualsearchreferencedoc)

