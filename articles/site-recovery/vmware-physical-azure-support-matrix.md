---
title: Unterstützungsmatrix für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure mit Azure Site Recovery | Microsoft-Dokumentation
description: Fasst die Unterstützung der Notfallwiederherstellung für VMware-VMs und physische Server auf Azure mit Azure Site Recovery zusammen.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: raynew
ms.openlocfilehash: 65c330a9b2dcc97160280daede926573fdef4c00
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679363"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Unterstützungsmatrix für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure

Dieser Artikel enthält eine Übersicht über die unterstützten Komponenten und Einstellungen für die Notfallwiederherstellung von VMware-VMs und physischen Servern in Azure mit [Azure Site Recovery](site-recovery-overview.md).

- [Erfahren Sie mehr](vmware-azure-architecture.md) über die Architektur der Notfallwiederherstellung für VMware-VMs/physische Server.
- Unsere [Tutorials](tutorial-prepare-azure.md) helfen Ihnen dabei, die Notfallwiederherstellung auszuprobieren.

## <a name="deployment-scenarios"></a>Bereitstellungsszenarien

**Szenario** | **Details**
--- | ---
Notfallwiederherstellung von virtuellen VMware-Computern | Replikation lokaler VMware-VMs in Azure. Dieses Szenario können Sie über das Azure-Portal oder mit [PowerShell](vmware-azure-disaster-recovery-powershell.md) bereitstellen.
Notfallwiederherstellung von physischen Servern | Die Replikation lokaler physischer Windows-/Linux-Server in Azure. Dieses Szenario können Sie im Azure-Portal bereitstellen.

## <a name="on-premises-virtualization-servers"></a>Lokale Virtualisierungsserver

**Server** | **Anforderungen** | **Details**
--- | --- | ---
vCenter Server | Version 6.7, 6.5, 6.0 oder 5.5 | Wir empfehlen Ihnen, einen vCenter-Server in Ihrer Notfallwiederherstellungsbereitstellung zu verwenden.
vSphere-Hosts | Version 6.7, 6.5, 6.0 oder 5.5 | Ihre vSphere-Hosts und vCenter-Server sollten sich im gleichen Netzwerk befinden wie der Prozessserver. Der Prozessserver wird standardmäßig auf dem Konfigurationsserver ausgeführt. [Weitere Informationen](vmware-physical-azure-config-process-server-overview.md)


## <a name="site-recovery-configuration-server"></a>Site Recovery-Konfigurationsserver

Der Konfigurationsserver ist ein lokaler Computer, auf dem Site Recovery-Komponenten ausgeführt werden (unter anderem der Konfigurationsserver, der Prozessserver und der Masterzielserver).

- Für die VMware-VMs muss der Konfigurationsserver eingerichtet werden, indem Sie eine OVF-Vorlage herunterladen, um einen virtuellen VMware-Computer zu erstellen.
- Für physische Servers muss der Konfigurationsservercomputer manuell eingerichtet werden.

