<properties
   pageTitle="Konfigurieren einer SQL Server-Firewall (Übersicht) | Microsoft Azure"
   description="Erfahren Sie, wie eine SQL-Datenbankfirewall mit der Firewallregeln auf Server- und Datenbankebene zum Verwalten des Zugriffs konfigurieren."
   keywords="Datenbankfirewall"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor="cgronlun"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="06/10/2016"
   ms.author="rickbyh"/>

# So wird's gemacht: Konfigurieren einer Azure SQL Server-Firewall – Übersicht


> [AZURE.SELECTOR]
- [Übersicht](sql-database-firewall-configure.md)
- [Azure-Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST-API](sql-database-configure-firewall-settings-rest.md)


Die Microsoft Azure SQL-Datenbank bietet einen relationalen Datenbankdienst für Azure und andere internetbasierte Anwendungen. Zum Schutz Ihrer Daten verhindern Firewalls jeglichen Zugriff auf Ihren Datenbankserver, bis Sie angeben, welche Computer zugriffsberechtigt sind. Die Firewall gewährt den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.

Zum Konfigurieren der Firewall erstellen Sie Firewallregeln, die Bereiche zulässiger IP-Adressen festlegen. Sie können Firewallregeln auf Server- und Datenbankebene erstellen.

- **Firewallregeln auf Serverebene:** Diese Regeln ermöglichen es Clients, auf den gesamten Azure SQL Server zuzugreifen (also auf alle Datenbanken innerhalb des gleichen logischen Servers). Diese Regeln sind in der **master**-Datenbank gespeichert. Firewallregeln auf Serverebene können über das Portal oder mithilfe von Transact-SQL-Anweisungen konfiguriert werden.
- **Firewallregeln auf Datenbankebene:** Diese Regeln ermöglichen es Clients, auf einzelne Datenbanken innerhalb des Azure SQL-Datenbankservers zuzugreifen. Diese Regeln werden pro Datenbank erstellt und in den einzelnen Datenbanken gespeichert (einschließlich **master**). Diese Regeln können beim Einschränken des Zugriffs auf bestimmte (sichere) Datenbanken innerhalb desselben logischen Servers hilfreich sein. Firewallregeln auf Datenbankebene können nur mithilfe von Transact-SQL-Anweisungen konfiguriert werden.

**Empfehlung:** Microsoft empfiehlt, nach Möglichkeit Firewallregeln auf Datenbankebene zu verwenden, um die Sicherheit und die Portabilität der Datenbank zu verbessern. Verwenden Sie Firewallregeln auf Serverebene für Administratoren und wenn Sie über viele Datenbanken mit identischen Zugriffsanforderungen verfügen und die Datenbanken nicht einzeln konfigurieren möchten.


## Firewallübersicht

Anfänglich wird jeglicher Transact-SQL-Zugriff auf Ihren Azure SQL Server durch die Firewall blockiert. Damit der Azure SQL Server verwendet werden kann, müssen Sie zum Azure-Portal wechseln und mindestens eine Firewallregel auf Serverebene festlegen, die den Zugriff auf den Azure SQL Server ermöglicht. Geben Sie mithilfe der Firewallregeln an, welche IP-Adressbereiche aus dem Internet zulässig sind und ob Azure-Anwendungen versuchen können, eine Verbindung mit dem Azure SQL Server herzustellen.

Wenn Sie jedoch selektiv nur den Zugriff auf eine der Datenbanken auf dem Azure SQL Server gewähren möchten, müssen Sie für die erforderliche Datenbank eine Regel auf Datenbankebene mit einem IP-Adressbereich erstellen, der über den IP-Adressbereich hinausgeht, der in der Firewallregel auf Serverebene angegeben ist, und sicherstellen, dass die IP-Adresse des Clients in dem Bereich liegt, der in der Regel auf Datenbankebene angegeben ist.

Verbindungsversuche über das Internet und Azure müssen zunächst die Firewall passieren, bevor sie Ihren Azure SQL Server oder Ihre SQL-Datenbank erreichen (wie im folgenden Diagramm dargestellt).

   ![Diagramm zur Beschreibung der Firewallkonfiguration][1]

## Herstellen einer Verbindung über das Internet

Wenn ein Computer versucht, eine Verbindung mit dem Datenbankserver über das Internet herzustellen, prüft die Firewall die Ursprungs-IP-Adresse der Anforderung anhand sämtlicher Firewallregeln auf Serverebene und (falls erforderlich) auf Datenbankebene:

- Liegt die IP-Adresse der Anforderung innerhalb eines der in den Firewallregeln auf Serverebene angegebenen Bereiche, wird die Verbindung mit dem Azure SQL-Datenbankserver gewährt.

- Liegt die IP-Adresse der Anforderung nicht innerhalb eines der in den Firewallregeln auf Serverebene angegebenen Bereiche, werden die Firewallregeln auf Datenbankebene geprüft. Liegt die IP-Adresse der Anforderung innerhalb eines der in den Firewallregeln auf Datenbankebene angegebenen Bereiche, wird nur die Verbindung mit der Datenbank gewährt, die eine entsprechende Regel auf Datenbankebene aufweist.

