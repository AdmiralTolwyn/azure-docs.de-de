---
title: Azure PowerShell-Skriptbeispiel – Hinzufügen einer Netzwerksicherheitsgruppen-Regel | Microsoft-Dokumentation
description: Azure PowerShell-Skriptbeispiel– fügt eine Netzwerksicherheitsgruppen-Regel zum Zulassen von eingehendem Datenverkehr an einem bestimmten Port hinzu
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 11/28/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 8e3187ba399ffa4d6c0ca863380205f7fbbfaae8
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036113"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Einzufügen einer eingehenden Netzwerksicherheitsgruppen-Regel

Dieses Beispielskript erstellt eine Netzwerksicherheitsgruppen-Regel zum Zulassen von eingehendem Datenverkehr an Port 8081.  Das Skript ruft die Ressource `Microsoft.Network/networkSecurityGroups` ab, in der sich der Cluster befindet, erstellt eine neue Netzwerksicherheits-Konfigurationsregel und aktualisiert die Netzwerksicherheitsgruppe. Passen Sie die Parameter nach Bedarf an.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installieren Sie bei Bedarf Azure PowerShell anhand der Anweisungen im [Azure PowerShell-Handbuch](/powershell/azure/overview). 

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Ruft die Ressource `Microsoft.Network/networkSecurityGroups` ab |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Ruft die Netzwerksicherheitsgruppe anhand des Namens ab|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Fügt einer Netzwerksicherheitsgruppe eine Netzwerksicherheits-Konfigurationsregel hinzu |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Legt den Zielstatus für eine Netzwerksicherheitsgruppe fest|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).
