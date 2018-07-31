---
title: Tutorial – Verwalten von virtuellen Azure-Computern mit Azure CLI 2.0 | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie Azure CLI 2.0 zum Verwalten von virtuellen Azure-Computern durch Anwenden von RBAC, Richtlinien, Sperren und Tags verwenden.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 18ea9920ef3f3602721bc27001a66bcd2fc2b0fd
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205696"
---
# <a name="tutorial-learn-about-linux-virtual-machine-governance-with-azure-cli-20"></a>Tutorial: Informationen zur Verwaltung von virtuellen Azure-Computern mit Azure CLI 2.0

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial die Azure CLI-Version 2.0.30 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0]( /cli/azure/install-azure-cli) Informationen dazu.

## <a name="understand-scope"></a>Der Bereich

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

In diesem Tutorial wenden Sie alle Verwaltungseinstellungen auf eine Ressourcengruppe an, Sie können diese Einstellungen also am Ende problemlos entfernen.

Zuerst wird eine Ressourcengruppe erstellt.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

Die Ressourcengruppe ist derzeit leer.

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Sie sollten sicherstellen, dass Benutzer in Ihrer Organisation einen geeigneten Zugriff auf diese Ressourcen haben. Den Benutzern sollte kein uneingeschränkter Zugriff erteilt werden, aber Sie müssen auch sicherstellen, dass sie ihre Arbeit erledigen können. Mit der [rollenbasierten Zugriffssteuerung](../../role-based-access-control/overview.md) können Sie verwalten, welche Benutzer die Berechtigung zum Ausführen bestimmter Aktionen in einem Bereich erhalten.

Um Rollenzuweisungen erstellen und entfernen zu können, benötigen Benutzer den Zugriff `Microsoft.Authorization/roleAssignments/*`. Dieser Zugriff wird über die Rolle „Besitzer“oder „Benutzerzugriffsadministrator“ gewährt.

Zum Verwalten virtueller Computerlösungen gibt es drei ressourcenspezifische Rollen, die Zugriff auf häufig benötigte Ressourcen bereitstellen:

