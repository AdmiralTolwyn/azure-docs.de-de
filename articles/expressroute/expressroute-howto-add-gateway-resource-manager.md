---
title: 'Azure ExpressRoute: Hinzufügen eines Gateways zu einem VNET PowerShell'
description: In diesem Artikel wird beschrieben, wie Sie einem bereits erstellten Resource Manager-VNET für ExpressRoute ein VNET-Gateway hinzufügen.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 6e6d79afa1126acb26cb8856c39ba486cce31a5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037424"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Konfigurieren eines Gateways für ein virtuelles Netzwerk für ExpressRoute mit PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager – Azure-Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassisch – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure-Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

In diesem Artikel wird beschrieben, wie Sie ein virtuelles Netzwerkgateway für ein vorhandenes virtuelles Netzwerk (VNet) hinzufügen und entfernen bzw. dessen Größe ändern. Die Schritte für diese Konfiguration gelten für VNETs, die mit dem Resource Manager-Bereitstellungsmodell für eine ExpressRoute-Konfiguration erstellt wurden. Weitere Informationen finden Sie unter [Informationen zu Gateways für virtuelle Netzwerke für ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Vorbereitungen

### <a name="working-with-powershell"></a>Arbeiten mit PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Referenzliste für Konfiguration

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie das VNet-Gateway erstellt haben, können Sie Ihr VNet mit einer ExpressRoute-Verbindung verknüpfen. Weitere Informationen finden Sie unter [Verknüpfen eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md).
