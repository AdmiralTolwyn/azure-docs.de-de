---
title: Continuous Deployment mit Jenkins unter Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie einen Continuous Deployment-Prozess mit Jenkins zum Bereitstellen und Durchführen eines Upgrades für eine Container-App über Kubernetes in Azure Kubernetes Service (AKS) automatisieren.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 09/27/2018
ms.author: iainfou
ms.openlocfilehash: cdf8c64f20e15074a1f055d2ab7abf4304d62505
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017906"
---
# <a name="create-a-continuous-deployment-pipeline-with-jenkins-and-azure-kubernetes-service-aks"></a>Erstellen einer Continuous Deployment-Pipeline mit Jenkins und Azure Kubernetes Service (AKS)

Zum schnellen Bereitstellen von Updates für Anwendungen in Azure Kubernetes Service (AKS) verwenden Sie häufig eine CI/CD-Plattform (Continuous Integration/Continuous Delivery). Bei einer CI/CD-Plattform kann ein Codecommit einen neuen Containerbuild auslösen, mit dem dann eine Instanz der aktualisierten Anwendung bereitgestellt wird. In diesem Artikel verwenden Sie Jenkins als CI/CD-Plattform zum Erstellen und Pushen von Containerimages in Azure Container Registry (ACR) und führen dann die Anwendungen in AKS aus. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Bereitstellen einer Azure-Beispielabstimmungsanwendung in einem AKS-Cluster
> * Erstellen einer einfachen Jenkins-Instanz
> * Konfigurieren von Jenkins-Anmeldeinformationen für die Interaktion mit ACR
> * Erstellen eines Jenkins-Buildauftrags und eines GitHub-Webhooks für automatisierte Builds
> * Testen der CI/CD-Pipeline zum Aktualisieren einer Anwendung in AKS basierend auf GitHub-Codecommits

## <a name="before-you-begin"></a>Voraussetzungen

Sie benötigen Folgendes, um die Schritte in diesem Artikel ausführen zu können:

- Grundkenntnisse zu Kubernetes, Git, CI/CD und Containerimages.

- Einen [AKS-Cluster][aks-quickstart] und `kubectl` konfiguriert mit den [AKS-Clusteranmeldeinformationen][aks-credentials].
- Eine [Azure Container Registry-Registrierung (ACR)][acr-quickstart], den ACR-Anmeldeservernamen und den AKS-Cluster konfiguriert für die [Authentifizierung mit der ACR][acr-authentication].

- Version 2.0.46 oder höher der Azure-Befehlszeilenschnittstelle installiert und konfiguriert. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0][install-azure-cli] Informationen dazu.
- Eine [Installation von Docker][docker-install] auf Ihrem Entwicklungssystem.
- Ein GitHub-Konto, ein [persönliches Zugriffstoken für GitHub][git-access-token] und eine Installation des Git-Clients auf Ihrem Entwicklungssystem.

## <a name="prepare-the-application"></a>Vorbereiten der Anwendung

In diesem Artikel verwenden Sie eine Azure-Abstimmungsanwendung, die eine in einem oder mehreren Pods gehostete Webschnittstelle und einen zusätzlichen Pod für die temporäre Datenspeicherung enthält. Bevor Sie Jenkins und AKS für automatisierte Bereitstellungen integrieren, müssen Sie zuerst manuell die Azure-Abstimmungsanwendung in Ihrem AKS-Cluster vorbereiten und bereitstellen. Diese manuelle Bereitstellung ist die erste Version der Anwendung und ermöglicht Ihnen, die Anwendung in Aktion zu sehen.

Forken Sie das folgende GitHub-Repository für die Beispielanwendung: [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis). Wählen Sie die Schaltfläche **Fork** (Verzweigen) in der rechten oberen Ecke aus, um das Repository in Ihr GitHub-Konto zu verzweigen.

Klonen Sie den Fork in Ihrem Entwicklungssystem. Stellen Sie sicher, dass Sie beim Klonen dieses Repositorys die URL Ihres Forks verwenden:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Wechseln Sie zum Verzeichnis Ihres geklonten Forks:

