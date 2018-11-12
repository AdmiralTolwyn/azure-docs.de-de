---
title: Einrichten eines Windows RDMA-Clusters zum Ausführen von MPI-Anwendungen | Microsoft Docs
description: Hier erfahren Sie, wie Sie einen Windows-basierten HPC Pack-Cluster mit virtuellen Computern der Größe H16r, H16mr, A8 oder A9 erstellen, um das Azure RDMA-Netzwerk zum Ausführen von MPI-Apps zu verwenden.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 7d9f5bc8-012f-48dd-b290-db81c7592215
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 03/06/2018
ms.author: danlep
ms.openlocfilehash: 52338cc21e46b544c2abb79cd7094615c837a2e8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233778"
---
# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Einrichten eines Windows RDMA-Clusters mit HPC Pack zum Ausführen von MPI-Anwendungen
Richten Sie anhand der Informationen unter [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) und [Größen von virtuellen HPC-Computern (High Performance Computing)](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances) (Abschnitt „RDMA-fähige Instanzen“) einen Windows RDMA-Cluster in Azure ein, um parallele MPI-Anwendungen (Message Passing Interface) auszuführen. Wenn Sie RDMA-fähige, Windows Server-basierte Knoten in einem HPC Pack-Cluster einrichten, wird die Kommunikation der MPI-Anwendungen in Azure effizient über ein auf RDMA-Technologie (Remote Direct Memory Access) basierendes Netzwerk mit niedriger Latenz und hohem Durchsatz abgewickelt.

## <a name="hpc-pack-cluster-deployment-options"></a>Optionen für die Bereitstellung von HPC Pack-Clustern
Microsoft HPC Pack ist ein kostenloses Tool zum Erstellen von HPC-Clustern (lokal oder in Azure) für die Ausführung Windows- oder Linux-basierter HPC-Anwendungen. HPC Pack enthält eine Laufzeitumgebung für die Microsoft-Implementierung von Message Passing Interface für Windows (MS-MPI). Bei Verwendung mit RDMA-fähigen Instanzen unter einem unterstützten Windows Server-Betriebssystem stellt HPC Pack eine effiziente Option für die Ausführung von Windows-MPI-Anwendungen dar, die auf das Azure RDMA-Netzwerk zugreifen. 

Dieser Artikel enthält zwei Szenarien sowie Links zu detaillierten Anleitungen zur Einrichtung eines Windows RDMA-Clusters mit Microsoft HPC Pack 2012 R2. 

* Szenario 1: Bereitstellen rechenintensiver Workerrolleninstanzen (PaaS)
* Szenario 2: Bereitstellen von Computeknoten in rechenintensiven virtuellen Computern (IaaS)

## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Szenario 1: Bereitstellen rechenintensiver Workerrolleninstanzen (PaaS)
Fügen Sie aus einem vorhandenen HPC Pack-Cluster zusätzliche Computeressourcen in Azure-Workerrolleninstanzen (Azure-Knoten) hinzu, die in einem Clouddienst (PaaS) ausgeführt werden. Diese Funktion wird auch als "Burst auf Azure" mit HPC Pack bezeichnet und unterstützt verschiedene Größen für die Workerrolleninstanzen. Geben Sie beim Hinzufügen der Azure-Knoten eine der RDMA-fähigen Größen an.

Im Anschluss finden Sie Überlegungen und Schritte zum Burst auf RDMA-fähige Azure-Instanzen über einen vorhandenen (üblicherweise lokalen) Cluster. Verwenden Sie ähnliche Verfahren, um einem HPC Pack-Hauptknoten, der in einer Azure-VM bereitgestellt wird, Workerrolleninstanzen hinzufügen.

