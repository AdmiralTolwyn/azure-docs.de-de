---
title: "Protokolle, Ausnahmen und benutzerdefinierte Diagnose für ASP.NET in Application Insights"
description: Diagnostizieren von Problemen in ASP.NET-Web-Apps durch Durchsuchen von Anforderungen, Ausnahmen und Protokollen, die durch Trace, NLog oder Log4Net  generiert wurden.
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 99860c53-0324-4a3a-9aa9-83f5dffba835
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/08/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 68d4b62b4915950dd18b6aa69b43043b4a90d9ff


---
# <a name="logs-exceptions-and-custom-diagnostics-for-aspnet-in-application-insights"></a>Protokolle, Ausnahmen und benutzerdefinierte Diagnose für ASP.NET in Application Insights
[Application Insights][start] enthält eine leistungsfähige [Diagnosesuche][diagnostic], mit der Sie vom Application Insights SDK Ihrer Anwendung gesendete Telemetriedaten suchen und eingehend untersuchen können. Viele Ereignisse wie z. B. Benutzerseitenansichten werden automatisch vom SDK gesendet.

Sie können auch Code zum Senden von benutzerdefinierten Ereignissen, Ausnahmeberichten und Ablaufverfolgungen schreiben. Wenn Sie bereits ein Protokollierungsframework, z. B. log4J, log4net, NLog oder System.Diagnostics.Trace verwenden, können Sie diese Protokolle erfassen und in die Suche aufnehmen. Dies erleichtert die Korrelation von Protokollablaufverfolgungen mit Benutzeraktionen, Ausnahmen und anderen Ereignissen.

## <a name="a-namesendabefore-you-write-custom-telemetry"></a><a name="send"></a>Vor dem Schreiben benutzerdefinierter Telemetrie
Wenn Sie [Application Insights noch nicht in Ihrem Projekt eingerichtet haben][start], holen Sie das jetzt nach.

Wenn Sie die Anwendung ausführen, sendet diese einige Telemetriedaten, die in der Diagnosesuche angezeigt werden, einschließlich vom Server empfangener Anforderungen, auf dem Client protokollierter Seitenansichten und nicht aufgefangener Ausnahmen.

Öffnen Sie die Diagnosesuche, um die Telemetriedaten anzuzeigen, die das SDK automatisch sendet.

![](./media/app-insights-search-diagnostic-logs/appinsights-45diagnostic.png)

![](./media/app-insights-search-diagnostic-logs/appinsights-31search.png)

Die Details unterscheiden sich je nach Anwendungstyp. Sie können auf jedes einzelne Ereignis klicken, um weitere Informationen zu erhalten.

## <a name="sampling"></a>Stichproben
Wenn Ihre Anwendung eine große Menge von Daten sendet und Sie das Application Insights-SDK für ASP.NET Version 2.0.0-beta3 oder höher verwenden, wird möglicherweise die adaptive Stichprobenerstellung verwendet, bei der nur ein bestimmter Prozentsatz der Telemetriedaten übermittelt wird. [Erfahren Sie mehr über das Erstellen von Stichproben.](app-insights-sampling.md)

## <a name="a-nameeventsacustom-events"></a><a name="events"></a>Benutzerdefinierte Ereignisse
Benutzerdefinierte Ereignisse werden sowohl in der [Diagnosesuche][diagnostic] als auch im [Metrik-Explorer][metrics] angezeigt. Diese Ereignisse können von Geräten, Webseiten und Serveranwendungen gesendet werden. Sie können für Diagnosezwecke und zum [Verstehen von Nutzungsmustern][track] verwendet werden.

Ein benutzerdefiniertes Ereignis hat einen Namen und kann auch Eigenschaften besitzen, nach denen Sie filtern können, sowie über numerische Messwerte verfügen.

JavaScript auf Client

    appInsights.trackEvent("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric measurements:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

C# auf Server

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);


VB auf Server

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)

### <a name="run-your-app-and-view-the-results"></a>Führen Sie Ihre App aus, und zeigen Sie die Ergebnisse an.
Öffnen Sie die Diagnosesuche.

Wählen Sie ein benutzerdefiniertes Ereignis und den Namen eines bestimmten Ereignisses aus.

![](./media/app-insights-search-diagnostic-logs/appinsights-332filterCustom.png)

Filtern Sie die Daten genauer, indem Sie einen Suchbegriff für einen Eigenschaftswert eingeben.  

![](./media/app-insights-search-diagnostic-logs/appinsights-23-customevents-5.png)

Zeigen Sie Detailinformationen zu den Eigenschaften eines einzelnen Ereignisses an.

