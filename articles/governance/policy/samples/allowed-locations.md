---
title: Beispiel – zulässige Speicherorte
description: Diese Beispielrichtlinie erfordert, dass alle Ressourcen an den genehmigten Speicherorten bereitgestellt werden.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: a13f181e90f0435f23e22a7c4db42976bd0afb44
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243545"
---
# <a name="allowed-locations"></a>Allowed locations (Zulässige Speicherorte)

Mit dieser Richtlinie können Sie die Speicherorte einschränken, die Ihre Organisation beim Bereitstellen von Ressourcen angeben kann. Wird zur Erzwingung Ihrer Geokonformitätsanforderungen verwendet. Schließt Ressourcengruppen, Microsoft.AzureActiveDirectory/b2c-Verzeichnisse und Ressourcen aus, die die Region „global“ verwenden. Sie geben ein Array von zulässigen Speicherorten an.

Sie können für die Bereitstellung dieser Beispielrichtlinie Folgendes verwenden:

- Das [Azure-Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure-Befehlszeilenschnittstelle](#azure-cli)
- [REST-API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Beispielrichtlinie

### <a name="policy-definition"></a>Richtliniendefinition

Die vollständige erstellte JSON-Richtliniendefinition, die von der REST-API, den Schaltflächen zum Bereitstellen in Azure und manuell im Portal verwendet wird:

[!code-json[full](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.json "Allowed locations")]

> [!NOTE]
> Verwenden Sie zum manuellen Erstellen einer Richtlinie im Portal die Abschnitte **properties.parameters** und **properties.policyRule** der obigen Definition. Umschließen Sie die beiden Abschnitte mit geschweiften Klammern (`{}`), damit sie gültigen JSON-Code darstellen.

### <a name="policy-rules"></a>Richtlinienregeln

Der JSON-Code, der die Regeln der Richtlinie definiert und von der Azure-Befehlszeilenschnittstelle und Azure PowerShell verwendet wird:

[!code-json[rule](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Richtlinienparameter

Der JSON-Code, der die Richtlinienparameter definiert und von der Azure-Befehlszeilenschnittstelle und Azure PowerShell verwendet wird:

[!code-json[parameters](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json "Policy parameters (JSON)")]

## <a name="parameters"></a>Parameter

|NAME |Type |Feld |BESCHREIBUNG |
|---|---|---|---|
|listOfAllowedLocations |Array |locations |Die Liste der zulässigen Speicherorte|

Beim Erstellen einer Zuweisung über PowerShell oder die Azure CLI können die Parameterwerte mithilfe von `-PolicyParameter` (PowerShell) bzw. `--params` (Azure CLI) als JSON-Code in einer Zeichenfolge oder über eine Datei übergeben werden.
PowerShell unterstützt darüber hinaus das `-PolicyParameterObject`-Element. Dafür muss an das Cmdlet eine Name-Wert-Hashtabelle übergeben werden. Dabei steht **Name** für den Parameternamen und **Wert** für den einzelnen Wert bzw. das Array von Werten, der bzw. das während der Zuweisung übergeben wird.

In diesem Beispielparameter sind nur die Speicherorte _eastus2_ oder _westus_ zulässig.

```json
{
    "listOfAllowedLocations": {
        "value": [
            "eastus2",
            "westus"
        ]
    }
}
```

## <a name="azure-portal"></a>Azure-Portal

[![Bereitstellen in Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fallowed-locations%2Fazurepolicy.json)
[![Bereitstellen in Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fallowed-locations%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Bereitstellen mit Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "allowed-locations" -DisplayName "Allowed locations" -description "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements. Excludes resource groups, Microsoft.AzureActiveDirectory/b2cDirectories, and resources that use the 'global' region." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json' -Mode Indexed

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "listOfAllowedLocations": { "value": [ "eastus2", "westus" ] } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'allowed-locations-assignment' -DisplayName 'Allowed locations Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
```

### <a name="remove-with-azure-powershell"></a>Entfernen mit Azure PowerShell

Führen Sie die folgenden Befehle aus, um die vorherige Zuweisung und Definition zu entfernen:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell: Erläuterung

In den Skripts zum Bereitstellen und Entfernen werden die folgenden Befehle verwendet. Jeder Befehl in der folgenden Tabelle ist mit der zugehörigen Dokumentation verknüpft:

| Get-Help | Notizen |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Erstellt eine neue Azure Policy-Definition. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Ruft eine einzelne Ressourcengruppe ab. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Erstellt eine neue Azure Policy-Zuweisung. In diesem Beispiel wird eine Definition bereitgestellt, eine Initiative ist aber ebenfalls zulässig. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Entfernt eine vorhandene Azure Policy-Zuweisung. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Entfernt eine vorhandene Azure Policy-Definition. |

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Bereitstellen über die Azure-Befehlszeilenschnittstelle

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'allowed-locations' --display-name 'Allowed locations' --description 'This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements. Excludes resource groups, Microsoft.AzureActiveDirectory/b2cDirectories, and resources that use the 'global' region.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json' --mode Indexed)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "listOfAllowedLocations": { "value": [ "eastus2", "westus" ] } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'allowed-locations-assignment' --display-name 'Allowed locations Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
```

### <a name="remove-with-azure-cli"></a>Entfernen über die Azure-Befehlszeilenschnittstelle

Führen Sie die folgenden Befehle aus, um die vorherige Zuweisung und Definition zu entfernen:

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Azure-Befehlszeilenschnittstelle: Erläuterung

| Get-Help | Notizen |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Erstellt eine neue Azure Policy-Definition. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Ruft eine einzelne Ressourcengruppe ab. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Erstellt eine neue Azure Policy-Zuweisung. In diesem Beispiel wird eine Definition bereitgestellt, eine Initiative ist aber ebenfalls zulässig. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Entfernt eine vorhandene Azure Policy-Zuweisung. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Entfernt eine vorhandene Azure Policy-Definition. |

## <a name="rest-api"></a>REST-API

Es gibt mehrere Tools, die für die Interaktion mit der Resource Manager-REST-API verwendet werden können, etwa [ARMClient](https://github.com/projectkudu/ARMClient) oder PowerShell.

### <a name="deploy-with-rest-api"></a>Bereitstellen über die REST-API

- Erstellen Sie die Richtliniendefinition (Abonnementbereich). Verwenden Sie als Anforderungstext den JSON-Code der [Richtliniendefinition](#policy-definition).

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-locations?api-version=2018-05-01
  ```

- Erstellen Sie die Richtlinienzuweisung (Ressourcengruppenbereich).

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/allowed-locations-assignment?api-version=2018-05-01
  ```

  Verwenden Sie als Anforderungstext das folgende JSON-Beispiel:

  ```json
  {
      "properties": {
          "displayName": "Allowed locations Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/allowed-locations",
          "parameters": {
              "listOfAllowedLocations": {
                  "value": [
                      "eastus2",
                      "westus"
                  ]
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Entfernen mit der REST-API

- Entfernen der Richtlinienzuweisung

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/allowed-locations-assignment?api-version=2018-05-01
  ```

- Entfernen der Richtliniendefinition

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-locations?api-version=2018-05-01
  ```

### <a name="rest-api-explanation"></a>REST-API: Erläuterung

| Dienst | Group | Vorgang | Notizen |
|---|---|---|---|
| Ressourcenverwaltung | Richtliniendefinitionen | [Erstellen](/rest/api/resources/policydefinitions/createorupdate) | Erstellt eine neue Azure Policy-Definition in einem Abonnement. Alternative: [Erstellen einer Verwaltungsgruppe](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Ressourcenverwaltung | Richtlinienzuweisungen | [Erstellen](/rest/api/resources/policyassignments/create) | Erstellt eine neue Azure Policy-Zuweisung. In diesem Beispiel wird eine Definition bereitgestellt, eine Initiative ist aber ebenfalls zulässig. |
| Ressourcenverwaltung | Richtlinienzuweisungen | [Löschen](/rest/api/resources/policyassignments/delete) | Entfernt eine vorhandene Azure Policy-Zuweisung. |
| Ressourcenverwaltung | Richtliniendefinitionen | [Löschen](/rest/api/resources/policydefinitions/delete) | Entfernt eine vorhandene Azure Policy-Definition. Alternative: [Löschen einer Verwaltungsgruppe](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich weitere [Azure Policy-Beispiele](index.md) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md).