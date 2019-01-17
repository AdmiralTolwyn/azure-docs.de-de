---
title: 'Azure Analysis Services-Tutorial, Lektion 10: Erstellen von Partitionen | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt, wie Partitionen im Azure Analysis Services-Tutorialprojekt erstellt werden.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 11be6d10589d981335d1f63d06933dba5265348a
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191835"
---
# <a name="create-partitions"></a>Erstellen von Partitionen

In dieser Lektion wird erläutert, wie Partitionen erstellt werden, um die Tabelle „FactInternetSales“ in kleinere logische Bereiche zu unterteilen, die unabhängig von anderen Partitionen verarbeitet (aktualisiert) werden können. Standardmäßig verfügt jede Tabelle Ihres Modells über eine Partition, die alle Spalten und Zeilen der Tabelle enthält. Bei der Tabelle „FactInternetSales“ sollen die Daten nach Jahren unterteilt werden: eine Partition für jedes der fünf Jahre in der Tabelle. Jede Partition kann so unabhängig voneinander verarbeitet werden. Weitere Informationen finden Sie unter [Partitionen](https://docs.microsoft.com/sql/analysis-services/tabular-models/partitions-ssas-tabular). 
  
Geschätzte Zeit zum Bearbeiten dieser Lektion: **15 Minuten**  
  
## <a name="prerequisites"></a>Voraussetzungen  
Dieses Thema ist Teil eines Tutorials zur Tabellenmodellierung, das in der richtigen Reihenfolge absolviert werden sollte. Bevor Sie diese Lektion beginnen, sollten Sie die vorherige Lektion abgeschlossen haben: [Lektion 9: Erstellen von Hierarchien](../tutorials/aas-lesson-9-create-hierarchies.md).  
  
## <a name="create-partitions"></a>Erstellen von Partitionen  
  
#### <a name="to-create-partitions-in-the-factinternetsales-table"></a>Zum Erstellen von Partitionen in der Tabelle „FactInternetSales“, gehen Sie folgendermaßen vor:  
  
1.  Erweitern Sie im tabellarischen Modell-Explorer **Tabellen**, und klicken Sie mit der rechten Maustaste auf **FactInternetSales** > **Partitionen**.  
  
2.  Klicken Sie im Partitions-Manager auf **Kopieren**, und ändern Sie anschließend den Namen in **FactInternetSales2010**.
  
    Da die Partition nur Reihen für einen bestimmten Zeitraum, nämlich für das Jahr 2010, umfassen soll, müssen Sie den Abfrageausdruck ändern.
  
4.  Klicken Sie zum Öffnen des Abfrage-Editors auf **Entwurf**, und klicken Sie dann auf die Abfrage **FactInternetSales2010**.

5.  Klicken Sie in der Vorschau in der Spaltenüberschrift **OrderDate** auf den Pfeil nach unten, und klicken Sie anschließend auf **Datums-/Zeitfilter** > **Zwischen**.

    ![AAS-Lektion10-Abfrage-Editor](../tutorials/media/aas-lesson10-query-editor.png)

6.  Lassen Sie im Dialogfeld „Zeilen filtern“ unter **Show rows where: OrderDate** (Zeilen anzeigen mit: OrderDate) die Option **ist nach oder gleich** unverändert, und geben Sie im Feld „Datum“ den Wert **1.1.2010** ein. Lassen Sie den Operator **Und** ausgewählt, und wählen Sie **Ist vor** aus. Geben Sie im Datumsfeld **1/1/2011** ein, und klicken Sie anschließend auf **OK**.

    ![AAS-Lektion10-Zeilen-filtern](../tutorials/media/aas-lesson10-filter-rows.png)
    
    Im Abfrage-Editor wird unter ANGEWENDETE SCHRITTE ein weiterer Schritt mit dem Namen „Gefilterte Zeilen“ angezeigt. Mit diesem Filter werden nur Bestelldaten aus 2010 ausgewählt.

8.  Klicken Sie auf **Importieren**.

    Beachten Sie, dass der Abfrageausdruck im Partitions-Manager jetzt über eine zusätzliche Gefilterte-Zeilen-Klausel verfügt.

    ![AAS-Lektion10-Abfrage](../tutorials/media/aas-lesson10-query.png)
  
    Diese Anweisung gibt an, dass die Partition nur Daten in denjenigen Zeilen enthalten soll, in denen OrderDate wie in der Gefilterte-Zeilen-Klausel angegeben im Kalenderjahr 2010 liegt.  
  
  
#### <a name="to-create-a-partition-for-the-2011-year"></a>Zum Erstellen einer Partition für das Jahr 2011, gehen Sie folgendermaßen vor:  
  
1.  Klicken Sie in der Partitionsliste auf die Partition **FactInternetSales2010**, die Sie erstellt haben, und klicken Sie dann auf **Kopieren**.  Ändern Sie den Partitionsnamen in **FactInternetSales2011**. 

    Zum Erstellen einer neuen Gefilterte-Zeilen-Klausel müssen Sie nicht den Abfrage-Editor verwenden. Da Sie eine Kopie der Abfrage für 2010 erstellt haben, müssen Sie nur eine geringfügige Änderung in der Abfrage für 2011 vornehmen.
  
2.  Damit die Partition nur Zeilen für das Jahr 2011 enthält, ersetzen Sie in **Abfrageausdruck** die Jahre in der Gefilterte-Zeilen-Klausel durch **2011** bzw. **2012**, wie nachfolgend beschrieben:  
  
    ```  
    let
        Source = #"SQL/localhost;AdventureWorksDW2014",
        dbo_FactInternetSales = Source{[Schema="dbo",Item="FactInternetSales"]}[Data],
        #"Removed Columns" = Table.RemoveColumns(dbo_FactInternetSales,{"OrderDateKey", "DueDateKey", "ShipDateKey"}),
        #"Filtered Rows" = Table.SelectRows(#"Removed Columns", each [OrderDate] >= #datetime(2011, 1, 1, 0, 0, 0) and [OrderDate] < #datetime(2012, 1, 1, 0, 0, 0))
    in
        #"Filtered Rows"
   
    ```  
  
#### <a name="to-create-partitions-for-2012-2013-and-2014"></a>Zum Erstellen von Partitionen für 2012, 2013 und 2014, gehen Sie folgendermaßen vor:  
  
- Folgen Sie den Anweisungen der vorherigen Schritte, und erstellen Sie Partitionen für 2012, 2013 und 2014. Ändern Sie die Jahre in der Gefilterte-Zeilen-Klausel, damit nur die Zeilen für das jeweilige Jahr enthalten sind. 
  

## <a name="delete-the-factinternetsales-partition"></a>Löschen der Partition „FactInternetSales“
Da Sie nun eine Partition für jedes Jahr besitzen, können Sie die Partition „FactInternetSales“ löschen. Dies verhindert eine Überlappung, wenn Sie bei der Verarbeitung der Partitionen „Alle verarbeiten“ auswählen.

#### <a name="to-delete-the-factinternetsales-partition"></a>Zum Löschen der Partition „FactInternetSales“, gehen Sie folgendermaßen vor:
-  Klicken Sie auf die Partition „FactInternetSales“, und klicken Sie anschließend auf **Löschen**.



## <a name="process-partitions"></a>Verarbeiten von Partitionen  
Beachten Sie, dass im Partitions-Manager in der Spalte **Zuletzt verarbeitet** für jede der neu erstellten Partitionen angezeigt wird, dass diese Partitionen noch nicht verarbeitet wurden. Beim Erstellen von Partitionen sollten Sie daher die Aktion „Partitionen verarbeiten“ oder „Tabelle verarbeiten“ ausführen, um die Daten der Partitionen zu aktualisieren.  
  
#### <a name="to-process-the-factinternetsales-partitions"></a>Zum Verarbeiten der „FactInternetSales“-Partitionen, gehen Sie folgendermaßen vor:  
  
1.  Klicken Sie auf **OK**, um den Partitions-Manager zu schließen.  
  
2.  Klicken Sie auf die Tabelle **FactInternetSales**, und klicken Sie anschließend auf das Menü **Modell** > **Verarbeiten** > **Partitionen verarbeiten**.  
  
3.  Überprüfen Sie im Dialogfeld „Partitionen verarbeiten“, dass **Modus** auf **Standard verarbeiten** festgelegt ist.  
  
4.  Aktivieren Sie für jede der fünf Partitionen, die Sie erstellt haben, das Kontrollkästchen in der Spalte **Verarbeiten**, und klicken Sie anschließend auf **OK**.  

    ![AAS-Lektion10-Partitionen-verarbeiten](../tutorials/media/aas-lesson10-process-partitions.png)
  
    Wenn Sie zur Eingabe von Identitätswechsel-Anmeldeinformationen aufgefordert werden, geben Sie den Windows-Benutzernamen und das Kennwort ein, die Sie in Lektion 2 angegeben haben.  
  
    Das Dialogfeld **Datenverarbeitung** mit Prozessdetails für jede Partition wird angezeigt. Beachten Sie, dass für jede Partition eine andere Anzahl von Zeilen übertragen wird. Jede Partition enthält nur die Zeilen für das Jahr, das in der WHERE-Klausel der SQL-Anweisung angegebenen wurde. Sobald die Verarbeitung abgeschlossen ist, schließen Sie das Dialogfeld „Datenverarbeitung“.  
  
    ![AAS-Lektion10-Verarbeitung-abgeschlossen](../tutorials/media/aas-lesson10-process-complete.png)
  
 ## <a name="whats-next"></a>Wie geht es weiter?
Fahren Sie mit der nächsten Lektion fort: [Lektion 11: Erstellen von Rollen](../tutorials/aas-lesson-11-create-roles.md). 
