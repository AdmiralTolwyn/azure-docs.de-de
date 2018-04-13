---
title: Testen einer Abfrage mithilfe von Beispieldaten in Azure Stream Analytics
description: In diesem Artikel wird beschrieben, wie Sie eine Abfrage mit einigen Beispieleingabedaten in Azure Stream Analytics testen.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 305b767ee86de6c8b04fed17514a9092afc2d735
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/12/2018
---
# <a name="test-a-query-and-sample-input-in-azure-stream-analytics"></a>Testen einer Abfrage und Beispieleingabe in Azure Stream Analytics 

Mithilfe von Azure Stream Analytics können Sie Stichproben von Eingabeereignissen erstellen, die aus einer Datei stammen, und Abfragen im Portal testen, ohne einen Auftrag starten oder beenden zu müssen.

## <a name="testing-your-query"></a>Testen Ihrer Abfrage

Öffnen Sie im Detailbereich des Stream Analytics-Auftrags das Blatt **Abfrage-Editor**, indem Sie unter **Abfrage** auf den Abfragenamen klicken. (Da in unserem Beispielszenario noch keine Abfrage erstellt wurde, klicken Sie auf die Platzhalter **< >**.)

![Der Stream Analytics-Abfrage-Editor](media/stream-analytics-sample-data-input/stream-analytics-query-editor.png)

Wie schon in der vorherigen Version wird das Blatt „Editor“ mit zahlreichen Funktionen zum Erstellen Ihrer Abfrage angezeigt. Das Blatt verfügt nun auf der linken Seite über einen neuen Bereich, in dem die Ein- und Ausgaben gezeigt werden, die von der Abfrage verwendet werden und für diesen Auftrag definiert wurden.

![Der Stream Analytics-Abfrage-Editor mit Ein- und Ausgabelisten](media/stream-analytics-sample-data-input/stream-analytics-query-editor-highlight.png)

Ferner angezeigt werden zusätzliche Ein- und Ausgaben, die nicht definiert sind. Sie stammen aus der neuen Abfragevorlage, mit der Sie beginnen. Sie ändern sich oder werden sogar vollständig ausgeblendet, sobald Sie die Abfrage bearbeiten. Sie können sie einstweilen problemlos ignorieren.

Um die Beispieleingabedaten zu testen, klicken Sie mit der rechten Maustaste auf eine Ihrer Eingaben und wählen dann **Beispieldaten aus Datei hochladen** aus.

![Der Befehl „Beispieldaten aus Datei hochladen“ im Stream Analytics Abfrage-Editor](media/stream-analytics-sample-data-input/stream-analytics-query-editor-upload.png)

Nachdem das Hochladen abgeschlossen ist, klicken Sie auf **Testen**, um diese Abfrage mit den Beispieldaten zu testen, die Sie zuvor bereitgestellt haben.

![Die Schaltfläche „Testen“ im Stream Analytics-Abfrage-Editor](media/stream-analytics-sample-data-input/stream-analytics-query-editor-test.png)

Wenn Sie die Testausgabe zur späteren Verwendung speichern möchten, wird die Ausgabe der Abfrage im Browser mit einem Link zu den Downloadergebnissen angezeigt. Sie können nun Ihre Abfrage einfach und wiederholt ändern und testen, um festzustellen, wie sich die Ausgabe ändert.

![Beispielausgabe im Stream Analytics-Abfrage-Editor](media/stream-analytics-sample-data-input/stream-analytics-query-editor-samples-output.png)

In der vorherigen Abbildung wurde eine zweite Ausgabe namens **HighAvgTempOutput** hinzugefügt.

Wenn Sie in einer Abfrage mehrere Ausgaben verwenden, können Sie die Ergebnisse jeder Ausgabe separat anzeigen und mühelos dazwischen umschalten.

Sobald Sie mit den Ergebnissen zufrieden sind, können Sie die Abfrage speichern, Ihren Auftrag starten, sich zurücklehnen und sich an der Leistungsfähigkeit von Stream Analytics erfreuen.

## <a name="get-help"></a>Hier erhalten Sie Hilfe

Um Hilfe zu erhalten, nutzen Sie das [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nächste Schritte
* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
