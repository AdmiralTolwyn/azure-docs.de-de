---
title: 'Beispiel: Data Lake Store-Verschlüsselung erforderlich'
description: Bei dieser Beispielrichtliniendefinition muss der Verschlüsselungszustand für Data Lake Store-Konten aktiviert sein.
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: 5b29caa7fa8b9169b8c2164975f9cdb2663c5cef
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463649"
---
# <a name="sample---require-data-lake-store-encryption"></a>Beispiel: Data Lake Store-Verschlüsselung erforderlich

Mit dieser integrierten Richtlinie werden alle Data Lake Store-Konten abgelehnt, für die keine Verschlüsselung aktiviert ist.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Vorlagenbeispiel

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DataLakeStore/accounts"
      },
      {
        "field": "Microsoft.DataLakeStore/accounts/encryptionState",
        "equals": "Disabled"
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Sie können diese Vorlage über das [Azure-Portal](#deploy-with-the-portal), mit [PowerShell](#deploy-with-powershell) oder mit der [Azure CLI](#deploy-with-azure-cli) bereitstellen. Verwenden Sie zum Abrufen der integrierten Richtlinie die ID `a7ff3161-0087-490a-9ad9-ad6217f4f43a`.

## <a name="deploy-with-the-portal"></a>Bereitstellen über das Portal

Wählen Sie beim Zuweisen einer Richtlinie die Option **Verschlüsselung für DataLakeStore-Konten erzwingen** aus den verfügbaren integrierten Definitionen aus.

## <a name="deploy-with-powershell"></a>Bereitstellen mit PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/a7ff3161-0087-490a-9ad9-ad6217f4f43a

New-AzPolicyAssignment -name "Data Lake Store encryption" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Bereinigen der PowerShell-Bereitstellung

Führen Sie den folgenden Befehl aus, um die Richtlinienzuweisung zu entfernen.

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name "Data Lake Store encryption" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Bereitstellen über die Azure-Befehlszeilenschnittstelle

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "Data Lake Store encryption" --policy a7ff3161-0087-490a-9ad9-ad6217f4f43a
```

### <a name="clean-up-azure-cli-deployment"></a>Bereinigen der Azure CLI-Bereitstellung

Führen Sie den folgenden Befehl aus, um die Richtlinienzuweisung zu entfernen.

```azurecli-interactive
az policy assignment delete --name "Data Lake Store encryption" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

- Unter [Azure Policy-Beispiele](index.md) finden Sie weitere Beispiele.