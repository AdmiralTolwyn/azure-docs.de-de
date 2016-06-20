<properties
	pageTitle="Installieren von Symantec Endpoint Protection auf einem neuen virtuellen Computer | Microsoft Azure"
	description="Hier erfahren Sie, wie Sie die Sicherheitserweiterung Symantec Endpoint Protection auf einem neuen oder vorhandenen virtuellen Azure-Computer, der mit dem klassischen Bereitstellungsmodell erstellt wurde, installieren und konfigurieren."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="iainfou"/>

# Installieren und Konfigurieren von Symantec Endpoint Protection auf einem virtuellen Windows-Computer

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.

In diesem Artikel wird beschrieben, wie der Symantec Endpoint Protection-Client auf einem neuen oder vorhandenen virtuellen Windows Server-Computer installiert und konfiguriert wird. Dies ist der vollständige Client, der Dienste wie Viren- und Spywareschutz, Firewall und Eindringschutz enthält.

Der Client wird als Sicherheitserweiterung durch die Verwendung des VM Agent installiert. Auf einem neuen virtuellen Computer installieren Sie den Agent zusammen mit dem Endpunktclient. Auf einem vorhandenen virtuellen Computer ohne den VM Agent müssen Sie den Agent zunächst herunterladen und installieren. Dieser Artikel deckt beide Situationen ab.

Wenn Sie über ein vorhandenes Abonnement von Symantec für eine lokale Lösung verfügen, können Sie es zum Schützen Ihrer virtuellen Azure-Computer verwenden. Wenn Sie noch kein Kunde sind, können Sie sich für ein Testabonnement registrieren. Weitere Informationen über diese Lösung finden Sie unter [Symantec Endpoint Protection on Microsoft's Azure platform][Symantec] (Symantec Endpoint Protection auf der Azure-Plattform von Microsoft) (in englischer Sprache). Diese Seite enthält auch Links zu Lizenzierungsinformationen und Anweisungen für das Installieren des Clients, wenn Sie bereits Symantec-Kunde sind.

## Installieren von Symantec Endpoint Protection auf einem neuen virtuellen Computer

Mit dem [klassischen Azure-Portal][Portal] können Sie den VM-Agent und die Symantec-Sicherheitserweiterung installieren, wenn Sie die Option **Aus Katalog** zum Erstellen des virtuellen Computers verwenden. Die Verwendung dieses Ansatzes ist eine einfache Möglichkeit, einen Schutz von Symantec hinzuzufügen, wenn Sie einen einzelnen virtuellen Computer erstellen.

Die Option **Aus Katalog** öffnet einen Assistenten, der Ihnen beim Einrichten des virtuellen Computers hilft. Sie verwenden die letzte Seite des Assistenten zum Installieren des VM-Agents und der Symantec-Sicherheitserweiterung.

Allgemeine Anweisungen dazu finden Sie unter [Erstellen eines virtuellen Windows Server-Computers][Create]. Wenn Sie zur letzten Seite des Assistenten gelangen:

1.	Unter VM-Agent müsste **VM-Agent installieren** bereits aktiviert sein.

2.	Aktivieren Sie **Symantec Endpoint Protection** unter „Sicherheitserweiterungen“.


	![Installieren des VM-Agents und des Endpoint Protection-Clients](./media/virtual-machines-windows-classic-install-symantec/InstallVMAgentandSymantec.png)

3.	Klicken Sie auf das Häkchen unten auf der Seite, um den virtuellen Computer zu erstellen.

## Installieren von Symantec Endpoint Protection auf einem vorhandenen virtuellen Computer

Bevor Sie beginnen, benötigen Sie Folgendes:

- Das Azure PowerShell-Modul, Version 0.8.2 oder höher, muss auf Ihrem Arbeitscomputer installiert sein. Sie können die installierte Version von Azure PowerShell mit dem Befehl **Get-Module azure | format-table version** überprüfen. Anweisungen und einen Link zur neuesten Version finden Sie unter [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell][PS]. Stellen Sie sicher, dass Sie bei Ihrem Azure-Abonnement angemeldet sind.

- Der VM-Agent muss auf dem virtuellen Azure-Computer ausgeführt werden.

Stellen Sie zunächst sicher, dass der VM-Agent bereits auf dem virtuellen Computer installiert ist. Füllen Sie den Clouddienstnamen und den Namen des virtuellen Computers aus, und führen Sie dann die folgenden Befehle an einer Azure PowerShell-Eingabeaufforderung mit Administratorrechten aus. Ersetzen Sie alles innerhalb der Anführungszeichen, einschließlich der Zeichen < and >.

> [AZURE.TIP] Wenn Sie den Namen des Clouddiensts und des virtuellen Computers nicht kennen, führen Sie **Get-AzureVM** aus, um die Namen aller virtuellen Computer im aktuellen Abonnement aufzulisten.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
	write-host $vm.VM.ProvisionGuestAgent

Der Befehl **write-host** zeigt **True** an, wenn der VM-Agent installiert ist. Wenn **False** zurückgegeben wird, nutzen Sie die Anweisungen und den Link zum Download im Azure-Blogbeitrag [VM Agent and Extensions - Part 2][Agent] (VM-Agent und Erweiterungen, Teil 2, in englischer Sprache).

Wenn der VM-Agent installiert ist, führen Sie diese Befehle aus, um den Symantec Endpoint Protection Agent zu installieren.

	$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

	Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

So überprüfen Sie, ob die Symantec-Sicherheitserweiterung installiert wurde und auf dem neuesten Stand ist:

1.	Melden Sie sich beim virtuellen Computer an. Weitere Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][Logon].
2.	Klicken Sie für Windows Server 2008 R2 auf **Start > Symantec Endpoint Protection**. Geben Sie für Windows Server 2012 oder Windows Server 2012 R2 im Startbildschirm **Symantec** ein, und klicken Sie dann auf **Symantec Endpoint Protection**.
3.	Wenden Sie im Fenster **Status-Symantec Endpoint Protection** auf der Registerkarte **Status** Updates an, oder führen Sie bei Bedarf einen Neustart aus.

## Zusätzliche Ressourcen

[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][Logon]

[Azure-VM-Erweiterungen und Features][Ext]


<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-classic-tutorial.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-windows-classic-connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493

<!---HONumber=AcomDC_0608_2016-->