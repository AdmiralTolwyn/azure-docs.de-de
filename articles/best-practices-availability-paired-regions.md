<properties
	pageTitle="Geschäftskontinuität und Notfallwiederherstellung: Azure Regionspaare | Microsoft Azure"
	description="Azure-Regionspaare stellen sicher, dass Anwendungen auch bei Datencenterausfällen stabil laufen."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="raynew"/>

# Geschäftskontinuität und Notfallwiederherstellung: Azure-Regionspaare

## Was sind Regionspaare?

Azure betreibt Datencenter in mehreren Gebieten auf der ganzen Erde. Bei einem Azure-Gebiet handelt es sich um einen definierten Bereich der Erde, der mindestens eine Azure-Region enthält. Eine Azure-Region ist ein Bereich in einem Gebiet mit mindestens einem Datencenter.

Jeder Azure-Region ist innerhalb des gleichen geografischen Gebiets eine andere Region als Regionspartner zugeordnet. Einzige Ausnahme ist „Brasilien, Süden“: Dieser Region ist eine Region außerhalb des geografischen Gebiets zugeordnet.


![Azure-Gebiet](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Abbildung 1 – Diagramm von Azure-Regionspaaren



| Gebiet | Regionspaare | |
| :-------------| :-------------   | :-------------      |
| Nordamerika | USA (Mitte/Norden) | USA (Mitte/Süden) |
| Nordamerika | USA (Ost) | USA (West) |
| Nordamerika | USA (Ost 2) | USA, Mitte |
|Nordamerika | USA, Westen 2 | USA, Westen-Mitte |
| Europa | Nordeuropa | Westeuropa |
| Asien | Südostasien | Ostasien |
| China | Ostchina | Nordchina |
| Japan | Japan Ost | Japan (Westen) |
| Brasilien | Brasilien, Süden (1) | USA (Mitte/Süden) |
| Australien | Australien (Osten) | Australien (Südosten) |
| US Government | US Government, Iowa | US Government, Virginia |
| Indien | Indien (Mitte) | Indien, Süden |
| Kanada | Kanada, Mitte | Kanada, Osten |


Tabelle 1: Übersicht über Azure-Regionspaare

> (1) Brasilien, Süden ist besonders, da dieses Gebiet ein Paar mit einer Region außerhalb des eigenen Gebiets bildet. „USA, Süden-Mitte“ fungiert als sekundäre Region von „Brasilien, Süden“, „Brasilien, Süden“ aber nicht als sekundäre Region von „USA, Süden-Mitte“.

Wir empfehlen das Replizieren von Workloads zwischen Regionalpaaren, um von Richtlinien für Isolierung und Verfügbarkeit von Azure zu profitieren. Beispielsweise werden geplante Azure-Systemupdates in Regionspaaren sequenziell (nicht gleichzeitig) bereitgestellt. Das heißt, dass selbst im seltenen Fall eines fehlerhaften Updates beide Regionen nicht gleichzeitig betroffen sind. Darüber hinaus wird im unwahrscheinlichen Fall eines umfassenden Ausfalls die Wiederherstellung mindestens einer Region aus jedem Paar priorisiert.

## Beispiel für ein Regionspaar
Abbildung 2 zeigt eine hypothetische Anwendung, die das Regionspaar für die Notfallwiederherstellung verwendet. Die grünen Zahlen markieren die regionsübergreifenden Aktivitäten von drei Azure-Diensten (Azure Compute, Storage und Database) und ihre Konfiguration für eine regionsübergreifende Replikation. Die eindeutigen Vorteile einer Bereitstellung in Regionspaaren werden von den orangefarbenen Zahlen hervorgehoben.


![Übersicht über die Vorteile von Regionspaaren](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Abbildung 2 – Hypothetisches Azure-Regionspaar

## Regionsübergreifende Aktivitäten
Wie in Abbildung 2 dargestellt.

![1Grün](./media/best-practices-availability-paired-regions/1Green.png) **Azure Compute (PaaS)** – Sie müssen zusätzliche Serverressourcen im Voraus bereitstellen, um sicherzustellen, dass Ressourcen während eines Notfalls in einer anderen Region zur Verfügung stehen. Weitere Informationen finden Sie unter [Technischer Leitfaden zur Resilienz in Azure](./resiliency/resiliency-technical-guidance.md).

![2Grün](./media/best-practices-availability-paired-regions/2Green.png) **Azure Storage** – Georedundanter Speicher (GRS) wird beim Erstellen eines Azure Storage-Kontos standardmäßig konfiguriert. Mithilfe von GRS werden Ihre Daten dreimal in der primären Region und dreimal im Regionspaar automatisch repliziert. Weitere Informationen finden Sie unter [Redundanzoptionen für Azure Storage](storage/storage-redundancy.md).


![3Grün](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL-Datenbanken** – Mithilfe der standardmäßigen Azure SQL-Georeplikation können Sie eine asynchrone Replikation von Transaktionen in ein Regionspaar konfigurieren. Bei Wahl der Option "Premium" für die Georeplikation können Sie die Replikation in jede Region der Welt konfigurieren. Allerdings wird empfohlen, dass Sie diese Ressourcen für die meisten Notfallwiederherstellungsszenarien in einem Regionspaar bereitstellen. Weitere Informationen finden Sie unter [Georeplikation in Azure SQL-Datenbank](./sql-database/sql-database-geo-replication-overview.md).

![4Grün](./media/best-practices-availability-paired-regions/4Green.png) **Azure-Ressourcen-Manager (ARM)** – ARM bietet grundsätzlich eine regionsübergreifende logische Isolierung von Dienstverwaltungskomponenten. Dies bedeutet, dass sich logische Fehler in einer Region weniger wahrscheinlich auf eine andere auswirken.

## Vorteile eines Regionspaars
Wie in Abbildung 2 dargestellt.

![5Orange](./media/best-practices-availability-paired-regions/5Orange.png) **Physische Isolierung** – Zwischen Azure-Datencentern in einem Regionspaar sollte nach Möglichkeit eine Entfernung von mindestens 480 km bestehen, was allerdings nicht in allen Gebieten zweckmäßig oder möglich ist. Durch die Trennung physischer Datencenter wird die Wahrscheinlichkeit einer gleichzeitigen Beeinträchtigung beider Regionen durch Naturkatastrophen, politische Unruhen, Stromausfälle oder physische Netzwerkausfälle verringert. Die Isolierung unterliegt den Einschränkungen des jeweiligen Gebiets (Größe, Verfügbarkeit der Energieversorgungs-/Netzwerkinfrastruktur, Vorschriften usw.).

![6Orange](./media/best-practices-availability-paired-regions/6Orange.png)**Von der Plattform bereitgestellte Replikation** – Einige Dienste, wie z. B. georedundanter Speicher, bieten eine automatische Replikation in das Regionspaar.

![7Orange](./media/best-practices-availability-paired-regions/7Orange.png) **Reihenfolge der Regionswiederherstellung** – Im Falle eines umfassenden Ausfalls hat bei jedem Paar die Wiederherstellung einer der Regionen Priorität. Für Anwendungen, die in Regionspaaren bereitgestellt sind, wird die priorisierte Wiederherstellung einer der Regionen garantiert. Wenn eine Anwendung in Regionen bereitgestellt ist, die kein Paar bilden, kann sich die Wiederherstellung verzögern. Im schlimmsten Fall werden die gewählten Regionen ggf. zuletzt wiederhergestellt.

![8Orange](./media/best-practices-availability-paired-regions/8Orange.png) **Sequenzielle Updates** – Geplante Azure-Systemupdates werden zur Minimierung von Ausfallzeit, der Auswirkungen von Fehlern und logischen Ausfällen im seltenen Fall eines fehlerhaften Updates sequenziell (nicht gleichzeitig) eingespielt.


![9Orange](./media/best-practices-availability-paired-regions/9Orange.png) **Speicherort von Daten** – Eine Region befindet sich innerhalb desselben Gebiets wie ihr Paar (mit Ausnahme von Brasilien, Süden), um steuerliche und rechtliche Anforderungen an den Speicherort von Daten zu erfüllen.

<!---HONumber=AcomDC_0824_2016-->