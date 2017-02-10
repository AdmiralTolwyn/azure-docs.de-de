---
title: "Notfallwiederherstellung und Gerätefailover für das StorSimple Virtual Array"
description: "Erfahren Sie, wie Sie das Failover für das StorSimple Virtual Array durchführen."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 94a8c3db-8e47-4e9a-917a-29b14a9263aa
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 67cdf4e3d8e81d2ea419603918a3204dc6860ae7


---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array"></a>Notfallwiederherstellung und Gerätefailover für das StorSimple Virtual Array
## <a name="overview"></a>Übersicht
In diesem Artikel wird die Notfallwiederherstellung für das Microsoft Azure StorSimple Virtual Array (auch als „lokales virtuelles StorSimple-Gerät“ bezeichnet) beschrieben, einschließlich der ausführlichen erforderlichen Schritte für das Failover auf ein anderes virtuelles Gerät bei einem Notfall. Ein Failover ermöglicht das Migrieren Ihrer Daten von einem *Quellgerät* im Rechenzentrum auf ein anderes *Zielgerät* an demselben oder einem anderen geografischen Standort. Das Gerätefailover gilt für das gesamte Gerät. Während des Failovers gehen die Clouddaten für das Quellgerät in den Besitz des Zielgeräts über.

Ein Gerätefailover wird über das Feature der Notfallwiederherstellung koordiniert und auf der Seite **Geräte** initiiert. Auf dieser Seite werden alle StorSimple-Geräte aufgeführt, die mit dem StorSimple-Manager-Dienst verbunden sind. Für jedes Gerät werden Anzeigename, Status, bereitgestellte und maximale Kapazität, Typ und Modell angezeigt.

![](./media/storsimple-ova-failover-dr/image15.png)

