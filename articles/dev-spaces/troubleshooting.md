---
title: Problembehandlung
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: conceptual
description: Schnelle Kubernetes-Entwicklung mit Containern und Microservices in Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Container, Helm, Service Mesh, Service Mesh-Routing, kubectl, k8s '
ms.openlocfilehash: 1ccb96bc8682ad505bc4b21e90951ea25c4c9954
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57898081"
---
# <a name="troubleshooting-guide"></a>Handbuch zur Problembehandlung

Dieses Handbuch enthält Informationen über allgemeine Probleme, die bei Verwendung von Azure Dev Spaces auftreten können.

## <a name="enabling-detailed-logging"></a>Aktivieren der ausführlichen Protokollierung

Um Probleme effektiver zu behandeln, kann es hilfreich sein, ausführlichere Protokolle zur Überprüfung zu erstellen.

Bei der Visual Studio-Erweiterung erreichen Sie dies durch Festlegen der Umgebungsvariable `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` auf 1. Achten Sie darauf, Visual Studio neu zu starten, damit die Umgebungsvariable übernommen wird. Nach der Aktivierung werden detaillierte Protokolle in das Verzeichnis `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` geschrieben.

An der Befehlzeilenschnittstelle (CLI) können Sie während der Befehlsausführung weitere Informationen ausgeben. Verwenden Sie dazu den Schalter `--verbose`. Sie können `%TEMP%\Azure Dev Spaces` auch nach ausführlicheren Protokollen durchsuchen. Auf einem Mac finden Sie das TEMP-Verzeichnis, indem Sie `echo $TMPDIR` in einem Terminalfenster ausführen. Auf einem Linux-Computer finden Sie das TEMP-Verzeichnis für gewöhnlich unter `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Debuggen von Diensten mit mehreren Instanzen

Derzeit funktioniert Azure Dev Spaces am besten, wenn eine einzelne Instanz, oder ein Pod, gedebuggt wird. Die Datei „azds.yaml“ enthält die Einstellung *replicaCount*, die die Anzahl der Pods angibt, die Kubernetes für Ihren Dienst ausführt. Wenn Sie die Einstellung „replicaCount“ ändern, um Ihre App so zu konfigurieren, dass mehrere Pods für einen bestimmten Dienst ausgeführt werden, fügt sich der Debugger dem ersten Pod an, wenn alphabetisch aufgeführt. Der Debugger fügt sich einem anderen Pod an, wenn der ursprüngliche Pod recycelt wird, was möglicherweise zu einem unerwarteten Verhalten führt.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Fehler „Azure Dev Spaces-Controller kann nicht erstellt werden“

Dieser Fehler kann angezeigt werden, wenn beim Erstellen des Controllers ein Fehler auftritt. Wenn es sich um einen vorübergehenden Fehler handelt, löschen Sie den Controller und erstellen Sie ihn dann erneut.

### <a name="try"></a>Versuchen Sie Folgendes:

Löschen Sie den Controller über die CLI von Azure Dev Spaces (AZDS CLI). Sie können diesen Vorgang nicht in Visual Studio oder Cloud Shell ausführen. Um die AZDS CLI zu installieren, installieren Sie zuerst die Azure CLI, und führen Sie diesen Befehl aus:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Führen Sie dann diesen Befehl aus, um den Controller zu löschen:

```cmd
azds remove -g <resource group name> -n <cluster name>
```

Sie können den Controller über die CLI oder in Visual Studio erneut erstellen. Folgen Sie den Anweisungen in den Tutorials wie beim ersten Start.


## <a name="error-service-cannot-be-started"></a>Fehler „Dienst kann nicht gestartet werden.“

Dieser Fehler kann angezeigt werden, wenn der Dienstcode nicht gestartet wird. Die Ursache liegt häufig im Benutzercode. Um weitere Diagnoseinformationen zu erhalten, nehmen Sie die folgenden Änderungen Ihrer Befehle und Einstellungen vor:

### <a name="try"></a>Versuchen Sie Folgendes:

In der Befehlszeile:

Verwenden Sie bei Verwendung von _azds.exe_ die Befehlszeilenoption „--verbose“, und verwenden Sie die Befehlszeilenoption „--output“ zur Angabe des Ausgabeformats.
 
```cmd
azds up --verbose --output json
```

In Visual Studio:

1. Öffnen Sie **Tools > Optionen**, und wählen Sie unter **Projekte und Projektmappen** die Option **Erstellen und ausführen** aus.
2. Ändern Sie die Einstellungen für **Ausführlichkeit der MSBuild-Projektbuildausgabe:** zu **Detailliert** oder **Diagnose**.

    ![Screenshot des Dialogfelds „Tools > Optionen“](media/common/VerbositySetting.PNG)
    
### <a name="multi-stage-dockerfiles"></a>Mehrstufige Dockerfiles:
Bei Verwendung einer mehrstufigen Dockerfile-Datei erhalten Sie eine *Dienst kann nicht gestartet werden*-Fehlermeldung. In diesem Fall enthält die ausführliche Ausgabe den folgenden Text:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Dieser Fehler tritt auf, weil AKS-Knoten eine ältere Version von Docker ausführen, die mehrstufige Builds nicht unterstützt. Um mehrstufige Builds zu vermeiden, müssen Sie Ihre Dockerfile-Datei neu schreiben.

### <a name="rerunning-a-service-after-controller-re-creation"></a>Erneutes Ausführen eines Diensts nach der Neuerstellung des Controllers
Sie erhalten eine *Dienst kann nicht gestartet werden*-Fehlermeldung, wenn Sie versuchen, einen Dienst erneut auszuführen, nachdem Sie den Azure Dev Spaces-Controller entfernt und dann neu erstellt haben, der diesem Cluster zugeordnet ist. In diesem Fall enthält die ausführliche Ausgabe den folgenden Text:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Dieser Fehler tritt auf, weil das Entfernen des Dev Spaces-Controllers keine zuvor von diesem Controller installierten Dienste entfernt. Die Neuerstellung des Controllers und der anschließende Versuch, die Dienste mithilfe des neuen Controllers auszuführen, schlagen fehl, da die alten Diensten immer noch vorhanden sind.

Um dieses Problem zu beheben, verwenden Sie den Befehl `kubectl delete`, um die alten Dienste manuell aus Ihrem Cluster zu entfernen, und führen Sie dann Dev Spaces erneut aus, um die neuen Dienste zu installieren.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Bei der DNS-Namensauflösung tritt für eine öffentliche URL, die einem Dev Spaces-Dienst zugeordnet ist, ein Fehler auf.

Sie können einen öffentlichen URL-Endpunkt für Ihren Dienst durch Angabe des `--public`-Schalters zum `azds prep`-Befehl oder Auswahl des `Publicly Accessible`-Kontrollkästchens in Visual Studio konfigurieren. Der öffentliche DNS-Name wird automatisch beim Ausführen Ihres Diensts in Dev Spaces registriert. Wenn dieser DNS-Name nicht registriert ist, erhalten Sie beim Herstellen der Verbindung mit der öffentlichen URL in Ihrem Webbrowser eine *Seite kann nicht angezeigt werden*- oder *Website kann nicht erreicht werden*-Fehlermeldung.

### <a name="try"></a>Versuchen Sie Folgendes:

Mit dem folgenden Befehl können Sie alle Ihren Dev Spaces-Diensten zugeordneten URLs auflisten:

```cmd
azds list-uris
```

Der Status *Ausstehend* für eine URL bedeutet, dass Dev Spaces auf den Abschluss der DNS-Registrierung wartet. Manchmal dauert es einige Minuten, bis die Registrierung abgeschlossen ist. Dev Spaces öffnet zudem für die einzelnen Dienste einen localhost-Tunnel, den Sie beim Warten auf die DNS-Registrierung verwenden können.

Wird für eine URL länger als fünf Minuten der Status *Ausstehend* angezeigt, deutet dies unter Umständen auf ein Problem mit dem externen DNS-Pod hin, der den öffentlichen Endpunkt erstellt, oder mit dem Pod des Nginx-Eingangscontrollers, der den öffentlichen Endpunkt abruft. Mit den folgenden Befehlen können Sie diese Pods löschen. AKS erstellt automatisch die gelöschten Pods neu.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>Fehler „Erforderliche Tools und Konfigurationen fehlen“

Dieser Fehler kann beim Starten von Visual Studio Code auftreten: „[Azure Dev Spaces] Erforderliche Tools und Konfigurationen zum Erstellen und Debuggen von ‚[Projektname]‘ fehlen.“
Der Fehler weist darauf hin, dass „azds.exe“ nicht in der PATH-Umgebungsvariablen angegeben ist, wie in Visual Studio Code zu sehen ist.

### <a name="try"></a>Versuchen Sie Folgendes:

Starten Sie Visual Studio Code über eine Eingabeaufforderung, wo die PATH-Umgebungsvariable ordnungsgemäß eingerichtet ist.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Fehler „Erforderliche Tools zum Erstellen und Debuggen von ‚Projektname‘ sind veraltet.“

Dieser Fehler wird in Visual Studio Code angezeigt, wenn Sie über eine neuere Version der Visual Studio Code-Erweiterung für Azure Dev Spaces verfügen, aber eine ältere Version der Azure Dev Spaces-Befehlszeilenschnittstelle (CLI).

### <a name="try"></a>Testen

Laden Sie die neueste Version der Azure Dev Spaces-CLI herunter, und installieren Sie sie:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Fehler: „azds“ wird nicht als interner oder externer Befehl, ausführbares Programm oder Batchdatei erkannt.
 
Dieser Fehler kann angezeigt werden, wenn „azds.exe“ nicht installiert oder nicht ordnungsgemäß konfiguriert ist.

### <a name="try"></a>Versuchen Sie Folgendes:

1. Überprüfen Sie, ob „azds.exe“ in „%ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI (Preview)“ vorhanden ist. Wenn ja, fügen Sie diesen Speicherort der PATH-Umgebungsvariablen hinzu.
2. Wenn „azds.exe“ nicht installiert ist, führen Sie den folgenden Befehl aus:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Warnung „Aufgrund einer nicht unterstützten Sprache konnte keine Dockerfile generiert werden“
Azure Dev Spaces bietet systeminternen Support für C# und Node.js. Wenn Sie *azds prep* in einem Verzeichnis ausführen, das in einer dieser Sprachen geschriebenen Code enthält, erstellt Azure Dev Spaces automatisch eine entsprechende Dockerfile-Datei.

Sie können Azure Dev Spaces auch mit Code verwenden, der in anderen Sprachen geschrieben ist. Dann müssen Sie jedoch vor der erstmaligen Ausführung von *azds up* die Dockerfile-Datei manuell selbst erstellen.

### <a name="try"></a>Versuchen Sie Folgendes:
Wenn Ihre Anwendung in einer Sprache geschrieben ist, die Azure Dev Spaces systemintern nicht unterstützt, müssen Sie zum Erstellen eines Containerimages zum Ausführen des Codes eine entsprechende Dockerfile-Datei bereitstellen. Docker stellt eine [Liste der bewährten Methoden für das Schreiben von Dockerfile-Dateien](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) sowie eine [Dockerfile-Referenz](https://docs.docker.com/engine/reference/builder/) bereit, die Ihnen beim Schreiben einer für Ihre Bedürfnisse geeigneten Dockerfile-Datei behilflich sein können.

Wenn Sie eine entsprechende Dockerfile-Datei haben, können Sie die Ausführung von *azds up* fortsetzen, um Ihre Anwendung in Azure Dev Spaces auszuführen.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Fehler „Upstream-Verbindungsfehler oder Trennung/Reset vor Headern“
Dieser Fehler kann angezeigt werden, wenn Sie versuchen, auf den Dienst zuzugreifen, beispielsweise, wenn Sie in einem Browser zur URL des Diensts wechseln. 

### <a name="reason"></a>Grund 
Der Containerport ist nicht verfügbar. Dieses Problem kann auftreten, weil: 
* Erstellung und Bereitstellung des Containers sind noch nicht abgeschlossen. Das Problem kann vorkommen, wenn Sie `azds up` ausführen oder den Debugger starten, und dann versuchen, auf den Container zuzugreifen, bevor er erfolgreich bereitgestellt wurde.
* Die Portkonfiguration ist nicht konsistent über _Dockerfile_, Helm-Diagramm und sämtlichen Servercode hinweg, durch den ein Port geöffnet wird.

### <a name="try"></a>Versuchen Sie Folgendes:
1. Wenn der Container gerade erstellt/bereitgestellt wird, können Sie 2 bis 3 Sekunden warten und dann erneut versuchen, auf den Dienst zuzugreifen. 
1. Überprüfen Sie Ihre Portkonfiguration. Die angegebenen Portnummern müssen in allen folgenden Ressourcen **identisch** sein:
    * **Dockerfile:** Angegeben durch die `EXPOSE`-Anweisung.
    * **[Helm-Chart](https://docs.helm.sh):** Angegeben durch die `externalPort`- und `internalPort`-Werte für einen Dienst (oft in einer `values.yml`-Datei).
    * Alle Ports, die im Anwendungscode geöffnet werden (z. B. in Node.js): `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Config-Datei nicht gefunden
