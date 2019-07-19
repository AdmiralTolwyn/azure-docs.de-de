---
title: Überwachungsprotokolle für Azure Database for MySQL
description: Beschreibt die Überwachungsprotokolle, die in Azure Database for MySQL verfügbar sind, sowie die verfügbaren Parameter zum Aktivieren von Protokolliergraden.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 86750cea5e7f0d4726f3e0e9a03795ef2a602d8b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443839"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Überwachungsprotokolle in Azure Database for MySQL

In Azure Database for MySQL ist das Überwachungsprotokoll für Benutzer verfügbar. Das Überwachungsprotokoll kann verwendet werden, um Aktivitäten auf Datenbankebene nachzuverfolgen, und es wird häufig für Konformitätszwecke genutzt.

> [!IMPORTANT]
> Diese Überwachungsprotokollfunktion befindet sich derzeit in der Vorschauphase.

## <a name="configure-audit-logging"></a>Konfigurieren der Überwachungsprotokollierung

Das Überwachungsprotokoll ist standardmäßig deaktiviert. Um es zu aktivieren, legen Sie `audit_log_enabled` auf EIN fest.

Weitere Parameter, die Sie anpassen können:

- `audit_log_events`: Steuert die zu protokollierenden Ereignisse. Unten ist eine Tabelle mit bestimmten Überwachungsereignissen angegeben.
- `audit_log_exclude_users`: MySQL-Benutzer, die aus der Protokollierung ausgeschlossen werden sollen. Es sind maximal vier Benutzer zulässig. Die maximale Länge des Parameters beträgt 256 Zeichen.

| **Event** | **Beschreibung** |
|---|---|
| `CONNECTION` | - Initiierung der Verbindung (erfolgreich oder nicht erfolgreich) <br> - Erneute Authentifizierung des Benutzers mit anderem Benutzernamen/Kennwort während der Sitzung <br> - Beendigung der Verbindung |
| `DML_SELECT`| SELECT-Abfragen |
| `DML_NONSELECT` | INSERT/DELETE/UPDATE-Abfragen |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Abfragen wie „DROP DATABASE“ |
| `DCL` | Abfragen wie „GRANT PERMISSION“ |
| `ADMIN` | Abfragen wie „SHOW STATUS“ |
| `GENERAL` | Alle in „DML_SELECT“, „DML_NONSELECT“, „DML“, „DDL“, „DCL“ und „ADMIN“ |
| `TABLE_ACCESS` | - Nur für MySQL 5.7 verfügbar <br> - Leseanweisungen für Tabelle, z. B. SELECT oder INSERT INTO... SELECT <br> - Löschanweisungen für Tabelle, z. B. DELETE oder TRUNCATE TABLE <br> - Einfügeanweisungen für Tabelle, z. B. INSERT oder REPLACE <br> - Aktualisierungsanweisungen für Tabelle, z. B. UPDATE |

## <a name="access-audit-logs"></a>Zugreifen auf Überwachungsprotokolle

Überwachungsprotokolle sind in Azure Monitor-Diagnoseprotokolle integriert. Nachdem Sie Überwachungsprotokolle auf Ihrem MySQL-Server aktiviert haben, können Sie sie an Azure Monitor-Protokolle, Event Hubs oder Azure Storage senden. Weitere Informationen zum Aktivieren von Diagnoseprotokollen im Azure-Portal finden Sie im Artikel zum [Portal für Überwachungsprotokolle](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Schemas für Diagnoseprotokolle

In den folgenden Abschnitten wird beschrieben, was von MySQL-Überwachungsprotokollen basierend auf dem Ereignistyp ausgegeben wird. Je nach Ausgabemethode können die enthaltenen Felder und ihre Reihenfolge variieren.

### <a name="connection"></a>Verbindung

| **Eigenschaft** | **Beschreibung** |
|---|---|
| `TenantId` | Ihre Mandanten-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Zeitstempel für den Aufzeichnungsbeginn des Protokolls in UTC |
| `Type` | Typ des Protokolls Immer `AzureDiagnostics` |
| `SubscriptionId` | GUID für das Abonnement, zu dem der Server gehört |
| `ResourceGroup` | Name der Ressourcengruppe, zu der der Server gehört |
| `ResourceProvider` | Name des Ressourcenanbieters Immer `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Ressourcen-URI |
| `Resource` | Name des Servers |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Name des Servers |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (nur für MySQL 5.7 verfügbar) |
| `connection_id_d` | Von MySQL generierte eindeutige Verbindungs-ID |
| `host_s` | Leer |
| `ip_s` | IP-Adresse des Clients, der die Verbindung mit MySQL herstellt |
| `user_s` | Name des Benutzers, der die Abfrage ausführt |
| `db_s` | Name der Datenbank, mit der die Verbindung besteht |
| `\_ResourceId` | Ressourcen-URI |

### <a name="general"></a>Allgemein

Das unten angegebene Schema gilt für die Ereignistypen „GENERAL“, „DML_SELECT“, „DML_NONSELECT“, „DML“, „DDL“, „DCL“ und „ADMIN“.

| **Eigenschaft** | **Beschreibung** |
|---|---|
| `TenantId` | Ihre Mandanten-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Zeitstempel für den Aufzeichnungsbeginn des Protokolls in UTC |
| `Type` | Typ des Protokolls Immer `AzureDiagnostics` |
| `SubscriptionId` | GUID für das Abonnement, zu dem der Server gehört |
| `ResourceGroup` | Name der Ressourcengruppe, zu der der Server gehört |
| `ResourceProvider` | Name des Ressourcenanbieters Immer `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Ressourcen-URI |
| `Resource` | Name des Servers |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Name des Servers |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` (nur für MySQL 5.6 verfügbar) |
| `event_time` | Sekunden des Abfragestarts im UNIX-Zeitstempel |
| `error_code_d` | Fehlercode, wenn für die Abfrage ein Fehler auftritt. `0` bedeutet, dass kein Fehler vorliegt |
| `thread_id_d` | ID des Threads, der die Abfrage ausgeführt hat |
| `host_s` | Leer |
| `ip_s` | IP-Adresse des Clients, der die Verbindung mit MySQL herstellt |
| `user_s` | Name des Benutzers, der die Abfrage ausführt |
| `sql_text_s` | Vollständiger Abfragetext |
| `\_ResourceId` | Ressourcen-URI |

### <a name="table-access"></a>Tabellenzugriff

| **Eigenschaft** | **Beschreibung** |
|---|---|
| `TenantId` | Ihre Mandanten-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Zeitstempel für den Aufzeichnungsbeginn des Protokolls in UTC |
| `Type` | Typ des Protokolls Immer `AzureDiagnostics` |
| `SubscriptionId` | GUID für das Abonnement, zu dem der Server gehört |
| `ResourceGroup` | Name der Ressourcengruppe, zu der der Server gehört |
| `ResourceProvider` | Name des Ressourcenanbieters Immer `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Ressourcen-URI |
| `Resource` | Name des Servers |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Name des Servers |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` oder `DELETE` |
| `connection_id_d` | Von MySQL generierte eindeutige Verbindungs-ID |
| `db_s` | Name der Datenbank, auf die zugegriffen wird |
| `table_s` | Name der Tabelle, auf die zugegriffen wird |
| `sql_text_s` | Vollständiger Abfragetext |
| `\_ResourceId` | Ressourcen-URI |

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Überwachungsprotokollen im Azure-Portal](howto-configure-audit-logs-portal.md)