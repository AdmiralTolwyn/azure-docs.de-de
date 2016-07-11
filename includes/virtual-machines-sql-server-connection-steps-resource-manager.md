### Konfigurieren einer DNS-Bezeichnung für die öffentliche IP-Adresse

Um über das Internet eine Verbindung mit dem SQL Server-Datenbankmodul herzustellen, konfigurieren Sie zunächst eine DNS-Bezeichnung für Ihre öffentliche IP-Adresse.

> [AZURE.NOTE] DNS-Bezeichnungen sind nicht erforderlich, wenn Sie nur eine Verbindung mit der SQL Server-Instanz im gleichen virtuellen Netzwerk oder nur eine lokale Verbindung herstellen möchten.

Wählen Sie zum Erstellen einer DNS-Bezeichnung als Erstes im Portal die Option **Virtuelle Computer** aus. Wählen Sie die SQL Server-VM aus, um deren Eigenschaften anzuzeigen.

1. Wählen Sie im Blatt für den virtuellen Computer Ihre **Öffentliche IP-Adresse** aus.

	![Öffentliche IP-Adresse](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)

2. Erweitern Sie in den Eigenschaften für die öffentliche IP-Adresse die Option **Konfiguration**.

3. Geben Sie eine DNS-Bezeichnung ein. Hierbei handelt es sich um einen A-Eintrag, mit dem eine Verbindung mit Ihrer SQL Server-VM nach dem Namen anstatt direkt nach der IP-Adresse hergestellt werden kann.

4. Klicken Sie auf die Schaltfläche **Save**.

	![DNS-Bezeichnung](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### Verbinden mit dem Datenbankmodul von einem anderen Computer aus

1. Öffnen Sie SQL Server Management Studio (SSMS) auf einem mit dem Internet verbundenen Computer.

2. Bearbeiten Sie im Dialogfeld **Mit Server verbinden** oder **Verbindung mit Datenbankmodul herstellen** den Wert **Servername**. Geben Sie den vollständigen DNS-Namen des virtuellen Computers ein (in der vorherigen Aufgabe bestimmt).

3. Wählen Sie im Feld **Authentifizierung** den Eintrag **SQL Server-Authentifizierung**.

5. Geben Sie im Feld **Anmelden** einen gültigen SQL-Anmeldenamen ein.

6. Geben Sie im Feld **Kennwort** das Kennwort für die Anmeldung ein.

7. Klicken Sie auf **Verbinden**.

	![SSMS-Verbindung](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)

<!---HONumber=AcomDC_0629_2016-->