---
title: Azure Resource Manager-Vorlagenfunktionen – Ressourcen | Microsoft Docs
description: Hier werden die Funktionen beschrieben, die in einer Azure Resource Manager-Vorlage zum Abrufen von Werten zu Ressourcen verwendet werden können.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: tomfitz
ms.openlocfilehash: 9ba4c9d9cd5f8a43be0f97053c02798e3b84a5f7
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2018
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Ressourcenfunktionen für Azure Resource Manager-Vorlagen

Der Ressourcen-Manager stellt die folgenden Funktionen zum Abrufen von Ressourcenwerten bereit:

* [listKeys](#listkeys)
* [listSecrets](#list)
* [list*](#list)
* [providers](#providers)
* [Referenz](#reference)
* [Ressourcengruppe](#resourcegroup)
* [Ressourcen-ID](#resourceid)
* [Abonnement](#subscription)

Informationen zum Abrufen von Werten aus Parametern, Variablen oder der aktuellen Bereitstellung finden Sie unter [Funktionen für Bereitstellungswerte](resource-group-template-functions-deployment.md).

<a id="listkeys" />
<a id="list" />

## <a name="listkeys-listsecrets-and-list"></a>listKeys, listSecrets und list*
`listKeys(resourceName or resourceIdentifier, apiVersion)`

`listSecrets(resourceName or resourceIdentifier, apiVersion)`

`list{Value}(resourceName or resourceIdentifier, apiVersion)`

Gibt die Werte für einen beliebigen Ressourcentyp zurück, der den list-Vorgang unterstützt. Am häufigsten werden `listKeys` und `listSecrets` verwendet. 

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| resourceName oder resourceIdentifier |Ja |Zeichenfolge |Eindeutiger Bezeichner für die Ressource. |
| apiVersion |Ja |Zeichenfolge |API-Version eines Ressourcen-Laufzeitstatus. In der Regel im Format **jjjj-mm-tt**. |

### <a name="return-value"></a>Rückgabewert

Das zurückgegebene Objekt von „listKeys“ hat das folgende Format:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Andere list-Funktionen weisen andere Rückgabeformate auf. Um das Format einer Funktion anzuzeigen, geben Sie es im Abschnitt „outputs“ wie in der Beispielvorlage dargestellt an. 

### <a name="remarks"></a>Anmerkungen

Jeder Vorgang, der mit **list** beginnt, kann als Funktion in der Vorlage verwendet werden. Zu den verfügbaren Vorgängen zählen neben „listKeys“ auch Vorgänge wie `list`, `listAdminKeys` und `listStatus`. Sie können jedoch keine **Listenvorgänge** verwenden, für die Werte im Hauptteil der Anforderung erforderlich sind. Beispielsweise sind für den Vorgang [List Account SAS](/rest/api/storagerp/storageaccounts#StorageAccounts_ListAccountSAS) Parameter wie *signedExpiry* im Hauptteil der Anforderung erforderlich, daher können Sie ihn nicht in einer Vorlage verwenden.

Um zu bestimmen, welche Ressourcentypen einen list-Vorgang aufweisen, stehen die folgenden Optionen zur Verfügung:

* Zeigen Sie die [REST-API-Vorgänge](/rest/api/) für einen Ressourcenanbieter an, und suchen Sie nach List-Vorgängen. Speicherkonten weisen z. B. den [listKeys-Vorgang](/rest/api/storagerp/storageaccounts#StorageAccounts_ListKeys) auf.
* Verwenden Sie das PowerShell-Cmdlet [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation). Im folgenden Beispiel werden alle List-Vorgänge für Speicherkonten abgerufen:

  ```powershell
  Get-AzureRmProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Verwenden Sie den folgenden Azure-CLI-Befehl, um nur die Listenvorgänge zu filtern:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

Geben Sie die Ressource entweder mithilfe der [resourceId-Funktion](#resourceid) oder des Formats `{providerNamespace}/{resourceType}/{resourceName}` an.

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) zeigt, wie die Primär- und Sekundärschlüssel von einem Speicherkonto im Abschnitt „outputs“ zurückgegeben werden können.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[listKeys(parameters('storageAccountName'), '2016-12-01')]"
      }
    }
}
``` 

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/listkeys.json --parameters storageAccountName=<your-storage-account>
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/listkeys.json -storageAccountName <your-storage-account>
```

<a id="providers" />

## <a name="providers"></a>providers
`providers(providerNamespace, [resourceType])`

Gibt Informationen zu einem Ressourcenanbieter und den von ihm unterstützten Ressourcentypen zurück. Wenn Sie keinen Ressourcentyp angeben, gibt die Funktion alle unterstützten Typen für den Ressourcenanbieter zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| providerNamespace |Ja |Zeichenfolge |Namespace des Anbieters |
| resourceType |Nein  |Zeichenfolge |Der Ressourcentyp innerhalb des angegebenen Namespace. |

### <a name="return-value"></a>Rückgabewert

Jeder unterstützte Typ wird im folgenden Format zurückgegeben: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Die Arraysortierung der zurückgegebenen Werte ist dabei nicht garantiert.

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) zeigt die Nutzungsweise der Anbieterfunktion:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

Für den Ressourcenanbieter **Microsoft.Web** und den Ressourcentyp **sites** wird im vorherigen Beispiel ein Objekt im folgenden Format zurückgegeben:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/providers.json --parameters providerNamespace=Microsoft.Web resourceType=sites
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/providers.json -providerNamespace Microsoft.Web -resourceType sites
```

<a id="reference" />

## <a name="reference"></a>Referenz
`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Gibt ein Objekt zurück, das den Laufzeitstatus einer Ressource darstellt.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| resourceName oder resourceIdentifier |Ja |Zeichenfolge |Name oder eindeutiger Bezeichner einer Ressource |
| apiVersion |Nein  |Zeichenfolge |API-Version der angegebenen Ressource. Schließen Sie diesen Parameter ein, wenn die Ressource nicht innerhalb der gleichen Vorlage bereitgestellt wird. In der Regel im Format **jjjj-mm-tt**. |
| 'Full' |Nein  |Zeichenfolge |Ein Wert, der angibt, ob das vollständige Ressourcenobjekt zurückgegeben werden soll. Wird `'Full'` nicht angegeben, wird nur das Eigenschaftenobjekt der Ressource zurückgegeben. Das vollständige Objekt enthält Werte wie die Ressourcen-ID und den Standort. |

### <a name="return-value"></a>Rückgabewert

Jeder Ressourcentyp gibt andere Eigenschaften für die Verweisfunktion zurück. Die Funktion gibt kein einzelnes vordefiniertes Format zurück. Darüber hinaus variiert der zurückgegebene Wert abhängig davon, ob Sie das vollständige Objekt angegeben haben. Um die Eigenschaften für einen Ressourcentyp anzuzeigen, geben Sie das Objekt wie im Beispiel gezeigt im Abschnitt „outputs“ zurück.

### <a name="remarks"></a>Anmerkungen

Die Verweisfunktion leitet ihren Wert von einem Laufzeitstatus ab und kann somit nicht im Variablenabschnitt verwendet werden. Sie kann im Ausgabeabschnitt einer Vorlage oder [verknüpften Vorlage](resource-group-linked-templates.md#link-or-nest-a-template) verwendet werden. Sie kann nicht im Ausgabeabschnitt einer [verschachtelten Vorlage](resource-group-linked-templates.md#link-or-nest-a-template) verwendet werden. Um die Werte für eine bereitgestellte Ressource in einer geschachtelten Vorlage zurückzugeben, konvertieren Sie Ihre geschachtelte Vorlage in eine verknüpfte Vorlage. 

Mithilfe der Referenzfunktion können Sie implizit deklarieren, dass eine Ressource von einer anderen abhängt, wenn die referenzierte Ressource innerhalb der gleichen Vorlage zur Verfügung gestellt wird. Die „dependsOn“-Eigenschaft muss nicht zusätzlich verwendet werden. Die Funktion wird nicht ausgewertet, bis die Ressource, auf die verwiesen wird, die Bereitstellung abgeschlossen hat.

Um die Eigenschaftennamen und Werte für einen Ressourcentyp anzuzeigen, erstellen Sie eine Vorlage, die das Objekt im Abschnitt „outputs“ zurückgibt. Wenn Sie über eine Ressource dieses Typs verfügen, gibt Ihre Vorlage das Objekt zurück, ohne neue Ressourcen bereitzustellen. 

Sie verwenden in der Regel die Funktion **Verweis**, um einen bestimmten Wert aus einem Objekt zurückzugeben, wie z.B. den Blob-Endpunkt-URI oder den vollständig qualifizierten Domänennamen.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Verwenden Sie `'Full'`, wenn Sie Ressourcenwerte benötigen, die nicht Teil des Eigenschaftsschemas sind. Rufen Sie zum Festlegen von Schlüsseltresor-Zugriffsrichtlinien beispielsweise die Identitätseigenschaften für einen virtuellen Computer ab.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
        "objectId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

Das vollständige Beispiel der vorherigen Vorlage finden Sie unter [Windows to Key Vault](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo08.msiWindowsToKeyvault.json) (Windows und Key Vault). Ein ähnliches Beispiel ist für [Linux](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo07.msiLinuxToArm.json) verfügbar.

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) stellt eine Ressource bereit und verweist auf diese Ressource.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
``` 

Im vorherigen Beispiel werden die beiden Objekt zurückgegeben. Das Eigenschaftenobjekt hat das folgende Format:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

Das vollständige Objekt hat das folgende Format:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/referencewithstorage.json --parameters storageAccountName=<your-storage-account>
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/referencewithstorage.json -storageAccountName <your-storage-account>
```

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) verweist auf ein Speicherkonto, das nicht in dieser Vorlage bereitgestellt wird. Das Speicherkonto ist bereits in derselben Ressourcengruppe vorhanden.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
            "type" : "object"
        }
    }
}
```

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/reference.json --parameters storageAccountName=<your-storage-account>
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/reference.json -storageAccountName <your-storage-account>
```

<a id="resourcegroup" />

## <a name="resourcegroup"></a>Ressourcengruppe
`resourceGroup()`

Gibt ein Objekt zurück, das die aktuelle Ressourcengruppe darstellt. 

### <a name="return-value"></a>Rückgabewert

Das zurückgegebene Objekt hat das folgende Format:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

### <a name="remarks"></a>Anmerkungen

Die Funktion „resourceGroup“ wird häufig verwendet, um Ressourcen am gleichen Speicherort wie die Ressourcengruppe zu erstellen. Das folgende Beispiel nutzt den Speicherort der Ressourcengruppe, um einer Website den Speicherort zuzuweisen.

```json
"resources": [
   {
      "apiVersion": "2016-08-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) gibt die Eigenschaften der Ressourcengruppe zurück.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

Im vorherigen Beispiel wird ein Objekt im folgenden Format zurückgegeben:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourcegroup.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourcegroup.json 
```

<a id="resourceid" />

## <a name="resourceid"></a>Ressourcen-ID
`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

Gibt den eindeutigen Bezeichner einer Ressource zurück. Diese Funktion wird verwendet, wenn der Ressourcenname zweideutig ist oder nicht innerhalb der gleichen Vorlage zur Verfügung gestellt wird. 

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nein  |Zeichenfolge (im GUID-Format) |Der Standardwert ist das aktuelle Abonnement. Geben Sie diesen Wert an, wenn Sie eine Ressource in einem anderen Abonnement abrufen möchten. |
| resourceGroupName |Nein  |Zeichenfolge |Der Standardwert ist die aktuelle Ressourcengruppe. Geben Sie diesen Wert an, wenn Sie eine Ressource in einer anderen Ressourcengruppe abrufen möchten. |
| resourceType |Ja |Zeichenfolge |Ressourcentyp einschließlich Namespace von Ressourcenanbieter. |
| resourceName1 |Ja |Zeichenfolge |Name der Ressource. |
| resourceName2 |Nein  |Zeichenfolge |Nächstes Ressourcen-Namensegment, wenn die Ressource geschachtelt ist. |

### <a name="return-value"></a>Rückgabewert

Der Bezeichner wird im folgenden Format zurückgeben:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Anmerkungen

Welche Parameterwerte Sie angeben, hängt davon ab, ob sich die Ressource in der gleichen Abonnement- und Ressourcengruppe befindet wie die aktuelle Bereitstellung.

Verwenden Sie für das Abrufen der Ressourcen-ID für ein Speicherkonto in derselben Abonnement- und Ressourcengruppe Folgendes:

```json
"[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
```

Verwenden Sie für das Abrufen der Ressourcen-ID für ein Speicherkonto in derselben Abonnementgruppe, aber einer anderen Ressourcengruppe Folgendes:

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Verwenden Sie für das Abrufen der Ressourcen-ID für ein Speicherkonto in einer anderen Abonnement- und Ressourcengruppe Folgendes:

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Verwenden Sie für das Abrufen der Ressourcen-ID für eine Datenbank in einer anderen Ressourcengruppe Folgendes:

```json
"[resourceId('otherResourceGroup', 'Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]"
```

Sie müssen diese Funktion oft nutzen, wenn Sie ein Speicherkonto oder einen virtuellen Computer in einer alternativen Ressourcengruppe verwenden. Das folgende Beispiel zeigt, wie eine Ressource von einer externen Ressourcengruppe leicht verwendet werden kann:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
      "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) gibt die Ressourcen-ID für ein Speicherkonto in der Ressourcengruppe zurück:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Typ | Wert |
| ---- | ---- | ----- |
| sameRGOutput | Zeichenfolge | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Zeichenfolge | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Zeichenfolge | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Zeichenfolge | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourceid.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourceid.json 
```

<a id="subscription" />

## <a name="subscription"></a>Abonnement
`subscription()`

Gibt Details zum Abonnement für die aktuelle Bereitstellung zurück. 

### <a name="return-value"></a>Rückgabewert

Die Funktion gibt das folgende Format zurück:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) zeigt die im Abschnitt „outputs“ abgerufene subscription-Funktion. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/subscription.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/subscription.json 
```

## <a name="next-steps"></a>Nächste Schritte
* Eine Beschreibung der Abschnitte in einer Azure Resource Manager-Vorlage finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](resource-group-authoring-templates.md).
* Informationen zum Zusammenführen mehrerer Vorlagen finden Sie unter [Verwenden von verknüpften Vorlagen bei der Bereitstellung von Azure-Ressourcen](resource-group-linked-templates.md).
* Informationen dazu, wie Sie beim Erstellen eines Ressourcentyps eine bestimmte Anzahl von Durchläufen ausführen, finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager](resource-group-create-multiple.md).
* Informationen zum Bereitstellen der erstellten Vorlage finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen ](resource-group-template-deploy.md).