![](./media/app-insights-search-diagnostic-logs/appinsights-23-customevents-4.png)

## <a name="a-namepagesa-page-views"></a><a name="pages"></a> Seitenansichten
Telemetriedaten für Seitenansichten werden vom trackPageView()-Aufruf in dem [JavaScript-Codeausschnitt gesendet, den Sie in Ihre Webseiten einfügen][usage]. Der Hauptzweck dieses Aufrufs ist es, die Anzahl der Seitenansichten zu erfassen, die Sie auf der Übersichtsseite sehen.

"trackPageView()" wird üblicherweise einmal in jeder HTML-Seite aufgerufen. Sie können jedoch weitere Aufrufe hinzufügen, wenn Sie z. B. bei einer App mit nur einer Seite eine neue Seite protokollieren möchten, sobald der Benutzer weitere Daten erhält.

    appInsights.trackPageView(pageSegmentName, "http://fabrikam.com/page.htm"); 

Manchmal ist es sinnvoll, Eigenschaften anzufügen, die Sie als Filter in der Diagnosesuche verwenden können:

    appInsights.trackPageView(pageSegmentName, "http://fabrikam.com/page.htm",
     {Game: currentGame.name, Difficulty: currentGame.difficulty});


## <a name="a-nametracea-trace-telemetry"></a><a name="trace"></a> Verfolgen von Telemetriedaten
Bei der Ablaufverfolgung von Telemetriedaten handelt es sich um Code, den Sie speziell zum Erstellen von Diagnoseprotokollen einfügen. 

Sie können beispielsweise Aufrufe wie die folgenden einfügen:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");


#### <a name="install-an-adapter-for-your-logging-framework"></a>Installieren eines Adapters für Ihr Protokollierungsframework
Sie können auch Protokolle durchsuchen, die mit einem Protokollierungsframework wie z. B. log4Net, NLog oder System.Diagnostics.Trace generiert wurden. 

1. Wenn Sie log4Net oder NLog verwenden möchten, installieren Sie es in Ihrem Projekt. 
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie dann **NuGet-Pakete verwalten**aus.
3. Klicken Sie auf „Online > Alle“, wählen Sie **Vorabversion einbeziehen** aus, und suchen Sie nach „Microsoft.ApplicationInsights“.
   
    ![Vorabversion des entsprechenden Adapters auswählen](./media/app-insights-search-diagnostic-logs/appinsights-36nuget.png)
4. Wählen Sie das entsprechende Paket aus:
   
   * Microsoft.ApplicationInsights.TraceListener (zum Erfassen von System.Diagnostics.Trace-Aufrufen)
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

Das NuGet-Paket installiert die erforderlichen Assemblys und ändert auch die Datei "web.config" oder "app.config".

#### <a name="a-namepepperainsert-diagnostic-log-calls"></a><a name="pepper"></a>Einfügen von Diagnoseprotokollaufrufen
Wenn Sie System.Diagnostics.Trace verwenden, wäre ein typischer Aufruf:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Wenn Sie log4net oder NLog bevorzugen:

    logger.Warn("Slow response - database01");

Führen Sie Ihre App im Debugmodus aus, oder stellen Sie sie bereit.

Sie sehen die Nachrichten in der Diagnosesuche, wenn Sie den Ablaufverfolgungsfilter auswählen.

### <a name="a-nameexceptionsaexceptions"></a><a name="exceptions"></a>Ausnahmen
Das Abrufen von Ausnahmeberichten in Application Insights bietet eine Reihe von Vorteilen, insbesondere, da Sie zwischen den Anforderungen mit Fehlern und den Ausnahmen navigieren und den Ausnahmestapel lesen können.

In einigen Fällen müssen Sie [einige Codezeilen einfügen][exceptions] , um sicherzustellen, dass die Ausnahmen automatisch aufgefangen werden.

Sie können auch expliziten Code zum Senden von Telemetriedaten für Ausnahmen schreiben:

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

Die Eigenschaften und Messparameter sind optional, sind aber hilfreich zum Filtern und Hinzufügen von zusätzlichen Informationen. Wenn Sie z. B. eine Anwendung haben, die mehrere Spiele ausführen kann, können Sie alle im Zusammenhang mit einem bestimmten Spiel stehenden Ausnahmeberichte ermitteln. Sie können jedem Wörterbuch beliebig viele Elemente hinzufügen.

#### <a name="viewing-exceptions"></a>Anzeigen von Ausnahmen
Auf dem Blatt "Übersicht" sehen Sie eine Zusammenfassung der gemeldeten Ausnahmen, und Sie können auf diese Ausnahmen klicken, um weitere Details anzuzeigen. Beispiel:

