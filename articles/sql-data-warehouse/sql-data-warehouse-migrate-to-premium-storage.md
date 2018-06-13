---
title: Migrieren eines vorhandenen Azure Data Warehouse zu Storage Premium | Microsoft-Dokumentation
description: Anweisungen zum Migrieren eines vorhandenen Data Warehouse zu Storage Premium
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: barbkess
editor: ''
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 03/15/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: 3b43bc17b7f9cf80a9520c5c573be3a48d82e4e7
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/14/2018
ms.locfileid: "31005561"
---
# <a name="migrate-your-data-warehouse-to-premium-storage"></a>Migrieren eines Data Warehouse zu Storage Premium
Für Azure SQL Data Warehouse wurde vor Kurzem [Storage Premium eingeführt, um die Leistung besser vorhersagen zu können][premium storage for greater performance predictability]. Vorhandene Data Warehouses mit derzeit Standardspeicher können nun zu Storage Premium migriert werden. Sie können die automatische Migration nutzen. Wenn Sie aber den Migrationszeitpunkt bestimmen möchten (was eine gewisse Ausfallzeit mit sich bringt), können Sie die Migration selbst vornehmen.

Wenn Sie über mehr als ein Data Warehouse verfügen, ermitteln Sie anhand des [Zeitplans für die automatische Migration][automatic migration schedule], wann die einzelnen Migrationen erfolgen.

## <a name="determine-storage-type"></a>Bestimmen des Speichertyps
Wenn Sie ein Data Warehouse vor den folgenden Terminen erstellt haben, verwenden Sie derzeit Standardspeicher.

| **Region** | **Vor diesem Datum erstelltes Data Warehouse** |
|:--- |:--- |
| Australien (Osten) |1. Januar 2018 |
| China, Osten |1. November 2016 |
| China, Norden |1. November 2016 |
| Deutschland, Mitte |1. November 2016 |
| Deutschland, Nordosten |1. November 2016 |
| Indien, Westen |1. Februar 2018 |
| Japan, Westen |1. Februar 2018 |
| USA Nord Mitte |10. November 2016 |

## <a name="automatic-migration-details"></a>Details zur automatischen Migration
Standardmäßig migrieren wir Ihre Datenbank zwischen 18:00 und 06:00 Uhr Ortszeit Ihrer Region, und zwar im Rahmen des [Zeitplans für die automatische Migration][automatic migration schedule]. Während der Migration kann Ihr vorhandenes Data Warehouse nicht genutzt werden. Die Migration dauert pro Data Warehouse pro Terabyte an gespeicherten Daten ca. eine Stunde. Im Rahmen der automatischen Migration fallen keine Kosten für Sie an.

> [!NOTE]
> Nach Abschluss der Migration wird das Data Warehouse zur Nutzung wieder online geschaltet.
>
>

Microsoft führt die folgenden Schritte aus, um die Migration abzuschließen (die von Ihnen keine Beteiligung verlangen). Beispiel: Ihr vorhandenes Data Warehouse mit Standardspeicher hat derzeit den Namen „MyDW“.

1. Microsoft benennt „MyDW“ in „MyDW_DO_NOT_USE_[Zeitstempel]“ um.
2. Microsoft hält „MyDW_DO_NOT_USE_[Zeitstempel]“ an. Während dieser Zeit wird eine Sicherung erstellt. Es kann zu mehreren Anhalte- und Fortsetzungsvorgängen kommen, falls bei diesem Prozess Probleme auftreten.
3. Microsoft erstellt anhand der in Schritt 2 erstellen Sicherung ein neues Data Warehouse in Storage Premium mit dem Namen „MyDW“. „MyDW“ wird erst angezeigt, nachdem der Wiederherstellungsvorgang abgeschlossen wurde.
4. Nach Abschluss der Wiederherstellung wird „MyDW“ zurück auf die gleichen Data Warehouse-Einheiten und in den (angehaltenen oder aktiven) Zustand wie vor der Migration versetzt.
5. Nach Abschluss der Migration löscht Microsoft „MyDW_DO_NOT_USE_[Zeitstempel]“.

> [!NOTE]
> Die folgenden Einstellungen werden im Rahmen der Migration nicht übernommen:
>
> * Die Überwachung auf Datenbankebene muss erneut aktiviert werden.
> * Firewallregeln auf Datenbankebene müssen erneut hinzugefügt werden. Firewallregeln auf Serverebene sind nicht betroffen.
>
>

### <a name="automatic-migration-schedule"></a>Zeitplan für die automatische Migration
Automatische Migrationen erfolgen zwischen 18:00 Uhr und 06:00 Uhr (Ortszeit in der jeweiligen Region) und im Rahmen des folgenden Ausfallzeitplans.

| **Region** | **Geschätztes Startdatum** | **Geschätztes Enddatum** |
|:--- |:--- |:--- |
| Australien (Osten) |19. März 2018 |20. März 2018 |
| China, Osten |Bereits migriert |Bereits migriert |
| China, Norden |Bereits migriert |Bereits migriert |
| Deutschland, Mitte |Bereits migriert |Bereits migriert |
| Deutschland, Nordosten |Bereits migriert |Bereits migriert |
| Indien, Westen |19. März 2018 |20. März 2018 |
| Japan, Westen |19. März 2018 |20. März 2018 |
| USA Nord Mitte |Bereits migriert |Bereits migriert |

