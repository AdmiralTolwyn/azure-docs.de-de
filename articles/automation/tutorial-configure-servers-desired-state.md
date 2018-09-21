---
title: Konfigurieren von Servern auf einen gewünschten Status und Verwalten der Abweichung mit Azure Automation
description: 'Tutorial: Verwalten von Serverkonfigurationen mit der Azure Automation-Zustandskonfiguration'
services: automation
ms.service: automation
ms.component: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 000875e4e591bcfe94ec99f8b16c8ec40bf52cf4
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45629881"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Konfigurieren von Servern mit einem gewünschten Status und Verwalten der Abweichung mit Azure Automation

Mit der Azure Automation-Zustandskonfiguration können Sie Konfigurationen für Ihre Server festlegen und sicherstellen, dass sich diese Server im Verlauf der Zeit im angegebenen Zustand befinden.

> [!div class="checklist"]
> - Integrieren eines virtuellen Computers in die Azure Automation DSC-Verwaltung
> - Hochladen einer Konfiguration in Azure Automation
> - Kompilieren einer Konfiguration in eine Knotenkonfiguration
> - Zuweisen einer Knotenkonfiguration zu einem verwalteten Knoten
> - Überprüfen des Konformitätsstatus eines verwalteten Knotens

## <a name="prerequisites"></a>Voraussetzungen

Um dieses Lernprogramm abzuschließen, benötigen Sie:

