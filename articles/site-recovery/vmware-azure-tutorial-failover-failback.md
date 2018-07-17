---
title: Ausführen eines Failovers und Failbacks für VMware-VMs und physische Server, die mit Site Recovery nach Azure repliziert werden | Microsoft-Dokumentation
description: Informationen zum Ausführen eines Failovers für VMware-VMs und physische Server auf Azure und eines Failbacks auf den lokalen Standort mit Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/06/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 1f7856edef3bb93300fce0ff00d9434400e239f8
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917043"
---
# <a name="fail-over-and-fail-back-vmware-vms-and-physical-servers-replicated-to-azure"></a>Ausführen eines Failovers und Failbacks für VMware-VMs und physische Server, die nach Azure repliziert werden.

In diesem Tutorial wird das Ausführen eines Failovers einer VMware-VM auf Azure beschrieben. Nach dem Failover erfolgt ein Failback zum lokalen Standort, wenn er verfügbar ist. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Überprüfen der VMware-VM-Eigenschaften, um die Konformität mit Azure-Anforderungen festzustellen
> * Ausführen eines Failovers auf Azure
> * Erstellen eines Prozessservers und Masterzielservers für das Failback
> * Erneutes Schützen von virtuellen Azure-Computern zum lokalen Standort
> * Failover von Azure auf den lokalen Standort
> * Erneutes Schützen von lokalen-VMs, um das erneute Replizieren nach Azure zu starten

>[!NOTE]
>Tutorials dienen zur Veranschaulichung des einfachsten Bereitstellungspfads für ein Szenario. Sie verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade. Ausführlichere Informationen zu den Schritten des Testfailovers finden Sie in [dieser Anleitung](site-recovery-failover.md).

Dies ist das fünfte Tutorial in einer Reihe. In diesem Tutorial wird davon ausgegangen, dass Sie bereits die Aufgaben in den vorherigen Tutorials durchgearbeitet haben.

1. [Vorbereiten von Azure](tutorial-prepare-azure.md)
2. [Lokales Vorbereiten von VMware](vmware-azure-tutorial-prepare-on-premises.md)
3. [Einrichten der Notfallwiederherstellung](vmware-azure-tutorial.md)
4. [Durchführen eines Notfallwiederherstellungsverfahrens](tutorial-dr-drill-azure.md)
5. Zusätzlich zu den oben genannten Schritten wird eine [Überprüfung der Architektur](vmware-azure-architecture.md) für das Notfallwiederherstellungsszenario empfohlen.

## <a name="failover-and-failback"></a>Failover und Failback

Failover und Failback weisen vier Phasen auf:

1. **Failover auf Azure**: Failover von Computern vom lokalen Standort auf Azure.
2. **Erneutes Schützen von virtuellen Azure-Computern**: Erneutes Schützen der Azure-VMs, sodass sie wieder zurück nach lokalen VMware-VMs repliziert werden. Der lokale virtuelle Computer wird während des erneuten Schützens deaktiviert. Dies hilft, die Konsistenz der Daten während der Replikation sicherzustellen.
3. **Failover auf lokalen Standort**: Ausführen eines Failovers, um ein Failback aus Azure durchzuführen.
4. **Erneutes Schützen von lokalen VMs**: Nachdem Ihre Daten per Failback zurückgeführt wurden, schützen Sie die lokalen virtuellen Computer, auf die Sie das Failback durchgeführt haben, erneut, damit diese mit der Replikation nach Azure beginnen.

## <a name="verify-vm-properties"></a>Überprüfen von VM-Eigenschaften

