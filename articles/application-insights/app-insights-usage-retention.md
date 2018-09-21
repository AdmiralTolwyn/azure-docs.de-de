---
title: Benutzerbindungsanalyse für Webanwendungen mit Azure Application Insights | Microsoft-Dokumentation
description: Wie viele Benutzer kehren zu Ihrer App zurück?
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: 6e042034d52551d58e290b54ca3547dedbc89c47
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35635180"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Benutzerbindungsanalyse für Webanwendungen mit Azure Application Insights

Mithilfe des Vermerkdauer-Features in [Azure Application Insights](app-insights-overview.md) können Sie analysieren, wie viele Benutzer zu Ihrer App zurückkehren und wie oft sie bestimmte Aufgaben durchführen oder Ziele erreichen. Beispiel: Wenn Sie eine Spielewebsite betreiben, können Sie die Anzahl der Benutzer, die nach einem verlorenen Spiel zurückkehren, mit der Anzahl vergleichen, die nach einem gewonnenen Spiel zurückkehren. Mit diesem Wissen können Sie sowohl die Benutzererfahrung als auch Ihre Geschäftsstrategie verbessern.

## <a name="get-started"></a>Erste Schritte

Wenn im Vermerkdauer-Tool des Application Insights-Portals noch keine Daten zu sehen sind, informieren Sie sich [hier](app-insights-usage-overview.md) über die ersten Schritte mit den Nutzungstools.

## <a name="the-retention-tool"></a>Das Vermerkdauer-Tool

![Vermerkdauer-Tool](./media/app-insights-usage-retention/retention.png)

1. Über die Symbolleiste kann der Benutzer folgende Aktionen ausführen: Erstellen von neuen Berichten zur Vermerkdauer, Öffnen von vorhandenen Berichten zur Vermerkdauer, Speichern des aktuellen Berichts zur Vermerkdauer oder Speichern unter einem bestimmten Namen, Rückgängigmachen von Änderungen an gespeicherten Berichten, Aktualisieren von Daten im Bericht, Teilen von Berichten per E-Mail oder direktem Link und Zugreifen auf die Dokumentationsseite. 
2. Standardmäßig werden im Bericht zur Vermerkdauer alle Benutzer aufgeführt, die einen bestimmten Vorgang durchgeführt haben, zu dem Tool zurückgekehrt sind und dann einen anderen Vorgang durchgeführt haben. Sie können Ereignisse in verschiedenen Kombinationen auswählen, um den Schwerpunkt auf bestimmte Benutzeraktivitäten zu setzen.
3. Wenden Sie einen oder mehrere Filter auf Eigenschaften an. Beispielsweise können Sie sich auf Benutzer in einem bestimmten Land oder einer bestimmten Region konzentrieren. Klicken Sie nach dem Festlegen der Filter auf **Aktualisieren**. 
4. Das Diagramm mit der gesamten Vermerkdauer zeigt eine Zusammenfassung der Vermerkdauer für die Benutzer im ausgewählten Zeitraum an. 
5. Das Raster gibt die Anzahl der Benutzer an, die gemäß dem Abfrage-Generator in Punkt 2 vermerkt wurden. Jede Zeile stellt eine Kohorte von Benutzern dar, die ein Ereignis während des angezeigten Zeitraums ausgeführt haben. Jede Zelle in der Zeile zeigt an, wie viele Benutzer dieser Kohorte mindestens einmal in einem späteren Zeitraum zurückgekehrt sind. Einige Benutzer kehren möglicherweise in mehreren Zeiträumen zurück. 
6. Die Application Insights-Karten zeigen die fünf wichtigsten Initiierungsereignisse sowie die fünf wichtigsten zurückgegebenen Ereignisse an, um Benutzern einen besseren Überblick über ihren Bericht zur Vermerkdauer zu verschaffen. 

![Zeigen auf die Vermerkdauer](./media/app-insights-usage-retention/hover.png)

Benutzer können auf die Zellen im Vermerkdauer-Tool zeigen, um auf die Analyseschaltfläche zuzugreifen und QuickInfos zur Bedeutung der Zelle zu erhalten. Über die Analyseschaltfläche gelangen Benutzer zum Analysetool, in dem bereits eine Abfrage zur Generierung von Benutzern auf der Grundlage der Zelle angegeben ist. 

## <a name="use-business-events-to-track-retention"></a>Nachverfolgen der Vermerkdauer mithilfe von Geschäftsereignissen

Um die nützlichste Vermerkdaueranalyse zu erhalten, messen Sie Ereignisse, die wichtige Geschäftsaktivitäten darstellen. 

Beispielsweise können viele Benutzer eine Seite in Ihrer App öffnen, ohne das darauf angezeigte Spiel zu spielen. Die Nachverfolgung der Seitenansichten an sich würde daher eine ungenaue Schätzung der Anzahl der Personen ergeben, die zurückkehren, um das Spiel erneut zu spielen, weil es ihnen gefallen hat. Um ein klares Bild der zurückkehrenden Spieler zu erhalten, sollte Ihre App ein benutzerdefiniertes Ereignis senden, wenn ein Benutzer tatsächlich spielt.  

Es empfiehlt sich, benutzerdefinierte Ereignisse zu codieren, die wichtige geschäftliche Aktionen darstellen, und diese für die Vermerkdaueranalyse zu verwenden. Zum Erfassen des Spielergebnisses müssen Sie eine Codezeile zum Senden eines benutzerdefinierten Ereignisses an Application Insights schreiben. Wenn Sie diese im Webseitencode oder in Node.JS schreiben, sieht sie wie folgt aus:

```JavaScript
    appinsights.trackEvent("won game");
```

Oder im ASP.NET-Servercode:

```csharp
   telemetry.TrackEvent("won game");
```

[Erfahren Sie mehr über das Schreiben von benutzerdefinierten Ereignissen](app-insights-api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Nächste Schritte
- Um mit der Nutzung zu beginnen, senden Sie [benutzerdefinierte Ereignisse](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) oder [Seitenansichten](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Wenn Sie bereits benutzerdefinierte Ereignisse oder Seitenansichten senden, finden Sie mithilfe der Nutzungstools heraus, wie Benutzer den Dienst verwenden.
    - [Benutzer, Sitzungen, Ereignisse](app-insights-usage-segmentation.md)
    - [Trichter](usage-funnels.md)
    - [Benutzerabläufe](app-insights-usage-flows.md)
    - [Arbeitsmappen](app-insights-usage-workbooks.md)
    - [Hinzufügen von Benutzerkontext](app-insights-usage-send-user-context.md)


