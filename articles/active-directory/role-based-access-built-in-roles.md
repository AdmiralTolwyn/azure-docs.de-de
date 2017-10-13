---
title: "Actions und NotActions – Rollenbasierte Zugriffssteuerung in Azure (RBAC) | Microsoft-Dokumentation"
description: "Dieses Thema beschreibt die integrierten Rollen der rollenbasierten Zugriffssteuerung (RBAC). Da die Rollen kontinuierlich hinzugefügt werden, ist es ratsam, die Dokumentation häufiger auf Aktualisierungen zu prüfen."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/28/2017
ms.author: andredm
ms.reviewer: 
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9a5de00793621cfdecea887c53a22d482a25d1b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Integrierte Rollen für die rollenbasierte Zugriffssteuerung in Azure
Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure umfasst die folgenden integrierten Rollen, die Benutzern, Gruppen und Diensten zugewiesen werden können. Die Definitionen integrierter Rollen können nicht geändert werden. Sie können jedoch [benutzerdefinierte Rollen in Azure RBAC](role-based-access-control-custom-roles.md) zur Anpassung an die spezifischen Anforderungen Ihrer Organisation erstellen.

## <a name="roles-in-azure"></a>Rollen in Azure
Die folgende Tabelle enthält kurze Beschreibungen der integrierten Rollen. Klicken Sie auf den Rollennamen, um eine ausführliche Liste der **actions** und **notactions** für die Rolle anzuzeigen. Die **Aktions** -Eigenschaft gibt die zulässigen Aktionen für Azure-Ressourcen an. Für Aktionszeichenfolgen dürfen Platzhalter verwendet werden. Die **notactions** -Eigenschaft gibt die Aktionen an, die von den zulässigen Aktionen ausgeschlossen sind.

Die Aktion definiert, welche Art von Vorgängen für einen bestimmten Ressourcentyp ausgeführt werden können. Beispiel:
- **Schreiben** ermöglicht Ihnen das Ausführen von PUT-, POST-, PATCH- und DELETE-Vorgängen.
- **Lesen** ermöglicht Ihnen das Ausführen von GET-Vorgängen.

In diesem Artikel werden nur die heute vorhandenen verschiedenen Rollen behandelt. Wenn Sie einem Benutzer eine Rolle zuweisen, können Sie jedoch die zulässigen Aktionen weiter einschränken, indem Sie einen Gültigkeitsbereich definieren. Dies ist hilfreich, wenn Sie einem Benutzer die Rolle „Mitwirkender von Website“ zuweisen möchten, jedoch nur für eine Ressourcengruppe.

> [!NOTE]
> Die Definitionen von Azure-Rollen werden ständig weiterentwickelt. Dieser Artikel wird so aktuell wie möglich gehalten. Die aktuellsten Definitionen von Rollen finden Sie jedoch immer in Azure PowerShell. Verwenden Sie das Cmdlet [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition), um alle aktuellen Rollen aufzulisten. Mithilfe von `(get-azurermroledefinition "<role name>").actions` oder `(get-azurermroledefinition "<role name>").notactions` können Sie in eine bestimmte Rolle weiter eintauchen. Verwenden Sie [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation), um die Vorgänge von bestimmten Azure-Ressourcenanbietern aufzulisten.


