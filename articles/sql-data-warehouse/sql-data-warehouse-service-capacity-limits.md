---
title: SQL Data Warehouse – Kapazitätsgrenzen | Microsoft Docs
description: Maximale Werte für Verbindungen, Datenbanken, Tabellen und Abfragen für SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: ''
ms.assetid: e1eac122-baee-4200-a2ed-f38bfa0f67ce
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: reference
ms.date: 03/15/2018
ms.author: kevin;barbkess
ms.openlocfilehash: b1ff33f80a8dd0a0861a5c39731c9f59689db101
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/17/2018
---
# <a name="sql-data-warehouse-capacity-limits"></a>Kapazitätsgrenzen von SQL Data Warehouse
Die folgenden Tabellen erhalten die maximalen Werte, die für verschiedene Komponenten von Azure SQL Data Warehouse zulässig sind.

## <a name="workload-management"></a>Workloadverwaltung
| Category (Kategorie) | BESCHREIBUNG | Maximum |
|:--- |:--- |:--- |
| [Data Warehouse-Einheiten (Data Warehouse Unit, DWUs)][Data Warehouse Units (DWU)] |Max. DWUs für ein einzelnes SQL Data Warehouse | Optimiert für Elastizität-[Leistungsstufe](performance-tiers.md): DW6000<br></br>Optimiert für Compute-[Leistungsstufe](performance-tiers.md): DW30000c |
| [Data Warehouse-Einheiten (Data Warehouse Unit, DWUs)][Data Warehouse Units (DWU)] |Standard-DTU pro Server |54.000<br></br>Standardmäßig verfügt jede SQL Server-Instanz (z.B. „myserver.database.windows.net“) über ein Kontingent von 54.000 DTUs, das bis zu DW6000c zulässt. Bei diesem Kontingentwert handelt es sich einfach um ein Sicherheitslimit. Sie können Ihr Kontingent erhöhen, indem Sie ein [Supportticket erstellen][creating a support ticket] und als Anfragetyp *Kontingent* wählen.  Multiplizieren Sie zum Berechnen Ihrer DTU-Anforderungen die Anzahl der insgesamt benötigten DWUs mit 7,5, oder multiplizieren Sie die erforderlichen cDWUs mit 9,0. Beispiel: <br></br>DW6000 x 7,5 = 45.000 DTUs<br></br>DW600c x 9,0 = 54.000 DTUs<br></br>Sie können den aktuellen DTU-Verbrauch im Portal über die Option „SQL-Server“ anzeigen. Sowohl angehaltene als auch nicht angehaltene Datenbanken werden in das DTU-Kontingent eingerechnet. |
| Datenbankverbindung |Gleichzeitig geöffnete Sitzungen |1024<br/><br/>Jeder der 1024 aktiven Sitzungen kann gleichzeitig Anforderungen an eine SQL Data Warehouse-Datenbank senden. Beachten Sie, dass die Anzahl der Abfragen begrenzt ist, die gleichzeitig ausgeführt werden können. Wenn der Grenzwert überschritten wird, gelangt die Anforderung in eine interne Warteschlange, in der sie auf die Verarbeitung wartet. |
| Datenbankverbindung |Maximaler Arbeitsspeicher für vorbereitete Anweisungen |20 MB |
| [Workloadverwaltung][Workload management] |Maximale Anzahl gleichzeitiger Abfragen |32<br/><br/> SQL Data Warehouse kann standardmäßig maximal 32 gleichzeitige Abfragen ausführen. Darüber hinausgehende Abfragen werden in eine Warteschlange eingereiht.<br/><br/>Die Anzahl der gleichzeitigen Abfragen kann abnehmen, wenn Benutzer höheren Ressourcenklassen zugeordnet werden oder wenn SQL Data Warehouse einen niedrigeren [Servicelevel](performance-tiers.md#service-levels) aufweist. Einige Abfragen, wie DMV-Abfragen, dürfen immer ausgeführt werden. |
| [tempdb][Tempdb] |Maximale GB |399GB pro DW100. Daher ist „tempdb“ in DWU1000 3,99 TB groß. |

## <a name="database-objects"></a>Datenbankobjekte
| Category (Kategorie) | BESCHREIBUNG | Maximum |
|:--- |:--- |:--- |
| Datenbank |Max. Größe |240 TB komprimiert auf dem Datenträger<br/><br/>Dieser Speicherplatz ist unabhängig von „tempdb“ oder vom Protokollspeicherplatz und daher für permanente Tabellen reserviert.  Komprimierung von gruppiertem Columnstore wird auf 5X geschätzt.  Diese Komprimierung ermöglicht der Datenbank einen Zuwachs auf ungefähr 1PB, wenn alle Tabellen mit gruppiertem Columnstore konfiguriert sind (die Standardtabellentyp). |
| Table |Max. Größe |60 TB komprimiert auf dem Datenträger |
| Table |Tabellen pro Datenbank |2 Milliarden |
| Table |Spalten pro Tabelle |1024 Spalten |
| Table |Bytes pro Spalte |Abhängig von der Spalte [Datentyp][data type].  Grenzwert ist 8.000 für Char-Datentypen, 4.000 für Nvarchar oder 2GB für MAX-Datentypen. |
| Table |Bytes pro Zeile, definierte Größe |8.060 Bytes<br/><br/>Die Anzahl von Bytes pro Zeile wird auf die gleiche Weise wie bei SQL Server mit aktivierter Seitenkomprimierung berechnet. Wie SQL Server unterstützt SQL Data Warehouse die Speicherung von Zeilenüberlaufsdaten, sodass **Spalten variabler Länge** aus der Zeile verschoben werden können. Wenn Zeilen variabler Länge aus der Zeile verschoben werden, wird nur der 24-Byte-Stamm im Hauptdatensatz gespeichert. Weitere Informationen finden Sie in [Zeilenüberlauf bei Daten über 8 KB][Row-Overflow Data Exceeding 8 KB]. |
| Table |Partitionen pro Tabelle |15.000<br/><br/>Um eine hohe Leistung zu erzielen, empfehlen wir, die Anzahl der Partitionen zu minimieren, die Sie zum Erfüllen Ihrer Geschäftsanforderungen benötigen. Mit einer steigenden Anzahl von Partitionen wächst der Verarbeitungsaufwand für Datendefinitionssprache (DDL)- und Datenbearbeitungssprache (DML)-Vorgänge, was zu Leistungseinbußen führt. |
| Table |Zeichen pro Partitionsbegrenzungswert. |4000 |
| Index |Nicht gruppierte Indizes pro Tabelle. |999<br/><br/>Gilt nur für Rowstore-Tabellen |
| Index |Gruppierte Indizes pro Tabelle. |1<br><br/>Gilt sowohl für Rowstore- als auch für Columnstore-Tabellen |
| Index |Größe des Indexschlüssels. |900 Bytes<br/><br/>Gilt nur für Rowstore-Indizes.<br/><br/>Indizes für „varchar“-Spalten mit einer maximalen Größe von mehr als 900 Bytes können erstellt werden, wenn die vorhandenen Daten in den Spalten bei der Indexerstellung nicht größer als 900 Bytes sind. Anschließende auf die Spalten angewendete INSERT- oder UPDATE-Anweisungen, die bewirken, dass die Gesamtgröße 900 Bytes überschreitet, haben allerdings keinen Erfolg. |
| Index |Schlüsselspalten pro Index. |16<br/><br/>Gilt nur für Rowstore-Indizes. Gruppierte Columnstore-Indizes enthalten alle Spalten. |
| Statistiken |Größe der kombinierten Spaltenwerte. |900 Bytes |
| Statistiken |Spalten pro Statistikobjekt. |32 |
| Statistiken |Für Spalten pro Tabelle erstellte Statistiken. |30.000 |
| Gespeicherte Prozeduren |Maximale Schachtelungsebenen. |8 |
| Sicht |Spalten pro Sicht |1024 |

## <a name="loads"></a>Lädt
| Category (Kategorie) | BESCHREIBUNG | Maximum |
|:--- |:--- |:--- |
| PolyBase-Auslastung |MB pro Zeile |1<br/><br/>PolyBase lädt nur Zeilen, die kleiner als 1 MB sind, und kann nicht in Spalten des Typs VARCHR(MAX), NVARCHAR(MAX) oder VARBINARY(MAX) laden.<br/><br/> |

## <a name="queries"></a>Abfragen
| Category (Kategorie) | BESCHREIBUNG | Maximum |
|:--- |:--- |:--- |
| Abfragen |In Warteschlange gestellte Abfragen von Benutzertabellen. |1000 |
| Abfragen |Gleichzeitige Abfragen von Systemsichten. |100 |
| Abfragen |In Warteschlange gestellte Abfragen von Systemsichten |1000 |
| Abfragen |Maximale Parameter |2098 |
| Batch |Maximale Größe |65.536*4096 |
| SELECT-Ergebnisse |Spalten pro Zeile |4096<br/><br/>Das Ergebnis einer SELECT-Anweisung kann nie mehr als 4.096 Spalten pro Zeile enthalten. Es gibt keine Garantie, dass Sie stets über 4096 verfügen. Wenn der Abfrageplan eine temporäre Tabelle erfordert, gilt möglicherweise der Maximalwert von 1024 Spalten pro Tabelle. |
| SELECT |Geschachtelte Unterabfragen |32<br/><br/>In einer SELECT-Anweisung sind maximal 32 geschachtelte Unterabfragen zulässig. Es gibt keine Garantie, dass Sie stets über 32 verfügen. Ein JOIN-Befehl kann z. B. eine Unterabfrage in den Abfrageplan einführen. Die Anzahl der Unterabfragen kann auch durch den verfügbaren Speicher eingeschränkt werden. |
| SELECT |Spalten pro JOIN |1024 Spalten<br/><br/>Für einen JOIN sind maximal 1.024 Spalten zulässig. Es gibt keine Garantie, dass Sie stets über 1024 verfügen. Wenn der JOIN-Plan eine temporäre Tabelle mit mehr Spalten als das JOIN-Ergebnis erfordert, gilt die Grenze von 1024 für die temporäre Tabelle. |
| SELECT |Bytes pro GROUP BY-Spalten. |8.060<br/><br/>Die Maximalgröße von Spalten in der GROUP BY-Klausel beträgt 8.060 Bytes. |
| SELECT |Bytes pro ORDER BY-Spalten |8.060<br/><br/>Die Maximalgröße von Spalten in der ORDER BY-Klausel beträgt 8.060 Bytes. |
| Bezeichner und Konstanten pro Anweisung |Anzahl der Bezeichner und Konstanten, auf die verwiesen wird. |65.535<br/><br/>SQL Data Warehouse beschränkt die Anzahl von Bezeichnern und Konstanten, die in einem einzelnen Ausdruck einer Abfrage enthalten sein können. Dieser Grenzwert ist 65.535. Das Überschreiten dieses Werts führt zum SQL Server-Fehler 8632. Weitere Informationen finden Sie unter [Interner Fehler: ein Ausdrucksdienstelimit wurde erreicht][Internal error: An expression services limit has been reached]. |

## <a name="metadata"></a>Metadaten
| Systemsicht | Maximale Anzahl von Zeilen |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10.000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Gesamtanzahl der DMS-Worker für die letzten 1000 SQL-Anforderungen. |
| sys.dm_pdw_errors |10.000 |
| sys.dm_pdw_exec_requests |10.000 |
| sys.dm_pdw_exec_sessions |10.000 |
| sys.dm_pdw_request_steps |Die Gesamtzahl der Schritte für die letzten 1000 SQL-Anforderungen, die in „sys.dm_pdw_exec_requests“ gespeichert sind. |
| sys.dm_pdw_os_event_logs |10.000 |
| sys.dm_pdw_sql_requests |Die letzten 1000 SQL-Anforderungen, die in „sys.dm_pdw_exec_requests“ gespeichert sind. |

## <a name="next-steps"></a>Nächste Schritte
Weitere Referenzinformationen finden Sie unter [SQL Data Warehouse-Referenz – Übersicht][SQL Data Warehouse reference overview].

<!--Image references-->

<!--Article references-->
[Data Warehouse Units (DWU)]: ./sql-data-warehouse-overview-what-is.md
[SQL Data Warehouse reference overview]: ./sql-data-warehouse-overview-reference.md
[Workload management]: ./resource-classes-for-workload-management.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[data type]: ./sql-data-warehouse-tables-data-types.md
[creating a support ticket]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Row-Overflow Data Exceeding 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[Internal error: An expression services limit has been reached]: https://support.microsoft.com/kb/913050
