---
title: "Wiederherstellen von Key Vault-Schlüssel und -Geheimnis für verschlüsselte virtuelle Computer mithilfe von Azure Backup | Microsoft Docs"
description: "Informationen zum Wiederherstellen von Key Vault-Schlüssel und -Geheimschlüssel in Azure Backup mithilfe von PowerShell"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 45214083-d5fc-4eb3-a367-0239dc59e0f6
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2db3449187d655248b13198b268841052570626
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/29/2017
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Wiederherstellen von Key Vault-Schlüssel und -Geheimschlüssel für verschlüsselte virtuelle Computer mithilfe von Azure Backup
Dieser Artikel befasst sich mit Azure VM Backup für die Wiederherstellung von verschlüsselten Azure-VMs, wenn Schlüssel und Geheimschlüssel nicht im Schlüsseltresor vorhanden sind. Diese Schritte können auch verwendet werden, wenn Sie eine separate Kopie von Schlüssel (Key Encryption Key) und Geheimschlüssel (BitLocker-Verschlüsselungsschlüssel) für den wiederhergestellten virtuellen Computer verwalten möchten.

## <a name="prerequisites"></a>Voraussetzungen
* **Sicherung verschlüsselter VMs** – Verschlüsselte Azure-VMs wurden mithilfe von Azure Backup gesichert. Finden Sie im Artikel [Sichern und Wiederherstellen von Azure-VMs mithilfe von PowerShell](backup-azure-vms-automation.md) ausführliche Informationen zur Sicherung verschlüsselter Azure-VMs.
* **Konfigurieren von Azure Key Vault** – Stellen Sie sicher, dass der Schlüsseltresor, für den Schlüssel und Geheimschlüssel wiederhergestellt werden müssen, bereits vorhanden ist. Finden Sie im Artikel [Erste Schritte mit Azure Key Vault](../key-vault/key-vault-get-started.md) ausführliche Informationen zum Verwalten des Schlüsseltresors.
* **Wiederherstellen von Datenträger** -stellen Sie sicher, dass Sie den Wiederherstellungsauftrag für die Wiederherstellung von Datenträgern für die Verwendung von verschlüsselten VM ausgelöst haben [PowerShell-Schritte](backup-azure-vms-automation.md#restore-an-azure-vm). Dies ist notwendig, da bei diesem Auftrag eine JSON-Datei in Ihrem Speicherkonto generiert wird, die Schlüssel und Geheimnisse für die verschlüsselte wiederherzustellende VM enthält.

## <a name="get-key-and-secret-from-azure-backup"></a>Abrufen von Schlüsseln und Geheimnissen aus Azure Backup

> [!NOTE]
> Sobald der Datenträger für die verschlüsselte VM wiederhergestellt wurde, überprüfen Sie Folgendes ein:
> 1. „$details“ ist mit Auftragsdetails zur Wiederherstellung von Datenträgern aufgefüllt, wie unter [Schritte zu PowerShell im Abschnitt zur Wiederherstellung von Datenträgern](backup-azure-vms-automation.md#restore-an-azure-vm) beschrieben wird.
> 2. Die VM sollte erst über wiederhergestellte Datenträger erstellt werden, **wenn der Schlüssel und das Geheimnis im Schlüsseltresor wiederhergestellt wurden**.
>
>

Fragen Sie die Eigenschaften des wiederhergestellten Datenträgers für die Auftragsdetails ab.

```
PS C:\> $properties = $details.properties
PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
PS C:\> $containerName = $properties["Config Blob Container Name"]
PS C:\> $encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Legen Sie den Kontext für Azure Storage fest, und stellen Sie die JSON-Konfigurationsdatei wieder her, die Details zu Schlüsseln und Geheimnissen für verschlüsselte VMs enthält.

```
PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
PS C:\> $destination_path = 'C:\vmencryption_config.json'
PS C:\> Get-AzureStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
PS C:\> $encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Schlüssel wiederherstellen
Nachdem die JSON-Datei unter dem oben genannten Zielpfad generiert wurde, generieren Sie die Schlüsselblobdatei aus der JSON-Datei. Geben Sie sie dann in das Cmdlet zum Wiederherstellen von Schlüsseln ein, um den Schlüssel (KEK) wieder im Schlüsseltresor zu platzieren.

```
PS C:\> $keyDestination = 'C:\keyDetails.blob'
PS C:\> [io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Geheimen Schlüssel wiederherstellen
Rufen Sie mithilfe der zuvor generierten JSON-Datei den Geheimnisnamen und -wert ab, und fügen Sie sie in das Cmdlet zum Festlegen von Geheimnissen ein, um das Geheimnis (BEK) wieder im Schlüsseltresor zu platzieren. **Verwenden Sie diese Cmdlets, wenn Ihr virtueller Computer mit BEK und Schlüsselaustauschschlüssel verschlüsselt ist.**

```
PS C:\> $secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Wenn Ihr virtueller Computer ist **verschlüsselt BEK nur über**, geheimen Blob-Datei aus den JSON-Code generieren und es zum Wiederherstellen der geheimen Cmdlet aus, um den geheimen Schlüssel (BEK) wieder im schlüsseltresor put-feed.

```
PS C:\> $secretDestination = 'C:\secret.blob'
PS C:\> [io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
PS C:\> Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
```

> [!NOTE]
> 1. Der Wert für „$secretname“ kann über die Ausgabe von „$encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl“ und den Text nach „secrets/“ abgerufen werden, z.B. lautet die Geheimnis-URL der Ausgabe „https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163“ und der Geheimnisname „B3284AAA-DAAA-4AAA-B393-60CAA848AAAA“.
> 2. Der Wert der Markierung „DiskEncryptionKeyFileName“ ist mit dem Namen des Geheimnisses identisch.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Erstellen eines virtuellen Computers über einen wiederhergestellten Datenträger
Wenn Sie verschlüsselte VM, die mithilfe von Azure VM Backup gesichert haben, erwähnten die PowerShell-Cmdlets Help Schlüssel und geheimen wieder zurück auf den schlüsseltresor wiederhergestellt werden. Nach der Wiederherstellung werden, finden Sie im Artikel [verwalten, Sichern und Wiederherstellen von Azure-VMs, die mithilfe von PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) verschlüsselte VMs aus wiederhergestellten Datenträger, Schlüssel und geheimen Schlüssel zu erstellen.

## <a name="legacy-approach"></a>Legacy-Methode
Die oben genannte Ansatz funktioniert für alle Wiederherstellungspunkte an. Der ältere Ansatz der erste Schlüssel und geheimen Daten von Wiederherstellungspunkten erfolgen wäre jedoch für Wiederherstellungspunkte, die älter sind als 11 Juli 2017 für VMs mit BEK und Schlüsselaustauschschlüssel verschlüsselt gültig. Nachdem der Auftrag zur Wiederherstellung von Datenträgern für die verschlüsselte VM mithilfe der Anweisungen unter [Schritte zu PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm) abgeschlossen wurde, stellen Sie sicher, dass „$rp“ mit einem gültigen Wert aufgefüllt ist.

### <a name="restore-key"></a>Schlüssel wiederherstellen
Rufen Sie mithilfe der folgenden Cmdlets Informationen über Schlüssel (KEK) vom Wiederherstellungspunkt ab, und fügen Sie sie in das Cmdlet zum Wiederherstellen von Schlüsseln ein, um ihn wieder in den Schlüsseltresor zu platzieren.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Geheimen Schlüssel wiederherstellen
Rufen Sie mithilfe der folgenden Cmdlets Informationen über Geheimnisse (BEK) vom Wiederherstellungspunkt ab, und fügen Sie sie in das Cmdlet zum Festlegen von Geheimnissen ein, um es wieder im Schlüsseltresor zu platzieren.

```
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> 1. Der Wert für „$secretname“ kann über die Ausgabe von „$rp1.KeyAndSecretDetails.SecretUrl“ und den Text nach „secrets/“ abgerufen werden, z.B. lautet die Geheimnis-URL der Ausgabe „https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163“ und der Geheimnisname „B3284AAA-DAAA-4AAA-B393-60CAA848AAAA“.
> 2. Der Wert der Markierung „DiskEncryptionKeyFileName“ ist mit dem Namen des Geheimnisses identisch.
> 3. Der Wert für „DiskEncryptionKeyEncryptionKeyURL“ kann mithilfe des Cmdlet [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx) aus dem Schlüsseltresor abgerufen werden, nachdem die Schlüssel wiederhergestellt wurden.
>
>

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie den Schlüssel und das Geheimnis wieder im Schlüsseltresor wiederhergestellt haben, lesen Sie den Artikel [Verwalten von Sicherungen und Wiederherstellungen von Azure-VMs mithilfe von PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Dieser enthält ausführliche Informationen zum Erstellen verschlüsselter VMs über wiederhergestellte Datenträger, Schlüssel und Geheimnisse.
