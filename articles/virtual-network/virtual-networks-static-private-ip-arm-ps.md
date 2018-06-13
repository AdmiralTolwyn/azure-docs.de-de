---
title: Konfigurieren von privaten IP-Adressen für virtuelle Computer – Azure PowerShell | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie private IP-Adressen für virtuelle Computer mithilfe von PowerShell konfigurieren.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b0e8153f1d0cecd4efe66dc7cce64addd6ed62aa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31424056"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-powershell"></a>Konfigurieren von privaten IP-Adressen für einen virtuellen Computer mithilfe von PowerShell

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

Azure verfügt über zwei Bereitstellungsmodelle: Azure Resource Manager und klassisch. Microsoft empfiehlt, Ressourcen mit dem Resource Manager-Bereitstellungsmodell zu erstellen. Weitere Informationen zu den Unterschieden zwischen den beiden Modellen finden Sie im Artikel zum Thema [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md) (Grundlegendes zu Azure-Bereitstellungsmodellen). Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell. Sie können [eine statische private IP-Adresse auch im klassischen Bereitstellungsmodell verwalten](virtual-networks-static-private-ip-classic-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Die folgenden Beispielbefehle für PowerShell setzen voraus, dass bereits eine einfache Umgebung erstellt wurde, die auf dem zuvor beschriebenen Szenario basiert. Wenn Sie die in diesem Dokument aufgeführten Befehle ohne Veränderungen ausführen möchten, erstellen Sie zunächst eine Testumgebung, wie unter [Erstellen eines virtuellen Netzwerks](quick-create-powershell.md) beschrieben.

## <a name="create-a-vm-with-a-static-private-ip-address"></a>Erstellen eines virtuellen Computers mit einer statischen privaten IP-Adresse
Erstellen Sie in einem VNet mit dem Namen *TestVNet* im Subnetz *FrontEnd* einen virtuellen Computer mit dem Namen *DNS01* und der statischen privaten IP-Adresse *192.168.1.101*. Gehen Sie dabei wie folgt vor:

1. Legen Sie die Variablen für das Speicherkonto, den Speicherort, die Ressourcengruppe und die zu verwendenden Anmeldeinformationen fest. Sie müssen einen Benutzernamen und ein Kennwort für den virtuellen Computer eingeben. Die Speichergruppe und die Ressourcengruppe müssen bereits vorhanden sein.

    ```powershell
    $stName  = "vnetstorage"
    $locName = "Central US"
    $rgName  = "TestRG"
    $cred    = Get-Credential -Message "Type the name and password of the local administrator account."
    ```

2. Rufen Sie das virtuelle Netzwerk und das Subnetz an, in dem der virtuelle Computer erstellt werden soll.

    ```powershell
    $vnet   = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    $subnet = $vnet.Subnets[0].Id
    ```

3. Erstellen Sie, sofern erforderlich, eine öffentliche IP-Adresse für den Zugriff auf den virtuellen Computer aus dem Internet.

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name TestPIP -ResourceGroupName $rgName `
    -Location $locName -AllocationMethod Dynamic
    ```

4. Erstellen Sie eine Netzwerkkarte, und verwenden Sie dabei die statische private IP-Adresse, die Sie dem virtuellen Computer zuweisen möchten. Stellen Sie sicher, dass die IP-Adresse innerhalb des Subnetzbereichs liegt, zu dem Sie den virtuellen Computer hinzufügen. Dies ist der wesentliche Schritt für diesen Artikel, bei dem Sie die private IP-Adresse als statische IP-Adresse festlegen.

    ```powershell
    $nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName `
    -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id `
    -PrivateIpAddress 192.168.1.101
    ```

5. Erstellen der VM mit der Netzwerkkarte:

    ```powershell
    $vm = New-AzureRmVMConfig -VMName DNS01 -VMSize "Standard_A1"
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName DNS01 `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri `
    -CreateOption fromImage
    New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm 
    ```

Es wird davon abgeraten, die private IP-Adresse, die dem virtuellen Azure-Computer innerhalb des Betriebssystems einer VM zugewiesen ist, statisch zuzuweisen, sofern dies nicht erforderlich ist (z.B. beim [Zuweisen mehrerer IP-Adressen zu einer Windows-VM](virtual-network-multiple-ip-addresses-powershell.md)). Wenn Sie die private IP-Adresse innerhalb des Betriebssystems manuell festlegen, sollten Sie sicherstellen, dass es sich um dieselbe Adresse wie die private IP-Adresse handelt, die der Azure-[Netzwerkschnittstelle](virtual-network-network-interface-addresses.md#change-ip-address-settings) zugewiesen ist. Andernfalls kann die Konnektivität mit dem virtuellen Computer verloren gehen. Erfahren Sie mehr über Einstellungen für [private IP-Adressen](virtual-network-network-interface-addresses.md#private). Die öffentliche IP-Adresse sollte niemals manuell einem virtuellen Azure-Computer im Betriebssystem des virtuellen Computers zugewiesen werden.

## <a name="retrieve-static-private-ip-address-information-for-a-network-interface"></a>Abrufen der Informationen zur statischen privaten IP-Adresse für eine Netzwerkschnittstelle
Führen Sie den folgenden PowerShell-Befehl aus, und sehen Sie sich die Werte für *PrivateIpAddress* und *PrivateIpAllocationMethod* an, um Informationen zur statischen privaten IP-Adresse des virtuellen Computers zu erhalten, der mit dem obigen Skript erstellt wurde:

```powershell
Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
```

Erwartete Ausgabe:

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    Etag                 : W/"[Id]"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"[Id]\"",
                               "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Static",
                               "Subnet": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="remove-a-static-private-ip-address-from-a-network-interface"></a>Entfernen einer statischen privaten IP-Adresse von einer Netzwerkschnittstelle
Führen Sie die folgenden PowerShell-Befehle aus, um die statische private IP-Adresse zu entfernen, die dem virtuellen Computer im obigen Skript hinzugefügt wurde:

```powershell
$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Dynamic"
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Erwartete Ausgabe:

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    Etag                 : W/"[Id]"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WindowsVM"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"[Id]\"",
                               "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Dynamic",
                               "Subnet": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="add-a-static-private-ip-address-to-a-network-interface"></a>Hinzufügen einer statischen privaten IP-Adresse zu einer Netzwerkschnittstelle
Führen Sie folgenden Befehl aus, um dem virtuellen Computer, der mit dem obigen Skript erstellt wurde, eine statische private IP-Adresse hinzuzufügen:

```powershell
$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Static"
$nic.IpConfigurations[0].PrivateIpAddress = "192.168.1.101"
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Es wird davon abgeraten, die private IP-Adresse, die dem virtuellen Azure-Computer innerhalb des Betriebssystems einer VM zugewiesen ist, statisch zuzuweisen, sofern dies nicht erforderlich ist (z.B. beim [Zuweisen mehrerer IP-Adressen zu einer Windows-VM](virtual-network-multiple-ip-addresses-powershell.md)). Wenn Sie die private IP-Adresse innerhalb des Betriebssystems manuell festlegen, sollten Sie sicherstellen, dass es sich um dieselbe Adresse wie die private IP-Adresse handelt, die der Azure-[Netzwerkschnittstelle](virtual-network-network-interface-addresses.md#change-ip-address-settings) zugewiesen ist. Andernfalls kann die Konnektivität mit dem virtuellen Computer verloren gehen. Erfahren Sie mehr über Einstellungen für [private IP-Adressen](virtual-network-network-interface-addresses.md#private). Die öffentliche IP-Adresse sollte niemals manuell einem virtuellen Azure-Computer im Betriebssystem des virtuellen Computers zugewiesen werden.

## <a name="change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>Ändern der Zuordnungsmethode für eine private IP-Adresse, die einer Netzwerkschnittstelle zugewiesen ist

Eine private IP-Adresse wird mithilfe der statischen oder der dynamischen Zuordnungsmethode zu einer Netzwerkwerkkarte zugewiesen. Dynamische IP-Adressen können sich nach dem Starten eines virtuellen Computers ändern, der zuvor beendet (freigegeben) war. Dies kann zu Problemen führen, wenn der virtuelle Computer einen Dienst hostet, der nach dem Neustarten des Computers aus dem beendeten (freigegebenen) Zustand weiterhin die gleiche IP-Adresse benötigt. Statische IP-Adressen werden beibehalten, bis der virtuelle Computer gelöscht wird. Um die Zuordnungsmethode einer IP-Adresse zu ändern, führen Sie das folgende Skript aus, das die Zuordnungsmethode von „dynamisch“ zu „statisch“ ändert. Wenn für die aktuelle private IP-Adresse die statische Zuordnungsmethode festgelegt ist, ändern Sie vor dem Ausführen des Skripts den Eintrag *Static* zu *Dynamic*.

```powershell
$RG = "TestRG"
$NIC_name = "testnic1"

$nic = Get-AzureRmNetworkInterface -ResourceGroupName $RG -Name $NIC_name
$nic.IpConfigurations[0].PrivateIpAllocationMethod = 'Static'
Set-AzureRmNetworkInterface -NetworkInterface $nic 
$IP = $nic.IpConfigurations[0].PrivateIpAddress

Write-Host "The allocation method is now set to"$nic.IpConfigurations[0].PrivateIpAllocationMethod"for the IP address" $IP"." -NoNewline
```

Wenn Sie den Namen der Netzwerkkarte nicht wissen, können Sie mithilfe des folgenden Befehls eine Liste der Netzwerkkarten in einer Ressourcengruppe anzeigen:

```powershell
Get-AzureRmNetworkInterface -ResourceGroupName $RG | Where-Object {$_.ProvisioningState -eq 'Succeeded'} 
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Verwaltung von [privaten IP-Adressen](virtual-network-network-interface-addresses.md).