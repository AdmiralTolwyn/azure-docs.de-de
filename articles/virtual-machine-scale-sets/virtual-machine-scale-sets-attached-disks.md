---
title: "Skalierungsgruppen für an virtuelle Azure-Computer angefügte Datenträger | Microsoft-Dokumentation"
description: "Erfahren Sie, wie Sie angefügte Datenträger mit VM-Skalierungsgruppen verwenden."
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: guybo
ms.openlocfilehash: 22c7e589efa9a9f401549ec9b95c58c4eaf07b94
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/29/2017
---
# <a name="azure-vm-scale-sets-and-attached-data-disks"></a>Azure-VM-Skalierungsgruppen und angefügte Datenträger
[Skalierungsgruppen für virtuelle Azure-Computer](/azure/virtual-machine-scale-sets/) unterstützen jetzt virtuelle Computer mit angefügten Datenträgern. Datenträger können im Speicherprofil für die Skalierungsgruppen definiert werden, die mit Azure Managed Disks erstellt wurden. Bisher waren das Betriebssystemlaufwerk und die temporären Laufwerke die einzigen Optionen für direkt angefügten Speicher für virtuelle Computer.

> [!NOTE]
>  Wenn Sie eine Skalierungsgruppe mit definierten angefügten Datenträgern erstellen, müssen Sie die Datenträger weiterhin in einem virtuellen Computer bereitstellen und formatieren, damit Sie sie verwenden können (wie es auch bei eigenständigen virtuellen Azure-Computern der Fall ist). Eine einfache Möglichkeit hierzu ist die Verwendung einer benutzerdefinierten Skripterweiterung, die ein Standardskript aufruft, um alle Datenträger auf einem virtuellen Computer zu partitionieren und zu formatieren.

