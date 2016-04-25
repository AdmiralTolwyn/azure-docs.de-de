<properties
	pageTitle="Überwachen der Leistung der Azure-Web-App"
	description="Ladezeit für Diagramme und Antwortzeit, Informationen zu den Abhängigkeiten und Festlegen von Benachrichtigungen zur Leistung."
	services="azure-portal"
    documentationCenter="na"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/23/2015"
	ms.author="awills"/>

# Überwachen der Leistung der Azure-Web-App

Im [Azure-Portal](https://portal.azure.com) können Sie festlegen, dass Statistiken und Details zu den Anwendungsabhängigkeiten in Ihren [Azure-Web-Apps](../app-service-web/app-service-web-overview.md) oder [virtuellen Computern](../virtual-machines/virtual-machines-linux-about.md) gesammelt werden.

Azure unterstützt die Überwachung der Anwendungsleistung (oder *APM*, Application Performance Monitoring) durch die Nutzung von *Erweiterungen*. Diese Erweiterungen werden in Ihrer Anwendung installiert und geben die gesammelten Daten an die Überwachungsdienste zurück.

Application Insights und New Relic sind zwei der zur Verfügung stehenden Erweiterungen zur Leistungsüberwachung. Um sie zu verwenden, müssen Sie einen Agent zur Laufzeit installieren. Application Insights bietet auch die Option zum Erstellen des Codes mit einem SDK. Mit dem SDK können Sie Code schreiben, um die Auslastung und Leistung Ihrer Anwendung genauer zu überwachen.

## Aktivieren einer Erweiterung

1. Klicken Sie auf **Durchsuchen**, und wählen Sie die Web-App oder den virtuellen Computer aus, für den Sie eine Überwachung durchführen möchten.

2. Fügen Sie die Application Insights- oder die New Relic-Erweiterung hinzu.

    Wenn Sie eine Web-App instrumentieren möchten:

![Einstellungen, Erweiterungen, Hinzufügen, Application Insights](./media/insights-perf-analytics/05-extend.png)

Oder wenn Sie einen virtuellen Computer verwenden:

![Klicken Sie auf die Kachel „Analytics“.](./media/insights-perf-analytics/10-vm1.png)

### Optional für Application Insights: Neuerstellen mit dem SDK

Application Insights kann durch Installieren eines SDKs in Ihrer App eine detailliertere Telemetrie bereitstellen.

Fügen Sie in Visual Studio Ihrem Projekt das Application Insights SDK hinzu.

![Klicken Sie mit der rechten Maustaste auf das Webprojekt, und wählen Sie "Application Insights hinzufügen".](./media/insights-perf-analytics/03-add.png)

Wenn Sie zur Anmeldung aufgefordert werden, verwenden Sie die Anmeldeinformationen für Ihr Azure-Konto.

Sie können die Telemetrie testen, indem Sie die App auf dem Entwicklungscomputer ausführen, oder Sie können einfach fortfahren und die App veröffentlichen.

Das SDK stellt eine API bereit, sodass Sie eine [benutzerdefinierte Telemetrie schreiben](../application-insights/app-insights-api-custom-events-metrics.md) können, um die Verwendung nachzuverfolgen.

## Untersuchen der Daten

Verwenden Sie die Anwendung für eine gewisse Zeit, um einige Telemetriedaten zu generieren.

1. In der Web-App oder auf dem virtuellen Computer können Sie sehen, dass die Erweiterung installiert ist.
2. Klicken Sie auf die Zeile für Ihre Anwendung, um zu diesem Anbieter zu navigieren: ![Klicken Sie auf "Aktualisieren".](./media/insights-perf-analytics/06-overview.png)

Sie können auch **Durchsuchen** verwenden, um direkt zu Ihrer Application Insights-Komponente oder zum verwendeten New Relic-Konto zu wechseln.

Nachdem Sie das Blatt geöffnet haben, beispielsweise für Application Insights, können Sie folgende Aufgaben ausführen:
- Öffnen der Einstellung „Leistung“.

![Klicken Sie auf dem Blatt mit der Application Insights-Übersicht auf die Kachel "Leistung".](./media/insights-perf-analytics/07-dependency.png)

- Führen Sie einen Drillthrough zu den einzelnen Anforderungen aus:

![Klicken Sie im Raster auf eine Abhängigkeit, um die zugehörigen Anforderungen anzuzeigen.](./media/insights-perf-analytics/08-requests.png)

- Das folgende Beispiel zeigt die in einer SQL-Abhängigkeit aufgewendete Zeit, einschließlich der Anzahl von SQL-Aufrufen sowie zugehörige Statistiken, z. B. die durchschnittliche Dauer und die Standardabweichung.

![](./media/insights-perf-analytics/01-example.png)



## Nächste Schritte

* [Überwachen von Dienstintegritätsmetriken](insights-how-to-customize-monitoring.md), um sicherzustellen, dass Ihr Dienst verfügbar und reaktionsfähig ist.
* [Aktivieren von Überwachung und Diagnose](insights-how-to-use-diagnostics.md), um detaillierte Hochfrequenzmetriken zu Ihrem Dienst zu sammeln.
* [Empfangen von Warnbenachrichtigungen](insights-receive-alert-notifications.md), wenn ein operatives Ereignis auftritt oder Metriken einen Schwellenwert überschreiten.
* Verwenden von [Application Insights für JavaScript-Apps und Webseiten](../application-insights/app-insights-web-track-usage.md), um eine Clientanalyse über die Browser zu erhalten, mit denen auf eine Webseite zugegriffen wird.
* [Überwachen der Verfügbarkeit und Reaktionsfähigkeit einer beliebigen Webseite](../application-insights/app-insights-monitor-web-app-availability.md) mit Application Insights, um zu ermitteln, ob eine Seite offline ist.

<!---HONumber=AcomDC_0413_2016-->