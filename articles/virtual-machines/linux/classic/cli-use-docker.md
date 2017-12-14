---
title: "Verwenden der Docker-VM-Erweiterung für Linux auf Azure"
description: "Beschreibt die Docker- und Azure Virtual Machines-Erweiterungen und zeigt die programmgesteuerte Erstellung von virtuellen Computern auf Azure, die von Docker gehostet werden, über die Befehlszeile mithilfe der Azure-CLI."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: eaff75e3-d929-4931-a4a1-8c377a8e7302
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/29/2016
ms.author: rasquill
ms.openlocfilehash: b276911ecbbf161cb6068c1af7a035850035b98d
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2017
---
# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>Verwenden der Docker-VM-Erweiterung aus der Azure-Befehlszeilenschnittstelle (Azure-CLI)
> [!IMPORTANT] 
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager- und klassische Bereitstellung](../../../resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells. Informationen zum Verwenden der benutzerdefinierten Docker-VM-Erweiterung im Resource Manager-Modell finden Sie [hier](../dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Dieses Thema beschreibt das Erstellen eines virtuellen Computers mit der Docker-VM-Erweiterung im ASM-Modus (Azure Service Management) in der Azure-Befehlszeilenschnittstelle auf einer beliebigen Plattform. [Docker](https://www.docker.com/) ist einer der beliebtesten Virtualisierungsansätze, der für das Isolieren von Daten und Computing auf gemeinsamen Ressourcen [Linux-Container](http://en.wikipedia.org/wiki/LXC) statt virtueller Computer verwendet. Verwenden Sie die Docker-VM-Erweiterung und den [Azure Linux Agent](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), um einen virtuellen Docker-Computer zu erstellen, der eine beliebige Anzahl von Containern für Ihre Anwendungen in Azure hostet. Eine allgemeine Diskussion über die Container und ihre Vorteile finden Sie unter [Docker High Level Whiteboard](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard)(Whiteboard auf hoher Ebene zu Docker) (in englischer Sprache).

## <a name="how-to-use-the-docker-vm-extension-with-azure"></a>Verwenden der Docker-VM-Erweiterung mit Azure
Um die Docker-VM-Erweiterung mit Azure verwenden zu können, müssen Sie eine höhere Version der [Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-sdk-tools-xplat) (Azure-CLI) als 0.8.6 installieren (bei der Erstellung dieses Dokuments ist die aktuelle Version 0.10.0). Sie können die Azure-CLI auf Mac, Linux und Windows installieren.

Die Verwendung von Docker auf Azure ist einfach:

* Installieren Sie die Azure-CLI und ihre Abhängigkeiten auf dem Computer, von dem aus Sie Azure steuern möchten (unter Windows ist dies eine Linux-Distribution, die als virtueller Computer ausgeführt wird).
* Verwenden Sie die Docker-Befehle der Azure-CLI, um einen Docker-VM-Host in Azure zu erstellen.
* Verwenden Sie die lokalen Docker-Befehle, um die Docker-Container im virtuellen Docker-Computer in Azure zu verwalten.

### <a name="install-the-azure-command-line-interface-azure-cli"></a>Installieren der Azure-Befehlszeilenschnittstelle (Azure-CLI)
Informationen zum Installieren und Konfigurieren der Azure-CLI finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](../../../cli-install-nodejs.md). Um die Installation zu bestätigen, geben Sie an der Eingabeaufforderung `azure` ein. Nach wenigen Sekunden sollten die ASCII-Zeichnungen der Azure-CLI mit den verfügbaren grundlegenden Befehlen angezeigt werden. Wenn die Installation erfolgreich war, sollten Sie bei Eingabe von `azure help vm` unter den aufgelisteten Befehlen „docker“ finden.

> [!NOTE]
> Docker enthält Tools für Windows, die als [Docker Machine](https://docs.docker.com/installation/windows/) bezeichnet werden. Automatisieren Sie damit das Erstellen eines Docker-Clients, mit dem Sie Azure-VMs als Docker-Hosts verwenden können.
> 
> 

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Verknüpfen der Azure-CLI mit Ihrem Azure-Konto
Bevor Sie die Azure-CLI nutzen können, müssen Sie Ihre Azure-Anmeldeinformationen mit der Azure-CLI auf Ihrer Plattform verknüpfen. Der Abschnitt [Verbinden mit Ihrem Azure-Abonnement](/cli/azure/authenticate-azure-cli) erläutert, wie die Datei **.publishsettings** heruntergeladen und importiert wird oder wie Ihre Azure-CLI mit einer Organisations-ID verknüpft wird.

> [!NOTE]
> Beide Methoden der Authentifizierung verhalten sich unterschiedlich. Lesen Sie daher unbedingt das genannte Dokument, um die Unterschiede zu verstehen.
> 
> 

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Installieren und Verwenden der Docker-VM-Erweiterung für Azure
Folgen Sie den [Installationsanweisungen für Docker](https://docs.docker.com/installation/#installation) , um Docker auf Ihrem Computer lokal zu installieren.

Damit Docker mit einem virtuellen Azure-Computer verwendet werden kann, muss das für den virtuellen Computer verwendete Linux-Image über den installierten [Azure Linux VM Agent](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) verfügen. Zurzeit stehen nur zwei Imagetypen zur Verfügung, die diesen bereitstellen:

* Ein Ubuntu-Image aus der Azure-Image-Galerie oder
* Ein benutzerdefiniertes Linux-Image, das Sie mit dem installierten und konfigurierten Azure Linux VM Agent erstellt haben. Unter [Azure Linux VM Agent](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) finden Sie weitere Informationen über das Erstellen eines benutzerdefinierten virtuellen Linux-Computers mit dem Azure VM Agent.

### <a name="using-the-azure-image-gallery"></a>Verwenden des Azure-Image-Katalogs
Verwenden Sie in einer Bash- oder Terminalsitzung den folgenden Azure-CLI-Befehl, um im VM-Katalog das neueste Ubuntu-Image zu suchen. Geben Sie

`azure vm image list | grep Ubuntu-14_04`

ein, oder wählen Sie einen der Imagenamen aus, z.B. `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`. Erstellen Sie anschließend mithilfe des folgenden Befehls eine VM mit diesem Image.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

Hinweis:

* *&lt;vm-cloudservice name&gt;* ist der Name des virtuellen Computers, der zum Docker-Containerhostcomputer in Azure wird.
* *&lt;username&gt;* ist der Benutzername des standardmäßigen Stammbenutzers des virtuellen Computers.
* *&lt;password&gt;* ist das Kennwort des Kontos *username*, das die Komplexitätsstandards für Azure erfüllt.

> [!NOTE]
> Derzeit muss ein Kennwort aus mindestens 8 Zeichen bestehen und dabei einen Kleinbuchstaben, einen Großbuchstaben, eine Ziffer und eines der folgenden Sonderzeichen enthalten: `!@#$%^&+=`. Der Punkt am Ende des vorhergehenden Satzes zählt nicht zu den Sonderzeichen.
> 
> 

War die Eingabe erfolgreich, sollte Ihnen in etwa das Folgende angezeigt werden, abhängig von den genauen Argumenten und Optionen, die Sie verwendet haben:

![](media/cli-use-docker/dockercreateresults.png)

> [!NOTE]
> Das Erstellen eines virtuellen Computers kann einige Minuten in Anspruch nehmen. Nach der Bereitstellung (Zustandswert `ReadyRole`) wird der Docker-Daemon (der Docker-Dienst) jedoch gestartet, und Sie können eine Verbindung zum Docker-Containerhost herstellen.
> 
> 

Um den virtuellen Docker-Computer, den Sie in Azure erstellt haben, zu testen, geben Sie Folgendes ein:

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

*&lt;vm-name-you-used&gt;* ist der Name des virtuellen Computers, den Sie in Ihrem Aufruf von `azure vm docker create` verwendet haben. Es sollte etwas angezeigt werden, das in etwa dem Folgenden entspricht, welches angibt, dass Ihr virtueller Docker-Hostcomputer betriebsbereit ist, in Azure ausgeführt wird und auf Ihre Befehle wartet. 

Sie können jetzt versuchen, über den Docker-Client eine Verbindung herzustellen, um Informationen abzurufen (bei einigen Docker-Client-Einrichtungen, z.B. auf dem Mac, müssen Sie möglicherweise `sudo` verwenden):

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

Um sicherzustellen, dass alles funktioniert, können Sie auf dem virtuellen Computer die Docker-Erweiterung suchen:

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Authentifizierung des virtuellen Docker-Hostcomputers
Neben dem virtuellen Docker-Computer erstellt der Befehl `azure vm docker create` automatisch auch die nötigen Zertifikate, um Ihrem Docker-Clientcomputer über HTTPS die Verbindung mit dem Azure-Containerhost zu ermöglichen. Die Zertifikate werden auf dem Client- und dem Hostcomputer gespeichert. Bei den nachfolgenden Versuchen werden die Zertifikate erneut verwendet und für den neuen Host freigegeben.

Standardmäßig werden die Zertifikate unter `~/.docker`abgelegt, und Docker wird für die Ausführung an Port **2376**konfiguriert. Wenn Sie einen anderen Port oder ein anderes Verzeichnis verwenden möchten, können Sie eine der folgenden Befehlszeilenoptionen `azure vm docker create` verwenden, damit der virtuelle Docker-Containerhost einen anderen Port oder andere Zertifikate verwendet, um eine Verbindung mit Clients herzustellen:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

Der Docker-Daemon auf dem Host ist so konfiguriert, dass er Clientverbindungen auf dem angegebenen Port mithilfe der durch den Befehl `azure vm docker create` generierten Zertifikate abhört und authentifiziert. Der Clientcomputer muss über diese Zertifikate verfügen, um Zugriff auf den Docker-Host zu erlangen.

> [!NOTE]
> Ein vernetzter Host ist ohne diese Zertifikate anfällig für jeden, der sich mit dem Computer verbindet. Bevor Sie die Standardkonfiguration ändern, setzen Sie sich mit den Risiken für Ihre Computer und Anwendungen auseinander.
> 
> 

## <a name="next-steps"></a>Nächste Schritte
* Sie sind nun bereit, das [Docker-Benutzerhandbuch] und Ihren virtuellen Docker-Computer zu verwenden. Informationen zum Erstellen eines Docker-fähigen virtuellen Computers im neuen Portal finden Sie unter [Gewusst wie: Verwenden der Docker-VM-Erweiterung mit dem Portal].
* Die Azure Docker-VM-Erweiterung unterstützt auch Docker Compose. Diese Lösung verwendet eine deklarative YAML-Datei, um eine auf einem Entwicklermodell basierende Anwendung umgebungsübergreifend zu machen und eine einheitliche Bereitstellung zu generieren. Weitere Informationen finden Sie unter [Erste Schritte mit Docker und Compose zum Definieren und Ausführen einer Anwendung mit mehreren Containern auf einem virtuellen Azure-Computer].  

<!--Anchors-->
[Subheading 1]:#subheading-1
[Subheading 2]:#subheading-2
[Subheading 3]:#subheading-3
[Next steps]:#next-steps

[How to use the Docker VM Extension with Azure]:#How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]:#Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]:#Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]:../../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]:../../../app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md
[Gewusst wie: Verwenden der Docker-VM-Erweiterung mit dem Portal]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Docker-Benutzerhandbuch]:https://docs.docker.com/userguide/

[Erste Schritte mit Docker und Compose zum Definieren und Ausführen einer Anwendung mit mehreren Containern auf einem virtuellen Azure-Computer]:../docker-compose-quickstart.md
