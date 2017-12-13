---
title: Erstellen einer VM auf der Grundlage eines verwalteten Images in Azure | Microsoft-Dokumentation
description: Erstellen Sie einen virtuellen Windows-Computer auf der Grundlage eines generalisierten verwalteten Images mit Azure PowerShell oder dem Azure-Portal im Resource Manager-Bereitstellungsmodell.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cynthn
ms.openlocfilehash: d8986c71fd0300af385750af898d620e6d3e1f24
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-vm-from-a-managed-image"></a>Erstellen eines virtuellen Computers aus einem verwalteten Image

Sie können mehrere virtuelle Computer mit PowerShell oder dem Azure-Portal aus einem verwalteten VM-Image erstellen. Ein verwaltetes VM-Image enthält die erforderlichen Informationen zum Erstellen eines virtuellen Computers, einschließlich der Datenträger für Betriebssystem und Daten. Die VHDs, aus denen das Image besteht, einschließlich der Betriebssystem-Datenträger und sonstiger Datenträger, werden als verwaltete Datenträger gespeichert. 

Sie müssen bereits [ein verwaltetes VM-Image erstellt](capture-image-resource.md) haben, das zum Erstellen des neuen virtuellen Computers verwendet werden soll. 

## <a name="use-the-portal"></a>Verwenden des Portals

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Wählen Sie im linken Menü die Option **Alle Ressourcen** aus. Sie können die Ressourcen nach **Typ** sortieren, um Ihre Images leicht zu finden.
3. Wählen Sie das gewünschte Image aus der Liste aus. Die Seite **Übersicht** für Images wird geöffnet.
4. Klicken Sie im Menü auf **+ VM erstellen**.
5. Geben Sie die Informationen zum virtuellen Computer ein. Der hier eingegebene Benutzername und das Kennwort werden zum Anmelden am virtuellen Computer verwendet. Klicken Sie zum Abschluss auf **OK**. Sie können die neue VM in einer bestehenden Ressourcengruppe erstellen oder **Neu erstellen** auswählen, um eine neue Ressourcengruppe zum Speichern der VM zu erstellen.
6. Wählen Sie eine Größe für den virtuellen Computer. Wählen Sie die Option **Alle anzeigen**, oder ändern Sie den Filter **Supported disk type** (Unterstützter Datenträgertyp), um weitere Größen anzuzeigen. 
7. Nehmen Sie unter **Einstellungen** Änderungen nach Bedarf vor, und klicken Sie auf **OK**. 
8. Auf der Seite „Zusammenfassung“ sollte Ihr Imagename für **Privates Image** aufgelistet werden. Klicken Sie auf **Ok**, um die Bereitstellung des virtuellen Computers zu starten.


## <a name="use-powershell"></a>Verwenden von PowerShell

### <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie die neueste Version des AzureRM.Compute- und des AzureRM.Network PowerShell-Moduls verwenden. Öffnen Sie eine PowerShell-Eingabeaufforderung als Administrator, und führen Sie zur Installation den folgenden Befehl aus:

```azurepowershell-interactive
Install-Module AzureRM.Compute,AzureRM.Network
```
Weitere Informationen finden Sie unter [Azure PowerShell-Versionsverwaltung](/powershell/azure/overview).



### <a name="collect-information-about-the-image"></a>Sammeln von Informationen zu dem Image

Zunächst müssen wir grundlegende Informationen zum Image erfassen und eine Variable für das Image erstellen. In diesem Beispiel wird ein verwaltetes VM-Image mit dem Namen **myImage** verwendet, das sich in der Ressourcengruppe **myResourceGroup** in der Region **USA, Westen-Mitte** befindet. 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks
Erstellen Sie das VNet und das Subnetz des [virtuellen Netzwerks](../../virtual-network/virtual-networks-overview.md).

Erstellen Sie das Subnetz. Mit diesem Beispielbefehl wird ein Subnetz namens **mySubnet** mit dem Adresspräfix **10.0.0.0/24** erstellt.  
   
```azurepowershell-interactive
$subnetName = "mySubnet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name $subnetName -AddressPrefix 10.0.0.0/24
```

Erstellen des virtuellen Netzwerks Im folgenden Beispiel wird ein virtuelles Netzwerk namens **myVnet** mit dem Adresspräfix **10.0.0.0/16** erstellt.  
   
```azurepowershell-interactive
$vnetName = "myVnet"
$vnet = New-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AddressPrefix 10.0.0.0/16 `
    -Subnet $singleSubnet
