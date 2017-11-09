---
title: Informationen zu Azure Site Recovery | Microsoft Docs
description: "Enthält eine Übersicht über den Azure Site Recovery-Dienst und eine Zusammenfassung der Bereitstellungsszenarien."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b4b39cd23557093edaec97f7ef7a3e354f1ecd03
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2017
---
# <a name="about-site-recovery"></a>Informationen zu Azure Site Recovery

Willkommen beim Azure Site Recovery-Dienst! Dieser Artikel enthält eine kurze Übersicht über den Dienst.

## <a name="business-continuity-and-disaster-recovery-bcdr-with-azure-recovery-services"></a>Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR) mit Azure Recovery Services

Als Organisation müssen Sie ermitteln, wie Sie Ihre Daten schützen und die Ausführung von Apps/Workloads sicherstellen können, wenn es zu geplanten und ungeplanten Ausfällen kommt.

Azure Recovery Services unterstützen Sie bei Ihrer BCDR-Strategie:

- **Site Recovery-Dienst**: Site Recovery sorgt für Geschäftskontinuität, indem Ihre Apps auf VMs weiter ausgeführt werden und physische Server verfügbar bleiben, wenn ein Standort ausfällt. Site Recovery repliziert Workloads, die auf VMs und physischen Servern ausgeführt werden, damit sie an einem sekundären Ort verfügbar bleiben, wenn die Verfügbarkeit des primären Standorts nicht mehr gegeben ist. Workloads werden am primären Standort wiederhergestellt, wenn er wieder betriebsbereit ist.
- **Backup-Dienst**: Zusätzlich sorgt der [Azure Backup](https://docs.microsoft.com/azure/backup/)-Dienst dafür, dass Ihre Daten sicher und wiederherstellbar bleiben, indem sie in Azure gesichert werden.

Site Recovery kann die Replikation für folgende Bereiche verwalten:

- Replikation von virtuellen Azure-Computern zwischen Azure-Regionen
- Replikation von lokalen virtuellen Computern und physischen Servern in Azure oder an einem sekundären Standort


## <a name="what-does-site-recovery-provide"></a>Was bietet Site Recovery?

**Feature** | **Details**
--- | ---
**Bereitstellen einer einfachen BCDR-Lösung** | Mit Site Recovery können Sie Replikation, Failover und Failback von einem zentralen Ort im Azure-Portal aus einrichten und verwalten.
**Replikation von Azure-VMs** | Sie können Ihre BCDR-Strategie so einrichten, dass Azure-VMs zwischen Azure-Regionen repliziert werden.
**Replizieren von lokalen VMs an einem anderen Ort** | Sie können lokale VMs und physische Server in Azure oder an einem sekundären lokalen Ort replizieren. Mit der Replikation in Azure entfallen die Kosten und die Komplexität, die mit der Verwaltung eines sekundären Datencenters verbunden sind.
**Replizieren beliebiger Workloads** | Sie können beliebige Workloads replizieren, die auf unterstützten Azure-VMs, lokalen Hyper-V-VMs, VMware-VMs und physischen Windows/Linux-Servern ausgeführt werden.
**Sicherstellen der Robustheit und des Schutzes von Daten** | Site Recovery orchestriert die Replikation, ohne dass dies Auswirkungen auf die Anwendungsdaten hat. Da die replizierten Daten in Azure Storage gespeichert werden, profitieren Sie von der entsprechenden Resilienz. Bei einem Failover werden virtuelle Azure-Computer auf der Grundlage der replizierten Daten erstellt.
**Erfüllen von RTOs und RPOs** | Halten Sie die Grenzwerte Ihrer Organisation für Recovery Time Objectives (RTO) und Recovery Point Objectives (RPO) ein. Site Recovery ermöglicht eine fortlaufende Replikation für virtuelle Azure-Computer und virtuelle VMware-Computer sowie eine Replikationsfrequenz von nur 30 Sekunden für Hyper-V. Sie können Recovery Time Objectives (RTOs) weiter reduzieren, indem Sie eine Einbindung in die [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)-Integration durchführen.
**Beibehalten der Konsistenz für Apps bei einem Failover** | Sie können Wiederherstellungspunkte mit anwendungskonsistenten Momentaufnahmen konfigurieren. Mit anwendungskonsistenten Momentaufnahmen werden Datenträgerdaten, alle Daten im Arbeitsspeicher und alle aktiven Transaktionen erfasst.
**Testen ohne Unterbrechungen** | Sie können auf einfache Weise Testfailover ausführen, um Übungen zur Notfallwiederherstellung zu unterstützen, ohne dass sich dies auf die laufende Replikation auswirkt.
**Ausführen von flexiblen Failovern** | Sie können geplante Failover ohne Datenverlust für erwartete Ausfälle oder ungeplante Failover mit minimalem Datenverlust (je nach Replikationsfrequenz) für unerwartete Notfälle ausführen. Wenn der primäre Standort wieder verfügbar ist, kann problemlos ein Failback ausgeführt werden.
**Erstellen von Wiederherstellungsplänen** | Sie können Failover und Wiederherstellungen von Anwendungen mit mehreren Ebenen auf mehreren VMs mit Wiederherstellungsplänen anpassen und sequenzieren. Sie gruppieren Computer innerhalb von Plänen und fügen Skripts und manuelle Aktionen hinzu. Wiederherstellungspläne können in Azure Automation-Runbooks integriert werden.
**Integrieren in vorhandene BCDR-Technologien** | Site Recovery ist in andere BCDR-Technologien integriert. Beispielsweise können Sie Site Recovery verwenden, um das SQL Server-Back-End von geschäftlichen Workloads zu schützen, einschließlich der nativen Unterstützung für SQL Server Always On zum Verwalten des Failovers von Verfügbarkeitsgruppen.
**Integrieren in die Automation-Bibliothek** | Eine umfassende Azure Automation-Bibliothek bietet produktionsbereite, anwendungsspezifische Skripts, die heruntergeladen und in Site Recovery integriert werden können.
**Verwalten von Netzwerkeinstellungen** | Site Recovery ist in Azure integriert, um eine einfache Anwendungsnetzwerkverwaltung zu ermöglichen, z.B. das Reservieren von IP-Adressen, Konfigurieren von Lastenausgleichsmodulen und Integrieren von Azure Traffic Manager zur Durchführung von effizienten Netzwerkwechseln.


## <a name="what-can-i-replicate"></a>Was kann ich replizieren?

**Unterstützt** | **Details**
--- | ---
**Was kann ich replizieren?** | Virtuelle Azure-Computer zwischen Azure-Regionen<br/><br/>  Lokale VMware-VMs, Hyper-V-VMs, physische Server (Windows und Linux) in Azure<br/><br/> Lokale VMware VMs, Hyper-V-VMs, physische Server in Virtual Machine Manager (VMM)
**Welche Regionen werden für Site Recovery unterstützt?** | [Unterstützte Regionen](https://azure.microsoft.com/regions/services/) |
**Welche Betriebssysteme benötigen replizierte Computer?** | [Azure-VM-Anforderungen](site-recovery-support-matrix-azure-to-azure.md#support-for-replicated-machine-os-versions)</br></br>[VMware-VM-Anforderungen](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> Für virtuelle Hyper-V-Computer wird jedes [Gastbetriebssystem](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) unterstützt, das auch von Azure und Hyper-V unterstützt wird.<br/><br/> [Anforderungen physischer Server](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**Welche VMware-Server/-Hosts benötige ich?** | VMware-VMs können sich auf [unterstützten vSphere-Hosts/vCenter-Servern](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers) befinden.
**Welche Workloads kann ich replizieren?** | Sie können jede Workload replizieren, die auf einem unterstützten Replikationscomputer ausgeführt wird. Darüber hinaus hat das Site Recovery-Team App-spezifische Tests für eine [Reihe von Apps](site-recovery-workload.md#workload-summary) durchgeführt.



## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich ausführlicher über [Workloadunterstützung](site-recovery-workload.md).
* [Erste Schritte](azure-to-azure-quickstart.md) mit der Replikation virtueller Azure-Computer zwischen Regionen 
