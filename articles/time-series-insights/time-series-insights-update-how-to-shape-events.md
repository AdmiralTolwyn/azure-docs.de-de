---
title: Strukturieren von Ereignissen – Azure Time Series Insights | Microsoft-Dokumentation
description: Erfahren Sie mehr über bewährte Methoden und das Formen von Ereignissen für Abfragen in der Vorschauversion von Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/16/2019
ms.custom: seodec18
ms.openlocfilehash: 567770c00c645aeb79e1efb0e9119b9ac829f3fe
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861658"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Strukturieren von Ereignissen in Azure Time Series Insights Preview

Dieser Artikel bietet Hilfestellung beim Strukturieren Ihrer JSON-Datei für die Erfassung und zum Maximieren der Effizienz Ihrer Azure Time Series Insights Preview-Abfragen.

## <a name="best-practices"></a>Bewährte Methoden

Sie berücksichtigen beim Senden von Ereignissen an Time Series Insights Preview verschiedene Aspekte. Insbesondere sollte Folgendes sichergestellt werden:

* Senden Sie Daten so effizient wie möglich über das Netzwerk.
* Speichern Sie Ihre Daten in einer Weise, die Ihnen dabei hilft, sie für Ihr Szenario besser geeignet zu aggregieren.

Gehen Sie wie folgt vor, um die optimale Abfrageleistung zu erzielen:

* Senden Sie keine unnötigen Eigenschaften. Die Abrechnung von Time Series Insights Preview erfolgt nach Nutzung. Es wird empfohlen, die Daten, die Sie abfragen möchten, zu speichern und zu verarbeiten.
* Verwenden Sie Instanzenfelder für statische Daten. Diese Vorgehensweise hilft Ihnen dabei, das Senden von statischen Daten über das Netzwerk zu vermeiden. Instanzenfelder, eine Komponente das Zeitreihenmodells, funktionieren wie Verweisdaten im Time Series Insights-Dienst, der allgemein verfügbar ist. Weitere Informationen zu Instanzenfeldern finden Sie unter [Zeitreihenmodell](./time-series-insights-update-tsm.md).
* Teilen Sie Dimensionseigenschaften zwischen zwei oder mehr Ereignissen. Diese Vorgehensweise hilft Ihnen dabei, Daten effizienter über das Netzwerk zu senden.
* Verwenden Sie keine tiefe Arrayschachtelung. Time Series Insights Preview unterstützt bis zu zwei Ebenen für geschachtelte Arrays, die Objekte enthalten. Time Series Insights Preview sorgt für flache Arrays in Nachrichten, indem eine Aufteilung in mehrere Ereignisse mit Eigenschaft-Wert-Paaren durchgeführt wird.
* Wenn nur wenige Messwerte für viele oder alle Ereignisse vorhanden sind, ist es besser, diese Messwerte als separate Eigenschaften innerhalb desselben Objekts zu senden. Durch das separate Senden wird die Anzahl von Ereignissen verringert und die Abfrageleistung möglicherweise gesteigert, weil weniger Ereignisse verarbeitet werden müssen.

Während der Erfassung werden Nutzlasten, die Schachtelungen enthalten, vereinfacht, sodass der Spaltenname ein Einzelwert mit einer Abgrenzung ist. Time Series Insights Preview verwendet Unterstriche zur Beschreibung. Beachten Sie, dass es sich hierbei um eine Änderung gegenüber der GA-Version des Produkts handelt, das Punkte verwendet. Während der Vorschauversion gibt es einen Vorbehalt hinsichtlich der Vereinfachung, der im zweiten Beispiel unten veranschaulicht wird.

## <a name="examples"></a>Beispiele

Das folgende Beispiel basiert auf einem Szenario, in dem zwei oder mehr Geräte Messungen oder Signale senden. Bei diesen Messungen oder Signalen kann es sich um Werte wie *Flussrate*, *Öldruck*, *Temperatur*und *Feuchtigkeit* handeln.

Im Beispiel wird eine einzelne Azure IoT Hub-Nachricht verwendet, bei der das äußere Array einen gemeinsam verwendeten Abschnitt für allgemeine Dimensionswerte enthält. Das äußere Array verwendet Zeitreiheninstanzdaten, um die Effizienz der Nachricht zu erhöhen. 

Die Zeitreiheninstanz enthält Gerätemetadaten. Diese Metadaten ändern sich nicht bei jedem Ereignis, stellen aber nützliche Eigenschaften für die Datenanalyse bereit. Um beim Senden über das Netzwerk Bytes einzusparen und somit die Effizienz der Nachricht zu steigern, erwägen Sie die Batchverarbeitung allgemeiner Dimensionswerte und die Verwendung von Zeitreiheninstanz-Metadaten.

