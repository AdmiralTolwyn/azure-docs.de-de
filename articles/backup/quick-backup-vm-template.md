---
title: 'Schnellstart: Sichern virtueller Computer mit Resource Manager-Vorlagen'
description: Erfahren Sie, wie Sie Ihre virtuellen Computer mit Azure Resource Manager-Vorlagen sichern.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: c40dc7ef8fc55acade709b1ffbbd86ff306f7f0e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79459241"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Sichern einer Azure-VM mit einer Resource Manager-Vorlage

Mit [Azure Backup](backup-overview.md) können Sie lokale Computer und Apps sowie virtuelle Azure-Computer sichern. In diesem Artikel wird gezeigt, wie Sie eine Azure-VM mit einer Resource Manager-Vorlage und Azure PowerShell sichern. In dieser Schnellstartanleitung geht es um die Bereitstellung einer Resource Manager-Vorlage zum Erstellen eines Recovery Services-Tresors. Weitere Informationen zur Entwicklung von Resource Manager-Vorlagen finden Sie in der [Resource Manager-Dokumentation](/azure/azure-resource-manager/) und der [Vorlagenreferenz](/azure/templates/microsoft.recoveryservices/allversions).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Alternativ können Sie einen virtuellen Computer mithilfe von [Azure PowerShell](./quick-backup-vm-powershell.md), der [Azure CLI](quick-backup-vm-cli.md) oder des [Azure-Portals](quick-backup-vm-portal.md) sichern.

## <a name="create-a-vm-and-recovery-services-vault"></a>Erstellen einer VM und eines Recovery Services-Tresors

Ein [Recovery Services-Tresor](backup-azure-recovery-services-vault-overview.md) ist ein logischer Container, in dem Sicherungsdaten für geschützte Ressourcen wie Azure-VMs gespeichert werden. Beim Ausführen eines Sicherungsauftrags wird im Recovery Services-Tresor ein Wiederherstellungspunkt erstellt. Sie können einen dieser Wiederherstellungspunkte dann verwenden, um Daten für einen bestimmten Zeitpunkt wiederherzustellen.

### <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Mit dieser Vorlage können Sie eine einfache Windows-VM und einen Recovery Services-Tresor bereitstellen, der zum Schutz mit der DefaultPolicy konfiguriert ist.

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json" range="1-247" highlight="221-245":::

In der Vorlage sind die folgenden Ressourcen definiert:

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.RecoveryServices/vaults**](/azure/templates/microsoft.recoveryservices/vaults)
- [**Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems)

### <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Wählen Sie zum Bereitstellen der Vorlage **Jetzt testen** aus, um die Azure Cloud Shell zu öffnen, und fügen Sie anschließend das folgende PowerShell-Skript in das Shellfenster ein. Klicken Sie zum Einfügen des Codes mit der rechten Maustaste auf das Shell-Fenster, und wählen Sie **Einfügen** aus.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

In diesem Schnellstart wird Azure PowerShell verwendet, um die Resource Manager-Vorlage bereitzustellen. Auch das [Azure-Portal](../azure-resource-manager/templates/deploy-portal.md), die [Azure-Befehlszeilenschnittstelle](../azure-resource-manager/templates/deploy-cli.md) und die [REST-API](../azure-resource-manager/templates/deploy-rest.md) können zum Bereitstellen von Vorlagen verwendet werden.

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

### <a name="start-a-backup-job"></a>Starten eines Sicherungsauftrags

Die Vorlage erstellt einen virtuellen Computer und aktiviert dort die Sicherung. Nachdem Sie die Vorlage bereitgestellt haben, müssen Sie einen Sicherungsauftrag starten. Weitere Informationen finden Sie unter [Starten eines Sicherungsauftrags](./quick-backup-vm-powershell.md#start-a-backup-job).

### <a name="monitor-the-backup-job"></a>Überwachen des Sicherungsauftrags

Informationen zum Überwachen des Sicherungsauftrags finden Sie unter [Überwachen des Sicherungsauftrags](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Bereinigen der Bereitstellung

Wenn der virtuelle Computer nicht mehr gesichert werden muss, können Sie ihn bereinigen.

- Wenn Sie das Wiederherstellen des virtuellen Computers ausprobieren möchten, überspringen Sie die Bereinigung.
- Wenn Sie einen vorhandenen virtuellen Computer verwendet haben, können Sie das letzte Cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) überspringen, um die Ressourcengruppe und den VM beizubehalten.

Deaktivieren Sie den Schutz, und entfernen Sie die Wiederherstellungspunkte und den Tresor. Löschen Sie anschließend die Ressourcengruppe und die zugehörigen VM-Ressourcen wie folgt:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Recovery Services-Tresor erstellt, den Schutz für einen virtuellen Computer aktiviert und den ersten Wiederherstellungspunkt erstellt.

- [Erfahren Sie, wie](tutorial-backup-vm-at-scale.md) Sie im Azure-Portal VMs sichern.
- [Erfahren Sie, wie](tutorial-restore-disk.md) Sie einen virtuellen Computer schnell wiederherstellen.
- Informieren Sie sich über das [Erstellen von Resource Manager-Vorlagen](../azure-resource-manager/templates/template-tutorial-create-first-template.md).
