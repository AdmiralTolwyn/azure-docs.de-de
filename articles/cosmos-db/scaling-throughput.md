---
title: Skalieren des Durchsatzes in Azure Cosmos DB
description: Dieser Artikel beschreibt, wie Azure Cosmos DB den Durchsatz elastisch skaliert.
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: dd17b08a16dedf474b2a1eca8fa8034672610c1f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454435"
---
# <a name="globally-scale-provisioned-throughput"></a>Globales Skalieren von bereitgestelltem Durchsatz 

In Azure Cosmos DB wird der bereitgestellte Durchsatz als Anforderungseinheit/Sekunde (RU/s, Mehrzahl: RUs) dargestellt. Anforderungseinheiten messen die Kosten für Lese- und Schreibvorgänge für Ihre Cosmos-Container wie in der folgenden Abbildung dargestellt:

![Anforderungseinheiten](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Sie können Anforderungseinheiten für einen Cosmos-Container oder eine Cosmos-Datenbanken bereitstellen. In einem Container bereitgestellte Anforderungseinheiten stehen ausschließlich für Vorgänge zur Verfügung, die in diesem Container ausgeführt werden. In einer Datenbank bereitgestellte Anforderungseinheiten werden für alle Container in dieser Datenbank (ausgenommen Container mit exklusiv zugewiesenen RUs) freigegeben.

Sie können jederzeit die bereitgestellte RU/s erhöhen oder verringern, um den Durchsatz elastisch zu skalieren. Weitere Informationen finden Sie im Artikel zum [Bereitstellen von Durchsatz](set-throughput.md) und zur elastischen Skalierung von Cosmos-Containern und -Datenbanken. Um den Durchsatz global zu skalieren, können Sie Ihrem Cosmos-Konto jederzeit Regionen hinzufügen oder diese daraus entfernen. Weitere Informationen finden Sie unter [Verwalten von Datenbankkonten in Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Die Zuordnung mehrerer Regionen zu einem Cosmos-Konto ist in vielen Szenarien wichtig, um eine geringe Latenz und [Hochverfügbarkeit](high-availability.md) auf der ganzen Welt zu erreichen.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Verteilen des bereitgestellten Durchsatzes über mehrere Regionen

Wenn Sie „R“ RUs in einem Cosmos-Container (oder einer Datenbank) bereitstellen, stellt Cosmos DB sicher, dass sie in *jeder* Region verfügbar sind, die mit Ihrem Cosmos-Konto verknüpft ist. Jedes Mal, wenn Sie Ihrem Konto eine neue Region hinzufügen, stellt Cosmos DB automatisch „R“ RUs in der neu hinzugefügten Region bereit. Die Vorgänge, die für Ihren Cosmos-Container ausgeführt werden, erhalten garantiert „R“ RUs in jeder Region. Sie können RUs nicht selektiv einer bestimmten Region zuweisen. Die für einen Cosmos-Container (oder eine Datenbank) bereitgestellten RUs werden für alle mit Ihrem Cosmos-Konto verbundenen Regionen bereitgestellt.

Angenommen, ein Cosmos-Container ist mit „R“ RUs konfiguriert und es gibt „N“ Regionen, die dem Cosmos-Konto zugeordnet sind:

- Wenn das Cosmos-Konto mit einer einzigen Schreibregion konfiguriert ist, beträgt die Gesamtzahl von global im Container verfügbaren RUs: r x n.

- Wenn das Cosmos-Konto mit mehreren Schreibregionen konfiguriert ist, beträgt die Gesamtzahl von global im Container verfügbaren RUs: r x (n+1). Die zusätzlichen r RUs werden automatisch bereitgestellt, um Updatekonflikte und Anti-Entropie-Datenverkehr in den Regionen zu verarbeiten.

Die Wahl des [Konsistenzmodells](consistency-levels.md) wirkt sich auch auf den Durchsatz aus. Für „Sitzung“, „Präfixkonsistenz„ und „letztliche Konsistenz“ können Sie den etwa 2-fachen Lesedurchsatz im Vergleich zu „starke Konsistenz“ und „begrenzte Veraltung“ erzielen.

## <a name="next-steps"></a>Nächste Schritte

Im folgenden Artikel erfahren Sie, wie Sie den Durchsatz konfigurieren:

* [Festlegen und Abrufen des Durchsatzes für Container und Datenbanken](set-throughput.md) 

