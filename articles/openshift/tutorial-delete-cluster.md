---
title: 'Tutorial: Löschen eines Azure Red Hat OpenShift-Clusters | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie einen Azure Red Hat OpenShift-Cluster mithilfe der Azure-Befehlszeilenschnittstelle löschen.
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: ce4142bdcdfb7a9ab687bb60dca91d6aab00c7bd
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079415"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Tutorial: Löschen eines Azure Red Hat OpenShift-Clusters

Dies ist das Ende des Tutorials. Wenn Sie das Testen des Beispielclusters abgeschlossen haben, erfahren Sie hier, wie Sie ihn und die ihm zugeordneten Ressourcen löschen, damit Ihnen nicht in Rechnung gestellt wird, was Sie nicht nutzen.

Im dritten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Löschen eines Azure Red Hat OpenShift-Clusters

In dieser Tutorialserie lernen Sie Folgendes:
> [!div class="checklist"]
> * [Erstellen eines Azure Red Hat OpenShift-Clusters](tutorial-create-cluster.md)
> * [Skalieren eines Azure Red Hat OpenShift-Clusters](tutorial-scale-cluster.md)
> * Löschen eines Azure Red Hat OpenShift-Clusters

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

* Erstellen Sie einen Cluster, indem Sie das Tutorial [Erstellen eines Azure Red Hat OpenShift-Clusters](tutorial-create-cluster.md) ausführen.

## <a name="step-1-sign-in-to-azure"></a>Schritt 1: Anmelden bei Azure

Wenn Sie die Azure CLI lokal ausführen, führen Sie `az login` aus, um sich bei Azure anzumelden.

```bash
az login
```

Wenn Sie Zugriff auf mehrere Abonnements haben, führen Sie `az account set -s {subscription ID}` aus, und ersetzen Sie `{subscription ID}` durch das zu verwendende Abonnement.

## <a name="step-2-delete-the-cluster"></a>Schritt 2: Löschen des Clusters

Öffnen Sie ein Bash-Terminal, und legen Sie die Variable CLUSTER_NAME auf den Namen Ihres Clusters fest:

```bash
CLUSTER_NAME=yourclustername
```

Jetzt löschen Sie Ihren Cluster:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Sie werden gefragt, ob Sie den Cluster löschen möchten. Nachdem Sie mit `y` bestätigt haben, nimmt das Löschen des Clusters einige Minuten in Anspruch. Wenn der Befehl abgeschlossen ist, sind die gesamte Ressourcengruppe und alle darin enthaltenen Ressourcen einschließlich des Clusters gelöscht.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Löschen eines Clusters mit dem Azure-Portal

Alternativ können Sie die zugeordnete Ressourcengruppe des Clusters online mithilfe des Azure-Portals löschen. Der Name der Ressourcengruppe stimmt mit dem Namen Ihres Clusters überein.

Aktuell ist die `Microsoft.ContainerService/openShiftManagedClusters`-Ressource, die beim Erstellen des Clusters erstellt wird, im Azure-Portal ausgeblendet. Markieren Sie in der `Resource group`-Ansicht `Show hidden types`, um die Ressourcengruppe anzuzeigen.

![Screenshot des Kontrollkästchens „Ausgeblendete Typen anzeigen“](./media/aro-portal-hidden-type.png)

Durch das Löschen der Ressourcengruppe werden alle zugehörigen Ressourcen gelöscht, die beim Erstellen eines Azure Red Hat OpenShift-Clusters erstellt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Teil des Tutorials haben Sie Folgendes gelernt:
> [!div class="checklist"]
> * Löschen eines Azure Red Hat OpenShift-Clusters

Weitere Informationen zur Verwendung von OpenShift finden Sie in der offiziellen [Red Hat OpenShift-Dokumentation](https://access.redhat.com/documentation/openshift_dedicated/3/)