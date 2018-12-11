---
title: Informationen zu Azure Migrate | Microsoft-Dokumentation
description: Enthält eine Übersicht über den Azure Migrate-Dienst.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/28/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 98ff54bcfe67d79d8c15da666aad0bebfe48f6e0
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839733"
---
# <a name="about-azure-migrate"></a>Informationen zu Azure Migrate

Mit dem Azure Migrate-Dienst werden lokale Workloads für die Migration zu Azure bewertet. Der Dienst bewertet die Migrationseignung lokaler Computer, führt die leistungsbasierte Größenauslegung durch und stellt Kostenschätzungen für die Ausführung Ihrer lokalen Computer in Azure bereit. Wenn Sie die Nutzung von Lift & Shift-Migrationen erwägen oder sich in einer frühen Bewertungsphase der Migration befinden, ist dieser Dienst gut für Sie geeignet. Nach der Bewertung können Sie Dienste wie [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) und [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) nutzen, um die Computer zu Azure zu migrieren.

## <a name="why-use-azure-migrate"></a>Gründe für die Verwendung von Azure Migrate

Azure Migrate bietet Folgendes:

- **Bewerten der Bereitschaft für Azure**: Bewerten Sie, ob Ihre lokalen Computer für die Ausführung in Azure geeignet sind.
- **Größenempfehlungen**: Nutzen Sie Größenempfehlungen für virtuelle Azure-Computer, die auf dem Leistungsverlauf lokaler virtueller Computer basieren.
- **Voraussichtliche monatliche Kosten**: Erhalten Sie Kostenschätzungen zur Ausführung lokaler Computer in Azure.  
- **Migration mit hoher Zuverlässigkeit**: Visualisieren Sie Abhängigkeiten von lokalen Computern, um Gruppen von Computern zu erstellen, die Sie gemeinsam bewerten und migrieren möchten.

## <a name="current-limitations"></a>Aktuelle Einschränkungen

