<properties
	pageTitle="Erstellen eines virtuellen Oracle WebLogic Server 12c-Computers | Microsoft Azure"
	description="Erstellen Sie einen virtuellen Oracle WebLogic Server 12c-Computer, auf dem Windows Server 2012 in Microsoft Azure ausgeführt wird, indem Sie das Ressourcen-Manager-Bereitstellungsmodell verwenden."
	services="virtual-machines-windows"
	authors="rickstercdn"
	manager="timlt"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="05/17/2016"
	ms.author="rclaus" />

#Erstellen eines Erstellen eines virtuellen Oracle WebLogic Server 12c-Computers in Azure

[AZURE.INCLUDE [virtual-machines-common-oracle-support](../../includes/virtual-machines-common-oracle-support.md)]

Das folgende Beispiel zeigt, wie Sie eine WebLogic Server 12c-Instanz auf einem virtuellen Computer erstellen können, den Sie zuvor erstellt und auf dem Sie Oracle WebLogic Server 12c unter Windows Server 2012 in Azure installiert haben.

##Konfigurieren eines virtuellen Oracle WebLogic Server 12c-Computers in Azure

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.

2.	Klicken Sie auf **Virtuelle Computer**.

3.	Klicken Sie auf den Namen des virtuellen Computers, an dem Sie sich anmelden möchten.

4.	Klicken Sie auf **Verbinden**.

5.	Befolgen Sie die Anweisungen, um eine Verbindung mit dem virtuellen Computer herzustellen. Wenn Sie zur Eingabe des Administratornamens und des Kennworts aufgefordert werden, verwenden Sie die Werte, die Sie beim Erstellen des virtuellen Computers bereitgestellt haben.

6.	Klicken Sie im Dialogfeld **WebLogic-Plattform Schnellstart** auf **Erste Schritte mit WebLogic-Server**. (Wenn das Dialogfeld **WebLogic-Plattform Schnellstart** nicht bereits geöffnet ist, öffnen Sie es, indem Sie auf die **Windows-Schaltfläche Start** klicken, **Admin-Server für WebLogic Server-Domäne starten** eingeben und dann auf das Symbol **Admin-Server für WebLogic Server-Domäne starten** klicken.)

7.	Wählen Sie im Dialogfeld **Willkommen** die Option **Neue WebLogic-Domäne erstellen** aus, und klicken Sie dann auf **Weiter**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image10.png)

8.	Übernehmen Sie im Dialogfeld **Domänenquelle auswählen** die Standardwerte, und klicken Sie dann auf **Weiter**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image11.png)

9.	Übernehmen Sie im Dialogfeld **Domänenname und -standort angeben** die Standardwerte, und klicken Sie dann auf **Weiter**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image12.png)

10.	Im Dialogfeld **Benutzername und Kennwort für Administrator konfigurieren**:

	1.	[Optional] Ändern Sie den Namen von **weblogic** in einen beliebigen Namen Ihrer Wahl.

	2.	Geben Sie ein Kennwort für den WebLogic Server-Administrator ein und bestätigen Sie es.

	3.	Klicken Sie auf **Weiter**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image13.png)

11.	Wählen Sie im Dialogfeld **Server-Startmodus und JDK konfigurieren** die Option **Produktionsmodus** aus, wählen Sie das verfügbare JDK aus (oder navigieren Sie ggf. zum gewünschten JDK), und klicken Sie dann auf **Weiter**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image14.png)

12.	Wählen Sie im Dialogfeld **Optionale Konfiguration auswählen** keine weiteren Optionen aus, und klicken Sie auf **Weiter**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image15.png)

13.	Klicken Sie im Dialogfeld **Konfigurationszusammenfassung** auf **Erstellen**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image16.png)

14.	Aktivieren Sie im Dialogfeld **Domäne erstellen** das Kontrollkästchen **Admin-Server starten**, und klicken Sie dann auf **Fertig**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image17.png)

15.	Eine Eingabeaufforderung für **startWebLogic.cmd** wird gestartet. Geben Sie bei Aufforderung Ihren WebLogic-Benutzernamen und das Kennwort ein.

##Installieren einer Anwendung auf einem virtuellen Oracle WebLogic Server 12c-Computer in Azure
1.	Bleiben Sie am virtuellen Computer angemeldet, und speichern Sie das shoppingcart.war-Beispiel lokal unter http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war. Erstellen Sie z. B. einen Ordner mit dem Namen **c:\\mywar**, und speichern Sie die WAR-Datei unter http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war in **c:\\mywar**.