```console
cd azure-voting-app-redis
```

Um die für die Beispielanwendung erforderlichen Containerimages zu erstellen, verwenden Sie die Datei *docker-compose.yaml* mit `docker-compose`:

```console
docker-compose up -d
```

Die erforderlichen Basisimages werden gepullt, und die Anwendungscontainer werden erstellt. Sie können das erstellte Image anschließend mit dem Befehl [docker-images][docker-images] anzeigen. Drei Images wurden heruntergeladen oder erstellt. Das `azure-vote-front`-Image enthält die Anwendung und verwendet das `nginx-flask`-Image als Grundlage. Das `redis`-Image wird zum Starten einer Redis-Instanz verwendet:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Bevor Sie das Containerimage *azure-vote-front* in ACR pushen können, rufen Sie Ihren ACR-Anmeldeserver mit dem Befehl [az acr list][az-acr-list] ab. Im folgenden Beispiel wird die ACR-Anmeldeserveradresse für eine Registrierung in der Ressourcengruppe *myResourceGroup* abgerufen:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Verwenden Sie den Befehl [docker tag][docker-tag], um das Image mit dem ACR-Anmeldeservernamen und der Versionsnummer `v1` zu versehen. Geben Sie Ihren eigenen `<acrLoginServer>`-Namen an, den Sie im vorherigen Schritt erhalten haben:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Pushen Sie das Image *azure-vote-front* schließlich in Ihre ACR-Registrierung. Ersetzen Sie erneut `<acrLoginServer>` durch den Anmeldeservernamen Ihrer eigenen ACR, z.B. `myacrregistry.azurecr.io`:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Bereitstellen der Beispielanwendung in AKS

Zum Bereitstellen der Beispielanwendung in Ihrem AKS-Cluster können Sie die Kubernetes-Manifestdatei im Stammverzeichnis des Repositorys der Azure-Abstimmungsanwendung verwenden. Öffnen Sie die Manifestdatei *azure-vote-all-in-one-redis.yaml* in einem Editor wie `vi`. Ersetzen Sie `microsoft` durch Ihren ACR-Anmeldeservernamen. Dieser Wert befindet sich in Zeile **47** der Manifestdatei:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Stellen Sie als Nächstes die Anwendung mithilfe des Befehls [kubectl apply][kubectl-apply] in Ihrem AKS-Cluster bereit:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Es wird ein Kubernetes-Lastenausgleichsdienst erstellt, um die Anwendung im Internet verfügbar zu machen. Dieser Vorgang kann einige Minuten dauern. Um den Fortschritt der Bereitstellung des Lastenausgleichs zu überwachen, verwenden Sie den Befehl [kubectl get service][kubectl-get] mit dem Argument `--watch`. Sobald die *externe IP-Adresse* nicht mehr *ausstehend* ist, sondern eine *IP-Adresse* angezeigt wird, verwenden Sie `Control + C`, um die kubectl-Überwachung zu beenden.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Öffnen Sie die externe IP-Adresse Ihres Diensts in einem Webbrowser, um die Anwendung in Aktion zu sehen. Ihre Azure-Abstimmungsanwendung wird wie im folgenden Beispiel angezeigt:

