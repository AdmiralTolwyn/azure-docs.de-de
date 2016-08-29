<properties 
	pageTitle="Verwenden von SSH unter Linux und Mac | Microsoft Azure" 
	description="Generieren und Verwenden von SSH-Schlüsseln unter Linux und Mac für die Ressourcen-Manager- und klassischen Bereitstellungsmodelle in Azure." 
	services="virtual-machines-linux" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines-linux" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2016" 
	ms.author="rasquill"/>

#Verwenden von SSH mit Linux und Mac in Azure

> [AZURE.SELECTOR]
- [Windows](virtual-machines-linux-ssh-from-windows.md)
- [Linux/Mac](virtual-machines-linux-ssh-from-linux.md)


In diesem Thema wird beschrieben, wie Sie unter Linux und Mac mithilfe von **ssh-keygen** und **openssl** Dateien im **SSH-RSA**- und **PEM**-Format erstellen und verwenden, um die Kommunikation mit Linux-basierten virtuellen Azure-Computern zu sichern. Für neue Bereitstellungen wird die Erstellung von Linux-basierten Azure-VMs anhand des Ressourcen-Manager-Bereitstellungsmodells empfohlen. Dazu ist eine Datei oder Zeichenfolge (je nach Bereitstellungsclient) mit einem öffentlichen Schlüssel im *SSH-RSA*-Format erforderlich. Das [Azure-Portal](https://portal.azure.com) akzeptiert gegenwärtig sowohl für klassische als auch für Resource Manager-Bereitstellungen nur die Zeichenfolgen im **SSH-RSA**-Format.


> [AZURE.NOTE] Falls Sie einen Moment Zeit haben, würden wir uns freuen, wenn Sie an dieser [kurzen Umfrage](https://aka.ms/linuxdocsurvey) teilnehmen könnten, um zur Verbesserung der Dokumentation für virtuelle Azure-Computer unter Linux beizutragen. Jede Antwort hilft uns dabei, Sie noch besser bei Ihrer Arbeit zu unterstützen.


> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)] Informationen dazu, wie Sie diese Dateien zum Sichern der Kommunikation mit Linux-VMs in Azure auf einem Windows-Computer erstellen, finden Sie unter [Verwenden von SSH-Schlüsseln unter Windows](virtual-machines-linux-ssh-from-windows.md).

## Welche Dateien sind erforderlich?

Ein grundlegendes SSH-Setup für Azure umfasst ein öffentliches und privates **SSH-RSA**-Schlüsselpaar mit 2.048 Bit (**ssh-keygen** speichert diese Dateien standardmäßig unter **~/.ssh/id\_rsa** und **~/.ssh/id-rsa.pub**, sofern Sie die Standardeinstellungen nicht ändern) sowie eine `.pem`-Datei, die zur Verwendung mit dem klassischen Bereitstellungsmodell des klassischen Portals aus der **id\_rsa**-Datei mit dem privaten Schlüssel generiert wird.

Im Folgenden sind die Dateitypen für die unterschiedlichen Bereitstellungsszenarien aufgeführt:

