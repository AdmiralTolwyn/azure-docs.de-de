---
title: "Grundlegendes zur Stream Analytics-Auftragsüberwachung | Microsoft Docs"
description: "Grundlegendes zur Stream Analytics-Auftragsüberwachung"
keywords: "Abfrageüberwachung"
services: stream-analytics
documentationcenter: 
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 5f5cc00f-4a7b-491e-89e1-dbafea46d399
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 7474f45494c6190ffcac354e75458b18f5777fb9
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2017
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Grundlegendes zur Stream Analytics-Auftragsüberwachung und zum Überwachen von Abfragen

## <a name="introduction-the-monitor-page"></a>Einführung: die Seite „Überwachen“
Im Azure-Portal werden wichtige Leistungsmetriken angezeigt, die zum Überwachen der Leistung Ihrer Abfragen und Aufträge sowie für die Problembehandlung verwendet werden können. Navigieren Sie zum Anzeigen dieser Metriken zum Stream Analytics-Auftrag, dessen Metriken Sie interessieren, und zeigen Sie auf der Seite „Übersicht“ den Abschnitt **Überwachung** an.  

![Link „Überwachung“](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Das Fenster wird wie folgt angezeigt:

![Auftragsüberwachungs-Dashboard](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Verfügbare Metriken für Stream Analytics
| Metrik                 | Definition                               |
| ---------------------- | ---------------------------------------- |
| Speichereinheitnutzung in %       | Die Auslastung der Streamingeinheiten, die einem Auftrag über seine Registerkarte „Skalieren“ zugewiesen sind. Sollte dieser Indikator 80 % erreichen oder darüber steigen, ist die Wahrscheinlichkeit hoch, dass die Ereignisverarbeitung verzögert wird oder keine Fortschritte mehr macht. |
| Eingabeereignisse           | Vom Stream Analytics-Auftrag erhaltene Datenmenge, ausgedrückt in der Anzahl von Ereignissen. Kann verwendet werden, um sicherzustellen, dass Ereignisse an die Eingabequelle gesendet werden. |
| Ausgabeereignisse          | Vom Stream Analytics-Auftrag an das Ausgabeziel gesendete Datenmenge, ausgedrückt in der Anzahl von Ereignissen. |
| Ereignisse für falsche Reihenfolge    | Anzahl der Ereignisse, die in falscher Reihenfolge empfangen und anhand der Richtlinie für die Ereignissortierung entweder verworfen oder mit einem angepassten Zeitstempel versehen wurden. Dies kann von der Konfiguration der Einstellung „Toleranzfenster für Fehlordnung“ beeinflusst werden. |
| Konvertierungsfehler | Anzahl der Datenkonvertierungsfehler im Zusammenhang mit einem Stream Analytics-Auftrag. |
| Laufzeitfehler         | Gesamtanzahl von Fehlern im Zusammenhang mit der Abfrageverarbeitung (mit Ausnahme von Fehlern, die beim Untersuchen von Ereignissen oder beim Ausgeben von Ergebnissen ermittelt werden) |
| Ereignisse bei verspäteter Eingabe      | Anzahl von Ereignissen, die spät von der Quelle eintreffen und entweder verworfen wurden oder über eine Anpassung des Zeitstempels verfügen (basierend auf der Konfiguration der Richtlinie für die Ereignissortierung unter der Einstellung „Toleranzfenster für Eingangsverzögerung“). |
| Funktionsanforderungen      | Anzahl der Aufrufe an die Azure Machine Learning-Funktion (falls vorhanden) |
| Fehlerhafte Funktionsanforderungen | Anzahl der fehlerhaften Aufrufe an die Azure Machine Learning-Funktion (falls vorhanden) |
| Funktionsereignisse        | Anzahl der an die Azure Machine Learning-Funktion gesendeten Ereignisse (falls vorhanden) |
| Eingabeereignisbytes      | Vom Stream Analytics-Auftrag erhaltene Datenmenge, in Byte. Kann verwendet werden, um sicherzustellen, dass Ereignisse an die Eingabequelle gesendet werden. |


## <a name="customizing-monitoring-in-the-azure-portal"></a>Anpassen der Überwachung im Azure-Portal
Sie können den Diagrammtyp, die angezeigten Metriken und den Uhrzeitbereich in den Einstellungen unter "Diagramm bearbeiten" anpassen. Weitere Informationen finden Sie unter [Anpassen der Überwachung](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Zeitdiagramm für die Abfrageüberwachung](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Letzte Ausgabe
Ein weiterer interessanter Datenpunkt für die Überwachung Ihres Auftrags ist der Zeitpunkt der letzten Ausgabe, der auf der Übersichtsseite angezeigt wird.
Bei dieser Zeitangabe handelt es sich um den Anwendungszeitpunkt (d.h. den Zeitpunkt mit dem Zeitstempel aus den Ereignisdaten) der letzten Ausgabe Ihres Auftrags.

## <a name="get-help"></a>Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nächste Schritte
* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

