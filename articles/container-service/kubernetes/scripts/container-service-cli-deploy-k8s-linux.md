---
title: 'Azure CLI-Skriptbeispiel: Erstellen von ACS Linux-Kubernetes-Cluster | Microsoft Dokumentation'
description: 'Azure CLI-Skriptbeispiel: Erstellen von ACS-Linux-Kubernetes-Cluster'
services: container-service
documentationcenter: ''
author: neilpeterson
manager: jeconnoc
editor: ''
tags: acs, azure-container-service
keywords: Docker, Container, Microservices, Kubernetes, DC/OS, Azure
ms.assetid: ''
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: nepeters
ms.openlocfilehash: 938990e0c39e96d909c07bef05d2630a01e3076a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423758"
---
# <a name="create-an-azure-container-service-kubernetes-linux-cluster"></a>Erstellen eines Kubernetes-Clusters von Azure Container Service für Linux

In diesem Beispiel wird ein Azure Container Service-Cluster mit Kubernetes für Linux-basierte Container ausgeführt.

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript

```azurecli
az group create --name myResourceGroup --location eastus

az acs create \
  --orchestrator-type kubernetes \
  --resource-group myResourceGroup \
  --name myK8SCluster \
  --generate-ssh-keys
```

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung 

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen zu entfernen.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet die folgenden Befehle zum Erstellen der Bereitstellung. Jedes Element in der Tabelle ist mit der befehlsspezifischen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az acs create](https://docs.microsoft.com/cli/azure/acs#az-acs-create) | Erstellt einen ACS-Cluster. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure).

Weitere CLI-Skriptbeispiele von Azure Container Service finden Sie in der [Dokumentation zu Azure Container Service](../cli-samples.md).

