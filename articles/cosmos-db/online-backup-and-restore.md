---
title: Onlinesicherung und -wiederherstellung mit Azure Cosmos DB | Microsoft-Dokumentation
description: Erfahren Sie mehr über das automatische Sichern und Wiederherstellen einer Azure Cosmos DB-Datenbank.
keywords: Sicherung und Wiederherstellung, Onlinesicherung
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: dddb3311ff5db964494697d76967f74c863d84e1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34615035"
---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Automatische Onlinesicherung und -wiederherstellung mit Azure Cosmos DB
Azure Cosmos DB erstellt in regelmäßigen Abständen automatisch Sicherungen aller Daten. Die automatischen Sicherungen erfolgen ohne Beeinträchtigung der Leistung oder Verfügbarkeit des Betriebs Ihrer Datenbanken. Alle Sicherungskopien werden in einem anderen Speicherdienst getrennt gespeichert, und diese Sicherungen werden zum besseren Schutz vor regionalen Ausfällen global repliziert. Die automatischen Sicherungen sind für den Fall vorgesehen, dass Sie Ihren Cosmos DB-Container versehentlich löschen und später eine Daten- oder Notfallwiederherstellungslösung benötigen sollten.  

Dieser Artikel beginnt mit einer Kurzübersicht über die Redundanz und Verfügbarkeit von Daten in Cosmos DB, an die sich eine Erörterung von Sicherungen anschließt. 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Hochverfügbarkeit mit Cosmos DB: Kurzübersicht
Cosmos DB ist auf eine [globale Verteilung](distribute-data-globally.md) ausgelegt und ermöglicht eine Skalierung des Durchsatzes in mehreren Azure-Regionen sowie ein durch Richtlinien gesteuertes Failover und transparente Multihosting-APIs. Azure Cosmos DB bietet [SLAs für eine Verfügbarkeit von 99,99 Prozent](https://azure.microsoft.com/support/legal/sla/cosmos-db) für alle Konten mit einer einzelnen Region und alle Konten mit mehreren Regionen mit gelockerter Konsistenz sowie eine Leseverfügbarkeit von 99,999 Prozent für alle Datenbankkonten mit mehreren Regionen. Alle Schreibvorgänge in Azure Cosmos DB werden mithilfe eines Quorums von Replikaten in einem lokalen Rechenzentrum beständig auf lokalen Datenträgern gespeichert, bevor der Client eine Bestätigung erhält. Beachten Sie, dass die Hochverfügbarkeit von Cosmos DB von lokalem Speicher und nicht von externen Speichertechnologien abhängig ist. Wenn Ihr Konto darüber hinaus mehreren Azure-Regionen zugeordnet ist, werden Ihre Schreibvorgänge auch in andere Regionen repliziert. Um Ihren Durchsatz zu skalieren und mit kurzer Latenz auf Daten zuzugreifen, können Sie in Ihrem Datenbankkonto über so viele Leseregionen wie gewünscht verfügen. In jeder Leseregion werden die (replizierten) Daten dauerhaft in einer Replikatgruppe gespeichert.  

Wie im folgenden Diagramm dargestellt, wird ein einzelner Cosmos DB-Container [horizontal partitioniert](partition-data.md). Eine „Partition“ ist im folgenden Diagramm durch einen Kreis dargestellt, und jede Partition bietet mithilfe einer Replikatgruppe hohe Verfügbarkeit. Dies ist die lokale Verteilung innerhalb einer einzelnen Azure-Region (siehe die x-Achse). Zusätzlich wird jede Partition (samt zugehöriger Replikatgruppe) anschließend auf mehrere Regionen verteilt, die Ihrem Datenbankkonto zugeordnet sind (in dieser Abbildung z.B. auf die drei Regionen USA, Osten, USA, Westen und Indien, Mitte). Die „Partitionsgruppe“ ist eine global verteilte Entität, die aus mehreren Kopien Ihrer Daten in jeder Region besteht (siehe die Y-Achse). Sie können den Ihrem Datenbankkonto zugeordneten Regionen eine Priorität zuweisen. Bei einem Notfall führt Cosmos DB ein transparentes Failover in die nächste Region durch. Sie können ein Failover auch manuell simulieren, um zu testen, ob Ihre Anwendung lückenlos verfügbar ist.  

Die folgende Abbildung veranschaulicht das hohe Maß an Redundanz dank Cosmos DB.

![Hohes Maß an Redundanz mit Cosmos DB](./media/online-backup-and-restore/redundancy.png)

![Hohes Maß an Redundanz mit Cosmos DB](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>Vollständige, automatische Onlinesicherungen
Huch, ich habe leider meinen Container bzw. meine Datenbank gelöscht! Cosmos DB sorgt nicht nur dafür, dass Ihre Daten, sondern auch die Sicherungen Ihrer Daten überaus redundant und gegen regionale Katastrophen geschützt sind. Diese automatisierten Sicherungen werden derzeit ungefähr alle vier Stunden ausgeführt, und es werden immer die beiden neuesten Sicherungen gespeichert. Wenn die Daten aus Versehen gelöscht oder beschädigt werden, wenden Sie sich innerhalb von acht Stunden an den [Azure-Support](https://azure.microsoft.com/support/options/). 

Die Sicherungen erfolgen ohne Beeinträchtigung der Leistung oder Verfügbarkeit Ihrer Datenbanken. Cosmos DB erstellt die Sicherung im Hintergrund, ohne Ihre bereitgestellten Anforderungseinheiten zu beanspruchen bzw. die Leistung oder die Verfügbarkeit Ihrer Datenbank zu beeinträchtigen. 

Im Gegensatz zu Ihren Daten, die in Cosmos DB gespeichert sind, werden automatische Sicherungen im Azure Blob Storage-Dienst gespeichert. Um niedrige Latenz und ein effizientes Hochladen zu gewährleisten, wird die Momentaufnahme Ihrer Sicherung in eine Instanz von Azure Blob Storage in der Region hochgeladen, in der sich auch die aktuelle Schreibregion Ihres Cosmos DB-Datenbankkontos befindet. Zum besseren Schutz vor regionalen Katastrophen wird jede Momentaufnahme Ihrer Sicherungsdaten in Azure Blob Storage nochmals mithilfe von georedundantem Speicher (GRS) in eine andere Region repliziert. Das folgende Diagramm zeigt, dass der gesamte Cosmos DB-Container (mit allen drei primären Partitionen in USA, Westen in diesem Beispiel) in einem von Azure Blob Storage-Remotekonto in USA, Westen gesichert wird und anschließend mithilfe von GRS nach USA, Osten repliziert wird. 

Das folgende Bild veranschaulicht regelmäßige vollständige Sicherungen aller Cosmos DB-Entitäten in georedundantem Azure Storage.

![Regelmäßige vollständige Sicherungen aller Cosmos DB-Entitäten in georedundantem Azure Storage](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>Aufbewahrungszeitraum der Sicherung
Wie oben beschrieben, erstellt Azure Cosmos DB alle vier Stunden Momentaufnahmen auf Partitionsebene. Es werden jeweils nur die letzten zwei Momentaufnahmen aufbewahrt. Wenn die Sammlung bzw. Datenbank jedoch gelöscht wird, werden die vorhandenen Momentaufnahmen für alle gelöschten Partitionen innerhalb der angegebenen Sammlung bzw. Datenbank für 30 Tage aufbewahrt.

Wenn Sie bei Verwendung der SQL-API eigene Momentaufnahmen beibehalten möchten, können Sie die Option zum Export in eine JSON-Datei im [Datenmigrationstool](import-data.md#export-to-json-file) von Azure Cosmos DB verwenden, um zusätzliche Sicherungen zu planen.

> [!NOTE]
> Beachten Sie bei der Bereitstellung des Durchsatzes für mehrere Container auf Datenbankebene, dass die Wiederherstellung für das gesamte Datenbankkonto erfolgt. Darüber hinaus müssen Sie sich unbedingt innerhalb von acht Stunden an den Support wenden, wenn Sie diese neue Funktion verwenden und versehentlich Ihren Container (Sammlung/Tabelle/Graph) gelöscht haben. 


## <a name="restoring-a-database-from-an-online-backup"></a>Wiederherstellen einer Datenbank von einer Onlinesicherung
Falls Sie Ihre Datenbank oder -sammlung versehentlich löschen, können Sie [ein Supportticket anfordern](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) oder den [Azure Support bitten](https://azure.microsoft.com/support/options/), die Daten aus der letzten automatischen Sicherung wiederherzustellen. Der Azure-Support steht nur für ausgewählte Tarife wie Standard und Developer zur Verfügung. Für den Basic-Tarif ist kein Support verfügbar. Weitere Informationen zu anderen Supportplänen finden Sie auf der Seite [Azure-Supportpläne](https://azure.microsoft.com/en-us/support/plans/). Wenn Sie die Datenbank aufgrund einer Datenbeschädigung wiederherstellen müssen (einschließlich Fälle, bei denen Dokumente innerhalb einer Sammlung gelöscht werden), erfahren Sie unter [Umgang mit Datenbeschädigung](#handling-data-corruption), wie Sie zusätzliche Schritte ausführen, um zu verhindern, dass die beschädigten Daten in die vorhandenen Sicherungen überschrieben werden. Für die Wiederherstellung einer bestimmten Momentaufnahme Ihrer Sicherung setzt Cosmos DB voraus, dass die Daten für die Dauer des Sicherungszyklus dieser Momentaufnahme verfügbar waren.

## <a name="handling-data-corruption"></a>Umgang mit Datenbeschädigung
Azure Cosmos DB bewahrt die letzten beiden Sicherungen jeder Partition im Datenbankkonto auf. Dieses Modell funktioniert gut, wenn Sie einen Container (Sammlung von Dokumenten, Diagramm, Tabelle) oder eine Datenbank versehentlich gelöscht haben, da eine der letzten Versionen wiederhergestellt werden kann. Wenn jedoch eine Datenbeschädigung auftritt, ist Azure Cosmos DB möglicherweise nicht über die Datenbeschädigung informiert, und diese könnte in die vorhandenen Sicherungen überschrieben worden sein. Sobald Sie eine Beschädigung feststellen, sollten Benutzer den beschädigten Container (Sammlung/Diagramm/Tabelle) löschen, sodass Sicherungen vor dem Überschreiben mit beschädigten Daten geschützt sind.

## <a name="next-steps"></a>Nächste Schritte

Wie Sie die Datenbank in mehrere Rechenzentren replizieren, erfahren Sie unter [Globale Verteilung von Daten mit Cosmos DB](distribute-data-globally.md). 

Zum Kontaktieren des Azure-Supports [fordern Sie im Azure-Portal ein Ticket an](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

