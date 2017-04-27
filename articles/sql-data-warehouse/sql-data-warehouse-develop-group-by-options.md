---
title: Group By-Optionen in SQL Data Warehouse | Microsoft Docs
description: "Tipps zum Implementieren von Group By-Optionen in Azure SQL Data Warehouse für die Entwicklung von Lösungen"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: f95a1e43-768f-4b7b-8a10-8a0509d0c871
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: da71cb834c13da5d0f5690f471efc6c696163f30
ms.lasthandoff: 04/03/2017


---
# <a name="group-by-options-in-sql-data-warehouse"></a>Group By-Optionen in SQL Data Warehouse
Die [GROUP BY][GROUP BY]-Klausel wird verwendet, um Daten zu einer Gruppe von Zeilen zusammenzufassen. Außerdem verfügt sie über einige Optionen zur Erweiterung der Funktionalität, die umgangen werden müssen, da sie von Azure SQL Data Warehouse nicht direkt unterstützt werden.

Die Optionen sind:

* GROUP BY mit ROLLUP
* GROUPING SETS
* GROUP BY mit CUBE

## <a name="rollup-and-grouping-sets-options"></a>Rollup- und Grouping Set-Optionen
Die einfachste Möglichkeit ist hierbei die alternative Verwendung von `UNION ALL` , um das Rollup durchzuführen, anstatt sich auf die explizite Syntax zu verlassen. Das Ergebnis ist identisch.

Dies ist ein Beispiel für eine Group By-Anweisung mit der `ROLLUP` -Option:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Durch die Verwendung von ROLLUP haben wir die folgenden Aggregationen angefordert:

* Land und Region
* Land
* Gesamtergebnis

Um dies zu ersetzen, müssen Sie `UNION ALL` verwenden. Für das Angeben der Aggregationen war die explizite Rückgabe der gleichen Ergebnisse erforderlich:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Für GROUPING SETS müssen wir lediglich den gleichen Prinzipal übernehmen, aber wir müssen nur UNION ALL-Abschnitte für die Aggregationsebenen erstellen, die wir anzeigen möchten.

## <a name="cube-options"></a>Cube-Optionen
Es ist möglich, mit dem UNION ALL-Ansatz ein GROUP BY WITH CUBE-Element zu erstellen. Das Problem ist, dass der Code schnell unübersichtlich und schwerfällig werden kann. Um dies zu vermeiden, können Sie diesen erweiterten Ansatz verwenden.

Wir verwenden hierfür das obige Beispiel.

Der erste Schritt ist das Definieren des „Cubes“, mit dem alle Aggregationsebenen festgelegt werden, die erstellt werden sollen. Es ist wichtig, das CROSS JOIN-Element der beiden abgeleiteten Tabellen zu beachten. Hiermit werden alle Ebenen für uns erzeugt. Der Rest des Codes dient eigentlich nur der Formatierung.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Hier sind die Ergebnisse des CTAS-Vorgangs angegeben:

![][1]

Der zweite Schritt ist das Angeben einer Zieltabelle zum Speichern von Zwischenergebnissen:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

Der dritte Schritt ist das Durchführen einer Schleife für den Cube mit den Spalten, um die Aggregation durchzuführen. Die Abfrage wird einmal für jede Zeile in der temporären #Cube-Tabelle ausgeführt, und die Ergebnisse werden in der temporären #Results-Tabelle gespeichert.

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Abschließend können wir die Ergebnisse zurückgeben, indem wir sie einfach aus der temporären #Results-Tabelle auslesen.

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Indem wir den Code in Abschnitte unterteilen und eine Schleife generieren, wird die Verwaltung und Pflege der Codeerstellung verbessert.

## <a name="next-steps"></a>Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][development overview].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[GROUP BY]: https://msdn.microsoft.com/library/ms177673.aspx


<!--Other Web references-->