**Komponente** | **Anforderungen**
--- |---
CPU-Kerne | 8
RAM | 16 GB
Anzahl der Datenträger | Drei Datenträger<br/><br/> Hierzu zählen der Betriebssystemdatenträger, der Prozessservercache-Datenträger und das Aufbewahrungslaufwerk für das Failback.
Freier Speicherplatz auf dem Datenträger | 600 GB für den Prozessservercache.
Freier Speicherplatz auf dem Datenträger | 600 GB für das Aufbewahrungslaufwerk.
Betriebssystem  | Windows Server 2012 R2 oder Windows Server 2016 mit Desktopoberfläche |
Gebietsschema des Betriebssystems | Englisch (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Nicht erforderlich für die Konfigurationsserverversion [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) oder höher. 
Windows Server-Rollen | Aktivieren Sie nicht Active Directory Domain Services, Internetinformationsdienste (IIS) oder Hyper-V. 
Gruppenrichtlinien| - Zugriff auf Eingabeaufforderung verhindern <br/> - Zugriff auf Programme zum Bearbeiten der Registrierung verhindern <br/> - Vertrauenslogik für Dateianlagen <br/> - Skriptausführung aktivieren <br/> - [Weitere Informationen](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Stellen Sie sicher, dass Sie:<br/><br/> - Es ist noch keine Standardwebsite vorhanden. <br/> - Die [anonyme Authentifizierung](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) ist aktiviert. <br/> - Aktivieren der Einstellung [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> - Es ist noch keine Website/App vorhanden, die an Port 443 lauscht.<br/>
NIC-Typ | VMXNET3 (bei Bereitstellung als VMware-VM)
Art der IP-Adresse | statischen
Ports | 443 für die Steuerkanalorchestrierung<br/>9443 für den Datentransport

## <a name="replicated-machines"></a>Replizierte Computer

Site Recovery unterstützt die Replikation beliebiger Workloads, die auf einem unterstützten Computer ausgeführt werden.

**Komponente** | **Details**
--- | ---
Computereinstellungen | Computer, die in Azure repliziert werden sollen, müssen die [Azure-Anforderungen](#azure-vm-requirements) erfüllen.
Computerworkload | Site Recovery unterstützt die Replikation beliebiger Workloads, die auf einem unterstützten Computer ausgeführt werden. [Weitere Informationen](https://aka.ms/asr_workload)
Windows | - Ab Windows Server 2019 (unterstützt ab [Update Rollup 34](https://support.microsoft.com/help/4490016) (Version 9.22 des Mobilitätsdienstes).<br/> - Windows Server 2016 (64-bit Server Core, Server mit Desktopdarstellung)<br/> - Windows Server 2012 R2, Windows Server 2012<br/> - Windows Server 2008 R2 mit mindestens SP1.<br/> - Windows Server 2008, 64 und 32-bit mit mindestens SP2]. Nur für die Migration unterstützt. [Weitere Informationen](migrate-tutorial-windows-server-2008.md)<br/> - Windows 10, Windows 8.1, Windows 8, Windows 7 64-bit (unterstützt ab [Update Rollup 36](https://support.microsoft.com/help/4503156) (ab Vversion 9.22 des Mobilitätsdienstes). Windows 7 RTM wird nicht unterstützt. 
Linux | Es werden nur 64-Bit-Systeme unterstützt. 32-Bit-Systeme werden nicht unterstützt.<br/><br/>Auf allen Linux-Server sollten [Linux Integration Services (LIS)-Komponente](https://www.microsoft.com/download/details.aspx?id=55106) installiert sein. Sie benötigen sie, um den Server in Azure nach einem Testfailover/Failover zu booten. Wenn LIS-Komponente fehlen, dann stellen Sie sicher, dass Sie die [Komponenten](https://www.microsoft.com/download/details.aspx?id=55106) installieren, bevor Sie die Replikation der Computer so aktivieren, dass sie in Azure booten. <br/><br/> Site Recovery orchestriert Failover zum Ausführen von Linux-Servern in Azure. Linux-Anbieter sollten jedoch möglicherweise den Support auf Distributionsversionen einschränken, die noch nicht veraltet sind.<br/><br/> Bei Linux-Distributionen werden nur die vordefinierten Kernel, die bei Veröffentlichungen/Updates von Nebenversionen der Distribution enthalten sind, unterstützt.<br/><br/> Das Aktualisieren geschützter Computer über die wichtigsten Linux-Distributionsversionen hinweg wird nicht unterstützt. Um ein Upgrade auszuführen, deaktivieren Sie die Replikation, aktualisieren das Betriebssystem und aktivieren die Replikation erneut.<br/><br/> [Erfahren Sie mehr](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) über die Unterstützung von Linux und Open-Source-Technologie in Azure.
Linux Red Hat Enterprise | 5.2 bis 5.11</b><br/> 6.1 bis 6.10</b><br/> 7.0 bis 7.6<br/> <br/> Bei Servern mit Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 sind keine [Linux Integration Services (LIS)-Komponenten](https://www.microsoft.com/download/details.aspx?id=55106) vorinstalliert. Stellen Sie sicher, dass Sie die [Komponenten](https://www.microsoft.com/download/details.aspx?id=55106) installieren, bevor Sie die Replikation der Computer so aktivieren, dass sie in Azure booten.
Linux: CentOS | 5.2 bis 5.11</b><br/> 6.1 bis 6.10</b><br/> 7.0 bis 7.6<br/> <br/> Bei Servern mit CentOS 5.2-5.11 & 6.1-6.10 sind keine [Linux Integration Services (LIS)-Komponenten](https://www.microsoft.com/download/details.aspx?id=55106) vorinstalliert. Stellen Sie sicher, dass Sie die [Komponenten](https://www.microsoft.com/download/details.aspx?id=55106) installieren, bevor Sie die Replikation der Computer so aktivieren, dass sie in Azure booten.
Ubuntu | Ubuntu 14.04 LTS Server [(Überprüfen Sie die unterstützen Kernel-Versionen)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS Server [(Überprüfen Sie die unterstützen Kernel-Versionen)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(Überprüfen Sie die unterstützten Kernel-Versionen)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(Überprüfen Sie die unterstützten Kernelversionen)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3 oder SUSE Linux Enterprise Server 11 SP4<br/> Das Upgrade replizierter Computer von SUSE Linux Enterprise Server 11 SP3 auf SP4 wird nicht unterstützt. Um ein Upgrade auszuführen, deaktivieren Sie die Replikation, und aktivieren Sie sie nach dem Upgrade erneut.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6<br/><br/> Mit einem Red Hat-kompatibler Kernel oder Unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5) 


### <a name="ubuntu-kernel-versions"></a>Ubuntu-Kernelversionen


**Unterstütztes Release** | **Mobility Service-Version** | **Kernelversion** |
--- | --- | --- |
14.04 LTS | [9.27][9.27 UR]| 3.13.0-24-generic bis 3.13.0-170-generic,<br/>3.16.0-25-generic bis 3.16.0-77-generic,<br/>3.19.0-18-generic bis 3.19.0-80-generic,<br/>4.2.0-18-generic bis 4.2.0-42-generic,<br/>4.4.0-21-generic bis 4.4.0-148-generic,<br/>4.15.0-1023-azure bis 4.15.0-1045-azure |
14.04 LTS | [9.26][9.26 UR]| 3.13.0-24-generic bis 3.13.0-170-generic,<br/>3.16.0-25-generic bis 3.16.0-77-generic,<br/>3.19.0-18-generic bis 3.19.0-80-generic,<br/>4.2.0-18-generic bis 4.2.0-42-generic,<br/>4.4.0-21-generic bis 4.4.0-148-generic,<br/>4.15.0-1023-azure bis 4.15.0-1045-azure |
14.04 LTS | [9.25][9.25 UR]  | 3.13.0-24-generic bis 3.13.0-169-generic,<br/>3.16.0-25-generic bis 3.16.0-77-generic,<br/>3.19.0-18-generic bis 3.19.0-80-generic,<br/>4.2.0-18-generic bis 4.2.0-42-generic,<br/>4.4.0-21-generic bis 4.4.0-146-generic,<br/>4.15.0-1023-azure bis 4.15.0-1042-azure |
14.04 LTS | [9.24][9.24 UR] | 3.13.0-24-generic bis 3.13.0-167-generic,<br/>3.16.0-25-generic bis 3.16.0-77-generic,<br/>3.19.0-18-generic bis 3.19.0-80-generic,<br/>4.2.0-18-generic bis 4.2.0-42-generic,<br/>4.4.0-21-generic bis 4.4.0-143-generic,<br/>4.15.0-1023-azure bis 4.15.0-1040-azure |
|||
16.04 LTS | [9.27][9.27 UR] | 4.4.0-21-generic bis 4.4.0-154-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-54-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1050-azure|
16.04 LTS | [9.26][9.26 UR] | 4.4.0-21-generic bis 4.4.0-148-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-50-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1045-azure|
16.04 LTS | [9.25][9.25 UR] | 4.4.0-21-generic bis 4.4.0-146-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-48-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1042-azure|
16.04 LTS | [9.24][9.24 UR] | 4.4.0-21-generic bis 4.4.0-143-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-46-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1040-azure|

### <a name="debian-kernel-versions"></a>Debian-Kernelversionen


**Unterstütztes Release** | **Mobility Service-Version** | **Kernelversion** |
--- | --- | --- |
Debian 7 | [9.24][9.24 UR], [9.25][9.25 UR],[9.26][9.26 UR], [9.27][9.27 UR]| 3.2.0-4-amd64 bis 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.27][9.27 UR] | 3.16.0-4-amd64 bis 3.16.0-9-amd64, 4.9.0-0.bpo.4-amd64 bis 4.9.0-0.bpo.9-amd64 |
Debian 8 | [9.24][9.24 UR], [9.25][9.25 UR], [9.26][9.26 UR] | 3.16.0-4-amd64 bis 3.16.0-8-amd64, 4.9.0-0.bpo.4-amd64 bis 4.9.0-0.bpo.8-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 – unterstützte Kernelversionen

**Release** | **Mobility Service-Version** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.27][9.27 UR] | SP1 3.12.49-11-default bis 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default bis 3.12.74-60.64.115-default</br></br> SP2 4.4.21-69-default bis 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default bis 4.4.121-92.114-default</br></br>SP3 4.4.73-5-default bis 4.4.180-94.97-default</br></br>SP3 4.4.138-4.7-azure bis 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-default bis 4.12.14-95.19-default</br>SP4 4.12.14-6.3-azure bis 4.12.14-6.15-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.26][9.26 UR] | SP1 3.12.49-11-default bis 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default bis 3.12.74-60.64.110-default</br></br> SP2 4.4.21-69-default bis 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default bis 4.4.121-92.109-default</br></br>SP3 4.4.73-5-default bis 4.4.178-94.91-default</br></br>SP3 4.4.138-4.7-azure bis 4.4.178-4.28-azure</br></br>SP4 4.12.14-94.41-default bis 4.12.14-95.16-default</br>SP4 4.12.14-6.3-azure bis 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.25][9.25 UR] | SP1 3.12.49-11-default bis 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default bis 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default bis 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default bis 4.4.121-92.104-default</br></br>SP3 4.4.73-5-default bis 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-azure bis 4.4.176-4.25-azure</br></br>SP4 4.12.14-94.41-default bis 4.12.14-95.13-default</br>SP4 4.12.14-6.3-azure bis 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.24][9.24 UR] | SP1 3.12.49-11-default bis 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default bis 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default bis 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default bis 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default bis 4.4.175-94.79-default</br></br>SP4 4.12.14-94.41-default bis 4.12.14-95.6-default |


## <a name="linux-file-systemsguest-storage"></a>Linux-Dateisysteme/-Gastspeicher

**Komponente** | **Unterstützt**
--- | ---
Dateisysteme | ext3, ext4, XFS
Volume-Manager | - LVM wird unterstützt.<br/> - /boot auf LVM wird ab [Update Rollup 31](https://support.microsoft.com/help/4478871/) (Version 9.20 des Mobilitätsdienstes) unterstützt. Es wird in früheren Mobilitätsdienstversionen nicht unterstützt.<br/> - Mehrere Betriebssystemdatenträger werden nicht unterstützt.
Paravirtualisierte Speichergeräte | Von paravirtualisierten Treibern exportierte Geräte werden nicht unterstützt.
E/A-Geräte mit Blöcken mit mehreren Warteschlangen | Nicht unterstützt.
Physische Server mit HP CCISS-Speichercontroller | Nicht unterstützt.
Benennungskonvention für Gerät/Bereitstellungspunkt | Der Gerätename oder Bereitstellungspunktname sollte eindeutig sein.<br/> Stellen Sie sicher, dass keine zwei Geräte/Bereitstellungspunkte Namen aufweisen, für die zwischen Groß-/Kleinschreibung unterschieden wird. So ist es beispielsweise nicht zulässig, zwei Geräte für denselben virtuellen Computers als *device1* und *Device1* zu benennen.
Verzeichnisse | Wenn Sie eine Version des Mobilitätsdiensts verwenden, die vor der Version 9.20 liegt (veröffentlich im [Update Rollup 31](https://support.microsoft.com/help/4478871/)), dann gelten die folgenden Einschränkungen:<br/><br/> - Diese Verzeichnisse (sofern als separate Partitionen/Dateisysteme eingerichtet) müssen sich auf demselben Betriebssystem-Datenträger auf dem Quellserver befinden: /(root), /boot, /usr, /usr/local, /var, /etc.</br> 2\. Das /boot-Verzeichnis muss sich auf einer Datenträgerpartition und nicht auf einem LVM-Volume befinden.<br/><br/> Ab Version 9.20 gelten diese Einschränkungen nicht. 
Startverzeichnis | – Startdatenträger dürfen nicht im GPT-Partitionsformat vorliegen. Dies ist eine Einschränkung der Azure-Architektur. GPT-Datenträger werden als Datenträger für Daten unterstützt.<br/><br/> Mehrere Startdatenträger auf einem virtuellen Computer werden nicht unterstützt.<br/><br/> - „/boot“ auf LVM-Volumes über mehrere Datenträger wird nicht unterstützt.<br/> - Ein Computer ohne einen Startdatenträger kann nicht repliziert werden.
Erforderlicher Speicherbedarf| 2 GB auf der /root-Partition <br/><br/> 250 MB im Installationsordner
XFSv5 | XFSv5-Features auf XFS-Dateisystemen wie Metadaten-Prüfsummen werden unterstützt (ab Mobility Service-Version 9.10).<br/> Verwenden Sie das Hilfsprogramm „xfs_info“, um den XFS-Superblock für die Partition zu überprüfen. Wenn `ftype` auf 1 festgelegt ist, werden XFSv5-Features verwendet.
BTRFS | BTRFS wird ab [Update Rollup 34](https://support.microsoft.com/help/4490016) (Version 9.22 des Mobilitätsdienstes) unterstützt. BTRFS wird nicht unterstützt, wenn:<br/><br/> - Das untergeordnete Volume des BTRFS-Dateisystems nach dem Aktivieren des Schutzes geändert wird.</br> - Das BTRFS-Dateisystem über mehrere Datenträger verteilt wird.</br> – Die BTRFS-Dateisystem RAID unterstützt.

## <a name="vmdisk-management"></a>VM-/Datenträgerverwaltung

**Aktion** | **Details**
--- | ---
Größe des Datenträgers auf einer replizierten VM ändern | Unterstützt.
Datenträger auf einer replizierten VM hinzufügen | Nicht unterstützt.<br/> Deaktivieren Sie die Replikation für die VM, fügen Sie den Datenträger hinzu, und aktivieren Sie dann erneut die Replikation.

## <a name="network"></a>Netzwerk

**Komponente** | **Unterstützt**
--- | ---
NIC-Teaming im Hostnetzwerk | Unterstützt für VMware-VMs. <br/><br/>Nicht unterstützt für die Replikation physischer Computer
VLAN im Hostnetzwerk | Ja.
IPv4 im Hostnetzwerk | Ja.
IPv6 im Hostnetzwerk | Nein.
NIC-Teaming im Gast-/Servernetzwerk | Nein.
IPv4 im Gast-/Servernetzwerk | Ja.
IPv6 im Gast-/Servernetzwerk | Nein.
Statische IP im Gast-/Servernetzwerk (Windows) | Ja.
Statische IP im Gast-/Servernetzwerk (Linux) | Ja. <br/><br/>VMs werden für die Verwendung von DHCP bei Failback konfiguriert.
Mehrere NICs im Gast-/Servernetzwerk | Ja.


## <a name="azure-vm-network-after-failover"></a>Azure-VM-Netzwerk (nach Failover)

**Komponente** | **Unterstützt**
--- | ---
Azure ExpressRoute | Ja
ILB | Ja
ELB | Ja
Azure Traffic Manager | Ja
Multi-NIC | Ja
Reservierte IP-Adresse | Ja
IPv4 | Ja
Quell-IP-Adresse beibehalten | Ja
Azure-VNET-Dienstendpunkte<br/> | Ja
Beschleunigte Netzwerke | Nein

## <a name="storage"></a>Storage
**Komponente** | **Unterstützt**
--- | ---
Dynamischer Datenträger | Der Betriebssystem-Datenträger muss ein Basisdatenträger sein. <br/><br/>Datenträger für Daten können dynamische Datenträger sein.
Dockerdatenträgerkonfiguration | Nein
Host-NFS | Ja für VMware<br/><br/> Nein für physische Server
Host-SAN (iSCSI/FC) | Ja
Host-vSAN | Ja für VMware<br/><br/> Nicht verfügbar für physische Server
Multipfad auf dem Host (MPIO) | Ja, getestet mit: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM für CLARiiON
Virtuelle Hostvolumes (VVols) | Ja für VMware<br/><br/> Nicht verfügbar für physische Server
Gast-/Server-VMDK | Ja
Freigegebener Gast-/Server-Clusterdatenträger | Nein
Verschlüsselter Gast-/Serverdatenträger | Nein
Gast-/Server-NFS | Nein
Gast-/Server-iSCSI | Nein
Gast/Server-SMB 3.0 | Nein
Gast-/Server-RDM | Ja<br/><br/> Nicht verfügbar für physische Server
Gast-/Serverdatenträger > 1 TB | Ja, der Datenträger muss größer als 1024 MB sein.<br/><br/>Bis zu 8.192 GB bei der Replikation auf verwaltete Datenträger (ab Version 9.26)<br></br> Bis zu 4.095 GB bei der Replikation in Speicherkonten
Gast-/Serverdatenträger mit einer logischen Sektorgröße von 4K und einer physischen Sektorgröße von 4k | Nein
Gast-/Serverdatenträger mit einer logischen Sektorgröße von 4K und einer physischen Sektorgröße von 512 Bytes | Nein
Gast-/Servervolume mit Stripesetdatenträgern > 4 TB <br/><br/>Logische Volumeverwaltung (Logical Volume Management, LVM)| Ja
Gast/Server – Speicherplätze | Nein
Gast/Server – Datenträger bei laufendem Systembetrieb hinzufügen/entfernen | Nein
Gast/Server – Datenträger ausschließen | Ja
Gast-/Servermultipfad (MPIO) | Nein
Gast/Server GPT-Partitionen | Fünf Partitionen werden ab [Update Rollup 37](https://support.microsoft.com/help/4508614/) (Version 9.25 des Mobilitätsdienstes) unterstützt. Bisher wurden vier unterstützt.
EFI-/UEFI-Start von Gast/Server | - Wird unterstützt, wenn Sie die Mobilitätsdienstversion 9.13 oder neuer verwenden.<br/> - Unterstützt bei der Migration von virtuellen VMware-Computern oder physischen Servern unter Windows Server 2012 oder höher zu Azure.<br/> - Sie können nur virtuelle Computer für die Migration replizieren. Ein Failback zu lokalen Ressourcen wird nicht unterstützt.<br/> – Es wird nur NTFS unterstützt. <br/> – Sichere UEFI-Starttypen werden nicht unterstützt. <br/> - Datenträgersektorgröße sollte pro physischem Sektor 512 bytes betragen.

## <a name="replication-channels"></a>Replikationskanäle

|**Typ der Replikation**   |**Unterstützt**  |
|---------|---------|
|Ausgelagerte Datenübertragungen (ODX)    |       Nein  |
|Offlineseeding        |   Nein      |
| Azure Data Box | Nein

## <a name="azure-storage"></a>Azure-Speicher

**Komponente** | **Unterstützt**
--- | ---
Lokal redundanter Speicher | Ja
Georedundanter Speicher | Ja
Georedundanter Speicher mit Lesezugriff | Ja
Speicherebene „Kalt“ | Nein
Speicherebene „Heiß“| Nein
Blockblobs | Nein
Encryption-at-rest (SSE)| Ja
Storage Premium | Ja
Import-/Exportdienst | Nein
Azure Storage-Firewalls für VNets | Ja.<br/> Konfiguriert in Zielspeicher-/Cachespeicherkonto (zum Speichern von Replikationsdaten).
Allgemeine v2-Speicherkonten (heiße und kalte Ebene) | Ja (Transaktionskosten sind wesentlich höher für V2 als für V1)

## <a name="azure-compute"></a>Azure Compute

**Feature** | **Unterstützt**
--- | ---
Verfügbarkeitsgruppen | Ja
Verfügbarkeitszonen | Nein
HUB | Ja
Verwaltete Datenträger | Ja

## <a name="azure-vm-requirements"></a>Azure-VM-Anforderungen

Lokale virtuelle Computer, die in Azure repliziert werden, müssen die in dieser Tabelle zusammengefassten Azure-VM-Anforderungen erfüllen. Wenn Site Recovery eine Voraussetzungsprüfung für die Replikation durchführt, ist diese nicht erfolgreich, wenn einige der Anforderungen nicht erfüllt sind.

**Komponente** | **Anforderungen** | **Details**
--- | --- | ---
Gastbetriebssystem | Überprüfen Sie die [unterstützten Betriebssysteme](#replicated-machines) für replizierte Computer. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Architektur des Gastbetriebssystems | 64 Bit. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Größe des Betriebssystem-Datenträgers | Bis zu 2.048 GB. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Anzahl von Betriebssystem-Datenträgern | 1 | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Anzahl von Datenträgern für Daten | Maximal 64. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Datenträgergröße | Bis zu 8.192 GB bei der Replikation auf verwaltete Datenträger (ab Version 9.26)<br></br>Bis zu 4.095 GB bei der Replikation in Speicherkonten| Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Netzwerkadapter | Es werden mehrere Adapter unterstützt. |
Freigegebene VHD | Nicht unterstützt. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Fiber-Channel-Datenträger | Nicht unterstützt. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
BitLocker | Nicht unterstützt. | BitLocker muss deaktiviert sein, bevor Sie die Replikation für einen Computer aktivieren. |
Name des virtuellen Computers | 1 bis 63 Zeichen.<br/><br/> Ist auf Buchstaben, Zahlen und Bindestriche beschränkt.<br/><br/> Der Computername muss mit einem Buchstaben oder einer Ziffer beginnen und enden. |  Aktualisieren Sie den Wert in den Computereigenschaften in Site Recovery.

## <a name="churn-limits"></a>Änderungseinschränkungen

Die folgende Tabelle enthält die Azure Site Recovery-Grenzwerte. 
- Diese Grenzwerte basieren auf unseren Tests, können aber nicht alle möglichen E/A-Kombinationen für Anwendungen abdecken.
- Die tatsächlichen Ergebnisse können je nach Ihrer E/A-Mischung für die Anwendungen variieren.
- Um die bestmöglichen Ergebnisse zu erzielen, empfehlen wir dringend, das Sie das [Tool für die Bereitstellungsplanung](site-recovery-deployment-planner.md) ausführen und umfangreiche Anwendungstests per Testfailover durchführen, um sich ein eindeutiges Bild der Anwendungsleistung zu verschaffen.

**Replikation des Ziels** | **Durchschnittliche E/A-Größe des Quelldatenträgers** |**Durchschnittliche Datenänderungsrate des Quelldatenträgers** | **Gesamte Datenänderungsrate des Quelldatenträgers pro Tag**
---|---|---|---
Standardspeicher | 8 KB | 2 MB/s | 168 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 8 KB  | 2 MB/s | 168 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 16 KB | 4 MB/s |  336 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 32 KB oder höher | 8 MB/s | 672 GB pro Datenträger
Premium-Datenträger – P20, P30, P40 oder P50 | 8 KB    | 5 MB/s | 421 GB pro Datenträger
Premium-Datenträger – P20, P30, P40 oder P50 | 16 KB oder höher |20 MB/s | 1\.684 GB pro Datenträger


**Quell-Datenänderungsrate** | **Maximales Limit**
---|---
Durchschnittliche Datenänderungsrate pro VM| 25 MB/s
Spitzenänderungsrate für alle Datenträger auf einer VM | 54 MB/s
Maximale Datenänderung pro Tag, die von einem Prozessserver unterstützt wird | 2 TB

- Dies sind Durchschnittswerte, bei denen eine E/A-Überlappung von 30% angenommen wird.
- Site Recovery kann einen höheren Durchsatz basierend auf dem Überlappungsverhältnis, höheren Schreibgrößen und dem tatsächlichen Workload-E/A-Verhalten verarbeiten.
- Für diese Zahlen wurde ein typischer Backlog von ca. fünf Minuten vorausgesetzt. Dies bedeutet, dass die Daten nach dem Hochladen verarbeitet werden und innerhalb von fünf Minuten ein Wiederherstellungspunkt erstellt wird.

## <a name="vault-tasks"></a>Tresortasks

**Aktion** | **Unterstützt**
--- | ---
Tresor über Ressourcengruppen hinweg verschieben | Nein
Tresor innerhalb von und über Abonnements hinweg verschieben | Nein
Speicher, Netzwerk, Azure-VMs über Ressourcengruppen hinweg verschieben | Nein
Verschieben von Speicher, Netzwerk, Azure-VMs innerhalb oder zwischen Abonnements. | Nein


## <a name="obtain-latest-components"></a>Erhalten der neusten Komponenten

**Name** | **Beschreibung** | **Details**
--- | --- | ---
Konfigurationsserver | Lokal installiert.<br/> Koordiniert die Kommunikation zwischen lokalen VMware-Servern oder physischen Computern und Azure. | - [Erfahren Sie mehr über](vmware-physical-azure-config-process-server-overview.md) den Konfigurationsserver.<br/> - [Erfahren Sie mehr über](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) das Upgrade auf die neuste Version.<br/> - [Erfahren Sie mehr über](vmware-azure-deploy-configuration-server.md) die Einrichtung des Konfigurationsservers. 
Prozessserver | Wird standardmäßig auf dem Konfigurationsserver installiert.<br/> Empfängt Replikationsdaten, optimiert sie durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet sie an Azure.<br/> Bei zunehmender Größe der Bereitstellung können Sie zusätzliche Prozessserver hinzufügen, um größere Mengen von Replikationsdatenverkehr zu bewältigen. | - [Erfahren Sie mehr über](vmware-physical-azure-config-process-server-overview.md) den Prozessserver.<br/> - [Erfahren Sie mehr über](vmware-azure-manage-process-server.md#upgrade-a-process-server) das Upgrade auf die neuste Version.<br/> - [Erfahren Sie mehr über](vmware-physical-large-deployment.md#set-up-a-process-server) die Einrichtung des horizontal skalierten Prozessservers.
Mobility Service | Installiert auf einem virtuellen VMware-Computer oder auf physischen Servern, die Sie replizieren möchten.<br/> Koordiniert die Replikation zwischen lokalen VMware-Servern/physischen Servern und Azure.| - [Erfahren Sie mehr über](vmware-physical-mobility-service-overview.md) den Mobilitätsdienst.<br/> - [Erfahren Sie mehr über](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) das Upgrade auf die neuste Version.<br/> 



## <a name="next-steps"></a>Nächste Schritte
[Erfahren Sie](tutorial-prepare-azure.md), wie Sie Azure für die Notfallwiederherstellung von VMware-VMs vorbereiten.

[9.27 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
