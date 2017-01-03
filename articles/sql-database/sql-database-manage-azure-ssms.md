---
title: Verwalten einer SQL-Datenbank mit SQL Server Management Studio | Microsoft Docs
description: "Erfahren Sie, wie Sie SQL Server Management Studio für die Verwaltung von SQL-Datenbankservern und -Datenbanken verwenden."
services: sql-database
documentationcenter: .net
author: stevestein
manager: jhubbard
editor: tysonn
ms.assetid: da6f3608-5993-4134-a497-ff2811e9f31f
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/29/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: d9ff74a49742fa77f5989b8b05e0567e3ca81dc5
ms.openlocfilehash: 89cb8827745b31b3a77b64d5cafd586957d60d30


---
# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio
> [!div class="op_single_selector"]
> * [Azure-Portal](sql-database-manage-portal.md)
> * [SSMS](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

Sie können SQL Server Management Studio (SSMS) verwenden, um Server und Datenbanken der Azure SQL-Datenbank zu verwalten. Dieses Thema enthält Anleitungen für häufige Aufgaben mit SSMS. Sie sollten bereits einen Server und eine Datenbank in der Azure SQL-Datenbank erstellt haben, bevor Sie beginnen. Weitere Informationen finden Sie unter [SQL-Datenbank-Tutorial: Erstellen einer SQL-Datenbank in Minuten mit dem Azure-Portal](sql-database-get-started.md) und [Herstellen einer Verbindung mit einer SQL-Datenbank mit SQL Server Management Studio und Ausführen einer T-SQL-Beispielabfrage](sql-database-connect-query-ssms.md).

> [!TIP]
> Das [Tutorial zu den ersten Schritten](sql-database-get-started.md) veranschaulicht, wie Sie einen Server und eine serverbasierte Firewall erstellen, Servereigenschaften anzeigen, eine Verbindung mithilfe von SQL Server Management Studio herstellen, die Masterdatenbank abfragen, eine Beispieldatenbank und eine leere Datenbank erstellen, Datenbankeigenschaften abfragen, und die Beispieldatenbank abfragen.
>

Es wird empfohlen, dass Sie die neueste Version von SSMS verwenden, wenn Sie mit der Azure SQL-Datenbank arbeiten. 

> [!IMPORTANT]
> Verwenden Sie immer die neueste Version von SSMS auf, da SSMS ständig verbessert wird, um mit den neuesten Updates von Azure und SQL-Datenbank einsetzbar zu sein. Unter [Herunterladen von SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)erfahren Sie, wie Sie die aktuelle Version erhalten.
> 
> 

## <a name="create-and-manage-azure-sql-databases"></a>Erstellen und Verwalten von Azure SQL-Datenbanken
Während Sie mit der **Master** -Datenbank verbunden sind, können Sie Datenbanken auf dem Server erstellen und bestehende Datenbanken modifizieren oder löschen. Die folgenden Schritte beschreiben, wie Sie eine Reihe häufiger Aufgaben der Datenbankverwaltung über Management Studio erledigen können. Vergewissern Sie sich zur Durchführung dieser Aufgaben, dass Sie mit der **Master** -Datenbank verbunden sind, und zwar mit dem Hauptbenutzernamen auf Serverebene, den Sie bei der Einrichtung des Servers erstellt haben.

Öffnen Sie den Ordner „Datenbanken“, erweitern Sie den Ordner **System Databases** (Systemdatenbanken), klicken Sie mit der rechten Maustaste auf **master**, und klicken Sie anschließend auf **Neue Abfrage**, um ein Abfragefenster in Management Studio zu öffnen.

* Verwenden Sie die Anweisung **CREATE DATABASE** , um eine Datenbank zu erstellen. Weitere Informationen finden Sie unter [CREATE DATABASE (SQL-Datenbank)](https://msdn.microsoft.com/library/dn268335.aspx). Die folgende Anweisung erstellt eine Datenbank mit dem Namen **myTestDB** und gibt an, dass es sich um eine Datenbank der Standard S0-Edition mit einer maximalen Standardgröße von 250 GB handelt.
  
      CREATE DATABASE myTestDB
      (EDITION='Standard',
       SERVICE_OBJECTIVE='S0');

Klicken Sie auf **Ausführen** , um die Abfrage durchzuführen.

* Mit der Anweisung **ALTER DATABASE** können Sie eine vorhandene Datenbank anpassen und beispielsweise den Namen oder die Edition der Datenbank ändern. Weitere Informationen finden Sie unter [ALTER DATABASE (SQL-Datenbank)](https://msdn.microsoft.com/library/ms174269.aspx). Die folgende Anweisung ändert die im vorherigen Schritt erstellte Datenbank und legt die Edition auf „Standard S1“ fest.
  
      ALTER DATABASE myTestDB
      MODIFY
      (SERVICE_OBJECTIVE='S1');
* Mit der Anweisung **DROP DATABASE** können Sie eine bestehende Datenbank löschen. Weitere Informationen finden Sie unter [DROP DATABASE (SQL-Datenbank)](https://msdn.microsoft.com/library/ms178613.aspx). Mit der folgenden Anweisung löschen Sie die Datenbank **myTestDB** ; führen Sie dies jedoch jetzt noch nicht durch, da Sie für sie im nächsten Schritt Anmeldungen erstellen werden.
  
      DROP DATABASE myTestBase;
* Die Master-Datenbank verfügt über die Ansicht **sys.databases** , in der Sie Details zu allen Datenbanken einsehen können. Führen Sie zur Anzeige aller vorhandenen Datenbanken die folgende Anweisung aus:
  
      SELECT * FROM sys.databases;
* In SQL-Datenbank wird die Anweisung **USE** nicht für einen Wechsel zwischen einzelnen Datenbanken unterstützt. Statt dessen müssen Sie direkt eine Verbindung zur Zieldatenbank herstellen.

> [!NOTE]
> Viele der Transact-SQL-Anweisungen, die eine Datenbank erstellen oder modifizieren, müssen in ihrem eigenen Batch ausgeführt werden und können nicht mit anderen Transact-SQL-Anweisungen gruppiert werden. Weitere Informationen finden Sie in den anweisungsspezifischen Artikeln, die über die oben aufgeführten Links verfügbar sind.
> 
> 

## <a name="create-and-manage-logins"></a>Erstellen und Verwalten von Anmeldungen
Die Datenbank vom Typ **master** enthält Anmeldungen und Informationen darüber, welche Anmeldungen zum Erstellen von Datenbanken oder anderen Anmeldungen berechtigt sind. Stellen Sie zur Verwaltung von Anmeldungen eine Verbindung zur **Master** -Datenbank her, und zwar mit der Hauptanmeldung auf Serverebene, die Sie beim Einrichten des Servers erstellt haben. Mit den Anweisungen **CREATE LOGIN**, **ALTER LOGIN** oder **DROP LOGIN** können Sie Abfragen in der Masterdatenbank ausführen, die die Anmeldungen für den gesamten Server verwalten. Weitere Informationen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/ee336235.aspx). 

* Verwenden Sie die Anweisung **CREATE LOGIN** , um eine Anmeldung auf Serverebene zu erstellen. Weitere Informationen finden Sie unter [CREATE LOGIN (SQL-Datenbank)](https://msdn.microsoft.com/library/ms189751.aspx). Die folgende Anweisung erstellt eine Anmeldung mit dem Namen **login1**. Ersetzen Sie **password1** durch ein Kennwort Ihrer Wahl.
  
      CREATE LOGIN login1 WITH password='password1';
* Mit der Anweisung **CREATE USER** erteilen Sie Berechtigungen auf Datenbank-Ebene. Alle Anmeldungen müssen in der Datenbank **master** erstellt werden. Damit eine Anmeldung auf eine andere Datenbank zugreifen kann, müssen Sie ihr Berechtigungen auf Datenbank-Ebene zuweisen, indem Sie die Anweisung **CREATE USER** für diese Datenbank ausführen. Weitere Informationen finden Sie unter [CREATE USER (SQL-Datenbank)](https://msdn.microsoft.com/library/ms173463.aspx). 
* Gehen Sie folgendermaßen vor, um "login1" die Zugriffsberechtigung auf eine Datenbank mit dem Namen **myTestDB**zu erteilen:
  
  1. Um den Objekt-Explorer zur Anzeige der erstellten Datenbank **myTestDB** zu aktualisieren, klicken Sie im Objekt-Explorer mit der rechten Maustaste auf den Servernamen und dann mit der linken auf **Aktualisieren**.  
     
     Wenn Sie die Verbindung geschlossen haben, können Sie sie im Menü **Datei** mit dem Befehl Objekt-Explorer verbinden wieder öffnen.
  2. Klicken Sie mit der rechten Maustaste auf die Datenbank **myTestDB** und wählen Sie **Neue Abfrage**.
  3. Führen Sie die folgende Anweisung für die Datenbank „myTestDB“ durch, um einen Datenbankbenutzer namens **login1User** zu erstellen, der der Anmeldung auf Serverebene **login1** zugeordnet ist.
     
         CREATE USER login1User FROM LOGIN login1;
* Nutzen Sie die gespeicherte Prozedur **sp\_addrolemember**, um das Benutzerkonto mit geeigneten Berechtigungen für die Datenbank auszustatten. Weitere Informationen finden Sie unter [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). Die folgende Anweisung erteilt **login1User** Lesezugriff auf die Datenbank, indem sie **login1User** der Rolle **db\_datareader** hinzufügt.
  
      exec sp_addrolemember 'db_datareader', 'login1User';    
* Verwenden Sie die Anweisung **ALTER LOGIN** , um eine bestehende Anmeldung zu modifizieren, etwa wenn sie das zugehörige Kennwort ändern möchten. Weitere Informationen finden Sie unter [ALTER LOGIN (SQL-Datenbank)](https://msdn.microsoft.com/library/ms189828.aspx). Die Anweisung **ALTER LOGIN** sollte für die **Masterdatenbank** ausgeführt werden. Wechseln Sie wieder zurück zu dem Abfragefenster, das mit dieser Datenbank verbunden ist. Die folgende Anweisung modifiziert die Anmeldung **login1** , um das Kennwort zurückzusetzen. Ersetzen Sie **newPassword** durch ein Kennwort Ihrer Wahl und **oldPassword** durch das aktuelle Kennwort der Anmeldung.
  
      ALTER LOGIN login1
      WITH PASSWORD = 'newPassword'
      OLD_PASSWORD = 'oldPassword';
* Mit der Anweisung **DROP LOGIN** können Sie eine bestehende Anmeldung löschen. Wenn Sie eine Anmeldung auf Serverebene löschen, werden auch alle zugehörigen Konten von Datenbankbenutzern gelöscht. Weitere Informationen finden Sie unter [DROP DATABASE (SQL-Datenbank)](https://msdn.microsoft.com/library/ms178613.aspx). Die Anweisung **DROP LOGIN** sollte für die **Masterdatenbank** ausgeführt werden. Die folgende Anweisung löscht die Anmeldung **login1** .
  
      DROP LOGIN login1;
* Die Masterdatenbank verfügt über eine Ansicht **sys.sql\_logins**, in der Sie die Anmeldungen einsehen können. Führen Sie zur Anzeige aller vorhandenen Anmeldungen die folgende Anweisung aus:
  
      SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-views"></a>Überwachen von SQL-Datenbank mit Dynamic Management Views
SQL-Datenbank unterstützt mehrere Dynamic Management Views, mit denen Sie eine Einzeldatenbank überwachen können. Im Folgenden finden Sie einige wenige Beispiele für die Art Überwachungsdaten, die Sie mit diesen Ansichten abrufen können. Umfassende Details und weitere Anwendungsbeispiele finden Sie unter [Überwachen der Azure SQL-Datenbank mit dynamischen Verwaltungssichten](https://msdn.microsoft.com/library/azure/ff394114.aspx).

* Die Abfrage einer dynamischen Verwaltungsansicht erfordert die Berechtigung **VIEW DATABASE STATE** . Wenn Sie einem bestimmten Benutzer die Berechtigung **VIEW DATABASE STATE** zuweisen möchten, stellen Sie eine Verbindung mit der Datenbank her, die Sie verwalten möchten, und führen Sie darauf bezogen folgende Anweisung aus:
  
      GRANT VIEW DATABASE STATE TO login1User;
* Berechnen Sie die Datenbankgröße mithilfe der Ansicht **sys.dm\_db\_partition\_stats**. Die Ansicht **sys.dm\_db\_partition\_stats** gibt die Anzahl von Seiten und Zeilen für jede Partition der Datenbank zurück, aus denen Sie dann die Datenbankgröße errechnen können. Die folgende Abfrage gibt die Größe Ihrer Datenbank in Megabyte zurück:
  
      SELECT SUM(reserved_page_count)*8.0/1024
      FROM sys.dm_db_partition_stats;   
* In den Ansichten **sys.dm\_exec\_connections** und **sys.dm\_exec\_sessions** können Sie Informationen über aktuelle Benutzerverbindungen und der Datenbank zugeordnete interne Aufgaben abrufen. Die folgende Abfrage gibt Informationen über die aktuelle Verbindung zurück:
  
      SELECT
          e.connection_id,
          s.session_id,
          s.login_name,
          s.last_request_end_time,
          s.cpu_time
      FROM
          sys.dm_exec_sessions s
          INNER JOIN sys.dm_exec_connections e
            ON s.session_id = e.session_id;
* In der Ansicht **sys.dm\_exec\_query\_stats** können Sie aggregierte Leistungsstatistiken für zwischengespeicherte Abfragepläne abrufen. Die folgende Abfrage gibt Informationen über die "Top-Fünf"-Abfragen gemessen an durchschnittlicher CPU-Zeit zurück.
  
      SELECT TOP 5 query_stats.query_hash AS "Query Hash",
          SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
          MIN(query_stats.statement_text) AS "Statement Text"
      FROM
          (SELECT QS.*,
          SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
          ((CASE statement_end_offset
              WHEN -1 THEN DATALENGTH(ST.text)
              ELSE QS.statement_end_offset END
                  - QS.statement_start_offset)/2) + 1) AS statement_text
           FROM sys.dm_exec_query_stats AS QS
           CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
      GROUP BY query_stats.query_hash
      ORDER BY 2 DESC;




<!--HONumber=Jan17_HO1-->


