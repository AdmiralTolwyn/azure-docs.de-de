
Jeder Endpunkt verfügt über einen *öffentlichen* und einen *privaten Port*:

- Der öffentliche Port wird von Azure-Lastenausgleich verwendet, um eingehenden Datenverkehr an den virtuellen Computer aus dem Internet zu überwachen.
- Der private Port wird vom virtuellen Computer zum Überwachen von eingehendem Datenverkehr verwendet, in der Regel für eine Anwendung oder ein Dienst, der auf dem virtuellen Computer ausgeführt wird.

Die Standardwerte für das IP-Protokoll und die TCP- oder UDP-Ports für bekannte Netzwerkprotokolle werden bereitgestellt, wenn Sie mit dem klassischen Azure-Portal Endpunkte erstellen. Bei benutzerdefinierten Endpunkten müssen Sie das richtige IP-Protokoll (TCP oder UDP) und die öffentlichen und privaten Ports angeben. Um nach dem Zufallsprinzip eingehenden Datenverkehr auf mehrere virtuelle Computer zu verteilen, müssen Sie einen Lastenausgleich, bestehend aus mehreren Endpunkten, erstellen.

Nachdem Sie einen Endpunkt erstellt haben, können Sie eine Zugriffssteuerungsliste (ACL) zum Definieren von Regeln verwenden, die eingehenden Datenverkehr an den öffentlichen Port des Endpunkts basierend der Quell-IP-Adresse zulassen oder verweigern. Wenn der virtuelle Computer in Azure ist, sollten Sie stattdessen Netzwerk-Sicherheitsgruppen verwenden. Weitere Informationen finden Sie unter [Informationen zu Netzwerksicherheitsgruppen](../articles/virtual-network/virtual-networks-nsg.md).

> [AZURE.NOTE]Firewallkonfiguration für virtuelle Computer in Azure erfolgt automatisch für Ports, die mit Remotekonnektivität-Endpunkten verknüpft sind, die Azure automatisch einrichtet. Für Ports, die für alle anderen Endpunkte angegeben werden, wird keine Konfiguration der Firewall des virtuellen Computers automatisch durchgeführt. Wenn Sie einen Endpunkt für den virtuellen Computer erstellen, müssen Sie sicherstellen, dass die Firewall des virtuellen Computers außerdem den Datenverkehr für das Protokoll und den privaten Port entsprechend der Endpunktkonfiguration ermöglicht. Informationen zum Konfigurieren der Firewall finden Sie in der Dokumentation oder der Onlinehilfe für das Betriebssystem, das auf dem virtuellen Computer ausgeführt wird.

## Erstellen eines Endpunkts

