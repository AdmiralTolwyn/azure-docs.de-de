---
title: Schnellstart mit Azure Application Insights | Microsoft-Dokumentation
description: Dieser Artikel bietet Informationen zum schnellen Einrichten einer Node.js-Web-App für die Überwachung mit Application Insights.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/11/2018
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 8bc725a5d9e3e9cdf82a01693aed83bff1f16c04
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991627"
---
# <a name="start-monitoring-your-nodejs-web-application"></a>Starten der Überwachung Ihrer Node.js-Webanwendung

Mit Azure Application Insights können Sie die Verfügbarkeit, Leistung und Nutzung Ihrer Webanwendung ganz einfach überwachen. Sie können auch Fehler in Ihrer Anwendung schnell erkennen und diagnostizieren, ohne darauf warten zu müssen, dass diese Fehler von Benutzern gemeldet werden. Ab der SDK-Version 0.20 können Sie allgemeine Drittanbieterpakete einschließlich MongoDB, MySQL und Redis überwachen.

Diese Schnellstartanleitung führt Sie durch die notwendigen Schritte, um das Application Insights SDK für Node.js, Version 0.22, zu einer vorhandenen Node.js-Webanwendung hinzuzufügen.

## <a name="prerequisites"></a>Voraussetzungen

So führen Sie diesen Schnellstart durch:

- Sie benötigen ein Azure-Abonnement und eine vorhandene Node.js-Webanwendung.

Wenn Sie noch nicht über eine Node.js-Webanwendung verfügen, können Sie mithilfe der Schnellstartanleitung unter [Erstellen einer Node.js-Web-App](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs) eine erstellen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="log-in-to-the-azure-portal"></a>Anmelden beim Azure-Portal

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.

## <a name="enable-application-insights"></a>Aktivieren von Application Insights

Application Insights kann Telemetriedaten von jeder mit dem Internet verbundenen Anwendung erfassen, unabhängig davon, ob die Anwendung lokal oder in der Cloud ausgeführt wird. Geben Sie folgendermaßen vor, um diese Daten anzuzeigen.

1. Klicken Sie auf **Ressource erstellen** > **Überwachung + Verwaltung** > **Application Insights**.

   ![Hinzufügen einer Application Insights-Ressource](./media/app-insights-nodejs-quick-start/001-u.png)

   Ein Dialogfeld für die Konfiguration wird geöffnet. Füllen Sie die Eingabefelder anhand der Informationen in der folgenden Tabelle aus:

    | Einstellungen        | Wert           | BESCHREIBUNG  |
   | ------------- |:-------------|:-----|
   | **Name**      | Global eindeutiger Wert | Der Name, der die zu überwachende App identifiziert. |
   | **Anwendungstyp** | Node.js-Anwendung | Der Typ der zu überwachenden App. |
   | **Ressourcengruppe**     | myResourceGroup      | Der Name der neuen Ressourcengruppe, die Application Insights-Daten hosten soll. |
   | **Location** | USA (Ost) | Wählen Sie einen Standort in Ihrer Nähe oder in der Nähe des Standorts, in dem Ihre App gehostet wird. |

2. Klicken Sie auf **Create**.

## <a name="configure-app-insights-sdk"></a>Konfigurieren des Application Insights SDK

1. Wählen Sie **Übersicht** > **Grundlagen** aus, und kopieren Sie den**Instrumentierungsschlüssel** Ihrer Anwendung.

   ![Formular für neue App Insights-Ressource](./media/app-insights-nodejs-quick-start/instrumentation-key-001.png)

2. Fügen Sie das Application Insights SDK für Node.js zu Ihrer Anwendung hinzu. Führen Sie folgenden Befehl im Stammverzeichnis Ihrer App aus:

   ```bash
   npm install applicationinsights --save
   ```

3. Bearbeiten Sie die erste JS-Datei Ihrer App, und fügen Sie dem obersten Teil Ihres Skripts die beiden unten stehenden Zeilen hinzu. Wenn Sie die [Node.js-Schnellstart-App](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs) verwenden, bearbeiten Sie die Datei „index.js“. Ersetzen Sie &lt;instrumentation_key&gt; durch den Instrumentierungsschlüssel Ihrer Anwendung. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Starten Sie die App neu.

