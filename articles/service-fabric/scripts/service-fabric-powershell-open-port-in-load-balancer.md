---
title: "Azure PowerShell-Skriptbeispiel – Öffnen eines Anwendungsports im Lastenausgleich | Microsoft-Dokumentation"
description: "Azure PowerShell-Skriptbeispiel – Öffnen eines Ports im Azure-Lastenausgleichsmodul für eine Service Fabric-Anwendung."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/08/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: c643fc9e575a8e836a361893d78348bbd627a425
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2017
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Öffnen eines Anwendungsports im Azure-Lastenausgleichsmodul

Eine in Azure ausgeführte Service Fabric-Anwendung befindet sich hinter dem Azure-Lastenausgleichsmodul. Mit diesem Beispielskript wird ein Port in einem Azure-Lastenausgleichsmodul geöffnet, um die Kommunikation einer Service Fabric-Anwendung mit externen Clients zu ermöglichen. Passen Sie die Parameter nach Bedarf an. Wenn sich Ihr Cluster in einer Netzwerksicherheitsgruppe befindet, fügen Sie auch [eine Regel für eingehende Netzwerksicherheitsgruppen](service-fabric-powershell-add-nsg-rule.md) hinzu, um eingehenden Datenverkehr zuzulassen.

Wenn Sie das Service Fabric-PowerShell-Modul benötigen, installieren Sie es zusammen mit dem [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Befehl | Hinweise |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Ruft eine Azure-Ressource ab.  |
| [Get-AzureRmLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer) | Ruft das Azure-Lastenausgleichsmodul ab. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig) | Fügt eine Testkonfiguration zu einem Lastenausgleichsmodul hinzu.|
| [Get-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/get-azurermloadbalancerprobeconfig) | Erstellt eine Testkonfiguration für ein Lastenausgleichsmodul. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig) | Fügt eine Regelkonfiguration zu einem Lastenausgleichsmodul hinzu. |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Legt den Zielzustand für ein Lastenausgleichsmodul fest. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche PowerShell-Beispiele für Azure Service Fabric finden Sie unter [Azure PowerShell-Beispiele](../service-fabric-powershell-samples.md).
