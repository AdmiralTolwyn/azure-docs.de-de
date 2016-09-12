<properties
	pageTitle="Erstellen und Hochladen einer Linux-VHD in Azure"
	description="Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die ein Linux-Betriebssystem enthält."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="szark"/>

# Informationen zu nicht unterstützten Verteilungen #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


**Wichtig**: Das Azure-Plattform-SLA gilt für virtuelle Computer unter dem Betriebssystem Linux nur dann, wenn eine der [unterstützten Verteilungen](virtual-machines-linux-endorsed-distros.md) verwendet wird. Alle Linux-Verteilungen, die im Azure-Image-Katalog bereitgestellt werden, sind unterstützte Verteilungen mit der erforderlichen Konfiguration.

- [Linux auf von Azure unterstützten Verteilungen](virtual-machines-linux-endorsed-distros.md)
- [Unterstützung für Linux-Images in Microsoft Azure](https://support.microsoft.com/kb/2941892)

Alle auf Azure ausgeführten Distributionen müssen eine Reihe an Voraussetzungen erfüllen, damit sie entsprechend auf der Plattform ausgeführt werden. Dieser Artikel umfasst in keinem Fall alle Aspekte, da jede Distribution unterschiedlich ist. Es ist durchaus möglich, dass Sie, selbst wenn Sie alle unten aufgeführten Kriterien erfüllen, Ihr Linus-System noch erheblich optimieren müssen, um gewährleisten zu können, dass es ordnungsgemäß auf der Plattform ausgeführt wird.

Daher wird empfohlen, dass Sie nach Möglichkeit mit einem unserer [Linux auf Azure-unterstützten Distributionen](virtual-machines-linux-endorsed-distros.md) beginnen. Die folgenden Artikel führen Sie durch die Vorbereitung der verschiedenen unterstützten Linux-Verteilungen, die auf Azure unterstützt werden:

- **[CentOS-basierte Verteilungen](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES und openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**

Der Rest dieses Artikels bietet eine allgemeine Hilfe für das Ausführen Ihrer Linux-Verteilung auf Azure.


## Allgemeine Installationshinweise für Linux ##

- Das VHDX-Format wird in Azure noch nicht unterstützt, dafür jedoch **virtuelle Festplatten mit fester Größe**. Sie können den Datenträger mit dem Hyper-V-Manager oder dem convert-vhd-Cmdlet in das VHD-Format konvertieren. Wenn Sie VirtualBox verwenden, bedeutet dies, dass Sie **Feste Größe** auswählen und nicht die Standardeinstellung, die dynamisch beim Erstellen des Datenträgers zugewiesen wird.

- Beim Installieren des Linux-Systems wird empfohlen, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird. [LVM](virtual-machines-linux-configure-lvm.md) oder [RAID](virtual-machines-linux-configure-raid.md) kann wahlweise auf Datenträgern verwendet werden.

- NUMA wird bei größeren VMs aufgrund eines Fehlers in den Linux Kernel-Versionen unter 2.6.37 nicht unterstützt. Dieses Problem betrifft in erster Linie jene Distributionen, die den vorgeschalteten Red Hat 2.6.32 Kernel verwenden. Bei der manuellen Installation des Azure Linux Agent (waagent) wird NUMA in der GRUB-Konfiguration für das Linux-Kernel automatisch deaktiviert.

- Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Der Linux-Agent kann konfiguriert werden, eine Auslagerungsdatei auf dem temporären Ressourcendatenträger zu erstellen. Weitere Informationen dazu finden Sie in den folgenden Schritten.

- Alle virtuellen Festplatten müssen eine Größe aufweisen, die ein Vielfaches von 1 MB ist.


### Installieren von Linux ohne Hyper-V ###

In einigen Fällen enthalten Linux-Installationsprogramme möglicherweise keine Treiber für Hyper-V in der anfänglichen Ramdisk (initrd oder initramfs), bis sie erkennen, dass sie eine Hyper-V-Umgebung ausführen. Wenn ein anderes Virtualisierungssystem (z. B. Virtualbox oder KVM) zur Vorbereitung des Linux-Image verwendet wird, müssen Sie möglicherweise das Image "initrd" neu erstellen, um sicherzustellen, dass mindestens die Kernelmodule `hv_vmbus` und `hv_storvsc` auf der anfänglichen Ramdisk verfügbar sind. Dies ist ein bekanntes Problem, zumindest auf Systemen, die auf der Red Hat-Upstream-Verteilung basieren.

Der Mechanismus für die Neuerstellung des Images "initrd" oder "initramfs" variiert je nach der Verteilung. In der Dokumentation zu Ihrer Verteilung oder über den Support erhalten Sie Informationen zur geeigneten Vorgehensweise. Es folgt ein Beispiel für das Neuerstellen des Image "initrd" mit dem Hilfsprogramm `mkinitrd`:

Sichern Sie zuerst das bestehende Image "initrd":

	# cd /boot
	# sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Erstellen Sie das Image "initrd" anschließend mithilfe der Kernelmodule `hv_vmbus` und `hv_storvsc` neu:

	# sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### Ändern der Größe von virtuellen Festplatten ###

VHD-Images auf Azure benötigen eine virtuelle Größe, die auf 1 MB ausgerichtet ist. Normalerweise sollten mit Hyper-V erstellte virtuelle Festplatten bereits korrekt ausgerichtet sein. Wenn die virtuelle Festplatte nicht ordnungsgemäß ausgerichtet wurde, wird möglicherweise sinngemäß die folgende Fehlermeldung angezeigt, wenn Sie versuchen, ein *Image* von der virtuellen Festplatte zu erstellen:

	"The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Zur Umgehung des Problems können Sie die Größe des virtuellen Computers mithilfe der Hyper-V-Manager-Konsole oder des Powershell-Cmdlets [Resize-VHD](http://technet.microsoft.com/library/hh848535.aspx) ändern. Wenn Sie nicht in einer Windows-Umgebung arbeiten, sollten Sie die Konvertierung (falls erforderlich) mit qemu-img durchführen und die Größe der virtuellen Festplatte ändern.

> [AZURE.NOTE] In den Versionen qemu-img-Versionen > oder = 2.2.1 taucht ein bekannter Bug auf, der zu Fehlern bei der Formatierung der VHD-führt. Dieses Problem wurde in QEMU 2.6 behoben. Sie sollten entweder qemu-img 2.2.0 oder niedriger verwenden oder auf 2.6 oder höher aktualisieren. Referenz: https://bugs.launchpad.net/qemu/+bug/1490611.


 1. Das direkte Ändern der Größe der virtuellen Festplatte mithilfe von Tools wie `qemu-img` oder `vbox-manage` kann dazu führen, dass die virtuelle Festplatte nicht startfähig ist. Daher empfiehlt es sich, die virtuelle Festplatte zuerst in ein RAW-Datenträgerimage zu konvertieren. Wenn das VM-Image bereits als RAW-Datenträgerimage erstellt wurde (die Standardeinstellung für einige Hypervisoren wie KVM), können Sie diesen Schritt überspringen:

		# qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

 2. Berechnen Sie die erforderliche Größe des Datenträger-Image, um sicherzustellen, dass die virtuelle Größe auf 1 MB ausgerichtet wird. Das folgenden Bash-Shell-Skript kann dabei helfen. Das Skript verwendet "`qemu-img info`", um die virtuelle Größe des Datenträger-Image zu ermitteln, und berechnet dann die Größe bis zum nächsten MB:

		rawdisk="MyLinuxVM.raw"
		vhddisk="MyLinuxVM.vhd"

		MB=$((1024*1024))
		size=$(qemu-img info -f raw --output json "$rawdisk" | \
		       gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

		rounded_size=$((($size/$MB + 1)*$MB))
		echo "Rounded Size = $rounded_size"

 3. Ändern Sie die Größe des RAW-Datenträgers mit $rounded\_size, wie im obigen Skript festgelegt:

		# qemu-img resize MyLinuxVM.raw $rounded_size

 4. Konvertieren Sie nun den RAW-Datenträger zurück in eine virtuelle Festplatte mit fester Größe:

		# qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd



## Linux-Kernelanforderungen ##

Die Treiber für die Linux-Integrationsdienste (Linux Integration Services, LIS) für Hyper-V und Azure tragen direkt zum Linux-Upstream-Kernel bei. Viele Distributionen, die eine neue Linux-Kernelversion (d. h. 3.x) enthalten, verfügen bereits über diese Treiber oder bieten ansonsten zurückportierte Versionen dieser Treiber mit ihren Kerneln. Diese Treiber werden ständig im Upstream-Kernel mit neuen Fehlerbehebungen und Features aktualisiert. Daher empfiehlt sich, nach Möglichkeit eine [bestätigte Distribution](virtual-machines-linux-endorsed-distros.md) auszuführen, die diese Fehlerbehebungen und Updates enthält.

Wenn Sie eine Variante der Red Hat Enterprise Linux-Versionen **6.0-6.3** ausführen, müssen Sie die neuesten LIS-Treiber für Hyper-V installieren. Die Treiber sind [unter diesem Speicherort](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409) zu finden. Ab RHEL **6.4+** (und Ableitungen) sind die LIS-Treiber bereits im Kernel enthalten. Daher sind keine zusätzlichen Installationspakete nötig, um diese Systeme auf Azure auszuführen.

Wenn ein benutzerdefiniertes Kernel erforderlich ist, empfiehlt es sich, eine neuere Kernelversion (d. h. **3.8+**) zu verwenden. Für Verteilungen oder Anbieter, die ihr eigenes Kernel verwalten, ist das Zurückportieren der LIS-Treiber vom Upstream-Kernel zu Ihrem benutzerdefinierten Kernel mit einem gewissen Aufwand verbunden. Selbst wenn Sie bereits eine relativ aktuelle Kernelversion ausführen, wird dringend empfohlen, die Upstream-Fehlerbehebungen in den LIS-Treibern nachzuverfolgen und diese bei Bedarf zurück zu portieren. Der Ort der LIS-Treiberquelldateien ist in der Datei [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) in der Linux-Kernelquellstruktur verfügbar:

	F:	arch/x86/include/asm/mshyperv.h
	F:	arch/x86/include/uapi/asm/hyperv.h
	F:	arch/x86/kernel/cpu/mshyperv.c
	F:	drivers/hid/hid-hyperv.c
	F:	drivers/hv/
	F:	drivers/input/serio/hyperv-keyboard.c
	F:	drivers/net/hyperv/
	F:	drivers/scsi/storvsc_drv.c
	F:	drivers/video/fbdev/hyperv_fb.c
	F:	include/linux/hyperv.h
	F:	tools/hv/

Das Fehlen der folgenden Patches haben in einem sehr geringen Ausmaß Probleme auf Azure verursacht. Daher müssen diese im Kernel enthalten sein. Diese Liste ist keineswegs vollständig für alle Verteilungen:

- [ata\_piix: defer disks to the Hyper-V drivers by default (Standardmäßiges Zurückstellen von Datenträgern zu Hyper-V-Treibern, in englischer Sprache)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [storvsc: Account for in-transit packets in the RESET path (Konto für Pakete auf dem Transportweg im RESET-Pfad, in englischer Sprache)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
- [storvsc: avoid usage of WRITE\_SAME (Vermeiden der Verwendung von WRITE\_SAME, in englischer Sprache)](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
- [storvsc: Disable WRITE SAME for RAID and virtual host adapter drivers (Deaktivieren von WRITE\_SAME für RAID und virtuelle Hostadaptertreiber, in englischer Sprache)](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
- [storvsc: NULL pointer dereference fix (Beheben der NULL-Zeigerdereferenzierung, in englischer Sprache)](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
- [storvsc: ring buffer failures may result in I/O freeze (Einfrieren von E/A durch Ringpufferfehler, in englischer Sprache)](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
- [scsi\_sysfs: Schutz gegen doppelte Ausführung von \_\_scsi\_remove\_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)


## Azure Linux Agent ##

Der [Azure Linux-Agent](virtual-machines-linux-agent-user-guide.md) (waagent) ist für die entsprechende Bereitstellung eines virtuellen Linux-Computers in Azure erforderlich. Unter [Linux Agent GitHub repo](https://github.com/Azure/WALinuxAgent) können Sie die neueste Version abrufen, Probleme einreichen oder Pull-Anforderungen senden.

- Der Linux-Agent wird unter der Apache 2.0-Lizenz veröffentlicht. Viele Distributionen stellen RPM- oder DEB-Pakete bereits für den Agent bereit, in einigen Fällen können diese mit ein wenig Aufwand installiert und aktualisiert werden.

- Für den Azure Linux-Agent ist Python v2.6+ erforderlich.

- Für den Agent ist zudem das Modul python-pyasn1 erforderlich. Die meisten Distributionen bieten dieses als ein separates Paket an, das installiert werden kann.

- In einigen Fällen ist der Azure Linux-Agent möglicherweise nicht mit dem NetworkManager kompatibel. Viele der über die Distributionen bereitgestellten RPM-/DEB-Pakete konfigurieren den NetworkManager im Widerspruch zum waagent-Paket. Daher wird beim Installieren des Linux Agent-Pakets der NetworkManager deinstalliert.


## Allgemeine Linux-Systemanforderungen ##

- Ändern Sie die Kernel-Boot-Zeile in Grub oder Grub2, um die folgenden Parameter einzubinden. Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen:

		console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300

	Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen.

	Neben den oben erwähnten Punkten wird es empfohlen, die folgenden Parameter zu *entfernen*, sofern sie vorhanden sind:

		rhgb quiet crashkernel=auto

	Weder der Graphical Boot noch der Quiet Boot sind in einer Cloud-Umgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen.

	Die Option `crashkernel` kann bei Bedarf konfiguriert bleiben. Beachten Sie jedoch, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert, was bei kleineren virtuellen Computern problematisch sein kann.

- Installieren des Azure Linux-Agents

	Der Azure Linux-Agent ist für das Bereitstellen eines Linux-Images auf Azure erforderlich. Viele Distributionen stellen den Agent als ein RPM- oder DEB-Paket (das Paket heißt für gewöhnlich „WALinuxAgent“ oder „walinuxagent“) bereit. Der Agent kann zudem manuell installiert werden, indem die folgenden Schritte im [Linux Agent Guide](virtual-machines-linux-agent-user-guide.md) eingehalten werden.

- Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist für gewöhnlich die Standardeinstellung.

- Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.

	Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein *temporärer* Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Modifizieren Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in /etc/waagent.conf entsprechend:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

- Führen Sie abschließend Befehle zum Rückgängigmachen des virtuellen Computers aus:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

	>[AZURE.NOTE] In Virtualbox wird nach dem Ausführen von „waagent -force -deprovision“ möglicherweise der folgende Fehler angezeigt: `[Errno 5] Input/output error`. Diese Fehlermeldung ist nicht kritisch und kann ignoriert werden.

- Sie müssen den virtuellen Computer anschließend herunterfahren und die virtuelle Festplatte in Azure hochladen.

<!---HONumber=AcomDC_0831_2016-->