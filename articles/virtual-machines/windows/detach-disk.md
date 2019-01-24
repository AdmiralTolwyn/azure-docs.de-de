---
title: Trennen eines Datenträgers mit Daten von einem virtuellen Windows-Computer – Azure | Microsoft Docs
description: Trennen eines Datenträgers unter Verwendung des Resource Manager-Bereitstellungsmodells von einem virtuellen Computer in Azure
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.component: disks
ms.openlocfilehash: 69db6f4e89720d2c0313628045e4ae0eda99013f
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465546"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Trennen eines Datenträgers von einem virtuellen Windows-Computer

Wenn Sie einen Datenträger, der an einen virtuellen Computer angefügt ist, nicht mehr benötigen, können Sie ihn leicht trennen. Dadurch wird der Datenträger von dem virtuellen Computer entfernt, aber nicht aus dem Speicher.

> [!WARNING]
> Beim Trennen eines Datenträgers wird dieser nicht automatisch gelöscht. Wenn Sie Storage Premium abonniert haben, fallen auch weiterhin Speichergebühren für den Datenträger an. Weitere Informationen finden Sie unter [Preisgestaltung und Abrechnung bei der Verwendung von Storage Premium](premium-storage.md#pricing-and-billing).
>
>

Wenn Sie die vorhandenen Daten erneut auf dem Datenträger verwenden möchten, können Sie ihn erneut an denselben virtuellen Computer oder an einen anderen anfügen.


## <a name="detach-a-data-disk-using-powershell"></a>Trennen eines Datenträgers mit PowerShell

Sie können mit PowerShell einen Datenträger *im laufenden Betrieb* entfernen. Stellen Sie jedoch sicher, dass der Datenträger nicht aktiv verwendet wird, bevor Sie ihn vom virtuellen Computer trennen.

In diesem Beispiel wird der Datenträger namens **myDisk** vom virtuellen Computer **myVM** in der Ressourcengruppe **myResourceGroup** entfernt. Sie entfernen zunächst den Datenträger mit dem Cmdlet [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk). Anschließend wird der Status des virtuellen Computers über das Cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) aktualisiert, um den Vorgang zum Entfernen des Datenträgers abzuschließen.

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

Der Datenträger verbleibt im Speicher, ist jedoch nicht mehr an einen virtuellen Computer angefügt.


## <a name="detach-a-data-disk-using-the-portal"></a>Trennen eines Datenträgers im Portal

1. Wählen Sie im Menü auf der linken Seite die Option **Virtuelle Computer** aus.
2. Wählen Sie den virtuellen Computer mit dem zu trennenden Datenträger aus, und klicken Sie auf **Beenden**, um die Zuordnung der VM aufzuheben.
3. Klicken Sie im Bereich für den virtuellen Computer auf **Datenträger**.
4. Wählen Sie oben im Bereich **Datenträger** die Option **Bearbeiten** aus.
5. Klicken Sie im Bereich **Datenträger** ganz rechts neben dem Datenträger, den Sie trennen möchten, auf die Schaltfläche „Trennen“ (![Detach button image](./media/detach-disk/detach.png)).
5. Nachdem der Datenträger entfernt wurde, klicken Sie oben auf dem Bereich auf **Speichern**.
6. Klicken Sie im Bereich für den virtuellen Computer auf **Übersicht** und dann oben im Bereich auf die Schaltfläche **Starten**, um die VM neu zu starten.

Der Datenträger verbleibt im Speicher, ist jedoch nicht mehr an einen virtuellen Computer angefügt.

## <a name="next-steps"></a>Nächste Schritte
Wenn Sie den Datenträger wiederverwenden möchten, können Sie ihn einfach [an einen anderen virtuellen Computer anfügen](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

