---
title: Importieren einer BACPAC-Datei zum Erstellen einer Datenbank
description: Erstellen Sie eine neue Azure SQL-Datenbank, indem Sie eine BACPAC-Datei importieren.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: 05698596f966f879da1affc58af0122d08d519ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79228626"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Schnellstart: Importieren einer BACPAC-Datei in eine Datenbank in Azure SQL-Datenbank

Sie können eine SQL Server-Datenbank mithilfe einer [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac)-Datei in eine Datenbank in Azure SQL-Datenbank importieren. Sie können die Daten aus einer `BACPAC`-Datei, die im Azure Blob Storage gespeichert ist (nur Standardspeicher), oder aus dem lokalen Speicher an einem lokalen Speicherort importieren. Um die Importgeschwindigkeit durch die Bereitstellung von mehr und schnelleren Ressourcen zu maximieren, skalieren Sie Ihre Datenbank während des Importvorgangs auf eine höhere Dienstebene und Computegröße. Sie können dann nach erfolgreichem Import zentral herunterskalieren.

> [!NOTE]
> Der Kompatibilitätsgrad der importierten Datenbank beruht auf dem Kompatibilitätsgrad der Quelldatenbank.

> [!IMPORTANT]
> Nachdem Sie die Datenbank importiert haben, können Sie wählen, ob die Datenbank mit dem aktuellen Kompatibilitätsgrad (Ebene 100 für die Datenbank „AdventureWorks2008R2“) oder mit einem höheren Grad ausgeführt werden soll. Weitere Informationen zu den Auswirkungen und Optionen für das Ausführen einer Datenbank mit einem bestimmten Kompatibilitätsgrad finden Sie unter [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Lesen Sie auch [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql). Dort finden Sie Informationen zu weiteren Einstellungen auf Datenbankebene, die sich auf Kompatibilitätsgrade beziehen.

## <a name="using-azure-portal"></a>Verwenden des Azure-Portals

Sehen Sie sich dieses Video an, um zu erfahren, wie Sie aus einer BACPAC-Datei im Azure-Portal importieren können, oder lesen Sie weiter unten weiter:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

Das [Azure-Portal](https://portal.azure.com) unterstützt *nur* das Erstellen einer Einzeldatenbank in Azure SQL-Datenbank und *nur* aus einer in Azure Blob-Speicher gespeicherten BACPAC-Datei.

Das Migrieren einer Datenbank in eine [verwaltete Instanz](sql-database-managed-instance.md) aus einer BACPAC-Datei mithilfe von Azure PowerShell wird derzeit nicht unterstützt. Verwenden Sie stattdessen SQL Server Management Studio oder SQLPackage.

> [!NOTE]
> Computer, die über das Azure-Portal oder PowerShell übermittelte Import-/Exportanforderungen verarbeiten, müssen die BACPAC-Datei sowie die von Data-Tier Application Framework (DacFX) generierten temporären Dateien speichern. Der erforderliche Speicherplatz variiert bei Datenbanken mit derselben Größe enorm. Der erforderliche Speicherplatz kann bis zum Dreifachen der Größe der Datenbank betragen. Der lokale Speicherplatz von Computern, die die Import-/Exportanforderung ausführen, beträgt nur 450GB. Daher kann bei einigen Anforderungen der Fehler `There is not enough space on the disk` auftreten. In diesem Fall besteht die Problemumgehung darin, „sqlpackage.exe“ auf einem Computer mit ausreichend Speicherplatz auszuführen. Es wird empfohlen, „SqlPackage“ zum Importieren oder Exportieren von Datenbanken zu verwenden, die größer als 150 GB sind, um dieses Problem zu vermeiden.

1. Wenn Sie über das Azure-Portal aus einer BACPAC-Datei in eine neue Einzeldatenbank importieren möchten, öffnen Sie die entsprechende Seite für den Datenbankserver, und wählen Sie auf der Symbolleiste **Datenbank importieren** aus.  

   ![Datenbankimport 1](./media/sql-database-import/sql-server-import-database.png)

1. Wählen Sie das Speicherkonto und den Container für die BACPAC-Datei aus, und wählen Sie dann die BACPAC-Datei, aus der importiert werden soll.

1. Geben Sie die Größe der neuen Datenbank (in der Regel identisch mit dem Ursprung) und die SQL Server-Anmeldeinformationen für das Ziel an. Eine Liste der möglichen Werte für eine neue Azure SQL-Datenbank finden Sie unter [Create Database](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Datenbankimport 2](./media/sql-database-import/sql-server-import-database-settings.png)

1. Klicken Sie auf **OK**.

1. Um den Fortschritt eines Importvorgangs zu überwachen, öffnen Sie die Seite für den Server der Datenbank, und wählen Sie unter **Einstellungen** die Option **Import-/Exportverlauf** aus. Bei erfolgreicher Ausführung weist der Import den Status **Abgeschlossen** auf.

   ![Datenbankimportstatus](./media/sql-database-import/sql-server-import-database-history.png)

1. Zum Überprüfen, ob die Datenbank auf dem Datenbankserver aktiv ist, wählen Sie **SQL-Datenbanken** aus, und prüfen Sie, ob der Status der neuen Datenbank **Online** lautet.

## <a name="using-sqlpackage"></a>Verwenden von „SqlPackage“

Informationen zum Importieren einer SQL Server-Datenbank mit dem Befehlszeilenprogramm [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) finden Sie unter [Importparameter und -eigenschaften](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). Im Lieferumfang von SqlPackage sind die neuesten Versionen von [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) und [SQL Server Data Tools für Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) enthalten. Sie können die neueste Version von [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) auch aus dem Microsoft Download Center herunterladen.

Aus Gründen der Skalierbarkeit und Leistung wird die Verwendung von SqlPackage (statt des Azure-Portals) für die meisten Produktionsumgebungen empfohlen. Einen Blogbeitrag des SQL Server-Kundenberatungsteams zur Migration mithilfe von `BACPAC`-Dateien finden Sie unter [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrieren von SQL Server zu Azure SQL-Datenbank mithilfe von BACPAC-Dateien).

Mit dem folgenden SqlPackage-Befehl wird die Datenbank **AdventureWorks2008R2** aus dem lokalen Speicher in einen Azure SQL-Datenbank-Server mit dem Namen **mynewserver20170403** importiert. Er erstellt eine neue Datenbank namens **myMigratedDatabase** mit der **Premium**-Dienstebene und dem Dienstziel **P6**. Ändern Sie diese Werte entsprechend Ihrer Umgebung.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Wenn Sie hinter einer Unternehmensfirewall eine Verbindung mit einem SQL-Datenbank-Server herstellen möchten, der eine einzelne Datenbank verwaltet, muss Port 1433 in der Firewall geöffnet sein. Um eine Verbindung mit einer verwalteten Instanz herzustellen, benötigen Sie eine [Point-to-Site-Verbindung](sql-database-managed-instance-configure-p2s.md) oder eine ExpressRoute-Verbindung.

In diesem Beispiel wird gezeigt, wie eine Datenbank mithilfe von SqlPackage mit universeller Active Directory-Authentifizierung importiert wird.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>PowerShell

> [!NOTE]
> Bei [einer verwalteten Instanz](sql-database-managed-instance.md) ist es derzeit nicht möglich, eine Datenbank aus einer BACPAC-Datei über Azure PowerShell in eine Instanzdatenbank zu migrieren. Verwenden Sie zum Importieren in eine verwalteten Instanz SQL Server Management Studio oder SQLPackage.

> [!NOTE]
> Die Computer, die über das Portal oder Powershell übermittelte Import-/Exportanforderungen verarbeiten, müssen die bacpac-Datei sowie die vom Datenebenenanwendungs-Framework (DacFX) generierten temporären Dateien speichern. Der erforderliche Speicherplatz schwankt bei Datenbanken mit derselben Größe enorm, und es kann bis zu drei Mal so viel Speicherplatz benötigt werden wie die Größe der Datenbank selbst. Der lokale Speicherplatz von Computern, die die Import-/Exportanforderung ausführen, beträgt nur 450GB. Daher könnten einige Anforderungen möglicherweise fehlschlagen und die Fehlermeldung "There is not enough space on the disk" (Es ist nicht genügend Speicherplatz auf dem Datenträger vorhanden) ausgegeben werden. In diesem Fall besteht die Problemumgehung darin, „sqlpackage.exe“ auf einem Computer mit ausreichend Speicherplatz auszuführen. Verwenden Sie zum Importieren/Exportieren von Datenbanken mit mehr als 150 GB „SqlPackage“, um dieses Problem zu vermeiden.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Das Azure Resource Manager-Modul von PowerShell wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Das AzureRM-Modul erhält mindestens bis Dezember 2020 weiterhin Fehlerbehebungen.  Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch. Weitere Informationen zur Kompatibilität finden Sie in der [Einführung in das neue Azure PowerShell Az-Modul](/powershell/azure/new-azureps-module-az).

Verwenden Sie das [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport)-Cmdlet, um eine Anforderung für einen Datenbankimport beim Azure SQL-Datenbank-Dienst einzureichen. Je nach Größe der Datenbank nimmt der Importvorgang einige Zeit in Anspruch.

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

Mit dem Cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) können Sie den Status des Importvorgangs überprüfen. Wenn Sie das Cmdlet direkt nach der Anforderung ausführen, wird in der Regel `Status: InProgress` zurückgegeben. Der Import ist abgeschlossen, wenn `Status: Succeeded` angezeigt wird.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az-sql-db-import](/cli/azure/sql/db#az-sql-db-import), um eine Anforderung für einen Datenbankimport beim Azure SQL-Datenbank-Dienst einzureichen. Je nach Größe der Datenbank nimmt der Importvorgang einige Zeit in Anspruch.

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> Ein weiteres Skriptbeispiel finden Sie unter [Importieren einer Datenbank aus einer BACPAC-Datei](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Einschränkungen

Das Importieren in eine Datenbank in einen Pool für elastische Datenbanken wird nicht unterstützt. Sie können jedoch Daten in eine Einzeldatenbank importieren und die Datenbank anschließend in einen Pool für elastische Datenbanken verschieben.

## <a name="import-using-wizards"></a>Importieren mithilfe von Assistenten

Sie können auch die folgenden Assistenten verwenden:

- [Assistent zum Importieren der Datenebenenanwendung in SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server-Import/Export-Assistent](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Herstellen einer Verbindung mit einer importierten SQL-Datenbank und zum Abfragen einer solchen Datenbank finden Sie unter [Schnellstart: Azure SQL-Datenbank: Verwenden von SQL Server Management Studio zum Herstellen der Verbindung und Abfragen von Daten](sql-database-connect-query-ssms.md).
- Einen Blogbeitrag des SQL Server-Kundenberatungsteams zur Migration mithilfe von BACPAC-Dateien finden Sie unter [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migrieren von SQL Server zu Azure SQL-Datenbank mithilfe von BACPAC-Dateien).
- Eine Erläuterung des gesamten Migrationsprozesses von SQL Server-Datenbanken, einschließlich Empfehlungen zur Leistung, finden Sie unter [Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank](sql-database-single-database-migrate.md).
- Informationen zum sicheren Verwalten und Freigeben von Speicherschlüsseln und SAS finden Sie im [Azure Storage-Sicherheitsleitfaden](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