![](./media/app-insights-search-diagnostic-logs/appinsights-039-1exceptions.png)[]

Klicken Sie auf einen Ausnahmetyp, um die jeweiligen Vorkommen anzuzeigen:

![](./media/app-insights-search-diagnostic-logs/appinsights-333facets.png)[]

Sie können auch die Diagnosesuche direkt öffnen, nach Ausnahmen filtern und den Ausnahmetyp auswählen, den Sie anzeigen möchten.

### <a name="reporting-unhandled-exceptions"></a>Berichterstellung für Ausnahmefehler
Application Insights meldet nicht behandelte Ausnahmefehler – wann immer es möglich ist – von Geräten, [Webbrowsern][usage] oder Webservern, unabhängig davon, ob diese vom [Statusmonitor][redfield] oder dem [Application Insights SDK][greenbrown] ermittelt wurden. 

In einigen Fällen ist dies jedoch nicht möglich, da die Ausnahmen vom .NET Framework aufgefangen werden.  Um sicherzustellen, dass Ihnen alle Ausnahmen angezeigt werden, müssen Sie daher einen kleinen Ausnahmehandler schreiben. Welches Verfahren sich am besten eignet, variiert je nach Technologie. Weitere Informationen finden Sie unter [Ausnahmetelemetrie für ASP.NET][exceptions] . 

### <a name="correlating-with-a-build"></a>Korrelation mit einem Build
Wenn Sie Diagnoseprotokolle lesen, ist es wahrscheinlich, dass der Quellcode seit Bereitstellung des Livecodes geändert wurde.

Daher ist es sinnvoll, Buildinformationen wie beispielsweise die URL der aktuellen Version sowie alle zu verfolgenden Ausnahmen in eine Eigenschaft einzufügen. 

Anstatt diese Eigenschaft separat zu jedem Ausnahmeaufruf hinzuzufügen, können Sie die Informationen im Standardkontext festlegen. 

    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }

Gehen Sie im App-Initialisierer wie z. B. "Global.asax.cs" so vor:

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }

### <a name="a-namerequestsa-server-web-requests"></a><a name="requests"></a> Webanforderungen des Servers
Telemetriedaten zu Anforderungen werden automatisch gesendet, wenn Sie [den Statusmonitor auf Ihrem Webserver installieren][redfield] oder [Ihrem Webprojekt Application Insights hinzufügen][greenbrown]. Diese Daten werden ebenfalls in die Diagramme zu Anforderungs- und Antwortzeiten im Metrik-Explorer und auf der Übersichtsseite übertragen.

Wenn Sie zusätzliche Ereignisse senden möchten, können Sie die TrackRequest()-API verwenden.

## <a name="a-namequestionsaq--a"></a><a name="questions"></a>FRAGEN UND ANTWORTEN
### <a name="a-nameemptykeyai-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>Eine Fehlermeldung "Instrumentationsschlüssel darf nicht leer sein" wird angezeigt.
Anscheinend haben Sie das Protokollierungsadapter-Nuget-Paket installiert, ohne Application Insights zu installieren.

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf `ApplicationInsights.config`. Wählen Sie dann **Application Insights aktualisieren** aus. Ein Dialogfeld wird angezeigt, das Sie zur Anmeldung bei Azure und zum Erstellen einer Application Insights-Ressource oder dem Wiederverwenden einer vorhandenen Ressource einlädt. Damit sollte das Problem behoben sein.

### <a name="a-namelimitsahow-much-data-is-retained"></a><a name="limits"></a>Wie viele Daten werden beibehalten?
Bis zu 500 Ereignisse pro Sekunde für jede Anwendung. Ereignisse werden sieben Tage lang aufbewahrt.

### <a name="some-of-my-events-or-traces-dont-appear"></a>Einige mir wichtige Ereignisse oder Ablaufverfolgungen werden nicht angezeigt.
Wenn Ihre Anwendung eine große Menge von Daten sendet und Sie das Application Insights-SDK für ASP.NET Version 2.0.0-beta3 oder höher verwenden, wird möglicherweise die adaptive Stichprobenerstellung verwendet, bei der nur ein bestimmter Prozentsatz der Telemetriedaten übermittelt wird. [Erfahren Sie mehr über das Erstellen von Stichproben.](app-insights-sampling.md)

## <a name="a-nameaddanext-steps"></a><a name="add"></a>Nächste Schritte
* [Einrichten von Tests der Verfügbarkeit und Reaktionsfähigkeit][availability]
* [Problembehandlung][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md





<!--HONumber=Nov16_HO3-->


