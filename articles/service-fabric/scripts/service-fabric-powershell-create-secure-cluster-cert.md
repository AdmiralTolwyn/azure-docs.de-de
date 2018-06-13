---
title: Azure PowerShell-Skriptbeispiel – Erstellen eines Service Fabric-Clusters | Microsoft-Dokumentation
description: Azure PowerShell-Skriptbeispiel – Erstellen eines Service Fabric-Clusters.
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: ad3c51f0f43d63fd784156eca680218850897e8f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31596550"
---
# <a name="create-a-service-fabric-cluster"></a>Erstellen von Service Fabric-Clustern

Dieses Beispielskript erstellt einen Service Fabric-Cluster mit fünf Knoten, der mit einem X.509-Zertifikat geschützt wird.  Der Befehl erstellt ein selbstsigniertes Zertifikat und lädt es in einen neuen Key Vault hoch. Das Zertifikat wird außerdem in ein lokales Verzeichnis kopiert.  Legen Sie den *-OS*-Parameter fest, um die Version von Windows oder Linux auszuwählen, in der die Clusterknoten ausgeführt werden.  Passen Sie die Parameter nach Bedarf an.

Installieren Sie bei Bedarf Azure PowerShell mithilfe der Anleitung im [Azure PowerShell-Handbuch](/powershell/azure/overview), und führen Sie dann `Connect-AzureRmAccount` aus, um eine Verbindung mit Azure herzustellen. 

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung 

Nach dem Ausführen des Skriptbeispiels können mit dem folgenden Befehl die Ressourcengruppe, der Cluster und alle zugehörigen Ressourcen entfernt werden.

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | Erstellt einen neuen Service Fabric-Cluster. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche Azure PowerShell-Beispiele für Azure Service Fabric finden Sie unter [Azure PowerShell-Beispiele](../service-fabric-powershell-samples.md).
