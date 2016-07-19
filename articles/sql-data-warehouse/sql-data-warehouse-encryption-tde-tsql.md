<properties
   pageTitle="Erste Schritte mit SQL Data Warehouse Transparent Data Encryption (TDE) TSQL | Microsoft Azure"
   description="Erste Schritte mit SQL Data Warehouse Transparent Data Encryption (TDE) TSQL"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/07/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# Erste Schritte mit Transparent Data Encryption (TDE)


> [AZURE.SELECTOR]
- [Sicherheitsübersicht](sql-data-warehouse-overview-manage-security.md)
- [Bedrohungserkennung](sql-data-warehouse-security-threat-detection.md)
- [Verschlüsselung (Portal)](sql-data-warehouse-encryption-tde.md)
- [Verschlüsselung (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
- [Übersicht über die Überwachung](sql-data-warehouse-auditing-overview.md)
- [Überwachung für Vorgängerversionsclients](sql-data-warehouse-auditing-downlevel-clients.md)


Azure SQL Data Warehouse Transparent Data Encryption (TDE) ist ein zusätzlicher Schutz vor der Bedrohung durch schädliche Aktivitäten. Hierzu werden die Schritte für die Echtzeitverschlüsselung und -entschlüsselung der Datenbank, die dazugehörigen Backups und die Transaktionsprotokolldateien im Ruhezustand ausgeführt, ohne dass Änderungen an der Anwendung erforderlich sind.

TDE verschlüsselt die Speicherung einer gesamten Datenbank, indem ein symmetrischer Schlüssel verwendet wird, der als Datenbankverschlüsselungsschlüssel bezeichnet wird. In SQL-Datenbank wird der Datenbankverschlüsselungsschlüssel mit einem integrierten Serverzertifikat geschützt. Das integrierte Serverzertifikat ist für jeden SQL-Datenbank-Server eindeutig. Microsoft führt für diese Zertifikate nach spätestens 90 Tagen automatisch eine Rotation durch. Der von SQL Data Warehouse verwendete Verschlüsselungsalgorithmus ist AES-256. Eine allgemeine Beschreibung von TDE finden Sie unter [Transparente Datenverschlüsselung (TDE)].

##Aktivieren der Verschlüsselung

Führen Sie diese Schritte aus, um TDE für ein SQL Data Warehouse zu aktivieren:

1. Stellen Sie eine Verbindung mit der Datenbank *master* auf dem Server her, auf dem die Datenbank gehostet wird. Verwenden Sie eine Anmeldung, bei der es sich um einen Administrator oder ein Mitglied der Rolle **dbmanager** in der Datenbank „master“ handelt.
2. Führen Sie die folgende Anweisung aus, um die Datenbank zu verschlüsseln.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

##Deaktivieren der Verschlüsselung

Führen Sie diese Schritte aus, um TDE für ein SQL Data Warehouse zu deaktivieren:

1. Stellen Sie eine Verbindung mit der Datenbank *master* her. Verwenden Sie eine Anmeldung, bei der es sich um einen Administrator oder ein Mitglied der Rolle **dbmanager** in der Datenbank „master“ handelt.
2. Führen Sie die folgende Anweisung aus, um die Datenbank zu verschlüsseln.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

##Überprüfen der Verschlüsselung

Führen Sie die folgenden Schritte aus, um den Verschlüsselungsstatus für ein SQL Data Warehouse zu überprüfen:

1. Stellen Sie eine Verbindung mit der Datenbank *master* oder einer Instanzdatenbank her. Verwenden Sie eine Anmeldung, bei der es sich um einen Administrator oder ein Mitglied der Rolle **dbmanager** in der Datenbank „master“ handelt.
2. Führen Sie die folgende Anweisung aus, um die Datenbank zu verschlüsseln.

```sql
SELECT
	[name],
	[is_encrypted]
FROM
	sys.databases;
```

Das Ergebnis ```1``` steht für eine verschlüsselte Datenbank, und mit ```0``` wird eine nicht verschlüsselte Datenbank angegeben.

##Verschlüsselung-DMVs  

- [sys.databases][]
- [sys.dm\_pdw\_nodes\_database\_encryption\_keys][]


<!--Anchors-->
[Transparente Datenverschlüsselung (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm\_pdw\_nodes\_database\_encryption\_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_0706_2016-->