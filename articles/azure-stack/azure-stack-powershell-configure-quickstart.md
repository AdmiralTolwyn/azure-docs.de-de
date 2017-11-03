---
title: "Installieren und Konfigurieren von PowerShell für den Azure Stack-Schnellstart | Microsoft-Dokumentation"
description: "Erfahren Sie mehr über das Installieren und Konfigurieren von PowerShell für Azure Stack."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: sngun
ms.openlocfilehash: 039806e164be29b80e604bbcf0f2997e635664e5
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/25/2017
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Einrichten von PowerShell in Azure Stack

*Gilt für: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Dieser Schnellstart unterstützt Sie beim Installieren und Konfigurieren einer Azure Stack-Umgebung mit PowerShell. Der Gültigkeitsbereich des in diesem Artikel bereitgestellten Skripts bezieht sich nur auf den **Azure Stack-Betreiber**.

Dieser Artikel stellt eine verkürzte Version der Schritte dar, die in den Artikeln [Installieren von PowerShell]( azure-stack-powershell-install.md), [Herunterladen von Tools]( azure-stack-powershell-download.md) und [Konfigurieren der PowerShell-Umgebung des Azure Stack-Betreibers]( azure-stack-powershell-configure-admin.md) beschrieben werden. Indem Sie die Skripts in diesem Thema verwenden, können Sie PowerShell für Azure Stack-Umgebungen einrichten, die mit Azure Active Directory oder Active Directory-Verbunddiensten (AD FS) bereitgestellt werden.  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Einrichten von PowerShell für auf Azure Active Directory basierenden Bereitstellungen

Melden Sie sich bei Ihrem Azure Stack Development Kit oder bei einem externen, Windows-basierten Client an, wenn Sie über VPN verbunden sind. Öffnen Sie eine PowerShell ISE-Sitzung mit erhöhten Rechten, und führen Sie dann das folgende Skript aus. Stellen Sie sicher, dass Sie die Variablen **TenantName**, **ArmEndpoint** und **GraphAudience** wie für Ihre Umgebungskonfiguration erforderlich aktualisieren:

> [!IMPORTANT]
> Das Release des PowerShell-Moduls AzureRM 1.2.11 enthält eine Liste mit wichtigen Änderungen. Informationen zum Upgrade aus Version 1.2.10 finden Sie im [Migrationshandbuch](https://aka.ms/azspowershellmigration).

```powershell
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following command:
Get-Module -ListAvailable | `
  where-Object {$_.Name -like “Azure*”} | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = “<Keyvault DNS suffix for your environment>”


# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Einrichten von PowerShell für auf AD FS basierende Bereitstellungen 

Melden Sie sich bei Ihrem Azure Stack Development Kit oder bei einem externen, Windows-basierten Client an, wenn Sie über VPN verbunden sind. Öffnen Sie eine PowerShell ISE-Sitzung mit erhöhten Rechten, und führen Sie dann das folgende Skript aus. Stellen Sie sicher, dass Sie die Variablen **ArmEndpoint** und **GraphAudience** wie für Ihre Umgebungskonfiguration erforderlich aktualisieren:

```powershell

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable | `
  where-Object {$_.Name -like “Azure*”} | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = “<Keyvault DNS suffix for your environment>”

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>Testen der Konnektivität

Da Sie PowerShell nun konfiguriert haben, können Sie die Konfiguration testen, indem Sie eine Ressourcengruppe erstellen:

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

Nachdem die Ressourcengruppe erstellt wurde, ist die Eigenschaft **Bereitstellungsstatus** auf **Erfolgreich** festgelegt.

## <a name="next-steps"></a>Nächste Schritte

* [Install and configure CLI (Installieren und Konfigurieren der CLI)](azure-stack-connect-cli.md)

* [Entwickeln von Vorlagen](user/azure-stack-develop-templates.md)







