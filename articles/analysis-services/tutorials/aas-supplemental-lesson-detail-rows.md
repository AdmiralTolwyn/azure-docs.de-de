---
title: Ergänzende Lektion zum Azure Analysis Services-Tutorial – Detailzeilen | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie ein Detailzeilenausdruck im Azure Analysis Services-Tutorialprojekt erstellt werden kann.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 626258488afec4b3c3f025ae85bd3b5866aa0cf3
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443976"
---
# <a name="supplemental-lesson---detail-rows"></a>Ergänzende Lektion – Detailzeilen

In dieser ergänzenden Lektion verwenden Sie den DAX-Editor zum Definieren eines benutzerdefinierten Detailzeilenausdrucks. Ein Detailzeilenausdrucks ist eine Eigenschaft eines Measure, die Endbenutzer mit weiteren Informationen zu aggregierten Ergebnissen eines Measure versorgt. 
  
Geschätzte Zeit zum Bearbeiten dieser Lektion: **10 Minuten**  
  
## <a name="prerequisites"></a>Voraussetzungen  
Diese ergänzende Lektion ist Teil eines Tutorials zur Tabellenmodellierung. Vor dem Ausführen der Aufgaben in dieser ergänzenden Lektion sollten Sie alle vorherigen Lektionen oder das Beispiel-Modellprojekt „Adventure Works Internet Sales“ abgeschlossen haben.  
  
## <a name="whats-the-issue"></a>Um welches Problem geht es?
Sehen Sie sich das Measure „InternetTotalSales“ an, bevor Sie einen Detailzeilenausdruck hinzufügen.

1.  Klicken Sie in SSDT auf das Menü **Modell** und dann auf **In Excel analysieren**, um Excel zu öffnen und eine leere PivotTable zu erstellen.
  
2.  Fügen Sie in den **PivotTable-Feldern** das Measure **InternetTotalSales** aus der Tabelle „FactInternetSales“ zu **Werte** hinzu, und **CalendarYear** aus der Tabelle „DimDate“ zu **Spalten** und **EnglishCountryRegionName** zu **Zeilen**. Die PivotTable zeigt nun ein aggregiertes Ergebnis für das Measure „InternetTotalSales“ nach Region und Jahr an. 

    ![aas-lektion-detail-zeilen-pivottable](../tutorials/media/aas-lesson-detail-rows-pivottable.png)

3. Doppelklicken Sie in der PivotTable auf einen aggregierten Wert für ein Jahr und einen Regionsnamen. Der Wert für Australien und das Jahr 2014. Ein neues Blatt wird geöffnet, das Daten enthält, die aber nicht wirklich hilfreich sind.

    ![aas-lektion-detail-zeilen-pivottable](../tutorials/media/aas-lesson-detail-rows-sheet.png)
  
Das Ziel hier ist eine Tabelle mit Datenspalten und -zeilen, die zum aggregierten Ergebnis des „InternetTotalSales“-Measure beitragen. Zu diesem Zweck fügen Sie einen Detailzeilenausdruck als Eigenschaft des Measure hinzu.

## <a name="add-a-detail-rows-expression"></a>Hinzufügen eines Dateizeilenausdrucks

#### <a name="to-create-a-detail-rows-expression"></a>So erstellen Sie einen Dateizeilenausdruck 
  
1. Klicken Sie im Measureraster der Tabelle „FactInternetSales“ auf das Measure **InternetTotalSales**. 

2. Klicken Sie unter **Eigenschaften** > **Detailzeilenausdruck** auf die Schaltfläche „Editor“, um den DAX-Editor zu öffnen.

    ![aas-lektion-detail-zeilen-ellipse](../tutorials/media/aas-lesson-detail-rows-ellipse.png)

3. Geben Sie im DAX-Editor den folgenden Ausdruck ein:

    ```
    SELECTCOLUMNS(
    FactInternetSales,
    "Sales Order Number", FactInternetSales[SalesOrderNumber],
    "Customer First Name", RELATED(DimCustomer[FirstName]),
    "Customer Last Name", RELATED(DimCustomer[LastName]),
    "City", RELATED(DimGeography[City]),
    "Order Date", FactInternetSales[OrderDate],
    "Internet Total Sales", [InternetTotalSales]
    )

    ```

    Dieser Ausdruck gibt die Namen, Spalten und Berechnungsergebnisse aus der Tabelle „FactInternetSales“ und verknüpften Tabellen zurück, wenn ein Benutzer doppelt auf ein aggregiertes Ergebnis in einer PivotTable oder einem Bericht klickt.

4. Löschen Sie in Excel das Blatt, das in Schritt 3 erstellt wurde, und doppelklicken Sie anschließend auf einen aggregierten Wert. Dieses Mal öffnet sich ein neues Blatt mit viel mehr hilfreichen Daten, da ein Detailzeilenausdruck für das Measure definiert wurde.

    ![aas-lektion-detail-zeilen-detailblatt](../tutorials/media/aas-lesson-detail-rows-detailsheet.png)

5. Stellen Sie ihr Modell erneut bereit.

  
## <a name="see-also"></a>Weitere Informationen  

[SELECTCOLUMNS-Funktion (DAX)](https://msdn.microsoft.com/library/mt761759.aspx)   
[Ergänzende Lektion – Dynamische Sicherheit](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Ergänzende Lektion – Unregelmäßige Hierarchien](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
 