> [!NOTE]
> Ein Tutorial zum Burst auf Azure mit HPC Pack finden Sie unter [Einrichten eines Hybridclusters mit HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Beachten Sie die Aspekte in den folgenden Schritten, die speziell für RDMA-fähige Azure-Knoten gelten.
> 
> 

![Burst auf Azure][burst]

### <a name="steps"></a>Schritte
1. **Bereitstellen und Konfigurieren eines HPC Pack 2012 R2-Hauptknotens**
   
    Laden Sie das HPC Pack-Installationspaket aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=49922) herunter. Voraussetzungen und Anleitungen für die Vorbereitung der Azure-Burstbereitstellung finden Sie unter [Burst to Azure with Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx) (Burst auf Azure-Workerinstanzen mit Microsoft HPC Pack).
2. **Konfigurieren eines Verwaltungszertifikats im Azure-Abonnement**
   
    Konfigurieren Sie ein Zertifikat, um die Verbindung zwischen dem Hauptknoten und Azure zu sichern. Optionen und Verfahren finden Sie unter [Szenarien zum Konfigurieren des Azure-Verwaltungszertifikats für HPC Pack](https://technet.microsoft.com/library/gg481759.aspx). Für Testbereitstellungen installiert HPC Pack ein Zertifikat mit dem Namen „Default Microsoft HPC Azure Management“, das Sie schnell in Ihr Azure-Abonnement hochladen können.
3. **Erstellen eines neuen Clouddiensts und eines Speicherkontos**
   
    Verwenden Sie das Azure-Portal, um einen Clouddienst (klassisch) und ein Speicherkonto (klassisch) für die Bereitstellung zu erstellen. Erstellen Sie diese Ressourcen in einer Region, in der die H-Serie bzw. die gewünschte Größe (A8 oder A9) verfügbar ist. Informationen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services/).

