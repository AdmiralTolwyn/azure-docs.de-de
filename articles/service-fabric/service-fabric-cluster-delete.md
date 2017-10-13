---
title: "Löschen eines Azure-Clusters und seiner Ressourcen | Microsoft Docs"
description: "Erfahren Sie, wie Sie ein Service Fabric-Cluster vollständig löschen, indem Sie entweder die Ressourcengruppe mit dem Cluster löschen oder Ressourcen selektiv löschen."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: de422950-2d22-4ddb-ac47-dd663a946a7e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/24/2017
ms.author: chackdan
ms.openlocfilehash: 7672aa12421fbe4ad86e7315d6a7a06c2ff5124d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Löschen eines Service Fabric-Clusters und seiner Ressourcen aus Azure
Ein Service Fabric-Cluster besteht, zusätzlich zu der Clusterressource selbst, aus vielen weiteren Azure-Ressourcen. Daher müssen Sie alle Ressourcen löschen, aus denen der Cluster besteht, um einen Service Fabric-Cluster zu löschen.
Dazu stehen Ihnen zwei Möglichkeiten zur Verfügung: Sie können die Ressourcengruppe löschen, in der sich der Cluster befindet (dabei werden die Clusterressource und alle weiteren Ressourcen in der Ressourcengruppe gelöscht). Alternativ können Sie speziell die Clusterressource und die dazugehörigen Ressourcen löschen (jedoch keine anderen Ressourcen in der Ressourcengruppe).

> [!NOTE]
> Die Löschung der Clusterressource löscht **nicht** die weiteren Ressourcen, aus denen sich Ihr Service Fabric-Cluster zusammensetzt.
> 
> 

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>Löschen der gesamten Ressourcengruppe (RG), in der sich der Service Fabric-Cluster befindet
Dies ist der einfachste Weg, um die Löschung aller zu Ihrem Cluster gehörenden Ressourcen zu gewährleisten, einschließlich der Ressourcengruppe. Sie können die Ressourcengruppe mit PowerShell oder über das Azure-Portal löschen. Wenn Ihre Ressourcengruppe über Ressourcen verfügt, die nicht mit dem Service Fabric-Cluster verknüpft sind, können Sie bestimmte Ressourcen löschen.

### <a name="delete-the-resource-group-using-azure-powershell"></a>Löschen der Ressourcengruppe mit Azure PowerShell
Sie können die Ressourcengruppe auch mit den folgenden PowerShell-Cmdlets löschen. Stellen Sie sicher, dass Azure PowerShell 1.0 oder höher auf Ihrem Computer installiert ist. Falls noch nicht erfolgt, sollten Sie die Schritte unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview)

Öffnen Sie ein PowerShell-Fenster und führen Sie die folgenden PS-Cmdlets aus:

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Wenn Sie die Option *-Force* nicht verwenden, erhalten Sie eine Aufforderung, den Löschvorgang zu bestätigen. Bei Bestätigung werden die Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Löschen einer Ressourcengruppe im Azure-Portal
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Navigieren Sie zu dem Service Fabric-Cluster, den Sie löschen möchten.
3. Klicken Sie auf der Clusterseite „Essentials“ (Zusammenfassung) auf den Namen der Ressourcengruppe.
4. Hierdurch wird die Seite **Resource Group Essentials** (Ressourcengruppe Zusammenfassung) geöffnet.
5. Klicken Sie auf **Löschen**.
6. Befolgen Sie die Anleitungen auf dieser Seite, um die Löschung der Ressourcengruppe abzuschließen.

![Ressourcengruppe löschen][ResourceGroupDelete]

## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>Löschen Sie die Clusterressource und die verwendeten Ressourcen, jedoch keine anderen Ressourcen in der Ressourcengruppe
Sind in der Ressourcengruppe nur Ressourcen enthalten, die mit dem Service Fabric-Cluster verknüpft sind, den Sie löschen möchten, ist es einfacher, die gesamte Ressourcengruppe zu löschen. Wenn Sie die einzelnen Ressourcen in der Ressourcengruppe selektiv nacheinander löschen möchten, gehen Sie folgendermaßen vor.

Wenn Sie Ihren Cluster mithilfe des Portals oder der Service Fabric Resource Manager-Vorlagen aus dem Vorlagenkatalog bereitgestellt haben, werden alle vom Cluster verwendeten Ressourcen mit den folgenden zwei Tags gekennzeichnet. Anhand der Tags können Sie entscheiden, welche Ressourcen Sie löschen möchten.

***Tag 1:*** Schlüssel = clusterName, Wert = „Name des Clusters“

***Tag 2:*** Schlüssel = resourceName, Wert = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>Löschen bestimmter Ressourcen im Azure-Portal
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Navigieren Sie zu dem Service Fabric-Cluster, den Sie löschen möchten.
3. Wechseln Sie zu **Alle Einstellungen** auf dem Blatt „Essentials“.
4. Klicken Sie auf dem Blatt „Einstellungen“ unter **Ressourcenverwaltung** auf **Tags**.
5. Klicken Sie auf dem Blatt „Tags“ auf eines der **Tags** , um eine Liste aller Ressourcen mit diesem Tag abzurufen.
   
    ![Ressource Tags][ResourceTags]
6. Wenn die Liste der markierten Ressourcen angezeigt wird, klicken Sie auf jede Ressource, und löschen Sie sie.
   
    ![Markierte Ressourcen][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Löschen der Ressourcen mit Azure PowerShell
Sie können die Ressourcen mit den folgenden Azure PowerShell-Cmdlets einzeln löschen. Stellen Sie sicher, dass Azure PowerShell 1.0 oder höher auf Ihrem Computer installiert ist. Falls noch nicht erfolgt, sollten Sie die Schritte unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview)

Öffnen Sie ein PowerShell-Fenster und führen Sie die folgenden PS-Cmdlets aus:

```powershell
Login-AzureRmAccount
```
Für jede Ressource, die Sie löschen möchten, führen Sie Folgendes aus:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

Führen Sie Folgendes aus, um die Clusterressource zu löschen:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die folgenden Artikel, um mehr über das Upgraden eines Clusters und das Partitionieren von Diensten zu erfahren:

* [Weitere Informationen über Clusterupgrades](service-fabric-cluster-upgrade.md)
* [Weitere Informationen über die Partitionierung statusbehafteter Dienste für maximale Skalierung](service-fabric-concepts-partitioning.md)

<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG
