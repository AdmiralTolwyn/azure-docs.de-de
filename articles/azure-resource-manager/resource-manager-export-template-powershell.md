---
title: Exportieren einer Resource Manager-Vorlage mit Azure PowerShell | Microsoft Docs
description: Verwenden Sie Azure Resource Manager und Azure PowerShell zum Exportieren einer Vorlage aus einer Ressourcengruppe.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/23/2018
ms.author: tomfitz
ms.openlocfilehash: cca81bf3f5a46b32cc901a0ac6024eb7888685f7
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55081601"
---
# <a name="export-azure-resource-manager-templates-with-powershell"></a>Exportieren von Azure Resource Manager-Vorlagen mit PowerShell

Mit Resource Manager können Sie eine Resource Manager-Vorlage aus vorhandenen Ressourcen in Ihrem Abonnement exportieren. Auf der Grundlage dieser generierten Vorlage können Sie sich über die Vorlagensyntax informieren oder ggf. die erneute Bereitstellung Ihrer Lösung automatisieren.

Beachten Sie, dass es zwei Möglichkeiten zum Exportieren einer Vorlage gibt:

* Sie können **die für eine Bereitstellung verwendete Vorlage exportieren**. Die exportierte Vorlage enthält alle Parameter und Variablen so, wie sie in der Originalvorlage angezeigt wurden. Dieser Ansatz ist sinnvoll, wenn Sie eine Vorlage abrufen müssen.
* Sie können **eine generierte Vorlage exportieren, die den aktuellen Zustand der Ressourcengruppe darstellt**. Die exportierte Vorlage basiert nicht auf einer Vorlage, die Sie für die Bereitstellung verwendet haben. Stattdessen wird eine Vorlage erstellt, bei der es sich um eine „Momentaufnahme“ oder „Sicherung“ der Ressourcengruppe handelt. Die exportierte Vorlage verfügt über viele hartcodierte Werte und vermutlich nicht über so viele Parameter, wie Sie sonst definieren. Verwenden Sie diese Option, um Ressourcen in der gleichen Ressourcengruppe erneut bereitzustellen. Um diese Vorlage für eine andere Ressourcengruppe zu verwenden, müssen Sie sie möglicherweise erheblich ändern.

In diesem Artikel werden beide Ansätze beschrieben.

## <a name="deploy-a-solution"></a>Bereitstellen einer Lösung

Zur Veranschaulichung beider Ansätze zum Exportieren einer Vorlage stellen wir zuerst eine Lösung für Ihr Abonnement bereit. Wenn Ihr Abonnement bereits eine Ressourcengruppe enthält, die Sie exportieren möchten, müssen Sie diese Lösung nicht bereitstellen. Im weiteren Verlauf dieses Artikels wird jedoch Bezug auf die Vorlage für diese Lösung genommen. Mit dem Beispielskript wird ein Speicherkonto bereitgestellt.

```powershell
New-AzResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -DeploymentName NewStorage
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```  

## <a name="save-template-from-deployment-history"></a>Speichern einer Vorlage aus dem Bereitstellungsverlauf

Mit dem Befehl [Save-AzureRmResourceGroupDeploymentTemplate](/powershell/module/az.resources/save-azresourcegroupdeploymenttemplate) können Sie eine Vorlage aus dem Bereitstellungsverlauf abrufen. Im folgenden Beispiel wird die zuvor bereitgestellte Vorlage gespeichert:

```powershell
Save-AzResourceGroupDeploymentTemplate -ResourceGroupName ExampleGroup -DeploymentName NewStorage
```

Damit wird der Speicherort der Vorlage zurückgegeben.

```powershell
Path
----
C:\Users\exampleuser\NewStorage.json
```

Öffnen Sie die Datei. Beachten Sie, dass es sich genau um die Vorlage handelt, die Sie für die Bereitstellung verwendet haben. Die Parameter und Variablen entsprechen der Vorlage aus GitHub. Sie können diese Vorlage erneut bereitstellen.

