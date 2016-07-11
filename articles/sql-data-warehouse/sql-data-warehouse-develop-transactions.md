<properties
   pageTitle="Transaktionen in SQL Data Warehouse | Microsoft Azure"
   description="Tipps zum Implementieren von Transaktionen in Azure SQL Data Warehouse zum Entwickeln von Lösungen"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/11/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Transaktionen in SQL Data Warehouse

Wie zu erwarten, unterstützt SQL Data Warehouse Transaktionen als Teil des Data Warehouse-Workloads. Um allerdings eine angemessene Leistung von SQL Data Warehouse sicherzustellen, wurden einige Features im Vergleich zu SQL Server eingeschränkt. In diesem Artikel werden die Unterschiede hervorgehoben und die anderen Features aufgelistet.

## Transaktionsisolationsstufen
SQL Data Warehouse implementiert ACID-Transaktionen. Die Isolation der Transaktionsunterstützung ist jedoch beschränkt auf `READ UNCOMMITTED` und kann nicht geändert werden. Sie können eine Reihe von Codemethoden implementieren, um fehlerhafte Datenlesevorgänge zu verhindern, wenn dies ein Problem für Sie darstellt. Die am häufigsten verwendeten Methoden nutzen sowohl CTAS als auch Wechsel von Partitionstabellen (oftmals gleitendes Fenstermuster genannt), um zu verhindern, dass Benutzer Daten abrufen, die noch vorbereitet werden. Sichten, die die Daten vorab filtern, sind auch ein beliebter Ansatz.

## Transaktionsgröße
Eine einzelne Transaktion zur Datenänderung ist in Bezug auf die Größe beschränkt. Der Grenzwert wird heute „pro Verteilung“ angewendet. Zum Ermitteln der Gesamtsumme müssen wir daher den Grenzwert mit der Verteilungsanzahl multiplizieren. Um eine Annäherung für die maximale Zeilenanzahl in der Transaktion zu erhalten, teilen Sie die Verteilungsobergrenze durch die Gesamtgröße jeder Spalte. Bei Spalten mit variabler Länge können Sie erwägen, anstelle der maximalen Größe eine durchschnittliche Spaltenlänge zu verwenden.

Für die Tabelle unten gelten die folgenden Annahmen:

* Gleichmäßige Verteilung der Daten
* Durchschnittliche Zeilenlänge beträgt 250 Byte

| DWU | Obergrenze pro Verteilung (GB) | Anzahl der Verteilungen | Max. Transaktionsgröße (GB) | Zeilenanzahl pro Verteilung | Max. Zeilenzahl pro Transaktion |
| ------ | -------------------------- | ----------------------- | -------------------------- | ----------------------- | ------------------------ |
| DW100 | 1 | 60 | 60 | 4\.000.000 | 240\.000.000 |
| DW200 | 1,5 | 60 | 90 | 6\.000.000 | 360\.000.000 |
| DW300 | 2,25 | 60 | 135 | 9\.000.000 | 540\.000.000 |
| DW400 | 3 | 60 | 180 | 12\.000.000 | 720\.000.000 |
| DW500 | 3,75 | 60 | 225 | 15\.000.000 | 900\.000.000 |
| DW600 | 4,5 | 60 | 270 | 18\.000.000 | 1\.080.000.000 |
| DW1000 | 7,5 | 60 | 450 | 30\.000.000 | 1\.800.000.000 |
| DW1200 | 9 | 60 | 540 | 36\.000.000 | 2\.160.000.000 |
| DW1500 | 11,25 | 60 | 675 | 45\.000.000 | 2\.700.000.000 |
| DW2000 | 15 | 60 | 900 | 60\.000.000 | 3\.600.000.000 |

Die Obergrenze für die Transaktionsgröße wird pro Transaktion oder Vorgang angewendet. Sie wird nicht übergreifend für alle gleichzeitigen Transaktionen angewendet. Daher ist es für jede Transaktion zulässig, diese Menge an Daten in das Protokoll zu schreiben.

Informationen zum Optimieren und Reduzieren der Datenmenge, die in das Protokoll geschrieben wird, finden Sie im Artikel [Bewährte Methoden für Transaktionen][].

