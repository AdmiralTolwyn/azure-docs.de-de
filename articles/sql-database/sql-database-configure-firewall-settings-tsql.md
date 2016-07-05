<properties
	pageTitle="Konfigurieren von Firewallregeln auf Serverebene und Datenbankebene für Azure SQL-Datenbank mithilfe von T-SQL | Microsoft Azure"
	description="Sie erfahren, wie Sie die Firewall für IP-Adressen mit Zugriff auf Azure SQL-Datenbanken konfigurieren."
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="06/15/2016"
	ms.author="rickbyh"/>


# Konfigurieren von Firewallregeln auf Serverebene und Datenbankebene für Azure SQL-Datenbank mithilfe von T-SQL


> [AZURE.SELECTOR]
- [Übersicht](sql-database-firewall-configure.md)
- [Azure-Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST-API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL-Datenbank verwendet Firewallregeln, um Verbindungen mit Ihren Servern und Datenbanken zu erlauben. Sie können auf Ihrem Azure SQL-Datenbank-Server auf Serverebene und Datenbankebene Firewalleinstellungen für die Masterdatenbank oder eine Benutzerdatenbank definieren, um den selektiven Zugriff auf die Datenbank zu ermöglichen.

> [AZURE.IMPORTANT] Um Anwendungen von Azure die Verbindung mit dem Datenbankserver zu ermöglichen, müssen Azure-Verbindungen aktiviert sein. Weitere Informationen zu Firewallregeln und dem Aktivieren von Verbindungen aus Azure finden Sie unter [Firewall für Azure SQL-Datenbank](sql-database-firewall-configure.md). Sie müssen möglicherweise einige zusätzliche TCP-Ports öffnen, wenn Sie Verbindungen innerhalb der Grenzen der Azure-Cloud herstellen möchten. Weitere Informationen finden Sie im Abschnitt **SQL-Datenbank V12: "Außerhalb" im Vergleich zu "Innerhalb"** im Artikel [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank V12](sql-database-develop-direct-route-ports-adonet-v12.md)


## Verwalten von Firewallregeln auf Serverebene über Transact-SQL

Firewallregeln auf Serverebene über Transact-SQL können nur über die Prinzipalanmeldung auf Serverebene oder vom Azure Active Directory-Administrator erstellt werden.

1. Starten Sie ein Abfragefenster, und stellen Sie mithilfe von SQL Server Management Studio eine Verbindung mit der virtuellen Masterdatenbank her.
2. Firewallregeln auf Serverebene können innerhalb des Abfragefensters ausgewählt, erstellt, aktualisiert oder gelöscht werden.
3. Führen Sie zum Erstellen oder Aktualisieren von Firewallregeln auf Serverebene die gespeicherte Prozedur "sp\_set\_firewall\_rule" aus. Im folgenden Beispiel wird ein Bereich von IP-Adressen auf dem Server "Contoso" aktiviert.<br/>Zeigen Sie zunächst die vorhandenen Regeln an.

		SELECT * FROM sys.firewall_rules ORDER BY name;

	Fügen Sie anschließend eine Firewallregel hinzu.

		EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
			@start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	Um eine Firewallregel auf Serverebene zu löschen, führen Sie die gespeicherte Prozedur "sp\_delete\_firewall\_rule" aus. Im folgenden Beispiel wird die Regel mit dem Namen "ContosoFirewallRule" gelöscht.
 
		EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 Weitere Informationen zu diesen gespeicherten Prozeduren finden Sie unter [sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) sowie unter [sp\_delete\_firewall\_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## Firewallregeln auf Datenbankebene

Firewallregeln auf Datenbankebene können nur von Datenbankbenutzern erstellt werden, die für die Datenbank über die Berechtigung **CONTROL** verfügen (also beispielsweise von Datenbankbesitzern).

1. Starten Sie nach dem Erstellen einer Firewall auf Serverebene für Ihre IP-Adresse ein Abfragefenster über das klassische Portal oder über SQL Server Management Studio.
2. Stellen Sie eine Verbindung mit der Datenbank her, für die Sie eine Firewallregel auf Datenbankebene erstellen möchten.

	Um eine neue Firewallregel auf Datenbankebene zu erstellen oder eine vorhandene zu ändern, führen Sie die gespeicherte Prozedur "sp\_set\_database\_firewall\_rule" aus. Im folgenden Beispiel wird eine neue Firewallregel mit dem Namen "ContosoFirewallRule" erstellt.
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
		    @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	Um eine vorhandene Firewallregel auf Datenbankebene zu löschen, führen Sie die gespeicherte Prozedur "sp\_delete\_database\_firewall\_rule" aus. Im folgenden Beispiel wird die Regel mit dem Namen "ContosoFirewallRule" gelöscht.
 
		EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Weitere Informationen zu diesen gespeicherten Prozeduren finden Sie unter [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) sowie unter [sp\_delete\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270030.aspx).

## Nächste Schritte

Artikel mit Anleitungen zum Erstellen von Firewallregeln auf Serverebene mit anderen Methoden finden Sie unter:

- [Konfigurieren von Firewallregeln auf Serverebene für Azure SQL-Datenbank mithilfe des Azure-Portals](sql-database-configure-firewall-settings.md)
- [Konfigurieren von Firewallregeln auf Serverebene für Azure SQL-Datenbank mithilfe von PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Konfigurieren von Firewallregeln auf Serverebene für Azure SQL-Datenbank mithilfe der REST-API](sql-database-configure-firewall-settings-rest.md)

Ein Tutorial zum Erstellen einer Datenbank finden Sie unter [Erstellen einer SQL-Datenbank in Minuten mit dem Azure-Portal](sql-database-get-started.md). Hilfe beim Herstellen einer Verbindung mit einer Azure SQL-Datenbank über Open-Source-Anwendungen oder Anwendungen von Drittanbietern finden Sie unter [Clientcodebeispiele für die ersten Schritte mit SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee336282.aspx). Informationen zum Navigieren zu Datenbanken finden Sie unter [Verwalten von Datenbankzugriff und Anmeldesicherheit](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## Zusätzliche Ressourcen

- [Sichern der Datenbank](sql-database-security.md)
- [Sicherheitscenter für SQL Server-Datenbankmodul und Azure SQL-Datenbank](https://msdn.microsoft.com/library/bb510589)

<!---HONumber=AcomDC_0622_2016-->