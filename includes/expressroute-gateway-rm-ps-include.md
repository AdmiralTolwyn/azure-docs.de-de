---
title: Includedatei
description: Includedatei
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/22/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 7e33d4ed7100287ef6b22aa4c90fd52671363902
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/19/2018
---
Bei den Schritten für diese Aufgabe wird ein VNet basierend auf den Werten verwendet, die in der folgenden Referenzliste für die Konfiguration enthalten sind. Zusätzliche Einstellungen und Namen werden ebenfalls in dieser Liste beschrieben. Wir verwenden diese Liste nicht direkt in einem der Schritte, obwohl wir Variablen basierend auf den Werten in dieser Liste hinzufügen. Sie können diese Liste als Referenz verwenden und die Werte durch Ihre eigenen Werte ersetzen.

**Referenzliste für Konfiguration**

* Name des virtuellen Netzwerks = TestVNet
* Adressraum des virtuellen Netzwerks: 192.168.0.0/16
* Ressourcengruppe = TestRG
* Name Subnet1 = FrontEnd 
* Subnet1-Adressraum = "192.168.1.0/24"
* Name des Gatewaysubnetzes: GatewaySubnet. Sie müssen ein Gatewaysubnetz immer *GatewaySubnet* nennen.
* Adressraum des Gatewaysubnetzes = 192.168.200.0/26
* Region = USA, Osten
* Name des Gateways = GW
* Name der Gateway-IP = GWIP
* Name der Gateway-IP-Konfiguration = gwipconf
* Typ = ExpressRoute. Dieser Typ ist für eine ExpressRoute-Konfiguration erforderlich.
* Name der öffentlichen Gateway-IP = gwpip

## <a name="add-a-gateway"></a>Hinzufügen eines Gateways
1. Stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her.

  ```powershell 
  Connect-AzureRmAccount
  Get-AzureRmSubscription 
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Definieren Sie Ihre Variablen für diese Übung. Achten Sie darauf, das Beispiel so zu bearbeiten, dass es den gewünschten Einstellungen entspricht.

  ```powershell 
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "GW"
  $GWIPName = "GWIP"
  $GWIPconfName = "gwipconf"
  $VNetName = "TestVNet"
  ```
3. Speichern Sie das virtuelle Netzwerkobjekt als Variable.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  ```
4. Fügen Sie Ihrem virtuellen Netzwerk ein Gatewaysubnetz hinzu. Das Gatewaysubnetz muss den Namen GatewaySubnet tragen. Sie sollten ein Gatewaysubnetz erstellen, das die Größe /27 oder höher hat (/26, /25 usw.).

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
  ```
5. Legen Sie die Konfiguration fest.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. Speichern Sie das Gatewaysubnetz als Variable.

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
  ```
7. Fordern Sie eine öffentliche IP-Adresse an. Die IP-Adresse wird angefordert, bevor das Gateway erstellt wird. Sie können die IP-Adresse, die Sie verwenden möchten, nicht selbst angeben. Sie wird dynamisch zugewiesen. Diese IP-Adresse wird im nächsten Konfigurationsabschnitt verwendet. Die AllocationMethod muss dynamisch sein.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  ```
8. Erstellen Sie die Konfiguration für Ihr Gateway. Die Gatewaykonfiguration definiert das zu verwendende Subnetz und die zu verwendende öffentliche IP-Adresse. In diesem Schritt geben Sie die Konfiguration an, die beim Erstellen des Gateways verwendet wird. Dieser Schritt erstellt das Gatewayobjekt nicht tatsächlich. Verwenden Sie das folgende Beispiel, um Ihre Gatewaykonfiguration zu erstellen.

  ```powershell
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```
9. Erstellen Sie das Gateway. In diesem Schritt ist **-GatewayType** besonders wichtig. Sie müssen den Wert **ExpressRoute**verwenden. Das Erstellen des Gateways kann nach der Ausführung dieser Cmdlets 45 Minuten oder länger dauern.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
  ```

## <a name="verify-the-gateway-was-created"></a>Erstellung des Gateways überprüfen
Verwenden Sie die folgenden Befehle, um zu überprüfen, ob das Gateway erstellt wurde:

```powershell
Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Ändern der Größe eines Gateways
Es gibt eine Reihe von [Gateway-SKUs](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Sie können jederzeit den folgenden Befehl verwenden, um die Gateway-SKU zu ändern.

> [!IMPORTANT]
> Dieser Befehl kann nicht für das UltraPerformance-Gateway verwendet werden. Wenn Sie das Gateway in ein UltraPerformance-Gateway ändern möchten, entfernen Sie zunächst das vorhandene ExpressRoute-Gateway, und erstellen Sie anschließend ein neues UltraPerformance-Gateway. Wenn Sie das Gateway von einem UltraPerformance-Gateway herabstufen möchten, entfernen Sie zunächst das UltraPerformance-Gateway, und erstellen Sie anschließend ein neues Gateway.
> 
> 

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Entfernen eines Gateways
Verwenden Sie den folgenden Befehl zum Entfernen eines Gateways:

```powershell
Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
