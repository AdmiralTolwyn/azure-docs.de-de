---
title: Vorbereiten des Ziels (VMware nach Azure) | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Sie Ihre Azure-Umgebung vorbereiten, um mit dem Replizieren von virtuellen VMware-Computern nach Azure zu beginnen.
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 11/23/2017
ms.author: bsiva
ms.openlocfilehash: 98e0a7cd77e8e6e9ce124845aad49bd03a2bf1d8
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2017
---
# <a name="prepare-target-vmware-to-azure"></a>Vorbereiten des Ziels (VMware nach Azure)
> [!div class="op_single_selector"]
> * [VMware zu Azure](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Physisch nach Azure](./site-recovery-prepare-target-physical-to-azure.md)

Dieser Artikel beschreibt, wie Sie Ihre Azure-Umgebung vorbereiten, um mit dem Replizieren von virtuellen VMware-Computern nach Azure zu beginnen.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird Folgendes vorausgesetzt:
- Sie haben einen Recovery Services-Tresor zum Schutz Ihrer virtuellen VMware-Computer erstellt. Sie können einen Recovery Services-Tresor im [Azure-Portal](http://portal.azure.com "Azure-Portal") erstellen.
- Sie haben [Ihre lokale Umgebung eingerichtet](./site-recovery-set-up-vmware-to-azure.md), um virtuelle VMware-Computer nach Azure zu replizieren.

## <a name="prepare-target"></a>Vorbereiten des Ziels

Nach Abschluss von **Schritt 1: Auswählen des Schutzziels** und **Schritt 2: Vorbereiten der Quelle** gelangen Sie zu **Schritt 3: Ziel**

![Vorbereiten des Ziels](./media/site-recovery-prepare-target-vmware-to-azure/prepare-target-vmware-to-azure.png)

1. **Abonnement:** Wählen Sie aus dem Dropdownmenü das Abonnement aus, in das Sie die virtuellen Computer replizieren möchten.
2. **Bereitstellungsmodell:** Wählen Sie das Bereitstellungsmodell aus (klassisch oder Resource Manager).

Basierend auf dem ausgewählten Bereitstellungsmodell wird eine Überprüfung vorgenommen, um sicherzustellen, dass Sie über mindestens ein kompatibles Speicherkonto und ein virtuelles Netzwerk im Zielabonnement verfügen, um die Replikation und das Failover Ihres virtuellen Computers auszuführen.

Wenn die Überprüfungen erfolgreich abgeschlossen sind, klicken Sie auf „OK“, um mit dem nächsten Schritt fortzufahren.

Falls Sie über kein kompatibles Resource Manager-Speicherkonto oder kein virtuelles Netzwerk verfügen, können Sie eins erstellen, indem Sie oben auf der Seite auf die Schaltflächen **+ Speicherkonto** oder **+ Netzwerk** klicken.

## <a name="next-steps"></a>Nächste Schritte
[Konfigurieren der Replikationseinstellungen](./site-recovery-setup-replication-settings-vmware.md).