- Liegt die IP-Adresse der Anforderung nicht innerhalb der in den Firewallregeln auf Serverebene oder Datenbankebene angegebenen Bereiche, schlägt die Verbindungsanforderung fehl.

> [AZURE.NOTE] Um von Ihrem lokalen Computer aus auf die Azure SQL-Datenbank zuzugreifen, stellen Sie sicher, dass die Firewall im Netzwerk und auf dem lokalen Computer eine ausgehende Kommunikation an TCP-Port 1433 zulässt.


## Herstellen einer Verbindung über Azure

Wenn eine Azure-Anwendung versucht, eine Verbindung mit dem Datenbankserver herzustellen, prüft die Firewall, ob Azure-Verbindungen zulässig sind. Eine Firewalleinstellung, bei der die Start- und Endadresse den Wert 0.0.0.0 aufweist, gibt an, dass diese Verbindungen zulässig sind. Ist der Verbindungsversuch nicht zulässig, gelangt die Anforderung nicht zum Azure SQL-Datenbankserver.

Sie können Verbindungen über Azure auf zwei Arten ermöglichen:

- Aktivieren Sie im [Microsoft Azure-Portal](https://portal.azure.com/) beim Erstellen eines neuen Servers das Kontrollkästchen **Azure-Diensten Zugriff auf den Server erlauben**.

- Klicken Sie im [klassischen Portal](http://go.microsoft.com/fwlink/p/?LinkID=161793) auf der Registerkarte **Konfigurieren** eines Servers im Abschnitt **Zulässige Dienste** für **Microsoft Azure-Dienste** auf **Ja**.

## Erstellen der ersten Firewallregel auf Serverebene

Die erste Firewalleinstellung auf Serverebene kann mit dem [Azure-Portal](https://portal.azure.com/) oder programmgesteuert mithilfe der REST-API oder Azure PowerShell erstellt werden. Nachfolgende Firewallregeln auf Serverebene können anhand dieser Methoden sowie über Transact-SQL erstellt und verwaltet werden. Weitere Informationen zu Firewallregeln auf Serverebene finden Sie unter [So wird's gemacht: Konfigurieren einer Azure SQL Server-Firewall über das Azure-Portal](sql-database-configure-firewall-settings.md).

## Erstellen von Firewallregeln auf Datenbankebene

Nachdem Sie die erste Firewall auf Serverebene konfiguriert haben, können Sie den Zugriff auf bestimmte Datenbanken einschränken. Wenn Sie in der Firewallregel auf Datenbankebene einen IP-Adressbereich angeben, der außerhalb des Bereichs liegt, der in der Firewallregel auf Serverebene angegeben ist, können nur die Clients auf die Datenbank zugreifen, die IP-Adressen in dem auf Datenbankebene angegebenen Bereich aufweisen. Es können maximal 128 Firewallregeln auf Datenbankebene für eine Datenbank verwendet werden. Firewallregeln auf Datenbankebene für Master- und Benutzerdatenbanken können über Transact-SQL erstellt und verwaltet werden. Weitere Informationen zum Konfigurieren von Firewallregeln auf Datenbankebene finden Sie unter [sp\_set\_database\_firewall\_rule (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/dn270010.aspx).

## Programmgesteuertes Verwalten von Firewallregeln

Außer im Azure-Portal können Firewallregeln mithilfe von Transact-SQL, REST-API und Azure PowerShell programmgesteuert verwaltet werden. In den folgenden Tabellen sind die Gruppen von Befehlen beschrieben, die für die jeweilige Methode zur Verfügung stehen.


### Transact-SQL

| Katalogsicht oder gespeicherte Prozedur | Ebene | Beschreibung |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [sys.firewall\_rules](https://msdn.microsoft.com/library/dn269980.aspx) | Server | Zeigt die aktuellen Firewallregeln auf Serverebene an. |
| [sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) | Server | Erstellt oder aktualisiert Firewallregeln auf Serverebene. |
| [sp\_delete\_firewall\_rule](https://msdn.microsoft.com/library/dn270024.aspx) | Server | Entfernt Firewallregeln auf Serverebene. |
| [sys.database\_firewall\_rules](https://msdn.microsoft.com/library/dn269982.aspx) | Datenbank | Zeigt die aktuellen Firewallregeln auf Datenbankebene an. |
| [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) | Datenbank | Erstellt oder aktualisiert Firewallregeln auf Datenbankebene. |
| [sp\_delete\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270030.aspx) | Datenbanken | Entfernt Firewallregeln auf Datenbankebene. |

### REST-API

| API | Ebene | Beschreibung |
|----------------------|--------|------------------------------------------------------------------|
| [List Firewall Rules](https://msdn.microsoft.com/library/azure/dn505715.aspx) | Server | Zeigt die aktuellen Firewallregeln auf Serverebene an. |
| [Create Firewall Rule](https://msdn.microsoft.com/library/azure/dn505712.aspx) | Server | Erstellt oder aktualisiert Firewallregeln auf Serverebene. |
| [Set Firewall Rule](https://msdn.microsoft.com/library/azure/dn505707.aspx) | Server | Aktualisiert die Eigenschaften einer vorhandenen Firewallregel auf Serverebene. |
| [Delete Firewall Rule](https://msdn.microsoft.com/library/azure/dn505706.aspx) | Server | Entfernt Firewallregeln auf Serverebene. |


### Azure PowerShell

| Cmdlet | Ebene | Beschreibung |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx) | Server | Gibt die aktuellen Firewallregeln auf Serverebene zurück. |
| [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) | Server | Erstellt eine neue Firewallregel auf Serverebene. |
| [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx) | Server | Aktualisiert die Eigenschaften einer vorhandenen Firewallregel auf Serverebene. |
| [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) | Server | Entfernt Firewallregeln auf Serverebene. |

> [AZURE.NOTE] Es kann bis zu fünf Minuten dauern, bis die Änderungen der Firewalleinstellungen wirksam werden.

## Problembehandlung der Datenbankfirewall

Wenn der Zugriff auf den Microsoft Azure SQL-Datenbankdienst nicht das erwartete Verhalten aufweist, sind folgende Punkte zu beachten:

- **Lokale Firewallkonfiguration:** Bevor Ihr Computer auf Azure SQL-Datenbank zugreifen kann, müssen Sie möglicherweise eine Firewallausnahme auf Ihrem Computer für TCP-Port 1433 erstellen. Sie müssen möglicherweise zusätzliche Ports öffnen, wenn Sie Verbindungen innerhalb der Grenzen der Azure-Cloud herstellen möchten. Weitere Informationen finden Sie im Abschnitt **SQL-Datenbank V12: „Außerhalb“ im Vergleich zu „Innerhalb“** im Artikel [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank V12](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Netzwerkadressübersetzung (NAT):** Aufgrund von NAT kann die IP-Adresse, die vom Computer zum Herstellen einer Verbindung mit Azure SQL-Datenbank verwendet wird, von der IP-Adresse abweichen, die in den IP-Konfigurationseinstellungen des Computers angezeigt wird. Melden Sie sich beim Portal an, und navigieren Sie zur Registerkarte **Konfigurieren** für den Server, auf dem die Datenbank gehostet wird, um die IP-Adresse anzuzeigen, die zum Herstellen einer Verbindung mit Azure verwendet wird. Im Abschnitt **Zulässige IP-Adressen** wird die aktuelle Client-IP-Adresse angezeigt. Klicken Sie auf **Hinzufügen** für **Zulässige IP-Adressen**, um diesem Computer den Zugriff auf den Server zu gestatten.

- **Änderungen an der Zulassungsliste sind noch nicht wirksam:** Es kann zu einer Verzögerung von bis zu fünf Minuten kommen, bevor Änderungen an der Firewallkonfiguration für Azure SQL-Datenbank wirksam werden.

- **Die Anmeldung ist nicht autorisiert, oder ein falsches Kennwort wurde verwendet:** Wenn eine Anmeldung über keine Berechtigungen für den Azure SQL-Datenbankserver verfügt oder das verwendete Kennwort falsch ist, wird die Verbindung mit dem Azure SQL-Datenbankserver verweigert. Durch das Erstellen einer Firewalleinstellung wird Clients lediglich die Möglichkeit gegeben, eine Verbindung mit dem Server herzustellen. Jeder Client muss die erforderlichen Sicherheitsanmeldeinformationen bereitstellen. Weitere Informationen zum Vorbereiten von Anmeldungen finden Sie unter "Verwalten von Datenbanken, Anmeldungen und Benutzern in der Azure SQL-Datenbank.

- **Dynamische IP-Adresse:** Wenn Sie über eine Internetverbindung mit dynamischer IP-Adressierung verfügen und Probleme beim Passieren der Firewall auftreten, können Sie eine der folgenden Lösungen ausprobieren:

 - Fragen Sie Ihren Internetdienstanbieter (ISP) nach dem IP-Adressbereich, der den Clientcomputern zugewiesen ist, mit denen auf den Azure SQL-Datenbankserver zugegriffen wird, und fügen Sie dann den IP-Adressbereich als eine Firewallregel hinzu.

 - Verwenden Sie stattdessen die statische IP-Adressierung für die Clientcomputer, und fügen Sie dann die IP-Adressen als Firewallregeln hinzu.

## Weitere Informationen

[So wird's gemacht: Konfigurieren einer Azure SQL Server-Firewall über das Azure-Portal](sql-database-configure-firewall-settings.md)

[Sicherheitscenter für SQL Server-Datenbankmodul und Azure SQL-Datenbank](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

<!---HONumber=AcomDC_0615_2016-->