### <a name="example-1"></a>Beispiel 1:

```JSON
[
  {
    "deviceId":"FXXX",
    "timestamp":"2018-01-17T01:17:00Z",
    "series":[
      {
        "Flow Rate ft3/s":1.0172575712203979,
        "Engine Oil Pressure psi ":34.7
      },
      {
        "Flow Rate ft3/s":2.445906400680542,
        "Engine Oil Pressure psi ":49.2
      }
    ]
  },
  {
    "deviceId":"FYYY",
    "timestamp":"2018-01-17T01:18:00Z",
    "series":[
      {
        "Flow Rate ft3/s":0.58015072345733643,
        "Engine Oil Pressure psi ":22.2
      }
    ]
  }
]
```

### <a name="time-series-instance"></a>Zeitreiheninstanz 

> [!NOTE]
> Die Time Series-ID ist *deviceId*.

```JSON
[
  {
    "timeSeriesId":[
      "FXXX"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"REVOLT SIMULATOR",
      "L2":"Battery System"
    }
  },
  {
    "timeSeriesId":[
      "FYYY"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"COMMON SIMULATOR",
      "L2":"Battery System"
    }
  }
]
```

Time Series Insights Preview verknüpft eine Tabelle (nach deren Vereinfachung) während der Abfragezeit. Die Tabelle enthält zusätzliche Spalten, z. B. **Typ**. Im folgenden Beispiel wird veranschaulicht, wie Sie Ihre Telemetriedaten [strukturieren](./time-series-insights-send-events.md#supported-json-shapes) können.

| deviceId  | type | L1 | L2 | timestamp | series_Flow Rate ft3/s | series_Engine Oil Pressure psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | Battery System | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   Battery System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    Battery System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

Beachten Sie im vorangehenden Beispiel die folgenden Punkte:

* Statische Eigenschaften werden in Time Series Insights Preview gespeichert, um über das Netzwerk gesendete Daten zu optimieren.
* Time Series Insights Preview-Daten werden zur Abfragezeit verknüpft, indem die in der Instanz definierte Time Series-ID verwendet wird.
* Es werden zwei Ebenen der Schachtelung verwendet. Diese Anzahl ist die höchste, die von Time Series Insights Preview unterstützt wird. Tief verschachtelte Arrays müssen vermieden werden.
* Da nur wenige Messwerte vorliegen, werden sie als separate Eigenschaften innerhalb desselben Objekts gesendet. In dem Beispiel sind **series_Flow Rate psi**, **series_Engine Oil Pressure psi** und **series_Flow Rate ft3/s** eindeutige Spalten.

>[!IMPORTANT]
> Instanzenfelder werden nicht mit Telemetriedaten gespeichert. Sie werden mit Metadaten im Zeitreihenmodell gespeichert.
> Die voranstehende Tabelle stellt die Abfrageansicht dar.

### <a name="example-2"></a>Beispiel 2:

Betrachten Sie das folgende JSON-Beispiel:

```JSON
{
  "deviceId": "FXXX",
  "timestamp": "2019-01-18T01:17:00Z",
  "data": {
        "flow": 1.0172575712203979,
    },
  "data_flow" : 1.76435072345733643
}
```
Im obigen Beispiel würde die vereinfachte `data_flow`-Eigenschaft einen Namenskonflikt mit der `data_flow`-Eigenschaft darstellen. In diesem Fall würde der *aktuellste* Eigenschaftswert den vorherigen überschreiben. Wenn dieses Verhalten eine Herausforderung für Ihre Geschäftsszenarien darstellt, wenden Sie sich an das TSI-Team.

> [!WARNING] 
> In Fällen, in denen doppelte Eigenschaften in derselben Ereignisnutzlast aufgrund der Vereinfachung oder einem anderen Mechanismus vorhanden sind, wird der neueste Eigenschaftswert gespeichert, der alle vorherigen Werte überschreibt.


## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Umsetzen dieser Richtlinien finden Sie unter [Azure Time Series Insights Preview-Abfragesyntax](./time-series-insights-query-data-csharp.md). Dort erfahren Sie mehr über die Abfragesyntax der REST-API in Time Series Insights Preview für den Datenzugriff.
- Informationen zu unterstützten JSON-Formen finden Sie unter [Unterstützte JSON-Formen](./time-series-insights-send-events.md#supported-json-shapes).
