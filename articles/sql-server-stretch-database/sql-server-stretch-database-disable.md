<properties
	pageTitle="Deaktivieren von Stretch-Datenbank und Zurückbringen von Remotedaten | Microsoft Azure"
	description="Erfahren Sie, wie Sie Stretch-Datenbank für eine Tabelle deaktivieren und optional Remotedaten zurückbringen können."
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
	ms.date="08/05/2016"
	ms.author="douglasl"/>

# Deaktivieren von Stretch-Datenbank und Zurückbringen von Remotedaten

Wählen Sie zum Deaktivieren von Stretch-Datenbank für eine Tabelle in SQL Server Management Studio **Stretch** für eine Tabelle aus. Wählen Sie dann eine der folgenden Optionen.

-   **Deaktivieren| Bring data back from Azure**. Kopieren Sie die Remotedaten für die Tabelle aus Azure, und fügen Sie sie wieder in SQL Server ein. Deaktivieren Sie dann Stretch-Datenbank für die Tabelle. Bei diesem Vorgang, der nicht abgebrochen werden kann, fallen Datenübertragungskosten an.

-   **Deaktivieren| Leave data in Azure**. Deaktivieren Sie Stretch-Datenbank für die Tabelle.  Verwerfen Sie die Remotedaten für die Tabelle in Azure.

Sie können auch Transact-SQL verwenden, Stretch-Datenbank für eine Tabelle oder eine Datenbank zu deaktivieren.

Nach dem Deaktivieren von Stretch-Datenbank für eine Tabelle wird die Datenmigration beendet und die Abfrageergebnisse enthalten nicht mehr die Ergebnisse aus der Remotetabelle.

Wenn Sie die Datenmigration anhalten möchten, finden Sie weitere Informationen unter [Anhalten und Fortsetzen von Stretch-Datenbank](sql-server-stretch-database-pause.md).

