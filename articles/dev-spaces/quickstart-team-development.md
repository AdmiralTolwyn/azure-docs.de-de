---
title: Entwicklung im Team unter Kubernetes
services: azure-dev-spaces
ms.date: 01/22/2020
ms.topic: quickstart
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie die Team Kubernetes-Entwicklung mit Containern und Microservices mit Azure Dev Spaces durchführen.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Container, Helm, Service Mesh, Service Mesh-Routing, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 1f087225fc594b7c6469c4988ea1bf93ec558a71
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605271"
---
# <a name="quickstart-team-development-on-kubernetes---azure-dev-spaces"></a>Schnellstart: Entwicklung im Team unter Kubernetes: Azure Dev Spaces

In diesem Leitfaden lernen Sie Folgendes:

- Einrichten von Azure Dev Spaces in einem verwalteten Kubernetes-Cluster in Azure
- Bereitstellen einer umfangreichen Anwendung mit mehreren Microservices in einem Entwicklungsbereich.
- Testen eines einzelnen Microservice in einem isolierten Entwicklungsbereich im Kontext der vollständigen Anwendung.

![Azure Dev Spaces-Entwicklung im Team](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Voraussetzungen

- ein Azure-Abonnement. Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen.
- Die [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) muss installiert sein.
- [Helm 3 muss installiert sein.][helm-installed]

## <a name="create-an-azure-kubernetes-service-cluster"></a>Erstellen eines Azure Kubernetes Service-Clusters

Sie müssen in einer [unterstützten Region][supported-regions] einen AKS-Cluster erstellen. Mit den unten angegebenen Befehlen wird eine Ressourcengruppe mit dem Namen *MyResourceGroup* und der AKS-Cluster *MyAKS* erstellt.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivieren von Azure Dev Spaces in Ihrem AKS-Cluster

Verwenden Sie den Befehl `use-dev-spaces`, um Dev Spaces in Ihrem AKS-Cluster zu aktivieren, und befolgen Sie die angezeigten Anweisungen. Mit dem unten angegebenen Befehl wird Dev Spaces im Cluster *MyAKS* in der Gruppe *MyResourceGroup* aktiviert und ein Entwicklungsbereich namens *dev* erstellt.

> [!NOTE]
> Mit dem Befehl `use-dev-spaces` wird außerdem die Azure Dev Spaces-Befehlszeilenschnittstelle installiert, falls diese nicht bereits installiert ist. Die Azure Dev Spaces-Befehlszeilenschnittstelle kann nicht in Azure Cloud Shell installiert werden.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Abrufen des Codes für die Beispielanwendung

In diesem Artikel verwenden Sie die [Azure Dev Spaces-Beispielanwendung „Bike Sharing“](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp), um die Nutzung von Azure Dev Spaces zu veranschaulichen.

Klonen Sie die Anwendung von GitHub, und navigieren Sie zum Verzeichnis der Anwendung:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Abrufen des HostSuffix für *dev*

Verwenden Sie den Befehl `azds show-context`, um das HostSuffix für *dev* anzuzeigen.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Aktualisieren des Helm-Diagramms mit Ihrem HostSuffix

Öffnen Sie [charts/values.yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml), und ersetzen Sie alle Instanzen von `<REPLACE_ME_WITH_HOST_SUFFIX>` durch den HostSuffix-Wert, den Sie zuvor abgerufen haben. Speichern Sie Ihre Änderungen, und schließen Sie die Datei.

## <a name="run-the-sample-application-in-kubernetes"></a>Ausführen der Beispielanwendung in Kubernetes

Die Befehle zum Ausführen der Beispielanwendung unter Kubernetes sind Teil eines bestehenden Prozesses und sind nicht von den Tools von Azure Dev Spaces abhängig. In diesem Fall ist Helm das Tool für die Ausführung dieser Beispielanwendung, aber es können auch andere Tools verwendet werden, um Ihre gesamte Anwendung in einem Namespace innerhalb eines Clusters auszuführen. Die Helm-Befehle sind auf den Entwicklungsbereich namens *dev* ausgerichtet, den Sie zuvor erstellt haben, aber dieser Entwicklungsbereich ist auch ein Kubernetes-Namespace. Daher können andere Tools auf die Entwicklungsbereiche auf dieselbe Weise wie andere Namespaces ausgerichtet sein.

Sie können Azure Dev Spaces für die Teamentwicklung verwenden, nachdem eine Anwendung in einem Cluster ausgeführt wurde, unabhängig davon, mit welchen Tools sie bereitgestellt wird.

Verwenden Sie den Befehl `helm install`, um die Beispielanwendung in Ihrem Cluster einzurichten und zu installieren.

```cmd
cd charts/
helm install bikesharingsampleappsampleapp . --dependency-update --namespace dev --atomic
```

Der Befehl `helm install` kann mehrere Minuten in Anspruch nehmen. Nachdem die Beispielanwendung in Ihrem Cluster installiert ist und Sie Dev Spaces in Ihrem Cluster aktiviert haben, verwenden Sie den Befehl `azds list-uris`, um die URLs für die Beispielanwendung in *dev* anzuzeigen, die aktuell ausgewählt ist.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Navigieren Sie zum Dienst *bikesharingweb*, indem Sie die öffentliche URL über den Befehl `azds list-uris` öffnen. Im obigen Beispiel lautet die öffentliche URL für den Dienst *bikesharingweb*`http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Wählen Sie *Aurelia Briggs (Kunde)* als Benutzer aus. Vergewissern Sie sich, dass der Text *Hi Aurelia Briggs | Abmelden* oben angezeigt wird.

![Azure Dev Spaces-Beispielanwendung „Bike Sharing“](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Erstellen von untergeordneten Entwicklungsbereichen

Verwenden Sie den Befehl `azds space select`, um unter *dev* zwei untergeordnete Bereiche zu erstellen:

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

Die obigen Befehle erstellen zwei untergeordnete Bereiche unter *dev* namens *azureuser1* und *azureuser2*. Diese beiden untergeordneten Bereiche stellen unterschiedliche Entwicklungsbereiche für die Entwickler *azureuser1* und *azureuser2* dar, die für Änderungen an der Beispielanwendung verwendet werden können.

Verwenden Sie den Befehl `azds space list`, um alle Entwicklungsbereiche aufzulisten, und bestätigen Sie, dass *dev/azureuser2* ausgewählt ist.

```cmd
$ azds space list
   Name            DevSpacesEnabled
-  --------------  ----------------
   default         False
   dev             True
   dev/azureuser1  True
*  dev/azureuser2  True
```

Verwenden Sie `azds list-uris`, um die URLs für die Beispielanwendung im aktuell ausgewählten Bereich *dev/azureuser2* anzuzeigen.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Bestätigen Sie, dass die durch den Befehl `azds list-uris` angezeigten URLs das Präfix *azureuser2.s.dev* aufweisen. Dieses Präfix bestätigt, dass der aktuell ausgewählte Bereich *azureuser2* und somit ein untergeordnetes Element von *dev* ist.

Navigieren Sie zum Dienst *bikesharingweb* für den Entwicklungsbereich *dev/azureuser2*, indem Sie die öffentliche URL über den Befehl `azds list-uris` öffnen. Im obigen Beispiel lautet die öffentliche URL für den Dienst *bikesharingweb*`http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`. Wählen Sie *Aurelia Briggs (Kunde)* als Benutzer aus. Vergewissern Sie sich, dass der Text *Hi Aurelia Briggs | Abmelden* oben angezeigt wird.

## <a name="update-code"></a>Aktualisieren des Codes

Öffnen Sie *BikeSharingWeb/components/Header.js* mit einem Text-Editor, und ändern Sie den Text im [span-Element mit dem `userSignOut` className](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Speichern Sie Ihre Änderungen, und schließen Sie die Datei.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Erstellen und Ausführen des aktualisierten bikesharingweb-Diensts im Entwicklungsereich *dev/azureuser2*

Navigieren Sie zum Verzeichnis *BikeSharingWeb/* , und führen Sie den Befehl `azds up` aus.

```cmd
$ cd ../BikeSharingWeb/
$ azds up

Using dev space 'dev/azureuser2' with target 'MyAKS'
Synchronizing files...2s
...
Service 'bikesharingweb' port 'http' is available at http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/
Service 'bikesharingweb' port 80 (http) is available at http://localhost:54256
...
```

Dieser Befehl erstellt den Dienst *bikesharingweb* im Entwicklungsbereich *dev/azureuser2* und führt ihn aus. Dieser Dienst wird zusätzlich zum *bikesharingweb*-Dienst ausgeführt, der unter *dev* ausgeführt und nur für Anforderungen mit dem URL-Präfix *azureuser2.s* verwendet wird. Weitere Informationen darüber, wie das Routing zwischen übergeordneten und untergeordneten Entwicklungsbereichen funktioniert, finden Sie unter [Funktionsweise und Konfiguration von Azure Dev Spaces](how-dev-spaces-works.md).

Navigieren Sie zum Dienst *bikesharingweb* für den Entwicklungsbereich *dev/azureuser2*, indem Sie die öffentliche URL öffnen, die in der Ausgabe des `azds up`-Befehls angezeigt wird. Wählen Sie *Aurelia Briggs (Kunde)* als Benutzer aus. Überprüfen Sie, ob der aktualisierte Text in der oberen rechten Ecke angezeigt wird. Möglicherweise müssen Sie die Seite aktualisieren oder den Cache Ihres Browsers leeren, wenn diese Änderung nicht sofort angezeigt wird.

![Aktualisierte Azure Dev Spaces-Beispielanwendung „Bike Sharing“](media/quickstart-team-development/bikeshare-update.png)

> [!NOTE]
> Wenn Sie während der Ausführung von `azds up` zu Ihrem Dienst navigieren, werden die Ablaufverfolgungen der HTTP-Anforderung ebenfalls in der Ausgabe des Befehls `azds up` angezeigt. Diese Ablaufverfolgungen können Sie bei der Problembehandlung und beim Debuggen Ihres Diensts unterstützen. Sie können diese Ablaufverfolgungen bei der Ausführung von `azds up` mit `--disable-http-traces` deaktivieren.

## <a name="verify-other-dev-spaces-are-unchanged"></a>Überprüfen, ob andere Entwicklungsbereiche unverändert sind

Wenn der Befehl `azds up` noch ausgeführt wird, drücken Sie *STRG+C*.

```cmd
$ azds list-uris --all
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser1.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser1.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://dev.bikesharingweb.fedcab0987.eus.azds.io/               Available
http://dev.gateway.fedcab0987.eus.azds.io/                      Available
```

Navigieren Sie zur *dev*-Version von *bikesharingweb* in Ihrem Browser, wählen Sie *Aurelia Briggs (Kunde)* als Benutzer aus und vergewissern Sie sich, dass der ursprüngliche Text in der oberen rechten Ecke angezeigt wird. Wiederholen Sie diese Schritte mit der URL *dev/azureuser1*. Beachten Sie, dass die Änderungen nur auf die *dev/azureuser2*-Version von *bikesharingweb* angewendet werden. Diese Isolierung von Änderungen an *dev/azureuser2* ermöglicht es *azureuser2*, Änderungen vorzunehmen, ohne *azureuser1* zu beeinflussen.

Damit diese Änderungen in *dev* und *dev/azureuser1* berücksichtigt werden, sollten Sie dem bestehenden Workflow oder der CI/CD-Pipeline Ihres Teams folgen. Dieser Workflow kann z. B. das Committen Ihrer Änderung in Ihrem Versionskontrollsystem und das Bereitstellen des Updates über eine CI/CD-Pipeline oder Tools wie Helm umfassen.

## <a name="clean-up-your-azure-resources"></a>Bereinigen Ihrer Azure-Ressourcen

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Azure Dev Spaces Sie bei der Entwicklung komplexerer, containerübergreifender Apps unterstützt und wie Sie die gemeinsame Entwicklung vereinfachen können, indem Sie in verschiedenen Bereichen mit verschiedenen Versionen oder Branches Ihres Codes arbeiten.

> [!div class="nextstepaction"]
> [Arbeiten mit mehreren Containern und Teamentwicklung](multi-service-nodejs.md)

[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