## <a name="create-a-scale-set-with-attached-data-disks"></a>Erstellen von Skalierungsgruppen mit angefügten Datenträgern
Eine einfache Möglichkeit zum Erstellen einer Skalierungsgruppe mit angefügten Datenträgern ist der [Azure CLI](https://github.com/Azure/azure-cli)-Befehl _vmss create_. Im folgenden Beispiel wird eine Azure-Ressourcengruppe und eine VM-Skalierungsgruppe mit 10 virtuellen Ubuntu-Computern erstellt, von denen jede über zwei angefügte Datenträger mit 50 bzw. 100 GB verfügt.
```bash
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```
Bitte beachten Sie, dass der Befehl _vmss create_ bestimmte Konfigurationswerte standardmäßig verwendet, wenn Sie diese nicht angeben. Sie können die verfügbaren Optionen, die überschrieben werden können, folgendermaßen anzeigen:
```bash
az vmss create --help
```
Eine weitere Möglichkeit zum Erstellen einer Skalierungsgruppe mit angefügten Datenträgern besteht darin, eine Skalierungsgruppe in einer Azure Resource Manager-Vorlage, einschließlich eines Abschnitts _dataDisks_ im _storageProfile_, zu definieren und die Vorlage bereitzustellen. Die Datenträger mit 50 und 100 GB im obigen Beispiel werden in der Vorlage folgendermaßen definiert:
```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    }
]
```
Ein vollständiges Beispiel für eine Skalierungsgruppenvorlage mit einem angefügten Datenträger, die Sie direkt bereitstellen können, finden Sie hier: [https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data](https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data).

## <a name="adding-a-data-disk-to-an-existing-scale-set"></a>Hinzufügen eines Datenträgers zu einer vorhandenen Skalierungsgruppe
> [!NOTE]
>  Sie können nur Datenträger an eine Skalierungsgruppe anfügen, die mit [Azure Managed Disks](./virtual-machine-scale-sets-managed-disks.md) erstellt wurde.

Sie können einen Datenträger zu einer VM-Skalierungsgruppe mithilfe des Azure CLI-Befehls _az vmss disk attach_ hinzufügen. Legen Sie eine LUN fest, die noch nicht verwendet wird. Im folgenden CLI-Beispiel wird ein Laufwerk mit 50 GB zu LUN 3 hinzugefügt:
```bash
az vmss disk attach -g dsktest -n dskvmss --size-gb 50 --lun 3
```

Im folgenden PowerShell-Beispiel wird ein Laufwerk mit 50 GB zu LUN 3 hinzugefügt:
```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myvmssrg -VMScaleSetName myvmss
$vmss = Add-AzureRmVmssDataDisk -VirtualMachineScaleSet $vmss -Lun 3 -Caching 'ReadWrite' -CreateOption Empty -DiskSizeGB 50 -StorageAccountType StandardLRS
Update-AzureRmVmss -ResourceGroupName myvmssrg -Name myvmss -VirtualMachineScaleSet $vmss
```

> [!NOTE]
> Verschiedene VM-Größen haben verschiedene Grenzwerte in Bezug auf die Anzahl der angefügten Laufwerke, die sie unterstützen. Lesen Sie vor dem Hinzufügen eines neuen Datenträgers die Informationen unter [Größen für virtuelle Computer in Azure](../virtual-machines/windows/sizes.md).

Sie können einen Datenträger auch hinzufügen, indem Sie der Eigenschaft _dataDisks_ im _storageProfile_ einer Skalierungsgruppendefinition einen neuen Eintrag hinzufügen und die Änderung übernehmen. Im [Azure-Ressourcen-Explorer](https://resources.azure.com/) finden Sie eine vorhandene Skalierungsgruppendefinition, mit der Sie dies testen können. Wählen Sie _Bearbeiten_, und fügen Sie zur Liste der Datenträger einen neuen Datenträger hinzu. Beispiel: (unter Verwendung des obigen Beispiels):
```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    },
    {
    "lun": 3,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 20
    }          
]
```

Wählen Sie dann _PUT_, um die Änderungen für Ihre Skalierungsgruppe zu übernehmen. Dieses Beispiel funktioniert nur, wenn Sie eine VM-Größe nutzen, die mehr als zwei angefügte Datenträger unterstützt.

> [!NOTE]
> Wenn Sie an einer Skalierungsgruppendefinition Änderungen vornehmen, indem Sie z.B. einen Datenträger hinzufügen oder entfernen, wird diese Änderungen für alle neu erstellten virtuellen Computer übernommen, für vorhandene virtuelle Computer jedoch nur, wenn für die Eigenschaft _upgradePolicy_ „Automatisch“ festgelegt ist. Wenn hierfür „Manuell“ festgelegt wurde, müssen Sie das neue Modell manuell auf vorhandene virtuelle Computer anwenden. Dies ist im Portal mithilfe des PowerShell-Befehls _Update-AzureRmVmssInstance_ oder mithilfe des CLI-Befehls _az vmss update-instances_ möglich.

## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>Hinzufügen von vorab aufgefüllten Datenträgern zu einer vorhandenen Skalierungsgruppe 
> Wenn Sie Datenträger zu einer vorhandenen Skalierungsgruppe hinzufügen, wird der Datenträger standardmäßig immer leer erstellt. (Dieses Szenario umfasst auch neue von der Skalierungsgruppe erstellte Instanzen.) Das Verhalten ist darin begründet, dass die Skalierungsgruppendefinition einen leeren Datenträger enthält. Wählen Sie eine der folgenden beiden Optionen, um vorab aufgefüllte Datenträger für eine vorhandene Skalierungsgruppe zu erstellen:

* Kopieren Sie Daten vom virtuellen Instanz 0-Computer auf die Datenträger in anderen virtuellen Computern, indem Sie ein benutzerdefiniertes Skript ausführen.
* Erstellen Sie ein verwaltetes Image mit dem Betriebssystemdatenträger sowie dem Datenträger (mit den erforderlichen Daten), und erstellen Sie dann eine neue Skalierungsgruppe mit dem Image. Auf diese Weise enthält jeder neue virtuelle Computer, der erstellt wird, einen Datenträger, der in der Definition der Skalierungsgruppe bereitgestellt wird. Da diese Definition auf ein Image mit einem Datenträger mit benutzerdefinierten Daten verweist, wird jeder virtuelle Computer in der Skalierungsgruppe automatisch mit diesen Änderungen aktiviert.

> Informationen zum Erstellen eines benutzerdefinierten Images finden Sie unter [Erstellen eines verwalteten Images eines generalisierten virtuellen Computers in Azure](/azure/virtual-machines/windows/capture-image-resource/). 

> Der Benutzer muss den virtuellen Instanz 0-Computer mit den erforderlichen Daten erfassen und dann diese VHD für die Imagedefinition verwenden.

## <a name="removing-a-data-disk-from-a-scale-set"></a>Entfernen eines Datenträgers aus einer Skalierungsgruppe
Sie können einen Datenträger aus einer VM-Skalierungsgruppe mithilfe des Azure CLI-Befehls _az vmss disk detach_ entfernen. Z.B. mit dem folgenden Befehl wird der für LUN 2 definierte Datenträger entfernt:
```bash
az vmss disk detach -g dsktest -n dskvmss --lun 2
```  
Sie können einen Datenträger auch aus einer Skalierungsgruppe entfernen, indem Sie einen Eintrag aus der Eigenschaft _dataDisks_ im _storageProfile_ entfernen und die Änderung übernehmen. 

## <a name="additional-notes"></a>Zusätzliche Hinweise
Unterstützung für Azure Managed Disks und Skalierungsgruppen, die an Datenträger angefügt sind, ist über die API-Version [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) oder höher der Microsoft.Compute-API verfügbar.

In der ursprünglichen Implementierung der Unterstützung angefügter Datenträger für Skalierungsgruppen ist es nicht möglich, Datenträger zu einzelnen virtuellen Computer hinzuzufügen oder davon zu entfernen.

Die Unterstützung des Azure-Portals für angefügte Datenträger in Skalierungsgruppen ist anfangs beschränkt. Je nach Ihren Anforderungen können Sie Azure-Vorlagen, CLI, PowerShell, SDKs und REST API zum Verwalten angefügter Datenträger verwenden.


