---
title: "Tutorial zu Kubernetes in Azure – Bereitstellen von Anwendungen"
description: "AKS-Tutorial – Bereitstellen eine Anwendung"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4468424a96b4949161218d495dd21f24285430fd
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/18/2017
---
# <a name="run-applications-in-azure-container-service-aks"></a>Ausführen von Anwendungen in Azure Container Service (AKS)

In diesem Tutorial – Teil 4 von 8 – wird eine Beispielanwendung in einem Kubernetes-Cluster bereitgestellt. Folgende Schritte werden ausgeführt:

> [!div class="checklist"]
> * Aktualisieren von Kubernetes-Manifestdateien
> * Ausführen einer Anwendung in Kubernetes
> * Testen der Anwendung

In den nachfolgenden Tutorials wird diese Anwendung horizontal hochskaliert und aktualisiert. Zudem wird die Operations Management Suite für die Überwachung des Kubernetes-Clusters konfiguriert.

Dieses Tutorial setzt voraus, dass Sie grundlegend mit den Konzepten von Kubernetes vertraut sind. Ausführliche Informationen zu Kubernetes finden Sie in der [Kubernetes-Dokumentation][kubernetes-documentation].

## <a name="before-you-begin"></a>Voraussetzungen

In vorherigen Tutorials wurde eine Anwendung in ein Containerimage gepackt, das Image wurde in Azure Container Registry hochgeladen, und es wurde ein Kubernetes-Cluster erstellt. 

Für dieses Tutorial benötigen Sie die vorab erstellte Kubernetes-Manifestdatei `azure-vote-all-in-one-redis.yaml`. Diese Datei wurde in einem vorherigen Tutorial mit dem Anwendungsquellcode heruntergeladen. Stellen Sie sicher, dass Sie das Repository geklont und in das geklonte Repository gewechselt haben.

Wenn Sie diese Schritte nicht ausgeführt haben und dies jetzt nachholen möchten, kehren Sie zum [Tutorial 1 – Erstellen von Containerimages][aks-tutorial-prepare-app] zurück.

## <a name="update-manifest-file"></a>Aktualisieren der Manifestdatei

In diesem Tutorial wurde Azure Container Registry (ACR) zum Speichern eines Containerimages verwendet. Vor dem Ausführen der Anwendung muss der ACR-Anmeldeservername in der Kubernetes-Manifestdatei aktualisiert werden.

Rufen Sie den ACR-Anmeldeservernamen mit dem [az acr list][az-acr-list]-Befehl auf.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Die Manifestdatei wurde vorab mit dem Serveranmeldenamen `microsoft` erstellt. Öffnen Sie die Datei mit einem Text-Editor. In diesem Beispiel wird die Datei mit `vi` geöffnet.

```console
vi azure-vote-all-in-one-redis.yaml
```

Ersetzen Sie `microsoft` durch den ACR-Anmeldeservernamen. Dieser Wert befindet sich in Zeile **47** der Manifestdatei.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

Speichern und schließen Sie die Datei.

## <a name="deploy-application"></a>Bereitstellen der Anwendung

Führen Sie die Anwendung mithilfe des Befehls [kubectl create][kubectl-create] aus. Dieser Befehl analysiert die Manifestdatei und erstellt die definierten Kubernetes-Objekte.

```azurecli
kubectl create -f azure-vote-all-in-one-redis.yaml
```

Ausgabe:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Testen der Anwendung

Es wird ein [Kubernetes-Dienst][kubernetes-service] erstellt, der die Anwendung für das Internet verfügbar macht. Dieser Vorgang kann einige Minuten dauern. 

Verwenden Sie zum Überwachen des Fortschritts den Befehl [kubectl get service][kubectl-get] mit dem Argument `--watch`.

```azurecli
kubectl get service azure-vote-front --watch
```

Die *externe IP-Adresse* für den Dienst *azure-vote-front* wird zunächst als *ausstehend* angezeigt.
  
```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Sobald die *externe IP-Adresse* nicht mehr *ausstehend* ist, sondern eine *IP-Adresse* angezeigt wird, verwenden Sie `CTRL-C`, um die kubectl-Überwachung zu beenden. 

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Um die Anwendung anzuzeigen, navigieren Sie zu der externen IP-Adresse.

![Abbildung: Kubernetes-Cluster in Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde die Azure Vote-Anwendung in einem Kubernetes-Cluster in AKS bereitgestellt. Folgende Aufgaben wurden ausgeführt:  

> [!div class="checklist"]
> * Herunterladen von Kubernetes-Manifestdateien
> * Ausführen der Anwendung in Kubernetes
> * Testen der Anwendung

Wechseln Sie zum nächsten Tutorial, wo Sie erfahren, wie Sie eine Kubernetes-Anwendung und die zugrunde liegende Kubernetes-Infrastruktur skalieren. 

> [!div class="nextstepaction"]
> [Scale Kubernetes application and infrastructure][aks-tutorial-scale] (Skalieren einer Kubernetes-Anwendung und -Infrastruktur)

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr#list
