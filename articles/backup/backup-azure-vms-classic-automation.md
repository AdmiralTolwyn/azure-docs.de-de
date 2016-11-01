<properties
	pageTitle="Bereitstellen und Verwalten von Sicherungen für Azure-VMs mit PowerShell | Microsoft Azure"
	description="Erfahren Sie, wie Sie Azure Backup mit PowerShell bereitstellen und verwalten."
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="cfreeman"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="markgal;trinadhk;jimpark" />


# Bereitstellen und Verwalten von Sicherungen für Azure-VMs mit PowerShell

> [AZURE.SELECTOR]
- [Ressourcen-Manager](backup-azure-vms-automation.md)
- [Klassisch](backup-azure-vms-classic-automation.md)

In diesem Artikel wird beschrieben, wie Sie Azure PowerShell zum Sichern und Wiederherstellen von Azure-VMs verwenden. Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: Resource Manager und klassische Bereitstellungen. Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells.

## Konzepte


Dieser Artikel enthält Informationen speziell zu den PowerShell-Cmdlets, die zum Sichern von virtuellen Computern verwendet werden. Einführungsinformationen zum Schützen von Azure-VMs finden Sie unter [Planen der Sicherungsinfrastruktur für virtuelle Computer in Azure](backup-azure-vms-introduction.md).

