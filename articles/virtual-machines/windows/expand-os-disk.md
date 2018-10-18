---
title: Erweitern des Betriebssystemlaufwerks einer Windows-VM in Azure | Microsoft-Dokumentation
description: Erweitern Sie die Größe des Betriebssystemlaufwerks eines virtuellen Computers mit Azure PowerShell im Resource Manager-Bereitstellungsmodell.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.openlocfilehash: 097aa128affb6a7ac0770c861ab70ec9d04e2d98
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319114"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Erweitern des Betriebssystemlaufwerks eines virtuellen Computers

Beim Erstellen eines neuen virtuellen Computers in einer Ressourcengruppe durch Bereitstellen eines Images von [Azure Marketplace](https://azure.microsoft.com/marketplace/) hat das Standard-Betriebssystemlaufwerk häufig eine Größe von 127 GB (bei manchen Images sind die Standard-Betriebssystemlaufwerke kleiner). Obwohl es möglich ist, der VM Datenträger hinzuzufügen (die Anzahl hängt von der SKU ab, die Sie ausgewählt haben), und darüber hinaus empfohlen wird, Anwendungen und CPU-intensive Workloads auf diesen zusätzlichen Datenträgern zu installieren, müssen Kunden oft das Betriebssystemlaufwerk erweitern, um bestimmte Szenarien wie z. B. die folgenden zu unterstützen:

- Unterstützung von Legacyanwendungen, die Komponenten auf dem Betriebssystemlaufwerk installieren.
- Migrieren eines lokalen physischen PCs oder virtuellen Computers mit einem größeren Betriebssystemlaufwerk.


> [!IMPORTANT]
> Das Ändern der Größe des Betriebssystemdatenträgers eines virtuellen Azure-Computers führt dazu, dass dieser neu gestartet wird.
>
> Wenn Sie die Datenträger erweitern, müssen Sie [das Volume innerhalb des Betriebssystems erweitern](#expand-the-volume-within-the-os), um den größere Datenträger nutzen zu können.
> 

## <a name="resize-a-managed-disk"></a>Ändern der Größe eines verwalteten Datenträgers

Öffnen Sie Powershell ISE oder Powershell-Fenster im Administratormodus, und führen Sie die folgenden Schritte aus:

1. Melden Sie sich im Ressourcenverwaltungsmodus bei Ihrem Microsoft Azure-Konto an, und wählen Sie Ihr Abonnement wie folgt aus:
   
   ```powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Legen Sie den Namen Ihrer Ressourcengruppe und VM wie folgt fest:
   
   ```powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Rufen Sie einen Verweis auf Ihre VM wie folgt ab:
   
   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Halten Sie die VM vor dem Ändern der Größe des Datenträgers wie folgt an:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Rufen Sie einen Verweis auf den verwalteten Betriebssystem-Datenträger ab. Legen Sie die Größe des verwalteten Betriebssystem-Datenträgers auf den gewünschten Wert fest, und aktualisieren Sie den Datenträger wie folgt:
   
   ```Powershell
   $disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Die neue Größe sollte die Größe des vorhandenen Datenträgers überschreiten. Der zulässige Höchstwert für Betriebssystemdatenträger beträgt 2048 GB. (Sie können auch einen größeren VHD-Blob einsetzen, doch das Betriebssystem kann nur mit den ersten 2048 GB arbeiten.)
   > 
   > 
6. Das Aktualisieren der VM kann einige Sekunden dauern. Starten Sie die VM nach Abschluss der Befehlsausführung wie folgt neu:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

Das ist alles! Stellen Sie jetzt eine RDP-Verbindung mit der VM her, öffnen Sie die Computerverwaltung (oder die Datenträgerverwaltung), und erweitern Sie das Laufwerk mit dem neu zugewiesenen Speicherplatz.

## <a name="resize-an-unmanaged-disk"></a>Ändern der Größe eines nicht verwalteten Datenträgers

Öffnen Sie Powershell ISE oder Powershell-Fenster im Administratormodus, und führen Sie die folgenden Schritte aus:

1. Melden Sie sich im Ressourcenverwaltungsmodus bei Ihrem Microsoft Azure-Konto an, und wählen Sie Ihr Abonnement wie folgt aus:
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Legen Sie den Namen Ihrer Ressourcengruppe und VM wie folgt fest:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Rufen Sie einen Verweis auf Ihre VM wie folgt ab:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Halten Sie die VM vor dem Ändern der Größe des Datenträgers wie folgt an:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Legen Sie die Größe des nicht verwalteten Betriebssystem-Datenträgers auf den gewünschten Wert fest, und aktualisieren Sie die VM wie folgt:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Die neue Größe sollte die Größe des vorhandenen Datenträgers überschreiten. Der zulässige Höchstwert für Betriebssystemdatenträger beträgt 2048 GB. (Sie können auch einen größeren VHD-Blob einsetzen, doch das Betriebssystem kann nur mit den ersten 2048 GB arbeiten.)
   > 
   > 
   
6. Das Aktualisieren der VM kann einige Sekunden dauern. Starten Sie die VM nach Abschluss der Befehlsausführung wie folgt neu:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>Skripts für den Betriebssystem-Datenträger

Nachstehend finden Sie als Referenz das vollständige Skript sowohl für verwaltete als auch nicht verwaltete Datenträger:


**Verwaltete Datenträger**

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

**Nicht verwaltete Datenträger**

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Ändern der Größe von Datenträgern

Dieser Artikel konzentriert sich hauptsächlich auf die Erweiterung des verwalteten/nicht verwalteten Betriebssystem-Datenträgers der VM. Das Skript kann aber auch zum Erweitern der Datenträger verwendet werden, die an die VM angefügt sind. Um beispielsweise den ersten Datenträger zu erweitern, der an den virtuellen Computer angefügt ist, ersetzen Sie das `OSDisk`-Objekt von `StorageProfile` mit dem `DataDisks`-Array, und verwenden Sie einen numerischen Index, um – wie unten dargestellt – einen Verweis auf den ersten angefügten Datenträger zu erhalten:

**Verwalteter Datenträger**

```powershell
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Nicht verwalteter Datenträger**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



Entsprechend können Sie auf andere Datenträger verweisen, die an die VM angefügt sind, indem Sie entweder (wie oben gezeigt) einen Index oder die **Name**-Eigenschaft des Datenträgers verwenden:


**Verwalteter Datenträger**

```powershell
(Get-AzureRmDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Nicht verwalteter Datenträger**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Erweitern des Volumes innerhalb des Betriebssystems

Nachdem Sie den Datenträger für die VM erweitert haben, müssen Sie in das Betriebssystem wechseln und das Volume erweitern, damit es den neuen Speicherplatz einschließen kann. Es gibt mehrere Methoden zum Erweitern einer Partition. Dieser Abschnitt behandelt das Verbinden der VM über eine RDP-Verbindung, um die Partition mit **DiskPart** zu erweitern.

1. Öffnen Sie eine RDP-Verbindung mit Ihrer VM.

2.  Öffnen Sie eine Eingabeaufforderung, und geben Sie **diskpart** ein.

2.  Geben Sie `list volume` an der Eingabeaufforderung von **DISKPART** ein. Notieren Sie sich das Volume, das Sie erweitern möchten.

3.  Geben Sie `select volume <volumenumber>` an der Eingabeaufforderung von **DISKPART** ein. Hiermit wählen Sie das Volume *Volumenummer* aus, das Sie in zusammenhängenden, leeren Speicherplatz auf dem gleichen Datenträger erweitern möchten.

4.  Geben Sie `extend [size=<size>]` an der Eingabeaufforderung von **DISKPART** ein. Hierdurch wird das ausgewählte Volume um *Größe* in Megabyte (MB) erweitert.


## <a name="next-steps"></a>Nächste Schritte

Datenträger können auch über das [Azure-Portal](attach-managed-disk-portal.md) angefügt werden.
