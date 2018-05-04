---
title: Konfigurieren der PowerShell-Umgebung des Azure Stack-Benutzers | Microsoft-Dokumentation
description: Konfigurieren der PowerShell-Umgebung des Azure Stack-Benutzers
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/26/2017
ms.author: mabrigg
ms.reviewer: Balsu.G
ms.openlocfilehash: e17fc85de3d11034889c39fd205b7ddc8cb344cc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2018
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Konfigurieren der PowerShell-Umgebung des Azure Stack-Benutzers

Als Azure Stack-Benutzer können Sie die PowerShell-Umgebung des Azure Stack Development Kits konfigurieren. Nach der Konfigurierung können Sie PowerShell verwenden, um Azure Stack-Ressourcen zu verwalten, z.B. Angebote abonnieren, VMs erstellen, Azure Resource Manager-Vorlagen bereitstellen usw. Dieses Thema ist auf die Nutzung mit den Benutzerumgebungen beschränkt. Wenn Sie PowerShell für die Umgebung des Cloudbetreibers einrichten möchten, finden Sie weitere Informationen im Artikel [Configure the Azure Stack operator's PowerShell environment (Konfigurieren der PowerShell-Umgebung des Azure Stack-Betreibers)](../azure-stack-powershell-configure-admin.md). 

## <a name="prerequisites"></a>Voraussetzungen 

Führen Sie die folgenden erforderlichen Schritte entweder über das [Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) oder auf einem Windows-basierten externen Client aus, wenn Sie [über VPN verbunden](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) sind:

* Installieren Sie [mit Azure Stack kompatible Azure PowerShell-Module](azure-stack-powershell-install.md).  
* Laden Sie die [Tools herunter, die zum Arbeiten mit Azure Stack erforderlich sind](azure-stack-powershell-download.md). 

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>Konfigurieren der Benutzerumgebung und Anmelden bei Azure Stack

Führen Sie basierend auf dem Bereitstellungstyp (Azure AD oder AD FS) eines der folgenden Skripts aus, um PowerShell für Azure Stack zu konfigurieren (Ersetzen Sie die AAD-Werte „tenantName“ und „ArmEndpoint“ und den Wert für den GraphAudience-Endpunkt gemäß Ihrer Umgebungskonfiguration.):

### <a name="azure-active-directory-aad-based-deployments"></a>Auf Azure Active Directory (AAD) basierende Bereitstellungen
       
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
   ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Auf Active Directory-Verbunddienste (AD FS) basierende Bereitstellungen 
          
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

  # Get the Active Directory tenantId that is used to deploy Azure Stack     
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
  ```

## <a name="register-resource-providers"></a>Registrieren von Ressourcenanbietern

Wenn die Ressourcenanbieter auf einem neu erstellten Abonnement arbeiten, für das über das Portal keine Ressourcen bereitgestellt sind, sind sie nicht automatisch registriert. Sie sollten diese explizit registrieren, indem Sie das folgende Skript verwenden:

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    } 
```

## <a name="test-the-connectivity"></a>Testen der Konnektivität

Nachdem nun alles eingerichtet ist, können Sie mit PowerShell Ressourcen in Azure Stack erstellen. Sie können beispielsweise eine Ressourcengruppe für eine Anwendung erstellen und einen virtuellen Computer hinzufügen. Verwenden Sie den folgenden Befehl, um eine Ressourcengruppe mit dem Namen „MyResourceGroup“ zu erstellen:

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Nächste Schritte
* [Entwickeln von Vorlagen für Azure Stack](azure-stack-develop-templates.md)
* [Bereitstellen von Vorlagen mit PowerShell](azure-stack-deploy-template-powershell.md)
