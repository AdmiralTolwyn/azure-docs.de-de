---
title: Einrichten der Web-App-Analyse für ASP.NET mit Azure Application Insights | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie mehr über die Leistung, die Verfügbarkeit und die Nutzungsanalysen für Ihre lokal oder in Azure gehostete ASP.NET-Website.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: mbullwin
ms.openlocfilehash: 4a1ea364180d389ffdbfc7cedd740a0e493070b3
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998993"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Einrichten von Application Insights für Ihre ASP.NET-Website

Mit diesem Verfahren wird Ihre ASP.NET-Web-App so konfiguriert, dass sie Telemetriedaten an den [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)-Dienst sendet. Dies funktioniert für ASP.NET-Apps, die entweder lokal auf Ihrem eigenen IIS-Server oder in der Cloud gehostet werden. Sie erhalten Diagramme und eine leistungsfähige Abfragesprache, mit deren Hilfe Sie die Leistung Ihrer App sowie deren Verwendung durch die Benutzer nachvollziehen können. Darüber hinaus erhalten Sie automatische Warnungen bei Ausfällen oder Leistungsproblemen. Für viele Entwickler sind diese Features bereits ausreichend, bei Bedarf kann die Telemetrie aber auch noch erweitert und angepasst werden.

Die Einrichtung ist mit wenigen Mausklicks in Visual Studio erledigt. Das Volumen der Telemetriedaten kann eingeschränkt werden, um Kosten zu vermeiden. Dadurch können Sie experimentieren und debuggen oder eine Website mit nur wenigen Benutzern überwachen. Und wenn Sie dann später Ihre Produktionswebsite überwachen möchten, können Sie den Grenzwert problemlos erhöhen.

## <a name="prerequisites"></a>Voraussetzungen
Sie benötigen Folgendes, um Application Insights Ihrer ASP.NET-Website hinzuzufügen:

