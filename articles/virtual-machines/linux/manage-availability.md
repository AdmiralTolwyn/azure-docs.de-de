---
title: Verwalten der Verfügbarkeit von Linux-VMs in Azure
description: Erfahren Sie, wie Sie mehrere virtuelle Computer verwenden, um Hochverfügbarkeit für Ihre Linux-Anwendung in Azure sicherzustellen.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5742ed346c6761dd443d6252e5c9e457fa952b87
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035903"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Verwalten der Verfügbarkeit virtueller Linux-Computer

Erfahren Sie, wie Sie mehrere virtuelle Computer einrichten und verwalten können, um Hochverfügbarkeit für Ihre Linux-Anwendung in Azure sicherzustellen. Sie können auch die [Verfügbarkeit virtueller Windows-Computer verwalten](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Informationen zum Erstellen einer Verfügbarkeitsgruppe im Resource Manager-Bereitstellungsmodell mithilfe der Befehlszeilenschnittstelle finden Sie unter [azure availset: Befehle zum Verwalten der Verfügbarkeitsgruppen](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Lastenausgleich zwischen virtuellen Computern finden Sie unter [Lastenausgleich für virtuelle Computer](../virtual-machines-linux-load-balance.md).

