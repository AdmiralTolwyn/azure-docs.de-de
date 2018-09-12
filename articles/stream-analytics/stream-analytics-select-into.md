---
title: Debuggen von Azure Stream Analytics-Abfragen mithilfe von SELECT INTO
description: In diesem Artikel wird beschrieben, wie Sie mithilfe von SELECT INTO-Anweisungen in der Abfragesyntax Datenstichproben in der Mitte der Abfrage in Azure Stream Analytics-Aufträgen erstellen.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: b056d4c29464451d3dc0ef62437f934535820489
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697991"
---
# <a name="debug-queries-by-using-select-into-statements"></a>Debuggen von Abfragen mithilfe von SELECT INTO-Anweisungen

Bei der Datenverarbeitung in Echtzeit kann es hilfreich sein zu wissen, wie Daten in der Mitte der Abfrage aussehen. Da Eingaben oder Schritte in einen Azure Stream Analytics-Auftrag mehrmals gelesen werden können, lassen sich zusätzliche SELECT INTO-Anweisungen schreiben. Dabei werden Zwischendaten in den Speicher ausgegeben, die Sie auf Richtigkeit untersuchen können, was beim Debuggen eines Programms mithilfe von *Überwachungsvariablen* erfolgt.

## <a name="use-select-into-to-check-the-data-stream"></a>Verwenden vom SELECT INTO zum Überprüfen des Datenstroms

Die folgende Beispielabfrage in einem Azure Stream Analytics-Auftrag weist eine Datenstromeingabe, zwei Verweisdateneingaben und eine Ausgabe in Azure Table Storage auf. Die Abfrage verknüpft Daten aus dem Event Hub und aus zwei Verweisblobs, um den Namen und Informationen zur Kategorie abzurufen:

![Beispiel einer SELECT INTO-Abfrage](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Beachten Sie, dass der Auftrag ausgeführt wird, aber keine Ereignisse in der Ausgabe erzeugt werden. Auf der hier gezeigten Kachel **Überwachung** sehen Sie, dass die Eingabe Daten erzeugt, aber Sie wissen nicht, welcher Schritt des **JOIN**-Vorgangs das Löschen aller Ereignisse verursacht hat.

![Die Kachel „Überwachung“](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
In diesem Fall können Sie einige zusätzliche SELECT INTO-Anweisungen zum Protokollieren der JOIN-Zwischenergebnisse und der Daten hinzufügen, die in der Eingabe gelesen werden.

In diesem Beispiel haben wir zwei neue „temporäre Ausgaben“ hinzugefügt. Dabei kann es sich um eine beliebige Senke handeln. Hier wird Azure Storage als Beispiel verwendet:

![Hinzufügen zusätzlicher SELECT INTO-Anweisungen](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Sie können dann die Abfrage wie folgt umschreiben:

![Umgeschriebene SELECT INTO-Abfrage](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Starten Sie nun den Auftrag erneut, und führen Sie ihn ein paar Minuten aus. Fragen Sie dann „temp1“ und „temp2“ mit dem Visual Studio-Cloud-Explorer ab, um die folgenden Tabellen zu generieren:

**Tabelle temp1**
![SELECT INTO Tabelle temp1](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**Tabelle temp2**
![SELECT INTO Tabelle temp2](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Wie Sie sehen können, enthalten temp1 und temp2 Daten, und die Namensspalte wird in temp2 ordnungsgemäß aufgefüllt. Da jedoch in der Ausgabe weitere keine Daten vorhanden sind, ist etwas falsch:

![SELECT INTO output1 Tabelle ohne Daten](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Nachdem Datenstichproben erstellt wurden, können Sie fast sicher sein, dass der zweite JOIN-Vorgang das Problem ist. Sie können die Verweisdaten aus dem Blob heruntergeladen und untersuchen:

![SELECT INTO Verweistabelle](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Wie Sie sehen können, unterscheidet sich das Format der GUID in diesen Verweisdaten vom Format der Spalte [from] in temp2. Deshalb sind die Daten nicht wie erwartet in output1 gelandet.

Sie können das Format der Daten korrigieren, sie in das Verweisblob hochladen und es erneut versuchen:

![SELECT INTO temporäre Tabelle](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Dieses Mal werden die Daten in der Ausgabe wie erwartet formatiert und aufgefüllt.

![SELECT INTO endgültige Tabelle](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)


## <a name="get-help"></a>Hier erhalten Sie Hilfe

Um Hilfe zu erhalten, nutzen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

