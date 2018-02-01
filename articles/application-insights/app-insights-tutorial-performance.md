---
title: Diagnostizieren von Leistungsproblemen mit Azure Application Insights | Microsoft-Dokumentation
description: Tutorial zum Suchen und Diagnostizieren von Leistungsproblemen in Ihrer Anwendung mithilfe von Azure Application Insights
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 437c45891d1d20f5fadca8a58954185a3aef56ac
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2018
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Suchen und Diagnostizieren von Leistungsproblemen mit Azure Application Insights

Azure Application Insights erfasst die Telemetriedaten Ihrer Anwendung, um die Funktion und die Leistung zu analysieren.  Sie können mit diesen Informationen ggf. Probleme identifizieren oder Verbesserungen für die Anwendung entwerfen, die eine breite Benutzergruppe erreichen.  In diesem Tutorial lernen Sie den Prozess kennen, in dem die Leistung der Serverkomponenten der Anwendung und die Perspektive des Clients analysiert wird.  Folgendes wird vermittelt:

> [!div class="checklist"]
> * Ermitteln der Leistung von serverseitigen Vorgängen
> * Analysieren von Servervorgängen zur Grundursachenermittlung von Leistungseinbußen
> * Ermitteln der langsamsten clientseitigen Vorgänge
> * Analysieren der Details der Seitenansichten mit der Abfragesprache


## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Installieren Sie [Visual Studio 2017](https://www.visualstudio.com/downloads/) mit den folgenden Workloads:
    - ASP.NET und Webentwicklung
    - Azure-Entwicklung
- Stellen Sie eine .NET-Anwendung in Azure bereit, und [aktivieren Sie das Application Insights SDK](app-insights-asp-net.md).
- [Aktivieren Sie Application Insights Profiler](app-insights-profiler.md#installation) für Ihre Anwendung.

## <a name="log-in-to-azure"></a>Anmelden an Azure
Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) am Azure-Portal an.

## <a name="identify-slow-server-operations"></a>Identifizieren langsamer Servervorgänge
Application Insights erfasst die Leistungsdetails der verschiedenen Vorgänge in Ihrer Anwendung.  Durch Identifizieren der Vorgänge mit der längsten Dauer können Sie potenzielle Probleme diagnostizieren oder die aktuelle Entwicklung bestmöglich auf die Verbesserung der allgemeinen Leistung der Anwendung ausrichten.

1. Wählen Sie zuerst **Application Insights** und anschließend Ihr Abonnement aus.  
1. Um den Bereich **Leistung** zu öffnen, klicken Sie entweder im Menü **Untersuchen** auf die Option **Leistung** oder auf das Diagramm **Serverantwortzeit**.

    ![Leistung](media/app-insights-tutorial-performance/performance.png)

2. Der Bereich **Leistung** zeigt die Anzahl und durchschnittliche Dauer der einzelnen Anwendungsvorgänge an.  Mit diesen Informationen können Sie die Vorgänge ermitteln, die die größten Auswirkungen auf Benutzer haben. In diesem Beispiel sind **GET Customers/Details** und **GET Home/Index** aufgrund der relativ hohen Dauer und der Anzahl der Aufrufe auffällige Vorgänge.  Andere Vorgänge dauern möglicherweise länger, wurden aber nur selten aufgerufen. Der Effekt einer Verbesserung wäre daher minimal.  

    ![Bereich „Leistung“](media/app-insights-tutorial-performance/performance-blade.png)

3. Das Diagramm zeigt derzeit die durchschnittliche Dauer aller Vorgänge im Zeitverlauf an.  Fügen Sie die für Sie interessanten Vorgänge hinzu, indem Sie sie an das Diagramm anheften.  Dies verdeutlicht, dass es einige Spitzen gibt, die eine Untersuchung wert sind.  Isolieren Sie sie weiter, indem Sie das Zeitfenster des Diagramms verkleinern.

    ![Anheftvorgänge](media/app-insights-tutorial-performance/pin-operations.png)

4.  Klicken Sie auf einen Vorgang, um seinen Leistungsbereich auf der rechten Seite anzuzeigen. Hier wird die Dauerverteilung verschiedener Abfragen dargestellt.  Benutzer bemerken Leistungseinbußen in der Regel bei einer Verzögerung von ca. einer halben Sekunde. Legen Sie die Dauer also auf mehr als 500 Millisekunden fest.  

    ![Dauerverteilung](media/app-insights-tutorial-performance/duration-distribution.png)