Überprüfen Sie die VM-Eigenschaften, und stellen Sie sicher, dass der virtuelle Computer die [Azure-Anforderungen](vmware-physical-azure-support-matrix.md#replicated-machines) erfüllt.

1. Klicken Sie unter **Geschützte Elemente** auf **Replizierte Elemente** > VM.

2. Im Bereich **Repliziertes Element** finden Sie eine Zusammenfassung der Informationen zu virtuellen Computern, den Integritätsstatus sowie die neuesten verfügbaren Wiederherstellungspunkte. Klicken Sie auf **Eigenschaften**, um weitere Details anzuzeigen.

3. In **Compute und Netzwerk** können Sie den Azure-Namen, die Ressourcengruppe, Zielgröße, [Verfügbarkeitsgruppe](../virtual-machines/windows/tutorial-availability-sets.md) und [Einstellungen verwalteter Datenträger](#managed-disk-considerations) ändern.

4. Sie können Netzwerkeinstellungen einschließlich des Netzwerks/Subnetzes, in dem der virtuelle Azure-Computer nach dem Failover platziert wird, sowie der IP-Adresse, die ihm zugewiesen wird, anzeigen und ändern.

5. Unter **Datenträger** finden Sie Informationen über das Betriebssystem und die Datenträger auf dem virtuellen Computer.

## <a name="run-a-failover-to-azure"></a>Ausführen eines Failovers auf Azure

1. Klicken Sie unter **Einstellungen** > **Replizierte Elemente** auf VM > **Failover**.

2. Wählen Sie unter **Failover** einen **Wiederherstellungspunkt** für das Failover aus. Sie können eine der folgenden Optionen auswählen:
   - **Neueste** (Standard): Mit dieser Option werden zuerst alle an Site Recovery gesendeten Daten verarbeitet. Sie bietet die niedrigste RPO (Recovery Point Objective), da die nach dem Failover erstellte Azure-VM über alle Daten verfügt, die bei Auslösung des Failovers zu Site Recovery repliziert wurden.
   - **Letzte Verarbeitung:** Mit dieser Option wird ein Failover des virtuellen Computers auf den letzten Wiederherstellungspunkt ausgeführt, der von Site Recovery verarbeitet wurde. Diese Option bietet eine niedrige Recovery Time Objective (RTO), da keine Zeit für die Verarbeitung unverarbeiteter Daten aufgewendet wird.
   - **Letzte App-Konsistenz:** Mit dieser Option wird ein Failover des virtuellen Computers auf den letzten App-konsistenten Wiederherstellungspunkt ausgeführt, der von Site Recovery verarbeitet wurde.
   - **Benutzerdefiniert**: Geben Sie einen Wiederherstellungspunkt an.

3. Klicken Sie auf **Der Computer wird vor Beginn des Failovers heruntergefahren**, um zu versuchen, virtuelle Quellcomputer herunterzufahren, bevor das Failover ausgelöst wird. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt.

In einigen Szenarien erfordert ein Failover zusätzliche Verarbeitungsschritte, die etwa 8 bis 10 Minuten dauern können. Sie haben vielleicht **eine längere Dauer beim Testfailover** für virtuelle VMware-Computer bemerkt, die Folgendes verwenden: den Mobilitätsdienst einer Version älter als 9.8, physische Server, VMware-VMs für Linux, virtuelle Hyper-V-Computer, die als physische Server geschützt werden, VMware-VMs, die den DHCP-Dienst nicht aktiviert haben und VMware-VMs, die nicht über folgende Starttreiber verfügen: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> **Brechen Sie ein Failover in Bearbeitung nicht ab:** Vor dem Starten des Failovers wird die VM-Replikation beendet.
> Wenn Sie ein Failover in Bearbeitung abbrechen, wird das Failover beendet, die Replikation der VM wird jedoch nicht erneut durchgeführt.

## <a name="connect-to-failed-over-virtual-machine-in-azure"></a>Verbindung zu der VM in Azure, für die ein Failover ausgeführt wurde

1. Wechseln Sie nach dem Failover zur VM, und überprüfen Sie sie, indem Sie eine [Verbindung](../virtual-machines/windows/connect-logon.md) herstellen.
2. Klicken Sie nach der Überprüfung auf **Commit**, um den Wiederherstellungspunkt des virtuellen Computers nach dem Failover abzuschließen. Nach dem Commit werden alle anderen verfügbaren Wiederherstellungspunkte gelöscht. So wird das Failover abgeschlossen.

>[!TIP]
> **Wiederherstellungspunkt ändern** hilft Ihnen bei der Auswahl eines anderen Wiederherstellungspunkts nach dem Failover, wenn Sie mit der VM, für die ein Failover ausgeführt wurde, nicht zufrieden sind. Nach dem **Commit** ist diese Option nicht mehr verfügbar.

## <a name="preparing-for-reprotection-of-azure-vm"></a>Vorbereitung auf erneuten Schutz der Azure-VM

### <a name="create-a-process-server-in-azure"></a>Erstellen eines Prozessservers in Azure

Der Prozessserver empfängt Daten von der Azure-VM und sendet Daten an den lokalen Standort. Zwischen dem Prozessserver und dem geschützten virtuellen Computer ist ein Netzwerk mit geringer Wartezeit erforderlich.

- Wenn Sie über eine Azure ExpressRoute-Verbindung verfügen, können Sie zu Testzwecken den lokalen Prozessserver verwenden (integrierter Prozessserver), der automatisch auf dem Konfigurationsserver installiert wird.
- Wenn Sie über eine VPN-Verbindung verfügen, oder Sie das Failback in einer Produktionsumgebung ausführen, müssen Sie eine Azure-VM als Azure-basierten Prozessserver für Failback einrichten.
- Um einen Prozessserver in Azure einzurichten, befolgen Sie die Anweisungen in [diesem Artikel](vmware-azure-set-up-process-server-azure.md).

### <a name="configure-the-master-target-server"></a>Konfigurieren des Masterzielservers

Ein Masterzielserver empfängt und verarbeitet die Replikationsdaten während des Failbacks von Azure. Dieser ist standardmäßig auf dem lokalen Konfigurationsserver verfügbar. In diesem Tutorial verwenden wir den Standard-Masterzielserver.

>[!NOTE]
>Für den Schutz einer Linux-basierten VM ist die Erstellung eines separaten Masterzielservers erforderlich. [Klicken Sie hier](vmware-azure-install-linux-master-target.md), um weitere Informationen zu erhalten.

Wenn der virtuelle Computer sich auf einem **ESXi-Host befindet, der von einem vCenter-Server verwaltet wird**, benötigt der Masterzielserver Zugriff auf den VM-Datenspeicher (VMDK), um replizierte Daten auf die VM-Datenträger zu schreiben. Stellen Sie sicher, dass der VM-Datenspeicher auf dem Host des Masterziels mit Lese-/Schreibzugriff eingebunden wird.

Wenn der virtuelle Computer sich auf einem **ESXi-Host befindet, der nicht von einem vCenter-Server verwaltet wird**, erstellt der Site Recovery-Dienst während des erneuten Schutzes einen neuen virtuellen Computer. Dieser virtuelle Computer wird auf dem ESX-Host erstellt, auf dem Sie das Masterziel erstellen.
Die Festplatte des virtuellen Computers muss sich in einen Datenspeicher befinden, der für den Host zugänglich ist, auf dem der Masterzielserver ausgeführt wird.

Wenn der virtuelle Computer **vCenter nicht verwendet**, sollten Sie die Ermittlung des Hosts abschließen, auf dem der Masterzielserver ausgeführt wird, bevor Sie den Computer erneut schützen können. Dasselbe gilt auch bei einem Failback für physische Server. Als weitere Option können Sie den lokalen virtuellen Computer (sofern er noch vorhanden ist) vor dem Ausführen des Failbacks löschen. Beim Failback wird dann ein neuer virtueller Computer auf dem gleichen Host erstellt, der als Masterziel-ESX-Host fungiert. Wenn Sie das Failback zu einem anderen Speicherort durchführen, werden die Daten in dem gleichen Datenspeicher und in dem gleichen ESX-Host wiederhergestellt, der vom lokalen Masterzielserver verwendet wird.

Es ist nicht möglich, Storage vMotion auf dem Masterzielserver zu verwenden. Wenn Sie dies tun, ist kein Failback möglich, da die Datenträger nicht verfügbar sind. Schließen Sie die Masterzielserver aus der vMotion-Liste aus.

>[!Warning]
>Wenn Sie für das erneute Schützen einer Replikationsgruppe einen anderen Masterzielserver verwenden, kann der Server keinen gemeinsamen Zeitpunkt angeben.

## <a name="reprotect-azure-vms"></a>Erneutes Schützen der virtuellen Azure-Computer

Das erneute Schützen einer Azure-VM führt dazu, dass Daten auf einer lokalen VM repliziert werden. Dieser Schritt ist obligatorisch, bevor Sie ein Failover von Azure auf die lokale VM durchführen können. Befolgen Sie die Anweisungen unten, um den erneuten Schutz auszuführen.

1. Klicken Sie in **Einstellungen** > **Replizierte Elemente** mit der rechten Maustaste auf den virtuellen Computer, für den ein Failover ausgeführt wurde und dann auf **Erneut schützen**.
2. Überprüfen Sie in **Erneut schützen**, ob **Azure auf lokal** ausgewählt ist.
3. Geben Sie den lokalen Masterzielserver und den Prozessserver an.
4. Wählen Sie in **Datenspeicher** den Masterziel-Datenspeicher aus, in dem die Datenträger lokal wiederhergestellt werden sollen. Wenn die VM gelöscht wurde, werden auf diesem Datenspeicher neue Datenträger erstellt. Diese Einstellung wird ignoriert, wenn die Datenträger bereits vorhanden sind. Sie müssen aber trotzdem einen Wert angeben.
5. Wählen Sie das Masterziel-Aufbewahrungslaufwerk aus. Die Failbackrichtlinie wird automatisch ausgewählt.
6. Klicken Sie auf **OK**, um das erneute Schützen zu starten. Ein Auftrag beginnt mit der Replikation des virtuellen Computers von Azure am lokalen Standort. Sie können den Fortschritt auf der Registerkarte **Aufträge** nachverfolgen.
7. Nachdem sich der Status der VM für **Replizierte Elemente** in **Geschützt** ändert, ist der Computer für ein Failover zum lokalen Standort bereit.

> [!NOTE]
> Die Azure-VM kann auf einer vorhandenen lokalen VM oder an einem anderen Standort wiederhergestellt werden. Weitere Informationen finden Sie in [diesem Artikel](concepts-types-of-failback.md).

## <a name="run-a-failover-from-azure-to-on-premises"></a>Ausführen eines Failovers von Azure auf den lokalen Standort

Um zurück nach dem lokalen Standort zu replizieren, wird eine Failbackrichtlinie verwendet. Diese Richtlinie wird automatisch erstellt, wenn Sie eine Replikationsrichtlinie für die Replikation zu Azure erstellen:

- Die Richtlinie wird dem Konfigurationsserver automatisch zugeordnet.
- Die Richtlinie kann nicht geändert werden.
- Die Werte der Richtlinie sind:
    - RPO-Schwellenwert = 15 Minuten
    - Aufbewahrungszeitraum des Wiederherstellungspunkts = 24 Stunden
    - Häufigkeit von Momentaufnahmen für App-Konsistenz = 60 Minuten

Führen Sie das Failover wie folgt aus:

1. Klicken Sie auf der Seite **Replizierte Elemente** mit der rechten Maustaste auf den Computer und dann auf **Failover**.
2. Überprüfen Sie in **Failover bestätigen**, dass das Failover von Azure aus erfolgt.
    ![failover-direction](media/vmware-azure-tutorial-failover-failback/failover-direction.PNG)
3. Wählen Sie den Wiederherstellungspunkt aus, den Sie für das Failover verwenden möchten. Ein anwendungskonsistenter Punkt liegt vor dem aktuellsten Zeitpunkt und verursacht einigen Datenverlust.

    >[!WARNING]
    >Wenn das Failover ausgeführt wird, fährt Site Recovery die Azure-VMs herunter und startet den lokalen virtuellen Computer. Da dies mit einer gewissen Ausfallzeit verbunden ist, wählen Sie einen geeigneten Zeitpunkt.

4. Der Fortschritt des Auftrags kann unter **Recovery Services-Tresor** > **Überwachung und Berichte** > **Site Recovery-Aufträge** zurückverfolgt werden.
5. Klicken Sie nach Abschluss des Failovers mit der rechten Maustaste auf die VM und dann auf **Commit**. Dadurch wird ein Auftrag ausgelöst, der die Azure-VMs entfernt.
6. Stellen Sie sicher, dass die Azure-VMs wie erwartet heruntergefahren wurden.

## <a name="reprotect-on-premises-machines-to-azure"></a>Erneutes Schützen lokaler Computer in Azure

Die Daten sollten nun wieder auf Ihrem lokalen Standort sein, aber sie werden nicht nach Azure repliziert. Sie können die Replikation nach Azure folgendermaßen erneut starten:

1. Wählen Sie im Tresor unter **Geschützte Elemente** >**Replizierte Elemente** den virtuellen Computer aus, für den das Failback durchgeführt wurde, und klicken Sie auf **Erneut schützen**.
2. Wählen Sie den Prozessserver, mit dem die replizierten Daten an Azure gesendet werden sollen, und klicken Sie auf **OK**.

Nachdem das erneute Schützen abgeschlossen ist, wird der virtuelle Computer zurück nach Azure repliziert, und Sie können ggf. ein Failover ausführen.
