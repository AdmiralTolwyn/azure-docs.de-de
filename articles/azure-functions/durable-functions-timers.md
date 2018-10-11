---
title: Timer in Durable Functions – Azure
description: Es wird beschrieben, wie Sie permanente Timer in der Erweiterung Durable Functions für Azure Functions implementieren.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: azfuncdf
ms.openlocfilehash: e45a8b1d99ef7ccd89f38ec9402cda1a4d49c817
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238553"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Timer in Durable Functions (Azure Functions)

[Durable Functions](durable-functions-overview.md) bieten *permanente Timer* für die Verwendung in Orchestratorfunktionen zum Implementieren von Verzögerungen oder zum Einrichten von Timeouts für asynchrone Aktionen. Permanente Timer sollten in Orchestratorfunktionen anstelle von `Thread.Sleep` und `Task.Delay` (C#) bzw. `setTimeout()` und `setInterval()` (JavaScript) verwendet werden.

Sie erstellen einen permanenten Timer, indem Sie die [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_)-Methode in [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) aufrufen. Die Methode gibt eine Aufgabe zurück, die an einem bestimmten Datum zu einer bestimmten Zeit fortgesetzt wird.

## <a name="timer-limitations"></a>Timereinschränkungen

Wenn Sie einen Timer erstellen, der um 16:30 Uhr abläuft, stellt das zugrunde liegende Durable Task Framework eine Nachricht in die Warteschlange, die nur um 16:30 Uhr sichtbar ist. Bei Ausführung im Verbrauchstarif von Azure Functions stellt der neu sichtbare Timer sicher, dass die Funktionen-App auf einem entsprechenden virtuellen Computer aktiviert wird.

> [!NOTE]
> * Permanente Timer sind aufgrund von Beschränkungen in Azure Storage auf 7 Tage beschränkt. Wir arbeiten an einer [Featureanfrage, den Timer über 7 Tage hinaus zu erweitern](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Verwenden Sie bei der Berechnung einer relativen Frist eines permanenten Timers immer [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) anstelle von `DateTime.UtcNow`, wie in den Beispielen unten dargestellt.

## <a name="usage-for-delay"></a>Verwendung für Verzögerung

Das folgende Beispiel veranschaulicht, wie Sie permanente Timer für die Ausführungsverzögerung verwenden. Im Beispiel wird zehn Tage lang täglich eine Abrechnungsbenachrichtigung ausgegeben.

#### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

#### <a name="javascript"></a>JavaScript

```js
const df = require("durable-functions");
const moment = require("moment-js");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivityAsync("SendBillingEvent");
    }
});
```

> [!WARNING]
> Vermeiden Sie Endlosschleifen in Orchestratorfunktionen. Informationen zum sicheren und effizienten Implementieren von Endlosschleifenszenarien finden Sie unter [Eternal orchestrations in Durable Functions (Azure Functions)](durable-functions-eternal-orchestrations.md) (Ewige Orchestrierungen in Durable Functions [Azure Functions]). 

## <a name="usage-for-timeout"></a>Verwendung für das Timeout

In diesem Beispiel wird veranschaulicht, wie mit permanenten Timern Timeouts implementiert werden.

#### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

#### <a name="javascript"></a>JavaScript

```js
const df = require("durable-functions");
const moment = require("moment-js");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, 's');

    const activityTask = context.df.callActivityAsync("GetQuote");
    const timeoutTask = context.df.createTimer(deadline);

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

> [!WARNING]
> Verwenden Sie `CancellationTokenSource`, um einen permanenten Timer abzubrechen (C#), oder rufen Sie `cancel()` für den zurückgegebenen `TimerTask` auf (JavaScript), wenn der Code nicht auf den Abschluss wartet. Das Durable Task Framework ändert den Status einer Orchestrierung nicht in „abgeschlossen“, solange nicht alle ausstehenden Vorgänge abgeschlossen oder abgebrochen werden.

Dieser Mechanismus beendet laufende Aktivitätsausführungsfunktionen nicht. Stattdessen lässt er zu, dass die Orchestratorfunktion das Ergebnis ignoriert und fortfährt. Wenn Ihre Funktionen-App den Verbrauchstarif verwendet, wird Ihnen weiterhin jeglicher Zeit- und Arbeitsspeicherverbrauch der abgebrochenen Aktivitätsfunktion in Rechnung gestellt. Standardmäßig gilt für Funktionen, die im Rahmen des Verbrauchstarifs ausgeführt werden, ein Timeout von fünf Minuten. Wenn dieses Limit überschritten wird, wird der Azure Functions-Host neu gestartet, um jegliche Ausführung zu beenden und eine unkontrollierte Abrechnungssituation zu verhindern. Der [Funktionstimeout ist konfigurierbar](functions-host-json.md#functiontimeout).

Ein ausführlicheres Beispiel zum Implementieren von Timeouts in Orchestratorfunktionen finden Sie in der exemplarischen Vorgehensweise [Human interaction in Durable Functions - Phone verification sample](durable-functions-phone-verification.md) (Menschliche Interaktion in Durable Functions – Telefonüberprüfungsbeispiel).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Behandeln von externen Ereignissen in Durable Functions (Azure Functions)](durable-functions-external-events.md)

