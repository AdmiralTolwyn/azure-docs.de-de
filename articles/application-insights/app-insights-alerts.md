<properties 
	pageTitle="Einrichten von Warnungen in Application Insights" 
	description="Erhalten Sie E-Mails zu Abstürzen, Ausnahmen und Metrikänderungen." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/20/2016" 
	ms.author="awills"/>
 
# Einrichten von Warnungen in Application Insights

[Visual Studio Application Insights][start] kann Sie bei Änderungen der Leistung oder von Nutzungsmetriken in Ihrer App benachrichtigen.

Application Insights überwacht die Live-App auf einer [Vielzahl von Plattformen][platforms], um Sie bei der Diagnose von Leistungsproblemen und beim Auswerten von Nutzungsmustern zu unterstützen.

Es gibt zwei Arten von Warnungen:
 
* **Webtests** informieren Sie, wenn Ihre Website im Internet nicht verfügbar ist oder langsam reagiert. [Weitere Informationen][availability].
* **Metrikwarnungen** teilen Ihnen mit, wenn eine Metrik für einen bestimmten Zeitraum einen Schwellenwert überschreitet – z. B. Fehleranzahl, Arbeitsspeicher oder Seitenaufrufe. 

Es gibt eine [separate Seite zu Webtests][availability], daher konzentrieren wir uns an dieser Stelle auf Metrikwarnungen.

> [AZURE.NOTE] Sie können darüber hinaus E-Mails über das Feature [Proaktive Erkennung](app-insights-proactive-detection.md) erhalten, in denen Sie bei ungewöhnlichen Leistungsmustern Ihrer App gewarnt werden. Im Gegensatz zu Warnungen erhalten Sie diese Benachrichtigungen, ohne dass Sie sie einrichten müssen. Sie dienen zum Optimieren der Leistung Ihrer App und nicht zum Auslösen eines Alarms zu dringenden Problemen.

## Metrikwarnungen

Wenn Sie Application Insights noch nicht für Ihre App eingerichtet haben, [holen Sie dies als erstes nach][start].

Um eine E-Mail zu erhalten, wenn eine Metrik einen Schwellenwert überschreitet, starten Sie entweder vom Metrik-Explorer oder von der Kachel "Warnungsregeln" auf dem Blatt "Übersicht".

![Klicken Sie auf dem Blatt "Warnungsregeln" auf "Warnung hinzufügen". Legen Sie Ihre App als die zu messende Ressource fest, geben Sie einen Namen für die Warnung ein, und wählen Sie eine Metrik.](./media/app-insights-alerts/01-set-metric.png)

* Legen Sie die Ressource vor den anderen Eigenschaften fest. **Wählen Sie die Ressource "(Komponenten)" aus**, wenn Sie Benachrichtigungen für Leistungs- oder Nutzungsmetriken festlegen möchten.
* Achten Sie auf die Einheiten, die beim Eingeben des Schwellenwerts gefordert sind.
* Der Name, den Sie der Warnung zuweisen, muss innerhalb der Ressourcengruppe (nicht nur in Ihrer Anwendung) eindeutig sein.
* Wenn Sie das Feld „E-Mail an Besitzer...“ aktivieren, werden Warnungen per E-Mail an alle Benutzer gesendet, die Zugriff auf diese Ressourcengruppe haben. Wenn Sie diese Personengruppe erweitern möchten, fügen Sie die entsprechenden Benutzer der [Ressourcengruppe oder dem Abonnement](app-insights-resources-roles-access-control.md) (nicht der Ressource) hinzu.
* Wenn Sie „Weitere E-Mail-Adressen“ angeben, werden Warnungen an diese Einzelpersonen oder Gruppen gesendet (unabhängig davon, ob Sie das Kontrollkästchen „E-Mail an Besitzer“ aktiviert haben). 
* Legen Sie eine [Webhookadresse](../azure-portal/insights-webhooks-alerts.md) fest, wenn Sie eine Web-App eingerichtet haben, die auf Warnungen reagiert. Der Aufruf erfolgt, wenn die Warnung aktiviert (ausgelöst) und wenn sie gelöst wird. (Beachten Sie aber, dass Abfrageparameter derzeit nicht als Webhook-Eigenschaften übergeben werden.)
* Sie können die Warnung deaktivieren oder aktivieren: Die zugehörigen Schaltflächen finden Sie oben auf dem Blatt.

