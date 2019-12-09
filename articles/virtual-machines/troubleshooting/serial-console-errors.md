---
title: Fehler in der seriellen Azure-Konsole | Microsoft-Dokumentation
description: Häufige Fehler in der seriellen Azure-Konsole
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: 7bd9fe4044dace4061285c016cb08562b556b98e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483586"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Häufige Fehler in der seriellen Azure-Konsole
In der seriellen Azure-Konsole können mehrere bekannte Fehler auftreten. Diese Fehler und die jeweils entsprechenden Lösungsschritte sind nachfolgend aufgeführt.

## <a name="common-errors"></a>Häufige Fehler

Error                             |   Lösung
:---------------------------------|:--------------------------------------------|
„Für die serielle Azure-Konsole muss die Startdiagnose aktiviert sein. Klicken Sie hier, um die Startdiagnose für Ihren virtuellen Computer zu konfigurieren.“ ![Startdiagnosefehler](./media/virtual-machines-serial-console/virtual-machines-serial-console-boot-diagnostics-error.png) | Stellen Sie sicher, dass die [Startdiagnose](boot-diagnostics.md) für den virtuellen Computer oder die VM-Skalierungsgruppe aktiviert ist. Wenn Sie die serielle Konsole in einer VM-Skalierungsgruppeninstanz verwenden, vergewissern Sie sich, dass für die Instanz das neueste Modell verwendet wird.
„Für die serielle Azure-Konsole muss ein virtueller Computer ausgeführt werden. Verwenden Sie die Schaltfläche ‚Start‘ oben, um Ihren virtuellen Computer zu starten.“ ![Fehler bei der Aufhebung der Zuordnung](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | Der virtuelle Computer oder die VM-Skalierungsgruppeninstanz muss den Status „Gestartet“ aufweisen, um auf die serielle Konsole zugreifen zu können. (Der virtuelle Computer darf weder beendet noch freigegeben sein.) Stellen Sie sicher, dass der virtuelle Computer oder die VM-Skalierungsgruppeninstanz ausgeführt wird, und wiederholen Sie den Vorgang.
„Die serielle Azure-Konsole ist für dieses Abonnement nicht aktiviert. Wenden Sie sich an Ihren Abonnementadministrator, um sie zu aktivieren.“ ![Fehler wegen Deaktivierung auf Abonnementebene](./media/virtual-machines-serial-console/virtual-machines-serial-console-subscription-disabled-error.png) | Die serielle Azure-Konsole kann auf Abonnementebene deaktiviert werden. Als Abonnementadministrator können Sie [die serielle Azure-Konsole aktivieren und deaktivieren](./serial-console-enable-disable.md). Wenn Sie kein Abonnementadministrator sind, wenden Sie sich für die nächsten Schritte an Ihren Abonnementadministrator.
Beim Zugriff auf das Speicherkonto für die Startdiagnose dieses virtuellen Computers wurde eine „Forbidden“-Antwort empfangen. ![Fehler bei der Speicherkontofirewall](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| Stellen Sie sicher, dass die Startdiagnose nicht über eine Kontofirewall verfügt. Damit die serielle Konsole funktioniert, ist Zugriff auf ein Startdiagnose-Speicherkonto erforderlich. Die serielle Konsole kann programmbedingt nicht verwendet werden, wenn für das Speicherkonto mit Startdiagnose Speicherkontofirewalls aktiviert sind.
Sie verfügen nicht über die erforderlichen Berechtigungen, um diese VM mit der seriellen Konsole zu verwenden. Achten Sie darauf, dass Sie mindestens über die Berechtigungen der Rolle „Mitwirkender für virtuelle Computer“ verfügen.| Für den Zugriff auf die serielle Konsole müssen Sie über Berechtigungen auf der Zugriffsebene „Mitwirkender“ oder höher für den virtuellen Computer oder die VM-Skalierungsgruppe verfügen. Weitere Informationen finden Sie auf der Seite mit der [Übersicht](serial-console-overview.md).
Das Speicherkonto „{0}“ für die Startdiagnose auf dieser VM wurde nicht gefunden. Stellen Sie sicher, dass die Startdiagnose für diesen virtuellen Computer aktiviert ist, dass dieses Speicherkonto nicht gelöscht wurde und dass Sie Zugriff auf dieses Speicherkonto haben. | Vergewissern Sie sich, dass Sie das Startdiagnose-Speicherkonto für den virtuellen Computer oder die VM-Skalierungsgruppe nicht gelöscht haben.
Die Bereitstellung für diesen virtuellen Computer war noch nicht erfolgreich. Stellen Sie sicher, dass der virtuelle Computer vollständig bereitgestellt wird, und versuchen Sie dann noch einmal, die Verbindung mit der seriellen Konsole herzustellen. | Die Bereitstellung des virtuellen Computers oder der VM-Skalierungsgruppe ist möglicherweise noch nicht abgeschlossen. Warten Sie einige Zeit, und versuchen Sie es erneut.
Sie haben nicht die erforderlichen Berechtigungen zum Schreiben in das Startdiagnose-Speicherkonto für diesen virtuellen Computer. Stellen Sie sicher, dass Sie mindestens die Berechtigungen eines VM-Mitwirkenden für „{0}“ besitzen. | Für den Zugriff auf die serielle Konsole sind Berechtigungen der Zugriffsebene „Mitwirkender“ für das Startdiagnose-Speicherkonto erforderlich. Weitere Informationen finden Sie auf der Seite mit der [Übersicht](serial-console-overview.md).
Die Ressourcengruppe für das Startdiagnose-Speicherkonto *&lt;STORAGEACCOUNTNAME&gt;* kann nicht ermittelt werden. Stellen Sie sicher, dass die Startdiagnose für diesen virtuellen Computer aktiviert ist und Sie über Zugriff auf dieses Speicherkonto verfügen. | Für den Zugriff auf die serielle Konsole sind Berechtigungen der Zugriffsebene „Mitwirkender“ für das Startdiagnose-Speicherkonto erforderlich. Weitere Informationen finden Sie auf der Seite mit der [Übersicht](serial-console-overview.md).
Das Websocket ist geschlossen oder konnte nicht geöffnet werden. | Möglicherweise müssen Sie Firewallzugriff auf `*.console.azure.com` hinzufügen. Ein detaillierterer, aber längerer Ansatz besteht darin, Firewallzugriff auf die [IP-Bereiche des Microsoft Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=41653) zuzulassen, die sich jedoch relativ regelmäßig ändern.
Die serielle Konsole funktioniert nicht mit einem Speicherkonto, für das Azure Data Lake Storage Gen2 mit hierarchischen Namespaces verwendet wird. | Dies ist ein bekanntes Problem mit hierarchischen Namespaces. Stellen Sie zur Behebung des Problems sicher, dass das Startdiagnose-Speicherkonto Ihrer VM nicht per Azure Data Lake Storage Gen2 erstellt wird. Diese Option kann nur bei der Erstellung des Speicherkontos festgelegt werden. Unter Umständen müssen Sie ein separates Startdiagnose-Speicherkonto ohne Aktivierung von Azure Data Lake Storage Gen2 erstellen, um dieses Problem zu beheben.


## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zur [seriellen Azure-Konsole für virtuelle Linux-Computer](./serial-console-linux.md)
* Weitere Informationen zur [seriellen Azure-Konsole für virtuelle Windows-Computer](./serial-console-windows.md)