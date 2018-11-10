---
title: Erstellen eines klassischen virtuellen Linux-Computers über die klassische Azure CLI | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen virtuellen Linux-Computer mithilfe der klassischen Azure CLI und dem klassischen Bereitstellungsmodell erstellen.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: d8e469289f72fe892ea7c3da99972e6326c75eb9
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242535"
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-classic-cli"></a>Erstellen eines klassischen virtuellen Linux-Computers über die klassische Azur CLI
> [!IMPORTANT] 
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager- und klassische Bereitstellung](../../../resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells. Die Resource Manager-Version finden Sie [hier](../create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Dieses Thema enthält Informationen zum Erstellen eines virtuellen Linux-Computers (VM, Virtual Machine) mithilfe der klassischen Azure CLI und des klassischen Bereitstellungsmodells. Wir verwenden ein Linux-Image aus den verfügbaren **IMAGES** in Azure. Die klassischen Azure CLI-Befehle enthalten u.a. folgende Konfigurationsoptionen:

* Verbinden des virtuellen Computers mit einem virtuellen Netzwerk
* Hinzufügen des virtuellen Computers zu einem vorhandenen Clouddienst
* Hinzufügen des virtuellen Computers zu einem vorhandenen Speicherkonto
* Hinzufügen des virtuellen Computers zu einer Verfügbarkeitsgruppe oder einem Speicherort

> [!IMPORTANT]
> Wenn Ihr virtueller Computer ein virtuelles Netzwerk verwenden soll, damit Sie sich direkt über den Hostnamen oder über eine lokal eingerichtete Verbindung mit ihm verbinden können, stellen Sie sicher, dass Sie das virtuelle Netzwerk schon dann angeben, wenn Sie den virtuellen Computer erstellen. Ein virtueller Computer kann so konfiguriert werden, dass er nur zum Zeitpunkt seiner Erstellung einem virtuellen Netzwerk hinzugefügt werden kann. Ausführliche Informationen über virtuelle Netzwerke erhalten Sie unter [Übersicht über Azure Virtual Network](https://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>Erstellen einer Linux-VM mithilfe des klassischen Bereitstellungsmodells
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]

