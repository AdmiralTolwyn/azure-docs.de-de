---
title: "Erstellen einer Azure-Instanz mit internem Lastenausgleich – PowerShell | Microsoft-Dokumentation"
description: Hier erfahren Sie, wie Sie im Ressourcen-Manager mithilfe von PowerShell einen internen Lastenausgleich erstellen.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: c6c98981-df9d-4dd7-a94b-cc7d1dc99369
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8feb3b5f9dddc7b54b9c5e733675c2a9aca2f223
ms.contentlocale: de-de
ms.lasthandoff: 09/25/2017

---

# <a name="create-an-internal-load-balancer-using-powershell"></a>Erstellen eines internen Lastenausgleichs mit PowerShell

> [!div class="op_single_selector"]
> * [Azure-Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure-Befehlszeilenschnittstelle](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Vorlage](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager-Bereitstellung und klassische Bereitstellung](../azure-resource-manager/resource-manager-deployment-model.md).  Dieser Artikel befasst sich mit dem Resource Manager-Bereitstellungsmodell, das von Microsoft für die meisten neuen Bereitstellungen anstatt des [klassischen Bereitstellungsmodells](load-balancer-get-started-ilb-classic-ps.md) empfohlen wird.

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

Die folgenden Schritte zeigen, wie Sie einen internen Load Balancer mit dem Azure Resource Manager und PowerShell erstellen. Beim Azure Resource Manager werden die Elemente, mit denen ein interner Load Balancer erstellt wird, einzeln konfiguriert und dann zusammengeführt, um einen Lastenausgleich zu erstellen.

Erstellen und konfigurieren Sie die folgenden Objekte, um einen Lastenausgleich bereitzustellen:

* Front-End-IP-Konfiguration – Dient zum Konfigurieren der privaten IP-Adresse für eingehenden Netzwerkdatenverkehr.
* Back-End-Adresspool – Dient zum Konfigurieren der Netzwerkschnittstellen, die den vom Lastenausgleich verteilten Datenverkehr vom Front-End-IP-Adresspool empfangen.
* Lastenausgleichsregeln – Quell- und lokale Portkonfiguration für den Lastenausgleich.
* Tests – Dient zum Konfigurieren des Integritätsstatustests für die VM-Instanzen.
* Eingehende NAT-Regeln – Dient zum Konfigurieren der Portregeln, um direkt auf eine der VM-Instanzen zuzugreifen.

Unter [Unterstützung von Azure Resource Manager für den Load Balancer](load-balancer-arm.md) erhalten Sie weitere Informationen zu Load Balancer-Komponenten von Azure Resource Manager.

Die folgenden Schritte zeigen, wie Sie einen Load Balancer zwischen zwei virtuellen Computern konfigurieren.

## <a name="set-up-powershell-to-use-resource-manager"></a>Einrichten von PowerShell für die Verwendung des Resource Managers

Stellen Sie sicher, dass Sie die neueste Produktionsversion des Azure-Moduls für PowerShell verwenden und PowerShell richtig für den Zugriff auf Ihr Azure-Abonnement eingerichtet wurde.

### <a name="step-1"></a>Schritt 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Schritt 2

Überprüfen Sie die Abonnements für das Konto.

```powershell
Get-AzureRmSubscription
```

Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert.

### <a name="step-3"></a>Schritt 3

Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="create-resource-group-for-load-balancer"></a>Erstellen einer Ressourcengruppe für den Load Balancer

Erstellen Sie eine neue Ressourcengruppe. (Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.)

```powershell
New-AzureRmResourceGroup -Name NRP-RG -location "West US"
```

Der Azure Resource Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser Speicherort wird als Standardspeicherort für die Ressourcen dieser Ressourcengruppe verwendet. Stellen Sie sicher, dass für alle Befehle, mit denen ein Lastenausgleich erstellt wird, die gleiche Ressourcengruppe verwendet wird.

Im obigen Beispiel haben wir eine Ressourcengruppe mit dem Namen **NRP-RG** und dem Standort **USA, Westen** erstellt.

## <a name="create-a-virtual-network-and-a-private-ip-address-for-a-front-end-ip-pool"></a>Erstellen eines virtuellen Netzwerks und einer privaten IP-Adresse für den Front-End-IP-Pool

Erstellt ein Subnetz für das virtuelle Netzwerk und weist es der Variablen "$backendSubnet" zu

```powershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Erstellen Sie ein virtuelles Netzwerk:

```powershell
$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

Erstellt das virtuelle Netzwerk, fügt dem virtuellen Netzwerk "NRPVNet" das Subnetz "lb-subnet-be" hinzu und weist es der Variablen "$vnet" zu

## <a name="create-a-front-end-ip-pool-and-back-end-address-pool"></a>Erstellen eines Front-End-IP-Adresspools und eines Back-End-Adresspools

Richten Sie einen Front-End-IP-Pool für den eingehenden Netzwerkverkehr des Lastenausgleichs und des Back-End-Adresspools ein, um den Lastenausgleichsverkehr zu empfangen.

### <a name="step-1"></a>Schritt 1

Erstellen Sie einen Front-End-IP-Adresspool mit der privaten IP-Adresse 10.0.2.5 für das Subnetz 10.0.2.0/24, das als Endpunkt für eingehenden Netzwerkdatenverkehr dient.

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2"></a>Schritt 2

Richten Sie einen Back-End-Adresspool für den Empfang des eingehenden Datenverkehrs vom Front-End-IP-Pool ein:

```powershell
$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-load-balancing-rules-nat-rules-probe-and-load-balancer"></a>Erstellen von Lastenausgleichsregeln, NAT-Regeln, Tests und des Lastenausgleichs

Nachdem Sie den Front-End-IP-Pool und den Back-End-Adresspool erstellt haben, können Sie die Regeln für die Lastenausgleichsressource erstellen:

### <a name="step-1"></a>Schritt 1

```powershell
$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

