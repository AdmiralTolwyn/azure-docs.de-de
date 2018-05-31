---
title: Zuordnen von Partitionen und Replikaten für Abfrage und Indizierung in Azure Search | Microsoft-Dokumentation
description: Passen Sie Partitions- und Replikatcomputerressourcen in Azure Search an, wobei jede Ressource in abrechenbaren Sucheinheiten abgerechnet wird.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: b6c2c8283d5a60013c525db296bf84cc50d76617
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203092"
---
# <a name="allocate-partitions-and-replicas-for-query-and-indexing-workloads-in-azure-search"></a>Zuordnen von Partitionen und Replikaten für Abfrage und Indizierung von Arbeitslasten in Azure Search
Nach dem [Auswählen eines Tarifs](search-sku-tier.md) und dem [Bereitstellen eines Suchdiensts](search-create-service-portal.md) kann im nächsten Schritt optional die Anzahl der von dem Dienst verwendeten Replikate oder Partitionen erhöht werden. Jeder Tarif bietet eine feste Anzahl von Abrechnungseinheiten. In diesem Artikel wird erläutert, wie Sie diese Einheiten für eine optimale Konfiguration zuordnen, bei der Ihre Anforderungen für die Abfrageausführung, Indizierung und Speicherung ausgeglichen sind.

