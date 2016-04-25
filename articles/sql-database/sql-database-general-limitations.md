<properties
   pageTitle="Azure SQL-Datenbanken – Allgemeine Einschränkungen und Leitlinien"
   description="Auf dieser Seite werden einige allgemeine Einschränkungen von Azure SQL-Datenbank sowie Aspekte der Interoperabilität und Unterstützung beschrieben."
   services="sql-database"
   documentationCenter="na"
   authors="carlrabeler"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="04/11/2016"
   ms.author="carlrab" />

# Azure SQL-Datenbanken – Allgemeine Einschränkungen und Leitlinien

In diesem Thema werden allgemeine Einschränkungen und Leitlinien für Azure SQL-Datenbank behandelt. Um Kontingente, Ressourcenverwaltung und Unterstützung besser zu verstehen, lesen Sie den Abschnitt [Zusätzliche Ressourcen](#additional-guidelines) am Ende dieses Themas.

## Konnektivität und Authentifizierung

  - Windows-Authentifizierung wird nicht unterstützt. Weitere Informationen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in Azure SQL-Datenbank](sql-database-manage-logins.md). Die Azure Active Directory-Authentifizierung wird jedoch mit bestimmten Einschränkungen unterstützt. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit SQL-Datenbank unter Verwendung der Azure Active Directory-Authentifizierung](sql-database-aad-authentication.md).

  - Microsoft Azure SQL-Datenbank unterstützt den TDS-Client (Tabular Data Stream) ab Version 7.3.

  - Nur TCP/IP-Verbindungen sind zulässig.

  - Der SQL Server 2008-Browser wird nicht unterstützt, da Microsoft Azure SQL-Datenbank keine dynamische Ports, sondern lediglich den Port 1433 hat.

## SQL Server-Agent/-Aufträge

Microsoft Azure SQL-Datenbank unterstützt nicht den SQL Server-Agent und keine SQL Server-Agent-Aufträge. Sie können jedoch den SQL Server-Agent in Ihrer lokalen SQL Server-Instanz ausführen und mit Microsoft Azure SQL-Datenbank verbinden.

## Unterstützung der SQL Server-Sortierung

Die standardmäßige Datenbanksortierung von Microsoft Azure SQL-Datenbank ist **SQL\_LATIN1\_GENERAL\_CP1\_CI\_AS**, wobei **LATIN1\_GENERAL** für Englisch (USA), **CP1** für Codepage 1252, **CI** für keine Unterscheidung von Groß-/Kleinschreibung und **AS** für die Unterscheidung nach Akzent steht. Es ist möglich, die Sortierung für V12-Datenbanken mithilfe von Transact-SQL zu ändern. Weitere Informationen zum Festlegen der Sortierung finden Sie unter [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## Benennungsanforderungen

Aus Sicherheitsgründen sind bestimmte Benutzernamen nicht zulässig. Dabei handelt es sich um folgende Namen:

 - **admin**
 - **administrator**
 - **guest**
 - **root**
 - **sa**

Namen für alle neuen Objekte müssen den SQL Server-Regeln für Bezeichner entsprechen. Weitere Informationen finden Sie unter [Bezeichner](https://msdn.microsoft.com/library/ms175874.aspx).

Darüber hinaus dürfen Anmelde- und Benutzernamen nicht das Zeichen „\\“ enthalten (Windows-Authentifizierung wird nicht unterstützt).

## Weitere Leitlinien

- Zusätzlich zu den allgemeinen Einschränkungen, die in diesem Artikel beschrieben werden, gelten für die SQL-Datenbank basierend auf Ihrer **Dienstebene** bestimmte Ressourcenkontingente und Einschränkungen. Eine Übersicht über die Dienstebenen finden Sie unter [SQL-Datenbanken-Dienstebenen](sql-database-service-tiers.md).

- Andere für SQL-Datenbank geltende Einschränkungen finden Sie unter [Ressourceneinschränkungen für Azure SQL-Datenbank](sql-database-resource-limits.md).

- Sicherheitsbezogene Leitlinien finden Sie unter [Sicherheitsrichtlinien und Einschränkungen von Azure SQL-Datenbank](sql-database-security-guidelines.md).

- Ein weiterer Aspekt ist die Kompatibilität von Azure SQL-Datenbank mit lokalen Versionen von SQL Server, wie z.B. SQL Server 2014 und SQL Server 2016. Die neueste Version von Azure SQL-Datenbank (V12 ) weist bei diesem Aspekt viele Verbesserungen auf. Weitere Informationen finden Sie unter [Neuerungen in SQL-Datenbank V12](sql-database-v12-whats-new.md).

- Informationen zur Verfügbarkeit von Treibern und Unterstützung für SQL-Datenbank finden Sie unter [Verbindungsbibliotheken für SQL-Datenbank und SQL Server](sql-database-libraries.md).

<!---HONumber=AcomDC_0413_2016-->