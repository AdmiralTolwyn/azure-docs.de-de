---
title: Häufig gestellte Fragen zu Azure Backup | Microsoft Docs
description: 'Antworten auf häufig gestellte Fragen zu Azure Backup-Features wie Azure Recovery Services-Tresoren, zu sicherbaren Elementen sowie zu Funktionsweise, Verschlüsselung und Grenzwerten. '
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: Sicherung und Notfallwiederherstellung; Backup-Dienst
ms.assetid: 1011bdd6-7a64-434f-abd7-2783436668d7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/9/2018
ms.author: markgal;arunak;trinadhk;sogup;
ms.openlocfilehash: d16991d15d76caa496b2923c8d0210b6ccb10a9a
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33939197"
---
# <a name="questions-about-the-azure-backup-service"></a>Fragen zum Azure Backup-Dienst
In diesem Artikel werden allgemeine Fragen zu den Azure Backup-Komponenten beantwortet. Einige Antworten enthalten Links zu Artikeln mit umfassenderen Informationen. Fragen zu Azure Backup können Sie durch Klicken auf **Kommentare** (rechts) stellen. Kommentare erscheinen am Ende dieses Artikels. Zum Kommentieren wird ein Livefyre-Konto benötigt. Außerdem können Sie Fragen zum Azure Backup-Dienst im [Diskussionsforum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)stellen.

Verwenden Sie die Links auf der rechten Seite unter **In diesem Artikel**, um sich einen Überblick über die Abschnitte dieses Artikels zu verschaffen.


## <a name="recovery-services-vault"></a>Recovery Services-Tresor

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>Gibt es eine Beschränkung hinsichtlich der Anzahl von Tresoren, die in einem Azure-Abonnement erstellt werden können? <br/>
Ja. Pro Abonnement können für jede unterstützte Region von Azure Backup bis zu 500 Recovery Services-Tresore erstellt werden. Erstellen Sie ein zusätzliches Abonnement, falls Sie zusätzliche Tresore benötigen.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>Gibt es Beschränkungen im Hinblick auf die Anzahl von Servern/Computern, die pro Tresor registriert werden können? <br/>
Sie können bis zu 1000 virtuelle Azure-Computer pro Tresor registrieren. Bei Verwendung von MAB-Agents können Sie bis zu 50 MAB-Agents pro Tresor verwenden. Sie können 50 MAB-Server/DPM-Server bei einem Tresor registrieren.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>Wie kann ich bei der Wiederherstellung von Daten die Daten eines Servers von einem anderen Server isolieren, wenn meine Organisation über einen Tresor verfügt?<br/>
Alle für denselben Tresor registrierten Server können die Daten wiederherstellen, die von anderen Servern *mit derselben Passphrase*gesichert wurden. Wenn Sie Server verwenden, deren Sicherungsdaten Sie von anderen Servern Ihrer Organisation isolieren möchten, sollten Sie für diese Server eine speziell dafür vorgesehene Passphrase nutzen. So können Sie beispielsweise für die Server der Personalabteilung, für die Server der Buchhaltung und für die Speicherserver jeweils eine eigene Verschlüsselungspassphrase verwenden.

### <a name="can-i-migrate-my-backup-data-or-vault-between-subscriptions-br"></a>Kann ich meine Sicherungsdaten oder meinen Sicherungstresor zwischen Abonnements „migrieren“? <br/>
Nein. Der Tresor wird auf einer Abonnementebene erstellt und kann nach seiner Erstellung keinem anderen Abonnement zugewiesen werden.

### <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-still-supported-br"></a>Recovery Services-Tresor basieren auf Resource Manager. Werden Sicherungstresore weiterhin unterstützt? <br/>
Sicherungstresore wurden in Recovery Services-Tresore umgewandelt. Wenn Sie den Sicherungstresor nicht in einen Recovery Services-Tresor umgewandelt haben, wurde dieser Vorgang für Sie übernommen. 

### <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Kann ich einen Backup-Tresor zu einem Recovery Services-Tresor migrieren? <br/>
Alle Sicherungstresore wurden in Recovery Services-Tresore umgewandelt. Wenn Sie den Sicherungstresor nicht in einen Recovery Services-Tresor umgewandelt haben, wurde dieser Vorgang für Sie übernommen.

