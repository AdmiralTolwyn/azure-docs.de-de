---
title: Arbeiten mit Proxys in Azure Functions | Microsoft-Dokumentation
description: "Übersicht zum Verwenden von Azure Functions-Proxys"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/11/2017
ms.author: alkarche
ms.openlocfilehash: 24bc439b6167d335a0862aa93debb9efe5aeae48
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2017
---
# <a name="work-with-azure-functions-proxies"></a>Verwenden von Azure-Funktionsproxys

In diesem Artikel werden das Konfigurieren von Azure Functions-Proxys und das Arbeiten mit ihnen erläutert. Mit diesem Feature können Sie in Ihrer Funktionen-App Endpunkte angeben, die von einer anderen Ressource implementiert werden. Sie können mit diesen Proxys eine große API in mehrere Funktionen-Apps aufteilen (wie in einer Microservicearchitektur), wobei Clients dennoch eine einzelne API-Oberfläche präsentiert wird.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Für Proxyausführungen gilt die standardmäßige Functions-Abrechnung. Weitere Informationen finden Sie unter [Azure Functions – Preise](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>Erstellen eines Proxys

In diesem Abschnitt erfahren Sie, wie Sie einen Proxy im Functions-Portal erstellen.

1. Öffnen Sie das [Azure-Portal], und navigieren Sie dann zu Ihrer Funktionen-App.
2. Wählen Sie im linken Bereich **Neuer Proxy** aus.
3. Geben Sie einen Namen für Ihren Proxy an.
4. Konfigurieren Sie den in dieser Funktionen-App verfügbar gemachten Endpunkt durch Angeben von **Routenvorlage** und **HTTP-Methoden**. Diese Parameter verhalten sich entsprechend den Regeln für [HTTP-Triggern].
5. Legen Sie für die **Back-End-URL** einen anderen Endpunkt fest. Dieser Endpunkt könnte eine Funktion in einer anderen Funktionen-App oder eine beliebige andere API sein. Der Wert muss nicht statisch sein und kann auf [Anwendungseinstellungen] und [Parameter aus der ursprünglichen Clientanforderung] verweisen.
6. Klicken Sie auf **Erstellen**.

Ihr Proxy ist jetzt als neuer Endpunkt in Ihrer Funktionen-App vorhanden. Aus Sicht eines Clients entspricht dies einem HttpTrigger in Azure Functions. Sie können Ihren neuen Proxy ausprobieren, indem Sie die Proxy-URL kopieren und mit Ihrem bevorzugten HTTP-Client testen.

## <a name="modify-requests-responses"></a>Ändern von Anforderungen und Antworten

Mit Azure Functions-Proxys können Sie Anforderungen an und Antworten vom Back-End ändern. Diese Transformationen können Variablen gemäß Definition in [Verwenden von Variablen] verwenden.

### <a name="modify-backend-request"></a>Ändern der Back-End-Anforderung

Standardmäßig wird die Back-End-Anforderung als Kopie der ursprünglichen Anforderung initialisiert. Zusätzlich zum Festlegen der Back-End-URL können Sie die HTTP-Methode, Header und Abfragezeichenfolgen-Parameter ändern. Die geänderten Werte können auf [Anwendungseinstellungen] und [Parameter aus der ursprünglichen Clientanforderung] verweisen.

Das Ändern von Back-End-Anforderungen ist gegenwärtig im Portal nicht möglich. Unter [Definieren eines requestOverrides-Objekts] erfahren Sie, wie Sie diese Funktion von „proxies.json“ anwenden.

### <a name="modify-response"></a>Ändern der Antwort

Standardmäßig wird die Clientantwort als Kopie der Back-End-Antwort initialisiert. Sie können Änderungen an Statuscode, Ursachentext, Headern und Text der Antwort vornehmen. Die geänderten Werte können auf [Anwendungseinstellungen], [Parameter aus der ursprünglichen Clientanforderung] und [Parameter aus der Back-End-Antwort] verweisen.

Das Ändern von Antworten ist gegenwärtig im Portal nicht möglich. Unter [Definieren eines responseOverrides-Objekts] erfahren Sie, wie Sie diese Funktion von „proxies.json“ anwenden.

## <a name="using-variables"></a>Verwenden von Variablen

Die Konfiguration für einen Proxy muss nicht statisch sein. Sie können ihn zur Verwendung von Variablen aus der ursprünglichen Anforderung, der Back-End-Antwort oder Anwendungseinstellungen konditionieren.

### <a name="request-parameters"></a>Verweisen auf Anforderungsparameter

Sie können Anforderungsparameter als Eingaben für die Back-End-URL-Eigenschaft oder im Rahmen der Änderung von Anforderungen und Antworten verwenden. Einige Parameter können der Routenvorlage entnommen werden, die in der Basisproxykonfiguration angegeben wird, und andere können von Eigenschaften der eingehenden Anforderung stammen.

#### <a name="route-template-parameters"></a>Routenvorlagenparameter
Auf Parameter, die in der Routenvorlage verwendet werden, kann mit dem Namen verwiesen werden. Die Parameternamen werden in geschweifte Klammern ({}) eingeschlossen.

Wenn ein Proxy etwa eine Routenvorlage wie `/pets/{petId}` hat, kann die Back-End-URL den Wert von `{petId}` enthalten, wie in `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Wenn die Routenvorlage mit einem Platzhalter wie z.B. `/api/{*restOfPath}` endet, ist der Wert `{restOfPath}` eine Zeichenfolgendarstellung der verbleibenden Pfadsegmente aus der eingehenden Anforderung.

#### <a name="additional-request-parameters"></a>Zusätzliche Anforderungsparameter
Zusätzlich zu den Routenvorlagenparametern können die folgenden Werte in Konfigurationswerten verwendet werden:

* **{request.method}**: Die HTTP-Methode, die in der ursprünglichen Anforderung verwendet wird.
* **{request.headers.\<HeaderName\>}**: Ein Header, der aus der ursprünglichen Anforderung gelesen werden kann. Ersetzen Sie *\<HeaderName\>* durch den Namen des Headers, den Sie lesen möchten. Wenn der Header nicht in der Anforderung enthalten ist, wird der Wert eine leere Zeichenfolge sein.
* **{request.querystring.\<ParameterName\>}**: Ein Abfragezeichenfolgen-Parameter, der aus der ursprünglichen Anforderung gelesen werden kann. Ersetzen Sie *\<ParameterName\>* durch den Namen des Parameters, den Sie lesen möchten. Wenn der Parameter nicht in der Anforderung enthalten ist, wird der Wert eine leere Zeichenfolge sein.

### <a name="response-parameters"></a>Verweisen auf Back-End-Antwortparameter

Antwortparameter können als Teil der Änderung der Antwort an den Client verwendet werden. Die folgenden Werte können in Konfigurationswerten verwendet werden:

* **{backend.response.statusCode}**: Der HTTP-Statuscode, der in der Back-End-Antwort zurückgegeben wird.
* **{backend.response.statusReason}**: Der HTTP-Ursachentext, der in der Back-End-Antwort zurückgegeben wird.
* **{backend.response.headers.\<HeaderName\>}**: Ein Header, der aus der Back-End-Antwort gelesen werden kann. Ersetzen Sie *\<HeaderName\>* durch den Namen des Headers, den Sie lesen möchten. Wenn der Header nicht in der Anforderung enthalten ist, wird der Wert eine leere Zeichenfolge sein.

### <a name="use-appsettings"></a>Verweisen auf Anwendungseinstellungen

Sie können auch auf [Anwendungseinstellungen verweisen, die für die Funktionen-App definiert sind](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop), indem Sie den Namen der Einstellung in Prozentzeichen (%) einschließen.

Bei der Back-End-URL *https://%ORDER_PROCESSING_HOST%/api/orders* wird z.B. „%ORDER_PROCESSING_HOST%“ durch den Wert der Einstellung ORDER_PROCESSING_HOST ersetzt.

> [!TIP] 
> Verwenden Sie Anwendungseinstellungen für Back-End-Hosts, wenn Sie mehrere Bereitstellungen oder Testumgebungen haben. Auf diese Weise können Sie sicherstellen, dass Sie immer mit dem richtigen Back-End für die jeweilige Umgebung kommunizieren.

## <a name="advanced-configuration"></a>Erweiterte Konfiguration

Die Proxys, die Sie konfigurieren, werden in einer Datei „proxies.json“ gespeichert, die sich im Stamm eines Funktionen-App-Verzeichnisses befindet. Sie können diese Datei manuell bearbeiten und als Teil Ihrer App bereitstellen, wenn Sie eine der [Bereitstellungsmethoden](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) verwenden, die Functions unterstützt. Das Feature muss [aktiviert](#enable) werden, damit die Datei verarbeitet wird. 

> [!TIP] 
> Wenn Sie keine der Bereitstellungsmethoden eingerichtet haben, können Sie auch mit der Datei „proxies.json“ im Portal arbeiten. Wechseln Sie zu Ihrer Funktionen-App, und wählen Sie **Plattformfeatures** und dann **App Service-Editor** aus. So können Sie die gesamte Dateistruktur Ihrer Funktionen-App anzeigen und Änderungen vornehmen.

„Proxies.json“ wird von einem Proxys-Objekt definiert, das aus benannten Proxys und ihren Definitionen besteht. Wenn Ihr Editor dies unterstützt, können Sie zur Codevervollständigung optional auf ein [JSON-Schema](http://json.schemastore.org/proxies) verweisen. Eine Beispieldatei könnte folgendermaßen aussehen:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Jeder Proxy hat einen Anzeigenamen, wie z.B. *proxy1* im vorherigen Beispiel. Das entsprechende Proxydefinitionsobjekt wird durch die folgenden Eigenschaften definiert:

* **matchCondition**: Erforderlich – ein Objekt, das die Anforderungen definiert, die die Ausführung dieses Proxys auslösen. Es enthält zwei Eigenschaften, die es mit [HTTP-Triggern] gemeinsam hat:
    * _Methoden_: Ein Array der HTTP-Methoden, auf die der Proxy antwortet. Wenn es nicht angegeben wird, antwortet der Proxy auf alle HTTP-Methoden in der Route.
    * _route_: Erforderlich – definiert die Routenvorlage, mit der gesteuert wird, auf welche Anforderungs-URLs Ihr Proxy antwortet. Im Gegensatz zu HTTP-Triggern ist dies kein Standardwert.
* **backendUri**: Die URL der Back-End-Ressource, zur der die Anforderung über einen Proxy gesendet werden soll. Dieser Wert kann auf Anwendungseinstellungen und Parameter aus der ursprünglichen Clientanforderung verweisen. Wenn diese Eigenschaft nicht enthalten ist, antwortet Azure Functions mit „HTTP 200 OK“.
* **requestOverrides**: Ein Objekt, das Transformationen der Back-End-Anforderung definiert. Siehe [Definieren eines requestOverrides-Objekts].
* **responseOverrides**: Ein Objekt, das Transformationen der Clientantwort definiert. Siehe [Definieren eines responseOverrides-Objekts].

> [!NOTE] 
> Die Azure Functions-Proxys berücksichtigen die Eigenschaft „routePrefix“ der Functions-Hostkonfiguration nicht. Wenn Sie ein Präfix wie „/api“ einschließen möchten, muss es in der Eigenschaft „route“ enthalten sein.

### <a name="requestOverrides"></a>Definieren eines requestOverrides-Objekts

Das requestOverrides-Objekt definiert Änderungen an der Anforderung, wenn die Back-End-Ressource aufgerufen wird. Das Objekt wird durch folgende Eigenschaften definiert:

* **backend.request.method**: Die HTTP-Methode, mit der das Back-End aufgerufen wird.
* **backend.request.querystring.\<ParameterName\>**: Ein Abfragezeichenfolgen-Parameter, der für den Aufruf des Back-Ends festgelegt werden kann. Ersetzen Sie *\<ParameterName\>* durch den Namen des Parameters, den Sie festlegen möchten. Wenn die leere Zeichenfolge angegeben wird, wird der Parameter nicht in die Back-End-Anforderung einbezogen.
* **backend.request.headers.\<HeaderName\>**: Ein Header, der für den Aufruf des Back-Ends festgelegt werden kann. Ersetzen Sie *\<HeaderName\>* durch den Namen des Headers, den Sie festlegen möchten. Wenn Sie die leere Zeichenfolge angeben, wird der Header nicht in die Back-End-Anforderung einbezogen.

Die Werte können auf Anwendungseinstellungen und Parameter aus der ursprünglichen Clientanforderung verweisen.

Eine Beispielkonfiguration könnte folgendermaßen aussehen:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="responseOverrides"></a>Definieren eines responseOverrides-Objekts

Das responseOverrides-Objekt definiert Änderungen an der Antwort, die an den Client zurückgegeben wird. Das Objekt wird durch folgende Eigenschaften definiert:

* **response.statusCode**: Der HTTP-Statuscode, der an den Client zurückgegeben werden soll.
* **response.statusReason**: Der HTTP-Ursachentext, der an den Client zurückgegeben werden soll.
* **response.body**: Die Zeichenfolgendarstellung des Texts, der an den Client zurückgegeben werden soll.
* **response.headers.\<HeaderName\>**: Ein Header, der für die Antwort an den Client festgelegt werden kann. Ersetzen Sie *\<HeaderName\>* durch den Namen des Headers, den Sie festlegen möchten. Wenn Sie die leere Zeichenfolge angeben, wird der Header nicht in die Antwort einbezogen.

Die Werte können auf Anwendungseinstellungen, Parameter aus der ursprünglichen Clientanforderung und Parameter aus der Back-End-Antwort verweisen.

Eine Beispielkonfiguration könnte folgendermaßen aussehen:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> In diesem Beispiel wird der Text direkt festgelegt, sodass keine `backendUri`-Eigenschaft erforderlich ist. Das Beispiel zeigt, wie Sie mit Azure Functions-Proxys APIs imitieren können.

## <a name="enable"></a>Aktivieren von Azure Functions-Proxys

Proxys sind jetzt standardmäßig aktiviert. Wenn Sie eine ältere Version der Proxyvorschau verwendet und Proxys deaktiviert haben, müssen Sie die Proxys einmal manuell aktivieren, damit Proxys ausgeführt werden.

1. Öffnen Sie das [Azure-Portal], und navigieren Sie dann zu Ihrer Funktionen-App.
2. Wählen Sie **Funktionen-App-Einstellungen**.
3. Schalten Sie **Azure Functions-Proxys aktivieren (Vorschau)** auf **Ein**.

Sie können auch zu diesem Schritt zurückkehren, um die Proxylaufzeit zu aktualisieren, sobald neue Features verfügbar sind.

[Azure-Portal]: https://portal.azure.com
[HTTP-Triggern]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definieren eines requestOverrides-Objekts]: #requestOverrides
[Definieren eines responseOverrides-Objekts]: #responseOverrides
[Anwendungseinstellungen]: #use-appsettings
[Verwenden von Variablen]: #using-variables
[Parameter aus der ursprünglichen Clientanforderung]: #request-parameters
[Parameter aus der Back-End-Antwort]: #response-parameters
