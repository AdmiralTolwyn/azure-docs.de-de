---
title: Ergänzende Lektion zum Azure Analysis Services-Tutorial – Unregelmäßige Hierarchien | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Sie unregelmäßige Hierarchien im Azure Analysis Services-Tutorial beseitigen können.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 955bc57bbf5f8bc3f0d91350f885d5dc77db93a8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2018
---
# <a name="supplemental-lesson---ragged-hierarchies"></a>Ergänzende Lektion – Unregelmäßige Hierarchien

In dieser ergänzenden Lektion beheben Sie ein häufiges Problem beim Pivotieren von Hierarchien, die leere Werte (Member) auf verschiedenen Ebenen enthalten. Ein Beispiel wäre etwa eine Organisation, in der einem hochrangigen Manager sowohl Abteilungsleiter als auch Mitarbeiter ohne Führungskompetenzen unterstellt sind. Ein anderes Beispiel wären geografische Hierarchien mit Land, Region und Stadt, bei denen einige Städte keinem Bundesland/Kanton untergeordnet sind (wie etwa im Fall von Washington D. C. oder Vatikanstadt). Wenn eine Hierarchie leere Member aufweist, steigt sie oft zu anderen oder unregelmäßigen Ebenen hinunter.

![aas-lektion-detail-unregelmäßige-hierarchien-tabelle](../tutorials/media/aas-lesson-detail-ragged-hierarchies-table.png)

Tabellarische Modelle mit dem Kompatibilitätsgrad 1400 verfügen über die zusätzliche Eigenschaft **Member ausblenden** für Hierarchien. Die **Standardeinstellung** geht davon aus, dass es auf keiner Ebene leere Member gibt. Die Einstellung **Leere Member ausblenden** schließt leere Member aus einer Hierarchie aus, wenn diese in ein PivotTable-Objekt oder einen Bericht eingefügt werden.  
  
Geschätzte Zeit zum Bearbeiten dieser Lektion: **20 Minuten**  
  
## <a name="prerequisites"></a>Voraussetzungen  
Dieses ergänzende Thema ist Teil eines Tutorials zur Tabellenmodellierung. Vor dem Ausführen der Aufgaben in dieser ergänzenden Lektion sollten Sie alle vorherigen Lektionen oder das Beispielmodellprojekt „Adventure Works Internet Sales“ abgeschlossen haben. 

Wenn Sie das Projekt „AW Internet Sales“ im Rahmen des Tutorials erstellt haben, enthält Ihr Modell noch keine unregelmäßigen Daten oder Hierarchien. Sie müssen dieses Problem zunächst herbeiführen, um diese ergänzende Lektion durchführen zu können. Dazu fügen Sie einige weitere Tabellen hinzu, erstellen Beziehungen, berechnete Spalten, ein Measure und eine neue Hierarchie „Organization“. Dies nimmt lediglich 15 Minuten in Anspruch. Anschließend können Sie das Problem in wenigen Minuten wieder lösen.  

## <a name="add-tables-and-objects"></a>Hinzufügen von Tabellen und Objekten
  
### <a name="to-add-new-tables-to-your-model"></a>So fügen Sie neue Tabellen in Ihrem Modell hinzu
  
1.  Erweitern Sie im tabellarischen Modell-Explorer **Datenquellen**, und klicken Sie anschließend auf Ihre Verbindung und dann auf **Neue Tabelle importieren**.
  
2.  Wählen Sie im Navigator **DimEmployee** und **FactResellerSales** aus, und klicken Sie anschließend auf **OK**.

3.  Klicken Sie im Abfrage-Editor auf **Importieren**.

4.  Erstellen Sie folgende [Beziehungen](../tutorials/aas-lesson-4-create-relationships.md):

    | Tabelle 1           | Column       | Filterrichtung   | Tabelle 2     | Column      | Aktiv |
    |-------------------|--------------|--------------------|-------------|-------------|--------|
    | FactResellerSales | OrderDateKey | Standard            | DimDate     | Datum        | Ja    |
    | FactResellerSales | DueDate      | Standard            | DimDate     | Datum        | Nein      |
    | FactResellerSales | ShipDateKey  | Standard            | DimDate     | Datum        | Nein      |
    | FactResellerSales | ProductKey   | Standard            | DimProduct  | ProductKey  | Ja    |
    | FactResellerSales | EmployeeKey  | Beide Tabellen | DimEmployee | EmployeeKey | Ja    |

5. Erstellen Sie in der Tabelle **DimEmployee** die folgenden [berechneten Spalten](../tutorials/aas-lesson-5-create-calculated-columns.md): 

    **Path** 
    ```
    =PATH([EmployeeKey],[ParentEmployeeKey])
    ```

    **FullName** 
    ```
    =[FirstName] & " " & [MiddleName] & " " & [LastName]
    ```

    **Ebene1** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,1)) 
    ```

    **Ebene2** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],2,1)) 
    ```

    **Ebene3** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],3,1)) 
    ```

    **Ebene4** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],4,1)) 
    ```

    **Ebene5** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],5,1)) 
    ```

6.  Erstellen Sie in der Tabelle **DimEmployee** eine [Hierarchie](../tutorials/aas-lesson-9-create-hierarchies.md) mit dem Namen **Organization**. Fügen Sie folgende Spalten in dieser Reihenfolge hinzu: **Ebene1**, **Ebene2**, **Ebene3**, **Ebene4** und **Ebene5**.

7.  Erstellen Sie in der Tabelle **FactResellerSales** das folgende [Measure](../tutorials/aas-lesson-6-create-measures.md):

    ```
    ResellerTotalSales:=SUM([SalesAmount])
    ```

8.  Verwenden Sie [Analysieren in Excel](../tutorials/aas-lesson-12-analyze-in-excel.md), um Excel zu öffnen und automatisch eine PivotTable zu erstellen.

9.  Fügen Sie unter **PivotTable-Felder** die Hierarchie **Organization** aus der Tabelle **DimEmployee** in **Zeilen** ein. Fügen Sie außerdem das Measure **ResellerTotalSales** aus der Tabelle **FactResellerSales** zu **Werte** hinzu.

    ![aas-lektion-detail-unregelmäßige-hierarchins-pivottable](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable.png)

    Wie Sie in der PivotTable sehen können, zeigt die Hierarchie unregelmäßige Zeilen an. Es gibt viele Zeilen, in denen leere Member angezeigt werden.

## <a name="to-fix-the-ragged-hierarchy-by-setting-the-hide-members-property"></a>So beseitigen Sie unregelmäßige Hierarchien mit der Eigenschaft „Member ausblenden“

1.  Erweitern Sie unter **tabellarischer Modell-Explorer** **Tabellen** > **DimEmployee** > **Hierarchien** > **Organization**.

2.  Wählen Sie unter **Eigenschaften** > **Member ausblenden** **Leere Member ausblenden** aus. 

    ![aas-lektion-detail-unregelmäßige-hierarchien-memberausblenden](../tutorials/media/aas-lesson-detail-ragged-hierarchies-hidemembers.png)

3.  Aktualisieren Sie in Excel die PivotTable. 

    ![aas-lektion-detail-unregelmäßige-hierarchien-pivottable-aktualisieren](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable-refresh.png)

    Das sieht doch sehr viel besser aus.

## <a name="see-also"></a>Siehe auch   
[Lektion 9: Erstellen von Hierarchien](../tutorials/aas-lesson-9-create-hierarchies.md)  
[Ergänzende Lektion – Dynamische Sicherheit](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[Ergänzende Lektion – Detailzeilen](../tutorials/aas-supplemental-lesson-detail-rows.md)  
