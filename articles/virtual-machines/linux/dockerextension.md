---
title: Verwenden der Azure Docker-VM-Erweiterung | Microsoft Docs
description: Erfahren Sie, wie die Docker-VM-Erweiterung verwendet wird, um schnell und sicher eine Docker-Umgebung in Azure mit Resource Manager-Vorlagen und Azure CLI 2.0 bereitzustellen.
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: 2fd7be23c4146051197c4b6d7db6deb06dfa416d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Erstellen einer Docker-Umgebung in Azure mit der Docker-VM-Erweiterung
Docker ist eine beliebte Plattform für die Containerverwaltung und Imageerstellung, die es Ihnen ermöglicht, schnell mit Containern unter Linux zu arbeiten. In Azure gibt es verschiedene Möglichkeiten, wie Sie Docker gemäß Ihren Anforderungen bereitstellen können. In diesem Artikel geht es um die Verwendung der Docker-VM-Erweiterung und der Azure Resource Manager-Vorlagen mithilfe von Azure CLI 2.0. Sie können diese Schritte auch per [Azure CLI 1.0](dockerextension-nodejs.md) ausführen.

## <a name="azure-docker-vm-extension-overview"></a>Übersicht über die Azure Docker-VM-Erweiterung
Die Azure Docker-VM-Erweiterung installiert und konfiguriert den Docker-Daemon, Docker-Client und Docker Compose auf dem virtuellen Linux-Computer (VM). Durch die Verwendung der Azure Docker-VM-Erweiterung haben Sie eine bessere Kontrolle und bessere Features als bei der alleinigen Nutzung von Docker Machine oder bei der selbstständigen Erstellung des Docker-Hosts. Mit diesen zusätzlichen Features, z.B. [Docker Compose](https://docs.docker.com/compose/overview/), ist die Azure Docker-VM-Erweiterung für robustere Entwickler- oder Produktionsumgebungen geeignet.

Weitere Informationen zu den verschiedenen Bereitstellungsmethoden, z.B. der Verwendung von Docker Machine und Azure Container Service, finden Sie in den folgenden Artikeln:

* Zur schnellen Erstellung eines Prototyps für eine App können Sie mit [Docker Machine](docker-machine.md) einen einzelnen Docker-Host erstellen.
* Zum Erstellen von produktionsreifen, skalierbaren Umgebungen, die zusätzliche Planungs- und Verwaltungstools bereitstellen, können Sie einen [Kubernetes](../../container-service/kubernetes/index.yml)- oder [Docker Swarm](../../container-service/dcos-swarm/index.yml)-Cluster in Azure Container Services bereitstellen.


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Bereitstellen einer Vorlage mit der Azure Docker-VM-Erweiterung
Wir verwenden eine vorhandene Schnellstartvorlage zum Erstellen einer Ubuntu-VM, bei der die Azure Docker-VM-Erweiterung zum Installieren und Konfigurieren des Docker-Hosts verwendet wird. Die Vorlage finden Sie hier: [Simple deployment of an Ubuntu VM with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)(Einfache Bereitstellung eines virtuellen Ubuntu-Computers mit Docker). Die neueste Version von [Azure CLI 2.0](/cli/azure/install-az-cli2) muss installiert sein, und Sie müssen mithilfe von [az login](/cli/azure/#az_login) bei einem Azure-Konto angemeldet sein.

Erstellen Sie zunächst mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli
az group create --name myResourceGroup --location eastus
```

Stellen Sie als Nächstes mit [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create) einen virtuellen Computer bereit, der die Azure Docker-VM-Erweiterung aus [dieser Azure Resource Manager-Vorlage auf GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu) enthält. Geben Sie bei Aufforderung Ihre eigenen eindeutigen Werte für *newStorageAccountName*, *adminUsername*, *adminPassword* und *dnsNameForPublicIP* an:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Es dauert einige Minuten, bis die Bereitstellung abgeschlossen ist.


## <a name="deploy-your-first-nginx-container"></a>Bereitstellen des ersten NGINX-Containers
Verwenden Sie [az vm show](/cli/azure/vm#az_vm_show), um die Details Ihrer VM anzuzeigen, einschließlich DNS-Name.

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

SSH zu Ihrem neuen Docker-Host. Geben Sie Ihren eigenen Benutzernamen und den DNS-Namen aus den vorherigen Schritten an:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Nach der Anmeldung am Docker-Host führen wir einen NGINX-Container aus:

```bash
sudo docker run -d -p 80:80 nginx
```

Die Ausgabe ähnelt dem folgenden Beispiel, wenn das NGINX-Image heruntergeladen und ein Container gestartet wird:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Überprüfen Sie den Status des Containers, der auf Ihrem Docker-Host ausgeführt wird, wie folgt:

```bash
sudo docker ps
```

Die Ausgabe ähnelt dem folgenden Beispiel. Sie sehen, dass der NGINX-Container ausgeführt wird und die Weiterleitung über die TCP-Ports 80 und 443 erfolgt:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Um den Container in Aktion zu sehen, öffnen Sie einen Webbrowser und geben den DNS-Namen Ihres Docker-Hosts ein:

![Ausführen des ngnix-Containers](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Vorlagenreferenz für die Azure Docker-VM-Erweiterung
Im vorherigen Beispiel wird eine vorhandene Schnellstartvorlage verwendet. Sie können die Azure Docker-VM-Erweiterung auch mit eigenen Resource Manager-Vorlagen bereitstellen. Fügen Sie Ihren Resource Manager-Vorlagen hierzu Folgendes hinzu, und definieren Sie den `vmName` Ihrer VM entsprechend:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.*",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Eine ausführlichere exemplarische Vorgehensweise zur Verwendung von Resource Manager-Vorlagen finden Sie unter [Übersicht über Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="next-steps"></a>Nächste Schritte
Unter Umständen möchten Sie mithilfe von [Docker Compose](https://docs.docker.com/compose/overview/) den [Docker-Daemon-TCP-Port konfigurieren](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), sich mit der [Docker-Sicherheit](https://docs.docker.com/engine/security/security/) vertraut machen oder Container bereitstellen. Weitere Informationen zur Azure Docker-VM-Erweiterung selbst finden Sie im [GitHub-Projekt](https://github.com/Azure/azure-docker-extension/).

Weitere Informationen zu den zusätzlichen Docker-Bereitstellungsoptionen in Azure finden Sie unter:

* [Verwenden eines Docker-Computers mit dem Azure-Treiber](docker-machine.md)  
* [Erste Schritte mit Docker und Compose zum Definieren und Ausführen einer Anwendung mit mehreren Containern auf einem virtuellen Azure-Computer](docker-compose-quickstart.md)
* [Bereitstellen eines Azure Container Service-Clusters](../../container-service/dcos-swarm/container-service-deployment.md)

