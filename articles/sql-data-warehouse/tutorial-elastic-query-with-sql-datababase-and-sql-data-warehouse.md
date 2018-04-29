---
title: 'Tutorial: Elastische Abfragen mit Azure SQL Data Warehouse | Microsoft-Dokumentation'
description: In diesem Tutorial wird Azure SQL Data Warehouse mithilfe des Features für elastische Abfragen über Azure SQL-Datenbank abgefragt.
services: sql-data-warehouse
author: hirokib
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/14/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: a31f035b5ec086a046028956c4a9c0de0d6a313d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-use-elastic-query-to-access-data-in-azure-sql-data-warehouse-from-azure-sql-database"></a>Tutorial: Zugreifen auf Daten in Azure SQL Data Warehouse über Azure SQL-Datenbank mithilfe elastischer Abfragen

In diesem Tutorial wird Azure SQL Data Warehouse mithilfe des Features für elastische Abfragen über Azure SQL-Datenbank abgefragt. 

## <a name="prerequisites-for-the-tutorial"></a>Voraussetzungen für das Tutorial

Bevor Sie mit dem Tutorial beginnen, müssen folgende Voraussetzungen erfüllt sein:

1. SQL Server Management Studio (SSMS) muss installiert sein.
2. Eine Azure SQL Server-Instanz mit einer Datenbank und einem Data Warehouse muss auf diesem Server erstellt sein.
3. Firewallregeln für den Zugriff auf Azure SQL Server müssen eingerichtet sein.

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>Einrichten einer Verbindung zwischen einer SQL Data Warehouse- und einer SQL-Datenbankinstanz 

1. Öffnen Sie mit SSMS oder einem anderen Abfrageclient eine neue Abfrage für **master** auf Ihrem logischen Server.

2. Erstellen Sie eine Anmeldung und einen Benutzer, der eine Verbindung zwischen der SQL-Datenbank und dem Data Warehouse darstellt.

   ```sql
   CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
   ```

3. Öffnen Sie mit SSMS oder einem anderen Abfrageclient eine neue Abfrage für die **SQL Data Warehouse-Instanz** auf Ihrem logischen Server.

4. Erstellen Sie mit der in Schritt 2 erstellten Anmeldung einen Benutzer in der Data Warehouse-Instanz.

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. Erteilen Sie dem Benutzer aus Schritt 4 Berechtigungen, die von der SQL-Datenbank ausgeführt werden sollen. In diesem Beispiel wird lediglich eine Berechtigung für eine SELECT-Anweisung für ein bestimmtes Schema gewährt, das zeigt, wie Abfragen von der SQL-Datenbank an eine bestimmte Domäne beschränkt werden können. 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. Öffnen Sie mit SSMS oder einem anderen Abfrageclient eine neue Abfrage für die **SQL-Datenbankinstanz** auf Ihrem logischen Server.

7. Erstellen Sie einen Hauptschlüssel, falls noch keiner vorhanden ist. 

   ```sql
   CREATE MASTER KEY; 
   ```

8. Erstellen Sie mit den Anmeldeinformationen, die Sie in Schritt 2 erstellt haben, datenbankweit gültige Anmeldeinformationen.

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL SalesDBElasticCredential
   WITH IDENTITY = 'SalesDBLogin',
   SECRET = 'aReallyStrongPassword@#!';
   ```

9. Erstellen Sie eine externe Datenquelle, die auf die Data Warehouse-Instanz verweist.

   ```sql
   CREATE EXTERNAL DATA SOURCE EnterpriseDwSrc WITH 
       (TYPE = RDBMS, 
       LOCATION = '<SERVER NAME>.database.windows.net', 
       DATABASE_NAME = '<SQL DATA WAREHOUSE NAME>', 
       CREDENTIAL = SalesDBElasticCredential, 
   ) ;
   ```

10. Nun können Sie externe Tabellen erstellen, die auf diese externe Datenquelle verweisen. Abfragen mit diesen Tabellen werden an die zu verarbeitende Data Warehouse-Instanz gesendet und an die Datenbankinstanz zurückgesendet.


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>Elastische Abfrage von der SQL-Datenbank an SQL Data Warehouse

In den nächsten Schritten erstellen wir eine Tabelle in unserer Data Warehouse-Instanz mit verschiedenen Werten. Anschließend zeigen wir, wie eine externe Tabelle zum Abfragen der Data Warehouse-Instanz über die Datenbankinstanz eingerichtet wird.

1. Öffnen Sie mit SSMS oder einem anderen Abfrageclient eine neue Abfrage für **SQL Data Warehouse** auf Ihrem logischen Server.

2. Senden Sie die folgende Abfrage, um eine **OrdersInformation**-Tabelle in Ihrer Data Warehouse-Instanz zu erstellen.

   ```sql
   CREATE TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL 
   ,   [CustomerID] [int] NOT NULL 
   ) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
   ```

3. Öffnen Sie mit SSMS oder einem anderen Abfrageclient eine neue Abfrage für die **SQL-Datenbank** auf Ihrem logischen Server.

4. Senden Sie die folgende Abfrage, um eine externe Tabellendefinition zu erstellen, die auf die **OrdersInformation** Tabelle in der Data Warehouse-Instanz verweist.

   ```sql
   CREATE EXTERNAL TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL
   ,   [CustomerID] [int] NOT NULL 
   ) 
   WITH 
   (
        DATA_SOURCE = EnterpriseDwSrc
   ,    SCHEMA_NAME = N'dbo'
   ,    OBJECT_NAME = N'OrderInformation'
   )
   ```

5. Beachten Sie, dass Ihre **SQL-Datenbankinstanz** nun eine externe Tabellendefinition enthält.

   ![Externe Tabellendefinition für elastische Abfragen](media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. Senden Sie die folgende Abfrage, die die Data Warehouse-Instanz abfragt. Sie sollten die fünf Werte erhalten, die Sie in Schritt 2 eingefügt haben. 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> Beachten Sie, dass die Rückgabe dieser Abfrage abgesehen von einigen Werten sehr viel Zeit in Anspruch nimmt. Bei der Verwendung elastischer Abfragen mit Data Warehouse sollten Sie die Gemeinkosten für die Abfrageverarbeitung und elektronische Verschiebung berücksichtigen. Wenn die Computeleistung und nicht die Latenz im Vordergrund steht, machen Sie von der Remoteausführung elastischer Abfragen Gebrauch.

Herzlichen Glückwunsch! Sie haben die Konfiguration der grundlegenden Einstellungen elastischer Abfragen abgeschlossen. 

## <a name="next-steps"></a>Nächste Schritte
Empfehlungen finden Sie unter [Vorgehensweise zur Verwendung elastischer Abfragen mit SQL Data Warehouse](how-to-use-elastic-query-with-sql-data-warehouse.md).