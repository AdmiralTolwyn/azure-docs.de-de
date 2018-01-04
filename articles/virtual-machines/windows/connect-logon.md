---
title: Herstellen einer Verbindung mit einem virtuellen Windows Server-Computer | Microsoft Docs
description: Hier erfahren Sie, wie Sie unter Verwendung des Azure-Portals und des Resource Manager-Bereitstellungsmodells eine Verbindung mit einem virtuellen Windows-Computer herstellen und sich bei diesem Computer anmelden.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: cynthn
ms.openlocfilehash: 3e7b7c2ffa7471b96ebd23ac430fbd21eb21e9c5
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/03/2018
---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Gewusst wie: Herstellen einer Verbindung mit einem virtuellen Azure-Computer unter Windows und Anmelden auf diesem Computer
Verwenden Sie die Schaltfläche **Verbinden** im Azure-Portal, um eine Remotedesktopsitzung (RDP) von einem Windows-Desktop zu starten. Zuerst stellen Sie eine Verbindung mit dem virtuellen Computer her, und dann melden Sie sich an.

Wenn Sie von einem Mac eine Verbindung mit einem virtuellen Windows-Computer herstellen möchten, müssen Sie einen RDP-Client für Mac installieren, etwa [Microsoft-Remotedesktop](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Herstellen einer Verbindung mit dem virtuellen Computer
1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/)an, falls Sie dies noch nicht getan haben.
2. Klicken Sie im linken Menü auf **Virtual Machines**.
3. Wählen Sie den gewünschten virtuellen Computer aus der Liste aus.
4. Klicken Sie auf der Seite für den virtuellen Computer auf **Verbinden**.
   
    ![Screenshot des Azure-Portals beim Herstellen einer Verbindung mit Ihrem virtuellen Computer](./media/connect-logon/connect.png)
   
   > [!TIP]
   > Wenn die Schaltfläche **Verbinden** im Portal ausgeblendet ist und keine [ExpressRoute](../../expressroute/expressroute-introduction.md)- oder [Site-to-Site-VPN-Verbindung](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) mit Azure besteht, müssen Sie eine öffentliche IP-Adresse erstellen und dem virtuellen Computer zuweisen, damit Sie RDP nutzen können. Lesen Sie mehr über [öffentliche IP-Adressen in Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="log-on-to-the-virtual-machine"></a>Anmelden beim virtuellen Computer
[!INCLUDE [virtual-machines-log-on-win-server](../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Nächste Schritte
Falls beim Herstellen der Verbindung Probleme auftreten, helfen Ihnen die Informationen unter [Problembehandlung bei Remotedesktopverbindungen](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)weiter. Dieser Artikel führt Sie durch die Diagnose und Behebung von häufigen Problemen.

