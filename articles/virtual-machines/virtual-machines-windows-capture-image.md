<properties
	pageTitle="Erfassen eines virtuellen Windows-Computers in Resource Manager | Microsoft Azure"
	description="Erfahren Sie, wie Sie im Resource Manager-Bereitstellungsmodell ein Image eines Windows-basierten virtuellen Azure-Computers erfassen können."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/13/2016"
	ms.author="cynthn"/>

# Erfassen eines virtuellen Windows-Computers im Resource Manager-Bereitstellungsmodell


In diesem Artikel wird erläutert, wie Sie einen virtuellen Azure-Computer, auf dem Windows ausgeführt wird, mithilfe von Azure PowerShell erfassen, um ihn als Image zum Erstellen anderer virtueller Computer zu verwenden. Dieses Image umfasst den Betriebssystemdatenträger und die an den virtuellen Computer angefügten Datenträger. Nicht enthalten sind die Ressourcen des virtuellen Netzwerks, die Sie zum Erstellen eines virtuellen Windows-Computers benötigen. Sie müssen diese Ressourcen separat einrichten, bevor Sie einen weiteren virtuellen Computer auf Basis des Images erstellen. Dieses Image wird außerdem als [generalisiertes Windows-Image](https://technet.microsoft.com/library/hh824938.aspx) vorbereitet.



## Voraussetzungen

Diese Schritte setzen voraus, dass Sie bereits einen virtuellen Azure-Computer im Resource Manager-Bereitstellungsmodell erstellt, das Betriebssystem konfiguriert, beliebige Datenträger angefügt und weitere Anpassungen wie die Installation von Anwendungen vorgenommen haben. Wenn Sie diese Schritte noch nicht ausgeführt haben, lesen Sie [Erstellen einer Windows-VM mit dem Ressourcen-Manager und PowerShell](virtual-machines-windows-ps-create.md). Sie können einen virtuellen Windows-Computer (Windows Virtual Machine, Windows-VM) aber auch genauso einfach im [Azure-Portal](https://portal.azure.com) erstellen. Lesen Sie dazu [How to create a Windows virtual machine in the Azure portal](virtual-machines-windows-hero-tutorial.md) (Gewusst wie: Erstellen eines virtuellen Windows-Computers im Azure-Portal).


## Vorbereiten des virtuellen Computers für die Erfassung des Images

In diesem Abschnitt wird erläutert, wie Sie den virtuellen Windows-Computer generalisieren. Dadurch werden unter anderem alle persönlichen Kontoinformationen entfernt. Sie sollten diesen Schritt üblicherweise ausführen, wenn Sie ein VM-Image zum schnellen Bereitstellen ähnlicher virtueller Computer verwenden möchten.

> [AZURE.WARNING] Bitte beachten Sie, dass der virtuelle Computer nach dem Generalisieren nicht über RDP angemeldet werden kann, da der Prozess alle Benutzerkonten entfernt. Diese Änderung kann nicht rückgängig gemacht werden.

1. Melden Sie sich bei Ihrem virtuellen Windows-Computer an. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu **Durchsuchen** > **Virtuelle Computer** > Ihr virtueller Windows-Computer > **Verbinden**.

2. Öffnen Sie ein Eingabeaufforderungsfenster als Administrator.

3. Ändern Sie das Verzeichnis in `%windir%\system32\sysprep`, und führen Sie dann „sysprep.exe“ aus.

4. Führen Sie im Dialogfeld **Systemvorbereitungstool** folgende Schritte aus:

	- Wählen Sie unter **Systembereinigungsaktion** die Option **Out-of-Box-Experience (OOBE) für System aktivieren** und stellen Sie sicher, dass **Verallgemeinern** aktiviert ist. Weitere Informationen zur Verwendung von Sysprep finden Sie unter [How to Use Sysprep: An Introduction](http://technet.microsoft.com/library/bb457073.aspx).

	- Wählen Sie unter **Optionen für Herunterfahren** die Option **Herunterfahren**.

	- Klicken Sie auf **OK**.

	![Sysprep ausführen](./media/virtual-machines-windows-capture-image/SysprepGeneral.png)

   Sysprep fährt den virtuellen Computer herunter. Sein Status im Azure-Portal wird in **Beendet** geändert.

</br>
## Erfassen des virtuellen Computers

Sie können den generalisierten Windows-Computer entweder mit Azure PowerShell oder mit dem neuen Tool „Azure Resource Manager-Explorer“ erfassen. In diesem Abschnitt werden die Schritte für beide Vorgehensweisen erläutert.

### Verwenden von PowerShell

In diesem Artikel wird davon ausgegangen, dass Sie Azure PowerShell in der Version 1.0.x installiert haben. Wir empfehlen, diese Version zu verwenden, weil neue Resource Manager-Features nicht zu älteren PowerShell-Versionen hinzugefügt werden. Wenn Sie PowerShell noch nicht installiert haben, finden Sie die Installationsschritte unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).

1. Öffnen Sie Azure PowerShell, und melden Sie sich bei Ihrem Azure-Konto an.

		Login-AzureRmAccount

	Dieser Befehl öffnet ein Popupfenster, in dem Sie Ihre Azure-Anmeldeinformationen eingeben können.

2. Wenn sich die standardmäßig ausgewählte Abonnement-ID von derjenigen unterscheidet, mit der Sie arbeiten möchten, verwenden Sie einen der beiden folgenden Befehle, um das gewünschte Abonnement festzulegen.

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	oder

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	Die in Ihrem Azure-Konto enthaltenen Abonnements können mithilfe des Befehls `Get-AzureRmSubscription` ermittelt werden.

3. Jetzt müssen Sie die Zuordnung der Ressourcen aufheben, die von diesem virtuellen Computer verwendet werden. Verwenden Sie hierzu diesen Befehl:

		Stop-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM

	Sie sehen, dass der *Status* des virtuellen Computers im Azure-Portal von **Beendet** in **Beendet (Zuordnung aufgehoben)** geändert wurde.

	>[AZURE.TIP] Sie können den Status Ihres virtuellen Computers auch mit PowerShell ermitteln, indem Sie </br> `$vm = Get-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM -status`</br> `$vm.Statuses`</br> verwenden. Das Feld **DisplayStatus** entspricht dem **Status**, der im Azure-Portal angezeigt wird.

4. Nun müssen Sie den Status des virtuellen Computers auf **Generalisiert** festlegen. Sie müssen diesen Schritt selbst ausführen, weil bei der oben durchgeführten Generalisierung (`sysprep`) der Status nicht auf eine für Azure verständliche Weise geändert wird.

		Set-AzureRmVm -ResourceGroupName YourResourceGroup -Name YourWindowsVM -Generalized

	>[AZURE.NOTE] Der durch den Befehl oben festgelegte Status „Generalisiert“ wird nicht im Portal angezeigt. Sie können ihn jedoch mithilfe des Befehls „Get-AzureRmVM“ überprüfen, wie im Tipp oben gezeigt.

5. Erfassen Sie das Image des virtuellen Computers mithilfe des folgenden Befehls in einem Zielspeichercontainer:

		Save-AzureRmVMImage -ResourceGroupName YourResourceGroup -VMName YourWindowsVM -DestinationContainerName YourImagesContainer -VHDNamePrefix YourTemplatePrefix -Path Yourlocalfilepath\Filename.json

	Die Variable `-Path` ist optional. Sie können sie verwenden, um die JSON-Vorlage lokal zu speichern. Die Variable `-DestinationContainerName` ist der Name des Containers, in dem Ihre Images aufbewahrt werden sollen. Die URL des gespeicherten Images ähnelt der Folgenden: `https://YourStorageAccountName.blob.core.windows.net/system/Microsoft.Compute/Images/YourImagesContainer/YourTemplatePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`. Das Image wird im gleichen Speicherkonto erstellt wie der ursprüngliche virtuelle Computer.

	>[AZURE.NOTE] Um den Speicherort des Images zu finden, öffnen Sie die JSON-Dateivorlage. Suchen Sie den Abschnitt **resources** > **storageProfile** > **osDisk** > **image** > **uri**, um den vollständigen Pfad zu Ihrem Image zu finden. Sie können auch den URI im Portal überprüfen. Dieser wird in ein Blob mit dem Namen **system** in Ihrem Speicherkonto kopiert.


### Verwenden von Azure-Ressourcen-Explorer (Vorschau)

[Azure-Ressourcen-Explorer (Vorschau)](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/) ist ein neues Tool zum Verwalten von Azure-Ressourcen, die im Resource Manager-Bereitstellungsmodell erstellt wurden. Dieses Tool bietet Ihnen folgende Möglichkeiten:

- Kennenlernen der APIs für die Azure-Ressourcenverwaltung
- Abrufen der API-Dokumentation
- Direktes Aufrufen von APIs in Ihren Azure-Abonnements

Wenn Sie mehr über dieses leistungsstarke Tool erfahren möchten, schauen Sie sich das Video zum [Azure Resource Manager-Explorer mit David Ebbo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo) an.

Sie können den Resource Manager auch zum Erfassen des virtuellen Computers verwenden – alternativ zum Verwenden von PowerShell.

1. Öffnen Sie die [Ressourcen-Explorer-Website](https://resources.azure.com/), und melden Sie sich bei Ihrem Azure-Konto an.

2. Wählen Sie oben rechts im Tool die Option **Lesen/schreiben**, um _PUT_- und _POST_-Vorgänge zuzulassen. Standardmäßig ist **Schreibgeschützt** festgelegt. Dies bedeutet, dass Sie nur _GET_-Vorgänge durchführen können.

	![Ressourcen-Explorer – Lesen/Schreiben](./media/virtual-machines-windows-capture-image/ArmExplorerReadWrite.png)

3. Suchen Sie als Nächstes Ihren virtuellen Windows-Computer. Sie können entweder oben im *Suchfeld* den Namen eingeben, oder Sie können links im Menü zu Ihrem virtuellen Computer navigieren: **subscriptions** > *Ihr Azure-Abonnement* > **resourceGroups** > *Ihre Ressourcengruppe* > **providers** > **Microsoft.Compute** > **virtualMachines** > *Ihr virtueller Windows-Computer*. Wenn Sie im linken Navigationsbereich auf Ihren virtuellen Computer klicken, wird auf der rechten Seite des Tools die zugehörige Vorlage angezeigt.

4. Rechts oben über der Vorlagenseite werden Registerkarten für die verschiedenen Vorgänge angezeigt, die für diesen virtuellen Computer zur Verfügung stehen. Klicken Sie auf die Registerkarte für **Aktionen (POST/DELETE)**.

	![Ressourcen-Explorer – Aktionsmenü](./media/virtual-machines-windows-capture-image/ArmExplorerActionMenu.png)

	- Es wird eine Liste mit allen Aktionen angezeigt, die Sie für den virtuellen Computer ausführen können.

		![Ressourcen-Explorer – einzelne Aktionen](./media/virtual-machines-windows-capture-image/ArmExplorerActionItems.png)

5. Heben Sie die Zuordnung für den virtuellen Computer auf, indem Sie auf die Aktionsschaltfläche für **deallocate** klicken. Der Status des virtuellen Computers wird von **Beendet** in **Beendet (Zuordnung aufgehoben)** geändert.

6. Markieren Sie den virtuellen Computer als generalisiert, indem Sie auf die Aktionsschaltfläche für **generalize** klicken. Sie können die Statusänderung überprüfen, indem Sie auf der linken Seite unter dem Namen Ihres virtuellen Computers auf **InstanceView** klicken und auf der rechten Seite zum Abschnitt **statuses** navigieren.

7. Unter der Aktionsschaltfläche **capture** können Sie die Werte für das Erfassen Ihres Images festlegen. Die ausgefüllten Werte sollten wie folgt aussehen:

	![Ressourcen-Explorer – „capture“](./media/virtual-machines-windows-capture-image/ArmExplorerCaptureAction.png)

	Klicken Sie auf die Aktionsschaltfläche **capture**, um das Image Ihres virtuellen Computers zu erfassen. Dadurch werden eine neue VHD für das Image und eine JSON-Vorlagendatei erstellt.

8. Um auf die neue Image-VHD und auf die Vorlage zugreifen zu können, müssen Sie das Azure-Tool zum Verwalten von Speicherressourcen herunterladen und installieren: den [Azure Storage-Explorer](http://storageexplorer.com/). Der Azure Storage-Explorer wird lokal auf Ihrem Computer installiert.

	- Öffnen Sie den Storage-Explorer, und melden Sie sich bei Ihrem Azure-Abonnement an. Es werden alle für Ihr Abonnement verfügbaren Speicherkonten angezeigt.

	- Links wird das Speicherkonto für den virtuellen Computer angezeigt, der in den vorherigen Schritten erfasst wurde. Doppelklicken Sie darunter auf den Menüeintrag **system**. Daraufhin wird auf der rechten Seite der Inhalt des Ordners **system** angezeigt.

		![Storage-Explorer – „system“](./media/virtual-machines-windows-capture-image/StorageExplorer1.png)

	- Doppelklicken Sie auf **Microsoft.Compute** > **Images**, wodurch alle Ihre Ordner für Images angezeigt werden. Doppelklicken Sie auf den Ordnernamen, den Sie beim Erfassen des Images im Ressourcen-Explorer für die Variable **destinationContainerName** eingegeben haben. Daraufhin wird sowohl die VHD als auch die JSON-Vorlagendatei angezeigt.

	- Von hier aus können Sie die URL ermitteln oder die VHD oder die Vorlage herunterladen, indem Sie mit der rechten Maustaste darauf klicken.

		![Storage-Explorer – Vorlage](./media/virtual-machines-windows-capture-image/StorageExplorer2.png)


## Bereitstellen eines neuen virtuellen Computers mithilfe des erfassten Images

Sie können auf Basis des erfassten Images einen neuen virtuellen Windows-Computer erstellen. In den folgenden Schritten wird gezeigt, wie Sie diesen virtuellen Computer mit Azure PowerShell und anhand des zuvor erfassten VM-Images in einem neuen virtuellen Netzwerk erstellen.

>[AZURE.NOTE] Das VM-Image und der zu erstellende virtuelle Computer müssen sich in dem gleichen Speicherkonto befinden.

### Erstellen von Netzwerkressourcen

Verwenden Sie das folgende PowerShell-Beispielskript, um ein virtuelles Netzwerk und eine Netzwerkschnittstellenkarte (Network Interface Card, NIC) für den neuen virtuellen Computer zu erstellen. Verwenden Sie für die durch das **$**-Zeichen dargestellten Variablen Werte, die für Ihre Anwendung geeignet sind.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### Erstellen eines neuen virtuellen Computers

Das folgende PowerShell-Skript veranschaulicht, wie Sie die Konfiguration für den virtuellen Computer einrichten und das erfasste VM-Image als Quelle für die neue Installation verwenden. </br>

	#Enter a new user name and password in the pop-up for the following
	$cred = Get-Credential

	#Get the storage account where the captured image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from image (-CreateOption fromImage) and give the URL of the captured image VHD for the -SourceImageUri parameter.
	#We found this URL in the local JSON template in the previous sections.
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfCapturedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

Sie sollten nun den neu erstellten virtuellen Computer im [Azure-Portal](https://portal.azure.com) unter **Durchsuchen** > **Virtuelle Computer** sehen. Alternativ können Sie ihn auch mit den folgenden PowerShell-Befehlen ermitteln:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Nächste Schritte

Informationen zum Verwalten des neuen virtuellen Computers mit Azure PowerShell finden Sie unter [Verwalten von virtuellen Computern mit Azure Resource Manager und PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0525_2016-->