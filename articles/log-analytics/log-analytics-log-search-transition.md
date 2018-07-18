---
title: Cheat Sheet für die Azure Log Analytics-Abfragesprache | Microsoft-Dokumentation
description: Dieser Artikel hilft beim Übergang zur neuen Abfragesprache für Log Analytics, wenn Sie bereits mit der bisherigen Sprache vertraut sind.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: bwren
ms.openlocfilehash: 9c487ab33859ae453a0074ef0344f61de19c7b4d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/23/2018
ms.locfileid: "25958170"
---
# <a name="transitioning-to-azure-log-analytics-new-query-language"></a>Übergang zur neuen Azure Log Analytics-Abfragesprache
In Log Analytics wurde vor kurzem eine neue Abfragesprache implementiert.  Dieser Artikel unterstützt Sie beim Übergang zur neuen Abfragesprache für Log Analytics, wenn Sie bereits mit der bisherigen Sprache vertraut sind.

## <a name="resources"></a>angeben


## <a name="language-converter"></a>Sprachkonverter

Wenn Sie mit der bisherigen Log Analytics-Abfragesprache vertraut sind, ist die einfachste Möglichkeit, eine vorhandene Abfrage in der neuen Sprache zu erstellen, die Verwendung des im Portal der Protokollsuche installierten Sprachkonverters, wenn Ihr Arbeitsbereich konvertiert ist.  Die Verwendung des Konverters ist ganz einfach: Geben Sie eine Legacyabfrage in das Textfeld ein, und klicken Sie auf **Konvertieren**.  Sie können auf die Suchschaltfläche klicken, um die Abfrage auszuführen, oder die Abfrage kopieren und an einer anderen Stelle verwenden.

![Sprachkonverter](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="resources"></a>angeben
Die [Dokumentationswebsite für die Log Analytics-Abfragesprache](https://docs.loganalytics.io) bietet alle Ressourcen, die Sie für einen schnellen Einstieg in die neue Sprache benötigen.  Dies schließt Tutorials, Beispiele und eine vollständige Sprachreferenz ein.


## <a name="cheat-sheet"></a>Cheat Sheet

Die folgende Tabelle zeigt einen Vergleich zwischen verschiedenen häufigen Abfragen für äquivalente Befehle in der neuen und der alten Abfragesprache in Azure Log Analytics.

| BESCHREIBUNG | Alt | Neu |
|:--|:--|:--|
| Alle Tabellen durchsuchen      | error | Suchen Sie „Fehler“ (ohne Berücksichtigung von Groß-/Kleinschreibung) |
| Auswählen von Daten aus einer Tabelle | Type=Event |  Ereignis |
|                        | Type=Event &#124; select Source, EventLog, EventID | Event &#124; project Source, EventLog, EventID |
|                        | Type=Event &#124; top 100 | Event &#124; take 100 |
| Zeichenfolgenvergleich      | Type=Event Computer=srv01.contoso.com   | Event &#124; where Computer == "srv01.contoso.com" |
|                        | Type=Event Computer=contains("contoso") | Event &#124; where Computer contains "contoso" (ohne Berücksichtigung von Groß-/Kleinschreibung)<br>Event &#124; where Computer contains_cs "Contoso" (unter Berücksichtigung von Groß-/Kleinschreibung) |
|                        | Type=Event Computer=RegEx("@contoso@")  | Event &#124; where Computer matches regex ".*contoso*" |
| Datumsvergleich        | Type=Event TimeGenerated > NOW-1DAYS | Event &#124; where TimeGenerated > ago(1d) |
|                        | Type=Event TimeGenerated>2017-05-01 TimeGenerated<2017-05-31 | Event &amp;#124; where TimeGenerated between (datetime(2017-05-01) . datetime(2017-05-31)) |
| Boolescher Vergleich     | Type=Heartbeat IsGatewayInstalled=false  | Heartbeat \| where IsGatewayInstalled == false |
| Sortieren                   | Type=Event &#124; sort Computer asc, EventLog desc, EventLevelName asc | Event \| sort by Computer asc, EventLog desc, EventLevelName asc |
| Unterscheiden               | Type=Event &#124; dedup Computer \| select Computer | Event &#124; summarize by Computer, EventLog |
| Erweitern von Spalten         | Type=Perf CounterName="% Processor Time" &#124; EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION | Perf &#124; where CounterName == "% Processor Time" \| extend Utilization = iff(CounterValue > 50, "HIGH", "LOW") |
| Aggregation            | Type=Event &#124; measure count() as Count by Computer | Event &#124; summarize Count = count() by Computer |
|                                | Type=Perf ObjectName=Processor CounterName="% Processor Time" &#124; measure avg(CounterValue) by Computer interval 5minute | Perf &#124; where ObjectName=="Processor" and CounterName=="% Processor Time" &#124; summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min) |
| Aggregation mit Grenzwert | Type=Event &#124; measure count() by Computer &#124; top 10 | Event &#124; summarize AggregatedValue = count() by Computer &#124; limit 10 |
| Union                  | Type=Event oder Type=Syslog | union Event, Syslog |
| Join                   | Type=NetworkMonitoring &#124; join inner AgentIP (Type=Heartbeat) ComputerIP | NetworkMonitoring &#124; join kind=inner (search Type == "Heartbeat") on $left.AgentIP == $right.ComputerIP |



## <a name="next-steps"></a>Nächste Schritte
- Sehen Sie sich ein [Tutorial zum Schreiben von Abfragen](https://go.microsoft.com/fwlink/?linkid=856078) an, in dem die neue Abfragesprache verwendet wird.
- Details zu allen Befehlen, Operatoren und Funktionen der neuen Abfragesprache finden Sie in der [Referenz zur Abfragesprache](https://go.microsoft.com/fwlink/?linkid=856079).  
