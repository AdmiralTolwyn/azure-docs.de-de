---
title: Migrieren von Azure-VMs zu Azure Managed Disks | Microsoft-Dokumentation
description: "Migrieren Sie virtuelle Azure-Computer, die in Speicherkonten mit nicht verwalteten Datenträgern erstellt wurden, zu Managed Disks."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: cynthn
ms.openlocfilehash: e23697b390e03bd2b71f2c905882070d864d62ed
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/29/2017
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrieren von Azure-VMs zu Managed Disks in Azure

Azure Managed Disks vereinfacht die Speicherverwaltung dadurch, dass die gesonderte Verwaltung von Speicherkonten entfällt.  Sie können auch Ihre vorhandenen Azure-VMs zu Managed Disks migrieren, um von der höheren Zuverlässigkeit von VMs in einer Verfügbarkeitsgruppe zu profitieren. Es wird sichergestellt, dass die Datenträger verschiedener VMs in einer Verfügbarkeitsgruppe ausreichend voneinander isoliert sind, um einzelne Fehlerquellen zu vermeiden. Datenträger verschiedener VMs werden automatisch in einer Verfügbarkeitsgruppe in unterschiedlichen Skalierungseinheiten von Speicher (sog. „Stamps“) platziert. Dadurch werden die Auswirkungen des Ausfalls einzelner Speicherskalierungseinheiten eingedämmt, die von Hardware- und Softwarefehlern verursacht werden.
Je nach Anforderungen stehen zwei Typen von Speicheroptionen zur Wahl:

- [Verwaltete Premium-Datenträger](../../storage/common/storage-premium-storage.md) sind SSD-basierte (Solid State Drives) Speichermedien, die für virtuelle Computer mit E/A-intensiven Workloads eine hohe Datenträgerleistung mit niedriger Latenz bieten. Durch Migration zu Premium Managed Disks können Sie von der Geschwindigkeit und Leistung dieser Laufwerke profitieren.

- [Standard Managed Disks](../../storage/common/storage-standard-storage.md) (verwaltete Standard-Datenträger) arbeiten mit HDD-basierten Speichermedien (mit Magnetfestplatten) und eignen sich hervorragend für Entwicklungs- und Testaufgaben sowie andere weniger häufig anfallende Workloads, bei denen Leistungsschwankungen keine große Rolle spielen.

Sie können in folgenden Szenarien zu Managed Disks migrieren:

| Migrieren Sie Folgendes:                                            | Link zur Dokumentation                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Konvertieren von eigenständigen virtuellen Computern und virtuellen Computern in einer Verfügbarkeitsgruppe in verwaltete Datenträger   | [Konvertieren von virtuellen Computern für die Verwendung verwalteter Datenträger](convert-unmanaged-to-managed-disks.md) |
| Eine einzelne VM aus dem klassischen Bereitstellungsmodell zum Ressourcen-Manager-Bereitstellungsmodell auf verwalteten Datenträgern     | [Migrieren einer einzelnen VM](migrate-single-classic-to-resource-manager.md)  | 
| Alle VMs in einem VNet aus dem klassischen Bereitstellungsmodell zum Ressourcen-Manager-Bereitstellungsmodell auf verwalteten Datenträgern     | [Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zum Ressourcen-Manager-Bereitstellungsmodell](migration-classic-resource-manager-ps.md) und dann [Konvertieren eines virtuellen Computers von nicht verwalteten Datenträgern in verwaltete Datenträger](convert-unmanaged-to-managed-disks.md) | 






## <a name="plan-for-the-conversion-to-managed-disks"></a>Planen der Migration zu Managed Disks

Dieser Abschnitt hilft Ihnen, hinsichtlich VM- und Datenträgertypen die beste Entscheidung zu treffen.


## <a name="location"></a>Standort

