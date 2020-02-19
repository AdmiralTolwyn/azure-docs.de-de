---
title: Verwenden von Verfügbarkeitszonen in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie in Azure Kubernetes Service (AKS) einen Cluster erstellen, der Knoten über Verfügbarkeitszonen verteilt.
services: container-service
author: mlearned
ms.custom: fasttrack-edit
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: mlearned
ms.openlocfilehash: b73cb09f95fa2b23fb23fb719fe57143e1731ceb
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086526"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Erstellen eines Azure Kubernetes Service-Clusters (AKS), der Verfügbarkeitszonen verwendet

Ein Azure Kubernetes Service-Cluster (AKS) verteilt Ressourcen wie Knoten und Speicher auf logische Abschnitte der zugrunde liegenden Azure-Computerinfrastruktur. Dieses Bereitstellungsmodell stellt sicher, dass die Knoten über getrennte Update- und Fehlerdomänen in einem einzigen Azure-Rechenzentrum laufen. AKS-Cluster, die mit diesem Standardverhalten bereitgestellt werden, bieten ein hohes Maß an Verfügbarkeit zum Schutz vor Hardwareausfällen oder geplanten Wartungsereignissen.

Um eine höhere Verfügbarkeit Ihrer Anwendungen zu gewährleisten, können AKS-Cluster über Verfügbarkeitszonen verteilt werden. Diese Zonen sind physisch getrennte Rechenzentren innerhalb einer bestimmten Region. Wenn die Clusterkomponenten auf mehrere Zonen verteilt sind, kann Ihr AKS-Cluster einen Fehler in einer dieser Zonen tolerieren. Ihre Anwendungen und Verwaltungsabläufe sind auch bei Problemen in einem gesamten Rechenzentrum weiterhin verfügbar.

Dieser Artikel zeigt Ihnen, wie Sie einen AKS-Cluster erstellen und die Knotenkomponenten auf Verfügbarkeitszonen verteilen.

## <a name="before-you-begin"></a>Voraussetzungen