## <a name="self-migration-to-premium-storage"></a>Selbst durchgeführte Migration zu Storage Premium
Wenn Sie steuern möchten, wann genau Ihr Data Warehouse nicht verfügbar sein soll, können Sie die folgenden Schritte ausführen, um ein vorhandenes Data Warehouse von Standardspeicher zu Storage Premium zu migrieren. Wenn Sie sich hierfür entscheiden, müssen Sie die selbst durchgeführte Migration vor Beginn der automatischen Migration in dieser Region abschließen. Dadurch wird sichergestellt, dass Sie alle Risiken aufgrund eines Konflikts mit der automatischen Migration vermeiden (siehe den [Zeitplan für die automatische Migration][automatic migration schedule]).

### <a name="self-migration-instructions"></a>Anleitung zur selbst durchgeführten Migration
Verwenden Sie zum Migrieren Ihres Data Warehouse die Sicherung- und Wiederherstellungsfunktionen. Der Wiederherstellungsanteil der Migration dauert pro Data Warehouse pro TB gespeicherter Daten ca. eine Stunde. Führen Sie die [Schritte zum Umbenennen während der Migration][steps to rename during migration] aus, wenn Sie nach Abschluss der Migration den gleichen Namen beibehalten möchten.

1. [Halten][Pause] Sie Ihr Data Warehouse an. 
2. Führen Sie eine [Wiederherstellung][Restore] der neuesten Momentaufnahme durch.
3. Löschen Sie das vorhandene Data Warehouse in Standardspeicher. **Wenn Sie diesen Schritt nicht ausführen, werden Ihnen beide Data Warehouses in Rechnung gestellt.**

> [!NOTE]
>
> Achten Sie beim Wiederherstellen Ihres Data Warehouse darauf, dass der letzte verfügbare Wiederherstellungspunkt nach dem Anhalten des Data Warehouse liegt.
>
> Die folgenden Einstellungen werden im Rahmen der Migration nicht übernommen:
>
> * Die Überwachung auf Datenbankebene muss erneut aktiviert werden.
> * Firewallregeln auf Datenbankebene müssen erneut hinzugefügt werden. Firewallregeln auf Serverebene sind nicht betroffen.
>
>

#### <a name="rename-data-warehouse-during-migration-optional"></a>Umbenennen des Data Warehouse während der Migration (optional)
Zwei Datenbanken auf demselben logischen Server können nicht den gleichen Namen haben. SQL Data Warehouse unterstützt jetzt die Möglichkeit zum Umbenennen eines Data Warehouse.

Beispiel: Ihr vorhandenes Data Warehouse mit Standardspeicher hat derzeit den Namen „MyDW“.

1. Benennen Sie "MyDW" mithilfe des folgenden ALTER DATABASE-Befehls um. (In diesem Beispiel benennen wir es in „MyDW_BeforeMigration“ um.) Dieser Befehl beendet alle vorhandenen Transaktionen und muss in der Masterdatenbank ausgeführt werden, um erfolgreich zu sein.
   ```
   ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
   ```
2. [Halten][Pause] Sie „MyDW_BeforeMigration“ an. 
3. Führen Sie mithilfe der neuesten Momentaufnahme eine [Wiederherstellung][Restore] durch, und erstellen Sie dabei eine neue Datenbank mit dem vorherigen Namen (z.B. „MyDW“).
4. Löschen Sie „MyDW_BeforeMigration“. **Wenn Sie diesen Schritt nicht ausführen, werden Ihnen beide Data Warehouses in Rechnung gestellt.**


## <a name="next-steps"></a>Nächste Schritte
Mit dem Wechsel zu Storage Premium haben wir auch die Anzahl von Datenbank-Blobdateien in der zugrunde liegenden Architektur Ihrer Data Warehouse-Instanz erhöht. Um die Leistungsvorteile dieses Wechsels zu maximieren, erstellen Sie Ihre gruppierten Columnstore-Indizes mit dem folgenden Skript neu. Das Skript erzwingt, dass einige Ihrer Daten in den zusätzlichen Blobs gespeichert werden. Wenn Sie keine Maßnahmen ergreifen, werden die Daten im Laufe der Zeit auf natürliche Weise verteilt, sobald Sie weitere Daten in Ihre Tabellen laden.

Wenn Probleme mit Ihrem Data Warehouse auftreten, können Sie [ein Supportticket erstellen][create a support ticket] und als möglichen Grund „Migration zu Storage Premium“ angeben.

<!--Image references-->

<!--Article references-->
[automatic migration schedule]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md
[Restore]: sql-data-warehouse-restore-database-portal.md
[steps to rename during migration]: #optional-steps-to-rename-during-migration
[scale compute power]: quickstart-scale-compute-portal.md
[mediumrc role]: resource-classes-for-workload-management.md

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage for greater performance predictability]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure Portal]: https://portal.azure.com
