---
title: Erstellen eines virtuellen Windows-Computers mit PowerShell | Microsoft Docs
description: Erstellen Sie virtuelle Windows-Computer mit Azure PowerShell und dem klassischen Bereitstellungsmodell.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 42c0d4be-573c-45d1-b9b0-9327537702f7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 75c6cf17ee269ae169d9f2f748d0985ca07e454e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-with-powershell-and-the-classic-deployment-model"></a>Erstellen eines virtuellen Windows-Computers mit PowerShell und dem klassischen Bereitstellungsmodell
> [!div class="op_single_selector"]
> * [Azure-Portal – Windows](tutorial.md)
> * [PowerShell – Windows](create-powershell.md)
> 
> 

<br>

> [!IMPORTANT] 
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager- und klassische Bereitstellung](../../../resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells. Erfahren Sie, wie Sie [diese Schritte mit dem Resource Manager-Modell ausführen](../../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Diese Schritte zeigen, wie Sie eine Reihe von Azure PowerShell-Befehlen anpassen, mit denen ein Windows-basierter virtueller Azure-Computer mit einem Bausteinansatz erstellt und vorab konfiguriert wird. Sie können diesen Prozess verwenden, um schnell einen Befehlssatz für einen neuen Windows-basierten virtuellen Computer zu erstellen und eine vorhandene Bereitstellung zu erweitern oder mehrere Befehlssätze zu erstellen, die schnell eine benutzerdefinierte Entwicklungs-/Test- oder IT-Expertenumgebung erstellen.

Diese Schritte folgen einem lückenfüllenden Ansatz zur Erstellung von Azure PowerShell-Befehlssätzen. Dieser Ansatz kann hilfreich sein, wenn Sie noch nicht mit PowerShell gearbeitet haben oder einfach wissen möchten, welche Werte Sie für die erfolgreiche Konfiguration angeben müssen. Erweiterte PowerShell-Benutzer können die Befehle verwenden und sie durch eigene Werte für die Variablen ersetzen (Zeilen, die mit "$" beginnen).

Wenn Sie dies noch nicht getan haben, verwenden Sie die Anweisungen unter [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview) , um Azure PowerShell auf Ihrem lokalen Computer zu installieren. Öffnen Sie anschließend eine Windows PowerShell-Eingabeaufforderung.

## <a name="step-1-add-your-account"></a>Schritt 1: Hinzufügen Ihres Kontos
1. Geben Sie an der PowerShell-Eingabeaufforderung **Add-AzureAccount** ein, und drücken Sie die **EINGABETASTE**. 
2. Geben Sie die mit Ihrem Azure-Abonnement verknüpfte E-Mail-Adresse ein, und klicken Sie auf **Weiter**. 
3. Geben Sie das Kennwort für Ihr Konto ein. 
4. Klicken Sie auf **Anmelden**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Schritt 2: Festlegen Ihres Abonnements und Speicherkontos
Legen Sie Ihr Azure-Abonnement und -Speicherkonto fest, indem Sie diese Befehle in der Windows PowerShell-Eingabeaufforderung ausführen. Ersetzen Sie alles in den Anführungszeichen, einschließlich der Zeichen < und >, durch die korrekten Namen.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Sie erhalten den korrekten Abonnementnamen aus der Eigenschaft "SubscriptionName" der Ausgabe des Befehls **Get-AzureSubscription** . Sie erhalten den korrekten Speicherkontonamen aus der Eigenschaft „Label“ der Ausgabe des Befehls **Get-AzureStorageAccount**, nachdem Sie den Befehl **Select-AzureSubscription** ausgeführt haben.

## <a name="step-3-determine-the-imagefamily"></a>Schritt 3: Bestimmen der ImageFamily
Als Nächstes müssen Sie den Wert "ImageFamily" oder "Beschriftung" für das Image bestimmen, das dem virtuellen Computer in Azure entspricht, den Sie erstellen möchten. Sie können die Liste der verfügbaren ImageFamily-Werte mit diesem Befehl abrufen.

    Get-AzureVMImage | select ImageFamily -Unique

Hier finden Sie einige Beispiele für ImageFamily-Werte für Windows-basierte Computer:

* Windows Server 2012 R2 Datacenter
* Windows Server 2008 R2 SP1
* Windows Server 2016 Technical Preview 4
* SQL Server 2012 SP1 Enterprise unter Windows Server 2012

Wenn Sie das gesuchte Image gefunden haben, öffnen Sie eine neue Instanz des Texteditors Ihrer Wahl oder der PowerShell Integrated Scripting Environment (ISE). Kopieren Sie den folgenden Code in die neue Textdatei oder PowerShell ISE, wobei Sie den Wert von "ImageFamily" ersetzen.

    $family="<ImageFamily value>"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

In einigen Fällen befindet sich der Name des Images in der Eigenschaft "Beschriftung" anstelle des ImageFamily-Wertes. Wenn Sie das Image, das Sie mithilfe der Eigenschaft "ImageFamily" suchen, gefunden haben, listen Sie die Images nach der Eigenschaft "Beschriftung" mit dem folgenden Befehl auf.

    Get-AzureVMImage | select Label -Unique

Wenn Sie das richtige Image mit diesem Befehl gefunden haben, öffnen Sie eine neue Instanz des Texteditors Ihrer Wahl oder der PowerShell ISE. Kopieren Sie den folgenden Code in die neue Textdatei oder PowerShell ISE, wobei Sie den Wert von "Label" ersetzen.

    $label="<Label value>"
    $image = Get-AzureVMImage | where { $_.Label -eq $label } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## <a name="step-4-build-your-command-set"></a>Schritt 4: Erstellen des Befehlssatzes
Erstellen Sie den Rest des Befehlssatzes, indem Sie den entsprechenden Satz an Blöcken unten in Ihre neue Textdatei oder ISE kopieren und dann die Variablenwerte eingeben und die Zeichen < und > entfernen. Anhand der beiden [Beispiele](#examples) am Ende dieses Artikels erhalten Sie eine Idee des Endergebnisses.

Starten Sie den Befehlssatz, indem Sie einen dieser beiden Befehlssätze auswählen (erforderlich).

Option 1: Geben Sie einen Namen für den virtuellen Computer und eine Größe an.

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Option 2: Geben Sie einen Namen, eine Größe und einen Verfügbarkeitsgruppennamen an.

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $availset="<set name>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Die InstanceSize-Werte für virtuelle Computer der D-, DS- oder G-Serie finden Sie unter [Größen virtueller Computer und Clouddienste für Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

> [!NOTE]
> Wenn Sie über ein Enterprise Agreement mit Software Assurance verfügen und den [Vorteil bei Hybridnutzung](https://azure.microsoft.com/pricing/hybrid-use-benefit/) für Windows Server nutzen möchten, fügen Sie den Parameter **-LicenseType** zum Cmdlet **New-AzureVMConfig** hinzu, und übergeben Sie den Wert **Windows_Server** für den normalen Anwendungsfall.  Achten Sie darauf, dass Sie ein Image verwenden, das Sie hochgeladen haben. Sie können kein Standardimage aus dem Katalog mit dem Vorteil bei Hybridnutzung verwenden.
> 
> 

Geben Sie optional für einen eigenständigen Windows-Computer das lokale Administratorkonto und Kennwort an.

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

Verwenden Sie ein sicheres Kennwort. Sie können die Sicherheit des Kennworts unter [Password Checker: Using Strong Passwords](https://www.microsoft.com/security/pc-security/password-checker.aspx)(Kennwortprüfung – Verwenden sicherer Kennwörter) überprüfen.

Geben Sie optional das lokale Administratorkonto und Kennwort, die Domäne und den Namen und das Kennwort eines Domänenkontos zum Hinzufügen des Windows-Computers zu einer vorhandenen Active Directory-Domäne an.

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="<FQDN of the domain that the machine is joining>"
    $domacctdomain="<domain of the account that has permission to add the machine to the domain>"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

Zusätzliche Vorabkonfigurationsoptionen für Windows-basierte virtuelle Computer finden Sie in der Syntax für die **Windows**- und **WindowsDomain**-Parametersätze in [Add-AzureProvisioningConfig](/powershell/module/azure/add-azureprovisioningconfig).

Weisen Sie dem virtuellen Computer optional eine bestimmte IP-Adresse (statische DIP) zu.

    $vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Sie können überprüfen, ob eine bestimmte IP-Adresse verfügbar ist mit:

    Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

Weisen Sie optional den virtuellen Computer zu einem bestimmten Subnetz in einem virtuellen Azure-Netzwerk zu.

    $vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

Fügen Sie optional einen einzelnen Datenträger zum virtuellen Computer hinzu.

    $disksize=<size of the disk in GB>
    $disklabel="<the label on the disk>"
    $lun=<Logical Unit Number (LUN) of the disk>
    $hcaching="<Specify one: ReadOnly, ReadWrite, None>"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Legen Sie für einen Active Directory-Domänencontroller "$hcaching" auf "Kein" fest.

Optional können Sie den virtuellen Computer einem vorhandenen Satz mit Lastenausgleich für den externen Datenverkehr hinzufügen.

    $protocol="<Specify one: tcp, udp>"
    $localport=<port number of the internal port>
    $pubport=<port number of the external port>
    $endpointname="<name of the endpoint>"
    $lbsetname="<name of the existing load-balanced set>"
    $probeprotocol="<Specify one: tcp, http>"
    $probeport=<TCP or HTTP port number of probe traffic>
    $probepath="<URL path for probe traffic>"
    $vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $protocol -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

Abschließend wählen Sie einen dieser erforderlichen Befehlsblöcke zum Erstellen des virtuellen Computers.

Option 1: Erstellen Sie den virtuellen Computer in einem vorhandenen Clouddienst.

    New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

Der Kurzname des Clouddiensts ist der Name in der Liste der Clouddienste im Azure-Portal oder in der Liste der Ressourcengruppen im Azure-Portal.

Option 2: Erstellen Sie den virtuellen Computer in einem vorhandenen Clouddienst und virtuellen Netzwerk.

    $svcname="<short name of the cloud service>"
    $vnetname="<name of the virtual network>"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## <a name="step-5-run-your-command-set"></a>Schritt 5: Ausführen des Befehlssatzes
Überprüfen Sie den aus mehreren Blöcken von Befehlen aus Schritt 4 bestehenden Azure PowerShell-Befehlssatz in einem Texteditor oder der PowerShell ISE. Stellen Sie sicher, dass Sie alle erforderlichen Variablen angegeben haben und diese die richtigen Werte aufweisen. Stellen Sie außerdem sicher, dass Sie alle < und > entfernt haben.

Wenn Sie einen Texteditor verwenden, kopieren Sie den Befehlssatz in die Zwischenablage, und klicken Sie dann mit der rechten Maustaste auf Ihre offene Windows PowerShell-Eingabeaufforderung. Dies gibt den Befehlssatz als Serie von PowerShell-Befehlen aus und erstellt den virtuellen Azure-Computer. Führen Sie alternativ den Befehlssatz in der PowerShell ISE aus.

Wenn Sie diesen virtuellen Computer erneut oder einen ähnlichen Computer erstellen, können Sie:

* diesen Befehlssatz als PowerShell-Skriptdatei (*.ps1) speichern
* Speichern Sie diesen Befehlssatz im Azure-Portal im Bereich **Automatisierungskonten** als Azure Automation-Runbook.

## <a id="examples"></a>Beispiele
Hier finden Sie zwei Beispiele für die Verwendung der vorangegangenen Schritte zum Erstellen von Azure PowerShell-Befehlssätzen, die Windows-basierte virtuelle Azure-Computer erstellen.

### <a name="example-1"></a>Beispiel 1
Ich brauche einen PowerShell-Befehl, um den ersten virtuellen Computer für einen Active Directory-Domänencontroller zu erstellen, der:

* das Windows Server 2012 R2 Datacenter-Image verwendet
* AZDC1 heißt
* ein eigenständiger Computer ist
* einen zusätzlichen Datenträger mit 20 GB aufweist
* über die statische IP-Adresse 192.168.244.4 verfügt
* sich im BackEnd-Subnetz des virtuellen Netzwerks "AZDatacenter" befindet
* sich im Clouddienst "Azure-TailspinToys" befindet

Hier finden Sie den entsprechenden Azure PowerShell-Befehlssatz zum Erstellen dieses virtuellen Computers, mit Leerzeilen zwischen jedem Block für Lesbarkeit.

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="AZDC1"
    $vmsize="Medium"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

    $vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

    $vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

    $disksize=20
    $disklabel="DCData"
    $lun=0
    $hcaching="None"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### <a name="example-2"></a>Beispiel 2
Ich brauche einen PowerShell-Befehlssatz, um einen virtuellen Computer für einen Line-of-Business-Server zu erstellen, der:

* das Windows Server 2012 R2 Datacenter-Image verwendet
* LOB1 heißt
* Mitglied der Domäne "corp.contoso.com" ist
* einen zusätzlichen Datenträger mit 200 GB aufweist
* sich im FrontEnd-Subnetz des virtuellen Netzwerks "AZDatacenter" befindet
* sich im Clouddienst "Azure-TailspinToys" befindet

Hier finden Sie den entsprechenden Azure PowerShell-Befehlssatz zum Erstellen dieses virtuellen Computers.

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="LOB1"
    $vmsize="Large"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="corp.contoso.com"
    $domacctdomain="CORP"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

    $disksize=200
    $disklabel="LOBData"
    $lun=0
    $hcaching="ReadWrite"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## <a name="next-steps"></a>Nächste Schritte
Wenn Sie einen Betriebssystem-Datenträger mit mindestens 127 GB benötigen, können Sie das [Betriebssystemlaufwerk erweitern](../../virtual-machines-windows-expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

