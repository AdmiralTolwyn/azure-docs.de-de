---
title: 'SAP HANA: Speicherkonfigurationen für virtuelle Azure-Computer | Microsoft-Dokumentation'
description: Speicherempfehlungen für virtuelle Computer mit SAP HANA-Bereitstellung.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d062b6fff9693d5bda75edd65b8fe88d834eff57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735861"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA: Speicherkonfigurationen für virtuelle Azure-Computer

Azure bietet verschiedene geeignete Speichertypen für virtuelle Azure-Computer mit SAP HANA. Für SAP HANA-Bereitstellungen kommen unter anderem folgende Azure-Speichertypen in Frage: 

- SSD Standard
- SSD Premium
- SSD Ultra (befindet sich momentan in der Public Preview-Phase und wird noch nicht für SAP-Produktionsanwendungen unterstützt)

Weitere Informationen zu diesen Datenträgertypen finden Sie im Artikel [Auswählen eines Datenträgertyps](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types).

Azure bietet zwei Bereitstellungsmethoden für virtuelle Festplatten (VHDs) in Azure Storage Standard und Premium. Wenn das allgemeine Szenario dies zulässt, sollten Sie Bereitstellungen mit [verwalteten Azure-Datenträgern](https://azure.microsoft.com/services/managed-disks/) verwenden. 

Eine Liste der Speichertypen und deren Vereinbarungen zum Servicelevel für IOPS- und Speicherdurchsatz finden Sie in der [Azure-Dokumentation für verwaltete Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/).

**Empfehlung: Verwenden Sie Azure Storage Premium zusammen mit der Azure-Schreibbeschleunigung sowie Azure Managed Disks für die Bereitstellung.**

In der lokalen Umgebung mussten Sie sich nur selten Gedanken über die E/A-Subsysteme und deren Funktionen machen. Das lag daran, dass der Hersteller des Geräts sicherstellen musste, dass die Mindestspeicheranforderungen für SAP HANA erfüllt sind. Da Sie die Azure-Infrastruktur selbst erstellen, müssen Sie mit einigen dieser Anforderungen vertraut sein. Aufgrund der geforderten Merkmale für den Mindestdurchsatz ist Folgendes erforderlich:

- Ermöglichen von Lese-/Schreibvorgängen für **/hana/log** mit 250 MB/s bei E/A-Größen von 1 MB
- Aktivieren der Leseaktivität mit mindestens 400 MB/s in **/hana/data** bei E/A-Größen von 16 MB und 64 MB
- Aktivieren der Schreibaktivität mit mindestens 250 MB/s in **/hana/data** bei E/A-Größen von 16 MB und 64 MB

Da für DBMS-Systeme (und somit auch für SAP HANA) eine geringe Speicherlatenz wichtig ist, behalten sie Daten im Arbeitsspeicher. Der kritische Pfad beim Speichern sind in der Regel die Schreibvorgänge in das Transaktionsprotokoll der DBMS-Systeme. Aber auch Vorgänge wie das Schreiben von Sicherungspunkten oder das Laden von Daten in den Arbeitsspeicher nach der Wiederherstellung nach einem Systemabsturz können wichtig sein. Daher ist für die Volumes **/hana/data** und **/hana/log** die Verwendung von Azure Premium-Datenträgern **zwingend** erforderlich. Um den minimalen Durchsatz von **/hana/log** und **/hana/data** zu erreichen, wie er von SAP gewünscht ist, müssen Sie mithilfe von MDADM oder LVM über mehrere Azure Storage Premium-Datenträger ein RAID 0-Volume erstellen und die RAID-Volumes als die Volumes **/hana/data** und **/hana/log** verwenden. 

**Empfehlung: Als Stripegrößen für RAID 0 wird Folgendes empfohlen:**

- 64 KB oder 128 KB für **/hana/data**
- 32 KB für **/hana/log**

> [!NOTE]
> Sie müssen keine Redundanzebenen mit RAID-Volumes konfigurieren, da Azure Storage Premium und Standard drei Images einer virtuellen Festplatte beibehalten. Die Nutzung eines RAID-Volumes dient nur dazu, Volumes zu konfigurieren, die genügend E/A-Durchsatz bieten.

Das Kumulieren einer Reihe von Azure-VHDs unter einem RAID-Volume ist für den IOPS- und Speicherdurchsatz kumulativ. Wenn Sie also ein RAID 0-Volume über drei Azure Storage Premium-P30-Datenträgern platzieren, erhalten Sie den dreifachen IOPS- und den dreifachen Speicherdurchsatz eines einzelnen Azure Storage Premium-P30-Datenträgers.

Bei den Cacheempfehlungen unten werden E/A-Merkmale für SAP HANA gemäß der folgenden Liste zugrunde gelegt:

- Es gibt kaum Workload durch Lesezugriffe auf die HANA-Datendateien. Ausnahmen betreffen umfangreiche E/As nach dem Neustart der HANA-Instanz oder wenn Daten in HANA geladen werden. Ein anderer Fall umfangreicherer Lese-E/As für Datendateien sind Sicherungen der HANA-Datenbank. Das hat zur Folge, dass Lesecaches meistens nicht sinnvoll sind, da in den meisten dieser Fälle alle Volumes mit Datendateien vollständig gelesen werden müssen.
- Das Schreiben in die Datendateien geschieht in Bursts auf der Grundlage der HANA-Sicherungspunkte und der HANA-Absturzwiederherstellung. Das Schreiben von Sicherungspunkten erfolgt asynchron und hält Benutzertransaktionen in keiner Weise auf. Beim Schreiben von Daten während der Wiederherstellung nach Abstürzen ist die Leistung ein kritischer Faktor, um schnell wieder zu einem reaktionsbereiten System zu kommen. Die Wiederherstellung nach Abstürzen dürfte allerdings eher eine Ausnahmesituation darstellen
- Es gibt kaum Lesevorgänge aus den HANA-Wiederholungsdateien. Ausnahmen bilden umfangreiche E/A-Vorgänge beim Ausführen von Sicherungen des Transaktionsprotokolls, der Wiederherstellung nach Abstürzen oder in der Neustartphase einer HANA-Instanz.  
- Der hauptsächliche Workload bei SAP HANA-Wiederholungsprotokolldateien besteht aus Schreibvorgängen. Abhängig von der Art des Workloads kann die Größe von E/As bis zu 4 KB herunter- oder in anderen Fällen bis zu 1 MB oder mehr heraufreichen. Wartezeiten beim Schreiben in das SAP HANA-Wiederholungsprotokoll sind ein kritischer Faktor für die Leistung.
- Alle Schreibvorgänge müssen zuverlässig und dauerhaft auf einem Datenträger gespeichert werden.

**Empfehlung: Aufgrund dieser E/A-Muster von SAP HANA sollte das Caching für die verschiedenen Volumes unter Verwendung von Azure Storage Premium wie folgt festgelegt werden:**

- **/hana/data** – kein Caching
- **/hana/log** – kein Caching – mit Ausnahme der M-Serie (weitere Informationen dazu weiter unten in diesem Dokument)
- **/hana/shared** – Read-Caching


Darüber hinaus sollten Sie den E/A-Gesamtdurchsatz eines virtuellen Computers beachten, wenn Sie die Größe festlegen oder sich für einen virtuellen Computer entscheiden. Der VM-Speicherdurchsatz im Allgemeinen ist im Artikel [Arbeitsspeicheroptimierte Größen virtueller Computer](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) beschrieben.

## <a name="linux-io-scheduler-mode"></a>E/A-Scheduler-Modus für Linux
Linux verfügt über mehrere verschiedene E/A-Scheduling-Modi. Linux-Anbieter und SAP empfehlen im Allgemeinen, den E/A-Scheduler-Modus für Datenträgervolumes von **cfq** in **noop** zu ändern. Details finden Sie im [SAP-Hinweis 1984798](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Speicherlösung für die Produktion mit Azure-Schreibbeschleunigung für virtuelle Azure-Computer der M-Serie
Die Azure-Schreibbeschleunigung ist eine Funktion, die exklusiv für virtuelle Azure-Computer der M-Serie eingeführt wird. Der Name macht bereits deutlich, dass der Zweck der Funktion die Verbesserung der E/A-Latenz bei Schreibvorgängen mit Azure Storage Premium ist. Für SAP HANA ist die Schreibbeschleunigung nur für das Volume **/hana/log** vorgesehen. **/hana/data** und **/hana/log** sind daher separate Volumes, und die Azure-Schreibbeschleunigung unterstützt nur das Volume **/hana/log**. 

> [!IMPORTANT]
> Die SAP HANA-Zertifizierung für virtuelle Computer der Azure M-Serie gilt ausschließlich mit der Azure-Schreibbeschleunigung für das Volume **/hana/log**. Folglich müssen SAP HANA-Bereitstellungen in Produktionsszenarien auf virtuellen Computern der Azure M-Serie für das Volume **/hana/log** mit der Azure-Schreibbeschleunigung konfiguriert werden.  

> [!NOTE]
> Überprüfen Sie für Produktionsszenarien in der [SAP-Dokumentation für IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html), ob ein bestimmter VM-Typ von SAP für SAP HANA unterstützt wird.

**Empfehlung: Für Produktionsszenarien werden folgende Konfigurationen empfohlen:**

| VM-SKU | RAM | Maximal VM-E/A<br /> Throughput | /hana/data | /hana/log | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2\.850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Virtuelle Computer vom Typ „M416xx_v2“ wurden von Microsoft noch nicht öffentlich verfügbar gemacht. Überprüfen Sie, ob der Speicherdurchsatz für die verschiedenen vorgeschlagenen Volumes für die Workload ausreicht, die Sie ausführen möchten. Wenn die Workload größere Volumes für **/hana/data** und **/hana/log** erfordert, müssen Sie die Anzahl der Azure Storage Premium-VHDs erhöhen. Wenn Sie ein Volume mit mehr VHDs ausstatten als in der Liste angegeben, erhöht sich der IOPS- und E/A-Durchsatz innerhalb der Grenzen des Azure-VM-Typs.

Die Azure-Schreibbeschleunigung funktioniert nur in Verbindung mit [verwalteten Azure-Datenträgern](https://azure.microsoft.com/services/managed-disks/). Daher müssen zumindest die Azure Storage Premium-Datenträger, die das Volume **/hana/log** bilden, als verwaltete Datenträger bereitgestellt werden.

Die Anzahl von Azure Storage Premium-VHDs pro VM, die von der Azure-Schreibbeschleunigung unterstützt werden können, ist begrenzt. Die aktuellen Limits lauten wie folgt:

- 16 VHDs für eine VM der Typen M128xx und M416xx
- 8 VHDs für eine VM der Typen M64xx und M208xx
- 4 VHDs für eine M32xx-VM

Ausführlichere Anweisungen zum Aktivieren der Azure-Schreibbeschleunigung finden Sie im Artikel [Schreibbeschleunigung](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Details und Einschränkungen für die Azure-Schreibbeschleunigung finden Sie in der gleichen Dokumentation.

**Empfehlung: Verwenden Sie die Schreibbeschleunigung für Datenträger, die das Volume „/hana/log“ bilden.**


## <a name="cost-conscious-azure-storage-configuration"></a>Kostenbewusste Azure Storage-Konfiguration
In der folgenden Tabelle ist eine Konfiguration mit VM-Typen aufgeführt, die Kunden ebenfalls verwenden, um SAP HANA auf Azure-VMs zu hosten. Einige VM-Typen erfüllen unter Umständen nicht alle Mindestspeicherkriterien für SAP HANA oder werden von SAP nicht offiziell für SAP HANA unterstützt. Aber bisher scheinen diese VMs für Nicht-Produktionsszenarien problemlos zu funktionieren. **/hana/data** und **/hana/log** werden zu einem einzelnen Volume zusammengefasst. Dadurch kann sich der Einsatz der Azure-Schreibbeschleunigung zu einem limitierenden Faktor im IOPS-Bereich entwickeln.

> [!NOTE]
> Überprüfen Sie für Szenarien mit SAP-Unterstützung in der [SAP-Dokumentation für IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html), ob ein bestimmter VM-Typ von SAP für SAP HANA unterstützt wird.

> [!NOTE]
> Eine Änderung gegenüber früheren Empfehlungen für eine preisgünstige Lösung stellt der Wechsel von [Azure HDD Standard-Laufwerken](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) zu [Azure SSD Standard-Datenträgern](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) mit höherer Leistung und Zuverlässigkeit dar.


| VM-SKU | RAM | Maximal VM-E/A<br /> Throughput | „/hana/data“ und „/hana/log“<br /> Striping mit LVM oder MDADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 × E30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 × E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GB | 1000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 × E30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 × E30 | 1 x E6 | 1 x E6 |2 × E30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 × E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 × E30 | 1 x E10 | 1 x E6 | 2 × E40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 × E30 | 1 x E10 | 1 x E6 | 2 × E50 |
| M208s_v2 | 2\.850 GiB | 1000 MB/s | 4 x P30 | 1 × E30 | 1 x E10 | 1 x E6 |  3 × E40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 1 × E30 | 1 x E10 | 1 x E6 |  4 × E40 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 1 × E30 | 1 x E10 | 1 x E6 |  4 × E40 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 1 × E30 | 1 x E10 | 1 x E6 |  4 × E50 |


Virtuelle Computer vom Typ „M416xx_v2“ wurden von Microsoft noch nicht öffentlich verfügbar gemacht. Die Datenträger, die für die kleineren Typen virtueller Computer mit 3 x P20 empfohlen werden, haben Übergröße im Vergleich zu den Speicherplatzempfehlungen, die im [SAP-Whitepaper zu TDI Speicher](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) angegeben sind. Die in der Tabelle gezeigte Wahl wurde getroffen, um ausreichend Datenträgerdurchsatz für SAP HANA bereitzustellen. Sie können problemlos Änderungen am Volume **/hana/backup** vornehmen, dessen Größe so festgelegt wurde, dass es Sicherungen aufnehmen kann, die dem Zweifachen der Arbeitsspeichergröße entsprechen.   
Überprüfen Sie, ob der Speicherdurchsatz für die verschiedenen vorgeschlagenen Volumes für die Workload ausreicht, die Sie ausführen möchten. Wenn die Workload größere Volumes für **/hana/data** und **/hana/log** erfordert, müssen Sie die Anzahl der Azure Storage Premium-VHDs erhöhen. Wenn Sie ein Volume mit mehr VHDs ausstatten als in der Liste angegeben, erhöht sich der IOPS- und E/A-Durchsatz innerhalb der Grenzen des Azure-VM-Typs. 

> [!NOTE]
> Für die oben angegebenen Konfigurationen bietet die [Vereinbarung zum Servicelevel für einzelne virtuelle Azure-Computer](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) KEINE Vorteile, da Azure Storage Premium und Azure Storage Standard gemeinsam verwendet werden. Die Auswahl wurde jedoch getroffen, um die Kosten zu optimieren. Sie müssten für alle Datenträger, die oben als Azure Storage Standard (Sxx) angegeben sind, Storage Premium auswählen, um die Konfiguration des virtuellen Computers mit der Vereinbarung zum Servicelevel für einen einzelnen virtuellen Azure-Computer kompatibel zu machen.


> [!NOTE]
> Die aufgeführten Empfehlungen für die Datenträgerkonfiguration orientieren sich an Mindestanforderungen, die SAP gegenüber seinen Infrastrukturanbietern formuliert. In realen Kundenbereitstellungen und Workloadszenarien traten Situationen ein, in denen diese Empfehlungen noch keine ausreichenden Funktionen bereitstellen konnten. Hierbei konnte es sich um Situationen handeln, in denen Kunden ein schnelleres erneutes Laden der Daten nach einem Neustart von HANA benötigten, oder wo Sicherungskonfigurationen eine höhere Bandbreite zum Speicher benötigten. Ein anderer Fall war **/hana/log**, wobei 5.000 IOPS für die spezifische Workload nicht ausreichend waren. Betrachten Sie also diese Empfehlungen als Ausgangspunkt, und nehmen Sie Anpassungen auf Grundlage der Anforderungen der Workload vor.
>  

## <a name="azure-ultra-ssd-storage-configuration-for-sap-hana"></a>Azure SSD Ultra-Speicherkonfiguration für SAP HANA
Microsoft führt derzeit einen neuen Azure Storage-Typ mit dem Namen [Azure SSD Ultra](https://azure.microsoft.com/updates/ultra-ssd-new-azure-managed-disks-offering-for-your-most-latency-sensitive-workloads/) ein. Der große Unterschied zwischen dem bisher angebotenen Azure-Speicher und SSD Ultra besteht darin, dass die Datenträgermerkmale nicht mehr an die Größe des Datenträgers gebunden sind. Als Kunde können Sie diese Merkmale für SSD Ultra definieren:

- Größe eines Datenträgers zwischen 4 GiB und 65.536 GiB
- IOPS-Bereich zwischen 100 IOPS und 160.000 IOPS (Höchstwert abhängig von VM-Typen)
- Speicherdurchsatz zwischen 300 MB/s und 2.000 MB/s

Details finden Sie im Artikel [Ankündigung von SSD Ultra – der nächsten Generation von Azure-Datenträgertechnologie (Vorschauversion)](https://azure.microsoft.com/blog/announcing-ultra-ssd-the-next-generation-of-azure-disks-technology-preview/).

Mit SSD Ultra können Sie einen einzelnen Datenträger definieren, der Ihre Anforderungen in Sachen Größe, IOPS und Datenträgerdurchsatz erfüllt. Sie müssen also keine Manager für logische Volumes wie LVM oder MDADM zusätzlich zu Azure Storage Premium mehr verwenden, um Volumes zu erstellen, die Ihre Anforderungen an IOPS und Durchsatz erfüllen. Sie können eine Konfigurationsmischung aus SSD Ultra und Storage Premium ausführen. Auf diese Weise können Sie die Verwendung von SSD Ultra auf die leistungskritischen Volumes „/hana/data“ und „/hana/log“ beschränken und die anderen Volumes mit Azure Storage Premium nutzen.

| VM-SKU | RAM | Maximal VM-E/A<br /> Throughput | Volume „/hana/data“ | E/A-Durchsatz für „/hana/data“ | IOPS für „/hana/data“ | Volume „/hana/log“ | E/A-Durchsatz für „/hana/log“ | IOPS für „/hana/log“ |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 500 MBit/s | 7\.500 | 256 GB | 500 MBit/s  | 2000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 500 MBit/s | 7\.500 | 256 GB | 500 MBit/s  | 2000 |
| M64ls | 512 GB | 1000 MB/s | 600 GB | 500 MBit/s | 7\.500 | 512 GB | 500 MBit/s  | 2000 |
| M64s | 1000 GiB | 1\.000 MB/s |  1\.200GB | 500 MBit/s | 7\.500 | 512 GB | 500 MBit/s  | 2000 |
| M64ms | 1750 GiB | 1\.000 MB/s | 2\.100 GB | 500 MBit/s | 7\.500 | 512 GB | 500 MBit/s  | 2000 |
| M128s | 2000 GiB | 2\.000 MB/s |2\.400 GB | 1\.200 MBit/s |9000 | 512 GB | 800 MBit/s  | 2000 | 
| M128ms | 3800 GiB | 2\.000 MB/s | 4\.800 GB | 1\.200 MBit/s |9000 | 512 GB | 800 MBit/s  | 2000 | 
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 3\.500 GB | 1\.000 MBit/s | 9000 | 512 GB | 500 MBit/s  | 2000 | 
| M208ms_v2 | 5700 GiB | 1\.000 MB/s | 7\.200 GB | 1\.000 MBit/s | 9000 | 512 GB | 500 MBit/s  | 2000 | 
| M416s_v2 | 5700 GiB | 2\.000 MB/s | 7\.200 GB | 1\.500 MBit/s | 9000 | 512 GB | 800 MBit/s  | 2000 | 
| M416ms_v2 | 11400 GiB | 2\.000 MB/s | 14.400 GB | 1\.500 MBit/s | 9000 | 512 GB | 800 MBit/s  | 2000 |   

Virtuelle Computer vom Typ „M416xx_v2“ wurden von Microsoft noch nicht öffentlich verfügbar gemacht. Die angegebenen Werte sind lediglich als Ausgangspunkt gedacht und müssen auf die tatsächlichen Anforderungen abgestimmt werden. Der Vorteil bei Azure SSD Ultra besteht darin, dass die Werte für IOPS und Durchsatz angepasst werden können, ohne die virtuellen Computer herunterzufahren oder die im System verarbeitete Workload anzuhalten.   

> [!NOTE]
> Bisher sind noch keine Speichermomentaufnahmen mit SSD Ultra-Speicher verfügbar. Dadurch wird die Verwendung von VM-Momentaufnahmen mit Azure Backup-Diensten blockiert.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter

- [Hochverfügbarkeit von SAP HANA für virtuelle Azure-Computer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).