Sie führen `azds up` aus und erhalten den folgenden Fehler: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Grund
Führen Sie `azds up` aus dem Stammverzeichnis des auszuführenden Codes aus, und initialisieren Sie den Codeordner für die Ausführung mit Azure Dev Spaces.

### <a name="try"></a>Versuchen Sie Folgendes:
1. Ändern Sie das aktuelle Verzeichnis in den Stammordner, der Dienstcode enthält. 
1. Wenn im Codeordner keine _azds.yaml_-Datei vorhanden ist, führen Sie `azds prep` aus, um Docker-, Kubernetes- und Azure Dev Spaces-Objekte zu generieren.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Fehler „Das Pipe-Programm ‚azds‘ wurde unerwartet mit Code 126 beendet.“
Das Starten des VS Code-Debuggers kann in einigen Fällen zu diesem Fehler führen.

### <a name="try"></a>Versuchen Sie Folgendes:
1. Schließen Sie VS Code, und öffnen Sie es erneut.
2. Drücken Sie erneut F5.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Debuggen des Fehlers „Debugger-Erweiterung für coreclr-Typ wurde nicht gefunden“
Beim Ausführen des VS Code-Debuggers wird der folgende Fehler gemeldet: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Grund
Auf Ihrem Entwicklungscomputer ist die VS Code-Erweiterung für C# nicht installiert. Die Erweiterung für C# enthält Debug-Unterstützung für .NET Core (CoreCLR).