> [AZURE.WARNING] Die maximale Transaktionsgröße kann nur für Tabellen mit HASH- oder ROUND\_ROBIN-Verteilung erreicht werden, bei denen die Daten gleichmäßig verteilt werden. Wenn bei der Transaktion Daten auf verzerrte Weise in die Verteilungen geschrieben werden, wird die Obergrenze wahrscheinlich vor der maximalen Transaktionsgröße erreicht.
<!--REPLICATED_TABLE-->

## Transaktionsstatus
SQL Data Warehouse verwendet die XACT\_STATE()-Funktion, um eine fehlgeschlagene Transaktion mit dem Wert "-2" zu melden. Dies bedeutet, dass die Transaktion fehlgeschlagen und nur für den Rollback markiert ist.

> [AZURE.NOTE] Die Verwendung von "-2" in der XACT\_STATE-Funktion zum Kennzeichnen einer fehlgeschlagenen Transaktion stellt für SQL Server unterschiedliche Verhalten dar. SQL Server verwendet den Wert "-1" für eine Transaktion, für die kein Commit durchgeführt werden kann. SQL Server kann einige Fehler innerhalb einer Transaktion tolerieren, ohne als nicht commitfähig gekennzeichnet zu werden. SELECT 1/0 würde beispielsweise einen Fehler verursachen, jedoch keinen Transaktionszustand erzwingen, der keinen Commit zulässt. SQL Server lässt auch Lesevorgänge in der Transaktion zu, für die kein Commit möglich ist. Dies ist allerdings in SQLDW nicht der Fall. Bei einem Fehler innerhalb einer SQLDW-Transaktion wird automatisch der Zustand "-2" festgelegt, einschließlich SELECT 1/0-Fehlern. Es ist daher wichtig, zu überprüfen, ob in Ihrem Anwendungscode XACT\_STATE() verwendet wird.

In SQL Server kann ein Codefragment angezeigt werden, das wie folgt aussieht:

```sql
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        ROLLBACK TRAN;

    END CATCH;
```

Beachten Sie, dass die `SELECT`-Anweisung vor der `ROLLBACK`-Anweisung auftritt. Beachten Sie auch, dass die Einstellung der `@xact`-Variable DECLARE verwendet und nicht `SELECT`.

In SQL Data Warehouse müsste der Code wie folgt aussehen:

```sql
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        ROLLBACK TRAN;

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

SELECT @xact;
```

Beachten Sie, dass das Rollback der Transaktion vor dem Lesen der Fehlerinformationen im `CATCH`-Block erfolgen muss.

## Error\_Line()-Funktion
Es ist auch erwähnenswert, dass SQL Data Warehouse die ERROR\_LINE()-Funktion nicht implementiert oder unterstützt. Wenn diese in Ihrem Code enthalten ist, müssen Sie sie entfernen, um die Kompatibilität mit SQL Data Warehouse zu gewährleisten. Verwenden Sie stattdessen Abfragebezeichnungen in Ihrem Code, um entsprechende Funktionalität zu implementieren. Weitere Informationen zu dieser Funktion finden Sie im Artikel [Abfragebezeichnungen].

## Verwenden von THROW und RAISERROR
THROW ist die modernere Implementierung zum Auslösen von Ausnahmen in SQL Data Warehouse, RAISERROR wird jedoch ebenfalls unterstützt. Es gibt aber einige erwähnenswerte Unterschiede.

- Benutzerdefinierte Fehlermeldungsnummern können für THROW nicht im Bereich 100.000 bis 150.000 liegen.
- RAISERROR-Fehlermeldungen sind bei 50.000 festgelegt.
- Die Verwendung von "sys.messages" wird nicht unterstützt.

## Einschränkungen
SQL Data Warehouse verfügt über einige weitere Einschränkungen in Bezug auf Transaktionen.

Dies sind:

- Keine verteilten Transaktionen
- Keine geschachtelten Transaktionen zulässig
- Keine Speicherpunkte zulässig
- Keine Unterstützung für DDL wie z.B. `CREATE TABLE` innerhalb von benutzerdefinierten Transaktionen

## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md
[Bewährte Methoden für Transaktionen]: sql-data-warehouse-develop-best-practices-transactions.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0629_2016-->