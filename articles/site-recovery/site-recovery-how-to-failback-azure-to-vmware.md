---
title: "Durchführen eines Failbacks von Azure zu VMware | Microsoft-Dokumentation"
description: "Nach einem Failover von virtuellen Computern auf Azure können Sie die virtuellen Computer mittels Failback wieder in die lokale Umgebung übertragen. Erfahren Sie, welche Schritte für ein Failback ausgeführt werden."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2017
ms.author: ruturajd
ms.openlocfilehash: ad424818f41e6b48e754dd0d39771248a1cd04fb
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/28/2017
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Failback von Azure zu einem lokalen Standort

In diesem Artikel wird beschrieben, wie Sie für virtuelle Azure-Computer ein Failback von Azure Virtual Machines auf den lokalen Standort durchführen. Befolgen Sie die Anweisungen in diesem Artikel, um Ihre virtuellen VMware-Computer oder Ihre physischen Windows-/Linux-Server nach einem Failover vom lokalen Standort auf Azure (beschrieben im Tutorial [Replizieren von virtuellen VMware-Computern und physischen Servern in Azure mithilfe von Azure Site Recovery](site-recovery-vmware-to-azure-classic.md)) per Failback wieder auf den lokalen Standort zurückzuführen.

> [!WARNING]
> Wenn Sie entweder die [Migration abgeschlossen](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), den virtuellen Computer in eine andere Ressourcengruppe verschoben oder den virtuellen Azure-Computer gelöscht haben, ist danach kein Failback möglich. Wenn Sie den Schutz des virtuellen Computers deaktivieren, ist kein Failback möglich.

> [!NOTE]
> Wenn für VMware-VMs ein Failover erfolgt ist, ist kein Failback auf einen Hyper-V-Host möglich.

## <a name="overview-of-failback"></a>Übersicht über das Failback
Das Failback funktioniert wie folgt. Nach einem Failover zu Azure erfolgt das Failback zu Ihrem lokalen Standort in mehreren Phasen:

1. [Schützen Sie die virtuellen Computer in Azure erneut](site-recovery-how-to-reprotect.md), damit sie mit der Replikation auf virtuelle VMware-Computer an Ihrem lokalen Standort beginnen. Im Rahmen dieses Vorgangs müssen Sie auch folgende Schritte ausführen:
    1. Einrichten eines lokalen Masterziels: Windows-Masterziel für virtuelle Windows-Computer und [Linux-Masterziel](site-recovery-how-to-install-linux-master-target.md) für virtuelle Linux-Computer
    2. Einrichten eines [Prozessservers](site-recovery-vmware-setup-azure-ps-resource-manager.md)
    3. Initiieren des [erneuten Schützens](site-recovery-how-to-reprotect.md) Dabei wird der lokale virtuelle Computer deaktiviert, und die Azure-VM-Daten werden mit den lokalen Datenträgern synchronisiert.
5. Initiieren Sie nach der Replikation Ihrer virtuellen Azure-Computer am lokalen Standort ein Failover von Azure zum lokalen Standort.

Nachdem Ihre Daten per Failback zurückgeführt wurden, schützen Sie die lokalen virtuellen Computer, auf die Sie das Failback durchgeführt haben, erneut, damit diese mit der Replikation nach Azure beginnen.

Sehen Sie sich das folgende Video zum Failover von Azure zu einem lokalen Standort an, um eine kurze Übersicht zu erhalten.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

### <a name="fail-back-to-the-original-or-alternate-location"></a>Failback zum ursprünglichen oder zu einem anderen Standort

Wenn Sie ein Failover für einen virtuellen VMware-Computer durchgeführt haben, können Sie das Failback zum gleichen virtuellen lokalen Quellcomputer ausführen, sofern er lokal noch vorhanden ist. In diesem Szenario werden nur die Änderungen zurückrepliziert. Dieses Szenario wird als Wiederherstellung am ursprünglichen Speicherort bezeichnet. Wenn der lokale virtuelle Computer nicht vorhanden ist, handelt es sich um ein Szenario mit Wiederherstellung an einem alternativen Speicherort.

