---
title: (VERALTET) Tutorial für Azure Container Service – Bereitstellen eines Clusters
description: Tutorial für Azure Container Service – Bereitstellen eines Clusters
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 58fef0357a903f2ab1d238bbab7b2d9dca673eb4
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55662056"
---
# <a name="deprecated-deploy-a-kubernetes-cluster-in-azure-container-service"></a>(VERALTET) Bereitstellen eines Kubernetes-Clusters in Azure Container Service

> [!TIP]
> Die aktualisierte Version dieses Tutorials zu Azure Kubernetes Service finden Sie im [Tutorial: Bereitstellen eines Azure Kubernetes Service-Clusters (AKS)](../../aks/tutorial-kubernetes-deploy-cluster.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Kubernetes bietet eine verteilte Plattform für containerbasierte Anwendungen. Mit Azure Container Service ist die Bereitstellung eines produktionsbereiten Kubernetes-Clusters schnell und einfach. In diesem Tutorial (Teil 3 von 7) wird ein Azure Container Service-Kubernetes-Clusters bereitgestellt. Folgende Schritte werden ausgeführt:

> [!div class="checklist"]
> * Bereitstellen eines Kubernetes-ACS-Clusters
> * Installation der Kubernetes-Befehlszeilenschnittstelle (kubectl)
> * Konfiguration von kubectl

In den nachfolgenden Tutorials wird die Azure Vote-Anwendung im Cluster bereitgestellt, skaliert und aktualisiert. Außerdem wird Log Analytics für die Überwachung des Kubernetes-Clusters konfiguriert.

## <a name="before-you-begin"></a>Voraussetzungen

In vorherigen Tutorials wurde ein Containerimage erstellt und in eine Azure Container Registry-Instanz hochgeladen. Wenn Sie diese Schritte nicht ausgeführt haben und dies jetzt nachholen möchten, kehren Sie zum [Tutorial 1 – Erstellen von Containerimages](./container-service-tutorial-kubernetes-prepare-app.md) zurück.

## <a name="create-kubernetes-cluster"></a>Erstellen eines Kubernetes-Clusters

Erstellen Sie mit dem Befehl [az acs create](/cli/azure/acs#az-acs-create) einen Kubernetes-Cluster in Azure Container Service. 

Das folgende Beispiel erstellt einen Cluster mit dem Namen `myK8sCluster` in einer Ressourcengruppe namens `myResourceGroup`. Diese Ressourcengruppe wurde im [vorherigen Tutorial](./container-service-tutorial-kubernetes-prepare-acr.md) erstellt.

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8SCluster --generate-ssh-keys 
```

Manchmal hat ein Azure-Abonnement eingeschränkten Zugriff auf Azure-Ressourcen. Dies ist beispielsweise bei einem eingeschränkten Testabonnement der Fall. Tritt bei der Bereitstellung ein Fehler aufgrund von begrenzt verfügbaren Kernen auf, verringern Sie die Anzahl der Standard-Agents, indem Sie `--agent-count 1` zum Befehl [az acs create](/cli/azure/acs#az-acs-create) hinzufügen. 

Nach einigen Minuten ist die Bereitstellung abgeschlossen, und es werden Informationen zur ACS-Bereitstellung im JSON-Format zurückgegeben.

## <a name="install-the-kubectl-cli"></a>Installieren der Befehlszeilenschnittstelle „kubectl“

Zum Herstellen der Verbindung mit dem Kubernetes-Cluster auf Ihrem Clientcomputer verwenden Sie den Kubernetes-Befehlszeilenclient [kubectl](https://kubernetes.io/docs/user-guide/kubectl/). 

Bei Verwendung von Azure Cloud Shell ist „kubectl“ bereits installiert. Wenn Sie ihn lokal installieren möchten, verwenden Sie den Befehl [az acs kubernetes install-cli](/cli/azure/acs/kubernetes).

Bei Ausführung unter Linux oder macOS müssen Sie möglicherweise sudo verwenden. Stellen Sie unter Windows sicher, dass die Shell als Administrator ausgeführt wird.

```azurecli-interactive 
az acs kubernetes install-cli 
```

Unter Windows ist *C:\Programme (x86)\kubectl.exe* die standardmäßige Installationsdatei. Sie müssen diese Datei möglicherweise zu diesen Windows-Verzeichnis hinzufügen. 

## <a name="connect-with-kubectl"></a>Verbinden mit kubectl

Führen Sie den Befehl [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes) aus, um „kubectl“ für die Verbindungsherstellung mit Ihrem Kubernetes-Cluster zu konfigurieren.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group myResourceGroup --name myK8SCluster
```

Um die Verbindung mit dem Cluster zu überprüfen, führen Sie den Befehl [kubectl get nodes](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) aus.

```azurecli-interactive
kubectl get nodes
```

Ausgabe:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

Nach Abschluss des Tutorials verfügen Sie über einen ACS-Kubernetes-Cluster, der für die Verarbeitung von Workloads bereit ist. In nachfolgenden Tutorials wird in diesem Cluster eine Anwendung mit mehreren Containern bereitgestellt, horizontal hochskaliert, aktualisiert und überwacht.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde ein Azure Container Service-Kubernetes-Clusters bereitgestellt. Die folgenden Schritte wurden durchgeführt:

> [!div class="checklist"]
> * Bereitstellen eines Kubernetes-ACS-Clusters
> * Installieren der Kubernetes-Befehlszeilenschnittstelle (kubectl)
> * Konfigurieren von kubectl

Fahren Sie mit dem nächsten Tutorial fort, um mehr über die Ausführung von Anwendungen im Cluster zu erfahren.

> [!div class="nextstepaction"]
> [Bereitstellen einer Anwendung in Kubernetes](./container-service-tutorial-kubernetes-deploy-application.md)
