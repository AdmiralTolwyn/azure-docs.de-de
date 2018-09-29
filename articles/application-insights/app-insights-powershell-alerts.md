---
title: Einrichten von Warnungen in Application Insights mithilfe von PowerShell | Microsoft Docs
description: Automatisieren Sie die Konfiguration von Application Insights so, dass Sie bei Metrikänderungen E-Mails erhalten.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 05d6a9e0-77a2-4a35-9052-a7768d23a196
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2016
ms.author: mbullwin
ms.openlocfilehash: e7e0f6747beea43b55ae1a20da44cde3c070cd89
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091371"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Einrichten von Warnungen in Application Insights mithilfe von PowerShell
Sie können die Konfiguration von [Warnungen](app-insights-alerts.md) in [Application Insights](app-insights-overview.md) automatisieren.

Darüber hinaus können Sie [Webhooks zum Automatisieren Ihrer Reaktion auf eine Warnung festlegen](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

> [!NOTE]
> Wenn Sie gleichzeitig Ressourcen und Warnungen erstellen möchten, sollten Sie [eine Azure Resource Manager-Vorlage](app-insights-powershell.md) verwenden.
>
>

## <a name="one-time-setup"></a>Einmalige Konfiguration
Wenn Sie PowerShell noch nicht mit Ihrem Azure-Abonnement verwendet haben:

Installieren Sie das Azure-PowerShell-Modul auf dem Computer, auf dem die Skripts ausgeführt werden sollen.

* Installieren Sie [Microsoft-Webplattform-Installer (Version 5 oder höher)](http://www.microsoft.com/web/downloads/platform.aspx).
* Installieren Sie hiermit Microsoft Azure PowerShell.

## <a name="connect-to-azure"></a>Herstellen einer Verbindung mit Azure
Starten Sie Azure PowerShell, und [stellen Sie eine Verbindung mit Ihrem Abonnement her](/powershell/azure/overview):

```PowerShell

    Add-AzureAccount
```


## <a name="get-alerts"></a>Abrufen von Warnungen
    Get-AzureAlertRmRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Warnung hinzufügen
    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US" // must be East US at present
     -RuleType Metric



## <a name="example-1"></a>Beispiel 1
E-Mail-Nachricht senden, wenn die Antwort des Servers auf HTTP-Anforderungen, gemittelt über 5 Minuten, langsamer als 1 Sekunde ist. Der Name meiner Application Insights-Ressource lautet IceCreamWebApp, und sie befindet sich in der Ressourcengruppe "Fabrikam". Ich bin Besitzer des Azure-Abonnements.

Die GUID ist die Abonnement-ID (nicht der Instrumentierungsschlüssel der Anwendung).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>Beispiel 2
Ich habe eine Anwendung, in der ich mit [TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric) eine Metrik namens „salesPerHour“ melde. Eine E-Mail an meine Kollegen senden, wenn "salesPerHour" gemittelt über 24 Stunden unter 100 fällt.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

Dieselbe Regel kann für die Metrik verwendet werden, die mit dem [Messparameter](app-insights-api-custom-events-metrics.md#properties) eines anderen Trackingaufrufs gemeldet wird, z.B. „TrackEvent“ oder „trackPageView“.

## <a name="metric-names"></a>Metriknamen
| Metrikname | Anzeigename | BESCHREIBUNG |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Browserausnahmen |Anzahl nicht erfasster Ausnahmen, die im Browser ausgelöst wurden. |
| `basicExceptionServer.count` |Serverausnahmen |Anzahl der nicht behandelten Ausnahmen, die von der Anwendung ausgelöst wurden |
| `clientPerformance.clientProcess.value` |Clientverarbeitungszeit |Zeit zwischen dem Empfang des letzten Byte eines Dokuments und dem Laden des DOM. Asynchrone Anforderungen werden möglicherweise immer noch verarbeitet. |
| `clientPerformance.networkConnection.value` |Netzwerkverbindungszeit zum Laden der Seite |Zeit, die der Browser für die Verbindung mit dem Netzwerk benötigt. Kann bei Zwischenspeicherung 0 sein. |
| `clientPerformance.receiveRequest.value` |Empfängt Antwortzeit |Zeit zwischen dem Senden der Anforderung durch den Browser und dem Empfangen der Antwort. |
| `clientPerformance.sendRequest.value` |Anforderungszeit senden |Vom Browser benötigte Zeit zum Senden der Anforderung. |
| `clientPerformance.total.value` |Browser-Seitenladezeit |Zeit ab der Benutzeranforderung, bis DOM, Stylesheets, Skripts und Bilder geladen werden. |
| `performanceCounter.available_bytes.value` |Verfügbarer Arbeitsspeicher |Physischer Arbeitsspeicher, der sofort für einen Prozess oder für die Nutzung durch das System verfügbar ist. |
| `performanceCounter.io_data_bytes_per_sec.value` |E/A-Rate für Prozess |Gesamtanzahl von pro Sekunde in Dateien, im Netzwerk und auf Geräten gelesenen und geschriebenen Bytes. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |Ausnahmerate |Pro Sekunde ausgelöste Ausnahmen. |
| `performanceCounter.percentage_processor_time.value` |Prozess-CPU |Der Prozentsatz der verstrichenen Zeit für alle Prozessthreads, die vom Prozessor zur Ausführung von Anweisungen für den Anwendungsprozess verwendet wird. |
| `performanceCounter.percentage_processor_total.value` |Prozessorzeit |Der Prozentsatz der Zeit, die der Prozessor nicht im Leerlauf in Threads verbringt. |
| `performanceCounter.process_private_bytes.value` |Private Bytes für Prozess |Speicher, der exklusiv den überwachten Anwendungsprozessen zugewiesen ist. |
| `performanceCounter.request_execution_time.value` |Ausführungsdauer für ASP.NET-Anforderungen |Ausführungszeit der aktuellen Anforderung. |
| `performanceCounter.requests_in_application_queue.value` |ASP.NET-Anforderungen in Ausführungswarteschlange |Länge der Anwendungsanforderungswarteschleife. |
| `performanceCounter.requests_per_sec.value` |ASP.NET-Anforderungsrate |Rate aller Anforderungen an die Anwendung pro Sekunde von ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Abhängigkeitsfehler |Anzahl der Aufrufe mit Fehlern von der Serveranwendung an externe Ressourcen. |
| `request.duration` |Serverantwortzeit |Zeit zwischen dem Empfang einer HTTP-Anforderung und dem Abschluss des Sendevorgangs der Antwort. |
| `request.rate` |Anforderungsrate |Rate aller Anforderungen an die Anwendung pro Sekunde. |
| `requestFailed.count` |Anforderungsfehler |Anzahl von HTTP-Anforderungen, die als Ergebnis Antwortcode >= 400 hatten. |
| `view.count` |Seitenaufrufe |Anzahl der Clientbenutzeranforderungen für eine Webseite. Synthetischer Datenverkehr wird herausgefiltert. |
| {benutzerdefinierter Metrikname} |{Ihr Metrikname} |Der von [TrackMetric](app-insights-api-custom-events-metrics.md#trackmetric) oder im [Messparameter eines Trackingaufrufs](app-insights-api-custom-events-metrics.md#properties) gemeldete Metrikwert. |

Die Metriken werden von verschiedenen Telemetriemodulen gesendet:

| Metrikgruppe | Erfassungsmodul |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>view |[Browser JavaScript](app-insights-javascript.md) |
| performanceCounter |[Leistung](app-insights-configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Abhängigkeit](app-insights-configuration-with-applicationinsights-config.md) |
| request,<br/>requestFailed |[Serveranfrage](app-insights-configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhooks
Sie können [Ihre Reaktion auf eine Warnung automatisieren](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure ruft eine Webadresse Ihrer Wahl auf, wenn eine Warnung ausgelöst wird.

## <a name="see-also"></a>Weitere Informationen
* [Skript zum Konfigurieren von Application Insights](app-insights-powershell-script-create-resource.md)
* [Erstellen von Application Insights- und Webtestressourcen aus Vorlagen](app-insights-powershell.md)
* [Automatisieren der Kopplung der Microsoft Azure-Diagnose mit Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Automatisieren Ihrer Reaktion auf eine Warnung](../monitoring-and-diagnostics/insights-webhooks-alerts.md)
