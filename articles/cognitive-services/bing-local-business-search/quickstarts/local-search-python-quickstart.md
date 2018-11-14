---
title: 'Schnellstart: Senden einer Abfrage an die API für die Bing-Suche nach ortsansässigen Unternehmen mit Python | Microsoft-Dokumentation'
titleSuffix: Azure Cognitive Services
description: Einführung in die Verwendung der API für die Bing-Suche nach ortsansässigen Unternehmen mit Python
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 3123de58ea3dc4ab2c426f1393f8eca19542bfbf
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957117"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Schnellstart: Senden einer Abfrage an die API für die Bing-Suche nach ortsansässigen Unternehmen mit Python

Verwenden Sie diesen Schnellstart, um Anforderungen an die API für die Bing-Suche nach ortsansässigen Unternehmen zu senden, die zum Leistungsumfang von Cognitive Services gehört. Diese einfache Anwendung ist zwar in Python geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit jeder Programmiersprache kompatibel ist, die HTTP-Anforderungen stellen und JSON analysieren kann.

Diese Beispielanwendung ruft lokale Antwortdaten aus der API für die Suchabfrage `hotel in Bellevue` ab.

## <a name="prerequisites"></a>Voraussetzungen

* [Python](https://www.python.org/) 2.x oder 3.x
 
Sie benötigen ein [Cognitive Services-API-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) mit Bing-APIs. Die [kostenlose Testversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) ist für diesen Schnellstart ausreichend. Verwenden Sie den Zugriffsschlüssel der kostenlosen Testversion.

## <a name="run-the-complete-application"></a>Ausführen der vollständigen Anwendung

Der folgende Code ruft die lokalisierten Ergebnisse ab. Er wird in den folgenden Schritten implementiert:
1. Deklarieren Sie Variablen zum Angeben des Endpunkts nach Host und Pfad.
2. Geben Sie den Abfrageparameter an. 
3. Definieren Sie die Suchfunktion, die die Anforderung erstellt und den Header „Ocp-Apim-Subscription-Key“ hinzufügt.
4. Legen Sie den Header „Ocp-Apim-Subscription-Key“ fest. 
5. Stellen Sie die Verbindung her, und senden Sie die Anforderung.
6. Drucken Sie die JSON-Ergebnisse.

Der vollständige Code für diese Demo sieht wie folgt aus:

````
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com/bing'
path = '/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&appid=' + subscriptionKey + '&traffictype=Internal_monitor&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

````

## <a name="next-steps"></a>Nächste Schritte
- [Schnellstart: Suche nach ortsansässigen Unternehmen mit Java](local-search-java-quickstart.md)
- [Schnellstart: Suche nach ortsansässigen Unternehmen mit C#](local-quickstart.md)
- [Schnellstart: Suche nach ortsansässigen Unternehmen mit Node](local-search-node-quickstart.md)