*Die Schaltfläche "Warnung hinzufügen" wird nicht angezeigt.* – Verwenden Sie ein Organisationskonto? Sie können Warnungen festlegen, wenn Sie für diese Anwendungsressource über Zugriffsberechtigungen für Besitzer oder Mitwirkende verfügen. Sehen Sie unter "Einstellungen" -> "Benutzer" nach. [Erfahren Sie mehr über die Zugriffssteuerung][roles].

> [AZURE.NOTE] Auf dem Blatt „Warnungen“ sehen Sie, dass bereits eine Warnung festgelegt ist: [Proaktive NRT-Diagnose](app-insights-nrt-proactive-diagnostics.md). Dies ist eine automatische Benachrichtigung, die eine bestimmte Metrik überwacht, nämlich die Anforderungsfehlerrate. Sofern Sie nicht entscheiden, diese Funktion zu deaktivieren, müssen Sie also keine eigene Warnung für die Anforderungsfehlerrate festlegen.

## Anzeigen Ihrer Warnungen

Sie erhalten eine E-Mail, wenn sich ein Warnungsstatus von "Inaktiv" in "Aktiv" ändert und umgekehrt.

Der aktuelle Status jeder Warnung wird im Fenster "Warnungsregeln" angezeigt.

Die Dropdownliste "Warnungen" enthält einen Überblick über die letzten Aktivitäten:

![](./media/app-insights-alerts/010-alert-drop.png)

Der Verlauf von Statusänderungen befindet sich im Überwachungsprotokoll:

![Klicken Sie auf dem Blatt „Übersicht“ auf „Einstellungen“, „Überwachungsprotokolle“.](./media/app-insights-alerts/09-alerts.png)



## Funktionsweise von Warnungen

* Eine Warnung kann drei Zustände annehmen: „Nie aktiviert“, „Aktiviert“ und „Gelöst“. „Aktiviert“ bedeutet, dass die angegebene Bedingung bei der letzten Auswertung erfüllt wurde.

* Wenn sich der Status einer Warnung ändert, wird eine Benachrichtigung generiert. (Wenn die Bedingung für die Warnung bereits erfüllt war, als Sie die Warnung erstellt haben, erhalten Sie möglicherweise erst dann eine Benachrichtigung, wenn die Bedingung nicht mehr erfüllt wird.)

* Jede Benachrichtigung generiert eine E-Mail-Nachricht, wenn Sie das E-Mail-Feld aktiviert oder E-Mail-Adressen angegeben haben. Sie können auch die Dropdownliste „Benachrichtigungen“ überprüfen.

* Eine Warnung wird jedes Mal ausgewertet, wenn eine Metrik empfangen wird, sonst aber aus keinem anderen Grund.

* Durch die Auswertung wird die Metrik über den vorangegangenen Zeitraum aggregiert und mit dem Schwellenwert verglichen, um den neuen Zustand zu ermitteln.

* Der von Ihnen ausgewählte Zeitraum gibt das Intervall an, über das Metriken aggregiert werden. Der Zeitraum hat keinen Einfluss darauf, wie häufig die Warnung ausgewertet wird: Dies hängt davon ab, wie häufig Metriken empfangen werden.

