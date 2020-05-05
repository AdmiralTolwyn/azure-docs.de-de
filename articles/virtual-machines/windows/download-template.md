---
title: Herunterladen der Vorlage für einen virtuellen Azure-Computer
description: Laden Sie die Vorlage für einen virtuellen Computer mithilfe des Portals oder mit PowerShell herunter.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: af6905f0ba62a9053e44134348721312ade6b9d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085381"
---
# <a name="download-the-template-for-a-vm"></a>Herunterladen einer Vorlage für einen virtuellen Computer
Wenn Sie über das Portal oder mithilfe von PowerShell einen virtuellen Computer in Azure erstellen, wird automatisch eine Resource Manager-Vorlage für Sie erstellt. Sie können diese Vorlage verwenden, um eine Bereitstellung schnell zu duplizieren. Die Vorlage enthält Informationen über alle Ressourcen in einer Ressourcengruppe. Bei virtuellen Computern bedeutet dies, dass die Vorlage alle Elemente enthält, die zur Unterstützung des virtuellen Computers in dieser Ressourcengruppe erstellt wurden – einschließlich der Netzwerkressourcen.

## <a name="download-the-template-using-the-portal"></a>Herunterladen der Vorlage über das Portal
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Wählen Sie im Menü auf der linken Seite die Option **Virtuelle Computer** aus.
3. Wählen Sie den gewünschten virtuellen Computer aus der Liste aus.
4. Wählen Sie **Vorlage exportieren** aus.
5. Klicken Sie im Menü oben auf der Seite auf **Herunterladen**, und speichern Sie die ZIP-Datei auf Ihrem lokalen Computer.
6. Öffnen Sie die ZIP-Datei, und extrahieren Sie die Dateien in einen Ordner. Die ZIP-Datei enthält Folgendes:
   
   * parameters.json
   * template.json

Die Datei „template.json“ ist die Vorlage.

## <a name="download-the-template-using-powershell"></a>Herunterladen der Vorlage mithilfe von PowerShell
Sie können die JSON-Vorlagendatei auch mithilfe des Cmdlets [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) herunterladen. Sie können den Parameter `-path` verwenden, um den Dateinamen und den Pfad für die JSON-Datei anzugeben. Dieses Beispiel zeigt, wie Sie die Vorlage für die Ressourcengruppe mit dem Namen **myResourceGroup** in den Ordner **C:\users\public\downloads** auf Ihrem lokalen Computer herunterladen.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Bereitstellen von Ressourcen mithilfe von Vorlagen finden Sie unter [Resource Manager-Vorlage – exemplarische Vorgehensweise](../../azure-resource-manager/resource-manager-template-walkthrough.md).

