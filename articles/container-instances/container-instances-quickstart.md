---
title: 'Schnellstart: Erstellen Ihres ersten Azure Container Instances-Containers'
description: Bereitstellung und erste Schritte mit Azure Container Instances
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 160ba84d2c022ca3af2eb13a9689a282b4a6b198
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Erstellen Ihres ersten Containers in Azure Container Instances
Azure Container Instances erleichtert die Erstellung und Verwaltung von Docker-Containern in Azure, ohne dass Sie virtuelle Computer bereitstellen oder einen übergeordneten Dienst einführen müssen. In dieser Schnellstartanleitung erstellen Sie einen Container in Azure und machen ihn mit einer öffentlichen IP-Adresse über das Internet verfügbar. Dieser Vorgang wird mit einem einzelnen Befehl durchgeführt. Nach wenigen Sekunden wird in Ihrem Browser Folgendes angezeigt:

![Mit Azure Container Instances bereitgestellte App im Browser][aci-app-browser]

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Sie können Azure Cloud Shell oder eine lokale Installation der Azure CLI für diesen Schnellstart verwenden. Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Schnellstart die Azure CLI-Version 2.0.21 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0]( /cli/azure/install-azure-cli) Informationen dazu.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Azure Container Instances-Instanzen sind Azure-Ressourcen und müssen in einer Azure-Ressourcengruppe (eine logische Sammlung für die Bereitstellung und Verwaltung von Azure-Ressourcen) platziert werden.

Erstellen Sie mit dem Befehl [az group create][az-group-create] eine Ressourcengruppe.

Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Erstellen eines Containers

Zum Erstellen eines Containers müssen Sie einen Namen, ein Docker-Image und eine Azure-Ressourcengruppe für den Befehl [az container create][az-container-create] angeben. Der Container kann außerdem mit einer öffentlichen IP-Adresse über das Internet verfügbar gemacht werden, dies ist jedoch optional. In dieser Schnellstartanleitung stellen Sie einen Container bereit, der eine kleine in [Node.js](http://nodejs.org) geschriebene Web-App hostet.

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public --ports 80
```

Innerhalb weniger Sekunden erhalten Sie eine Antwort auf Ihre Anforderung. Der Container befindet sich zunächst im Zustand **Wird erstellt...**, wird aber innerhalb weniger Sekunden gestartet. Sie können den Status mit dem Befehl [az container show][az-container-show] überprüfen:

```azurecli-interactive
az container show --name mycontainer --resource-group myResourceGroup
```

Am unteren Rand der Ausgabe werden der Bereitstellungsstatus und die IP-Adresse des Containers angezeigt:

```json
...
"ipAddress": {
      "ip": "13.88.176.27",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    },
    "osType": "Linux",
    "provisioningState": "Succeeded"
...
```

Sobald sich der Container im Zustand **Erfolgreich** befindet, ist er über den Browser unter der angegebenen IP-Adresse erreichbar.

![Mit Azure Container Instances bereitgestellte App im Browser][aci-app-browser]

## <a name="pull-the-container-logs"></a>Herunterladen der Containerprotokolle

Die Protokolle für den erstellten Container können mithilfe des Befehls [az container logs][az-container-logs] abgerufen werden:

```azurecli-interactive
az container logs --name mycontainer --resource-group myResourceGroup
```

Ausgabe:

```bash
Server running...
10.240.255.107 - - [20/Nov/2017:19:16:28 +0000] "GET / HTTP/1.1" 200 1663 "" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
10.240.255.107 - - [20/Nov/2017:19:16:28 +0000] "GET / HTTP/1.1" 200 1663
10.240.255.107 - - [20/Nov/2017:19:16:28 +0000] "GET /favicon.ico HTTP/1.1" 404 19
```

## <a name="delete-the-container"></a>Löschen des Containers

Wenn Sie den Container nicht mehr benötigen, können Sie ihn mithilfe des Befehls [az container delete][az-container-delete] entfernen:

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

Um sicherzustellen, dass der Container gelöscht wurde, führen Sie den Befehl [az container list](/cli/azure/container#az_container_list) aus:

```azurecli-interactive
az container list --resource-group myResourceGroup -o table
```

Der Container **mycontainer** sollte nicht in der Ausgabe des Befehls angezeigt werden. Wenn die Ressourcengruppe keine anderen Container enthält, wird keine Ausgabe angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Den gesamten Code für den Container aus dieser Schnellstartanleitung sowie die Dockerfile-Datei finden Sie auf [GitHub][app-github-repo]. Wenn Sie ihn selbst erstellen und über Azure Container Registry in Azure Container Instances bereitstellen möchten, fahren Sie mit dem Azure Container Instances-Tutorial fort.

> [!div class="nextstepaction"]
> [Azure Container Instances-Tutorial](./container-instances-tutorial-prepare-app.md)

Um Optionen zum Ausführen von Containern in einem Orchestrierungssystem in Azure zu testen, lesen Sie die Schnellstarts [Bereitstellen einer Service Fabric-Containeranwendung unter Windows in Azure][service-fabric] oder [Deploy an Azure Container Service (AKS) cluster][container-service] (Bereitstellen eines Azure Container Service-Clusters [AKS]).

<!-- LINKS -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[az-group-create]: /cli/azure/group?view=azure-cli-latest#az_group_create
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-delete]: /cli/azure/container?view=azure-cli-latest#az_container_delete
[az-container-list]: /cli/azure/container?view=azure-cli-latest#az_container_list
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
[container-service]: ../aks/kubernetes-walkthrough.md


<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