> [AZURE.NOTE] Bevor Sie beginnen, machen Sie sich mit den [Voraussetzungen](backup-azure-vms-prepare.md) zur Arbeit mit Azure Backup und den [Einschränkungen](backup-azure-vms-prepare.md#limitations) der aktuellen Lösung zur Sicherung virtueller Computer vertraut.

Um PowerShell effektiv zu nutzen, ist es notwendig, dass Sie sich kurz mit der Objekthierarchie vertraut machen und wissen, womit Sie beginnen sollten.

![Objekthierarchie](./media/backup-azure-vms-classic-automation/object-hierarchy.png)

Die beiden wichtigsten Abläufe sind das Aktivieren des Schutzes für einen virtuellen Computer und das Wiederherstellen von Daten von einem Wiederherstellungspunkt aus. Der Schwerpunkt dieses Artikels liegt darauf, Ihnen die erforderlichen Kenntnisse für die Arbeit mit den PowerShell-Cmdlets zu vermitteln, um diese beiden Szenarien zu ermöglichen.


## Einrichtung und Registrierung
Vorbereitung:

1. [Laden Sie die neueste PowerShell herunter](https://github.com/Azure/azure-powershell/releases) (erforderliche Mindestversion ist 1.0.0).

2. Ermitteln Sie die Azure-PowerShell-Cmdlets zur Datensicherung mithilfe des folgenden Befehls:

```
PS C:\> Get-Command *azurermbackup*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmBackupItem                           1.0.1      AzureRM.Backup
Cmdlet          Disable-AzureRmBackupProtection                    1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupContainerReregistration        1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupProtection                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupContainer                         1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupItem                              1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJob                               1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJobDetails                        1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupRecoveryPoint                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVaultCredentials                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupRetentionPolicyObject             1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Register-AzureRmBackupContainer                    1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupProtectionPolicy               1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupVault                          1.0.1      AzureRM.Backup
Cmdlet          Restore-AzureRmBackupItem                          1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Stop-AzureRmBackupJob                              1.0.1      AzureRM.Backup
Cmdlet          Unregister-AzureRmBackupContainer                  1.0.1      AzureRM.Backup
Cmdlet          Wait-AzureRmBackupJob                              1.0.1      AzureRM.Backup
```

Die folgenden Installations- und Registrierungsaufgaben können mit PowerShell automatisiert werden:

- Erstellen eines Sicherungstresors
- Registrieren der virtuellen Computer beim Azure Backup-Dienst

### Erstellen eines Sicherungstresors

> [AZURE.WARNING] Kunden, die Azure Backup zum ersten Mal verwenden, müssen den Azure Backup-Anbieter registrieren, der mit ihrem Abonnement verwendet werden soll. Führen Sie hierzu den folgenden Befehl aus: Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Backup"

Sie können mit dem Cmdlet **New-AzureRMBackupVault** einen neuen Sicherungstresor erstellen. Der Sicherungstresor ist eine ARM-Ressource. Deshalb müssen Sie ihn innerhalb einer Ressourcengruppe einfügen. Führen Sie die folgenden Befehle in einer Azure PowerShell-Konsole mit erhöhten Rechten aus:

```
PS C:\> New-AzureRmResourceGroup –Name “test-rg” –Location “West US”
PS C:\> $backupvault = New-AzureRmBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

Mit dem Cmdlet **Get-AzureRmBackupVault** können Sie eine Liste aller Sicherungstresore in einem bestimmten Abonnement abrufen.

> [AZURE.NOTE] Es ist sinnvoll, das Sicherungstresor-Objekt in einer Variablen zu speichern. Das Tresor-Objekt ist als Eingabe für viele Azure Backup-Cmdlets erforderlich.


### Registrieren der virtuellen Computer
Der erste Schritt zum Konfigurieren der Sicherung mit Azure Backup besteht darin, Ihren Computer oder virtuellen Computer bei einem Azure-Sicherungstresor zu registrieren. Das Cmdlet **Register-AzureRmBackupContainer** verwendet die Eingabeinformationen eines virtuellen Azure-IaaS-Computers, um diesen beim angegebenen Tresor zu registrieren. Der Registrierungsvorgang ordnet den virtuellen Azure-Computer dem Sicherungstresor zu und verfolgt den virtuellen Computer über den gesamten Sicherungslebenszyklus hinweg.

Durch die Registrierung des virtuellen Computers beim Azure Backup-Dienst wird ein Containerobjekt der obersten Ebene erstellt. Ein Container enthält i. d. R. mehrere Elemente, die gesichert werden können. Bei virtuellen Computern enthält der Container jedoch nur ein Sicherungselement.

```
PS C:\> $registerjob = Register-AzureRmBackupContainer -Vault $backupvault -Name "testvm" -ServiceName "testvm"
```

## Sichern von virtuellen Azure-Computern

### Erstellen einer Schutzrichtlinie
Es ist nicht obligatorisch, eine neue Schutzrichtlinie zu erstellen, um die Sicherung von virtuellen Computern zu starten. Der Tresor enthält eine Standardrichtlinie, die für eine schnelle Aktivierung des Schutzes verwendet und dann später bearbeitet werden kann, wenn die richtigen Details zur Verfügung stehen. Mit dem Cmdlet **Get-AzureRmBackupProtectionPolicy** können Sie eine Liste der im Tresor verfügbaren Richtlinien abrufen:

```
PS C:\> Get-AzureRmBackupProtectionPolicy -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DefaultPolicy             AzureVM            Daily              26-Aug-15 12:30:00 AM
```

> [AZURE.NOTE] Die Zeitzone des Feldes „BackupTime“ in PowerShell ist UTC. Wenn jedoch der Zeitpunkt der Sicherung im Azure-Portal angezeigt wird, wird die Zeitzone auf Ihr lokales System zusammen mit der UTC-Abweichung ausgerichtet.

Eine Sicherungsrichtlinie ist mindestens einer Aufbewahrungsrichtlinie zugeordnet. Die Aufbewahrungsrichtlinie definiert, wie lange ein Wiederherstellungspunkt in Azure Backup gespeichert wird. Das Cmdlet **New-AzureRmBackupRetentionPolicy** erstellt PowerShell-Objekte, die Informationen zu Aufbewahrungsrichtlinien enthalten. Diese Aufbewahrungsrichtlinienobjekte werden als Eingaben für das Cmdlet *New-AzureRmBackupProtectionPolicy* oder direkt mit dem Cmdlet *Enable-AzureRmBackupProtection* verwendet.

Eine Sicherungsrichtlinie definiert, wann und wie oft die Sicherung eines Elements erfolgt. Das Cmdlet **New-AzureRmBackupProtectionPolicy** erstellt ein PowerShell-Objekt, das Informationen zu Sicherungsrichtlinien enthält. Die Sicherungsrichtlinie wird als Eingabe für das Cmdlet *Enable-AzureRmBackupProtection* verwendet.

```
PS C:\> $Daily = New-AzureRmBackupRetentionPolicyObject -DailyRetention -Retention 30
PS C:\> $newpolicy = New-AzureRmBackupProtectionPolicy -Name DailyBackup01 -Type AzureVM -Daily -BackupTime ([datetime]"3:30 PM") -RetentionPolicy $Daily -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DailyBackup01             AzureVM            Daily              01-Sep-15 3:30:00 PM
```

### Schutz aktivieren
Zum Aktivieren des Schutzes sind zwei Objekte erforderlich: das Element und die Richtlinie, die beide zum selben Tresor gehören müssen. Sobald die Richtlinie mit dem Element verknüpft wurde, wird der Sicherungsworkflow nach dem definierten Zeitplan gestartet.

```
PS C:\> Get-AzureRmBackupContainer -Type AzureVM -Status Registered -Vault $backupvault | Get-AzureRmBackupItem | Enable-AzureRmBackupProtection -Policy $newpolicy
```

### Erste Sicherung
Der Sicherungszeitplan sorgt dafür, dass die erste vollständige Kopie des Elements sowie die inkrementelle Kopie für die nachfolgenden Sicherungen erstellt werden. Wenn Sie die erste Sicherung jedoch zu einem bestimmten Zeitpunkt oder sogar sofort erzwingen möchten, verwenden Sie das Cmdlet **Backup-AzureRmBackupItem**:

```
PS C:\> $container = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -Name "testvm"
PS C:\> $backupjob = Get-AzureRmBackupItem -Container $container | Backup-AzureRmBackupItem
PS C:\> $backupjob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

> [AZURE.NOTE] Die Zeitzone der Felder „StartTime“ und „EndTime“ in PowerShell ist UTC. Wenn jedoch ähnliche Informationen im Azure-Portal angezeigt werden, wird die Zeitzone auf die lokale Systemuhr ausgerichtet.

### Überwachen eines Sicherungsauftrags
Die meisten Vorgänge mit langer Ausführungszeit werden in Azure Backup als Auftrag modelliert. So kann der Fortschritt auf einfache Weise nachverfolgt werden, ohne dass das Azure-Portal immer geöffnet bleiben muss.

Mit dem Cmdlet **Get-AzureRmBackupJob** können Sie den aktuellen Status eines Auftrags abrufen, der sich in Bearbeitung befindet.

```
PS C:\> $joblist = Get-AzureRmBackupJob -Vault $backupvault -Status InProgress
PS C:\> $joblist[0]

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

Anstatt die Fertigstellung dieser Aufträge abzufragen, was unnötigen, zusätzlichen Code bedeutet, ist es einfacher, das Cmdlet **Wait-AzureRmBackupJob** zu verwenden. Bei Verwendung in einem Skript hält das Cmdlet die Ausführung an, bis entweder der Auftrag abgeschlossen oder der angegebene Timeoutwert erreicht ist.

```
PS C:\> Wait-AzureRmBackupJob -Job $joblist[0] -Timeout 43200
```


## Wiederherstellen eines virtuellen Azure-Computers

Um die gesicherten Daten wiederherstellen zu können, müssen Sie das gesicherte Element und den Wiederherstellungspunkt angeben, der die Zeitpunktdaten enthält. Diese Informationen werden für das Cmdlet „Restore-AzureRmBackupItem“ bereitgestellt, um eine Wiederherstellung von Daten aus dem Tresor im Konto des Kunden zu initiieren.

### Auswählen des virtuellen Computers

Zum Abrufen des PowerShell-Objekts, das das richtige Sicherungselement identifiziert, müssen Sie vom Container im Tresor aus starten und dann die Objekthierarchie nach unten durchlaufen. Um den Container auszuwählen, der den virtuellen Computer darstellt, verwenden Sie das Cmdlet **Get-AzureRmBackupContainer** und reichen dieses an das Cmdlet **Get-AzureRmBackupItem** weiter.

```
PS C:\> $backupitem = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -name "testvm" | Get-AzureRmBackupItem
```

### Auswählen eines Wiederherstellungspunkts

Sie können jetzt alle Wiederherstellungspunkte für das Sicherungselement mithilfe des Cmdlets **Get-AzureRmBackupRecoveryPoint** auflisten und anschließend den Wiederherstellungspunkt auswählen. Üblicherweise wählen Benutzer den neuesten *AppConsistent*-Punkt in der Liste aus.

```
PS C:\> $rp =  Get-AzureRmBackupRecoveryPoint -Item $backupitem
PS C:\> $rp

RecoveryPointId    RecoveryPointType  RecoveryPointTime      ContainerName
---------------    -----------------  -----------------      -------------
15273496567119     AppConsistent      01-Sep-15 12:27:38 PM  iaasvmcontainer;testvm;testv...
```

Die Variable ```$rp``` ist ein Array mit Wiederherstellungspunkten für das ausgewählte Sicherungselement, und zwar in zeitlich umgekehrter Reihenfolge sortiert, d. h. der letzte Wiederherstellungspunkt befindet sich an Index 0. Verwenden Sie die standardmäßige PowerShell-Arrayindizierung zum Auswählen des Wiederherstellungspunkts. Beispiel: ```$rp[0]``` wählt den letzten Wiederherstellungspunkt aus.

### Wiederherstellen von Datenträgern

Es besteht ein entscheidender Unterschied zwischen Wiederherstellungsvorgängen, die über das Azure-Portal ausgeführt werden, und Wiederherstellungsvorgängen, die mit Azure PowerShell ausgeführt werden. Mit PowerShell endet der Wiederherstellungsvorgang mit der Wiederherstellung der Datenträger und der Konfigurationsinformationen aus dem Wiederherstellungspunkt. Es wird kein virtueller Computer erstellt.

> [AZURE.WARNING] Mit „Restore-AzureRmBackupItem“ wird kein virtueller Computer erstellt. Es werden nur die Datenträger im angegebenen Speicherkonto wiederhergestellt. Dies ist ein anderes Verhalten als beim Azure-Portal.

```
PS C:\> $restorejob = Restore-AzureRmBackupItem -StorageAccountName "DestAccount" -RecoveryPoint $rp[0]
PS C:\> $restorejob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Restore         InProgress      01-Sep-15 1:14:01 PM   01-Jan-01 12:00:00 AM
```

Nachdem der Wiederherstellungsauftrag abgeschlossen ist, können Sie die Details des Wiederherstellungsvorgangs mit dem Cmdlet **Get-AzureRmBackupJobDetails** abrufen. Die *ErrorDetails*-Eigenschaft enthält die Informationen, die zum erneuten Erstellen des virtuellen Computers benötigt werden.

```
PS C:\> $restorejob = Get-AzureRmBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmBackupJobDetails -Job $restorejob
```

### Erstellen des virtuellen Computers

Das Erstellen des virtuellen Computers aus den wiederhergestellten Datenträgern kann mithilfe der älteren Azure Service Management-PowerShell-Cmdlets, der neuen Azure Resource Manager-Vorlagen oder sogar über das Azure-Portal erfolgen. Das folgende kurze Beispiel zeigt die Vorgehensweise mithilfe der Azure Service Management-Cmdlets.

```
 $properties  = $details.Properties

 $storageAccountName = $properties["Target Storage Account Name"]
 $containerName = $properties["Config Blob Container Name"]
 $blobName = $properties["Config Blob Name"]

 $keys = Get-AzureStorageKey -StorageAccountName $storageAccountName
 $storageAccountKey = $keys.Primary
 $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey


 $destination_path = "C:\Users\admin\Desktop\vmconfig.xml"
 Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path -Context $storageContext


$obj = [xml](((Get-Content -Path $destination_path -Encoding UniCode)).TrimEnd([char]0x00))
 $pvr = $obj.PersistentVMRole
 $os = $pvr.OSVirtualHardDisk
 $dds = $pvr.DataVirtualHardDisks
 $osDisk = Add-AzureDisk -MediaLocation $os.MediaLink -OS $os.OS -DiskName "panbhaosdisk"
 $vm = New-AzureVMConfig -Name $pvr.RoleName -InstanceSize $pvr.RoleSize -DiskName $osDisk.DiskName

 if (!($dds -eq $null))
 {
	 foreach($d in $dds.DataVirtualHardDisk)
 	 {
		 $lun = 0
		 if(!($d.Lun -eq $null))
		 {
	 		 $lun = $d.Lun
		 }
		 $name = "panbhadataDisk" + $lun
     Add-AzureDisk -DiskName $name -MediaLocation $d.MediaLink
     $vm | Add-AzureDataDisk -Import -DiskName $name -LUN $lun
	}
}

New-AzureVM -ServiceName "panbhasample" -Location "SouthEast Asia" -VM $vm
```

Weitere Informationen zum Erstellen eines virtuellen Computers aus den wiederhergestellten Datenträgern finden Sie in den Artikeln zu den folgenden Cmdlets:

- [Add-AzureDisk](https://msdn.microsoft.com/library/azure/dn495252.aspx)
- [New-AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx)
- [New-AzureVM,](https://msdn.microsoft.com/library/azure/dn495254.aspx)

## Codebeispiele

### 1\. Abrufen des Abschlussstatus von untergeordneten Aufgaben

Um den Abschlussstatus der einzelnen untergeordneten Aufgaben nachzuverfolgen, können Sie das Cmdlet **Get-AzureRmBackupJobDetails** verwenden:

```
PS C:\> $details = Get-AzureRmBackupJobDetails -JobId $backupjob.InstanceId -Vault $backupvault
PS C:\> $details.SubTasks

Name                                                        Status
----                                                        ------
Take Snapshot                                               Completed
Transfer data to Backup vault                               InProgress
```

### 2\. Erstellen eines täglichen/wöchentlichen Berichts mit Sicherungsaufträgen

In der Regel möchten Administratoren wissen, welche Sicherungsaufträge in den letzten 24 Stunden ausgeführt wurden, und den Status dieser Sicherungsaufträge erhalten. Darüber hinaus gibt die Menge der übertragenen Daten Administratoren die Möglichkeit, ihre monatliche Datenverwendung einzuschätzen. Das folgende Skript ruft die Rohdaten aus dem Azure Backup-Dienst ab und zeigt die Informationen in der PowerShell-Konsole an.

```
param(  [Parameter(Mandatory=$True,Position=1)]
        [string]$backupvaultname,

        [Parameter(Mandatory=$False,Position=2)]
        [int]$numberofdays = 7)


#Initialize variables
$DAILYBACKUPSTATS = @()
$backupvault = Get-AzureRmBackupVault -Name $backupvaultname
$enddate = ([datetime]::Today).AddDays(1)
$startdate = ([datetime]::Today)

for( $i = 1; $i -le $numberofdays; $i++ )
{
    # We query one day at a time because pulling 7 days of data might be too much
    $dailyjoblist = Get-AzureRmBackupJob -Vault $backupvault -From $startdate -To $enddate -Type AzureVM -Operation Backup
    Write-Progress -Activity "Getting job information for the last $numberofdays days" -Status "Day -$i" -PercentComplete ([int]([decimal]$i*100/$numberofdays))

    foreach( $job in $dailyjoblist )
    {
        #Extract the information for the reports
        $newstatsobj = New-Object System.Object
        $newstatsobj | Add-Member -Type NoteProperty -Name Date -Value $startdate
        $newstatsobj | Add-Member -Type NoteProperty -Name VMName -Value $job.WorkloadName
        $newstatsobj | Add-Member -Type NoteProperty -Name Duration -Value $job.Duration
        $newstatsobj | Add-Member -Type NoteProperty -Name Status -Value $job.Status

        $details = Get-AzureRmBackupJobDetails -Job $job
        $newstatsobj | Add-Member -Type NoteProperty -Name BackupSize -Value $details.Properties["Backup Size"]
        $DAILYBACKUPSTATS += $newstatsobj
    }

    $enddate = $enddate.AddDays(-1)
    $startdate = $startdate.AddDays(-1)
}

$DAILYBACKUPSTATS | Out-GridView
```

Wenn Sie der Berichtsausgabe Diagrammfunktionen hinzufügen möchten, erfahren Sie Näheres im TechNet-Blog unter [Charting with PowerShell](http://blogs.technet.com/b/richard_macdonald/archive/2009/04/28/3231887.aspx) (Diagrammerstellung mit PowerShell).

## Nächste Schritte

Falls Sie PowerShell für die Arbeit mit Azure-Ressourcen vorziehen, lesen Sie den PowerShell-Artikel für den Schutz von Windows Server: [Bereitstellen und Verwalten der Sicherung in Azure für Windows Server-/Windows-Clientcomputer mit PowerShell](./backup-client-automation-classic.md). Es gibt auch einen PowerShell-Artikel über das Verwalten von DPM-Sicherungen: [Bereitstellen und Verwalten der Sicherung für DPM](./backup-dpm-automation-classic.md). Diese beiden Artikel weisen eine Version für Resource Manager-Bereitstellungen und eine für klassische Bereitstellungen auf.

<!---HONumber=AcomDC_0810_2016-->