* Wenn eine Zeit lang kein Daten für eine bestimmte Metrik empfangen wurden, wirkt sich diese Lücke unterschiedlich auf die Auswertung von Warnungen und die Diagramme im Metrik-Explorer aus. Wenn länger als durch das Samplingintervall des Diagramms vorgegeben keine Daten empfangen werden, hat das Diagramm im Metrik-Explorer den Wert 0. Eine auf derselben Metrik basierende Warnung wird jedoch nicht erneut ausgewertet, und der Zustand der Warnung bleibt unverändert.

    Sobald dann Daten empfangen werden, springt das Diagramm auf einen Wert ungleich 0 (null) zurück. Die Warnung wird auf Grundlage der Daten ausgewertet, die für den angegebenen Zeitraum zur Verfügung stehen. Wenn der neue Datenpunkt der einzige in diesem Zeitraum verfügbare Datenpunkt ist, basiert das Aggregat auf diesem Datenpunkt.

* Eine Warnung kann häufig zwischen den Zuständen "Warnung" und "Fehlerfrei" hin und her wechseln, auch wenn Sie einen langen Zeitraum festlegen. Dies kann vorkommen, wenn sich der metrische Wert um den Schwellenwert herum bewegt. Der Schwellenwert weist keine Hysterese auf: Der Übergang zur Warnung erfolgt beim selben Wert wie der Übergang zum fehlerfreien Zustand.



## Verfügbarkeitswarnungen

Sie können Webtests einrichten, um eine beliebige Website von Standorten rund um die Welt aus zu testen. [Weitere Informationen][availability].

## Welche Warnungen sollten festgelegt werden?

Das hängt von Ihrer Anwendung ab. Zunächst einmal sollten Sie nicht zu viele Metriken festlegen. Beobachten Sie Ihre Metrikdiagramme eine Zeitlang, während die App ausgeführt wird, um ein Gefühl für das Normalverhalten zu erhalten. So finden Sie leichter Möglichkeiten zum Verbessern der Leistung. Richten Sie dann Warnungen ein, die Ihnen mitteilen, wenn die Metriken den Normalbereich verlassen.

Zu den gängigen Warnungen zählen Folgende:

* [Webtests][availability] sind wichtig, wenn es sich bei der Anwendung um eine Website oder einen Webdienst handelt, der im öffentlichen Internet sichtbar ist. Sie teilen Ihnen mit, wenn Ihre Website ausfällt oder langsam reagiert – selbst wenn das Problem eher beim Netzbetreiber als bei Ihrer App zu suchen ist. Hierbei handelt es sich allerdings um synthetische Tests, die daher nicht das tatsächliche Benutzererlebnis messen.
* [Browsermetriken][client], insbesondere **Browser-Seitenladezeiten**, eignen sich für Webanwendungen. Wenn Ihre Seite viele Skripts enthält, sollten Sie auf **Browserausnahmen** achten. Um diese Metriken und Warnungen zu erhalten, müssen Sie die [Webseitenüberwachung][client] einrichten.
* **Serverantwortzeit** und **Anforderungsfehler** für die Serverseite von Webanwendungen. Achten Sie neben der Einrichtung von Warnungen auf diese Metriken, um festzustellen, ob sie bei hohen Anforderungsraten unverhältnismäßig variieren: Dies kann darauf hindeuten, dass für Ihre App nicht genügend Systemressourcen vorhanden sind.
* **Serverausnahmen** erfordern ein [zusätzliches Setup](app-insights-asp-net-exceptions.md), damit sie angezeigt werden.

## Automation

* [Verwenden von PowerShell zum Automatisieren der Einrichtung von Warnungen](app-insights-powershell-alerts.md)
* [Verwenden von Webhooks zum Automatisieren der Reaktion auf Warnungen](../azure-portal/insights-webhooks-alerts.md)

## Weitere Informationen

* [Verfügbarkeitswebtests](app-insights-monitor-web-app-availability.md)
* [Automatisieren der Einrichtung von Warnungen](app-insights-powershell-alerts.md)
* [Proaktive Erkennung](app-insights-proactive-detection.md) 



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_0622_2016-->