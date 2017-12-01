---
title: Verwalten von Labrichtlinien in Azure DevTest Labs | Microsoft-Dokumentation
description: "Erfahren Sie, wie Sie Labrichtlinien wie VM-Größen, die maximale Anzahl von VMs pro Benutzer und das automatisierte Herunterfahren definieren."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f42baa1ac37f425f915b6949e018f6e7397d3ae
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2017
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Verwalten aller Richtlinien für ein Lab in Azure DevTest Labs

Mit Azure DevTest Labs können Sie Kosten und unnötigen Aufwand in Ihren Labs minimieren, indem Sie Richtlinien (Einstellungen) für jedes Lab verwalten. Dieser Artikel erläutert die einzelnen Schritte zum Einrichten der Richtlinien.  

## <a name="set-allowed-virtual-machine-sizes"></a>Festlegen der zulässigen Größen virtueller Computer
Die Richtlinie für die zulässigen VM-Größen hilft dabei, unnötigen Aufwand im Lab zu minimieren, indem sie Ihnen ermöglicht, die im Lab zulässigen VM-Größen anzugeben. Wenn diese Richtlinie aktiviert ist, können nur VM-Größen aus dieser Liste zum Erstellen von virtuellen Computern verwendet werden.

1. Wählen Sie im [Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) ein Lab und dann **Konfiguration und Richtlinien** aus.

    ![Zugreifen auf die Konfiguration und die Richtlinien des Labs](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. Wählen Sie im Bereich **Konfiguration und Richtlinien** des Labs die Option **Zulässige Größen virtueller Computer** aus.
   
    ![Allowed virtual machines sizes](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Wählen Sie **Ein**, um diese Richtlinie zu aktivieren, und **Aus**, um sie zu deaktivieren.

1. Wenn Sie diese Richtlinie aktiviert haben, wählen Sie mindestens eine VM-Größe, die im Lab erstellt werden kann.

1. Wählen Sie **Speichern**aus.

## <a name="set-virtual-machines-per-user"></a>Festlegen der virtuellen Computer pro Benutzer
Mit der Richtlinie für **Virtuelle Computer pro Benutzer** können Sie die maximale Anzahl von virtuellen Computern angeben, die von einem einzelnen Benutzer erstellt werden können. Wenn ein Benutzer versucht, einen virtuellen Computer zu erstellen oder anzufordern, und das Benutzerlimit erreicht wurde, wird in einer Fehlermeldung darauf hingewiesen, dass der virtuelle Computer nicht erstellt/angefordert werden kann. 

1. Wählen Sie im Bereich **Konfiguration und Richtlinien** des Labs die Option **Virtuelle Computer pro Benutzer** aus.
   
    ![Virtual machines per user](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Wählen Sie **Ja**, um die Anzahl von virtuellen Computern pro Benutzer zu begrenzen. Wenn Sie die Anzahl von virtuellen Computern pro Benutzer nicht begrenzen möchten, wählen Sie **Nein**. Wenn Sie **Ja** auswählen, geben Sie einen numerischen Wert für die maximale Anzahl von virtuellen Computern ein, die von einem Benutzer erstellt oder angefordert werden können. 

1. Wählen Sie **Ja**, um die Anzahl von virtuellen Computern zu begrenzen, die eine SSD (Solid-State Disk) verwenden können. Wenn Sie die Anzahl von virtuellen Computern, die eine SSD verwenden können, nicht begrenzen möchten, wählen Sie **Nein**. Wenn Sie **Ja** auswählen, geben Sie einen Wert für die maximale Anzahl von virtuellen Computern ein, die mit einer SSD erstellt werden können. 

1. Wählen Sie **Speichern**aus.

## <a name="set-virtual-machines-per-lab"></a>Festlegen der virtuellen Computer pro Lab
Mit der Richtlinie für **Virtuelle Computer pro Lab** können Sie die maximale Anzahl von virtuellen Computern angeben, die für das aktuelle Lab erstellt werden können. Wenn ein Benutzer versucht, einen virtuellen Computer zu erstellen, und die Labgrenze erreicht wurde, wird in einer Fehlermeldung darauf hingewiesen, dass der virtuelle Computer nicht erstellt werden kann. 

1. Wählen Sie im Bereich **Konfiguration und Richtlinien** des Labs die Option **Virtuelle Computer pro Lab** aus.
   
    ![Virtual machines per lab](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Wählen Sie **Ja**, um die Anzahl von virtuellen Computern pro Lab zu begrenzen. Wenn Sie die Anzahl von virtuellen Computern pro Lab nicht begrenzen möchten, wählen Sie **Nein**. Wenn Sie **Ja** auswählen, geben Sie einen numerischen Wert für die maximale Anzahl von virtuellen Computern ein, die von einem Benutzer erstellt oder angefordert werden können. 

1. Wählen Sie **Ja**, um die Anzahl von virtuellen Computern zu begrenzen, die eine SSD (Solid-State Disk) verwenden können. Wenn Sie die Anzahl von virtuellen Computern, die eine SSD verwenden können, nicht begrenzen möchten, wählen Sie **Nein**. Wenn Sie **Ja** auswählen, geben Sie einen Wert für die maximale Anzahl von virtuellen Computern ein, die mit einer SSD erstellt werden können. 

1. Wählen Sie **Speichern**aus.

## <a name="set-auto-shutdown"></a>Festlegen des automatischen Herunterfahrens
Die Richtlinie „Automatisch herunterfahren“ hilft dabei, unnötigen Aufwand im Lab zu minimieren, indem sie Ihnen ermöglicht, die Uhrzeit anzugeben, zu der die virtuellen Computer für dieses Lab heruntergefahren werden.

1. Wählen Sie im Bereich **Konfiguration und Richtlinien** des Labs die Option **Automatisch herunterfahren** aus.
   
    ![Auto-shutdown](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Wählen Sie **Ein**, um diese Richtlinie zu aktivieren, und **Aus**, um sie zu deaktivieren.

1. Wenn Sie diese Richtlinie aktivieren, geben Sie die Uhrzeit (und die Zeitzone) an, zu der alle virtuellen Computer im aktuellen Lab heruntergefahren werden sollen.

1. Geben Sie für die Option, mit der 15 Minuten vor der angegebenen Uhrzeit des automatischen Herunterfahrens eine Benachrichtigung gesendet wird, entweder **Ja** oder **Nein** an. Wenn Sie **Ja** auswählen, geben Sie einen Webhook-URL-Endpunkt oder eine E-Mail-Adresse ein, der bzw. die angibt, wo die Benachrichtigung veröffentlicht bzw. an wen diese gesendet werden soll. Der Benutzer erhält eine Benachrichtigung und hat die Möglichkeit, das Herunterfahren zu verzögern.

   Weitere Informationen zu Webhooks finden Sie unter [Erstellen eines Webhooks oder einer API-Azure-Funktion](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Wählen Sie **Speichern**aus.

Standardmäßig gilt diese Richtlinie nach der Aktivierung für alle virtuellen Computer im aktuellen Lab. Um diese Einstellung von einem bestimmten virtuellen Computer zu entfernen, öffnen Sie den Verwaltungsbereich des virtuellen Computers, und ändern Sie die Einstellung **Automatisch herunterfahren**.

## <a name="set-auto-start"></a>Festlegen des automatischen Startens
Mit der Richtlinie „Automatisch starten“ können Sie angeben, wann die virtuellen Computer im aktuellen Lab gestartet werden sollen.  

1. Wählen Sie im Bereich **Konfiguration und Richtlinien** des Labs die Option **Automatisch starten** aus.
   
    ![Auto-start](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Wählen Sie **Ein**, um diese Richtlinie zu aktivieren, und **Aus**, um sie zu deaktivieren.

3. Wenn Sie diese Richtlinie aktivieren, geben Sie die geplante Startzeit, die Zeitzone und die Wochentage an, an denen die Uhrzeit gelten soll. 

4. Wählen Sie **Speichern**aus.

Nach der Aktivierung wird diese Richtlinie nicht automatisch auf alle virtuellen Computer im aktuellen Lab angewendet. Um diese Einstellung auf einen bestimmten virtuellen Computer anzuwenden, öffnen Sie den Verwaltungsbereich des virtuellen Computers, und ändern Sie die Einstellung **Automatisch starten**.

## <a name="set-expiration-date"></a>Festlegen des Ablaufdatums
Sie können beim [Erstellen des virtuellen Computers](devtest-lab-add-vm.md) ein Ablaufdatum festlegen. Wählen Sie in **Erweiterte Einstellungen** zum Festlegen einer Ablaufoption das Kalendersymbol aus, um ein Datum anzugeben, an dem der virtuelle Computer automatisch gelöscht wird. Standardmäßig läuft der virtuelle Computer nie ab.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nächste Schritte
Sobald Sie die verschiedenen VM-Richtlinieneinstellungen für Ihr Lab definiert und angewendet haben, können Sie als Nächstes versuchen, die folgenden Aufgaben auszuführen:

* [Grundlegendes zu freigegebenen IP-Adressen](devtest-lab-shared-ip.md): Erläutert die Verwendung von freigegebenen IP-Adressen in DevTest Labs, um die Anzahl von öffentlichen IP-Adressen zu minimieren, die zum Herstellen einer Verbindung mit den virtuellen Computern Ihres Labs erforderlich sind.
* [Konfigurieren des Kostenmanagements](devtest-lab-configure-cost-management.md): Veranschaulicht die Nutzung des Diagramms **Monatlicher geschätzter Kostentrend**  
  zum Anzeigen der geschätzten Kosten des aktuellen Monats bis zum gegenwärtigen Zeitpunkt und die voraussichtlichen Kosten am Ende des Monats.
* [Erstellen eines benutzerdefinierten Images](devtest-lab-create-template.md): Wenn Sie eine VM erstellen, geben Sie eine Basis an. Dabei kann es sich entweder um ein benutzerdefiniertes Image oder ein Marketplace-Image handeln. In diesem Artikel erfahren Sie, wie Sie ein benutzerdefiniertes Image aus einer VHD-Datei erstellen.
* [Konfigurieren von Marketplace-Images](devtest-lab-configure-marketplace-images.md): Azure DevTest Labs unterstützt die Erstellung von VMs auf der Basis von Azure Marketplace-Images. In diesem Artikel erfahren Sie, wie Sie ggf. angeben, welche Azure Marketplace-Images zum Erstellen virtueller Computer in einem Lab verwendet werden können.
* [Erstellen einer VM in einem Lab:](devtest-lab-add-vm-with-artifacts.md) In diesem Artikel wird veranschaulicht, wie Sie eine VM aus einem Basisimage erstellen (entweder aus einem benutzerdefinierten Image oder einem Marketplace-Image) und wie Sie mit Artefakten auf Ihrer VM arbeiten.

