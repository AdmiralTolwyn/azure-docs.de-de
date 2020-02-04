---
title: Erstellen Ihrer ersten dauerhaften Funktion in Azure mit JavaScript
description: Hier erfahren Sie, wie Sie eine dauerhafte Azure-Funktion mit Visual Studio Code erstellen und veröffentlichen.
author: ColbyTresness
ms.topic: quickstart
ms.date: 11/07/2018
ms.reviewer: azfuncdf, cotresne
ms.openlocfilehash: b0a1d1a9305f6de2a072ee1ded310d8de174436b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845724"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Erstellen Ihrer ersten dauerhaften Funktion in JavaScript

*Durable Functions* ist eine Erweiterung von [Azure Functions](../functions-overview.md), mit der Sie zustandsbehaftete Funktionen in einer serverlosen Umgebung schreiben können. Die Erweiterung verwaltet Status, Prüfpunkte und Neustarts für Sie.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

In diesem Artikel erfahren Sie, wie Sie die Azure Functions-Erweiterung von Visual Studio Code verwenden, um lokal eine dauerhafte Funktion namens „hello world“ zu erstellen und zu testen.  Mit dieser Funktion werden Aufrufe anderer Funktionen orchestriert und miteinander verkettet. Anschließend veröffentlichen Sie den Funktionscode in Azure.

