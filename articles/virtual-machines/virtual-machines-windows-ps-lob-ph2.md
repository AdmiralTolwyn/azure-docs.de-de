<properties 
	pageTitle="Branchenanwendung, Phase 2 | Microsoft Azure" 
	description="In Phase 2 der Branchenanwendung erstellen und konfigurieren Sie in Azure die zwei Active Directory-Replikatdomänencontroller." 
	documentationCenter=""
	services="virtual-machines-windows" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# Branchenanwendungs-Workload, Phase 2: Konfigurieren der Domänencontroller

In dieser Phase der Bereitstellung einer hochverfügbaren Branchenanwendung in den Azure-Infrastrukturdiensten konfigurieren Sie zwei Replikatdomänencontroller im virtuellen Azure-Netzwerk, sodass Webanforderungen der Clients nach Webressourcen im virtuellen Azure-Netzwerk authentifiziert werden können. Die für die Authentifizierung erforderlichen Daten müssen dann nicht über die Verbindung mit Ihrem lokalen Netzwerk gesendet werden.

Diese Phase muss vor Beginn von [Phase 3](virtual-machines-windows-ps-lob-ph3.md) ausgeführt worden sein. Eine Übersicht über alle Phasen finden Sie unter [Bereitstellen einer hochverfügbaren Branchenanwendung in Azure](virtual-machines-windows-lob-overview.md).

## Erstellen der virtuellen Computer für die Domänencontroller in Azure

Zunächst müssen Sie in Tabelle M die Spalte **Name des virtuellen Computers** ausfüllen und die Größen der virtuellen Computer nach Bedarf in Spalte **Mindestgröße** ändern.

Element | Name des virtuellen Computers | Katalogimage | Mindestgröße 
--- | --- | --- | --- 
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (erster Domänencontroller, Beispiel: DC1) | Windows Server 2012 R2 Datacenter | Standard\_D2
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (zweiter Domänencontroller, Beispiel: DC2) | Windows Server 2012 R2 Datacenter | Standard\_D2
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (primärer Datenbankserver, Beispiel: SQL1) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | 	Standard\_DS4
4\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (sekundärer Datenbankserver, Beispiel: SQL2) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | 	Standard\_DS4
5\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (Mehrheitsknoten des Clusters, Beispiel: MN1) | Windows Server 2012 R2 Datacenter | Standard\_D1
6\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (erster Webserver, Beispiel: WEB1) | Windows Server 2012 R2 Datacenter | Standard\_D3
7\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (zweiter Webserver, Beispiel: WEB2) | Windows Server 2012 R2 Datacenter | Standard\_D3

**Tabelle M: Virtuelle Computer für die Branchenanwendung mit hoher Verfügbarkeit in Azure**

Einzelheiten zu den Größen der virtuellen Computer finden Sie unter [Größen virtueller Computer](virtual-machines-linux-sizes.md).

Mit dem folgenden Azure PowerShell-Befehlsblock erstellen Sie die virtuellen Computer für die beiden Domänencontroller. Geben Sie die Werte für die Variablen ein, lassen Sie dabei aber die < and >-Zeichen weg. Die Werte für diesen PowerShell-Befehlssatz entnehmen Sie den folgenden Tabellen:

- Tabelle M für Ihre virtuellen Computer
- Tabelle V für die Einstellungen Ihres virtuellen Netzwerks
- Tabelle S für Ihr Subnetz
- Tabelle ST für Ihre Speicherkonten
- Tabelle A für Ihre Verfügbarkeitsgruppen

Die Tabellen V, S, ST und A haben Sie in [Phase 1: Konfigurieren von Azure](virtual-machines-windows-ps-lob-ph1.md) ausgefüllt.

> [AZURE.NOTE] Die folgenden Befehlssätze verwenden Azure PowerShell 1.0 und höher. Weitere Informationen finden Sie unter [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/) (in englischer Sprache).

