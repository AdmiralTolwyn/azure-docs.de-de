---
title: SQL-Datenbankanwendungsentwicklung – Übersicht | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über verfügbare Verbindungsbibliotheken und bewährte Methoden für Anwendungen, die eine Verbindung mit SQL-Datenbank herstellen.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
manager: craigg
ms.date: 06/20/2018
ms.openlocfilehash: 707e10f77bf00ed12f09a23e490105f52ceed4ab
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241598"
---
# <a name="sql-database-application-development-overview"></a>Übersicht zur SQL-Datenbankanwendungsentwicklung
In diesem Artikel werden die grundlegenden Aspekte beschrieben, die ein Entwickler beim Schreiben von Code zum Herstellen einer Verbindung mit Azure SQL-Datenbanken berücksichtigen sollte.

> [!TIP]
> Das [Tutorial zu den ersten Schritten](sql-database-get-started-portal.md) veranschaulicht, wie Sie einen Server und eine serverbasierte Firewall erstellen, Servereigenschaften anzeigen, eine Verbindung mithilfe von SQL Server Management Studio herstellen, die Masterdatenbank abfragen, eine Beispieldatenbank und eine leere Datenbank erstellen, Datenbankeigenschaften abfragen, und die Beispieldatenbank abfragen.
>

## <a name="language-and-platform"></a>Sprache und Plattform
Für eine Vielzahl von Programmiersprachen und Plattformen sind Codebeispiele verfügbar. Links zu den Codebeispielen finden Sie unter: 

* Weitere Informationen: [Verbindungsbibliotheken für SQL-Datenbank und SQL Server](sql-database-libraries.md).

## <a name="tools"></a>Tools 
Sie können Open-Source-Tools wie [Cheetah](https://github.com/wunderlist/cheetah), [Sql-Cli](https://www.npmjs.com/package/sql-cli) und [VS Code](https://code.visualstudio.com/) nutzen. Darüber hinaus funktioniert Azure SQL-Datenbank mit Microsoft-Tools wie [Visual Studio](https://www.visualstudio.com/downloads/) und [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).  Sie können auch das Azure-Verwaltungsportal, PowerShell und REST-APIs nutzen, um produktiver zu arbeiten.

## <a name="resource-limitations"></a>Ressourcenbeschränkungen
Azure SQL-Datenbank verwaltet die für eine Datenbank verfügbaren Ressourcen mithilfe zweier verschiedener Mechanismen: Ressourcenkontrolle und Durchsetzung von Grenzwerten. Weitere Informationen finden Sie unter

- [Einschränkungen des DTU-basierten Ressourcenmodells – Einzeldatenbank](sql-database-dtu-resource-limits-single-databases.md)
- [Einschränkungen des DTU-basierten Ressourcenmodells – Pools für elastische Datenbanken](sql-database-dtu-resource-limits-elastic-pools.md)
- [V-Kern-basierte Ressourceneinschränkungen – Einzeldatenbanken](sql-database-vcore-resource-limits-single-databases.md)
- [V-Kern-basierte Ressourceneinschränkungen – Pools für elastische Datenbanken](sql-database-vcore-resource-limits-elastic-pools.md)

## <a name="security"></a>Sicherheit
Azure SQL-Datenbank stellt Ressourcen zum Einschränken des Zugriffs, zum Schützen von Daten sowie zum Überwachen von Aktivitäten in einer SQL-Datenbank bereit.

* Weitere Informationen: [Sichern der SQL-Datenbank](sql-database-security-overview.md)

## <a name="authentication"></a>Authentifizierung
* Azure SQL-Datenbank unterstützt Benutzer und Anmeldungen mit SQL Server-Authentifizierung sowie Benutzer und Anmeldungen mit [Azure Active Directory-Authentifizierung](sql-database-aad-authentication.md) .
* Sie müssen eine bestimmte Datenbank angeben, anstatt automatisch die *master* -Datenbank zu verwenden.
* Sie können die Transact-SQL-Anweisung **USE myDatabaseName;** in SQL-Datenbanken nicht verwenden, um zu einer anderen Datenbank zu wechseln.
* Weitere Informationen: [Sicherheit von SQL-Datenbank: Verwalten von Datenbankzugriff und Anmeldesicherheit](sql-database-manage-logins.md)

## <a name="resiliency"></a>Resilienz
Tritt beim Verbinden mit SQL-Datenbank ein vorübergehender Fehler auf, muss der Code den Aufruf wiederholen.  Die Wiederholungslogik sollte Backofflogik verwenden, damit die SQL-Datenbank nicht unnötig überlastet wird, wenn mehrere Clients die Wiederholung gleichzeitig durchführen.

* Codebeispiele: Codebeispiele zur Veranschaulichung der Wiederholungslogik finden Sie in den Beispielen für die Sprache Ihrer Wahl unter [Verbindungsbibliotheken für SQL-Datenbank und SQL Server](sql-database-libraries.md).
* Weitere Informationen: [Fehlermeldungen für Clientprogramme von SQL-Datenbank](sql-database-develop-error-messages.md)

## <a name="managing-connections"></a>Verwalten von Verbindungen
* Ändern Sie in der Clientverbindungslogik das Standardtimeout in 30 Sekunden.  Der Standardwert von 15 Sekunden ist zu kurz für Verbindungen, die über das Internet hergestellt werden.
* Wenn Sie einen [Verbindungspool](https://msdn.microsoft.com/library/8xx3tyca.aspx)verwenden, trennen Sie unbedingt die Verbindung, sobald Ihre Anwendung sie nicht aktiv verwendet und nicht gerade auf die erneute Verwendung der Verbindung vorbereitet wird.

## <a name="network-considerations"></a>Netzwerküberlegungen
* Vergewissern Sie sich, dass auf dem Computer, der das Clientprogramm hostet, die Firewall ausgehende TCP-Kommunikation über Port 1433 zulässt.  Weitere Informationen: [Konfigurieren einer Firewall für Azure SQL-Datenbank](sql-database-configure-firewall-settings.md)
* Wenn Ihr Clientprogramm eine Verbindung mit SQL-Datenbank herstellt, wobei der Client auf einem virtuellen Azure-Computer ausgeführt wird, müssen Sie bestimmte Portbereiche auf dem virtuellen Computer öffnen. Weitere Informationen: [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank](sql-database-develop-direct-route-ports-adonet-v12.md)
* Bei Clientverbindungen mit Azure SQL-Datenbankwird der Proxy manchmal umgangen und direkt mit der Datenbank interagiert. Andere Ports als 1433 werden wichtig. Weitere Informationen finden Sie unter [Verbindungsarchitektur der Azure SQL-Datenbank](sql-database-connectivity-architecture.md) und [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="data-sharding-with-elastic-scale"></a>Datensharding mit elastischer Skalierung
Die elastische Skalierung vereinfacht das horizontale Hoch- und Herunterskalieren. 

* [Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen und Azure SQL-Datenbank](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Datenabhängiges Routing](sql-database-elastic-scale-data-dependent-routing.md)
* [Erste Schritte mit der Vorschauversion von Elastic Scale für Azure SQL-Datenbank](sql-database-elastic-scale-get-started.md)

## <a name="next-steps"></a>Nächste Schritte
Entdecken Sie alle [Funktionen von SQL-Datenbank](sql-database-technical-overview.md).
