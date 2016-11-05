
1. Geben Sie zum Eskalieren von Berechtigungen Folgendes ein:
   
        sudo -s
   
    Geben Sie das Kennwort ein.
2. Geben Sie Folgendes ein, um die MySQL Community Server-Edition zu installieren:
   
        zypper install mysql-community-server
   
    Warten Sie, bis MySQL heruntergeladen und installiert wurde.
3. Geben Sie Folgendes ein, damit MySQL beim Start des Systems gestartet wird:
   
        insserv mysql
4. Starten Sie den MySQL-Daemon (mysqld) manuell mit dem folgenden Befehl:
   
        rcmysql start
   
    Geben Sie Folgendes ein, um den Status des MySQL-Daemons zu prüfen:
   
        rcmysql status
   
    Geben Sie Folgendes ein, um den MySQL-Daemon zu beenden:
   
        rcmysql stop
   
   > [!IMPORTANT]
   > Nach der Installation ist das MySQL-Stammkennwort standardmäßig leer. Es wird empfohlen, das Skript **mysql\_secure\_installation** auszuführen, das bei der Sicherung von MySQL hilft. Sie werden vom Skript aufgefordert, das MySQL-Stammkennwort zu ändern, anonyme Benutzerkonten zu entfernen, Remote-Stammanmeldeinformationen zu deaktivieren, Testdatenbanken zu entfernen und die Berechtigungstabelle erneut zu laden. Es wird empfohlen, all diese Optionen mit "Ja" zu beantworten und das Stammkennwort zu ändern.
   > 
   > 
5. Geben Sie Folgendes ein, um das Skript für die Installation von MySQL auszuführen:
   
        mysql_secure_installation
6. Anmelden bei MySQL:
   
        mysql -u root -p
   
    Geben Sie das MySQL-Stammkennwort ein, das Sie im vorherigen Schritt geändert haben. Daraufhin wird eine Eingabeaufforderung angezeigt, über die Sie SQL-Anweisungen starten können, um mit der Datenbank zu interagieren.
7. Führen Sie zum Erstellen eines neuen MySQL-Benutzers bei der **mysql>**-Eingabeaufforderung den folgenden Befehl aus:
   
        CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    Beachten Sie, dass das Semikolon (;) am Ende der Zeilen entscheidend für den Abschluss der Befehle ist.
8. Geben Sie die folgenden Befehle aus, um eine Datenbank zu erstellen und um dafür die `mysqluser`-Benutzerberechtigungen zu gewähren:
   
        CREATE DATABASE testdatabase;
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    Beachten Sie, dass die Benutzernamen und Kennwörter der Datenbank nur von Skripten verwendet werden, die eine Verbindung zur Datenbank herstellen. Benutzernamen für Datenbankkonten sind nicht notwendigerweise tatsächliche Benutzerkonten im System.
9. Um sich von einem anderen Computer aus anzumelden, geben Sie Folgendes ein:
   
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';
   
    Hierbei steht `ip-address` für die IP-Adresse des Computers, von dem aus Sie eine Verbindung mit MySQL herstellen möchten.
10. Geben Sie Folgendes ein, um das Verwaltungsdienstprogramm für die MySQL-Datenbank zu beenden:
    
        quit

## Hinzufügen eines Endpunkts
1. Nachdem MySQL installiert wurde, müssen Sie für den Remotezugriff auf MySQL einen Endpunkt konfigurieren. Melden Sie sich beim [klassischen Azure-Portal][AzurePortal] an. Klicken Sie auf **Virtual Machines**, klicken Sie auf den Namen des neuen virtuellen Computers und danach auf **Endpunkte**.
2. Klicken Sie unten auf der Seite auf **Hinzufügen**.
3. Fügen Sie einen Endpunkt mit dem Namen "MySQL" hinzu, und wählen Sie als Protokoll **TCP** aus. Setzen Sie die beiden Ports **Öffentlich** und **Privat** auf "3306".
4. Um von Ihrem Computer aus eine Remoteverbindung mit dem virtuellen Computer herzustellen, geben Sie Folgendes ein:
   
        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net
   
    Wenn Sie den in diesem Tutorial erstellten virtuellen Computer verwenden, geben Sie folgenden Befehl ein:
   
        mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePortal]: http://manage.windowsazure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png

<!---HONumber=AcomDC_0128_2016-->