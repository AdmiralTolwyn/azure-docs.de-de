---
title: "Firewallregeln für Azure SQL-Datenbank | Microsoft-Dokumentation"
description: Erfahren Sie, wie eine SQL-Datenbankfirewall mit der Firewallregeln auf Server- und Datenbankebene zum Verwalten des Zugriffs konfigurieren.
keywords: Datenbankfirewall
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: ac57f84c-35c3-4975-9903-241c8059011e
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 10/11/2017
ms.author: carlrab
ms.openlocfilehash: 1988bc7ab5b498db32d7bb40623f1194d7290b94
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/03/2018
---
# <a name="azure-sql-database-server-level-and-database-level-firewall-rules"></a>Firewallregeln auf Server- und Datenbankebene für Azure SQL-Datenbank 

Die Microsoft Azure SQL-Datenbank bietet einen relationalen Datenbankdienst für Azure und andere internetbasierte Anwendungen. Zum Schutz Ihrer Daten verhindern Firewalls jeglichen Zugriff auf Ihren Datenbankserver, bis Sie angeben, welche Computer zugriffsberechtigt sind. Die Firewall gewährt den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.

#### <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Regeln für virtuelle Netzwerke als Alternative zu IP-Regeln

Zusätzlich zu den IP-Regeln verwaltet die Firewall ebenfalls *virtuelle Netzwerkregeln*. Virtuelle Netzwerkregeln basieren auf den Virtual Network-Dienstendpunkten. Virtuelle Netzwerkregeln sollten den IP-Regeln in manchen Fällen vorgezogen werden. Weitere Informationen finden Sie unter [Virtual Network service endpoints and rules for Azure SQL Database (Virtuelle Netzwerkdienstendpunkte und -regeln für Azure SQL)](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Übersicht

Anfänglich wird jeglicher Transact-SQL-Zugriff auf Ihren Azure SQL Server durch die Firewall blockiert. Damit Sie Ihre Azure SQL Server-Instanz verwenden können, müssen Sie eine oder mehrere Firewallregeln auf Serverebene angeben, die Zugriff auf Ihre Azure SQL Server-Instanz ermöglichen. Geben Sie mit den Firewallregeln an, welche IP-Adressbereiche aus dem Internet zulässig sind, und ob Azure-Anwendungen versuchen können, eine Verbindung mit Ihrer Azure SQL Server-Instanz herzustellen.

Um selektiv Zugriff auf nur eine der Datenbanken in Ihrer Azure SQL Server-Instanz zu gewähren, müssen Sie eine Regel auf Datenbankebene für die erforderliche Datenbank erstellen. Geben Sie in der Firewallregel auf Datenbankebene einen IP-Adressbereich an, der außerhalb des Bereichs liegt, der in der Firewallregel auf Serverebene angegeben ist, und stellen Sie sicher, dass die IP-Adresse des Clients innerhalb des auf Datenbankebene angegebenen Bereichs liegt.

Verbindungsversuche über das Internet und Azure müssen zunächst die Firewall passieren, bevor sie Ihren Azure SQL-Server oder Ihre SQL-Datenbank erreichen (wie im folgenden Diagramm dargestellt):

   ![Diagramm zur Beschreibung der Firewallkonfiguration][1]

* **Firewallregeln auf Serverebene:** Diese Regeln ermöglichen es Clients, auf den gesamten Azure SQL Server zuzugreifen (also auf alle Datenbanken innerhalb des gleichen logischen Servers). Diese Regeln sind in der **master** -Datenbank gespeichert. Firewallregeln auf Serverebene können über das Portal oder mithilfe von Transact-SQL-Anweisungen konfiguriert werden. Zum Erstellen von Firewallregeln auf Serverebene mit dem Azure-Portal oder PowerShell müssen Sie der Besitzer oder ein Mitwirkender des Abonnements sein. Zum Erstellen einer Firewallregel auf Serverebene mit Transact-SQL müssen Sie die Verbindung mit der SQL-Datenbankinstanz als Prinzipalanmeldung auf Serverebene oder als Azure Active Directory-Administrator herstellen (d.h. ein Benutzer mit Azure-Berechtigungen muss zuerst eine Firewallregel auf Serverebene erstellen).
* **Firewallregeln auf Datenbankebene**: Diese Regeln ermöglichen Clients, auf bestimmte (sichere) Datenbanken innerhalb desselben logischen Servers zuzugreifen. Sie können diese Regeln für jede Datenbank (einschließlich der Datenbank **master**) erstellen, die in den einzelnen Datenbanken gespeichert werden. Firewallregeln auf Datenbankebene für Master- und Benutzerdatenbanken können nur mithilfe von Transact-SQL-Anweisungen erstellt und verwaltet werden und erst dann, nachdem Sie die erste Firewall auf Serverebene konfiguriert haben. Wenn Sie in der Firewallregel auf Datenbankebene einen IP-Adressbereich angeben, der außerhalb des Bereichs liegt, der in der Firewallregel auf Serverebene angegeben ist, können nur die Clients auf die Datenbank zugreifen, die IP-Adressen in dem auf Datenbankebene angegebenen Bereich aufweisen. Es können maximal 128 Firewallregeln auf Datenbankebene für eine Datenbank verwendet werden. Weitere Informationen zum Konfigurieren von Firewallregeln auf Datenbankebene finden Sie im Beispiel weiter unten in diesem Artikel und unter [sp_set_database_firewall_rule (Azure SQL-Datenbanken)](https://msdn.microsoft.com/library/dn270010.aspx).

**Empfehlung:** Microsoft empfiehlt, nach Möglichkeit Firewallregeln auf Datenbankebene zu verwenden, um die Sicherheit und die Portabilität der Datenbank zu verbessern. Verwenden Sie Firewallregeln auf Serverebene für Administratoren und wenn Sie über viele Datenbanken mit identischen Zugriffsanforderungen verfügen und die Datenbanken nicht einzeln konfigurieren möchten.

> [!Important]
> Microsoft Azure SQL-Datenbank unterstützt maximal 128 Firewallregeln.
>

> [!Note]
> Informationen zu portablen Datenbanken im Kontext der Geschäftskontinuität finden Sie unter [Authentifizierungsanforderungen für die Notfallwiederherstellung](sql-database-geo-replication-security-config.md).
>

### <a name="connecting-from-the-internet"></a>Herstellen einer Verbindung über das Internet

Wenn ein Computer versucht, über das Internet eine Verbindung mit dem Datenbankserver herzustellen, prüft die Firewall zuerst die Ursprungs-IP-Adresse der Anforderung anhand von Firewallregeln auf Serverebene für die Datenbank, die von der Verbindung angefordert wird:

* Liegt die IP-Adresse der Anforderung innerhalb eines der in den Firewallregeln auf Datenbankebene angegebenen Bereiche, wird die Verbindung der SQL-Datenbank gewährt, die die Regel enthält.
* Liegt die IP-Adresse der Anforderung nicht innerhalb eines der in den Firewallregeln auf Datenbankebene angegebenen Bereiche, werden die Firewallregeln auf Serverebene geprüft. Liegt die IP-Adresse der Anforderung innerhalb eines der in den Firewallregeln auf Serverebene angegebenen Bereiche, wird die Verbindung gewährt. Firewallregeln auf Serverebene wirken sich auf alle SQL-Datenbanken auf dem Azure SQL-Server aus.  
* Liegt die IP-Adresse der Anforderung nicht innerhalb der in den Firewallregeln auf Datenbankebene oder Serverebene angegebenen Bereiche, schlägt die Verbindungsanforderung fehl.

> [!NOTE]
> Um von Ihrem lokalen Computer aus auf die Azure SQL-Datenbank zuzugreifen, stellen Sie sicher, dass die Firewall im Netzwerk und auf dem lokalen Computer eine ausgehende Kommunikation an TCP-Port 1433 zulässt.
> 

### <a name="connecting-from-azure"></a>Herstellen einer Verbindung über Azure
Um Anwendungen von Azure die Verbindung mit dem Azure SQL-Server zu ermöglichen, müssen Azure-Verbindungen aktiviert sein. Wenn eine Azure-Anwendung versucht, eine Verbindung mit dem Datenbankserver herzustellen, prüft die Firewall, ob Azure-Verbindungen zulässig sind. Eine Firewalleinstellung, bei der die Start- und Endadresse den Wert 0.0.0.0 aufweist, gibt an, dass diese Verbindungen zulässig sind. Ist der Verbindungsversuch nicht zulässig, gelangt die Anforderung nicht zum Azure SQL-Datenbankserver.

> [!IMPORTANT]
> Diese Option konfiguriert die Firewall derart, dass alle von Azure ausgehenden Verbindungen zugelassen werden, einschließlich Verbindungen von den Abonnements anderer Kunden. Wenn Sie diese Option auswählen, stellen Sie sicher, dass die Anmelde- und die Benutzerberechtigungen den Zugriff nur auf autorisierte Benutzer beschränken.
> 

## <a name="creating-and-managing-firewall-rules"></a>Erstellen und Verwalten von Firewallregeln
Die erste Firewalleinstellung auf Serverebene kann im [Azure-Portal](https://portal.azure.com/) oder programmgesteuert mithilfe von [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql), der [Azure-CLI](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) oder der [REST-API](https://docs.microsoft.com/rest/api/sql/firewallrules) erstellt werden. Nachfolgende Firewallregeln auf Serverebene können anhand dieser Methoden sowie über Transact-SQL erstellt und verwaltet werden. 

> [!IMPORTANT]
> Firewallregeln auf Datenbankebene können nur mit Transact-SQL erstellt und verwaltet werden. 
>

Um die Leistung zu verbessern, werden Firewallregeln auf Serverebene vorübergehend auf Datenbankebene zwischengespeichert. Informationen zum Aktualisieren des Caches finden Sie unter [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). 

> [!TIP]
> Mithilfe der [SQL Server-Datenbanküberwachung](sql-database-auditing.md) können Sie Firewall-Änderungen auf Server- und Datenbankebene überwachen.
>

## <a name="manage-firewall-rules-using-the-azure-portal"></a>Verwalten von Firewallregeln über das Azure-Portal

Zum Festlegen einer Firewallregel auf Serverebene im Azure-Portal können Sie entweder zur Seite „Übersicht“ für die Azure SQL-Datenbank oder zur Seite „Übersicht“ für den logischen Server Ihrer Azure-Datenbank wechseln.

> [!TIP]
> Ein Tutorial finden Sie unter [Erstellen einer Datenbank im Azure-Portal](sql-database-get-started-portal.md).
>

**Auf der Seite „Übersicht“ der Datenbank**

1. Um eine Firewallregel auf Serverebene auf der Seite „Übersicht“ der Datenbank festzulegen, klicken Sie auf der Symbolleiste auf **Serverfirewall festlegen**, wie in der folgenden Abbildung dargestellt. Die Seite **Firewalleinstellungen** für den Server Ihrer SQL-Datenbank-Instanz wird geöffnet.

      ![Serverfirewallregel](./media/sql-database-get-started-portal/server-firewall-rule.png) 

2. Klicken Sie auf der Symbolleiste auf **Client-IP-Adresse hinzufügen**, um die IP-Adresse des Computers hinzuzufügen, den Sie derzeit verwenden. Klicken Sie dann auf **Speichern**. Eine Firewallregel auf Serverebene wird für Ihre aktuelle IP-Adresse erstellt.

      ![Festlegen der Serverfirewallregel](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

**Auf der Seite „Übersicht“ für den Server**

Die Übersichtsseite für Ihren Server wird geöffnet, die den vollqualifizierten Servernamen (z.B. **mynewserver20170403.database.windows.net**) und Optionen für die weitere Konfiguration enthält.

1. Um eine Regel auf Serverebene auf der Seite „Übersicht“ für den Server festzulegen, klicken Sie im linken Menü unter „Einstellungen“ auf **Firewall**: 

2. Klicken Sie auf der Symbolleiste auf **Client-IP-Adresse hinzufügen**, um die IP-Adresse des Computers hinzuzufügen, den Sie derzeit verwenden. Klicken Sie dann auf **Speichern**. Eine Firewallregel auf Serverebene wird für Ihre aktuelle IP-Adresse erstellt.

## <a name="manage-firewall-rules-using-transact-sql"></a>Verwalten von Firewallregeln mit Transact-SQL
| Katalogsicht oder gespeicherte Prozedur | Ebene | BESCHREIBUNG |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Zeigt die aktuellen Firewallregeln auf Serverebene an. |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Erstellt oder aktualisiert Firewallregeln auf Serverebene. |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Entfernt Firewallregeln auf Serverebene. |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Datenbank |Zeigt die aktuellen Firewallregeln auf Datenbankebene an. |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Datenbank |Erstellt oder aktualisiert Firewallregeln auf Datenbankebene. |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Datenbanken |Entfernt Firewallregeln auf Datenbankebene. |


In den folgenden Beispielen werden die vorhandenen Regeln überprüft, ein IP-Adressbereich auf dem Server „Contoso“ aktiviert und eine Firewallregel gelöscht:
   
```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```
  
Fügen Sie anschließend eine Firewallregel hinzu.
   
```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Um eine Firewallregel auf Serverebene zu löschen, führen Sie die gespeicherte Prozedur "sp_delete_firewall_rule" aus. Im folgenden Beispiel wird die Regel mit dem Namen „ContosoFirewallRule“ gelöscht:
   
```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```   

## <a name="manage-firewall-rules-using-azure-powershell"></a>Verwalten von Firewallregeln mit Azure PowerShell
| Cmdlet | Ebene | BESCHREIBUNG |
| --- | --- | --- |
| [Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule) |Server |Gibt die aktuellen Firewallregeln auf Serverebene zurück. |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) |Server |Erstellt eine neue Firewallregel auf Serverebene. |
| [Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule) |Server |Aktualisiert die Eigenschaften einer vorhandenen Firewallregel auf Serverebene. |
| [Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule) |Server |Entfernt Firewallregeln auf Serverebene. |


Im folgenden Beispiel wird mithilfe von PowerShell eine Firewallregel auf Serverebene festgelegt:

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> PowerShell-Beispiele im Kontext eines Schnellstarts finden Sie unter [Erstellen einer Datenbank – PowerShell](sql-database-get-started-powershell.md) und [Erstellen einer einzelnen Datenbank und Konfigurieren einer Firewallregel mithilfe von PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).
>

## <a name="manage-firewall-rules-using-azure-cli"></a>Verwalten von Firewallregeln mit Azure CLI
| Cmdlet | Ebene | BESCHREIBUNG |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Server|Erstellt eine Serverfirewallregel|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Server|Listet die Firewallregeln auf einem Server auf|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Server|Zeigt die Details einer Firewallregel an|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Server|Aktualisiert eine Firewallregel|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Server|Löscht eine Firewallregel|

Im folgenden Beispiel wird mithilfe der Azure CLI eine Firewallregel auf Serverebene festgelegt: 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Ein Azure CLI-Beispiel im Kontext eines Schnellstarts finden Sie unter [Erstellen einer Datenbank – Azure CLI](sql-database-get-started-cli.md) und [Erstellen einer einzelnen Datenbank und Konfigurieren einer Firewallregel mithilfe der Azure CLI](scripts/sql-database-create-and-configure-database-cli.md).
>

## <a name="manage-firewall-rules-using-rest-api"></a>Verwalten von Firewallregeln mithilfe der REST-API
| API | Ebene | BESCHREIBUNG |
| --- | --- | --- |
| [List Firewall Rules](https://docs.microsoft.com/rest/api/sql/FirewallRules/ListByServer) |Server |Zeigt die aktuellen Firewallregeln auf Serverebene an. |
| [Create or Update Firewall Rule](https://docs.microsoft.com/rest/api/sql/FirewallRules/CreateOrUpdate) |Server |Erstellt oder aktualisiert Firewallregeln auf Serverebene. |
| [Delete Firewall Rule](https://docs.microsoft.com/rest/api/sql/FirewallRules/Delete) |Server |Entfernt Firewallregeln auf Serverebene. |

## <a name="server-level-firewall-rule-versus-a-database-level-firewall-rule"></a>Firewallregel auf Serverebene im Vergleich zu Firewallregel auf Datenbankebene
F: Sollten Benutzer einer Datenbank vollständig von anderen Datenbanken isoliert werden?   
  Wenn ja, sollten Sie den Zugriff mit Firewallregeln auf Datenbankebene gewähren. Dadurch verhindern Sie die Verwendung von Firewallregeln auf Serverebene, die durch die Firewall den Zugriff auf alle Datenbanken ermöglichen und weniger Sicherheit bieten.   
 
F: Benötigen Benutzer unter der IP-Adresse Zugriff auf alle Datenbanken?   
  Verwenden Sie Firewallregeln auf Serverebene, wenn Sie weniger Firewallregeln konfigurieren möchten.   

F: Wird den Benutzern oder Teams, die die Firewall-Regeln konfigurieren, der Zugriff nur über das Azure-Portal, PowerShell oder die REST-API gewährt?   
  Dann müssen Sie Firewallregeln auf Serverebene verwenden. Firewallregeln auf Datenbankebene können nur mit Transact-SQL konfiguriert werden.  

F: Werden den Benutzern oder Teams, die die Firewall-Regeln konfigurieren, übergeordnete Berechtigungen auf Datenbankebene verweigert?   
  Verwenden Sie Firewallregeln auf Serverebene. Zum Konfigurieren von Firewallregeln auf Datenbankebene mit Transact-SQL benötigen Sie mindestens eine `CONTROL DATABASE`-Berechtigung auf Datenbankebene.  

F: Verwalten die Benutzer oder Teams, die die Firewall-Regeln konfigurieren oder überwachen, die Firewallregeln für viele (vielleicht sogar Hunderte) Datenbanken zentral?   
  Diese Entscheidung hängt von Ihren Anforderungen und Ihrer Umgebung ab. Firewallregeln auf Serverebene sind möglicherweise einfacher zu konfigurieren, doch durch Skripterstellung können Sie Regeln auf Datenbankebene konfigurieren. Selbst wenn Sie Firewallregeln auf Serverebene verwenden, müssen Sie möglicherweise Firewallregeln auf Datenbankebene überwachen, um zu prüfen, ob Benutzer mit der `CONTROL`-Berechtigung für die Datenbank Firewallregeln auf Datenbankebene erstellt haben.   

F: Kann ich ein Kombination aus Firewallregeln auf Serverebene und Firewallregeln auf Datenbankebene verwenden?   
  Ja. Einige Benutzer, z. B. Administratoren, benötigen möglicherweise Firewallregeln auf Serverebene. Andere Benutzer, z. B. Benutzer einer Datenbankanwendung, benötigen möglicherweise Firewallregeln auf Datenbankebene.   

## <a name="troubleshooting-the-database-firewall"></a>Problembehandlung der Datenbankfirewall
Wenn der Zugriff auf den Microsoft Azure SQL-Datenbankdienst nicht das erwartete Verhalten aufweist, sind folgende Punkte zu beachten:

* **Lokale Firewallkonfiguration:** Bevor Ihr Computer auf Azure SQL-Datenbank zugreifen kann, müssen Sie möglicherweise eine Firewallausnahme auf Ihrem Computer für TCP-Port 1433 erstellen. Wenn Sie innerhalb der Grenzen der Azure-Cloud Verbindungen herstellen, müssen Sie möglicherweise zusätzliche Ports öffnen. Weitere Informationen finden Sie im Abschnitt **SQL-Datenbank: ,Außerhalb‘ im Vergleich zu ,Innerhalb‘** im Artikel [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank](sql-database-develop-direct-route-ports-adonet-v12.md).
* **Netzwerkadressübersetzung (Network Address Translation, NAT):** Aufgrund von NAT kann sich die IP-Adresse, die Ihr Computer zur Verbindung mit Azure SQL-Datenbank verwendet, möglicherweise von der IP-Adresse unterscheiden, die in den IP-Konfigurationseinstellungen des Computers angezeigt wird. Um die IP-Adresse anzuzeigen, mit der der Computer die Verbindung mit Azure herstellt, melden Sie sich beim Portal an, und navigieren Sie zur Registerkarte **Konfigurieren** des Servers, der Ihre Datenbank hostet. Im Abschnitt **Zulässige IP-Adressen** wird die **aktuelle Client-IP-Adresse** angezeigt. Klicken Sie bei **Zulässige IP-Adressen** auf **Hinzufügen**, um diesem Computer den Zugriff auf den Server zu ermöglichen.
* **Änderungen an der Zulassungsliste sind noch nicht wirksam:** Änderungen der Firewallkonfiguration für die Azure SQL-Datenbank werden möglicherweise erst nach fünf Minuten wirksam.
* **Die Anmeldung ist nicht autorisiert, oder ein falsches Kennwort wurde verwendet**: Wenn eine Anmeldung nicht über Berechtigungen für den Azure SQL-Datenbankserver verfügt, oder das verwendete Kennwort falsch ist, wird die Verbindung mit dem Azure SQL-Datenbankserver verweigert. Durch das Erstellen einer Firewalleinstellung wird Clients lediglich die Möglichkeit gegeben, eine Verbindung mit dem Server herzustellen. Jeder Client muss die erforderlichen Sicherheitsanmeldeinformationen bereitstellen. Weitere Informationen zum Vorbereiten von Anmeldungen finden Sie unter "Verwalten von Datenbanken, Anmeldungen und Benutzern in der Azure SQL-Datenbank.
* **Dynamische IP-Adresse:** Wenn Sie über eine Internetverbindung mit dynamischer IP-Adressierung verfügen und Probleme beim Passieren der Firewall auftreten, können Sie eine der folgenden Lösungen ausprobieren:
  
  * Fragen Sie Ihren Internetdienstanbieter (ISP) nach dem IP-Adressbereich, der den Clientcomputern zugewiesen ist, mit denen auf den Azure SQL-Datenbankserver zugegriffen wird, und fügen Sie dann den IP-Adressbereich als eine Firewallregel hinzu.
  * Verwenden Sie stattdessen die statische IP-Adressierung für die Clientcomputer, und fügen Sie dann die IP-Adressen als Firewallregeln hinzu.

## <a name="next-steps"></a>Nächste Schritte

- Einen Schnellstart zum Erstellen einer Datenbank sowie einer Firewallregel auf Serverebene finden Sie unter [Erstellen einer Azure SQL-Datenbank](sql-database-get-started-portal.md).
- Hilfe beim Herstellen einer Verbindung mit einer Azure SQL-Datenbank über Open Source-Anwendungen oder Anwendungen von Drittanbietern finden Sie unter [Clientcodebeispiele für die ersten Schritte mit SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Informationen zu zusätzlichen Ports, die Sie ggf. öffnen müssen, finden Sie im Abschnitt **SQL-Datenbank: ,Außerhalb‘ im Vergleich zu ,Innerhalb‘** im Artikel [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank](sql-database-develop-direct-route-ports-adonet-v12.md).
- Eine Übersicht über die Sicherheit von Azure SQL-Datenbank finden Sie unter [Sichern der SQL-Datenbank](sql-database-security-overview.md).

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
