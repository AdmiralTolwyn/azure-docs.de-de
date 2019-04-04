---
title: Workloadpriorität | Microsoft-Dokumentation
description: Anleitung zum Festlegen der Priorität von Abfragen in Azure SQL Data Warehouse
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: e53a6fcefb0f5370f6e24cc50fad2ad4ad4c64e3
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/14/2019
ms.locfileid: "57876225"
---
# <a name="sql-data-warehouse-workload-importance-preview"></a>Workloadpriorität (Vorschauversion) für SQL Data Warehouse

In diesem Artikel wird erläutert, wie sich die Workloadpriorität auf die Ausführungsreihenfolge von SQL Data Warehouse-Anforderungen auswirkt.

> [!Note]
> Die Workloadpriorität ist für SQL Data Warehouse Gen2 verfügbar.

## <a name="importance"></a>Wichtigkeit

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Unternehmensanforderungen können erfordern, dass Data Warehousing-Workloads über mehr Priorität verfügen als andere.  Stellen Sie sich ein Szenario vor, in dem unternehmenskritische Vertriebsdaten vor der Abrechnung des Geschäftsjahres geladen werden.  Datenladevorgänge für andere Quellen wie Wetterdaten verfügen über keine strikten SLAs (Vereinbarungen zum Servicelevel).   Durch Festlegen einer hohen Priorität für eine Anforderung zum Laden von Vertriebsdaten und einer niedrigen Priorität für eine Anforderung zum Laden von Wetterdaten wird sichergestellt, dass der Ladevorgang für die Vertriebsdaten zuerst auf Ressourcen zugreifen kann und schneller abgeschlossen wird.

## <a name="importance-levels"></a>Prioritätsstufen

Es gibt fünf Prioritätsstufen: „low“, „below_normal“, „normal“, „above_normal“ und „high“.  Anforderungen, für die keine Priorität festgelegt wird, weisen die Standardstufe „normal“ auf.  Anforderungen mit der gleichen Prioritätsstufe weisen das herkömmliche Planungsverhalten auf.

## <a name="importance-scenarios"></a>Prioritätsszenarios

Neben dem oben beschriebenen herkömmlichen Prioritätsszenario mit Vertriebs- und Wetterdaten gibt es weitere Szenarios, in denen die Workloadpriorität bei den Anforderungen für Datenverarbeitung und Abfragen hilft.

### <a name="locking"></a>Sperren

Der Zugriff auf Sperren für Lese- und Schreibaktivitäten ist ein Bereich, in dem natürliche Konflikte entstehen.  Aktivitäten wie [Partitionswechsel](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) oder [RENAME OBJECT](/sql/t-sql/statements/rename-transact-sql) erfordern Sperren mit erhöhten Rechten.  Ohne Workloadpriorität optimiert SQL Data Warehouse den Durchsatz.  Die Optimierung des Durchsatzes bedeutet, dass Anforderungen in der Warteschlange Anforderungen mit höheren Sperranforderungen umgehen können, die vorher in die Anforderungswarteschlange aufgenommen wurden, wenn ausgeführte Anforderungen und Anforderungen in der Warteschlange die gleichen Sperranforderungen und Ressourcen aufweisen.  Sobald die Workloadpriorität für Anforderungen mit höheren Sperranforderungen angewendet wurde, werden Anforderungen mit höherer Priorität vor Anforderungen mit niedriger Priorität ausgeführt.

Betrachten Sie das folgende Beispiel:

Die Abfrage „Q1“ wird aktiv ausgeführt und wählt Daten aus SalesFact aus.
Die Abfrage „Q2“ befindet sich in der Warteschlange und wartet darauf, dass die Abfrage „Q1“ abgeschlossen ist.  Die Abfrage „Q2“ wurde um 9:00 Uhr übermittelt und versucht, neue Daten per Partitionswechsel in SalesFact einzufügen.
Die Abfrage „Q3“ wurde um 9:01 Uhr übermittelt und soll Daten aus SalesFact auswählen.

Wenn die Abfragen „Q2“ und „Q3“ dieselbe Priorität aufweisen und „Q1“ immer noch ausgeführt wird, wird zuerst „Q3“ ausgeführt. Die Abfrage „Q2“ wartet weiterhin auf eine exklusive Sperre für SalesFact.  Wenn die Abfrage „Q2“ eine höhere Priorität als „Q3“ aufweist, muss die Abfrage „Q3“ auf den Abschluss von „Q2“ warten, bevor sie ausgeführt werden kann.

### <a name="non-uniform-requests"></a>Nicht einheitliche Anforderungen

Die Priorität ist auch zum Erfüllen von Abfrageanforderungen in Szenarios nützlich, in denen Anforderungen mit verschiedenen Ressourcenklassen übermittelt werden.  Wie bereits erwähnt, optimiert SQL Data Warehouse den Durchsatz bei gleicher Priorität.  Wenn Anforderungen mit gemischten Größen (z. B. „smallrc“ oder „mediumrc“) in die Warteschlange eingereiht werden, wählt SQL Data Warehouse die erste eingegangene Anforderung aus, die von den verfügbaren Ressourcen abgedeckt werden kann.  Wenn die Workloadpriorität angewendet wird, wird als Nächstes die Anforderung mit der höchsten Priorität geplant.
  
Betrachten Sie das folgende Beispiel für DW500c:

Mit „Q1“, „Q2“, „Q3“ und „Q4“ werden smallrc-Abfragen ausgeführt.
„Q5“ wird um 9:00 Uhr mit der Ressourcenklasse „mediumrc“ übermittelt.
„Q6“ wird um 9:01 Uhr mit der Ressourcenklasse „smallrc“ übermittelt.

Da die Abfrage „Q5“ die Klasse „mediumrc“ aufweist, erfordert sie zwei Parallelitätsslots.  Die Abfrage „Q5“ muss warten, bis zwei der aktiven Abfragen abgeschlossen sind.  Jedoch wird sofort „Q6“ geplant, da die Ressourcen zum Ausführen der Abfrage vorhanden sind, wenn eine der aktiven Abfragen (Q1-Q4) abgeschlossen wird.  Wenn „Q5“ über eine höhere Priorität als „Q6“ verfügt, wartet „Q6“ darauf, dass „Q5“ ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Workloadklassifizierung in SQL Data Warehouse finden Sie unter [SQL Data Warehouse Workload Classification](sql-data-warehouse-workload-classification.md) (Workloadklassifizierung in SQL Data Warehouse) und [Erstellen eines Workloadklassifizierers](quickstart-create-a-workload-classifier-tsql.md). Sie können die Abfragen und die zugewiesene Wichtigkeit unter [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) anzeigen.
