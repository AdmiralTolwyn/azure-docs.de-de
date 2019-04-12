---
title: Aktivieren der Sicherung beim Erstellen eines virtuellen Azure-Computers mit Azure Backup
description: Hier wird beschrieben, wie die Sicherung beim Erstellen eines virtuellen Azure-Computers mit Azure Backup aktiviert wird.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: d96b898c8f72abd7e4eb3522ae046e9fc926f387
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403577"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Aktivieren der Sicherung beim Erstellen eines virtuellen Azure-Computers

Verwenden Sie den Azure Backup-Dienst zum Sichern von Azure-VMs (virtuelle Computer). VMs werden gemäß eines Zeitplans gesichert, der in einer Sicherungsrichtlinie festgelegt wird. Die Wiederherstellungspunkte werden aus Sicherungen erstellt. Die Wiederherstellungspunkte werden in Recovery Services-Tresoren gespeichert.

In diesem Artikel erfahren Sie, wie Sie die Sicherung beim Erstellen eines virtuellen Computers (Virtual Machine, VM) über das Azure-Portal aktivieren.  

## <a name="before-you-start"></a>Vorbereitung

- [Überprüfen Sie](backup-support-matrix-iaas.md#supported-backup-actions), welche Betriebssysteme unterstützt werden, wenn Sie beim Erstellen einer VM die Sicherung aktivieren.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Falls Sie noch nicht bei Ihrem Konto angemeldet sind, melden Sie beim [Azure-Portal](https://portal.azure.com) an.
 
## <a name="create-a-vm-with-backup-configured"></a>Erstellen einer VM mit konfigurierter Sicherung

1. Klicken Sie im Azure-Portal auf **Ressource erstellen**.

2. Klicken Sie im Azure Marketplace auf **Compute**, und wählen Sie dann ein VM-Image aus.

3. Richten Sie die VM gemäß den Anweisungen für [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) oder [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) ein.

4. Klicken Sie auf der Registerkarte **Verwaltung** unter **Backup aktivieren** auf **Ein**.
5. Azure Backup führt Sicherungen in einen Recovery Services-Tresor durch. Klicken Sie auf **Neu erstellen**, wenn Sie über keinen vorhandenen Tresor verfügen.
6. Akzeptieren Sie den vorgeschlagenen Tresornamen, oder geben Sie einen eigenen an.
7. Geben Sie eine Ressourcengruppe an oder erstellen Sie sie, in der sich der Tresor befinden wird. Der Tresor der Ressourcengruppe kann sich von der VM-Ressourcengruppe unterscheiden.

    ![Aktivieren der Sicherung für einen virtuellen Computer](./media/backup-during-vm-creation/enable-backup.png) 

8. Akzeptieren Sie die Standardsicherungsrichtlinie, oder ändern Sie die Einstellungen.
    - Eine Sicherungsrichtlinie gibt an, wie häufig Sicherungsmomentaufnahmen des virtuellen Computers erstellt und wie lang die Sicherungskopien aufbewahrt werden sollen. 
    - Die Standardrichtlinie sichert den virtuellen Computer einmal täglich.
    - Sie können Ihre eigene Sicherungsrichtlinie für eine Azure-VM anpassen, um täglich oder wöchentlich Sicherungen zu erstellen.
    - [Weitere Informationen](backup-azure-vms-introduction.md#backup-and-restore-considerations) zu Überlegungen zur Sicherung für Azure-VMs.
    - [Weitere Informationen](backup-instant-restore-capability.md) zur Funktionalität der sofortigen Wiederherstellung.

      ![Standardsicherungsrichtlinie](./media/backup-during-vm-creation/daily-policy.png) 


## <a name="start-a-backup-after-creating-the-vm"></a>Starten einer Sicherung nach dem Erstellen der VM 

Ihre VM-Sicherung wird in Übereinstimmung mit Ihrer Sicherungsrichtlinie ausgeführt. Es wird jedoch empfohlen, eine erste Sicherung auszuführen. 

Nachdem die VM erstellt wurde, gehen Sie wie folgt vor:

1. Klicken Sie unter den VM-Eigenschaften auf **Sicherung**. Der VM-Status lautet „Erste Sicherung ausstehend“, bis die erste Sicherung ausgeführt wird.
2. Klicken Sie auf **Jetzt sichern**, um eine On-Demand-Sicherung auszuführen.

    ![Ausführen einer On-Demand-Sicherung](./media/backup-during-vm-creation/run-backup.png) 

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Bereitstellen einer geschützten VM mithilfe einer Resource Manager-Vorlage

In den vorherigen Schritten wurde erläutert, wie Sie eine VM über das Azure-Portal erstellen und in einem Recovery Services-Tresor schützen. Informationen zur schnellen Bereitstellung und zum Schützen von VMs in einem Recovery Services-Tresor finden Sie unter [Deploy a Windows VM and enable backup (Bereitstellen einer Windows-VM und Aktivieren der Sicherung)](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).



## <a name="next-steps"></a>Nächste Schritte 

Nachdem Sie Ihre VM geschützt haben, erfahren Sie, wie Sie diese verwalten und wiederherstellen können.

- [Verwalten und Überwachen virtueller Computer](backup-azure-manage-vms.md) 
- [Wiederherstellen eines virtuellen Computers](backup-azure-arm-restore-vms.md) 

Wenn Sie auf Probleme stoßen, [lesen](backup-azure-vms-troubleshoot.md) Sie den Leitfaden zur Problembehandlung.
