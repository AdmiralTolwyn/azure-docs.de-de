---
title: 'Bereitstellungsmodi: Resource Manager und Dienstverwaltung (klassisch) | Microsoft-Dokumentation'
description: Grundlegendes zu den Unterschieden zwischen dem Modell der Ressourcen-Manager-Bereitstellung und dem Modell der klassischen Bereitstellung
services: virtual-network
documentationcenter: 
author: telmosampaio
manager: carmonm
editor: 
tags: azure-resource-manager,azure-service-management
redirect_url: ./azure-resource-manager/resource-manager-deployment-model
ms.assetid: 18a235d8-38ac-4886-9e56-b3855c73ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: telmos
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 6dd2433bf744dd207375e92fd6f24b14cd3f828c
ms.lasthandoff: 04/27/2017


---
# <a name="azure-deployment-models"></a>Azure-Bereitstellungsmodelle
Die Azure-Plattform befindet sich in einer Übergangsphase.  Unabhängig davon, ob Sie Azure gerade erst kennen lernen oder schon seit Jahren verwenden, sollten Sie sich unbedingt mit einigen der wesentlichen Änderungen auseinandersetzen, die wir während dieses Übergangs an der Plattform vornehmen.

Alle Azure-Ressourcen unterstützen mindestens eines der beiden folgenden Bereitstellungsmodelle:

* **Ressourcen-Manager:** Dies ist das neueste Bereitstellungsmodell für Azure-Ressourcen. Die meisten neueren Ressourcen unterstützen dieses Bereitstellungsmodell bereits, und letztendlich wird das für alle Ressourcen der Fall sein.   
* **Klassisch:** Dieses Modell wird von den meisten derzeit vorhandenen Azure-Ressourcen unterstützt. Neue Ressourcen, die in Azure hinzugefügt werden, unterstützen dieses Modell nicht.

Die Dokumentation für die einzelnen Azure-Ressourcen legt dar, mit welchen Dienstmodellen sie jeweils erstellt werden können.

## <a name="why-does-this-matter"></a>Warum ist dies wichtig?
Aus folgenden Gründen:

