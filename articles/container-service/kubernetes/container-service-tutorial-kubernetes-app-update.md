---
title: '(VERALTET) Tutorial für Azure Container Service: Aktualisieren einer Anwendung'
description: Tutorial für Azure Container Service – Aktualisieren einer Anwendung
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e65ca30e4f15b6f69f39160c67813047c40ce8ee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274123"
---
# <a name="deprecated-update-an-application-in-kubernetes"></a>(VERALTET) Aktualisieren einer Anwendung in Kubernetes

> [!TIP]
> Die aktualisierte Version dieses Tutorials, in dem Azure Kubernetes Service verwendet wird, finden Sie unter [Tutorial: Aktualisieren einer Anwendung in Azure Kubernetes Service (AKS)](../../aks/tutorial-kubernetes-app-update.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Nach der Bereitstellung einer Anwendung in Kubernetes kann diese durch Angeben eines neuen Containerimages oder einer neuen Imageversion aktualisiert werden. Dabei wird das Update gestaffelt bereitgestellt, sodass jeweils nur ein Teil der Bereitstellung aktualisiert wird. Durch diese Art des Updates kann die Anwendung während des Updates weiterhin ausgeführt werden. Darüber hinaus wird ein Rollbackmechanismus bereitgestellt, falls ein Fehler bei der Bereitstellung auftritt. 

In diesem Tutorial – Teil 6 von 7 – wird die Azure Voting-Beispiel-App aktualisiert. Sie führen folgende Aufgaben aus:

> [!div class="checklist"]
> * Aktualisieren des Front-End-Anwendungscodes
> * Erstellen eines aktualisierten Containerimages
> * Übertragen des Containerimages in Azure Container Registry per Push
> * Bereitstellen des aktualisierten Containerimages

In nachfolgenden Tutorials wird Log Analytics für die Überwachung des Kubernetes-Clusters konfiguriert.

## <a name="before-you-begin"></a>Voraussetzungen

In vorherigen Tutorials wurde eine Anwendung in ein Containerimage gepackt, das Image wurde in Azure Container Registry hochgeladen, und es wurde ein Kubernetes-Cluster erstellt. Anschließend wurde die Anwendung im Kubernetes-Cluster ausgeführt. 

Zudem wurde ein Anwendungsrepository geklont, das den Anwendungsquellcode und eine vorab erstellte Docker Compose-Datei enthält, die in diesem Tutorial verwendet wird. Stellen Sie sicher, dass Sie einen Klon des Repositorys erstellt und in das geklonte Verzeichnis gewechselt haben. Darin befindet sich ein Verzeichnis mit dem Namen `azure-vote` und eine Datei mit dem Namen `docker-compose.yml`.

Wenn Sie diese Schritte nicht ausgeführt haben und dies jetzt nachholen möchten, kehren Sie zum [Tutorial 1 – Erstellen von Containerimages](./container-service-tutorial-kubernetes-prepare-app.md) zurück. 

## <a name="update-application"></a>Aktualisieren der Anwendung

Im Rahmen dieses Tutorials wird eine Änderung an der Anwendung vorgenommen, und die aktualisierte Anwendung wird im Kubernetes-Cluster bereitgestellt. 

Der Quellcode der Anwendung befindet sich im `azure-vote`-Verzeichnis. Öffnen Sie die Datei `config_file.cfg` mit einem Code-Editor oder Text-Editor. In diesem Beispiel wird `vi` verwendet.

```bash
vi azure-vote/azure-vote/config_file.cfg
```

Ändern Sie die Werte für `VOTE1VALUE` und `VOTE2VALUE`, und speichern Sie dann die Datei.

```plaintext
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Speichern und schließen Sie die Datei.

## <a name="update-container-image"></a>Aktualisieren des Containerimages

Verwenden Sie [docker-compose](https://docs.docker.com/compose/), um das Front-End-Image neu zu erstellen und die aktualisierte Anwendung auszuführen. Mit dem `--build`-Argument wird Docker Compose angewiesen, das Anwendungsimage neu zu erstellen.

```bash
docker-compose up --build -d
```

## <a name="test-application-locally"></a>Lokales Testen der Anwendung

Wechseln Sie zu `http://localhost:8080`, um die aktualisierte Anwendung anzuzeigen.

![Abbildung: Kubernetes-Cluster in Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Kennzeichnen und Übertragen von Images per Push

Kennzeichnen Sie das Image `azure-vote-front` mit dem loginServer-Namen der Containerregistrierung. 

Rufen Sie den Anmeldeservernamen mit dem Befehl [az acr list](/cli/azure/acr#az-acr-list) ab.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Verwenden Sie [docker tag](https://docs.docker.com/engine/reference/commandline/tag/), um das Image zu kennzeichnen. Ersetzen Sie `<acrLoginServer>` durch den Namen des Azure Container Registry-Anmeldeservers oder den Hostnamen der öffentlichen Registrierung. Beachten Sie außerdem, dass die Imageversion auf `redis-v2` aktualisiert wird.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Verwenden Sie [docker push](https://docs.docker.com/engine/reference/commandline/push/), um das Image in Ihre Registrierung hochzuladen. Ersetzen Sie `<acrLoginServer>` durch den Namen des Azure Container Registry-Anmeldeservers.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>Bereitstellen der aktualisierten Anwendung

Zur Gewährleistung der maximalen Betriebszeit müssen mehrere Instanzen des Anwendungs-Pods ausgeführt werden. Überprüfen Sie diese Konfiguration mit dem Befehl [kubectl get pod](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get).

```bash
kubectl get pod
```

Ausgabe:

```output
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Wenn das Image „azure-vote-front“ nicht auf mehreren Pods ausgeführt wird, skalieren Sie die Bereitstellung von `azure-vote-front`.


```bash
kubectl scale --replicas=3 deployment/azure-vote-front
```

Um die Anwendung zu aktualisieren, verwenden Sie den Befehl [kubectl set](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set). Aktualisieren Sie `<acrLoginServer>` mit dem Anmeldeserver oder dem Hostnamen der Containerregistrierung.

```bash
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

Verwenden Sie zum Überwachen der Bereitstellung den Befehl [kubectl get pod](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get). Nach Bereitstellung der aktualisierten Anwendung werden die Pods beendet und mit dem neuen Containerimage neu erstellt.

```bash
kubectl get pod
```

Ausgabe:

```output
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>Testen der aktualisierten Anwendung

Rufen Sie die externe IP-Adresse des Diensts `azure-vote-front` ab.

```bash
kubectl get service azure-vote-front
```

Wechseln Sie zu der IP-Adresse, um die aktualisierte Anwendung anzuzeigen.

![Abbildung: Kubernetes-Cluster in Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Anwendung aktualisiert und diese Aktualisierung in einem Kubernetes-Cluster bereitgestellt. Die folgenden Aufgaben wurden ausgeführt:

> [!div class="checklist"]
> * Aktualisieren des Front-End-Anwendungscodes
> * Erstellen eines aktualisierten Containerimages
> * Übertragen des Containerimages in Azure Container Registry per Push
> * Bereitstellen der aktualisierten Anwendung

Im nächsten Tutorial erfahren Sie, wie Sie Kubernetes mit Log Analytics überwachen.

> [!div class="nextstepaction"]
> [Überwachen von Kubernetes mit Log Analytics](./container-service-tutorial-kubernetes-monitor.md)
