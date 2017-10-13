---
title: "Erweitern eines Betriebssystemdatenträgers auf einem virtuellen Linux-Computer mit Azure CLI 1.0 | Microsoft Docs"
description: "Erfahren Sie, wie Sie den virtuellen Betriebssystemdatenträger auf einer Linux-VM unter Verwendung von Azure CLI 1.0 und des Resource Manager-Bereitstellungsmodells erweitern."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 0aedcd70b54c2ed47ec327ccf0529a48351353c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="expand-os-disk-on-a-linux-vm-using-the-azure-cli-with-the-azure-cli-10"></a>Erweitern des Betriebssystemdatenträgers auf einer Linux-VM mithilfe von Azure CLI 1.0
Die Standardgröße der virtuellen Festplatte für das Betriebssystem (operating system; OS) beträgt normalerweise 30 GB auf einem virtuellen Linux-Computer (VM) in Azure. Sie können [Datenträger hinzufügen](add-disk.md), um zusätzlichen Speicherplatz zur Verfügung zu stellen, aber möglicherweise möchten Sie auch den Betriebssystemdatenträger erweitern. In diesem Artikel wird beschrieben, wie der Betriebssystemdatenträger für einen virtuellen Linux-Computer mit nicht verwalteten Datenträgern mithilfe von Azure CLI 1.0 erweitert werden kann.

## <a name="cli-versions-to-complete-the-task"></a>CLI-Versionen zum Durchführen dieser Aufgabe
Führen Sie die Aufgabe mit einer der folgenden CLI-Versionen durch:

- [Azure-CLI 1.0](#prerequisites): Unsere CLI für das klassische Bereitstellungsmodell und das Resource Manager-Bereitstellungsmodell (in diesem Artikel)
- [Azure CLI 2.0:](expand-disks.md) Unsere CLI der nächsten Generation für das Resource Manager-Bereitstellungsmodell

## <a name="prerequisites"></a>Voraussetzungen
Installieren Sie die [neueste Azure CLI 1.0](../../cli-install-nodejs.md), und melden Sie sich mithilfe des Resource Manager-Modus wie folgt bei einem [Azure-Konto](https://azure.microsoft.com/pricing/free-trial/) an:

```azurecli
azure config mode arm
```

Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Beispiele für Parameternamen sind *myResourceGroup* und *myVM*.

## <a name="expand-os-disk"></a>Erweitern des Betriebssystemdatenträgers

1. Vorgänge auf virtuellen Festplatten können nicht durchgeführt werden, wenn die VM ausgeführt wird. Im folgenden Beispiel wird die VM *myVM* in der Ressourcengruppe *myResourceGroup* beendet und die Zuordnung dafür aufgehoben:

    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `azure vm stop` gibt die Computerressourcen nicht frei. Verwenden Sie `azure vm deallocate`, um Computerressourcen freizugeben. Die VM muss aufgehoben werden, um die virtuelle Festplatte zu erweitern.

2. Aktualisieren Sie mit dem Befehl `azure vm set` die Größe des nicht verwalteten Betriebssystemdatenträgers. Im folgenden Beispiel wird die VM *myVM* in der Ressourcengruppe *myResourceGroup* auf eine Größe von *50* GB aktualisiert:

    ```azurecli
    azure vm set \
        --resource-group myResourceGroup \
        --name myVM \
        --new-os-disk-size 50
    ```

3. Starten Sie Ihre VM wie folgt:

    ```azurecli
    azure vm start --resource-group myResourceGroup --name myVM
    ```

4. SSH mit Ihrer VM mit den entsprechenden Anmeldeinformationen. Verwenden Sie `df -h`, um zu überprüfen, ob die Größe des Betriebssystemdatenträgers geändert wurde. Die folgende Beispielausgabe zeigt, dass die primäre Partition (*/dev/sda1*) jetzt eine Größe von 50 GB hat:

    ```bash
    Filesystem      Size  Used Avail Use% Mounted on
    udev            1.7G     0  1.7G   0% /dev
    tmpfs           344M  5.0M  340M   2% /run
    /dev/sda1        49G  1.3G   48G   3% /
    ```

## <a name="next-steps"></a>Nächste Schritte
Wenn Sie zusätzlichen Speicher benötigen, [fügen Sie Datenträger zu einer Linux-VM hinzu](add-disk.md). Weitere Informationen zur Datenträgerverschlüsselung finden Sie unter [Encrypt disks on a Linux VM using the Azure CLI (Verschlüsseln von Datenträgern auf einer Linux-VM mithilfe der Azure-Befehlszeilenschnittstelle)](encrypt-disks.md).