### <a name="try"></a>Versuchen Sie Folgendes:
Installieren Sie die [VS Code-Erweiterung für C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Debuggen des Fehlers „Der konfigurierte Debugtyp ‚coreclr‘ wird nicht unterstützt.“
Beim Ausführen des VS Code-Debuggers wird der folgende Fehler gemeldet: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Grund
Auf Ihrem Entwicklungscomputer ist die VS Code-Erweiterung für Azure Dev Spaces nicht installiert.

### <a name="try"></a>Versuchen Sie Folgendes:
Installieren Sie die [VS Code-Erweiterung für Azure Dev Spaces](get-started-netcore.md).

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Debugfehler „Ungültiger ‚cwd‘-Wert ‚/src‘. Das System kann die angegebene Datei nicht finden.“ oder „launch: program ‚/src/[Pfad zur Projektbinärdatei]‘ ist nicht vorhanden“.
Bei der Ausführung des VS Code-Debuggers wird der folgende Fehler gemeldet: `Invalid 'cwd' value '/src'. The system cannot find the file specified.` und/oder `launch: program '/src/[path to project executable]' does not exist`.

### <a name="reason"></a>Grund
Standardmäßig verwendet die VS Code-Erweiterung `src` als Arbeitsverzeichnis für das Projekt im Container. Wenn Sie Ihre `Dockerfile` aktualisiert haben, um ein anderes Arbeitsverzeichnis anzugeben, kann dieser Fehler angezeigt werden.

### <a name="try"></a>Versuchen Sie Folgendes:
Aktualisieren Sie die Datei `launch.json` unter dem Unterverzeichnis `.vscode` Ihres Projektordners. Ändern Sie die `configurations->cwd`-Anweisung so, dass sie auf das selbe Verzeichnis verweist, wie die `WORKDIR`-Variable, die in der `Dockerfile` Ihres Projekts definiert ist. Möglicherweise müssen Sie auch die `configurations->program`-Anweisung aktualisieren.

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Der Typ- oder Namespacename „MyLibrary“ konnte nicht gefunden werden

### <a name="reason"></a>Grund 
Der Buildkontext befindet sich standardmäßig auf Projekt-/Dienstebene, aus diesem Grund kann ein verwendetes Bibliotheksprojekt nicht gefunden werden.

### <a name="try"></a>Versuchen Sie Folgendes:
Erforderliche Maßnahmen:
1. Ändern Sie die _azds.yaml_-Datei, um den Buildkontext auf Projektmappenebene festzulegen.
2. Ändern Sie die _Dockerfile_- und _Dockerfile.develop_-Dateien so, dass sie relativ zum neuen Buildkontext ordnungsgemäß auf die _csproj_-Dateien verweisen.
3. Platzieren Sie neben der SLN-Datei eine _DOCKERIGNORE_-Datei, und ändern Sie sie nach Bedarf.

Ein Beispiel finden Sie unter https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Autorisierungsfehler „Microsoft.DevSpaces/register/action“
Sie müssen in Ihrem Azure-Abonnement zum Verwalten von Azure Dev Spaces Zugriff als *Besitzer* oder *Mitwirkender* haben. Diese Fehlermeldung wird möglicherweise angezeigt, wenn Sie versuchen, Dev Spaces zu verwalten, und nicht über Zugriff als *Besitzer* oder *Mitwirkender* auf das zugehörige Azure-Abonnement verfügen.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Grund
Für das ausgewählte Azure-Abonnement ist der `Microsoft.DevSpaces`-Namespace nicht registriert.

### <a name="try"></a>Versuchen Sie Folgendes:
Jemand, der als Besitzer oder Mitwirkender Zugriff auf das Azure-Abonnement hat, kann den folgenden Azure CLI-Befehl ausführen, um den `Microsoft.DevSpaces`-Namespace manuell zu registrieren:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Dev Spaces-Timeout beim Schritt *Warten auf Containerimagebuild* mit virtuellen AKS-Knoten

### <a name="reason"></a>Grund
Dies tritt auf, wenn Sie versuchen, mit Dev Spaces einen Dienst auszuführen, der zur Ausführung auf einem [virtuellen AKS-Knoten](https://docs.microsoft.com/azure/aks/virtual-nodes-portal) konfiguriert ist. Dev Spaces unterstützt derzeit nicht das Erstellen oder Debuggen von Diensten auf virtuellen Knoten.

Wenn Sie `azds up` mit dem `--verbose`-Schalter ausführen oder ausführliche Protokollierung in Visual Studio aktivieren, finden Sie weitere Informationen unter:

```cmd
Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Dies zeigt, dass der Pod des Diensts *virtual-node-aci-linux* zugewiesen wurde, d.h. einem virtuellen Knoten.

### <a name="try"></a>Versuchen Sie Folgendes:
Aktualisieren Sie das Helm-Diagramm für den Dienst, um die Werte *nodeSelector* und/oder *tolerations* zu entfernen, die die Ausführung des Diensts auf einem virtuellen Knoten zulassen. Diese Werte werden in der Regel in der `values.yaml`-Datei des Diagramms definiert.

Sie können immer noch einen AKS-Cluster verwenden, auf dem das Feature des virtuellen Knotens aktiviert ist, wenn der Dienst, den Sie über Dev Spaces erstellen/debuggen möchten, auf einem VM-Knoten ausgeführt wird. Dies ist die Standardkonfiguration.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>„Fehler: Bereiter Tiller-Pod konnte nicht gefunden werden“ beim Starten von Dev Spaces

### <a name="reason"></a>Grund
Dieser Fehler tritt auf, wenn der Helm-Client nicht mehr mit dem im Cluster ausgeführten Tiller-Pod kommunizieren kann.

### <a name="try"></a>Versuchen Sie Folgendes:
Ein Neustart der Agent-Knoten in Ihrem Cluster behebt in der Regel dieses Problem.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Azure Dev Spaces-Proxy beeinträchtigt ggf. andere Pods, die in einer Dev Space-Instanz ausgeführt werden

### <a name="reason"></a>Grund
Wenn Sie Dev Spaces auf einem Namespace in Ihrem AKS-Cluster aktivieren, wird ein zusätzlicher Container namens _mindaro-proxy_ in jedem Pod installiert, der in diesem Namespace ausgeführt wird. Dieser Container fängt Aufrufe an die Dienste im Pod ab. Dies ist eine wesentliche Entwicklungsfunktion des Dev Spaces-Teams; sie kann jedoch mit bestimmten in diesen Pods ausgeführten Diensten in Konflikt stehen. Es treten erfahrungsgemäß Konflikte bei Pods auf, in denen Azure Cache for Redis ausgeführt wird. Es treten Verbindungsfehler und Fehler bei der Kommunikation zwischen Primär- und Sekundärgerät auf.

### <a name="try"></a>Versuchen Sie Folgendes:
Sie können die betroffenen Pods zu einem Namespace innerhalb des Clusters verschieben, in dem Dev Spaces _nicht_ aktiviert ist. Der Rest der Anwendung kann weiterhin in einem Namespace ausgeführt werden, in dem Dev Spaces aktiviert ist. Dev Spaces installiert den Container _mindaro-proxy_ nicht in Namespaces, für die Dev Spaces nicht aktiviert ist.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces scheint meine vorhandene Dockerfile nicht zum Erstellen eines Containers zu verwenden

### <a name="reason"></a>Grund
Azure Dev Spaces kann so konfiguriert werden, dass es auf eine bestimmte _Dockerfile_ in Ihrem Projekt verweist. Wenn es den Anschein hat, dass Azure Dev Spaces zum Erstellen Ihrer Container nicht die erwartete _Dockerfile_-Dateiverwendet, müssen Sie Azure Dev Spaces möglicherweise explizit mitteilen, welche Dockerfile-Datei verwendet werden soll. 

### <a name="try"></a>Versuchen Sie Folgendes:
Öffnen Sie die _azds.yaml_-Datei, die Azure Dev Spaces in Ihrem Projekt generiert hat. Verweisen Sie mit der Anweisung *configurations -> develop -> build -> dockerfile* auf die Dockerfile-Datei, die Sie verwenden möchten:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```
