---
title: Bedingte Bereitstellung mit Vorlagen
description: Beschreibt, wie eine Ressource in einer Azure Resource Manager-Vorlage bedingt bereitgestellt werden kann.
ms.topic: conceptual
ms.date: 09/03/2019
ms.openlocfilehash: e3d78f875e39628194ac30310314e59e2ef19fdb
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147167"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>Bedingte Bereitstellung in Resource Manager-Vorlagen

Manchmal müssen Sie eine Ressource optional in einer Vorlage bereitstellen. Verwenden Sie das `condition`-Element, um anzugeben, ob die Ressource bereitgestellt wird. Der Wert für dieses Element wird mit „true“ oder „false“ aufgelöst. Wenn der Wert TRUE ist, wird die Ressource erstellt. Wenn der Wert FALSE ist, wird die Ressource nicht erstellt. Der Wert kann nur auf die gesamte Ressource angewandt werden.

## <a name="new-or-existing-resource"></a>Neue oder vorhandene Ressource

Sie können bedingte Bereitstellung verwenden, um eine neue Ressource zu erstellen oder eine vorhandene zu verwenden. Im folgenden Beispiel wird gezeigt, wie eine Bedingung verwendet wird, um ein neues Speicherkonto bereitzustellen oder ein vorhandenes Speicherkonto zu verwenden.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Wenn der Parameter **newOrExisting** auf **new** festgelegt ist, wird die Bedingung zu „true“ ausgewertet. Das Speicherkonto wird bereitgestellt. Ist **newOrExisting** dagegen auf **existing** festgelegt, wird die Bedingung zu „false“ ausgewertet, und das Speicherkonto wird nicht bereitgestellt.

Eine vollständige Beispielvorlage, die das `condition`-Element verwendet, finden Sie unter [VM mit einem neuen oder vorhandenen virtuellen Netzwerk, Speicher und einer neuen oder vorhandenen öffentlichen IP-Adresse](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="allow-condition"></a>Zulassen einer Bedingung

Sie können einen Parameterwert übergeben, der angibt, ob eine Bedingung zulässig ist. Im folgenden Beispiel wird eine SQL Server-Instanz bereitgestellt und werden optional Azure-IPs zugelassen.

```json
{
    "type": "Microsoft.Sql/servers",
    "name": "[parameters('serverName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "version": "12.0"
    },
    "resources": [
        {
            "condition": "[parameters('allowAzureIPs')]",
            "type": "firewallRules",
            "name": "AllowAllWindowsAzureIps",
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
            ],
            "properties": {
                "endIpAddress": "0.0.0.0",
                "startIpAddress": "0.0.0.0"
            }
        }
    ]
}
```

Die komplette Vorlage finden Sie unter [Azure SQL logical server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="runtime-functions"></a>Laufzeitfunktionen

Bei Verwendung einer Funktion vom Typ [reference](resource-group-template-functions-resource.md#reference) oder [list](resource-group-template-functions-resource.md#list) mit einer Ressource, die bedingt bereitgestellt wird, wird die Funktion auch dann ausgewertet, wenn die Ressource nicht bereitgestellt wird. Es wird eine Fehlermeldung angezeigt, wenn die Funktion auf eine nicht vorhandene Ressource verweist.

Verwenden Sie die [if](resource-group-template-functions-logical.md#if)-Funktion, um sicherzustellen, dass die Funktion nur für Bedingungen ausgewertet wird, wenn die Ressource bereitgestellt wird. Eine Beispielvorlage, die „if“ und „reference“ mit einer bedingt bereitgestellten Ressource verwendet, finden Sie unter der [if](resource-group-template-functions-logical.md#if)-Funktion.

## <a name="condition-with-complete-mode"></a>Bedingung mit vollständigem Modus

Wenn Sie eine Vorlage mit [vollständigem Modus](deployment-modes.md) bereitstellen und eine Ressource aufgrund einer nicht zutreffenden Bedingung nicht bereitgestellt wird, hängt das Ergebnis davon ab, welche Rest-API-Version Sie zum Bereitstellen der Vorlage verwenden. Wenn Sie eine frühere Version als 2019-05-10 verwenden, wird die Ressourcen **nicht gelöscht**. Bei Version 2019-05-10 oder höher wird die Ressourcen **gelöscht**. Die neuesten Versionen von Azure PowerShell und Azure CLI löschen die Ressource, wenn die Bedingung nicht zutrifft.

## <a name="next-steps"></a>Nächste Schritte

* Empfehlungen zum Erstellen von Vorlagen finden Sie unter [Azure Resource Manager template best practices (Bewährte Methoden für das Erstellen von Azure Resource Manager-Vorlagen)](template-best-practices.md).
* Informationen, wie mehrere Instanzen einer Ressource erstellt werden, finden Sie unter [Iteration von Ressourcen, Eigenschaften oder Variablen in Azure Resource Manager-Vorlagen](resource-group-create-multiple.md).