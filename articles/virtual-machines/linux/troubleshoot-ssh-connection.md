---
title: Behandeln von Problemen bei der SSH-Verbindung mit einem virtuellen Azure-Computer | Microsoft Docs
description: Erfahren Sie, wie Sie SSH-Fehler wie etwa fehlgeschlagene oder abgelehnte SSH-Verbindungen auf virtuellen Azure-Computern unter Linux beheben.
keywords: SSH-Verbindung abgelehnt, SSH-Fehler, Azure SSH, SSH-Verbindungsfehler
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: b7fe6dadb444ebbe6af6239562f507e451f9f605
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2018
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Behandeln von Problemen, Fehlern oder Ablehnungen im Zusammenhang mit der SSH-Verbindung mit einem virtuellen Azure Linux-Computer
Es gibt verschiedene Gründe dafür, dass SSH-Fehler (Secure Shell) oder SSH-Verbindungsfehler auftreten oder dass die SSH-Verbindung abgelehnt wird, wenn Sie versuchen, eine Verbindung mit einem virtuellen Azure-Computer unter Linux herzustellen. Dieser Artikel hilft Ihnen, diese Probleme zu ermitteln und zu beheben. Sie können das Azure-Portal, die Azure-Befehlszeilenschnittstelle oder die VM-Zugriffserweiterung für Linux verwenden, um Verbindungsproblemen zu ermitteln und zu beheben.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Azure-Experten im [MSDN Azure-Forum oder im Stack Overflow-Forum](http://azure.microsoft.com/support/forums/)Fragen stellen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](http://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten**aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](http://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Schritte zur schnellen Problembehandlung
Versuchen Sie nach jedem Problembehandlungsschritt, die Verbindung mit dem virtuellen Computer erneut herzustellen.

1. Zurücksetzen der SSH-Konfiguration
2. Setzen Sie die Anmeldeinformationen für den Benutzer zurück.
3. Überprüfen Sie, ob die Regeln der [Netzwerksicherheitsgruppe](../../virtual-network/virtual-networks-nsg.md) SSH-Datenverkehr zulassen.
   * Stellen Sie sicher, dass eine Netzwerksicherheitsgruppen-Regel vorhanden ist, die SSH-Datenverkehr zulässt (standardmäßig über TCP-Port 22).
   * Sie können die Portweiterleitung/-zuordnung nicht ohne Azure-Lastenausgleich verwenden.
4. Überprüfen Sie die [Ressourcenintegrität des virtuellen Computers](../../resource-health/resource-health-overview.md). 
   * Stellen Sie sicher, dass der virtuelle Computer als fehlerfrei gemeldet wird.
   * Wenn Sie die Startdiagnose aktiviert haben, stellen Sie sicher, dass der virtuelle Computer in den Protokollen keine Startfehler meldet.
5. Starten Sie den virtuellen Computer neu.
6. Stellen Sie den virtuellen Computer erneut bereit.

Lesen Sie weiter, falls Sie ausführlichere Schritte und Erläuterungen zur Problembehandlung benötigen.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Verfügbare Methoden zum Beheben von SSH-Verbindungsproblemen
Sie können die Anmeldeinformationen oder die SSH-Konfiguration mithilfe einer der folgenden Methoden zurücksetzen:

* [Azure-Portal](#use-the-azure-portal): Ermöglicht das schnelle Zurücksetzen der SSH-Konfiguration oder des SSH-Schlüssels, falls die Azure-Tools nicht installiert sind.
* [Azure CLI 2.0:](#use-the-azure-cli-20) Wenn Sie bereits eine Befehlszeile geöffnet haben, können Sie die SSH-Konfiguration oder Anmeldeinformationen schnell zurücksetzen. Sie können auch [Azure CLI 1.0](#use-the-azure-cli-10) verwenden.
* [VMAccessForLinux-Erweiterung in Azure](#use-the-vmaccess-extension): Erstellen und verwenden Sie JSON-Definitionsdateien wieder, um die SSH-Konfiguration oder Benutzeranmeldeinformationen zurückzusetzen.

Versuchen Sie nach jedem Problembehandlungsschritt, die Verbindung mit dem virtuellen Computer erneut herzustellen. Sollte sich immer noch keine Verbindung herstellen lassen, versuchen Sie es mit dem nächsten Schritt.

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals
Das Azure-Portal bietet eine schnelle Möglichkeit, die SSH-Konfiguration oder Benutzeranmeldeinformationen zurücksetzen, ohne dafür Tools auf dem lokalen Computer installieren zu müssen.

Wählen Sie im Azure-Portal Ihren virtuellen Computer aus. Scrollen Sie nach unten zum Abschnitt **Support + Problembehandlung**, und wählen Sie **Kennwort zurücksetzen**, wie im folgenden Beispiel gezeigt:

![Zurücksetzen der SSH-Konfiguration oder Anmeldeinformationen im Azure-Portal](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Zurücksetzen der SSH-Konfiguration
Wählen Sie zuerst die Option `Reset configuration only` aus dem Dropdownmenü **Modus**, wie im vorigen Screenshot gezeigt, und klicken Sie dann auf die Schaltfläche **Zurücksetzen**. Nachdem die Aktion abgeschlossen ist, versuchen Sie erneut, auf Ihren virtuellen Computer zuzugreifen.

### <a name="reset-ssh-credentials-for-a-user"></a>Zurücksetzen von SSH-Anmeldeinformationen für einen Benutzer
Um die Anmeldeinformationen eines vorhandenen Benutzers zurückzusetzen, wählen Sie entweder `Reset SSH public key` oder `Reset password` aus dem Dropdownmenü **Modus**, wie im vorigen Screenshot gezeigt. Geben Sie den Benutzernamen sowie einen SSH-Schlüssel oder ein neues Kennwort an, und klicken Sie auf die Schaltfläche **Zurücksetzen**.

Über dieses Menü können Sie auch einen Benutzer mit sudo-Berechtigungen auf dem virtuellen Computer erstellen. Geben Sie einen neuen Benutzernamen sowie ein Kennwort oder einen SSH-Schlüssel ein, und klicken Sie dann auf die Schaltfläche **Zurücksetzen**.

## <a name="use-the-azure-cli-20"></a>Verwenden von Azure CLI 2.0
Wenn nicht bereits geschehen, installieren Sie die neueste Version von [Azure CLI 2.0](/cli/azure/install-az-cli2), und melden Sie sich mit [az login](/cli/azure/#login) bei einem Azure-Konto an.

Wenn Sie ein benutzerdefiniertes Linux-Datenträgerimage erstellt und hochgeladen haben, stellen Sie sicher, dass [Microsoft Azure Linux Agent](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Version 2.0.5 oder höher installiert ist. Bei virtuellen Computern, die über Images aus dem Katalog erstellt wurde, ist diese Zugriffserweiterung bereits installiert und konfiguriert.

### <a name="reset-ssh-configuration"></a>Zurücksetzen der SSH-Konfiguration
Sie können zunächst versuchen, die SSH-Konfiguration auf die Standardwerte zurückzusetzen und den SSH-Server auf der VM neu zu starten. Beachten Sie, dass hierdurch nicht der Name, das Kennwort oder die SSH-Schlüssel des Benutzerkontos geändert wird bzw. werden.
Im folgenden Beispiel wird mithilfe von [az vm user reset-ssh](/cli/azure/vm/user#reset-ssh) die SSH-Konfiguration auf der VM mit dem Namen `myVM` in `myResourceGroup` zurückgesetzt. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Zurücksetzen von SSH-Anmeldeinformationen für einen Benutzer
Im folgenden Beispiel werden mithilfe von [az vm user update](/cli/azure/vm/user#update) die Anmeldeinformationen für `myUsername` auf den in `myPassword` angegebenen Wert zurückgesetzt, der auf der VM `myVM` in `myResourceGroup` angegeben ist. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Wenn Sie die SSH-Schlüsselauthentifizierung verwenden, können Sie den SSH-Schlüssel für einen bestimmten Benutzer zurücksetzen. Das folgende Beispiel aktualisiert mithilfe von **az vm access set-linux-user** auf dem virtuellen Computer `myVM` in `myResourceGroup` den in `~/.ssh/id_rsa.pub` gespeicherten SSH-Schlüssel für den Benutzer `myUsername`. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Verwenden der Erweiterung VMAccess
Die VM-Zugriffserweiterung für Linux liest eine JSON-Datei, die die auszuführenden Aktionen definiert. Zu diesen Aktionen gehört das Zurücksetzen von SSHD, das Zurücksetzen eines SSH-Schlüssels und das Hinzufügen eines Benutzers. Sie verwenden weiterhin die Azure-Befehlszeilenschnittstelle, um die VM-Zugriffserweiterung aufzurufen, aber Sie können die JSON-Dateien bei Bedarf VM-übergreifend wiederverwenden. Auf diese Weise können Sie ein Repository mit JSON-Dateien erstellen, die für verschiedene Szenarien aufgerufen werden können.

### <a name="reset-sshd"></a>Zurücksetzen von SSHD
Erstellen Sie eine Datei namens `settings.json` mit folgendem Inhalt:

```json
{  
    "reset_ssh":"True"
}
```

Rufen Sie dann über die Azure-Befehlszeilenschnittstelle die `VMAccessForLinux`-Erweiterung auf, und geben Sie die entsprechende JSON-Datei an, um Ihre SSHD-Verbindung zurückzusetzen. Im folgenden Beispiel wird mithilfe von [az vm extension set](/cli/azure/vm/extension#set) die SSHD auf der VM mit dem Namen `myVM` in `myResourceGroup` zurückgesetzt. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Zurücksetzen von SSH-Anmeldeinformationen für einen Benutzer
Wenn SSHD ordnungsgemäß funktioniert, können Sie die Anmeldeinformationen für einen bestimmten Benutzer zurücksetzen. Um das Kennwort für einen Benutzer zurückzusetzen, erstellen Sie eine Datei namens `settings.json`. Das folgende Beispiel setzt die Anmeldeinformationen für `myUsername` auf den in `myPassword` angegebenen Wert zurück. Geben Sie folgende Zeilen in Ihre `settings.json`-Datei ein, und verwenden Sie dabei Ihre eigenen Werte:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Um den SSH-Schlüssel für einen Benutzer zurückzusetzen, erstellen Sie zuerst eine Datei namens `settings.json`. Das folgende Beispiel setzt auf dem virtuellen Computer `myVM` in `myResourceGroup` die Anmeldeinformationen für den Benutzer `myUsername` auf den in `myPassword` angegebenen Wert zurück. Geben Sie folgende Zeilen in Ihre `settings.json`-Datei ein, und verwenden Sie dabei Ihre eigenen Werte:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Nachdem Sie die JSON-Datei erstellt haben, verwenden Sie die Azure-Befehlszeilenschnittstelle, um die `VMAccessForLinux`-Erweiterung aufzurufen, mit der Sie unter Angabe der JSON-Datei die SSH-Benutzeranmeldeinformationen zurücksetzen können. Das folgende Beispiel setzt Anmeldeinformationen auf dem virtuellen Computer mit dem Namen `myVM` in `myResourceGroup` zurück. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-cli-10"></a>Verwenden der Azure-Befehlszeilenschnittstelle 1.0
Falls noch nicht geschehen, [installieren Sie die Azure-Befehlszeilenschnittstelle 1.0, und stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her](../../cli-install-nodejs.md). Vergewissern Sie sich wie folgt, dass Sie den Resource Manager-Modus verwenden:

```azurecli
azure config mode arm
```

Wenn Sie ein benutzerdefiniertes Linux-Datenträgerimage erstellt und hochgeladen haben, stellen Sie sicher, dass [Microsoft Azure Linux Agent](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Version 2.0.5 oder höher installiert ist. Bei virtuellen Computern, die über Images aus dem Katalog erstellt wurde, ist diese Zugriffserweiterung bereits installiert und konfiguriert.

### <a name="reset-ssh-configuration"></a>Zurücksetzen der SSH-Konfiguration
Die SSHD-Konfiguration selbst kann fehlerhaft sein, oder beim Dienst ist ein Fehler aufgetreten. Sie können SSHD zurücksetzen, um sicherzustellen, dass die SSH-Konfiguration gültig ist. Das Zurücksetzen von SSHD sollte der erste Schritt bei der Problembehandlung sein.

Das folgende Beispiel setzt SSHD auf einem virtuellen Computer mit dem Namen `myVM` in der Ressourcengruppe `myResourceGroup` zurück. Verwenden Sie folgendermaßen Ihre eigenen Namen für den virtuellen Computer und die Ressourcengruppe:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Zurücksetzen von SSH-Anmeldeinformationen für einen Benutzer
Wenn SSHD ordnungsgemäß funktioniert, können Sie das Kennwort für einen bestimmten Benutzer zurücksetzen. Das folgende Beispiel setzt auf dem virtuellen Computer `myVM` in `myResourceGroup` die Anmeldeinformationen für den Benutzer `myUsername` auf den in `myPassword` angegebenen Wert zurück. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Wenn Sie die SSH-Schlüsselauthentifizierung verwenden, können Sie den SSH-Schlüssel für einen bestimmten Benutzer zurücksetzen. Das folgende Beispiel aktualisiert den in `~/.ssh/id_rsa.pub` gespeicherten SSH-Schlüssel für den Benutzer namens `myUsername` auf dem virtuellen Computer `myVM` in `myResourceGroup`. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```


## <a name="restart-a-vm"></a>Neustarten eines virtuellen Computers
Wenn Sie die SSH-Konfiguration und Benutzeranmeldeinformationen zurückgesetzt haben oder dabei ein Fehler aufgetreten ist, können Sie den virtuellen Computer neu starten, um zugrunde liegende Computeprobleme zu beheben.

### <a name="azure-portal"></a>Azure-Portal
Um einen virtuellen Computer über das Azure-Portal neu zu starten, wählen Sie den Computer aus, und klicken Sie auf die Schaltfläche **Neu starten**, wie im folgenden Beispiel gezeigt:

![Neustarten eines virtuellen Computers im Azure-Portal](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli-10"></a>Azure-Befehlszeilenschnittstelle 1.0
Das folgende Beispiel startet den virtuellen Computer mit dem Namen `myVM` in der Ressourcengruppe `myResourceGroup` neu. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Im folgenden Beispiel wird mit [az vm restart](/cli/azure/vm#restart) der virtuelle Computer `myVM` in der Ressourcengruppe `myResourceGroup` neu gestartet. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>Erneutes Bereitstellen eines virtuellen Computers
Sie können einen virtuellen Computer in Azure auf einem anderen Knoten erneut bereitstellen und dadurch möglicherweise zugrunde liegende Netzwerkprobleme beheben. Informationen zum erneuten Bereitstellen eines virtuellen Computers finden Sie unter [Einen virtuellen Computer in einem neuen Azure-Knoten erneut bereitstellen](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Nach Beendigung dieses Vorgangs gehen kurzlebige Datenträgerdaten verloren, und dynamische IP-Adressen, die dem virtuellen Computer zugeordnet sind, werden aktualisiert.
> 
> 

### <a name="azure-portal"></a>Azure-Portal
Um einen virtuellen Computer über das Azure-Portal erneut bereitzustellen, wählen Sie den Computer aus, und scrollen Sie nach unten zum Abschnitt **Support + Problembehandlung**. Klicken Sie auf die Schaltfläche **Erneut bereitstellen**, die im folgenden Beispiel gezeigt:

![Erneutes Bereitstellen eines virtuellen Computers im Azure-Portal](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli-10"></a>Azure-Befehlszeilenschnittstelle 1.0
Das folgende Beispiel stellt den virtuellen Computer mit dem Namen `myVM` in der Ressourcengruppe `myResourceGroup` erneut bereit. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Im folgenden Beispiel wird mit [az vm redeploy](/cli/azure/vm#redeploy) die Bereitstellung des virtuellen Computers `myVM` in der Ressourcengruppe `myResourceGroup` erneuert. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Mit dem klassischen Bereitstellungsmodell erstellte virtuelle Computer
Führen Sie die folgenden Schritte aus, um die häufigsten SSH-Verbindungsfehler für virtuelle Computer zu beheben, die mit dem klassischen Bereitstellungsmodell erstellt wurden. Versuchen Sie nach jedem Schritt, die Verbindung mit dem virtuellen Computer erneut herzustellen.

* Setzen Sie den Remotezugriff über das [Azure-Portal](https://portal.azure.com)zurück. Wählen Sie im Azure-Portal Ihren virtuellen Computer aus, und klicken Sie auf die Schaltfläche **Remote zurücksetzen**.
* Starten Sie den virtuellen Computer neu. Wählen Sie im [Azure-Portal](https://portal.azure.com) Ihren virtuellen Computer aus, und klicken Sie auf die Schaltfläche **Neu starten**.
    
* Stellen Sie den virtuellen Computer auf einem neuen Azure-Knoten erneut bereit. Informationen zum erneuten Bereitstellen eines virtuellen Computers finden Sie unter [Einen virtuellen Computer in einem neuen Azure-Knoten erneut bereitstellen](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
    Nach Beendigung dieses Vorgangs gehen kurzlebige Datenträgerdaten verloren, und dynamische IP-Adressen, die dem virtuellen Computer zugeordnet sind, werden aktualisiert.
* Führen Sie die unter [Zurücksetzen des Kennworts oder des SSH-Schlüssels für Linux-basierte virtuelle Computer](classic/reset-access-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) beschriebenen Anweisungen aus, um Folgendes zu erreichen:
  
  * Zurücksetzen des Kennworts oder des SSH-Schlüssels
  * Erstellen eines *sudo*-Benutzerkontos
  * Zurücksetzen der SSH-Konfiguration
* Überprüfen Sie die Ressourcenintegrität des virtuellen Computers auf etwaige Plattformprobleme.<br>
     Wählen Sie Ihre VM aus, und scrollen Sie nach unten zu **Einstellungen** > **Integrität überprüfen**.

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* Wenn Sie nach Ausführung der Schritte immer noch nicht keine SSH-Verbindung mit Ihrem virtuellen Computer herstellen können, finden Sie unter [Ausführliche Schritte zur Problembehandlung bei SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) weitere Hinweise, damit Sie das Problem lösen können.
* Weitere Informationen zur Problembehandlung beim Anwendungszugriff finden Sie unter [Problembehandlung beim Zugriff auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Weitere Informationen zur Problembehandlung bei virtuellen Computern, die mit dem klassischen Bereitstellungsmodell erstellt wurden, finden Sie unter [Zurücksetzen eines Kennworts oder eines SSH-Schlüssels für Linux-basierte virtuelle Computer](classic/reset-access-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

