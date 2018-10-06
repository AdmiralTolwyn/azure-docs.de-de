---
title: SAP HANA-Infrastrukturkonfigurationen und -Vorgänge in Azure | Microsoft-Dokumentation
description: Betriebshandbuch für SAP HANA-Systeme, die auf virtuellen Azure-Computern bereitgestellt werden.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: db2d7fbe395a6d7e332d79183a331b45f7767f51
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434059"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>SAP HANA-Infrastrukturkonfigurationen und -Vorgänge in Azure
Dieses Dokument enthält Anleitungen für die Konfiguration der Azure-Infrastruktur und SAP HANA-Betriebssystemen, die auf nativen virtuellen Azure-Computern bereitgestellt werden. Das Dokument enthält auch Informationen zur Konfiguration für die horizontale SAP HANA-Skalierung für die M128s-VM-SKU. Dieses Dokument ist nicht als Ersatz für die SAP-Standarddokumentation gedacht, zu der folgende Inhalte gehören:

- [SAP-Administratorhandbuch](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP-Installationshandbücher](https://service.sap.com/instguides)
- [SAP-Hinweise](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Voraussetzungen
Um dieses Handbuch zu verwenden, benötigen Sie grundlegende Kenntnisse der folgenden Azure-Komponenten:

- [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure-Netzwerk und virtuelle Netzwerke](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Weiterführende Informationen zu SAP NetWeaver und zu anderen SAP-Komponenten in Azure finden Sie im Abschnitt [SAP in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) in der [Azure-Dokumentation](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Grundlegende Überlegungen zum Setup
In den folgenden Abschnitten werden grundlegende Einrichtungsüberlegungen für die Bereitstellung von SAP HANA-Systemen auf virtuellen Azure-Computern beschrieben.

### <a name="connect-into-azure-virtual-machines"></a>Herstellen von Verbindungen mit virtuellen Azure-Computern
Wie im [Planungshandbuch für virtuelle Azure-Computer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) dokumentiert, gibt es zwei grundlegende Methoden zum Herstellen einer Verbindung mit virtuellen Azure-Computern:

- Herstellen einer Verbindung über das Internet und öffentliche Endpunkte auf einer Jump-VM oder auf dem virtuellen Computer, auf dem SAP HANA ausgeführt wird
- Herstellen einer Verbindung über eine [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) oder Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/)

Site-to-Site-Konnektivität über VPN oder ExpressRoute ist für Produktionsszenarien erforderlich. Dieser Typ von Verbindung ist auch für Nicht-Produktionsszenarien erforderlich, die in Produktionsszenarien übertragen, in denen SAP-Software verwendet wird. Die folgende Abbildung zeigt ein Beispiel zu standortübergreifender Konnektivität:

![Standortübergreifende Konnektivität](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Auswählen der Arten von virtuellen Azure-Computern
Die Arten von virtuellen Azure-Computern, die für Produktionsszenarien verwendet werden können, sind in der [SAP-Dokumentation für IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html) aufgeführt. Für Nicht-Produktionsszenarien ist eine größere Auswahl von virtuellen Azure-Computern verfügbar.

>[!NOTE]
> Für Nicht-Produktionsszenarien sollten Sie die Arten von virtuellen Computern verwenden, die im [SAP-Hinweis 1928533](https://launchpad.support.sap.com/#/notes/1928533) aufgeführt sind. Überprüfen Sie zur Verwendung von Azure-VMs für Produktionsszenarien die von SAP veröffentlichte [Liste zertifizierter IaaS-Plattformen ](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) auf für SAP HANA zertifizierte VMs.

Stellen Sie die virtuellen Computer in Azure bereit, indem Sie Folgendes verwenden:

- Das Azure-Portal
- Azure PowerShell-Cmdlets
- Die Azure-Befehlszeilenschnittstelle

Sie können eine vollständig installierte SAP HANA-Plattform auch über die [SAP Cloudplattform](https://cal.sap.com/) auf den Azure Virtual Machine-Diensten bereitstellen. Der Installationsvorgang ist unter [Bereitstellen von SAP S/4HANA oder BW/4HANA in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) beschrieben, oder nutzen Sie die [hier](https://github.com/AzureCAT-GSI/SAP-HANA-ARM) beschriebene Automatisierung.

### <a name="choose-azure-storage-type"></a>Auswählen des Azure-Speichertyps
Azure bietet zwei Speichertypen, die sich für virtuelle Azure-Computer mit SAP HANA eignen:

- [Azure Storage Standard](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure Premium-Speicher](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure bietet zwei Bereitstellungsmethoden für virtuelle Festplatten (VHDs) in Azure Storage Standard und Premium. Wenn das allgemeine Szenario dies zulässt, sollten Sie Bereitstellungen mit [verwalteten Azure-Datenträgern](https://azure.microsoft.com/services/managed-disks/) verwenden.

Eine Liste der Speichertypen und deren Vereinbarungen zum Servicelevel für IOPS- und Speicherdurchsatz finden Sie in der [Azure-Dokumentation für verwaltete Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Konfigurieren des Speichers für virtuelle Computer in Azure

Bislang mussten Sie sich beim Kauf von SAP HANA-Geräten für lokale Umgebungen niemals Gedanken um die E/A-Subsysteme und die zugehörigen Funktionen kümmern. Denn der Hersteller des Geräts musste sicherstellen, dass die Mindestspeicheranforderungen für SAP HANA erfüllt sind. Wenn Sie die Azure-Infrastruktur selbst erstellen, sollten Sie sich auch über einige dieser Anforderungen bewusst sein. Zudem sollten Sie die Konfigurationsanforderungen kennen, die in den folgenden Abschnitten vorgestellt werden. Dies gilt auch, falls Sie die virtuellen Computer konfigurieren, auf denen Sie SAP HANA ausführen möchten. Einige der geforderten Merkmale ergeben sich durch folgende Notwendigkeiten:

- Aktivieren des Lese-/Schreibvolumes in **/hana/log** mit mindestens 250 MB/s bei E/A-Größen von 1 MB
- Aktivieren der Leseaktivität mit mindestens 400 MB/s in **/hana/data** bei E/A-Größen von 16 MB und 64 MB
- Aktivieren der Schreibaktivität mit mindestens 250 MB/s in **/hana/data** bei E/A-Größen von 16 MB und 64 MB

Da geringe Speicherlatenz wichtig für DBMS-Systeme ist, auch für solche wie SAP HANA, behalten Sie Daten im Arbeitsspeicher. Der kritische Pfad beim Speichern sind in der Regel die Schreibvorgänge in das Transaktionsprotokoll der DBMS-Systeme. Aber auch Vorgänge wie das Schreiben von Sicherungspunkten oder das Laden von Daten in den Arbeitsspeicher nach der Wiederherstellung nach einem Systemabsturz können wichtig sein. Daher ist es erforderlich, Azure Premium-Datenträger für die Volumes **/hana/data** und **/hana/log** zu verwenden. Um den minimalen Durchsatz von **/hana/log** und **/hana/data** zu erreichen, wie er von SAP gewünscht ist, müssen Sie mithilfe von MDADM oder LVM über mehrere Azure Storage Premium-Datenträger ein RAID 0-Volume erstellen und die RAID-Volumes als die Volumes **/hana/data** und **/hana/log** verwenden. Als Stripegrößen für RAID 0 gelten folgende Empfehlungen:

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
- Alle Schreibvorgänge müssen in zuverlässiger Weise dauerhaft auf einem Datenträger gespeichert werden

Als Folge dieser beobachteten E/A-Muster von SAP HANA sollten die Caches für verschiedene Volumes unter Verwendung von Azure Premium Storage wie folgt festgelegt werden:

- **/hana/data** – kein Caching
- **/hana/log** – kein Caching – mit Ausnahme der M-Serie (weitere Informationen dazu weiter unten in diesem Dokument)
- **/hana/shared** – Read-Caching


Darüber hinaus sollten Sie den E/A-Gesamtdurchsatz eines virtuellen Computers beachten, wenn Sie die Größe festlegen oder sich für einen virtuellen Computer entscheiden. Der VM-Speicherdurchsatz im Allgemeinen ist im Artikel [Arbeitsspeicheroptimierte Größen virtueller Computer](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) beschrieben.

#### <a name="cost-conscious-azure-storage-configuration"></a>Kostenbewusste Azure Storage-Konfiguration
In der folgenden Tabelle ist eine Konfiguration mit VM-Typen aufgeführt, die Kunden häufig verwenden, um SAP HANA auf Azure-VMs zu hosten. Es gibt möglicherweise einige VM-Typen, die nicht alle minimalen Kriterien für SAP HANA erfüllen. Aber bisher scheinen diese VMs für Nicht-Produktionsszenarien problemlos zu funktionieren. 

> [!NOTE]
> Überprüfen Sie für Produktionsszenarien in der [SAP-Dokumentation für IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html), ob ein bestimmter VM-Typ von SAP für SAP HANA unterstützt wird.


| VM-SKU | RAM | Maximal VM-E/A<br /> Throughput | „/hana/data“ und „/hana/log“<br /> Striping mit LVM oder MDADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GB | 384 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GB | 1000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


Die Datenträger, die für die kleineren Typen virtueller Computer mit 3 x P20 empfohlen werden, haben Übergröße im Vergleich zu den Speicherplatzempfehlungen, die im [SAP-Whitepaper zu TDI Speicher](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) angegeben sind. Die in der Tabelle gezeigte Wahl wurde getroffen, um ausreichend Datenträgerdurchsatz für SAP HANA bereitzustellen. Sie können problemlos Änderungen am Volume **/hana/backup** vornehmen, dessen Größe so festgelegt wurde, dass es Sicherungen aufnehmen kann, die dem Zweifachen der Arbeitsspeichergröße entsprechen.   
Überprüfen Sie, ob der Speicherdurchsatz für die verschiedenen vorgeschlagenen Volumes die Workload erfüllt, die Sie ausführen möchten. Wenn die Workload größere Volumes für **/hana/data** und **/hana/log** erfordert, müssen Sie die Anzahl der Azure Storage Premium-VHDs erhöhen. Durch das Ändern der Größe eines Volumes mit mehr VHDs, als aufgelistet sind, erhöhen Sie den IOPS- und E/A-Durchsatz innerhalb der Grenzen des Azure-VM-Typs. 

> [!NOTE]
> Für die oben angegebenen Konfigurationen bietet die [Vereinbarung zum Servicelevel für einzelne virtuelle Azure-Computer](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) KEINE Vorteile, da Azure Storage Premium und Azure Storage Standard gemeinsam verwendet werden. Die Auswahl wurde jedoch getroffen, um die Kosten zu optimieren.


#### <a name="azure-storage-configuration-to-benefit-for-meeting-single-vm-sla"></a>Azure Storage-Konfiguration, die Vorteile beim Erfüllen der Vereinbarung zum Servicelevel für eine einzelne VM bietet
Wenn Sie von der [Vereinbarung zum Servicelevel für einzelne virtuelle Azure-Computer](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) profitieren möchten, dürfen Sie nur Azure Storage Premium-VHDs verwenden.

> [!NOTE]
> Überprüfen Sie für Produktionsszenarien in der [SAP-Dokumentation für IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html), ob ein bestimmter VM-Typ von SAP für SAP HANA unterstützt wird.

| VM-SKU | RAM | Maximal VM-E/A<br /> Throughput | „/hana/data“ und „/hana/log“<br /> Striping mit LVM oder MDADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E16v3 | 128 GB | 384 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M64ls | 512 GB | 1000 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |


Die Datenträger, die für die kleineren Typen virtueller Computer mit 3 x P20 empfohlen werden, haben Übergröße im Vergleich zu den Speicherplatzempfehlungen, die im [SAP-Whitepaper zu TDI Speicher](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) angegeben sind. Die in der Tabelle gezeigte Wahl wurde getroffen, um ausreichend Datenträgerdurchsatz für SAP HANA bereitzustellen. Sie können problemlos Änderungen am Volume **/hana/backup** vornehmen, dessen Größe so festgelegt wurde, dass es Sicherungen aufnehmen kann, die dem Zweifachen der Arbeitsspeichergröße entsprechen.  
Überprüfen Sie, ob der Speicherdurchsatz für die verschiedenen vorgeschlagenen Volumes die Workload erfüllt, die Sie ausführen möchten. Wenn die Workload größere Volumes für **/hana/data** und **/hana/log** erfordert, müssen Sie die Anzahl der Azure Storage Premium-VHDs erhöhen. Durch das Ändern der Größe eines Volumes mit mehr VHDs, als aufgelistet sind, erhöhen Sie den IOPS- und E/A-Durchsatz innerhalb der Grenzen des Azure-VM-Typs. 




#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Speicherlösung mit der Azure-Schreibbeschleunigung für virtuelle Computer der Azure-M-Serie
Die Azure-Schreibbeschleunigung ist eine Funktion, die nur für VMs der M-Serie eingeführt wird. Der Name macht bereits deutlich, dass der Zweck der Funktion die Verbesserung der E/A-Latenz bei Schreibvorgängen mit Azure Storage Premium ist. Für SAP HANA ist die Schreibbeschleunigung nur für das Volume **/hana/log** vorgesehen. Daher müssen die bisher gezeigten Konfigurationen geändert werden. Die Hauptänderung besteht in der Unterteilung zwischen **/hana/data** und **/hana/log**, damit die Azure-Schreibbeschleunigung nur für das Volume **/hana/log** verwendet wird. 

> [!IMPORTANT]
> Die SAP HANA-Zertifizierung für virtuelle Computer der Azure M-Serie gilt ausschließlich mit der Azure-Schreibbeschleunigung für das Volume **/hana/log**. Folglich müssen SAP HANA-Bereitstellungen in Produktionsszenarien auf virtuellen Computern der Azure M-Serie für das Volume **/hana/log** mit der Azure-Schreibbeschleunigung konfiguriert werden.  

> [!NOTE]
> Überprüfen Sie für Produktionsszenarien in der [SAP-Dokumentation für IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html), ob ein bestimmter VM-Typ von SAP für SAP HANA unterstützt wird.

Die empfohlenen Konfigurationen sehen wie folgt aus:

| VM-SKU | RAM | Maximal VM-E/A<br /> Throughput | /hana/data | /hana/log | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Überprüfen Sie, ob der Speicherdurchsatz für die verschiedenen vorgeschlagenen Volumes die Workload erfüllt, die Sie ausführen möchten. Wenn die Workload größere Volumes für **/hana/data** und **/hana/log** erfordert, müssen Sie die Anzahl der Azure Storage Premium-VHDs erhöhen. Durch das Ändern der Größe eines Volumes mit mehr VHDs, als aufgelistet sind, erhöhen Sie den IOPS- und E/A-Durchsatz innerhalb der Grenzen des Azure-VM-Typs.

Die Azure-Schreibbeschleunigung funktioniert nur in Verbindung mit [verwalteten Azure-Datenträgern](https://azure.microsoft.com/services/managed-disks/). Daher müssen zumindest die Azure Storage Premium-Datenträger, die das Volume **/hana/log** bilden, als verwaltete Datenträger bereitgestellt werden.

Die Anzahl von Azure Storage Premium-VHDs pro VM, die von der Azure-Schreibbeschleunigung unterstützt werden können, ist begrenzt. Die aktuellen Limits lauten wie folgt:

- 16 VHDs für eine M128xx-VM
- 8 VHDs für eine M64xx-VM
- 4 VHDs für eine M32xx-VM

Ausführlichere Anweisungen zum Aktivieren der Azure-Schreibbeschleunigung finden Sie im Artikel [Schreibbeschleunigung](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Details und Einschränkungen für die Azure-Schreibbeschleunigung finden Sie in der gleichen Dokumentation.

> [!NOTE]
> Die aufgeführten Empfehlungen für die Datenträgerkonfiguration orientieren sich an Mindestanforderungen, die SAP gegenüber seinen Infrastrukturanbietern formuliert. In realen Kundenbereitstellungen und Workloadszenarien traten Situationen ein, in denen diese Empfehlungen noch keine ausreichenden Funktionen bereitstellen konnten. Hierbei konnte es sich um Situationen handeln, in denen Kunden ein schnelleres erneutes Laden der Daten nach einem Neustart von HANA benötigten, oder wo Sicherungskonfigurationen eine höhere Bandbreite zum Speicher benötigten. Andere Fälle umfassten **/hana/log**, wobei 5000 IOPS für die spezifische Workload nicht ausreichend waren. Betrachten Sie also diese Empfehlungen als Ausgangspunkt, und nehmen Sie Anpassungen auf Grundlage der Anforderungen der Workload vor.
>  

### <a name="set-up-azure-virtual-networks"></a>Einrichten von virtuellen Azure-Netzwerken
Wenn Sie Site-to-Site-Konnektivität mit Azure über VPN oder ExpressRoute haben, müssen Sie mindestens ein virtuelles Azure-Netzwerk haben, das über ein virtuelles Gateway mit der VPN- oder ExpressRoute-Verbindung verbunden ist. Bei einfachen Bereitstellungen kann das virtuelle Gateway in einem Subnetz des virtuellen Azure-Netzwerks (VNet) bereitgestellt werden, das auch die SAP HANA-Instanzen hostet. Um SAP HANA zu installieren, erstellen Sie zwei weitere Subnetze innerhalb des virtuellen Azure-Netzwerks. In einem Subnetz werden die virtuellen Computer gehostet, auf denen die SAP HANA-Instanzen ausgeführt werden. Im anderen Subnetz werden virtuelle Jumpbox- oder Verwaltungscomputer ausgeführt, auf denen SAP HANA Studio, andere Verwaltungssoftware oder Ihre Anwendungssoftware gehostet wird.

Wenn Sie die virtuellen Computer zum Ausführen von SAP HANA installieren, benötigen die virtuellen Computer Folgendes:

- Zwei installierte virtuelle Netzwerkkarten: eine Netzwerkkarte, um eine Verbindung mit dem Verwaltungssubnetz herzustellen, und eine Netzwerkkarte, um eine Verbindung aus dem lokalen Netzwerk oder aus anderen Netzwerken mit der SAP HANA-Instanz auf dem virtuellen Azure-Computer herzustellen
- Statische private IP-Adressen, die für beide virtuellen Netzwerkkarten bereitgestellt werden

> [!NOTE]
> Sie sollten einzelnen virtuellen Netzwerkkarten statische IP-Adressen über Azure-Tools zuweisen. Virtuellen Netzwerkkarten sollten nicht innerhalb des Gastbetriebssystems statische IP-Adressen zugewiesen werden. Einige Azure-Dienste wie der Azure Backup-Dienst basieren darauf, dass mindestens die primäre virtuelle Netzwerkkarte auf DHCP festgelegt ist und nicht auf statische IP-Adressen. Weitere Informationen finden Sie auch im Dokument zur [Problembehandlung bei der Sicherung virtueller Azure-Computer](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Wenn Sie einer VM mehrere statische IP-Adressen zuweisen müssen, müssen Sie ihr auch mehrere virtuelle Netzwerkkarten zuweisen.
>
>

Für dauerhafte Bereitstellungen müssen Sie jedoch eine virtuelle Rechenzentrums-Netzwerkarchitektur in Azure erstellen. Bei dieser Architektur empfiehlt sich die Trennung des Azure-VNet-Gateway, das sich mit dem lokalen Netzwerk verbindet, in ein separates Azure VNet. Dieses separate VNet sollte den gesamten Datenverkehr hosten, der entweder ins lokale Netzwerk oder ins Internet geleitet wird. Dieser Ansatz ermöglicht es Ihnen, Software zur Überprüfung und Protokollierung des Datenverkehrs, der in das virtuelle Rechenzentrum in Azure einfließt, in diesem separaten Hub-VNet bereitzustellen. Sie haben also ein VNet, das sämtliche Software und Konfigurationen bezüglich des ein- und ausgehenden Datenverkehrs zu Ihrer Azure-Bereitstellung hostet.

Die Artikel [Virtuelles Rechenzentrum in Microsoft Azure: Eine Netzwerkperspektive](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) und [Virtuelles Azure-Rechenzentrum und die Steuerungsebene für Unternehmen](https://docs.microsoft.com/azure/architecture/vdc/) enthalten weitere Informationen zum virtuellen Rechenzentrumsansatz und dem dazugehörigen Azure-VNet-Design.


>[!NOTE]
>Für den Datenverkehr zwischen dem VNet-Hub und dem Spoke-VNet mit [Azure-VNet-Peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) fallen zusätzliche [Kosten](https://azure.microsoft.com/pricing/details/virtual-network/) an. Basierend auf diesen Kosten müssen Sie möglicherweise Kompromisse zwischen dem Betrieb eines strengen Hub-Spoke-Netzwerkdesigns und dem Betrieb mehrerer [Azure-ExpressRoute-Gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) eingehen, die Sie mit „Spokes“ verbinden, um VNet-Peering zu umgehen. Für Azure-ExpressRoute-Gateways fallen jedoch ebenfalls zusätzliche [Kosten](https://azure.microsoft.com/pricing/details/vpn-gateway/) an. Darüber hinaus können zusätzliche Kosten für Software von Drittanbietern anfallen, die Sie für die Protokollierung, Überprüfung und Überwachung des Datenverkehrs verwenden. Abhängig von den Kosten für den Datenaustausch über VNet-Peering auf der einen Seite und den Kosten, die durch zusätzliche Azure-ExpressRoute-Gateways und zusätzliche Softwarelizenzen entstehen, auf der anderen, können Sie sich für eine Mikrosegmentierung innerhalb eines VNet entscheiden, indem Sie Subnetze als Isolationseinheit anstelle von VNets verwenden.


Einen Überblick über die verschiedenen Methoden zum Zuweisen von IP-Adressen finden Sie unter [IP-Adresstypen und Zuordnungsmethoden in Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Für VMs mit SAP HANA sollten Sie mit zugewiesenen statischen IP-Adressen arbeiten. Der Grund ist, dass einige Konfigurationsattribute für HANA IP-Adressen referenzieren.

[Azure-Netzwerksicherheitsgruppen (NSGs)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) werden verwendet, um Datenverkehr zu leiten, der an die SAP HANA-Instanz oder die Jumpbox weitergeleitet wird. Die Netzwerksicherheitsgruppen und [Anwendungssicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) sind mit dem SAP HANA-Subnetz und dem Verwaltungssubnetz verknüpft.

Die folgende Abbildung zeigt eine Übersicht eines allgemeinen Bereitstellungsschemas für SAP HANA gemäß einer Hub-Spoke-VNet-Architektur:

![Allgemeines Bereitstellungsschema für SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)

Um SAP HANA in Azure ohne Site-to-Site-Verbindung bereitzustellen, müssen Sie die SAP HANA-Instanz dennoch vor dem öffentlichen Internet schützen und hinter einem Weiterleitungsproxy verstecken. In diesem einfachen Szenario werden für die Bereitstellung in Azure integrierte DNS-Dienste verwendet, um Hostnamen aufzulösen. In einer komplexeren Bereitstellung, in der öffentlich zugängliche IP-Adressen verwendet werden, sind die in Azure integrierten DNS-Dienste besonders wichtig. Verwenden Sie Azure-NSGs und [Azure-NVAs](https://azure.microsoft.com/solutions/network-appliances/), um das Routing vom Internet in Ihre Azure-VNet-Architektur in Azure zu steuern und zu überwachen. Die folgende Abbildung zeigt ein allgemeines Schema für die Bereitstellung von SAP HANA ohne eine Site-to-Site-Verbindung in einer Hub-Spoke-VNet-Architektur:
  
![Allgemeines Bereitstellungsschema für SAP HANA ohne eine Standort-zu-Standort-Verbindung](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Eine weitere Beschreibung zur Verwendung von Azure-NVAs zum Steuern und Überwachen des Zugriffs aus dem Internet ohne die Hub-and-Spoke-VNet-Architektur finden Sie im Artikel [Bereitstellen hochverfügbarer virtueller Netzwerkgeräte](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Konfigurieren der Azure-Infrastruktur für die horizontale SAP HANA-Skalierung
Microsoft bietet eine VM-SKU der M-Serie, die für eine horizontale SAP HANA-Skalierung zertifiziert ist. Der VM-Typ M128s wurde für eine horizontale Skalierung von bis zu 16 Knoten zertifiziert. Informationen zu Änderungen hinsichtlich Zertifizierungen für horizontale SAP HANA-Skalierungen in Azure-VMs finden Sie in der [Liste der zertifizierten IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Die minimale Betriebssystemversionen für die Bereitstellung von horizontaler Skalierung in Azure-VMs sind:

- SUSE Linux 12 SP3
- Red Hat Linux 7.4

Zusammensetzung der horizontalen Skalierung mit 16 Knoten

- Ein Knoten ist der Masterknoten
- Bis zu 15 Knoten sind Workerknoten

>[!NOTE]
>In horizontalen Azure-VM-Skalierungen kann kein Standbyknoten verwendet werden.
>

Es gibt zwei Gründe, warum kein Standbyknoten konfiguriert werden kann:

- Azure bietet an dieser Stelle keinen nativen NFS-Dienst. Daher müssen NFS-Freigaben mithilfe von Drittanbieterfunktionen konfiguriert werden.
- Keine der NFS-Konfigurationen von Drittanbietern kann mit ihren auf Azure eingesetzten Lösungen die Speicherlatenzkriterien für SAP HANA erfüllen.

Daher können **/hana/data**- und **/hana/log**-Volumes nicht freigegeben werden. Die fehlende Freigabe dieser Volumes der einzelnen Knoten verhindert die Verwendung eines SAP HANA-Standbyknotens in einer horizontalen Skalierung.

Als Ergebnis wird das grundlegende Design für einen einzelnen Knoten in einer horizontalen Skalierung wie folgt aussehen:

![Grundlagen der horizontalen Skalierung eines einzelnen Knotens](media/hana-vm-operations/scale-out-basics.PNG)

Die grundlegende Konfiguration von einem VM-Knoten für die horizontale SAP HANA-Skalierung sieht wie folgt aus:

- Für **/hana/shared** erstellen Sie einen hochverfügbaren NFS-Cluster basierend auf SUSE Linux 12 SP3. Diese Cluster hostet die **/hana/shared**-NFS-Freigabe(n) Ihrer horizontalen Skalierung und SAP NetWeaver oder BW/4HANA Central Services. Informationen zum Erstellen einer solchen Konfiguration finden Sie im Artikel [Hochverfügbarkeit für NFS auf Azure-VMs unter SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
- Alle anderen Datenträgervolumes werden **NICHT** für die unterschiedlichen Knoten freigegeben und basieren **NICHT** auf NFS. Installationskonfigurationen und Schritte für horizontale HANA-Installationsskalierungen mit nicht freigegebenen **/hana/data**- und **/hana/log**-Volumes finden Sie im weiteren Verlauf dieses Dokuments.

>[!NOTE]
>Der in der Grafik dargestellte hoch verfügbare NFS-Cluster wird derzeit nur mit SUSE Linux unterstützt. Eine hoch verfügbare NFS-Lösung basierend auf Red Hat wird später erläutert.

Die Größenanpassung der Volumes für die Knoten entspricht dem horizontalen Skalieren mit Ausnahme von **/hana/shared**. Für M128s werden die folgenden Größen und Typen empfohlenen:

| VM-SKU | RAM | Maximal VM-E/A<br /> Throughput | /hana/data | /hana/log | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Überprüfen Sie, ob der Speicherdurchsatz für die verschiedenen vorgeschlagenen Volumes die Workload erfüllt, die Sie ausführen möchten. Wenn die Workload größere Volumes für **/hana/data** und **/hana/log** erfordert, müssen Sie die Anzahl der Azure Storage Premium-VHDs erhöhen. Durch das Ändern der Größe eines Volumes mit mehr VHDs, als aufgelistet sind, erhöhen Sie den IOPS- und E/A-Durchsatz innerhalb der Grenzen des Azure-VM-Typs. Verwenden Sie auch die Azure-Schreibbeschleunigung für die Datenträger, die das **/hana/log**-Volume bilden.
 
Im Dokument zu den [SAP HANA-TDI-Speicheranforderungen](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) finden Sie eine Formel, die die Größe des **/hana/shared**-Volumes für die horizontale Skalierung als die Speichergröße eines einzelnen Workerknotens pro 4 Workerknoten definiert.

Angenommen, Sie verwenden die für die horizontale SAP HANA-Skalierung zertifizierte Azure-VM M128s mit ca. 2 TB Speicher, können die SAP-Empfehlungen wie folgt zusammengefasst werden:

- Bei einem Masterknoten und bis zu neun Workerknoten benötigt **/hana/shared** 2 TB an Speicher. 
- Bei einem Masterknoten und fünf bis acht Workerknoten benötigt **/hana/shared** 4 TB an Speicher. 
- Bei einem Masterknoten und neun bis zwölf Workerknoten benötigt **/hana/shared** 6 TB an Speicher. 
- Bei einem Masterknoten und 12 bis 15 Workerknoten wird ein **/hana/shared**-Volume mit 8 TB Speicher benötigt.

Das andere wichtige Design, das in der Grafik der Einzelknotenkonfiguration für eine SAP HANA-VM mit horizontaler Skalierung dargestellt wird, ist die VNet-, besser gesagt die Subnetzkonfiguration. SAP empfiehlt dringend eine Trennung des client-/anwendungsbezogenen Datenverkehrs von der Kommunikation zwischen den HANA-Knoten. Wie in der Grafik dargestellt, können Sie dafür zwei verschiedene VNICs an die VM anfügen. Beide NICs befinden sich in unterschiedlichen Subnetzen und haben zwei verschiedene IP-Adressen. Anschließend steuern Sie den Datenverkehr mit Routingregeln über NSGs oder benutzerdefinierte Routen.

Vor allem in Azure gibt es keine Möglichkeiten und Methoden, um die Dienstqualität und Kontingente für bestimmte vNICs durchzusetzen. Daher eröffnet die Trennung der client-/anwendungsbezogenen Kommunikation und der knotenübergreifenden Kommunikation keine Möglichkeiten, den einen Datenverkehr gegenüber dem anderen zu priorisieren. Stattdessen bleibt die Trennung eine Sicherheitsmaßnahme beim Abschirmen der knotenübergreifenden Kommunikation bei horizontalen Skalierungen.  

>[!IMPORTANT]
>SAP empfiehlt dringend, den Netzwerkverkehr auf der Client-/Anwendungsseite und den knotenübergreifenden Datenverkehr zu trennen, wie in diesem Dokument beschrieben. Daher wird die Implementierung einer Architektur wie in den letzten Grafiken dringend empfohlen.
>

Aus Netzwerksicht sieht die minimal erforderliche Netzwerkarchitektur so aus:

![Grundlagen der horizontalen Skalierung eines einzelnen Knotens](media/hana-vm-operations/scale-out-networking-overview.PNG)

Bisher werden maximal 15 Workerknoten zusätzlich zu einem Masterknoten unterstützt.

Aus Speichersicht würde die Speicherarchitektur so aussehen:


![Grundlagen der horizontalen Skalierung eines einzelnen Knotens](media/hana-vm-operations/scale-out-storage-overview.PNG)

Das **/hana/shared**-Volume befindet sich in einer hoch verfügbaren NFS-Freigabekonfiguration. Alle anderen Laufwerke sind hingegen „lokal“ in einzelnen VMs implementiert. 

### <a name="highly-available-nfs-share"></a>Hoch verfügbare NFS-Freigabe
Der hoch verfügbare NFS-Cluster funktioniert derzeit nur mit SUSE Linux. Das Dokument [Hochverfügbarkeit für NFS auf Azure-VMs unter SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) beschreibt die entsprechende Einrichtung. Wenn Sie den NFS-Cluster nicht für andere HANA-Konfigurationen außerhalb des Azure-VNets, das die SAP HANA-Instanzen ausführt, freigeben, installieren Sie ihn im gleichen VNet. Installieren Sie ihn in einem eigenen Subnetz und stellen Sie sicher, dass nicht jeder beliebige Datenverkehr auf das Subnetz zugreifen kann. Stattdessen können Sie den Datenverkehr auf dieses Subnetz auf die IP-Adressen der VM beschränken, die den Datenverkehr auf das **/hana/shared**-Volume ausführen.

Hinsichtlich des vNIC einer HANA-VM mit horizontaler Skalierung, der für das Routing des **/hana/shared**-Datenverkehrs zuständig ist, wird folgendes empfohlen:

- Da der Datenverkehr zu **/hana/shared** moderat ist, leiten Sie ihn durch den vNIC, der dem Clientnetzwerk in der Minimalkonfiguration zugewiesen ist.
- Für den Datenverkehr zu **/hana/shared** können Sie auch ein drittes Subnetz im VNet implementieren, indem Sie die horizontale SAP HANA-Skalierung bereitstellen, und einen dritten vNIC zuweisen, der in diesem Subnetz gehostet wird. Verwenden Sie den dritten vNIC und die zugehörigen IP-Adresse für den Datenverkehr an die NFS-Freigabe. Sie können dann separate Zugriffs- und Routingregeln anwenden.

>[!IMPORTANT]
>Der Netzwerkverkehr zwischen den VMs, auf denen SAP HANA mit horizontaler Skalierung bereitgestellt ist, und das hochverfügbare NFS dürfen unter keinen Umständen über [NVA](https://azure.microsoft.com/solutions/network-appliances/)- oder ähnliche virtuelle Geräte geleitet werden. Azure-NSGs zählen nicht zu diesen Geräten. Überprüfen Sie Ihre Routingregeln, um sicherzustellen, dass NVAs oder ähnliche virtuelle Geräte beim Zugriff auf die hochverfügbare NFS-Freigabe von den VMs mit SAP HANA umgeleitet werden.
> 

Wenn Sie den hochverfügbaren NFS-Cluster für SAP HANA-Konfigurationen freigeben möchten, verschieben Sie all diese HANA-Konfigurationen in dasselbe VNet. 
 

### <a name="installing-sap-hana-scale-out-n-azure"></a>Installieren der horizontalen SAP HANA-Skalierung in Azure
Zum Installieren einer horizontalen SAP-Konfiguration führen Sie die folgenden Schritte aus:

- Bereitstellen einer neuen Azure-VNet-Architektur oder Anpassen einer bestehenden
- Bereitstellen der neuen VMs, die mithilfe von Azure Storage Premium-Volumes verwaltete werden
- Bereitstellen eines neuen hochverfügbaren NFS-Clusters oder Anpassen eines bestehenden
- Anpassen des Netzwerkroutings, um sicherzustellen, dass die knotenübergreifende Kommunikation zwischen VMs nicht durch [NVAs](https://azure.microsoft.com/solutions/network-appliances/) geleitet wird. Dasselbe gilt für den Datenverkehr zwischen den VMs und dem hoch verfügbaren NFS-Cluster.
- Installieren des SAP HANA-Masterknotens
- Anpassen der Konfigurationsparameter des SAP HANA-Masterknotens
- Fortsetzen der Installation der SAP HANA-Workerknoten

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Installation von SAP HANA mit horizontaler Skalierung
Wenn Ihre Azure VM-Infrastruktur bereitgestellt ist und alle anderen Vorbereitungen getroffen sind, müssen Sie die horizontale SAP HANA-Skalierung in diesen Schritten installieren:

- Installieren Sie den SAP HANA-Masterknoten gemäß der SAP-Dokumentation.
- **Nach der Installation müssen Sie die global.ini-Datei ändern und den Parameter „basepath_shared = no“ zur Datei hinzufügen**. Durch diesen Parameter kann SAP HANA mit horizontaler Skalierung ohne „freigegebene“ **/hana/data**- und **/hana/log**-Volumes zwischen den Knoten ausgeführt werden. Details finden Sie im [SAP-Hinweis #2080991](https://launchpad.support.sap.com/#/notes/2080991).
- Die SAP HANA-Instanz muss nach dem Ändern des global.ini-Parameters neu gestartet werden.
- Fügen Sie zusätzliche Workerknoten hinzu. Siehe auch <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>. Geben Sie während oder nach der Installation das interne Netzwerk für die knotenübergreifende SAP HANA-Kommunikation an, z.B. das lokale hdblcm. Eine detaillierte Dokumentation finden Sie in [SAP-Hinweis 2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Nach dieser Einrichtung wird die von Ihnen installierte horizontale Skalierung nicht freigegebene Datenträger für die Ausführung von **/hana/data** und **/hana/log** verwenden. Das **/hana/shared**-Volume wird dabei in der hoch verfügbaren NFS-Freigabe platziert.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA Dynamic Tiering 2.0 für Azure-VMs

Zusätzlich zu den SAP HANA-Zertifizierungen für Azure VMs der M-Serie wird SAP HANA Dynamic Tiering 2.0 auch in Microsoft Azure unterstützt (siehe Links zur Dokumentation zu SAP HANA Dynamic Tiering weiter unten). Es gibt zwar keinen Unterschied in der Installation des Produkts oder bei seinem Einsatz, aber z.B. stehen über SAP HANA Cockpit in einem virtuellen Azure-Computer einige wichtige Punkte zur Verfügung, die für die offizielle Unterstützung in Azure erforderlich sind. Diese wichtigen Punkte werden nachfolgend beschrieben. In diesem Artikel wird die Abkürzung „DT 2.0“ anstelle des vollständigen Namens Dynamic Tiering 2.0 verwendet.

SAP HANA Dynamic Tiering 2.0 wird von SAP BW oder S4HANA nicht unterstützt. Hauptanwendungsfälle sind derzeit native HANA-Anwendungen.


### <a name="overview"></a>Übersicht

Die folgende Abbildung stellt eine Übersicht der DT 2.0-Unterstützung in Microsoft Azure dar. Eine Reihe von obligatorischen Anforderungen muss ausgeführt werden, um der offiziellen Zertifizierung gerecht zu werden:

- DT 2.0 muss auf einer dedizierten Azure-VM installiert sein. Eine Ausführung auf der gleichen VM, auf der SAP HANA ausgeführt wird, ist nicht möglich.
- SAP HANA- und DT 2.0-VMs müssen in demselben Azure-VNET bereitgestellt werden.
- Die SAP HANA- und DT 2.0-VMs müssen mit aktiviertem beschleunigtem Azure-Netzwerkbetrieb bereitgestellt werden.
- Der Speichertyp für die DT 2.0-VMs muss Azure Storage Premium sein.
- Mehrere Azure-Datenträger müssen der DT 2.0-VM angefügt werden.
- Ein Software-RAID/Stripesetvolume mit alle Azure-Datenträger übergreifendem Striping muss (entweder per LVM oder mdadm) erstellt werden.

Dies wird in den folgenden Abschnitten ausführlicher erläutert.

![Übersicht über die Architektur von SAP HANA DT 2.0](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Dedizierte Azure-VM für SAP HANA DT 2.0

In Azure IaaS wird DT 2.0 nur auf einem dedizierten virtuellen Computer unterstützt. Es ist nicht zulässig, DT 2.0 auf der gleichen Azure-VM auszuführen, auf der die HANA-Instanz ausgeführt wird. Zunächst können zwei VM-Typen zum Ausführen von SAP HANA DT 2.0 verwendet werden:

M64-32ms, E32sv3 

Die VM-Typenbeschreibung finden Sie [hier](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

Unter Berücksichtigung der Grundidee von DT 2.0 – Auslagern „warmer“ Daten, um Kosten zu sparen – ist es sinnvoll, entsprechende VM-Größen zu verwenden. Es gibt keine strenge Regel bezüglich möglicher Kombinationen. Dies hängt von der spezifischen Kundenworkload ab.

Empfohlene Konfigurationen:

| SAP HANA-VM-Typ | DT 2.0-VM-Typ |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Alle Kombinationen für SAP HANA zertifizierter VMs der M-Serie mit unterstützten DT 2.0-VMs (M64-32ms, E32sv3) sind möglich.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Azure-Netzwerke und SAP HANA DT 2.0

Die Installation von DT 2.0 auf einem dedizierten virtuellen Computer erfordert einen Netzwerkdurchsatz von mindestens 10 Gb zwischen der DT-2.0- und der SAP HANA-VM. Aus diesem Grund müssen Sie alle virtuellen Computer im gleichen Azure-VNET platzieren und den beschleunigten Azure-Netzwerkbetrieb aktivieren.

Weitere Informationen zum beschleunigten Azure-Netzwerkbetrieb finden Sie [hier](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

### <a name="vm-storage-for-sap-hana-dt-20"></a>VM-Speicher für SAP HANA DT 2.0

Laut bewährten DT 2.0-Methoden sollte der Datenträger-E/A-Durchsatz pro physischem Kern mindestens 50 MB/s betragen. Die Spezifikation für die beiden für DT 2.0 unterstützten Azure-VM-Typen gibt den maximalen Datenträger-E/A-Durchsatz für den virtuellen Computer an:

- E32sv3: 768 MB/s (nicht zwischengespeichert), also ein Verhältnis von 48 MB/s pro physischem Kern
- M64-32ms: 1.000 MB/s (nicht zwischengespeichert), also ein Verhältnis von 62,5 MB/s pro physischem Kern

Der DT 2.0-VM müssen mehrere Azure-Datenträger angefügt und ein Software-RAID (Striping) muss auf Betriebssystemebene erstellt werden, um den maximalen Datenträgerdurchsatz pro virtuellem Computer zu erzielen. Ein einzelner Azure-Datenträger kann nicht genügend Durchsatz bereitstellen, um die VM-Obergrenze in dieser Hinsicht zu erreichen. Azure Storage Premium ist zur Ausführung von DT 2.0 obligatorisch. 

- Nähere Informationen zu den verfügbaren Azure-Datenträgertypen finden Sie [hier](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage).
- Nähere Informationen zum Erstellen eines Software-RAID über mdadm finden Sie [hier](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
- Nähere Informationen zur LVM-Konfiguration zum Erstellen eines Stripesetvolumes für maximalen Durchsatz finden Sie [hier](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm).

Je nach den Größenanforderungen stehen Ihnen verschiedene Optionen zum Erreichen des maximalen Durchsatzes eines virtuellen Computers zur Verfügung. Hier sind die möglichen Datenvolumedatenträger-Konfigurationen zum Erreichen der Obergrenze des VM-Durchsatzes für jeden DT 2.0-VM-Typ aufgeführt. Die VM E32sv3 sollte als Einstieg für kleinere Workloads angesehen werden. Falls sich herausstellen sollte, dass sie nicht schnell genug ist, muss sie möglicherweise durch die VM M64-32ms ersetzt werden.
Weil die VM M64-32ms über viel Arbeitsspeicher verfügt, erreicht die E/A-Last möglicherweise insbesondere bei leseintensiven Workloads nicht das Limit. Aus diesem Grund können abhängig von der kundenspezifischen Workload weniger Datenträger im Stripeset ausreichend sein. Aber um auf der sicheren Seite zu sein, wurden die folgenden Datenträgerkonfigurationen ausgewählt, um den maximalen Durchsatz zu gewährleisten:


| VM-SKU | Datentr.-Konfig. 1 | Datentr.-Konfig. 2 | Datentr.-Konfig. 3 | Datentr.-Konfig. 4 | Datentr.-Konfig. 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3,5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2,5 TB | 6 x P15 -> 1,5 TB | 


Vor allem bei leseintensiven Workloads könnte die Aktivierung des Azure-Hostcaches mit Schreibschutz – wie für die Datenvolumes von Datenbanksoftware empfohlen – die E/A-Leistung steigern. Für das Transaktionsprotokoll hingegen darf der Azure-Hostdatenträger-Cache nicht aktiviert sein. 

In Bezug auf die Größe des Protokollvolumes werden 15% der Datengröße heuristisch betrachtet als Ausgangspunkt empfohlen. Zum Erstellen des Protokollvolumes sind abhängig von Kosten und Durchsatzanforderungen verschiedene Azure-Datenträgertypen geeignet. Auch für das Protokollvolume wird hoher Durchsatz bevorzugt, und bei M64-32ms sollten Sie unbedingt die Schreibbeschleunigung aktivieren (bei SAP HANA obligatorisch). Dies bewirkt eine optimale Datenträgerschreib-Latenzzeit für das Transaktionsprotokoll (nur für die M-Serie verfügbar). Es sind einige Punkte wie die maximale Anzahl von Datenträgern pro VM-Typ zu berücksichtigen. Details zur Schreibbeschleunigung finden Sie [hier](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


Hier sind einige Beispiele zum Dimensionieren des Protokollvolumes:

| Datenvolumegröße und Datentr.-Typ | Protokollvolume und Datentr.-Typ-Konfig. 1 | Protokollvolume und Datentr.-Typ-Konfig. 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2,5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1,5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Wie für SAP HANA beim horizontalen Hochskalieren muss das Verzeichnis „/hana/shared“ von der SAP HANA-VM und der DT 2.0-VM gemeinsam genutzt werden. Die gleiche Architektur wie für das horizontale Hochskalieren von SAP HANA unter Einsatz dedizierter VMs, die als hochverfügbare NFS-Server fungieren, wird empfohlen. Um ein freigegebenes Sicherungsvolume zu gewährleisten, kann der identische Entwurf verwendet werden. Aber der Kunde entscheidet, ob Hochverfügbarkeit erforderlich ist, oder ob es ausreicht, einfach einen dedizierten virtuellen Computer zu verwenden, dessen Speicherkapazität ausreicht, als Sicherungsserver zu fungieren.



### <a name="links-to-dt-20-documentation"></a>Links zur DT 2.0-Dokumentation 

- [SAP HANA Dynamic Tiering: Installation and Update Guide (Installations- und Aktualisierungshandbuch)](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [Official SAP HANA Dynamic Tiering tutorials and resources (Offizielle Tutorials und Ressourcen)](https://www.sap.com/developer/topics/hana-dynamic-tiering.html)
- [SAP HANA dynamic tiering – delivering on low TCO warm data management, with impressive performance (Management „warmer“ Daten zu niedrigen TCO mit eindrucksvoller Leistung)](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2.0 SPS 02 dynamic tiering enhancements (Verbesserungen)](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Vorgänge für die Bereitstellung von SAP HANA auf virtuellen Azure Computern
In den folgenden Abschnitten werden einige der Vorgänge beschrieben, die auszuführen sind, um SAP HANA-Systeme auf virtuellen Azure-Computern bereitzustellen.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Sicherungs- und Wiederherstellungsvorgänge auf virtuellen Azure-Computern
In den folgenden Dokumenten ist beschrieben, wie Sie Ihre SAP HANA-Bereitstellung sichern und wiederherstellen:

- [Übersicht über SAP HANA-Sicherungen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA-Sicherungen auf Dateiebene](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Benchmark von SAP HANA-Speichermomentaufnahmen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Starten und Neustarten von virtuellen Computern, die SAP HANA enthalten
Ein markantes Merkmal der öffentlichen Azure-Cloud ist, dass Ihnen Gebühren nur für die Datenverarbeitungsminuten berechnet werden. Wenn Sie beispielsweise einen virtuellen Computer herunterfahren, auf dem SAP HANA ausgeführt wird, werden Ihnen nur die Speicherkosten für diese Zeit in Rechnung gestellt. Ein weiteres Merkmal ist verfügbar, wenn Sie in Ihrer Erstbereitstellung statische IP-Adressen für Ihre virtuellen Computer angeben. Wenn Sie einen virtuellen Computer neu starten, auf dem SAP HANA ausgeführt wird, wird der virtuelle Computer mit seinen vorherigen IP-Adressen neu gestartet. 


### <a name="use-saprouter-for-sap-remote-support"></a>Verwenden von SAPRouter für SAP-Remoteunterstützung
Wenn Sie eine Standort-zu-Standort-Verbindung zwischen Ihren lokalen Standorten und Azure haben, und wenn Sie SAP-Komponenten ausführen, wird SAProuter wahrscheinlich bereits ausgeführt. In diesem Fall führen Sie die folgenden Schritte für Remoteunterstützung aus:

- Übernehmen der privaten und der statischen IP-Adresse des virtuellen Computers, auf dem SAP HANA gehostet wird, in die SAProuter-Konfiguration
- Konfigurieren der NSG des Subnetzes, in dem der virtuelle HANA-Computer gehostet wird, für Zulassen von Datenverkehr über den TCP/IP-Port 3299

Wenn Sie eine Verbindung mit Azure über das Internet herstellen und keinen SAP-Router für den virtuellen Computer mit SAP HANA haben, müssen Sie die Komponente installieren. Installieren Sie SAProuter auf einem separaten virtuellen Computer im Verwaltungssubnetz. Die folgende Abbildung zeigt ein allgemeines Schema für die Bereitstellung von SAP HANA ohne eine Standort-zu-Standort-Verbindung und mit SAProuter:

![Allgemeines Bereitstellungsschema für SAP HANA ohne eine Standort-zu-Standort-Verbindung und SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Installieren Sie SAProuter unbedingt auf einem separaten virtuellen Computer und nicht auf Ihrer Jumpbox-VM. Der separate virtuelle Computer muss eine statische IP-Adresse haben. Um die Verbindung Ihres SAProuters mit dem SAProuter herzustellen, der von SAP gehostet wird, wenden Sie sich an SAP, um eine IP-Adresse zu erhalten. (Der SAProuter, der von SAP gehostet wird, ist das Gegenstück zu der SAProuter-Instanz, die Sie auf Ihrem virtuellen Computer installieren.) Verwenden Sie die IP-Adresse von SAP, um Ihre SAProuter-Instanz zu konfigurieren. In den Konfigurationseinstellungen ist der TCP-Port 3299 der einzige erforderliche Port.

Weitere Informationen zum Einrichten und Verwalten von Verbindungen zur Remoteunterstützung über SAProuter finden Sie in der [SAP-Dokumentation](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Hochverfügbarkeit mit SAP HANA auf nativen virtuellen Azure-Computern
Wenn Sie mit SUSE Linux Enterprise Server für SAP-Anwendungen 12 SP1 oder höher arbeiten, können Sie einen Pacemaker-Cluster mit STONITH Geräten einrichten. Sie können die Geräte verwenden, um eine SAP HANA-Konfiguration einzurichten, in der synchrone Replikation mit HANA-Systemreplikation und automatischem Failover verwendet wird. Weitere Informationen über die Einrichtungsvorgehensweise finden Sie unter [Leitfaden zur Hochverfügbarkeit von SAP HANA für virtuelle Azure-Computer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
