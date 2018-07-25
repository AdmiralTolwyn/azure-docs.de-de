---
title: Erstellen eines Azure SQL-Datenbank-Agents für elastische Aufträge mithilfe von PowerShell | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe von PowerShell einen Agent für elastische Aufträge erstellen.
services: sql-database
author: johnpaulkee
manager: craigg
ms.service: sql-database
ms.topic: tutorial
ms.date: 06/14/2018
ms.author: joke
ms.openlocfilehash: 2cc8db0ce849e0f0d376824665aac7dbc2af29db
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035216"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>Erstellen eines Agents für elastische Aufträge mithilfe von PowerShell

Mit [elastischen Aufträgen](elastic-jobs-overview.md) können einzelne oder mehrere T-SQL-Skripts (Transact-SQL) für mehrere Datenbanken gleichzeitig ausgeführt werden.

In diesem Tutorial erfahren Sie, wie Sie eine datenbankübergreifende Abfrage ausführen:

> [!div class="checklist"]
> * Erstellen eines elastischen Auftrags
> * Erstellen von Auftragsanmeldeinformationen, damit Aufträge Skripts für die Ziele ausführen können
> * Definieren der Ziele (Server, Pools für elastische Datenbanken, Datenbanken, Shardzuordnungen), für die der Auftrag ausgeführt werden soll
> * Erstellen von datenbankweit gültigen Anmeldeinformationen in den Zieldatenbanken, damit der Agent eine Verbindung herstellen und Aufträge ausführen kann
> * Erstellen eines Auftrags
> * Hinzufügen von Auftragsschritten zu einem Auftrag
> * Starten der Auftragsausführung
> * Überwachen eines Auftrags

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie noch kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Installieren Sie das Modul „**AzureRM.Sql** 4.8.1-preview“, um die aktuellen Cmdlets für elastische Aufträge zu erhalten. Führen Sie die folgenden Befehle in PowerShell mit Administratorzugriff aus.

```powershell
# Installs the latest PackageManagement powershell package which PowershellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# Installs the latest PowershellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Restart your powershell session with administrative access

# Places AzureRM.Sql preview cmdlets side by side with existing AzureRM.Sql version
Install-Module -Name AzureRM.Sql -AllowPrerelease -RequiredVersion 4.8.1-preview -Force

# Import the AzureRM.Sql 4.8.1 module
Import-Module AzureRM.Sql -RequiredVersion 4.8.1

# Confirm if module successfully imported - if the imported version is 4.8.1, then continue
Get-Module AzureRM.Sql
```

## <a name="create-required-resources"></a>Erstellen der erforderlichen Ressourcen

