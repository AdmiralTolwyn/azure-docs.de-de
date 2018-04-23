---
title: Verschlüsseln von Datenträgern auf einem virtuellen Windows-Computer in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie virtuelle Datenträger auf einem virtuellen Windows-Computer mithilfe von Azure PowerShell verschlüsseln, um die Sicherheit zu erhöhen.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/07/2018
ms.author: iainfou
ms.openlocfilehash: 442ff942150af8a8dec89164fbc017a9e6f360e8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-encrypt-virtual-disks-on-a-windows-vm"></a>Verschlüsseln virtueller Datenträger auf einem virtuellen Windows-Computer
Zum Verbessern der Sicherheit und Compliance von virtuellen Computern können virtuelle Datenträger in Azure verschlüsselt werden. Die Verschlüsselung der Datenträger basiert auf kryptografischen Schlüsseln, die in Azure Key Vault gesichert werden. Diese kryptografischen Schlüssel werden von Ihnen kontrolliert, und Sie können deren Verwendung überwachen. In diesem Artikel wird erläutert, wie Sie virtuelle Datenträger auf einem virtuellen Windows-Computer mithilfe von Azure PowerShell verschlüsseln. Sie können auch [Datenträger auf einem virtuellen Linux-Computer mithilfe von Azure CLI 2.0 verschlüsseln](../linux/encrypt-disks.md).

## <a name="overview-of-disk-encryption"></a>Übersicht über die Datenträgerverschlüsselung
Virtuelle Datenträger auf virtuellen Windows-Computern werden im Ruhezustand mithilfe von BitLocker verschlüsselt. Für die Verschlüsselung virtueller Datenträger in Azure fallen keine Gebühren an. Kryptografische Schlüssel werden in Azure Key Vault mit Softwareschutz gespeichert. Alternativ können Sie Schlüssel aber auch in Hardwaresicherheitsmodulen (HSMs) mit FIPS 140-2 Level 2-Zertifizierung importieren oder generieren. Die kryptografischen Schlüssel dienen zum Verschlüsseln und Entschlüsseln virtueller Datenträger, die an Ihren virtuellen Computer angefügt sind. Diese kryptografischen Schlüssel werden allein von Ihnen kontrolliert, und Sie können deren Verwendung überwachen. Über einen Azure Active Directory-Dienstprinzipal werden die kryptografischen Schlüssel beim Ein- und Ausschalten virtueller Computer sicher ausgegeben.

Gehen Sie zum Verschlüsseln eines virtuellen Computers wie folgt vor:

1. Erstellen Sie in einer Azure Key Vault-Instanz einen kryptografischen Schlüssel.
2. Konfigurieren Sie den kryptografischen Schlüssel so, dass er zum Verschlüsseln von Datenträgern verwendet werden kann.
3. Erstellen Sie einen Azure Active Directory-Dienstprinzipal mit geeigneten Berechtigungen, um den kryptografischen Schlüssel aus der Azure Key Vault-Instanz zu lesen.
4. Führen Sie den Befehl zum Verschlüsseln Ihrer virtuellen Datenträger aus. Geben Sie dabei den Azure Active Directory-Dienstprinzipal und den geeigneten kryptografischen Schlüssel an.
5. Der Azure Active Directory-Dienstprinzipal fordert den erforderlichen kryptografischen Schlüssel von Azure Key Vault an.
6. Die virtuellen Datenträger werden mithilfe des angegebenen kryptografischen Schlüssels verschlüsselt.

## <a name="encryption-process"></a>Verschlüsselungsvorgang
Für die Datenträgerverschlüsselung werden folgende zusätzliche Komponenten benötigt:

* **Azure Key Vault:** Schützt die für die Datenträgerverschlüsselung/-entschlüsselung verwendeten kryptografischen Schlüssel und Geheimnisse. 
  * Sie können eine ggf. bereits vorhandene Azure Key Vault-Instanz verwenden. Für die Datenträgerverschlüsselung wird keine dedizierte Key Vault-Instanz benötigt.
  * Zur Trennung administrativer Grenzen und der Schlüsselsichtbarkeit können Sie eine dedizierte Key Vault-Instanz erstellen.
* **Azure Active Directory:** Wickelt den sicheren Austausch der erforderlichen kryptografischen Schlüssel und die Authentifizierung für angeforderte Aktionen ab. 
  * In der Regel können Sie Ihre Anwendung in einer bereits vorhandenen Instanz von Azure Active Directory platzieren.
  * Der Dienstprinzipal bietet einen sicheren Mechanismus zum Anfordern und Ausgeben der geeigneten kryptografischen Schlüssel. Sie entwickeln im eigentlichen Sinne keine Anwendung, die in Azure Active Directory integriert wird.

