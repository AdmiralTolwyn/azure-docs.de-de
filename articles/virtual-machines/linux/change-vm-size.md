---
title: Ändern der Größe eines virtuellen Linux-Computers mit der Azure CLI | Microsoft-Dokumentation
description: So skalieren Sie einen virtuellen Linux-Computer zentral hoch oder herunter, indem Sie die VM-Größe ändern.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2017
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0b8802c91ceb59d391dc27a71da905de9c15a1dc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993227"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Ändern der Größe eines virtuellen Linux-Computers über die Azure CLI 

Nachdem Sie einen virtuellen Computer (VM) bereitstellen, können Sie ihn zentral hoch- oder herunterskalieren, indem Sie die [VM-Größe][vm-sizes] ändern. In einigen Fällen müssen Sie zuerst die Zuordnung des virtuellen Computers aufheben. Sie müssen die Zuordnung aufheben, wenn die gewünschte Größe in dem Hardwarecluster nicht verfügbar ist, in dem der virtuelle Computer gehostet wird. In diesem Artikel wird erläutert, wie Sie die Größe eines virtuellen Linux-Computers mithilfe der Azure CLI ändern. 

## <a name="resize-a-vm"></a>Ändern der Größe eines virtuellen Computers
Zum Ändern der Größe eines virtuellen Computers muss die neueste [Azure CLI](/cli/azure/install-az-cli2) installiert sein, und Sie müssen über [az login](/cli/azure/reference-index#az_login) bei einem Azure-Konto angemeldet sein.

1. Zeigen Sie mit [az vm list-vm-resize-options](/cli/azure/vm#az_vm_list_vm_resize_options) die Liste der verfügbaren VM-Größen in dem Hardwarecluster an, in dem der virtuelle Computer gehostet wird. Das folgende Beispiel listet VM-Größen für den virtuellen Computer `myVM` in der Region der Ressourcengruppe `myResourceGroup` auf:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Wenn die gewünschte VM-Größe aufgeführt wird, ändern Sie mithilfe von [az vm resize](/cli/azure/vm#az_vm_resize) die Größe des virtuellen Computers. Das folgende Beispiel ändert den virtuellen Computer `myVM` in die Größe `Standard_DS3_v2`:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    Der virtuelle Computer wird während dieses Vorgangs neu gestartet. Nach dem Neustart werden Ihr vorhandenes Betriebssystem und die Datenträger neu zugeordnet. Alle Daten auf dem temporären Datenträger gehen verloren.

3. Wenn die gewünschte VM-Größe nicht aufgeführt wird, müssen Sie zuerst mit [az vm deallocate](/cli/azure/vm#az_vm_deallocate) die Zuordnung des virtuellen Computers aufheben. Nach diesem Vorgang kann der virtuelle Computer auf eine beliebige, in der Region unterstützte Größe geändert und dann gestartet werden. Mit den folgenden Schritten wird für den virtuellen Computer `myVM` in der Ressourcengruppe `myResourceGroup` die Zuordnung aufgehoben und die Größe geändert. Anschließend wird der Computer gestartet:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Die Aufhebung der Zuordnung der VM gibt auch jegliche dynamische IP-Adressen frei, die der VM zugewiesen sind. Das Betriebssystem und die Datenträger sind nicht betroffen.

## <a name="next-steps"></a>Nächste Schritte
Führen Sie mehrere VM-Instanzen aus, und skalieren Sie diese zentral hoch, um zusätzliche Skalierbarkeit zu erhalten. Weitere Informationen finden Sie unter [Automatisches Skalieren von Linux-Computern in einer VM-Skalierungsgruppe][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