Im obigen Beispiel werden die folgenden Elemente erstellt:

* NAT-Regel, bei der der gesamte eingehende Datenverkehr an Port 3441 an Port 3389 gesendet wird.
* Eine zweite NAT-Regel, bei der der gesamte eingehende Datenverkehr an Port 3442 an Port 3389 gesendet wird.
* Eine Lastenausgleichsregel, bei der ein Lastenausgleich des gesamten eingehenden Datenverkehrs beim öffentlichen Port 80 an den lokalen Port 80 im Back-End-Adresspool durchgeführt wird.
* Eine Testregel, die den Integritätsstatus für den Pfad „HealthProbe.aspx“ überprüft.

### <a name="step-2"></a>Schritt 2

Erstellen Sie den Load Balancer, indem Sie alle Objekte (NAT-Regeln, Load-Balancer-Regeln, Testkonfigurationen) zusammenfügen:

```powershell
$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-network-interfaces"></a>Erstellen von Netzwerkschnittstellen

Nach dem Erstellen des internen Lastenausgleichs müssen Sie definieren, welche Netzwerkschnittstellen den eingehenden Netzwerkdatenverkehr des Lastenausgleichs, die NAT-Regeln und die Tests empfangen können. Die Netzwerkschnittstelle wird in diesem Fall einzeln konfiguriert und kann später einem virtuellen Computer zugewiesen werden.

### <a name="step-1"></a>Schritt 1

Rufen Sie das virtuelle Netzwerk der Ressource und das Subnetz ab, um Netzwerkschnittstellen zu erstellen:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

In diesem Schritt wird eine Netzwerkschnittstelle erstellt, die dem Back-End-Adresspool des Lastenausgleichs angehört, und die erste NAT-Regel für RDP wird dieser Netzwerkschnittstelle zugeordnet:

```powershell
$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2"></a>Schritt 2

Erstellen Sie eine zweite Netzwerkschnittstelle namens "LB-Nic2-BE":

In diesem Schritt wird eine zweite Netzwerkschnittstelle erstellt und dem gleichen Back-End-Adresspool des Lastenausgleichs zugewiesen, und es wird die zweite NAT-Regel zugeordnet, die für RDP erstellt wurde:

```powershell
$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

Als Endergebnis wird die folgende Ausgabe angezeigt:

    $backendnic1

Erwartete Ausgabe:

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



### <a name="step-3"></a>Schritt 3

Verwenden Sie den Befehl Add-AzureRmVMNetworkInterface, um die NIC einem virtuellen Computer zuzuweisen.

Eine Schritt-für-Schritt-Anleitung zum Erstellen eines virtuellen Computers und zum Zuweisen zu einer NIC finden Sie in der folgenden Dokumentation: [Erstellen einer Azure-VM mithilfe von PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface"></a>Hinzufügen der Netzwerkschnittstelle

Falls Sie bereits einen virtuellen Computer erstellt haben, können Sie die Netzwerkschnittstelle mit den folgenden Schritten hinzufügen:

### <a name="step-1"></a>Schritt 1

Laden Sie die Lastenausgleichsressource in eine Variable (sofern nicht schon geschehen). Die verwendete Variable heißt „$lb“ und verwendet die Namen aus der Lastenausgleichsressource in den obigen Schritten.

```powershell
$lb = Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2"></a>Schritt 2

Laden Sie die Back-End-Konfiguration in eine Variable.

```powershell
$backend = Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3"></a>Schritt 3

Laden Sie die bereits erstellte Netzwerkschnittstelle in eine Variable. Der Variablenname lautet „$nic“. Als Name der Netzwerkschnittstelle wird der Name aus dem obigen Beispiel verwendet.

```powershell
$nic = Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4"></a>Schritt 4

Ändern Sie die Back-End-Konfiguration in der Netzwerkschnittstelle.

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5"></a>Schritt 5

Speichern Sie das Netzwerkschnittstellenobjekt.

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Nachdem eine Netzwerkschnittstelle zum Back-End-Pool des Lastenausgleichs hinzugefügt wurde, empfängt sie Netzwerkdatenverkehr basierend auf den Lastenausgleichsregeln für diese Lastenausgleichsressouce.

## <a name="update-an-existing-load-balancer"></a>Aktualisieren eines vorhandenen Load Balancers

### <a name="step-1"></a>Schritt 1
Weisen Sie der Variablen „$slb“ unter Verwendung des Lastenausgleichs aus dem obigen Beispiel mithilfe von „Get-AzureRmLoadBalancer“ ein Lastenausgleichsobjekt zu.

```powershell
$slb = Get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

### <a name="step-2"></a>Schritt 2

Im folgenden Beispiel fügen Sie einem vorhandenen Lastenausgleich eine neue eingehende NAT-Regel hinzu, die Port 81 in der Front-End-Anwendung und Port 8181 für den Back-End-Pool verwendet.

```powershell
$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3"></a>Schritt 3

Speichern Sie die neue Konfiguration mithilfe von „Set-AzureLoadBalancer“.

```powershell
$slb | Set-AzureRmLoadBalancer
```

## <a name="remove-a-load-balancer"></a>Entfernen eines Load Balancers

Verwenden Sie den Befehl „Remove-AzureRmLoadBalancer“, um den zuvor erstellten Load Balancer namens „NRP-LB“ in der Ressourcengruppe „NRP-RG“ zu löschen.

```powershell
Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Sie können den optionalen Switch „-Force“ verwenden, um die Aufforderung zum Löschen zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)

