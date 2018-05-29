---
title: Einzelne Azure SQL-Datenbank | Microsoft-Dokumentation
description: Verwalten Sie die Dienstebene, Leistungsstufe und Speicherkapazität für eine einzelne Azure SQL-Datenbank.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: c1fe162beca258fe8ec3d03ce2844c1abe3176dc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188972"
---
# <a name="manage-resources-for-a-single-database-in-azure-sql-database"></a>Verwalten von Ressourcen für eine einzelne Datenbank in Azure SQL-Datenbank

Mit einer einzelnen Datenbank legen Sie den Umfang der Ressourcen fest, die die Datenbank benötigt, um die Workload gemäß der Festlegungen von Dienstebene, Leistungsstufe und Speichermenge zu behandeln. 

## <a name="manage-single-database-resources-using-the-azure-portal"></a>Verwalten der Ressourcen einer einzelnen Datenbank mit dem Azure-Portal

Gehen Sie wie folgt vor, um die Dienstebene, Leistungsebene oder Speichermenge für eine neue oder vorhandene Azure SQL-Datenbank mit dem Azure-Portal festzulegen oder zu ändern: Öffnen Sie das Fenster **Leistung konfigurieren** für Ihre Datenbank, indem Sie wie im folgenden Screenshot gezeigt auf **Tarif (DTUs skalieren)** klicken. 

- Legen Sie die Dienstebene fest bzw. ändern Sie sie, indem Sie die Dienstebene für Ihre Workload auswählen. 
- Legen Sie die Leistungsebene (**DTUs**) in einer Dienstebene mit dem Schieberegler **DTU** fest bzw. ändern Sie sie.
- Legen Sie die Speichermenge für die Leistungsebene mit dem Schieberegler **Speicher** fest bzw. ändern Sie sie. 

![Konfigurieren von Dienst- und Leistungsebene](./media/sql-database-single-database-resources/change-service-tier.png)

Klicken Sie auf **Übersicht**, um einen laufenden Vorgang zu überwachen und/oder abzubrechen.

![Abbrechen eines Vorgangs](./media/sql-database-single-database-resources/cancel-operation.png)

> [!IMPORTANT]
> Wenn Sie eine P11- oder P15-Dienstebene auswählen, helfen Ihnen die Informationen unter [Aktuelle Einschränkungen von P11- und P15-Datenbanken mit einer maximalen Größe von 4 TB](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb) weiter.
>

## <a name="manage-single-database-resources-using-powershell"></a>Verwalten der Ressourcen einer einzelnen Datenbank mit PowerShell

Verwenden Sie diese PowerShell-Cmdlets, um Dienstebenen, Leistungsstufen und Speichermengen für Azure SQL-Datenbank-Instanzen mit PowerShell festzulegen und zu ändern. Wenn Sie PowerShell installieren oder aktualisieren müssen, helfen Ihnen die Informationen unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-azurerm-ps) weiter. 

| Cmdlet | BESCHREIBUNG |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Erstellt eine Datenbank |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Ruft mindestens eine Datenbank ab|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Legt Eigenschaften für eine Datenbank fest oder verschiebt eine vorhandene Datenbank in einen Pool für elastische Datenbanken. Verwenden Sie z.B. die **MaxSizeBytes**-Eigenschaft, um die maximale Größe für eine Datenbank festzulegen.|
|[Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity)|Ruft den Status von Datenbankvorgängen ab. |
|[Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)|Bricht den asynchronen Aktualisierungsvorgang für die Datenbank ab.|


