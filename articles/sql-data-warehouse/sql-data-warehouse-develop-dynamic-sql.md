---
title: Verwenden von dynamischem SQL in Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Tipps zur Verwendung von dynamischem SQL-Code in Azure SQL Data Warehouse für die Entwickelung von Lösungen
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 604074e0a645918f7033360b79a1b7cad050c9e4
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31523165"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dynamischer SQL-Code in SQL Data Warehouse
Tipps zur Verwendung von dynamischem SQL-Code in Azure SQL Data Warehouse für die Entwickelung von Lösungen

## <a name="dynamic-sql-example"></a>Beispiel für dynamisches SQL

Beim Entwickeln von Anwendungscode für SQL Data Warehouse müssen Sie unter Umständen dynamischen SQL-Code verwenden, um flexible, generische und modulare Lösungen bereitstellen zu können. SQL Data Warehouse unterstützt derzeit keine BLOB-Datentypen. Dass keine Blobdatentypen unterstützt werden, könnte die Einschränkung der Größe Ihrer Zeichenfolgen zur Folge haben, da Blobdatentypen sowohl den Typ VARCHAR(MAX) als auch NVARCHAR(MAX) enthalten. Wenn Sie diese Typen im Anwendungscode zum Erstellen großer Zeichenfolgen verwendet haben, müssen Sie den Code in Segmente unterteilen und stattdessen die EXEC-Anweisung verwenden.

Ein einfaches Beispiel:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Wenn die Zeichenfolge kurz ist, können Sie [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) wie üblich verwenden.

> [!NOTE]
> Anweisungen, die als dynamisches SQL ausgeführt werden, unterliegen weiterhin allen TSQL-Validierungsregeln.
> 
> 

## <a name="next-steps"></a>Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](sql-data-warehouse-overview-develop.md).

