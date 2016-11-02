<properties
	pageTitle="Always Encrypted – Schützen von vertraulichen Daten in Azure SQL-Datenbank mithilfe der Datenbankverschlüsselung | Microsoft Azure"
	description="Schützen Sie vertrauliche Daten in der SQL-Datenbank in wenigen Minuten."
	keywords="Verschlüsselung, Verschlüsselungsschlüssel, Cloud-Verschlüsselung"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="cgronlun"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/18/2016"
	ms.author="sstein"/>

# Always Encrypted – Schützen von vertraulichen Daten in SQL-Datenbank und Speichern der Verschlüsselungsschlüssel in Azure Key Vault

> [AZURE.SELECTOR]
- [Azure-Schlüsseltresor](sql-database-always-encrypted-azure-key-vault.md)
- [Windows-Zertifikatspeicher](sql-database-always-encrypted.md)


In diesem Artikel erfahren Sie, wie Sie vertrauliche Daten in einer SQL-Datenbank mithilfe der Datenverschlüsselung unter Einsatz des [Always-Encrypted-Assistenten](https://msdn.microsoft.com/library/mt459280.aspx) in [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx) sichern. Er enthält auch Anweisungen, die zeigen, wie Sie jeden Verschlüsselungsschlüssel in Azure Key Vault speichern.

Always Encrypted ist eine neue Datenverschlüsselungstechnologie in Azure SQL-Datenbank und SQL Server, mit der vertrauliche ruhende Daten auf dem Server, auf dem Weg zwischen Client und Server und während der Verwendung geschützt werden. Always Encrypted stellt sicher, dass vertrauliche Daten im Datenbanksystem niemals im Klartextformat angezeigt werden. Nachdem Sie die Datenverschlüsselung konfiguriert haben, können nur Clientanwendungen oder App-Server, die Zugriff auf die Schlüssel haben, auf Klartextdaten zugreifen. Ausführliche Informationen finden Sie unter [Always Encrypted (Database Engine)](https://msdn.microsoft.com/library/mt163865.aspx).


Nach dem Konfigurieren der Datenbank für die Verwendung von Always Encrypted erstellen Sie eine Clientanwendung in C# mit Visual Studio, um mit den verschlüsselten Daten zu arbeiten.

Führen Sie die Schritte in diesem Artikel aus, um zu lernen, wie Sie Always Encrypted für eine Azure SQL-Datenbank einrichten. In diesem Artikel erlernen Sie die Vorgehensweise für folgende Aufgaben:

- Verwenden des Always Encrypted-Assistenten in SSMS zum Erstellen von [Always Encrypted-Schlüsseln](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
    - Erstellen eines Spaltenhauptschlüssels ([Column Master Key, CMK](https://msdn.microsoft.com/library/mt146393.aspx)).
    - Erstellen eines Spaltenverschlüsselungsschlüssels ([Column Encryption Key, CEK](https://msdn.microsoft.com/library/mt146372.aspx)).
- Erstellen einer Datenbanktabelle und Verschlüsseln von Spalten.
- Erstellen einer Anwendung zum Einfügen, Auswählen und Anzeigen von Daten aus den verschlüsselten Spalten


## Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Ein Azure-Konto und ein Azure-Abonnement. Falls Sie diese benötigen, können Sie sich für eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/) registrieren.
- [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) Version 13.0.700.242 oder höher.
- [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) oder höher (auf dem Clientcomputer)
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- [Azure PowerShell](../powershell-install-configure.md), Version 1.0 oder höher. Geben Sie **(Get-Module Azure -ListAvailable).Version** ein, um herauszufinden, welche Version von PowerShell ausgeführt wird.



## Aktivieren der Clientanwendung für den Zugriff auf den SQL-Datenbank-Dienst

Sie müssen Ihre Clientanwendung aktivieren, um auf den SQL-Datenbank-Dienst zuzugreifen. Richten Sie dazu die erforderliche Authentifizierung ein, und rufen Sie die *ClientId* und das *Geheimnis* ab, die Sie zum Authentifizieren der Anwendung im folgenden Code benötigen.

1. Öffnen Sie das [klassische Azure-Portal](http://manage.windowsazure.com).
2. Wählen Sie **Active Directory**, und klicken Sie auf die Active Directory-Instanz, die Ihre Anwendung verwendet.
3. Klicken Sie auf **Anwendungen** und dann auf **ADD**.
4. Geben Sie einen Namen für die Anwendung ein (z.B. *myClientApp*), wählen Sie **WEBANWENDUNG**, und klicken Sie auf den Pfeil, um den Vorgang fortzusetzen.
5. Unter **URL für Anmeldung** und **APP-ID-URI** können Sie einfach eine gültige URL eingeben (z.B. *http://myClientApp*) und dann fortfahren.
6. Klicken Sie auf **KONFIGURIEREN**.
7. Kopieren Sie Ihre **CLIENT-ID**. (Sie benötigen diesen Wert später für Ihren Code.)
8. Wählen Sie im Abschnitt **Schlüssel** in der Dropdownliste **Dauer auswählen** die Option **1 Jahr** aus. (Sie kopieren den Schlüssel, nachdem Sie in Schritt 14 gespeichert haben.)
11. Scrollen Sie nach unten, und klicken Sie auf **Anwendung hinzufügen**.
12. Behalten Sie für **ANZEIGEN** die Festlegung **Microsoft-Apps** bei, und wählen Sie **Microsoft Azure-Dienstverwaltung**. Klicken Sie auf das Häkchen, um fortzufahren.
13. Wählen Sie **Zugriff auf Azure-Dienstverwaltung** in der Dropdownliste **Delegierte Berechtigungen** aus.
14. Klicken Sie auf **SPEICHERN**.
15. Nachdem der Speichervorgang abgeschlossen ist, kopieren Sie den Schlüsselwert in den Abschnitt **Schlüssel**. (Sie benötigen diesen Wert später für Ihren Code.)



## Erstellen eines Schlüsseltresors zum Speichern Ihrer Schlüssel

Da nun die Client-App konfiguriert ist und Sie über die Client-ID verfügen, ist es an der Zeit, einen Schlüsseltresor zu erstellen und die Zugriffsrichtlinie zu konfigurieren, damit Sie und Ihre Anwendung auf die geheimen Schlüssel im Tresor (Always Encrypted-Schlüssel) zugreifen können. Die Berechtigungen *create*, *get*, *list*, *sign*, *verify*, *wrapKey* und *unwrapKey* sind zum Erstellen eines neuen Spaltenhauptschlüssels sowie zum Einrichten der Verschlüsselung mit SQL Server Management Studio erforderlich.

Sie können schnell einen Schlüsseltresor erstellen, indem Sie das folgende Skript ausführen. Eine ausführliche Erläuterung dieser Cmdlets und weitere Informationen zum Erstellen und Konfigurieren eines Schlüsseltresors finden Sie unter [Erste Schritte mit Azure Key Vault](../Key-Vault/key-vault-get-started.md).



    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $clientId = '<client ID that you copied in step 7 above>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'


    Login-AzureRmAccount
    $subscriptionId = (Get-AzureRmSubscription -SubscriptionName $subscriptionName).SubscriptionId
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup –Name $resourceGroupName –Location $location
    New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzureRmKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $clientId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list




## Erstellen einer leeren SQL-­Datenbank
1. Melden Sie sich auf dem [Azure-Portal](https://portal.azure.com/) an.
2. Navigieren Sie zu **Neu** > **Daten + Speicher** > **SQL-Datenbank**.
3. Erstellen Sie eine **leere** Datenbank mit dem Namen **Clinic** auf einem neuen oder vorhandenen Server. Eine ausführliche Anleitung zum Erstellen einer Datenbank im Azure-Portal finden Sie unter [Erstellen einer SQL-Datenbank in wenigen Minuten](sql-database-get-started.md).

	![Leere Datenbank erstellen](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Sie benötigen die Verbindungszeichenfolge später im Tutorial – navigieren Sie also nach dem Erstellen der Datenbank zur neuen Clinic-Datenbank, und kopieren Sie die Verbindungszeichenfolge. Sie können die Verbindungszeichenfolge zu einem beliebigen Zeitpunkt abrufen, aber sie lässt sich im Azure-Portal einfach kopieren.

1. Navigieren Sie zu **SQL-Datenbanken** > **Clinic** > **Datenbank-Verbindungszeichenfolgen anzeigen**.
2. Kopieren Sie die Verbindungszeichenfolge für **ADO.NET**.

	![Verbindungszeichenfolge kopieren](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)


## Herstellen einer Verbindung für die Datenbank mit SSMS

Öffnen Sie SSMS, und stellen Sie für die Clinic-Datenbank eine Verbindung mit dem Server her.


1. Öffnen Sie SSMS. (Navigieren Sie zu **Verbinden** > **Datenbankmodul**, um das Fenster **Mit Server verbinden** zu öffnen, falls es nicht geöffnet ist.)
2. Geben Sie Ihren Servernamen und die Anmeldeinformationen ein. Den Servernamen finden Sie auf dem Blatt „SQL-Datenbank“ und in der zuvor kopierten Verbindungszeichenfolge. Geben Sie den vollständigen Servernamen ein, einschließlich *database.windows.net*.

	![Verbindungszeichenfolge kopieren](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Wenn das Fenster **Neue Firewallregel** geöffnet wird, melden Sie sich bei Azure an und lassen SSMS eine neue Firewallregel für Sie erstellen.


## Erstellen einer Tabelle

In diesem Abschnitt erstellen Sie eine Tabelle zum Speichern von Patientendaten. Sie ist anfänglich nicht verschlüsselt – im nächsten Abschnitt konfigurieren Sie die Verschlüsselung.

1. Erweitern Sie **Datenbanken**.
1. Klicken Sie mit der rechten Maustaste auf die Datenbank **Clinic**, und klicken Sie dann auf **Neue Abfrage**.
2. Fügen Sie den folgenden Transact-SQL-Code (T-SQL) in das Fenster mit der neuen Abfrage ein, und wählen Sie **Ausführen**.


        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## Verschlüsseln von Spalten (Konfigurieren von Always Encrypted)

SSMS enthält einen Assistenten zum einfachen Konfigurieren von Always Encrypted, indem Spaltenhauptschlüssel, Spaltenverschlüsselungsschlüssel und verschlüsselte Spalten für Sie eingerichtet werden.

1. Erweitern Sie **Datenbanken** > **Clinic** > **Tabellen**.
2. Klicken Sie mit der rechten Maustaste auf die Tabelle **Patients**, und wählen Sie **Spalten verschlüsseln**, um den Always Encrypted-Assistenten zu öffnen:

    ![Spalten verschlüsseln](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

Der Always Encrypted-Assistent umfasst die folgenden Abschnitte: **Spaltenauswahl**, **Konfiguration des Hauptschlüssels**, **Überprüfung** und **Zusammenfassung**.

### Spaltenauswahl##

Klicken Sie auf der Seite **Einführung** auf **Weiter**, um die Seite **Spaltenauswahl** zu öffnen. Wählen Sie auf dieser Seite die Spalten, die Sie verschlüsseln möchten, [den Typ der Verschlüsselung, und welcher Spaltenverschlüsselungsschlüssel (Column Encryption Key, CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) verwendet werden soll.

Verschlüsseln Sie für jeden Patienten die Daten **SSN** (US-Sozialversicherungsnummer) und **BirthDate** (Geburtsdatum). Für die Spalte „SSN“ wird die deterministische Verschlüsselung verwendet, bei der Suchvorgänge nach Gleichheit, Joins und Group By unterstützt werden. Für die Spalte „BirthDate“ wird die zufällige Verschlüsselung verwendet, bei der keine Operationen unterstützt werden.

Legen Sie als **Verschlüsselungstyp** für die Spalte „SSN“ **Deterministisch** und für die Spalte BirthDate **Zufällig** fest. Klicken Sie auf **Weiter**.

![Spalten verschlüsseln](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### Hauptschlüsselkonfiguration###

Auf der Seite **Hauptschlüsselkonfiguration** richten Sie Ihren CMK ein und wählen den Schlüsselspeicheranbieter aus, unter dem der CMK gespeichert werden soll. Derzeit können Sie einen CMK im Windows-Zertifikatspeicher, im Azure-Schlüsseltresor oder in einem Hardwaresicherheitsmodul (HSM) speichern.

Dieses Tutorial zeigt, wie Sie Ihre Schlüssel in Azure Key Vault speichern.

1.     Wählen Sie **Azure Key Vault** aus.
1.     Wählen Sie in der Dropdownliste den gewünschten Schlüsseltresor aus.
1.     Klicken Sie auf **Next**.

![Hauptschlüsselkonfiguration](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)


### Überprüfen###

Sie können die Spalten jetzt verschlüsseln oder ein PowerShell-Skript für die spätere Ausführung speichern. Wählen Sie in diesem Tutorial die Option **Jetzt fertig stellen**, und klicken Sie auf **Weiter**.

### Zusammenfassung ###

Überprüfen Sie, ob alle Einstellungen richtig sind, und klicken Sie auf **Fertig stellen**, um die Einrichtung von Always Encrypted abzuschließen.


![Zusammenfassung](./media/sql-database-always-encrypted-azure-key-vault/summary.png)


### Überprüfen der Aktionen des Assistenten

Nach Abschluss des Assistenten ist die Datenbank für Always Encrypted eingerichtet. Der Assistent führte die folgenden Aktionen aus:

- Erstellung eines Spaltenhauptschlüssels und Speicherung in Ihrem Azure Key Vault.
- Erstellung eines Spaltenverschlüsselungsschlüssels und Speicherung in Ihrem Azure Key Vault.
- Konfigurieren der ausgewählten Spalten für die Verschlüsselung Die Tabelle „Patients“ enthält derzeit noch keine Daten, aber alle vorhandenen Daten in den ausgewählten Spalten wären jetzt verschlüsselt.

Sie können die Erstellung der Schlüssel in SSMS überprüfen, indem Sie die Optionen **Clinic** > **Sicherheit** > **Always Encrypted-Schlüssel** erweitern.


## Erstellen einer Clientanwendung für die Verwendung der verschlüsselten Daten

Nach dem Einrichten von Always Encrypted können Sie eine Anwendung erstellen, die *Einfüge-* und *Auswahlvorgänge* in den verschlüsselten Spalten durchführt.

> [AZURE.IMPORTANT] Ihre Anwendung muss [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx)-Objekte verwenden, wenn Klartextdaten mit Always Encrypted-Spalten an den Server übergeben werden. Das Übergeben von Literalwerten ohne SqlParameter-Objekte führt zu einer Ausnahme.

1. Öffnen Sie Visual Studio, und erstellen Sie eine neue C#-Konsolenanwendung. Stellen Sie sicher, dass Ihr Projekt auf **.NET Framework 4.6** oder höher festgelegt ist.
2. Geben Sie dem Projekt den Namen **AlwaysEncryptedConsoleAKVApp**, und klicken Sie auf **OK**. ![Neue Konsolenanwendung](./media/sql-database-always-encrypted-azure-key-vault/console-app.png)
3. Installieren Sie die NuGet-Pakete, indem Sie zu **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** navigieren.

Führen Sie diese beiden Codezeilen in der Paket-Manager-Konsole aus.

    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory



## Ändern der Verbindungszeichenfolge zur Aktivierung von Always Encrypted

In diesem Abschnitt wird beschrieben, wie Sie Always Encrypted in Ihrer Datenbank-Verbindungszeichenfolge aktivieren.


Zum Aktivieren von Always Encrypted müssen Sie der Verbindungszeichenfolge das Schlüsselwort **Column Encryption Setting** hinzufügen und es auf **Enabled** festlegen.

Sie können dies direkt in der Verbindungszeichenfolge festlegen, oder Sie können zum Festlegen ein [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx)-Element verwenden. Die Beispielanwendung im nächsten Abschnitt veranschaulicht, wie Sie das **SqlConnectionStringBuilder**-Element verwenden.



### Aktivieren von Always Encrypted in der Verbindungszeichenfolge

Fügen Sie der Verbindungszeichenfolge das folgende Schlüsselwort hinzu.

    Column Encryption Setting=Enabled


### Aktivieren von Always Encrypted per SqlConnectionStringBuilder

Der folgende Code zeigt, wie Sie Always Encrypted aktivieren, indem Sie [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) auf [Enabled](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx) festlegen.

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## Registrieren des Azure-Schlüsseltresor-Anbieters

Der folgende Code zeigt, wie Sie den Azure Key Vault-Anbieter mit dem ADO.NET-Treiber registrieren.

    private static ClientCredential _clientCredential;

    static void InitializeAzureKeyVaultProvider()
    {
       _clientCredential = new ClientCredential(clientId, clientSecret);

       SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
          new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

       Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
          new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

       providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
       SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
    }



## Always Encrypted-Beispielkonsolenanwendung

In diesem Beispiel wird Folgendes veranschaulicht:

- Ändern der Verbindungszeichenfolge zur Aktivierung von Always Encrypted
- Registrieren Sie Azure Key Vault als Schlüsselspeicheranbieter für die Anwendung.
- Einfügen von Daten in die verschlüsselten Spalten
- Auswählen eines Datensatzes per Filterung nach einem bestimmten Wert in einer verschlüsselten Spalte

Ersetzen Sie den Inhalt von **Program.cs** durch den folgenden Code. Ersetzen Sie die Verbindungszeichenfolge für die globale connectionString-Variable in der Zeile direkt vor der Main-Methode durch Ihre gültige Verbindungszeichenfolge aus dem Azure-Portal. Dies ist die einzige Änderung, die Sie an diesem Code vornehmen müssen.

Führen Sie die App aus, um Always Encrypted in Aktion zu erleben.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

    namespace AlwaysEncryptedConsoleAKVApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string clientId = @"<client id from step 7 above>";
        static string clientSecret = "<key from step 13 above>";


        static void Main(string[] args)
        {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider()
        {

            _clientCredential = new ClientCredential(clientId, clientSecret);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope)
        {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }



## Überprüfen, ob die Daten verschlüsselt sind

Sie können schnell überprüfen, ob die eigentlichen Daten auf dem Server verschlüsselt sind, indem Sie die Patientendaten mit SSMS abfragen (mit der aktuellen Verbindung, für die **Column Encryption Setting** noch nicht aktiviert ist).

Führen Sie die folgende Abfrage für die Clinic-Datenbank aus.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Sie sehen, dass die verschlüsselten Spalten keine Klartextdaten enthalten.

   ![Neue Konsolenanwendung](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)


Um SSMS zu verwenden, um auf die Klartextdaten zuzugreifen, können Sie der Verbindung den Parameter *Column Encryption Setting=enabled* hinzufügen.

1. Klicken Sie in SSMS im **Objekt-Explorer** mit der rechten Maustaste auf Ihren Server, und wählen Sie **Trennen**.
2. Klicken Sie auf **Verbinden** > **Datenbankmodul**, um das Fenster **Mit Server verbinden** zu öffnen, und klicken Sie dann auf **Optionen**.
3. Klicken Sie auf **Zusätzliche Verbindungsparameter**, und geben Sie **Column Encryption Setting=enabled** ein.

	![Neue Konsolenanwendung](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)

4. Führen Sie die folgende Abfrage für die Clinic-Datenbank aus.

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Die Klartextdaten werden jetzt in den verschlüsselten Spalten angezeigt.


	![Neue Konsolenanwendung](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## Nächste Schritte
Nach dem Erstellen einer Datenbank, für die Always Encrypted verwendet wird, sollten Sie folgende Schritte ausführen:

- [Lassen Sie Ihre Schlüssel rotieren, und bereinigen Sie sie](https://msdn.microsoft.com/library/mt607048.aspx).
- [Migrieren Sie Daten, die bereits mit Always Encrypted verschlüsselt sind](https://msdn.microsoft.com/library/mt621539.aspx).


## Verwandte Informationen

- [Always Encrypted (Cliententwicklung)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Transparent Data Encryption](https://msdn.microsoft.com/library/bb934049.aspx)
- [SQL Server-Verschlüsselung](https://msdn.microsoft.com/library/bb510663.aspx)
- [Always Encrypted-Assistent](https://msdn.microsoft.com/library/mt459280.aspx)
- [Always Encrypted-Blog](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

<!---HONumber=AcomDC_0824_2016-->