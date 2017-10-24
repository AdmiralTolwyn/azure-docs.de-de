---
title: Verwalten von Azure Analysis Services mit PowerShell | Microsoft-Dokumentation
description: Verwaltung von Azure Analysis Services mit PowerShell
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: owend
ms.openlocfilehash: 385dd7798893447817dfc2c3a3538a13409ab6e3
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2017
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Verwalten von Azure Analysis Services mit PowerShell

Dieser Artikel beschreibt PowerShell-Cmdlets, die zum Ausführen von Azure Analysis Services-Verwaltungsaufgaben für Server und Datenbanken verwendet werden. 

Für Serververwaltungsaufgaben wie das Erstellen oder Löschen eines Servers, das Anhalten oder Fortsetzen von Servervorgängen oder das Ändern des Servicelevels (Tarif) werden Azure Resource Manager (AzureRM)-Cmdlets verwendet. Für andere Aufgaben zum Verwalten von Datenbanken, z.B. Hinzufügen oder Entfernen von Rollenmitgliedern, Verarbeiten oder Partitionieren, werden die im gleichen SqlServer-Modul wie SQL Server Analysis Services enthaltenen Cmdlets verwendet.

## <a name="permissions"></a>Berechtigungen
Die meisten PowerShell-Aufgaben erfordern, dass Sie über Administratorberechtigungen auf dem verwalteten Analysis Services-Server verfügen. Geplante PowerShell-Aufgaben sind unbeaufsichtigte Vorgänge. Das Konto, in dem der Scheduler ausgeführt wird, muss auf dem Analysis Services-Server über Administratorrechte verfügen. 

Bei Servervorgängen, für die AzureRm-Cmdlets verwendet werden, muss Ihr Konto oder das Konto mit dem Scheduler außerdem zur Rolle „Besitzer“ für die Ressource in der [rollenbasierten Zugriffssteuerung in Azure](../active-directory/role-based-access-control-what-is.md) gehören. 

## <a name="server-operations"></a>Servervorgänge 
Azure Analysis Services-Cmdlets sind im Komponentenmodul [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) enthalten. Informationen zum Installieren von AzureRM-Cmdlet-Modulen, finden Sie im PowerShell-Katalog unter [Azure Resource Manager-Cmdlets](/powershell/azure/overview).

|Cmdlet|Beschreibung| 
|------------|-----------------| 
|[Add-AzureAnalysisServicesAccount](/powershell/module/azurerm.analysisservices/add-azureanalysisservicesaccount)|Fügt ein authentifiziertes Konto hinzu, das für Cmdlet-Anforderungen von Azure Analysis Services-Servern verwendet werden soll.| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Ruft die Details einer Serverinstanz ab.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Erstellt eine Serverinstanz.|   
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Entfernt eine Serverinstanz.|  
|[Restart-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Startet eine Instanz von Analysis Services-Server in der derzeit angemeldeten Umgebung neu, wird im Befehl Add-AzureAnalysisServicesAccount angegeben.|  
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|Setzt eine Serverinstanz fort.|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Hält eine Serverinstanz an.| 
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|Ändert eine Serverinstanz.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Überprüft das Vorhandensein einer Serverinstanz.| 

## <a name="database-operations"></a>Datenbankvorgänge

Für Vorgänge der Azure Analysis Services-Datenbank wird das gleiche [SqlServer](https://www.powershellgallery.com/packages/SqlServer)-Modul wie bei SQL Server Analysis Services verwendet. Allerdings werden nicht alle Cmdlets für Azure Analysis Services unterstützt. 

Das SqlServer-Modul bietet aufgabenspezifische Cmdlets für die Datenbankverwaltung sowie das allgemeine Cmdlet Invoke-ASCmd, das TMSL-Abfragen (Tabular Model Scripting Language) und -Skripts akzeptiert. Die folgenden Cmdlets im SqlServer-Modul werden von Azure Analysis Services unterstützt.

  
|Cmdlet|Beschreibung|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Hinzufügen eines Mitglieds zu einer Datenbankrolle.| 
|[Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet)|Sichern einer Analysis Services-Datenbank.|  
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Entfernen eines Mitglieds aus einer Datenbankrolle.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Ausführen eines TMSL-Skripts.|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|Verarbeiten einer Datenbank.|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|Verarbeiten einer Partition.| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|Verarbeiten einer Tabelle.|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|Zusammenführen einer Partition.|  
|[Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet)|Wiederherstellen einer Analysis Services-Datenbank.| 
  

## <a name="related-information"></a>Verwandte Informationen

* [Herunterladen des SQL Server PowerShell-Moduls](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Herunterladen von SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [SqlServer-Modul im PowerShell-Katalog](https://www.powershellgallery.com/packages/SqlServer)    
* [Programmierung von tabellarischen Modellen für den Kompatibilitätsgrad 1200 und höher](https://msdn.microsoft.com/library/mt712541.aspx)
