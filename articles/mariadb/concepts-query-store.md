---
title: Abfragespeicher in Azure Database for MariaDB
description: In diesem Artikel wird das Abfragespeicherfeature in Azure Database for MariaDB beschrieben.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 883f780059e38c53dedda309dd059cc714539f80
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462090"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>Überwachen der Leistung von Azure Database for MariaDB mit dem Abfragespeicher

**Gilt für:**  Azure Database for MariaDB 10.2

> [!NOTE]
> Der Abfragespeicher befindet sich in der Vorschauphase.

Das Abfragespeicherfeature in Azure Database for MariaDB ermöglicht die Nachverfolgung der Abfrageleistung im Zeitverlauf. Der Abfragespeicher vereinfacht das Beheben von Leistungsproblemen, da er es Ihnen ermöglicht, die am längsten ausgeführten und ressourcenintensivsten Abfragen schnell zu ermitteln. Der Abfragespeicher erfasst automatisch einen Verlauf der Abfragen und Laufzeitstatistiken und bewahrt diese auf, damit Sie sie überprüfen können. Er unterteilt die Daten nach Zeitfenstern, damit Sie Verwendungsmuster für Datenbanken erkennen können. Die Daten für alle Benutzer, Datenbanken und Abfragen werden in der Schemadatenbank **mysql** der Azure Database for MariaDB-Instanz gespeichert.

## <a name="common-scenarios-for-using-query-store"></a>Gängige Szenarien für die Verwendung des Abfragespeichers

Der Abfragespeicher kann in unterschiedlichen Szenarien genutzt werden, z. B.:

- Erkennen von zurückgestellten Abfragen
- Bestimmen der Häufigkeit zum Ausführen einer Abfrage in einem angegebenen Zeitfenster
- Vergleichen der durchschnittlichen Ausführungsdauer einer Abfrage für bestimmte Zeitfenster zum Anzeigen großer Abweichungen

## <a name="enabling-query-store"></a>Aktivieren des Abfragespeichers

Der Abfragespeicher ist ein optionales Feature. Daher ist er auf einem Server nicht standardmäßig aktiviert. Der Abfragespeicher wird global für alle Datenbanken auf einem bestimmten Server aktiviert oder deaktiviert. Eine Aktivierung oder Deaktivierung für einzelne Datenbanken ist nicht möglich.

### <a name="enable-query-store-using-the-azure-portal"></a>Aktivieren des Abfragespeichers über das Azure-Portal

1. Melden Sie sich beim Azure-Portal an, und wählen Sie Ihren Azure Database for MariaDB-Server aus.
1. Wählen Sie im Abschnitt **Einstellungen** des Menüs die Option  **Serverparameter** aus.
1. Suchen Sie nach dem Parameter „query_store_capture_mode“.
1. Legen Sie den Wert auf „ALL“ fest, und wählen Sie **Speichern** aus.

So aktivieren Sie Wartestatistiken in Ihrem Abfragespeicher:

1. Suchen Sie nach dem Parameter „query_store_wait_sampling_capture_mode“.
1. Legen Sie den Wert auf „ALL“ fest, und wählen Sie **Speichern** aus.

Es kann bis zu 20 Minuten dauern, bis der erste Datenbatch in der mysql-Datenbank gespeichert wurde.

## <a name="information-in-query-store"></a>Informationen im Abfragespeicher

Der Abfragespeicher verfügt über zwei Speicher:

- Einen Laufzeitstatistikspeicher zur Aufbewahrung der Statistikinformationen im Zusammenhang mit der Abfrageausführung
- Einen Wartestatistikspeicher zur Aufbewahrung von Wartestatistikinformationen

Um die Speicherverwendung zu minimieren, wird die Laufzeitausführungsstatistik im Laufzeitstatistikspeicher für ein festes, konfigurierbares Zeitfenster aggregiert. Die Informationen in diesem Speicher können durch Abfragen der Abfragespeicheransichten angezeigt werden.

Die folgende Abfrage gibt Informationen über Abfragen im Abfragespeicher zurück:

```sql
SELECT * FROM mysql.query_store;
```

Die folgende Abfrage wird für die Wartestatistik verwendet:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Suchen von Warteanfragen

