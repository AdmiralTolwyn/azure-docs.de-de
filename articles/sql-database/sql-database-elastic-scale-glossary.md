---
title: Tools für elastische Datenbanken – Glossar
description: Erläuterung von Begriffen, die für die Tools für flexible Datenbanken verwendet werden
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: ab972db78cd213497fb96486b3e16b01f2c4c6eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823629"
---
# <a name="elastic-database-tools-glossary"></a>Tools für elastische Datenbanken – Glossar

Die folgenden Begriffe werden für die [Tools für elastische Datenbanken](sql-database-elastic-scale-introduction.md), ein Feature von Azure SQL-Datenbank, definiert. Die Tools werden zur Verwaltung von [Shardzuordnungen](sql-database-elastic-scale-shard-map-management.md) verwendet. Sie beinhalten die [Clientbibliothek](sql-database-elastic-database-client-library.md), das [Split-Merge-Tool](sql-database-elastic-scale-overview-split-and-merge.md), [Pools für elastische Datenbanken](sql-database-elastic-pool.md) und [Abfragen](sql-database-elastic-query-overview.md). 

Diese Begriffe werden in [Hinzufügen eines Shards mithilfe der Tools für elastische Datenbanken](sql-database-elastic-scale-add-a-shard.md) und [Beheben von Problemen bei der Shardzuordnung mithilfe der RecoveryManager-Klasse](sql-database-elastic-database-recovery-manager.md) verwendet.

![Begriffe zur elastischen Skalierung][1]

**Datenbank:** eine Azure SQL-Datenbank. 

**Datenabhängiges Routing**: die Funktion, die es einer Anwendung ermöglicht, unter Verwendung eines bestimmten Shardingschlüssels eine Verbindung mit einem Shard herzustellen. Siehe [Datenabhängiges Routing](sql-database-elastic-scale-data-dependent-routing.md). Vgl. **[Abfragen mehrerer Shards](sql-database-elastic-scale-multishard-querying.md)** .

**Globale Shardzuordnung**: die Zuordnungen zwischen Shardingschlüsseln und den zugehörigen Shards in einer **Shardgruppe**. Die globale Shardzuordnung wird im **Shardzuordnungs-Manager** gespeichert. Vgl. **lokale Shardzuordnung**.

**Listenshardzuordnung**: eine Shardzuordnung, in der die Shardingschlüssel einzeln zugeordnet sind. Vgl. **Bereichsshardzuordnung**.   

**Lokale Shard-Zuordnung:** wird für einen Shard gespeichert und enthält die Zuordnungen für die Shardlets, die sich in dem Shard befinden.

**Abfrage mehrerer Shards**: die Möglichkeit, eine Abfrage für mehrere Shards durchzuführen. Die Resultsets werden über die UNION ALL-Semantik zurückgegeben (auch als „Auffächerungsabfrage“ bezeichnet). Vgl. **Datenabhängiges Routing**.

**Mehrinstanzenfähig** und **Einzelner Mandant**: eine mehrinstanzenfähige Datenbank und eine Einzelmandantendatenbank:

![Einzelmandanten- und mehrinstanzenfähige Datenbanken](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Hier ist eine Darstellung von Einzelmandanten- und mehrinstanzenfähigen **Sharddatenbanken** . 

![Einzelmandanten- und mehrinstanzenfähige Datenbanken](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Bereichs-Shard-Zuordnung:** eine Shard-Zuordnung, in der die Shard-Verteilungsstrategie auf mehreren Bereichen zusammenhängender Werte basiert. 

**Verweistabellen**: Tabellen, die nicht partitioniert sind, aber über mehrere Shards hinweg repliziert werden. Postleitzahlen können z. B. in einer Verweistabelle gespeichert werden. 

**Shard:** eine Azure SQL-Datenbank, in der die Daten aus einem horizontal partitionierten DataSet gespeichert werden. 

**Shardelastizität**: die Fähigkeit, sowohl **horizontal** als auch **vertikal** zu skalieren.

**Shard-Tabellen:** Tabellen, die horizontal partitioniert sind, deren Daten also anhand ihrer Sharding-Schlüsselwerte über Shards verteilt werden. 

**Sharding-Schlüssel:** ein Spaltenwert, der bestimmt, wie Daten über Shards hinweg verteilt werden. Folgende Werttypen sind zulässig: **int**, **bigint**, **varbinary** oder **uniqueidentifier**. 

**Shard-Gruppe:** die Auflistung von Shards, die der gleichen Shard-Zuordnung im Shard-Zuordnungs-Manager zugeordnet sind.  

**Shardlet:** alle Daten, die mit einem Wert eines Sharding-Schlüssels in einem Shard verknüpft sind. Ein Shardlet ist die kleinste Einheit der Datenverschiebung, die bei der erneuten Verteilung von Shard-Tabellen möglich ist. 

**Shard-Zuordnung:** der Satz von Zuordnungen zwischen Sharding-Schlüsseln und den entsprechenden Shards.

**Shard-Zuordnungs-Manager:** ein Verwaltungsobjekt und Datenspeicher mit den Shard-Zuordnungen, Shard-Speicherorten und Zuordnungen für eine oder mehrere Shard-Gruppen.

![Zuordnungen][2]

## <a name="verbs"></a>Verben
**Horizontales Skalieren**: das Skalieren einer Sammlung von Shards durch Hinzufügen oder Entfernen von Shards zu bzw. aus einer Shardzuordnung, wie unten dargestellt.

![Horizontale und vertikale Skalierung][3]

**Zusammenführen:** das Verschieben von Shardlets aus zwei Shards in einen Shard und das entsprechende Aktualisieren der Shard-Zuordnung.

**Shardlet-Verschiebung:** das Verschieben eines einzelnen Shardlets in einen anderen Shard. 

**Shard:** das horizontale Partitionieren identisch strukturierter Daten über mehrere Datenbanken anhand eines Sharding-Schlüssels.

**Aufteilen**: das Verschieben mehrerer Shardlets aus einem Shard in einen anderen (i.d.R. neuen) Shard. Als Aufteilungspunkt wird ein Sharding-Schlüssel vom Benutzer bereitgestellt.

**Vertikales Skalieren**: das Heraufskalieren (oder Herunterskalieren) der Computegröße eines einzelnen Shards. Dies erfolgt z. B. durch Ändern eines Shards von Standard in Premium (wodurch mehr Computerressourcen zur Verfügung stehen). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

