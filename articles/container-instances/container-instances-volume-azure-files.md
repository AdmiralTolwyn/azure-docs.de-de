---
title: Einbinden eines Azure Files-Volumes in Azure Container Instances
description: Erfahren Sie, wie Sie ein Azure Files-Volume einbinden, sodass der Zustand bei Azure Container Instances beibehalten wird.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 25cac6a66baeb1587e4b5ba3f0923ca9c4394706
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325486"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Einbinden einer Azure-Dateifreigabe in Azure Container Instances

Standardmäßig ist Azure Container Instances zustandslos. Wenn der Container abstürzt oder beendet wird, gehen alle Zustände verloren. Um den Zustand nach Ablauf der Lebensdauer des Containers beizubehalten, müssen Sie ein Volume aus einem externen Speicher einbinden. In diesem Artikel wird das Einbinden einer mit [Azure Files](../storage/files/storage-files-introduction.md) erstellten Azure-Dateifreigabe für die Verwendung mit Azure Container Instances veranschaulicht. Azure Files bietet vollständig verwaltete Dateifreigaben in der Cloud, auf die über das Branchenstandardprotokoll Server Message Block (SMB) zugegriffen werden kann. Durch das Verwenden einer Azure-Dateifreigabe mit Azure Container Instances werden Dateifreigabefeatures bereitgestellt, die Azure-Dateifreigaben mit virtuellen Azure-Computern ähneln.