Führen Sie nach der Bereitstellung der richtigen Werte den daraus resultierenden Befehlsblock an der Azure PowerShell-Eingabeaufforderung aus.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 2 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 1 – Availability set name column>"
	
	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column>"
	$staticIP="<Table V – Item 6 - Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first domain controller." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column>"
	$staticIP="<Table V – Item 7 - Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second domain controller." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] Da diese virtuellen Computer für eine Intranetanwendung gedacht sind, wird ihnen keine öffentliche IP-Adresse oder ein DNS-Domänenname zugewiesen, und sie sind nicht über das Internet erreichbar. Dies bedeutet jedoch auch, dass Sie damit über das Azure-Portal keine Verbindung herstellen können. Die Schaltfläche **Verbinden** ist nicht verfügbar, wenn Sie die Eigenschaften des virtuellen Computers anzeigen.

## Konfigurieren des ersten Domänencontrollers

Verwenden Sie einen Remotedesktopclient Ihrer Wahl, und erstellen Sie eine Remotedesktopverbindung mit dem virtuellen Computer, der als erster Domänencontroller fungiert. Verwenden Sie den zugehörigen Intranet-DNS- oder Computernamen und die Anmeldeinformationen des lokalen Administratorkontos.

Als Nächstes müssen Sie dem ersten Domänencontroller den zusätzlichen Datenträger hinzufügen.

### <a id="datadisk"></a>Initialisieren eines leeren Datenträgers

1.	Klicken Sie im linken Bereich des Server-Managers auf **Datei- und Speicherdienste** und anschließend auf **Datenträger**.
2.	Klicken Sie im Inhaltsbereich in der Gruppe **Datenträger** auf **Datenträger 2** (wobei **Partition** die Einstellung **Unbekannt** aufweisen muss).
3.	Klicken Sie auf **Aufgaben** und anschließend auf **Neues Volume**.
4.	Klicken Sie auf der Seite „Voraussetzungen“ des Assistenten für neue Volumes auf **Weiter**.
5.	Klicken Sie auf der Seite „Server und Datenträger auswählen“ auf **Datenträger 2** und anschließend auf **Weiter**. Wenn Sie dazu aufgefordert werden, klicken Sie auf OK.
6.	Klicken Sie auf der Seite „Geben Sie die Größe des Volumes an“ auf **Weiter**.
7.	Klicken Sie auf der Seite „Einem Laufwerkbuchstaben oder Ordner zuweisen“ auf **Weiter**.
8.	Klicken Sie auf der Seite „Dateisystemeinstellungen auswählen“ auf **Weiter**.
9.	Klicken Sie auf der Seite „Auswahl bestätigen“ auf **Erstellen**.
10.	Wenn Sie fertig sind, klicken Sie auf **Schließen**.

Testen Sie danach die Konnektivität des ersten Domänencontrollers mit Standorten Ihres Unternehmensnetzwerks.

### <a id="testconn"></a>Testen der Konnektivität

1.	Öffnen Sie vom Desktop eine Windows PowerShell-Eingabeaufforderung.
2.	Testen Sie mit dem Befehl **Ping** die Konnektivität zu Namen und IP-Adressen von Ressourcen in Ihrem Unternehmensnetzwerk.

Dadurch stellen Sie sicher, dass die DNS-Namensauflösung ordnungsgemäß funktioniert (d. h., dass der virtuelle Computer korrekt für die lokalen DNS-Server konfiguriert ist) und Pakete zum und vom standortübergreifenden virtuellen Netzwerk gesendet werden können. Wenn bei diesem grundlegenden Test ein Fehler auftritt, wenden Sie sich an Ihre IT-Abteilung, um die Probleme bei DNS-Namensauflösung und Paketübermittlung zu beheben.

Führen Sie danach an der Windows PowerShell-Eingabeaufforderung des ersten Domänencontrollers die folgenden Befehle aus:

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Sie werden aufgefordert, die Anmeldeinformationen eines Domänenadministratorkontos einzugeben. Der Computer wird neu gestartet.

## Konfigurieren des zweiten Domänencontrollers

Verwenden Sie einen Remotedesktopclient Ihrer Wahl, und erstellen Sie eine Remotedesktopverbindung mit dem virtuellen Computer, der als zweiter Domänencontroller fungiert. Verwenden Sie den zugehörigen Intranet-DNS- oder Computernamen und die Anmeldeinformationen des lokalen Administratorkontos.

