---
title: Häufig gestellte Fragen zu Log Analytics | Microsoft Docs
description: Antworten auf häufig gestellte Fragen zum Azure Log Analytics-Dienst.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: eb1a60ff533e9e24f3dc80057129da47a2d9a726
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128529"
---
# <a name="log-analytics-faq"></a>Häufig gestellte Fragen zu Log Analytics
Dieser Microsoft-Artikel enthält eine Liste häufig gestellter Fragen zu Log Analytics in Microsoft Azure. Wenn Sie weiteren Fragen zu Log Analytics haben, besuchen Sie das [Diskussionsforum](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights), und stellen Sie Ihre Fragen. Wenn eine Frage häufiger gestellt wird, fügen wir sie diesem Artikel hinzu, damit sie schnell und einfach gefunden werden kann.

## <a name="general"></a>Allgemein

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>F: Verwendet Log Analytics denselben Agent wie das Azure Security Center?

A. Seit Anfang Juni 2017 verwendet Azure Security Center den Microsoft Monitoring Agent zum Sammeln und Speichern von Daten. Weitere Informationen finden Sie unter [Plattformmigration des Azure Security Center – FAQs](../security-center/security-center-platform-migration-faq.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>F: Welche Überprüfungen werden von den AD- und SQL-Bewertungslösungen durchgeführt?

A: Die folgende Abfrage zeigt eine Beschreibung aller Überprüfungen, die derzeit ausgeführt werden:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Die Ergebnisse können dann zur weiteren Prüfung in Excel exportiert werden.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>F: Warum sehe ich etwas anderes als „OMS“ in der System Center Operations Manager-Konsole?

A: Je nachdem, welchen Updaterollup von Operations Manager Sie verwenden, wird Ihnen ggf. ein Knoten für *System Center Advisor*, *Operational Insights* oder *Log Analytics* angezeigt.

Die Aktualisierung der Textzeichenfolgen für *OMS* befindet sich in einem Management Pack, das manuell importiert werden muss. Um den aktuellen Text und die aktuellen Funktionen anzuzeigen, befolgen Sie die Anweisungen im aktuellen System Center Operations Manager-Updaterollup-KB-Artikel, und aktualisieren Sie die Konsole.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>F: Gibt es eine lokale Version von Log Analytics?

A: Nein. Log Analytics ist ein skalierbarer Clouddienst, der große Datenmengen verarbeitet und speichert. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>F: Wie behebe ich das Problem, wenn Log Analytics keine Daten mehr erfasst?

F: Für ein Abonnement und einen Arbeitsbereich, die vor dem 2. April 2018 erstellt wurden und sich im Tarif *Free* befinden, wird die Datensammlung für den Rest des Tages eingestellt, wenn mehr als 500 MB an einem Tag gesendet wurden. Das Erreichen des Tageslimits ist häufig die Ursache dafür, dass Log Analytics die Datensammlung beendet oder Daten scheinbar fehlen.  

Log Analytics erstellt ein Ereignis vom Typ *Heartbeat* und kann verwendet werden, um zu ermitteln, ob die Datensammlung beendet wurde. 

Führen Sie die folgende Abfrage in der Suche aus, um zu überprüfen, ob Sie das Tageslimit erreichen und Daten fehlen: `Heartbeat | summarize max(TimeGenerated)`

Führen Sie zum Überprüfen eines bestimmten Computers die folgende Abfrage aus: `Heartbeat | where Computer=="contosovm" | summarize max(TimeGenerated)`

Wenn die Datensammlung beendet wird, werden je nach gewähltem Zeitbereich keine zurückgegebenen Datensätze angezeigt.   

Die folgende Tabelle beschreibt die Gründe, warum die Datensammlung endet, und eine empfohlene Aktion zum Fortsetzen der Datensammlung:

| Grund für Beenden der Datensammlung                       | Aktion zum Fortsetzen der Datensammlung |
| -------------------------------------------------- | ----------------  |
| Limit für kostenlose Daten erreicht<sup>1</sup>       | Warten Sie, bis die Sammlung im Folgemonat automatisch neu gestartet wird. Oder:<br> Wechseln Sie zu einem kostenpflichtigen Tarif |
| Das Azure-Abonnement befindet sich aus folgendem Grund in einem angehaltenen Zustand: <br> Kostenlose Testversion endete <br> Azure Pass ist abgelaufen <br> Monatliches Ausgabenlimit ist erreicht (z.B. in einem MSDN- oder Visual Studio-Abonnement)                          | Konvertieren in ein kostenpflichtiges Abonnement <br> Konvertieren in ein kostenpflichtiges Abonnement <br> Limit entfernen oder warten, bis das Limit zurückgesetzt wird |

<sup>1</sup> Wenn Ihr Arbeitsbereich im Tarif *Free* liegt, können Sie täglich nur 500 MB an Daten an den Dienst senden. Wenn Sie das Tageslimit erreichen, wird die Datensammlung bis zum nächsten Tag angehalten. Daten, die gesendet werden, während die Datensammlung angehalten ist, werden nicht indiziert und stehen nicht für die Suche zur Verfügung. Bei Fortsetzung der Datensammlung erfolgt nur die Verarbeitung neu gesendeter Daten. 

Log Analytics orientiert sich an der UTC-Zeit, und jeder Tag beginnt um Mitternacht. Wenn der Arbeitsbereich das Tageslimit erreicht, wird die Verarbeitung während der ersten Stunde des nächsten UTC-Tages fortgesetzt.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>F: Wie kann ich benachrichtigt werden, wenn die Datensammlung beendet wird?

A: Führen Sie die Schritte unter [Erstellen einer Warnungsregel mit dem Azure-Portal](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) aus, um eine Benachrichtigung zu erhalten, wenn die Datensammlung beendet wird.

Legen Sie beim Erstellen der Warnung für das Beenden der Datensammlung Folgendes fest:

- **Definieren der Warnungsbedingung**: Festlegen Ihres Log Analytics-Arbeitsbereichs als Ressourcenziel
- **Warnungskriterien**:
   - **Signalname** auf **Benutzerdefinierte Protokollsuche**
   - **Suchabfrage** auf `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Warnungslogik**: **Basiert auf** *Anzahl von Ergebnissen* und **Bedingung** ist *Größer als* ein **Schwellenwert** von *0*
   - **Zeitraum** auf *30* Minuten und **Warnungshäufigkeit** auf alle *10* Minuten
- **Definieren der Warnungsdetails**:
   - **Name** auf *Datensammlung beendet*
   - **Schweregrad** auf *Warnung*

Geben Sie eine vorhandene [Aktionsgruppe](../monitoring-and-diagnostics/monitoring-action-groups.md) an, oder erstellen Sie eine neue, damit Sie benachrichtigt werden, wenn die Protokollwarnung Kriterien erfüllt, und ein Heartbeat für mehr als 15 Minuten ausbleibt.

## <a name="configuration"></a>Konfiguration
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>F: Kann ich den Namen der Tabelle bzw. des Blobcontainers zum Einlesen von Daten aus Azure-Diagnose (WAD) ändern?

A. Nein, derzeit ist es nicht möglich, aus beliebigen Tabellen oder Containern im Azure-Speicher zu lesen.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>F: Welche IP-Adressen verwendet der Log Analytics-Dienst? Wie stelle ich sicher, dass meine Firewall nur Datenverkehr zum Log Analytics-Dienst zulässt?

A. Der Log Analytics-Dienst basiert auf Azure. Log Analytics-IP-Adressen finden Sie in den [Microsoft Azure Datacenter IP Ranges](http://www.microsoft.com/download/details.aspx?id=41653) (Microsoft Azure-Rechenzentrum-IP-Adressbereiche).

Sobald Dienstbereitstellungen erfolgen, ändern sich die tatsächlichen IP-Adressen des Log Analytics-Diensts. Die für Ihre Firewall zugelassenen DNS-Namen sind in den [Netzwerkanforderungen](log-analytics-concept-hybrid.md#network-firewall-requirements) dokumentiert.

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>F: Ich verwende ExpressRoute für die Verbindung mit Azure. Wird für meinen Log Analytics-Datenverkehr meine ExpressRoute-Verbindung verwendet?

A. Die verschiedenen Typen von ExpressRoute-Datenverkehr werden in der [ExpressRoute-Dokumentation](../expressroute/expressroute-faqs.md#supported-services) beschrieben.

Für Datenverkehr zu Log Analytics wird eine ExpressRoute-Verbindung mit öffentlichem Peering verwendet.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>F: Gibt es eine einfache Möglichkeit zum Verschieben eines vorhandenen Log Analytics-Arbeitsbereichs in einen anderen Log Analytics-Arbeitsbereich bzw. ein anderes Azure-Abonnement?

A. Mit dem Cmdlet `Move-AzureRmResource` können Sie einen Log Analytics-Arbeitsbereich sowie ein Automation-Konto aus einem Azure-Abonnement in ein anderes verschieben. Weitere Informationen finden Sie unter [Move-AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

Diese Änderung kann auch im Azure-Portal erfolgen.

Sie können Daten nicht aus einem Log Analytics-Arbeitsbereich in einen anderen verschieben oder die Region ändern, in der Log Analytics-Daten gespeichert werden.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>F: Wie füge ich Log Analytics System Center Operations Manager hinzu?

A: Nach einer Aktualisierung auf den neuen Updaterollup und dem Import von Management Packs können Sie eine Verbindung von Operations Manager mit Log Analytics herstellen.

>[!NOTE]
>Die Operations Manager-Verbindung mit Log Analytics ist nur für System Center Operations Manager 2012 SP1 und höher verfügbar.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>F: Wie kann ich sicherstellen, dass ein Agent mit Log Analytics kommunizieren kann?

A: Um sicherzustellen, dass der Agent mit OMS kommunizieren kann, wechseln Sie zu „Systemsteuerung > Sicherheit und Einstellungen > **Microsoft Monitoring Agent**“.

Suchen Sie unter der Registerkarte **Azure Log Analytics (OMS)** nach einem grünen Häkchen. Ein grünes Häkchen bestätigt, dass der Agent mit dem Azure-Dienst kommunizieren kann.

Ein gelbes Warnsymbol bedeutet, dass der Agent Probleme bei der Kommunikation mit Log Analytics hat. Ein häufiger Grund ist, dass der Microsoft Monitoring Agent-Dienst beendet wurde. Starten Sie den Dienst mit dem Dienststeuerungs-Manager neu.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>F: Wie beende ich die Kommunikation eines Agents mit Log Analytics?

A: Entfernen Sie in System Center Operations Manager den Computer aus der Liste der OMS-verwalteten Computer. Operations Manager aktualisiert die Konfiguration des Agents so, dass er keine Berichte mehr an Log Analytics sendet. Bei Agents, die direkt mit Log Analytics verbunden sind, können Sie die Kommunikation beenden, indem Sie zu „Systemsteuerung > Sicherheit und Einstellungen > **Microsoft Monitoring Agent**“ wechseln.
Entfernen Sie unter **Azure Log Analytics (OMS)** alle aufgeführten Arbeitsbereiche.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>F: Warum tritt ein Fehler auf, wenn ich versuche, meinen Arbeitsbereich aus einem Azure-Abonnement in ein anderes zu verschieben?

A: Wenn Sie das Azure-Portal verwenden, stellen Sie sicher, dass nur der Arbeitsbereich für das Verschieben ausgewählt ist. Wählen Sie nicht die Lösungen, denn sie werden automatisch verschieben, nachdem der Arbeitsbereich verschoben ist. 

Stellen Sie sicher, dass Sie in beiden Azure-Abonnements über Berechtigungen verfügen.

## <a name="agent-data"></a>Agent-Daten
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>F: Wie viele Daten kann ich über den Agent an Log Analytics senden? Gibt es eine maximale Datenmenge pro Kunde?
A. Im Tarif Free gilt eine tägliche Obergrenze von 500 MB pro Arbeitsbereich. Die Tarife Standard und Premium sehen keine Begrenzung der Datenmenge vor, die hochgeladen werden kann. Als Clouddienst ist Log Analytics so ausgelegt, dass ein automatisches Hochskalieren erfolgt, um das vom Kunden eingehende Datenvolumen zu bewältigen, selbst wenn es sich um mehrere Terabytes pro Tag handelt.

Der Log Analytics-Agent ist auf einen kleinen Speicherbedarf ausgelegt. Das Datenvolumen variiert basierend auf den Lösungen, die Sie aktivieren. Ausführliche Informationen zum Datenvolumen sowie eine Aufschlüsselung nach Lösung finden Sie auf der Seite [Verwendung](log-analytics-usage.md).

Weitere Informationen finden Sie in einem [Kundenblog](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html), in dem die Ergebnisse nach Auswertung der Ressourcennutzung (Speicherbedarf) des OMS-Agents angezeigt werden.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>F: Wie viel Netzwerkbandbreite nutzt der Microsoft-Verwaltungs-Agent (MMA) beim Senden von Daten an Log Analytics?

A. Die Bandbreite hängt von der gesendeten Datenmenge ab. Daten werden komprimiert, bevor sie über das Netzwerk gesendet werden.

### <a name="q-how-much-data-is-sent-per-agent"></a>F: Wie viele Daten werden pro Agent gesendet?

A. Die pro Agent gesendete Datenmenge hängt von Folgendem ab:

* Den Lösungen, die Sie aktiviert haben
* Der Anzahl der Protokolle und Leistungsindikatoren, die gesammelt werden
* Der Menge der Daten in den Protokollen

Der Tarif Free ist eine gute Möglichkeit, dem Dienst mehrere Server hinzuzufügen und das typische Datenvolumen zu messen. Die Gesamtverwendung wird auf der Seite [Verwendung](log-analytics-usage.md) gezeigt.

Für Computer, die den WireData-Agent ausführen können, zeigen Sie mithilfe der folgenden Abfrage an, wie viele Daten gesendet werden:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Nächste Schritte
* [Erste Schritte mit Log Analytics](log-analytics-get-started.md). Hier erfahren Sie mehr über Log Analytics und wie Sie binnen Minuten loslegen können.