![Ausführen einer dauerhaften Funktion in Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Installieren Sie [Visual Studio Code](https://code.visualstudio.com/download).

* Stellen Sie sicher, dass Sie über die neueste Version der [Azure Functions Core Tools](../functions-run-local.md) verfügen.

* Vergewissern Sie sich bei Verwendung eines Windows-Computers, dass der [Azure-Speicheremulator](../../storage/common/storage-use-emulator.md) installiert ist und ausgeführt wird. Auf einem Mac- oder Linux-Computer muss ein echtes Azure-Speicherkonto verwendet werden.

* Vergewissern Sie sich, dass bei Ihnen mindestens die Version 8.0 von [Node.js](https://nodejs.org/) installiert ist.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Erstellen Ihres lokalen Projekts 

In diesem Abschnitt wird mithilfe von Visual Studio Code ein lokales Azure Functions-Projekt erstellt. 

1. Drücken Sie in Visual Studio Code die F1-Taste, um die Befehlspalette zu öffnen. Suchen Sie in der Befehlspalette den Befehl `Azure Functions: Create new project...`, und wählen Sie ihn aus.

1. Wählen Sie einen Verzeichnisspeicherort für Ihren Projektarbeitsbereich und anschließend **Auswählen** aus.

    > [!NOTE]
    > Diese Schritte sollten außerhalb eines Arbeitsbereichs ausgeführt werden. Wählen Sie in diesem Fall keinen Projektordner aus, der Teil eines Arbeitsbereichs ist.

1. Geben Sie gemäß der Eingabeaufforderungen die folgenden Informationen für die gewünschte Sprache ein:

    | Eingabeaufforderung | Wert | Beschreibung |
    | ------ | ----- | ----------- |
    | Auswählen einer Sprache für Ihr Funktions-App-Projekt | JavaScript | Erstellen Sie ein lokales Node.js-Functions-Projekt. |
    | „Select a version“ (Wählen Sie eine Version aus.) | Azure Functions v2 | Diese Option wird nur angezeigt, wenn die Core Tools noch nicht installiert sind. In diesem Fall werden die Core Tools beim erstmaligen Ausführen der App installiert. |
    | Auswählen einer Vorlage für die erste Funktion Ihres Projekts | HTTP-Trigger | Erstellen Sie eine durch HTTP ausgelöste Funktion in der neuen Funktions-App. |
    | Angeben eines Funktionsnamens | HTTPTrigger | Drücken Sie die EINGABETASTE, um den Standardnamen zu verwenden. |
    | Autorisierungsstufe | Funktion | Die Autorisierungsstufe `function` erfordert die Angabe eines Zugriffsschlüssels beim Aufrufen des HTTP-Endpunkts Ihrer Funktion. Dies erschwert den Zugriff auf einen ungeschützten Endpunkt. Weitere Informationen finden Sie unter [Autorisierungsschlüssel](../functions-bindings-http-webhook.md#authorization-keys).  |
    | Auswählen, wie Sie Ihr Projekt öffnen möchten | Hinzufügen zum Arbeitsbereich | Erstellt die Funktions-App im aktuellen Arbeitsbereich. |

Von Visual Studio Code werden bei Bedarf die Azure Functions Core Tools installiert. Außerdem wird ein Funktions-App-Projekt in einem neuen Arbeitsbereich erstellt. Dieses Projekt enthält die Konfigurationsdateien [host.json](../functions-host-json.md) und [local.settings.json](../functions-run-local.md#local-settings-file). Darüber hinaus wird der Ordner „HttpExample“ mit der [Definitionsdatei „function.json“](../functions-reference-node.md#folder-structure) und der [Datei „index.js“](../functions-reference-node.md#exporting-a-function) (Node.js-Datei mit dem Funktionscode) erstellt.

Und es wird die Datei „package.json“ im Stammordner erstellt.

## <a name="install-the-durable-functions-npm-package"></a>Installieren des Durable Functions-npm-Pakets

1. Installieren Sie das `durable-functions` npm-Paket, indem Sie `npm install durable-functions` im Stammverzeichnis der Funktions-App ausführen.

## <a name="creating-your-functions"></a>Erstellen Ihrer Funktionen

Wir werden nun die drei Funktionen erstellen, die Sie für den Einstieg in Durable Functions benötigen: einen HTTP-Starter, einen Orchestrator und eine Aktivitätsfunktion. Der HTTP-Starter initiiert die gesamte Lösung, und der Orchestrator wird Arbeit an verschiedene Aktivitätsfunktionen verteilen.

### <a name="http-starter"></a>HTTP-Starter

Erstellen Sie zunächst eine per HTTP ausgelöste Funktion, die eine Orchestrierung für die dauerhafte Funktion startet.

1. Wählen Sie in *Azure Functions* das Symbol zum **Erstellen einer Funktion** aus.

    ![Erstellen einer Funktion](./media/quickstart-js-vscode/create-function.png)

2. Wählen Sie den Ordner mit dem Funktions-App-Projekt und dann die Funktionsvorlage **HTTP-Starter für dauerhafte Funktionen** aus.

    ![Auswählen der Vorlage für den HTTP-Starter](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Behalten Sie den Standardnamen `DurableFunctionsHttpStart` bei, und drücken Sie die ** **EINGABETASTE**. Wählen Sie anschließend die Authentifizierung **Anonym** aus.

    ![Auswählen der anonymen Authentifizierung](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

Sie verfügen nun über einen Einstiegspunkt für Ihre dauerhafte Funktion. Fügen Sie als Nächstes einen Orchestrator hinzu.

### <a name="orchestrator"></a>Orchestrator

Nun erstellen wir einen Orchestrator für die Koordinierung von Aktivitätsfunktionen.

1. Wählen Sie in *Azure Functions* das Symbol zum **Erstellen einer Funktion** aus.

    ![Erstellen einer Funktion](./media/quickstart-js-vscode/create-function.png)

2. Wählen Sie den Ordner mit dem Funktions-App-Projekt und dann die Funktionsvorlage **Orchestrator für dauerhafte Funktionen** aus. Behalten Sie den Standardnamen „DurableFunctionsOrchestrator“ bei.

    ![Auswählen der Orchestratorvorlage](./media/quickstart-js-vscode/create-function-choose-template.png)

Damit haben Sie einen Orchestrator für die Koordinierung von Aktivitätsfunktionen hinzugefügt. Fügen Sie als Nächstes die referenzierte Aktivitätsfunktion hinzu.

### <a name="activity"></a>Aktivität

Nun erstellen wir eine Aktivitätsfunktion, um die Arbeit der Lösung tatsächlich auszuführen.

1. Wählen Sie in *Azure Functions* das Symbol zum **Erstellen einer Funktion** aus.

    ![Erstellen einer Funktion](./media/quickstart-js-vscode/create-function.png)

2. Wählen Sie den Ordner mit dem Funktions-App-Projekt und dann die Funktionsvorlage **Aktivität für dauerhafte Funktionen** aus. Behalten Sie den Standardnamen „Hello“ bei.

    ![Auswählen der Aktivitätsvorlage](./media/quickstart-js-vscode/create-function-choose-template.png)

Sie haben nun alle Komponenten hinzugefügt, die erforderlich sind, um eine Orchestrierung zu starten und Aktivitätsfunktionen zu verketten.

## <a name="test-the-function-locally"></a>Lokales Testen der Funktion

Mit Azure Functions Core-Tools können Sie ein Azure Functions-Projekt auf dem lokalen Entwicklungscomputer ausführen. Sie werden beim ersten Starten einer Funktion in Visual Studio Code zum Installieren dieser Tools aufgefordert.

1. Starten Sie bei Verwendung eines Windows-Computers den Azure-Speicheremulator, und vergewissern Sie sich, dass die Eigenschaft **AzureWebJobsStorage** von *local.settings.json* auf `UseDevelopmentStorage=true` festgelegt ist.

    Für Storage Emulator 5.8 muss die Eigenschaft **AzureWebJobsSecretStorageType** von „local.settings.json“ auf `files` festgelegt sein. Bei Verwendung eines Mac- oder Linux-Computers muss die Eigenschaft **AzureWebJobsStorage** auf die Verbindungszeichenfolge eines vorhandenen Azure-Speicherkontos festgelegt werden. Ein Speicherkonto wird weiter unten in diesem Artikel erstellt.

2. Legen Sie zum Testen der Funktion einen Breakpoint im Funktionscode fest, und drücken Sie F5, um das Funktions-App-Projekt zu starten. Die Ausgabe der Core Tools wird im Bereich **Terminal** angezeigt. Falls Sie Durable Functions zuvor noch nicht verwendet haben, wird die Durable Functions-Erweiterung installiert, und der Buildvorgang dauert ggf. einige Sekunden.

    > [!NOTE]
    > Für JavaScript Durable Functions ist die Version **1.7.0** oder höher der Erweiterung **Microsoft.Azure.WebJobs.Extensions.DurableTask** erforderlich. Führen Sie den folgenden Befehl aus dem Stammordner Ihrer Azure Functions-App aus, um die Durable Functions-Erweiterung `func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.7.0` zu installieren.

3. Kopieren Sie im Bereich **Terminal** den URL-Endpunkt Ihrer über HTTP ausgelösten Funktion.

    ![Lokale Azure-Ausgabe](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Ersetzen Sie `{functionName}` durch `DurableFunctionsOrchestrator`.

5. Senden Sie mit einem Tool wie [Postman](https://www.getpostman.com/) oder [cURL](https://curl.haxx.se/) eine HTTP-POST-Anforderung an den URL-Endpunkt.

   Die Antwort ist das erste Ergebnis der HTTP-Funktion, um mitzuteilen, dass die dauerhafte Orchestrierung erfolgreich gestartet wurde. Es ist noch nicht das Endergebnis der Orchestrierung. Die Antwort enthält einige nützliche URLs. Zunächst fragen wir den Status der Orchestrierung ab.

6. Kopieren Sie den URL-Wert für `statusQueryGetUri`, fügen Sie ihn in die Adressleiste des Browsers ein, und führen Sie anschließend die Anforderung aus. Alternativ können Sie auch weiter Postman verwenden, um die GET-Anforderung auszuführen.

   Mit der Anforderung wird für die Orchestrierungsinstanz der Status abgefragt. Sie sollten schließlich eine Antwort erhalten, die zeigt, dass die Instanz abgeschlossen wurde, und die die Ausgaben oder Ergebnisse der dauerhaften Funktion enthält. Er sieht wie folgt aus: 

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-11-08T07:07:40Z",
        "lastUpdatedTime": "2018-11-08T07:07:52Z"
    }
    ```

7. Drücken Sie in VS Code**UMSCHALT+F5**, um das Debuggen zu beenden.

Nachdem Sie sichergestellt haben, dass die Funktion auf Ihrem lokalen Computer richtig ausgeführt wird, können Sie das Projekt in Azure veröffentlichen.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testen der Funktion in Azure

1. Kopieren Sie die URL des HTTP-Triggers im Bereich **Ausgabe**. Die URL, über die Ihre per HTTP ausgelöste Funktion aufgerufen wird, sollte das folgende Format haben:

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. Fügen Sie diese neue URL für die HTTP-Anforderung in die Adresszeile des Browsers ein. Sie sollten die gleiche Statusantwort wie zuvor erhalten, als Sie die veröffentlichte App verwendet haben.

## <a name="next-steps"></a>Nächste Schritte

Sie haben mit Visual Studio Code eine dauerhafte JavaScript-basierte Funktions-App erstellt und veröffentlicht.

> [!div class="nextstepaction"]
> [Informationen zu gängigen Mustern für dauerhafte Funktionen](durable-functions-overview.md#application-patterns)