---
title: Bereitstellungsmodi
description: Beschreibt das Festlegen, ob für Azure Resource Manager eine vollständige oder inkrementelle Bereitstellung verwendet wird.
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 1077d92f076797fb03c4fe750b353e2306f9b6de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460244"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager-Bereitstellungsmodi

Bei der Bereitstellung Ihrer Ressourcen geben Sie an, dass es sich bei der Bereitstellung entweder um ein inkrementelles Update oder um ein vollständiges Update handelt. Der Unterschied zwischen diesen beiden Modi besteht darin, wie Resource Manager vorhandene Ressourcen in der Ressourcengruppe behandelt, die nicht in der Vorlage enthalten sind.

In beiden Modi versucht Resource Manager, alle in der Vorlage angegebenen Ressourcen zu erstellen. Wenn die Ressource bereits in der Ressourcengruppe vorhanden ist und ihre Einstellungen unverändert sind, wird für diese Ressource kein Vorgang ausgeführt. Wenn Sie die Eigenschaftswerte für eine Ressource ändern, wird die Ressource mit diesen neuen Werten aktualisiert. Falls Sie versuchen, den Speicherort oder Typ einer vorhandenen Ressource zu aktualisieren, tritt bei der Bereitstellung ein Fehler auf. Stellen Sie stattdessen eine neue Ressource mit dem gewünschten Speicherort oder Typ bereit.

Der Standardmodus ist inkrementell.

## <a name="complete-mode"></a>Vollständiger Modus

Im vollständigen-Modus **löscht** Resource Manager Ressourcen, die in der Ressourcengruppe vorhanden, aber nicht in der Vorlage angegeben sind.

Wenn Ihre Vorlage eine Ressource enthält, die nicht bereitgestellt ist, weil die [Bedingung](conditional-resource-deployment.md) nicht erfüllt wird, hängt das Ergebnis davon ab, welche Rest-API-Version Sie zum Bereitstellen der Vorlage verwenden. Wenn Sie eine frühere Version als 2019-05-10 verwenden, wird die Ressource **nicht gelöscht**. Bei Version 2019-05-10 oder höher wird die Ressource **gelöscht**. Die neuesten Versionen von Azure PowerShell und Azure CLI löschen die Ressource.

Wenden Sie den vollständigen Modus mit [Kopierschleifen](copy-resources.md) mit Vorsicht an. Alle Ressourcen, die nicht in der Vorlage angegeben sind, werden nach dem Auflösen der Kopierschleife gelöscht.

Wenn Sie in [mehr als einer Ressourcengruppe in einer Vorlage](cross-resource-group-deployment.md) bereitstellen, können Ressourcen, die sich in der Ressourcengruppe befinden, die im Bereitstellungsvorgang angegeben wurde, gelöscht werden. Ressourcen in den sekundären Ressourcengruppen werden nicht gelöscht.

Bei der Verarbeitung von Löschungen im vollständigen Modus gibt es zwischen Ressourcentypen einige Unterschiede. Übergeordnete Ressourcen werden automatisch gelöscht, wenn sie nicht in einer Vorlage enthalten sind, die im vollständigen-Modus bereitgestellt wird. Einige untergeordnete Ressourcen werden nicht automatisch gelöscht, wenn sie nicht in der Vorlage enthalten sind. Diese untergeordneten Ressourcen werden jedoch gelöscht, wenn die übergeordnete Ressource gelöscht wird.

Beispiel: Wenn Ihre Ressourcengruppe eine DNS-Zone (Ressourcentyp „Microsoft.Network/dnsZones“) und einen CNAME-Eintrag (Ressourcentyp „Microsoft.Network/dnsZones/CNAME“) enthält, ist die DNS-Zone die übergeordnete Ressource des CNAME-Eintrags. Wenn Sie im vollständigen Modus bereitstellen und die DNS-Zone nicht in Ihre Vorlage aufnehmen, werden sowohl die DNS-Zone als auch der CNAME-Eintrag gelöscht. Wenn Sie die DNS-Zone in Ihre Vorlage aufnehmen, den CNAME-Eintrag jedoch nicht, wird der CNAME-Eintrag nicht gelöscht.