Warteereignistypen kombinieren verschiedene Warteereignisse nach Ähnlichkeit in Buckets. Der Abfragespeicher enthält den Warteereignistyp, den spezifischen Warteereignisnamen und die entsprechende Abfrage. Die Möglichkeit zum Korrelieren dieser Warteinformationen mit den Abfragelaufzeitstatistiken bedeutet, dass Sie einen ausführlicheren Überblick darüber erhalten, welche Faktoren sich auf die Abfrageleistung auswirken.

Im Folgenden finden Sie einige Beispiele dafür, wie Sie mithilfe der Wartestatistiken im Abfragespeicher weitere Erkenntnisse zur Ihrer Workload erhalten:

| **Beobachtung** | **Aktion** |
|---|---|
|Lange Sperrwartevorgänge | Überprüfen Sie die Abfragetexte der betroffenen Abfragen, und identifizieren Sie die Zielentitäten. Suchen Sie im Abfragespeicher nach anderen Abfragen, die die gleiche Entität ändern, welche häufig ausgeführt wird bzw. eine lange Dauer aufweist. Nachdem Sie diese Abfragen ermittelt haben, ändern Sie ggf. die Anwendungslogik, um die Parallelität zu verbessern, oder verwenden Sie eine weniger restriktive Isolationsstufe. |
|Lange Puffer-E/A-Wartevorgänge | Suchen Sie die Abfragen mit einer hohen Anzahl an physischen Lesevorgängen im Abfragespeicher. Wenn diese mit Abfragen mit langen E/A-Wartevorgängen übereinstimmen, führen Sie ggf. einen Index für die zugrunde liegenden Entität ein, um Suchvorgänge (anstelle von Scanvorgängen) auszuführen. Dies verringert den E/A-Aufwand der Abfragen. Überprüfen Sie die **Leistungsempfehlungen** für Ihren Server im Portal, um festzustellen, ob Indexempfehlungen für diesen Server vorhanden sind, mit denen sich die Abfragen ggf. optimieren lassen. |
|Lange Arbeitsspeicher-Wartevorgänge | Suchen Sie die im Abfragespeicher die speicherintensivsten Abfragen. Diese Abfragen verzögern wahrscheinlich zusätzlich den Fortschritt der betroffen Abfragen. Überprüfen Sie die **Leistungsempfehlungen** für Ihren Server im Portal, um festzustellen, ob Indexempfehlungen vorhanden sind, mit denen sich diese Abfragen ggf. optimieren lassen.|

## <a name="configuration-options"></a>Konfigurationsoptionen

Wenn der Abfragespeicher aktiviert ist, speichert er Daten in Aggregationsfenstern von 15 Minuten mit bis zu 500 unterschiedlichen Abfragen pro Fenster.

Die folgenden Optionen stehen für die Konfiguration der Abfragespeicherparameter zur Verfügung.

| **Parameter** | **Beschreibung** | **Standard** | **Bereich** |
|---|---|---|---|
| query_store_capture_mode | Schaltet die Abfragespeicherfunktion basierend auf dem Wert ein oder aus. Hinweis: Bei ausgeschaltetem Leistungsschema (performance_schema) werden durch Aktivieren von „query_store_capture_mode“ das Leistungsschema und ein Teil der Leistungsschemainstrumente aktiviert, die für dieses Feature benötigt werden. | ALL | NONE, ALL |
| query_store_capture_interval | Das Erfassungsintervall des Abfragespeichers in Minuten. Ermöglicht das Angeben des Intervalls, nach dem die Abfragemetriken aggregiert werden. | 15 | 5–60 |
| query_store_capture_utility_queries | Kann ein- oder ausgeschaltet werden, um die Erfassung aller Hilfsabfragen, die im System ausgeführt werden, zu aktivieren oder zu deaktivieren. | NO | YES, NO |
| query_store_retention_period_in_days | Zeitfenster in Tagen für die Aufbewahrung der Daten im Abfragespeicher. | 7 | 1 – 30 |

Die folgenden Optionen gelten speziell für Wartestatistiken.

