---
title: Überprüfen des Dateisystems beim Starten einer Azure-VM | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das Problem beheben, dass beim Starten des virtuellen Computers angezeigt wird, dass das Dateisystem überprüft wird.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: ba3138076da89610bef878583e424153594c5dcc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981375"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Windows zeigt beim Starten einer Azure-VM „Dateisystem wird überprüft“ an.

Dieser Artikel beschreibt den Fehler „Dateisystem wird überprüft“, der möglicherweise beim Starten eines virtuellen Windows-Computers (VM) in Microsoft Azure angezeigt wird.

> [!NOTE] 
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [das Resource Manager-Modell und das klassische Bereitstellungsmodell](../../azure-resource-manager/management/deployment-models.md). Dieser Artikel beschreibt die Verwendung des Resource Manager-Bereitstellungsmodells, das anstelle des klassischen Bereitstellungsmodells für neue Bereitstellungen empfohlen wird.

## <a name="symptom"></a>Symptom 

Eine Windows-VM wird nicht gestartet. Wenn Sie die Start-Screenshots unter [Startdiagnose](boot-diagnostics.md) überprüfen, sehen Sie, dass der Prozess der Datenträgerüberprüfung („chkdsk.exe“) mit einer der folgenden Meldungen ausgeführt wird:

- „Scanning and repairing drive (C:)“ (Laufwerk (C:) wird gescannt und repariert)
- „Checking file system on C:“ (Dateisystem auf „C:“ wird überprüft)

## <a name="cause"></a>Ursache

Wenn im Dateisystem ein NTFS-Fehler gefunden wird, überprüft Windows die Konsistenz des Datenträgers beim nächsten Neustart und repariert diese. In der Regel geschieht dies, wenn beim virtuellen Computer ein unerwarteter Neustart durchgeführt oder das Herunterfahren des virtuellen Computers abrupt unterbrochen wurde.

## <a name="solution"></a>Lösung 

Nach Abschluss der Datenträgerüberprüfung wird Windows normal neu gestartet. Wenn der virtuelle Computer immer wieder zur Datenträgerüberprüfung zurückkehrt, versuchen Sie, sie offline auf dem virtuellen Computer auszuführen:
1.  Erstellen Sie eine Momentaufnahme des Betriebssystemdatenträgers des betroffenen virtuellen Computers als Sicherung. Weitere Informationen finden Sie unter [Erstellen einer Momentaufnahme eines Datenträgers](../windows/snapshot-copy-managed-disk.md).
2.  [Fügen Sie den Betriebssystemdatenträger an einen virtuellen Computer für die Wiederherstellung an](troubleshoot-recovery-disks-portal-windows.md).  
3.  Führen Sie auf der Wiederherstellungs-VM für den angefügten Betriebssystemdatenträger die Datenträgerüberprüfung aus. Im folgenden Beispiel ist der Laufwerkbuchstabe des angefügten Betriebssystemdatenträgers E. 
        
        chkdsk E: /f
4.  Trennen Sie den Datenträger nach Abschluss des Datenträgerüberprüfung von der Wiederherstellungs-VM, und fügen Sie den Datenträger dann erneut als Betriebssystemdatenträger an den betroffenen virtuellen Computer an. Weitere Informationen finden Sie unter [Beheben von Problemen mit einer Windows-VM durch Anfügen des Betriebssystemdatenträgers an eine Wiederherstellungs-VM](troubleshoot-recovery-disks-portal-windows.md).
