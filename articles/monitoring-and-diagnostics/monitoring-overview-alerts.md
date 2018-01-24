---
title: "Überblick über Warnungen in Microsoft Azure und Microsoft Azure Monitor| Microsoft-Dokumentation"
description: "Mit Warnungen können Sie Metriken, Ereignisse oder Protokolle für Ihre Azure-Ressourcen überwachen. Lassen Sie sich benachrichtigen, wenn eine von Ihnen festgelegte Bedingung erfüllt ist."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c1f0182f27cfb8441a09abd2031b365a4ab4315a
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2017
---
# <a name="what-are-alerts-in-microsoft-azure"></a>Was sind Warnungen in Microsoft Azure?
In diesem Artikel werden die verschiedenen Quellen von Warnungen in Microsoft Azure, der Zweck dieser Warnungen, die damit verbundenen Vorteile und die ersten Schritte bei der Verwendung beschrieben. Er bezieht sich insbesondere auf Azure Monitor, enthält aber auch Verweise auf andere Dienste mit Warnungen. Warnungen stellen ein Verfahren für die Überwachung in Azure dar, bei dem Sie Bedingungen für Daten konfigurieren können und benachrichtigt werden, wenn die Bedingungen mit den aktuellen Überwachungsdaten übereinstimmen.


## <a name="taxonomy-of-azure-alerts"></a>Taxonomie von Azure-Warnungen
In Azure werden die folgenden Begriffe verwendet, um Warnungen und ihre Funktionen zu beschreiben:
* **Warnung**: Eine Definition mit Kriterien (mindestens eine Regel oder Bedingung), die aktiviert wird, wenn die Kriterien erfüllt sind.
* **Aktiv**: Der Status, wenn die für eine Warnung definierten Kriterien erfüllt sind.
* **Gelöst**: Der Status, wenn die für eine Warnung definierten Kriterien nicht mehr erfüllt sind, nachdem dies zuvor der Fall war.
* **Benachrichtigung**: Die Aktion, die durchgeführt wird, wenn eine Warnung aktiv wird.
* **Aktion**: Ein bestimmte Kontaktaufnahme, die an den Empfänger einer Benachrichtigung gesendet wird (z.B. E-Mail an eine Adresse oder das Posten an eine Webhook-URL). Benachrichtigungen können in der Regel mehrere Aktionen auslösen.

    > [!NOTE]
    > Im Rahmen der Weiterentwicklung von Warnungen in Azure ist eine neue einheitliche Oberfläche in der Vorschauversion verfügbar. Die neue Oberfläche „Warnungen (Vorschauversion)“ verwendet eine andere Taxonomie. Erfahren Sie mehr über [Warnungen (Vorschauversion)](monitoring-overview-unified-alerts.md). 
    >

## <a name="alerts-in-different-azure-services"></a>Warnungen bei unterschiedlichen Azure-Diensten
Warnungen sind übergreifend für mehrere Azure-Überwachungsdienste verfügbar. Informationen dazu, wie und wann Sie diese Dienste verwenden können, finden Sie in [diesem Artikel](./monitoring-overview.md). Hier ist eine Aufstellung der Warnungstypen angegeben, die in Azure verfügbar sind:


| Dienst | Warnungstyp | Unterstützte Dienste | BESCHREIBUNG |
|---|---|---|---|
| Azure Monitor | [Metrikwarnungen](./insights-alerts-portal.md) | [Unterstützte Metriken von Azure Monitor](./monitoring-supported-metrics.md) | Sie erhalten eine Benachrichtigung, wenn eine beliebige Metrik auf Plattformebene eine bestimmte Bedingung erfüllt (z.B. CPU-Prozentsatz von mehr als 90 auf einer VM während der letzten fünf Minuten). |
|Azure Monitor | [Near Real-Time Metric Alerts (Preview)](./monitoring-near-real-time-metric-alerts.md)| [Unterstützte Ressourcen aus Azure Monitor](./monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for) | Erhalten Sie eine Benachrichtigung schneller als Metrikwarnungen, wenn mindestens eine Metrik auf Plattformebene angegebene Bedingungen erfüllt (z. B. die CPU-Auslastung auf einem virtuellen Computer innerhalb der letzten 5 Minuten größer als 90 % und der Dateneingangsverkehr im Netzwerk größer als 500 MB war). |
| Azure Monitor | [Aktivitätsprotokollwarnungen](./monitoring-activity-log-alerts.md) | Alle in Azure Resource Manager verfügbaren Ressourcentypen | Sie erhalten eine Benachrichtigung, wenn neue Ereignisse im [Azure-Aktivitätsprotokoll](./monitoring-overview-activity-logs.md) bestimmte Bedingungen erfüllen (z.B. beim Vorgang „VM löschen“ in „myProductionResourceGroup“bei Anzeige eines neuen Service Health-Ereignisses mit dem Status „Aktiv“). |
| Application Insights | [Metrikwarnungen](../application-insights/app-insights-alerts.md) | Beliebige Anwendung, die für das Senden von Daten an Application Insights instrumentiert ist | Sie erhalten eine Benachrichtigung, wenn eine Metrik auf Anwendungsebene eine bestimmte Bedingung erfüllt (z.B. eine Serverantwortzeit von mehr als zwei Sekunden). |
| Application Insights | [Webtestwarnungen](../application-insights/app-insights-monitor-web-app-availability.md) | Beliebige Website, die für das Senden von Daten an Application Insights instrumentiert ist | Sie erhalten eine Benachrichtigung, wenn die Verfügbarkeit oder Reaktionsfähigkeit einer Website nicht den Erwartungen entspricht. |
| Log Analytics | [Log Analytics-Warnungen](../log-analytics/log-analytics-alerts.md) | Beliebiger Dienst, der für das Senden von Daten an Log Analytics konfiguriert ist | Sie erhalten eine Benachrichtigung, wenn eine Log Analytics-Suchabfrage für Metrik- bzw. Ereignisdaten bestimmte Kriterien erfüllt. |

## <a name="alerts-on-azure-monitor-data"></a>Warnungen zu Azure Monitor-Daten
Es gibt drei Typen von Warnungen für Daten, die über Azure Monitor verfügbar sind: Metrikwarnungen, Near Real-Time Metric Alerts (Preview) und Aktivitätsprotokollwarnungen.

* **Metrikwarnungen**: Diese Warnung wird ausgelöst, wenn der Wert für eine bestimmte Metrik einen von Ihnen definierten Schwellenwert überschreitet. Die Warnung generiert eine Benachrichtigung, wenn die Warnung den Status „Aktiviert“ (nach Überschreitung des Schwellenwerts und Erfüllung der Warnungsbedingung) oder „Gelöst“ (nach der erneuten Überschreitung des Schwellenwerts und Nichterfüllung der Bedingung) hat. Eine laufend ergänzte Liste verfügbarer Metriken, die vom Azure-Monitor unterstützt werden, finden Sie unter [Unterstützte Metriken von Azure Monitor](monitoring-supported-metrics.md).
* **Near Real-Time Metric Alerts (Preview)**: Diese Warnungen sind Metrikwarnungen ähnlich, unterscheiden sich aber in einigen Aspekten. Wie der Name bereits vermuten lässt, können diese Warnungen nahezu in Echtzeit (schon nach einer Minute) ausgelöst werden. Außerdem unterstützen sie die Überwachung mehrerer (zurzeit zwei) Metriken.  Die Warnung generiert eine Benachrichtigung, wenn die Warnung den Status „Aktiviert“ (nach gleichzeitiger Überschreitung der Schwellenwerte für jede Metrik und Erfüllung der Warnungsbedingung) oder „Gelöst“ (wenn der Schwellenwert durch mindestens eine Metrik erneut überschritten und die Bedingung nicht mehr erfüllt wird) aufweist.

    > [!NOTE]
    > Feature Real-Time Metric Alerts sind zurzeit als öffentliche Preview verfügbar. Die Funktionen und die Benutzeroberfläche können jederzeit geändert werden.
    >
    >

* **Aktivitätsprotokollwarnungen**: Eine Streamingprotokollwarnung, die ausgelöst wird, wenn ein Aktivitätsprotokollereignis generiert wird, das von Ihnen zugewiesene Filterkriterien erfüllt. Diese Warnungen haben nur einen Status: „Aktiviert“. Dies liegt daran, dass das Warnungsmodul die Filterkriterien einfach auf ein neues Ereignis anwendet. Diese Warnungen können zum Empfangen von Benachrichtigungen für den Fall verwendet werden, dass ein neuer Service Health-Vorfall eintritt oder ein Benutzer oder eine Anwendung in Ihrem Abonnement einen Vorgang durchführt, z.B. „Virtuellen Computer löschen“.