* [Mitwirkender von virtuellen Computern](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Mitwirkender von virtuellem Netzwerk](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Mitwirkender von Speicherkonto](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Anstatt einzelnen Benutzern Rollen zuzuweisen, ist es häufig einfacher, [eine Azure Active Directory-Gruppe zu erstellen](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md), um die Benutzer zusammenzufassen, die ähnliche Aktionen ausführen müssen. Danach weisen Sie diese Gruppe der entsprechenden Rolle zu. Zur Vereinfachung erstellen Sie in diesem Artikel eine Azure Active Directory-Gruppe ohne Mitglieder. Sie können diese Gruppe dennoch einer Rolle für einen bestimmten Bereich zuweisen. 

Das folgende Beispiel erstellt eine Azure Active Directory-Gruppe mit dem Namen *VMDemoContributors* und dem E-Mail-Kontonamen *vmDemoGroup*. Der E-Mail-Kontoname dient als Alias für die Gruppe.

```azurecli-interactive
adgroupId=$(az ad group create --display-name VMDemoContributors --mail-nickname vmDemoGroup --query objectId --output tsv)
```

Nachdem die Eingabeaufforderung zurückkehrt, dauert es einen Moment, bis die Gruppe in Azure Active Directory propagiert wurde. Warten Sie 20 oder 30 Sekunden, und weisen Sie dann mit dem Befehl [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) die neue Azure Active Directory-Gruppe der Rolle „Mitwirkender für virtuelle Computer“ für die Ressourcengruppe zu.  Wenn Sie den folgenden Befehl ausführen, bevor die Gruppe propagiert wurde, erhalten Sie die Fehlermeldung **Der Prinzipal „<guid>“ ist im Verzeichnis nicht enthalten**. Führen Sie den Befehl dann erneut aus.

```azurecli-interactive
az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

In der Regel müssen Sie den Prozess für die Rollen *Mitwirkender von virtuellem Netzwerk* und *Mitwirkender von Speicherkonto* wiederholen, um sicherzustellen, dass die Benutzer den richtigen Rollen für die Verwaltung der bereitgestellten Ressourcen zugewiesen werden. In diesem Artikel können Sie diese Schritte überspringen.

## <a name="azure-policy"></a>Azure Policy

[Azure Policy](../../azure-policy/azure-policy-introduction.md) hilft Ihnen dabei, sicherzustellen, dass alle Ressourcen im Abonnement die Unternehmensstandards erfüllen. Ihr Abonnement enthält bereits verschiedene Richtliniendefinitionen. Um die verfügbaren Richtliniendefinitionen anzuzeigen, verwenden Sie den Befehl [az policy definition list](/cli/azure/policy/definition#az_policy_definition_list):

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

Die vorhandenen Richtliniendefinitionen werden angezeigt. Der Richtlinientyp lautet entweder **BuiltIn** oder **Custom**. Durchsuchen Sie die Definitionen, um eine Definition zu finden, in der eine Bedingung beschrieben wird, die Sie zuweisen möchten. In diesem Artikel weisen Sie Richtlinien zu, mit denen Folgendes erreicht wird:

* Begrenzen der Standorte für alle Ressourcen
* Begrenzen der SKUs für virtuelle Computer
* Überwachen von virtuellen Computern, die keine verwalteten Datenträger verwenden

Im folgenden Beispiel rufen Sie drei Richtliniendefinitionen anhand des Anzeigenamens ab. Sie weisen diese Definitionen mit dem Befehl [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create) den Ressourcengruppen zu. Für einige Richtlinien stellen Sie Parameterwerte bereit, um die zulässigen Werte anzugeben.

```azurecli-interactive
# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
locationDefinition=$(az policy definition list --query "[?displayName=='Allowed locations'].name | [0]" --output tsv)
skuDefinition=$(az policy definition list --query "[?displayName=='Allowed virtual machine SKUs'].name | [0]" --output tsv)
auditDefinition=$(az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks'].name | [0]" --output tsv)

# Assign policy for allowed locations
az policy assignment create --name "Set permitted locations" \
  --resource-group myResourceGroup \
  --policy $locationDefinition \
  --params '{ 
      "listOfAllowedLocations": {
        "value": [
          "eastus", 
          "eastus2"
        ]
      }
    }'

# Assign policy for allowed SKUs
az policy assignment create --name "Set permitted VM SKUs" \
  --resource-group myResourceGroup \
  --policy $skuDefinition \
  --params '{ 
      "listOfAllowedSKUs": {
        "value": [
          "Standard_DS1_v2", 
          "Standard_E2s_v2"
        ]
      }
    }'

# Assign policy for auditing unmanaged disks
az policy assignment create --name "Audit unmanaged disks" \
  --resource-group myResourceGroup \
  --policy $auditDefinition