> [!NOTE]
> Zurzeit ist das Einbinden einer Azure-Dateifreigabe in Linux-Container eingeschränkt. Bis alle Features auch für Windows-Container verfügbar sind, finden Sie die aktuellen Plattformunterschiede in der [Übersicht](container-instances-overview.md#linux-and-windows-containers).

## <a name="create-an-azure-file-share"></a>Erstellen einer Azure-Dateifreigabe

Bevor Sie eine Azure-Dateifreigabe in Azure Container Instances einbinden, müssen Sie sie erstellen. Führen Sie das folgende Skript aus, um ein Speicherkonto zum Hosten der Dateifreigabe und die Freigabe selbst zu erstellen. Der Name des Speicherkontos muss global eindeutig sein, daher fügt das Skript der Basis-Zeichenfolge einen Zufallswert hinzu.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Erhalten der Zugriffsinformationen für das Azure-Speicherkonto

Um eine Azure-Dateifreigabe als Volume in Azure Container Instances einzubinden, benötigen Sie drei Werte: den Namen des Speicherkontos, den Freigabenamen und den Speicherzugriffsschlüssel.

Wenn Sie das obige Skript verwendet haben, wurde der Name des Speicherkontos in der Variable $ACI_PERS_STORAGE_ACCOUNT_NAME gespeichert. Um den Kontonamen einzusehen, geben Sie Folgendes ein:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

Der Freigabename ist bereits bekannt (im Skript oben festgelegt als *acishare*), daher verbleibt nur der Speicherkontoschlüssel, der mit dem folgenden Befehl gesucht werden kann:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume---cli"></a>Bereitstellen des Containers und Einbinden des Volumes – CLI

Um eine Azure-Dateifreigabe mithilfe der Azure CLI als Volume in einen Container einzubinden, geben Sie die Freigabe und den Einbindungspunkt des Volumes bei der Erstellung des Containers mit [az container create][az-container-create] an. Wenn Sie die vorherigen Schritte ausgeführt haben, können Sie die Freigabe, die Sie zuvor erstellt haben, einbinden, indem Sie mit dem folgenden Befehl einen Container erstellen:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

Der `--dns-name-label`-Wert muss in der Azure-Region, in der Sie die Containerinstanz erstellen, eindeutig sein. Aktualisieren Sie den Wert im vorherigen Befehl, wenn Sie beim Ausführen des Befehls eine Fehlermeldung bezüglich der **DNS-Namensbezeichnung** erhalten.

## <a name="manage-files-in-mounted-volume"></a>Verwalten von Dateien in eingebundenen Datenträgern

Sobald der Container gestartet ist, können Sie die einfache Web-App verwenden, die über das Microsoft [aci-hellofiles][aci-hellofiles] image to create small text files in the Azure file share at the mount path you specified. Obtain the web app's fully qualified domain name (FQDN) with the [az container show][az-container-show]-Image zum Erstellen kleiner Textdateien in der Azure-Dateifreigabe in dem von Ihnen angegebenen Einbindungspfad bereitgestellt wird. Rufen Sie den vollqualifizierten Domänennamen (FQDN) der Web-App mit dem Befehl [az container show][az-container-show] ab:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

Nachdem Sie den Text mit der App gespeichert haben, können Sie im [Azure-Portal][portal] or a tool like the [Microsoft Azure Storage Explorer][storage-explorer]oder mit einem Tool wie [Microsoft Azure Storage Explorer][storage-explorer] die auf die Dateifreigabe geschriebene Datei abrufen und überprüfen.

## <a name="deploy-container-and-mount-volume---yaml"></a>Bereitstellen des Containers und Einbinden des Volumes – YAML

Sie können auch eine Containergruppe bereitstellen und mit der Azure CLI und einer [YAML-Vorlage](container-instances-multi-container-yaml.md) ein Volume in einen Container einbinden. Die Bereitstellung der YAML-Vorlage ist die bevorzugte Methode bei der Bereitstellung von Containergruppen, die aus mehreren Containern bestehen.

Die folgende YAML-Vorlage definiert eine Containergruppe mit einem Container, der ein `aci-hellofiles`-Image einbindet. Der Container bindet die zuvor als Volume erstellte Azure-Dateifreigabe *acishare* ein. Geben Sie, wo angegeben, den Namen und den Speicherschlüssel für das Speicherkonto ein, das die Dateifreigabe hostet. 

Wie im CLI-Beispiel muss der `dnsNameLabel`-Wert in der Azure-Region, in der Sie die Containerinstanz erstellen, eindeutig sein. Aktualisieren Sie den Wert in der YAML-Datei, falls erforderlich.

```yaml
apiVersion: '2018-10-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Um mit der YAML-Vorlage bereitzustellen, speichern Sie den vorherigen YAML-Code in einer Datei namens `deploy-aci.yaml` und führen dann den Befehl [az container create][az-container-create] mit dem Parameter `--file` aus:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Bereitstellen des Containers und Einbinden des Volumes – Resource Manager

Zusätzlich zur CLI- und YAML-Bereitstellung können Sie mit einer [Azure Resource Manager-Vorlage](/azure/templates/microsoft.containerinstance/containergroups) eine Containergruppe bereitstellen und ein Volume einbinden.

Füllen Sie in der Vorlage zunächst das `volumes`-Array im Abschnitt `properties` für die Containergruppe auf. 

Füllen Sie dann für jeden Container, in den Sie das Volume einbinden möchten, das `volumeMounts`-Array im `properties`-Abschnitt der Containerdefinition auf.

Die folgende Resource Manager-Vorlage definiert eine Containergruppe, die einen mit dem `aci-hellofiles`-Image erstellten Container enthält. Der Container bindet die zuvor als Volume erstellte Azure-Dateifreigabe *acishare* ein. Geben Sie, wo angegeben, den Namen und den Speicherschlüssel für das Speicherkonto ein, das die Dateifreigabe hostet. 

Wie in den vorherigen Beispielen muss der `dnsNameLabel`-Wert in der Azure-Region, in der Sie die Containerinstanz erstellen, eindeutig sein. Aktualisieren Sie den Wert in der Vorlage, falls erforderlich.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

Um mit der Resource Manager-Vorlage bereitzustellen, speichern Sie den vorherigen JSON-Code in einer Datei namens `deploy-aci.json` und führen dann den Befehl [az group deployment create][az-group-deployment-create] mit dem Parameter `--template-file` aus:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Einbinden mehrerer Volumes

Sie können mehrere Volumes in eine Containerinstanz einbinden, indem Sie zum Bereitstellen eine [Azure Resource Manager-Vorlage](/azure/templates/microsoft.containerinstance/containergroups) oder eine YAML-Datei verwenden. Stellen Sie zum Verwenden einer Vorlage oder YAML-Datei die Freigabedetails bereit, und definieren Sie die Volumes, indem Sie das Array `volumes` im Abschnitt `properties` der Vorlage auffüllen. 

Wenn Sie z.B. zwei Azure Files-Freigaben mit den Namen *share1* und *share2* im Speicherkonto *myStorageAccount* erstellt haben, wird das Array `volumes` in einer Resource Manager-Vorlage ähnlich wie im folgenden Beispiel aussehen:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Füllen Sie als Nächstes für jeden Container in der Containergruppe, in den Sie die Volumes einbinden möchten, das Array `volumeMounts` im Abschnitt `properties` der Containerdefinition auf. Dadurch werden beispielsweise die beiden zuvor definierten Volumes *myvolume1* und *myvolume2* eingebunden:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie andere Volumetypen in Azure Container Instances bereitgestellt werden:

* [Einbinden eines emptyDir-Volumes in Azure Container Instances](container-instances-volume-emptydir.md)
* [Einbinden eines gitRepo-Volumes in Azure Container Instances](container-instances-volume-gitrepo.md)
* [Einbinden eines geheimen Volumes in Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create