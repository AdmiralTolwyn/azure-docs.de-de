<properties
	pageTitle="Anmelden bei einem klassischen virtuellen Azure-Computer | Microsoft Azure"
	description="Verwenden Sie das klassische Azure-Portal für die Anmeldung bei einem virtuellen Windows-Computer, der mit dem klassischen Bereitstellungsmodell erstellt wurde."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="cynthn"/>


# Anmelden bei einem virtuellen Windows-Computer über das klassische Azure-Portal

Im klassischen Azure-Portal können Sie über die Schaltfläche **Verbinden** eine Remotedesktopsitzung starten und sich bei einem virtuellen Windows-Computer anmelden.

Möchten Sie eine Verbindung mit einem virtuellen Linux-Computer herstellen? Siehe [Anmelden bei einem mit Linux betriebenen virtuellen Computer](virtual-machines-linux-classic-log-on.md).

Erfahren Sie, wie Sie [diese Schritte über das neue Azure-Portal ausführen](virtual-machines-windows-connect-logon.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## Exemplarische Vorgehensweise per Video

Hier finden Sie ein Video mit einer exemplarischen Vorgehensweise für die Schritte dieses Tutorials. Dabei werden auch Endpunkte sowie öffentliche und private Ports für die Verbindung mit einem virtuellen Computer in Azure behandelt.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]


## Herstellen einer Verbindung mit dem virtuellen Computer

1. Melden Sie sich am klassischen Azure-Portal an.

2. Klicken Sie auf **Virtuelle Computer**, und wählen Sie den virtuellen Computer aus.

3. Klicken Sie in der Befehlsleiste am unteren Rand der Seite auf **Verbinden**.

	![Anmelden beim virtuellen Computer](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)
	
> [AZURE.TIP] Wenn die Schaltfläche **Verbinden** nicht verfügbar ist, finden Sie weitere Informationen in den Tipps zur Problembehandlung am Ende dieses Artikels.

## Melden Sie sich beim virtuellen Computer an.

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Nächste Schritte

-	Wenn die Schaltfläche **Verbinden** inaktiv ist oder andere Probleme mit der Remotedesktopverbindung auftreten, versuchen Sie, die Konfiguration zurückzusetzen. Klicken Sie im Dashboard des virtuellen Computers unter **Auf einen Blick** auf **Remotekonfiguration zurücksetzen**.
-	Wenn Sie Probleme mit Ihrem Kennwort haben, setzen Sie es zurück. Klicken Sie im Dashboard des virtuellen Computers unter **Auf einen Blick** auf **Kennwort zurücksetzen**.

Wenn die Tipps nicht funktionieren oder nicht das Gesuchte sind, finden Sie entsprechende Informationen unter [Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](virtual-machines-windows-troubleshoot-rdp-connection.md). Dieser Artikel führt Sie durch die Diagnose und Behebung von häufigen Problemen.

<!---HONumber=AcomDC_0803_2016-->