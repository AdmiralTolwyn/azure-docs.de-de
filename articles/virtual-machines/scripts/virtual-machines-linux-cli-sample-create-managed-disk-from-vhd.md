---
title: 'Erstellen eines verwalteten Datenträgers auf der Grundlage einer VHD-Datei in einem Speicherkonto im gleichen Abonnement: CLI-Beispiel'
description: 'Azure CLI-Beispielskript: Erstellen verwalteter Datenträger aus einer VHD-Datei in einem Speicherkonto in demselben Abonnement'
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: d15e0519e9ee5e5d1d6832e37c10119e40add890
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75458489"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Erstellen verwalteter Datenträger aus einer VHD-Datei in einem Speicherkonto in demselben Abonnement mithilfe der Befehlszeilenschnittstelle

Dieses Skript erstellt einen verwalteten Datenträger aus einer VHD-Datei in einem Speicherkonto in demselben Abonnement. Sie können dieses Skript verwenden, um eine bestimmte (nicht generalisierte/mit Sysprep vorbereitete) VHD auf den verwalteten Betriebssystem-Datenträger zu importieren, damit mit ihr ein virtueller Computer erstellt werden kann. Außerdem können Sie damit eine Daten-VHD auf verwaltete Datenträger importieren. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]


## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet die folgenden Befehle, um einen verwalteten Datenträger aus einer VHD zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Erstellt mithilfe des URI einen verwalteten Datenträger aus einer VHD in einem Speicherkonto in demselben Abonnement |

## <a name="next-steps"></a>Nächste Schritte

[Erstellen eines virtuellen Computers mit einem vorhandenen verwalteten Betriebssystemdatenträger mit PowerShell](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure).

Zusätzliche CLI-Skriptbeispiele für virtuelle Computer und verwaltete Datenträger finden Sie in der [Dokumentation zu Linux-VMs in Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
