---
title: Azure Service Fabric-Unterschiede zwischen Linux und Windows | Microsoft-Dokumentation
description: Es wird beschrieben, welche Unterschiede zwischen Azure Service Fabric unter Linux und Azure Service Fabric unter Windows bestehen.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/19/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 7dceb7bb38b1dac778151e197db3b5be49dd568a
ms.openlocfilehash: 25976ba919454e26f1dd7965de5db7c4f80b9355
ms.contentlocale: de-de
ms.lasthandoff: 09/25/2017

---
# <a name="differences-between-service-fabric-on-linux-and-windows"></a>Unterschiede zwischen Service Fabric unter Linux und Windows

Einige Features werden unter Windows unterstützt, aber noch nicht unter Linux. Die Übereinstimmung der Featuresätze wird zu einem späteren Zeitpunkt erreicht sein, und mit jeder Version wird die Lücke kleiner. Zwischen den aktuell verfügbaren Versionen (d.h. zwischen Version 6.0 unter Windows und Version 6.0 unter Linux) bestehen die folgenden Unterschiede: 

* Alle Programmiermodelle befinden sich in der Vorschauphase (Reliable Actors, zustandslose Reliable Services und zustandsbehaftete Reliable Services für Java/C#)
* Envoy (ReverseProxy) befindet sich unter Linux in der Vorschauphase
* Eigenständiges Installationsprogramm für Linux ist unter Linux noch nicht verfügbar
* Konsolenumleitung (für Linux- oder Windows-Produktionscluster nicht unterstützt)
* Fault Analysis Service (FAS) unter Linux
* DNS-Dienst für Service Fabric-Dienste (DNS-Dienst wird für Container unter Linux unterstützt)
* CLI-Befehlsentsprechungen bestimmter PowerShell-Befehle (siehe Liste unten, wobei die meisten Einträge nur für eigenständige Cluster gelten)

Die Entwicklungstools unterscheiden sich bei Windows und Linux ebenfalls. Visual Studio, PowerShell, VSTS und ETW werden unter Windows und Yeoman, Eclipse, Jenkins und LTTng unter Linux verwendet.

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>PowerShell-Cmdlets, die für einen Linux-Service Fabric-Cluster nicht funktionieren

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>Nächste Schritte
* [Vorbereiten Ihrer Entwicklungsumgebung unter Linux](service-fabric-get-started-linux.md)
* [Prepare your development environment on OSX (Vorbereiten Ihrer Entwicklungsumgebung unter OSX)](service-fabric-get-started-mac.md)
* [Erstellen und Bereitstellen Ihrer ersten Service Fabric-Java-Anwendung unter Linux mithilfe von Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Erstellen und Bereitstellen Ihrer ersten Service Fabric-Java-Anwendung unter Linux mithilfe des Service Fabric-Plug-Ins für Eclipse](service-fabric-get-started-eclipse.md)
* [Erstellen der ersten Java-Anwendung unter Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Verwalten von Anwendungen mit der Service Fabric-Befehlszeilenschnittstelle](service-fabric-application-lifecycle-sfctl.md)