- Installieren Sie [Visual Studio 2017 für Windows](https://www.visualstudio.com/downloads/) mit folgenden Workloads:
    - ASP.NET und Webentwicklung
    - Azure-Entwicklung

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="ide"></a> Schritt 1: Hinzufügen des Application Insights SDK

> [!IMPORTANT]
> Der Prozess zum Hinzufügen von Application Insights variiert je nach ASP.NET-Vorlagentyp. Wählen Sie bei Verwendung der Vorlage **Leer** oder **Mobile Azure-App** die Option **Projekt** > **Application Insights-Telemetrie hinzufügen**. Alle anderen ASP.NET-Vorlagen finden Sie unten in den Anleitungen. 

Klicken Sie mit der rechten Maustaste im Projektmappen-Explorer auf den Namen Ihrer Web-App, und wählen Sie **Application Insights konfigurieren**.

![Screenshot: Projektmappen-Explorer mit Hervorhebung von „Application Insights konfigurieren“](./media/asp-net/0001-configure-application-insights.png)

(Je nach der Version Ihres Application Insights SDK werden Sie ggf. aufgefordert, ein Upgrade auf das aktuelle SDK-Release durchzuführen. Wählen Sie **SDK aktualisieren**, falls Sie diese Aufforderung erhalten.)

![Screenshot: Neue Version des Microsoft Application Insights SDK verfügbar. Hervorhebung von „SDK aktualisieren“](./media/asp-net/0002-update-sdk.png)

Bildschirm für Application Insights-Konfiguration:

Wählen Sie die Option **Kostenlos starten**.

![Screenshot der Seite „App bei Application Insights registrieren“](./media/asp-net/0004-start-free.png)

Wenn Sie die Ressourcengruppe oder den Speicherort Ihrer Daten festlegen möchten, klicken Sie auf **Einstellungen konfigurieren**. Ressourcengruppen werden zum Steuern das Zugriffs auf die Daten verwendet. Wenn Sie über mehrere Apps verfügen, die einen Teil desselben Systems bilden, können Sie die dazugehörigen Application Insights-Daten in derselben Ressourcengruppe anordnen.

 Wählen Sie **Registrieren**. 

![Screenshot der Seite „App bei Application Insights registrieren“](./media/asp-net/0005-register-ed.png)

 Die Telemetriedaten werden an das [Azure-Portal](https://portal.azure.com) gesendet – sowohl während des Debuggens als auch nach dem Veröffentlichen Ihrer App.
> [!NOTE]
> Wenn beim Debuggen keine Telemetriedaten an das Verwaltungsportal gesendet werden sollen, fügen Sie Ihrer App nur das Application Insights SDK hinzu, konfigurieren Sie aber im Portal keine Ressource. Telemetriedaten werden beim Debuggen in Visual Studio angezeigt. Sie können später zu dieser Konfigurationsseite zurückkehren oder warten, bis Sie Ihre App bereitgestellt haben, und die [Telemetrie zur Laufzeit aktivieren](../../azure-monitor/app/monitor-performance-live-website-now.md).

## <a name="run"></a> Schritt 2: Führen Sie Ihre App aus.
Führen Sie Ihre App mit F5 aus. Öffnen Sie verschiedene Seiten, um Telemetriedaten zu generieren.

In Visual Studio wird die Anzahl von protokollierten Ereignissen angezeigt.

![Screenshot von Visual Studio Die Schaltfläche „Application Insights“ wird während des Debuggens angezeigt.](./media/asp-net/0006-Events.png)

## <a name="step-3-see-your-telemetry"></a>Schritt 3: Anzeigen der Telemetrie
Sie können Ihre Telemetriedaten entweder in Visual Studio oder im Application Insights-Webportal anzeigen. Suchen Sie nach Telemetriedaten in Visual Studio, und nutzen Sie sie beim Debuggen Ihrer App. Überwachen Sie Leistung und Verwendung im Web-Portal, wenn Ihr System aktiv ist. 

### <a name="see-your-telemetry-in-visual-studio"></a>Anzeigen Ihrer Telemetriedaten in Visual Studio

Gehen Sie wie folgt vor, um in Visual Studio Application Insights-Daten anzuzeigen.  Wählen Sie **Projektmappen-Explorer** > **Verbundene Dienste**, klicken Sie mit der rechten Maustaste auf **Application Insights**, und klicken Sie anschließend auf **Nach Livetelemetriedaten suchen**.

Im Application Insights-Suchfenster in Visual Studio werden die Telemetriedaten Ihrer Anwendung angezeigt, die auf der Serverseite Ihrer App generiert werden. Experimentieren Sie mit den Filtern, und klicken Sie auf ein beliebiges Ereignis, um weitere Details anzuzeigen.

![Screenshot der Ansicht mit den Daten aus der Debugsitzung im Application Insights-Fenster](./media/asp-net/55.png)

> [!Tip]
> Gehen Sie wie folgt vor, wenn keine Daten angezeigt werden: Stellen Sie sicher, dass der richtige Zeitbereich festgelegt ist, und klicken Sie auf das Suchsymbol.

[Erfahren Sie mehr zu Application Insights-Tools in Visual Studio](../../azure-monitor/app/visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Anzeigen von Telemetriedaten im Webportal

Sie können Telemetriedaten auch im Application Insights-Webportal anzeigen (sofern Sie nicht nur das SDK installiert haben). Im Portal stehen mehr Diagramme, Analysetools und komponentenübergreifende Ansichten zur Verfügung als in Visual Studio. Außerdem stellt das Portal Warnungen bereit.

Öffnen Sie die Application Insights-Ressource. Melden Sie sich entweder am [Azure-Portal](https://portal.azure.com/) an, und greifen Sie darin auf die Daten zu, oder wählen Sie **Projektmappen-Explorer** > **Verbundene Dienste**, klicken Sie mit der rechten Maustaste auf **Application Insights** > **Application Insights-Portal öffnen**, um darauf zuzugreifen.

Das Portal wird mit einer Ansicht der Telemetriedaten Ihrer App geöffnet.

![Screenshot der Application Insights-Übersichtsseite](./media/asp-net/66.png)

Klicken Sie im Portal auf eine beliebige Kachel oder auf ein beliebiges Diagramm, um weitere Details anzuzeigen.

[Erfahren Sie mehr über die Verwendung von Application Insights im Azure-Portal](../../azure-monitor/app/app-insights-dashboards.md).

## <a name="step-4-publish-your-app"></a>Schritt 4: Veröffentlichen der App
Veröffentlichen Sie Ihre App auf Ihrem IIS-Server oder in Azure. Sehen Sie sich [Live Metrics Stream](../../azure-monitor/app/metrics-explorer.md#live-metrics-stream) an, um sicherzustellen, dass alles reibungslos funktioniert.

Sie können dann verfolgen, wie Ihre Telemetriedaten im Application Insights-Portal erstellt werden. Darin können Sie Metriken überwachen, die Telemetriedaten durchsuchen und [Dashboards](../../azure-monitor/app/app-insights-dashboards.md) einrichten. Außerdem können Sie die leistungsfähige [Data Explorer-Abfragesprache](https://aka.ms/LogAnalyticsLanguage) verwenden, um die Nutzung und Leistung zu analysieren oder nach bestimmten Ereignissen zu suchen.

Sie können Ihre Telemetriedaten auch in [Visual Studio](../../azure-monitor/app/visual-studio.md) mit Tools wie der Diagnosesuche und [Trends](../../azure-monitor/app/visual-studio-trends.md) weiter analysieren.

> [!NOTE]
> Wenn Ihre App so viele Telemetriedaten sendet, dass die [Einschränkungsgrenzwerte](../../azure-monitor/app/pricing.md#limits-summary) bald erreicht werden, wird das automatische [Sampling](../../azure-monitor/app/sampling.md) aktiviert. Mit dem Sampling wird die Menge der von der App gesendeten Telemetriedaten reduziert, während gleichzeitig korrelierte Daten für Diagnosezwecke beibehalten werden.
>
>

## <a name="land"></a> Fertig

Glückwunsch! Sie haben in Ihrer App das Application Insights-Paket installiert und so konfiguriert, dass Telemetriedaten an den Application Insights-Dienst in Azure gesendet werden.

![Diagramm mit dem Weg der Telemetriedaten](./media/asp-net/01-scheme.png)

Die Azure-Ressource, die die Telemetriedaten Ihrer App erhält, wird durch einen *Instrumentierungsschlüssel* angegeben. Diesen Schlüssel finden Sie in der Datei „ApplicationInsights.config“.


## <a name="upgrade-to-future-sdk-versions"></a>Durchführen eines Upgrades auf zukünftige SDK-Versionen
Zur Durchführung eines Upgrades auf eine [neue Version des SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases) öffnen Sie den **NuGet-Paket-Manager** und filtern die Ansicht nach installierten Paketen. Wählen Sie **Microsoft.ApplicationInsights.Web** und dann **Upgrade** aus.

Wenn Sie Anpassungen an „ApplicationInsights.config“ vorgenommen haben, sollten Sie diese vor dem Upgrade speichern. Übernehmen Sie Ihre Änderungen anschließend für die neue Version.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nächste Schritte

Es gibt noch weitere Themen, die für Sie unter Umständen von Interesse sind:

* [Instrumentieren von Web-Apps zur Laufzeit mit Application Insights](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md)

### <a name="more-telemetry"></a>Mehr Telemetrie

* **[Browser- und Seitenladedaten](../../azure-monitor/app/javascript.md)**: Fügen Sie einen Codeausschnitt in Ihre Webseiten ein.
* **[Ausführlichere Abhängigkeits- und Ausnahmenüberwachung](../../azure-monitor/app/monitor-performance-live-website-now.md)**: Installieren Sie den Statusmonitor auf Ihrem Server.
* **[Programmieren benutzerdefinierter Ereignisse](../../azure-monitor/app/api-custom-events-metrics.md)**, um Benutzeraktionen zu zählen, zeitlich abzustimmen oder zu messen.
* **[Abrufen von Protokolldaten](../../azure-monitor/app/asp-net-trace-logs.md)**: Korrelieren Sie Protokolldaten mit Ihren Telemetriedaten.

### <a name="analysis"></a>Analyse

* **[Arbeiten mit Application Insights in Visual Studio](../../azure-monitor/app/visual-studio.md)**<br/>Enthält Informationen zum Debuggen per Telemetrie, zur Diagnosesuche und zum Drillthrough für Code.
* **[Navigation und Dashboards im Application Insights-Portal](../../azure-monitor/app/app-insights-dashboards.md)**<br/> Enthält Informationen zu Dashboards, leistungsstarken Diagnose- und Analysetools, Warnungen und zum Export von Telemetriedaten sowie eine Livekarte der Abhängigkeiten Ihrer Anwendung.
* **[Analytics](../../azure-monitor/log-query/get-started-portal.md)**: Die leistungsfähige Abfragesprache.

### <a name="alerts"></a>Alerts

* [Verfügbarkeitstests](../../azure-monitor/app/monitor-web-app-availability.md): Erstellen Sie Tests, um sicherzustellen, dass Ihre Website im Web sichtbar ist.
* [Intelligente Diagnose](../../azure-monitor/app/proactive-diagnostics.md): Diese Tests werden automatisch ausgeführt, sodass Sie keinerlei Einrichtungsschritte ausführen müssen. Sie werden darüber benachrichtigt, ob für Ihre App eine ungewöhnlich hohe Zahl von Anforderungen mit Fehlern vorliegt.
* [Metrikwarnungen](../../azure-monitor/app/alerts.md): Legen Sie Metrikwarnungen fest, damit Sie gewarnt werden, wenn für eine Metrik ein Schwellenwert überschritten wird. Sie können diese für benutzerdefinierte Metriken festlegen, die Sie in Ihrer App codieren.

### <a name="automation"></a>Automation

* [Automatisieren der Erstellung einer Application Insights-Ressource](../../azure-monitor/app/powershell.md)