Die Ressourcenkonfiguration ist verfügbar, wenn Sie einen Dienst zum [Basic-Tarif](http://aka.ms/azuresearchbasic) oder zu einem der [Standard-Tarife](search-limits-quotas-capacity.md) bereitstellen. Bei Diensten in diesen Tarifen wird die Kapazität in Abstufungen von *Sucheinheiten* (Search Units, SUs) erworben, wobei jede Partition und jedes Replikat jeweils als einzelne SU zählt. 

Bei Verwendung von weniger Sucheinheiten fällt die Rechnung entsprechend niedriger aus. Die Abrechnung ist aktiviert, solange der Dienst bereitgestellt wird. Wenn Sie einen Dienst vorübergehend nicht verwenden und eine Abrechnung vermeiden möchten, müssen Sie den Dienst löschen und später bei Bedarf neu erstellen.

> [!Note]
> Durch Löschen eines Diensts werden alle darin befindlichen Elemente gelöscht. Es gibt keine Möglichkeit in Azure Search, beibehaltene Suchdaten zu sichern und wiederherzustellen. Um einen vorhandenen Index in einem neuen Dienst erneut bereitzustellen, sollten Sie das Programm ausführen, das ursprünglich zum Erstellen und Laden des Index verwendet wurde. 

## <a name="terminology-partitions-and-replicas"></a>Terminologie: Partitionen und Replikate
Ein Suchdienst basiert in erster Linie auf Partitionen und Replikaten.

| Ressource | Definition |
|----------|------------|
|*Partitionen* | Partitionen stellen Indexspeicher und E/A für Lese-/Schreibvorgänge (beispielsweise bei der Neuerstellung oder Aktualisierung eines Index) bereit.|
|*Replikate* | Replikate sind Instanzen des Suchdiensts und dienen in erster Linie zum Lastenausgleich bei Abfragevorgängen. Jedes Replikat hostet jeweils eine Kopie eines Index. Wenn Sie über 12 Replikate verfügen, stehen für jeden im Dienst geladenen Index 12 Kopien zur Verfügung.|

> [!NOTE]
> Welche Indizes auf einem Replikat ausgeführt werden, ist nicht direkt beeinflussbar. Eine Kopie der einzelnen Indizes jedes Replikats ist Teil der Dienstarchitektur.
>

## <a name="how-to-allocate-partitions-and-replicas"></a>Gewusst wie: Zuordnen von Partitionen und Replikaten
In Azure Search wird einem Dienst zunächst eine Mindestmenge von Ressourcen (bestehend aus einer Partition und einem Replikat) zugeordnet. Bei Tarifen, die dies unterstützen, können Sie die Verarbeitungsressourcen schrittweise anpassen, indem Sie die Anzahl an Partitionen erhöhen, um mehr Speicher- und E/A-Kapazität zu erhalten oder mehr Replikate hinzuzufügen, um ein höheres Abfrageaufkommen zu bewältigen oder die Leistung zu verbessern. Ein einzelner Dienst muss über genügend Ressourcen verfügen, um sämtliche Workloads (Indizierung und Abfragen) bewältigen zu können. Workloads können nicht auf mehrere Dienste aufgeteilt werden.

Wir empfehlen, die Replikat- und Partitionszuordnung über das Azure-Portal zu erhöhen oder zu verändern. Das Portal erzwingt Grenzwerte für zulässige Kombinationen, um die Obergrenzen nicht zu überschreiten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und wählen Sie den Suchdienst aus.
2. Öffnen Sie in den **Einstellungen** das Blatt **Skalieren**, und passen Sie mithilfe der Schieberegler die Anzahl von Partitionen und Replikaten an.

Als Alternative zum Portal kann die [Verwaltungs-REST-API](https://docs.microsoft.com/rest/api/searchmanagement/services) verwendet werden, wenn die Bereitstellung skript- oder codebasiert erfolgen soll.

Allgemein gilt: Suchanwendungen benötigen mehr Replikate als Partitionen, insbesondere, wenn die Dienstvorgänge auf Abfrageworkloads ausgerichtet sind. Warum das so ist, erfahren Sie im [Abschnitt zu Hochverfügbarkeit](#HA).

> [!NOTE]
> Sobald ein Dienst bereitgestellt ist, kann kein direktes Upgrade auf eine höhere SKU erfolgen. In diesem Fall müssen Sie einen Suchdienst unter dem neuen Tarif erstellen und Ihre Indizes neu laden. Informationen zur Dienstbereitstellung finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service-portal.md) .
>
>

<a id="HA"></a>

## <a name="high-availability"></a>Hochverfügbarkeit
Da ein Hochskalieren einfach und relativ schnell durchzuführen ist, wird im Allgemeinen empfohlen, mit einer Partition und einem oder zwei Replikaten zu beginnen und dann bei steigenden Abfragevolumen hochzuskalieren. Abfrageworkloads werden in erster Linie auf Replikaten ausgeführt. Wenn Sie einen höheren Durchsatz oder Hochverfügbarkeit benötigen, sind wahrscheinlich zusätzliche Replikate erforderlich.

Allgemeine Empfehlungen für Hochverfügbarkeit sind:

* Zwei Replikate für Hochverfügbarkeit von schreibgeschützten Workloads (Abfragen)
* Drei oder mehr Replikate für Hochverfügbarkeit von Lese-/Schreibworkloads (Abfragen und Indizierung, wenn einzelne Dokumente hinzugefügt, aktualisiert oder gelöscht werden)

Vereinbarungen zum Servicelevel (Service Level Agreements, SLAs) für Azure Search sind auf Abfragevorgänge und auf Indexupdates (Hinzufügen, Aktualisieren oder Löschen von Dokumenten) ausgerichtet.

Der Tarif „Basic“ erreicht den Höchstwert bei einer Partition und drei Replikaten. Wenn Sie die Flexibilität benötigen, auf Schwankungen beim Bedarf an Indizierungen und Abfragedurchsatz sofort zu reagieren, ziehen Sie einen der Standard-Tarife in Betracht.

### <a name="index-availability-during-a-rebuild"></a>Indexverfügbarkeit während einer Neuerstellung

Die Hochverfügbarkeit von Azure Search gilt für Abfragen und Indexaktualisierungen ohne Indexneuerstellung. Wenn Sie ein Feld löschen, einen Datentyp ändern oder ein Feld umbenennen, müssen Sie den Index neu erstellen. Um den Index neu zu erstellen, müssen Sie ihn löschen, neu erstellen und die Daten neu laden.

> [!NOTE]
> Sie können einem Azure Search-Index neue Felder hinzufügen, ohne den Index neu zu erstellen. Der Wert des neuen Felds lautet NULL für alle Dokumente, die bereits im Index vorhanden sind.

Um die Verfügbarkeit des Index während einer Neuerstellung zu gewährleisten, muss im gleichen Dienst eine Kopie des Index mit einem anderen Namen oder eine Kopie des Index mit dem gleichen Namen in einem anderen Dienst vorhanden sein und Ihr Code mit einer Umleitungs- oder Failoverlogik versehen werden.

## <a name="disaster-recovery"></a>Notfallwiederherstellung
Derzeit steht kein integrierter Mechanismus für die Notfallwiederherstellung bereit. Das Hinzufügen von Partitionen oder Replikaten wäre die falsche Strategie, um die Zielsetzungen für eine Notfallwiederherstellung zu erfüllen. Der gängigste Ansatz ist, Redundanz auf Dienstebene durch Bereitstellung eines zweiten Suchdiensts in einer anderen Region hinzuzufügen. Die Umleitungs- oder Failoverlogik muss genau wie bei der Verfügbarkeit während der Indexneuerstellung in Ihrem Code bereitgestellt werden.

## <a name="increase-query-performance-with-replicas"></a>Erhöhen der Abfrageleistung mit Replikaten
Eine Abfragelatenz deutet darauf hin, dass zusätzliche Replikate erforderlich sind. Im Allgemeinen besteht der erste Schritt zum Verbessern der Abfrageleistung im Hinzufügen weiterer Instanzen dieser Ressource. Beim Hinzufügen von Replikaten werden zusätzliche Kopien des Index online geschaltet, um größere Abfrageworkloads zu unterstützen und die Anforderungslast gleichmäßig auf mehrere Replikate zu verteilen.

Es können keine festen Schätzungen für die Abfragen pro Sekunde (Queries Per Second, QPS) abgegeben werden: Die Abfrageleistung kann je nach Komplexität der jeweiligen Abfrage und konkurrierenden Workloads stark variieren. Obwohl das Hinzufügen von Replikaten die Leistung deutlich erhöht, ist das Endergebnis aber nicht streng linear: Das Hinzufügen von 3 Replikaten garantiert keinen dreifachen Durchsatz.

Anleitungen zum Schätzen der Abfragen pro Sekunde (QPS) für Ihre Workloads finden Sie unter [Überlegungen zur Leistung und Optimierung von Azure Search](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Erhöhen der Indizierungsleistung mit Partitionen
Suchanwendungen, die Datenaktualisierung nahezu in Echtzeit erfordern, benötigen proportional mehr Partitionen als Replikate. Das Hinzufügen von Partitionen verteilt Lese-/ Schreibvorgänge über eine größere Anzahl von Computerressourcen. Außerdem erhalten Sie mehr Speicherplatz auf dem Datenträger zum Speichern zusätzlicher Indizes und Dokumente.

Größere Indizes erfordern eine längere Abfragezeit. Daher werden Sie feststellen, dass jede inkrementelle Zunahme an Partitionen einen kleineren, aber proportionalen Anstieg der Replikate erforderlich macht. Die Komplexität Ihrer Abfragen und das Abfragevolumen haben darauf Einfluss, wie schnell die Abfrage ausgeführt wird.

## <a name="basic-tier-partition-and-replica-combinations"></a>Basic-Tarif: Partitions- und Replikatskombinationen
Ein Basic-Dienst kann genau eine Partition und bis zu drei Replikate besitzen. Die Obergrenze liegt bei drei SUs. Nur die Replikate können angepasst werden. Für Hochverfügbarkeit bei Abfragen sind mindestens zwei Replikate erforderlich.

<a id="chart"></a>

## <a name="standard-tiers-partition-and-replica-combinations"></a>Standard-Tarife: Partitions- und Replikatskombinationen
Diese Tabelle enthält die SUs, die für eine Unterstützung der Kombinationen aus Replikaten und Partitionen erforderlich sind. Dabei gilt für alle Standard-Tarife ein Grenzwert von 36 SUs.

|   | **1 Partition** | **2 Partitionen** | **3 Partitionen** | **4 Partitionen** | **6 Partitionen** | **12 Partitionen** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 Replikat:** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 Replikate** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 Replikate** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 Replikate** |4 SU |8 SU |12 SU |16 SU |24 SU |N/V |
| **5 Replikate** |5 SU |10 SU |15 SU |20 SU |30 SU |N/V |
| **6 Replikate** |6 SU |12 SU |18 SU |24 SU |36 SU |N/V |
| **12 Replikate** |12 SU |24 SU |36 SU |N/V |N/V |N/V |

SUs, Preise und Kapazität werden auf der Azure-Website ausführlich erläutert. Weitere Informationen finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Die Anzahl der Replikate und Partitionen ist ein ganzzahliger Teiler von 12 (d.h. 1, 2, 3, 4, 6, 12). Der Grund: Azure Search unterteilt jeden Index vorab in 12 Shards, damit er gleichmäßig auf alle Partitionen verteilt werden kann. Wenn Ihr Dienst z.B. drei Partitionen aufweist und Sie einen Index erstellen, enthält jede Partition 4 Shards des Indexes. Azure Search erstellt Shards eines Index in Form von Implementierungsdetails, die sich bei zukünftigen Versionen ändern können. Auch wenn die Anzahl heute 12 beträgt, sollten Sie nicht davon ausgehen, das dies auch in Zukunft immer so ist.
>
>

## <a name="billing-formula-for-replica-and-partition-resources"></a>Formel für die Abrechnung von Replikat- und Partitionsressourcen
Die Formel zum Berechnen der Anzahl der erforderlichen SUs für bestimmte Kombinationen ergibt sich aus dem Produkt der Replikate und Partitionen: (R x P = SU). Beispielsweise werden drei Replikate multipliziert mit drei Partitionen als neun SUs abgerechnet.

Die Kosten pro SU sind durch den Tarif vorgegeben. Der Kostensatz pro SU für den Basic-Tarif ist dabei niedriger als für den Standard-Tarif. Die Preise für die einzelnen Tarife finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/search/).