> [!NOTE]
> Ein Failback kann nur zum ursprünglichen vCenter- und Konfigurationsserver erfolgen. Sie können keinen neuen Konfigurationsserver bereitstellen, um mit seiner Hilfe ein Failback auszuführen. Darüber hinaus können Sie dem bestehenden Konfigurationsserver kein neues vCenter hinzufügen, um ein Failback in das neue vCenter auszuführen.

#### <a name="original-location-recovery"></a>Wiederherstellung am ursprünglichen Speicherort

Wenn Sie ein Failback zum ursprünglichen virtuellen Computer durchführen, sind folgende Bedingungen erforderlich:
* Wenn der virtuelle Computer von einem vCenter-Server verwaltet wird, muss der ESX-Host des Masterziels Zugriff auf den Datenspeicher des virtuellen Computers haben.
* Wenn sich der virtuelle Computer auf einem ESX-Host befindet, jedoch nicht von vCenter verwaltet wird, muss sich die Festplatte des virtuellen Computers in einen Datenspeicher befinden, auf den der Host des Masterziels zugreifen kann.
* Wenn sich Ihr virtueller Computer auf einem ESX-Host befindet und nicht vCenter verwendet, müssen Sie für den ESX-Host des Masterziels eine Ermittlung ausführen, bevor Sie den erneuten Schutz ausführen. Dasselbe gilt auch bei einem Failback für physische Server.
* Sie können ein Failback zu einem virtuellen Storage Area Network (vSAN) oder auf einen Datenträger, durchführen, der auf einem direktem Geräteverweis (RDM) basiert, wenn die Datenträger bereits vorhanden und mit dem lokalen virtuellen Computer verbunden sind.

#### <a name="alternate-location-recovery"></a>Wiederherstellung an einem alternativen Speicherort
Wenn der lokale virtuelle Computer vor dem erneuten Schützen des virtuellen Computers nicht vorhanden ist, handelt es sich um ein Szenario mit Wiederherstellung an einem alternativen Speicherort. Mit dem Workflow zum erneuten Schützen wird der lokale virtuelle Computer neu erstellt. Außerdem wird ein vollständiger Download der Daten durchgeführt.

* Wenn Sie ein Failback an einem alternativen Speicherort des virtuellen Computers ausführen, erfolgt die Wiederherstellung auf demselben ESX-Host, auf dem der Masterzielserver bereitgestellt ist. Der zum Erstellen des Datenträgers verwendete Datenspeicher ist derselbe Datenspeicher, der beim erneuten Schützen des virtuellen Computers ausgewählt wurde.
* Sie können nur ein Failback nur auf einen VMFS- (Dateisystem für virtuelle Computer) oder vSAN-Datenspeicher ausführen. Bei RDM funktionieren das erneute Schützen und das Failback nicht.
* Das erneute Schützen umfasst eine große anfängliche Datenübertragung gefolgt von den Änderungen. Dieser Prozess ist vorhanden, da der virtuelle Computer lokal nicht vorhanden ist. Die vollständigen Daten müssen zurückrepliziert werden. Dieser erneute Schutz dauert außerdem länger als die Wiederherstellung am ursprünglichen Speicherort.
* Ein Failback auf RDM-basierte Datenträger ist nicht möglich. Nur neue virtuelle Datenträger (VMDKs) können in einem VMFS-/vSAN-Datenspeicher erstellt werden.

Für einen physischen Computer kann beim Failover auf Azure nur ein Failback als virtueller VMware-Computer (auch als P2A2V bezeichnet) ausgeführt werden. Dieser Flow fällt unter Wiederherstellung an einem alternativen Speicherort.

* Für einen geschützten physischen Server unter Windows Server 2008 R2 SP1, für den ein Failover auf Azure ausgeführt wurde, kann kein Failback ausgeführt werden.
* Stellen Sie sicher, dass Sie mindestens einen Masterzielserver und die erforderlichen ESX/ESXi-Hosts ermitteln, auf die Sie ein Failback ausführen müssen.

## <a name="have-you-completed-reprotection"></a>Haben Sie das erneute Schützen abgeschlossen?
Führen Sie vor dem Fortfahren alle Schritte für das erneute Schützen aus, damit sich die virtuellen Computer in einem replizierten Zustand befinden und Sie ein Failover zurück zu einem lokalen Standort initiieren können. Weitere Informationen finden Sie unter [Erneutes Schützen von Azure zu lokal](site-recovery-how-to-reprotect.md).