```

Im vorherigen Beispiel wird davon ausgegangen, dass Ihnen die Parameter für eine Richtlinie bereits bekannt sind. Wenn Sie die Parameter anzeigen möchten, verwenden Sie:

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>Bereitstellen des virtuellen Computers

Sie haben Rollen und Richtlinien zugewiesen und können daher Ihre Lösung bereitstellen. Die Standardgröße ist „Standard_DS1_v2“ – eine Ihrer zulässigen SKUs. Der Befehl erstellt SSH-Schlüssel, falls sie nicht an einem Standardschlüsselspeicherort vorhanden sind.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Nach Abschluss der Bereitstellung können Sie weitere Verwaltungseinstellungen auf die Lösung anwenden.

## <a name="lock-resources"></a>Sperren von Ressourcen

[Ressourcensperren](../../azure-resource-manager/resource-group-lock-resources.md) verhindern, dass Benutzer in Ihrer Organisation versehentlich wichtige Ressourcen löschen oder ändern. Im Gegensatz zur rollenbasierten Zugriffssteuerung wenden Ressourcensperren eine Einschränkung auf alle Benutzer und Rollen an. Sie können die Sperrebene auf *CanNotDelete* oder *ReadOnly* festlegen.

Zum Erstellen oder Löschen von Verwaltungssperren benötigen Sie Zugriff auf `Microsoft.Authorization/locks/*`-Aktionen. Unter den integrierten Rollen können nur **Besitzer** und **Benutzerzugriffsadministrator** diese Aktionen ausführen.

Zum Sperren des virtuellen Computers und der Netzwerksicherheitsgruppe verwenden Sie den Befehl [az lock create](/cli/azure/lock#az_lock_create):

```azurecli-interactive
# Add CanNotDelete lock to the VM
az lock create --name LockVM \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVM \
  --resource-type Microsoft.Compute/virtualMachines

# Add CanNotDelete lock to the network security group
az lock create --name LockNSG \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVMNSG \
  --resource-type Microsoft.Network/networkSecurityGroups
```

Um die Sperren zu testen, führen Sie den folgenden Befehl aus:

```azurecli-interactive 
az group delete --name myResourceGroup
```

Es wird eine Fehlermeldung darüber angezeigt, dass der Löschvorgang aufgrund einer Sperre nicht ausgeführt werden kann. Die Ressourcengruppe kann nur gelöscht werden, wenn Sie die Sperren direkt entfernen. Dieser Schritt wird unter [Bereinigen von Ressourcen](#clean-up-resources) gezeigt.

## <a name="tag-resources"></a>Markieren von Ressourcen

Durch Anwenden von [Tags](../../azure-resource-manager/resource-group-using-tags.md) können Sie Ihre Azure-Ressourcen logisch nach Kategorien organisieren. Jedes Tag besteht aus einem Namen und einem Wert. So können Sie beispielsweise den Namen „Umgebung“ und den Wert „Produktion“ auf alle Ressourcen in der Produktion anwenden.

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

Verwenden Sie zum Anwenden von Tags auf einen virtuellen Computer den Befehl [az resource tag](/cli/azure/resource#az_resource_tag). Eventuell bereits vorhandene Tags der Ressource werden nicht beibehalten.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Suchen von Ressourcen nach Tag

Um Ressourcen über einen Tagnamen und einen Wert zu suchen, verwenden Sie den Befehl [az resource list](/cli/azure/resource#az_resource_list):

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

Sie können die zurückgegebenen Werte für Verwaltungstasks verwenden, z.B. zum Beenden aller virtuellen Computer mit einem Tagwert.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Anzeigen von Kosten nach Tagwert

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Die gesperrte Netzwerksicherheitsgruppe kann erst gelöscht werden, wenn die Sperre entfernt wird. Um die Sperre zu entfernen, rufen Sie die IDs der Sperren ab, und übergeben Sie sie an den Befehl [az lock delete](/cli/azure/lock#az_lock_delete):

```azurecli-interactive
vmlock=$(az lock show --name LockVM \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Compute/virtualMachines \
  --resource-name myVM --output tsv --query id)
nsglock=$(az lock show --name LockNSG \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Network/networkSecurityGroups \
  --resource-name myVMNSG --output tsv --query id)
az lock delete --ids $vmlock $nsglock
```

Wenn Ressourcengruppe, virtueller Computer und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [az group delete](/cli/azure/group#az_group_delete) entfernen. Beenden Sie die SSH-Sitzung auf Ihrem virtuellen Computer, und löschen Sie die Ressourcen wie folgt:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein benutzerdefiniertes Image eines virtuellen Computers erstellt. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Zuweisen von Benutzern zu einer Rolle
> * Anwenden von Richtlinien zum Durchsetzen von Standards
> * Schützen wichtiger Ressourcen mit Sperren
> * Markieren von Ressourcen mit Tags für die Abrechnung und Verwaltung

Im nächsten Tutorial erhalten Sie Informationen zu hoch verfügbaren virtuellen Computern.

> [!div class="nextstepaction"]
> [Überwachen virtueller Computer](tutorial-monitoring.md)