Als Nächstes müssen Sie dem zweiten Domänencontroller den zusätzlichen Datenträger hinzufügen. Anweisungen finden Sie in der Beschreibung der Prozedur [Initialisieren eines leeren Datenträgers](#datadisk).

Führen Sie danach an der Windows PowerShell-Eingabeaufforderung des zweiten Domänencontrollers die folgenden Befehle aus:

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Sie werden aufgefordert, die Anmeldeinformationen eines Domänenadministratorkontos einzugeben. Der Computer wird neu gestartet.

Anschließend müssen Sie die DNS-Server für Ihr virtuelles Netzwerk aktualisieren, damit Azure den virtuellen Computern die IP-Adressen der beiden neuen Domänencontroller als deren DNS-Server zuweist. Die für dieses Verfahren benötigten Werte stammen aus Tabelle V (Einstellungen Ihres virtuellen Netzwerks) und Tabelle M (für Ihre virtuellen Computer).

1.	Klicken Sie im linken Bereich des Azure-Portals auf **Virtuelle Netzwerke**, und klicken Sie dann auf den Namen Ihres virtuellen Netzwerks (Tabelle V – Element 1 – Spalte „Wert“).
2.	Klicken Sie im Bereich **Einstellungen** auf **DNS-Server**.
3.	Geben Sie im Bereich **DNS-Server** Folgendes ein:
	- Für **Primärer DNS-Server**: Tabelle V – Element 6 – Wertspalte
	- Für **Sekundärer DNS-Server**: Tabelle V – Element 7 – Wertspalte
4.	Klicken Sie im Azure-Portal im linken Bereich auf **Virtuelle Computer**.
5.	Klicken Sie im Bereich **Virtuelle Computer** auf den Namen Ihres ersten Domänencontrollers (Tabelle M – Element 1 – Spalte mit Namen der virtuellen Computer).
6.	Klicken Sie im Bereich für den virtuellen Computer auf **Neu starten**.
7.	Wenn der erste Domänencontroller gestartet wurde, klicken Sie im Bereich **Virtuelle Computer** auf den Namen des zweiten Domänencontrollers (Tabelle M – Element 2 – Spalte mit Namen der virtuellen Computer).
8.	Klicken Sie im Bereich für den virtuellen Computer auf **Neu starten**. Warten Sie, bis der zweite Domänencontroller gestartet ist.

Die beiden Domänencontroller mussten neu gestartet werden, damit sie nicht mit dem lokalen DNS-Server als DNS-Server konfiguriert sind. Da es sich bei beiden selbst um DNS-Server handelt, wurden sie bei ihrer Heraufstufung auf Domänencontroller automatisch mit den lokalen DNS-Servern als DNS-Weiterleitung konfiguriert.

Um sicherzustellen, dass die Server im virtuellen Azure-Netzwerk die lokalen Domänencontroller verwenden, muss jetzt eine Active Directory-Replikationssite erstellt werden. Melden Sie sich beim primären Domänencontroller mit einem Domänenadministratorkonto an, und führen Sie an einer Windows PowerShell-Eingabeaufforderung die folgenden Befehle auf Administratorebene aus:

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet 
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

Im nächsten Schritt fügen Sie ein Benutzerkonto zum Verwalten der virtuellen SQL Server-Computer hinzu.

	New-ADUser -SamAccountName sqlservice -AccountPassword (read-host "Set user password" -assecurestring) -name "sqlservice" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

Im folgenden Diagramm ist die aus dem erfolgreichen Abschluss dieser Phase resultierende Konfiguration mit Platzhaltern anstelle der Computernamen dargestellt.

![](./media/virtual-machines-windows-ps-lob-ph2/workload-lobapp-phase2.png)

## Nächster Schritt

- Zum Fortsetzen der Konfiguration dieser Workload wechseln Sie zu [Phase 3](virtual-machines-windows-ps-lob-ph3.md).

<!---HONumber=AcomDC_0601_2016-->