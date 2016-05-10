<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="cynthn" solutions="" manager="timlt" editor="tysonn" />

4. Nach Klicken auf **Verbinden** wird eine Remotedesktopprotokolldatei (RDP-Datei) erstellt und heruntergeladen. Klicken Sie auf **Öffnen**, damit diese Datei verwendet wird.

	![Screenshot der heruntergeladenen .RDP-Datei.](./media/virtual-machines-log-on-win-server/open-rdp.png)

5. Es erscheint eine Warnung mit dem Hinweis, dass die RDP-Datei von einem unbekannten Herausgeber stammt. Dies ist normal. Klicken Sie im Fenster "Remotedesktop" auf **Verbinden**, um den Vorgang fortzusetzen.

	![Screenshot einer Warnung zu einem unbekannten Herausgeber](./media/virtual-machines-log-on-win-server/rdp-warn.png)

6. Geben Sie im Fenster "Windows-Sicherheit" den Benutzernamen und das Kennwort eines Kontos auf dem virtuellen Computer ein, und klicken Sie anschließend auf **OK**.

 	In der Regel sind die Anmeldeinformationen der Benutzername und das Kennwort des lokalen Kontos, die Sie beim Erstellen des virtuellen Computers angegeben haben. In diesem Fall ist die Domäne der Name des virtuellen Computers. Das Eingabeformat lautet *VM-Name*&#92;*Benutzername*.
	
	Wenn der virtuelle Computer zu einer Domäne in Ihrer Organisation gehört, stellen Sie sicher, dass der Benutzername den Namen der Domäne im Format *Domäne*&#92;*Benutzername* enthält. Das Konto muss außerdem entweder zur Gruppe „Administratoren“ gehören oder über Remotezugriffsrechte für den virtuellen Computer verfügen.
	
	Wenn der virtuelle Computer ein Domänencontroller ist, geben Sie den Benutzernamen und das Kennwort eines Domänenadministratorkontos für diese Domäne an.

7.	Klicken Sie auf **Ja**, um die Identität des virtuellen Computers zu bestätigen und das Anmelden zu beenden.

	![Screenshot mit einer Meldung zur Überprüfung der Identität des virtuellen Computers](./media/virtual-machines-log-on-win-server/cert-warning.png)

<!---HONumber=AcomDC_0504_2016-->