## <a name="export-resource-group-as-template"></a>Exportieren einer Ressourcengruppe als Vorlage

Statt eine Vorlage aus dem Bereitstellungsverlauf abzurufen, können Sie mithilfe des Befehls [Export-AzureRmResourceGroup](/powershell/module/az.resources/export-azresourcegroup) eine Vorlage abrufen, die den aktuellen Status einer Ressourcengruppe darstellt. Diesen Befehl können Sie verwenden, wenn Sie viele Änderungen an Ihrer Ressourcengruppe vorgenommen haben und keine vorhandene Vorlage alle Änderungen darstellt. Sie dient als eine Momentaufnahme der Ressourcengruppe, die Sie verwenden können, um die erneute Bereitstellung in der gleichen Ressourcengruppe auszuführen. Um die exportierte Vorlage für andere Lösungen verwenden zu können, müssen Sie sie erheblich ändern.

```powershell
Export-AzResourceGroup -ResourceGroupName ExampleGroup
```

Damit wird der Speicherort der Vorlage zurückgegeben.

```powershell
Path
----
C:\Users\exampleuser\ExampleGroup.json
```

Öffnen Sie die Datei. Beachten Sie, dass sich diese Vorlage von der Vorlage in GitHub unterscheidet. Sie weist andere Parameter und keine Variablen auf. Die Speicher-SKU und der Speicherort sind hartcodierte Werte. Im folgenden Beispiel ist die exportierte Vorlage dargestellt. Ihre Vorlage weist jedoch einen etwas anderen Parameternamen auf.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccounts_nf3mvst4nqb36standardsa_name": {
      "defaultValue": null,
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[parameters('storageAccounts_nf3mvst4nqb36standardsa_name')]",
      "apiVersion": "2016-01-01",
      "location": "southcentralus",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

Sie können diese Vorlage erneut bereitstellen. Für das Speicherkonto muss jedoch ein eindeutiger Name erraten werden. Der Name Ihres Parameters ist etwas anders.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -TemplateFile C:\Users\exampleuser\ExampleGroup.json `
  -storageAccounts_nf3mvst4nqb36standardsa_name tfnewstorage0501
```

## <a name="customize-exported-template"></a>Anpassen der exportierten Vorlage

Sie können diese Vorlage ändern, sodass sie einfacher und flexibler verwendet werden kann. Um mehr Speicherorte zu berücksichtigen, ändern Sie die Location-Eigenschaft so, dass der Speicherort der Ressourcengruppe verwendet wird:

```json
"location": "[resourceGroup().location]",
```

Entfernen Sie den Parameter für den Namen des Speicherkontos, damit der eindeutige Name des Speicherkontos nicht erraten werden muss. Fügen Sie einen Parameter für ein Speichernamensuffix und eine Speicher-SKU hinzu:

```json
"parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
},
```

Fügen Sie eine Variable hinzu, die den Namen des Speicherkontos mit der Funktion „uniqueString“ erstellt:

```json
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
```

Legen Sie den Namen des Speicherkontos auf die Variable fest:

```json
"name": "[variables('storageAccountName')]",
```

Legen Sie die SKU auf den Parameter fest:

```json
"sku": {
    "name": "[parameters('storageAccountType')]",
    "tier": "Standard"
},
```

Ihre Vorlage sieht jetzt wie folgt aus:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), parameters('storageSuffix'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "[parameters('storageAccountType')]",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

Stellen Sie die geänderte Vorlage erneut bereit.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zur Verwendung des Portals zum Exportieren einer Vorlage finden Sie unter [Exportieren einer Azure Resource Manager-Vorlage aus vorhandenen Ressourcen](resource-manager-export-template.md).
* Informationen zum Definieren von Parametern in der Vorlage finden Sie unter [Erstellen von Vorlagen](resource-group-authoring-templates.md#parameters).
* Tipps zum Beheben gängiger Azure-Bereitstellungsfehler finden Sie unter [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](resource-manager-common-deployment-errors.md).