> [!TIP]
> Ein PowerShell-Beispielskript, mit dem die Leistungsmetriken einer Datenbank überwacht werden, die Skalierung auf eine höhere Leistungsebene durchgeführt wird und eine Warnregel für eine der Leistungsmetriken erstellt wird, finden Sie unter [Überwachen und Skalieren einer einzelnen SQL-Datenbank mit PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

## <a name="manage-single-database-resources-using-the-azure-cli"></a>Verwalten der Ressourcen einer einzelnen Datenbank mit der Azure CLI

Verwenden Sie diese Befehle der [Azure CLI-SQL-Datenbank](/cli/azure/sql/db), um Dienstebenen, Leistungsstufen und die Speichermenge von Azure SQL-Datenbanken mit der Azure CLI festzulegen oder zu ändern. Führen Sie die CLI mithilfe von [Cloud Shell](/azure/cloud-shell/overview) in Ihrem Browser aus, oder [installieren](/cli/azure/install-azure-cli) Sie sie unter macOS, Linux oder Windows. Informationen zum Erstellen und Verwalten von Pools für elastische SQL-Datenbanken finden Sie unter [Pools für elastische Datenbanken](sql-database-elastic-pool.md).

| Cmdlet | BESCHREIBUNG |
| --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Erstellt eine Serverfirewallregel|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Listet die Firewallregeln auf einem Server auf|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Zeigt die Details einer Firewallregel an|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Aktualisiert eine Firewallregel|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Löscht eine Firewallregel|
|[az sql db op list](/cli/azure/sql/db/op?#az_sql_db_op_list)|Ruft eine Liste der Vorgänge ab, die für die Datenbank ausgeführt werden.|
|[az sql db op cancel](/cli/azure/sql/db/op#az_sql_db_op_cancel)|Bricht den asynchronen Vorgang für die Datenbank ab.|

> [!TIP]
> Ein Azure CLI-Beispielskript, mit dem eine Azure SQL-Einzeldatenbank auf eine andere Leistungsebene skaliert wird, nachdem die Größeninformationen der Datenbank abgerufen wurden, finden Sie unter [Überwachen und Skalieren einer einzelnen SQL-Datenbank mit der Azure CLI](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-single-database-resources-using-transact-sql"></a>Verwalten der Ressourcen einer einzelnen Datenbank mit Transact-SQL

Verwenden Sie diese T-SQL-Befehle, um Dienstebenen, Leistungsstufen und Speichermengen für Azure SQL-Datenbanken mit Transact-SQL festzulegen und zu ändern. Sie können diese Befehle mit dem Azure-Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) oder einem beliebigen anderen Programm ausführen, mit dem eine Verbindung mit einem Azure SQL-Datenbankserver hergestellt und Transact-SQL-Befehle übergeben werden können. 

| Get-Help | BESCHREIBUNG |
| --- | --- |
|[CREATE DATABASE (Azure SQL-Datenbank)](/sql/t-sql/statements/create-database-azure-sql-database)|Erstellt eine neue Datenbank. Sie müssen über eine Verbindung mit der Masterdatenbank verfügen, um eine neue Datenbank erstellen zu können.|
| [ALTER DATABASE (Azure SQL-Datenbank)](/sql/t-sql/statements/alter-database-azure-sql-database) |Ändert eine Azure SQL-Datenbank. |
|[sys.database_service_objectives (Azure SQL-Datenbank)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Gibt die Edition (Dienstebene), das Dienstziel (Tarif) und den Namen des Pools für elastische Datenbanken, falls vorhanden, für eine Azure SQL-Datenbank oder ein Azure SQL Data Warehouse zurück. Wenn eine Anmeldung an der Masterdatenbank in einem Azure SQL-Datenbankserver besteht, werden Informationen zu allen Datenbanken zurückgegeben. Für Azure SQL Data Warehouse müssen Sie über eine Verbindung mit der Masterdatenbank verfügen.|
|[sys.database_usage (Azure SQL-Datenbank)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Listet Anzahl, Typ und Dauer von Datenbanken auf einem Azure SQL-Datenbankserver auf.|

Das folgende Beispiel zeigt, wie die maximale Größe für eine Datenbank mit dem Befehl ALTER DATABASE geändert wird:

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-database-resources-using-the-rest-api"></a>Verwalten der Ressourcen einer einzelnen Datenbank mit der REST-API

Verwenden Sie diese REST-API-Anforderungen, um Dienstebenen, Leistungsstufen und Speichermengen für Azure SQL-Datenbanken festzulegen oder zu ändern.

| Get-Help | BESCHREIBUNG |
| --- | --- |
|[Datenbanken – Erstellen oder Aktualisieren](/rest/api/sql/databases/createorupdate)|Erstellt eine neue Datenbank oder aktualisiert eine bereits vorhandene Datenbank|
|[Datenbanken – Abrufen](/rest/api/sql/databases/get)|Ruft eine Datenbank ab|
|[Datenbanken – Abrufen nach Pool für elastische Datenbanken](/rest/api/sql/databases/getbyelasticpool)|Ruft eine Datenbank in einem Pool für elastische Datenbanken ab|
|[Datenbanken – Abrufen nach empfohlenem Pool für elastische Datenbanken](/rest/api/sql/databases/getbyrecommendedelasticpool)|Ruft eine Datenbank in einem empfohlenen Pool für elastische Datenbanken ab|
|[Datenbanken – Auflisten nach Pool für elastische Datenbanken](/rest/api/sql/databases/listbyelasticpool)|Gibt eine Liste der Datenbanken in einem Pool für elastische Datenbanken zurück.|
|[Datenbanken – Auflisten nach empfohlenem Pool für elastische Datenbanken](/rest/api/sql/databases/listbyrecommendedelasticpool)|Gibt eine Liste von Datenbanken in einem empfohlenen Pool für elastische Datenbanken zurück|
|[Datenbanken – Auflisten nach Server](/rest/api/sql/databases/listbyserver)|Gibt eine Liste der Datenbanken auf einem Server zurück|
|[Datenbanken – Aktualisieren](/rest/api/sql/databases/update)|Aktualisiert eine vorhandene Datenbank|
|[Vorgänge – Liste](/rest/api/sql/Operations/List)|Zeigt eine Liste aller verfügbaren SQL-REST-API-Vorgänge.|



## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Dienstebenen, Leistungsebenen und Speichermengen finden Sie unter [[DTU-basiertes Einkaufsmodell für Azure SQL-Datenbank](sql-database-service-tiers-dtu.md) und [Auf virtuellen Kernen basierendes Einkaufsmodell für Azure SQL-Datenbank (Vorschau)](sql-database-service-tiers-vcore.md).
- Informationen zu Pools für elastische Datenbanken finden Sie unter [Pools für elastische Datenbanken als Hilfe beim Verwalten und Skalieren mehrerer SQL-Datenbanken](sql-database-elastic-pool.md).
- Erfahren Sie mehr über [Einschränkungen für Azure-Abonnements und -Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md).
