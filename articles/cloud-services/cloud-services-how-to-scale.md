---
title: Automatisches Skalieren eines Clouddiensts im (klassischen) Portal | Microsoft-Dokumentation
description: "(Klassisch) Erfahren Sie, wie Sie mit dem klassischen Portal Regeln für die automatische Skalierung für eine Clouddienst-Webrolle oder -Workerrolle in Azure konfigurieren."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: eb167d70-4eba-42a4-b157-d8d0688abf4b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 90d55bbac6e113d6add848ace67cf0749e26342b
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/29/2017
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-classic-portal"></a>Konfigurieren der automatischen Skalierung für einen Clouddienst im klassischen Portal
> [!div class="op_single_selector"]
> * [Azure-Portal](cloud-services-how-to-scale-portal.md)
> * [Klassisches Azure-Portal](cloud-services-how-to-scale.md)

Auf der Skalierungsseite des klassischen Azure-Portals können Sie automatische Skalierungseinstellungen für Ihre Web- oder Workerrolle konfigurieren. Alternativ können Sie die manuelle Skalierung anstelle regelbasierter automatischer Skalierung konfigurieren.

> [!NOTE]
> Dieser Artikel konzentriert sich auf Clouddienst-Webrollen und -Workerrollen. Wenn Sie einen virtuellen Computer (klassisch) direkt erstellen, wird er in einem Clouddienst gehostet. Einige dieser Informationen gelten für diese Typen virtueller Computer. Das Skalieren einer Verfügbarkeitsgruppe virtueller Computer besteht nur darin, dass Sie sie entsprechend der Skalierungsregeln, die Sie konfigurieren, ein- und ausschalten. Weitere Informationen zu virtuellen Computern und Verfügbarkeitsgruppen finden Sie unter [Konfigurieren einer Verfügbarkeitsgruppe für virtuelle Computer im klassischen Bereitstellungsmodell](../virtual-machines/windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Folgendes ist allerdings zu beachten, bevor die Skalierung einer Anwendung konfiguriert wird:

* Die Skalierung ist abhängig von der Kernspeichernutzung.

    Größere Rolleninstanzen verwenden mehr Kerne. Sie können eine Anwendung nur innerhalb der für Ihr Abonnement geltenden Kerngrenzwerte skalieren. Nehmen Sie z.B. an, Ihr Abonnement sei auf 20 Kerne begrenzt. Wenn Sie eine Anwendung mit zwei mittelgroßen Clouddiensten ausführen (insgesamt vier Kerne), stehen für das zentrale Hochskalieren anderer Clouddienstbereitstellungen in Ihrem Abonnement nur noch sechzehn Kerne zur Verfügung. Weitere Informationen zu Größen finden Sie unter [Größen für Clouddienste](cloud-services-sizes-specs.md).

* Sie müssen eine Warteschlange anlegen und dieser einer Rolle zuweisen, bevor Sie eine Anwendung auf Basis eines Nachrichtenschwellwerts skalieren können. Weitere Informationen finden Sie unter [Verwenden des Warteschlangenspeicherdiensts](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Sie können Ressourcen skalieren, die mit Ihrem Clouddienst verknüpft sind. Weitere Informationen zum Verknüpfen von Ressourcen finden Sie unter [Verknüpfen einer Ressource mit einem Clouddienst](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service).

* Um die Hochverfügbarkeit Ihrer Anwendung zu gewährleisten, sollte diese mit mindestens zwei Rolleninstanzen bereitgestellt werden. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel](https://azure.microsoft.com/support/legal/sla/).

## <a name="schedule-scaling"></a>Zeitplan (Skalierung)
In der Standardeinstellung folgen alle Rollen keinem bestimmten Zeitplan. Daher gelten alle geänderten Einstellungen für alle Uhrzeiten und alle Tage im Laufe des Jahres. Wenn Sie möchten, können Sie manuelle oder automatische Skalierung für einen der folgenden Modi einrichten:

* Wochentage
* Wochenenden
* Woche nachts
* Woche morgens
* Bestimmte Datumsangaben
* Bestimmte Datumsbereiche

Die Planungseinstellung wird im [klassischen Azure-Portal](https://manage.windowsazure.com/) auf der Seite  
**Cloud Services** > **\[Ihr Clouddienst\]** > **Skalieren** > **\[Produktion oder Staging\]** konfiguriert.

Klicken Sie für jede Rolle, die Sie ändern möchten, auf die Schaltfläche **Zeiten für Zeitplan einrichten** .

![Automatisches Skalieren von Clouddiensten auf Basis eines Zeitplans][scale_schedules]

## <a name="manual-scale"></a>Manuelles Skalieren
Auf der Seite **Skalieren** können Sie die Anzahl der in einem Clouddienst ausgeführten Instanzen manuell erhöhen oder verringern. Diese Einstellung wird für jeden Zeitplan konfiguriert, den Sie erstellt haben, oder für alle Zeiten, wenn Sie keinen Zeitplan erstellt haben.

1. Klicken Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/)auf **Cloud Services**und dann auf den Namen des Clouddiensts, um das Dashboard zu öffnen.
   
   > [!TIP]
   > Wenn Sie Ihren Clouddienst nicht sehen können, müssen Sie möglicherweise von **Produktion** zu **Staging** wechseln oder umgekehrt.

2. Klicken Sie auf **Skalieren**.
3. Wählen Sie den Zeitplan, für den Sie die Skalierungsoptionen ändern möchten. *Keine geplanten Zeiten* ist der Standard, wenn Sie keine Zeitpläne definiert haben.
4. Suchen Sie den Abschnitt **Nach Metrik skalieren**, und wählen Sie **KEINE** aus. Diese Einstellung ist der Standard für alle Rollen.
5. Jede Rolle in einem Clouddienst hat einen Regler, über den die Anzahl der verwendbaren Instanzen geändert werden kann.
   
    ![Manuelles Skalieren einer Clouddienstrolle][manual_scale]
   
    Wenn Sie mehr Instanzen benötigen, müssen Sie möglicherweise die [Größe des Clouddiensts für den virtuellen Computer](cloud-services-sizes-specs.md)ändern.
6. Klicken Sie auf **Speichern**.  
   Je nach Reglerstellung werden Rolleninstanzen hinzugefügt oder entfernt.

> [!TIP]
> Wenn Sie ![][tip_icon] sehen, bewegen Sie die Maus dorthin, und Sie können Hilfe zu den Aktionen einer bestimmten Einstellung erhalten.

## <a name="automatic-scale---cpu"></a>Automatisches Skalieren – CPU
Dieser Modus skaliert, ob die durchschnittliche prozentuale CPU-Nutzung bestimmte Schwellenwerte über- oder unterschreitet. Rolleninstanzen werden erstellt oder gelöscht, wenn dies geschieht.

1. Klicken Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/)auf **Cloud Services**und dann auf den Namen des Clouddiensts, um das Dashboard zu öffnen.
   
   > [!TIP]
   > Wenn Sie Ihren Clouddienst nicht sehen können, müssen Sie möglicherweise von **Produktion** zu **Staging** wechseln oder umgekehrt.

2. Klicken Sie auf **Skalieren**.
3. Wählen Sie den Zeitplan, für den Sie die Skalierungsoptionen ändern möchten. *Keine geplanten Zeiten* ist der Standard, wenn Sie keine Zeitpläne definiert haben.
4. Suchen Sie den Abschnitt **Nach Metrik skalieren**, und wählen Sie **CPU** aus.
5. Jetzt können Sie einen minimalen und maximalen Bereich von Rolleninstanzen, die Ziel-CPU-Auslastung (um zentrales Hochskalieren auszulösen) und die Anzahl der Instanzen, um die zentral hoch- und herunterskaliert werden soll, konfigurieren.

![Skalieren einer Clouddienstrolle nach CPU-Auslastung][cpu_scale]

> [!TIP]
> Wenn Sie ![][tip_icon] sehen, bewegen Sie die Maus dorthin, und Sie können Hilfe zu den Aktionen einer bestimmten Einstellung erhalten.

## <a name="automatic-scale---queue"></a>Automatisches Skalieren – Warteschlange
Dieser Modus skaliert automatisch, ob die Anzahl der Nachrichten in einer Warteschlange einen angegebenen Schwellenwert über- oder unterschreitet. Rolleninstanzen werden erstellt oder gelöscht, wenn dies geschieht.

1. Klicken Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/)auf **Cloud Services**und dann auf den Namen des Clouddiensts, um das Dashboard zu öffnen.
   
   > [!TIP]
   > Wenn Sie Ihren Clouddienst nicht sehen können, müssen Sie möglicherweise von **Produktion** zu **Staging** wechseln oder umgekehrt.