1. **SSH-RSA**-Schlüssel sind unabhängig vom Bereitstellungsmodell für alle Bereitstellungen mithilfe des [Azure-Portals](https://portal.azure.com) erforderlich.
2. PEM-Dateien sind erforderlich, um VMs mithilfe des [klassischen Portals](https://manage.windowsazure.com) zu erstellen. PEM-Dateien werden auch in klassischen Bereitstellungen mit der [Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md) unterstützt.

## Erstellen von Schlüsseln zur Verwendung mit SSH

Wenn Sie bereits über SSH-Schlüssel verfügen, übergeben Sie die Datei mit dem öffentlichen Schlüssel beim Erstellen des virtuellen Azure-Computers.

Falls Sie die Dateien erstellen müssen, gehen Sie wie folgt vor:

1. Stellen Sie sicher, dass Ihre Implementierung von **ssh-keygen** und **openssl** auf dem neuesten Stand ist. Die Implementierung unterscheidet sich je nach Plattform.

	- Besuchen Sie im Fall von Mac die [Produktsicherheitswebsite von Apple](https://support.apple.com/HT201222), und wählen Sie ggf. die entsprechenden Updates aus.
	- Für Debian-basierte Linux-Distributionen wie Ubuntu, Debian, Mint usw.:

			sudo apt-get install --upgrade-only openssl

	- Für RPM-basierte Linux-Distributionen wie CentOS und Oracle Linux:

			sudo yum update openssl

	- Für SLES und OpenSUSE

			sudo zypper update openssl

2. Erstellen Sie mit **ssh-keygen** 2.048 Bit große RSA-Dateien für den öffentlichen und privaten Schlüssel, und übernehmen Sie den Standardspeicherort und -namen von `~/.ssh/id_rsa`, sofern Sie keinen bestimmten Speicherort oder Namen für die Dateien verwenden. Der grundlegende Befehl lautet wie folgt:

		ssh-keygen -t rsa -b 2048 

	In der Regel fügt die **ssh-keygen**-Implementierung einen Kommentar sowie in vielen Fällen den Benutzernamen und Hostnamen des Computers hinzu. Sie können mithilfe der Option `-C` einen bestimmten Kommentar angeben.

3. Erstellen Sie eine PEM-Datei aus der Datei `~/.ssh/id_rsa`, damit Sie mit dem klassischen Portal arbeiten können. Verwenden Sie den **openssl**-Befehl wie folgt:

		openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

	Wenn Sie die PEM-Datei aus einer anderen Datei mit einem privaten Schlüssel erstellen möchten, ändern Sie das `-key`-Argument.

> [AZURE.NOTE] Wenn Sie vorhaben, mit dem klassischen Bereitstellungsmodell bereitgestellte Dienste zu verwalten, können Sie auch eine **CER**-Datei erstellen. Dies erfordert jedoch weder die Verwendung von **SSH** noch das Herstellen einer Verbindung mit Linux-VMs und ist daher nicht Gegenstand dieses Artikels. Geben Sie zum Konvertieren der PEM-Datei in eine DER-codierte X.509-Zertifikatdatei unter Linux oder auf einem Mac Folgendes ein: <br /> openssl x509 -outform der -in myCert.pem -out myCert.cer

## Verwenden bereits vorhandener SSH-Schlüssel

Sie können SSH-RSA (`.pub`)-Schlüssel für alle neuen Bereitstellungen verwenden, insbesondere mit dem Ressourcen-Manager-Bereitstellungsmodell und dem Vorschauportal. Zur Verwendung des klassischen Portals müssen Sie möglicherweise eine `.pem`-Datei aus Ihren Schlüsseln erstellen.

## Erstellen einer VM mit Ihrer Datei mit dem öffentlichen Schlüssel

Nachdem Sie die benötigten Dateien erstellt haben, stehen Ihnen viele Möglichkeiten zur Verfügung, um eine VM zu erstellen, mit der Sie durch den Austausch von privaten und öffentlichen Schlüsseln eine sichere Verbindung herstellen können. In fast allen Fällen (insbesondere bei Ressourcen-Manager-Bereitstellungen) übergeben Sie die PUB-Datei, wenn Sie dazu aufgefordert werden, den Pfad einer SSH-Schlüsseldatei oder den Inhalt einer Datei als Zeichenfolge einzugeben.

### Beispiel: Erstellen einer VM mit der Datei „id\_rsa.pub“

Das häufigste Szenario ist die imperative Erstellung einer VM – oder das Hochladen einer Vorlage zum Erstellen einer VM. Das folgende Codebeispiel veranschaulicht die Erstellung einer neuen, sicheren Linux-VM in Azure, indem der Name der öffentlichen Datei (in diesem Fall die Standarddatei `~/.ssh/id_rsa.pub`) an den Befehl `azure vm create` übergeben wird. (Die anderen Argumente, z.B. für die Ressourcengruppe und das Speicherkonto, wurden zuvor erstellt.) In diesem Beispiel wird die Resource Manager-Bereitstellungsmethode verwendet. Stellen Sie daher sicher, dass die Azure-Befehlszeilenschnittstelle entsprechend mit `azure config mode arm` festgelegt ist:

	azure vm create \
	--nic-name testnic \
	--public-ip-name testpip \
	--vnet-name testvnet \
	--vnet-subnet-name testsubnet \
	--storage-account-name computeteststore 
	--image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
	--username ops \
	-ssh-publickey-file ~/.ssh/id_rsa.pub \
	testrg testvm westeurope linux

Das nächste Beispiel zeigt, wie das **SSH-RSA**-Format mit einer Ressourcen-Manager-Vorlage und der Azure-Befehlszeilenschnittstelle verwendet wird, um eine Ubuntu-VM zu erstellen, die durch einen Benutzernamen und den Inhalt der Datei `~/.ssh/id_rsa.pub` in Form einer Zeichenfolge gesichert wird. (In diesem Fall wurde die Zeichenfolge des öffentlichen Schlüssels zum Verbessern der Lesbarkeit gekürzt.)

	azure group deployment create \
	--resource-group test-sshtemplate \
	--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
	--name mysshdeployment
	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	testnewStorageAccountName: testsshvmtemplate3
	adminUserName: ops
	sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQA+/L+rHIjz+nXTzxApgnP+iKDZco9 user@macbookpro
	dnsNameForPublicIP: testsshvmtemplate
	location: West Europe
	vmName: sshvm
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "mysshdeployment"
	+ Waiting for deployment to complete
	data:    DeploymentName     : mysshdeployment
	data:    ResourceGroupName  : test-sshtemplate
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-10-08T00:12:12.2529678Z
	data:    Mode               : Incremental
	data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	data:    ContentVersion     : 1.0.0.0
	data:    Name                   Type    Value

	data:    newStorageAccountName  String  testtestsshvmtemplate3
	data:    adminUserName          String  ops
	data:    sshKeyData             String  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDAkek3P6V3EhmD+xP+iKDZco9 user@macbookpro
	data:    dnsNameForPublicIP     String  testsshvmtemplate
	data:    location               String  West Europe
	data:    vmSize                 String  Standard_A2
	data:    vmName                 String  sshvm
	data:    ubuntuOSVersion        String  14.04.4-LTS
	info:    group deployment create command OK


### Beispiel: Erstellen einer VM mit einer PEM-Datei

Sie können die PEM-Datei anschließend wie im folgenden Beispiel gezeigt mit dem klassischen Portal oder dem klassischen Bereitstellungsmodus (`azure config mode asm`) und `azure vm create` verwenden:

	azure vm create \
	-l "West US" -n testpemasm \
	-P -t myCert.pem -e 22 \
	testpemasm \
	b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160406-de-DE-30GB \
	ops
	info:    Executing command vm create
	warn:    --vm-size has not been specified. Defaulting to "Small".
	+ Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160406-de-DE-30GB
	+ Looking up cloud service
	info:    cloud service testpemasm not found.
	+ Creating cloud service
	+ Retrieving storage accounts
	+ Configuring certificate
	+ Creating VM
	info:    vm create command OK


## Herstellen einer Verbindung mit Ihrer VM

Für den **SSH**-Befehl werden als Eingabeparameter ein Benutzername zur Anmeldung, die Netzwerkadresse des Computers und der Port, an dem die Verbindung mit der Adresse hergestellt werden soll, verwendet. Zudem sind viele weitere spezielle Varianten möglich. (Weitere Informationen zu **SSH** finden Sie in folgendem [Artikel über Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell), in englischer Sprache.)

Ein typisches Verwendungsbeispiel mit einer Ressourcen-Manager-Bereitstellung kann wie folgt aussehen, wenn Sie lediglich eine Unterdomäne und einen Bereitstellungsspeicherort angegeben haben:

	ssh user@subdomain.westus.cloudapp.azure.com -p 22

Wenn Sie eine Verbindung mit einem Clouddienst herstellen, der mit dem klassischen Bereitstellungsmodell bereitgestellt wurde, würde die Adresse wie folgt aussehen:

	ssh user@subdomain.cloudapp.net -p 22

Da sich die Adressform ändern kann, müssen Sie die Adresse der Azure-VM ermitteln (es ist immer möglich, die IP-Adresse, oder falls zugewiesen, einen benutzerdefinierten Domänennamen zu verwenden).

### Ermitteln der SSH-Adresse Ihrer Azure-VM bei klassischen Bereitstellungen

Sie können die mit einer VM und dem klassischen Bereitstellungsmodell zu verwendende Adresse ermitteln, indem Sie den Befehl `azure vm show` mit dem VM-Namen ausführen:

	azure vm show testpemasm
	info:    Executing command vm show
	+ Getting virtual machines
	data:    DNSName "testpemasm.cloudapp.net"
	data:    Location "West US"
	data:    VMName "testpemasm"
	data:    IPAddress "100.116.160.154"
	data:    InstanceStatus "ReadyRole"
	data:    InstanceSize "Small"
	data:    Image "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-de-DE-30GB"
	data:    OSDisk hostCaching "ReadWrite"
	data:    OSDisk name "testpemasm-testpemasm-0-201510102050230517"
	data:    OSDisk mediaLink "https://portalvhds4blttsxgjj1rf.blob.core.windows.net/vhd-store/testpemasm-2747c9c432b043ff.vhd"
	data:    OSDisk sourceImageName "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-de-DE-30GB"
	data:    OSDisk operatingSystem "Linux"
	data:    OSDisk iOType "Standard"
	data:    ReservedIPName ""
	data:    VirtualIPAddresses 0 address "40.83.178.221"
	data:    VirtualIPAddresses 0 name "testpemasmContractContract"
	data:    VirtualIPAddresses 0 isDnsProgrammed true
	data:    Network Endpoints 0 localPort 22
	data:    Network Endpoints 0 name "ssh"
	data:    Network Endpoints 0 port 22
	data:    Network Endpoints 0 protocol "tcp"
	data:    Network Endpoints 0 virtualIPAddress "40.83.178.221"
	data:    Network Endpoints 0 enableDirectServerReturn false
	info:    vm show command OK

### Ermitteln der SSH-Adresse Ihrer Azure-VM bei Ressourcen-Manager-Bereitstellungen

	azure vm show testrg testvm
	info:    Executing command vm show
	+ Looking up the VM "testvm"
	+ Looking up the NIC "testnic"
	+ Looking up the public ip "testpip"

Überprüfen Sie den Abschnitt mit dem Netzwerkprofil:

	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-21-8E-AE
	data:          Provisioning State        :Succeeded
	data:          Name                      :testnic
	data:          Location                  :westeurope
	data:            Private IP alloc-method :Static
	data:            Private IP address      :192.168.1.101
	data:            Public IP address       :40.115.48.189
	data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
	data:
	data:    Diagnostics Instance View:
	info:    vm show command OK

Wenn Sie bei der Erstellung der VM nicht den SSH-Standardport 22 verwendet haben, können Sie die Ports mit eingehenden Regeln wie im folgenden Beispiel gezeigt mit dem Befehl `azure network nsg show` ermitteln:

	azure network nsg show testrg testnsg
	info:    Executing command network nsg show
	+ Looking up the network security group "testnsg"
	data:    Id                              : /subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testnsg
	data:    Name                            : testnsg
	data:    Type                            : Microsoft.Network/networkSecurityGroups
	data:    Location                        : westeurope
	data:    Provisioning state              : Succeeded
	data:    Security group rules:
	data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
	data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
	data:    testnsgrule                    *                  *            *               22                Tcp       Inbound    Allow   1000
	data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
	data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
	data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
	data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
	data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
	data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
	info:    network nsg show command OK

### Beispiel: Ausgabe einer SSH-Sitzung mit PEM-Schlüsseln und einer klassischen Bereitstellung

Wenn Sie eine VM anhand einer PEM-Datei erstellt haben, die aus Ihrer Datei `~/.ssh/id_rsa` generiert wurde, können Sie direkt eine SSH-Verbindung mit dieser VM herstellen. Beachten Sie, dass in diesem Fall Ihr privater Schlüssel in `~/.ssh/id_rsa` vom Zertifikathandshake verwendet wird. (Beim VM-Erstellungsprozess wird der öffentliche Schlüssel aus der PEM-Datei berechnet und die SSH-RSA-Form des öffentlichen Schlüssels in `~/.ssh/authorized_users` eingefügt.) Das Herstellen der Verbindung kann wie im folgenden Beispiel aussehen:

	ssh ops@testpemasm.cloudapp.net -p 22
	The authenticity of host 'testpemasm.cloudapp.net (40.83.178.221)' can't be established.
	RSA key fingerprint is dc:bb:e4:cc:59:db:b9:49:dc:71:a3:c8:37:36:fd:62.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added 'testpemasm.cloudapp.net,40.83.178.221' (RSA) to the list of known hosts.
	
    Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-49-generic x86_64)
	
	* Documentation:  https://help.ubuntu.com/

    System information as of Fri Apr 15 18:51:42 UTC 2016

    System load: 0.31              Memory usage: 2%   Processes:       213
    Usage of /:  42.1% of 1.94GB   Swap usage:   0%   Users logged in: 0

    Graph this data and manage this system at:
    https://landscape.canonical.com/

    Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

    0 packages can be updated.
	0 updates are security updates.
	
	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.
	
	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.


## Falls Sie Probleme beim Herstellen der Verbindung haben

Möglicherweise lässt sich das Problem mithilfe der Empfehlungen unter [Problembehandlung bei SSH-Verbindungen](virtual-machines-linux-troubleshoot-ssh-connection.md) beheben.

## Nächste Schritte
 
Nachdem Sie eine Verbindung mit Ihrer VM hergestellt haben, müssen Sie die ausgewählte Distribution vor der weiteren Verwendung unbedingt aktualisieren.

<!---HONumber=AcomDC_0817_2016-->