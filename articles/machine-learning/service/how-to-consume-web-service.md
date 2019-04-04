---
title: Erstellen eines Clients zum Nutzen eines bereitgestellten Webdiensts
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie ein Webdienst genutzt werden kann, der bei der Bereitstellung eines Modells mit dem Azure Machine Learning-Modell generiert wurde. Der Webdienst stellt eine REST-API bereit. Erstellen Sie Clients für diese API mithilfe einer Programmiersprache Ihrer Wahl.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: a862c920f1e070ab1bbb8af2546bc3d4350347b0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889946"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Nutzen eines als Webdienst bereitgestellten Azure Machine Learning-Modells

Durch die Bereitstellung eines Azure Machine Learning-Modells als Webdienst wird eine REST-API erstellt. Sie können Daten an diese API senden und die vom Modell zurückgegebene Vorhersage empfangen. In diesem Dokument erfahren Sie, wie Sie Clients für den Webdienst mithilfe von C#, Go, Java und Python erstellen.

Sie erstellen einen Webdienst, wenn Sie ein Image für Azure Container Instances, Azure Kubernetes Service oder Project Brainwave (Field Programmable Gate Arrays) bereitstellen. Sie erstellen Images aus registrierten Modellen und Bewertungsdateien. Der URI für den Zugriff auf einen Webdienst kann über das [Azure Machine Learning SDK](https://aka.ms/aml-sdk) abgerufen werden. Wenn die Authentifizierung aktiviert ist, können Sie das SDK auch zum Abrufen der Authentifizierungsschlüssel verwenden.

Dies ist der allgemeine Workflow zum Erstellen eines Clients, der einen Machine Learning-Webdienst verwendet:

1. Verwenden des SDK zum Abrufen der Verbindungsinformationen
1. Festlegen des Typs der vom Modell verwendeten Anforderungsdaten
1. Erstellen einer Anwendung, die den Webdienst aufruft

## <a name="connection-information"></a>Verbindungsinformationen

> [!NOTE]
> Verwenden Sie das Azure Machine Learning SDK zum Abrufen der Webdienstinformationen. Dies ist ein Python SDK. Sie können eine beliebige Sprache verwenden, um einen Client für den Dienst zu erstellen.

Die [azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)-Klasse stellt die erforderlichen Informationen zum Erstellen eines Clients bereit. Die folgenden `Webservice`-Eigenschaften sind hilfreich, wenn Sie eine Clientanwendung erstellen:

* `auth_enabled`: `True`, wenn die Authentifizierung aktiviert ist, andernfalls `False`.
* `scoring_uri`: Die REST-API-Adresse.

Es gibt drei Möglichkeiten zum Abrufen dieser Informationen für bereitgestellte Webdienste:

* Wenn Sie ein Modell bereitstellen, wird ein `Webservice`-Objekt mit Informationen über den Dienst zurückgegeben:

    ```python
    service = Webservice.deploy_from_model(name='myservice',
                                           deployment_config=myconfig,
                                           models=[model],
                                           image_config=image_config,
                                           workspace=ws)
    print(service.scoring_uri)
    ```

* Mit `Webservice.list` können Sie eine Liste bereitgestellter Webdienste für Modelle in Ihrem Arbeitsbereich abrufen. Sie können Filter hinzufügen, um die Liste der zurückgegebenen Informationen einzugrenzen. Weitere Informationen zu den möglichen Filtern finden Sie in der Referenzdokumentation [Webservice.list](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py).

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    ```

* Wenn Sie den Namen des bereitgestellten Diensts kennen, können Sie eine neue Instanz von `Webservice` erstellen und den Arbeitsbereich und den Dienstnamen als Parameter angeben. Dieses neue Objekt enthält Informationen über den bereitgestellten Dienst.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    ```

### <a name="authentication-key"></a>Authentifizierungsschlüssel

Wenn Sie Authentifizierung für eine Bereitstellung aktivieren, werden automatisch Authentifizierungsschlüssel erstellt.

* Die Authentifizierung ist standardmäßig aktiviert, wenn die Bereitstellung in Azure Kubernetes Service erfolgt.
* Die Authentifizierung ist standardmäßig deaktiviert, wenn die Bereitstellung in Azure Container Instances erfolgt.

Um die Authentifizierung zu steuern, verwenden Sie den `auth_enabled`-Parameter beim Erstellen oder Aktualisieren einer Bereitstellung.

Wenn die Authentifizierung aktiviert ist, können Sie mithilfe der Methode `get_keys` einen primären und einen sekundären Authentifizierungsschlüssel abrufen:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Wenn Sie einen Schlüssel erneut generieren müssen, verwenden Sie [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

## <a name="request-data"></a>Anforderungsdaten

Die REST-API erwartet als Hauptteil der Anforderung ein JSON-Dokument mit der folgenden Struktur:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Die Struktur der Daten muss dem entsprechen, was vom Bewertungsskript und vom Modell im Dienst erwartet wird. Die Daten können vor der Übergabe an das Modell vom Bewertungsskript geändert werden.

Beispielsweise erwartet das Modell im Beispiel [Train within Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) ein Array mit 10 Ziffern. Das Bewertungsskript für dieses Beispiel erstellt ein NumPy-Array aus der Anforderung und übergibt es an das Modell. Das folgende Beispiel zeigt die Daten, die von diesem Dienst erwartet werden:

```json
{
    "data": 
        [
            [
                0.0199132141783263, 
                0.0506801187398187, 
                0.104808689473925, 
                0.0700725447072635, 
                -0.0359677812752396, 
                -0.0266789028311707, 
                -0.0249926566315915, 
                -0.00259226199818282, 
                0.00371173823343597, 
                0.0403433716478807
            ]
        ]
}
``` 

Der Webdienst kann mehrere Sätze von Daten in einer Anforderung akzeptieren. Er gibt ein JSON-Dokument mit einem Array von Antworten zurück.

### <a name="binary-data"></a>Binärdaten

Wenn Ihr Modell Binärdaten (beispielsweise ein Bild) akzeptiert, müssen Sie die für Ihre Bereitstellung verwendete Datei `score.py` so ändern, dass sie HTTP-Rohanforderungen akzeptiert. Hier ist ein Beispiel für eine Datei `score.py`, die Binärdaten akzeptiert:

```python 
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody 
        # and send to the model. Then return the response.
        
        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Der Namespace `azureml.contrib` ändert sich häufig, während wir daran arbeiten, den Dienst zu verbessern. Daher sollte alles in diesem Namespace als Vorschau und als von Microsoft nicht vollständig unterstützt betrachtet werden.
>
> Wenn Sie dies in Ihrer lokalen Entwicklungsumgebung testen müssen, können Sie die Komponenten im Namespace `contrib` mit dem folgenden Befehl installieren:
> 
> ```shell
> pip install azureml-contrib-services
> ```

## <a name="call-the-service-c"></a>Aufrufen des Diensts (C#)

In diesem Beispiel wird die Verwendung von C# zum Aufrufen des Webdiensts veranschaulicht, der im Beispiel [Train within notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) erstellt wurde:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key
            string scoringUri = "<your web service URI>";
            string authKey = "<your key>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

Die zurückgegebenen Ergebnisse ähneln dem folgenden JSON-Dokument:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Aufrufen des Diensts (Go)

In diesem Beispiel wird die Verwendung von Go zum Aufrufen des Webdiensts veranschaulicht, der im Beispiel [Train within notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) erstellt wurde:

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key (if any) for your service
var authKey string = "<your key>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

Die zurückgegebenen Ergebnisse ähneln dem folgenden JSON-Dokument:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Aufrufen des Diensts (Java)

In diesem Beispiel wird die Verwendung von Java zum Aufrufen des Webdiensts veranschaulicht, der im Beispiel [Train within notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) erstellt wurde:

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key
        String key = "<your key>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

Die zurückgegebenen Ergebnisse ähneln dem folgenden JSON-Dokument:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Aufrufen des Diensts (Python)

In diesem Beispiel wird die Verwendung von Python zum Aufrufen des Webdiensts veranschaulicht, der im Beispiel [Train within notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) erstellt wurde:

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key
key = '<your key>'

# Two sets of data to score, so we get two results back
data = {"data": 
            [
                [
                    0.0199132141783263, 
                    0.0506801187398187, 
                    0.104808689473925, 
                    0.0700725447072635, 
                    -0.0359677812752396, 
                    -0.0266789028311707, 
                    -0.0249926566315915, 
                    -0.00259226199818282, 
                    0.00371173823343597, 
                    0.0403433716478807
                ],
                [
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303]
            ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = { 'Content-Type':'application/json' }
# If authentication is enabled, set the authorization header
headers['Authorization']=f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers = headers)
print(resp.text)

```

Die zurückgegebenen Ergebnisse ähneln dem folgenden JSON-Dokument:

```JSON
[217.67978776218715, 224.78937091757172]
```
