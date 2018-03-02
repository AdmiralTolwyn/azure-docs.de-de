---
title: "Ressourceneinschränkungen für Azure SQL-Datenbanken | Microsoft Docs"
description: "Diese Seite beschreibt einige allgemeine Ressourceneinschränkungen für Azure SQL-Datenbanken."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 884e519f-23bb-4b73-a718-00658629646a
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
<<<<<<< HEAD
ms.date: 02/27/2018
ms.author: carlrab
ms.openlocfilehash: 839705b902b8e1343c1e0bda97a2ec1dc6b47042
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/28/2018
=======
ms.date: 02/12/2018
ms.author: carlrab
ms.openlocfilehash: 47f42c10a791caa8ab20401574fb853ad3e4f0e9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/21/2018
>>>>>>> 6def4612c80a1e9bab4008c57d68ccdbec8d0794
---
# <a name="azure-sql-database-resource-limits"></a>Ressourceneinschränkungen für Azure SQL-Datenbank

## <a name="single-database-storage-sizes-and-performance-levels"></a>Einzeldatenbank: Speichergrößen und Leistungsstufen

Die folgende Tabelle enthält die verfügbaren Ressourcen für Einzeldatenbanken auf jeder Dienstebene und Leistungsstufe. Sie können mit [Azure-Portal](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), [PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), [Azure CLI](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli) oder [REST-API](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api) Dienstebene, Leistungsstufe und Speichermenge für eine einzelne Datenbank festlegen.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

## <a name="single-database-change-storage-size"></a>Einzeldatenbank: Ändern der Speichergröße

