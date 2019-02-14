---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 82b3349afd10b585a10619229a2bc6d849d71524
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246886"
---
## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group) eine Azure-Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Erstellen Sie mit dem Befehl [az storage account create](/cli/azure/storage/account) ein allgemeines Speicherkonto. Das allgemeine Speicherkonto kann für alle vier Dienste verwendet werden: Blobs, Files, Tables und Queues. 

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Angeben der Anmeldeinformationen für das Speicherkonto

Für die meisten Befehle der Azure-Befehlszeilenschnittstelle, die in diesem Tutorial verwendet werden, werden die Anmeldeinformationen für das Speicherkonto benötigt. Diese können auf unterschiedliche Weise angegeben werden. Eine der einfachsten Methoden besteht jedoch darin, die Umgebungsvariablen `AZURE_STORAGE_ACCOUNT` und `AZURE_STORAGE_ACCESS_KEY` festzulegen.

Zeigen Sie zunächst mithilfe des Befehls [az storage account keys list](/cli/azure/storage/account/keys) Ihre Speicherkontoschlüssel an:

```azurecli-interactive
az storage account keys list \
    --account-name mystorageaccount \
    --resource-group myResourceGroup \
    --output table
```

Legen Sie dann die Umgebungsvariablen `AZURE_STORAGE_ACCOUNT` und `AZURE_STORAGE_ACCESS_KEY` fest. Hierzu können Sie in der Bash-Shell den Befehl `export` verwenden:

```bash
export AZURE_STORAGE_ACCOUNT="mystorageaccountname"
export AZURE_STORAGE_ACCESS_KEY="myStorageAccountKey"
```
