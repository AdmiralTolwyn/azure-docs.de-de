---
title: Erstellen von dauerhaften Funktionen mit dem Azure-Portal
description: Erfahren Sie, wie Sie die Durable Functions-Erweiterung für Azure Functions für die Portalentwicklung installieren.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf, glenga
ms.openlocfilehash: acbba991e6dcce56fad7f27c45f85214cc8fc707
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637005"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Erstellen von dauerhaften Funktionen mit dem Azure-Portal

Die Erweiterung [Durable Functions](durable-functions-overview.md) für Azure Functions finden Sie im NuGet-Paket [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Diese Erweiterung muss in Ihrer Funktions-App installiert werden. In diesem Artikel wird gezeigt, wie Sie dieses Paket installieren, um dauerhafte Funktionen im Azure-Portal zu entwickeln.

>[!NOTE]
>
>* Wenn Sie dauerhafte Funktionen in C# entwickeln, sollten Sie stattdessen die [Entwicklung in Visual Studio 2017](durable-functions-create-first-csharp.md) in Betracht ziehen.
* Wenn Sie dauerhafte Funktionen in JavaScript entwickeln, erwägen Sie stattdessen eine **Entwicklung in Visual Studio Code**.
>
>Das Erstellen dauerhafter Funktionen unter Verwendung von JavaScript wird im Portal noch nicht unterstützt. Verwenden Sie stattdessen Visual Studio Code.

## <a name="create-a-function-app"></a>Erstellen einer Funktionen-App

Sie müssen über eine Funktions-App zum Hosten der Ausführung einer Funktion verfügen. Mit einer Funktions-App können Sie Funktionen in logischen Einheiten gruppieren. Dies erleichtert die Verwaltung, Bereitstellung und Freigabe von Ressourcen. Sie müssen eine C#-Funktions-App erstellen, weil JavaScript-Vorlagen für dauerhafte Funktionen noch nicht unterstützt werden.  

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Standardmäßig verwendet die erstellte Funktions-App Version 2.x der Azure Functions-Runtime. Die Durable Functions-Erweiterung funktioniert sowohl mit den Versionen 1.x als auch mit dem Versionen 2.x der Azure Functions-Runtime. Vorlagen sind jedoch nur verfügbar, wenn Version 2.x der Runtime als Zielversion verwendet wird.

## <a name="create-an-orchestrator-function"></a>Erstellen einer Orchestratorfunktion

1. Erweitern Sie die Funktionen-App, und klicken Sie auf die Schaltfläche **+** neben **Functions**. Wenn dies die erste Funktion in Ihrer Funktions-App ist, wählen Sie **Im Portal** und dann **Weiter**. Fahren Sie andernfalls mit Schritt 3 fort.

   ![Schnellstartseite für Funktionen im Azure-Portal](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Klicken Sie auf **Weitere Vorlagen** und anschließend auf Vorlagen fertigstellen und anzeigen**.

    ![Functions-Schnellstartanleitung: Auswählen weiterer Vorlagen](./media/durable-functions-create-portal/add-first-function.png)

1. Geben Sie `durable` in das Suchfeld ein, und wählen Sie dann die Vorlage **HTTP-Starter für dauerhafte Funktionen** aus.

1. Klicken Sie bei Aufforderung auf **Installieren**, um die Azure DurableTask-Erweiterung sowie alle Abhängigkeiten in der Funktions-App zu installieren. Sie müssen die Erweiterung nur einmalig für eine Funktions-App installieren. Klicken Sie nach erfolgreichem Abschluss der Installation auf **Weiter**.

    ![Installieren von Bindungserweiterungen](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Geben Sie der neuen Funktion nach Abschluss der Installation den Namen `HttpStart`, und klicken Sie auf **Erstellen**. Die erstellte Funktion wird zum Starten der Orchestrierung verwendet.

1. Erstellen Sie eine weitere Funktion in der Funktions-App, dieses Mal mit der Vorlage **Orchestrator für dauerhafte Funktionen**. Geben Sie der neuen Funktion den Namen `HelloSequence`.

1. Erstellen Sie mithilfe der Vorlage `Hello`Aktivität für dauerhafte Funktionen**eine dritte Funktion namens**.

## <a name="test-the-durable-function-orchestration"></a>Testen der Orchestrierung für die dauerhafte Funktion

1. Wechseln Sie zurück zur Funktion **HttpStart**, wählen Sie **</> Funktions-URL abrufen** und dann **Kopieren** aus, um die URL zu kopieren. Sie verwenden diese URL, um die Funktion **HelloSequence** zu starten.

1. Verwenden Sie ein HTTP-Tool wie Postman oder cURL, um eine POST-Anforderung an die kopierte URL zu senden. Das folgende Beispiel zeigt einen cURL-Befehl, der eine POST-Anforderung an die dauerhafte Funktion sendet:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    In diesem Beispiel ist `{your-function-app-name}` die Domäne, die den Namen Ihrer Funktions-App repräsentiert. Die Antwortnachricht enthält einen Satz aus URI-Endpunkten, die Sie zum Überwachen und Verwalten der Ausführung verwenden können. Sie sieht ähnlich wie im folgenden Beispiel aus:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Rufen Sie den Endpunkt-URI `statusQueryGetUri` auf, um den aktuellen Status der dauerhaften Funktion anzuzeigen. Dies kann wie im folgenden Beispiel aussehen:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Setzen Sie den Aufruf des `statusQueryGetUri`-Endpunkts fort, bis sich der Status in **Completed** ändert. Daraufhin wird eine Antwort ähnlich wie im folgenden Beispiel angezeigt: 

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Ihre erste dauerhafte Funktion steht nun in Azure zur Verwendung bereit.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zu gängigen Mustern für dauerhafte Funktionen](durable-functions-overview.md)