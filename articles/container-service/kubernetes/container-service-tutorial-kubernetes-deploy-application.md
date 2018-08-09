---
title: Tutorial für Azure Container Service – Bereitstellen einer Anwendung
description: Tutorial für Azure Container Service – Bereitstellen einer Anwendung
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 46b8aead2a217ab827731a6636d3527fd99ea753
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432105"
---
# <a name="run-applications-in-kubernetes"></a>Ausführen von Anwendungen in Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

In diesem Tutorial – Teil 4 von 7 – wird eine Beispielanwendung in einem Kubernetes-Cluster bereitgestellt. Folgende Schritte werden ausgeführt:

> [!div class="checklist"]
> * Aktualisieren von Kubernetes-Manifestdateien
> * Ausführen einer Anwendung in Kubernetes
> * Testen der Anwendung

In den nachfolgenden Tutorials wird diese Anwendung horizontal hochskaliert. Zudem wird Log Analytics für die Überwachung des Kubernetes-Clusters konfiguriert.

Dieses Tutorial setzt voraus, dass Sie grundlegend mit den Konzepten von Kubernetes vertraut sind. Ausführliche Informationen zu Kubernetes finden Sie in der [Kubernetes-Dokumentation](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Voraussetzungen

In vorherigen Tutorials wurde eine Anwendung in ein Containerimage gepackt, das Image wurde in Azure Container Registry hochgeladen, und es wurde ein Kubernetes-Cluster erstellt. 

Für dieses Tutorial benötigen Sie die vorab erstellte Kubernetes-Manifestdatei `azure-vote-all-in-one-redis.yml`. Diese Datei wurde in einem vorherigen Tutorial mit dem Anwendungsquellcode heruntergeladen. Stellen Sie sicher, dass Sie das Repository geklont und in das geklonte Repository gewechselt haben.

Wenn Sie diese Schritte nicht ausgeführt haben und dies jetzt nachholen möchten, kehren Sie zum [Tutorial 1 – Erstellen von Containerimages](./container-service-tutorial-kubernetes-prepare-app.md) zurück. 

## <a name="update-manifest-file"></a>Aktualisieren der Manifestdatei

In diesem Tutorial wurde Azure Container Registry (ACR) zum Speichern eines Containerimages verwendet. Vor dem Ausführen der Anwendung muss der ACR-Anmeldeservername in der Kubernetes-Manifestdatei aktualisiert werden.

Rufen Sie den ACR-Anmeldeservernamen mit dem [az acr list](/cli/azure/acr#az-acr-list)-Befehl auf.

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Die Manifestdatei wurde vorab mit dem Serveranmeldenamen `microsoft` erstellt. Öffnen Sie die Datei mit einem Text-Editor. In diesem Beispiel wird die Datei mit `vi` geöffnet.

```bash
vi azure-vote-all-in-one-redis.yml
```

Ersetzen Sie `microsoft` durch den ACR-Anmeldeservernamen. Dieser Wert befindet sich in Zeile **47** der Manifestdatei.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Speichern und schließen Sie die Datei.

## <a name="deploy-application"></a>Bereitstellen der Anwendung

Führen Sie die Anwendung mithilfe des Befehls [kubectl create](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create) aus. Dieser Befehl analysiert die Manifestdatei und erstellt die definierten Kubernetes-Objekte.

```azurecli-interactive
kubectl create -f azure-vote-all-in-one-redis.yml
```

Ausgabe:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Testen der Anwendung

Es wird ein [Kubernetes-Dienst](https://kubernetes.io/docs/concepts/services-networking/service/) erstellt, der die Anwendung für das Internet verfügbar macht. Dieser Vorgang kann einige Minuten dauern. 

Verwenden Sie zum Überwachen des Fortschritts den Befehl [kubectl get service](https://review.docs.microsoft.com/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) mit dem Argument `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Die **externe IP-Adresse** für den Dienst `azure-vote-front` wird zunächst als `pending` angezeigt. Sobald die externe IP-Adresse nicht mehr `pending` ist, sondern eine `IP address` angezeigt wird, verwenden Sie `CTRL-C`, um die kubectl-Überwachung zu beenden.

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Um die Anwendung anzuzeigen, navigieren Sie zu der externen IP-Adresse.

![Abbildung: Kubernetes-Cluster in Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde die Azure Vote-Anwendung in einem Azure Container Service-Kubernetes-Cluster bereitgestellt. Folgende Aufgaben wurden ausgeführt:  

> [!div class="checklist"]
> * Herunterladen von Kubernetes-Manifestdateien
> * Ausführen der Anwendung in Kubernetes
> * Testen der Anwendung

Wechseln Sie zum nächsten Tutorial, wo Sie erfahren, wie Sie eine Kubernetes-Anwendung und die zugrunde liegende Kubernetes-Infrastruktur skalieren. 

> [!div class="nextstepaction"]
> [Scale Kubernetes application and infrastructure](./container-service-tutorial-kubernetes-scale.md) (Skalieren einer Kubernetes-Anwendung und -Infrastruktur)