2. Klicken Sie auf **Skalieren**.
3. Suchen Sie den Abschnitt **Nach Metrik skalieren**, und wählen Sie **WARTESCHLANGE** aus.
4. Jetzt können Sie einen minimalen und maximalen Bereich von Rolleninstanzen, die Warteschlange und die Anzahl der Warteschlangennachrichten, die für jede Instanz verarbeitet werden sollen, und die Anzahl der Instanzen, um die zentral hoch- und herunterskaliert werden soll, konfigurieren.

![Skalieren einer Clouddienstrolle nach Nachrichtenwarteschlange][queue_scale]

> [!TIP]
> Wenn Sie ![][tip_icon] sehen, bewegen Sie die Maus dorthin, und Sie können Hilfe zu den Aktionen einer bestimmten Einstellung erhalten.

## <a name="scale-linked-resources"></a>Skalieren verknüpfter Ressourcen
Häufig empfiehlt es sich, beim Skalieren einer Rolle auch die von der Anwendung verwendete Datenbank zu skalieren. Wenn Sie die Datenbank mit dem Clouddienst verknüpfen, können Sie auf die Skalierungseinstellungen für diese Ressource zugreifen, indem Sie auf den entsprechenden Link klicken.

1. Klicken Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/)auf **Cloud Services**und dann auf den Namen des Clouddiensts, um das Dashboard zu öffnen.
   
   > [!TIP]
   > Wenn Sie Ihren Clouddienst nicht sehen können, müssen Sie möglicherweise von **Produktion** zu **Staging** wechseln oder umgekehrt.

2. Klicken Sie auf **Skalieren**.
3. Suchen Sie die **verknüpften Ressourcen**, und klicken Sie auf **Skalierung für diese Datenbank verwalten**.
   
   > [!NOTE]
   > Wenn Sie keine **verknüpften Ressourcen** sehen, besitzen Sie wahrscheinlich keine verknüpften Ressourcen.

![][linked_resource]

[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png