- Der DTU-Preis für eine einzelne Datenbank enthält eine bestimmte Menge Speicher ohne zusätzliche Kosten. Zusätzlicher Speicher über die inbegriffene Speichermenge hinaus kann gegen zusätzliche Gebühren bis zur Obergrenze in Inkrementen von 250 GB bis zu 1 TB und dann in Inkrementen von 256 GB über 1 TB hinaus bereitgestellt werden. Informationen zu enthaltenen Speichermengen und Maximalgrößen finden Sie unter [Einzeldatenbank: Speichergrößen und Leistungsstufen](#single-database-storage-sizes-and-performance-levels).
- Zusätzlicher Speicher für eine einzelne Datenbank kann durch Erhöhen der maximalen Größe mithilfe von [Azure-Portal](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update) oder [REST-API](/rest/api/sql/databases/update) bereitgestellt werden.
- Der Preis für zusätzlichen Speicher für eine einzelne Datenbank errechnet sich aus der Menge an zusätzlich bereitgestelltem Speicher multipliziert mit dem Einheitenpreis für zusätzlichen Speicher für die Dienstebene. Ausführliche Informationen zu den Preisen für zusätzlichen Speicherplatz siehe [SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Einzeldatenbank: Ändern von DTUs

Nach erster Auswahl von Dienstebene, Leistungsstufe und Speichermenge können Sie eine einzelne Datenbank basierend auf der tatsächlichen Erfahrung mit [Azure-Portal](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update) oder [REST-API](/rest/api/sql/databases/update) dynamisch zentral hoch- oder herunterskalieren. 

Das folgende Video zeigt die dynamische Änderung der Leistungsstufe zum Heraufsetzen verfügbarer DTUs für eine einzelne Datenbank.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Wenn Sie die Dienst- und/oder die Leistungsstufe einer Datenbank ändern, wird ein Replikat der ursprünglichen Datenbank mit der neuen Leistungsebene erstellt und anschließend die Verbindung auf dieses Replikat umgestellt. Während dieses Vorgangs gehen keine Daten verloren. Allerdings sind die Verbindungen zur Datenbank inaktiv, während kurz auf das Replikat umgestellt wird. Daher werden möglicherweise einige aktive Transaktionen zurückgesetzt. Die Dauer der Umstellung kann variieren, aber sie liegt im Allgemeinen unter vier Sekunden und in 99 % der Fälle unter 30 Sekunden. Falls im Moment der Verbindungstrennung viele Transaktionen stattfinden, kann die Umstellung unter Umständen auch länger dauern. 

Die Dauer des gesamten zentralen Hochskalierungsvorgangs hängt sowohl von der Größe als auch vom Diensttarif der Datenbank vor und nach der Änderung ab. Beispielsweise sollte eine 250-GB-Datenbank beim Wechsel in einen, aus einem oder innerhalb eines Standarddiensttarifs innerhalb von sechs Stunden zentral hochskalieren. Eine Datenbank der gleichen Größe, die ihre Leistungsstufen innerhalb der Premium-Dienstebene ändert, sollte das zentrale Hochskalieren innerhalb von drei Stunden vollziehen.

> [!TIP]
> Weitere Informationen zum Überwachen laufender Vorgänge finden Sie unter: [Verwalten von Vorgängen mithilfe der SQL-REST-API](/rest/api/sql/Operations/List), [Verwalten von Vorgängen mithilfe der Befehlszeilenschnittstelle](/cli/azure/sql/db/op) und [Überwachen von Vorgängen mit T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). Verwenden Sie auch diese beiden PowerShell-Befehle: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) und [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Wenn Sie ein Upgrade auf eine höhere Dienstebene oder Leistungsstufe durchführen, wird die maximale Datenbankgröße nicht erhöht, sofern Sie nicht ausdrücklich eine höhere Maximalgröße angeben.
* Für ein Downgrade einer Datenbank muss die verwendete Datenbankmenge kleiner als die maximal zulässige Größe der Zieldienstebene und Leistungsstufe sein. 
* Bei einem Downgrade von **Premium** auf die **Standard**-Ebene fallen zusätzliche Speicherkosten an, wenn sowohl (1) die maximale Größe der Datenbank von der Zielleistungsstufe unterstützt wird als auch (2) die maximale Größe die enthaltene Speichermenge der Zielleistungsstufe überschreitet. Wenn z.B. eine P1-Datenbank mit einer maximalen Größe von 500 GB auf S3 reduziert wird, fallen zusätzliche Speicherkosten an, da S3 eine maximale Größe von 500 GB unterstützt und die enthaltene Speichermenge nur 250 GB beträgt. Daher beträgt die zusätzliche Speichermenge 500 GB – 250 GB = 250 GB. Ausführliche Informationen zu den Preisen für zusätzlichen Speicherplatz siehe [SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/). Wenn die tatsächlich verwendete Speichermenge kleiner als die enthaltene Speichermenge ist, können Sie diese zusätzlichen Kosten vermeiden, indem Sie die maximale Datenbankgröße auf die enthaltene Menge reduzieren. 
* Bei einem Upgrade einer Datenbank, für die [Georeplikation](sql-database-geo-replication-portal.md) aktiviert ist, führen Sie vor dem Upgrade der primären Datenbank zunächst ein Upgrade der zugehörigen sekundären Datenbanken auf die gewünschte Leistungsstufe durch (allgemeine Anleitung). Bei einem Upgrade auf eine andere Edition muss zunächst ein Upgrade der sekundären Datenbank durchgeführt werden.
* Bei einem Downgrade einer Datenbank mit aktivierter [Georeplikation](sql-database-geo-replication-portal.md) führen Sie vor dem Downgraden der sekundären Datenbank zunächst ein Downgrade der zugehörigen primären Datenbanken auf die gewünschte Leistungsstufe durch (allgemeine Anleitung). Bei einem Downgrade auf eine andere Edition muss zunächst ein Downgrade der primären Datenbank durchgeführt werden.
* Die Angebote des Wiederherstellungsdienstes variieren für die verschiedenen Dienstebenen. Wenn Sie ein Downgrade auf den Tarif **Basic** durchführen, verfügen Sie über einen kürzeren Aufbewahrungszeitraum von Sicherungen. Weitere Informationen finden Sie unter [Azure SQL-Datenbanksicherungen](sql-database-automated-backups.md).
* Die neuen Eigenschaften für die Datenbank werden erst angewendet, wenn die Änderungen abgeschlossen sind.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Einzeldatenbank: Einschränkungen von P11 und P15 bei Maximalgrößen über 1 TB

Eine maximale Größe von mehr als 1 TB für P11- und P15-Datenbanken wird derzeit in folgenden Regionen unterstützt: „USA, Osten 2“, „USA, Westen“, „USA Gov Virginia“, „Europa, Westen“, „Deutschland, Mitte“, „Asien, Südosten“, „Japan, Osten“, „Australien, Osten“, „Kanada, Mitte“ und „Kanada, Osten“. Die folgenden Aspekte und Einschränkungen gelten für P11- und P15-Datenbanken mit einer maximalen Größe von mehr als 1 TB:

- Wenn Sie beim Erstellen einer Datenbank eine Maximalgröße von mehr als 1 TB wählen (Wert 4 TB oder 4.096 GB), tritt für den Erstellungsbefehl ein Fehler auf, falls die Datenbank in einer nicht unterstützten Region bereitgestellt wird.
- Für vorhandene P11- und P15-Datenbanken, die sich in einer der unterstützten Regionen befinden, können Sie den maximalen Speicher über 1 TB hinaus in Inkrementen von 256 GB auf bis zu 4 TB erhöhen. Um festzustellen, ob eine größere Größe in Ihrer Region unterstützt wird, verwenden Sie die Funktion [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql), oder überprüfen Sie die Größe der Datenbank im Azure-Portal. Die Aktualisierung einer vorhandenen P11- oder P15-Datenbank kann nur mithilfe einer Prinzipalanmeldung auf Serverebene oder von Mitgliedern der Datenbankrolle „dbmanager“ ausgeführt werden. 
- Bei Ausführung eines Upgradevorgangs in einer unterstützten Region wird die Konfiguration umgehend aktualisiert. Die Datenbank bleibt während des Upgradevorgangs online. Allerdings können Sie die vollständige Menge an Speicher über 1 TB erst dann nutzen, wenn die tatsächlichen Datenbankdateien auf die neue Maximalgröße aktualisiert wurden. Die erforderliche Dauer hängt von der Größe der zu aktualisierenden Datenbank ab. 
- Beim Erstellen oder Aktualisieren einer P11- oder P15-Datenbank können Sie nur in Inkrementen von 256 GB zwischen einer Maximalgröße von 1 TB und 4 TB wählen. Beim Erstellen einer P11-/P15-Datenbank ist die Standardspeicheroption von 1 TB vorausgewählt. Für Datenbanken, die sich in einer der unterstützten Regionen befinden, können Sie das Speichermaximum für eine neue oder vorhandene Einzeldatenbank auf maximal 4 TB erhöhen. Für alle anderen Regionen kann die Maximalgröße nicht mehr als 1 TB betragen. Der Preis ändert sich nicht, wenn Sie 4TB Speicher wählen.
- Wenn die maximale Größe einer Datenbank auf mehr als 1 TB festgelegt ist, kann sie auch dann nicht in 1 TB geändert werden, wenn die tatsächlich verwendete Speichermenge unter 1 TB liegt. Folglich können Sie eine P11- oder P15-Datenbank mit einer maximalen Größe über 1 TB nicht auf eine 1-TB-P11- oder 1-TB-P15- oder niedrigere Leistungsstufe (z.B. P1-P6) herabstufen. Diese Einschränkung gilt auch für Wiederherstellungs- und Kopierszenarien, einschließlich Point-in-Time- und Geowiederherstellung, langfristige Aufbewahrung von Sicherungen und Datenbankkopiervorgänge. Sobald eine Datenbank mit einer maximalen Größe über 1 TB konfiguriert wird, müssen alle Wiederherstellungsvorgänge dieser Datenbank für ein P11-/P15-Ziel mit einer maximalen Größe von über 1 TB ausgeführt werden.
- Szenarien für aktive Georeplikation:
   - Einrichten einer Georeplikationsbeziehung: Falls es sich bei der primären Datenbank um eine P11- oder P15-Datenbank handelt, müssen auch die sekundären Datenbanken vom Typ „P11“ oder „P15“ sein. Niedrigere Leistungsstufen werden als sekundäre Datenbanken abgelehnt, da sie mehr als 1 TB nicht unterstützen können.
   - Aktualisieren der primären Datenbank in einer Georeplikationsbeziehung: Die Änderung der maximalen Größe für eine primäre Datenbank in mehr als 1 TB löst die gleiche Änderung für die sekundäre Datenbank aus. Beide Upgrades müssen erfolgreich ausgeführt werden, damit die Änderung für die primäre Datenbank wirksam wird. Für die Option mit mehr als 1 TB gelten Regionseinschränkungen. Wenn sich die sekundäre Datenbank in einer Region befindet, die nicht mehr als 1 TB unterstützt, wird die primäre Datenbank nicht aktualisiert.
- Die Verwendung des Import/Export-Diensts zum Laden von Datenbanken des Typs „P11-P15“ mit mehr als 1 TB wird nicht unterstützt. Verwenden Sie „SqlPackage.exe“, um Daten zu [importieren](sql-database-import.md) und [exportieren](sql-database-export.md).

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Pool für elastische Datenbanken: Speichergrößen und Leistungsstufen

Die folgenden Tabellen enthalten die verfügbaren Ressourcen für Pools für elastische SQL-Datenbank-Instanzen auf jeder Dienstebene und Leistungsstufe. Sie können mit [Azure-Portal](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), [Azure CLI](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli) oder [REST-API](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api) Dienstebene, Leistungsstufe und Speichermenge festlegen.

> [!NOTE]
> Die Ressourcengrenzwerte einzelner Datenbanken in Pools für elastische Datenbanken entsprechen im Allgemeinen hinsichtlich DTU-Anzahl und Dienstebene denen einzelner Datenbanken außerhalb von Pools. Auf eine S2-Datenbank können z.B. maximal 120 Mitarbeiter gleichzeitig zugreifen. Also können auch 120 Mitarbeiter gleichzeitig auf eine Datenbank in einem Standard-Pool zugreifen, wenn max. 50 DTUs pro Datenbank im Pool zulässig sind (entspricht S2).
>

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Wenn alle DTUs eines Pools für elastische Datenbanken verwendet werden, erhält jede Datenbank im Pool gleich viel Ressourcen zum Verarbeiten von Abfragen. Der SQL-Datenbank-Dienst bietet eine faire gemeinsame Nutzung von Ressourcen durch Datenbanken, indem gleiche Slices an Computezeit zugesichert werden. Diese faire gemeinsame Nutzung in Pools für elastische Datenbanken ergänzt jegliche Ressourcen, die jeder Datenbank auf andere Weise garantiert werden, wenn das DTU-Minimum pro Datenbank auf einen Wert ungleich null festgelegt ist.

### <a name="database-properties-for-pooled-databases"></a>Eigenschaften von Datenbanken in einem Pool

Die folgende Tabelle beschreibt die Eigenschaften von Datenbanken in einem Pool.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| Max. Anz. von eDTUs pro Datenbank |Die maximale Anzahl von eDTUs, die jede Datenbank im Pool verwenden kann, sofern basierend auf der Nutzung durch andere Datenbanken im Pool verfügbar. Die maximale Anzahl der eDTUs pro Datenbank ist keine Ressourcengarantie für eine Datenbank. Dies ist eine globale Einstellung, die für alle Datenbanken im Pool gilt. Legen Sie die maximale Anzahl der eDTUs pro Datenbank hoch genug fest, sodass Spitzen bei der Datenbanknutzung verarbeitet werden können. Sie sollten ein gewisses Maß an Mehrlast einplanen, da für den Pool im Allgemeinen von Nutzungsmustern starker und schwacher Auslastung ausgegangen wird, bei der aber nicht alle Datenbanken gleichzeitig stark ausgelastet sind. Angenommen, die Spitzenauslastung pro Datenbank beträgt 20 eDTUs und betrifft nur 20 % der 100 Datenbanken im Pool. Wenn die eDTU-Höchstanzahl pro Datenbank auf 20 eDTUs festgelegt ist, ist es sinnvoll, die fünffache Mehrlast für den Pool einzuplanen und die eDTUs pro Pool auf 400 festzulegen. |
| Min. Anz. von eDTUs pro Datenbank |Die minimale Anzahl der eDTUs, die für jede Datenbank im Pool garantiert werden können. Dies ist eine globale Einstellung, die für alle Datenbanken im Pool gilt. Die Mindestanzahl der eDTUs pro Datenbank kann auf 0 festgelegt werden. Dies ist auch der Standardwert. Diese Eigenschaft ist auf einen Wert zwischen 0 und der durchschnittlichen eDTU-Nutzung pro Datenbank festgelegt. Das Produkt aus der Anzahl von Datenbanken im Pool und der Mindestzahl von eDTUs pro Datenbank darf die tatsächliche Anzahl der eDTUs pro Pool nicht übersteigen. Wenn ein Pool beispielsweise 20 Datenbanken umfasst und die Mindestanzahl der eDTUs pro Datenbank auf 10 eDTUs festgelegt hat, müssen mindestens 200 eDTUs pro Pool festgelegt sein. |
| Max. Speicherkapazität pro Datenbank |Der maximale Speicherplatz für eine Datenbank in einem Pool. In einem Pool zusammengefasste Datenbanken nutzen den Poolspeicher gemeinsam, daher ist der Datenbankspeicher auf den jeweils kleineren Wert des verbleibenden Poolspeichers oder des maximalen Speicherplatzes pro Datenbank beschränkt. Der maximale Speicherplatz pro Datenbank bezieht sich auf die maximale Größe der Datendateien und beinhaltet nicht den von Protokolldateien belegten Speicherplatz. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>Pool für elastische Datenbanken: Ändern der Speichergröße

- Der eDTU-Preis für einen Pool für elastische Datenbanken enthält eine bestimmte Menge Speicher ohne zusätzliche Kosten. Zusätzlicher Speicher über die inbegriffene Speichermenge hinaus kann gegen zusätzliche Gebühren bis zur Obergrenze in Inkrementen von 250 GB bis zu 1 TB und dann in Inkrementen von 256 GB über 1 TB hinaus bereitgestellt werden. Informationen zu enthaltenen Speichermengen und Maximalgrößen finden Sie unter [Pool für elastische Datenbanken: Speichergrößen und Leistungsstufen](#elastic-pool-storage-sizes-and-performance-levels).
- Zusätzlicher Speicher für einen Pool für elastische Datenbanken kann durch Erhöhen der maximalen Größe mithilfe von [Azure-Portal](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) oder [REST-API](/rest/api/sql/elasticpools/update) bereitgestellt werden.
- Der Preis für zusätzlichen Speicher für einen Pool für elastische Datenbanken errechnet sich aus der Menge an zusätzlich bereitgestelltem Speicher multipliziert mit dem Einheitenpreis für zusätzlichen Speicher für die Dienstebene. Ausführliche Informationen zu den Preisen für zusätzlichen Speicherplatz siehe [SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-edtus"></a>Pool für elastische Datenbanken: Ändern von eDTUs

Sie können die verfügbaren Ressourcen für einen Pool für elastische Datenbanken basierend auf den Anforderungen der Ressource mit [Azure-Portal](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) oder [ REST-API](/rest/api/sql/elasticpools/update) herauf- oder herabsetzen.

- Beim Neuskalieren von Pool-eDTUs werden Datenbankverbindungen vorübergehend getrennt. Dies ist ebenfalls beim Neuskalieren von DTUs für eine einzelne (nicht zu einem Pool gehörende) Datenbank der Fall. Ausführliche Informationen zu Dauer und Auswirkungen der Trennung von Verbindungen für eine Datenbank während Neuskalierungsvorgängen finden Sie unter [Einzeldatenbank: Ändern von DTUs](#single-database-change-storage-size). 
- Die Dauer der Neuskalierung von Pool-eDTUs kann von der Gesamtmenge des Speicherplatzes abhängen, die von allen Datenbanken im Pool verwendet wird. Im Allgemeinen beträgt die Neuskalierungs-Latenzzeit durchschnittlich höchstens 90 Minuten pro 100 GB. Wenn beispielsweise der gesamte, von allen Datenbanken im Pool verwendete Speicherplatz 200 GB beträgt, beträgt die erwartete Neuskalierungs-Latenzzeit höchstens drei Stunden. In einigen Fällen kann die Neuskalierungs-Latenzzeit in der Ebene „Standard“ oder „Basic“ unabhängig von der Menge des verwendeten Speicherplatzes weniger als fünf Minuten betragen.
- Änderungen der minimalen oder maximalen Anzahl von eDTUs pro Datenbank werden in der Regel in höchstens fünf Minuten durchgeführt.
- Beim Herabstufen von Pool-eDTUs muss die verwendete Speichermenge kleiner als die maximal zulässige Größe der Zieldienstebenen- und Leistungsstufen-eDTUs sein.
- Beim Neuskalieren von Pool-eDTUs fallen zusätzliche Speicherkosten an, wenn (1) die maximale Speichergröße des Pools vom Zielpool unterstützt wird, und (2) die maximale Speichergröße die enthaltene Speichermenge für den Zielpool überschreitet. Wenn z.B. ein 100-eDTU-Standard-Pool mit einer maximalen Größe von 100 GB zu einem 50-eDTU-Standard-Pool herabgestuft wird, fallen zusätzliche Speicherkosten an, da der Zielpool eine maximale Größe von 100 GB unterstützt, und die enthaltene Speichermenge nur 50 GB beträgt. Daher beträgt die zusätzliche Speichermenge 100 GB – 50 GB = 50 GB. Ausführliche Informationen zu den Preisen für zusätzlichen Speicherplatz siehe [SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/). Wenn die tatsächlich verwendete Speichermenge kleiner als die enthaltene Speichermenge ist, können Sie diese zusätzlichen Kosten vermeiden, indem Sie die maximale Datenbankgröße auf die enthaltene Menge reduzieren. 

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Was ist die maximale Anzahl von Servern und Datenbanken?

| Maximum | Wert |
| :--- | :--- |
| Datenbanken pro Server | 5.000 |
| Anzahl von Servern pro Abonnement und Region | 21 |
|||

> [!IMPORTANT]
> Wenn sich die Anzahl der Datenbanken dem Grenzwert pro Server nähert, kann Folgendes geschehen:
> <br> • Höhere Latenz bei der Ausführung von Abfragen bei der Masterdatenbank.  Dies bezieht sich auch auf die Ansichten der Ressourcennutzungsstatistiken (z. B. sys.resource_stats) ein.
> <br> • Höhere Latenz bei Verwaltungsvorgängen und dem Rendern von Portalblickpunkten. Dazu gehört auch das Aufzählen von Datenbanken auf dem Server gehört.

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>Was geschieht, wenn die Ressourcengrenzen von Datenbank und Pool für elastische Datenbanken erreicht werden?

### <a name="compute-dtus-and-edtus"></a>Compute (DTUs und eDTUs)

Bei zunehmender Datenbank-Computenutzung (gemessen an DTUs und eDTUs) steigt die Abfragewartezeit und kann auch durch einen Timeout beendet werden. Unter diesen Bedingungen können Abfragen durch den Dienst in eine Warteschlange gestellt werden, und ihnen werden Ressourcen für die Ausführung bereitgestellt, sobald diese frei werden.
Wenn eine hohe Computenutzung festgestellt wird, stehen folgende Optionen als Gegenmaßnahmen zur Verfügung:

- Erhöhen der Leistungsstufe der Datenbank oder des Pools für elastische Datenbanken, um mehr DTUs oder eDTUs für die Datenbank bereitzustellen. Informationen finden Sie unter [Einzeldatenbank: Ändern von DTUs](#single-database-change-dtus) und [Pool für elastische Datenbanken: Ändern von eDTUs](#elastic-pool-change-edtus).
- Optimieren der Abfragen, um die Ressourcenverwendung für jede Abfrage zu reduzieren. Weitere Informationen finden Sie unter [Abfrageoptimierung/Abfragehinweise](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Speicher

Wenn der verwendete Datenbankspeicherplatz die maximale Größe erreicht, treten bei Einfügungen in die Datenbank und Updates, die die Datenmenge erhöhen, Fehler auf, sodass Clients eine [Fehlermeldung](sql-database-develop-error-messages.md) erhalten. Die Datenbankanweisungen SELECT und DELETE werden weiterhin erfolgreich ausgeführt.

Wenn eine hohe Speicherplatznutzung festgestellt wird, stehen folgende Optionen als Gegenmaßnahmen zur Verfügung:

- Erhöhen der maximalen Größe der Datenbank bzw. des Pools für elastische Datenbanken oder Änderung der Leistungsstufe, um mehr enthaltenen Speicher zu erhalten. Siehe [Ressourceneinschränkungen für Azure SQL-Datenbank](sql-database-resource-limits.md).
- Wenn sich die Datenbank in einem Pool für elastische Datenbanken befindet, kann sie auch aus dem Pool heraus verschoben werden, damit ihr Speicherplatz nicht mit anderen Datenbanken gemeinsam genutzt wird.

### <a name="sessions-and-workers-requests"></a>Sitzungen und Worker (Anforderungen) 

Die maximale Anzahl gleichzeitiger Sitzungen und Worker wird von Dienstebene und Leistungsstufe (DTUs und eDTUs) bestimmt.  Neue Anforderungen werden abgelehnt, wenn Sitzung oder Worker Grenzwerte erreicht haben, und Clients erhalten eine Fehlermeldung. Während die Anzahl der verfügbaren Verbindungen von der Anwendung gesteuert werden kann, ist die Anzahl paralleler Worker oft schwieriger zu schätzen und zu steuern. Dies gilt insbesondere während der Spitzenlastzeiten, wenn Datenbankressourcen-Grenzen erreicht werden und Worker aufgrund länger laufender Abfragen Schlange stehen. 

Wenn eine hohe Sitzungs- oder Workernutzung festgestellt wird, stehen folgende Optionen als Gegenmaßnahmen zur Verfügung:
- Erhöhen der Dienstebene oder Leistungsstufe der Datenbank oder des Pools für elastische Datenbanken. Informationen finden Sie unter [Einzeldatenbank: Ändern der Speichergröße](#single-database-change-storage-size), [Einzeldatenbank: Ändern von DTUs](#single-database-change-dtus), [Pool für elastische Datenbanken: Ändern der Speichergröße](#elastic-pool-change-storage-size) und [Pool für elastische Datenbanken: Ändern von eDTUs](#elastic-pool-change-edtus).
- Optimieren von Abfragen, um die Ressourcenverwendung durch die einzelnen Abfragen zu verringern, wenn die Ursache der zunehmenden Auslastung durch Worker Konflikte bezüglich der Computeressourcen sind. Weitere Informationen finden Sie unter [Abfrageoptimierung/Abfragehinweise](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu Dienstebenen finden Sie unter [Verfügbare Leistungsoptionen für eine Azure SQL-Datenbank](sql-database-service-tiers.md).
- Informationen zu einzelnen Datenbanken finden Sie unter [Ressourceneinschränkungen für Azure SQL-Datenbank](sql-database-resource-limits.md).
- Informationen zu Pools für elastische Datenbanken finden Sie unter [Pools für elastische Datenbanken als Hilfe beim Verwalten und Skalieren mehrerer SQL-Datenbanken](sql-database-elastic-pool.md).
- Informationen zu allgemeinen Azure-Einschränkungen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md).
- Informationen zu DTUs und eDTUs finden Sie unter [Datenbanktransaktionseinheiten (DTUs) und elastische Datenbanktransaktionseinheiten (eDTUs)](sql-database-what-is-a-dtu.md).
- Informationen zu Größenbeschränkungen für tempdb finden Sie unter https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