| Rollenname | Beschreibung |
| --- | --- |
| [Mitwirkender des API-Verwaltungsdienstes](#api-management-service-contributor) |Kann API-Verwaltungsdienste und die APIs verwalten |
| [Operatorrolle des API Management-Diensts](#api-management-service-operator-role) | Kann den API Management-Dienst, aber nicht die APIs selbst verwalten |
| [Leserrolle des API Management-Diensts](#api-management-service-reader-role) | Besitzt schreibgeschützten Zugriff auf den API Management-Dienst und die APIs |
| [Mitwirkender der Application Insights-Komponente](#application-insights-component-contributor) |Kann Application Insights-Komponenten verwalten |
| [Operator für Automation](#automation-operator) |Kann Aufträge starten, unterbrechen und fortsetzen |
| [Mitwirkender für Sicherungen](#backup-contributor) | Kann Sicherungen in einem Recovery Services-Tresor verwalten |
| [Sicherungsoperator](#backup-operator) | Kann Sicherungen in einem Recovery Services-Tresor verwalten, aber nicht entfernen |
| [Sicherungsleser](#backup-reader) | Kann alle Dienste für die Sicherungsverwaltung anzeigen  |
| [Abrechnungsleser](#billing-reader) | Kann sämtliche Abrechnungsinformationen anzeigen  |
| [Mitwirkender von BizTalk](#biztalk-contributor) |Kann BizTalk-Dienste verwalten |
| [Mitwirkender von ClearDB-MySQL-DB](#cleardb-mysql-db-contributor) |Kann ClearDB MySQL-Datenbanken verwalten |
| [Mitwirkender](#contributor) |Kann alles außer den Zugriff verwalten |
| [Mitwirkender von Data Factory](#data-factory-contributor) |Kann Data Factorys und darin enthaltene untergeordnete Ressourcen erstellen und verwalten. |
| [DevTest Labs-Benutzer](#devtest-labs-user) |Kann alles anzeigen sowie virtuelle Maschinen verbinden, starten, neu starten und herunterfahren |
| [DNS Zone Contributor](#dns-zone-contributor) |Kann DNS-Zonen und -Einträge verwalten |
| [Azure Cosmos DB-Kontomitwirkender](#documentdb-account-contributor) |Kann Azure Cosmos DB-Konten verwalten |
| [Mitwirkender von Intelligent Systems-Konto](#intelligent-systems-account-contributor) |Kann Intelligent Systems-Konten verwalten |
| Mitwirkender für Logik-Apps | Kann alle Aspekte einer Logik-App verwalten, aber keine Neuerstellung durchführen |
| Logik-App-Operator |Kann Workflows starten und beenden, die in einer Logik-App definiert sind |
| [Überwachungsleser](#monitoring-reader) |Kann alle Überwachungsdaten lesen |
| [Überwachungsmitwirkender](#monitoring-contributor) |Kann Überwachungsdaten lesen und Überwachungseinstellungen bearbeiten |
| [Mitwirkender von virtuellem Netzwerk](#network-contributor) |Kann alle Netzwerkressourcen verwalten |
| [Mitwirkender von New Relic APM-Konto](#new-relic-apm-account-contributor) |Kann New Relic Application Performance Management-Konten und -Anwendungen verwalten |
| [Besitzer](#owner) |Kann alles verwalten, einschließlich des Zugriffs |
| [Leser](#reader) |Kann alles anzeigen, jedoch keine Änderungen vornehmen |
| [Mitwirkender von Redis-Cache](#redis-cache-contributor) |Kann Redis-Caches verwalten |
| [Mitwirkender von Zeitplanungsauftragssammlung](#scheduler-job-collections-contributor) |Kann Zeitplanungsauftragssammlungen verwalten |
| [Mitwirkender von Suchdienst](#search-service-contributor) |Kann Suchdienste verwalten |
| [Sicherheits-Manager](#security-manager) |Kann Sicherheitskomponenten, Sicherheitsrichtlinien und virtuelle Maschinen verwalten |
| [Site Recovery-Mitwirkender](#site-recovery-contributor) | Kann Site Recovery im Recovery Services-Tresor verwalten |
| [Site Recovery-Operator](#site-recovery-operator) | Kann Failover- und Failbackvorgänge für Site Recovery im Recovery Services-Tresor verwalten |
| [Site Recovery-Leser](#site-recovery-reader) | Kann alle Site Recovery-Verwaltungsvorgänge anzeigen  |
| [Mitwirkender von SQL DB](#sql-db-contributor) |Kann SQL-Datenbanken verwalten, jedoch nicht die zugehörigen sicherheitsbezogenen Richtlinien |
| [SQL-Sicherheits-Manager](#sql-security-manager) |Kann die sicherheitsbezogenen Richtlinien von SQL-Server-Instanzen und SQL-Datenbanken verwalten |
| [Mitwirkender von SQL Server](#sql-server-contributor) |Kann SQL-Server und SQL-Datenbanken verwalten, jedoch nicht die zugehörigen sicherheitsbezogenen Richtlinien |
| [Mitwirkender von klassischem Speicherkonto](#classic-storage-account-contributor) |Kann klassische Speicherkonten verwalten |
| [Mitwirkender von Speicherkonto](#storage-account-contributor) |Kann Speicherkonten verwalten |
| [Mitwirkender für Supportanfragen](#support-request-contributor) | Kann Supportanfragen erstellen und verwalten |
| [Benutzerzugriffsadministrator](#user-access-administrator) |Kann den Benutzerzugriff auf Azure-Ressourcen verwalten |
| [Mitwirkender von klassischen virtuellen Computern](#classic-virtual-machine-contributor) |Kann klassische virtuelle Computer verwalten, jedoch nicht das virtuelle Netzwerk oder Speicherkonto, mit dem sie verbunden sind |
| [Mitwirkender von virtuellen Computern](#virtual-machine-contributor) |Kann virtuelle Computer verwalten, jedoch nicht das virtuelle Netzwerk oder Speicherkonto, mit dem sie verbunden sind |
| [Mitwirkender von klassischem Netzwerk](#classic-network-contributor) |Kann klassische virtuelle Netzwerke und reservierte IP-Adressen verwalten |
| [Mitwirkender von Webplan](#web-plan-contributor) |Kann Webpläne verwalten |
| [Mitwirkender von Website](#website-contributor) |Kann Websites verwalten, jedoch nicht die Webpläne, mit denen sie verbunden sind |

## <a name="role-permissions"></a>Rollenberechtigungen
Die folgenden Tabellen beschreiben die spezifischen Berechtigungen der einzelnen Rollen. Dazu gehören **Actions**, die Berechtigungen erteilen, und **NotActions**, die sie einschränken.

### <a name="api-management-service-contributor"></a>Mitwirkender des API-Verwaltungsdienstes
Kann API-Verwaltungsdienste verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/* |Erstellen und Verwalten des API Management-Diensts |
| Microsoft.Authorization/*/read |Lesen von Autorisierungen |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="api-management-service-operator-role"></a>Operatorrolle des API Management-Diensts
Kann API-Verwaltungsdienste verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/*/read | Dient zum Lesen von API Management-Dienstinstanzen. |
| Microsoft.ApiManagement/Service/backup/action | Dient zum Sichern des API Management-Diensts im angegebenen Container in einem vom Benutzer bereitgestellten Speicherkonto. |
| Microsoft.ApiManagement/Service/delete | Löschen einer API Management-Dienstinstanz |
| Microsoft.ApiManagement/Service/managedeployments/action | Dient zum Ändern der SKU/Einheiten sowie zum Hinzufügen oder Entfernen regionaler Bereitstellungen des API Management-Diensts. |
| Microsoft.ApiManagement/Service/read | Dient zum Lesen der Metadaten für eine API Management-Dienstinstanz. |
| Microsoft.ApiManagement/Service/restore/action | Dient zum Wiederherstellen des API Management-Diensts aus dem angegebenen Container in einem vom Benutzer bereitgestellten Speicherkonto. |
| Microsoft.ApiManagement/Service/updatehostname/action | Dient zum Einrichten, Aktualisieren oder Entfernen benutzerdefinierter Domänennamen für einen API Management-Dienst. |
| Microsoft.ApiManagement/Service/write | Dient zum Erstellen einer neuen Instanz des API Management-Diensts. |
| Microsoft.Authorization/*/read |Lesen von Autorisierungen |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="api-management-service-reader-role"></a>Leserrolle des API Management-Diensts
Kann API-Verwaltungsdienste verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/*/read | Dient zum Lesen von API Management-Dienstinstanzen. |
| Microsoft.ApiManagement/Service/read | Dient zum Lesen der Metadaten für eine API Management-Dienstinstanz. |
| Microsoft.Authorization/*/read |Lesen von Autorisierungen |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="application-insights-component-contributor"></a>Mitwirkender der Application Insights-Komponente
Kann Application Insights-Komponenten verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Insights/components/* |Erstellen und Verwalten von Insights-Komponenten |
| Microsoft.Insights/webtests/* |Erstellen und Verwalten von Webtests |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="automation-operator"></a>Operator für Automation
Kann Aufträge starten, unterbrechen und fortsetzen

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Automation/automationAccounts/jobs/read |Lesen von Aufträgen für Automation-Konten |
| Microsoft.Automation/automationAccounts/jobs/resume/action |Fortsetzen eines Auftrags für Automation-Konten |
| Microsoft.Automation/automationAccounts/jobs/stop/action |Beenden eines Auftrags für Automation-Konten |
| Microsoft.Automation/automationAccounts/jobs/streams/read |Lesen von Auftragsdatenströmen für Automation-Konten |
| Microsoft.Automation/automationAccounts/jobs/suspend/action |Unterbrechen eines Auftrags für Automation-Konten |
| Microsoft.Automation/automationAccounts/jobs/write |Schreiben von Aufträgen für Automation-Konten |
| Microsoft.Automation/automationAccounts/jobSchedules/read |Lesen eines Auftragszeitplans für Automation-Konten |
| Microsoft.Automation/automationAccounts/jobSchedules/write |Lesen eines Auftragszeitplans für Automation-Konten |
| Microsoft.Automation/automationAccounts/read |Lesen von Automation-Konten |
| Microsoft.Automation/automationAccounts/runbooks/read |Lesen von Automation-Runbooks |
| Microsoft.Automation/automationAccounts/schedules/read |Lesen von Zeitplänen für Automation-Konten |
| Microsoft.Automation/automationAccounts/schedules/write |Schreiben von Zeitplänen für Automation-Konten |
| Microsoft.Insights/components/* |Erstellen und Verwalten von Insights-Komponenten |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="backup-contributor"></a>Mitwirkender für Sicherungen
Kann alle Aktionen für die Sicherungsverwaltung durchführen, außer dem Erstellen eines Recovery Services-Tresors und dem Erteilen von Zugriff für andere

| **Aktionen** | |
| --- | --- |
| Microsoft.Network/virtualNetworks/read | Lesen von virtuellen Netzwerken |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Verwalten der Ergebnisse eines Vorgangs in der Sicherungsverwaltung |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Erstellen und Verwalten von Sicherungscontainern in Sicherungsfabrics des Recovery Services-Tresors |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Erstellen und Verwalten von Sicherungsaufträgen |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportieren von Sicherungsaufträgen in Excel-Tabellen |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Erstellen und Verwalten von Metadaten in Zusammenhang mit der Sicherungsverwaltung |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Erstellen und Verwalten der Ergebnisse von Sicherungsverwaltungsvorgängen |
| Microsoft.RecoveryServices/Vaults/backupPolicies/* | Erstellen und Verwalten von Sicherungsrichtlinien |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Erstellen und Verwalten von Elementen, die gesichert werden können |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Erstellen und Verwalten von gesicherten Elementen |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Erstellen und Verwalten von Containern mit Sicherungselementen |
| Microsoft.RecoveryServices/Vaults/certificates/* | Erstellen und Verwalten von Zertifikaten in Zusammenhang mit Sicherungen in einem Recovery Services-Tresor |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Erstellen und Verwalten erweiterter Informationen in Zusammenhang mit einem Tresor |
| Microsoft.RecoveryServices/Vaults/read | Lesen von Recovery Services-Tresoren |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Verwalten von Ermittlungsvorgängen zum Abrufen neu erstellter Container |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Erstellen und Verwalten von registrierten Identitäten |
| Microsoft.RecoveryServices/Vaults/usages/* | Erstellen und Verwalten der Nutzung des Recovery Services-Tresors |
| Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen |
| Microsoft.Storage/storageAccounts/read | Lesen von Speicherkonten |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="backup-operator"></a>Sicherungsoperator
Kann alle Aktionen für die Sicherungsverwaltung durchführen, außer dem Erstellen von Tresoren, dem Entfernen von Sicherungen und dem Erteilen von Zugriff für andere

| **Aktionen** | |
| --- | --- |
| Microsoft.Network/virtualNetworks/read | Lesen von virtuellen Netzwerken |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Lesen der Ergebnisse eines Vorgangs in der Sicherungsverwaltung |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Einlesen von Vorgangsergebnissen in Schutzcontainer |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Durchführen von Sicherungsvorgängen in einem gesicherten Element nach Bedarf |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Lesen der Ergebnisse eines für ein gesichertes Element ausgeführten Vorgangs |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationStatus/read | Lesen des Status eines für ein gesichertes Element ausgeführten Vorgangs |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Lesen von gesicherten Elementen |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Lesen des Wiederherstellungspunkts eines gesicherten Elements |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Durchführen eines Wiederherstellungsvorgangs unter Verwendung eines Wiederherstellungspunkts eines gesicherten Elements |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Erstellen eines Sicherungselements |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Lesen von Containern mit Sicherungselementen |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Erstellen und Verwalten von Sicherungsaufträgen |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportieren von Sicherungsaufträgen in Excel-Tabellen |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Lesen von Metadaten in Zusammenhang mit der Sicherungsverwaltung |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Erstellen und Verwalten der Ergebnisse von Sicherungsverwaltungsvorgängen |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Lesen der Ergebnisse von in Sicherungsrichtlinien durchgeführten Vorgängen |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Lesen von Sicherungsrichtlinien |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Erstellen und Verwalten von Elementen, die gesichert werden können |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Lesen von gesicherten Elementen |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Lesen von gesicherten Containern mit Sicherungselementen |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Lesen von erweiterten Informationen in Zusammenhang mit einem Tresor |
| Microsoft.RecoveryServices/Vaults/extendedInformation/write | Schreiben von erweiterten Informationen in Zusammenhang mit einem Tresor |
| Microsoft.RecoveryServices/Vaults/read | Lesen von Recovery Services-Tresoren |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Verwalten von Ermittlungsvorgängen zum Abrufen neu erstellter Container |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Lesen der Ergebnisse eines mit registrierten Elementen des Tresors durchgeführten Vorgangs |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Lesen von registrierten Elementen des Tresors |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Schreiben von registrierten Elementen in den Tresor |
| Microsoft.RecoveryServices/Vaults/usages/read | Lesen der Nutzung des Recovery Services-Tresors |
| Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen |
| Microsoft.Storage/storageAccounts/read | Lesen von Speicherkonten |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### <a name="backup-reader"></a>Sicherungsleser
Kann die Sicherungsverwaltung im Recovery Services-Tresor überwachen

| **Aktionen** | |
| --- | --- |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read  | Lesen der Ergebnisse eines Vorgangs in der Sicherungsverwaltung |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read  | Einlesen von Vorgangsergebnissen in Schutzcontainer |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read  | Lesen der Ergebnisse eines für ein gesichertes Element ausgeführten Vorgangs |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationStatus/read  | Lesen des Status eines für ein gesichertes Element ausgeführten Vorgangs |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read  | Lesen von gesicherten Elementen |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read  | Lesen von Containern mit Sicherungselementen |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read  | Lesen der Ergebnisse von Sicherungsaufträgen |
| Microsoft.RecoveryServices/Vaults/backupJobs/read  | Lesen von Sicherungsaufträgen |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportieren von Sicherungsaufträgen in Excel-Tabellen |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read  | Lesen von Metadaten in Zusammenhang mit der Sicherungsverwaltung |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read  | Lesen der Ergebnisse von Vorgängen der Sicherungsverwaltung |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read  | Lesen der Ergebnisse von in Sicherungsrichtlinien durchgeführten Vorgängen |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read  | Lesen von Sicherungsrichtlinien |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read  |  Lesen von gesicherten Elementen |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read  | Lesen von gesicherten Containern mit Sicherungselementen |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read  | Lesen von erweiterten Informationen in Zusammenhang mit einem Tresor |
| Microsoft.RecoveryServices/Vaults/read  | Lesen von Recovery Services-Tresoren |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read  | Lesen der Ergebnisse von Ermittlungsvorgängen zum Abrufen neu erstellter Container |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read  | Lesen der Ergebnisse eines mit registrierten Elementen des Tresors durchgeführten Vorgangs |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read  | Lesen von registrierten Elementen des Tresors |
| Microsoft.RecoveryServices/Vaults/usages/read  |  Lesen der Nutzung des Recovery Services-Tresors |

### <a name="billing-reader"></a>Abrechnungsleser
Kann sämtliche Abrechnungsinformationen anzeigen

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Billing/*/read |Lesen von Abrechnungsinformationen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="biztalk-contributor"></a>Mitwirkender von BizTalk
Kann BizTalk-Dienste verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.BizTalkServices/BizTalk/* |Erstellen und Verwalten von BizTalk-Diensten |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="cleardb-mysql-db-contributor"></a>Mitwirkender von ClearDB-MySQL-DB
Kann ClearDB MySQL-Datenbanken verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |
| successbricks.cleardb/databases/* |Erstellen und Verwalten von ClearDB MySQL-Datenbanken |

### <a name="contributor"></a>Mitwirkender
Kann alles außer den Zugriff verwalten

| **Aktionen** |  |
| --- | --- |
| * |Erstellen und Verwalten von Ressourcen aller Typen |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete |Rollen und Rollenzuweisungen können nicht gelöscht werden. |
| Microsoft.Authorization/*/Write |Rollen und Rollenzuweisungen können nicht erstellt werden. |

### <a name="data-factory-contributor"></a>Mitwirkender von Data Factory
Erstellt und verwaltet Data Factorys und darin enthaltene untergeordnete Ressourcen.

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.DataFactory/dataFactories/* |Erstellt und verwaltet Data Factorys und darin enthaltene untergeordnete Ressourcen. |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="devtest-labs-user"></a>DevTest Labs-Benutzer
Kann alles anzeigen sowie virtuelle Maschinen verbinden, starten, neu starten und herunterfahren

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Compute/availabilitySets/read |Lesen der Eigenschaften von Verfügbarkeitsgruppen |
| Microsoft.Compute/virtualMachines/*/read |Lesen der Eigenschaften eines virtuellen Computers (VM-Größen, Laufzeitstatus, VM-Erweiterungen usw.) |
| Microsoft.Compute/virtualMachines/deallocate/action |Aufhebung der Zuordnung virtueller Computer |
| Microsoft.Compute/virtualMachines/read |Lesen der Eigenschaften eines virtuellen Computers |
| Microsoft.Compute/virtualMachines/restart/action |Neustarten virtueller Computer |
| Microsoft.Compute/virtualMachines/start/action |Starten virtueller Computer |
| Microsoft.DevTestLab/*/read |Lesen der Eigenschaften eines Labs |
| Microsoft.DevTestLab/labs/createEnvironment/action |Erstellen einer Lab-Umgebung |
| Microsoft.DevTestLab/labs/formulas/delete |Löschen von Formeln |
| Microsoft.DevTestLab/labs/formulas/read |Lesen von Formeln |
| Microsoft.DevTestLab/labs/formulas/write |Hinzufügen oder Ändern von Formeln |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action |Auswerten von Labrichtlinien |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action |Verknüpfen eines Back-End-Adresspools für Load Balancer |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action |Verknüpfen einer eingehenden NAT-Regel für Load Balancer |
| Microsoft.Network/networkInterfaces/*/read |Lesen der Eigenschaften einer Netzwerkschnittstelle (z.B. alle Load Balancer, zu denen die Netzwerkschnittstelle gehört) |
| Microsoft.Network/networkInterfaces/join/action |Verknüpfen eines virtuellen Computers mit einer Netzwerkschnittstelle |
| Microsoft.Network/networkInterfaces/read |Lesen von Netzwerkschnittstellen |
| Microsoft.Network/networkInterfaces/write |Schreiben in Netzwerkschnittstellen |
| Microsoft.Network/publicIPAddresses/*/read |Lesen der Eigenschaften einer öffentlichen IP-Adresse |
| Microsoft.Network/publicIPAddresses/join/action |Verknüpfen einer öffentlichen IP-Adresse |
| Microsoft.Network/publicIPAddresses/read |Lesen von öffentlichen IP-Adressen im Netzwerk |
| Microsoft.Network/virtualNetworks/subnets/join/action |Verknüpfen eines virtuellen Netzwerks |
| Microsoft.Resources/deployments/operations/read |Lesen von Bereitstellungsvorgängen |
| Microsoft.Resources/deployments/read |Lesen von Bereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Storage/storageAccounts/listKeys/action |Auflisten von Speicherkontoschlüsseln |

### <a name="dns-zone-contributor"></a>DNS Zone Contributor
Kann DNS-Zonen und -Einträge verwalten.

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/\*/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Insights/alertRules/\* |Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Network/dnsZones/\* |Erstellen und Verwalten von DNS-Zonen und -Einträgen |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/\* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Support/\* |Erstellen und Verwalten von Supporttickets |

### <a name="azure-cosmos-db-account-contributor"></a>Azure Cosmos DB-Kontomitwirkender
Kann Azure Cosmos DB-Konten verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.DocumentDb/databaseAccounts/* |Erstellen und Verwalten von DocumentDB-Konten |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="intelligent-systems-account-contributor"></a>Mitwirkender von Intelligent Systems-Konto
Kann Intelligent Systems-Konten verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Warnungsregeln |
| Microsoft.IntelligentSystems/accounts/* |Erstellen und Verwalten von Intelligent Systems-Konten |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="monitoring-reader"></a>Überwachungsleser
Kann alle Überwachungsdaten (Metriken, Protokolle usw.) lesen. Siehe auch [Erste Schritte mit Rollen, Berechtigungen und Sicherheit in Azure Monitor](/monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Aktionen** |  |
| --- | --- |
| */Lesen |Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
| Microsoft.OperationalInsights/workspaces/search/action |Durchsuchen von Log Analytics-Daten |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="monitoring-contributor"></a>Überwachungsmitwirkender
Kann sämtliche Überwachungsdaten lesen und Überwachungseinstellungen bearbeiten. Siehe auch [Erste Schritte mit Rollen, Berechtigungen und Sicherheit in Azure Monitor](/monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Aktionen** |  |
| --- | --- |
| */Lesen |Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
| Microsoft.Insights/AlertRules/* |Lesen/Schreiben/Löschen von Warnungsregeln. |
| Microsoft.Insights/components/* |Kann Application Insights-Komponenten lesen/schreiben/delegieren. |
| Microsoft.Insights/DiagnosticSettings/* |Lesen/Schreiben/Löschen von Diagnoseeinstellungen. |
| Microsoft.Insights/eventtypes/* |Auflisten von Aktivitätsprotokollereignissen (Verwaltungsereignissen) in einem Abonnement. Diese Berechtigung gilt sowohl für den programmgesteuerten als auch für den Portalzugriff auf das Aktivitätsprotokoll. |
| Microsoft.Insights/LogDefinitions/* |Diese Berechtigung ist für Benutzer notwendig, die über das Portal auf Aktivitätsprotokolle zugreifen müssen. Auflisten der Protokollkategorien im Aktivitätsprotokoll. |
| Microsoft.Insights/MetricDefinitions/* |Lesen von Metrikdefinitionen (Liste der verfügbaren Metriktypen für eine Ressource). |
| Microsoft.Insights/Metrics/* |Lesen von Metriken für eine Ressource. |
| Microsoft.Insights/Register/Action |Registriert den Microsoft.Insights-Anbieter. |
| Microsoft.Insights/webtests/* |Lesen/Schreiben/Löschen von Application Insights-Webtests. |
| Microsoft.OperationalInsights/workspaces/intelligencepacks/* |Lesen/Schreiben/Löschen von Log Analytics-Lösungspaketen. |
| Microsoft.OperationalInsights/workspaces/savedSearches/* |Lesen/Schreiben/Löschen von gespeicherten Log Analytics-Suchvorgängen. |
| Microsoft.OperationalInsights/workspaces/search/action |Durchsuchen von Log Analytics-Arbeitsbereichen. |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action |Auflisten der Schlüssel für einen Log Analytics-Arbeitsbereich. |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* |Lesen/Schreiben/Löschen von Log Analytics-Speicherdetailinformationen. |

### <a name="network-contributor"></a>Mitwirkender von virtuellem Netzwerk
Kann alle Netzwerkressourcen verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Network/* |Erstellen und Verwalten von Netzwerken |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="new-relic-apm-account-contributor"></a>Mitwirkender von New Relic APM-Konto
Kann New Relic Application Performance Management-Konten und -Anwendungen verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |
| NewRelic.APM/accounts/* |Erstellen und Verwalten von New Relic Application Performance Management-Konten |

### <a name="owner"></a>Besitzer
Kann alles verwalten, einschließlich des Zugriffs

| **Aktionen** |  |
| --- | --- |
| * |Erstellen und Verwalten von Ressourcen aller Typen |

### <a name="reader"></a>Leser
Kann alles anzeigen, jedoch keine Änderungen vornehmen

| **Aktionen** |  |
| --- | --- |
| */Lesen |Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |

### <a name="redis-cache-contributor"></a>Mitwirkender von Redis-Cache
Kann Redis-Caches verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Cache/redis/* |Erstellen und Verwalten von Redis-Caches |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="scheduler-job-collections-contributor"></a>Mitwirkender von Zeitplanungsauftragssammlung
Kann Zeitplanungsauftragssammlungen verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Scheduler/jobcollections/* |Erstellen und Verwalten von Auftragssammlungen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="search-service-contributor"></a>Mitwirkender von Suchdienst
Kann Suchdienste verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Search/searchServices/* |Erstellen und Verwalten von Suchdiensten |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="security-manager"></a>Sicherheits-Manager
Kann Sicherheitskomponenten, Sicherheitsrichtlinien und virtuelle Maschinen verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.ClassicCompute/*/read |Lesen von Konfigurationsinformationen zu klassischen virtuellen Compute-Computern |
| Microsoft.ClassicCompute/virtualMachines/*/write |Schreiben der Konfiguration für virtuelle Computer |
| Microsoft.ClassicNetwork/*/read |Lesen von Konfigurationsinformationen zu klassischem Netzwerk |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Security/* |Erstellen und Verwalten von Sicherheitskomponenten und -richtlinien |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="site-recovery-contributor"></a>Site Recovery-Mitwirkender
Kann alle Site Recovery-Verwaltungsaktionen verwalten, mit Ausnahme der Erstellung des Recovery Services-Tresors und der Zuweisung von Zugriffsrechten für andere Benutzer

| **Aktionen** | |
| --- | --- |
| Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Network/virtualNetworks/read | Lesen von virtuellen Netzwerken |
| Microsoft.RecoveryServices/Vaults/certificates/write | Aktualisieren des Tresoranmeldeinformationen-Zertifikats |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Erstellen und Verwalten erweiterter Informationen in Zusammenhang mit einem Tresor |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/*  | Lesen von Warnungen für den Recovery Services-Tresor |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read  | Lesen der Konfiguration für Recovery Services-Tresorbenachrichtigungen |
| Microsoft.RecoveryServices/Vaults/read | Lesen von Recovery Services-Tresoren |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Verwalten von Ermittlungsvorgängen zum Abrufen neu erstellter Container |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Erstellen und Verwalten von registrierten Identitäten |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Erstellen oder Aktualisieren von Einstellungen für Replikationswarnungen |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Lesen von Replikationsereignissen |
| Microsoft.RecoveryServices/vaults/replicationFabrics/* | Erstellen und Verwalten von Replikationsfabrics |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Erstellen und Verwalten von Replikationsaufträgen |
| Microsoft.RecoveryServices/vaults/replicationPolicies/* | Erstellen und Verwalten von Replikationsrichtlinien |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Erstellen und Verwalten von Wiederherstellungsplänen |
| Microsoft.RecoveryServices/Vaults/storageConfig/* | Erstellen und Verwalten der Speicherkonfiguration des Recovery Services-Tresors |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Lesen der Tokeninformationen für den Recovery Services-Tresor |
| Microsoft.RecoveryServices/Vaults/usages/read | Lesen der Nutzungsdetails eines Recovery Services-Tresors |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen |
| Microsoft.Storage/storageAccounts/read | Lesen von Speicherkonten |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="site-recovery-operator"></a>Site Recovery-Operator
Kann Failover und Failbacks durchführen, aber keine anderen Site Recovery-Verwaltungsaktionen, oder den Zugriff für andere Benutzer zuweisen

| **Aktionen** | |
| --- | --- |
| Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Insights/alertRules/* | Erstellen und Verwalten von Warnungsregeln |
| Microsoft.Network/virtualNetworks/read | Lesen von virtuellen Netzwerken |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Lesen von erweiterten Informationen in Zusammenhang mit einem Tresor |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/*  | Lesen von Warnungen für den Recovery Services-Tresor |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read  | Lesen der Konfiguration für Recovery Services-Tresorbenachrichtigungen |
| Microsoft.RecoveryServices/Vaults/read | Lesen von Recovery Services-Tresoren |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Verwalten von Ermittlungsvorgängen zum Abrufen neu erstellter Container |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Lesen des Vorgangsstatus und -ergebnisses für einen übermittelten Vorgang |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Lesen von Containern, die für eine Ressource registriert sind |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Lesen von Einstellungen für Replikationswarnungen |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Lesen von Replikationsereignissen |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Prüfen der Konsistenz von Fabrics |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Lesen von Replikationsfabrics |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ reassociateGateway/action | Erneutes Zuordnen des Replikationsgateways |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Erneuern des Replikationsfabric-Zertifikats |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Lesen von Replikationsfabric-Netzwerken |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | Lesen der Replikationsfabric-Netzwerkzuordnung |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | Lesen von Schutzcontainern |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | Abrufen der Liste mit allen schützbaren Elementen |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ applyRecoveryPoint/action | Anwenden eines bestimmten Wiederherstellungspunkts |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ failoverCommit/action | Committen eines Failovers für ein Failoverelement |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ plannedFailover/action | Starten eines geplanten Failovers für ein geschütztes Element |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | Abrufen einer Liste mit allen geschützten Elementen |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Abrufen einer Liste mit verfügbaren Wiederherstellungspunkten |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ repairReplication/action | Reparieren der Replikation für ein geschütztes Element |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/reProtect/action | Starten des erneuten Schutzes für ein geschütztes Element|
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover/action | Starten des Testfailovers eines geschützten Elements |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ testFailoverCleanup/action | Starten der Bereinigung eines Testfailovers |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ unplannedFailover/action | Starten des ungeplanten Failovers eines geschützten Elements |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ updateMobilityService/action | Aktualisieren des Mobilitätsdiensts |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | Lesen von Schutzcontainerzuordnungen |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Lesen von Recovery Services-Anbietern |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider/action | Aktualisieren des Recovery Services-Anbieters |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | Lesen von Speicherklassifizierungen für Replikationsfabrics |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | Lesen von Speicherklassifizierungszuordnungen |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Lesen von registrierten vCenter-Informationen |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Erstellen und Verwalten von Replikationsaufträgen |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Lesen von Replikationsrichtlinien |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ failoverCommit/action | Committen des Failovers für ein Wiederherstellungsplan-Failover |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ plannedFailover/action | Starten des Failovers eines Wiederherstellungsplans |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Lesen von Wiederherstellungsplänen |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Starten des erneuten Schutzes eines Wiederherstellungsplans |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Starten des Testfailovers eines Wiederherstellungsplans |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ testFailoverCleanup/action | Starten der Bereinigung eines Wiederherstellungsplan-Testfailovers |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ unplannedFailover/action | Starten des ungeplanten Failovers eines Wiederherstellungsplans |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Lesen der Speicherkonfiguration eines Recovery Services-Tresors |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Lesen der Tokeninformationen für den Recovery Services-Tresor |
| Microsoft.RecoveryServices/Vaults/usages/read | Lesen der Nutzungsdetails eines Recovery Services-Tresors |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* | Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Ressourcengruppen |
| Microsoft.Storage/storageAccounts/read | Lesen von Speicherkonten |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |

### <a name="site-recovery-reader"></a>Site Recovery-Leser
Kann den Site Recovery-Status im Recovery Services-Tresor überwachen und Supporttickets ausstellen

| **Aktionen** | |
| --- | --- |
| Microsoft.Authorization/*/read | Lesen von Rollen und Rollenzuweisungen |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read  | Lesen von erweiterten Informationen in Zusammenhang mit einem Tresor |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read  | Lesen von Warnungen für den Recovery Services-Tresor |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read  | Lesen der Konfiguration für Recovery Services-Tresorbenachrichtigungen |
| Microsoft.RecoveryServices/Vaults/read  | Lesen von Recovery Services-Tresoren |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read  | Verwalten von Ermittlungsvorgängen zum Abrufen neu erstellter Container |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read  | Lesen des Vorgangsstatus und -ergebnisses für einen übermittelten Vorgang |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read  | Lesen von Containern, die für eine Ressource registriert sind |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Lesen von Einstellungen für Replikationswarnungen |
| Microsoft.RecoveryServices/vaults/replicationEvents/read  | Lesen von Replikationsereignissen |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read  | Lesen von Replikationsfabrics |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read  | Lesen von Replikationsfabric-Netzwerken |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read  | Lesen der Replikationsfabric-Netzwerkzuordnung |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read  |  Lesen von Schutzcontainern |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read  | Abrufen der Liste mit allen schützbaren Elementen |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read  | Abrufen einer Liste mit allen geschützten Elementen |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read  | Abrufen einer Liste mit verfügbaren Wiederherstellungspunkten |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read  | Lesen von Schutzcontainerzuordnungen |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read  | Lesen von Recovery Services-Anbietern |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read  | Lesen von Speicherklassifizierungen für Replikationsfabrics |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read  |  Lesen von Speicherklassifizierungszuordnungen |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read  |  Lesen von registrierten vCenter-Informationen |
| Microsoft.RecoveryServices/vaults/replicationJobs/read  |  Lesen des Status von Replikationsaufträgen |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read  |  Lesen von Replikationsrichtlinien |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read  |  Lesen von Wiederherstellungsplänen |
| Microsoft.RecoveryServices/Vaults/storageConfig/read  |  Lesen der Speicherkonfiguration eines Recovery Services-Tresors |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read  |  Lesen der Tokeninformationen für den Recovery Services-Tresor |
| Microsoft.RecoveryServices/Vaults/usages/read  |  Lesen der Nutzungsdetails eines Recovery Services-Tresors |
| Microsoft.Support/*  |  Erstellen und Verwalten von Support-Tickets |

### <a name="sql-db-contributor"></a>Mitwirkender von SQL DB
Kann SQL-Datenbanken verwalten, jedoch nicht die zugehörigen sicherheitsbezogenen Richtlinien

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Rollen und Rollenzuweisungen |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Sql/servers/databases/* |Erstellen und Verwalten von SQL-Datenbanken |
| Microsoft.Sql/servers/read |Lesen von SQL Server |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* |Kann keine Überwachungsrichtlinien bearbeiten |
| Microsoft.Sql/servers/databases/auditingSettings/* |Überwachungsrichtlinien können nicht bearbeiten werden |
| Microsoft.Sql/servers/databases/auditRecords/read |Kann keine Überwachungsdatensätze lesen |
| Microsoft.Sql/servers/databases/connectionPolicies/* |Kann keine Verbindungsrichtlinien bearbeiten |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |Kann keine Datenmaskierungsrichtlinien bearbeiten |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |Richtlinien für Sicherheitswarnungen können nicht bearbeitet werden |
| Microsoft.Sql/servers/databases/securityMetrics/* |Kann keine Sicherheitsmetriken bearbeiten |

### <a name="sql-security-manager"></a>SQL-Sicherheits-Manager
Kann die sicherheitsbezogenen Richtlinien von SQL-Server-Instanzen und SQL-Datenbanken verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen der Microsoft-Autorisierung |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Sql/servers/auditingPolicies/* |Erstellen und Verwalten von SQL Server-Überwachungsrichtlinien |
| Microsoft.Sql/servers/auditingSettings/* |Erstellen und Verwalten von SQL Server-Überwachungseinstellungen |
| Microsoft.Sql/servers/databases/auditingPolicies/* |Erstellen und Verwalten von Überwachungsrichtlinien von SQL Server-Datenbanken |
| Microsoft.Sql/servers/databases/auditingSettings/* |Erstellen und Verwalten von Überwachungseinstellungen von SQL Server-Datenbanken |
| Microsoft.Sql/servers/databases/auditRecords/read |Lesen von Überwachungsdatensätzen |
| Microsoft.Sql/servers/databases/connectionPolicies/* |Erstellen und Verwalten von Verbindungsrichtlinien von SQL Server-Datenbanken |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |Erstellen und Verwalten von Datenmaskierungsrichtlinien von SQL Server-Datenbanken |
| Microsoft.Sql/servers/databases/read |Lesen von SQL-Datenbanken |
| Microsoft.Sql/servers/databases/schemas/read |Lesen von Datenbankschemas von SQL-Servern |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read |Lesen von Spalten der Datenbanktabellen von SQL-Servern |
| Microsoft.Sql/servers/databases/schemas/tables/read |Lesen von Datenbanktabellen von SQL-Servern |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |Erstellen und Verwalten von Richtlinien für Sicherheitswarnungen von SQL Server-Datenbanken |
| Microsoft.Sql/servers/databases/securityMetrics/* |Erstellen und Verwalten von Sicherheitsmetriken von SQL Server-Datenbanken |
| Microsoft.Sql/servers/read |Lesen von SQL Server |
| Microsoft.Sql/servers/securityAlertPolicies/* |Erstellen und Verwalten von Richtlinien für Sicherheitswarnungen von SQL Server |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="sql-server-contributor"></a>Mitwirkender von SQL Server
Kann SQL-Server und SQL-Datenbanken verwalten, jedoch nicht die zugehörigen sicherheitsbezogenen Richtlinien

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Autorisierungen |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Sql/servers/* |Erstellen und Verwalten von SQL-Servern |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* |Kann keine SQL Server-Überwachungsrichtlinien bearbeiten |
| Microsoft.Sql/servers/auditingSettings/* |SQL Server-Überwachungseinstellungen können nicht bearbeitet werden |
| Microsoft.Sql/servers/databases/auditingPolicies/* |Kann keine Überwachungsrichtlinien von SQL Server-Datenbanken bearbeiten |
| Microsoft.Sql/servers/databases/auditingSettings/* |Überwachungseinstellungen von SQL Server-Datenbanken können nicht bearbeitet werden |
| Microsoft.Sql/servers/databases/auditRecords/read |Kann keine Überwachungsdatensätze lesen |
| Microsoft.Sql/servers/databases/connectionPolicies/* |Kann keine Verbindungsrichtlinien von SQL Server-Datenbanken bearbeiten |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |Kann keine Datenmaskierungsrichtlinien von SQL Server-Datenbanken bearbeiten |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |Richtlinien für Sicherheitswarnungen von SQL Server-Datenbanken können nicht bearbeitet werden |
| Microsoft.Sql/servers/databases/securityMetrics/* |Kann keine Sicherheitsmetriken von SQL Server-Datenbanken bearbeiten |
| Microsoft.Sql/servers/securityAlertPolicies/* |Richtlinien für Sicherheitswarnungen von SQL Server können nicht bearbeitet werden |

### <a name="classic-storage-account-contributor"></a>Mitwirkender von klassischem Speicherkonto
Kann klassische Speicherkonten verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Autorisierungen |
| Microsoft.ClassicStorage/storageAccounts/* |Erstellen und Verwalten von Speicherkonten |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="storage-account-contributor"></a>Mitwirkender von Speicherkonto
Kann Speicherkonten verwalten, aber nicht den Zugriff darauf

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen aller Autorisierungen |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.Insights/diagnosticSettings/* |Verwalten der Diagnoseeinstellungen |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Storage/storageAccounts/* |Erstellen und Verwalten von Speicherkonten |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="support-request-contributor"></a>Mitwirkender für Supportanfragen
Kann Supporttickets im Abonnementbereich erstellen und verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Lesen von Autorisierungen |
| Microsoft.Support/* | Erstellen und Verwalten von Support-Tickets |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lesen von Rollen und Rollenzuweisungen |

### <a name="user-access-administrator"></a>Benutzerzugriffsadministrator
Kann den Benutzerzugriff auf Azure-Ressourcen verwalten

| **Aktionen** |  |
| --- | --- |
| */Lesen |Lesen von Ressourcen aller Typen mit Ausnahme geheimer Schlüssel |
| Microsoft.Authorization/* |Verwalten der Autorisierung |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="classic-virtual-machine-contributor"></a>Mitwirkender von klassischen virtuellen Computern
Kann klassische virtuelle Computer verwalten, jedoch nicht das virtuelle Netzwerk oder Speicherkonto, mit dem sie verbunden sind

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Autorisierungen |
| Microsoft.ClassicCompute/domainNames/* |Erstellen und Verwalten von klassischen Compute-Domänennamen |
| Microsoft.ClassicCompute/virtualMachines/* |Erstellen und Verwalten von virtuellen Computern |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action |Verknüpfen von Netzwerksicherheitsgruppen |
| Microsoft.ClassicNetwork/reservedIps/link/action |Verknüpfen von reservierten IP-Adressen |
| Microsoft.ClassicNetwork/reservedIps/read |Lesen von reservierten IP-Adressen |
| Microsoft.ClassicNetwork/virtualNetworks/join/action |Verknüpfen von virtuellen Netzwerken |
| Microsoft.ClassicNetwork/virtualNetworks/read |Lesen von virtuellen Netzwerken |
| Microsoft.ClassicStorage/storageAccounts/disks/read |Lesen von Speicherkontodatenträgern |
| Microsoft.ClassicStorage/storageAccounts/images/read |Lesen von Speicherkontoimages |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action |Auflisten von Speicherkontoschlüsseln |
| Microsoft.ClassicStorage/storageAccounts/read |Lesen von klassischen Speicherkonten |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="virtual-machine-contributor"></a>Mitwirkender von virtuellen Computern
Kann virtuelle Computer verwalten, jedoch nicht das virtuelle Netzwerk oder Speicherkonto, mit dem sie verbunden sind

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Autorisierungen |
| Microsoft.Compute/availabilitySets/* |Erstellen und Verwalten von Compute-Verfügbarkeitsgruppen |
| Microsoft.Compute/locations/* |Erstellen und Verwalten von Compute-Speicherorten |
| Microsoft.Compute/virtualMachines/* |Erstellen und Verwalten von virtuellen Computern |
| Microsoft.Compute/virtualMachineScaleSets |Erstellen und Verwalten von Skalierungsgruppen für virtuelle Computer |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action |Verknüpfen von Back-End-Adresspools für Netzwerkanwendungsgateway |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action |Verknüpfen von Back-End-Adresspools für Load Balancer |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action |Verknüpfen von eingehenden NAT-Pools für Load Balancer |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action |Verknüpfen von eingehenden NAT-Regeln für Load Balancer |
| Microsoft.Network/loadBalancers/read |Lesen von Load Balancern |
| Microsoft.Network/locations/* |Erstellen und Verwalten von Netzwerkspeicherorten |
| Microsoft.Network/networkInterfaces/* |Erstellen und Verwalten von Netzwerkschnittstellen |
| Microsoft.Network/networkSecurityGroups/join/action |Verknüpfen von Netzwerksicherheitsgruppen |
| Microsoft.Network/networkSecurityGroups/read |Lesen von Netzwerksicherheitsgruppen |
| Microsoft.Network/publicIPAddresses/join/action |Verknüpfen von öffentlichen IP-Adressen im Netzwerk |
| Microsoft.Network/publicIPAddresses/read |Lesen von öffentlichen IP-Adressen im Netzwerk |
| Microsoft.Network/virtualNetworks/read |Lesen von virtuellen Netzwerken |
| Microsoft.Network/virtualNetworks/subnets/join/action |Verknüpfen von Subnetzen in virtuellen Netzwerken |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Storage/storageAccounts/listKeys/action |Auflisten von Speicherkontoschlüsseln |
| Microsoft.Storage/storageAccounts/read |Lesen von Speicherkonten |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="classic-network-contributor"></a>Mitwirkender von klassischem Netzwerk
Kann klassische virtuelle Netzwerke und reservierte IP-Adressen verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Autorisierungen |
| Microsoft.ClassicNetwork/* |Erstellen und Verwalten von klassischen Netzwerken |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |

### <a name="web-plan-contributor"></a>Mitwirkender von Webplan
Kann Webpläne verwalten

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Autorisierungen |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |
| Microsoft.Web/serverFarms/* |Erstellen und Verwalten von Serverfarmen |

### <a name="website-contributor"></a>Mitwirkender von Website
Kann Websites verwalten, jedoch nicht die Webpläne, mit denen sie verbunden sind

| **Aktionen** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Lesen von Autorisierungen |
| Microsoft.Insights/alertRules/* |Erstellen und Verwalten von Insights-Warnungsregeln |
| Microsoft.Insights/components/* |Erstellen und Verwalten von Insights-Komponenten |
| Microsoft.ResourceHealth/availabilityStatuses/read |Lesen des Status der Ressourcen |
| Microsoft.Resources/deployments/* |Erstellen und Verwalten von Ressourcengruppenbereitstellungen |
| Microsoft.Resources/subscriptions/resourceGroups/read |Lesen von Ressourcengruppen |
| Microsoft.Support/* |Erstellen und Verwalten von Support-Tickets |
| Microsoft.Web/certificates/* |Erstellen und Verwalten von Websitezertifikaten |
| Microsoft.Web/listSitesAssignedToHostName/read |Lesen von Websites, die einem Hostnamen zugewiesen sind |
| Microsoft.Web/serverFarms/join/action |Verknüpfen von Serverfarmen |
| Microsoft.Web/serverFarms/read |Lesen von Serverfarmen |
| Microsoft.Web/sites/* |Erstellen und Verwalten von Websites (die Erstellung von Websites erfordert auch Schreibberechtigungen für den zugehörigen App Service-Plan) |

## <a name="see-also"></a>Weitere Informationen
* [Rollenbasierte Zugriffssteuerung](role-based-access-control-configure.md): Erste Schritte mit RBAC im Azure-Portal.
* [Benutzerdefinierte Rollen in Azure RBAC](role-based-access-control-custom-roles.md): Erfahren Sie, wie Sie benutzerdefinierte Rollen entsprechend Ihren Zugriffsanforderungen erstellen.
* [Erstellen eines Verlaufsberichts zu Zugriffsänderungen:](role-based-access-control-access-change-history-report.md)Verfolgen Sie Änderungen an Rollenzuweisungen in RBAC.
* [Problembehandlung bei rollenbasierter Zugriffssteuerung:](role-based-access-control-troubleshooting.md)Sehen Sie sich Vorschläge zur Behebung häufig auftretender Probleme an.
