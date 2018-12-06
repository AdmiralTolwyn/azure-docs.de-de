---
title: Auswählen der richtigen Konsistenzebene für Ihre Anwendung mit Azure Cosmos DB | Microsoft-Dokumentation
description: Auswählen der richtigen Konsistenzebene für Ihre Anwendung in Azure Cosmos DB.
keywords: Konsistenz, Leistung, Azure Cosmos DB, Azure, Microsoft Azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2018
ms.author: mjbrown
ms.openlocfilehash: 42128a05ad9f82ff6b202eb6566c1fea60caa760
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52162421"
---
# <a name="choose-the-right-consistency-level-for-your-application"></a>Auswählen der richtigen Konsistenzebene für Ihre Anwendung

Verteilte Datenbanken, die auf Replikation für Hochverfügbarkeit, niedrige Latenzzeiten oder beides angewiesen sind, bilden den grundlegenden Kompromiss zwischen Lesekonsistenz und Verfügbarkeit, Latenz und Durchsatz. Die meisten kommerziell verfügbaren verteilten Datenbanken verlangen von Entwicklern, dass sie zwischen den beiden extremen Konsistenzmodellen wählen: starke Konsistenz und letztliche Konsistenz. Azure Cosmos DB bietet Entwicklern die Auswahl zwischen fünf klar definierten Konsistenzmodellen: starke Konsistenz, begrenzte Veraltung, Sitzungskonsistenz, Präfixkonsistenz und letztliche Konsistenz. Jedes dieser Konsistenzmodelle ist klar definiert, intuitiv und kann für spezifische reale Szenarien verwendet werden. Jedes der fünf Konsistenzmodelle bietet [Verfügbarkeits- und Leistungskompromisse](consistency-levels-tradeoffs.md) und wird durch umfassende SLAs abgesichert. Die folgenden einfachen Überlegungen helfen Ihnen, in vielen gängigen Szenarien die richtige Wahl zu treffen.

## <a name="sql-api-and-table-api"></a>SQL-API und Tabellen-API

Beachten Sie die folgenden Punkte, wenn Ihre Anwendung mit der Cosmos DB-SQL-API oder der Tabellen-API erstellt wird.

- Für viele reale Szenarien ist Sitzungskonsistenz optimal und die empfohlene Option. Weitere Informationen finden Sie unter [Verwalten des Sitzungstokens für Ihre Anwendung](how-to-manage-consistency.md#utilize-session-tokens).

- Wenn Ihre Anwendung eine starke Konsistenz erfordert, wird empfohlen, die Konsistenzebene „begrenzte Veraltung“ zu verwenden.

- Wenn Sie strengere Konsistenzgarantien benötigen als die, die durch Sitzungskonsistenz und Latenz im einstelligen Millisekundenbereich für Schreibvorgänge bereitgestellt werden, wird empfohlen, die Konsistenzebene mit begrenzter Veraltung zu verwenden.  

- Wenn Ihre Anwendung letztliche Konsistenz erfordert, wird empfohlen, die Konsistenzebene „Präfixkonsistenz“ zu verwenden.

- Wenn Sie weniger strenge Konsistenzgarantien als mit Sitzungskonsistenz benötigen, wird empfohlen, die Konsistenzebene „Präfixkonsistenz“ zu verwenden.

- Wenn Sie die höchste Verfügbarkeit und niedrigste Latenz benötigen, verwenden Sie die letztliche Konsistenzebene.

## <a name="cassandra-mongodb-and-gremlin-api"></a>Cassandra-, MongoDB- und Gremlin-API

- Weitere Informationen zum Mapping zwischen der „Lesekonsistenzebene“ der Apache Cassandra- und Cosmos DB-Konsistenzebenen finden Sie unter [Konsistenzebenen und Cosmos DB-APIs](consistency-levels-across-apis.md#cassandra-mapping).

- Informationen zur Zuordnung zwischen dem „Leseinteresse“ der MongoDB- und Azure Cosmos DB-Konsistenzebenen finden Sie unter [Konsistenzebenen und Cosmos DB-APIs](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>Konsistenzgarantien in der Praxis

In der Praxis erhalten Sie möglicherweise stärkere Konsistenzgarantien. Die Konsistenzgarantien für einen Lesevorgang entsprechen der Aktualität und der Sortierung des Zustands der angeforderten Datenbank. Die Lesekonsistenz ist an die Sortierung und Verbreitung der Schreib-/Aktualisierungsvorgänge gebunden.  

* Wenn die Konsistenzebene auf **begrenzte Veraltung** festgelegt ist, garantiert Cosmos DB, dass die Clients immer den Wert eines vorherigen Schreibvorgangs mit einer Verzögerung lesen, die durch das Veraltungsfenster gebunden wird.

* Wenn die Konsistenzebene auf **stark** festgelegt ist, ist das Veraltungszeitfenster gleich Null, und für die Clients wird garantiert, dass sie den letzten Wert des Schreibvorgangs lesen, für den ein Commit ausgeführt wurde.

* Für die verbleibenden drei Konsistenzebenen ist das Veraltungsfenster weitgehend von Ihrer Workload abhängig. Wenn beispielsweise keine Schreibvorgänge für die Datenbank ausgeführt werden, liefert ein Lesevorgang mit der Konsistenzebene **letztliche Konsistenz**, **Sitzungskonsistenz** oder **Präfixkonsistenz** wahrscheinlich die gleichen Ergebnisse wie ein Lesevorgang mit starker Konsistenzebene.

Wenn Ihr Cosmos DB-Konto mit einer anderen Konsistenzebene als der starken Konsistenz konfiguriert ist, können Sie anhand der PBS-Metrik (Probabilistic Bounded Staleness) die Wahrscheinlichkeit ermitteln, dass Ihre Clients starke und konsistente Lesevorgänge für Ihre Workloads erzielen. Diese Metrik wird im Azure-Portal verfügbar gemacht. Weitere Informationen finden Sie unter [Überwachen der PBS-Metrik (Probabilistically Bounded Staleness)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

PBS (Probabilistic Bounded Staleness) zeigt, wie letztendlich Ihre letztendliche Konsistenz ist. Diese Metrik gibt Aufschluss darüber, wie oft Sie eine stärkere Konsistenz erreichen können als die Konsistenzebene, die Sie aktuell in Ihrem Cosmos DB-Konto konfiguriert haben. Das heißt, Sie sehen die Wahrscheinlichkeit (gemessen in Millisekunden), dass stark konsistente Lesevorgänge in Regionen mit Schreib- und Lesevorgängen auftreten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Konsistenzebenen erhalten Sie in den folgenden Artikeln:

* [Zuordnung der Konsistenzebenen in Cosmos DB-APIs](consistency-levels-across-apis.md)
* [Kompromisse in Bezug auf Verfügbarkeit und Leistung für verschiedene Konsistenzebenen](consistency-levels-tradeoffs.md)
* [Verwalten des Sitzungstokens für Ihre Anwendung](how-to-manage-consistency.md#utilize-session-tokens)
* [Überwachen der PBS-Metrik (Probabilistically Bounded Staleness)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)