## <a name="azure-backup-agent"></a>Azure Backup-Agent
Eine ausführliche Liste mit Fragen finden Sie in den [häufig gestellten Fragen zur Sicherung von Dateien/Ordnern mit Azure](backup-azure-file-folder-backup-faq.md).

## <a name="azure-vm-backup"></a>Azure VM Backup
Eine ausführliche Liste mit Fragen finden Sie in den [häufig gestellten Fragen zu Azure VM Backup](backup-azure-vm-backup-faq.md).

## <a name="back-up-vmware-servers"></a>Sichern von VMware-Servern

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Kann ich VMware vCenter-Server in Azure sichern?

Ja. Mit Azure Backup Server können Sie VMware vCenter und ESXi in Azure sichern. Informationen zur unterstützten VMware-Version finden Sie im Artikel [Azure Backup Server-Schutzmatrix](backup-mabs-protection-matrix.md). Eine ausführliche Anleitung finden Sie unter [Sichern eines VMware-Servers mit Azure Backup Server](backup-azure-backup-server-vmware.md).


## <a name="azure-backup-server-and-system-center-data-protection-manager"></a>Azure Backup Server und System Center Data Protection Manager
### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Kann ich Azure Backup Server verwenden, um eine Bare Metal Recovery-Sicherung (BMR) für einen physischen Server zu erstellen? <br/>
Ja.

### <a name="can-i-register-my-dpm-server-to-multiple-vaults-br"></a>Kann ich meinen DPM-Server für mehrere Tresore registrieren? <br/>
Nein. Ein DPM- oder MABS-Server kann nur für einen Tresor registriert werden.

### <a name="which-version-of-system-center-data-protection-manager-is-supported"></a>Welche Version von System Center Data Protection Manager wird unterstützt?

