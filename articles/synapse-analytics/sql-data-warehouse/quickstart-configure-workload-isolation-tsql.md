---
title: 'Schnellstart: Konfigurieren der Workloadisolation – T-SQL'
description: Verwenden Sie T-SQL zum Konfigurieren der Workloadisolation.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/27/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 99c64e703158c40c2cc110a18be7b8c8d3800ff0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82207801"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Schnellstart: Konfigurieren der Workloadisolation mithilfe von T-SQL

In dieser Schnellstartanleitung erstellen Sie schnell eine Arbeitsauslastungsgruppe und einen Klassifizierer zum Reservieren von Ressourcen für das Laden von Daten. Die Arbeitsauslastungsgruppe weist Datenladevorgängen 20 % der Systemressourcen zu.  Der Workloadklassifizierer weist der Arbeitsauslastungsgruppe für Datenladevorgänge Anforderungen zu.  Bei einer Isolation von 20 % für Datenladevorgänge sind Ressourcen zum Einhalten von SLAs garantiert.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

> [!NOTE]
> Das Erstellen einer SQL-Analyse-Instanz in Azure Synapse Analytics führt möglicherweise zu einem neuen abrechenbaren Dienst.  Weitere Informationen finden Sie unter [Azure Synapse Analytics – Preise](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Voraussetzungen

In dieser Schnellstartanleitung wird vorausgesetzt, dass Sie bereits über eine SQL-Analyse-Instanz in Azure Synapse und über CONTROL DATABASE-Berechtigungen verfügen. Wenn Sie ein Data Warehouse erstellen müssen, verwenden Sie die Anweisungen unter [Erstellen und Verbinden – Portal](create-data-warehouse-portal.md), um ein Data Warehouse namens **mySampleDataWarehouse** zu erstellen.

## <a name="create-login-for-dataloads"></a>Erstellen einer Anmeldung für „DataLoads“

Erstellen Sie eine SQL Server-Authentifizierungsanmeldung in der `master`-Datenbank, indem Sie [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) für „ELTLogin“ verwenden.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Benutzer erstellen

[Erstellen Sie den Benutzer](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) „ELTLogin“ in „mySampleDataWarehouse“.

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Erstellen einer Arbeitsauslastungsgruppe

Erstellen Sie eine [Arbeitsauslastungsgruppe](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) für „DataLoads“ mit einer Isolation von 20 %.

```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5)
;
```

## <a name="create-a-workload-classifier"></a>Erstellen eines Workloadklassifizierers

Erstellen Sie einen [Workloadklassifizierer](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), um „ELTLogin“ der Arbeitsauslastungsgruppe „DataLoads“ zuzuordnen.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Anzeigen vorhandener Arbeitsauslastungsgruppen, Klassifizierer und Laufzeitwerte

```sql
--Workload groups
SELECT * FROM
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Ihnen werden Gebühren für Ihre Data Warehouse-Einheiten und die in Ihrem Data Warehouse gespeicherten Daten berechnet. Diese Compute- und Speicherressourcen werden separat in Rechnung gestellt.

- Falls Sie die Daten im Speicher belassen möchten, können Sie Computeressourcen anhalten, wenn Sie den SQL-Pool nicht verwenden. Wenn Sie Computeressourcen anhalten, werden Ihnen nur die Datenspeichergebühren in Rechnung gestellt. Sie können die Computeressourcen fortsetzen, wenn Sie mit den Daten arbeiten möchten.
- Wenn zukünftig keine Gebühren anfallen sollen, können Sie das Data Warehouse löschen.

## <a name="next-steps"></a>Nächste Schritte

- Sie haben nun eine Arbeitsauslastungsgruppe erstellt. Führen Sie einige Abfragen als ELTLogin durch, um zu ermitteln, welche Leistung erzielt wird. Sie können die Abfragen und die zugewiesene Arbeitsauslastungsgruppe unter [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) anzeigen.
- Weitere Informationen zur Synapse SQL-Workloadverwaltung finden Sie unter [Workloadverwaltung](sql-data-warehouse-workload-management.md) und [Workloadisolation](sql-data-warehouse-workload-isolation.md).
