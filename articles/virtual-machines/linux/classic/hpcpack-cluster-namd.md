---
title: NAMD mit Microsoft HPC Pack auf virtuellen Linux-Computern | Microsoft Docs
description: Sie stellen einen Microsoft HPC Pack-Cluster in Azure bereit und führen eine NAMD-Simulation mit „charmrun“ auf mehreren Linux-Computeknoten aus.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 76072c6b-ac35-4729-ba67-0d16f9443bd7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/13/2016
ms.author: danlep
ms.openlocfilehash: 61dd49d4bd3183b6b9a78036d6d7d01798e4dc89
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/05/2018
ms.locfileid: "30842012"
---
# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Ausführen von NAMD mit dem Microsoft HPC Pack auf Linux-Computeknoten in Azure
In diesem Artikel wird eine Möglichkeit zum Ausführen einer Linux-HPC-Workload (High-Performance Computing) auf virtuellen Azure-Computern beschrieben. Hier richten Sie einen [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029)-Cluster in Azure mit Linux-Computeknoten ein und führen eine [NAMD](http://www.ks.uiuc.edu/Research/namd/)-Simulation aus, um die Struktur eines großen Biomolekularsystems zu visualisieren.  

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD** (Nanoscale Molecular Dynamics Program) ist ein paralleles Molecular Dynamics-Paket für die Simulation großer Biomolekularsysteme mit Millionen von Atomen. Beispiele für diese Systeme sind Viren, Zellstrukturen und große Proteine. NAMD skaliert für normale Simulationen auf Hunderte Kerne und für die größten Simulationen auf mehr als 500.000 Kerne.
* **Microsoft HPC Pack** verfügt über Funktionen zum Ausführen von umfangreichen HPC- und parallelen Anwendungen in Clustern mit lokalen Computern oder virtuellen Azure-Computern. HPC Pack wurde ursprünglich als Lösung für Windows HPC-Workloads entwickelt und unterstützt jetzt die Ausführung von Linux HPC-Anwendungen auf Linux-Computeknoten-VMs, die in einem HPC Pack-Cluster bereitgestellt werden. Eine Einführung finden Sie unter [Erste Schritte mit Linux-Serverknoten in einem HPC Pack-Cluster in Azure](hpcpack-cluster.md) .

## <a name="prerequisites"></a>Voraussetzungen
* **HPC Pack-Cluster mit Linux-Computeknoten**: Stellen Sie einen HPC Pack-Cluster mit Linux-Computeknoten in Azure entweder mit einer [Azure Resource Manager-Vorlage](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) oder einem [Azure PowerShell-Skript](hpcpack-cluster-powershell-script.md) bereit. Voraussetzungen und Schritte für beide Optionen finden Sie unter [Erste Schritte mit Linux-Computeknoten in einem HPC Pack-Cluster in Azure](hpcpack-cluster.md) . Wenn Sie die Bereitstellungsoption per PowerShell-Skript wählen, hilft Ihnen die Beispielkonfigurationsdatei weiter, die im Rahmen der Beispieldateien am Ende dieses Artikels aufgeführt ist. Mit dieser Datei wird ein Azure-basierter HPC Pack-Cluster konfiguriert, der aus einem Windows Server 2012 R2-Hauptknoten und vier CentOS 6.6-Computeknoten der Größe „Large“ besteht. Passen Sie diese Datei je nach Bedarf für Ihre Umgebung an.
* **NAMD-Software und Lernprogrammdateien** : Sie können die NAMD-Software für Linux von der [NAMD](http://www.ks.uiuc.edu/Research/namd/) -Website herunterladen (Registrierung erforderlich). Dieser Artikel basiert auf der NAMD-Version 2.10, und es wird das Archiv [Linux-x86_64 (64 Bit Intel/AMD mit Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310) verwendet. Laden Sie auch die [NAMD-Lernprogrammdateien](http://www.ks.uiuc.edu/Training/Tutorials/#namd)herunter. Bei den Downloads handelt es sich um TAR-Dateien, und Sie benötigen ein Windows-Tool, um die Dateien auf dem Hauptknoten des Clusters zu extrahieren. Führen Sie zum Extrahieren der Dateien die Anleitung weiter unten in diesem Artikel aus. 
* **VMD** (optional) – zum Anzeigen der Ergebnisse des NAMD-Auftrags laden Sie das Programm zur Molekularvisualisierung, [VMD](http://www.ks.uiuc.edu/Research/vmd/) , auf einen Computer Ihrer Wahl herunter und installieren es. Die aktuelle Version ist 1.9.2. Informationen zu den ersten Schritten finden Sie auf der Downloadwebsite von VMD.  

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Einrichten der gegenseitigen Vertrauensstellung zwischen Computeknoten
Für das Ausführen eines knotenübergreifenden Auftrags auf mehreren Linux-Knoten müssen die Knoten sich gegenseitig vertrauen (per **rsh** oder **ssh**). Wenn Sie den HPC Pack-Cluster mit dem IaaS-Bereitstellungsskript für Microsoft HPC Pack erstellen, richtet das Skript automatisch dauerhafte gegenseitige Vertrauensstellungen für das angegebene Administratorkonto ein. Für Benutzer ohne Administratorrechte, die Sie in der Domäne des Clusters erstellen, müssen Sie temporäre gegenseitige Vertrauensstellungen zwischen den Knoten einrichten, wenn diesen ein Auftrag zugewiesen wird. Entfernen Sie die Beziehung nach Abschluss des Auftrags. Wenn Sie dies für alle Benutzer durchführen möchten, geben Sie ein RSA-Schlüsselpaar für den Cluster an, den HPC Pack zum Einrichten der Vertrauensstellung verwendet. Befolgen Sie die Anweisungen.

### <a name="generate-an-rsa-key-pair"></a>Generieren eines RSA-Schlüsselpaars
Das Generieren eines RSA-Schlüsselpaars, das einen öffentlichen Schlüssel und einen privaten Schlüssel enthält, ist mit dem Linux-Befehl **ssh-keygen** sehr einfach.

1. Melden Sie sich auf einem Linux-Computer an.
2. Führen Sie den folgenden Befehl aus:
   
   ```bash
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > Drücken Sie die **EINGABETASTE**, um die Standardeinstellungen zu verwenden, bis der Befehl abgeschlossen ist. Geben Sie hier keine Passphrase ein. Wenn Sie zur Eingabe eines Kennworts aufgefordert werden, drücken Sie einfach die **EINGABETASTE**.
   > 
   > 
   
   ![Generieren eines RSA-Schlüsselpaars][keygen]
3. Wechseln Sie in das Verzeichnis "~/.ssh". Der private Schlüssel wird in "id_rsa" gespeichert und der öffentliche Schlüssel in "id_rsa.pub".
   
   ![Private und öffentliche Schlüssel][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Hinzufügen des Schlüsselpaars zum HPC Pack-Cluster
1. [Stellen Sie über Remotedesktop eine Verbindung mit der Hauptknoten-VM her](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Verwenden Sie hierzu die Domänenanmeldeinformationen, die Sie bei der Bereitstellung des Clusters angegeben haben (z.B. „hpc\clusteradmin“). Sie verwalten den Cluster über den Hauptknoten.
2. Verwenden Sie Windows Server-Standardverfahren zum Erstellen eines Domänenbenutzerkontos in der Active Directory-Domäne des Clusters. Verwenden Sie z. B. das Tool Active Directory-Benutzer und -Computer auf dem Hauptknoten. In den Beispielen in diesem Artikel wird davon ausgegangen, dass Sie einen Domänenbenutzer mit dem Namen „hpcuser“ in der Domäne „hpclab“ („hpclab\hpcuser“) erstellen.
3. Fügen Sie den Domänenbenutzer dem HPC Pack-Cluster als Clusterbenutzer hinzu. Anweisungen finden Sie unter [Add or Remove Cluster Users](https://technet.microsoft.com/library/ff919330.aspx) (Hinzufügen oder Entfernen von Clusterbenutzern).
4. Erstellen Sie die Datei "C:\cred.xml", und kopieren Sie die RSA-Schlüsseldaten in diese Datei. Ein Beispiel finden Sie in den Beispieldateien am Ende dieses Artikels.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
5. Öffnen Sie eine Eingabeaufforderung, und geben Sie den folgenden Befehl ein, um die Anmeldeinformationen für das Konto „hpclab\hpcuser“ festzulegen. Verwenden Sie den **extendeddata**-Parameter, um den Namen der für die Schlüsseldaten erstellten Datei „C:\cred.xml“ zu übergeben.
   
   ```command
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   Dieser Befehl wird ohne Ausgabe abgeschlossen. Speichern Sie die Datei "cred.xml" nach dem Festlegen der Anmeldeinformationen für die zum Ausführen der Aufträge erforderlichen Benutzerkonten an einem sicheren Ort, oder löschen Sie sie.
6. Wenn Sie das RSA-Schlüsselpaar auf einem der Linux-Knoten generiert haben, sollten Sie die Schlüssel nach der Verwendung löschen. HPC Pack richtet kein gegenseitiges Vertrauen ein, wenn eine vorhandene Datei "id_rsa" oder "id_rsa.pub" gefunden wird.

> [!IMPORTANT]
> Es wird nicht empfohlen, Linux-Aufträge als Clusteradministrator auf einem freigegebenen Cluster auszuführen, da ein von einem Administrator übermittelter Auftrag auf den Linux-Knoten unter dem root-Konto ausgeführt wird. Ein Auftrag, der von einem Benutzer ohne Administratorrechte übermittelt wurde, wird unter einem lokalen Linux-Benutzerkonto ausgeführt, das den gleichen Namen wie der Auftragsbenutzer hat. HPC Pack richtet dann das gegenseitige Vertrauen für diesen Linux-Benutzer auf allen Knoten ein, die dem Auftrag zugewiesen sind. Der Linux-Benutzer kann vor der Ausführung des Auftrags manuell auf den Linux-Knoten eingerichtet werden, oder HPC Pack erstellt den Benutzer automatisch, wenn der Auftrag übermittelt wird. Wenn der Benutzer von HPC Pack erstellt wurde, löscht HPC Pack ihn nach Abschluss des Auftrags wieder. Die Schlüssel werden nach Auftragsabschluss auf den Knoten gelöscht, um Sicherheitsrisiken zu verringern.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Einrichten einer Dateifreigabe für Linux-Knoten
Richten Sie nun eine SMB-Standardfreigabe ein, und stellen Sie den freigegebenen Ordner für alle Linux-Knoten bereit, damit die Linux-Knoten auf NAMD-Dateien mit einem gemeinsamen Pfad zugreifen können. Im Folgenden sind die Schritte zum Bereitstellen eines freigegebenen Ordners auf dem Hauptknoten angegeben. Eine Freigabe wird für Distributionen wie beispielsweise CentOS 6.6 empfohlen, die den Azure-Dateidienst derzeit nicht unterstützen. Wenn Ihre Linux-Knoten eine Azure-Dateifreigabe unterstützen, helfen Ihnen die Informationen unter [Verwenden von Azure File Storage unter Linux](../../../storage/files/storage-how-to-use-files-linux.md) weiter. Weitere Dateifreigabeoptionen für HPC Pack finden Sie unter [Erste Schritte mit Linux-Computeknoten in einem HPC Pack-Cluster in Azure](hpcpack-cluster.md).

1. Erstellen Sie auf dem Hauptknoten einen Ordner, und geben Sie ihn für alle Benutzer frei, indem Sie Lese-/Schreibberechtigungen festlegen. In diesem Beispiel ist „\\\\CentOS66HN\Namd“ der Name des Ordners, wobei „CentOS66HN“ der Hostname des Hauptknotens ist.
2. Erstellen Sie einen Unterordner namens „namd2“ im freigegebenen Ordner. Erstellen Sie in „namd2“ einen weiteren Unterordner mit dem Namen „namdsample“.
3. Extrahieren Sie die NAMD-Dateien in dem Ordner. Verwenden Sie dazu eine Windows-Version von **tar** oder ein anderes Windows-Dienstprogramm, das TAR-Archive verarbeiten kann. 
   
   * Extrahieren Sie das NAMD-TAR-Archiv in „\\\\CentOS66HN\Namd\namd2“.
   * Extrahieren Sie die Dateien des Tutorials unter „\\\\CentOS66HN\Namd\namd2\namdsample“.
4. Öffnen Sie ein Windows PowerShell-Fenster, und führen Sie die folgenden Befehle aus, um den freigegebenen Ordner auf dem Linux-Knoten bereitzustellen.
   
    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Der erste Befehl erstellt einen Ordner namens "/namd2" auf allen Knoten in der Gruppe "LinuxNodes". Der zweite Befehl stellt den freigegebenen Ordner "//CentOS66HN/Namd/namd2" im Ordner bereit. Die Bits "dir_mode" und "file_mode" werden dabei auf 777 festgelegt. Die Parameter *username* und *password* im Befehl sollten mit den Anmeldeinformationen eines Benutzers auf dem Hauptknoten übereinstimmen.

> [!NOTE]
> Das Symbol „\`“ im zweiten Befehl ist ein Escapesymbol für PowerShell. „\`,“ bedeutet, dass das „,“ (Komma) ein Teil des Befehls ist.
> 
> 

## <a name="create-a-bash-script-to-run-a-namd-job"></a>Erstellen eines Bash-Skripts zum Ausführen eines NAMD-Auftrags
Der NAMD-Auftrag benötigt eine *nodelist* -Datei für **charmrun** , um die Anzahl von Knoten zu ermitteln, die beim Starten von NAMD-Prozessen verwendet werden. Sie verwenden ein Bash-Skript, das die nodelist-Datei generiert und **charmrun** mit dieser nodelist-Datei ausführt. Sie können dann einen NAMD-Auftrag in HPC-Cluster-Manager übermitteln, der dieses Skript aufruft.

Erstellen Sie mit einem Text-Editor Ihrer Wahl ein Bash-Skript im Ordner „/namd2“ mit den NAMD-Programmdateien, und geben Sie ihm den Namen „hpccharmrun.sh“. Sie können eine kurze Machbarkeitsstudie erstellen, indem Sie das Beispielskript „hpccharmrun.sh“ kopieren, das am Ende dieses Artikels angegeben ist, und zu [Übermitteln eines NAMD-Auftrags](#submit-a-namd-job) wechseln.

> [!TIP]
> Speichern Sie das Skript als Textdatei mit Linux-Zeilenenden (nur LF, nicht CR-LF). Dadurch wird sichergestellt, dass es auf den Linux-Knoten ordnungsgemäß ausgeführt wird.
> 
> 

Es folgen Details dazu, was mit diesem Bash-Skript durchgeführt wird. 

1. Definieren Sie einige Variablen.
   
   ```bash
   #!/bin/bash
   
   # The path of this script
   SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
   # Charmrun command
   CHARMRUN=${SCRIPT_PATH}/charmrun
   # Argument of ++nodelist
   NODELIST_OPT="++nodelist"
   # Argument of ++p
   NUMPROCESS="+p"
   ```
2. Rufen Sie Knoteninformationen aus den Umgebungsvariablen ab. Unter $NODESCORES ist eine Liste mit Teilungswörtern aus $CCP_NODES_CORES gespeichert. $COUNT ist die Größe von $NODESCORES.
   
   ```
   # Get node information from the environment variables
   NODESCORES=(${CCP_NODES_CORES})
   COUNT=${#NODESCORES[@]}
   ```    
   
   Das Format für die Variable "$CCP_NODES_CORES" lautet wie folgt:
   
   ```
   <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
   ```
   
   Unter dieser Variablen werden die Gesamtzahl der Knoten, die Knotennamen und die Anzahl der Kerne auf jedem Knoten aufgelistet, die dem Auftrag zugeordnet sind. Wenn der Auftrag z.B. 10 Kerne zum Ausführen benötigt, lautet der Wert von „$CCP_NODES_CORES“ etwa wie folgt:
   
   ```
   3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
   ```
3. Wenn die Variable $CCP_NODES_CORES nicht festgelegt wurde, starten Sie **charmrun** direkt. (Dies sollte nur auftreten, wenn Sie dieses Skript direkt auf den Linux-Knoten ausführen.)
   
   ```
   if [ ${COUNT} -eq 0 ]
   then
     # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
     #echo ${CHARMRUN} $*
     ${CHARMRUN} $*
   ```
4. Sie können auch eine nodelist-Datei für **charmrun**erstellen.
   
   ```
   else
     # Create the nodelist file
     NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$
   
     # Write the head line
     echo "group main" > ${NODELIST_PATH}
   
     # Get every node name and number of cores and write into the nodelist file
     I=1
     while [ ${I} -lt ${COUNT} ]
     do
         echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
         let "I=${I}+2"
     done
   ```
5. Führen Sie **charmrun** mit der nodelist-Datei aus, erfassen Sie den Rückgabestatus, und entfernen Sie abschließend die nodelist-Datei.
   
   ${CCP_NUMCPUS} ist eine andere Umgebungsvariable, die vom HPC Pack-Hauptknoten festgelegt wird. Sie speichert die Gesamtanzahl der Kerne, die diesem Auftrag zugeordnet sind. Wir verwenden diese Anzahl für die Angabe der Anzahl von Prozessen für "charmrun".
   
   ```
   # Run charmrun with nodelist arg
   #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   
   RTNSTS=$?
   rm -f ${NODELIST_PATH}
   fi
   
   ```
6. Beenden Sie den Vorgang mit dem Rückgabenstatus von **charmrun** .
   
   ```
   exit ${RTNSTS}
   ```

Im Folgenden finden Sie die Informationen in der nodelist-Datei, die vom Skript generiert wird:

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Beispiel: 

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>Übermitteln eines NAMD-Auftrags
Jetzt können Sie einen NAMD-Auftrag in HPC-Cluster-Manager übermitteln.

1. Stellen Sie eine Verbindung mit dem Hauptknoten des Clusters her, und starten Sie HPC-Cluster-Manager.
2. Vergewissern Sie sich unter **Ressourcenverwaltung**, dass die Linux-Serverknoten den Status **Online** aufweisen. Ist dies nicht der Fall, wählen Sie sie aus, und klicken Sie auf **Online schalten**.
3. Klicken Sie in **Job Management** auf **Neuer Auftrag**.
4. Geben Sie einen Namen für den Auftrag ein, z.B. *hpccharmrun*.
   
   ![Neuer HPC-Auftrag][namd_job]
5. Wählen Sie auf der Seite **Auftragsdetails** unter **Job Resources** den Ressourcentyp **Knoten** aus, und legen Sie **Minimum** auf „3“ fest. Wir führen den Auftrag auf drei Linux-Knoten aus, und jeder Knoten hat vier Kerne.
   
   ![Auftragsressourcen][job_resources]
6. Klicken Sie im linken Navigationsbereich auf **Aufgaben bearbeiten**, und klicken Sie dann zum Hinzufügen einer Aufgabe zum Auftrag auf **Hinzufügen**.    
7. Legen Sie auf der Seite **Aufgabendetails und E/A-Umleitung** die folgenden Werte fest.
   
   * **Befehlszeile** -
     `/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`
     
     > [!TIP]
     > Die obige Befehlszeile ist ein einzelner Befehl ohne Zeilenumbrüche. Unter **Befehlszeile** wird sie umgebrochen und in mehreren Zeilen angezeigt.
     > 
     > 
   * **Arbeitsverzeichnis** – /namd2
   * **Minimum** – 3
     
     ![Taskdetails][task_details]
     
     > [!NOTE]
     > Sie legen das Arbeitsverzeichnis hier fest, da **charmrun** versucht, auf jedem Knoten zum gleichen Arbeitsverzeichnis zu navigieren. Wenn das Arbeitsverzeichnis nicht festgelegt ist, startet HPC Pack den Befehl in einem zufällig benannten Ordner, der auf einem der Linux-Knoten erstellt wurde. Dadurch kann der folgende Fehler auf den anderen Knoten auftreten: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` Um dieses Problem zu vermeiden, geben Sie einen Ordnerpfad an, auf den von allen Knoten als Arbeitsverzeichnis zugegriffen werden kann.
     > 
     > 
8. Klicken Sie auf **OK** und dann auf **Senden**, um diesen Auftrag auszuführen.
   
   Standardmäßig sendet HPC Pack den Auftrag unter dem Konto des aktuell angemeldeten Benutzers. Sie werden nach dem Klicken auf **Senden**möglicherweise in einem Dialogfeld aufgefordert, den Benutzernamen und das Kennwort einzugeben.
   
   ![Anmeldeinformationen][creds]
   
   Unter bestimmten Umständen speichert HPC Pack von Ihnen zuvor eingegebene Benutzerinformationen, sodass dieses Dialogfeld nicht angezeigt wird. Damit HPC Pack es wieder anzeigt, geben Sie den folgenden Befehl an einer Eingabeaufforderung ein und senden dann den Auftrag.
   
   ```command
   hpccred delcreds
   ```
9. Der Auftrag nimmt mehrere Minuten in Anspruch.
10. Sie finden das Auftragsprotokoll in \\<headnodeName>\Namd\namd2\namd2_hpccharmrun.log und die Ausgabedateien in \\<headnodeName>\Namd\namd2\namdsample\1-2-sphere\.
11. Starten Sie gegebenenfalls VMD, um die Auftragsergebnisse anzuzeigen. Die Schritte zur Visualisierung der NAMD-Ausgabedateien (in diesem Fall ein Molekül des Proteins Ubiquitin in einer Wasserkugel) gehen über den Rahmen dieses Artikels hinaus. Weitere Einzelheiten finden Sie im [NAMD-Lernprogramm](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) .
    
    ![Auftragsergebnisse][vmd_view]

## <a name="sample-files"></a>Beispieldateien
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>XML-Beispielkonfigurationsdatei für die Clusterbereitstellung mit einem PowerShell-Skript
```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>    
```

### <a name="sample-credxml-file"></a>Beispieldatei "cred.xml"
```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```

### <a name="sample-hpccharmrunsh-script"></a>Beispielskript für "hpccharmrun.sh"
```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 

<!--Image references-->
[keygen]:media/hpcpack-cluster-namd/keygen.png
[keys]:media/hpcpack-cluster-namd/keys.png
[namd_job]:media/hpcpack-cluster-namd/namd_job.png
[job_resources]:media/hpcpack-cluster-namd/job_resources.png
[creds]:media/hpcpack-cluster-namd/creds.png
[task_details]:media/hpcpack-cluster-namd/task_details.png
[vmd_view]:media/hpcpack-cluster-namd/vmd_view.png
