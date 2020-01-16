---
title: Erstellen einer Momentaufnahme einer virtuellen Festplatte in Azure
description: Erfahren Sie, wie eine Kopie eines virtuellen Azure-Computers als Sicherung oder für die Behandlung von Problemen erstellen.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: bc74a3eea1f99de6080788d6f3fddcac823092dc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75370900"
---
# <a name="create-a-snapshot"></a>Erstellen einer Momentaufnahme

Eine Momentaufnahme ist eine vollständige, schreibgeschützte Kopie einer virtuellen Festplatte (Virtual Hard Drive, VHD). Sie können eine Momentaufnahme eines Betriebssystem- oder VHD-Datenträgers zur Verwendung als Sicherung oder zum Beheben von VM-Problemen erstellen.

Wenn Sie mit der Momentaufnahme einen neuen virtuellen Computer erstellen möchten, wird empfohlen, den virtuellen Computer vor dem Erstellen einer Momentaufnahme ordnungsgemäß herunterzufahren, um alle ausgeführten Prozesse zu löschen.

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals 

Gehen Sie zum Erstellen einer Momentaufnahme wie folgt vor: 
1.  Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Ressource erstellen** aus.
2. Suchen Sie nach **Momentaufnahme**, und wählen Sie diese Option aus.
3. Wählen Sie im Fenster **Momentaufnahme** die Option **Erstellen** aus. Das Fenster **Momentaufnahme erstellen** wird angezeigt.
4. Geben Sie einen **Namen** für die Momentaufnahme ein.
5. Wählen Sie eine vorhandene [Ressourcengruppe](../../azure-resource-manager/management/overview.md#resource-groups) aus, oder geben Sie den Namen einer neuen Ressourcengruppe ein. 
6. Wählen Sie den **Standort** eines Azure-Datencenters aus.  
7. Wählen Sie für **Quelldatenträger** den verwalteten Datenträger aus, für den eine Momentaufnahme erstellt werden soll.
8. Wählen Sie den **Kontotyp** aus, der zum Speichern der Momentaufnahme verwendet werden soll. Wählen Sie **Standard (HDD)** aus, sofern die Momentaufnahme nicht auf einem Hochleistungsdatenträger gespeichert werden muss.
9. Klicken Sie auf **Erstellen**.

## <a name="use-powershell"></a>Verwenden von PowerShell

In den folgenden Schritten wird gezeigt, wie der VHD-Datenträger kopiert und die Momentaufnahmekonfiguration erstellt wird. Sie können dann eine Momentaufnahme des Datenträgers mit dem Cmdlet [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) erstellen. 

 

1. Legen Sie einige Parameter fest: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. Rufen Sie den virtuellen Computer ab:

   ```azurepowershell-interactive
   $vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
   ```

3. Erstellen Sie die Momentaufnahmekonfiguration. In diesem Beispiel wird die Momentaufnahme des Betriebssystemdatenträgers erstellt:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > Wenn Sie die Momentaufnahme in Speicher mit Zonenresilienz speichern möchten, erstellen Sie die Momentaufnahme in einer Region, die [Verfügbarkeitszonen](../../availability-zones/az-overview.md) unterstützt, und beziehen Sie den Parameter `-SkuName Standard_ZRS` ein.   
   
4. Erstellen Sie die Momentaufnahme:

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Nächste Schritte

Erstellt einen virtuellen Computer aus einer Momentaufnahme, indem aus einer Momentaufnahme zuerst ein verwalteter Datenträger erstellt und dieser neue verwaltete Datenträger anschließend als Betriebssystemdatenträger angefügt wird. Weitere Informationen finden Sie im Beispiel unter [Erstellen eines virtuellen Computers aus einer Momentaufnahme mit PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
