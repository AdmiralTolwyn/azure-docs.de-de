---
title: Behandeln von Problemen beim Bereitstellen virtueller Linux-Computer (klassisches Bereitstellungsmodell) | Microsoft Docs
description: Behandeln Sie Probleme beim Erstellen eines neuen virtuellen Linux-Computers in Azure (klassisches Bereitstellungsmodell).
services: virtual-machines-linux
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: c8a963fa-6b2a-4c7a-a1f4-7793adb02b19
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/06/2016
ms.author: cjiang
ms.openlocfilehash: 581fbaa477bd603fea5fdc0ef77c6ef7498b7897
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2018
---
# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Behandeln von Problemen beim Erstellen eines neuen virtuellen Linux-Computers in Azure (klassisches Bereitstellungsmodell)
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

> [!IMPORTANT] 
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager- und klassische Bereitstellung](../../../resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells. Die Resource Manager-Version dieses Artikels finden Sie [hier](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Sammeln von Überwachungsprotokollen
Sammeln Sie zur Problembehandlung zunächst die Überwachungsprotokolle, um den Fehler zu ermitteln, auf den das Problem zurückzuführen ist.

Klicken Sie im Azure-Portal auf **Durchsuchen** > **Virtuelle Computer** > *Ihr virtueller Windows-Computer* > **Einstellungen** > **Überwachungsprotokolle**.

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y** : Bei einem generalisierten Linux-Betriebssystem, das mit der Generalisierungseinstellung hochgeladen und/oder erfasst wird, treten keine Fehler auf. Analog dazu gilt: Bei einem spezialisierten Linux-Betriebssystem, das mit der Spezialisierungseinstellung hochgeladen und/oder erfasst wird, treten keine Fehler auf.

**Uploadfehler:**

**N<sup>1</sup>:** Wenn ein generalisiertes Linux-Betriebssystem als spezialisiertes Betriebssystem hochgeladen wird, tritt bei der Bereitstellung ein Timeoutfehler auf, da der virtuelle Computer nicht über die Bereitstellungsphase hinauskommt.

**N<sup>2</sup>:** Wenn ein spezialisiertes Linux-Betriebssystem als generalisiertes Betriebssystem hochgeladen wird, tritt ein Bereitstellungsfehler auf, da der neue virtuelle Computer mit dem ursprünglichen Computernamen, Benutzernamen und Kennwort ausgeführt wird.

**Lösung:**

Laden Sie zur Behebung dieser Fehler die ursprüngliche (lokal verfügbare) virtuelle Festplatte mit der gleichen Einstellung (generalisiert/spezialisiert) hoch, die auch für das Betriebssystem verwendet wird. Beim Hochladen als generalisiertes Image muss zuerst „-deprovision“ ausgeführt werden. Weitere Informationen finden Sie unter [Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält](create-upload-vhd-classic.md) .

**Erfassungsfehler:**

**N<sup>3</sup>:** Wenn ein generalisiertes Linux-Betriebssystem als spezialisiertes Betriebssystem erfasst wird, tritt bei der Bereitstellung ein Timeoutfehler auf, da der ursprüngliche virtuelle Computer als generalisiert gekennzeichnet und somit nicht verwendbar ist.

**N<sup>4</sup>:** Wenn ein spezialisiertes Linux-Betriebssystem als generalisiertes Betriebssystem erfasst wird, tritt ein Bereitstellungsfehler auf, da der neue virtuelle Computer mit dem ursprünglichen Computernamen, Benutzernamen und Kennwort ausgeführt wird. Darüber hinaus ist der ursprüngliche virtuelle Computer als spezialisiert gekennzeichnet und somit nicht verwendbar.

**Lösung:**

Löschen Sie zur Behebung dieser Fehler das aktuelle Image über das Portal, und [erfassen Sie es auf der Grundlage der aktuellen VHDs erneut](capture-image-classic.md). Verwenden Sie dabei die gleiche Einstellung (generalisiert/spezialisiert), die auch für das Betriebssystem verwendet wird.

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problem: Benutzerdefiniertes Image/Katalogimage/Marketplace-Image; Zuordnungsfehler
Dieser Fehler tritt auf, wenn die Anforderung für einen neuen virtuellen Computer an einen Cluster gesendet wird, der entweder nicht über genügend Speicherplatz verfügt oder die angeforderte VM-Größe nicht unterstützt. In einem einzelnen Clouddienst können keine unterschiedlichen VM-Serien verwendet werden. Wenn Sie also einen neuen virtuellen Computer mit einer Größe erstellen möchten, die vom Clouddienst nicht unterstützt wird, tritt ein Verarbeitungsfehler auf.

Je nach den Einschränkungen des Clouddiensts, den Sie zum Erstellen des neuen virtuellen Computers verwenden, liegt unter Umständen eine von zwei Fehlerursachen vor:

**Ursache 1:** Der Clouddienst ist mit einem bestimmten Cluster oder mit einer Affinitätsgruppe (und dadurch standardmäßig mit einem bestimmten Cluster) verknüpft. In dieser Affinitätsgruppe wird also versucht, neue Anforderungen von Computeressourcen in dem Cluster auszuführen, in dem auch die vorhandenen Ressourcen gehostet werden. Der Cluster unterstützt aber unter Umständen die angeforderte VM-Größe nicht oder verfügt nicht über genügend Speicherplatz, was einen Zuordnungsfehler zur Folge hat. Dies gilt unabhängig davon, ob die neuen Ressourcen über einen neuen Clouddienst oder einen vorhandenen Clouddienst erstellt werden.

**Lösung 1:**

* Erstellen Sie einen neuen Clouddienst, und ordnen Sie ihn einer Region oder einem regionsbasierten virtuellen Netzwerk zu.
* Erstellen Sie in dem neuen Clouddienst einen neuen virtuellen Computer.
  Sollte bei der Erstellung eines neuen Clouddiensts ein Fehler auftreten, wiederholen Sie den Vorgang zu einem späteren Zeitpunkt, oder ändern Sie die Region für den Clouddienst.

> [!IMPORTANT]
> Falls Sie erfolglos versucht haben, einen neuen virtuellen Computer in einem vorhandenen Clouddienst zu erstellen, und für Ihren neuen virtuellen Computer einen neuen Clouddienst erstellen mussten, haben Sie die Möglichkeit, alle Ihre virtuellen Computer im gleichen Clouddienst zusammenzufassen. Löschen Sie hierzu die virtuellen Computer im vorhandenen Clouddienst, und erfassen Sie sie auf der Grundlage ihrer Datenträger im neuen Clouddienst neu. Berücksichtigen Sie dabei allerdings, dass der neue Clouddienst einen neuen Namen und eine neue VIP besitzt und diese für alle Abhängigkeiten aktualisiert werden müssen, die derzeit diese Informationen für den vorhandenen Clouddienst verwenden.
> 
> 

**Ursache 2:** Der Clouddienst ist einem bestimmten virtuellen Netzwerk zugeordnet, das mit einer Affinitätsgruppe (und dadurch standardmäßig mit einem bestimmten Cluster) verknüpft ist. In dieser Affinitätsgruppe wird versucht, alle neuen Anforderungen von Computeressourcen in dem Cluster auszuführen, in dem auch die vorhandenen Ressourcen gehostet werden. Der Cluster unterstützt aber unter Umständen die angeforderte VM-Größe nicht oder verfügt nicht über genügend Speicherplatz, was einen Zuordnungsfehler zur Folge hat. Dies gilt unabhängig davon, ob die neuen Ressourcen über einen neuen Clouddienst oder einen vorhandenen Clouddienst erstellt werden.

**Lösung 2:**

* Erstellen Sie ein neues regionales virtuelles Netzwerk.
* Erstellen Sie den neuen virtuellen Computer in dem neuen virtuellen Netzwerk.
* [Verbinden Sie Ihr vorhandenes virtuelles Netzwerk](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) mit dem neuen virtuellen Netzwerk. Weitere Informationen zu regionalen virtuellen Netzwerken finden Sie [hier](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/). Alternativ dazu können Sie [Ihr auf einer Affinitätsgruppe basierendes virtuelles Netzwerk zu einem regionalen virtuellen Netzwerk migrieren](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)und anschließend den neuen virtuellen Computer erstellen.

## <a name="next-steps"></a>Nächste Schritte
Wenn beim Starten eines beendeten virtuellen Linux-Computers oder beim Ändern der Größe eines vorhandenen virtuellen Linux-Computers Probleme in Azure auftreten, finden Sie Informationen unter [Behandeln von Problemen beim Neustart oder Ändern der Größe eines vorhandenen virtuellen Linux-Computers in Azure (klassisches Bereitstellungsmodell)](restart-resize-error-troubleshooting.md).

