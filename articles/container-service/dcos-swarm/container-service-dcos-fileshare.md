---
title: (VERALTET) Dateifreigabe für Azure DC/OS-Cluster
description: Bereitstellen und Einbinden einer Dateifreigabe für einen DC/OS-Cluster in Azure Container Service
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: e6651fc5988a1e1830807219cda02ab057db9a4f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "60480383"
---
# <a name="deprecated-create-and-mount-a-file-share-to-a-dcos-cluster"></a>(VERALTET) Erstellen und Bereitstellen einer Dateifreigabe für einen DC/OS-Cluster

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In diesem Tutorial wird erklärt, wie eine Dateifreigabe unter Azure erstellt und in jeden Agent und Master des DC/OS-Clusters eingebunden wird. Das Einrichten einer Dateifreigabe vereinfacht das Freigeben von Dateien über Ihren gesamten Cluster hinweg im Hinblick auf Konfiguration, Zugriff, Protokolle und mehr. In diesem Tutorial werden die folgenden Aufgaben ausgeführt:

> [!div class="checklist"]
> * Erstellen eines Azure-Speicherkontos
> * Erstellen einer Dateifreigabe
> * Einbinden der Freigabe in das DC/OS-Cluster

Sie benötigen einen ACS DC/OS-Cluster, um die Schritte in diesem Tutorial auszuführen. Bei Bedarf können Sie mit diesem [Beispielskript](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) einen erstellen.

Für dieses Tutorial ist mindestens Version 2.0.4 der Azure CLI erforderlich. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle]( /cli/azure/install-azure-cli) weitere Informationen. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Erstellen einer Dateifreigabe auf Microsoft Azure

Bevor Sie eine Azure-Dateifreigabe mit einem ACS DC/OS-Cluster verwenden, müssen das Speicherkonto und die Dateifreigabe erstellt werden. Führen Sie das folgende Skript aus, um den Speicher und die Dateifreigabe zu erstellen. Aktualisieren Sie die Parameter mit denen aus Ihrer Umgebung.

```azurecli-interactive
# Change these four parameters
DCOS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
DCOS_PERS_RESOURCE_GROUP=myResourceGroup
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=dcosshare

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>Einbinden der Freigabe in Ihren Cluster

Als Nächstes muss die Dateifreigabe auf jedem virtuellen Computer in Ihrem Cluster eingebunden werden. Diese Aufgabe wird mit dem CIFS-Tool/Protokoll durchgeführt. Der Einbindungsvorgang kann manuell auf jedem Knoten des Clusters oder durch Ausführen eines Skripts gegen jeden Knoten im Cluster abgeschlossen werden.

In diesem Beispiel werden zwei Skripts ausgeführt: eines, das die Azure-Dateifreigabe einbindet, und ein zweites, das dieses Skripts auf jedem Knoten des DC/OS-Clusters ausführt.

Zuerst sind der Azure Storage-Kontoname und der Zugriffsschlüssel erforderlich. Führen Sie die folgenden Befehle aus, um diese Informationen abzurufen. Notieren Sie diese Werte, da sie in einem späteren Schritt verwendet werden.

Azure Storage-Kontoname:

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Zugriffsschlüssel für das Azure Storage-Konto:

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

Rufen Sie dann den FQDN des Master-DC/OS ab, und speichern Sie ihn in einer Variablen.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

Kopieren Sie Ihren privaten Schlüssel auf den Masterknoten. Dieser Schlüssel ist erforderlich, um eine SSH-Verbindung mit allen Knoten im Cluster zu erstellen. Aktualisieren Sie den Benutzernamen, wenn ein kein Standardwert für die Erstellung des Clusters verwendet wurde. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

Erstellen Sie eine SSH-Verbindung mit dem Master (oder dem ersten Master) des DC/OS-basierten Clusters. Aktualisieren Sie den Benutzernamen, wenn ein kein Standardwert für die Erstellung des Clusters verwendet wurde.

```azurecli-interactive
ssh azureuser@$FQDN
```

Erstellen Sie eine Datei namens **cifsMount.sh**, und kopieren Sie den folgenden Inhalt hinein. 

Dieses Skript dient zum Einbinden der Azure-Dateifreigabe. Aktualisieren Sie die Variablen `STORAGE_ACCT_NAME` und `ACCESS_KEY` mit den zuvor gesammelten Informationen.

```azurecli-interactive
#!/bin/bash

# Azure storage account name and access key
SHARE_NAME=dcosshare
STORAGE_ACCT_NAME=mystorageaccount
ACCESS_KEY=mystorageaccountKey

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/$SHARE_NAME" ]; then sudo mkdir -p "/mnt/share/$SHARE_NAME" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //$STORAGE_ACCT_NAME.file.core.windows.net/$SHARE_NAME /mnt/share/$SHARE_NAME -o vers=3.0,username=$STORAGE_ACCT_NAME,password=$ACCESS_KEY,dir_mode=0777,file_mode=0777
```
Erstellen Sie eine zweite Datei namens **getNodesRunScript.sh**, und kopieren Sie den folgenden Inhalt in die Datei. 

Dieses Skript erkennt alle Knoten des Clusters und führt dann das Skript **cifsMount.sh** aus, um auf jedem Knoten die Dateifreigabe einzubinden.

```azurecli-interactive
#!/bin/bash

# Install jq used for the next command
sudo apt-get install jq -y

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes

# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
do
  ssh `whoami`@$line -o StrictHostKeyChecking=no < ./cifsMount.sh
  done
```

Führen Sie das Skript zum Einbinden der Azure-Dateifreigabe auf allen Knoten des Clusters aus.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

Die Dateifreigabe ist jetzt unter `/mnt/share/dcosshare` auf jedem Knoten des Clusters verfügbar.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde eine Azure-Dateifreigabe für einen DC/OS-Cluster mithilfe der folgenden Schritte eingebunden:

> [!div class="checklist"]
> * Erstellen eines Azure-Speicherkontos
> * Erstellen einer Dateifreigabe
> * Einbinden der Freigabe in das DC/OS-Cluster

Wechseln Sie zum nächsten Tutorial, um mehr über das Integrieren einer Azure Container Registry mit DC/OS in Azure zu erfahren.  

> [!div class="nextstepaction"]
> [Lastausgleich für Anwendungen](container-service-dcos-acr.md)
