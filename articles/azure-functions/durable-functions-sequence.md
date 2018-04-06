---
title: Funktionsverkettung in Durable Functions – Azure
description: Es wird beschrieben, wie Sie ein Durable Functions-Beispiel mit einer Funktionssequenz ausführen.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 0020f19e00f3365c4a0d80ebb67aeeedd7fe76df
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/23/2018
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Funktionsverkettung in Durable Functions – „Hello Sequence“-Beispiel

Bei der Funktionsverkettung geht es um das Muster für die Ausführung einer Funktionssequenz in einer bestimmten Reihenfolge. Häufig muss die Ausgabe einer Funktion auf die Eingabe einer anderen Funktion angewendet werden. In diesem Artikel wird ein Beispiel beschrieben, in dem [Durable Functions](durable-functions-overview.md) zum Implementieren der Funktionsverkettung verwendet wird.

## <a name="prerequisites"></a>Voraussetzungen

* [Installieren Sie langlebige Funktionen](durable-functions-install.md).

## <a name="the-functions"></a>Die Funktionen

In diesem Artikel werden die folgenden Funktionen in der Beispiel-App beschrieben:

* `E1_HelloSequence`: Eine Orchestratorfunktion, mit der `E1_SayHello` in einer Sequenz mehrfach aufgerufen wird. Die Ausgaben der `E1_SayHello`-Aufrufe werden gespeichert, und die Ergebnisse werden aufgezeichnet.
* `E1_SayHello`: Eine Aktivitätsfunktion, mit der einer Zeichenfolge das Wort „Hello“ vorangestellt wird.

In den folgenden Abschnitten werden die Konfiguration und der Code beschrieben, die für C#-Skripts verwendet werden. Der Code für die Visual Studio-Entwicklung ist am Ende des Artikels angegeben.
 
## <a name="functionjson-file"></a>Datei „function.json“

Hier ist der Inhalt der Datei *function.json* für die Orchestratorfunktion angegeben, falls Sie Visual Studio Code oder das Azure-Portal für die Entwicklung verwenden. Die meisten *function.json*-Dateien für Orchestratoren ähneln dieser Datei stark.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Ein wichtiger Punkt ist der Bindungstyp `orchestrationTrigger`. Alle Orchestratorfunktionen müssen diesen Triggertyp verwenden.

> [!WARNING]
> Um die Regel „keine E/A“ für Orchestratorfunktionen zu beachten, sollten Sie keine Eingabe- oder Ausgabebindungen verwenden, wenn Sie die Triggerbindung `orchestrationTrigger` einsetzen.  Falls andere Eingabe- oder Ausgabebindungen erforderlich sind, sollten sie stattdessen im Kontext von `activityTrigger`-Funktionen verwendet werden, die vom Orchestrator aufgerufen werden.

## <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C#-Skript (Visual Studio Code und Azure-Portal-Beispielcode) 

Hier ist der Quellcode angegeben:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Alle C#-Orchestrierungsfunktionen müssen über einen Parameter vom Typ `DurableOrchestrationContext` verfügen, der in der Assembly `Microsoft.Azure.WebJobs.Extensions.DurableTask` enthalten ist. Wenn Sie C#-Skript verwenden, kann auf die Assembly mit der Notation `#r` verwiesen werden. Mit diesem Kontextobjekt können Sie andere Funktionen vom Typ *Aktivität* aufrufen und Eingabeparameter mit der dazugehörigen [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_)-Methode übergeben.

Im Code wird `E1_SayHello` dreimal nacheinander mit unterschiedlichen Parameterwerten aufgerufen. Der Rückgabewert jedes Aufrufs wird der Liste `outputs` hinzugefügt, die am Ende der Funktion zurückgegeben wird.

Die Datei *function.json* für die Aktivitätsfunktion `E1_SayHello` ähnelt der Datei von `E1_HelloSequence`, aber es wird nicht der Bindungstyp `orchestrationTrigger`, sondern `activityTrigger` verwendet.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Alle Funktionen, die von einer Orchestrierungsfunktion aufgerufen werden, müssen die Bindung `activityTrigger` verwenden.

Die Implementierung von `E1_SayHello` ist ein relativ einfacher Zeichenfolgen-Formatierungsvorgang.

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Diese Funktion verfügt über einen Parameter vom Typ [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html). Er wird genutzt, um Eingaben zu erhalten, die über den Aufruf an [`CallActivityAsync<T>`](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) der Orchestratorfunktion übergeben wurden.

## <a name="run-the-sample"></a>Ausführen des Beispiels

Senden Sie die folgende HTTP POST-Anforderung, um die `E1_HelloSequence`-Orchestrierung auszuführen.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

Wenn Sie das Beispiel z. B. in einer Funktions-App namens „myfunctionapp“ ausführen, ersetzen Sie „{host}“ durch „myfunctionapp.azurewebsites.net“.

Das Ergebnis ist eine HTTP 202-Antwort, die wie folgt aussehen kann (hier gekürzt):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

An diesem Punkt wird die Orchestrierung in die Warteschlange eingereiht und sofort ausgeführt. Die URL im Header `Location` kann verwendet werden, um den Status der Ausführung zu überprüfen.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Das Ergebnis ist der Status der Orchestrierung. Die Ausführung und der Abschluss werden schnell durchgeführt, sodass Sie den Status *Completed* (Abgeschlossen) mit dieser Antwort sehen (hier gekürzt):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Sie sehen, dass das `runtimeStatus`-Element der Instanz *Completed* lautet und dass `output` das per JSON serialisierte Ergebnis der Ausführung der Orchestratorfunktion enthält.

> [!NOTE]
> Der HTTP POST-Endpunkt, von dem die Orchestratorfunktion gestartet wurde, wird in der Beispiel-App als HTTP-Triggerfunktion mit dem Namen „HttpStart“ implementiert. Sie können eine ähnliche Startlogik auch für andere Triggertypen wie `queueTrigger`, `eventHubTrigger` oder `timerTrigger` implementieren.

Sehen Sie sich die Protokolle zur Funktionsausführung an. Die Funktion `E1_HelloSequence` wurde mehrmals gestartet und abgeschlossen. Dies liegt am Wiedergabeverhalten, das in der [Übersicht](durable-functions-overview.md) beschrieben ist. Andererseits ist es nur zu drei Ausführungen von `E1_SayHello` gekommen, da diese Funktionsausführungen nicht wiedergegeben werden.

## <a name="visual-studio-sample-code"></a>Visual Studio-Beispielcode

So sieht die Orchestrierung als einzelne C#-Datei in einem Visual Studio-Projekt aus:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Beispiel wird eine einfache Funktionsverkettungsorchestrierung veranschaulicht. Im nächsten Beispiel wird die Implementierung des Musters „Auffächern nach außen/innen“ gezeigt. 

> [!div class="nextstepaction"]
> [Beispiel für „Auffächern nach außen/innen“ ausführen](durable-functions-cloud-backup.md)
