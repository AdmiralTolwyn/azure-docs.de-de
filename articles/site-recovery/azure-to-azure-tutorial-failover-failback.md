---
title: Ausführen eines Failovers und Failbacks von replizierten Azure-VMs auf eine sekundäre Azure-Region mit Azure Site Recovery
description: Erfahren Sie, wie Sie ein Failover und Failback von replizierten Azure-VMs auf eine sekundäre Azure-Region mit Azure Site Recovery durchführen.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/31/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: cb9bfd000c377f63e99350d0615a7e92ba8bb8ef
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716243"
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions"></a>Ausführen eines Failovers und Failbacks von Azure-VMs zwischen Azure-Regionen

Der Dienst [Azure Site Recovery](site-recovery-overview.md) unterstützt Ihre Strategie zur Notfallwiederherstellung, indem Replikation, Failover und Failback von lokalen Computern und virtuellen Azure-Computern (Virtual Machines, VMs) verwaltet und orchestriert werden.

In diesem Tutorial wird beschrieben, wie für eine einzelne Azure-VM ein Failover zu einer sekundären Azure-Region durchgeführt wird. Nach dem Failover erfolgt ein Failback auf die primäre Region, wenn sie verfügbar ist. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Ausführen eines Failovers der Azure-VM
> * Erneutes Schützen der sekundären Azure-VM, damit sie zur primären Region repliziert wird
> * Ausführen des Failbacks der sekundären VM
> * Erneutes Schützen der primären VM zurück zur sekundären Region



## <a name="prerequisites"></a>Voraussetzungen

- Führen Sie unbedingt ein [Notfallwiederherstellungsverfahren](azure-to-azure-tutorial-dr-drill.md) durch, um zu überprüfen, ob alles wie erwartet funktioniert.
- Überprüfen Sie die VM-Eigenschaften, bevor Sie das Testfailover ausführen. Die VM muss den [Azure-Anforderungen](azure-to-azure-support-matrix.md#support-for-replicated-machine-os-versions) entsprechen.

## <a name="run-a-failover-to-the-secondary-region"></a>Ausführen eines Failovers zur sekundären Region

1. Wählen Sie unter **Replizierten Elemente** die VM, für die ein Failover ausgeführt werden soll, > **Failover** aus.

   ![Failover](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. Wählen Sie unter **Failover** einen **Wiederherstellungspunkt** für das Failover aus. Sie können eine der folgenden Optionen auswählen:

   * **Neueste** (Standard): Diese Option verarbeitet alle Daten in Site Recovery und stellt das niedrigste Recovery Point Objective (RPO) bereit.
   * **Latest processed** (Zuletzt verarbeitet): Diese Option stellt den virtuellen Computer auf den Zustand des letzten Wiederherstellungspunkts wieder her, der bereits von Site Recovery verarbeitet wurde.
   * **Benutzerdefinierte**: Verwenden Sie diese Option für ein Failover auf einen bestimmten Wiederherstellungspunkt. Diese Option eignet sich für ein Testfailover.

3. Klicken Sie auf **Der Computer wird vor Beginn des Failovers heruntergefahren**, wenn Site Recovery versuchen soll, virtuelle Quellcomputer herunterzufahren, bevor das Failover ausgelöst wird. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist.

4. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt.

5. Überprüfen Sie den virtuellen Computer nach Abschluss des Failovers, indem Sie sich am Computer anmelden. Falls Sie einen anderen Wiederherstellungspunkt für den virtuellen Computer verwenden möchten, können Sie die Option **Wiederherstellungspunkt ändern** wählen.

6. Nachdem Sie mit dem virtuellen Computer, für den das Failover durchgeführt wurde, zufrieden sind, können Sie für das Failover die Option **Commit** wählen.
   Dadurch werden alle mit dem Dienst verfügbaren Wiederherstellungspunkte gelöscht. Die Option **Wiederherstellungspunkt ändern** ist nicht mehr verfügbar.

## <a name="reprotect-the-secondary-vm"></a>Erneutes Schützen der sekundären VM

Nach dem Failover der VM müssen Sie sie erneut schützen, damit sie wieder zurück zur primären Region repliziert wird.

1. Stellen Sie sicher, dass sich die VM im Status **Commit für Failover ausgeführt** befindet, dass die primäre Region verfügbar ist und dass Sie neue Ressourcen darin erstellen und darauf zugreifen können.
2. Klicken Sie unter **Tresor** > **Replizierte Elemente** mit der rechten Maustaste auf die VM, für die ein Failover durchgeführt wurde, und dann auf **Erneut schützen**.

   ![Rechtsklick zum erneuten Schützen](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Beachten Sie, dass die Wirkrichtung des Schutzes – von der sekundären zur primären Region – bereits ausgewählt ist.
3. Lesen Sie die Informationen zu **Ressourcengruppe, Netzwerk, Speicher und Verfügbarkeitsgruppen**, und klicken Sie auf „OK“. Alle markierten Ressourcen (neu) werden während des Vorgangs zum erneuten Schützen erstellt.
4. Klicken Sie auf **OK**, um einen Auftrag zum erneuten Schützen auszulösen. Dieser Auftrag liefert die neuesten Daten an den Zielstandort. Anschließend werden die Deltas zur primären Region repliziert. Die VM befindet sich jetzt in einem geschützten Zustand.

## <a name="fail-back-to-the-primary-region"></a>Ausführen eines Failbacks auf die primäre Region

Nachdem VMs erneut geschützt wurden, können Sie bei Bedarf ein Failback zur primären Region ausführen. Befolgen Sie dazu die [Failover-Anweisungen](#run-a-failover) unten.