Für Diagnoseprotokolldaten, die über Azure Monitor verfügbar sind, empfehlen wir das Weiterleiten der Daten in Log Analytics und das Verwenden einer Log Analytics-Warnung. Das folgende Diagramm enthält eine Zusammenfassung der Quellen von Daten in Azure Monitor und des Konzepts zum Erzeugen von Warnungen aus diesen Daten.

![Warnungen im Überblick](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-alert"></a>Wie erhalte ich eine Benachrichtigung zu einer Azure Monitor-Warnung?
Bisher wurden für Azure-Warnungen unterschiedlicher Dienste die eigenen integrierten Benachrichtigungsmethoden verwendet. In Azure Monitor gibt es jetzt eine wiederverwendbare Benachrichtigungsgruppierung mit der Bezeichnung „Aktionsgruppen“. Mit Aktionsgruppen wird eine Reihe von Empfängern für eine Benachrichtigung angegeben (beliebige Anzahl von E-Mail-Adressen, Telefonnummern (für SMS) oder Webhook-URLs). Jedes Mal, wenn eine Warnung aktiviert wird, die auf die Aktionsgruppe verweist, erhalten alle Empfänger eine Benachrichtigung. Auf diese Weise ist es möglich, eine Gruppierung von Empfängern (z.B. Ihre Liste mit Technikern in Bereitschaft) für viele Warnungsobjekte wiederzuverwenden. Derzeit werden Aktionsgruppen nur von Aktivitätsprotokollwarnungen genutzt, aber auch für mehrere andere Azure-Warnungstypen wird an der Verwendung von Aktionsgruppen gearbeitet.

Für die Aktionsgruppen werden Benachrichtigungen unterstützt, indem zusätzlich zu E-Mail-Adressen und SMS-Telefonnummern eine Webhook-URL bereitgestellt wird. Dies ermöglicht die Automatisierung und Fehlerbehebung, z.B. mit folgenden Komponenten:
    - Azure Automation-Runbook
    - Azure Function
    - Azure-Logik-App
    - ein Drittanbieter-Dienst

Near Real-Time Metric Alerts (Preview) und Aktivitätsprotokollwarnungen verwenden Aktionsgruppen.

Für Metrikwarnungen werden noch keine Aktionsgruppen verwendet. Für eine einzelne Metrikwarnung können Sie Benachrichtigungen für folgende Zwecke konfigurieren:
* Senden von E-Mail-Benachrichtigungen an den Dienstadministrator, an Co-Administratoren oder an zusätzliche von Ihnen festgelegte E-Mail-Adressen
* Aufrufen eines Webhooks, um zusätzliche automatisierte Aktionen auszuführen

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen über Warnregeln und ihre Konfiguration erhalten Sie hier:

* Weitere Informationen zu [Metriken](monitoring-overview-metrics.md)
* Konfigurieren von [Metrikwarnungen über das Azure-Portal](insights-alerts-portal.md)
* Konfigurieren von [Metrikwarnungen mithilfe von PowerShell](insights-alerts-powershell.md)
* Konfigurieren von [Metrikwarnungen über die Befehlszeilenschnittstelle (CLI)](insights-alerts-command-line-interface.md)
* Konfigurieren von [Metrikwarnungen mithilfe der Azure Monitor-REST-API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Weitere Informationen zu [Aktivitätsprotokollen](monitoring-overview-activity-logs.md)
* Konfigurieren von [Aktivitätsprotokollwarnungen über das Azure-Portal](monitoring-activity-log-alerts.md)
* Konfigurieren von [Aktivitätsprotokollwarnungen über den Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Weitere Informationen zum [Webhookschema für Aktivitätsprotokollwarnungen](monitoring-activity-log-alerts-webhook.md)
* Weitere Informationen zu [Near Real-Time Metric Alerts](monitoring-near-real-time-metric-alerts.md)
* Weitere Informationen zu [Dienstbenachrichtigungen](monitoring-service-notifications.md)
* Weitere Informationen zu [Aktionsgruppen](monitoring-action-groups.md)
* Konfigurieren von [Warnungen über Warnungen (Vorschauversion)](monitor-alerts-unified-usage.md)
