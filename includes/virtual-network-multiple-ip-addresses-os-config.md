## <a name="a-nameos-configaadd-ip-addresses-to-a-vm-operating-system"></a><a name="os-config"></a>Hinzufügen von IP-Adressen zu einem VM-Betriebssystem

Stellen Sie eine Verbindung mit einem virtuellen Computer her, den Sie mit mehreren privaten IP-Adresse erstellt haben, und melden Sie sich an. Alle dem virtuellen Computer hinzugefügten privaten IP-Adressen müssen manuell hinzugefügt werden. (Das gilt auch für die primäre Adresse.) Führen Sie für das Betriebssystem Ihres virtuellen Computers folgende Schritte aus:

### <a name="windows"></a>Windows

1. Geben Sie an einer Eingabeaufforderung den Befehl *ipconfig /all*ein.  Sie können nur die *primäre* private Adresse anzeigen (über DHCP).
2. Geben Sie an der Eingabeaufforderung *ncpa.cpl* ein, um das Fenster **Netzwerkverbindungen** zu öffnen.
3. Öffnen Sie die Eigenschaften für den entsprechenden Adapter: **LAN-Verbindung**.
4. Doppelklicken Sie auf „Internetprotokoll, Version 4“ (IPv4).
5. Wählen Sie **Folgende IP-Adresse verwenden** aus, und geben Sie die folgenden Werte ein:

    * **IP-Adresse:**Geben Sie die *primäre* private IP-Adresse ein.
    * **Subnetzmaske:**wird basierend auf dem Subnetz festgelegt. Wenn das Subnetz beispielsweise ein /24-Subnetz ist, lautet die Subnetzmaske „255.255.255.0“.
    * **Standardgateway:**die erste IP-Adresse im Subnetz. Wenn das Subnetz „10.0.0.1/24“ ist, lautet die Gateway-IP-Adresse „10.0.0.0/24“.
    * Klicken Sie auf **Folgende DNS-Serveradressen verwenden** , und geben Sie die folgenden Werte ein:
        * **Bevorzugter DNS-Server:** Geben Sie „168.63.129.16“ ein, falls Sie nicht Ihren eigenen DNS-Server verwenden.  Wenn Sie Ihren eigenen DNS-Server verwenden, geben Sie die IP-Adresse für Ihren Server ein.
    * Klicken Sie auf die Schaltfläche **Erweitert** , und fügen Sie zusätzliche IP-Adressen hinzu. Fügen Sie jede der in Schritt 8 aufgeführten sekundären privaten IP-Adressen der NIC mit dem gleichen Subnetz hinzu, das für die primäre IP-Adresse angegeben ist.
        >[!WARNING] 
        >Wenn Sie die oben genannten Schritte nicht ordnungsgemäß befolgen, verlieren Sie möglicherweise Konnektivität mit Ihren virtuellen Computer. Vergewissern Sie sich, dass die für Schritt 5 eingegebenen Informationen korrekt sind, ehe Sie fortfahren.

    * Klicken Sie auf **OK**, um die TCP/IP-Einstellungen zu schließen, und dann erneut auf **OK**, um die Adaptereinstellungen zu schließen. Die RDP-Verbindung wird wiederhergestellt.

6. Geben Sie an einer Eingabeaufforderung den Befehl *ipconfig /all*ein. Alle hinzugefügten IP-Adressen werden angezeigt, und DHCP ist deaktiviert.


### <a name="validation-windows"></a>Überprüfung (Windows)

Um sicherzustellen, dass Sie mit Ihrer sekundären IP-Konfiguration über die ihr zugeordneten öffentlichen IP-Adresse eine Verbindung mit dem Internet herstellen können, sobald Sie sie anhand der oben beschriebenen Schritte ordnungsgemäß hinzugefügt haben, verwenden den folgenden Befehl:

```bash
ping -S 10.0.0.5 hotmail.com
```
>[!NOTE]
>Sie können das Internet nur pingen, wenn der oben erwähnten privaten IP-Adresse, die Sie verwenden, eine öffentliche IP-Adresse zugeordnet ist.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Öffnen Sie ein Terminalfenster.
2. Stellen Sie sicher, dass Sie der root-Benutzer sind. Geben Sie andernfalls den folgenden Befehl ein:

    ```bash
    sudo -i
    ```

3. Aktualisieren Sie die Konfigurationsdatei der Netzwerkschnittstelle (es wird von „eth0“ ausgegangen).

    * Behalten Sie den vorhandenen Eintrag für DHCP bei. Die Konfiguration der primären IP-Adresse bleibt unverändert.
    * Fügen Sie mit den folgenden Befehlen eine Konfiguration für eine weitere statische IP-Adresse hinzu:

        ```bash
        cd /etc/network/interfaces.d/
        ls
        ```

    Es sollte eine CFG-Datei angezeigt werden.