| **Parameter** | **Beschreibung** | **Standard** | **Bereich** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Ermöglicht das Ein-/Ausschalten der Wartestatistik. | NONE | NONE, ALL |
| query_store_wait_sampling_frequency | Ändert die Frequenz der Stichprobenentnahme für Wartezeiten in Sekunden. Möglicher Bereich: zwischen fünf und 300 Sekunden. | 30 | 5–300 |

> [!NOTE]
> **query_store_capture_mode** hat aktuell Vorrang vor dieser Konfiguration. Das bedeutet, dass sowohl **query_store_capture_mode** als auch **query_store_wait_sampling_capture_mode** auf „ALL“ festgelegt sein müssen, damit die Wartestatistik funktioniert. Wenn **query_store_capture_mode** deaktiviert ist, ist auch die Wartestatistik deaktiviert, da für die Wartestatistik das aktivierte Leistungsschema (performance_schema) und der vom Abfragespeicher erfasste Abfragetext (query_text) verwendet werden.

Verwenden Sie das [Azure-Portal](howto-server-parameters.md), um für einen Parameter einen anderen Wert abzurufen oder festzulegen.

## <a name="views-and-functions"></a>Ansichten und Funktionen

Mithilfe der folgenden Ansichten und Funktionen können Sie den Abfragespeicher anzeigen und verwalten. Mit der [öffentlichen Rolle für die Auswahl von Berechtigungen](howto-create-users.md#create-additional-admin-users) kann jeder diese Ansichten verwenden, um die Daten im Abfragespeicher anzuzeigen. Diese Ansichten sind nur in der **mysql**-Datenbank verfügbar.

Abfragen werden normalisiert, indem ihre Struktur nach dem Entfernen von Literalen und Konstanten untersucht wird. Wenn zwei Abfragen mit Ausnahme von Literalwerten identisch sind, haben sie denselben Hash.

### <a name="mysqlquerystore"></a>mysql.query_store

In dieser Ansicht werden alle Daten im Abfragespeicher zurückgegeben. Es gibt eine Zeile für jede einzelne Datenbank-ID, Benutzer-ID und Abfrage-ID.

| **Name** | **Datentyp** | **IS_NULLABLE** | **Beschreibung** |
|---|---|---|---|
| `schema_name`| varchar(64) | NO | Name des Schemas |
| `query_id`| bigint(20) | NO| Eindeutige ID, die für die spezifische Abfrage generiert wird. Wenn die gleiche Abfrage in einem anderen Schema ausgeführt wird, wird eine neue ID generiert. |
| `timestamp_id` | timestamp| NO| Zeitstempel für die Ausführung der Abfrage. Dieser Wert basiert auf der Konfiguration von „query_store_interval“.|
| `query_digest_text`| longtext| NO| Der normalisierte Abfragetext nach dem Entfernen aller Literale.|
| `query_sample_text` | longtext| NO| Erste Darstellung der eigentlichen Abfrage mit Literalen.|
| `query_digest_truncated` | bit| YES| Gibt an, ob der Abfragetext gekürzt wurde. Der Wert lautet „YES“, wenn die Abfrage länger als 1 KB ist.|
| `execution_count` | bigint(20)| NO| Gibt an, wie oft die Abfrage für diese Zeitstempel-ID bzw. während des konfigurierten Intervallzeitraums ausgeführt wurde.|
| `warning_count` | bigint(20)| NO| Anzahl von Warnungen, die von dieser Abfrage während des Intervalls generiert wurden.|
| `error_count` | bigint(20)| NO| Anzahl von Fehlern, die von dieser Abfrage während des Intervalls generiert wurden.|
| `sum_timer_wait` | double| YES| Gesamte Ausführungsdauer dieser Abfrage während des Intervalls.|
| `avg_timer_wait` | double| YES| Durchschnittliche Ausführungsdauer dieser Abfrage während des Intervalls.|
| `min_timer_wait` | double| YES| Minimale Ausführungszeit für diese Abfrage.|
| `max_timer_wait` | double| YES| Maximale Ausführungszeit.|
| `sum_lock_time` | bigint(20)| NO| Gesamte Zeit, die für alle Sperren für diese Abfrageausführung während dieses Zeitfensters aufgewendet wurde.|
| `sum_rows_affected` | bigint(20)| NO| Anzahl betroffener Zeilen.|
| `sum_rows_sent` | bigint(20)| NO| Anzahl von Zeilen, die an den Client gesendet wurden.|
| `sum_rows_examined` | bigint(20)| NO| Anzahl untersuchter Zeilen.|
| `sum_select_full_join` | bigint(20)| NO| Anzahl vollständiger Verknüpfungen.|
| `sum_select_scan` | bigint(20)| NO| Anzahl ausgewählter Scans. |
| `sum_sort_rows` | bigint(20)| NO| Anzahl sortierter Zeilen.|
| `sum_no_index_used` | bigint(20)| NO| Anzahl der Fälle, in denen von der Abfrage keine Indizes verwendet wurden.|
| `sum_no_good_index_used` | bigint(20)| NO| Anzahl der Fälle, in denen die Abfrageausführungs-Engine keine guten Indizes verwendet hat.|
| `sum_created_tmp_tables` | bigint(20)| NO| Gesamtzahl erstellter temporärer Tabellen.|
| `sum_created_tmp_disk_tables` | bigint(20)| NO| Gesamtzahl temporärer Tabellen, die auf dem Datenträger erstellt wurden (E/A-Generierung).|
| `first_seen` | timestamp| NO| Erstes Auftreten der Abfrage (UTC) während des Aggregationsfensters.|
| `last_seen` | timestamp| NO| Letztes Auftreten der Abfrage (UTC) während dieses Aggregationsfensters.|

### <a name="mysqlquerystorewaitstats"></a>mysql.query_store_wait_stats

Diese Ansicht gibt Warteereignisdaten im Abfragespeicher zurück. Es gibt eine Zeile für jede einzelne Datenbank-ID, Benutzer-ID und jedes Ereignis.

| **Name**| **Datentyp** | **IS_NULLABLE** | **Beschreibung** |
|---|---|---|---|
| `interval_start` | timestamp | NO| Start des Intervalls (15-Minuten-Inkrement).|
| `interval_end` | timestamp | NO| Ende des Intervalls (15-Minuten-Inkrement).|
| `query_id` | bigint(20) | NO| Generierte eindeutige ID in der normalisierten Abfrage (aus Abfragespeicher).|
| `query_digest_id` | varchar(32) | NO| Der normalisierte Abfragetext nach dem Entfernen aller Literale (aus dem Abfragespeicher). |
| `query_digest_text` | longtext | NO| Erste Darstellung der eigentlichen Abfrage mit Literalen (aus dem Abfragespeicher). |
| `event_type` | varchar(32) | NO| Kategorie des Warteereignisses. |
| `event_name` | varchar(128) | NO| Name des Warteereignisses. |
| `count_star` | bigint(20) | NO| Anzahl von Warteereignissen, für die während des Intervalls Stichproben für die Abfrage entnommen wurden. |
| `sum_timer_wait_ms` | double | NO| Gesamte Wartezeit dieser Abfrage während des Intervalls (in Millisekunden). |

### <a name="functions"></a>Functions

| **Name**| **Beschreibung** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Löscht alle Abfragespeicherdaten vor dem angegebenen Zeitstempel. |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Löscht alle Warteereignisdaten vor dem angegebenen Zeitstempel. |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Löscht Empfehlungen, die vor dem angegebenen Zeitstempel ablaufen. |

## <a name="limitations-and-known-issues"></a>Einschränkungen und bekannte Probleme

- Wenn für einen MariaDB-Server der Parameter `default_transaction_read_only` aktiviert ist, kann der Abfragespeicher keine Daten erfassen.
- Die Funktion des Abfragespeichers kann durch lange Unicodeabfragen (\>= 6.000 Bytes) unterbrochen werden.
- Der Aufbewahrungszeitraum für Wartestatistiken beträgt 24 Stunden.
- Für Wartestatistiken werden Stichproben verwendet, um einen Teil der Ereignisse zu erfassen. Die Häufigkeit kann mit dem Parameter `query_store_wait_sampling_frequency` geändert werden.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Query Performance Insight in Azure Database for MariaDB](concepts-query-performance-insight.md).
