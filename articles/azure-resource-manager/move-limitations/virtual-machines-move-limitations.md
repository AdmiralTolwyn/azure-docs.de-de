---
title: Verschieben virtueller Azure-Computer in ein neues Abonnement oder in eine neue Ressourcengruppe
description: Verwenden Sie Azure Resource Manager, um virtuelle Computer in eine neue Ressourcengruppe oder ein neues Abonnement zu verschieben.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: b03e9618e621216f74cb02828183df7ee6b502ea
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150938"
---
# <a name="move-guidance-for-virtual-machines"></a>Anleitung zum Verschieben virtueller Computer

In diesem Artikel werden Szenarien beschrieben, die derzeit nicht unterstützt werden, und die Schritte zum Verschieben virtueller Computer mit Sicherungen.

## <a name="scenarios-not-supported"></a>Nicht unterstützte Szenarien

Folgende Szenarios werden noch nicht unterstützt:

* Verwaltete Datenträger in Verfügbarkeitszonen können nicht in ein anderes Abonnement verschoben werden.
* Eine Virtual Machine Scale Sets-Instanz mit Load Balancer der Standard-SKU oder einer öffentlichen IP-Adresse der Standard-SKU kann nicht verschoben werden.
* Von Marketplace-Ressourcen erstellte virtuelle Computer, an die Pläne angefügt sind, können nicht ressourcengruppen- oder abonnementübergreifend verschoben werden. Heben Sie die Bereitstellung des virtuellen Computers im aktuellen Abonnement auf, und stellen Sie ihn im neuen Abonnement erneut bereit.
* Virtuelle Computer in einem vorhandenen virtuellen Netzwerk können nicht zu einem neuen Abonnement verschoben werden, wenn Sie nicht alle Ressourcen im virtuellen Netzwerk verschieben.
* Virtuelle Computer mit niedriger Priorität und VM-Skalierungsgruppen mit niedriger Priorität können nicht ressourcengruppen- oder abonnementübergreifend verschoben werden.
* Virtuelle Computer in einer Verfügbarkeitsgruppe können nicht einzeln verschoben werden.

## <a name="virtual-machines-with-azure-backup"></a>Virtuelle Computer mit Azure Backup

Verwenden Sie die folgende Problemumgehung, um mit Azure Backup konfigurierte virtuelle Computer zu verschieben:

* Ermitteln Sie den Speicherort Ihres virtuellen Computers.
* Suchen Sie eine Ressourcengruppe mit dem folgenden Namensmuster: `AzureBackupRG_<location of your VM>_1`, z. B. „AzureBackupRG_westus2_1“
* Wenn Sie das Azure-Portal verwenden, aktivieren Sie die Option „Ausgeblendete Typen anzeigen“
* Wenn Sie PowerShell verwenden, verwenden Sie das Cmdlet `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`
* Wenn Sie die CLI (Befehlszeilenschnittstelle) verwenden, verwenden Sie den Befehl `az resource list -g AzureBackupRG_<location of your VM>_1`
* Suchen Sie nach der Ressource mit dem Typ `Microsoft.Compute/restorePointCollections` und dem Namensmuster `AzureBackup_<name of your VM that you're trying to move>_###########`.
* Löschen Sie diese Ressource. Dieser Vorgang löscht nur die sofortigen Wiederherstellungspunkte und nicht die gesicherten Daten im Tresor.
* Nach Abschluss des Löschvorgangs können Sie den Tresor und den virtuellen Computer in das Zielabonnement verschieben. Nach dem Verschieben können Sie ohne Datenverlust mit den Sicherungsdateien fortfahren.
* Weitere Informationen zum Verschieben von Recovery Services-Tresoren für die Sicherung finden Sie unter [Einschränkungen von Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Nächste Schritte

Befehle zum Verschieben von Ressourcen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../resource-group-move-resources.md).