4. **Erstellen einer Azure-Knotenvorlage**
   
    Verwenden Sie den Assistenten zum Erstellen von Knotenvorlagen in HPC Cluster Manager. Die Schritte finden Sie unter [Erstellen einer Azure-Knotenvorlage](https://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) im Thema mit den Schritten zum Bereitstellen von Azure-Knoten mit Microsoft HPC Pack.
   
    Für Anfangstests wird empfohlen, eine manuelle Verfügbarkeitsrichtlinie in der Vorlage zu konfigurieren.
5. **Hinzufügen von Knoten zum Cluster**
   
    Verwenden Sie den Assistenten zum Hinzufügen von Knoten in HPC Cluster Manager. Weitere Informationen finden Sie unter [Hinzufügen von Azure-Knoten zum Windows HPC-Cluster](https://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).
   
    Wählen Sie beim Angeben der Knotengröße eine der RDMA-fähigen Instanzgrößen aus.
   
   > [!NOTE]
   > In jeder Burst-auf-Azure-Bereitstellung mit rechenintensiven Instanzen stellt HPC Pack zusätzlich zu den von Ihnen angegebenen Azure-Workerrolleninstanzen automatisch mindestens zwei RDMA-fähige Instanzen (beispielsweise A8) als Proxyknoten bereit. Die Proxyknoten verwenden Kerne, die dem Abonnement zugeordnet sind, und verursachen zusammen mit den Azure-Workerrolleninstanzen Gebühren.
   > 
   > 
6. **Starten (Bereitstellen) der Knoten und Onlineschalten der Knoten zum Ausführen von Aufträgen**
   
    Wählen Sie die Knoten aus, und verwenden Sie die Aktion **Starten** in HPC Cluster Manager. Wenn die Bereitstellung abgeschlossen ist, wählen Sie die Knoten aus, und verwenden Sie die Aktion **Online schalten** in HPC Cluster Manager. Die Knoten sind zum Ausführen von Aufträgen bereit.
7. **Übermitteln von Aufträgen an den Cluster**
   
   Verwenden Sie HPC Pack-Tools zur Auftragsübermittlung, um Clusteraufträge auszuführen. Weitere Informationen finden Sie unter [Microsoft HPC Pack: Auftragsverwaltung](https://technet.microsoft.com/library/jj899585.aspx).
8. **Beenden von Knoten (Aufheben der Bereitstellung)**
   
   Wenn Sie die Aufträge ausgeführt haben, schalten Sie die Knoten offline, und verwenden Sie die Aktion **Beenden** in HPC Cluster Manager.

## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Szenario 2: Bereitstellen von Computeknoten in rechenintensiven virtuellen Computern (IaaS)
In diesem Szenario stellen Sie den HPC Pack-Hauptknoten und Clustercomputeknoten auf virtuellen Computern in einem virtuellen Azure-Netzwerk bereit. HPC Pack bietet eine Reihe von [Bereitstellungsoptionen für virtuelle Azure-Computer](../../windows/hpcpack-cluster-options.md). Hierzu zählen unter anderem automatisierte Bereitstellungsskripts und Azure-Schnellstartvorlagen. In den folgenden Anweisungen erfahren Sie, wie Sie das [HPC Pack IaaS-Bereitstellungsskript](hpcpack-cluster-powershell-script.md) zum Automatisieren der Bereitstellung eines HPC Pack 2012 R2-Clusters in Azure nutzen.

![Cluster in Azure-VMs][iaas]

### <a name="steps"></a>Schritte
1. **Erstellen eines Clusterhauptknotens und von Computeknoten-VMs durch Ausführen des IaaS-Bereitstellungsskripts von HPC Pack auf einem Clientcomputer**
   
    Laden Sie das HPC Pack-Paket mit dem IaaS-Bereitstellungsskript aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949)herunter.
   
    Erstellen Sie zur Vorbereitung des Clientcomputers die Skriptkonfigurationsdatei, und führen Sie das Skript aus. Weitere Informationen finden Sie unter [Erstellen eines HPC-Clusters mit dem IaaS-Bereitstellungsskript von HPC Pack](hpcpack-cluster-powershell-script.md). 
   
    Überlegungen zum Bereitstellen von RDMA-fähigen Computeknoten finden Sie unter [Größen von virtuellen HPC-Computern (High Performance Computing)](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances). Beachten Sie Folgendes:
   
   * **Virtuelles Netzwerk:** Geben Sie ein neues virtuelles Netzwerk in einer Region an, in der die H-Serie bzw. die gewünschte Größe (A8 oder A9) verfügbar ist. Informationen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services/).

   * **Windows Server-Betriebssystem:** Geben Sie zur Unterstützung von RDMA-Verbindungen für die virtuellen Computer des Computeknotens ein kompatibles Windows Server-Betriebssystem wie Windows Server 2012 R2 an.
   * **Clouddienste:** Da das Skript das klassische Bereitstellungsmodell verwendet, werden die virtuellen Clustercomputer mithilfe von Azure-Clouddiensten bereitgestellt (`ServiceName`-Einstellungen in der Konfigurationsdatei). Es empfiehlt sich, den Hauptknoten in einem Clouddienst und die Computeknoten in einem anderen Clouddienst bereitzustellen. 
   * **Hauptknotengröße**: In diesem Szenario sollte für den Hauptknoten mindestens die Größe A4 (sehr groß) verwendet werden.
   * **HpcVmDrivers-Erweiterung**: Das Bereitstellungsskript installiert den Azure-VM-Agent und die Erweiterung „HpcVmDrivers“ automatisch bei der Bereitstellung von Computeknoten der Größe A8 oder A9 mit einem Windows Server-Betriebssystem. Mit HpcVmDrivers werden auf den Computeknoten-VMs Treiber installiert, damit eine Verbindung mit dem RDMA-Netzwerk hergestellt werden kann. Auf virtuellen Computer der RDMA-fähigen H-Serie müssen Sie die Erweiterung „HpcVmDrivers“ manuell installieren. Siehe [Größen von virtuellen HPC-Computern (High Performance Computing)](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   * **Netzwerkkonfiguration des Clusters**: Das Bereitstellungsskript richtet den HPC Pack-Cluster automatisch in Topologie 5 (alle Knoten im Unternehmensnetzwerk) ein. Diese Topologie ist für alle HPC Pack-Clusterbereitstellungen auf virtuellen Computern erforderlich. Ändern Sie die Topologie des Clusternetzwerks später nicht.
1. **Onlineschalten der Computeknoten zum Ausführen von Aufträgen**
   
    Wählen Sie die Knoten aus, und verwenden Sie die Aktion **Online schalten** in HPC Cluster Manager. Die Knoten sind zum Ausführen von Aufträgen bereit.
3. **Übermitteln von Aufträgen an den Cluster**
   
    Stellen Sie eine Verbindung mit dem Hauptknoten her, um Aufträge zu übermitteln, oder richten Sie dafür einen lokalen Computer ein. Weitere Informationen finden Sie unter [Übermitteln von Aufträgen an einen HPC-Cluster in Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
4. **Offlineschalten und Beenden der Knoten (Aufheben der Zuordnung)**
   
    Wenn Sie die Aufträge ausgeführt haben, schalten Sie die Knoten in HPC Cluster Manager offline. Verwenden Sie dann Azure-Verwaltungstools, um sie herunterzufahren.

## <a name="run-mpi-applications-on-the-cluster"></a>Ausführen von MPI-Anwendungen im Cluster
### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Beispiel: Ausführen von "mpipingpong" auf einem HPC Pack-Cluster
Führen Sie zum Überprüfen einer HPC Pack-Bereitstellung der RDMA-fähigen Instanzen den HPC Pack-Befehl **mpipingpong** im Cluster aus. **mpipingpong** werden wiederholt Datenpakete zwischen Knotenpaaren gesendet, um Latenz- und Durchsatzwerte sowie Statistiken für das RDMA-fähige Anwendungsnetzwerk zu berechnen. Dieses Beispiel zeigt ein typisches Muster für die Ausführung eines MPI-Auftrags (in diesem Fall **mpipingpong**) mit dem Clusterbefehl **mpiexec**.

In diesem Beispiel wird angenommen, dass Sie Azure Knoten in einer „Burst-auf-Azure“-Konfiguration hinzugefügt haben ([Szenario 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-\(PaaS\) in this article)). Wenn Sie HPC Pack auf einem Cluster von Azure-VMs bereitgestellt haben, müssen Sie die Befehlssyntax ändern, um eine andere Knotengruppe anzugeben, und zusätzliche Umgebungsvariablen festlegen, um Netzwerkdatenverkehr an das RDMA-Netzwerk weiterzuleiten.

So führen Sie "mpipingpong" auf dem Cluster aus

1. Öffnen Sie eine Eingabeaufforderung auf dem Hauptknoten oder auf einem ordnungsgemäß konfigurierten Clientcomputer.
2. Geben Sie zum Schätzen der Latenz zwischen Knotenpaaren in einer Azure-Burstbereitstellung mit vier Knoten den folgenden Befehl ein, um einen Auftrag zum Ausführen von „mpipingpong“ mit einer geringen Paketgröße und einer großen Anzahl von Iterationen zu senden:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```
   
    Der Befehl gibt die ID des Auftrags zurück, der gesendet wird.
   
    Wenn Sie den HPC Pack-Cluster auf Azure-VMs bereitgestellt haben, geben Sie eine Knotengruppe an, die Computeknoten-VMs enthält, die in einem Clouddienst bereitgestellt wurden, und ändern Sie den Befehl **mpiexec** wie folgt:
   
    ```Command
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```
3. Geben Sie nach Abschluss des Auftrags zum Anzeigen der Ausgabe (in diesem Fall die Ausgabe von Aufgabe 1 des Auftrags) Folgendes ein:
   
    ```Command
    task view <JobID>.1
    ```
   
    &lt;*JobID*&gt; ist dabei die ID des Auftrags, der gesendet wurde.
   
    Die Ausgabe enthält Latenzergebnisse wie die folgenden.
   
    ![Pingpong-Latenz][pingpong1]
4. Geben Sie zum Schätzen des Durchsatzes zwischen Paaren von Azure-Burstknoten den folgenden Befehl ein, um einen Auftrag zum Ausführen von **mpipingpong** mit einer großen Paketgröße und einer kleinen Anzahl von Iterationen zu senden:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```
   
    Der Befehl gibt die ID des Auftrags zurück, der gesendet wird.
   
    Ändern Sie auf einem HPC Pack-Cluster, der auf Azure-VMs bereitgestellt wurde, den Befehl wie in Schritt 2 beschrieben.
5. Geben Sie nach Abschluss des Auftrags zum Anzeigen der Ausgabe (in diesem Fall die Ausgabe von Aufgabe 1 des Auftrags) Folgendes ein:
   
    ```Command
    task view <JobID>.1
    ```
   
   Die Ausgabe enthält Durchsatzergebnisse wie die folgenden.
   
   ![Pingpong-Durchsatz][pingpong2]

### <a name="mpi-application-considerations"></a>Überlegungen zu MPI-Anwendungen
Im Anschluss finden Sie Aspekte, die beim Ausführen von MPI-Anwendungen mit HPC Pack in Azure berücksichtigt werden müssen. Einige gelten nur für Bereitstellungen von Azure-Knoten (Workerrolleninstanzen, die in einer "Burst-auf-Azure"-Konfiguration hinzugefügt wurden).

* Workerrolleninstanzen in einem Clouddienst werden in regelmäßigen Abständen ohne vorherige Ankündigung von Azure erneut bereitgestellt (beispielsweise zur Systemwartung oder beim Ausfall einer Instanz). Wenn eine Instanz erneut bereitgestellt wird, während sie einen MPI-Auftrag ausführt, gehen die Daten der Instanz verloren, und die Instanz kehrt in den Zustand zurück, der bei ihrer ursprünglichen Bereitstellung vorlag. Dies kann dazu führen, dass der MPI-Auftrag nicht erfolgreich ausgeführt wird. Je mehr Knoten Sie für einen einzelnen MPI-Auftrag verwenden, und je länger der Auftrag ausgeführt wird, umso wahrscheinlicher ist es, dass eine der Instanzen während der Ausführung eines Auftrags erneut bereitgestellt wird. Berücksichtigen Sie dies auch, wenn Sie einen einzelnen Knoten in der Bereitstellung als Dateiserver festlegen.
* Für die Ausführung von MPI-Aufträgen in Azure müssen Sie nicht unbedingt RDMA-fähige Instanzen verwenden. Sie können beliebige Instanzgrößen nutzen, die von HPC Pack unterstützt werden. Die RDMA-fähigen Instanzen werden jedoch für die Ausführung relativ umfangreicher MPI-Aufträge empfohlen, die stark von der Latenz und der Bandbreite des Netzwerks abhängen, das die Knoten miteinander verbindet. Wenn Sie zum Ausführen von latenz- und bandbreitenabhängigen MPI-Aufträgen andere Größen verwenden, empfehlen wir die Ausführung kleiner Aufträge, bei denen eine einzelne Aufgabe nur auf wenigen Knoten ausgeführt wird.
* Für Anwendungen, die auf Azure-Instanzen bereitgestellt werden, gelten die Lizenzbedingungen der Anwendung. Informieren Sie sich bei Verwendung kommerzieller Anwendungen beim jeweiligen Hersteller über lizenzbedingte oder anderweitige Einschränkungen für die Ausführung in der Cloud. Nicht alle Hersteller bieten ein nutzungsbasiertes Lizenzmodell an.
* Azure-Instanzen benötigen ein erweitertes Setup für den Zugriff auf lokale Knoten, Freigaben und Lizenzserver. Beispiel: Um den Azure-Knoten den Zugriff auf einen lokalen Lizenzserver zu ermöglichen, können Sie in Azure ein virtuelles Site-to-Site-Netzwerk konfigurieren.
* Zum Ausführen von MPI-Anwendungen auf Azure-Instanzen registrieren Sie jede MPI-Anwendung bei der Windows-Firewall auf den Instanzen, indem Sie den Befehl **hpcfwutil** ausführen. Dadurch kann die MPI-Kommunikation über einen Port erfolgen, der von der Firewall dynamisch zugewiesen wird.
  
  > [!NOTE]
  > Für Burst-auf-Azure-Bereitstellungen können Sie auch einen Befehl für eine Firewallausnahme konfigurieren, der automatisch auf allen neuen Azure-Knoten ausgeführt wird, die dem Cluster hinzugefügt werden. Nachdem Sie den Befehl **hpcfwutil** ausgeführt und sichergestellt haben, dass Ihre Anwendung funktioniert, fügen Sie den Befehl einem Startskript für die Azure-Knoten hinzu. Weitere Informationen finden Sie unter [Verwenden eines Startskripts für Azure-Knoten](https://technet.microsoft.com/library/jj899632.aspx).
  > 
  > 
* HPC Pack verwendet die Clusterumgebungsvariable CCP_MPI_NETMASK, um einen Bereich zulässiger Adressen für die MPI-Kommunikation anzugeben. Seit HPC Pack 2012 R2 wirkt sich die Clusterumgebungsvariable CCP_MPI_NETMASK nur auf die MPI-Kommunikation zwischen Clustercomputeknoten aus, die zu einer Domäne gehören (entweder lokal oder in Azure-VMs). Die Variable wird von Knoten ignoriert, die einer Burst-auf-Azure-Konfiguration hinzugefügt werden.
* MPI-Aufträge können nicht auf Azure-Instanzen ausgeführt werden, die in verschiedenen Clouddiensten bereitgestellt wurden (z. B. in Burst-auf-Azure-Bereitstellungen mit unterschiedlichen Knotenvorlagen oder auf Azure-VM-Computeknoten, die in mehreren Clouddiensten bereitgestellt wurden). Wenn Sie über mehrere Azure-Knotenbereitstellungen verfügen, die mit unterschiedlichen Knotenvorlagen gestartet werden, darf der MPI-Auftrag nur auf einem Satz von Azure-Knoten ausgeführt werden.
* Wenn Sie Azure-Knoten zum Cluster hinzufügen und online schalten, versucht der HPC-Auftragsplanungsdienst sofort, Aufträge auf den Knoten zu starten. Wenn nur ein Teil Ihrer Workload in Azure ausgeführt werden kann, stellen Sie sicher, dass Sie Auftragsvorlagen aktualisieren oder erstellen, um exakt zu definieren, welche Auftragstypen auf Azure ausgeführt werden können. Um beispielsweise sicherzustellen, dass mit einer Auftragsvorlage übermittelte Aufträge nur auf Azure-Knoten ausgeführt werden, fügen Sie der Auftragsvorlage die Knotengruppeneigenschaft hinzu, und wählen Sie "AzureNodes" als den erforderlichen Wert aus. Wenn Sie benutzerdefinierte Gruppen für die Azure-Knoten erstellen, verwenden Sie das HPC PowerShell-Cmdlet Add-HpcGroup.

## <a name="next-steps"></a>Nächste Schritte
* Verwenden Sie bei der Entwicklung als Alternative zur Verwendung von HPC Pack den Azure Batch-Dienst, um MPI-Anwendung in verwalteten Pools von Computeknoten in Azure auszuführen. Entsprechende Informationen finden Sie unter [Verwendung von Tasks mit mehreren Instanzen zum Ausführen von MPI-Anwendungen (Message Passing Interface) in Azure Batch](../../../batch/batch-mpi.md).

<!--Image references-->
[burst]:media/hpcpack-rdma-cluster/burst.png
[iaas]:media/hpcpack-rdma-cluster/iaas.png
[pingpong1]:media/hpcpack-rdma-cluster/pingpong1.png
[pingpong2]:media/hpcpack-rdma-cluster/pingpong2.png