1.	Melden Sie sich beim [klassischen Azure-Portal](http://manage.windowsazure.com) an, falls noch nicht geschehen.
2.	Klicken Sie auf **Virtuelle Computer** und dann auf den Namen des virtuellen Computers, den Sie konfigurieren möchten.
3.	Klicken Sie auf **Endpunkte**. Auf der Seite **Endpunkte** sind alle aktuellen Endpunkte für den virtuellen Computer aufgelistet. (Dieses Beispiel ist eine Windows-VM. Eine Linux-VM weist standardmäßig einen Endpunkt für SSH auf.)

	![Endpunkte](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png)

4.	Klicken Sie auf der Taskleiste auf **Hinzufügen**.
5.	Wählen Sie auf der Seite **Einem virtuellen Computer einen Endpunkt hinzufügen** den Typ des Endpunkts aus.

	- Wenn Sie einen neuen Endpunkt erstellen, der nicht Teil eines Lastenausgleichs oder der erste Endpunkt in einem neuen Satz mit Lastenausgleich ist, wählen Sie **Eigenständigen Endpunkt hinzufügen** aus, und klicken Sie dann auf den Pfeil nach links.
	- Wählen Sie andernfalls **Endpunkt zu einer vorhandenen Gruppe mit Lastenausgleich hinzufügen** und den Namen des Satzes mit Lastenausgleich aus, und klicken Sie dann auf den Pfeil nach links. Geben Sie auf der Seite **Die Details des Endpunkts angeben** einen Namen für den Endpunkt an, und klicken Sie dann auf das Häkchen, um den Endpunkt zu erstellen.

6.	Geben Sie auf der Seite **Die Details des Endpunkts angeben** einen Namen für den Endpunkt in **Name** an. Sie können auch den Namen eines Protokolls aus der Liste auswählen, die Anfangswerte für **Protokoll**, **Öffentlicher Port** und **Privater Port** eintragen wird.
7.	Für einen benutzerdefinierten Endpunkt wählen Sie unter **Protokoll** entweder **TCP** oder **UDP** aus.
8.	Für angepasste Ports geben Sie unter **Öffentlicher Port** die Portnummer für den eingehenden Datenverkehr aus dem Internet ein. Geben Sie unter **Privater Port** die Nummer des Ports an, auf dem der virtuelle Computer überwacht. Diese Portnummern dürfen sich unterscheiden. Stellen Sie sicher, dass die Firewall auf dem virtuellen Computer konfiguriert wurde, um den Datenverkehr für das Protokoll (in Schritt 7) und den privaten Port zu ermöglichen.
9.	Wenn dieser Endpunkt die erste Instanz in einer Gruppe mit Lastenausgleich sein soll, klicken Sie auf **Gruppe mit Lastenausgleich erstellen** und dann auf den Pfeil nach rechts. Geben Sie auf der Seite **Gruppe mit Lastenausgleich konfigurieren** einen Lastenausgleichsnamen, ein Prüfpunkt-Protokoll und einen Port sowie das Prüfpunkt-Intervall und die Anzahl gesendeter Prüfpunkten an. Der Azure-Lastenausgleich sendet Prüfpunkte an die virtuellen Computer in einem Satz mit Lastenausgleich, um deren Verfügbarkeit zu überwachen. Der Azure-Lastenausgleich leitet keinen Datenverkehr zu virtuellen Maschinen weiter, die nicht auf die Überprüfung reagieren. Klicken Sie auf den Pfeil nach rechts.
10.	Aktivieren Sie das Kontrollkästchen, um den Endpunkt zu erstellen.

Der neue Endpunkt wird auf der Seite **Endpunkte** aufgeführt.

![Endpunkt erfolgreich erstellt](./media/virtual-machines-common-classic-setup-endpoints/endpointwindowsnew.png)

 

## Verwaltung der ACL für einen Endpunkt

Zum Definieren der Computer, die Datenverkehr senden können, kann die ACL für einen Endpunkt Datenverkehr anhand der IP-Quelladresse einschränken. Gehen Sie folgendermaßen vor, um eine ACL an einem Endpunkt hinzuzufügen, zu modifizieren oder zu entfernen.

> [AZURE.NOTE] Wenn der Endpunkt Teil eines Satzes mit Lastenausgleich ist, werden alle Änderungen, die Sie an der ACL oder an einem Endpunkt vornehmen, auf alle Endpunkte in diesem Satz angewendet.

Befindet sich der virtuelle Computer in einem virtuellen Azure-Netzwerk, sollten Sie anstelle von ACLs Netzwerksicherheitsgruppen verwenden. Weitere Informationen finden Sie unter [Informationen zu Netzwerksicherheitsgruppen](../articles/virtual-network/virtual-networks-nsg.md).

1.	Melden Sie sich beim klassischen Azure-Portal an, falls noch nicht geschehen.
2.	Klicken Sie auf **Virtuelle Computer** und dann auf den Namen des virtuellen Computers, den Sie konfigurieren möchten.
3.	Klicken Sie auf **Endpunkte**. Wählen Sie in der Liste den entsprechenden Endpunkt aus.

    ![ACL](./media/virtual-machines-common-classic-setup-endpoints/EndpointsShowsDefaultEndpointsForVM.png)

5.	Klicken Sie auf der Taskleiste auf **ACL verwalten**, um das Dialogfeld **ACL-Details festlegen** zu öffnen.

    ![ACL-Details festlegen](./media/virtual-machines-common-classic-setup-endpoints/EndpointACLdetails.png)

6.	Verwenden Sie die Zeilen in der Liste zum Hinzufügen, Löschen oder Bearbeiten von Regeln für eine ACL und Änderung ihrer Reihenfolge. Der Wert unter **Remotesubnetz** ist ein IP-Adressbereich für eingehenden Datenverkehr aus dem Internet, den der Azure Load Balancer verwendet, um Datenverkehr basierend auf der IP-Quelladresse zuzulassen oder zu verweigern. Sie müssen den IP-Adressbereich im CIDR-Format, auch bekannt als Adresspräfixformat, angeben. Ein Beispiel ist 131.107.0.0/16.

Sie können Regeln verwenden, um nur Verkehr von bestimmten Computern zuzulassen, die Ihren Computern im Internet entsprechen oder um Datenverkehr von bestimmten, bekannten Adressbereichen zu verweigern.

Die Regeln werden der Reihe nach, beginnend mit der ersten und endend mit der letzten Regel, bewertet. Dies bedeutet, dass die Regeln gemäß der Restriktivität geordnet werden sollen. Beispiele und weitere Informationen finden Sie unter [Was ist eine Netzwerk-Zugriffssteuerungsliste?](../articles/virtual-network/virtual-networks-acl.md).

<!---HONumber=AcomDC_0713_2016-->