Wählen Sie einen Standort, an dem Azure Managed Disks verfügbar sind. Wenn Sie zu Premium Managed Disks wechseln, stellen Sie außerdem sicher, dass Storage Premium in der Region verfügbar ist, zu der der Wechsel erfolgen soll. Aktuelle Informationen zu verfügbaren Standorten finden Sie unter [Azure-Dienste nach Region](https://azure.microsoft.com/regions/#services).

## <a name="vm-sizes"></a>VM-Größen

Wenn Sie zu Premium Managed Disks migrieren, müssen Sie die Größe der VM in eine Storage Premium-fähige Größe ändern, die in der Region mit dem Standort der VM verfügbar ist. Überprüfen Sie die Storage Premium-fähigen VM-Größen. Die Größenspezifikationen der Azure-VM sind unter [Größen für virtuelle Computer](sizes.md)aufgelistet.
Sehen Sie sich die Leistungsmerkmale von virtuellen Computern für Storage Premium an, und wählen Sie die am besten für Ihre Workload geeignete VM-Größe aus. Stellen Sie sicher, dass auf Ihrem virtuellen Computer ausreichend Bandbreite zum Steuern des Datenverkehrs des Datenträgers verfügbar ist.

## <a name="disk-sizes"></a>Datenträgergrößen

**Premium Managed Disks**

Es gibt sieben verwaltete Premium-Datenträgertypen, die Sie mit Ihrer VM verwenden können. Jeder Typ weist bestimmte IOPS- und Durchsatzeinschränkungen auf. Berücksichtigen Sie diese Beschränkungen beim Auswählen des Premium-Datenträgertyps für Ihren virtuellen Computer je nach Anforderungen Ihrer Anwendung hinsichtlich Kapazität, Leistung, Skalierbarkeit und Spitzenlasten.

| Premium-Datenträgertyp  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Datenträgergröße           | 128 GB| 512 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS pro Datenträger       | 120   | 240   | 500   | 2.300              | 5.000              | 7.500              | 7.500              | 
| Durchsatz pro Datenträger | 25 MB pro Sekunde  | 50 MB pro Sekunde  | 100 MB pro Sekunde | 150 MB pro Sekunde | 200 MB pro Sekunde | 250 MB pro Sekunde | 250 MB pro Sekunde |

**Verwaltete Standard-Datenträger**

Es gibt sieben Arten von verwalteten Standard-Datenträgern, die mit Ihrer VM verwendet werden können. Jede davon bietet unterschiedliche Kapazität, jedoch gelten für alle dieselben IOPS- und Durchsatzgrenzwerte. Wählen Sie den Typ der verwalteten Standard-Datenträger basierend auf den Kapazitätsanforderungen Ihrer Anwendung.

| Standard-Datenträgertyp  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| Datenträgergröße           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| IOPS pro Datenträger       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| Durchsatz pro Datenträger | 60 MB pro Sekunde | 60 MB pro Sekunde | 60 MB pro Sekunde | 60 MB pro Sekunde | 60 MB pro Sekunde | 60 MB pro Sekunde | 60 MB pro Sekunde | 

## <a name="disk-caching-policy"></a>Zwischenspeicherungsrichtlinie für Datenträger

**Premium Managed Disks**

Standardmäßig ist die Richtlinie für das Zwischenspeichern für alle Premium-Datenträger *Schreibgeschützt* und für die Premium-Betriebssystem-Datenträger, die an den virtuellen Computer angeschlossen sind, *Lesen/Schreiben*. Diese Konfigurationseinstellung wird empfohlen, um die optimale E/A-Leistung für Ihre Anwendung zu erreichen. Für Datenträger mit hohem oder ausschließlichem Schreibzugriff (z. B. SQL Server-Protokolldateien) deaktivieren Sie das Zwischenspeichern, sodass Sie eine bessere Anwendungsleistung erzielen können.

## <a name="pricing"></a>Preise

Überprüfen Sie die [Preise für Managed Disks](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). Die Preise für Premium Managed Disks (verwaltete Premium-Datenträger) sind identisch mit denen für Premium Unmanaged Disks (nicht verwaltete Premium-Datenträger). Doch die Preise für verwaltete Standard-Datenträger unterscheiden sich von denen für nicht verwaltete Standard-Datenträger.



## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Managed Disks](managed-disks-overview.md).