```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Erstellen einer öffentlichen IP-Adresse und einer Netzwerkschnittstelle

Sie benötigen eine [öffentliche IP-Adresse](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) und eine Netzwerkschnittstelle, um die Kommunikation mit dem virtuellen Computer im virtuellen Netzwerk zu ermöglichen.

Erstellen einer öffentlichen IP-Adresse Dieses Beispiel erstellt eine öffentliche IP-Adresse mit dem Namen **myPip**. 
   
```azurepowershell-interactive
$ipName = "myPip"
$pip = New-AzureRmPublicIpAddress `
    -Name $ipName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod Dynamic
```
       
Erstellen der NIC Dieses Beispiel erstellt eine NIC mit dem Namen **myNic**. 
   
```azurepowershell-interactive
$nicName = "myNic"
$nic = New-AzureRmNetworkInterface `
    -Name $nicName `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id
```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Erstellen einer Netzwerksicherheitsgruppe und einer RDP-Regel

Damit Sie sich über RDP bei Ihrem virtuellen Computer anmelden können, benötigen Sie eine Netzwerksicherheitsregel (Network Security Rule, NSG), die RDP-Zugriff auf Port 3389 zulässt. 

Dieses Beispiel erstellt eine NSG namens **myNsg**, das eine Regel namens **myRdpRule** enthält, die RDP-Datenverkehr über Port 3389 zulässt. Weitere Informationen zu NSGs finden Sie unter [Öffnen von Ports für einen virtuellen Computer in Azure mithilfe von PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```azurepowershell-interactive
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Erstellen einer Variablen für das virtuelle Netzwerk

Erstellen einer Variablen für das abgeschlossene virtuelle Netzwerk 

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

### <a name="get-the-credentials-for-the-vm"></a>Abrufen der Anmeldeinformationen für die VM

Mit dem folgenden Cmdlet wird ein Fenster geöffnet, in dem Sie einen neuen Benutzernamen und ein Kennwort als lokales Administratorkonto für den Remotezugriff auf die VM eingeben. 

```azurepowershell-interactive
$cred = Get-Credential
```

### <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>Festlegen von Variablen für den VM-Namen, Computernamen und die VM-Größe

Erstellen Sie Variablen für den VM- und Computernamen. In diesem Beispiel wird der VM-Name als **myVM** und der Name des Computers als **myComputer** festgelegt.

```azurepowershell-interactive
$vmName = "myVM"
$computerName = "myComputer"
```

Legen Sie die Größe des virtuellen Computers fest. In diesem Beispiel wird eine VM der Größe **Standard_DS1_v2** erstellt. Weitere Informationen finden Sie in der Dokumentation zu den [VM-Größen](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/).

```azurepowershell-interactive
$vmSize = "Standard_DS1_v2"
```

Fügen Sie der VM-Konfiguration den VM-Namen und die VM-Größe hinzu.

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

### <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>Festlegen des VM-Images als Quellimage für die neue VM

Legen Sie das Quellimage mithilfe der ID des verwalteten VM-Images fest.

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

### <a name="set-the-os-configuration-and-add-the-nic"></a>Legen Sie die Betriebssystemkonfiguration fest, und fügen Sie die Netzwerkkarte hinzu.

Geben Sie den Speichertyp (PremiumLRS oder StandardLRS) und die Größe des Betriebssystem-Datenträgers ein. Bei diesem Beispiel wird der Kontotyp auf **PremiumLRS**, die Datenträgergröße auf **128GB** und das Datenträgercaching auf **ReadWrite** festgelegt.

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk -VM $vm  `
    -StorageAccountType PremiumLRS `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

### <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Erstellen Sie die neue VM mithilfe der Konfiguration, die Sie erstellt und in der Variablen **$vm** gespeichert haben.

```azurepowershell-interactive
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

### <a name="verify-that-the-vm-was-created"></a>Stellen Sie sicher, dass der virtuelle Computer erstellt wurde
Anschließend müsste der neu erstellte virtuelle Computer im [Azure-Portal](https://portal.azure.com) unter **Durchsuchen** > **Virtuelle Computer** angezeigt werden. Alternativ können Sie ihn auch mit den folgenden PowerShell-Befehlen anzeigen:

```azurepowershell-interactive
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Nächste Schritte
Informationen zum Verwalten Ihres neuen virtuellen Computers mit Azure PowerShell finden Sie unter [Erstellen und Verwalten von virtuellen Windows-Computern mit dem Azure PowerShell-Modul](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

