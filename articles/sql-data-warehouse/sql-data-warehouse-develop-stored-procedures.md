---
title: Gespeicherte Prozeduren in SQL Data Warehouse | Microsoft Docs
description: "Tipps zum Implementieren von gespeicherten Prozeduren in Azure SQL Data Warehouse für die Entwicklung von Lösungen"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 9b238789-6efe-4820-bf77-5a5da2afa0e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e42d80f0ca35f3fbb67389c66d072bc40d8a8d2c
ms.lasthandoff: 04/03/2017


---
# <a name="stored-procedures-in-sql-data-warehouse"></a>Gespeicherte Prozeduren in SQL Data Warehouse
SQL Data Warehouse unterstützt viele Transact-SQL-Features aus SQL Server. Darüber hinaus sind bestimmte Features für das horizontale Hochskalieren vorhanden, die genutzt werden sollten, um die Leistung einer Lösung zu verbessern.

In Bezug auf die Verwaltung der Skalierung und Leistung von SQL Data Warehouse sind auch einige Features und Funktionen vorhanden, die Unterschiede beim Verhalten aufweisen. Es gibt auch Features, die nicht unterstützt werden.

In diesem Artikel wird erläutert, wie Sie gespeicherte Prozeduren in SQL Data Warehouse implementieren.

## <a name="introducing-stored-procedures"></a>Einführung in gespeicherte Prozeduren
Gespeicherte Prozeduren sind eine hervorragende Möglichkeit zum Einschließen (Kapseln) von SQL-Code. Hierbei wird Code im Data Warehouse in der Nähe Ihrer Daten gespeichert. Indem Code in besser verwaltbare Einheiten eingeschlossen wird, unterstützen gespeicherte Prozeduren Entwickler beim Modularisieren ihrer Lösungen. Dies ermöglicht eine bessere Wiederverwendbarkeit des Codes. Für jede gespeicherte Prozedur können außerdem Parameter verwendet werden, um sie flexibler zu machen.

SQL Data Warehouse bietet eine vereinfachte und optimierte Implementierung von gespeicherten Prozeduren. Der größte Unterschied im Vergleich zu SQL Server ist, dass die gespeicherte Prozedur kein vorab kompilierter Code ist. In Data Warehouses ist die Kompilierungszeit im Allgemeinen weniger wichtig. Wichtiger ist es, dass der Code der gespeicherten Prozedur richtig optimiert wird, wenn er für große Datenvolumina verwendet wird. Das Ziel besteht darin, Stunden, Minuten und Sekunden zu sparen, keine Millisekunden. Es ist daher hilfreicher, gespeicherte Prozeduren als Container für SQL-Logik zu betrachten.     

Wenn SQL Data Warehouse Ihre gespeicherte Prozedur ausführt, werden die SQL-Anweisungen zur Laufzeit analysiert, übersetzt und optimiert. Während dieses Vorgangs wird jede Anweisung in verteilte Abfragen konvertiert. Der SQL-Code, der für die Daten tatsächlich ausgeführt wird, unterscheidet sich von der übermittelten Abfrage.

## <a name="nesting-stored-procedures"></a>Schachteln von gespeicherten Prozeduren
Wenn gespeicherte Prozeduren andere gespeicherte Prozeduren aufrufen oder dynamischen SQL-Code ausführen, wird die innere gespeicherte Prozedur bzw. der Codeaufruf als „geschachtelt“ bezeichnet.

SQL Data Warehouse unterstützt maximal acht Schachtelungsebenen. Dies ist ein Unterschied zu SQL Server. In SQL Server sind 32 Schachtelungsebenen zulässig.

Der Aufruf der obersten gespeicherten Prozedur entspricht Schachtelungsebene 1.

```sql
EXEC prc_nesting
```
Wenn die gespeicherte Prozedur auch einen weiteren EXEC-Aufruf durchführt, wird dadurch die Schachtelungsebene auf 2 erhöht.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Wenn die zweite Prozedur dann dynamischen SQL-Code ausführt, wird dadurch die Schachtelungsebene auf 3 erhöht.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Beachten Sie, dass @@NESTLEVEL von SQL Data Warehouse derzeit nicht unterstützt wird. Sie müssen die Schachtelungsebene selbst verfolgen. Es ist eher unwahrscheinlich, dass Sie die Obergrenze von acht Schachtelungsebenen erreichen. Falls doch, müssen Sie Ihren Code entsprechend anpassen, damit diese Obergrenze eingehalten wird.

## <a name="insertexecute"></a>INSERT..EXECUTE
SQL Data Warehouse lässt nicht zu, dass Sie das Resultset einer gespeicherten Prozedur mit einer INSERT-Anweisung verwenden. Es gibt aber eine andere Möglichkeit.

Ein Beispiel hierzu finden Sie im folgenden Artikel zu [temporären Tabellen] .

## <a name="limitations"></a>Einschränkungen
Es gibt einige Aspekte von gespeicherten Transact-SQL-Prozeduren, die in SQL Data Warehouse nicht implementiert sind.

Sie lauten wie folgt:

* Temporäre gespeicherte Prozeduren
* Nummerierte gespeicherte Prozeduren
* Erweiterte gespeicherte Prozeduren
* Gespeicherte CLR-Prozeduren
* Verschlüsselungsoption
* Replikationsoption
* Tabellenwertparameter
* Schreibgeschützte Parameter
* Standardparameter
* Ausführungskontexte
* return-Anweisung

## <a name="next-steps"></a>Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][development overview].

<!--Image references-->

<!--Article references-->
[temporären Tabellen]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/library/ms187371.aspx

<!--Other Web references-->

