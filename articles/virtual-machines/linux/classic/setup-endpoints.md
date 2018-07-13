---
title: Einrichten von Endpunkten auf einer klassischen Linux-VM | Microsoft Docs
description: Erfahren Sie, wie Endpunkte für virtuelle Linux-Computer im Azure-Portal eingerichtet werden, um die Kommunikation mit einem virtuellen Linux-Computer in Azure zu ermöglichen.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: f3749738-1109-4a1d-8635-40e4bd220e91
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: 03cb90dcdcc7a7407898ddd8cbc30f1af0833676
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38295686"
---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Gewusst wie: Einrichten von Endpunkten auf einem klassischen virtuellen Linux-Computer in Azure
Alle virtuellen Linux-Computer, die Sie mit dem klassischen Bereitstellungsmodell in Azure erstellen, können automatisch über einen privaten Netzwerkkanal mit anderen virtuellen Computern im gleichen Clouddienst oder virtuellen Netzwerk kommunizieren. Computer im Internet oder anderen virtuellen Netzwerken erfordern jedoch von den Endpunkten, dass sie den eingehenden Netzwerkdatenverkehr auf einen virtuellen Computer leiten. Dieser Artikel ist auch für [virtuelle Windows-Computer](../../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)verfügbar.

> [!IMPORTANT]
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager- und klassische Bereitstellung](../../../resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Im **Resource Manager**-Bereitstellungsmodell werden Endpunkte mithilfe von **Netzwerksicherheitsgruppen (NSGs)** konfiguriert. Weitere Informationen finden Sie unter [Öffnen von Ports und Endpunkten](../nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Wenn Sie einen virtuellen Linux-Computer im Azure-Portal erstellen, wird normalerweise automatisch ein Endpunkt für Secure Shell (SSH) erstellt. Sie können zusätzliche Endpunkte beim Erstellen des virtuellen Computers oder später nach Bedarf konfigurieren.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Nächste Schritte
* Sie können einen VM-Endpunkt auch über die [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)erstellen. Führen Sie den Befehl **azure vm endpoint create** aus.
* Wenn Sie einen virtuellen Computer im Resource Manager-Bereitstellungsmodell erstellt haben, können Sie die Azure-CLI im Resource Manager-Modus verwenden, um [Netzwerksicherheitsgruppen](../../../virtual-network/tutorial-filter-network-traffic-cli.md) zum Steuern des Datenverkehrs an den virtuellen Computer zu erstellen.