Es wird empfohlen, den [aktuellen](http://aka.ms/azurebackup_agent) Azure Backup-Agent des letzten Updaterollups (UR) für System Center Data Protection Manager (DPM) zu installieren. 
- Das neueste Update für System Center DPM 2012 R2 ist [Updaterollup 14](https://support.microsoft.com/help/4043315/update-rollup-14-for-system-center-2012-r2-data-protection-manager).
- Das neueste Update für System Center DPM 2016 ist [Updaterollup 2](https://support.microsoft.com/en-us/help/3209593).

### <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-protect-on-premises-applicationvm-workloads-to-azure"></a>Ich habe zum Schutz meiner Dateien und Ordner den Azure Backup-Agent installiert. Kann ich System Center DPM installieren, um lokale Anwendungs- bzw. VM-Workloads mit Azure zu schützen?

Ja. Wenn Sie allerdings Azure Backup mit System Center Data Protection Manager (DPM) verwenden möchten, müssen Sie zuerst DPM und anschließend den Azure Backup-Agent installieren. Das Installieren der Azure Backup-Komponenten in dieser Reihenfolge stellt sicher, dass der Azure Backup-Agent mit DPM verwendet werden kann. Das Installieren des Azure Backup-Agents vor der Installation von DPM wird nicht empfohlen bzw. unterstützt.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Kann ich mit DPM Apps in Azure Stack sichern?

Nein. Sie können Azure Stack zwar mit Azure Backup schützen, das Sichern von Apps in Azure Stack wird von Azure Backup aber derzeit nicht unterstützt.

## <a name="how-azure-backup-works"></a>Funktionsweise von Azure Backup
### <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>Werden die übertragenen Sicherungsdaten gelöscht, wenn ich einen Sicherungsauftrag nach dem Starten abbreche? <br/>
Nein. Alle Daten, die vor dem Abbrechen des Sicherungsauftrags in den Tresor übertragen wurden, bleiben im Tresor erhalten. Azure Backup nutzt einen Prüfpunktmechanismus, um den Sicherungsdaten während des Sicherungsvorgangs von Zeit zu Zeit Prüfpunkte hinzuzufügen. Da in den Sicherungsdaten Prüfpunkte vorhanden sind, kann der nächste Sicherungsprozess die Integrität der Dateien überprüfen. Der nächste Sicherungsauftrag erfolgt inkrementell bezogen auf die zuvor gesicherten Daten. Inkrementelle Sicherungen übertragen nur neue oder geänderte Daten, wodurch eine bessere Nutzung der Bandbreite zustande kommt.

Wenn Sie einen Sicherungsauftrag für eine Azure-VM abbrechen, werden die übertragenen Daten ignoriert. Beim nächsten Sicherungsauftrag werden die Daten inkrementell bezogen auf den letzten erfolgreichen Sicherungsauftrag übertragen.

### <a name="are-there-limits-on-when-or-how-many-times-a-backup-job-can-be-scheduledbr"></a>Gelten Grenzwerte in Bezug darauf, wann und wie häufig ein Sicherungsauftrag geplant werden kann?<br/>
Ja. Sie können Sicherungsaufträge unter Windows Server oder auf Windows-Arbeitsstationen bis zu dreimal pro Tag ausführen. Für System Center DPM können Sie Sicherungsaufträge bis zu zweimal pro Tag ausführen. Für IaaS-VMs können Sie einen Sicherungsauftrag einmal pro Tag ausführen. Verwenden Sie die Planungsrichtlinie für Windows Server oder eine Windows-Arbeitsstation, um tägliche oder wöchentliche Zeitpläne anzugeben. Mit System Center DPM können Sie tägliche, wöchentliche, monatliche und jährliche Zeitpläne angeben.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-i-backed-upbr"></a>Warum ist die Datenmenge, die an den Recovery Services-Tresor übertragen wird, kleiner als die von mir gesicherte Datenmenge?<br/>
 Alle Daten, die vom Azure Backup-Agent, von SCDPM oder Azure Backup Server gesichert werden, werden vor der Übertragung komprimiert und verschlüsselt. Nach Anwendung der Komprimierung und Verschlüsselung sind die Daten im Recovery Services-Tresor 30 bis 40 Prozent kleiner.

## <a name="what-can-i-back-up"></a>Für die Sicherung geeignete Daten
### <a name="which-operating-systems-does-azure-backup-support-br"></a>Welche Betriebssysteme werden von Azure Backup unterstützt? <br/>
Azure Backup unterstützt die folgenden Betriebssysteme für die Sicherung von Dateien und Ordnern sowie Workloadanwendungen, die mithilfe von Azure Backup Server und System Center Data Protection Manager (DPM) geschützt werden.

| Betriebssystem | Plattform | SKU |
|:--- | --- |:--- |
| Windows 8 und die neuesten SPs |64-Bit |Enterprise, Pro |
| Windows 7 und die neuesten SPs |64-Bit |Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 und die neuesten SPs |64-Bit |Enterprise, Pro |
| Windows 10 |64-Bit |Enterprise, Pro, Home |
| Windows Server 2016 |64-Bit |Standard, Datacenter, Essentials |
| Windows Server 2012 R2 und die neuesten SPs |64-Bit |Standard, Datacenter, Foundation |
| Windows Server 2012 und die neuesten SPs |64-Bit |Datacenter, Foundation, Standard |
| Windows Storage Server 2016 und die neuesten SPs |64-Bit |Standard, Workgroup | 
| Windows Storage Server 2012 R2 und die neuesten SPs |64-Bit |Standard, Workgroup |
| Windows Storage Server 2012 und neuesten SPs |64-Bit |Standard, Workgroup |
| Windows Server 2012 R2 und die neuesten SPs |64-Bit |Essential |
| Windows Server 2008 R2 SP1 |64-Bit |Standard, Enterprise, Datacenter, Foundation |

**Für Azure-VM-Sicherung:**

* **Linux**: Azure Backup unterstützt eine [Liste von Verteilungen, die von Azure unterstützt werden](../virtual-machines/linux/endorsed-distros.md). Ausnahme: CoreOS Linux.  Andere Bring-Your-Own-Linux-Verteilungen sollten ebenfalls funktionieren, sofern der VM-Agent auf dem virtuellen Computer verfügbar ist und Python unterstützt wird.
* **Windows Server**: Versionen, die älter als Windows Server 2008 R2 sind, werden nicht unterstützt.


### <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up-br"></a>Gibt es eine Größenbeschränkung für die einzelnen Datenquellen, die gesichert werden? <br/>
Azure Backup erzwingt eine maximale Größe für eine Datenquelle, jedoch sind die Grenzwerte für die Quelle sehr hoch. Seit August 2015 beträgt die maximale Größe für eine Datenquelle für die unterstützten Betriebssysteme:

| Nr. | Betriebssystem | Maximale Größe der Datenquelle |
|:---:|:--- |:--- |
| 1 |Windows Server 2012 oder höher |54.400 GB |
| 2 |Windows 8 oder höher |54.400 GB |
| 3 |Windows Server 2008, Windows Server 2008 R2 |1.700 GB |
| 4 |Windows 7 |1.700 GB |

In der folgenden Tabelle wird beschrieben, wie die einzelnen Datenquellengrößen bestimmt werden.

| Datenquelle | Details |
|:---:|:--- |
| Volume |Die Datenmenge, die von einem Volume eines Server- oder Clientcomputers gesichert wird |
| Virtueller Hyper-V-Computer |Summe der Daten aller virtuellen Festplatten des zu sichernden virtuellen Computers |
| Microsoft SQL Server-Datenbank |Größe einer einzelnen zu sichernden SQL-Datenbank |
| Microsoft SharePoint |Summe der Inhalts- und Konfigurationsdatenbanken in einer zu sichernden SharePoint-Farm |
| Microsoft Exchange |Summe aller Exchange-Datenbanken eines zu sichernden Exchange-Servers |
| BMR/Systemstatus |Jede einzelne Kopie der BMR oder des Systemstatus des zu sichernden Computers |

Für Azure-IaaS-VM-Sicherungen kann jede VM bis zu 16 Datenträger haben, und jeder Datenträger kann bis zu 4095 GB groß sein.

### <a name="is-there-a-limit-on-the-amount-of-data-held-in-a-recovery-services-vault"></a>Gibt es einen Grenzwert für die Datenmenge in einem Recovery Services-Tresor?
Es gibt keine Beschränkung für die Menge der Daten, die in einem Recovery Services-Tresor gesichert werden können.

## <a name="retention-policy-and-recovery-points"></a>Aufbewahrungsrichtlinie und Wiederherstellungspunkte
### <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-that-is-on-windows-server-without-dpmbr"></a>Gibt es einen Unterschied zwischen der Aufbewahrungsrichtlinie für DPM und Windows Server bzw. Windows-Client (also unter Windows Server ohne DPM)?<br/>
Nein. Sowohl für DPM als auch für Windows Server bzw. Windows-Clients gelten tägliche, wöchentliche, monatliche und jährliche Aufbewahrungsrichtlinien.

### <a name="can-i-configure-my-retention-policies-selectively--that-is-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>Kann ich meine Aufbewahrungsrichtlinien selektiv konfigurieren – d.h. wöchentlich und täglich, jedoch nicht jährlich und monatlich?<br/>
Ja, durch die Struktur der Azure Backup-Aufbewahrungsrichtlinie können Sie die Aufbewahrungsrichtlinie entsprechend Ihren Anforderungen flexibel definieren.

### <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>Kann ich eine Sicherung um 18:00 Uhr planen und Aufbewahrungsrichtlinien für einen anderen Zeitpunkt festlegen?<br/>
Nein. Aufbewahrungsrichtlinien können nur bei den Sicherungspunkten angewendet werden. In der folgenden Abbildung ist die Aufbewahrungsrichtlinie für Sicherungen angegeben, die um 24:00 und 18:00 Uhr erstellt werden. <br/>

![Planen von Sicherung und Aufbewahrung](./media/backup-azure-backup-faq/Schedule.png)
<br/>

### <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Dauert die Wiederherstellung eines älteren Datenpunkts länger, wenn eine Sicherung lange Zeit aufbewahrt wird? <br/>
Nein. Die Dauer zum Wiederherstellen des ältesten oder neuesten Punkts ist gleich. Jeder Wiederherstellungspunkt verhält sich wie ein vollständiger Punkt.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>Wenn jeder Wiederherstellungspunkt sich wie ein vollständiger Punkt verhält, wie wirkt sich dies auf den gesamten abrechenbaren Sicherungsspeicher aus?<br/>
Bei typischen Produkten für die langfristige Aufbewahrung werden Sicherungsdaten als vollständige Punkte gespeichert. Die vollständigen Punkte sind im Hinblick auf den Speicher *ineffizient*, aber einfacher und schneller wiederherzustellen. Inkrementelle Kopien sind speicher*effizient*. Allerdings müssen Sie eine Datenkette wiederherstellen, was sich auf die Wiederherstellungszeit auswirkt. Die Speicherarchitektur von Azure Backup bietet Ihnen die Vorteile beider Ansätze, indem die Daten optimal zur schnellen Wiederherstellung und zu geringen Speicherkosten gespeichert werden. Mit diesem Ansatz der Datenspeicherung wird sichergestellt, dass die Eingangs- und Ausgangsbandbreite effizient genutzt wird. So werden sowohl der Umfang der Datenspeicherung als auch die benötigte Zeit für die Wiederherstellung der Daten möglichst gering gehalten. Weitere Informationen zur Effizienz inkrementeller Sicherungen finden Sie [hier](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>Gibt es eine Beschränkung für die Anzahl von Wiederherstellungspunkten, die erstellt werden können?<br/>
Pro geschützter Instanz können bis zu 9.999 Wiederherstellungspunkte erstellt werden. Geschützte Instanzen sind Computer, Server (physisch oder virtuell) oder Workloads, die Daten in Azure sichern. Weitere Informationen finden Sie in den Erläuterungen unter [Sicherung und Aufbewahrung](./backup-introduction-to-azure-backup.md#backup-and-retention) sowie unter [Was ist eine geschützte Instanz?](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance).

### <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>Wie viele Wiederherstellungen kann ich für die Daten ausführen, die auf Azure gesichert werden?<br/>
Es gibt keine Beschränkung für die Anzahl der Wiederherstellungen aus Azure Backup.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure-br"></a>Fallen für mich beim Wiederherstellen von Daten für den ausgehenden Datenverkehr von Azure Kosten an? <br/>
Nein. Ihre Wiederherstellungen sind kostenlos, und der ausgehende Datenverkehr wird nicht in Rechnung gestellt.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Was passiert, wenn ich meine Sicherungsrichtlinie ändere?
Wenn eine neue Richtlinie angewendet wird, gelten der Zeitplan und die Aufbewahrungseinstellungen der neuen Richtlinie. Bei einer Ausweitung der Aufbewahrung werden bereits vorhandene Wiederherstellungspunkte markiert, um sie gemäß der neuen Richtlinie aufzubewahren. Bei einer Verkürzung der Aufbewahrung werden sie zum Löschen im Rahmen der nächsten Bereinigung markiert und demgemäß gelöscht.

## <a name="azure-backup-encryption"></a>Azure Backup-Verschlüsselung
### <a name="is-the-data-sent-to-azure-encrypted-br"></a>Werden die Daten verschlüsselt an Azure gesendet? <br/>
Ja. Daten werden auf dem lokalen Server-/Client-/SCDPM-Computer mit AES256 verschlüsselt, und die Daten werden über eine sichere HTTPS-Verbindung gesendet.

### <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>Werden die Sicherungsdaten auf Azure ebenfalls verschlüsselt?<br/>
Ja. Die an Azure gesendeten Daten bleiben verschlüsselt (im Ruhezustand). Die Sicherungsdaten werden zu keinem Zeitpunkt von Microsoft entschlüsselt. Wenn Sie eine Azure-VM sichern, greift Azure Backup auf die Verschlüsselung des virtuellen Computers zurück. Wenn Ihr virtueller Computer z.B. mithilfe von Azure Disk Encryption oder einer anderen Verschlüsselungstechnologie verschlüsselt ist, verwendet Azure Backup diese Verschlüsselung, um Ihre Daten zu sichern.

### <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>Wie lang muss der Verschlüsselungsschlüssel zur Verschlüsselung von Sicherungsdaten mindestens sein? <br/>
Bei Verwendung des Azure Backup-Agents muss der Verschlüsselungsschlüssel mindestens 16 Zeichen lang sein. Bei virtuellen Azure-Computern gibt es keine Längenbegrenzung für Schlüssel, die von Azure Key Vault verwendet werden. 

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>Was geschieht, wenn ich den Schlüssel verlege? Kann ich die Daten wiederherstellen, oder kann Microsoft die Daten wiederherstellen? <br/>
Der Schlüssel zum Verschlüsseln der Sicherungsdaten ist nur am Standort des Kunden vorhanden. Microsoft behält keine Kopie in Azure und hat keinen Zugriff auf den Schlüssel. Wenn der Kunde den Schlüssel verlegt, kann Microsoft die gesicherten Daten nicht wiederherstellen.