- Ein Azure Automation-Konto. Informationen zum Erstellen eines ausführenden Azure Automation-Kontos finden Sie unter [Azure Run As Account](automation-sec-configure-azure-runas-account.md)(Ausführendes Azure-Konto).
- Eine Azure Resource Manager-VM (nicht klassisch) unter Windows Server 2008 R2 oder höher. Eine Anleitung zum Erstellen einer VM finden Sie unter [Erstellen Ihres ersten virtuellen Windows-Computers im Azure-Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Azure PowerShell-Modul, Version 3.6 oder höher. Führen Sie `Get-Module -ListAvailable AzureRM` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-azurerm-ps) Informationen dazu.
- Erfahrung mit DSC (Desired State Configuration, Konfiguration des gewünschten Zustands). Weitere Informationen zu DSC finden Sie unter [Windows PowerShell DSC – Übersicht](https://docs.microsoft.com/powershell/dsc/overview).

## <a name="log-in-to-azure"></a>Anmelden an Azure

Melden Sie sich mit dem Befehl `Connect-AzureRmAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Erstellen und Hochladen einer Konfiguration in Azure Automation

Für dieses Tutorial verwenden wir eine einfache DSC-Konfiguration, die sicherstellt, dass IIS auf der VM installiert ist.

Weitere Informationen zu DSC-Konfigurationen finden Sie unter [DSC-Konfigurationen](/powershell/dsc/configurations).

Geben Sie in einem Texteditor Folgendes ein, und speichern Sie die Datei lokal als `TestConfig.ps1`.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

Rufen Sie das Cmdlet `Import-AzureRmAutomationDscConfiguration` auf, um die Konfiguration in Ihr Automation-Konto hochzuladen:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilieren einer Konfiguration in eine Knotenkonfiguration

Eine DSC-Konfiguration muss in eine Knotenkonfiguration kompiliert werden, bevor sie einem Knoten zugewiesen werden kann.

Informationen zum Kompilieren von Konfigurationen finden Sie unter [DSC-Konfigurationen](/powershell/dsc/configurations).

Rufen Sie das Cmdlet `Start-AzureRmAutomationDscCompilationJob` auf, um die `TestConfig`-Konfiguration in eine Knotenkonfiguration zu kompilieren:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Dies erstellt eine Konfiguration mit dem Namen `TestConfig.WebServer` in Ihrem Automation-Konto.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrieren eines virtuellen Computers für die Verwaltung durch die Zustandskonfiguration

Mit Azure Automation State Configuration können Sie virtuelle Azure-Computer (mit dem klassischen Modell oder dem Resource Manager-Modell), lokale virtuelle Computer, Linux-Computer, virtuelle AWS-Computer und lokale physische Computer verwalten. In diesem Thema wird das Registrieren von ausschließlich Azure Resource Manager-VMs behandelt. Informationen zum Registrieren anderer Computertypen finden Sie unter [Integrieren von Computern für die Verwaltung durch die Azure Automation-Zustandskonfiguration](automation-dsc-onboarding.md).

Rufen Sie das Cmdlet `Register-AzureRmAutomationDscNode` auf, um Ihren virtuellen Computer bei der Azure Automation-Zustandskonfiguration zu registrieren.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Dadurch wird der angegebene virtuelle Computer als verwalteter Knoten für die Zustandskonfiguration registriert.

### <a name="specify-configuration-mode-settings"></a>Angeben der Konfigurationsmoduseinstellungen

Wenn Sie eine VM als verwalteten Knoten registrieren, können Sie auch Eigenschaften der Konfiguration angeben. Sie können z.B. festlegen, dass der Zustand der VM nur einmalig angewendet werden soll (DSC versucht nicht, die Konfiguration nach der ersten Prüfung zu übernehmen), indem Sie `ApplyOnly` als Wert der **ConfigurationMode**-Eigenschaft angeben:

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Sie können auch angeben, wie oft DSC den Konfigurationszustand überprüft, indem Sie die **ConfigurationModeFrequencyMins**-Eigenschaft verwenden:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Weitere Informationen zum Festlegen von Konfigurationseigenschaften für einen verwalteten Knoten finden Sie unter [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Weitere Informationen zu DSC-Konfigurationseinstellungen finden Sie unter [Konfigurieren des lokalen Konfigurations-Managers](/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Zuweisen einer Knotenkonfiguration zu einem verwalteten Knoten

Nun können wir die kompilierte Knotenkonfiguration der zu konfigurierenden VM zuordnen.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -Id $node.Id
```

Damit wird dem registrierten DSC-Knoten `DscVm` die benannte Knotenkonfiguration `TestConfig.WebServer` zugeordnet.
Standardmäßig wird der DSC-Knoten alle 30 Minuten auf Konformität mit der Knotenkonfiguration geprüft.
Informationen zum Ändern des Intervalls für die Konformitätsprüfung finden Sie unter [Konfigurieren des lokalen Konfigurations-Managers](/PowerShell/DSC/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Überprüfen des Konformitätsstatus eines verwalteten Knotens

Sie können Berichte zum Konformitätsstatus eines verwalteten Knotens abrufen, indem Sie das Cmdlet `Get-AzureRmAutomationDscNodeReport` aufrufen:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Id $node.Id

# Display the most recent report
$reports[0]
```

## <a name="next-steps"></a>Nächste Schritte

- Eine Einführung finden Sie unter [Erste Schritte mit Azure Automation State Configuration](automation-dsc-getting-started.md).
- Weitere Informationen zum Integrieren von Knoten finden Sie unter [Onboarding von Computern zur Verwaltung durch Azure Automation DSC](automation-dsc-onboarding.md).
- Wie Sie DSC-Konfigurationen kompilieren und anschließend Zielknoten zuweisen, erfahren Sie unter [Kompilieren von DSC-Konfigurationen in Azure Automation DSC](automation-dsc-compile.md).
- Eine PowerShell-Cmdlet-Referenz ist unter [Azure Automation State Configuration-Cmdlets](/powershell/module/azurerm.automation/#automation) verfügbar.
- Eine Preisübersicht finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/).
- Ein Verwendungsbeispiel für Azure Automation State Configuration in einer Continuous Deployment-Pipeline finden Sie unter [Continuous Deployment mit Azure Automation State Configuration und Chocolatey](automation-dsc-cd-chocolatey.md).