---
title: 'PowerShell-Beispiel: Überwachen und Skalieren eines Pools für elastische SQL-Datenbanken in der Azure SQL-Datenbank | Microsoft-Dokumentation'
description: Azure PowerShell-Beispielskript zum Überwachen und Skalieren eines Pools für elastische SQL-Datenbanken in Azure SQL-Datenbank
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: ''
ms.service: sql-database
ms.custom: monitor & tune, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/01/2018
ms.author: janeng
ms.openlocfilehash: a66aca8420e5512eb139325f9f1b9603540f1fa7
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2018
---
# <a name="use-powershell-to-monitor-and-scale-a-sql-elastic-pool-in-azure-sql-database"></a>Verwenden von PowerShell zum Überwachen und Skalieren eines Pools für elastische SQL-Datenbanken in der Azure SQL-Datenbank

Dieses PowerShell-Beispielskript überwacht die Leistungsmetriken eines Pools für elastische Datenbanken, skaliert ihn auf eine höhere Leistungsstufe und erstellt eine Warnungsregel auf einer der Leistungsmetriken. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1?highlight=16-17 "Monitor and scale single SQL Database")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nach Ausführung des Skriptbeispiels können mit dem folgenden Befehl die Ressourcengruppe und alle damit verbundenen Ressourcen entfernt werden.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Erstellt einen logischen Server, der eine Datenbank oder einen Pool für elastische Datenbanken hostet. |
| [New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool) | Erstellt einen Pool für elastische Datenbanken auf einem logischen Server. |
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Erstellt eine Datenbank auf einem logischen Server als einzelne oder zu einem Pool gehörende Datenbank. |
| [Get-AzureRmMetric](/powershell/module/azurerm.insights/get-azurermmetric) | Zeigt die Größennutzungsinformationen für die Datenbank an.|
| [Add-AzureRMMetricAlertRule](/powershell/module/azurerm.insights/add-azurermmetricalertrule) | Fügt eine metrikbasierte Warnungsregel hinzu oder aktualisiert sie. |
| [Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool) | Aktualisiert die Eigenschaften von Pool für elastische Datenbanken. |
| [Add-AzureRMMetricAlertRule](/powershell/module/azurerm.insights/add-azurermmetricalertrule) | Legt eine Warnungsregel zur zukünftigen automatischen Überwachung von DTUs fest. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |
|||

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche PowerShell-Skriptbeispiele für SQL-Datenbank finden Sie in den [Azure PowerShell samples for Azure SQL Database](../sql-database-powershell-samples.md) (Azure PowerShell-Beispiele für Azure SQL-Datenbank).
