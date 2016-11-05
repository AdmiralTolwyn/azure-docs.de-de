---
title: Verwalten von Preisen und Kontingenten für Application Insights
description: Wählen Sie den benötigten Preisplan aus
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/27/2016
ms.author: awills

---
# Verwalten von Preisen und Kontingenten für Application Insights
*Application Insights befindet sich in der Vorschau.*

[Preise][pricing] für [Visual Studio Application Insights][start] basieren auf dem Datenvolumen pro Anwendung. Es gibt einen grundlegenden Free-Tarif, in dem Sie mit wenigen Einschränkungen die meisten Features nutzen können.

Jede Application Insights-Ressource wird als separater Dienst abgerechnet und auf der Rechnung für Ihr Azure-Abonnement aufgeführt.

[Informationen hierzu finden Sie in der Preisübersicht.][pricing]

## Anzeigen der Kontingente und des Preisplans für Ihre Application Insights-Ressource
Sie können das Blatt zu Kontingenten und Preisen über die Ressourceneinstellungen Ihrer Anwendung öffnen.

![Wählen Sie „Einstellungen“ > „Kontingent und Preise“.](./media/app-insights-pricing/01-pricing.png)

Die Wahl des Tarifs wirkt sich auf Folgendes aus:

* [Monatliches Kontingent](#monthly-quota): Die Menge der Telemetriedaten, die Sie jeden Monat analysieren können.
* [Datenrate](#data-rate): Die maximale Rate, mit der Daten aus Ihrer App verarbeitet werden können.
* [Fortlaufender Export](#continuous-export) – Die Möglichkeit des Exports von Daten in andere Tools und Dienste.

Diese Grenzwerte werden für jede Application Insights-Ressource separat festgelegt.

### Kostenlose Premium-Testversion
Wenn Sie erstmals eine neue Application Insights-Ressource erstellen, wird diese im Free-Tarif gestartet.

Sie können jederzeit zur kostenlosen 30-Tage-Testversion des Premium-Tarifs wechseln. Somit erhalten Sie die Vorteile des Premium-Tarifs. Nach 30 Tagen wird dieser automatisch auf den Tarif zurückgesetzt, den Sie zuvor verwendet haben, sofern Sie nicht explizit einen anderen Tarif auswählen. Wählen Sie den gewünschten Tarif zu einem beliebigen Zeitpunkt während des Testzeitraums aus. Sie erhalten weiterhin die kostenlose Testversion bis Ablauf des Zeitraums von 30 Tagen.

## Monatliches Kontingent
* In jedem Kalendermonat kann Ihre Anwendung maximal eine bestimmte Menge an Telemetriedaten an Application Insights senden. Derzeit liegt das Kontingent für den kostenlosen Tarif (Tarif „Free“) bei 5 Mio. Datenpunkten pro Monat. Bei den anderen Tarifen ist es deutlich höher. Ist das Kontingent erreicht, können Sie weitere dazukaufen. Die genauen Zahlen können Sie der [Preisübersicht][pricing] entnehmen.
* Das Kontingent hängt von dem Tarif ab, den Sie ausgewählt haben.
* Das Kontingent beginnt um Mitternacht UTC am ersten Tag jedes Monats.
* Das Datenpunktediagramm zeigt, wie viel von Ihrem Kontingent in diesem Monat verwendet wurde.
* Das Kontingent wird in *Datenpunkten* gemessen. Ein einzelner Datenpunkt ist ein Aufruf einer der Nachverfolgungsmethoden, unabhängig davon, ob sie explizit in Ihrem Code oder durch eines der Standardtelemetriemodule aufgerufen wird. Er kann mehrere angefügte Eigenschaften und Metriken besitzen.
* Datenpunkte werden generiert:
  * Über [SDK-Module](app-insights-configuration-with-applicationinsights-config.md), die automatisch Daten sammeln, beispielsweise zum Melden einer Anforderung oder eines Absturzes oder zum Messen der Leistung.
  * Über von Ihnen geschriebene [API](app-insights-api-custom-events-metrics.md)-`Track...`-Aufrufe, z. B. `TrackEvent` oder `trackPageView`.
  * Über [Verfügbarkeitswebtests](app-insights-monitor-web-app-availability.md), die Sie eingerichtet haben.
* Beim Debuggen können Sie die Datenpunkte sehen, die von Ihrer App im Visual Studio-Ausgabefenster gesendet werden. Client-Ereignisse können durch Öffnen der Registerkarte „Netzwerk“ im Debuggingbereich Ihres Browsers angezeigt werden (in der Regel mit F12).
* *Sitzungsdaten* werden im Kontingent nicht berücksichtigt. Hierzu zählen die Anzahl von Benutzern oder Sitzungen sowie Umgebungs- und Gerätedaten.
* Wenn Sie die Datenpunkte durch Überprüfung zählen möchten, sind diese an verschiedenen Orten zu finden:
  * Jedes in der [Diagnosesuche](app-insights-diagnostic-search.md) angezeigte Element, einschließlich HTTP-Anforderungen, Ausnahmen, Protokollablaufverfolgungen, Seitenaufrufe, Abhängigkeitsereignisse und benutzerdefinierte Ereignisse.
  * Jede Rohmessung einer [Metrik](app-insights-metrics-explorer.md), etwa eines Leistungsindikators. (Die Punkte, die Sie in den Diagrammen sehen, sind normalerweise Aggregate von mehreren Rohdatenpunkten).
  * Jeder Punkt auf einem Web-Verfügbarkeitsdiagramm ist auch ein Aggregat von mehreren Datenpunkten.
* Sie können auch einzelne Datenpunkte in der Quelle während des Debuggens überprüfen:
  * Wenn Sie Ihre App im Debugmodus in Visual Studio ausführen, werden die Datenpunkte im Ausgabefenster protokolliert.
  * Um Clientdatenpunkte anzuzeigen, öffnen Sie den Debuggingbereich Ihres Browserfensters (in der Regel über F12) und die Registerkarte „Netzwerk“.
* Die Datenrate wird möglicherweise (standardmäßig) durch die [adaptive Stichprobenerstellung](app-insights-sampling.md) reduziert. Dies bedeutet, dass bei steigender Verwendung Ihrer App die Telemetrierate nicht in dem von Ihnen erwarteten Maß ansteigt.

### Überschreitung
Wenn die Anwendung mehr als das monatliche Kontingent sendet, haben Sie folgende Möglichkeiten:

* Sie bezahlen für zusätzliche Daten. Informationen hierzu finden Sie in der [Preisübersicht][pricing]. Sie können diese Option im Voraus auswählen. Im Free-Tarif ist diese Option nicht verfügbar.
* Sie führen ein Upgrade für Ihren Tarif aus.
* Sie unternehmen nichts. Sitzungsdaten weiterhin aufgezeichnet, aber andere Daten werden in der Diagnosesuche oder im Metrik-Explorer nicht aufgeführt.

## Wie viele Daten sende ich?
Das Diagramm im unteren Bereich des Blatts mit der Preisübersicht zeigt das Datenpunktvolumen Ihrer Anwendung gruppiert nach Datenpunkttypen an. (Sie können dieses Diagramm auch im Metrik-Explorer erstellen.)

![Im unteren Bereich des Blatts mit der Preisübersicht](./media/app-insights-pricing/03-allocation.png)

Klicken Sie auf das Diagramm, um weitere Details einzublenden, oder ziehen Sie den Mauszeiger darüber, und klicken Sie auf „(+)“, um einen Zeitraum im Detail anzuzeigen.

Das Diagramm zeigt die Menge der Daten, die der Application Insights-Dienst nach der [Stichprobenerstellung](app-insights-sampling.md) empfängt.

Wenn das Datenvolumen Ihr monatliches Kontingent erreicht, wird eine Anmerkung im Diagramm angezeigt.

## Datenrate
Zusätzlich zum monatlichen Kontingent gibt es Begrenzungen der Datenrate. Beim Tarif [Free][pricing] liegt die Grenze bei 200 Datenpunkten pro Sekunde, gemittelt über 5 Minuten. Bei kostenpflichtigen Tarifen ist der Grenzwert 500/s, gemittelt über eine Minute.

Es gibt drei Buckets, die getrennt gezählt werden:

* [TrackTrace-Aufrufe](app-insights-api-custom-events-metrics.md#track-trace) und [Erfasste Protokolle](app-insights-asp-net-trace-logs.md)
* [Ausnahmen](app-insights-api-custom-events-metrics.md#track-exception), begrenzt auf 50 Punkte/s
* Alle anderen Telemetriedaten (Seitenaufrufe, Sitzungen, Anforderungen, Abhängigkeiten, Metriken, benutzerdefinierte Ereignisse).

*Was geschieht, wenn meine App, das Pro-Sekunde-Volumen überschreitet?*

* Die Datenmenge, die Ihre App sendet, wird minütlich gemessen. Wenn sie die pro Sekunde, über eine Minute gemittelt Datenmenge überschreitet, lehnt der Server einige Anforderungen ab. Einige Versionen des SDK versuchen in diesem Fall, die Daten erneut zu senden, sodass über Minuten hinweg die vielfache Menge gesendet wird; andere wie beispielsweise JavaScript SDK löschen die abgelehnten Daten einfach.

Tritt eine Drosselung auf, erhalten Sie zur Warnung eine Benachrichtigung über diesen Vorgang.

*Woher weiß ich, wie viele Datenpunkte meine App sendet?*

* Öffnen Sie „Einstellungen/Kontingent und Preise“, um das Diagramm mit dem Datenvolumen anzuzeigen.
* Oder fügen Sie im Metrik-Explorer ein neues Diagramm hinzu, und wählen Sie **Datenpunktvolumen** als Metrik aus. Aktivieren Sie "Gruppierung", und gruppieren Sie nach **Datentyp**.

## So verringern Sie die Datenrate
Wenn Begrenzungsdrosselungen auftreten, können Sie verschiedene Schritte ausführen:

* Verwenden Sie [Stichproben](app-insights-sampling.md). Diese Technologie verringert die Datenrate, ohne die Metriken zu verzerren und ohne die Navigation zwischen verwandten Elementen bei der Suche zu stören.
* [Begrenzen Sie die Anzahl der gemeldeten AJAX-Aufrufe](app-insights-javascript.md#detailed-configuration) für jeden Seitenaufruf, oder deaktivieren Sie AJAX-Berichte.
* Deaktivieren Sie nicht benötigte Erfassungsmodule durch [Bearbeiten von „ApplicationInsights.config“](app-insights-configuration-with-applicationinsights-config.md). Das kann z. B. für Leistungsindikator- oder Abhängigkeitsdaten gelten.
* Aggregieren Sie Metriken vorab. Wenn Sie Ihrer App Aufrufe an TrackMetric eingefügt haben, können Sie Datenverkehr reduzieren, indem Sie die Überladung verwenden, die Ihre Berechnung des Durchschnitts und die Standardabweichung eines Batches von Messungen akzeptiert. Oder Sie können ein [vorab aggregierendes Paket](https://www.myget.org/gallery/applicationinsights-sdk-labs) verwenden.

## Stichproben
Die [Stichprobenerstellung](app-insights-sampling.md) ist eine Methode, die Rate, mit der Telemetriedaten an Ihre App gesendet werden, zu verringern. Gleichzeitig soll die Möglichkeit erhalten bleiben, bei Diagnosesuchläufen relevante Ereignisse zu ermitteln und korrekte Ereigniszahlen zu erhalten.

Die Stichprobenerstellung ist eine effektive Möglichkeit, die Gebühren zu senken und innerhalb Ihres monatlichen Kontingents zu bleiben. Der Stichprobenalgorithmus behält Elemente in Bezug auf die Telemetrie bei, sodass Sie beispielsweise in Search die Anforderung ermitteln können, die in Beziehung zu einer bestimmten Ausnahme steht. Der Algorithmus behält außerdem korrekte Zahlenwerte bei, sodass Sie im Metrik-Explorer die richtigen Werte für Anforderungsraten, Ausnahmeraten und weitere Messwerte sehen.

Es gibt verschiedene Formen der Stichprobenerstellung.

* Beim ASP.NET-SDK wird standardmäßig die [adaptive Stichprobenerstellung](app-insights-sampling.md) verwendet, bei der die Menge der an Ihre App gesendeten Telemetriedaten automatisch angepasst wird. Die Stichprobenerstellung erfolgt im SDK Ihrer Web-App automatisch, sodass der Telemetriedatenverkehr im Netzwerk verringert wird.
* Die *Erfassungs-Stichprobenerstellung* stellt eine Alternative dar, die an dem Punkt arbeitet, an dem Telemetriedaten von Ihrer App den Application Insights-Dienst erreichen. Diese Art der Stichprobenerstellung wirkt sich nicht auf die Menge der Telemetriedaten aus, die von Ihrer App gesendet werden, verringert jedoch die Menge der Daten, die vom Dienst beibehalten werden. Damit können Sie das Kontingent verringern, das von Telemetriedaten von Browsern und anderen SDKs beansprucht wird.

Konfigurieren Sie zum Festlegen der Erfassungs-Stichprobenerstellung die Einstellung auf dem Blatt „Kontingent und Preise“:

![Klicken Sie im Blatt „Kontingent und Preise“ auf die Kachel „Stichproben“, und wählen Sie eine Einheit für die Stichprobenerstellung.](./media/app-insights-pricing/04.png)

> [!WARNING]
> Der auf der Kachel „Beibehaltene Stichproben“ angegebene Wert gibt nur den Wert an, den Sie für die Erfassungs-Stichprobenerstellung festgelegt haben. Er zeigt nicht den Stichproben-Prozentsatz, der im SDK in Ihrer App angewendet wird.
> 
> Falls für die eingehenden Telemetriedaten bereits im SDK Stichproben erstellt wurden, wird die Erfassungs-Stichprobenerstellung nicht angewendet.
> 
> 

Verwenden Sie etwa folgende [Analytics-Abfrage](app-insights-analytics.md), um den tatsächlichen Stichproben-Prozentsatz unabhängig davon zu ermitteln, wo er angewendet wird:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

In jedem beibehaltenen Datensatz gibt `itemCount` die Anzahl ursprünglicher Datensätze an, die der Datensatz darstellt (Anzahl zuvor verworfener Datensätze + 1).

## Anzeigen der Rechnung für Ihr Azure-Abonnement
Die Gebühren für Application Insights werden Ihrer Azure-Rechnung hinzugefügt. Sie sehen die Details zu Ihrer Azure-Rechnung im Bereich "Abrechnung" des Azure-Portals oder im [Azure-Abrechnungsportal](https://account.windowsazure.com/Subscriptions).

![Wählen Sie im seitlichen Menü die Option „Abrechnung“.](./media/app-insights-pricing/02-billing.png)

## Begrenzungen von Namen
1. Bis zu 200 eindeutige Metriknamen und 200 eindeutige Eigenschaftennamen für Ihre Anwendung. Zu den Metriken gehören Daten, die über TrackMetric gesendet werden, sowie Messungen für andere Datentypen wie z. B. Ereignisse. [Metriken und Eigenschaftennamen][api] gelten global pro Instrumentationsschlüssel.
2. [Eigenschaften][apiproperties] können nur zur Filterung und zur Gruppierung verwendet werden, solange sie pro Eigenschaft weniger als 100 eindeutige Werte aufweisen. Sind mehr als 100 eindeutige Werte vorhanden, können Sie die Eigenschaft zwar weiterhin für Suchvorgänge, aber nicht mehr zum Filtern oder Gruppieren verwenden.
3. Standardeigenschaften wie z. B. RequestName und die Seiten-URL, sind auf 1000 eindeutige Werte pro Woche beschränkt. Nach 1000 eindeutigen Werten werden zusätzliche Werte als „Andere Werte“ gekennzeichnet. Die ursprünglichen Werte können nach wie vor für die Volltextsuche und die Filterung verwendet werden.

Sollte Ihre Anwendung diese Grenzwerte überschreiten, können Sie Ihre Daten auf verschiedene Instrumentationsschlüssel aufteilen (also [neue Application Insights-Ressourcen erstellen](app-insights-create-new-resource.md) und einen Teil der Daten an die neuen Instrumentationsschlüssel senden). Dadurch ergibt sich unter Umständen auch eine bessere Struktur. Mithilfe von [Dashboards](app-insights-dashboards.md#dashboards) können Sie die unterschiedlichen Metriken auf einem einzelnen Bildschirm anzeigen und so problemlos verschiedene Metriken miteinander vergleichen.

## Zusammenfassung der Grenzwerte
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/



<!---HONumber=AcomDC_0831_2016-->