## <a name="prerequisites"></a>Voraussetzungen

> [!IMPORTANT]
> Beim Failover zu Azure ist der lokale Standort möglicherweise nicht verfügbar und daher kann der Konfigurationsserver nicht verfügbar oder heruntergefahren sein. Während des erneuten Schützens und des Failbacks sollte der lokale Konfigurationsserver ausgeführt werden und einen fehlerfreien verbundenen Zustand aufweisen.

* Ein Konfigurationsserver ist lokal erforderlich, wenn Sie ein Failback durchführen. Der Server muss aktiv und mit dem Dienst verbunden sein, damit eine ordnungsgemäße Integrität vorliegt. Während des Failbacks muss der virtuelle Computer in der Konfigurationsserverdatenbank vorhanden sein, ansonsten schlägt das Failback fehl. Stellen Sie daher sicher, dass Sie die regelmäßigen geplanten Sicherungen des Servers ausführen. Im Notfall müssen Sie den Server mit der gleichen IP-Adresse wiederherstellen, damit das Failback funktioniert.
* Der Masterzielserver sollte vor dem Auslösen des erneuten Schützens/Failbacks keine Momentaufnahmen enthalten.

## <a name="steps-to-fail-back"></a>Schritte für das Failback

> [!IMPORTANT]
> Stellen Sie vor dem Initiieren des Failbacks sicher, dass der erneute Schutz für die virtuellen Computer abgeschlossen ist. Die virtuellen Computer müssen geschützt sein und den Integritätsstatus **OK** aufweisen. Informationen zum erneuten Schutz der virtuellen Computer finden Sie unter [Erneuter Schutz](site-recovery-how-to-reprotect.md).

1. Wählen Sie auf der Seite mit den replizierten Elementen den virtuellen Computer aus, und klicken Sie mit der rechten Maustaste darauf, um **Ungeplantes Failover** auszuwählen.
2. Überprüfen Sie unter **Failover bestätigen** die Failoverrichtung (von Azure), und wählen Sie dann den Wiederherstellungspunkt aus (den neuesten oder den neuesten App-konsistenten Wiederherstellungspunkt), der für das Failover verwendet werden soll. Der App-konsistente Punkt liegt vor dem aktuellen Zeitpunkt und verursacht einige Datenverluste.
3. Während des Failovers fährt Site Recovery die virtuellen Computer auf Azure herunter. Vergewissern Sie sich, dass das Failback wie erwartet abgeschlossen wurde, und überprüfen Sie anschließend, ob die virtuellen Computer auf Azure heruntergefahren wurden.

### <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Auf welchen Wiederherstellungspunkt kann ich für die virtuellen Computer ein Failback durchführen?

Während des Failbacks haben Sie für das Failback der virtuellen Computer bzw. des Wiederherstellungsplans zwei Möglichkeiten.

Wenn Sie den letzten Verarbeitungspunkt auswählen, wird für alle virtuellen Computer ein Failover zum letzten verfügbaren Zeitpunkt durchgeführt. Falls im Wiederherstellungsplan eine Replikationsgruppe enthalten ist, wird für jeden virtuellen Computer der Replikationsgruppe ein Failover zu ihrem unabhängigen letzten Zeitpunkt durchgeführt.

Sie können das Failback eines virtuellen Computers erst ausführen, wenn er mindestens einen Wiederherstellungspunkt aufweist. Ein Failback für einen Wiederherstellungsplan ist erst möglich, wenn alle beteiligten virtuellen Computer mindestens einen Wiederherstellungspunkt aufweisen.

> [!NOTE]
> Bei einem letzten Wiederherstellungspunkt handelt es sich um einen absturzkonsistenten Wiederherstellungspunkt.

