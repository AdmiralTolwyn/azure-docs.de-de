---
title: Proaktive NRT-Diagnose in Application Insights | Microsoft Docs
description: Weist Sie auf ungewöhnliche Fehlermuster in Ihrer App hin und bietet Diagnoseanalysen. Es ist keine Konfiguration erforderlich.
services: application-insights
documentationcenter: ''
author: yorac
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: awills

---
# Proaktive Fehlerdiagnose
[Visual Studio Application Insights](app-insights-overview.md) benachrichtigt Sie automatisch und nahezu in Echtzeit, wenn es bei Ihrer Web-App zu einer ungewöhnlichen Zunahme von Fehlern kommt. Die Lösung erkennt eine ungewöhnliche Zunahme der Rate fehlerhafter HTTP-Anforderungen. Um Sie bei der Selektierung und Diagnose des Problems zu unterstützen, wird in der Benachrichtigung eine Analyse der Merkmale der Anforderungsfehler und der verknüpften Telemetriedaten angegeben. Außerdem werden Links zum Application Insights-Portal zur weiteren Diagnose bereitgestellt. Diese Funktion muss nicht eingerichtet oder konfiguriert werden, da sie Machine Learning-Algorithmen verwendet, um die normale Fehlerrate zu bestimmen.

Dieses Feature funktioniert mit Java- und ASP.NET-Web-Apps, die in der Cloud oder auf Ihren eigenen Servern gehostet werden. Es funktioniert auch mit allen Apps, die Daten zur Anforderungstelemetrie generieren, z. B. mit einer Workerrolle, mit der [TrackRequest()](app-insights-api-custom-events-metrics.md#track-request) aufgerufen wird.

Nachdem Sie [Application Insights für Ihr Projekt](app-insights-overview.md) eingerichtet haben und Ihre App eine bestimmte Mindestmenge von Telemetriedaten generiert hat, benötigt die proaktive Fehlerdiagnose 24 Stunden, um das normale Verhalten Ihrer App zu ermitteln, bevor sie aktiviert wird und Warnungen senden kann.

Hier sehen Sie eine Beispielwarnung:

![Beispiel für eine intelligente Warnung mit Clusteranalyse im Umfeld des Fehlers](./media/app-insights-proactive-failure-diagnostics/010.png)

> [!NOTE]
> Standardmäßig erhalten Sie eine kürzere E-Mail als in diesem Beispiel. Sie können jedoch [zu diesem Detailformat wechseln](#configure-alerts).
> 
> 

Sie enthält folgende Angaben:

* Die Fehlerrate im Vergleich zum normalen App-Verhalten
* Anzahl der betroffenen Benutzer – so wissen Sie, wie ernst die Lage ist.
* Ein charakteristisches Muster für die Fehler. In diesem Beispiel gibt es einen bestimmten Antwortcode, einen Anforderungsnamen (Vorgang) und eine App-Version. So wissen Sie sofort, wo im Code Sie suchen müssen. Weitere Möglichkeiten sind beispielsweise ein bestimmter Browser oder Clientbetriebssystem.
* Die Ausnahme, Protokollablaufverfolgungen und Abhängigkeitsfehler (Datenbanken oder andere externe Komponenten), die den charakterisierten Anforderungsfehlern zugeordnet zu sein scheinen.
* Direkte Links zu relevanten Suchvorgängen in den Telemetriedaten in Application Insights.

## Vorteile von proaktiven Warnungen
Normale [Metrikwarnungen](app-insights-alerts.md) informieren Sie, dass möglicherweise ein Problem vorliegt. Bei der proaktiven Fehlerdiagnose wird die Diagnose dagegen für Sie gestartet, und es wird Ihnen ein großer Teil der Analyse abgenommen, der ansonsten manuell ausgeführt werden müsste. Sie erhalten die Ergebnisse fein säuberlich verpackt und können so schnell zur Ursache des Problems vordringen.

## So funktioniert's
Bei der proaktiven NRT-Diagnose (Near Real Time) werden die von Ihrer App erhaltenen Telemetriedaten und insbesondere die Rate von Anforderungsfehlern überwacht. Diese Metrik zählt die Anzahl der Anforderungen, für die die `Successful request`-Eigenschaft FALSE ist. Standardmäßig gilt `Successful request== (resultCode < 400)` (es sei denn, Sie haben benutzerdefinierten Code geschrieben, um Ihre eigenen [TrackRequest](app-insights-api-custom-events-metrics.md#track-request)-Aufrufe zu [filtern](app-insights-api-filtering-sampling.md#filtering) oder zu erstellen).

Die Leistung Ihrer App weist ein typisches Verhaltensmuster auf. Einige Anforderungen sind anfälliger für Fehler als andere, und die gesamte Fehlerrate kann mit zunehmender Auslastung steigen. Bei der proaktiven Fehlerdiagnose werden diese Anomalien mithilfe von Machine Learning ermittelt.

Wenn Telemetriedaten aus Ihrer Web-App bei Application Insights eingehen, vergleicht die proaktive Fehlerdiagnose das aktuelle Verhalten mit den Mustern der letzten Tage. Wenn ein ungewöhnlicher Anstieg der Fehlerrate im Vergleich zur vorherigen Leistung beobachtet wird, wird eine Analyse ausgelöst.

Wenn eine Analyse ausgelöst wird, führt der Dienst eine Clusteranalyse für die fehlgeschlagene Anforderung aus, um in den Werten ein Muster zu ermitteln, durch die sich die Fehler charakterisieren lassen. Im obigen Beispiel hat die Analyse ermittelt, dass die meisten Fehler zu einem bestimmten Ergebniscode, Anforderungsnamen, Server-URL-Host und einer Rolleninstanz gehören. Im Gegensatz dazu hat die Analyse ermittelt, dass die Clientbetriebssystem-Eigenschaft über mehrere Werte verteilt wird und daher nicht aufgeführt wird.

Wenn Ihr Dienst mit diesen Telemetriedaten instruiert wurde, ermittelt der Analyzer eine Ausnahme und einen Abhängigkeitsfehler, die Anforderungen im identifizierten Cluster zuzuordnen sind, sowie ein Beispiel für Ablaufverfolgungsprotokolle, die diesen Anforderungen zugeordnet sind.

Das Analyseergebnis wird Ihnen als Warnung gesendet, sofern Sie nichts anderes konfiguriert haben.

Wie bei [manuell festgelegten Warnungen](app-insights-alerts.md) können Sie den Status der Warnung prüfen und die Warnung auf dem Blatt „Warnungen“ Ihrer Application Insights-Ressource konfigurieren. Im Gegensatz zu anderen Warnungen müssen Sie die proaktive Fehlerdiagnose jedoch nicht einrichten oder konfigurieren. Wenn Sie möchten, können Sie sie deaktivieren oder die Ziel-E-Mail-Adressen ändern.

## Konfigurieren von Warnungen
Sie können die proaktive Diagnose deaktivieren, die E-Mail-Empfänger ändern, einen Webhook erstellen oder ausführlichere Warnmeldungen festlegen.

Öffnen Sie die Seite „Warnungen“. Die proaktive Diagnose wird zusammen mit allen Warnungen aufgeführt, die Sie manuell festgelegt haben, und Sie können sehen, ob sie sich derzeit im Status „Warnung“ befindet.

![Klicken Sie auf der Seite „Übersicht“ auf die Kachel „Warnungen“. Klicken Sie alternativ dazu auf einer beliebigen Seite „Metriken“ auf die Schaltfläche „Warnungen“.](./media/app-insights-proactive-failure-diagnostics/021.png)

Klicken Sie auf die Warnung, um sie zu konfigurieren.

![Konfiguration](./media/app-insights-proactive-failure-diagnostics/031.png)

Beachten Sie, dass Sie die proaktive Diagnose deaktivieren, aber nicht löschen (oder eine neue erstellen) können.

#### Detaillierte Warnungen
Bei Auswahl von "Detaillierte Analysen erhalten" enthält die E-Mail weitere Diagnoseinformationen. Manchmal werden Sie das Problem allein anhand der Daten in der E-Mail diagnostizieren können.

Es gibt ein geringes Risiko, dass die ausführlichere Warnung vertrauliche Informationen enthält, weil sie Ausnahme- und Ablaufverfolgungsmeldungen umfasst. Dies geschieht jedoch nur, wenn Ihr Code vertrauliche Informationen in diesen Nachrichten zulassen könnte.

## Selektierung und Diagnose einer Warnung
Eine Warnung gibt an, dass ein ungewöhnlicher Anstieg bei der Rate der Anforderungsfehler erkannt wurde. Wahrscheinlich liegt ein Problem mit Ihrer App oder deren Umgebung vor.

Anhand des Prozentsatzes der Anforderungen und der Anzahl der betroffenen Benutzer können Sie entscheiden, wie dringend das Problem ist. Im Beispiel oben zeigt die Fehlerrate von 22,5 %, die einer normalen Fehlerrate von 1 % gegenübersteht, dass ganz offensichtlich Probleme vorliegen. Auf der anderen Seite sind nur 11 Benutzer betroffen. Wenn es sich dabei um Ihre App handeln würde, könnten Sie beurteilen, wie schwerwiegend die Fehler sind.

In vielen Fällen können Sie das Problem über den Anforderungsnamen, die Ausnahme, den Abhängigkeitsfehler und die Ablaufverfolgungsdaten schnell diagnostizieren.

Es gibt einige andere Anhaltspunkte. Beispielsweise entspricht die Abhängigkeitsfehlerrate in diesem Beispiel der Ausnahmerate (89,3 %). Dies legt nahe, dass die Ausnahme sich direkt aus dem Abhängigkeitsfehler ergibt, sodass Sie genau wissen, wo im Code Sie suchen müssen.

Über die Links in den einzelnen Abschnitten gelangen Sie direkt zu einer [Suchseite](app-insights-diagnostic-search.md), die nach den entsprechenden Anforderungen, Ausnahmen, Abhängigkeiten oder Ablaufverfolgungen gefiltert ist. Dort können Sie weitere Nachforschungen anstellen. Alternativ können Sie das [Azure-Portal](https://portal.azure.com) öffnen, zur Application Insights-Ressource für Ihre App wechseln und das Blatt „Fehler“ öffnen.

In diesem Beispiel wird durch Klicken auf „View dependency failures details“ (Details von Abhängigkeitsfehlern anzeigen) das Application Insights-Blatt „Suchen“ für die SQL-Anweisung mit der zugrunde liegenden Ursache geöffnet: In Pflichtfeldern wurden NULL-Werte angegeben, und die Überprüfung während des Speichervorgangs ist negativ ausgefallen.

![Diagnosesuche](./media/app-insights-proactive-failure-diagnostics/051.png)

## Überprüfen aktueller Warnungen
Um Warnungen im Portal zu überprüfen, öffnen Sie **Einstellungen, Überwachungsprotokolle**.

![Zusammenfassung von Warnungen](./media/app-insights-proactive-failure-diagnostics/041.png)

Klicken Sie auf eine Warnung, um vollständige Details anzuzeigen.

Klicken Sie alternativ auf **Proaktive Erkennung**, um direkt zur letzten Warnung zu wechseln:

![Zusammenfassung von Warnungen](./media/app-insights-proactive-failure-diagnostics/070.png)

## Wo liegt der Unterschied?
Die proaktive Fehlerdiagnose ergänzt andere ähnliche Features von Application Insights.

* [Metrikwarnungen](app-insights-alerts.md) werden von Ihnen festgelegt und können eine Vielzahl von Metriken überwachen, z.B. die CPU-Belegung, Anforderungsraten, Seitenladezeiten usw. Sie können sie z. B. einsetzen, um rechtzeitig benachrichtigt zu werden, wenn weitere Ressourcen hinzugefügt werden müssen. Im Gegensatz dazu deckt die proaktive Fehlerdiagnose einen kleinen Bereich kritischer Metriken ab (zurzeit nur die Rate von Anforderungsfehlern), durch die Sie nahezu in Echtzeit benachrichtigt werden, sobald die Rate der Anforderungsfehler für Ihre Web-App im Vergleich zum normalen Verhalten der Web-App drastisch ansteigt.
  
    Bei der proaktiven Fehlerdiagnose wird der Schwellenwert automatisch an die aktuellen Bedingungen angepasst.
  
    Mit der proaktiven Fehlerdiagnose wird die Diagnose für Sie gestartet.
* Die [Proaktive Anomaliediagnose](app-insights-proactive-anomaly-diagnostics.md) verwendet zudem intelligente Funktionen, um ungewöhnliche Muster in Ihren Metriken zu ermitteln, und muss nicht von Ihnen konfiguriert werden. Im Gegensatz zur proaktiven Fehlerdiagnose besteht der Zweck der proaktiven Anomaliediagnose jedoch in der Ermittlung von Nutzungssegmenten, die beispielsweise durch bestimmte Seiten in einem bestimmten Browsertyp nicht zufriedenstellend verarbeitet werden. Die Analyse wird täglich ausgeführt, und falls ein Ergebnis gefunden wird, ist dies wahrscheinlich wesentlich weniger dringend als eine Warnung. Im Gegensatz dazu wird die Analyse für die proaktive Fehlerdiagnose kontinuierlich für eingehende Telemetriedaten ausgeführt, und Sie werden innerhalb weniger Minuten benachrichtigt, wenn die Serverfehlerraten höher ausfallen als erwartet.

## Bei einer Warnung durch die proaktive Fehlerdiagnose
*Warum habe ich diese Warnung erhalten?*

* Im Vergleich zu den normalen Grundwerten des vorhergehenden Zeitraums wurde ein ungewöhnlicher Anstieg der Anforderungsfehlerrate festgestellt. Nach der Analyse der Fehler und zugeordneten Telemetriedaten ist davon auszugehen, dass ein Problem vorliegt, das Sie untersuchen sollten.

*Bedeutet die Benachrichtigung, dass ich definitiv ein Problem habe?*

* Wir versuchen, bei Störungen oder Beeinträchtigungen der App eine Warnung zu senden. Nur Sie können jedoch die Semantik oder die Auswirkungen auf die App oder Benutzer vollständig einschätzen.

*Meine Daten werden also angesehen?*

* Nein. Der Dienst ist vollständig automatisch. Nur Sie erhalten die Benachrichtigungen. Ihre Daten sind [privat](app-insights-data-retention-privacy.md).

*Muss ich diese Benachrichtigung abonnieren?*

* Nein. Diese Warnungsregel ist für jede Anwendung festgelegt, die Anforderungstelemetriedaten sendet.

*Kann ich das Abonnement abbestellen oder die Benachrichtigungen stattdessen an meine Kollegen senden lassen?*

* Ja, klicken Sie unter „Warnungsregeln“ auf die Regel für die proaktive Diagnose, um sie zu konfigurieren. Sie können die Warnung deaktivieren oder die Empfänger für die Warnung ändern.

*Ich habe die E-Mail verloren. Wo finde ich die Benachrichtigungen im Portal?*

* In den Überwachungsprotokollen. Klicken Sie auf „Einstellungen“, „Überwachungsprotokolle“ und dann auf eine beliebige Warnung, um zu sehen, wo sich die Alarme befinden, jedoch mit eingeschränkter Detailansicht.

*Einige Warnungen betreffen bekannte Probleme. Diese Warnungen möchte ich nicht erhalten.*

* Wir arbeiten an einer Funktion zur Warnungsunterdrückung.

## Nächste Schritte
Mit den folgenden Diagnosetools können Sie die Telemetrie Ihrer App untersuchen:

* [Metrik-Explorer](app-insights-metrics-explorer.md)
* [Suchexplorer](app-insights-diagnostic-search.md)
* [Analytics: Leistungsfähige Abfragesprache](app-insights-analytics-tour.md)

Proaktive Erkennungen sind vollkommen automatisch. Vielleicht möchten Sie aber weitere Warnungen einrichten?

* [Einrichten von Warnungen in Application Insights](app-insights-alerts.md)
* [Verfügbarkeitswebtests](app-insights-monitor-web-app-availability.md)

<!---HONumber=AcomDC_0907_2016-->