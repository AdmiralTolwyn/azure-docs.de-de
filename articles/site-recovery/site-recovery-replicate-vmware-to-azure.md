---
title: Replizieren von Anwendungen (VMware in Azure) | Microsoft Docs
description: "Dieser Artikel beschreibt, wie Sie die Replikation von virtuellen Computern, die unter VMware ausgeführt werden, in Azure einrichten."
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/29/2017
ms.author: asgang
ms.openlocfilehash: a0d146081b552ee181fdf93fb60790c27f108888
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-applications-running-on-vmware-vms-to-azure"></a>Replizieren von Anwendungen, die auf virtuellen VMware-Computern ausgeführt werden, in Azure



Dieser Artikel beschreibt, wie Sie die Replikation von virtuellen Computern, die unter VMware ausgeführt werden, in Azure einrichten.
## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie Folgendes bereits durchgeführt haben:

1.  [Einrichten der lokalen Quellumgebung](site-recovery-set-up-vmware-to-azure.md)
2.  [Einrichten der Zielumgebung in Azure](site-recovery-prepare-target-vmware-to-azure.md)


## <a name="enable-replication"></a>Replikation aktivieren
#### <a name="before-you-start"></a>Vorbereitung
Wenn Sie VMware-VMs replizieren, beachten Sie Folgendes:

* Ihr Azure-Benutzerkonto benötigt bestimmte [Berechtigungen](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) zum Aktivieren der Replikation eines neuen virtuellen Computers in Azure.
* VMware-VMs werden alle 15 Minuten ermittelt. Es kann 15 Minuten oder länger dauern, bis sie nach der Ermittlung im Portal angezeigt werden. Ebenso kann Ermittlung 15 Minuten oder länger dauern, wenn Sie einen neuen vCenter-Server oder vSphere-Host hinzufügen.
* Es kann auch 15 Minuten oder länger dauern, bis Umgebungsänderungen auf dem virtuellen Computer (z.B. die Installation von VMware-Tools) im Portal aktualisiert werden.
* Sie können den Zeitpunkt der letzten Ermittlung für die VMware-VMs auf der Seite **Konfigurationsserver** im Feld **Letzter Kontakt um** für den vCenter-Server oder vSphere-Host überprüfen.
* Wenn Sie Computer für die Replikation hinzufügen möchten, ohne auf die planmäßige Ermittlung zu warten, markieren Sie den Konfigurationsserver (nicht darauf klicken), und klicken Sie auf die Schaltfläche **Refresh** (Aktualisieren).
* Wenn Sie die Replikation aktivieren, installiert der Prozessserver den Mobilitätsdienst automatisch auf dem Computer, falls dieser vorbereitet ist.


**Aktivieren Sie die Replikation jetzt wie folgt**:

1. Klicken Sie auf **Schritt 2: Replizieren Sie die Anwendung** > **Quelle**. Klicken Sie nach der erstmaligen Aktivierung der Replikation im Tresor auf **+Replizieren**, um die Replikation für weitere Computer zu aktivieren.
2. Wählen Sie auf der Seite **Quelle** unter **Quelle** den Konfigurationsserver aus.
3. Wählen Sie unter **Computertyp** die Option **Virtuelle Computer** oder **Physische Computer** aus.
4. Wählen Sie unter **vCenter-/vSphere-Hypervisor** den vCenter-Server aus, der den vSphere-Host verwaltet, oder wählen Sie den Host aus. Wenn Sie physische Computer replizieren, ist diese Einstellung nicht relevant.
5. Wählen Sie den Prozessserver aus. Wenn Sie keine zusätzlichen Prozessserver erstellt haben, ist dies der Name des Konfigurationsservers. Klicken Sie dann auf **OK**.

    ![Replikation aktivieren](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. Wählen Sie unter **Ziel** das Abonnement und die Ressourcengruppe aus, in dem bzw. der Sie die virtuellen Computer erstellen möchten, für die ein Failover durchgeführt wurde. Wählen Sie das Bereitstellungsmodell aus, das in Azure für die virtuellen Computer verwendet werden soll, für die ein Failover durchgeführt wurde (klassisch oder Resource Manager).
7. Wählen Sie das Azure-Speicherkonto aus, das Sie für die Replikation von Daten verwenden möchten. Beachten Sie Folgendes:

   * Sie können ein Premium- oder Standardspeicherkonto auswählen. Wenn Sie ein Premium-Konto auswählen, müssen Sie für fortlaufende Replikationsprotokolle ein zusätzliches Standard-Speicherkonto angeben. Die Konten müssen sich in derselben Region wie der Recovery Services-Tresor befinden.
   * Wenn Sie ein anderes Speicherkonto als Ihr bestehendes verwenden möchten, erstellen Sie einen *Platzhalterlink zum Erstellen eines Speicherkontos mithilfe des in den ersten Schritten behandelten Resource Manager*. Klicken Sie zum Erstellen eines Speicherkontos mit dem Resource Manager auf **Neu erstellen**. Wenn Sie ein Speicherkonto mit dem klassischen Modell erstellen möchten, verwenden Sie hierfür das [Azure-Portal](../storage/common/storage-create-storage-account.md).

8. Wählen Sie das Azure-Netzwerk und das Subnetz aus, mit dem Azure-VMs eine Verbindung herstellen, wenn sie nach einem Failover erstellt werden. Das Netzwerk muss sich in derselben Region wie der Recovery Services-Tresor befinden. Wählen Sie die Option **Jetzt für die ausgewählten Computer konfigurieren** aus, um die Netzwerkeinstellung auf alle Computer anzuwenden, die geschützt werden sollen. Wählen Sie **Später konfigurieren** aus, um das Azure-Netzwerk pro Computer auszuwählen. Wenn Sie über kein Netzwerk verfügen, müssen Sie ein [Netzwerk erstellen](#set-up-an-azure-network). Klicken Sie zum Erstellen eines Netzwerks mit dem Resource Manager auf **Neu erstellen**. Falls Sie ein Netzwerk mit dem klassischen Modell erstellen möchten, verwenden Sie hierfür das [Azure-Portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Wählen Sie, falls zutreffend, ein Subnetz aus. Klicken Sie dann auf **OK**.

    ![Replikation aktivieren](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. Klicken Sie auf **Virtuelle Computer** > **Virtuelle Computer auswählen**, und wählen Sie die Computer aus, die Sie replizieren möchten. Sie können nur Computer auswählen, für die die Replikation aktiviert werden kann. Klicken Sie dann auf **OK**.

    ![Replikation aktivieren](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. Geben Sie unter **Eigenschaften** > **Eigenschaften konfigurieren**das Konto aus, das der Prozessserver zum automatischen Installieren des Mobilitätsdiensts auf dem Computer verwenden soll.  
11. Standardmäßig werden alle Datenträger repliziert. Klicken Sie zum Ausschließen von Datenträgern aus der Replikation auf **Alle Datenträger**, und entfernen Sie alle Datenträger, die Sie nicht replizieren möchten.  Klicken Sie dann auf **OK**. Sie können später weitere Eigenschaften festlegen. [Weitere Informationen](site-recovery-exclude-disk.md) zum Ausschließen von Datenträgern.

    ![Replikation aktivieren](./media/site-recovery-vmware-to-azure/enable-replication6.png)

12. Überprüfen Sie unter **Replikationseinstellungen** > **Replikationseinstellungen konfigurieren**, ob die richtige Replikationsrichtlinie ausgewählt ist. Sie können die Replikationsrichtlinieneinstellungen unter **Einstellungen** > **Replikationsrichtlinien** > Richtlinienname > **Einstellungen bearbeiten** ändern. Die Änderungen, die Sie an einer Richtlinie vornehmen, werden auf die Replikation und auf neue Computer angewendet.
13. Aktivieren Sie **Multi-VM-Konsistenz** , wenn Sie Computer in einer Replikationsgruppe zusammenfassen möchten, und geben Sie einen Namen für die Gruppe an. Klicken Sie dann auf **OK**. Beachten Sie Folgendes:

    * Die Computer in einer Replikationsgruppe werden gemeinsam repliziert und verfügen beim Failover über gemeinsame ausfallsichere und app-konsistente Wiederherstellungspunkte.
    * Es wird empfohlen, dass Sie virtuelle Computer und physische Server zusammenfassen, damit sie Ihre Workloads widerspiegeln. Das Aktivieren von Multi-VM-Konsistenz kann sich auf die Leistung der Workload auswirken und sollte nur verwendet werden, wenn Computer die gleiche Workload ausführen und Sie Konsistenz benötigen.

    ![Replikation aktivieren](./media/site-recovery-vmware-to-azure/enable-replication7.png)
14. Klicken Sie auf **Replikation aktivieren**. Sie können den Fortschritt des Auftrags **Schutz aktivieren** unter **Einstellungen** > **Aufträge** > **Site Recovery-Aufträge** verfolgen. Nachdem der Auftrag **Schutz abschließen** ausgeführt wurde, ist der Computer bereit für das Failover.

> [!NOTE]
> Wenn der Computer für die Pushinstallation vorbereitet ist, wird die Mobilitätsdienstkomponente installiert, wenn der Schutz aktiviert wird. Nachdem die Komponente auf dem Computer installiert wurde, wird ein Schutzauftrag gestartet. Er führt zu einem Fehler. Nach dem Fehler müssen Sie jeden Computer manuell neu starten. Nach dem Neustart wird der Schutzauftrag erneut gestartet, und die erste Replikation wird ausgeführt.
>
>

## <a name="view-and-manage-vm-properties"></a>Anzeigen und Verwalten von VM-Eigenschaften

Es wird empfohlen, dass Sie die Eigenschaften des Quellcomputers überprüfen. Beachten Sie, dass der Name des virtuellen Azure-Computers die [Anforderungen für virtuelle Azure-Computer](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)erfüllen muss.

1. Klicken Sie auf **Einstellungen** > **Replizierte Elemente**, und wählen Sie den Computer aus. Die Seite **Zusammenfassung** enthält Informationen zu den Einstellungen und zum Status der Computer.
2. Unter **Eigenschaften** können Sie die Informationen zur Replikation und zum Failover für den virtuellen Computer anzeigen.
3. Unter **Compute und Netzwerk** > **Compute-Eigenschaften** können Sie den Namen und die Zielgröße des virtuellen Azure-Computers angeben. Ändern Sie ggf. den Namen, damit er die Azure-Anforderungen erfüllt.
    ![Replikation aktivieren](./media/site-recovery-vmware-to-azure/vmproperties.png)

4.  Sie können eine [Ressourcengruppe](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines) auswählen, der der Computer nach dem Failover angehört. Sie können diese Einstellung jederzeit vor dem Failover ändern. Wenn Sie den Computer nach dem Failover zu einer anderen Ressourcengruppe migrieren, funktionieren die Schutzeinstellungen des Computers nicht mehr.
5. Sie können eine [Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) auswählen, wenn der Computer nach dem Failover einer Verfügbarkeitsgruppe angehören muss. Beachten Sie bei der Auswahl einer Verfügbarkeitsgruppe folgende Punkte:

    * Es werden nur Verfügbarkeitsgruppen aufgeführt, die der angegebenen Ressourcengruppe angehören.  
    * Computer mit unterschiedlichen virtuellen Netzwerken dürfen nicht der gleichen Verfügbarkeitsgruppe angehören.
    * Einer Verfügbarkeitsgruppe dürfen nur virtuelle Computer mit der gleichen Größe angehören.
5. Sie können auch Informationen zum Zielnetzwerk, zum Subnetz sowie zu der IP-Adresse anzeigen und hinzufügen, die der Azure-VM zugewiesen wird.
6. Unter **Datenträger** werden das Betriebssystem und die Datenträger auf der VM angezeigt, die repliziert werden.

### <a name="network-adapters-and-ip-addressing"></a>Netzwerkadapter und IP-Adressierung

- Sie können die Ziel-IP-Adresse festlegen. Wenn Sie keine Adresse angeben, wird für den Computer, für den das Failover durchgeführt wurde, DHCP verwendet. Wenn Sie eine Adresse festlegen, die beim Failover nicht verfügbar ist, funktioniert das Failover nicht. Dieselbe Ziel-IP-Adresse kann für das Testfailover verwendet werden, wenn die Adresse im Testfailover-Netzwerk verfügbar ist.
- Die Anzahl der Netzwerkkarten hängt von der Größe ab, die Sie für den virtuellen Zielcomputer angeben. Hierbei gilt Folgendes:
    - Wenn die Anzahl der Netzwerkkarten des Quellcomputers maximal der Anzahl der Netzwerkkarten entspricht, die für die Größe des Zielcomputers zulässig ist, hat der Zielcomputer die gleiche Anzahl von Netzwerkkarten wie der Quellcomputer.
    - Wenn die Anzahl der Netzwerkadapter für den virtuellen Quellcomputer die maximal zulässige Anzahl für die Größe des Zielcomputers übersteigt, wird die Anzahl verwendet, die maximal für die Größe des Zielcomputers zulässig ist.
    - Ein Beispiel: Wenn ein Quellcomputer zwei Netzwerkkarten besitzt und der Zielcomputer aufgrund seiner Größe vier Netzwerkkarten unterstützt, erhält der Zielcomputer zwei Netzwerkkarten. Wenn der Quellcomputer dagegen zwei Netzwerkadapter besitzt und der Zielcomputer aufgrund seiner Größe nur einen Adapter unterstützt, erhält der Zielcomputer nur einen Adapter.
    - Wenn der virtuelle Computer über mehrere Netzwerkadapter verfügt, werden alle mit dem gleichen Netzwerk verbunden.
    - Wenn der virtuelle Computer über mehrere Netzwerkadapter verfügt, wird der erste, der in der Liste angezeigt wird, zum *Standard*-Netzwerkadapter im virtuellen Azure-Computer.

### <a name="azure-hybrid-use-benefit"></a>Azure-Vorteil bei Hybridnutzung

Microsoft Software Assurance-Kunden können dank des Azure-Hybridnutzungsvorteils Lizenzierungskosten für Windows Server-Computer sparen, die nach Azure migriert werden, oder Azure für die Notfallwiederherstellung nutzen. Wenn Sie für den Azure-Hybridnutzungsvorteil berechtigt sind, können Sie festlegen, dass dieser Vorteil auf den virtuellen Computer angewendet werden soll, der bei einem Failover von Azure Site Recovery in Azure erstellt wird. Gehen Sie dazu folgendermaßen vor:
- Navigieren Sie zum Abschnitt „Compute- und Netzwerkeigenschaften“ des replizierten virtuellen Computers.
- Beantworten Sie die Frage, ob Sie eine Windows Server-Lizenz besitzen, gemäß derer Sie für den Azure-Hybridnutzungsvorteil berechtigt sind.
- Aktivieren Sie für den Computer, der bei einem Failover erstellt wird, das Kontrollkästchen „Ich bestätige, dass ich eine berechtigte Windows Server-Lizenz mit Software Assurance für die Anwendung dieses Hybridnutzungsvorteils besitze.“.
- Speichern Sie die Einstellungen für den replizierten Computer.

Weitere Informationen finden Sie auf der Seite zum [Azure-Hybridnutzungsvorteil](https://aka.ms/azure-hybrid-use-benefit-pricing).

## <a name="common-issues"></a>Häufige Probleme

* Datenträger dürfen nicht größer sein als 1 TB.
* Bei dem Betriebssystem-Datenträger sollte es sich um einen Basisdatenträger anstelle eines dynamischen Datenträgers handeln.
* Für Computer der zweiten Generation/virtuelle UEFI-Computer muss es sich bei der Betriebssystemfamilie um Windows handeln und der Startdatenträger muss kleiner als 300 GB sein.

## <a name="next-steps"></a>Nächste Schritte

Sobald der Schutz abgeschlossen ist und der Computer den Zustand „Geschützt“ aufweist, können Sie ein [Failover](site-recovery-failover.md) durchführen, um zu überprüfen, ob Ihre Anwendung in Azure verfügbar gemacht wird.

Wenn Sie den Schutz deaktivieren möchten, informieren Sie sich über das [Bereinigen von Registrierungs- und Schutzeinstellungen](site-recovery-manage-registration-and-protection.md).