* Die von Ihnen verwendeten Features der Azure-Plattform unterscheiden sich in diesen beiden Modellen.  Beispielsweise können Ressourcen, die mit dem Resource Manager-Bereitstellungsmodell angelegt werden, mithilfe von [Azure Resource Manager-Vorlagen](azure-resource-manager/resource-group-overview.md#template-deployment) erstellt werden. Dies gilt jedoch nicht für Ressourcen, die mit dem klassischen Bereitstellungsmodell erstellt werden.
* Die jeweiligen Features oder Verhalten der Azure-Ressource können sich zwischen den beiden Modellen unterscheiden oder nur in einem der beiden Modelle vorhanden sein.  Beispielsweise ist der Lastenausgleich *implizit* für Datenverkehr zwischen virtuellen Computern, die mit dem klassischen Bereitstellungsmodell erstellt wurden, da virtuelle Computer Mitglieder eines Azure-Clouddiensts sind und die Last automatisch zwischen virtuellen Maschinen innerhalb eines Clouddiensts verteilt wird. Virtuelle Computer, die mit dem Ressourcen-Manager erstellt werden, sind keine Mitglieder eines Clouddiensts, sodass eine separate Azure Load Balancer-Ressource *explizit* für den Lastenausgleich zwischen mehreren virtuellen Computer erstellt werden muss.  
* Das Erstellen, Konfigurieren und Verwalten Ihrer Azure-Ressourcen erfolgt in diesen beiden Modellen unterschiedlich.
* Ressourcen, die mit dem einem Bereitstellungsmodell  erstellt wurden, können nicht unbedingt mit Ressourcen zusammenarbeiten, die mit dem anderen Bereitstellungsmodell erstellt wurden. Beispielsweise können Azure Virtual Machines, die mithilfe des einen Bereitstellungsmodells erstellt wurden, nur mit Azure Virtual Networks verbunden werden, die mit dem gleichen Bereitstellungsmodell erstellt wurden.    

Beiden Bereitstellungsmodellen liegt jeweils eine Anwendungsprogrammierschnittstelle (Application Programming Interface, API) für die einzelnen Ressourcen zugrunde.  Es gibt eine [Resource Manager-API](https://msdn.microsoft.com/library/azure/dn948464.aspx) für das Resource Manager-Bereitstellungsmodell und eine [Dienstverwaltungs-API](https://msdn.microsoft.com/library/azure/ee460799.aspx) für das klassische Bereitstellungsmodell. Entwickler können Code schreiben, um mit diesen APIs *direkt*zu interagieren.  

In der Regel interagieren IT-Spezialisten jedoch *indirekt* mit diesen APIs: über ein grafisches Portal in einem Webbrowser, über Azure PowerShell-Cmdlets auf einem Windows-Computer oder über die Azure-Befehlszeilenschnittstelle (CLI) auf einem Computer unter Windows, OS X oder Linux. Alle drei dieser indirekten Methoden, die von IT-Spezialisten verwendet werden, interagieren direkt mit den APIs. Wenn also neue Funktionen für die Azure-Plattform oder für Azure-Ressourcen eingeführt werden, stehen sie immer zuerst direkt über die API zur Verfügung. Die indirekten Methoden erhalten Unterstützung für die neuen Ressourcen und Funktionen, nachdem die API zur Verfügung gestellt wurde.  

In den folgenden Abschnitten wird erläutert, wie Azure-Ressourcen mit den verschiedenen Bereitstellungsmodellen über die drei indirekten Methoden konfiguriert werden.

## <a name="portals"></a>Portale
Azure verfügt über zwei Portale:

* **[Azure-Portal](https://manage.windowsazure.com):** Wenn Sie Azure schon seit einer Weile verwenden, kennen Sie dieses Portal. Es dient zum Erstellen und Konfigurieren älterer Azure-Ressourcen, die das klassische Bereitstellungsmodell unterstützen. Sie können es nicht zum Erstellen oder Konfigurieren von Ressourcen verwenden, die nur den Ressourcen-Manager unterstützen. 
* **[Azure-Vorschauportal](https://azure.microsoft.com/overview/preview-portal/):** Wenn Sie eine neuere Azure-Ressource nutzen, haben Sie wahrscheinlich dieses Portal verwendet. Es kann zum Erstellen und Konfigurieren einiger Azure-Ressourcen verwendet werden. Sie werden damit letztendlich alle Azure-Ressourcen erstellen und konfigurieren können. Für einige Ressourcen, die beide Bereitstellungsmodelle unterstützen, kann dieses Portal zum Erstellen und Konfigurieren einer Ressource mit einem beliebigen der beiden Bereitstellungsmodelle verwendet werden. 

Einige Ressourcen und Features können nur in entweder dem einem oder dem anderen der beiden Portale erstellt und konfiguriert werden. Einige Ressourcen oder Features können (noch) nicht in einem der Portale erstellt oder konfiguriert werden und lassen sich nur mit PowerShell und/oder der Befehlszeilenschnittstelle konfigurieren. Die Dokumentation für die einzelnen Azure-Ressourcen legt dar, mit welcher Methode sie jeweils erstellt werden können. 

## <a name="powershell"></a>PowerShell
Mit [PowerShell](/powershell/azureps-cmdlets-docs) können Sie eine Befehlszeile verwenden oder Skripts zum Erstellen und Konfigurieren von Azure-Ressourcen auf einem Windows-Computer schreiben.  Einzelne Azure-Ressourcen weisen [Resource Manager-Cmdlets](/powershell/azure/overview), [Dienstverwaltungs-Cmdlets](/powershell/azure/overview?view=azuresmps-3.7.0) oder beides auf.  Einige Ressourcen und Features können nur mit PowerShell oder der Befehlszeilenschnittstelle erstellt und/oder konfiguriert werden. Abhängig von der Ressource stehen Ihnen bei Verwendung der Ressourcen-Manager-PowerShell-Cmdlets zwei Optionen zum Erstellen und Konfigurieren von Azure-Ressourcen zur Verfügung:

* **Nur PowerShell-Cmdlets:** Sie können jede Azure-Ressource einzeln über die Cmdlets für die einzelnen Ressourcen erstellen und konfigurieren. Dies kann über eine Befehlszeile erfolgen oder durch Einschließen mehrerer Befehle in einem PowerShell-Skript, das Sie speichern und versionieren können.
* **PowerShell-Cmdlets mit einer Azure-Ressourcen-Manager-Vorlage:** Sie können PowerShell zum Erstellen von Azure-Ressourcen über eine Azure-Ressourcen-Manager-Vorlage verwenden. Vorlagen können gespeichert und versioniert werden. Weitere Informationen finden Sie im Artikel [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](resource-group-template-deploy.md) . Es gibt mehrere [Azure-Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates/) für gängige Lösungen, die heruntergeladen und geändert werden können.

## <a name="cli"></a>Befehlszeilenschnittstelle (CLI)
Sie können Azure-Ressourcen auf Windows-, OS X- oder Linux-Computern über die Befehlszeilenschnittstelle erstellen und konfigurieren.  Lesen Sie den Artikel [Installieren der Azure-Befehlszeilenschnittstelle](cli-install-nodejs.md) , um die Befehlszeilenschnittstelle auf dem Betriebssystem Ihrer Wahl zu installieren. Wie in PowerShell gibt es verschiedene Befehle, die abhängig davon verwendet werden müssen, ob Sie Ressourcen mit dem [Resource Manager-Bereitstellungsmodell](xplat-cli-azure-resource-manager.md) oder mit dem [klassischen Bereitstellungsmodell (Dienstverwaltung)](virtual-machines/linux/classic/manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) erstellen.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [Resource Manager](azure-resource-manager/resource-group-overview.md).
* Erlernen Sie das [Entwerfen von Vorlagen](best-practices-resource-manager-design-templates.md).


