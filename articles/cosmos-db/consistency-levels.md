---
title: Konsistenzebenen in Azure Cosmos DB | Microsoft-Dokumentation
description: Azure Cosmos DB bietet fünf Konsistenzebenen, um für vorhersehbare Kompromisse zwischen Konsistenz, Verfügbarkeit und Latenz sorgen zu können.
keywords: Letztliche Konsistenz, Azure Cosmos DB, Azure, Microsoft Azure
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: andrl
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e9503759b776bb045c4dc0357b1ab88be1294013
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038253"
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Einstellbare Datenkonsistenzebenen in Azure Cosmos DB
Azure Cosmos DB ist für jedes Datenmodell von Grund auf im Hinblick auf eine globale Verteilung konzipiert. Es bietet planbare Garantien für geringe Wartezeiten sowie mehrere klar definierte gelockerte Konsistenzmodelle. Azure Cosmos DB bietet derzeit fünf Konsistenzebenen: Stark, Begrenzte Veraltung, Sitzung, Konsistentes Präfix und Letztlich. „Begrenzte Veraltung“, „Sitzung“, „Präfixkonsistenz“ und „Letztlich“ werden als gelockerte Konsistenzmodelle bezeichnet, da sie weniger Konsistenz bieten als „Stark“ (das Modell mit der höchstmöglichen Konsistenz). 

Neben den Konsistenzmodellen **Stark** und **Letztlich**, die bei verteilten Datenbanken üblich sind, bietet Azure Cosmos DB drei weitere sorgfältig programmierte und operationalisierte Modelle: **Begrenzte Veraltung**, **Sitzung** und **Präfixkonsistenz**. Der Nutzen dieser Konsistenzebenen hat sich jeweils im Rahmen von praktischen Anwendungsfällen bestätigt. Diese fünf Konsistenzebenen ermöglichen Ihnen, fundierte Kompromisse zwischen Konsistenz, Verfügbarkeit und Latenz zu finden. 

Im folgenden Video zeigt Azure Cosmos DB-Programm-Manager Andrew Liu die sofort verwendbaren globalen Verteilungsfeatures.