- Es können nur lokale virtuelle VMware-Computer (Virtual Machines, VMs) für die Migration zu virtuellen Azure-Computern bewertet werden. Die virtuellen VMware-Computer müssen mit vCenter Server (Version 5.5, 6.0 oder 6.5) verwaltet werden.
- Wenn Sie virtuelle Hyper-Computer und physische Server bewerten möchten, verwenden Sie den [Azure Site Recovery-Bereitstellungsplaner](https://aka.ms/asr-dp-hyperv-doc) für Hyper-V und unsere [Partnertools](https://azure.microsoft.com/migration/partners/) für physische Computer.
- Sie können in einer einzigen Ermittlung bis zu 1.500 und in einem einzelnen Projekt bis zu 1.500 virtuelle Computer ermitteln. Außerdem können Sie in einem einzelnen Assessment bis zu 1.500 virtuelle Computer bewerten.
- Wenn Sie eine Ermittlung für eine größere Umgebung durchführen möchten, können Sie die Ermittlung aufteilen und mehrere Projekte erstellen. [Weitere Informationen](how-to-scale-assessment.md). Azure Migrate unterstützt bis zu 20 Projekte pro Abonnement.
- Azure Migrate unterstützt nur verwaltete Datenträger für die Migrationsbewertung.
-  Sie können ein Azure Migrate-Projekt nur in den USA erstellen. Es ist aber möglich, dass Sie eine Migration zu einem beliebigen Azure-Zielstandort planen.
    - Nur die Metadaten, die in der lokalen Umgebung erkannt werden, werden in der Region des Migrationsprojekts gespeichert.
    - Metadaten werden in einer Region der ausgewählten Geografie gespeichert: „USA, Westen-Mitte“ bzw. „USA, Osten“.
    - Wenn Sie die Visualisierung von Abhängigkeiten durch Erstellung eines neuen Log Analytics-Arbeitsbereichs verwenden, wird der Arbeitsbereich in derselben Region wie das Projekt erstellt.


## <a name="what-do-i-need-to-pay-for"></a>Für was muss ich zahlen?

Weitere Informationen zu den Preisen von Azure Migrate finden Sie [hier](https://azure.microsoft.com/pricing/details/azure-migrate/).


## <a name="whats-in-an-assessment"></a>Was umfasst eine Bewertung?

Bewertungseinstellungen können je nach Bedarf angepasst werden. Die Bewertungseigenschaften werden in der folgenden Tabelle zusammengefasst:

**Eigenschaft** | **Details**
--- | ---
**Zielstandort** | Der Azure-Standort, zu dem die Migration durchgeführt werden soll.<br/><br/>Azure Migrate unterstützt derzeit 33 Regionen als Standort des Migrationsziels. [Hier](https://azure.microsoft.com/global-infrastructure/services/) sind die Regionen aufgeführt. Die Zielregion ist standardmäßig auf „USA, Westen 2“ festgelegt.
**Speichertyp** | Der Typ der verwalteten Datenträger, den Sie für alle virtuellen Computer zuordnen möchten, die Teil der Bewertung sind. Lautet das Größenkriterium *Größenanpassung vom Typ „Wie lokal“*, können Sie als Zieldatenträger Premium-Datenträger (Standardeinstellung), Standard-SSD-Datenträger oder Standard-HDD-Datenträger angeben. Bei *leistungsbasierter Größenanpassung* sowie den oben genannten Optionen können Sie „Automatisch“ auswählen. Dadurch wird sichergestellt, dass für die Größenempfehlung automatisch die Leistungsdaten der virtuellen Computer herangezogen werden. Wenn Sie beispielsweise eine [SLA von 99,9% für Einzelinstanz-VMs](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) erzielen möchten, können Sie den Speichertyp als „Managed Disks Premium“ angeben. So wird sichergestellt, dass alle Datenträger der Bewertung als Datenträger vom Typ „Managed Disks Premium“ empfohlen werden. Hinweis: Azure Migrate unterstützt nur verwaltete Datenträger für die Migrationsbewertung.
**Reservierte Instanzen** |  Mit dieser Eigenschaft wird angegeben, ob Sie [reservierte Instanzen](https://azure.microsoft.com/pricing/reserved-vm-instances/) in Azure besitzen. Dies wird dann von Azure Migrate bei der Kostenschätzung entsprechend berücksichtigt.
**Größenkriterium** | Sie können die Größenanpassung auf der Grundlage des **Leistungsverlaufs** der lokalen virtuellen Computer vornehmen (Standardeinstellung) oder die Größe ohne Berücksichtigung des Leistungsverlauf **an die lokale Umgebung** anpassen.
**Leistungsverlauf** | Standardmäßig bewertet Azure Migrate die Leistung lokaler Computer anhand des Leistungsverlaufs des letzten Tags mit einem 95-prozentigen Perzentilwert.
**Komfortfaktor** | Bei Azure Migrate wird während der Bewertung ein Puffer (Komfortfaktor) berücksichtigt. Dieser Puffer wird zusätzlich zu den Daten zur Computernutzung für VMs (CPU, Arbeitsspeicher, Datenträger und Netzwerk) angewendet. Beim Komfortfaktor geht es um Bereiche wie saisonale Nutzung, kurzer Leistungsverlauf und voraussichtliche zukünftige Zunahme der Nutzung.<br/><br/> Beispiel: Für einen virtuellen Computer mit zehn Kernen und einer Auslastung von 20 Prozent ergibt sich normalerweise ein virtueller Computer mit zwei Kernen. Bei einem Komfortfaktor von 2.0x ist das Ergebnis dagegen eine VM mit vier Kernen. Die Standardeinstellung für den Komfortfaktor lautet 1.3x.
**VM-Serie** | Die für die Größenschätzung verwendete VM-Serie. Wenn Sie beispielsweise eine Produktionsumgebung haben, die Sie nicht zu virtuellen Computern der A-Serie in Azure migrieren möchten, können Sie die A-Serie aus der Liste oder Serie ausschließen. Die Größenanpassung erfolgt nur basierend auf der ausgewählten Serie.   
**Währung** | Rechnungswährung. Der Standardwert ist US-Dollar.
**Rabatt (%)** | Alle abonnementspezifischen Rabatte, die Sie zusätzlich zum Azure-Angebot erhalten. Die Standardeinstellung ist 0 %.
**VM-Betriebszeit** | Wenn Ihre virtuellen Computer nicht rund um die Uhr in Azure aktiv sind, können Sie den Zeitraum angeben (Anzahl der Tage pro Monat und Anzahl der Stunden pro Tag), für den sie voraussichtlich ausgeführt werden, um eine entsprechende Kostenschätzung zu erhalten. Der Standardwert sind 31 Tage pro Monat und 24 Stunden pro Tag.
**Azure-Angebot** | Das [Azure-Angebot](https://azure.microsoft.com/support/legal/offer-details/), bei dem Sie registriert sind. Dies wird dann von Azure Migrate bei der Kostenschätzung entsprechend berücksichtigt.
**Azure-Hybridvorteil** | Mit dieser Eigenschaft wird angegeben, ob Sie über Software Assurance verfügen und Anspruch auf den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-use-benefit/) mit Preisnachlässen haben.

## <a name="how-does-azure-migrate-work"></a>Wie funktioniert Azure Migrate?

1.  Sie erstellen ein Azure Migrate-Projekt.
2.  Für Azure Migrate wird eine lokale VM genutzt, die als „Collectorappliance“ bezeichnet wird, um Informationen zu Ihren lokalen Computern zu ermitteln. Für die Erstellung der Appliance laden Sie die Setupdatei im OVA-Format (Open Virtualization Appliance) herunter und importieren sie als virtuellen Computer auf Ihrem lokalen vCenter Server-Computer.
3. Sie stellen über vCenter Server eine Verbindung mit dem virtuellen Computer her und geben während der Verbindungsherstellung ein neues Kennwort ein.
4. Sie führen den Collector auf dem virtuellen Computer aus, um die Ermittlung zu initiieren.
5. Der Collector erfasst VM-Metadaten mit den VMware PowerCLI-Cmdlets. Die Ermittlung erfolgt ohne Agent, und auf VMware-Hosts oder VMs wird nichts installiert. Die gesammelten Metadaten enthalten VM-Informationen (Kerne, Arbeitsspeicher, Datenträger, Datenträgergrößen und Netzwerkadapter). Außerdem werden Leistungsdaten für VMs gesammelt, z.B. CPU- und Arbeitsspeicherauslastung, Datenträger-IOPS, Datenträgerdurchsatz (MBit/s) und Netzwerkausgabe (MBit/s).
5.  Die Metadaten werden per Pushvorgang in das Azure Migrate-Projekt übertragen. Sie können sie im Azure-Portal anzeigen.
6.  Zur Durchführung der Bewertung fassen Sie die ermittelten virtuellen Computer in Gruppen zusammen. Beispielsweise können Sie virtuelle Computer gruppieren, auf denen die gleiche Anwendung ausgeführt wird. Für eine präzisere Gruppierung können Sie mithilfe der Abhängigkeitsvisualisierung Abhängigkeiten eines bestimmten Computers anzeigen oder aber Abhängigkeiten für alle Computer in einer Gruppe anzeigen und die Gruppe weiter eingrenzen.
7.  Nachdem eine Gruppe definiert wurde, erstellen Sie eine Bewertung für sie.
8.  Nach Abschluss der Bewertung können Sie das Ergebnis im Portal anzeigen oder im Excel-Format herunterladen.

  ![Azure Migrate-Architektur](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>Wie lauten die Portanforderungen?

In der Tabelle sind die Ports zusammengefasst, die für die Azure Migrate-Kommunikation erforderlich sind.

Komponente | Kommuniziert mit |  Details
--- | --- |---
Collector  | Azure Migrate-Dienst | Der Collector stellt über den SSL-Port 443 eine Verbindung mit dem Dienst her.
Collector | vCenter Server | Standardmäßig stellt der Collector eine Verbindung mit vCenter Server über Port 443 her. Wenn der Server an einem anderen Port lauscht, konfigurieren Sie ihn auf dem virtuellen Collectorcomputer als ausgehenden Port.
Lokale VM | Log Analytics-Arbeitsbereich | [TCP 443] | [Microsoft Monitoring Agent (MMA)](../log-analytics/log-analytics-windows-agent.md) nutzt TCP-Port 443, um eine Verbindung mit Log Analytics herzustellen. Sie benötigen diesen Port nur, wenn Sie die Visualisierung von Abhängigkeiten verwenden, für die der MMA-Agent erforderlich ist.


## <a name="what-happens-after-assessment"></a>Was passiert nach der Bewertung?

Nachdem Sie lokale Computer bewertet haben, können Sie einige Tools nutzen, um die Migration durchzuführen:

- **Azure Site Recovery:** Sie können Azure Site Recovery für die Migration zu Azure verwenden. Dazu [bereiten Sie die erforderlichen Azure-Komponenten](../site-recovery/tutorial-prepare-azure.md) vor, einschließlich eines Speicherkontos und eines virtuellen Netzwerks. Lokal [bereiten Sie Ihre VMware-Umgebung vor](../site-recovery/vmware-azure-tutorial-prepare-on-premises.md). Wenn alles vorbereitet ist, richten Sie die Replikation in Azure ein und aktivieren sie. Anschließend migrieren Sie die virtuellen Computer. [Weitere Informationen](../site-recovery/vmware-azure-tutorial.md).
- **Azure Database Migration:** Wenn Ihre lokalen Computer eine Datenbank wie etwa SQL Server, MySQL oder Oracle ausführen, können Sie den [Azure Database Migration Service](../dms/dms-overview.md) für die Migration zu Azure verwenden.


## <a name="next-steps"></a>Nächste Schritte

- [Arbeiten Sie das Tutorial durch](tutorial-assessment-vmware.md), um eine Bewertung für einen lokalen virtuellen VMware-Computer zu erstellen.
- Sehen Sie sich die [häufig gestellten Fragen](resources-faq.md) zu Azure Migrate an.
