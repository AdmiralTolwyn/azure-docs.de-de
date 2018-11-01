---
title: Verwalten von Gruppen mit Azure SQL-Datenbanken | Microsoft-Dokumentation
description: Führen Sie die Schritte zur Erstellung und Verwaltung eines elastischen Auftrags aus.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 07/16/2018
ms.openlocfilehash: e036bb8b32ab81c63767d4a26fea103cf56b6a66
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242095"
---
# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Erstellen und Verwalten von horizontal hochskalierten Azure SQL-Datenbanken mithilfe von elastischen Aufträgen (Preview)


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


**Aufträge für die elastische Datenbank** vereinfachen die Verwaltung von Datenbankengruppen durch die Ausführung von administrativen Vorgängen wie Schemaänderungen, Verwaltung von Anmeldeinformationen, Aktualisierungen von Verweisdaten, Sammlung von Leistungsdaten und Sammlung von Telemetriedaten zu Mandanten (Kunden). Elastische Datenbankaufträge sind derzeit über das Azure-Portal und PowerShell-Cmdlets verfügbar. Das Azure-Portal bietet allerdings nur eingeschränkte Funktionalität, die auf die Ausführung in allen Datenbanken in einem [Pool für elastische Datenbanken](sql-database-elastic-pool.md) begrenzt ist. Informationen zum Zugriff auf zusätzliche Features und zum Anwenden von Skripts auf eine Gruppe von Datenbanken, einschließlich einer benutzerdefinierten Sammlung oder eines Shardsatzes (erstellt mithilfe der [Clientbibliothek für elastische Datenbanken](sql-database-elastic-scale-introduction.md)) finden Sie unter [Erstellen und Verwalten von Aufträgen mithilfe von PowerShell](sql-database-elastic-jobs-powershell.md). Weitere Informationen zu Aufträgen finden Sie unter [Übersicht über elastische Datenbankaufträge](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Voraussetzungen
* Ein Azure-Abonnement. Eine kostenlose Testversion finden Sie unter [Kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/).
* Ein Pool für elastische Datenbanken. Informationen finden Sie unter [Was ist ein Pool für elastische Datenbanken?](sql-database-elastic-pool.md).
* Installation von Dienstkomponenten für elastische Datenbankaufträge. Siehe [Installieren des Diensts für elastische Datenbankaufträge](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Erstellen von Aufträgen
1. Klicken Sie im [Azure-Portal](https://portal.azure.com)in einem vorhandenen elastischen Pool von Datenbankaufträgen auf **Auftrag erstellen**.
2. Geben Sie den Benutzernamen und das Kennwort des Datenbankadministrators (der bei der Installation der Aufträge erstellt wurde) für die Auftragsverwaltungs-Datenbank (Metadatenspeicher für Aufträge) ein.
   
    ![Benennen Sie den Auftrag, geben Sie den Code ein, oder fügen Sie ihn ein, und klicken Sie auf "Ausführen".][1]
3. Geben Sie auf dem Blatt **Auftrag erstellen** einen Namen für den Auftrag ein.
4. Geben Sie zum Herstellen der Verbindung mit den Zieldatenbanken einen Benutzernamen mit dem zugehörigen Kennwort ein, der über ausreichende Berechtigungen für die Skriptausführung verfügt.
5. Geben Sie das T-SQL-Skript ein, oder fügen Sie es ein.
6. Klicken Sie auf **Speichern** und dann auf **Ausführen**.
   
    ![Erstellen und Ausführen von Aufträgen][5]

## <a name="run-idempotent-jobs"></a>Ausführen idempotenter Aufträge
Beim Ausführen eines Skripts für einen Satz von Datenbanken müssen Sie darauf achten, dass das Skript idempotent ist. Das heißt, das Skript muss mehrmals ausgeführt werden können, auch wenn es zuvor in einem unvollständigen Zustand Fehler verursacht hat. Wenn beispielsweise ein Skript fehlschlägt, wird der Auftrag automatisch wiederholt, bis er erfolgreich abgeschlossen wurde (mit Einschränkungen, da die Wiederholungslogik irgendwann keine Wiederholung mehr ausführt). Die Vorgehensweise dabei ist, eine "IF EXISTS"-Klausel zu verwenden und jede gefundene Instanz zu löschen, bevor Sie ein neues Objekt erstellen. Im Folgenden ist ein Beispiel dargestellt:

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Verwenden Sie alternativ eine "IF NOT EXISTS"-Klausel vor dem Erstellen einer neuen Instanz:

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

Dieses Skript aktualisiert dann die zuvor erstellte Tabelle.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>Überprüfen des Auftragsstatus
Nachdem ein Auftrag gestartet wurde, können Sie seinen Fortschritt überprüfen.

1. Klicken Sie auf der Seite für Pools für elastische Datenbanken auf **Aufträge verwalten**.
   
    ![Klicken Sie auf "Aufträge verwalten".][2]
2. Klicken Sie auf den Namen (a) eines Auftrags. Der **STATUS** kann "Abgeschlossen" oder "Fehlgeschlagen" lauten. Die Auftragsdetails (b) werden mit Datum und Uhrzeit der Erstellung und Ausführung angezeigt. Die Liste (c) zeigt den Fortschritt des Skripts für jede Datenbank im Pool unter Angabe des Datums und der Uhrzeit an.
   
    ![Überprüfen beendeter Aufträge][3]

## <a name="checking-failed-jobs"></a>Überprüfen der fehlgeschlagenen Aufträge
Wenn ein Auftrag fehlschlägt, wird ein Protokoll zu seiner Ausführung erstellt. Klicken Sie auf den Namen des fehlgeschlagenen Auftrags, um dessen Details anzuzeigen.

![Überprüfen fehlgeschlagener Aufträge][4]

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png


