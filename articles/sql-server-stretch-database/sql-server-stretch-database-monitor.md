<properties
	pageTitle="Überwachen und Behandeln von Problemen der Datenmigration (Stretch Database) | Microsoft Azure"
	description="Erfahren Sie Einzelheiten über das Überwachen des Status der Datenmigration."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# Überwachen und Behandeln von Problemen der Datenmigration (Stretch-Datenbank)

Wählen Sie **Aufgaben | Stretch | Überwachung** für eine Datenbank in SQL Server Management Studio aus, um die Datenmigration im Stretch Database Monitor zu überwachen.

## Prüfen des Status der Datenmigration in Stretch Database Monitor
Wählen Sie **Aufgaben | Stretch | Überwachung** für eine Datenbank in SQL Server Management Studio aus, um Stretch Database Monitor zu öffnen und die Datenmigration zu überwachen.

-   Der obere Teil der Monitors zeigt allgemeine Informationen über die Stretch-fähigen SQL Server-Datenbank und die Azure-Remotedatenbank an.

-   Im unteren Teil der Monitors wird der Status der Datenmigration für jede Stretch-fähige Tabelle in der Datenbank angezeigt.

![Stretch Database Monitor][StretchMonitorImage1]

## <a name="Migration"></a>Prüfen des Status der Datenmigration in einer dynamischen Verwaltungsansicht
Öffnen Sie die dynamische Verwaltungssicht **sys.dm\_db\_rda\_migration\_status**, um zu ermitteln, wie viele Batches und Zeilen mit Daten migriert wurden. Weitere Informationen finden Sie unter [sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

## <a name="Firewall"></a>Beheben von Problemen bei der Datenmigration

**Zeilen aus meiner Stretch-aktivierten Tabelle werden nicht zu Azure migriert. Was ist das Problem?**

Es gibt mehrere Probleme, die eine Migration beeinflussen können. Überprüfen Sie die folgenden Punkte.

-   Überprüfen Sie die Netzwerkkonnektivität für den SQL Server-Computer.

-   Stellen Sie sicher, dass die Azure-Firewall Ihren SQL Server nicht daran hindert, eine Verbindung mit dem Remoteendpunkt herzustellen.

-   Suchen Sie in der dynamischen Verwaltungsansicht **sys.dm\_db\_rda\_migration\_status** nach dem Status des aktuellen Batches. Wenn ein Fehler aufgetreten ist, überprüfen Sie die Werte error\_number, error\_state und error\_severity für den Batch.

    -   Weitere Informationen über die Ansicht finden Sie unter [sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

    -   Weitere Informationen zum Inhalt einer SQL Server-Fehlermeldung finden Sie unter [sys.messages (Transact-SQL)](https://msdn.microsoft.com/library/ms187382.aspx).

**Die Azure-Firewall blockiert Verbindungen von meinem lokalen Server.**

Möglicherweise müssen Sie in den Einstellungen der Azure-Firewall des Azure-Servers eine Regel hinzufügen, um SQL Server zu erlauben, mit dem Azure-Remoteserver zu kommunizieren.

## Weitere Informationen

[Verwalten von Stretch Database und Behandeln von Problemen ](sql-server-stretch-database-manage.md)

<!--Image references-->
[StretchMonitorImage1]: ./media/sql-server-stretch-database-monitor/StretchDBMonitor.png

<!---HONumber=AcomDC_0615_2016-->