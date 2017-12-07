---
title: Bereitstellen von LAMP auf einem virtuellen Linux-Computer in Azure | Microsoft-Dokumentation
description: "Tutorial – Installieren des LAMP-Stacks auf einem virtuellen Linux-Computer in Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: danlep
ms.openlocfilehash: 8fcf411db844e227e0c4db0e690a1832f98b42f1
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/28/2017
---
# <a name="install-a-lamp-web-server-on-an-azure-vm"></a>Installieren eines LAMP-Webservers auf einem virtuellen Azure-Computer
In diesem Artikel werden Sie durch die Bereitstellung eines Apache-Webservers sowie von MySQL und PHP (LAMP-Stack) auf einem virtuellen Ubuntu-Computer in Azure geführt. Wenn Sie den NGINX-Webserver bevorzugen, finden Sie entsprechende Informationen im Tutorial zum [LEMP-Stack](tutorial-lemp-stack.md). Um den LAMP-Server in Aktion zu sehen, können Sie optional eine WordPress-Website installieren und konfigurieren. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Ubuntu-Computers („L“ im LAMP-Stack)
> * Öffnen von Port 80 für Webdatenverkehr
> * Installieren von Apache, MySQL und PHP
> * Überprüfen der Installation und Konfiguration
> * Installieren von WordPress auf dem LAMP-Server


Dieses Setup ist für schnelle Tests oder Proof of Concept gedacht. Weitere Informationen zum LAMP-Stack, einschließlich Empfehlungen für eine Produktionsumgebung, finden Sie in der [Ubuntu-Dokumentation](https://help.ubuntu.com/community/ApacheMySQLPHP).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial die Azure CLI-Version 2.0.4 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0]( /cli/azure/install-azure-cli) Informationen dazu. 

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Installieren von Apache, MySQL und PHP

Führen Sie den folgenden Befehl aus, um die Ubuntu-Paketquellen zu aktualisieren und Apache, MySQL und PHP zu installieren. Beachten Sie das Caretzeichen (^) am Ende des Befehls, das Teil des Paketnamens `lamp-server^` ist. 


```bash
sudo apt update && sudo apt install lamp-server^
```


Sie werden aufgefordert, die Pakete und andere Abhängigkeiten zu installieren. Legen Sie bei entsprechender Aufforderung ein Stammkennwort für MySQL fest, und drücken Sie dann die [Eingabetaste], um den Vorgang fortzusetzen. Befolgen Sie die restlichen Anweisungen. Dieser Prozess installiert die PHP-Erweiterungen, die mindestens für die Verwendung von PHP mit MySQL erforderlich sind. 

![Seite für das MySQL-Stammkennwort][1]

## <a name="verify-installation-and-configuration"></a>Überprüfen der Installation und Konfiguration


### <a name="apache"></a>Apache

Überprüfen Sie die Version von Apache mit dem folgenden Befehl:
```bash
apache2 -v
```

Nachdem Apache installiert und Port 80 für den virtuellen Computer geöffnet wurde, ist der Zugriff auf den Webserver über das Internet möglich. Öffnen Sie zum Anzeigen der Apache2 Ubuntu-Standardseite einen Webbrowser, und geben Sie die öffentliche IP-Adresse des virtuellen Computers ein. Verwenden Sie die öffentliche IP-Adresse, die Sie zum Herstellen einer SSH-Verbindung mit dem virtuellen Computer verwendet haben:

![Apache-Standardseite][3]


### <a name="mysql"></a>MySQL

Überprüfen Sie die Version von MySQL mit dem folgenden Befehl (beachten Sie die Großschreibung des `V`-Parameters):

```bash
mysql -V
```

Führen Sie zum Sichern der Installation von MySQL das Skript `mysql_secure_installation` aus. Falls Sie nur einen temporären Server einrichten, können Sie diesen Schritt überspringen.

```bash
mysql_secure_installation
```

Geben Sie ein Stammkennwort für MySQL ein, und konfigurieren Sie die Sicherheitseinstellungen für Ihre Umgebung.

Wenn Sie MySQL-Features (MySQL-Datenbank erstellen, Benutzer hinzufügen oder Konfigurationseinstellungen ändern) ausprobieren möchten, melden Sie sich bei MySQL an. Dieser Schritt ist für den Abschluss des Tutorials nicht erforderlich.

```bash
mysql -u root -p
```

Beenden Sie anschließend die MySQL-Eingabeaufforderung durch Eingabe von `\q`.

### <a name="php"></a>PHP

Überprüfen Sie die Version von PHP mit dem folgenden Befehl:

```bash
php -v
```

Wenn Sie weitere Tests durchführen möchten, erstellen Sie schnell eine PHP-Infoseite zum Anzeigen in einem Browser. Die PHP-Infoseite wird mit dem folgenden Befehl erstellt:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Nun können Sie die erstellte PHP-Infoseite überprüfen. Öffnen Sie einen Browser, und wechseln Sie zu `http://yourPublicIPAddress/info.php`. Ersetzen Sie die öffentliche IP-Adresse Ihres virtuellen Computers. Die Seite sollte ähnlich wie diese Abbildung aussehen.

![PHP-Infoseite][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen LAMP-Server in Azure bereitgestellt. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Erstellen eines virtuellen Ubuntu-Computers
> * Öffnen von Port 80 für Webdatenverkehr
> * Installieren von Apache, MySQL und PHP
> * Überprüfen der Installation und Konfiguration
> * Installieren von WordPress auf dem LAMP-Server

Im nächsten Tutorial erfahren Sie, wie Sie Webserver mit SSL-Zertifikaten sichern.

> [!div class="nextstepaction"]
> [Sichern von Webservern mit SSL](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lamp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png