---
title: "Azure CLI-Skriptbeispiel – Erstellen von zwei VMs mit interner und externer NSG | Microsoft-Dokumentation"
description: "Azure CLI-Skriptbeispiel – Erstellen von zwei VMs mit interner und externer NSG"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.custom: mvc
ms.openlocfilehash: a74eead266451d15ea22538e19c5f12bf04c08b8
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/09/2018
---
# <a name="secure-network-traffic-between-virtual-machines"></a>Sicherer Netzwerkdatenverkehr zwischen virtuellen Computern

Dieses Skript erstellt zwei virtuelle Computer und sichert den eingehenden Datenverkehr für beide VMs. Die erste VM ist über das Internet zugänglich und wird mit einer Netzwerksicherheitsgruppe (NSG) konfiguriert, um Datenverkehr über die Ports 3389 und 80 zuzulassen. Die zweite VM ist nicht über das Internet zugänglich. Sie wird mit einer NSG konfiguriert, die nur Datenverkehr vom ersten virtuellen Computer zulässt. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-windows-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung 

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen zu entfernen.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe, einen virtuellen Computer und alle zugehörigen Ressourcen zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Erstellt ein virtuelles Azure-Netzwerk und ein Subnetz. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Erstellt ein Subnetz. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Erstellt den virtuellen Computer und verbindet diesen mit der Netzwerkkarte, dem virtuellen Netzwerk, dem Subnetz und der NSG. Dieser Befehl legt außerdem das zu verwendende VM-Image und die Administratoranmeldeinformationen fest.  |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_update) | Aktualisiert eine NSG-Regel. In diesem Beispiel wird die Back-End-Regel aktualisiert, um nur Datenverkehr vom Front-End-Subnetz zuzulassen. |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_list) | Gibt Informationen zu einer Netzwerksicherheitsgruppenregel zurück. In diesem Beispiel wird der Regelname in einer Variable gespeichert, um eine spätere Verwendung im Skript zu ermöglichen. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure).

Zusätzliche VM-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Windows-VMs in Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