> [!NOTE]
> Es dauert ca. 3-5 Minuten, bis die ersten Daten im Portal angezeigt werden. Wenn es sich um eine Test-App mit geringem Datenverkehr handelt, denken Sie daran, dass die meisten Metriken nur erfasst werden, wenn aktive Anforderungen oder Vorgänge vorhanden sind.

## <a name="start-monitoring-in-the-azure-portal"></a>Starten der Überwachung im Azure-Portal

1. Sie können jetzt im Azure-Portal erneut die Seite **Übersicht** für Application Insights öffnen, von der Sie den Instrumentierungsschlüssel abgerufen haben, um Details zu Ihrer aktuell ausgeführten Anwendung anzuzeigen.

   ![Application Insights – Menü „Übersicht“](./media/app-insights-nodejs-quick-start/overview-001.png)

2. Klicken Sie auf **App-Zuordnung**, um ein visuelles Layout der Abhängigkeitsbeziehungen zwischen den Komponenten Ihrer Anwendung zu erhalten. Jede Komponente zeigt KPIs wie z.B. Last, Leistung, Fehler und Warnungen an.

   ![Anwendungszuordnung](./media/app-insights-nodejs-quick-start/application-map.png)

3. Klicken Sie auf die Schaltfläche für die **App-Analyse** ![Schaltfläche „Anwendungszuordnung“](./media/app-insights-nodejs-quick-start/006.png).  Dadurch wird die **Application Insights-Analyse** geöffnet, die eine erweiterte Abfragesprache zum Analysieren aller Daten bereitstellt, die von Application Insights gesammelt werden. In diesem Fall wird eine Abfrage für Sie generiert, die die Anzahl von Anforderungen als Diagramm darstellt. Sie können selbst Abfragen zum Analysieren anderer Daten schreiben.

   ![Analysediagramm der Benutzeranforderungen in einem bestimmten Zeitraum](./media/app-insights-nodejs-quick-start/007-Black.png)

4. Kehren Sie zur Seite **Übersicht** zurück, und untersuchen Sie die KPI-Graphen.  Dieses Dashboard zeigt Statistiken zur Integrität Ihrer Anwendung, einschließlich der Anzahl von eingehenden Anforderungen, der Dauer dieser Anforderungen und aller auftretenden Fehler. 

   ![Diagramm der Übersichtszeitachse für die Integrität](./media/app-insights-nodejs-quick-start/overview-perf.png)

   Damit das Diagramm **Ladezeit der Seitenansicht** mit **clientseitigen Telemetriedaten** aufgefüllt werden kann, fügen Sie dieses Skript zu jeder Seite hinzu, die Sie nachverfolgen möchten:

   ```HTML
   <!-- 
   To collect user behavior analytics tools about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
       function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
       }({
           instrumentationKey:"<insert instrumentation key>"
       });
       
       window.appInsights=appInsights;
       appInsights.trackPageView();
   </script>
   ```

5. Klicken Sie unter der Überschrift **Untersuchen** auf **Browser**. Hier finden Sie Metriken in Zusammenhang mit der Leistung der Seiten Ihrer App. Sie können auf **Neues Diagramm hinzufügen** klicken, um zusätzliche benutzerdefinierte Ansichten zu erstellen, oder auf **Bearbeiten** klicken, um Typ, Höhe, Farbpalette, Gruppierungen und Metriken der vorhandenen Diagramme zu ändern.

   ![Servermetriken – Diagramm](./media/app-insights-nodejs-quick-start/009-Black.png)

Weitere Informationen zum Überwachen von Node.js finden Sie in der [weiteren Dokumentation zu Application Insights und Node.js](app-insights-nodejs.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie planen, die nachfolgenden Schnellstartanleitungen oder Tutorials durchzuarbeiten, sollten Sie die in dieser Schnellstartanleitung erstellten Ressourcen nicht bereinigen. Falls Sie nicht fortfahren möchten, können Sie die folgenden Schritte ausführen, um alle erstellten Ressourcen dieses Schnellstarts im Azure-Portal zu löschen.

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Ressourcengruppen** und dann auf **myResourceGroup**.
2. Klicken Sie auf der Seite mit Ihrer Ressourcengruppe auf **Löschen**, geben Sie im Textfeld **myResourceGroup** ein, und klicken Sie dann auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Suchen nach und Diagnostizieren von Leistungsproblemen](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
