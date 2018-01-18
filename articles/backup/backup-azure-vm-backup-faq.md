---
title: "Häufig gestellte Fragen zu Azure VM Backup | Microsoft-Dokumentation"
description: "Hier finden Sie Antworten auf häufig gestellte Fragen zur Funktionsweise von Azure VM Backup, zu Einschränkungen sowie zu den Folgen von Richtlinienänderungen."
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: Azure VM Backup, Azure VM Restore, Sicherungsrichtlinie
ms.assetid: c4cd7ff6-8206-45a3-adf5-787f64dbd7e1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.openlocfilehash: 5ba381e366bea78e2d0ace3651c52b7c03e18275
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2018
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Fragen zum Azure VM Backup-Dienst
Dieser Artikel enthält Antworten auf häufig gestellte Fragen, damit Sie sich schnell mit den Komponenten von Azure VM Backup vertraut machen können. Einige Antworten enthalten Links zu Artikeln mit umfassenderen Informationen. Außerdem können Sie Fragen zum Azure Backup-Dienst im [Diskussionsforum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)stellen.

## <a name="configure-backup"></a>Konfigurieren der Sicherung
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Unterstützen Recovery Services-Tresore klassische virtuelle Computer oder Resource Manager-basierte virtuelle Computer? <br/>
Recovery Services-Tresore unterstützen beide Modelle.  Sie können eine klassische VM (die im klassischen Portal erstellt wurde) oder eine Resource Manager-VM (die im Azure-Portal erstellt wurde) in einem Recovery Services-Tresor schützen.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup"></a>Welche Konfigurationen werden von der Azure-VM-Sicherung nicht unterstützt?
Informationen zu unterstützten Betriebssystemen finden Sie [hier](backup-azure-arm-vms-prepare.md#supported-operating-systems-for-backup). Informationen zu Einschränkungen beim Sichern eines virtuellen Computers finden Sie [hier](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm).

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>Warum wird mein virtueller Computer im Sicherungskonfigurations-Assistenten nicht angezeigt?
Im Sicherungskonfigurations-Assistenten werden nur virtuelle Computer aufgeführt, die folgende Voraussetzungen erfüllen:
  * Sie sind noch nicht geschützt: Navigieren Sie zum Ermitteln des Sicherungsstatus eines virtuellen Computers zum VM-Blatt, und überprüfen Sie über das Einstellungsmenü des Blatts den Sicherungsstatus. Weitere Informationen zum Überprüfen des Sicherungsstatus eines virtuellen Computers finden Sie [hier](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-management-blade).
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

## <a name="restore"></a>Wiederherstellung
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

## <a name="manage-vm-backups"></a>Verwalten von VM-Sicherungen
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>Was passiert, wenn ich eine Sicherungsrichtlinie für VMs ändere?
Wenn eine neue Richtlinie für VMs angewendet wird, werden der Zeitplan und die Aufbewahrung der neuen Richtlinie beachtet. Bei einer Ausweitung der Aufbewahrung werden bereits vorhandene Wiederherstellungspunkte markiert, um sie gemäß der neuen Richtlinie aufzubewahren. Bei einer Verkürzung der Aufbewahrung werden sie zum Löschen im Rahmen der nächsten Bereinigung markiert und demgemäß gelöscht. 