4. Öffnen Sie die Datei. Am Ende der Datei sollten die folgenden Zeilen angezeigt werden:

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. Fügen Sie nach den vorhandenen Zeilen in dieser Datei die folgenden Zeilen ein:

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    netmask <your subnet mask>
    ```

6. Speichern Sie die Datei mit dem folgenden Befehl:

    ```bash
    :wq
    ```

7. Setzen Sie die Netzwerkschnittstelle mit dem folgenden Befehl zurück:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > Führen Sie bei Verwendung einer Remoteverbindung „ifdown“ und „ifup“ in der gleichen Zeile aus.
    >

8. Geben Sie den folgenden Befehl ein, um zu überprüfen, ob die IP-Adresse der Netzwerkschnittstelle hinzugefügt wurde:

    ```bash
    ip addr list eth0
    ```

    Es sollte die IP-Adresse angezeigt werden, die Sie als Teil der Liste hinzugefügt haben.

### <a name="linux-redhat-centos-and-others"></a>Linux (Redhat, CentOS u.a.)

1. Öffnen Sie ein Terminalfenster.
2. Stellen Sie sicher, dass Sie der root-Benutzer sind. Geben Sie andernfalls den folgenden Befehl ein:

    ```bash
    sudo -i
    ```

3. Geben Sie Ihr Kennwort ein, und befolgen Sie die entsprechenden Anweisungen. Nachdem Sie der root-Benutzer sind, navigieren Sie mit dem folgenden Befehl zum Ordner der Netzwerkskripts:

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Listen Sie die zugehörigen ifcfg-Dateien mit dem folgenden Befehl auf:

    ```bash
    ls ifcfg-*
    ```

    Unter den Dateien sollte die Datei *ifcfg-eth0* angezeigt werden.

5. Erstellen Sie zum Hinzufügen einer IP-Adresse eine entsprechende Konfigurationsdatei, wie weiter unten gezeigt. Beachten Sie, dass für jede IP-Konfiguration eine Datei erstellt werden muss.

    ```bash
    touch ifcfg-eth0:0
    ```

6. Öffnen Sie die Datei *ifcfg-eth0:0* mithilfe des folgenden Befehls:

    ```bash
    vi ifcfg-eth0:0
    ```

7. Fügen Sie der Datei mithilfe des folgenden Befehls Inhalt hinzu (in diesem Fall: *Eth0:0*). Aktualisieren Sie die Informationen auf der Grundlage Ihrer IP-Adresse.

    ```bash
    DEVICE=eth0:0
    BOOTPROTO=static
    ONBOOT=yes
    IPADDR=192.168.101.101
    NETMASK=255.255.255.0
    ```

8. Speichern Sie die Datei mit dem folgenden Befehl:

    ```bash
    :wq
    ```

9. Starten Sie die Netzwerkdienste neu, und stellen Sie sicher, dass die Änderungen übernommen wurden. Führen Sie dazu die folgenden Befehle aus:

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    In der zurückgegebenen Liste sollte die hinzugefügte IP-Adresse *eth0:0*angezeigt werden.

### <a name="validation-linux"></a>Überprüfung (Linux)

Um sicherzustellen, dass Sie mit Ihrer sekundären IP-Konfiguration über die ihr zugeordneten öffentlichen IP-Adresse eine Verbindung mit dem Internet herstellen können, verwenden den folgenden Befehl:

```bash
ping -I 10.0.0.5 hotmail.com
```
>[!NOTE]
>Sie können das Internet nur pingen, wenn der oben erwähnten privaten IP-Adresse, die Sie verwenden, eine öffentliche IP-Adresse zugeordnet ist.

Wenn Sie ausgehende Verbindungen von einem sekundären NIC für virtuelle Linux-Computer überprüfen möchten, müssen Sie möglicherweise entsprechende Routen hinzufügen. Dazu gibt es zahlreiche Möglichkeiten. Weitere Informationen für Ihre Linux-Distribution finden Sie in der entsprechenden Dokumentation. Nachfolgend finden Sie eine der möglichen Methoden:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Achten Sie darauf, folgende Ersetzungen vorzunehmen:
    - **10.0.0.5** durch die private IP-Adresse, der eine öffentliche IP-Adresse zugeordnet ist
    - **10.0.0.1** durch Ihr Standardgateway
    - **eth2** durch dem Namen Ihrer sekundären NIC


<!--HONumber=Feb17_HO2-->