Dieser Artikel gilt nur für StorSimple Virtual Arrays. Informationen zum Failover für ein Gerät der 8000er Serie finden Sie unter [Failover und Notfallwiederherstellung für das StorSimple-Gerät](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery"></a>Was ist die Notfallwiederherstellung?
Bei einer Notfallwiederherstellung funktioniert das primäre Gerät nicht mehr. In diesem Fall können Sie die dem ausgefallenen Gerät zugeordneten Clouddaten auf ein anderes Gerät verschieben und dabei das primäre Gerät als *Quelle* und ein anderes Gerät als *Ziel* angeben. Dieser Vorgang wird als *Failover*bezeichnet. Während des Failovers wechseln alle Volumes bzw. Freigaben den Besitzer und werden auf das Zielgerät übertragen. Eine Filterung der Daten ist nicht zulässig.

Die Notfallwiederherstellung ist als Wiederherstellung des gesamten Geräts ausgelegt, und es wird die auf Heat Maps basierende Staffelung und Nachverfolgung verwendet. Eine Heat Map wird definiert, indem den Daten anhand von Lese- und Schreibmustern ein Heat-Wert zugewiesen wird. Mit dieser Heat Map werden die Datenelemente mit den niedrigsten Heat-Werten zuerst in der Cloud angeordnet, und die Datenelemente mit den hohen Heat-Werten (am häufigsten verwendet) verbleiben auf der lokalen Ebene. Bei der Notfallwiederherstellung wird die Heat Map verwendet, um die Daten aus der Cloud wiederherzustellen und zu aktivieren. Mit dem Gerät werden alle Volumes/Freigaben der letzten Sicherung (intern ermittelt) abgerufen, und anhand dieser Sicherungsdaten wird eine Wiederherstellung durchgeführt. Der gesamte Prozess der Notfallwiederherstellung wird vom Gerät orchestriert.

## <a name="prerequisites-for-device-failover"></a>Voraussetzungen für das Gerätefailover
### <a name="prerequisites"></a>Voraussetzungen
Bei jedem Gerätefailover sollten die folgenden Voraussetzungen erfüllt sein:

* Das Quellgerät muss sich in einem **deaktivierten** Zustand befinden.
* Das Zielgerät muss im klassischen Azure-Portal als **Aktiv** angezeigt werden. Sie müssen ein virtuelles Zielgerät mit der gleichen oder einer höheren Kapazität bereitstellen. Anschließend sollten Sie die lokale Webbenutzeroberfläche verwenden, um das virtuelle Gerät zu konfigurieren und zu registrieren.
  
  > [!IMPORTANT]
  > Versuchen Sie nicht, das registrierte virtuelle Gerät über den Dienst zu konfigurieren, indem Sie auf **Geräteinstallation abschließen**klicken. Über den Dienst sollte keine Gerätekonfiguration durchgeführt werden.
  > 
  > 
* Quell- und Zielgerät müssen vom gleichen Typ sein. Sie können nur für ein virtuelles Gerät, das als Dateiserver konfiguriert ist, ein Failover auf einen anderen Dateiserver durchführen. Dies gilt auch für einen iSCSI-Server.
* Für die Notfallwiederherstellung eines Dateiservers empfehlen wir Ihnen, dass Sie für das Zielgerät den Beitritt zu derselben Domäne wie der Domäne der Quelle durchführen, damit die Freigabeberechtigungen automatisch aufgelöst werden. In dieser Version wird nur das Failover auf ein Zielgerät in derselben Domäne unterstützt.

### <a name="other-considerations"></a>Weitere Überlegungen
* Es ist ratsam, alle Volumes oder Freigaben auf dem Quellgerät in den Offlinezustand zu versetzen.
* Wenn es sich um ein geplantes Failover handelt, wird empfohlen, eine Sicherung des Geräts zu erstellen und dann mit dem Failover zu starten, um Datenverluste zu minimieren. Wenn es sich um ein nicht geplantes Failover handelt, wird die letzte Sicherung zum Wiederherstellen des Geräts verwendet.
* Die verfügbaren Zielgeräte für die Notfallwiederherstellung sind Geräte, die im Vergleich zum Quellgerät die gleiche oder eine höhere Kapazität aufweisen. Geräte, die mit dem Dienst verbunden sind, aber das Speicherplatzkriterium nicht erfüllen, stehen als Zielgeräte nicht zur Verfügung.

### <a name="dr-prechecks"></a>Vorüberprüfungen für die Notfallwiederherstellung
Vor Beginn der Notfallwiederherstellung werden auf dem Gerät Vorüberprüfungen durchgeführt. Mit diesen Überprüfungen können Sie sicherstellen, dass keine Fehler auftreten, wenn die Notfallwiederherstellung beginnt. Die Vorüberprüfungen umfassen Folgendes:

* Überprüfen des Speicherkontos
* Überprüfen der Cloudverbindung mit Azure
* Überprüfen des verfügbaren Speicherplatzes auf dem Zielgerät
* Überprüfen, ob ein iSCSI-Server-Quellgerät über gültige ACR-Namen, IQN (nicht länger als 220 Zeichen) und ein CHAP-Kennwort (12 bis 16 Zeichen) für die Volumes verfügt

Falls eine der obigen Vorüberprüfungen fehlschlägt, kann die Notfallwiederherstellung nicht durchgeführt werden. Sie müssen diese Fehler beheben und den Vorgang dann wiederholen.

Nachdem die Notfallwiederherstellung erfolgreich abgeschlossen wurde, gehen die Clouddaten auf dem Quellgerät in den Besitz des Zielgeräts über. Das Quellgerät ist dann nicht mehr im Portal verfügbar. Der Zugriff auf alle Volumes/Freigaben auf dem Quellgerät ist blockiert, und das Zielgerät wird in den aktiven Zustand versetzt.

> [!IMPORTANT]
> Auch wenn das Gerät nicht mehr verfügbar ist, werden von der virtuellen Maschine, die Sie auf dem Hostsystem bereitgestellt haben, noch Ressourcen verbraucht. Nachdem die Notfallwiederherstellung erfolgreich abgeschlossen wurde, können Sie den virtuellen Computer aus Ihrem Hostsystem löschen.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Durchführen eines Failovers auf ein virtuelles Array
Es wird empfohlen, ein weiteres virtuelles StorSimple-Array bereitzustellen, über die lokale Webbenutzeroberfläche zu konfigurieren und beim StorSimple Manager-Dienst zu registrieren, bevor Sie diesen Vorgang ausführen.

> [!IMPORTANT]
> * Das Failover eines StorSimple-Geräts der 8000er Serie auf ein virtuelles Gerät der 1200er Serie ist unzulässig.
> * Sie können für ein virtuelles Gerät, für das Federal Information Processing Standard (FIPS) aktiviert, und das im Government-Portal bereitgestellt ist, ein Failover an ein virtuelles Gerät im klassischen Azure-Portal durchführen. Umgekehrt trifft dies auch zu.
> 
> 

Führen Sie die folgenden Schritte aus, um Ihr Gerät auf einem virtuellen StorSimple-Zielgerät wiederherzustellen.

1. Versetzen Sie die Volumes/Freigaben auf dem Host in den Offlinezustand. Befolgen Sie die betriebssystemspezifischen Anweisungen auf dem Host, um Volumes bzw. Freigaben in den Offlinezustand zu versetzen. Falls sie nicht bereits offline sind, müssen Sie alle Volumes/Freigaben auf dem Gerät offline schalten, indem Sie auf** Geräte > Freigaben** (bzw. **Geräte > Volumes**) zugreifen. Wählen Sie eine Freigabe bzw. ein Volume aus, und klicken Sie unten auf der Seite auf **Offline schalten**. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**. Wiederholen Sie diesen Vorgang für alle Freigaben/Volumes auf dem Gerät.
2. Wählen Sie auf der Seite **Geräte** das Quellgerät für das Failover aus, und klicken Sie auf **Deaktivieren**. 
    ![](./media/storsimple-ova-failover-dr/image16.png)
3. Sie werden aufgefordert, diesen Schritt zu bestätigen. Die Gerätedeaktivierung ist ein dauerhafter Prozess, der nicht rückgängig gemacht werden kann. Sie erhalten auch eine Erinnerung, dass Sie die Freigaben/Volumes auf dem Host offline schalten sollen.
   
    ![](./media/storsimple-ova-failover-dr/image18.png)
4. Nach der Bestätigung wird die Deaktivierung gestartet. Nachdem die Deaktivierung erfolgreich abgeschlossen ist, werden Sie benachrichtigt.
   
    ![](./media/storsimple-ova-failover-dr/image19.png)
5. Auf der Seite **Geräte** ändert sich der Gerätezustand in **Deaktiviert**.
   
    ![](./media/storsimple-ova-failover-dr/image20.png)
6. Wählen Sie das deaktivierte Gerät aus, und klicken Sie unten auf der Seite auf **Failover**.
7. Führen Sie im angezeigten Assistenten „Failover bestätigen“ folgende Schritte aus:
   
   1. Wählen Sie in der Dropdownliste mit den verfügbaren Geräten ein **Zielgerät** aus. Nur die Geräte, die über ausreichende Kapazität verfügen, werden in der Dropdownliste angezeigt.
   2. Überprüfen Sie die Details des Quellgeräts, z. B. Gerätename, Gesamtkapazität und die Namen der Freigaben, die am Failover beteiligt sind.
      
       ![](./media/storsimple-ova-failover-dr/image21.png)
8. Versehen Sie **Ich nehme zur Kenntnis, dass ein Failover dauerhaft erfolgt, und dass das Quellgerät nach dem erfolgreichen Abschluss des Failovers gelöscht wird**mit einem Häkchen.
9. Klicken Sie auf das Häkchensymbol ![](./media/storsimple-ova-failover-dr/image1.png).
10. Ein Failoverauftrag wird initiiert, und Sie werden benachrichtigt. Klicken Sie auf **Auftrag anzeigen** , um das Failover zu überwachen.
    
     ![](./media/storsimple-ova-failover-dr/image22.png)
11. Auf der Seite **Aufträge** sehen Sie einen Failoverauftrag, der für das Quellgerät erstellt wurde. Mit diesem Auftrag werden die Vorüberprüfungen für die Notfallwiederherstellung durchgeführt.
    
    ![](./media/storsimple-ova-failover-dr/image23.png)
    
     Nachdem die Vorüberprüfungen für die Notfallwiederherstellung erfolgreich abgeschlossen wurden, erzeugt der Failoverauftrag Wiederherstellungsaufträge für jede Freigabe/jedes Volume, die bzw. das auf dem Quellgerät vorhanden ist.
    
    ![](./media/storsimple-ova-failover-dr/image24.png)
12. Wechseln Sie nach Abschluss des Failovers zur Seite **Geräte** .
    
    a. Wählen Sie das virtuelle StorSimple-Gerät aus, das Sie als Zielgerät für das Failover verwendet haben.
    
    b. Navigieren Sie zur Seite **Freigaben** (oder **Volumes** bei einem iSCSI-Server). Alle Freigaben (Volumes) des alten Geräts sollten nun aufgelistet werden.
    
    ![](./media/storsimple-ova-failover-dr/image25.png)

![](./media/storsimple-ova-failover-dr/video_icon.png) **Video verfügbar**

In diesem Video wird veranschaulicht, wie Sie für ein lokales virtuelles StorSimple-Gerät ein Failover auf ein anderes virtuelles Gerät durchführen.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/StorSimple-Virtual-Array-Disaster-Recovery/player]
> 
> 