>[!VIDEO https://www.youtube.com/embed/-4FsGysVD14]

## <a name="distributed-databases-and-consistency"></a>Verteilte Datenbanken und Konsistenz
Kommerzielle verteilte Datenbanken werden in zwei Kategorien unterteilt: Datenbanken, die keinerlei klar definierte, belegbare Konsistenzoptionen bieten, und Datenbanken, die zwei gegensätzliche Programmierbarkeitsoptionen (starke Konsistenz oder letztliche Konsistenz) bieten. 

Erstere liefern Anwendungsentwicklern minuziöse Replikationsprotokolle und bürden ihnen die schwierige Aufgabe auf, zwischen Konsistenz, Verfügbarkeit, Wartezeit und Durchsatz abzuwägen. Letztere setzen die Anwendungsentwickler unter Druck, eine der beiden Extreme auszuwählen. Trotz der Fülle der Untersuchungen und Vorschläge für mehr als 50 Konsistenzmodelle war die verteilte Datenbankcommunity nicht in der Lage, Konsistenzebenen über starke und letztliche Konsistenz kommerzialisieren. Cosmos DB bietet Entwicklern die Auswahl zwischen fünf klar definierten Konsistenzmodellen aus dem gesamten Konsistenzspektrum: Starke Konsistenz, Begrenzte Veraltung, [Sitzungskonsistenz](http://dl.acm.org/citation.cfm?id=383631), Präfixkonsistenz und Letztliche Konsistenz. 

![Azure Cosmos DB bietet mehrere überlegt definierte (gelockerte) Konsistenzmodelle.](./media/consistency-levels/five-consistency-levels.png)

Die folgende Tabelle zeigt die speziellen Garantien der einzelnen Konsistenzebenen.
 
**Konsistenzebenen und Garantien**

| Konsistenzebene | Garantien |
| --- | --- |
| STARK (Strong) | Linearisierbarkeit. Lesevorgänge geben garantiert die neueste Version eines Elements zurück.|
| Begrenzte Veraltung (Bounded staleness) | Präfixkonsistenz. Lesevorgänge bleiben hinter Schreibvorgängen höchstens um Präfix k oder Intervall t zurück. |
| Sitzung   | Präfixkonsistenz. Monotone Lesevorgänge, monotone Schreibvorgänge, Lesen der eigenen Schreibvorgänge, Schreibvorgänge folgen Lesevorgängen |
| Präfixkonsistenz | Die zurückgegebenen Updates sind ein bestimmtes Präfix aller Updates ohne Lücken |
| Letztlich (Eventual)  | Lesevorgänge in falscher Reihenfolge |

Sie können die Standardkonsistenzebene für Ihr Cosmos DB-Konto konfigurieren (und die Konsistenz später für eine bestimmte Leseanforderung außer Kraft setzen). Intern gilt die Standardkonsistenzebene für Daten innerhalb der Partitionssätze, die sich über mehrere Regionen erstrecken können. Etwa 73 Prozent der Azure Cosmos DB-Mandanten arbeiten mit Sitzungskonsistenz. 20 Prozent bevorzugen die begrenzte Veraltung. Etwa drei Prozent der Azure Cosmos DB-Kunden experimentieren zunächst mit verschiedenen Konsistenzebenen, ehe sie sich für eine bestimmte Konsistenzoption für ihre Anwendung entscheiden. Nur zwei Prozent der Azure Cosmos DB-Mandanten überschreiben Konsistenzebenen auf der Grundlage individueller Anforderungen. 

In Cosmos DB sind Lesevorgänge mit Sitzungs-, Präfix- und letztlicher Konsistenz zweimal günstiger als Lesevorgänge mit starker Konsistenz oder begrenzter Veraltung. Cosmos DB bietet branchenführende umfassende SLAs für Verfügbarkeit, Durchsatz und Wartezeit sowie Konsistenzgarantien. Azure Cosmos DB verwendet eine [Linearisierbarkeitsprüfung](http://dl.acm.org/citation.cfm?id=1806634), die kontinuierlich für unsere Diensttelemetriedaten durchgeführt wird und Sie transparent über mögliche Konsistenzverstöße informiert. Bei Verwendung der begrenzten Veraltung überwacht und meldet Azure Cosmos DB jegliche Verstöße gegen die Grenzen „k“ und „t“. Für alle fünf gelockerten Konsistenzebenen meldet Azure Cosmos DB außerdem die Metrik [Probabilistically bounded staleness](http://dl.acm.org/citation.cfm?id=2212359) (Probabilistisch begrenzte Veraltung) direkt an Sie.  

## <a name="service-level-agreements"></a>Vereinbarungen zum Servicelevel (SLAs)

Azure Cosmos-Datenbank bietet umfassende [SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/) mit 99,99 Prozent und Garantien für Durchsatz, Konsistenz, Verfügbarkeit und Wartezeit für Azure Cosmos DB-Datenbankkonten, die auf eine einzelne Azure-Region ausgerichtet und mit einer der fünf verfügbaren Konsistenzebenen konfiguriert sind, oder für Datenbankkonten, die sich über mehrere Azure-Regionen erstrecken und mit einer der vier gelockerten Konsistenzebenen konfiguriert sind. Darüber hinaus bietet Azure Cosmos DB unabhängig von der Wahl der Konsistenzebene eine SLA mit 99,999 Prozent für die Leseverfügbarkeit von Datenbankkonten, die sich über mindestens zwei Azure-Regionen erstrecken.

## <a name="scope-of-consistency"></a>Umfang der Konsistenz
Die Granularität der Konsistenz wird auf eine einzelne Benutzeranforderung beschränkt. Eine Schreibanforderung kann einer Transaktion des Typs „Einfügen“, „Ersetzen“, „Einfügen/Aktualisieren (Upsert)“ oder „Löschen“ entsprechen. Wie bei Schreibvorgängen ist eine Lese-/Abfragetransaktion auch auf eine einzelne Benutzeranforderung beschränkt. Der Benutzer muss ggf. ein großes Resultset paginieren, das sich über mehrere Partitionen erstreckt, doch jede Lesetransaktion ist auf eine einzelne Seite beschränkt und erfolgt innerhalb einer einzelnen Partition.

## <a name="consistency-levels"></a>Konsistenzebenen
Sie können eine Standardkonsistenzebene für Ihr Datenbankkonto konfigurieren, die für alle Container (und Datenbanken) in Ihrem Cosmos DB-Konto gilt. Standardmäßig wird für alle Lesevorgänge und Abfragen für benutzerdefinierte Ressourcen die Standardkonsistenzebene verwendet, die für das Datenbankkonto festgelegt ist. Sie können die Konsistenzebene einer bestimmten Lese-/Abfrageanforderung mithilfe der einzelnen unterstützten APIs lockern. Vom Azure Cosmos DB-Replikationsprotokoll werden fünf Arten von Konsistenzebenen unterstützt, die (wie in diesem Abschnitt beschrieben) einen klaren Kompromiss zwischen bestimmten Konsistenzgarantien und Leistung bieten.

<a id="strong"></a>
**Stark**: 

* „Starke Konsistenz“ bietet garantierte [Linearisierbarkeit](https://aphyr.com/posts/313-strong-consistency-models), was heißt, dass die Lesevorgänge auf jeden Fall die neueste Version eines Elements zurückgeben. 
* Mit der Konsistenzebene STARK wird gewährleistet, dass ein Schreibvorgang erst sichtbar ist, nachdem er dauerhaft vom Mehrheitsquorum der Replikate bestätigt wurde. Ein Schreibvorgang wird entweder synchron dauerhaft sowohl vom primären Replikat als auch vom Quorum der sekundären Replikate bestätigt oder abgebrochen. Ein Lesevorgang wird immer von dem Mehrheitslesequorum bestätigt. Ein Client kann niemals einen unbestätigten oder unvollständigen Schreibvorgang sehen, wodurch gewährleistet wird, dass er immer auf die neuesten bestätigten Schreibvorgänge zugreift. 
* Azure Cosmos DB-Konten, die mit dem Modell „Starke Konsistenz“ konfiguriert sind, kann nur eine Azure-Region zugeordnet werden.  
* Die Kosten eines Lesevorgangs (im Sinne genutzter [Anforderungseinheiten](request-units.md) ) mit der Konsistenzebene STARK sind höher als bei SITZUNG und LETZTLICH, jedoch identisch mit BEGRENZTE VERALTUNG.

<a id="bounded-staleness"></a>
**Begrenzte Veraltung**: 

* Die Konsistenzebene „Begrenzte Veraltung“ garantiert, dass Lesevorgänge hinter Schreibvorgängen höchstens *K* Versionen oder Präfixe eines Elements oder mit dem Zeitintervall *t* zurückbleiben. 
* Bei Wahl von „Begrenzte Veraltung“ kann die „Veraltung“ auf zwei Weisen konfiguriert werden: Anzahl der *K*-Versionen des Elements, um das die Lesevorgänge den Schreibvorgängen hinterherhinken, und das Zeitintervall *t*. 
* Begrenzte Veraltung bietet eine vollständige globale Reihenfolge außer innerhalb des „Veraltungsfensters“. Die monotonen Lesegarantien bestehen innerhalb einer Region sowohl innerhalb als auch außerhalb des Veraltungszeitfensters. 
* Die begrenzte Veraltung bietet eine höhere Konsistenzgarantie als die Konsistenzebenen „Sitzung“, „Präfixkonsistenz“ und „Letztlich“. Für global verteilte Anwendungen wird empfohlen, BEGRENZTE VERALTUNG in Szenarien zu nutzen, in denen Sie eine hohe Konsistenz, aber auch eine Verfügbarkeit von 99,99% und niedrige Latenz wünschen.   
* Azure Cosmos DB-Konten, die mit der Konsistenz „Begrenzte Veraltung“ konfiguriert sind, kann eine beliebige Anzahl von Azure-Regionen zugeordnet werden. 
* Die Kosten eines Lesevorgangs (im Sinne genutzter Anforderungseinheiten) mit der Konsistenzebene BEGRENZTE VERALTUNG sind höher als bei SITZUNG und LETZTLICH, jedoch identisch mit STARKE KONSISTENZ.

<a id="session"></a>
**Sitzung**: 

* Anders als die globalen Konsistenzmodelle, die von den Konsistenzebenen STARK und BEGRENZTE VERALTUNG geboten werden, ist die Konsistenzebene SITZUNG auf eine bestimmte Clientsitzung beschränkt. 
* Die Konsistenzebene SITZUNG ist ideal für alle Szenarien, an denen eine Geräte- oder Benutzersitzung beteiligt ist, da sie monotone Lese- und Schreibvorgänge garantiert und RYW-Garantien bietet (Read Your Own Writes, eigene Schreibvorgänge lesen). 
* Die Konsistenzebene SITZUNG bietet vorhersagbare Konsistenz für eine Sitzung, einen maximalen Lesedurchsatz und Lese- und Schreibvorgänge mit niedrigster Latenz. 
* Azure Cosmos DB-Konten, die mit „Sitzungskonsistenz“ konfiguriert sind, kann eine beliebige Anzahl von Azure-Regionen zugeordnet werden. 
* Die Kosten für einen Lesevorgang (hinsichtlich genutzter Anforderungseinheiten) mit der Konsistenzebene „Sitzung“ sind geringer als bei „Stark“ und „Begrenzte Veraltung“, aber höher als bei „Letztlich“.

<a id="consistent-prefix"></a>
**Präfixkonsistenz**: 

* Die Präfixkonsistenz garantiert bei Fehlen weiterer Schreibvorgänge, dass es bei den Replikaten innerhalb der Gruppe letztendlich zur Konvergenz kommt. 
* Präfixkonsistenz stellt sicher, dass Lesevorgänge keine Schreibvorgänge außerhalb der Reihenfolge angezeigt werden. Wenn Schreibvorgänge in der Reihenfolge `A, B, C` erfolgen, wird einem Client entweder `A`, `A,B` oder `A,B,C`, aber niemals eine falsche Reihenfolge wie `A,C` oder `B,A,C` angezeigt.
* Azure Cosmos DB-Konten, die mit Präfixkonsistenz konfiguriert sind, kann eine beliebige Anzahl von Azure-Regionen zugeordnet werden. 

<a id="eventual"></a>
**Letztlich**: 

* „Letztliche Konsistenz“ garantiert bei Fehlen weiterer Schreibvorgänge, dass es bei den Replikaten innerhalb der Gruppe letztendlich zur Konvergenz kommt. 
* Die Konsistenzebene LETZTLICH stellt die schwächste Form von Konsistenz dar, bei der ein Client ggf. ältere Werte als die ihm zuvor angezeigten abruft.
* Die Konsistenzebene LETZTLICH bietet die schwächste Lesekonsistenz, jedoch die niedrigste Latenz für Lese- und Schreibvorgänge.
* Azure Cosmos DB-Konten, die mit „Letztliche Konsistenz“ konfiguriert sind, kann eine beliebige Anzahl von Azure-Regionen zugeordnet werden. 
* Die Kosten eines Lesevorgangs (hinsichtlich genutzter Anforderungseinheiten) mit der Ebene „Letztliche Konsistenz“ sind die niedrigsten aller Konsistenzebenen von Azure Cosmos DB.

## <a name="configuring-the-default-consistency-level"></a>Konfigurieren der Standardkonsistenzebene
1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf der linken Navigationsleiste auf **Azure Cosmos DB**.
2. Wählen Sie auf der Seite **Azure Cosmos DB** das zu ändernde Datenbankkonto aus.
3. Klicken Sie auf der Kontoseite auf **Standardkonsistenz**.
4. Wählen Sie auf der Seite **Standardkonsistenz** die neue Konsistenzebene aus, und klicken Sie auf **Speichern**.
   
    ![Screenshot mit dem Symbol „Einstellungen“ und dem Eintrag „Standardkonsistenz“](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>Konsistenzebenen für Abfragen
Bei benutzerdefinierten Ressourcen entspricht die Konsistenzebene für Abfragen standardmäßig der Konsistenzebene für Lesevorgänge. Der Index wird bei jedem Einfügen, Ersetzen oder Löschen eines Dokuments eines Elements im Cosmos DB-Container standardmäßig synchron aktualisiert. Auf diese Weise können die Abfragen dieselbe Konsistenzebene wie die von Lesevorgängen von Datenpunkten berücksichtigen. Obwohl Azure Cosmos DB für Schreibvorgänge optimiert ist und beständige Mengen von Schreibvorgängen, die synchrone Indexwartung und Bereitstellung konsistenter Abfragen unterstützt, können Sie bestimmte Container so konfigurieren, dass ihr Index verzögert aktualisiert wird. Die verzögerte Indizierung steigert die Schreibleistung noch weiter und ist ideal für Sammelerfassungsszenarien mit einer starken Lesearbeitsauslastung geeignet.  

| Indizierungsmodus | Lesevorgänge | Abfragen |
| --- | --- | --- |
| Konsistent (Standard) |Wählen Sie „Stark“, „Begrenzte Veraltung“, „Sitzung“, „Präfixkonsistenz“ oder „Letztlich“. |Wählen Sie STARK, BEGRENZTE VERALTUNG, SITZUNG oder LETZTLICH. |
| Verzögert |Wählen Sie „Stark“, „Begrenzte Veraltung“, „Sitzung“, „Präfixkonsistenz“ oder „Letztlich“. |Letztlich (Eventual) |
| Keine |Wählen Sie „Stark“, „Begrenzte Veraltung“, „Sitzung“, „Präfixkonsistenz“ oder „Letztlich“. |Nicht zutreffend |

Wie bei Leseanforderungen kann die Konsistenzebene einer bestimmten Abfrageanforderung in jeder API heruntergestuft werden.

## <a name="consistency-levels-for-the-mongodb-api"></a>Konsistenzebenen für die MongoDB-API

Azure Cosmos DB implementiert derzeit MongoDB-Version 3.4, in der zwei Konsistenzeinstellungen zur Verfügung stehen: starke Konsistenz und letztliche Konsistenz. Da Azure Cosmos DB mehrere APIs enthält, werden die Konsistenzeinstellungen auf Kontoebene angewendet, und die Konsistenz wird durch die einzelnen APIs gesteuert.  Bis MongoDB 3.6 gab es das Konzept für Sitzungskonsistenz nicht. Wenn Sie für ein MongoDB-API-Konto die Nutzung der Sitzungskonsistenz festgelegt haben, wurde bei Verwendung der MongoDB-APIs die Konsistenz auf „Letztlich“ festgelegt. Falls Sie für ein MongoDB-API-Konto eine Garantie für das Lesen eigener Schreibvorgänge benötigen, sollte die Standardkonsistenzebene auf „Stark“ oder „Begrenzte Veraltung“ festgelegt werden.

## <a name="next-steps"></a>Nächste Schritte
Wenn Sie weitere Informationen zu Konsistenzebenen und deren Vor- und Nachteile möchten, empfehlen wir die folgenden Ressourcen:

* [Erläuterung der Konsistenz replizierter Daten anhand von Baseball (Video) von Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
* [Erläuterung der Konsistenz replizierter Daten anhand von Baseball (Whitepaper) von Doug Terry](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* [Sitzungsgarantien für schwach konsistente replizierte Daten](http://dl.acm.org/citation.cfm?id=383631)
* [Konsistenzkompromisse im Design moderner verteilter Datenbanksysteme: CAP ist nur ein Teil der Wahrheit](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
* [Eventual Consistent – Revisited (Letztlich konsistent – noch einmal überdacht)](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
* [The Load, Capacity, and Availability of Quorum Systems (Last, Kapazität und Verfügbarkeit von Quorumsystemen), SIAM Journal on Computing](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)
* [Line-up: a complete and automatic linearizability checker, Proceedings of the 2010 ACM SIGPLAN conference on Programming language design and implementation (Aufstellung: eine vollständige und automatische Linearisierbarkeitsüberprüfung, Verfahren der 2010 ACM SIGPLAN-Konferenz zu Design und Implementierung von Programmiersprachen)](http://dl.acm.org/citation.cfm?id=1806634)
* [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums (Probabilistisch begrenzte Veraltung für praktische partielle Quoren)](http://dl.acm.org/citation.cfm?id=2212359)