Azure CLI-Version 2.0.76 oder höher muss installiert und konfiguriert sein. Führen Sie  `az --version` aus, um die Version zu ermitteln. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie weitere Informationen unter  [Installieren der Azure CLI][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Einschränkungen und regionale Verfügbarkeit

AKS-Cluster können derzeit über Verfügbarkeitszonen in den folgenden Regionen erstellt werden:

* USA (Mitte)
* USA (Ost) 2
* East US
* Frankreich, Mitte
* Japan, Osten
* Nordeuropa
* Asien, Südosten
* UK, Süden
* Europa, Westen
* USA, Westen 2

Die folgenden Einschränkungen gelten, wenn Sie einen AKS-Cluster mit Verfügbarkeitszonen erstellen:

* Verfügbarkeitszonen können Sie nur beim Erstellen des Clusters aktivieren.
* Die Einstellungen der Verfügbarkeitszone können nach dem Erstellen des Clusters nicht mehr aktualisiert werden. Außerdem können Sie einen bestehenden, nicht verfügbaren Zonencluster nicht aktualisieren, wenn Sie Verfügbarkeitszonen verwenden möchten.
* Sie können Verfügbarkeitszonen für einen AKS-Cluster auch nicht mehr deaktivieren, nachdem er erstellt wurde.
* Die gewählte Knotengröße (VM-SKU) muss in allen Verfügbarkeitszonen verfügbar sein.
* Cluster mit aktivierten Verfügbarkeitszonen erfordern den Einsatz von Azure Load Balancer Standard-Instanzen zur Verteilung über die Zonen.
* Sie müssen Kubernetes Version 1.13.5 oder höher verwenden, um Load Balancer Standard-Instanzen einsetzen zu können.

AKS-Cluster, die Verfügbarkeitszonen verwenden, müssen die Azure Load Balancer-SKU *Standard* verwenden. Dies ist der Standardwert für den Load Balancer-Typ. Dieser Load Balancer-Typ kann nur zum Zeitpunkt der Clustererstellung definiert werden. Weitere Informationen und die Einschränkungen des Standardlastenausgleichs finden Sie unter [Einschränkungen][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Einschränkungen für Azure-Datenträger

Volumes, die von Azure verwaltete Datenträger verwenden, sind derzeit keine zonalen Ressourcen. Pods, die in einer anderen Zone als ihrer ursprünglichen Zone neu geplant wurden, können ihre vorherigen Datenträger nicht wieder anhängen. Es wird empfohlen, zustandslose Workloads auszuführen, die keinen persistenten Speicher erfordern, der auf zonale Probleme stoßen kann.

Wenn Sie zustandsbehaftete Workloads ausführen müssen, verwenden Sie Taints und Toleranzen in Ihren Pod-Spezifikationen, um den Kubernetes-Scheduler anzuweisen, Pods in der gleichen Zone wie Ihre Datenträger zu erstellen. Alternativ können Sie auch netzwerkbasierten Speicher wie Azure Files verwenden, der sich an Pods anhängen kann, da sie zwischen den Zonen geplant sind.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Übersicht über Verfügbarkeitszonen für AKS-Cluster

Verfügbarkeitszonen sind ein Hochverfügbarkeitsangebot, das Anwendungen und Daten vor Ausfällen von Rechenzentren schützt. Zonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Zur Gewährleistung der Resilienz sind in allen aktivierten Regionen mindestens drei separate Zonen vorhanden. Die physische Trennung von Verfügbarkeitszonen innerhalb einer Region schützt Anwendungen und Daten vor Ausfällen von Rechenzentren. Zonenredundante Dienste replizieren Ihre Anwendungen und Daten in mehreren Verfügbarkeitszonen, um sie vor Single Points of Failure zu schützen.

Weitere Informationen finden Sie unter [Was sind Verfügbarkeitszonen in Azure?][az-overview].

AKS-Cluster, die über Verfügbarkeitszonen bereitgestellt werden, können Knoten über mehrere Zonen innerhalb einer einzelnen Region verteilen. So kann beispielsweise ein Cluster in der Region  *USA, Osten 2* Knoten in allen drei Verfügbarkeitszonen in *USA, Osten 2* anlegen. Diese Verteilung der AKS-Clusterressourcen verbessert die Clusterverfügbarkeit, da sie gegen den Ausfall einer bestimmten Zone resistent sind.

![AKS-Knotenverteilung über Verfügbarkeitszonen hinweg](media/availability-zones/aks-availability-zones.png)

Bei einem Zonenausfall können die Knoten manuell oder über die automatische Clusterskalierung neu abgeglichen werden. Wenn eine einzelne Zone nicht verfügbar ist, werden Ihre Anwendungen weiterhin ausgeführt.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Erstellen eines AKS-Clusters über Verfügbarkeitszonen hinweg

Wenn Sie einen Cluster mit dem Befehl [az aks create][az-aks-create] erstellen, definiert der Parameter `--zones`, in welchen Zonen Agent-Knoten eingesetzt werden. Die AKS-Steuerungsebenenkomponenten für Ihren Cluster sind auch zonenübergreifend in der Höchstverfügbarkeitskonfiguration verteilt, wenn Sie bei der Clustererstellung den Parameter `--zones` festlegen.

Wenn Sie beim Erstellen eines AKS-Clusters keine Zonen für den Standard-Agentpool definieren, verwenden die AKS-Steuerungsebenenkomponenten für Ihren Cluster keine Verfügbarkeitszonen. Sie können zusätzliche Knotenpools mit dem Befehl [az aks nodepool add][az-aks-nodepool-add] hinzufügen und `--zones` für diese neuen Knoten angeben. Für die Komponenten der Steuerungsebene sind die Verfügbarkeitszonen allerdings nicht sichtbar. Sie können die Zonenwahrnehmung für einen Knotenpool oder die AKS-Steuerungsebenenkomponenten nach der Bereitstellung nicht mehr ändern.

Das folgende Beispiel erstellt einen AKS-Cluster namens *myAKSCluster* in der Ressourcengruppe namens *myResourceGroup*. Es werden insgesamt *3* Knoten angelegt – ein Agent in Zone *1*, einer in *2* und dann ein weiterer in *3*. Die AKS-Steuerungsebenenkomponenten sind auch in der Höchstverfügbarkeitskonfiguration über Zonen verteilt, da sie als Teil des Clustererstellungsprozesses definiert sind.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

Die Erstellung des AKS-Clusters dauert einige Minuten.

## <a name="verify-node-distribution-across-zones"></a>Überprüfen der Verteilung der Knoten auf die Zonen

Wenn der Cluster bereit ist, listen Sie die Agent-Knoten in der Skalierungsgruppe auf, um zu sehen, in welcher Verfügbarkeitszone sie bereitgestellt werden.

Rufen Sie zuerst die Anmeldeinformationen für den AKS-Cluster mit dem Befehl [az aks get-credentials][az-aks-get-credentials] ab:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Verwenden Sie anschließend den Befehl [kubectl describe][kubectl-describe], um die Knoten im Cluster aufzulisten. Filtern Sie den *failure-domain.beta.kubernetes.io/zone*-Wert wie im folgenden Beispiel gezeigt:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Das folgende Beispiel zeigt die drei Knoten, die auf die angegebene Region und die Verfügbarkeitszonen verteilt sind, wie z. B. *eastus2-1* für die erste Verfügbarkeitszone und *eastus2-2* für die zweite Verfügbarkeitszone:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Wenn Sie einem Agentpool zusätzliche Knoten hinzufügen, verteilt die Azure-Plattform die zugrunde liegenden VMs automatisch auf die angegebenen Verfügbarkeitszonen.

Beachten Sie, dass in neueren Kubernetes-Versionen (ab 1.17.0) in AKS `topology.kubernetes.io/zone`zusätzlich zur veralteten Bezeichnung`failure-domain.beta.kubernetes.io/zone` die neuere Bezeichnung verwendet wird.

## <a name="verify-pod-distribution-across-zones"></a>Überprüfen der Verteilung der Pods auf die Zonen

Wie unter [Well-Known Labels, Annotations and Taints][kubectl-well_known_labels] (Bekannte Bezeichnungen, Anmerkungen und Taints) dokumentiert, wird in Kubernetes die Bezeichnung `failure-domain.beta.kubernetes.io/zone` zum automatischen Verteilen von Pods in einem Replikationscontroller oder Replikationsdienst in den verschiedenen verfügbaren Zonen verwendet. Um dies zu testen, können Sie den Cluster von 3 auf 5 Knoten zentral hochskalieren, um die korrekte Verteilung der Pods zu überprüfen:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Wenn der Skalierungsvorgang nach einigen Minuten abgeschlossen ist, sollte über den Befehl `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` eine Ausgabe ähnlich dem folgenden Beispiel ausgegeben werden:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

Wie Sie sehen können, sind nun zwei zusätzliche Knoten in Zone 1 und Zone 2 vorhanden. Sie können eine Anwendung bereitstellen, die aus drei Replikaten besteht. Als Beispiel wird NGINX verwendet:

```console
kubectl run nginx --image=nginx --replicas=3
```

Wenn Sie die Knoten überprüfen, auf denen Ihre Pods ausgeführt werden, können Sie sehen, dass die Pods auf den Knoten ausgeführt werden, die den drei verschiedenen Verfügbarkeitszonen entsprechen. Beispielsweise erhalten Sie mit dem Befehl `kubectl describe pod | grep -e "^Name:" -e "^Node:"` eine Ausgabe ähnlich der folgenden:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Wie Sie in dieser Ausgabe sehen können, wird der erste Pod auf dem Knoten 0 ausgeführt, der sich in der Verfügbarkeitszone `eastus2-1` befindet. Der zweite Pod wird auf dem Knoten 2 ausgeführt, der `eastus2-3` entspricht, und der dritte Knoten auf dem Knoten 4, der `eastus2-2` entspricht. Ohne zusätzliche Konfiguration verteilt Kubernetes die Pods ordnungsgemäß auf alle drei Verfügbarkeitszonen.

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel beschreibt, wie Sie einen AKS-Cluster erstellen, der Verfügbarkeitszonen verwendet. Weitere Informationen zu hochverfügbaren Clustern finden Sie unter [Best Practices für Geschäftskontinuität und Notfallwiederherstellung in Azure Kubernetes Service (AKS)][best-practices-bc-dr].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
