---
title: Häufig gestellte Fragen zu Azure Site Recovery | Microsoft Docs
description: Dieser Artikel enthält häufig gestellte Fragen zu Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/08/2018
ms.author: raynew
ms.openlocfilehash: 5d1010a65a112b97124a8d7d46caceb3d61e2cac
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/09/2018
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Häufig gestellte Fragen (FAQ) zu Azure Site Recovery
Dieser Artikel enthält häufig gestellte Fragen zur Azure Site Recovery. Sollten Sie nach der Lektüre dieses Artikels noch Fragen haben, stellen Sie diese bitte im [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## <a name="general"></a>Allgemein
### <a name="what-does-site-recovery-do"></a>Welche Funktion hat Site Recovery?
Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem die Replikation von virtuellen Azure-Computern zwischen Regionen, von lokalen virtuellen Computern und physischen Servern in Azure und von lokalen Computer in ein sekundäres Rechenzentrum orchestriert und automatisiert werden. [detaillierte Kapazitätsplanung](site-recovery-overview.md)

### <a name="what-can-site-recovery-protect"></a>Was kann mit Site Recovery geschützt werden?
* **Virtuelle Azure-Computer:** Mit Site Recovery können alle Workloads repliziert werden, die auf einem unterstützten virtuellen Azure-Computer ausgeführt werden.
* **Virtuelle Hyper-V-Computer**: Mit Site Recovery kann jede Workload, die auf einer Hyper-V-VM ausgeführt wird, geschützt werden.
* **Physische Server**: Mit Site Recovery können physische Server unter Windows oder Linux geschützt werden.
* **Virtuelle VMware-Computer**: Mit Site Recovery kann jede Workload, die auf einer VMware-VM ausgeführt wird, geschützt werden.



### <a name="can-i-replicate-azure-vms"></a>Können virtuelle Azure-Computer repliziert werden?
Ja, Sie können die unterstützten virtuellen Azure-Computer zwischen Azure-Regionen replizieren. [Weitere Informationen](site-recovery-azure-to-azure.md)

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Was benötige ich in Hyper-V, um die Replikation mit Site Recovery zu orchestrieren?
Was Sie für Hyper-V-Hostserver benötigen, richtet sich nach dem Bereitstellungsszenario. Sehen Sie sich die Voraussetzungen für Hyper-V an:

* [Replizieren von Hyper-V-VMs (ohne VMM) in Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replizieren von Hyper-V-VMs (mit VMM) in Azure](site-recovery-vmm-to-azure.md)
* [Replizieren von Hyper-V-VMs in ein sekundäres Rechenzentrum](site-recovery-vmm-to-vmm.md)
* Wenn Sie die Replikation an einem sekundären Rechenzentrum durchführen, helfen Ihnen die Informationen unter [Unterstützte Gastbetriebssysteme für Hyper-V-VMs](https://technet.microsoft.com/library/mt126277.aspx)weiter.
* Wenn Sie in Azure replizieren, unterstützt Site Recovery alle Gastbetriebssysteme, die [von Azure unterstützt werden](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Kann ich virtuelle Computer schützen, wenn Hyper-V auf einem Clientbetriebssystem ausgeführt wird?
Nein. VMs müssen sich auf einem Hyper-V-Hostserver befinden, der auf einem unterstützten Windows-Servercomputer ausgeführt wird. Wenn Sie einen Clientcomputer schützen müssen, können Sie diesen als physischen Computer in [Azure](site-recovery-vmware-to-azure.md) oder in einem [sekundären Rechenzentrum](site-recovery-vmware-to-vmware.md) replizieren.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Welche Workloads können mit Site Recovery geschützt werden?
Mit Site Recovery können die meisten Workloads geschützt werden, die auf einer unterstützten VM oder einem physischen Server ausgeführt werden. Site Recovery bietet Unterstützung für die anwendungsorientierte Replikation, sodass für Apps ein „intelligenter Zustand“ wiederhergestellt werden kann. Eine Integration in Microsoft-Anwendungen wie SharePoint, Exchange, Dynamics, SQL Server und Active Directory ist möglich. Zudem kann Site Recovery eng in die Produkte führender Anbieter, z.B. Oracle, SAP, IBM und Red Hat, eingebunden werden. [Erfahren Sie mehr](site-recovery-workload.md) über den Schutz von Workloads.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Müssen Hyper-V-Hosts sich in VMM-Clouds befinden?
Wenn Sie die Replikation in einem sekundären Rechenzentrum durchführen möchten, müssen Hyper-V-VMs auf Hyper-V-Hostservern in einer VMM-Cloud angeordnet sein. Falls Sie in Azure replizieren möchten, können Sie virtuelle Computer mit oder ohne VMM-Clouds replizieren. [Weitere Informationen](tutorial-hyper-v-to-azure.md) zur Hyper-V-Replikation in Azure.

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Kann ich Site Recovery mit VMM bereitstellen, wenn ich nur über einen VMM-Server verfüge?

Ja. Sie können VMs auf Hyper-V-Servern in der VMM-Cloud in Azure replizieren, oder Sie können zwischen VMM-Clouds auf demselben Server replizieren. Für die Replikation „lokal zu lokal“ empfehlen wir Ihnen, sowohl am primären Standort als auch am sekundären Standort jeweils einen VMM-Server zu verwenden.  

### <a name="what-physical-servers-can-i-protect"></a>Welche physischen Server können geschützt werden?
Sie können physische Server unter Windows und Linux in Azure oder an einem sekundären Standort replizieren. Informationen zu Anforderungen für die [Replikation in Azure](vmware-physical-azure-support-matrix.md#replicated-machines) und die [Replikation an einem sekundären Standort](vmware-physical-secondary-support-matrix.md#replicated-vm-support)


Beachten Sie, dass physische Server als VMs in Azure ausgeführt werden, wenn der lokale Server ausfällt. Das Failback auf einen lokalen physischen Server wird derzeit nicht unterstützt. Für einen als physisch geschützten Computer kann nur ein Failback auf einen virtuellen VMware-Computer ausgeführt werden.

### <a name="what-vmware-vms-can-i-protect"></a>Welche VMware-VMs können geschützt werden?

Zum Schützen von VMware-VMs benötigen Sie einen vSphere-Hypervisor und virtuelle Computer, auf denen VMware-Tools ausgeführt werden. Außerdem wird empfohlen, einen VMware vCenter-Server zum Verwalten der Hypervisoren einzusetzen. Weitere Informationen zu Anforderungen für die [Replikation in Azure](vmware-physical-azure-support-matrix.md#replicated-machines) oder die [Replikation an einem sekundären Standort](vmware-physical-secondary-support-matrix.md#replicated-vm-support)


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Kann ich mit Site Recovery die Notfallwiederherstellung für meine Zweigstellen verwalten?
Ja. Wenn Sie Site Recovery zum Orchestrieren von Replikation und Failover in Zweigstellen verwenden, erhalten Sie eine einheitliche Orchestrierung und Anzeige der Workloads aller Zweigstellen an einer zentralen Stelle. Sie können problemlos über Ihren Hauptsitz für alle Zweigstellen Failover ausführen und die Notfallwiederherstellung verwalten, ohne die Zweigstellen zu besuchen.

## <a name="pricing"></a>Preise
Bei Fragen in Zusammenhang mit Preisen lesen Sie die FAQs unter [Site Recovery – Preise ](https://azure.microsoft.com/en-in/pricing/details/site-recovery/).

## <a name="security"></a>Sicherheit
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Werden Replikationsdaten an den Site Recovery-Dienst gesendet?
Nein. Site Recovery fängt replizierte Daten nicht ab und besitzt keine Informationen dazu, was auf Ihren virtuellen Computern oder physischen Servern ausgeführt wird.
Replikationsdaten werden zwischen lokalen Hyper-V-Hosts, VMware-Hypervisoren oder physischen Servern und Azure-Speicher an Ihrem sekundären Standort ausgetauscht. Site Recovery hat keine Möglichkeit, diese Daten abzufangen. Nur die Metadaten, die zum Orchestrieren von Replikation und Failover erforderlich sind, werden an den Site Recovery-Dienst gesendet.  

Site Recovery ist nach ISO 27001:2013, 27018, HIPAA und DPA zertifiziert und durchläuft gerade die Prüfungen für SOC2 und FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Aus Compliance-Gründen müssen sogar unsere lokalen Metadaten innerhalb derselben geografischen Region verbleiben. Kann Site Recovery uns helfen?
Ja. Durch die Erstellung eines Site Recovery-Tresors in einer Region wird sichergestellt, dass alle Metadaten, die wir zum Ermöglichen und Orchestrieren von Replikation und Failover benötigen, innerhalb der geografischen Grenzen dieser Region bleiben.

### <a name="does-site-recovery-encrypt-replication"></a>Verschlüsselt Site Recovery die Replikation?
Für virtuelle Computer und physische Server wird bei der Replikation zwischen lokalen Standorten die Verschlüsselung während der Übertragung unterstützt. Für virtuelle Computer und physische Server wird bei der Replikation in Azure sowohl die Verschlüsselung während der Übertragung als auch die [Verschlüsselung ruhender Daten (in Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption) unterstützt.

## <a name="replication"></a>Replikation

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Kann ich über ein Standort-zu-Standort-VPN zu Azure replizieren?
Azure Site Recovery repliziert Daten über einen öffentlichen Endpunkt in ein Azure Storage-Konto. Replikation erfolgt nicht über ein Site-to-Site-VPN. Sie können ein Site-to-Site-VPN mit einem virtuellen Azure-Netzwerk erstellen. Die Site Recovery-Replikation wird dadurch nicht beeinträchtigt.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Kann ich virtuelle Computer mithilfe von ExpressRoute zu Azure replizieren?
Ja, ExpressRoute kann zum Replizieren virtueller Computer zu Azure verwendet werden. Azure Site Recovery repliziert Daten über einen öffentlichen Endpunkt in ein Azure Storage-Konto. Wenn Sie ExpressRoute für die Site Recovery-Replikation verwenden möchten, müssen Sie [öffentliches Peering](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) einrichten. Nachdem für die virtuellen Computer ein Failover auf ein virtuelles Azure-Netzwerk ausgeführt wurde, können Sie mithilfe der Einrichtung des [privaten Peering](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) für das virtuelle Azure-Netzwerk darauf zugreifen.

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Gelten Voraussetzungen für die Replikation von virtuellen Computern in Azure?
[Virtuelle VMware-Computer](vmware-physical-azure-support-matrix.md#replicated-machines) und [virtuelle Hyper-V-Computer](hyper-v-azure-support-matrix.md#replicated-vms), die Sie in Azure replizieren möchten, müssen den Azure-Anforderungen entsprechen.

Ihr Azure-Benutzerkonto benötigt bestimmte [Berechtigungen](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) zum Aktivieren der Replikation eines neuen virtuellen Computers in Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Kann ich virtuelle Hyper-V-Computer der 2. Generation in Azure replizieren?
Ja. Während des Failovers konvertiert Site Recovery Computer der 2. Generation in die 1. Generation. Beim Failback werden die Computer wieder in die 2. Generation zurückkonvertiert. [Weitere Informationen](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Wenn ich in Azure repliziere, wie bezahle ich für Azure-VMs?
Während der regulären Replikation werden Daten in den georedundanten Azure-Speicher repliziert. Dabei fallen keinerlei Gebühren für virtuelle Azure-IaaS-Computer an. Dies ist ein großer Vorteil. Bei einem Failover zu Azure erstellt Site Recovery automatisch virtuelle Azure-IaaS-Computer, und Ihnen werden die in Azure genutzten Computeressourcen in Rechnung gestellt.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Kann ich Site Recovery-Szenarien mit einem SDK automatisieren?
Ja. Sie können Site Recovery-Workflows mithilfe der REST-API, PowerShell oder Azure SDK automatisieren. Derzeit unterstützte Szenarien für die Bereitstellung von Site Recovery mit PowerShell:

* [Replizieren von Hyper-V-VMs in VMM-Clouds im Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Replizieren von Hyper-V-VMs ohne VMM in Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Replizieren von VMware in Azure mit PowerShell Resource Manager](vmware-azure-disaster-recovery-powershell.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Wenn ich in Azure repliziere, welche Art von Speicherkonto benötige ich?
Sie benötigen ein LRS- oder GRS-Speicherkonto. Wir empfehlen Ihnen die Verwendung von GRS, damit Resilienz für die Daten besteht, wenn es zu einem regionalen Ausfall kommt oder wenn die primäre Region nicht wiederhergestellt werden kann. Das Konto muss sich in derselben Region wie der Recovery Services-Tresor befinden. Storage Premium wird für die Replikation von virtuellen VMware-Computern, virtuellen Hyper-V-Computern und physischen Servern verwendet, wenn Sie Site Recovery im Azure-Portal bereitstellen.

### <a name="how-often-can-i-replicate-data"></a>Wie oft kann ich Daten replizieren?
* **Hyper-V:** Virtuelle Hyper-V-Computer können alle 30 Sekunden (außer bei Storage Premium), alle 5 Minuten oder alle 15 Minuten repliziert werden. Wenn Sie die SAN-Replikation eingerichtet haben, erfolgt die Replikation synchron.
* **VMware- und physische Server:** Hier ist eine Replikationshäufigkeit irrelevant. Die Replikation ist fortlaufend.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Kann die Replikation von vorhandenen Wiederherstellungsstandorten auf einen weiteren tertiären Standort erweitert werden?
Eine erweiterte oder verkettete Replikation wird nicht unterstützt. Fordern Sie dieses Feature im [Feedbackforum](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Kann ich eine Offlinereplikation durchführen, wenn ich zum ersten Mal in Azure repliziere?
Dies wird nicht unterstützt. Fordern Sie dieses Feature im [Feedbackforum](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Können bestimmte Datenträger von der Replikation ausgeschlossen werden?
Dies wird beim Replizieren von virtuellen VMware-Computern und virtuellen Hyper-V-Computern nach Azure über das Azure-Portal unterstützt.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Können virtuelle Computer mit dynamischen Datenträgern repliziert werden?
Dynamische Datenträger werden unterstützt, wenn virtuelle Hyper-V-Computer repliziert werden. Sie werden auch unterstützt, wenn VMware-VMs und physische Computer in Azure repliziert werden. Der Betriebssystem-Datenträger muss ein Basisdatenträger sein.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>Kann ich einen neuen Computer einer vorhandenen Replikationsgruppe hinzufügen?
Das Hinzufügen von neuen Computern zu vorhandenen Replikationsgruppen wird unterstützt. Wählen Sie hierzu die Replikationsgruppe aus (auf dem Blatt „Replizierte Element“), und klicken Sie mit der rechten Maustaste auf das Kontextmenü für die Replikationsgruppe, und wählen Sie die entsprechende Option.

![Hinzufügen von Computern zur Replikationsgruppe](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Kann ich die Bandbreite drosseln, die für den Hyper-V-Replikationsdatenverkehr zugeordnet ist?
Ja. In den Bereitstellungsartikeln erfahren Sie mehr über die Drosselung der Bandbreite:

* [Kapazitätsplanung für die Replikation von VMware-VMs und physischen Servern](site-recovery-plan-capacity-vmware.md)
* [Kapazitätsplanung für die Replikation von Hyper-V-VMs in Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)

## <a name="failover"></a>Failover
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Wie greife ich nach einem Failover auf Azure auf die virtuellen Azure-Computer zu?
Sie können auf die Azure-VMs über eine sichere Internetverbindung, über eine Site-to-Site-VPN-Verbindung oder über Azure ExpressRoute zugreifen. Sie müssen eine Reihe von Vorbereitungen treffen, um eine Verbindung herzustellen. [Weitere Informationen](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Wie wird bei einem Failover auf Azure in Azure sichergestellt, dass die Daten stabil sind?
Azure ist auf Resilienz ausgelegt. Site Recovery ist bereits für ein Failover in ein sekundäres Azure-Rechenzentrum (gemäß Azure-SLA) konzipiert, falls sich die Notwendigkeit ergibt. Wenn dies der Fall ist, stellen wir sicher, dass Ihre Metadaten und Tresore innerhalb der gleichen geografischen Region bleiben, die Sie für Ihren Tresor ausgewählt haben.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Wenn ich eine Replikation zwischen zwei Rechenzentren ausführe, was geschieht, wenn in meinem primären Rechenzentrum ein unerwarteter Ausfall auftritt?
Sie können ein ungeplantes Failover vom sekundären Standort auslösen. Site Recovery benötigt keine Verbindung mit dem primären Standort, um das Failover auszuführen.

### <a name="is-failover-automatic"></a>Erfolgt ein Failover automatisch?
Ein Failover erfolgt nicht automatisch. Sie initiieren Failover mit einem Mausklick im Portal, oder Sie können die [Site Recovery-PowerShell](/powershell/module/azurerm.siterecovery) verwenden, um ein Failover auslösen. Ein Failback ist eine einfache Aktion im Site Recovery-Portal.

Zum Automatisieren können Sie den lokalen Orchestrator oder Operations Manager verwenden, um einen Fehler bei virtuellen Computern zu erkennen und dann das Failover mithilfe des SDK auszulösen.

* [Weitere Informationen](site-recovery-create-recovery-plans.md) .
* [Weitere Informationen](site-recovery-failover.md) .
* [Weitere Informationen](site-recovery-failback-azure-to-vmware.md) .

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-failover-back-to-a-different-host"></a>Angenommen, mein lokaler Host reagiert nicht mehr oder ist abgestürzt. Kann ein Failover zurück auf einen anderen Host ausgeführt werden?
Ja, Sie können über die Wiederherstellung an einem alternativen Speicherort ein Failback auf einen anderen Host als Azure ausführen. Unter den folgenden Links für virtuelle VMware-Computer und virtuelle Hyper-V-Computer werden die verfügbaren Optionen beschrieben.

* [Für virtuelle VMware-Computer](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Für virtuelle Hyper-V-Computer](hyper-v-azure-failback.md#perform-failback)

## <a name="service-providers"></a>Dienstanbieter
### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Ich bin ein Dienstanbieter. Ist Site Recovery für dedizierte und gemeinsam genutzte Infrastrukturmodelle geeignet?
Ja, Site Recovery unterstützt dedizierte und gemeinsam genutzte Infrastrukturmodelle.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Für einen Dienstanbieter wird die Identität meines Mandanten an den Site Recovery-Dienst weitergegeben?
Nein. Die Mandantenidentität bleibt anonym. Ihre Mandanten benötigen keinen Zugriff auf das Site Recovery-Portal. Nur der Administrator des Dienstanbieters interagiert mit dem Portal.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Werden die Anwendungsdaten der Mandanten jemals an Azure übermittelt?
Bei der Replikation zwischen den Standorten von Dienstanbietern werden die Anwendungsdaten niemals an Azure übermittelt. Die Daten werden während der Übertragung verschlüsselt und direkt zwischen den Standorten des Dienstanbieters repliziert.

Wenn Sie in Azure replizieren, werden Anwendungsdaten an den Azure-Speicher gesendet, jedoch nicht an den Site Recovery-Dienst. Daten werden während der Übertragung verschlüsselt und bleiben in Azure verschlüsselt.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Erhalten meine Mandanten eine Rechnung über Azure-Dienste?
Nein. Azure rechnet direkt mit dem Dienstanbieter ab. Mandantenspezifische Rechnungen müssen von den Dienstanbietern selbst erstellt werden.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Wenn ich in Azure repliziere, müssen wir dann ständig virtuelle Computer in Azure ausführen?
Nein. Die Daten werden in einem Azure-Speicherkonto Ihres Abonnements repliziert. Bei einem Testfailover (Notfallwiederherstellungsübung) oder einem tatsächlichen Failover erstellt Site Recovery automatisch virtuelle Computer in Ihrem Abonnement.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Stellen Sie die Isolierung auf Mandantenebene sicher, wenn ich in Azure repliziere?
Ja.

### <a name="what-platforms-do-you-currently-support"></a>Welche Plattformen werden derzeit unterstützt?
Wir unterstützen Bereitstellungen auf der Grundlage von Azure Pack, Cloud Platform System und System Center (2012 und höher). [Erfahren Sie mehr](https://technet.microsoft.com/library/dn850370.aspx) zur Integration von Azure Pack und Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Werden einzelne Bereitstellungen von Azure Pack und VMM-Servern unterstützt?
Ja, Sie können virtuelle Hyper-V-Computer in Azure oder zwischen Dienstanbieterstandorten replizieren.  Beachten Sie, dass für die Replikation zwischen Dienstanbieterstandorten die Azure-Runbookintegration nicht verfügbar ist.

## <a name="next-steps"></a>Nächste Schritte
* Lesen Sie die [Site Recovery-Übersicht](site-recovery-overview.md)
* Informieren Sie sich [Site Recovery-Architektur](site-recovery-components.md)  