Wenn Sie den anwendungskonsistenten Wiederherstellungspunkt auswählen, wird bei einem Failback für einen einzelnen virtuellen Computer die Wiederherstellung des letzten verfügbaren anwendungskonsistenten Wiederherstellungspunkts durchgeführt. Bei einem Wiederherstellungsplan mit einer Replikationsgruppe erfolgt für jede Replikationsgruppe die Wiederherstellung für den gemeinsamen verfügbaren Wiederherstellungspunkt.
Hinweis: Anwendungskonsistente Wiederherstellungspunkte können zeitlich weiter zurück liegen, und es kann zu einem Verlust von Daten kommen.

### <a name="what-happens-to-vmware-tools-post-failback"></a>Was geschieht mit VMware-Tools nach dem Failback?

Während des Failovers auf Azure können die VMware-Tools nicht auf dem virtuellen Azure-Computer ausgeführt werden. Bei einem virtuellen Windows-Computer deaktiviert ASR die VMware-Tools während des Failovers. Bei einem virtuellen Linux-Computer deinstalliert ASR die VMware-Tools während des Failovers.

Im Zuge des Failbacks eines virtuellen Windows-Computers werden die VMware-Tools wieder aktiviert. Analog dazu werden die VMware-Tools beim Failback eines virtuellen Linux-Computers wieder auf dem Computer installiert.

## <a name="next-steps"></a>Nächste Schritte

Nachdem das Failback abgeschlossen ist, müssen Sie den virtuellen Computer committen, um sicherzustellen, dass die in Azure wiederhergestellten virtuellen Computer gelöscht werden.

### <a name="commit"></a>Commit
Ein Commit ist erforderlich, um den virtuellen Computer, für den das Failover durchgeführt wurde, aus Azure zu entfernen.
Klicken Sie mit der rechten Maustaste auf das geschützte Element, und klicken Sie dann auf **Commit**. Ein Auftrag entfernt die virtuellen Computer, für die in Azure ein Failover ausgeführt wurde.

### <a name="reprotect-from-on-premises-to-azure"></a>Erneutes Schützen von einem lokalen Standort nach Azure

Nach Abschluss des Commits befindet sich Ihr virtueller Computer wieder am lokalen Standort, er ist jedoch nicht geschützt. Führen Sie daher folgende Schritte aus, um die Replikation zu Azure erneut zu starten:

1. Wählen Sie unter **Tresor** > **Einstellung** > **Replizierte Elemente** die virtuellen Computer aus, für die das Failback durchgeführt wurde, und klicken Sie dann auf **Erneut schützen**.
2. Geben Sie den Wert des Prozessservers an, der zum Zurücksenden von Daten an Azure verwendet werden muss.
3. Klicken Sie auf **OK**, um den Auftrag zum erneuten Schützen zu starten.

> [!NOTE]
> Nachdem ein lokaler virtueller Computer gestartet wurde, dauert es eine Weile, bis der Agent auf dem Konfigurationsserver registriert wird (bis zu 15 Minuten). Während dieses Zeitraums schlägt das erneute Schützen fehl, und eine zurückgegebene Fehlermeldung gibt an, dass der Agent nicht installiert ist. Warten Sie einige Minuten, und versuchen Sie dann noch einmal, das erneute Schützen durchzuführen.

Nachdem der Auftrag zum erneuten Schützen abgeschlossen ist, wird der virtuelle Computer zurück nach Azure repliziert, und Sie können ein Failover ausführen.

## <a name="common-issues"></a>Häufige Probleme
Stellen Sie sicher, dass die vCenter-Instanz verbunden ist, bevor Sie ein Failback ausführen. Andernfalls schlägt das Trennen und erneute Anfügen von Datenträgern an den virtuellen Computer fehl.

### <a name="common-error-codes"></a>Allgemeine Fehlercodes

#### <a name="error-code-8038"></a>Fehlercode 8038

*Fehler beim Aktivieren des lokalen virtuellen Computers aufgrund des Fehlers*

Ursache 
1. Der lokale virtuelle Computer wird auf einem Host aktiviert, auf dem nicht genügend Speicher verfügbar ist.

Lösung
1. Sie können mehr Speicher auf dem ESXi-Host bereitstellen.
2. Migrieren Sie den virtuellen Computer mithilfe von vMotion zu einem anderen ESXi-Host, auf dem ausreichend Speicher zum Starten des virtuellen Computers zur Verfügung steht.