5.  In diesem Beispiel sehen Sie, dass die Verarbeitungsdauer für eine erhebliche Anzahl von Anforderungen bei über einer Sekunde liegt. Die Details dieses Vorgangs lassen sich durch Klicken auf **Vorgangsdetails** aufrufen.

    ![Vorgangsdetails](media/app-insights-tutorial-performance/operation-details.png)

    > [!NOTE]
    Aktivieren Sie die [Vorschauoberfläche](app-insights-previews.md) „Unified details: E2E Transaction Diagnostics“ (Einheitliche Details: E2E-Transaktionsdiagnose), um die gesamten serverseitigen Telemetriedaten in einer Bildschirmansicht anzuzeigen, z.B. Anforderungen, Abhängigkeiten, Ausnahmen, Ablaufverfolgungen, Ereignisse usw. 

    Bei aktivierter Vorschau können Sie verfolgen, wie viel Zeit für Abhängigkeitsaufrufe aufgewendet wurde und welche Fehler oder Ausnahmen aufgetreten sind – alles in einer Oberfläche. Für komponentenübergreifende Transaktionen ermöglicht das Gantt-Diagramm zusammen mit dem Detailbereich eine schnelle Diagnose der Grundursache in Bezug auf die Komponente, Abhängigkeit oder Ausnahme. Sie können den untersten Abschnitt erweitern, um den zeitlichen Ablauf für alle Ablaufverfolgungen oder Ereignisse anzuzeigen, die für den gewählten Komponentenvorgang gesammelt wurden. [Weitere Informationen zur neuen Oberfläche](app-insights-transaction-diagnostics.md)  

    ![Transaktionsdiagnose](media/app-insights-tutorial-performance/e2e-transaction-preview.png)


6.  Die Informationen, die Sie bisher gesammelt haben, bestätigen, dass es Leistungseinbußen gibt. Sie erzählen jedoch wenig über die Grundursache.  Hier kommt der **Profiler** ins Spiel: Er zeigt den tatsächlichen Code, der für den Vorgang ausgeführt wurde, und den Zeitaufwand für die einzelnen Schritte an. Einige Vorgänge haben möglicherweise keine Ablaufverfolgung, da der Profiler in regelmäßigen Abständen ausgeführt wird.  Im Laufe der Zeit sollten weitere Vorgänge aber Ablaufverfolgungen erhalten.  Um den Profiler für den Vorgang zu starten, klicken Sie auf **Profiler-Ablaufverfolgungen**.
5.  Die Ablaufverfolgung zeigt die einzelnen Ereignisse für jeden Vorgang an, damit Sie die Grundursache für die Dauer des gesamten Vorgangs diagnostizieren können.  Klicken Sie auf eines der obersten Beispiele, die am längsten dauern.
6.  Klicken Sie auf **Langsamsten Pfad anzeigen**, um den Pfad der Ereignisse zu markieren, die für die hohe Gesamtdauer des Vorgangs verantwortlich sind.  In diesem Beispiel können Sie sehen, dass der langsamste Aufruf von der Methode *FabrikamFiberAzureStorage.GetStorageTableData* stammt. Der Teil, der den Großteil der Zeit beansprucht, ist die Methode *CloudTable.CreateIfNotExist*. Wenn diese Codezeile bei jedem Aufruf der Funktion ausgeführt wird, werden unnötige Netzwerkaufruf- und CPU-Ressourcen verbraucht. Die beste Möglichkeit zum Korrigieren des Code besteht darin, diese Zeile in eine Startmethode einzufügen, die nur einmal ausgeführt wird. 

    ![Profilerdetails](media/app-insights-tutorial-performance/profiler-details.png)

7.  Der **Leistungstipp** oben im Bildschirm belegt die Einschätzung, dass die hohe Dauer der Wartezeit geschuldet ist.  Klicken Sie auf den Link **Warten**, um die Dokumentation zu den verschiedenen Ereignistypen aufzurufen.

    ![Leistungstipp](media/app-insights-tutorial-performance/performance-tip.png)

8.  Für eine weiterführende Analyse können Sie auf **ETL-Ablaufverfolgung herunterladen** klicken und die Ablaufverfolgung in Visual Studio herunterladen.

## <a name="use-analytics-data-for-server"></a>Verwenden von Analysedaten für Server
Application Insights Analytics bietet eine umfangreiche Abfragesprache, mit der Sie alle von Application Insights erfassten Daten analysieren können.  Sie können damit tiefgehende Analysen von Anforderungs- und Leistungsdaten ausführen.

