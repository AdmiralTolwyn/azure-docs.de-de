---
title: Lokales Bereitstellen der Remoteüberwachungslösung (Visual Studio Code) – Azure | Microsoft-Dokumentation
description: In dieser Schrittanleitung wird gezeigt, wie der Solution Accelerator für die Remoteüberwachung zu Test- und Entwicklungszwecken mithilfe von Visual Studio Code auf Ihrem lokalen Computer bereitgestellt wird.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 946f815cecea4cc172fac35c0b260d795317e6e1
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316217"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Lokales Bereitstellen des Solution Accelerators für die Remoteüberwachung – Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

In diesem Artikel wird gezeigt, wie der Solution Accelerator für die Remoteüberwachung zu Test- und Entwicklungszwecken auf Ihrem lokalen Computer bereitgestellt wird. Erfahren Sie, wie Sie die Microservices in Visual Studio Code ausführen. Eine lokale Microservices-Bereitstellung verwendet die folgenden Clouddienste: IoT Hub, Cosmos DB, Azure Stream Analytics und Azure Time Series Insights.

## <a name="prerequisites"></a>Voraussetzungen

Für die Bereitstellung der vom Solution Accelerator für die Remoteüberwachung benötigten Azure-Dienste benötigen Sie ein aktives Azure-Abonnement.

Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Einrichtung des Computers

Um die lokale Bereitstellung abzuschließen, müssen die folgenden Tools auf dem lokalen Entwicklungscomputer installiert sein:

* [Git-Client](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [C#-Erweiterung von Visual Studio Code](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) – Diese Software ist eine Voraussetzung für die PCS-CLI, die von den Skripts zum Erstellen von Azure-Ressourcen genutzt wird. Verwenden Sie nicht Node.js v10.

> [!NOTE]
> Visual Studio Code ist für Windows, Mac und Ubuntu verfügbar.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Ausführen der Microservices

In diesem Abschnitt führen Sie die Microservices für die Remoteüberwachung aus. Sie führen die Webbenutzeroberfläche nativ, den Gerätesimulationsdienst in Docker und die Microservices in Visual Studio Code aus.

### <a name="build-the-code"></a>Erstellen des Codes

Navigieren Sie an der Eingabeaufforderung zu „azure-iot-pcs-remote-monitoring-dotnet\services“, und führen Sie die folgenden Befehle zum Erstellen des Codes aus.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Bereitstellen aller anderen Microservices auf dem lokalen Computer

In den folgenden Schritten wird erläutert, wie Sie die Microservices für die Remoteüberwachung in Visual Studio 2017 ausführen:

1. Starten Sie Visual Studio Code.
1. Öffnen Sie das Modul **azure-iot-pcs-remote-monitoring-dotnet** aus Ihrer lokalen Kopie in Visual Studio Code.
1. Kopieren Sie die Dateien **launch.json** und **tasks.json** aus „Scripts\local\launch\idesettings\vscode\.“. Erstellen Sie einen neuen Ordner **azure-iot-pcs-remote-monitoring-dotnet\.vscode**, und fügen Sie die Dateien dort ein.
1. Öffnen Sie den Debuggingbereich in Visual Studio Code, und führen Sie die Konfiguration **Run all microservices** (Alle Microservices ausführen) aus. Diese Konfiguration führt den Microservice für Gerätesimulationen in Docker und die anderen Microservices im Debugger aus.

Die Ausgabe des **Auth**-Diensts in der **Debugging-Konsole** ähnelt z.B. der folgenden:

[![Deploy-Local-Auth-Service](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Ausführen der Webbenutzeroberfläche

In diesem Schritt starten Sie die Webbenutzeroberfläche. Navigieren Sie zum Ordner **azure-iot-pcs-remote-monitoring-dotnet\webui** in Ihrer lokalen Kopie, und führen Sie die folgenden Befehle aus:

```cmd
npm install
npm start
```

Nach Abschluss des Starts wird im Browser die Seite **http:\//localhost:3000/dashboard**. angezeigt. Die Fehler auf dieser Seite sind zu erwarten. Um die Anwendung ohne Fehler anzuzeigen, führen Sie den folgenden Schritt aus.

### <a name="configure-and-run-nginx"></a>Konfigurieren und Ausführen von NGINX

Richten Sie einen Reverseproxyserver ein, um die Webanwendung und die Microservices zu verknüpfen, die auf dem lokalen Computer ausgeführt werden:

* Kopieren Sie die Datei **nginx.conf** aus dem Ordner **webui\scripts\localhost** in das Installationsverzeichnis **nginx\conf**.
* Führen Sie **NGINX** aus.

Weitere Informationen zum Ausführen von **NGINX** finden Sie unter [nginx for Windows](https://nginx.org/en/docs/windows.html) (nginx für Windows).

### <a name="connect-to-the-dashboard"></a>Verbinden mit dem Dashboard

Navigieren Sie für den Zugriff auf das Dashboard der Remoteüberwachungslösung in Ihrem Browser zu „http:\//localhost:9000“.

## <a name="clean-up"></a>Bereinigen

Um unnötige Gebühren zu vermeiden, entfernen Sie nach Abschluss Ihrer Tests die Clouddienste aus Ihrem Azure-Abonnement. Navigieren Sie zum Entfernen der Dienste zum [Azure-Portal](https://ms.portal.azure.com), und löschen Sie die Ressourcengruppe, die mit dem Skript **start.cmd** erstellt wurde.

Sie können auch die lokale Kopie des Remoteüberwachungsrepositorys löschen, die erstellt wurde, als Sie den Quellcode aus GitHub geklont haben.

## <a name="next-steps"></a>Nächste Schritte

Nach Bereitstellung der Remoteüberwachungslösung können Sie sich als Nächstes [mit den Funktionen des Lösungsdashboards vertraut machen](quickstart-remote-monitoring-deploy.md).