2.	Öffnen Sie die **WebLogic Server-Verwaltungskonsole** (http://localhost:7001/console). Geben Sie bei Aufforderung Ihren WebLogic-Benutzernamen und das Kennwort ein.

3.	Klicken Sie in der **WebLogic Server-Verwaltungskonsole** auf **Sperren und bearbeiten**, und klicken Sie auf **Bereitstellungen** und dann auf **Installieren**.

4.	Geben Sie den **Pfad** **c:\\myway\\shoppingcart.war** ein.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image18.png)

	Klicken Sie auf **Weiter**.

5.	Wählen Sie „Diese Bereitstellung als Anwendung installieren“, und klicken Sie dann auf **Weiter**.

6.	Klicken Sie auf **Fertig stellen**.

7.	Klicken Sie in der **WebLogic Server-Verwaltungskonsole** auf **Speichern** und dann auf **Änderungen aktivieren**.

8.	Klicken Sie auf **Bereitstellungen**, wählen Sie **shoppingcart** aus, und klicken Sie auf **Start** und dann auf **Alle Anforderungen bearbeiten**. Wenn Sie aufgefordert werden, die Auswahl zu bestätigen, klicken Sie auf **Ja**.

9.	Um die lokal ausgeführte Warenkorbanwendung zu sehen, öffnen Sie einen Browser mit der URL <http://localhost:7001/shoppingcart>.

10.	Erstellen Sie einen Endpunkt für den virtuellen Computer:

	1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.

	2.	Klicken Sie auf **Durchsuchen**.

	3.	Klicken Sie auf **Virtuelle Computer**.

	4.	Wählen Sie den virtuellen Computer aus.

	5.	Klicken Sie auf **Einstellungen**.

	6.	Klicken Sie auf **Endpunkte**.

	7.	Klicken Sie auf **Hinzufügen**.

	8.	Geben Sie einen Namen für den Endpunkt an:

		1. Verwenden Sie **TCP** als Protokoll.

		2. Verwenden Sie Port **80** als öffentlichen Port.

		3. Verwenden Sie Port **7001** als privaten Port.

	9.	Lassen Sie die übrigen Optionen unverändert.

	10. Klicken Sie auf **OK**.

11.	Lassen Sie eine eingehende Verbindung über die Firewall für Port 7001 zu.

	1.	Melden Sie sich am virtuellen Computer an.

	2.	Klicken Sie auf die Windows-Schaltfläche **Start**, geben Sie **Windows-Firewall mit erweiterter Sicherheit** ein, und klicken Sie dann auf das Symbol **Windows-Firewall mit erweiterter Sicherheit**. Die Verwaltungskonsole für die **Windows-Firewall mit erweiterter Sicherheit** wird geöffnet.

	3.	Klicken Sie in der Firewall-Verwaltungskonsole im linken Bereich auf **Eingehende Regeln** (wenn **Eingehende Regeln** nicht angezeigt wird, erweitern Sie den obersten Knoten im linken Bereich), und klicken Sie dann auf „Neue Regel“ im rechten Bereich.

	4.	Wählen Sie für **Regeltyp** die Option **Port** aus, und klicken Sie auf **Weiter**.

	5.	Legen Sie für **Protokoll und Port** die Option **TCP** fest, wählen Sie **Bestimmte lokale Ports** aus, geben Sie **7001** für den Port ein, und klicken Sie dann auf **Weiter**.

	6.	Klicken Sie auf **Verbindung zulassen** und anschließend auf **Weiter**.

	7.	Übernehmen Sie die Standardeinstellungen für die Profile, für die die Regel gilt, und klicken Sie auf **Weiter**.

	8.	Geben Sie einen Namen für die Regel und optional eine Beschreibung ein, und klicken Sie dann auf **Fertig stellen**.

12.	Um die ausgeführte Warenkorbanwendung im Internet anzuzeigen, öffnen Sie einen Browser mit der URL in Form von `http://<<unique_domain_name>>/shoppingcart`. (Sie können den Wert für <<*unique\_domain\_name*>> im [Azure-Portal](https://ms.portal.azure.com/) festlegen, indem Sie auf **Virtuelle Computer** klicken und dann den virtuellen Computer auswählen, den Sie zum Ausführen von Oracle WebLogic Server verwenden.)


##Zusätzliche Ressourcen
Nachdem Sie den virtuellen Computer mit Oracle WebLogic Server eingerichtet haben, lesen Sie die folgenden Themen, um weitere Informationen zu erhalten.

-	[Images virtueller Oracle-Computer – verschiedene Überlegungen](virtual-machines-windows-classic-oracle-considerations.md)

-	[Oracle WebLogic Server-Produktdokumentation](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

-	[Verwendung von Oracle WebLogic Server 12c mit Linux in Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

-	[Oracle Virtual Machine images for Azure (Images von virtuellen Oracle-Computern für Azure; in englischer Sprache)](virtual-machines-linux-classic-oracle-images.md)

<!---HONumber=AcomDC_0601_2016-->