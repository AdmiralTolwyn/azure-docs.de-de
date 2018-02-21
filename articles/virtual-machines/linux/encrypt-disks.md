---
title: "Verschlüsseln von Datenträgern auf einem virtuellen Linux-Computer in Azure | Microsoft-Dokumentation"
description: "Erfahren Sie, wie Sie virtuelle Datenträger auf einer Linux-VM mithilfe der Azure CLI 2.0 verschlüsseln, um die Sicherheit zu erhöhen."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: iainfou
ms.openlocfilehash: 4a10df360249b4b0b28ecbe4762bbb165ef9bb8d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-encrypt-virtual-disks-on-a-linux-vm"></a>Verschlüsseln virtueller Datenträger auf einer Linux-VM
Zum Verbessern der Sicherheit und Compliance von virtuellen Computern können virtuelle Datenträger und der virtuelle Computer selbst verschlüsselt werden. Die Verschlüsselung der virtuellen Computer basiert auf kryptografischen Schlüsseln, die in Azure Key Vault gesichert werden. Diese kryptografischen Schlüssel werden von Ihnen kontrolliert, und Sie können deren Verwendung überwachen. In diesem Artikel wird erläutert, wie Sie virtuelle Datenträger auf einer Linux-VM mithilfe der Azure CLI 2.0 verschlüsseln. Sie können diese Schritte auch per [Azure CLI 1.0](encrypt-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ausführen.

## <a name="quick-commands"></a>Schnellbefehle
Der folgende Abschnitt enthält Informationen zu den grundlegenden Befehlen für die Verschlüsselung virtueller Datenträger auf Ihrem virtuellen Computer. Ausführlichere Informationen und Kontext für die einzelnen Schritte finden Sie im übrigen Dokument ([ab hier](#overview-of-disk-encryption)).

Die neueste Version von [Azure CLI 2.0](/cli/azure/install-az-cli2) muss installiert sein, und Sie müssen mithilfe von [az login](/cli/azure/#az_login) bei einem Azure-Konto angemeldet sein. Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Beispielparameternamen sind u. a. *myResourceGroup*, *myKey* und *myVM*.

Aktivieren Sie zunächst in Ihrem Azure-Abonnement mit [az provider register](/cli/azure/provider#az_provider_register) den Azure Key Vault-Anbieter, und erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*:

```azurecli
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

Erstellen Sie mit [az keyvault create](/cli/azure/keyvault#az_keyvault_create) einen Azure-Schlüsseltresor, und aktivieren Sie den Schlüsseltresor zur Verwendung der Datenträgerverschlüsselung. Geben Sie einen eindeutigen Key Vault-Namen für *keyvault_name* an, wie nachfolgend gezeigt:

```azurecli
keyvault_name=myuniquekeyvaultname
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Erstellen Sie mithilfe von [az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create) einen kryptografischen Schlüssel in Ihrem Schlüsseltresor. Im folgenden Beispiel wird ein Schlüssel mit dem Namen *myKey* erstellt:

```azurecli
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```

Erstellen Sie mit [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) einen Dienstprinzipal unter Verwendung von Azure Active Directory. Der Dienstprinzipal übernimmt die Authentifizierung und sorgt für den Austausch kryptografischer Schlüssel aus Key Vault. Im folgenden Beispiel werden die Werte für die Dienstprinzipal-ID und das Kennwort zur späteren Verwendung in Befehlen gelesen:

```azurecli
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

Das Kennwort wird nur ausgegeben, wenn Sie den Dienstprinzipal erstellen. Falls gewünscht, zeigen Sie das Kennwort an, und notieren Sie es (`echo $sp_password`). Sie können Ihre Dienstprinzipale mit [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) auflisten und mit [az ad sp show](/cli/azure/ad/sp#az_ad_sp_show) zusätzliche Informationen zu einem bestimmten Dienstprinzipal anzeigen.

Legen Sie mit [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) Berechtigungen für Ihren Schlüsseltresor fest. Im folgenden Beispiel wird die Dienstprinzipal-ID über den vorherigen Befehl angegeben:

```azurecli
az keyvault set-policy --name $keyvault_name --spn $sp_id \
    --key-permissions wrapKey \
    --secret-permissions set
```

Erstellen Sie mit [az vm create](/cli/azure/vm#az_vm_create) eine VM, und fügen Sie einen 5-GB-Datenträger an die VM an. Nur bestimmte Marketplace-Images unterstützen die Datenträgerverschlüsselung. Im folgenden Beispiel wird unter Verwendung eines *CentOS 7.2n*-Images ein virtueller Computer mit dem Namen *myVM* erstellt:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image OpenLogic:CentOS:7.2n:7.2.20160629 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

Stellen Sie mithilfe des SSH-Netzwerkprotokolls eine Verbindung mit Ihrer VM her, und verwenden Sie dabei die in der Ausgabe des vorherigen Befehls enthaltene *publicIpAddress*. Erstellen Sie eine Partition und ein Dateisystem, und binden Sie dann den Datenträger ein. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit einer Linux-VM zum Einbinden des neuen Datenträgers](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Schließen Sie Ihre SSH-Sitzung.

Verschlüsseln Sie Ihre VM mit [az vm encryption enable](/cli/azure/vm/encryption#az_vm_encryption_enable). Das folgende Beispiel verwendet die Variablen *$sp_id* und *$sp_password* aus dem vorherigen Befehl `ad sp create-for-rbac`:

```azurecli
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Es dauert etwas, bis der Vorgang zum Verschlüsseln des Datenträgers abgeschlossen ist. Überwachen Sie den Status des Vorgangs mit [az vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show):

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Der Status wird als **EncryptionInProgress** angezeigt. Warten Sie, bis der Status für den Betriebssystemdatenträger als **VMRestartPending** angezeigt wird, und starten Sie Ihre VM mit [az vm restart](/cli/azure/vm#az_vm_restart) neu:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

Der Vorgang zur Datenträgerverschlüsselung wird während des Starts abgeschlossen, warten Sie deshalb einige Minuten, bevor Sie den Status der Verschlüsselung mit [az vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show) erneut überprüfen:

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Der Status sollte jetzt sowohl für den Betriebssystemdatenträger als auch für die Datenträger als **Encrypted** angezeigt werden.


## <a name="overview-of-disk-encryption"></a>Übersicht über die Datenträgerverschlüsselung
Virtuelle Datenträger auf virtuellen Linux-Computern werden im Ruhezustand mithilfe von [dm-crypt](https://wikipedia.org/wiki/Dm-crypt) verschlüsselt. Für die Verschlüsselung virtueller Datenträger in Azure fallen keine Gebühren an. Kryptografische Schlüssel werden in Azure Key Vault mit Softwareschutz gespeichert. Alternativ können Sie Schlüssel aber auch in Hardwaresicherheitsmodulen (HSMs) mit FIPS 140-2 Level 2-Zertifizierung importieren oder generieren. Diese kryptografischen Schlüssel werden allein von Ihnen kontrolliert, und Sie können deren Verwendung überwachen. Die kryptografischen Schlüssel dienen zum Verschlüsseln und Entschlüsseln virtueller Datenträger, die an Ihren virtuellen Computer angefügt sind. Über einen Azure Active Directory-Dienstprinzipal werden die kryptografischen Schlüssel beim Ein- und Ausschalten virtueller Computer sicher ausgegeben.

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

* Folgende Linux-Server-SKUs: Ubuntu, CentOS, SUSE und SUSE Linux Enterprise Server (SLES) sowie Red Hat Enterprise Linux
* Alle Ressourcen (wie Key Vault, Speicherkonto und virtueller Computer) müssen sich in der gleichen Azure-Region und im gleichen Abonnement befinden.
* Virtuelle Computer der standardmäßigen Serien A, D, DS, G, GS usw.
* Bei Aktualisierung der kryptografischen Schlüssel auf einem bereits verschlüsselten virtuellen Linux-Computer

Die Datenträgerverschlüsselung wird derzeit in folgenden Szenarien nicht unterstützt:

* Bei virtuellen Computern des Basic-Tarifs
* Bei virtuellen Computern, die mit dem klassischen Bereitstellungsmodell erstellt wurden
* Bei Deaktivierung der Betriebssystem-Datenträgerverschlüsselung auf virtuellen Linux-Computern
* Bei Verwendung von benutzerdefinierten Linux-Images

Weitere Informationen zu unterstützten Szenarien und Einschränkungen finden Sie unter [Azure Disk Encryption für virtuelle IaaS-Computer](../../security/azure-security-disk-encryption.md).


## <a name="create-azure-key-vault-and-keys"></a>Erstellen der Azure Key Vault-Instanz und der Schlüssel
Die neueste Version von [Azure CLI 2.0](/cli/azure/install-az-cli2) muss installiert sein, und Sie müssen mithilfe von [az login](/cli/azure/#az_login) bei einem Azure-Konto angemeldet sein. Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Beispielparameternamen sind u. a. *myResourceGroup*, *myKey* und *myVM*.

Erstellen Sie zum Speichern Ihrer kryptografischen Schlüssel zunächst eine Azure Key Vault-Instanz. In Azure Key Vault können Schlüssel, geheime Schlüssel und Kennwörter gespeichert werden, um eine sichere Implementierung in Anwendungen und Diensten zu ermöglichen. Bei der Verschlüsselung virtueller Datenträger dient Key Vault zum Speichern eines kryptografischen Schlüssels, der zum Verschlüsseln oder Entschlüsseln der virtuellen Datenträger verwendet wird.

Aktivieren Sie in Ihrem Azure-Abonnement mit [az provider register](/cli/azure/provider#az_provider_register) den Azure Key Vault-Anbieter, und erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort `eastus`:

```azurecli
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

Die Azure Key Vault-Instanz mit den kryptografischen Schlüsseln und die dazugehörigen Computeressourcen wie Speicher und der eigentliche virtuelle Computer müssen sich in der gleichen Region befinden. Erstellen Sie mit [az keyvault create](/cli/azure/keyvault#az_keyvault_create) einen Azure-Schlüsseltresor, und aktivieren Sie den Schlüsseltresor zur Verwendung der Datenträgerverschlüsselung. Geben Sie einen eindeutigen Key Vault-Namen für *keyvault_name* an, wie nachfolgend gezeigt:

```azurecli
keyvault_name=myuniquekeyvaultname
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Kryptografische Schlüssel können mit Softwareschutz oder mit HSM-Schutz (Hardwaresicherheitsmodul) gespeichert werden. Für die Verwendung eines HSMs wird eine Key Vault-Premiuminstanz benötigt. Die Erstellung einer Key Vault-Premiuminstanz ist im Gegensatz zur Verwendung einer Key Vault-Standardinstanz, bei der Schlüssel mit Softwareschutz gespeichert werden, mit zusätzlichen Kosten verbunden. Wenn Sie eine Key Vault-Premiuminstanz erstellen möchten, fügen Sie dem Befehl aus dem vorherigen Schritt `--sku Premium` hinzu. Im folgenden Beispiel werden softwaregeschützte Schlüssel verwendet, da Sie eine Key Vault-Standardinstanz erstellt haben.

Bei beiden Schutzmodellen muss der Azure-Plattform Zugriff gewährt werden, um beim Start des virtuellen Computers die kryptografischen Schlüssel anfordern und die virtuellen Datenträger entschlüsseln zu können. Erstellen Sie mithilfe von [az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create) einen kryptografischen Schlüssel in Ihrem Schlüsseltresor. Im folgenden Beispiel wird ein Schlüssel mit dem Namen *myKey* erstellt:

```azurecli
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```


## <a name="create-the-azure-active-directory-service-principal"></a>Erstellen des Azure Active Directory-Dienstprinzipals
Wenn virtuelle Datenträger verschlüsselt oder entschlüsselt werden, geben Sie ein Konto an, mit dem die Authentifizierung und der Austausch kryptografischer Schlüssel aus Key Vault abgewickelt werden. Dieses Konto, ein Azure Active Directory-Dienstprinzipal, ermöglicht es der Azure-Plattform, im Auftrag des virtuellen Computers die geeigneten kryptografischen Schlüssel anzufordern. In Ihrem Abonnement steht zwar eine Azure Active Directory-Standardinstanz zur Verfügung, viele Organisationen verwenden jedoch dedizierte Azure Active Directory-Verzeichnisse.

Erstellen Sie mit [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) einen Dienstprinzipal unter Verwendung von Azure Active Directory. Im folgenden Beispiel werden die Werte für die Dienstprinzipal-ID und das Kennwort zur späteren Verwendung in Befehlen gelesen:

```azurecli
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

Das Kennwort wird nur angezeigt, wenn Sie den Dienstprinzipal erstellen. Falls gewünscht, zeigen Sie das Kennwort an, und notieren Sie es (`echo $sp_password`). Sie können Ihre Dienstprinzipale mit [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) auflisten und mit [az ad sp show](/cli/azure/ad/sp#az_ad_sp_show) zusätzliche Informationen zu einem bestimmten Dienstprinzipal anzeigen.

Zum erfolgreichen Ver- und Entschlüsseln virtueller Datenträger müssen Berechtigungen für den in Key Vault gespeicherten kryptografischen Schlüssel festgelegt werden, damit der Azure Active Directory-Dienstprinzipal die Schlüssel lesen kann. Legen Sie mit [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) Berechtigungen für Ihren Schlüsseltresor fest. Im folgenden Beispiel wird die Dienstprinzipal-ID über den vorherigen Befehl angegeben:

```azurecli
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions wrapKey \
  --secret-permissions set
```


## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers
Erstellen Sie mit [az vm create](/cli/azure/vm#az_vm_create) eine VM für die Verschlüsselung, und fügen Sie einen 5-GB-Datenträger an die VM an. Nur bestimmte Marketplace-Images unterstützen die Datenträgerverschlüsselung. Im folgenden Beispiel wird unter Verwendung eines *CentOS 7.2n*-Images ein virtueller Computer mit dem Namen *myVM* erstellt:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image OpenLogic:CentOS:7.2n:7.2.20160629 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

Stellen Sie mithilfe des SSH-Netzwerkprotokolls eine Verbindung mit Ihrer VM her, und verwenden Sie dabei die in der Ausgabe des vorherigen Befehls enthaltene *publicIpAddress*. Erstellen Sie eine Partition und ein Dateisystem, und binden Sie dann den Datenträger ein. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit einer Linux-VM zum Einbinden des neuen Datenträgers](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Schließen Sie Ihre SSH-Sitzung.


## <a name="encrypt-virtual-machine"></a>Verschlüsseln eines virtuellen Computers
Um die virtuellen Datenträger zu verschlüsseln, müssen alle vorherigen Komponenten miteinander kombiniert werden:

1. Geben Sie den Azure Active Directory-Dienstprinzipal und das Kennwort an.
2. Geben Sie die Key Vault-Instanz zum Speichern der Metadaten Ihrer verschlüsselten Datenträger an.
3. Geben Sie die kryptografischen Schlüssel für die eigentliche Ver- und Entschlüsselung an.
4. Geben Sie an, ob Sie den Betriebssystem-Datenträger, die allgemeinen Datenträger oder alles verschlüsseln möchten.

Verschlüsseln Sie Ihre VM mit [az vm encryption enable](/cli/azure/vm/encryption#az_vm_encryption_enable). Das folgende Beispiel verwendet die Variablen *$sp_id* und *$sp_password* aus dem vorherigen Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac):

```azurecli
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Es dauert etwas, bis der Vorgang zum Verschlüsseln des Datenträgers abgeschlossen ist. Überwachen Sie den Status des Vorgangs mit [az vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show):

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Die Ausgabe ähnelt dem folgenden (abgeschnittenen) Beispiel:

```json
[
  "dataDisk": "EncryptionInProgress",
  "osDisk": "EncryptionInProgress",
]
```

Warten Sie, bis der Status für den Betriebssystemdatenträger als **VMRestartPending** angezeigt wird, und starten Sie Ihre VM mit [az vm restart](/cli/azure/vm#az_vm_restart) neu:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

Der Vorgang zur Datenträgerverschlüsselung wird während des Starts abgeschlossen, warten Sie deshalb einige Minuten, bevor Sie den Status der Verschlüsselung mit **az vm encryption show** erneut überprüfen:

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Der Status sollte jetzt sowohl für den Betriebssystemdatenträger als auch für die Datenträger als **Encrypted** angezeigt werden.


## <a name="add-additional-data-disks"></a>Hinzufügen zusätzlicher Datenträger
Nachdem Sie Ihre Datenträger verschlüsselt haben, können Sie dem virtuellen Computer später weitere virtuelle Datenträger hinzufügen und diese ebenfalls verschlüsseln. So können Sie Ihrem virtuellen Computer beispielsweise wie folgt einen zweiten virtuellen Datenträger hinzufügen:

```azurecli
az vm disk attach-new --resource-group myResourceGroup --vm-name myVM --size-in-gb 5
```

Führen Sie den Befehl zum Verschlüsseln die virtuellen Datenträger erneut wie folgt aus:

```azurecli
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```


## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Verwalten von Azure Key Vault (einschließlich Informationen zum Löschen kryptografischer Schlüssel und Vault-Instanzen) finden Sie unter [Verwalten von Schlüsseltresor mit CLI](../../key-vault/key-vault-manage-with-cli2.md).
* Weitere Informationen zur Datenträgerverschlüsselung (etwa zum Vorbereiten des Uploads eines verschlüsselten benutzerdefinierten virtuellen Computers in Azure) finden Sie unter [Azure-Datenträgerverschlüsselung für virtuelle Windows- und Linux-IaaS-Computer](../../security/azure-security-disk-encryption.md).
