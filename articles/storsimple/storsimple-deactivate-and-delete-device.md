---
title: "Deaktivieren und Löschen eines StorSimple-Geräts | Microsoft Docs"
description: "Beschreibt, wie ein StorSimple-Gerät aus dem Dienst entfernt wird, indem es zunächst deaktiviert und anschließend gelöscht wird."
services: storsimple
documentationcenter: 
author: SharS
manager: timlt
editor: 
ms.assetid: 155cda38-c5ae-45dc-b7e8-6444494afc9e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: anbacker
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f5778d54060c9be1b0c90c34bcf7c8e9bacb414d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2017
---
# <a name="deactivate-and-delete-a-storsimple-8000-series-device-via-storsimple-manager-service"></a>Deaktivieren und Löschen von Geräten der StorSimple 8000-Serie über den StorSimple Manager-Dienst
> [!NOTE]
> Das klassische Portal für StorSimple ist veraltet. Ihre StorSimple-Geräte-Manager werden gemäß dem Zeitplan für die Abschaltung automatisch in das neue Azure-Portal verschoben. Sie erhalten zu dieser Verschiebung eine E-Mail und eine Portalbenachrichtigung. Dieses Dokument wird ebenfalls bald entfernt. Den Artikel zum neuen Azure-Portal finden Sie unter [Deaktivieren und Löschen eines StorSimple-Geräts](storsimple-8000-deactivate-and-delete-device.md). Antworten auf Fragen zu dieser Verschiebung finden Sie unter [FAQ: Move to Azure portal (Verschieben in das Azure-Portal: häufig gestellte Fragen (FAQ))](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Übersicht
Möglicherweise möchten Sie ein StorSimple-Gerät außer Betrieb nehmen (z. B. weil Sie es ersetzen oder aufrüsten möchten oder weil Sie StorSimple nicht mehr verwenden). Wenn dies der Fall ist, müssen Sie das Gerät deaktivieren, bevor Sie es löschen können. Das Deaktivieren trennt die Verbindung zwischen dem Gerät und dem entsprechenden StorSimple Manager-Dienst. In diesem Tutorial wird erläutert, wie ein StorSimple-Gerät aus dem Dienst entfernt wird, indem es zuerst deaktiviert und anschließend gelöscht wird. 

Wenn Sie ein Gerät deaktivieren, sind alle Daten, die lokal auf dem Gerät gespeichert wurden, nicht mehr zugänglich. Nur Daten auf dem Gerät, die in der Cloud gespeichert wurden, können wiederhergestellt werden.  

> [!WARNING]
> Die Deaktivierung ist ein ENDGÜLTIGER Vorgang und kann nicht rückgängig gemacht werden. Ein deaktiviertes Gerät kann erst beim StorSimple Manager-Dienst registriert werden, wenn es auf die Standardwerkseinstellungen zurückgesetzt wurde. 
> 
> Das Zurücksetzen der Werkseinstellungen löscht alle Daten, die lokal auf Ihrem Gerät gespeichert wurden. Daher ist es wichtig, dass Sie von allen Daten eine Momentaufnahme in der Cloud machen, bevor Sie ein Gerät deaktivieren. Dadurch können Sie alle Daten zu einem späteren Zeitpunkt wiederherstellen.
> 
> 

In diesem Tutorial werden folgende Punkte erläutert:

* Deaktivieren eines Geräts und Löschen der Daten
* Deaktivieren eines Geräts und Beibehalten der Daten

Außerdem wird erläutert, wie die Deaktivierung und das Löschen bei einem virtuellen StorSimple-Gerät funktioniert.

> [!NOTE]
> Vor dem Deaktivieren eines physischen oder virtuellen StorSimple-Geräts müssen alle von diesem Gerät abhängigen Clients und Hosts beendet oder entfernt werden.
> 
> 

## <a name="deactivate-and-delete-data"></a>Deaktivieren und Löschen von Daten
Wenn Sie das Gerät vollständig löschen und die Daten auf dem Gerät nicht beibehalten möchten, führen Sie die folgenden Schritte aus:

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>So deaktivieren Sie ein Gerät und löschen die Daten
1. Vor dem Deaktivieren eines Geräts müssen Sie alle Volumecontainer (und die Volumes) löschen, die dem Gerät zugeordnet sind. Sie können Volumecontainer nur löschen, nachdem Sie die zugeordneten Sicherungen gelöscht haben.
2. Deaktivieren Sie das Gerät wie folgt:
   
   1. Wählen Sie auf der Seite **Geräte** des StorSimple Manager-Diensts das Gerät aus, das Sie deaktivieren möchten, und klicken Sie unten auf der Seite auf **Deaktivieren**.
   2. Eine Bestätigungsmeldung wird angezeigt. Klicken Sie auf **Ja** , um fortzufahren. Der Prozess zum Deaktivieren beginnt und dauert einige Minuten.
3. Sie können nach der Deaktivierung das Gerät vollständig löschen. Das Löschen eines Geräts entfernt es aus der Liste der mit dem Dienst verbundenen Geräte. Der Dienst kann das gelöschte Gerät dann nicht mehr länger verwalten. Führen Sie die folgenden Schritte aus, um das Gerät zu löschen:
   
   1. Wählen Sie auf der Seite **Geräte** des StorSimple Manager-Diensts ein deaktiviertes Gerät, das Sie löschen möchten.
   2. Klicken Sie unten auf der Seite auf **Löschen**.
   3. Sie werden aufgefordert, diesen Schritt zu bestätigen. Klicken Sie auf **Ja** , um fortzufahren.
      
      Es dauert einige Minuten, bis das Gerät gelöscht wird.

## <a name="deactivate-and-retain-data"></a>Deaktivieren und Beibehalten von Daten
Wenn Sie das Gerät löschen, aber die Daten beibehalten möchten, führen Sie die folgenden Schritte aus:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>So deaktivieren Sie ein Gerät und behalten die Daten bei
1. Deaktivieren Sie das Gerät. Alle Volumecontainer und die Momentaufnahmen des Geräts bleiben erhalten.
   
   1. Wählen Sie auf der Seite **Geräte** des StorSimple Manager-Diensts das Gerät aus, das Sie deaktivieren möchten, und klicken Sie unten auf der Seite auf **Deaktivieren**.
   2. Eine Bestätigungsmeldung wird angezeigt. Klicken Sie auf **Ja** , um fortzufahren. Der Prozess zum Deaktivieren beginnt und dauert einige Minuten.
2. Sie können nun ein Failover der Volumecontainer und der zugehörigen Momentaufnahmen durchführen. Informationen zu den Verfahren finden Sie unter [Ausführen eines Failovers und einer Notfallwiederherstellung für das StorSimple-Gerät](storsimple-device-failover-disaster-recovery.md).
3. Sie können nach der Deaktivierung und der Ausführung des Failovers das Gerät vollständig löschen. Das Löschen eines Geräts entfernt es aus der Liste der mit dem Dienst verbundenen Geräte. Der Dienst kann das gelöschte Gerät dann nicht mehr länger verwalten. Führen Sie die folgenden Schritte aus, um das Gerät zu löschen:
   
   1. Wählen Sie auf der Seite **Geräte** des StorSimple Manager-Diensts ein deaktiviertes Gerät, das Sie löschen möchten.
   2. Klicken Sie unten auf der Seite auf **Löschen**.
   3. Sie werden aufgefordert, diesen Schritt zu bestätigen. Klicken Sie auf **Ja** , um fortzufahren.
      
      Es dauert einige Minuten, bis das Gerät gelöscht wird.

## <a name="deactivate-and-delete-a-virtual-device"></a>Deaktivieren und Löschen eines virtuellen Geräts
Wenn Sie ein virtuelles StorSimple-Gerät deaktivieren, wird die Zuordnung des virtuellen Computers aufgehoben. Anschließend können Sie den virtuellen Computer und die bei seiner Bereitstellung erstellten Ressourcen löschen. Nach der Deaktivierung kann das virtuelle Gerät nicht im vorherigen Zustand wiederhergestellt werden. 

Beim Deaktivieren werden die folgenden Aktionen ausgeführt:

* Das virtuelle StorSimple-Gerät wird entfernt.
* Die für das virtuelle StorSimple-Gerät erstellten OSDisks und Datenträger werden entfernt.
* Der bei der Bereitstellung erstellte gehostete Dienst und das Virtual Network werden beibehalten. Wenn diese Entitäten nicht verwendet werden, sollten sie manuell gelöscht werden.
* Vom virtuellen StorSimple-Gerät erstellte Cloudmomentaufnahmen werden beibehalten.

## <a name="next-steps"></a>Nächste Schritte
* Um das deaktivierte Gerät mit den Werkseinstellungen wiederherzustellen, wechseln Sie zu [Zurücksetzen des Geräts auf die Standardwerkseinstellungen](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Um technische Unterstützung zu erhalten, [kontaktieren Sie den Microsoft-Support](storsimple-contact-microsoft-support.md).
* Weitere Informationen zum Verwenden des StorSimple Manager-Diensts finden Sie unter [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple Manager-Diensts](storsimple-manager-service-administration.md). 

