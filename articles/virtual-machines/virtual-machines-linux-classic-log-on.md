<properties
	pageTitle="Anmelden bei einem virtuellen Linux-Computer in Azure | Microsoft Azure"
	description="Erfahren Sie, wie Sie sich an einem virtuellen Azure-Computer unter Linux mithilfe eines Secure Shell (SSH)-Clients anmelden."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="rasquill"/>


#Anmelden bei einem mit Linux betriebenen virtuellen Computer #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](virtual-machines-linux-portal-create.md).

Sie müssen einen SSH-Client auf dem Computer installieren, über den Sie sich beim virtuellen Computer anmelden möchten. Es gibt eine Vielzahl an SSH-Client-Programmen, die Sie verwenden können. Hier einige mögliche Programme:

- Für einen virtuellen Computer mit einem Linux-Betriebssystem verwenden Sie zur Anmeldung einen Secure Shell (SSH)-Client. Es ist sehr unwahrscheinlich, dass es eine Distribution gibt, bei der dieser nicht standardmäßig installiert ist. Weitere Informationen über Linux finden Sie unter [Verwenden von SSH](virtual-machines-linux-ssh-from-linux.md).
- Auf einem Computer, auf dem ein Windows-Betriebssystem ausgeführt wird, sollten Sie einen SSH-Client wie PuTTY verwenden. Weitere Informationen erhalten Sie unter [PuTTY Download Page](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) (in englischer Sprache).


>[AZURE.NOTE] Weitere Tipps zu den Voraussetzungen und zur Problembehandlung finden Sie unter [Herstellen einer Verbindung mit einem virtuellen Azure-Computer über RDP oder SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294).

Mit den folgenden Schritten wird veranschaulicht, wie Sie mithilfe des SSH-Clients unter OS X auf den virtuellen Computer zugreifen.

1. Sie können **Hostname** und **Portinformationen** über das [Verwaltungsportal](http://manage.windowsazure.com) aufrufen. Sie finden die erforderlichen Informationen im Dashboard des virtuellen Computers. Klicken Sie in der **Schnelleinsicht** des Dashboards auf den Namen des virtuellen Computers, und navigieren Sie zu den **SSH-Details**.

	![SSH-Details abrufen](./media/virtual-machines-linux-classic-log-on/portalsshdetails.png)

2. Melden Sie sich mit dem Konto, das Sie beim Erstellen des virtuellen Computers angegeben haben, und mit dem entsprechenden Hostnamen und Port an. Nähere Informationen zum Erstellen eines virtuellen Computers mit Benutzername und Kennwort finden Sie unter [Erstellen eines virtuellen Linux-Computers](virtual-machines-linux-classic-createportal.md).

	![Anmelden beim virtuellen Computer](./media/virtual-machines-linux-classic-log-on/sshport.png)

>[AZURE.NOTE] Mit der VMAccess-Erweiterung können Sie den SSH-Schlüssel oder das SSH-Kennwort zurücksetzen, falls Sie diese vergessen sollten. Falls Sie den Benutzernamen vergessen, können Sie die Erweiterung verwenden, um einen neuen Benutzernamen mit sudo-Autorität zu erstellen. Eine Anleitung finden Sie unter [Zurücksetzen eines Kennworts oder einer SSH für virtuelle Linux-Computer].

Sie können jetzt mit dem virtuellen Computer wie mit jedem anderen Server arbeiten.

<!-- LINKS -->
[Zurücksetzen eines Kennworts oder einer SSH für virtuelle Linux-Computer]: http://go.microsoft.com/fwlink/p/?LinkId=512138

<!---HONumber=AcomDC_0511_2016-->