Eine Liste der Verarbeitung von Löschungen durch Ressourcentypen finden Sie unter [Löschen von Azure-Ressourcen für Bereitstellungen im vollständigen Modus](complete-mode-deletion.md).

Wenn die Ressourcengruppe [gesperrt](../management/lock-resources.md) ist, werden die Ressourcen im vollständigen Modus nicht gelöscht.

> [!NOTE]
> Nur Vorlagen auf Stammebene unterstützen den vollständigen Bereitstellungsmodus. Für [verknüpfte oder geschachtelte Vorlagen](linked-templates.md) müssen Sie den inkrementellen Modus verwenden.
>
> [Bereitstellungen auf der Abonnementstufe](deploy-to-subscription.md) unterstützen den vollständigen Modus nicht.
>
> Das Portal unterstützt den vollständigen Modus derzeit nicht.
>

## <a name="incremental-mode"></a>Inkrementeller Modus

Im inkrementellen Modus lässt Resource Manager Ressourcen **unverändert**, die in der Ressourcengruppe vorhanden, aber nicht in der Vorlage angegeben sind. Ressourcen in der Vorlagen **werden der Ressourcengruppe hinzugefügt**.

> [!NOTE]
> Bei der erneuten Bereitstellung einer vorhandenen Ressource im inkrementellen Modus werden alle Eigenschaften erneut angewendet. Die **Eigenschaften werden nicht inkrementell hinzugefügt**. Ein weit verbreitetes Missverständnis ist, dass Eigenschaften, die nicht in der Vorlage angegeben sind, unverändert bleiben. Falls Sie bestimmte Eigenschaften nicht angeben, interpretiert Resource Manager die Bereitstellung als Überschreibung dieser Werte. Eigenschaften, die nicht in der Vorlage enthalten sind, werden auf die Standardwerte zurückgesetzt. Geben Sie alle nicht standardmäßigen Werte für die Ressource an und nicht nur diejenigen, die Sie aktualisieren. Die Ressourcendefinition in der Vorlage enthält immer den endgültigen Zustand der Ressource. Sie kann keine partielle Aktualisierung einer vorhandenen Ressource darstellen.

## <a name="example-result"></a>Beispielergebnis

Im folgenden Szenario wird der Unterschied zwischen dem inkrementellen und dem vollständigen Modus veranschaulicht:

Die **Ressourcengruppe** enthält Folgendes:

* Ressource A
* Ressource B
* Ressource C

Die **Vorlage** enthält Folgendes:

* Ressource A
* Ressource B
* Ressource D

Bei der Bereitstellung im **inkrementellen** Modus enthält die Ressourcengruppe:

* Ressource A
* Ressource B
* Ressource C
* Ressource D

Bei der Bereitstellung im **vollständigen** Modus wird Ressource C gelöscht. Die Ressourcengruppe enthält:

* Ressource A
* Ressource B
* Ressource D

## <a name="set-deployment-mode"></a>Festlegen des Bereitstellungsmodus

Verwenden Sie zum Festlegen des Bereitstellungsmodus mit PowerShell den `Mode`-Parameter.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Verwenden Sie zum Festlegen des Bereitstellungsmodus mit der Azure CLI den `mode`-Parameter.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Im folgenden Beispiel ist eine verknüpfte Vorlage mit festgelegtem inkrementellen Bereitstellungsmodus dargestellt:

```json
"resources": [
  {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Erstellen von Ressourcen-Manager-Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](template-syntax.md).
* Informationen zum Bereitstellen von Vorlagen finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](deploy-powershell.md).
* Informationen zum Anzeigen der Vorgänge für einen Ressourcenanbieter finden Sie unter [Azure-REST-API](/rest/api/).
