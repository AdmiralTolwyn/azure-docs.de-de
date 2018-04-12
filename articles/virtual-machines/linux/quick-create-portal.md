---
title: Azure-Schnellstart – Erstellen einer VM mit dem Portal | Microsoft-Dokumentation
description: Azure-Schnellstart – Erstellen einer VM mit dem Portal
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/11/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6585f28e2b70aee6efbfa99bf2ec4320d6d15382
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Erstellen einer Linux-VM mit dem Azure-Portal

Virtuelle Azure-Computer können über das Azure-Portal erstellt werden. Diese Methode bietet eine browserbasierte Benutzeroberfläche zum Erstellen und Konfigurieren von virtuellen Computern und alle zugehörigen Ressourcen. In diesem Schnellstart werden die Erstellung eines virtuellen Computers und die Installation eines Webservers auf dem virtuellen Computer Schritt für Schritt beschrieben.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="create-ssh-key-pair"></a>Erstellen eines SSH-Schlüsselpaars

Für diesen Schnellstart benötigen Sie ein SSH-Schlüsselpaar. Sie können diesen Schritt überspringen, wenn Sie bereits über ein vorhandenes SSH-Schlüsselpaar verfügen.

Führen Sie diesen Befehl über eine Bash-Shell aus, und befolgen Sie die Anweisungen auf dem Bildschirm. Die Ausgabe des Befehls enthält den Dateinamen der Datei mit dem öffentlichen Schlüssel. Kopieren Sie den Inhalt der Datei mit dem öffentlichen Schlüssel (`cat ~/.ssh/id_rsa.pub`) in die Zwischenablage. Achten Sie bei Verwendung des Windows-Subsystems für Linux darauf, dass Sie keine Zeilenumbruchzeichen aus der Ausgabe kopieren. Notieren Sie sich den Namen der Datei mit dem privaten Schlüssel für später.

```bash
ssh-keygen -t rsa -b 2048
```

Ausführlichere Informationen zu diesem Vorgang finden Sie [hier](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="log-in-to-azure"></a>Anmelden an Azure 

Melden Sie sich unter http://portal.azure.com beim Azure-Portal an.

## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Klicken Sie im Azure-Portal links oben auf **Ressource erstellen**.

2. Wählen Sie **Compute** und dann **Ubuntu Server 16.04 LTS**. 

3. Geben Sie die Informationen zum virtuellen Computer ein. Wählen Sie unter **Authentifizierungstyp** die Option **Öffentlicher SSH-Schlüssel**. Achten Sie beim Einfügen Ihres öffentlichen SSH-Schlüssels darauf, alle voran- bzw. nachgestellten Leerzeichen zu entfernen. Klicken Sie zum Abschluss auf **OK**.

    ![Eingeben grundlegender Informationen zu Ihrem virtuellen Computer im Portalblatt](./media/quick-create-portal/create-vm-portal-basic-blade.png)

4. Wählen Sie eine Größe für den virtuellen Computer. Wählen Sie die Option **Alle anzeigen**, oder ändern Sie den Filter **Supported disk type** (Unterstützter Datenträgertyp), um weitere Größen anzuzeigen. 

    ![Screenshot: VM-Größen](./media/quick-create-portal/create-linux-vm-portal-sizes.png)  

5. Übernehmen Sie unter **Einstellungen** die Standardwerte, und klicken Sie auf **OK**.

6. Klicken Sie auf der Zusammenfassungsseite auf **OK**, um die Bereitstellung des virtuellen Computers zu starten.

7. Die VM wird im Dashboard des Azure-Portals angeheftet. Nach Abschluss der Bereitstellung wird automatisch die VM-Zusammenfassung geöffnet.


## <a name="connect-to-virtual-machine"></a>Herstellen der Verbindung mit dem virtuellen Computer

Stellen Sie eine SSH-Verbindung mit dem virtuellen Computer her.

1. Klicken Sie in den Eigenschaften des virtuellen Computers auf die Schaltfläche **Verbinden**. Die Verbindungsschaltfläche zeigt eine SSH-Verbindungszeichenfolge an, mit der Sie eine Verbindung mit dem virtuellen Computer herstellen können.

    ![Portal 9](./media/quick-create-portal/portal-quick-start-9.png) 

2. Führen Sie den folgenden Befehl aus, um eine SSH-Sitzung zu erstellen. Ersetzen Sie die Verbindungszeichenfolge mit derjenigen, die Sie aus dem Azure-Portal kopiert haben.

```bash 
ssh azureuser@40.112.21.50
```

## <a name="install-nginx"></a>Installieren von NGINX

Verwenden Sie das folgende Bash-Skript, um Paketquellen zu aktualisieren und das neueste NGINX-Paket zu installieren. 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Beenden Sie die SSH-Sitzung nach Abschluss des Vorgangs, und wechseln Sie zurück zu den VM-Eigenschaften im Azure-Portal.


## <a name="open-port-80-for-web-traffic"></a>Öffnen von Port 80 für Webdatenverkehr 

Mit einer Netzwerksicherheitsgruppe (NSG) wird eingehender und ausgehender Datenverkehr geschützt. Wenn im Azure-Portal eine VM erstellt wird, wird für SSH-Verbindungen an Port 22 eine Regel für eingehenden Datenverkehr erstellt. Da diese VM einen Webserver hostet, muss für Port 80 eine NSG-Regel erstellt werden.

1. Klicken Sie auf dem virtuellen Computer auf den Namen der **Ressourcengruppe**.
2. Wählen Sie die **Netzwerksicherheitsgruppe** aus. Die NSG können Sie anhand der Spalte **Typ** identifizieren. 
3. Klicken Sie im Menü auf der linken Seite unter „Einstellungen“ auf **Eingangssicherheitsregeln**.
4. Klicken Sie auf **Hinzufügen**.
5. Geben Sie unter **Name** den Text **http** ein. Vergewissern Sie sich, dass **Quellportbereich** auf `*`, **Zielportbereich** auf *80* und **Aktion** auf  *Zulassen* festgelegt ist. 
6. Klicken Sie auf **OK**.


## <a name="view-the-nginx-welcome-page"></a>Anzeigen der NGINX-Willkommensseite

Nachdem NGINX installiert und Port 80 für Ihre VM geöffnet wurde, ist der Zugriff auf den Webserver aus dem Internet möglich. Öffnen Sie einen Webbrowser, und geben Sie die öffentliche IP-Adresse der VM ein. Sie finden die öffentliche IP-Adresse im Azure-Portal in den Eigenschaften des virtuellen Computers.

![NGINX-Standardwebsite](./media/quick-create-cli/nginx.png) 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcengruppe, VM und alle zugehörigen Ressourcen nicht mehr benötigt werden, löschen Sie sie. Wählen Sie hierzu die Ressourcengruppe für den virtuellen Computer aus, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie einen einfachen virtuellen Computer und eine Netzwerksicherheitsgruppen-Regel bereitgestellt sowie einen Webserver installiert. Fahren Sie mit dem Tutorial für virtuelle Linux-Computer fort, um weitere Informationen zu virtuellen Azure-Computern zu erhalten.

> [!div class="nextstepaction"]
> [Tutorials für virtuelle Azure Linux-Computer](./tutorial-manage-vm.md)