## <a name="requirements-and-limitations"></a>Voraussetzungen und Einschränkungen
Unterstützte Szenarien und Voraussetzungen für die Datenträgerverschlüsselung:

* Aktivieren der Verschlüsselung auf neuen virtuellen Windows-Computern über Azure Marketplace-Images oder benutzerdefinierte VHD-Images
* Aktivieren der Verschlüsselung auf vorhandenen virtuellen Windows-Computern in Azure
* Aktivieren der Verschlüsselung auf virtuellen Windows-Computern, die mithilfe von Speicherplätzen konfiguriert sind
* Deaktivieren der Verschlüsselung auf Betriebssystem- und Datenlaufwerken für virtuelle Windows-Computer
* Alle Ressourcen (wie Key Vault, Speicherkonto und virtueller Computer) müssen sich in der gleichen Azure-Region und im gleichen Abonnement befinden.
* Virtuelle Computer im Standard-Tarif, z.B. virtuelle Computer der Reihen A, D, DS, G und GS

Die Datenträgerverschlüsselung wird derzeit in folgenden Szenarien nicht unterstützt:

* Bei virtuellen Computern des Basic-Tarifs
* Bei virtuellen Computern, die mit dem klassischen Bereitstellungsmodell erstellt wurden
* Bei Aktualisierung der kryptografischen Schlüssel auf einem bereits verschlüsselten virtuellen Computer
* Bei Integration in den lokalen Schlüsselverwaltungsdienst

## <a name="create-azure-key-vault-and-keys"></a>Erstellen der Azure Key Vault-Instanz und der Schlüssel
Stellen Sie vor Beginn sicher, dass die neueste Version des Azure PowerShell-Moduls installiert ist. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview). Ersetzen Sie bei den Befehlsbeispielen alle Beispielparameter durch Ihre eigenen Namen, Orte und Schlüsselwerte. Die folgenden Beispiele verwenden eine Konvention von *myResourceGroup*, *myKeyVault*, *myVM* usw.

Erstellen Sie zum Speichern Ihrer kryptografischen Schlüssel zunächst eine Azure Key Vault-Instanz. In Azure Key Vault können Schlüssel, geheime Schlüssel und Kennwörter gespeichert werden, um eine sichere Implementierung in Anwendungen und Diensten zu ermöglichen. Bei der Verschlüsselung virtueller Datenträger erstellen Sie einen Schlüsseltresor zum Speichern eines kryptografischen Schlüssels, der zum Verschlüsseln oder Entschlüsseln der virtuellen Datenträger verwendet wird. 

Aktivieren Sie in Ihrem Azure-Abonnement mit [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) den Azure Key Vault-Anbieter, und erstellen Sie mit [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) eine Ressourcengruppe. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *USA, Osten*:

```powershell
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

Die Azure Key Vault-Instanz mit den kryptografischen Schlüsseln und die dazugehörigen Computeressourcen wie Speicher und der eigentliche virtuelle Computer müssen sich in der gleichen Region befinden. Erstellen Sie mit [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) ein Azure Key Vault, und aktivieren Sie KEy Vault für die Verwendung mit Datenträgerverschlüsselung. Geben Sie einen eindeutigen Schlüsseltresornamen für *keyVaultName* an, wie nachfolgend gezeigt:

```powershell
$keyVaultName = "myUniqueKeyVaultName"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Kryptografische Schlüssel können mit Softwareschutz oder mit HSM-Schutz (Hardwaresicherheitsmodul) gespeichert werden. Für die Verwendung eines HSMs wird eine Key Vault-Premiuminstanz benötigt. Die Erstellung einer Key Vault-Premiuminstanz ist im Gegensatz zur Verwendung einer Key Vault-Standardinstanz, bei der Schlüssel mit Softwareschutz gespeichert werden, mit zusätzlichen Kosten verbunden. Wenn Sie eine Key Vault-Premiuminstanz erstellen möchten, fügen Sie im vorherigen Schritt die Parameter *-Sku "Premium"* hinzu. Im folgenden Beispiel werden softwaregeschützte Schlüssel verwendet, da wir eine Key Vault-Standardinstanz erstellt haben. 

Bei beiden Schutzmodellen muss der Azure-Plattform Zugriff gewährt werden, um beim Start des virtuellen Computers die kryptografischen Schlüssel anfordern und die virtuellen Datenträger entschlüsseln zu können. Erstellen Sie mithilfe von [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) einen kryptografischen Schlüssel in Ihrem Schlüsseltresor. Im folgenden Beispiel wird ein Schlüssel mit dem Namen *myKey* erstellt:

```powershell
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```


## <a name="create-the-azure-active-directory-service-principal"></a>Erstellen des Azure Active Directory-Dienstprinzipals
Wenn virtuelle Datenträger verschlüsselt oder entschlüsselt werden, geben Sie ein Konto an, mit dem die Authentifizierung und der Austausch kryptografischer Schlüssel aus Key Vault abgewickelt werden. Dieses Konto, ein Azure Active Directory-Dienstprinzipal, ermöglicht es der Azure-Plattform, im Auftrag des virtuellen Computers die geeigneten kryptografischen Schlüssel anzufordern. In Ihrem Abonnement steht zwar eine Azure Active Directory-Standardinstanz zur Verfügung, viele Organisationen verwenden jedoch dedizierte Azure Active Directory-Verzeichnisse.

Erstellen Sie mit [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) einen Dienstprinzipal in Azure Active Directory. Orientieren Sie sich bei der Angabe eines sicheren Kennworts an den [Kennwortrichtlinien und -einschränkungen in Azure Active Directory](../../active-directory/authentication/concept-sspr-policy.md):

```powershell
$appName = "My App"
$securePassword = ConvertTo-SecureString -String "P@ssw0rd!" -AsPlainText -Force
$app = New-AzureRmADApplication -DisplayName $appName `
    -HomePage "https://myapp.contoso.com" `
    -IdentifierUris "https://contoso.com/myapp" `
    -Password $securePassword
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
```

Zum erfolgreichen Ver- und Entschlüsseln virtueller Datenträger müssen Berechtigungen für den in Key Vault gespeicherten kryptografischen Schlüssel festgelegt werden, damit der Azure Active Directory-Dienstprinzipal die Schlüssel lesen kann. Legen Sie mit [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) Berechtigungen für Ihren Schlüsseltresor fest:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyvaultName `
    -ServicePrincipalName $app.ApplicationId `
    -PermissionsToKeys "WrapKey" `
    -PermissionsToSecrets "Set"
```


## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers
Um den Verschlüsselungsvorgang zu testen, erstellen Sie einen virtuellen Computer mit [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm). Im folgenden Beispiel wird unter Verwendung eines *Windows Server 2016 Datacenter*-Images der virtuelle Computer *myVM* erstellt. Geben Sie bei entsprechender Aufforderung den Benutzernamen und das Kennwort für den virtuellen Computer ein:

```powershell
$cred = Get-Credential

New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-virtual-machine"></a>Verschlüsseln eines virtuellen Computers
Um die virtuellen Datenträger zu verschlüsseln, müssen alle vorherigen Komponenten miteinander kombiniert werden:

1. Geben Sie den Azure Active Directory-Dienstprinzipal und das Kennwort an.
2. Geben Sie die Key Vault-Instanz zum Speichern der Metadaten Ihrer verschlüsselten Datenträger an.
3. Geben Sie die kryptografischen Schlüssel für die eigentliche Ver- und Entschlüsselung an.
4. Geben Sie an, ob Sie den Betriebssystem-Datenträger, die allgemeinen Datenträger oder alles verschlüsseln möchten.

Verschlüsseln Sie Ihren virtuellen Computer unter Verwendung des Azure Key Vault-Schlüssels und der Anmeldeinformationen für den Azure Active Directory-Dienstprinzipal mit [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension). Im folgenden Beispiel werden alle Schlüsselinformationen abgerufen und anschließend der virtuelle Computer *myVM* verschlüsselt:

```powershell
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -AadClientID $app.ApplicationId `
    -AadClientSecret (New-Object PSCredential "user",$securePassword).GetNetworkCredential().Password `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Bestätigen Sie die Aufforderung zum Fortsetzen der Verschlüsselung des virtuellen Computers. Der virtuelle Computer wird während des Vorgangs neu gestartet. Nach Abschluss des Verschlüsselungsvorgangs und dem Neustart des virtuellen Computers können Sie den Verschlüsselungsstatus mit [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) überprüfen:

```powershell
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```powershell
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Verwalten von Azure Key Vault finden Sie unter [Einrichten von Key Vault für virtuelle Computer](key-vault-setup.md).
* Weitere Informationen zur Datenträgerverschlüsselung (etwa zum Vorbereiten des Uploads eines verschlüsselten benutzerdefinierten virtuellen Computers in Azure) finden Sie unter [Azure-Datenträgerverschlüsselung für virtuelle Windows- und Linux-IaaS-Computer](../../security/azure-security-disk-encryption.md).