>   [AZURE.NOTE] Durch das Deaktivieren von Stretch-Datenbank für eine Tabelle oder eine Datenbank wird das Remoteobjekt nicht gelöscht. Wenn Sie die Remotetabelle oder die Remotedatenbank löschen möchten, müssen Sie sie mithilfe des Azure-Verwaltungsportals entfernen. Für die Remoteobjekte fallen weiterhin Azure-Kosten an, bis Sie die Objekte löschen. Weitere Informationen finden Sie unter [SQL Server Stretch-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## Deaktivieren von Stretch-Datenbank für eine Tabelle

### Verwenden von SQL Server Management Studio zum Deaktivieren von Stretch-Datenbank für eine Tabelle

1.  Wählen Sie in SQL Server Management Studio im Objekt-Explorer die Tabelle aus, für die Stretch-Datenbank deaktiviert werden soll.

2.  Klicken Sie mit der rechten Maustaste, und wählen Sie **Stretch** aus. Wählen Sie dann eine der folgenden Optionen:

    -   **Deaktivieren| Bring data back from Azure**. Kopieren Sie die Remotedaten für die Tabelle aus Azure, und fügen Sie sie wieder in SQL Server ein. Deaktivieren Sie dann Stretch-Datenbank für die Tabelle. Dieser Befehl kann nicht abgebrochen werden.

        >   [AZURE.NOTE] Für das Kopieren der Remotedaten für die Tabelle aus Azure zurück in SQL Server fallen Datenübertragungskosten an. Weitere Informationen finden Sie unter [Datenübertragungen – Preisdetails](https://azure.microsoft.com/pricing/details/data-transfers/).

        Wenn alle Remotedaten aus Azure in SQL Server kopiert wurden, wird Stretch für die Tabelle deaktiviert.

    -   **Deaktivieren| Leave data in Azure**. Deaktivieren Sie Stretch-Datenbank für die Tabelle.  Verwerfen Sie die Remotedaten für die Tabelle in Azure.

    >   [AZURE.NOTE] Durch das Deaktivieren von Stretch-Datenbank für eine Tabelle werden die Remotedaten oder die Remotetabelle nicht gelöscht. Wenn Sie die Remotetabelle löschen möchten, müssen Sie sie mithilfe des Azure-Verwaltungsportals entfernen. Für die Remotetabelle fallen weiterhin Azure-Kosten an, bis Sie sie löschen. Weitere Informationen finden Sie unter [SQL Server Stretch-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### Verwenden von Transact-SQL zum Deaktivieren von Stretch-Datenbank für eine Tabelle

-   Wenn Sie Stretch für eine Tabelle deaktivieren möchten, und die Remotedaten für die Tabelle aus Azure zurück in SQL Server kopieren möchten, führen Sie folgenden Befehl aus. Wenn alle Remotedaten aus Azure in SQL Server kopiert wurden, wird Stretch für die Tabelle deaktiviert.

    Dieser Befehl kann nicht abgebrochen werden.

    ```tsql
	USE <Stretch-enabled database name>;
    GO
    ALTER TABLE <Stretch-enabled table name>  
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
    GO
    ```
    >   [AZURE.NOTE] Für das Kopieren der Remotedaten für die Tabelle aus Azure zurück in SQL Server fallen Datenübertragungskosten an. Weitere Informationen finden Sie unter [Datenübertragungen – Preisdetails](https://azure.microsoft.com/pricing/details/data-transfers/).

-   Führen Sie den folgenden Befehl aus, um Stretch für eine Tabelle zu deaktivieren und die Remotedaten zu verwerfen.

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```

>   [AZURE.NOTE] Durch das Deaktivieren von Stretch-Datenbank für eine Tabelle werden die Remotedaten oder die Remotetabelle nicht gelöscht. Wenn Sie die Remotetabelle löschen möchten, müssen Sie sie mithilfe des Azure-Verwaltungsportals entfernen. Für die Remotetabelle fallen weiterhin Azure-Kosten an, bis Sie sie löschen. Weitere Informationen finden Sie unter [SQL Server Stretch-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## Deaktivieren von Stretch-Datenbank für eine Datenbank
Vor dem Deaktivieren von Stretch-Datenbank für eine Datenbank müssen Sie Stretch-Datenbank für die einzelnen Stretch-fähigen Tabellen in der Datenbank deaktivieren.

### Verwenden von SQL Server Management Studio zum Deaktivieren von Stretch-Datenbank für eine Datenbank

1.  Wählen Sie in SQL Server Management Studio im Objekt-Explorer die Datenbank aus, für die Stretch-Datenbank deaktiviert werden soll.

2.  Klicken Sie mit der rechten Maustaste, und wählen Sie **Aufgaben** und dann **Stretch**aus. Wählen Sie anschließend **Deaktivieren**.

>   [AZURE.NOTE] Durch das Deaktivieren von Stretch-Datenbank für eine Datenbank wird die Remotedatenbank nicht gelöscht. Wenn Sie die Remotedatenbank löschen möchten, müssen Sie sie mithilfe des Azure-Verwaltungsportals entfernen. Für die Remotedatenbank fallen weiterhin Azure-Kosten an, bis Sie sie löschen. Weitere Informationen finden Sie unter [SQL Server Stretch-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### Verwenden von Transact-SQL zum Deaktivieren von Stretch-Datenbank für eine Datenbank
Führen Sie den folgenden Befehl aus:

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

>   [AZURE.NOTE] Durch das Deaktivieren von Stretch-Datenbank für eine Datenbank wird die Remotedatenbank nicht gelöscht. Wenn Sie die Remotedatenbank löschen möchten, müssen Sie sie mithilfe des Azure-Verwaltungsportals entfernen. Für die Remotedatenbank fallen weiterhin Azure-Kosten an, bis Sie sie löschen. Weitere Informationen finden Sie unter [SQL Server Stretch-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## Siehe auch

[ALTER DATABASE SET-Optionen (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Anhalten und Fortsetzen von Stretch-Datenbank](sql-server-stretch-database-pause.md)

<!---HONumber=AcomDC_0810_2016-->
