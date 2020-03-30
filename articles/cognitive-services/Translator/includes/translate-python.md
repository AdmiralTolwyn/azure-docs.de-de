---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 648842e86410985e3a6fb21f474b9df9d14e109d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906695"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Erstellen eines Projekts und Importieren der erforderlichen Module

Erstellen Sie in Ihrer bevorzugten IDE oder Ihrem bevorzugten Editor ein neues Python-Projekt. Kopieren Sie anschließend den folgenden Codeausschnitt in Ihr Projekt in eine Datei namens `translate-text.py`. Achten Sie darauf, dass der Interpreter Ihrer IDE auf die richtige Version von Python verweist, um zu vermeiden, dass Bibliotheken nicht erkannt werden.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Wenn Sie diese Module bisher nicht verwendet haben, müssen Sie sie vor der Ausführung Ihres Programms installieren. Führen Sie zum Installieren dieser Pakete `pip install requests uuid` aus.

Der erste Kommentar weist Ihren Python-Interpreter, UTF-8-Codierung zu verwenden. Anschließend werden erforderliche Module importiert, um Ihren Abonnementschlüssel aus einer Umgebungsvariablen zu lesen, die HTTP-Anforderung und einen eindeutigen Bezeichner zu erstellen und die von der Textübersetzungs-API zurückgegebene JSON-Antwort zu verarbeiten.

## <a name="set-the-subscription-key-endpoint-and-path"></a>Festlegen des Abonnementschlüssels, des Endpunkts und des Pfads

Dieses Beispiel liest den Textübersetzungs-Abonnementschlüssel und den Endpunkt aus den Umgebungsvariablen `TRANSLATOR_TEXT_KEY` und `TRANSLATOR_TEXT_ENDPOINT`. Wenn Sie mit Umgebungsvariablen nicht vertraut sind, können Sie `subscription_key` und `endpoint` als Zeichenfolge festlegen und die Bedingungsanweisungen auskommentieren.

Kopieren Sie diesen Code in Ihr Projekt:

```python
key_var_name = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
subscription_key = os.environ[key_var_name]

endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

Der globale Endpunkt der Textübersetzung ist als die `endpoint` festgelegt. `path` legt die `translate`-Route fest und gibt die gewünschte Version der API (Version 3) an.

`params` wird zum Festlegen der Ausgabesprachen verwendet. In diesem Beispiel wird aus dem Englischen ins Italienische und Deutsche übersetzt: `it` und `de`.

>[!NOTE]
> Weitere Informationen zu Endpunkten, Routen und Anforderungsparametern finden Sie unter [Textübersetzungs-API 3.0: Translate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate).

```python
path = '/translate?api-version=3.0'
params = '&to=de&to=it'
constructed_url = endpoint + path + params
```

## <a name="add-headers"></a>Hinzufügen von Headern

Eine Anforderung lässt sich am einfachsten authentifizieren, indem Sie den Abonnementschlüssel als `Ocp-Apim-Subscription-Key`-Header übergeben. Diese Vorgehensweise wird in diesem Beispiel verwendet. Alternativ können Sie den Abonnementschlüssel durch ein Zugriffstoken ersetzen und dieses als `Authorization`-Header übergeben, um Ihre Anforderung zu überprüfen. Weitere Informationen finden Sie unter [Authentifizierung](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Kopieren Sie diesen Codeausschnitt in Ihr Projekt:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Wenn Sie ein Cognitive Services-Abonnement mehrerer Dienste verwenden, müssen Sie auch `Ocp-Apim-Subscription-Region` in Ihre Anforderungsparameter aufnehmen. [Erfahren Sie mehr über die Authentifizierung mit dem Abonnement für mehrere Dienste](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-translate-text"></a>Erstellen einer Anforderung zum Übersetzen von Text

Definieren Sie die Zeichenfolge (oder Zeichenfolgen), die Sie übersetzen möchten:

```python
body = [{
    'text': 'Hello World!'
}]
```

Erstellen Sie als Nächstes mithilfe des `requests`-Moduls eine POST-Anforderung. Sie umfasst drei Argumente: die verkettete URL, der Anforderungsheader und der Anforderungstext:

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>Ausgeben der Antwort

Im letzten Schritt werden die Ergebnisse ausgegeben. In diesem Codeausschnitt werden die Ergebnisse übersichtlicher gemacht, indem die Schlüssel sortiert, Einzüge festgelegt und Element- und Schlüsseltrennzeichen deklariert werden.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Korrektes Zusammenfügen

Das war's: Sie haben ein einfaches Programm erstellt, das die Textübersetzungs-API aufruft und eine JSON-Antwort zurückgibt. Führen Sie das Programm jetzt aus:

```console
python translate-text.py
```

[Auf GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python) finden Sie das vollständige Beispiel, falls Sie Ihren Code mit unserem Code vergleichen möchten.

## <a name="sample-response"></a>Beispiel für eine Antwort

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "text": "Hallo Welt!",
                "to": "de"
            },
            {
                "text": "Salve, mondo!",
                "to": "it"
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Abonnementschlüssel in Ihrem Programm hartcodiert haben, entfernen Sie ihn unbedingt, wenn Sie diese Schnellstartanleitung abgeschlossen haben.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die API-Referenz an, um zu erfahren, welche Möglichkeiten die Textübersetzungs-API bietet.

> [!div class="nextstepaction"]
> [API-Referenz](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