1. Kehren Sie zum Bereich „Vorgangsdetails“ zurück, und klicken Sie auf die Schaltfläche „Analytics“ (Analysen).

    ![Schaltfläche „Analytics“](media/app-insights-tutorial-performance/server-analytics-button.png)

2. Application Insights Analytics wird mit einer Abfrage für jede der Ansichten im Bereich geöffnet.  Sie können diese Abfragen ohne weitere Veränderungen ausführen oder sie Ihren Anforderungen entsprechend ändern.  Die erste Abfrage zeigt die Dauer dieses Vorgangs im Zeitverlauf an.

    ![Analyse](media/app-insights-tutorial-performance/server-analytics.png)


## <a name="identify-slow-client-operations"></a>Identifizieren langsamer Clientvorgänge
Zusätzlich zum Ermitteln von optimierbaren Serverprozessen kann Application Insights die Perspektive des Clientbrowsers analysieren.  So können Sie nicht nur mögliche Verbesserungen an Clientkomponenten, sondern auch Probleme mit verschiedenen Browsern oder verschiedenen Standorten identifizieren.

1. Wählen Sie unter **Untersuchen** die Option **Browser** aus, um die Browserzusammenfassung zu öffnen.  Dies ist eine grafische Zusammenfassung der verschiedenen Telemetrien Ihrer Anwendung aus der Perspektive des Browsers.

    ![Browserzusammenfassung](media/app-insights-tutorial-performance/browser-summary.png)

2.  Scrollen Sie nach unten bis zu **Welche meiner Seiten sind am langsamsten?**.  Damit rufen Sie eine Liste der Anwendungsseiten auf, die am langsamsten von Clients geladen werden.  Mit diesen Informationen können Sie die Seiten priorisieren, die die größten Auswirkungen auf Benutzer haben.
3.  Klicken Sie auf eine der Seiten, um den Bereich **Seitenansicht** zu öffnen.  Im Beispiel wird die Seite **/FabrikamProd** im Durchschnitt sehr langsam geladen.  Der Bereich **Seitenansicht** stellt Details zu dieser Seite bereit, z.B. eine Aufschlüsselung der verschiedenen Zeiträume.

    ![Seitenansicht](media/app-insights-tutorial-performance/page-view.png)

4.  Klicken Sie auf die höchste Dauer, um die Details dieser Anforderungen aufzurufen.  Klicken Sie dann auf die einzelnen Anforderungen, um die Details zum Client anzuzeigen, der die Seite anfordert, z.B. den Browsertyp und dessen Standort.  Diese Informationen können Ihnen dabei helfen, zu bestimmen, ob Leistungsprobleme mit bestimmten Clienttypen zu tun haben.

    ![Anforderungsdetails](media/app-insights-tutorial-performance/request-details.png)

## <a name="use-analytics-data-for-client"></a>Verwenden von Analysedaten für Clients
So wie bei den für die Serverleistung gesammelten Daten stellt Application Insights alle Clientdaten für tiefgehende Analysen mit Analytics zur Verfügung.

1. Kehren Sie zur Browserzusammenfassung zurück, und klicken Sie auf das Analysesymbol.

    ![Analysesymbol](media/app-insights-tutorial-performance/client-analytics-icon.png)

2. Application Insights Analytics wird mit einer Abfrage für jede der Ansichten im Bereich geöffnet. Die erste Abfrage zeigt die Dauer für verschiedene Seitenansichten im Zeitverlauf an.

    ![Analyse](media/app-insights-tutorial-performance/client-analytics.png)

3.  Die intelligente Diagnose ist eine Funktion von Application Insights Analytics, die eindeutige Muster in den Daten ermittelt.  Wenn Sie auf den Punkt für die intelligente Diagnose im Liniendiagramm klicken, wird dieselbe Abfrage ohne Datensätze ausgeführt, die die Anomalie verursacht hat.  Details zu diesen Datensätzen werden im Kommentarabschnitt der Abfrage angezeigt, damit Sie ermitteln können, welche Eigenschaften dieser Seitenansichten die hohe Dauer verursacht haben.

    ![Intelligente Diagnose](media/app-insights-tutorial-performance/client-smart-diagnostics.png)


## <a name="next-steps"></a>Nächste Schritte
Nun, da Sie wissen, wie Sie Laufzeitausnahmen ermitteln, können Sie mit dem nächsten Tutorial fortfahren. Dort erfahren Sie, wie Sie im Falle von Fehlern Warnungen erstellen.

> [!div class="nextstepaction"]
> [Ausgeben von Warnungen zur Anwendungsintegrität](app-insights-tutorial-alert.md)
