---
title: Häufig gestellte Fragen zur Azure-VM-Sicherung
description: Hier finden Sie Antworten auf häufig gestellte Fragen zur Funktionsweise von Azure VM Backup, zu Einschränkungen sowie zu den Folgen von Richtlinienänderungen.
services: backup
author: trinadhk
manager: shreeshd
keywords: Azure VM Backup, Azure VM Restore, Sicherungsrichtlinie
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: trinadhk
ms.openlocfilehash: 58b0622da2ef617e652c8bb9dacbf7daa2d79966
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42144905"
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Fragen zum Azure VM Backup-Dienst
Dieser Artikel enthält Antworten auf häufig gestellte Fragen, damit Sie sich schnell mit den Komponenten von Azure VM Backup vertraut machen können. Einige Antworten enthalten Links zu Artikeln mit umfassenderen Informationen. Außerdem können Sie Fragen zum Azure Backup-Dienst im [Diskussionsforum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)stellen.

## <a name="configure-backup"></a>Konfigurieren der Sicherung
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Unterstützen Recovery Services-Tresore klassische virtuelle Computer oder Resource Manager-basierte virtuelle Computer? <br/>
Recovery Services-Tresore unterstützen beide Modelle.  Sie können einen klassischen virtuellen Computer oder einen virtuellen Resource Manager-Computer in einem Recovery Services-Tresor sichern.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup"></a>Welche Konfigurationen werden von der Azure-VM-Sicherung nicht unterstützt?
Informationen zu unterstützten Betriebssystemen finden Sie [hier](backup-azure-arm-vms-prepare.md#supported-operating-systems-for-backup). Informationen zu Einschränkungen beim Sichern eines virtuellen Computers finden Sie [hier](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm).

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>Warum wird mein virtueller Computer im Sicherungskonfigurations-Assistenten nicht angezeigt?
Im Sicherungskonfigurations-Assistenten werden nur virtuelle Computer aufgeführt, die folgende Voraussetzungen erfüllen:
  * Sie sind noch nicht geschützt: Navigieren Sie zum Ermitteln des Sicherungsstatus eines virtuellen Computers zum VM-Blatt, und überprüfen Sie über das Einstellungsmenü des Blatts den Sicherungsstatus. Weitere Informationen zum Überprüfen des Sicherungsstatus eines virtuellen Computers finden Sie [hier](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-operations-menu).
  * Er gehört zur gleichen Region wie der virtuelle Computer.

## <a name="backup"></a>Backup
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>Wird bei bedarfsbasierten Sicherungsaufträgen der gleiche Aufbewahrungszeitplan verwendet wie bei geplanten Sicherungen?
Nein. Bei einem bedarfsbasierten Sicherungsauftrag muss die gewünschte Aufbewahrungsdauer angegeben werden. Bei Aufträgen, die über das Portal ausgelöst werden, beträgt die Aufbewahrungsdauer standardmäßig 30 Tage. 

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Ich habe kürzlich für einige virtuelle Computer Azure Disk Encryption aktiviert. Funktionieren meine Sicherungen auch weiterhin?
Sie müssen dem Azure Backup-Dienst Zugriff auf Key Vault gewähren. Die erforderlichen Berechtigungen können Sie über PowerShell erteilen. Führen Sie dazu die Schritte des Abschnitts *Aktivieren der Sicherung* in der [PowerShell-Dokumentation](backup-azure-vms-automation.md) aus.

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>Ich habe Datenträger eines virtuellen Computers zu verwalteten Datenträgern gemacht. Funktionieren meine Sicherungen auch weiterhin?
Ja. Sicherungen funktionieren reibungslos, und die Sicherung muss nicht neu konfiguriert werden. 

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Mein virtueller Computer ist heruntergefahren. Soll eine bedarfsgesteuerte oder geplante Sicherung durchgeführt werden?
Ja. Selbst wenn ein Computer heruntergefahren ist, funktionieren Sicherungen, und der Wiederherstellungspunkt ist als absturzkonsistent gekennzeichnet. Weitere Informationen finden Sie in [diesem Artikel](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines) im Abschnitt zu Datenkonsistenz.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Kann ich einen aktuellen Sicherungsauftrag abbrechen?
Ja. Sie können einen Sicherungsauftrag in der Phase „Momentaufnahme wird erstellt...“ abbrechen. **Ein Auftrag kann nicht abgebrochen werden, wenn eine Datenübertragung von der Momentaufnahme stattfindet**. 

### <a name="i-enabled-resource-group-lock-on-my-backed-up-managed-disk-vms-will-my-backups-continue-to-work"></a>Ich habe für meine gesicherten, verwalteten Datenträger-VMs die Ressourcengruppensperre aktiviert. Funktionieren meine Sicherungen auch weiterhin?
Wenn der Benutzer die Ressourcengruppe sperrt, kann der Backup-Dienst die älteren Wiederherstellungspunkte nicht löschen. Aus diesem Grund fangen neue Backups an fehlzuschlagen, da ein Grenzwert von maximal 18 Wiederherstellungspunkten gilt, der vom Back-End vorgegeben wird. Wenn Ihre Sicherungen nach der Ressourcengruppensperre mit einem internen Fehler fehlschlagen, befolgen Sie diese [Schritte zum Entfernen der Wiederherstellungspunktsammlung](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock).

### <a name="does-backup-policy-take-daylight-saving-timedst-into-account"></a>Berücksichtigt die Sicherungsrichtlinie die Sommerzeit (Daylight Saving Time, DST)?
Nein. Denken Sie daran, dass Datum und Uhrzeit auf dem lokalen Computer in Ihrer lokalen Uhrzeit und unter Berücksichtigung der Sommerzeit angezeigt werden. Daher kann die für geplante Sicherungen konfigurierte Zeit aufgrund der DST von Ihrer lokalen Uhrzeit abweichen.

## <a name="restore"></a>Restore
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>Wann sollte ich Datenträger wiederherstellen und wann den gesamten virtuellen Computer?
Bei der vollständigen Wiederherstellung eines virtuellen Azure-Computers handelt es sich gewissermaßen um eine Schnellerstellungsoption. Die VM-Wiederherstellungsoption ändert die Namen von Datenträgern, von Containern, die von diesen Datenträgern verwendet werden, öffentlichen IP-Adressen und Namen von Netzwerkschnittstellen. Die Änderung ist erforderlich, um die Eindeutigkeit von Ressourcen beizubehalten, die während der Erstellung eines virtuellen Computers erstellt werden. Sie fügt jedoch nicht den virtuellen Computer der Verfügbarkeitsgruppe hinzu. 

Die Datenträgerwiederherstellung sollte in folgenden Fällen verwendet werden:
  * Zum Anpassen des virtuellen Computers, der auf der Grundlage einer Zeitpunktkonfiguration erstellt wird (beispielsweise, um die Größe zu ändern)
  * Zum Hinzufügen von Konfigurationen, die zum Zeitpunkt der Sicherung nicht vorhanden waren 
  * Zum Steuern der Namenskonvention für erstellte Ressourcen
  * Zum Hinzufügen des virtuellen Computers zu einer Verfügbarkeitsgruppe
  * Für eine beliebige andere Konfiguration, die nur über PowerShell/eine deklarative Vorlagendefinition erreicht werden kann
  
### <a name="can-i-use-backups-of-unmanaged-disk-vm-to-restore-after-i-upgrade-my-disks-to-managed-disks"></a>Kann ich Sicherungen einer VM eines nicht verwalteten Datenträgers zur Wiederherstellung verwenden, nachdem ich meine Datenträger zu verwalteten Datenträgern upgegradet habe?
Ja, Sie können Sicherungen verwenden, die vor der Migration von nicht verwalteten zu verwalteten Datenträgern erstellt wurden. Ein Auftrag zum Wiederherstellen einer VM erstellt standardmäßig eine VM mit nicht verwalteten Datenträgern. Sie können die Funktion „Datenträger wiederherstellen“ verwenden, um Datenträger wiederherzustellen. Diese können Sie verwenden, um eine VM auf verwalteten Datenträgern zu erstellen. 

### <a name="what-is-the-procedure-to-restore-a-vm-to-a-restore-point-taken-before-the-conversion-from-unmanaged-to-managed-disks-was-done-for-a-vm"></a>Wie wird eine VM auf einem Wiederherstellungspunkt wiederhergestellt, bevor die Konvertierung von nicht verwalteten zu verwalteten Datenträgern für eine VM durchgeführt wurde?
In diesem Szenario erstellt standardmäßig ein Auftrag zum Wiederherstellen einer VM eine VM mit nicht verwalteten Datenträgern. So erstellen Sie einen virtuellen Computer mit verwalteten Datenträgern:
1. [Wiederherstellen von nicht verwalteten Datenträgern](tutorial-restore-disk.md#restore-a-vm-disk)
2. [Konvertieren der wiederhergestellten Datenträger in verwaltete Datenträger](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk)
3. [Erstellen eines virtuellen Computers mit verwalteten Datenträgern](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk) <br>
Informationen zu PowerShell-Cmdlets finden Sie [hier](backup-azure-vms-automation.md#restore-an-azure-vm).

### <a name="can-i-restore-the-vm-if-my-vm-is-deleted"></a>Kann ich den virtuellen Computer wiederherstellen, falls mein virtueller Computer gelöscht wird?
Ja. Die Lebenszyklen eines virtuellen Computers und des entsprechenden Sicherungselements unterscheiden sich. Selbst wenn Sie den virtuellen Computer löschen, können Sie daher das entsprechende Sicherungselement im Recovery Services-Tresor aufrufen und mithilfe eines der Wiederherstellungspunkte eine Wiederherstellung auslösen. 

## <a name="manage-vm-backups"></a>Verwalten von VM-Sicherungen
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>Was passiert, wenn ich eine Sicherungsrichtlinie für VMs ändere?
Wenn eine neue Richtlinie für VMs angewendet wird, werden der Zeitplan und die Aufbewahrung der neuen Richtlinie beachtet. Bei einer Ausweitung der Aufbewahrung werden bereits vorhandene Wiederherstellungspunkte markiert, um sie gemäß der neuen Richtlinie aufzubewahren. Bei einer Verkürzung der Aufbewahrung werden sie zum Löschen im Rahmen der nächsten Bereinigung markiert und demgemäß gelöscht. 

### <a name="how-can-i-move-a-vm-enrolled-in-azure-backup-between-resource-groups"></a>Wie kann ich eine VM, die in Azure Backup registriert ist, zwischen Ressourcengruppen verschieben?
Führen Sie die folgenden Schritte aus, um die gesicherte VM erfolgreich in die Zielressourcengruppe zu verschieben: 
1. Halten Sie die Sicherung vorübergehend an, und bewahren Sie Sicherungsdaten auf.
2. Verschieben Sie die VM in die Zielressourcengruppe.
3. Schützen Sie sie erneut mit demselben oder einem neuen Tresor.

Benutzer können eine Wiederherstellung mithilfe der verfügbaren Wiederherstellungspunkte durchführen, die vor dem Verschiebevorgang erstellt wurden.