![Beispiel für eine in AKS ausgeführte Azure-Abstimmungsanwendung](media/aks-jenkins/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Bereitstellen von Jenkins auf einer Azure-VM

Zum schnellen Bereitstellen von Jenkins zur Verwendung in diesem Artikel können Sie mit dem folgenden Skript einen virtuellen Azure-Computer bereitstellen, den Zugriff auf das Netzwerk konfigurieren und eine einfache Installation von Jenkins abschließen. Zur Authentifizierung zwischen Jenkins und dem AKS-Cluster kopiert das Skript Ihre Kubernetes-Konfigurationsdatei von Ihrem Entwicklungssystem auf das Jenkins-System.

> [!WARNING]
> Dieses Beispielskript ist für Demozwecke bestimmt, um schnell eine auf einer Azure-VM ausgeführte Jenkins-Umgebung bereitzustellen. Hierbei wird die benutzerdefinierte Azure-Skripterweiterung verwendet, um eine VM zu konfigurieren und dann die erforderlichen Anmeldeinformationen anzuzeigen. Ihre *~/.kube/config* wird auf die Jenkins-VM kopiert.

Führen Sie die folgenden Befehle aus, um das Skript herunterzuladen und auszuführen. Sie sollten den Inhalt jedes Skripts vor der Ausführung überprüfen: [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh).

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Es dauert einige Minuten, bis die VM erstellt und die erforderlichen Komponenten für Docker und Jenkins bereitgestellt wurden. Wenn das Skript abgeschlossen ist, gibt es eine Adresse für den Jenkins-Server und einen Schlüssel zum Entsperren des Dashboards aus, wie in der folgenden Beispielausgabe gezeigt:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Öffnen Sie einen Webbrowser mit der angezeigten URL, und geben Sie den Entsperrschlüssel ein. Befolgen Sie die angezeigten Aufforderungen, um die Jenkins-Konfiguration abzuschließen:

- Wählen Sie **Install suggested plugins** (Vorgeschlagene Plug-Ins installieren) aus.
- Erstellen Sie den ersten Administratorbenutzer. Geben Sie einen Benutzernamen (z.B. *azureuser*) und anschließend ein eigenes sicheres Kennwort ein. Geben Sie abschließend einen vollständigen Namen und eine E-Mail-Adresse an.
- Klicken Sie auf **Speichern und Beenden**.
- Klicken Sie auf **Start using Jenkins** (Jenkins verwenden), sobald Jenkins bereit ist.
    - Sollte in Ihrem Browser beim Start von Jenkins eine leere Seite angezeigt werden, starten Sie den Jenkins-Dienst neu. Um den Dienst neu zu starten, öffnen Sie mit SSH eine Verbindung mit der öffentlichen IP-Adresse Ihrer Jenkins-Instanz, und geben Sie `sudo service jenkins restart` ein. Aktualisieren Sie nach dem Neustart des Diensts Ihren Webbrowser.
- Melden Sie sich mit dem Benutzernamen und dem Kennwort, die Sie während der Installation erstellt haben, bei Jenkins an.

## <a name="create-a-jenkins-environment-variable"></a>Erstellen einer Jenkins-Umgebungsvariable

Eine Jenkins-Umgebungsvariable wird verwendet, um den Namen des ACR-Anmeldeservers zu speichern. Auf diese Variable wird während der Ausführung eines Jenkins-Buildauftrags verwiesen. Erstellen Sie diese Umgebungsvariable anhand der folgenden Schritte:

- Wählen Sie auf der linken Seite des Jenkins-Portals **Manage Jenkins (Jenkins verwalten)** > **Configure System (System konfigurieren)** aus.
- Wählen Sie unter **Global Properties** (Globale Eigenschaften) die Option **Environment variables** (Umgebungsvariablen) aus. Fügen Sie eine Variable mit dem Namen `ACR_LOGINSERVER` und Ihrem ACR-Anmeldeserver als Wert hinzu.

    ![Jenkins-Umgebungsvariablen](media/aks-jenkins/env-variables.png)

- Klicken Sie nach Abschluss des Vorgangs unten auf der Seite für die Jenkins-Konfiguration auf **Save** (Speichern).

## <a name="create-a-jenkins-credential-for-acr"></a>Erstellen von Jenkins-Anmeldeinformationen für ACR

Um Jenkins das Erstellen aktualisierter Containerimages und das anschließende Pushen an ACR zu ermöglichen, müssen Sie Anmeldeinformationen für ACR angeben. Für diese Authentifizierung können Azure Active Directory-Dienstprinzipale verwendet werden. In den Voraussetzungen haben Sie den Dienstprinzipal für Ihren AKS-Cluster mit den Berechtigungen *Leser* in Ihrer ACR-Registrierung konfiguriert. Durch diese Berechtigungen kann der AKS-Cluster Images aus der ACR-Registrierung *pullen*. Während des CI/CD-Vorgangs erstellt Jenkins neue Containerimages basierend auf Anwendungsupdates. Diese müssen dann in die ACR-Registrierung *gepusht* werden. Konfigurieren Sie für die Trennung von Rollen und Berechtigungen nun einen Dienstprinzipal für Jenkins mit den Berechtigungen *Mitwirkender* in Ihrer ACR.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Erstellen eines Dienstprinzipals für die Verwendung von ACR durch Jenkins

Erstellen Sie als Erstes mit dem Befehl [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] einen Dienstprinzipal:

```azurecli
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "626dd8ea-042d-4043-a8df-4ef56273670f",
  "displayName": "azure-cli-2018-09-28-22-19-34",
  "name": "http://azure-cli-2018-09-28-22-19-34",
  "password": "1ceb4df3-c567-4fb6-955e-f95ac9460297",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Notieren Sie sich die Werte *appId* und *password* in der Ausgabe. Diese Werte werden in den folgenden Schritten zum Konfigurieren der Anmeldeinformationsressource in Jenkins verwendet.

Rufen Sie die Ressourcen-ID Ihrer ACR-Registrierung mit dem Befehl [az acr show][az-acr-show] ab, und speichern Sie sie als Variable. Geben Sie Ihren Ressourcengruppennamen und den ACR-Namen an:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Erstellen Sie nun eine Rollenzuweisung, um den Dienstprinzipal die Berechtigungen *Mitwirkender* für die ACR zuzuweisen. Geben Sie im folgenden Beispiel Ihre eigene *appId* an, die in der Ausgabe eines vorherigen Befehl aufgeführt wurde, um den Dienstprinzipal zu erstellen:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Erstellen einer Anmeldeinformationsressource in Jenkins für den ACR-Dienstprinzipal

Nachdem die Rollenzuweisung in Azure erstellt wurde, speichern Sie nun Ihre ACR-Anmeldeinformationen in einem Jenkins-Anmeldeinformationsobjekt. Auf diese Anmeldeinformationen wird während eines Jenkins-Erstellungsauftrags verwiesen.

Klicken Sie links im Jenkins-Portal auf **Credentials (Anmeldeinformationen)** > **Jenkins** > **Global credentials (unrestricted) (Globale Anmeldeinformationen (nicht eingeschränkt))** > **Add Credentials (Anmeldeinformationen hinzufügen)**.

Stellen Sie sicher, dass als Typ für die Anmeldeinformationen **Username with password** ausgewählt ist, und geben Sie Folgendes ein:

- **Username** (Benutzername): *appId* des für die Authentifizierung bei Ihrer ACR-Registrierung erstellten Dienstprinzipals.
- **Password** (Kennwort): *password* des für die Authentifizierung bei Ihrer ACR-Registrierung erstellten Dienstprinzipals.
- **ID:** Bezeichner für die Anmeldeinformationen, z.B. *acr-credentials*

Nach Abschluss des Vorgangs ähnelt das Formular für die Anmeldeinformationen etwa dem folgenden Beispiel:

![Erstellen eines Jenkins-Anmeldeinformationsobjekts mit den Dienstprinzipalinformationen](media/aks-jenkins/acr-credentials.png)

Klicken Sie auf **OK**, und kehren Sie zum Jenkins-Portal zurück.

## <a name="create-a-jenkins-project"></a>Erstellen eines Jenkins-Projekts

Wählen Sie auf der Startseite des im Jenkins-Portal links **New item** (Neues Element) aus:

1. Geben Sie als Auftragsnamen *azure-vote* ein. Wählen Sie **Freestyle Project** und anschließend **OK** aus.
1. Wählen Sie im Abschnitt **Allgemein** die Option für **GitHub-Projekt** aus, und geben Sie die URL Ihres verzweigten Repositorys ein (beispielsweise *https://github.com/\<your-github-account\>/azure-voting-app-redis*).
1. Wählen Sie im Bereich **Quellcodeverwaltung** die Option **Git** aus, und geben Sie die URL vom Typ *.git* Ihres verzweigten Repositorys ein. Beispiel: *https://github.com/\<your-github-account\>/azure-voting-app-redis.git*
    - Klicken Sie auf **Add (Hinzufügen)** > **Jenkins**, um die Anmeldeinformationen hinzuzufügen.
    - Wählen Sie unter **Kind** die Option **Secret text**, und geben Sie Ihr persönliches GitHub-Zugriffstoken ([GitHub personal access token][git-access-token]) als Geheimnis ein.
    - Wählen Sie **Add**, wenn Sie fertig sind.

    ![GitHub-Anmeldeinformationen](media/aks-jenkins/github-creds.png)

1. Wählen Sie im Bereich **Build Triggers** (Buildtrigger) die Option **GitHub hook trigger for GITscm polling** (GitHub-Hooktrigger für GITscm-Abruf) aus.
1. Wählen Sie unter **Build Environment** (Buildumgebung) die Option **Use secret texts or files** (Geheime Texte oder Dateien verwenden) aus.
1. Wählen Sie unter **Bindings** (Bindungen) die Option **Add (Hinzufügen)** > **Username and password (separated) (Benutzername und Kennwort (getrennt))** aus.
    - Geben Sie `ACR_ID` unter **Username Variable (Variable für Benutzernamen)** und `ACR_PASSWORD` unter **Password Variable (Variable für Kennwort)** ein.

    ![Jenkins-Bindungen](media/aks-jenkins/bindings.png)

1. Fügen Sie einen **Build Step** (Buildschritt) vom Typ **Execute shell** (Shell ausführen) hinzu, und verwenden Sie den folgenden Text. Mit diesem Skript wird ein neues Containerimage erstellt und per Pushvorgang in Ihre ACR übertragen.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Fügen Sie einen weiteren **Build Step** vom Typ **Execute shell** hinzu, und verwenden Sie den folgenden Text. Dieses Skript aktualisiert die Anwendungsbereitstellung in AKS mit dem neuen Containerimage aus ACR.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Klicken Sie nach Abschluss des Vorgangs auf **Save**.

## <a name="test-the-jenkins-build"></a>Testen des Jenkins-Builds

Bevor Sie den Auftrag basierend auf GitHub-Commits automatisieren, testen Sie die Jenkins-Erstellung zunächst manuell. Bei dieser manuellen Erstellung wird überprüft, ob der Buildauftrag richtig konfiguriert wurde, die richtige Kubernetes-Authentifizierungsdatei vorhanden ist und die Authentifizierung bei ACR funktioniert.

Wählen Sie im Menü auf der linken Seite des Projekts **Build Now** (Jetzt erstellen) aus.

![Jenkins-Testbuild](media/aks-jenkins/test-build.png)

Der erste Build dauert einen Moment, während die Docker-Imageebenen beim Jenkins-Server abgerufen werden. Nachfolgende Builds können die zwischengespeicherten Imageebenen verwenden und so die Buildzeiten verbessern.

Während des Buildvorgangs wird das GitHub-Repository auf dem Jenkins-Buildserver geklont. Ein neues Containerimage wird erstellt und per Pushvorgang in die ACR übertragen. Abschließend wird die Azure Vote-Anwendung, die im AKS-Cluster ausgeführt wird, aktualisiert, damit das neue Image verwendet wird. Da am Anwendungscode keine Änderungen vorgenommen wurden, wird die Anwendung nicht geändert, wenn Sie die Beispiel-App in einem Webbrowser anzeigen.

Nachdem der Buildauftrag abgeschlossen ist, klicken Sie im Buildverlauf auf **build #1**. Wählen Sie **Console Output** (Konsolenausgabe) aus, und zeigen Sie die Ausgabe des Buildvorgangs an. In der letzten Zeile sollte die erfolgreiche Durchführung des Builds angezeigt werden.

## <a name="create-a-github-webhook"></a>Erstellen eines GitHub-Webhooks

Nachdem die manuelle Erstellung abgeschlossen ist, integrieren Sie nun GitHub in den Jenkins-Build. Der Webhook dient dazu, den Jenkins-Buildauftrag bei jedem Codecommit in GitHub auszuführen. Führen Sie die folgenden Schritte aus, um den GitHub-Webhook zu erstellen:

1. Navigieren Sie in einem Webbrowser zu Ihrem geforkten GitHub-Repository.
1. Wählen Sie **Einstellungen** und anschließend **Webhooks** auf der linken Seite aus.
1. Wählen Sie **Webhook hinzufügen** aus. Geben Sie als *Payload URL* (Nutzlast-URL) `http://<publicIp:8080>/github-webhook/` ein, wobei `<publicIp>` die IP-Adresse des Jenkins-Servers ist. Stellen Sie sicher, dass Sie den nachgestellten Schrägstrich (/) hinzufügen. Übernehmen Sie die anderen Standardwerte für den Inhaltstyp, damit *push*-Ereignisse ausgelöst werden.
1. Wählen Sie **Webhook hinzufügen** aus.

    ![Erstellen eines GitHub-Webhooks für Jenkins](media/aks-jenkins/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Testen der gesamten CI/CD-Pipeline

Nun können Sie die gesamte CI/CD-Pipeline testen. Wenn Sie einen Codecommit nach GitHub pushen, werden die folgenden Schritte ausgeführt:

1. Der GitHub-Webhook ruft Jenkins auf.
1. Jenkins startet den Buildauftrag und pullt den letzten Codecommit aus GitHub.
1. Ein Docker-Build wird mit dem aktualisierten Code gestartet, und das neue Containerimage wird mit der letzten Buildnummer markiert.
1. Dieses neue Containerimage wird an Azure Container Registry gepusht.
1. Ihre bei Azure Kubernetes Service bereitgestellte Anwendung wird mit dem letzten Containerimage aus der Azure Container Registry aktualisiert.

Öffnen Sie auf dem Entwicklungscomputer die geklonte Anwendung mit einem Code-Editor. Öffnen Sie im Verzeichnis */azure-vote/azure-vote* die Datei mit dem Namen **config_file.cfg**. Aktualisieren Sie die vote-Werte in dieser Datei auf andere Werte als „cats“ und „dogs“, wie im folgenden Beispiel gezeigt:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Nach der Aktualisierung können Sie die Datei speichern und die Änderungen committen und an Ihren Fork des GitHub-Repositorys pushen. Der GitHub-Webhook löst in Jenkins einen neuen Buildauftrag aus. Überwachen Sie den Buildprozess auf dem Jenkins-Webdashboard. Es dauert einige Sekunden, den aktuellen Code zu pullen, das aktualisierte Image zu erstellen und zu pushen und die aktualisierte Anwendung in AKS bereitzustellen.

Wenn der Build abgeschlossen ist, aktualisieren Sie Ihren Webbrowser mit der Azure-Beispielabstimmungsanwendung. Ihre Änderungen werden wie im folgenden Beispiel angezeigt:

![Azure-Beispielabstimmungsanwendung in AKS durch den Jenkins-Buildauftrag aktualisiert](media/aks-jenkins/azure-vote-updated.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Jenkins als Teil einer CI/CD-Lösung verwenden. AKS kann in andere CI/CD-Lösungen und Automatisierungstools integriert werden, z.B. für ein [Azure DevOps-Projekt][azure-devops] oder zum [Erstellen eines AKS-Clusters mit Ansible][aks-ansible].

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[docker-install]: https://docs.docker.com/install/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az-acr-list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md#grant-aks-access-to-acr
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[azure-devops]: ../devops-project/azure-devops-project-aks.md
[aks-ansible]: ../ansible/ansible-create-configure-aks.md