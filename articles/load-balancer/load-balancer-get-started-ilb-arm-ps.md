---
title: Erstellen einer Azure-Instanz mit internem Lastenausgleich mit PowerShell | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie einen internen Lastenausgleich erstellen, indem Sie das Azure PowerShell-Modul mit Azure Resource Manager verwenden.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jennoc
tags: azure-resource-manager
ms.assetid: c6c98981-df9d-4dd7-a94b-cc7d1dc99369
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 54846ddc142a5bf7cd37c03fd9c069dd0c94897f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/29/2018
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-powershell-module"></a>Erstellen eines internen Lastenausgleichs mit dem Azure PowerShell-Modul

> [!div class="op_single_selector"]
> * [Azure-Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure-Befehlszeilenschnittstelle](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Vorlage](../load-balancer/load-balancer-get-started-ilb-arm-template.md)


[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="get-started-with-the-configuration"></a>Erste Schritte bei der Konfiguration

In diesem Artikel wird beschrieben, wie Sie einen internen Lastenausgleich erstellen, indem Sie Azure Resource Manager mit dem Azure PowerShell-Modul verwenden. Beim Resource Manager-Bereitstellungsmodell werden die Objekte, die zum Erstellen eines internen Lastenausgleichs benötigt werden, einzeln konfiguriert. Nachdem die Objekte erstellt und konfiguriert wurden, werden sie kombiniert, um einen Lastenausgleich zu erstellen.

Für die Bereitstellung eines Lastenausgleichs müssen die folgenden Objekte erstellt werden:

* Front-End-IP-Pool: Die private IP-Adresse für den gesamten eingehenden Netzwerkdatenverkehr.
* Back-End-Adresspool: Die Netzwerkschnittstellen für den Empfang des Datenverkehrs, für den der Lastenausgleich durchgeführt wurde, von der Front-End-IP-Adresse.
* Lastenausgleichsregeln: Die Portkonfiguration (Quelle und lokal) für den Lastenausgleich.
* Testkonfiguration: Die Tests des Integritätsstatus für virtuelle Computer.
* NAT-Eingangsregeln: Die Portregeln für den direkten Zugriff auf virtuelle Computer.

Weitere Informationen zu den Komponenten des Lastenausgleichs finden Sie unter [Azure Resource Manager-Unterstützung für Load Balancer](load-balancer-arm.md).

Die folgenden Schritte zeigen, wie Sie einen Load Balancer zwischen zwei virtuellen Computern konfigurieren.

## <a name="set-up-powershell-to-use-resource-manager"></a>Einrichten von PowerShell für die Verwendung des Resource Managers

Stellen Sie sicher, dass Sie über die aktuelle Produktionsversion des Azure PowerShell-Moduls verfügen. PowerShell muss für den Zugriff auf Ihr Azure-Abonnement richtig konfiguriert sein.

### <a name="step-1-start-powershell"></a>Schritt 1: Starten von PowerShell

Starten Sie das PowerShell-Modul für Azure Resource Manager.

```powershell
Login-AzureRmAccount
```

### <a name="step-2-view-your-subscriptions"></a>Schritt 2: Anzeigen Ihrer Abonnements

Prüfen Sie Ihre verfügbaren Azure-Abonnements.

```powershell
Get-AzureRmSubscription
```

Geben Sie Ihre Anmeldeinformationen ein, wenn Sie zur Authentifizierung aufgefordert werden.

### <a name="step-3-select-the-subscription-to-use"></a>Schritt 3: Auswählen des zu verwendenden Abonnements

Wählen Sie aus, welches Ihrer Azure-Abonnements Sie zum Bereitstellen des Lastenausgleichs verwenden möchten.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4-choose-the-resource-group-for-the-load-balancer"></a>Schritt 4: Auswählen der Ressourcengruppe für den Lastenausgleich

Erstellen Sie eine neue Ressourcengruppe für den Lastenausgleich. Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.

```powershell
New-AzureRmResourceGroup -Name NRP-RG -location "West US"
```

Der Azure Resource Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Der Standort wird als Standardstandort für alle Ressourcen der Ressourcengruppe verwendet. Verwenden Sie immer dieselbe Ressourcengruppe für alle Befehle, die sich auf die Erstellung des Lastenausgleichs beziehen.

Im Beispiel haben wir eine Ressourcengruppe mit dem Namen **NRP-RG** und dem Standort „USA, Westen“ erstellt.

## <a name="create-the-virtual-network-and-ip-address-for-the-front-end-ip-pool"></a>Erstellen des virtuellen Netzwerks und der IP-Adresse für den Front-End-IP-Pool

Erstellen Sie ein Subnetz für das virtuelle Netzwerk, und weisen Sie es der Variablen **$backendSubnet** zu.

```powershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Erstellen Sie ein virtuelles Netzwerk.

```powershell
$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

Das virtuelle Netzwerk wird erstellt. Das Subnetz **LB-Subnet-BE** wird dem virtuellen Netzwerk **NRPVNet** hinzugefügt. Diese Werte werden der Variablen **$vnet** zugewiesen.

## <a name="create-the-front-end-ip-pool-and-back-end-address-pool"></a>Erstellen des Front-End-IP-Pools und Back-End-Adresspools

Erstellen Sie einen Front-End-IP-Pool für den eingehenden Datenverkehr und einen Back-End-Adresspool für den Empfang des Datenverkehrs mit Lastenausgleich.

### <a name="step-1-create-a-front-end-ip-pool"></a>Schritt 1: Erstellen eines Front-End-IP-Pools

Erstellen Sie einen Front-End-IP-Pool mit der privaten IP-Adresse 10.0.2.5 für das Subnetz 10.0.2.0/24. Diese Adresse ist der Endpunkt für den eingehenden Netzwerkdatenverkehr.

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2-create-a-back-end-address-pool"></a>Schritt 2: Erstellen eines Back-End-Adresspools

Erstellen Sie einen Back-End-Adresspool für den Empfang des eingehenden Datenverkehrs vom Front-End-IP-Pool:

```powershell
$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-the-configuration-rules-probe-and-load-balancer"></a>Erstellen von Konfigurationsregeln, Test und Lastenausgleich

Nachdem der Front-End-IP-Pool und der Back-End-Adresspool erstellt wurden, können Sie die Regeln für die Lastenausgleichsressource angeben.

### <a name="step-1-create-the-configuration-rules"></a>Schritt 1: Erstellen der Konfigurationsregeln

Im Beispiel werden die folgenden vier Regelobjekte erstellt:

* Eine NAT-Eingangsregel für das Remotedesktopprotokoll (RDP): Leitet den gesamten eingehenden Datenverkehr von Port 3441 an Port 3389 um.
* Eine zweite NAT-Eingangsregel für RDP: Leitet den gesamten eingehenden Datenverkehr von Port 3442 an Port 3389 um.
* Eine Integritätstestregel: Überprüft den Integritätsstatus des Pfads von „HealthProbe.aspx“.
* Eine Lastenausgleichsregel: Führt den Lastenausgleich für den gesamten eingehenden Datenverkehr am öffentlichen Port 80 am lokalen Port 80 im Back-End-Adresspool durch.

```powershell
$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

### <a name="step-2-create-the-load-balancer"></a>Schritt 2: Erstellen des Lastenausgleichs

Erstellen Sie den Lastenausgleich, und kombinieren Sie die Regelobjekte (NAT-Eingang für RDP, Lastenausgleich und Integritätstest):

```powershell
$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-the-network-interfaces"></a>Erstellen der Netzwerkschnittstellen

Definieren Sie nach dem Erstellen des internen Lastenausgleichs, welche Netzwerkschnittstellen (NICs) den eingehenden Netzwerkdatenverkehr mit Lastenausgleich, die NAT-Regeln und die Tests empfangen. Jede Netzwerkschnittstelle wird individuell konfiguriert und später einem virtuellen Computer zugewiesen.

### <a name="step-1-create-the-first-network-interface"></a>Schritt 1: Erstellen der ersten Netzwerkschnittstelle

Rufen Sie das virtuelle Netzwerk der Ressource und das Subnetz ab. Diese Werte werden verwendet, um die Netzwerkschnittstellen zu erstellen:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

Erstellen Sie die erste Netzwerkschnittstelle mit dem Namen **lb-nic1-be**. Weisen Sie die Schnittstelle dem Back-End-Pool des Lastenausgleichs zu. Ordnen Sie die erste NAT-Regel für RDP dieser NIC zu:

```powershell
$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2-create-the-second-network-interface"></a>Schritt 2: Erstellen der zweiten Netzwerkschnittstelle

Erstellen Sie die zweite Netzwerkschnittstelle mit dem Namen **lb-nic2-be**. Weisen Sie die zweite Schnittstelle demselben Back-End-Pool des Lastenausgleichs zu, den Sie für die erste Schnittstelle verwendet haben. Weisen Sie die zweite NIC der zweiten NAT-Regel für RDP zu:

```powershell
$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

Überprüfen Sie die Konfiguration:

    $backendnic1

Die Einstellungen sollten wie folgt lauten:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3-assign-the-nic-to-a-vm"></a>Schritt 3: Zuweisen der NIC zu einer VM

Weisen Sie die NIC einem virtuellen Computer zu, indem Sie den Befehl `Add-AzureRmVMNetworkInterface` verwenden.

Eine Schritt-für-Schritt-Anleitung zum Erstellen eines virtuellen Computers und Zuweisen der NIC finden Sie unter [Erstellen eines virtuellen Windows-Computers mit PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface"></a>Hinzufügen der Netzwerkschnittstelle

Nachdem der virtuelle Computer erstellt wurde, können Sie die Netzwerkschnittstelle hinzufügen.

### <a name="step-1-store-the-load-balancer-resource"></a>Schritt 1: Speichern der Lastenausgleichsressource

Speichern Sie die Lastenausgleichsressource in einer Variablen (sofern nicht bereits geschehen). Wir verwenden den Variablennamen **$lb**. Verwenden Sie für die Attributwerte im Skript die Namen für die Lastenausgleichsressourcen, die in den vorherigen Schritten erstellt wurden.

```powershell
$lb = Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2-store-the-back-end-configuration"></a>Schritt 2: Speichern der Back-End-Konfiguration

Speichern Sie die Back-End-Konfiguration in der Variablen **$backend**.

```powershell
$backend = Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3-store-the-network-interface"></a>Schritt 3: Speichern der Netzwerkschnittstelle

Speichern Sie die Netzwerkschnittstelle in einer anderen Variablen. Diese Schnittstelle wurde unter „Erstellen der Netzwerkschnittstellen, Schritt 1“ erstellt. Wir verwenden den Variablennamen **$nic1**. Verwenden Sie den gleichen Netzwerkschnittstellennamen wie im vorherigen Beispiel.

```powershell
$nic = Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4-change-the-back-end-configuration"></a>Schritt 4: Ändern der Back-End-Konfiguration

Ändern Sie die Back-End-Konfiguration in der Netzwerkschnittstelle.

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5-save-the-network-interface-object"></a>Schritt 5: Speichern des Netzwerkschnittstellenobjekts

Speichern Sie das Netzwerkschnittstellenobjekt.

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Nachdem die Schnittstelle für den Back-End-Pool hinzugefügt wurde, wird für den Netzwerkdatenverkehr der Lastenausgleich gemäß den Regeln durchgeführt. Diese Regeln wurden unter „Erstellen von Konfigurationsregeln, Test und Lastenausgleich“ konfiguriert.

## <a name="update-an-existing-load-balancer"></a>Aktualisieren eines vorhandenen Load Balancers

### <a name="step-1-assign-the-load-balancer-object-to-a-variable"></a>Schritt 1: Zuweisen des Lastenausgleichsobjekts zu einer Variablen

Weisen Sie das Lastenausgleichsobjekt (aus dem vorherigen Beispiel) der Variablen **$slb** zu, indem Sie den Befehl `Get-AzureRmLoadBalancer` verwenden:

```powershell
$slb = Get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

### <a name="step-2-add-a-nat-rule"></a>Schritt 2: Hinzufügen einer NAT-Regel

Fügen Sie einem vorhandenen Lastenausgleich eine neue NAT-Eingangsregel hinzu. Verwenden Sie Port 81 für den Front-End-Pool und Port 8181 für den Back-End-Pool:

```powershell
$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3-save-the-configuration"></a>Schritt 3: Speichern der Konfiguration

Speichern Sie die neue Konfiguration mit dem Befehl `Set-AzureLoadBalancer`:

```powershell
$slb | Set-AzureRmLoadBalancer
```

## <a name="remove-an-existing-load-balancer"></a>Entfernen eines vorhandenen Lastenausgleichs

Löschen Sie den Lastenausgleich **NRP-LB** in der Ressourcengruppe **NRP-RG** mit dem Befehl `Remove-AzureRmLoadBalancer`:

```powershell
Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Verwenden Sie den optionalen Switch **-Force**, um das Anzeigen der Bestätigungsaufforderung für den Löschvorgang zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren des Verteilungsmodus für den Lastenausgleich](load-balancer-distribution-mode.md)
* [Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)