## <a name="business-continuity-disaster-recovery-bcdr"></a>Business Continuity Disaster Recovery (BCDR)
Ein Business Continuity Disaster Recovery (BCDR)-Szenario liegt vor, wenn das gesamte Azure-Rechenzentrum nicht mehr funktioniert. Dies kann sich auf den StorSimple Manager-Dienst und die zugehörigen StorSimple-Geräte auswirken.

Wenn StorSimple-Geräte direkt vor einem Notfall registriert wurden, müssen diese StorSimple-Geräte unter Umständen gelöscht werden. Nach dem Notfall können Sie diese Geräte neu erstellen und konfigurieren.

## <a name="errors-during-dr"></a>Fehler bei der Notfallwiederherstellung
**Ausfall der Cloudverbindung während der Notfallwiederherstellung**

Falls die Cloudverbindung nach dem Starten der Notfallwiederherstellung und vor Abschluss der Wiederherstellung des Geräts unterbrochen wird, tritt für die Notfallwiederherstellung ein Fehler auf, und Sie werden benachrichtigt. Das Zielgerät, das für die Notfallwiederherstellung verwendet wurde, wird dann als *Nicht verwendbar* gekennzeichnet. Dieses Zielgerät kann dann nicht mehr für weitere Notfallwiederherstellungen verwendet werden.

**Keine kompatiblen Zielgeräte**

Wenn die verfügbaren Zielgeräte nicht über genügend Speicherplatz verfügen, wird ein Fehler mit dem Hinweis angezeigt, dass keine kompatiblen Zielgeräte vorhanden sind.

**Fehler bei Vorüberprüfungen**

Falls eine der Vorüberprüfungen nicht erfolgreich ist, werden Vorüberprüfungsfehler angezeigt.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr darüber, wie Sie das [StorSimple Virtual Array mit der lokalen Webbenutzeroberfläche verwalten](storsimple-ova-web-ui-admin.md).




<!--HONumber=Nov16_HO3-->


