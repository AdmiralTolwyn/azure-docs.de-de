---
title: 'Azure Policy: JSON-Beispiel – Verweigern des kalten Zugriffstierings für Speicherkonten'
description: Diese JSON-Beispielrichtlinie untersagt die Verwendung des kalten Zugriffstierings für Blob-Speicherkonten.
services: azure-policy
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-policy
ms.devlang: ''
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 10/30/2017
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: a396d8c8c791ac2eb1f4f9f983e6224d0daa1bcf
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2018
---
# <a name="deny-cool-access-tiering-for-storage-accounts"></a>Verweigern des kalten Zugriffstierings für Speicherkonten

Diese Richtlinie untersagt die Verwendung des kalten Zugriffstierings für Blob-Speicherkonten.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Vorlagenbeispiel

[!code-json[main](../../../policy-templates/samples/Storage/storage-account-access-tier/azurepolicy.json "Deny cool access tiering for storage accounts")]

Sie können diese Vorlage über das [Azure-Portal](#deploy-with-the-portal), mit [PowerShell](#deploy-with-powershell) oder mit der [Azure CLI](#deploy-with-azure-cli) bereitstellen.

## <a name="deploy-with-the-portal"></a>Bereitstellen über das Portal

[![Bereitstellen in Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FStorage%2Fstorage-account-access-tier%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Bereitstellen mit PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$definition = New-AzureRmPolicyDefinition -Name "storage-account-access-tier" -DisplayName "Deny cool access tiering for storage accounts" -description "Ensures there's no usage of cool access tiering for storage." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/storage-account-access-tier/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>Bereinigen der PowerShell-Bereitstellung

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen zu entfernen.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Bereitstellen über die Azure-Befehlszeilenschnittstelle

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'storage-account-access-tier' --display-name 'Deny cool access tiering for storage accounts' --description 'Ensures there's no usage of cool access tiering for storage.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/storage-account-access-tier/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "storage-account-access-tier"
```

### <a name="clean-up-azure-cli-deployment"></a>Bereinigen der Azure CLI-Bereitstellung

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen zu entfernen.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nächste Schritte

- Zusätzliche Beispielvorlagen für Azure Policy finden Sie unter [Templates for Azure Policy (Vorlagen für Azure Policy)](../json-samples.md).
