---
title: Azure-Hybridvorteil für Windows Server | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Vorteile der Windows Software Assurance optimal nutzen, um lokale Lizenzen in Azure zu verwenden.
services: virtual-machines-windows
documentationcenter: ''
author: kmouss
manager: jeconnoc
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/22/2017
ms.author: kmouss
ms.openlocfilehash: bfad3ff71be07026cf4a7dd6ad75df399349f844
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Azure-Hybridvorteil für Windows Server
Für Kunden mit Software Assurance ermöglicht der Azure-Hybridvorteil für Windows Server die Verwendung der lokalen Windows Server-Lizenzen und die Ausführung von virtuellen Windows-Computern in Azure zu geringeren Kosten. Sie können den Azure-Hybridvorteil für Windows Server dazu nutzen, neue virtuelle Computer über ein beliebiges von Azure unterstütztes Windows Server-Plattformimage oder benutzerdefiniertes Windows-Image bereitzustellen. In diesem Artikel werden die Schritte zum Bereitstellen der neuen VMs mit dem Azure-Hybridvorteil für Windows Server und das Aktualisieren von vorhandenen, ausgeführten VMs beschrieben. Weitere Informationen zum Azure-Hybridvorteil für die Windows Server-Lizenzierung und den Kosteneinsparungen finden Sie auf der Seite zum [Azure-Hybridvorteil für die Windows Server-Lizenzierung](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!IMPORTANT]
> Die „[HUB]“ Windows Server-Legacyimages, die für Kunden mit Enterprise Agreement im Azure Marketplace veröffentlicht wurden, wurden ab dem 11.09.2017 außer Betrieb genommen. Verwenden Sie im Portal das standardmäßige Windows Server-Image mit der Option „Sparen Sie Geld“, um von dem Azure-Hybridvorteil für Windows Server zu profitieren. Weitere Informationen finden Sie in [diesem Artikel](https://support.microsoft.com/en-us/help/4036360/retirement-azure-hybrid-use-benefit-images-for-ea-subscriptions).
>

> [!NOTE]
> Der Azure-Hybridvorteil für Windows Server mit VMs, bei denen zusätzliche Software wie SQL Server oder Marketplace-Images eines Drittanbieters in Rechnung gestellt werden, wird nicht fortgeführt. Wenn ein 409-Fehler angezeigt wird, z.B. dass das Ändern der Eigenschaft „LicenseType“ nicht zulässig ist, versuchen Sie, einen neuen virtuellen Windows Server-Computer zu konvertieren oder bereitzustellen, für den zusätzliche Softwarekosten gelten, was in der jeweiligen Region möglicherweise nicht unterstützt wird. Gleiches gilt, wenn bei der Suche nach der Portalkonfigurationsoption für die Konvertierung diese VM nicht angezeigt wird.
>


> [!NOTE]
> Bei klassischen VMs wird lediglich die Bereitstellung von neuen VMs über lokale benutzerdefinierte Images unterstützt. Um die in diesem Artikel genannten unterstützten Funktionen nutzen zu können, müssen Sie klassische VMs zunächst zum Resource Manager-Modell migrieren.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Möglichkeiten zur Nutzung des Azure-Hybridvorteils für Windows Server
Es gibt mehrere Möglichkeiten, um virtuelle Windows-Computer mit dem Azure-Hybridvorteil zu nutzen:

1. Sie können VMs über eines der bereitgestellten [Windows Server-Images im Azure Marketplace](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview) bereitstellen.
2. Sie können [eine benutzerdefinierte VM hochladen](#upload-a-windows-vhd) und [die Bereitstellung per Resource Manager-Vorlage durchführen](#deploy-a-vm-via-resource-manager) oder dazu [Azure PowerShell](#detailed-powershell-deployment-walkthrough) nutzen.
3. Sie können einen vorhandenen virtuellen Computer zwischen der Ausführung mit dem Azure-Hybridvorteil umschalten und konvertieren oder bei Bedarf für Windows Server bezahlen.
4. Sie können auch eine neue VM-Skalierungsgruppe mit dem Azure-Hybridvorteil für Windows Server bereitstellen.

> [!NOTE]
> Die Konvertierung einer vorhandenen VM-Skalierungsgruppe für die Nutzung des Azure-Hybridvorteils für Windows Server wird nicht unterstützt.
>

## <a name="deploy-a-vm-from-a-windows-server-marketplace-image"></a>Bereitstellen einer VM über ein Windows Server-Marketplace-Image
Der Azure-Hybridvorteil für Windows Server ist für alle Windows Server-Images, die über den Azure Marketplace erhältlich sind, aktiviert. Hierzu zählen z.B. Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 und Windows Server 2008 SP1. Mithilfe dieser Images können VMs direkt aus dem Azure-Portal, mithilfe von Resource Manager-Vorlagen, über Azure PowerShell oder andere SDKs bereitgestellt werden.

Sie können diese Images direkt über das Azure-Portal bereitstellen. Zeigen Sie die Liste mit den Images zur Verwendung in Resource Manager-Vorlagen und für Azure PowerShell wie folgt an:

### <a name="powershell"></a>PowerShell
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
Sie können die Schritte zum [Erstellen eines virtuellen Windows-Computers mit PowerShell](#https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) durchführen und die Eigenschaft „LicenseType“ mit dem Wert „Windows_Server“ übergeben. Diese Option ermöglicht die Verwendung Ihrer vorhandenen Windows Server-Lizenz in Azure.

### <a name="portal"></a>Portal
Sie können die Schritte zum [Erstellen eines virtuellen Windows-Computer mit dem Azure-Portal](#https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) durchführen, und die Option zur Verwendung Ihrer vorhandenen Windows Server-Lizenz auswählen.

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Konvertieren eines vorhandenen virtuellen Computers mit Azure-Hybridvorteil für Windows Server
Wenn Sie einen vorhandenen virtuellen Computer konvertieren möchten, um den Azure-Hybridvorteil für Windows Server zu nutzen, können Sie den Lizenztyp des virtuellen Computers wie folgt aktualisieren:

### <a name="convert-to-using-azure-hybrid-benefit-for-windows-server"></a>Zur Nutzung des Azure-Hybridvorteils für Windows Server konvertieren
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="convert-back-to-pay-as-you-go"></a>Zurück zur nutzungsbasierten Bezahlung konvertieren
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="portal"></a>Portal
Über das VM-Blatt im Portal können Sie den virtuellen Computer zur Verwendung des Azure-Hybridvorteils konvertieren, indem Sie die Option „Konfiguration“ auswählen und die Option „Azure-Hybridvorteil“ aktivieren.

> [!NOTE]
> Falls die Option zum Aktivieren des Azure-Hybridvorteils nicht unter „Konfiguration“ angezeigt wird, liegt dies daran, dass die Konvertierung für den ausgewählten VM-Typ noch nicht unterstützt wird (z.B. eine VM, die aus einem benutzerdefinierten Image oder aus einem Image mit zusätzlicher kostenpflichtiger Software wie SQL Sever oder Azure Marketplace-Drittanbietersoftware erstellt wurde).
>

## <a name="upload-a-windows-server-vhd"></a>Hochladen einer Windows Server-VHD
Für die Bereitstellung eines virtuellen Windows Server-Computers in Azure müssen Sie zunächst eine virtuelle Festplatte (VHD) erstellen, die den Windows-Basisbuild enthält. Diese virtuelle Festplatte muss entsprechend über Sysprep vorbereitet werden, bevor Sie sie in Azure hochladen. Informieren Sie sich über die [VHD-Anforderungen und den Sysprep-Prozess](upload-generalized-managed.md). Weitere Informationen finden Sie auch unter [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Sysprep-Unterstützung für Serverrollen). Sichern Sie den virtuellen Computer vor dem Ausführen von Sysprep. 

Nachdem Sie Ihre VHD vorbereitet haben, laden Sie sie wie folgt in Ihr Azure Storage-Konto hoch:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint Server und Dynamics können ebenfalls Ihre Software Assurance-Lizenzierung verwenden. Sie müssen weiterhin das Windows Server-Image vorbereiten, indem Sie die Anwendungskomponenten installieren, die entsprechenden Lizenzschlüssel bereitstellen und anschließend das Datenträgerimage in Azure hochladen. Lesen Sie in der entsprechenden Dokumentation die Informationen zum Ausführen von Sysprep mit Ihrer Anwendung, etwa [Überlegungen zur Installation von SQL Server mit Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) oder [Build a SharePoint Server 2016 Reference Image (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx) (Erstellen eines SharePoint Server 2016-Referenzimages [Sysprep]).
>
>

Informieren Sie sich auch über das [Hochladen der VHD in Azure](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account).

## <a name="deploy-a-vm-via-resource-manager-template"></a>Bereitstellen virtueller Computer mit Resource Manager-Vorlagen
In den Resource Manager-Vorlagen muss ein zusätzlicher Parameter für `licenseType` angegeben werden. Informieren Sie sich weiter über das [Erstellen von Azure Resource Manager-Vorlagen](../../resource-group-authoring-templates.md). Nachdem Sie die virtuelle Festplatte in Azure hochgeladen haben, bearbeiten Sie die Resource Manager-Vorlage, um den Lizenztyp als Teil des Computeanbieters einzuschließen, und stellen die Vorlage als normale Vorlage bereit:

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-a-vm-via-powershell-quickstart"></a>Schnellstartanleitung für das Bereitstellen eines virtuellen Computers über PowerShell
Beim Bereitstellen der Windows Server-VM über PowerShell ist ein zusätzlicher Parameter für `-LicenseType` vorhanden. Nachdem Sie die virtuelle Festplatte in Azure hochgeladen haben, erstellen Sie einen virtuellen Computer mit `New-AzureRmVM` und geben den Lizenzierungstyp wie folgt an:

Für Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

Ein ausführlicheres Handbuch zu den verschiedenen Schritten finden Sie unter [Erstellen eines virtuellen Windows-Computers mit PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Überprüfen, ob für den virtuellen Computer der Lizenzierungsvorteil genutzt wird
Sobald Sie die VM über PowerShell, die Resource Manager-Vorlage oder das Portal bereitgestellt haben, können Sie den Lizenztyp mit `Get-AzureRmVM` wie folgt überprüfen:

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Die Ausgabe sieht in etwa wie das folgende Beispiel für Windows Server aus:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Diese Ausgabe steht im Gegensatz zu der folgenden VM, die ohne Azure-Hybridvorteil für die Windows Server-Lizenzierung bereitgestellt wird:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="list-all-azure-hybrid-benefit-for-windows-server-vms-in-a-subscription"></a>Auflisten des gesamten Azure-Hybridvorteilsangebots für Windows Server-VMs in einem Abonnement

Um alle mit dem Azure-Hybridvorteil für Windows Server bereitgestellten virtuellen Computer anzuzeigen und aufzulisten, können Sie den folgenden Befehl aus Ihrem Abonnement ausführen:

```powershell
$vms = Get-AzureRMVM 
foreach ($vm in $vms) {"VM Name: " + $vm.Name, "   Azure Hybrid Benefit for Windows Server: "+ $vm.LicenseType}
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Bereitstellen einer VM-Skalierungsgruppe mit dem Azure-Hybridvorteil für Windows Server
In den Resource Manager-Vorlagen Ihrer VM-Skalierungsgruppe muss ein zusätzlicher Parameter für `licenseType` angegeben werden. Informieren Sie sich weiter über das [Erstellen von Azure Resource Manager-Vorlagen](../../resource-group-authoring-templates.md). Bearbeiten Sie Ihre Resource Manager-Vorlage so, dass die Eigenschaft „licenseType“ als Teil von „virtualMachineProfile“ der Skalierungsgruppe hinzugefügt wird, und stellen Sie wie gewohnt Ihre Vorlage bereit. Sehen Sie sich hierzu das folgende Beispiel unter Verwendung eines Windows Server 2016-Image an:


```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
Sie können auch [eine VM-Skalierungsgruppe erstellen und bereitstellen](#https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-create) und die Eigenschaft „LicenseType“ festlegen.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu [Einsparungen durch den Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

Weitere ausführliche Informationen finden Sie unter [Azure-Hybridvorteil für die Windows Server-Lizenzierung](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit).

Weitere Informationen zum [Verwenden von Resource Manager-Vorlagen](../../azure-resource-manager/resource-group-overview.md)

Weitere Informationen zur [Nutzung des Azure-Hybridvorteils für Windows Server und Azure Site Recovery für eine noch kosteneffizientere Anwendungsmigration zu Azure](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)

Weitere Informationen zum [Bereitstellen von Windows 10 unter Azure mit mehrinstanzenfähigen Hostingrechten](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)

Weitere Informationen finden Sie unter [Häufig gestellte Fragen](#https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/).