Um einen Agent für elastische Aufträge erstellen zu können, benötigen Sie eine [Auftragsdatenbank](elastic-jobs-overview.md#job-database) (S0 oder höher). 

*Das folgende Skript erstellt eine neue Ressourcengruppe, einen neuen Server und eine neue Datenbank, die Sie als Auftragsdatenbank verwenden können. Außerdem erstellt das Skript einen zweiten Server mit zwei leeren Datenbanken, für die Aufträge ausgeführt werden können.*

Für elastische Aufträge gelten keine speziellen Benennungsanforderungen. Sie können also beliebige Namenskonventionen verwenden, solange diese die [Anforderungen von Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) erfüllen.

```powershell
# Sign in to your Azure account
Connect-AzureRmAccount

# Create a resource group
Write-Output "Creating a resource group..."
$ResourceGroupName = Read-Host "Please enter a resource group name"
$Location = Read-Host "Please enter an Azure Region"
$Rg = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
$Rg

# Create a server
Write-Output "Creating a server..."
$AgentServerName = Read-Host "Please enter an agent server name"
$AgentServerName = $AgentServerName + "-" + [guid]::NewGuid()
$AdminLogin = Read-Host "Please enter the server admin name"
$AdminPassword = Read-Host "Please enter the server admin password"
$AdminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$AdminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AdminLogin, $AdminPasswordSecure
$AgentServer = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $AgentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$AgentServer | New-AzureRmSqlServerFirewallRule -AllowAllAzureIPs
$AgentServer

# Create the job database
Write-Output "Creating a blank SQL database to be used as the Job Database..."
$JobDatabaseName = "JobDatabase"
$JobDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $JobDatabaseName -RequestedServiceObjectiveName "S0"
$JobDatabase
```

```powershell
# Create a target server and some sample databases - uses the same admin credential as the agent server just for simplicity
Write-Output "Creating target server..."
$TargetServerName = Read-Host "Please enter a target server name"
$TargetServerName = $TargetServerName + "-" + [guid]::NewGuid()
$TargetServer = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $TargetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set target server firewall rules to allow all Azure IPs
$TargetServer | New-AzureRmSqlServerFirewallRule -AllowAllAzureIPs
$TargetServer | New-AzureRmSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$TargetServer

# Create some sample databases to execute jobs against...
$Db1 = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb1"
$Db1
$Db2 = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb2"
$Db2
```

## <a name="enable-the-elastic-jobs-preview-for-your-subscription"></a>Aktivieren der Vorschauversion von elastischen Aufträgen für Ihr Abonnement

Wenn Sie elastische Aufträge verwenden möchten, müssen Sie das Feature durch Ausführen des folgenden Befehls in Ihrem Azure-Abonnement registrieren. (Dieser Befehl muss einmalig in jedem Abonnement ausgeführt werden, in dem Sie elastische Aufträge verwenden möchten.)

```powershell
Register-AzureRmProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

## <a name="create-the-elastic-job-agent"></a>Erstellen des Agents für elastische Aufträge

Bei einem Agent für elastische Aufträge handelt es sich um eine Azure-Ressource zum Erstellen, Ausführen und Verwalten von Aufträgen. Der Agent führt Aufträge gemäß einem Zeitplan oder einmalig aus.

Für das Cmdlet **New-AzureRmSqlElasticJobAgent** muss bereits eine Azure SQL-Datenbank vorhanden sein. Die Parameter *ResourceGroupName*, *ServerName* und *DatabaseName* müssen daher auf vorhandene Ressourcen verweisen.

```powershell
Write-Output "Creating job agent..."
$AgentName = Read-Host "Please enter a name for your new Elastic Job agent"
$JobAgent = $JobDatabase | New-AzureRmSqlElasticJobAgent -Name $AgentName
$JobAgent
```

## <a name="create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets"></a>Erstellen von Auftragsanmeldeinformationen, damit Aufträge Skripts für die Ziele ausführen können

Aufträge verwenden datenbankweit gültige Anmeldeinformationen, um bei der Ausführung eine Verbindung mit den in der Zielgruppe angegebenen Zieldatenbanken herzustellen. Diese datenbankweit gültigen Anmeldeinformationen werden auch zur Verbindungsherstellung mit der Masterdatenbank verwendet, um alle Datenbanken auf einem Server oder in einem Pool für elastische Datenbanken aufzuzählen, sofern eine dieser Optionen der Zielgruppe angehört.

Die datenbankweit gültigen Anmeldeinformationen müssen in der Auftragsdatenbank erstellt werden.  
Für eine erfolgreiche Auftragsausführung müssen alle Zieldatenbanken über eine Anmeldung mit ausreichenden Berechtigungen verfügen.

![Anmeldeinformationen für elastische Aufträge](media/elastic-jobs-overview/job-credentials.png)

Beachten Sie neben den Anmeldeinformationen in der Abbildung auch die hinzugefügten Befehle vom Typ *GRANT* im folgenden Skript. Diese Berechtigungen werden für das Skript benötigt, das wir für diesen Beispielauftrag ausgewählt haben. Da im Beispiel eine neue Tabelle in den Zieldatenbanken erstellt wird, benötigt jede Zieldatenbank die passenden Berechtigungen, um eine erfolgreiche Ausführung zu gewährleisten.

Führen Sie das folgende Skript aus, um die erforderlichen Auftragsanmeldeinformationen (in der Auftragsdatenbank) zu erstellen:

```powershell
# In the master database (target server)
# - Create the master user login
# - Create the master user from master user login
# - Create the job user login
$Params = @{
  'Database' = 'master'
  'ServerInstance' =  $TargetServer.ServerName + '.database.windows.net'
  'Username' = $AdminLogin
  'Password' = $AdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN masteruser WITH PASSWORD='password!123'"
}
Invoke-SqlCmd @Params
$Params.Query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @Params
$Params.Query = "CREATE LOGIN jobuser WITH PASSWORD='password!123'"
Invoke-SqlCmd @Params

# For each of the target databases
# - Create the jobuser from jobuser login
# - Make sure they have the right permissions for successful script execution
$TargetDatabases = @( $Db1.DatabaseName, $Db2.DatabaseName )
$CreateJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$GrantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$GrantCreateScript = "GRANT CREATE TABLE TO jobuser"

$TargetDatabases | % {
  $Params.Database = $_

  $Params.Query = $CreateJobUserScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantAlterSchemaScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantCreateScript
  Invoke-SqlCmd @Params
}

# Create job credential in Job database for master user
Write-Output "Creating job credentials..."
$LoginPasswordSecure = (ConvertTo-SecureString -String "password!123" -AsPlainText -Force)

$MasterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $LoginPasswordSecure
$MasterCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "masteruser" -Credential $MasterCred

$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $LoginPasswordSecure
$JobCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "jobuser" -Credential $JobCred
```

## <a name="define-the-target-databases-you-want-to-run-the-job-against"></a>Definieren der Zieldatenbanken, für die der Auftrag ausgeführt werden soll

Eine [Zielgruppe](elastic-jobs-overview.md#target-group) definiert mindestens eine Gruppe von Datenbanken, für die ein Auftragsschritt ausgeführt wird. 

Der folgende Codeausschnitt erstellt zwei Zielgruppen: *ServerGroup* und *ServerGroupExcludingDb2*. *ServerGroup* ist auf alle Datenbanken ausgerichtet, die zum Zeitpunkt der Ausführung auf dem Server vorhanden sind. *ServerGroupExcludingDb2* ist ebenfalls auf alle Datenbanken auf dem Server ausgerichtet, schließt allerdings *TargetDb2* aus:

```powershell
Write-Output "Creating test target groups..."
# Create ServerGroup target group
$ServerGroup = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name 'ServerGroup'
$ServerGroup | Add-AzureRmSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName

# Create ServerGroup with an exclusion of Db2
$ServerGroupExcludingDb2 = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$ServerGroupExcludingDb2 | Add-AzureRmSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName
$ServerGroupExcludingDb2 | Add-AzureRmSqlElasticJobTarget -ServerName $TargetServerName -Database $Db2.DatabaseName -Exclude
```

## <a name="create-a-job"></a>Erstellen eines Auftrags

```powershell
Write-Output "Creating a new job"
$JobName = "Job1"
$Job = $JobAgent | New-AzureRmSqlElasticJob -Name $JobName -RunOnce
$Job
```

## <a name="create-a-job-step"></a>Erstellen eines Auftragsschritts

In diesem Beispiel werden zwei Auftragsschritte definiert, die durch den Auftrag ausgeführt werden sollen. Der erste Auftragsschritt (*step1*) erstellt in jeder Datenbank aus der Zielgruppe *ServerGroup* eine neue Tabelle (*Step1Table*). Der zweite Auftragsschritt (*step2*) erstellt in jeder Datenbank (mit Ausnahme von *TargetDb2*, da die [zuvor definierte](#define-the-target-databases-you-want-to-run-the-job-against) Zielgruppe diese Datenbank ausschließt) eine neue Tabelle (*Step2Table*).

```powershell
Write-Output "Creating job steps"
$SqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$SqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$Job | Add-AzureRmSqlElasticJobStep -Name "step1" -TargetGroupName $ServerGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText1
$Job | Add-AzureRmSqlElasticJobStep -Name "step2" -TargetGroupName $ServerGroupExcludingDb2.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText2
```


## <a name="run-the-job"></a>Ausführen des Auftrags

Verwenden Sie den folgenden Befehl, um den Auftrag sofort zu starten:

```powershell
Write-Output "Start a new execution of the job..."
$JobExecution = $Job | Start-AzureRmSqlElasticJob
$JobExecution
```

Nach erfolgreichem Abschluss des Vorgangs sehen Sie in „TargetDb1“ zwei neue Tabellen und in „TargetDb2“ nur eine neue Tabelle:


   ![Überprüfen der neuen Tabellen in SSMS](media/elastic-jobs-overview/job-execution-verification.png)




## <a name="monitor-status-of-job-executions"></a>Überwachen des Auftragsausführungsstatus

Die folgenden Codeausschnitte dienen zum Abrufen von Details zur Auftragsausführung:

```powershell
# Get the latest 10 executions run
$JobAgent | Get-AzureRmSqlElasticJobExecution -Count 10

# Get the job step execution details
$JobExecution | Get-AzureRmSqlElasticJobStepExecution

# Get the job target execution details
$JobExecution | Get-AzureRmSqlElasticJobTargetExecution -Count 2
```

## <a name="schedule-the-job-to-run-later"></a>Planen einer späteren Auftragsausführung

Führen Sie den folgenden Befehl aus, um die Ausführung eines Auftrags für einen späteren Zeitpunkt zu planen:

```powershell
# Run every hour starting from now
$Job | Set-AzureRmSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, um die in diesem Tutorial erstellten Azure-Ressourcen zu löschen.

> [!TIP]
> Wenn Sie diese Aufträge weiterverwenden möchten, überspringen Sie die Bereinigung der in diesem Artikel erstellten Ressourcen. Führen Sie andernfalls die folgenden Schritte aus, um alle Ressourcen zu löschen, die im Rahmen dieses Artikels erstellt wurden:
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $ResourceGroupName
```


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein Transact-SQL-Skript für eine Gruppe von Datenbanken ausgeführt.  Sie haben gelernt, wie Sie folgende Aufgaben ausführen:

> [!div class="checklist"]
> * Erstellen eines elastischen Auftrags
> * Erstellen von Auftragsanmeldeinformationen, damit Aufträge Skripts für die Ziele ausführen können
> * Definieren der Ziele (Server, Pools für elastische Datenbanken, Datenbanken, Shardzuordnungen), für die der Auftrag ausgeführt werden soll
> * Erstellen von datenbankweit gültigen Anmeldeinformationen in den Zieldatenbanken, damit der Agent eine Verbindung herstellen und Aufträge ausführen kann
> * Erstellen eines Auftrags
> * Hinzufügen eines Auftragsschritts zum Auftrag
> * Starten der Auftragsausführung
> * Überwachen des Auftrags

> [!div class="nextstepaction"]
>[Use Transact-SQL (T-SQL) to create and manage Elastic Database Jobs](elastic-jobs-tsql.md) (Erstellen und Verwalten von Aufträgen für die elastische Datenbank mithilfe von Transact-SQL (T-SQL))