---
title: Virtueller Azure-Computer – PowerShell-Beispiele | Microsoft-Dokumentation
description: Virtueller Azure-Computer – PowerShell-Beispiele
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 31dbb8485232d1b446469135b5bda0c8c7884190
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2018
---
# <a name="azure-virtual-machine-powershell-samples"></a>Virtueller Azure-Computer – PowerShell-Beispiele

Die folgende Tabelle enthält Links zu Beispielen von PowerShell-Skripts, die virtuelle Windows-Computer erstellen und verwalten.

| | |
|---|---|
|**Erstellen von virtuellen Computern**||
| [Schnelles Erstellen eines virtuellen Computers](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Erstellt eine Ressourcengruppe, einen virtuellen Computer und alle zugehörigen Ressourcen mit der geringsten Anzahl von Eingabeaufforderungen.|
| [Erstellen einer vollständig konfigurierten VM](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Erstellt eine Ressourcengruppe, einen virtuellen Computer und alle zugehörigen Ressourcen.|
| [Erstellen hoch verfügbarer VMs](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Erstellt mehrere virtuelle Computer in einer Konfiguration mit hoher Verfügbarkeit und Lastenausgleich.|
| [Erstellen einer VM und Ausführen eines Konfigurationsskripts](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Erstellt einen virtuellen Computer und verwendet die benutzerdefinierte Azure-Skripterweiterung zum Installieren von IIS. |
| [Erstellen einer VM und Ausführen einer DSC-Konfiguration](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Erstellt einen virtuellen Computer und verwendet die Azure-DSC-Erweiterung (Desired State Configuration) zum Installieren von IIS. |
| [Hochladen einer virtuellen Festplatte und Erstellen von virtuellen Computern](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Lädt eine lokale VHD-Datei in Azure hoch, erstellt ein Image aus der VHD und erstellt dann aus diesem Image einen virtuellen Computer. |
| [Erstellen eines virtuellen Computers von einem verwalteten Betriebssystemdatenträger](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Erstellt einen virtuellen Computer durch Anfügen eines vorhandenen verwalteten Datenträgers als Betriebssystemdatenträger. |
| [Erstellen eines virtuellen Computers aus einer Momentaufnahme](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Erstellt einen virtuellen Computer aus einer Momentaufnahme, indem aus der Momentaufnahme zuerst ein verwalteter Datenträger erstellt und dieser neue verwaltete Datenträger anschließend als Betriebssystemdatenträger angefügt wird. |
|**Verwalten von Storage**||
| [Erstellen eines verwalteten Datenträgers aus einer VHD in demselben oder einem anderen Abonnement](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Erstellt einen verwalteten Datenträger aus einer bestimmten VHD als Betriebssystemdatenträger oder aus einer Daten-VHD als Datenträger für Daten in demselben oder einem anderen Abonnement.  |
| [Erstellen eines verwalteten Datenträgers aus einer Momentaufnahme](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Erstellt einen verwalteten Datenträger aus einer Momentaufnahme. |
| [Kopieren eines verwalteten Datenträgers in dasselbe oder ein anderes Abonnement](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Kopiert einen verwalteten Datenträger in dasselbe oder ein anderes Abonnement in derselben Region wie der übergeordnete Datenträger. 
| [Exportieren einer Momentaufnahme als VHD-Datei in ein Speicherkonto](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Exportiert eine verwaltete Momentaufnahme als VHD-Datei in ein Speicherkonto in einer anderen Region. |
| [Erstellen einer Momentaufnahme aus einer VHD](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Erstellt eine Momentaufnahme aus einer VHD, um mehrere identische verwaltete Datenträger in kürzester Zeit aus der Momentaufnahme zu erstellen.  |
| [Kopieren einer Momentaufnahme in dasselbe oder ein anderes Abonnement](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Kopiert eine Momentaufnahme in dasselbe oder ein anderes Abonnement in derselben Region wie die übergeordnete Momentaufnahme. |
|**Sichern von virtuellen Computern**||
| [Verschlüsseln eines virtuellen Computers und der Datenträger](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Erstellt einen Azure Key Vault, Verschlüsselungsschlüssel und Dienstprinzipal und verschlüsselt dann einen virtuellen Computer. |
|**Überwachen virtueller Computer**||
| [Überwachen einer VM mit der Operations Management Suite](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Erstellt einen virtuellen Computer, installiert den OMS-Agent (Operations Management Suite) und registriert die VM in einem OMS-Arbeitsbereich.  |
| | |

