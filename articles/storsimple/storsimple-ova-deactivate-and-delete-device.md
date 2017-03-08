---
title: "Deaktivieren und Löschen eines StorSimple Virtual Array | Microsoft Docs"
description: "Beschreibt, wie ein StorSimple-Gerät aus dem Dienst entfernt wird, indem es zunächst deaktiviert und anschließend gelöscht wird."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: bf5ddb32-da4b-446f-ab91-215e9020e1c8
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: d2008fa396405de99c527a84831a6544cdc0fc1d
ms.openlocfilehash: d62b484150a6786f0a6abbff1ca2c172e1ad43db
ms.lasthandoff: 02/28/2017


---
# <a name="deactivate-and-delete-a-storsimple-virtual-array-via-storsimple-manager"></a>Deaktivieren und Löschen eines StorSimple Virtual Array mit StorSimple Manager
## <a name="overview"></a>Übersicht
Wenn Sie ein StorSimple Virtual Array deaktivieren, trennen Sie die Verbindung zwischen dem Gerät und dem entsprechenden StorSimple Manager-Dienst. Die Deaktivierung ist ein ENDGÜLTIGER Vorgang und kann nicht rückgängig gemacht werden. Ein deaktiviertes Gerät kann nicht wieder für den StorSimple Manager-Dienst registriert werden.

In den folgenden Fällen kann es vorkommen, dass Sie ein virtuelles StorSimple-Gerät deaktivieren und löschen müssen:

* Ihr Gerät ist online, und Sie planen das Failover auf dieses Gerät. Dies kann erforderlich sein, wenn Sie ein Upgrade auf ein größeres Gerät planen. Nach dem Übertragen der Gerätedaten und Abschluss des Failovers können Sie das Gerät löschen.
* Ihr Gerät ist offline, und Sie planen das Failover auf dieses Gerät. Dies kann in einem Notfall passieren, bei dem das primäre Gerät aufgrund eines Ausfalls im Rechenzentrum nicht betriebsbereit ist. Sie planen, das Failover über das Gerät auf ein sekundäres Gerät durchzuführen. Nach dem Übertragen der Gerätedaten und Abschluss des Failovers können Sie das Gerät löschen.
* Sie möchten das Gerät außer Betrieb nehmen und dann löschen. 

Wenn Sie ein Gerät deaktivieren, sind alle Daten, die lokal gespeichert wurden, nicht mehr zugänglich. Nur die in der Cloud gespeicherten Daten können wiederhergestellt werden. Wenn Sie planen, die Gerätedaten nach der Deaktivierung aufzubewahren, sollten Sie eine Cloudmomentaufnahme aller Daten erstellen, bevor Sie ein Gerät deaktivieren. Dadurch können Sie alle Daten zu einem späteren Zeitpunkt wiederherstellen.

In diesem Tutorial werden folgende Punkte erläutert:

* Deaktivieren eines Geräts 
* Löschen eines deaktivierten Geräts

## <a name="deactivate-a-device"></a>Deaktivieren eines Geräts
Führen Sie dazu die folgenden Schritte aus, um Ihr Gerät zu deaktivieren:

#### <a name="to-deactivate-the-device"></a>So deaktivieren Sie das Gerät
1. Wechseln Sie auf die Seite **Geräte** . Wählen Sie das Gerät aus, das Sie deaktivieren möchten.
   
    ![Zu deaktivierendes Gerät auswählen](./media/storsimple-ova-deactivate-and-delete-device/deactivate1m.png)
2. Klicken Sie unten auf der Seite auf **Deaktivieren**.
   
    ![Auf „Deaktivieren“ klicken](./media/storsimple-ova-deactivate-and-delete-device/deactivate2m.png)
3. Eine Bestätigungsmeldung wird angezeigt. Klicken Sie auf **Ja** , um fortzufahren. 
   
    ![Deaktivierung bestätigen](./media/storsimple-ova-deactivate-and-delete-device/deactivate3m.png)
   
    Der Prozess zum Deaktivieren beginnt und dauert einige Minuten.
   
    ![Deaktivierung wird durchgeführt](./media/storsimple-ova-deactivate-and-delete-device/deactivate4m.png)
4. Nach der Deaktivierung wird die Liste mit den Geräten aktualisiert. 
   
    ![Vollständig deaktivieren](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)
   
    Sie können das Gerät jetzt löschen. 

## <a name="delete-the-device"></a>Löschen des Geräts
Ein Gerät muss zuerst deaktiviert werden, um es löschen zu können. Das Löschen eines Geräts entfernt es aus der Liste der mit dem Dienst verbundenen Geräte. Der Dienst kann das gelöschte Gerät dann nicht mehr länger verwalten. Die Daten, die dem Gerät zugeordnet sind, verbleiben aber in der Cloud. Denken Sie daran, dass für diese Daten dann Gebühren anfallen. 

Führen Sie die folgenden Schritte aus, um das Gerät zu löschen:

#### <a name="to-delete-the-device"></a>So löschen Sie das Gerät
1. Wählen Sie auf der Seite **Geräte** des StorSimple Manager-Diensts ein deaktiviertes Gerät, das Sie löschen möchten.
   
   ![Zu löschendes Gerät auswählen](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)
2. Klicken Sie unten auf der Seite auf **Löschen**.
   
   ![Auf „Löschen“ klicken](./media/storsimple-ova-deactivate-and-delete-device/deactivate6m.png)
3. Sie werden aufgefordert, diesen Schritt zu bestätigen. Geben Sie den Gerätenamen ein, um das Löschen des Geräts zu bestätigen. Beachten Sie, dass beim Löschen des Geräts nicht die Clouddaten gelöscht werden, die dem Gerät zugeordnet sind. Klicken Sie auf das Häkchen, um den Vorgang fortzusetzen.
   
   ![Bestätigen des Löschens](./media/storsimple-ova-deactivate-and-delete-device/deactivate7m.png) 
4. Es dauert einige Minuten, bis das Gerät gelöscht wird. 
   
   ![Löschvorgang wird durchgeführt](./media/storsimple-ova-deactivate-and-delete-device/deactivate8m.png)
   
    Nachdem das Gerät gelöscht wurde, wird die Liste mit den Geräten aktualisiert.
   
   ![Löschvorgang abgeschlossen](./media/storsimple-ova-deactivate-and-delete-device/deactivate9m.png)

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Verwenden des StorSimple Manager-Diensts finden Sie unter [Verwalten Ihres StorSimple Virtual Array mithilfe des StorSimple Manager-Diensts](storsimple-ova-manager-service-administration.md). 


