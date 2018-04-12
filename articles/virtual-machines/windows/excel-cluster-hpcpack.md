---
title: HPC Pack-Cluster für Excel und SOA | Microsoft Docs
description: Erste Schritte mit umfangreichen Excel- und SOA-Workloads in einem HPC Pack-Cluster in Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,hpc-pack
ms.assetid: cb6a9abe-caf3-44da-b911-849a50f6cfb3
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.openlocfilehash: aaf26e04fdb38fd76f4ab8211f9fdda8ebafd668
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2018
---
# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Erste Schritte mit Excel- und SOA-Workloads in einem HPC Pack-Cluster in Azure
In diesem Artikel erfahren Sie, wie Sie einen Microsoft HPC Pack 2012 R2-Cluster mithilfe einer Azure-Schnellstartvorlage oder optional eines Azure PowerShell-Bereitstellungsskripts für Azure-VMs bereitstellen. Der Cluster verwendet VM-Images aus Azure Marketplace, die für die Ausführung von Microsoft Excel- oder SOA-Workloads mit HPC Pack konzipiert sind. Mit dem Cluster können Sie Excel-HPC- und SOA-Dienste über einen lokalen Clientcomputer ausführen. Zu den Excel-HPC-Diensten zählen die Abladung von Excel-Arbeitsmappen sowie benutzerdefinierte Funktionen (User-Defined Functions, UDFs) von Excel.

> [!IMPORTANT] 
> Dieser Artikel bezieht sich auf Features, Vorlagen und Skripts für HPC Pack 2012 R2. Dieses Szenario wird derzeit in HPC Pack 2016 nicht unterstützt.
>

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Das folgende Diagramm gibt einen allgemeinen Überblick über den zu erstellenden HPC Pack-Cluster.

![HPC-Cluster mit Knoten, auf denen Excel-Workloads ausgeführt werden][scenario]

## <a name="prerequisites"></a>Voraussetzungen
* **Clientcomputer** : Sie benötigen einen Windows-basierten Clientcomputer, um Excel- und SOA-Beispielaufträge an den Cluster zu übermitteln. Außerdem benötigen Sie einen Windows-Computer, um die Clusterbereitstellungsskripts von Azure PowerShell auszuführen (wenn Sie diese Bereitstellungsmethode auswählen).
* **Azure-Abonnement** – Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie in wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) einrichten.
* **Kernkontingent**: Unter Umständen muss das Kontingent für die Kerne erhöht werden. Dies gilt insbesondere, wenn Sie mehrere Clusterknoten mit Multicore-VM-Größen bereitstellen. Wenn Sie eine Azure-Schnellstartvorlage verwenden, gilt das Kernekontingent im Resource Manager pro Azure-Region. In diesem Fall müssen Sie möglicherweise das Kontingent in einer bestimmten Region erhöhen. Weitere Informationen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../../azure-subscription-service-limits.md). Um ein Kontingent zu erhöhen, können Sie kostenlos [eine Anfrage an den Onlinekundensupport richten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) .
* **Microsoft Office-Lizenz**: Wenn Sie Computeknoten mit einem Marketplace HPC Pack 2012 R2-VM-Image mit Microsoft Excel bereitstellen, wird eine 30-Tage-Evaluierungsversion von Microsoft Excel Professional Plus 2013 installiert. Nach Ablauf des Evaluierungszeitraums müssen Sie eine gültige Lizenz für Microsoft Office bereitstellen, um Excel zu aktivieren, damit Sie weiterhin Workloads ausführen können. Weitere Informationen finden Sie unter [Excel-Aktivierung](#excel-activation) weiter unten in diesem Artikel. 

## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Schritt 1: Einrichten eines HPC Pack-Clusters in Azure
Wir zeigen Ihnen zwei Optionen für die Einrichtung des HPC Pack 2012 R2-Clusters: Bei der ersten kommen eine Azure-Schnellstartvorlage und das Azure-Portal zum Einsatz, bei der zweiten wird ein Azure PowerShell-Bereitstellungsskript verwendet.

### <a name="option-1-use-a-quickstart-template"></a>Option 1. Mit einer Schnellstartvorlage
Verwenden Sie eine Azure-Schnellstartvorlage, um schnell einen HPC Pack-Cluster im Azure-Portal bereitzustellen. Wenn Sie die Vorlage im Portal öffnen, erscheint eine einfache Benutzeroberfläche, über die Sie die Einstellungen für Ihren Cluster angeben können. Gehen Sie wie folgt vor: 

> [!TIP]
> Verwenden Sie ggf. eine [Azure Marketplace-Vorlage](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn), die einen ähnlichen Cluster speziell für Excel-Workloads erstellt. Die Schritte unterscheiden sich geringfügig von den folgenden.
> 
> 

1. Besuchen Sie bei GitHub die [Seite mit der Vorlage für die HPC-Clustererstellung](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Machen Sie sich ggf. mit den Informationen zur Vorlage sowie mit dem Quellcode vertraut.
2. Klicken Sie auf **In Azure bereitstellen** , um im Azure-Portal eine Bereitstellung mit der Vorlage zu beginnen.
   
   ![Vorlage für Azure bereitstellen][github]
3. Führen Sie im Portal die folgenden Schritte aus, um die Parameter für die HPC-Clustervorlage einzugeben:
   
   a. Geben Sie auf der Seite **Parameter** Werte für die Vorlagenparameter ein, bzw. (Klicken Sie auf das Symbol neben einer Einstellung, um Hilfeinformationen anzuzeigen.) Der Bildschirm weiter unten zeigt Beispielwerte. In diesem Beispiel erstellen wir einen Cluster namens *hpc01* in der Domäne *hpc.local* mit einem Haupt- und zwei Serverknoten. Die Serverknoten werden auf der Grundlage eines HPC Pack-VM-Image erstellt, das auch Microsoft Excel beinhaltet.
   
   ![Parameter eingeben][parameters-new-portal]
   
   > [!NOTE]
   > Der virtuelle Computer für den Hauptknoten wird automatisch auf der Grundlage des [neuesten Marketplace-Image](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) von HPC Pack 2012 R2 für Windows Server 2012 R2 erstellt. Aktuell basiert das Image auf HPC Pack 2012 R2 Update 3.
   > 
   > Die virtuellen Computer für die Serverknoten werden auf der Grundlage des neuesten Image der ausgewählten Serverknotenfamilie erstellt. Wählen Sie die Option **ComputeNodeWithExcel** aus, um das neueste HPC Pack-Serverknotenimage mit einer Evaluierungsversion von Microsoft Excel Professional Plus 2013 zu erhalten. Um einen Cluster für allgemeine SOA-Sitzungen oder für die Abladung von Excel-UDFs bereitzustellen, wählen Sie die Option **ComputeNode** (ohne Installation von Excel) aus.
   > 
   > 
   
   b. Wählen Sie das Abonnement aus.
   
   c. Erstellen Sie eine Ressourcengruppe für den Cluster (beispielsweise *hpc01RG*).
   
   d. Wählen Sie einen Ort für die Ressourcengruppe (beispielsweise „USA, Mitte“).
   
   e. Lesen Sie die **rechtlichen Bedingungen**. Klicken Sie auf **Erwerben**, sofern Sie den Bedingungen zustimmen. Legen Sie die Werte für die Vorlage fest, und klicken Sie anschließend auf **Erstellen**.
4. Wenn die Bereitstellung nach etwa 30 Minuten abgeschlossen ist, exportieren Sie die Clusterzertifikatsdatei aus dem Clusterhauptknoten. Dieses öffentliche Zertifikat importieren Sie in einem späteren Schritt zur serverseitigen Authentifizierung für eine sichere HTTP-Bindung auf dem Clientcomputer .
   
   a. Wechseln Sie im Azure-Portal zum Dashboard, wählen Sie den Hauptknoten, und klicken Sie auf **Verbinden** am oberen Rand der Seite, um mithilfe von Remotedesktop eine Verbindung herzustellen.
   
    <!-- ![Connect to the head node][connect] -->
   
   b. Exportieren Sie das Hauptknotenzertifikat (zu finden unter „Cert:\LocalMachine\My“) mithilfe von Standardverfahren im Zertifikat-Manager ohne den privaten Schlüssel. In diesem Beispiel wird *CN = hpc01.eastus.cloudapp.azure.com*exportiert.
   
   ![Zertifikat exportieren][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Option 2. Mit dem HPC Pack-IaaS-Bereitstellungsskript
Das HPC Pack-IaaS-Bereitstellungsskript ist eine weitere vielseitige Bereitstellungsmethode für HPC Pack-Cluster. Es erstellt einen Cluster im klassischen Bereitstellungsmodell, während die Vorlage das Azure Resource Manager-Bereitstellungsmodell verwendet. Das Skript ist außerdem mit einem Abonnement im Azure Global- oder Azure China-Dienst kompatibel.

**Zusätzliche Voraussetzungen**

* **Azure PowerShell** - [Installieren und konfigurieren Sie Azure PowerShell](/powershell/azure/overview) (ab Version 0.8.10) auf Ihrem Clientcomputer.
* **HPC Pack-IaaS-Bereitstellungsskript** : Laden Sie die neueste Version des Skripts aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949)herunter, und entpacken Sie sie. Führen Sie `New-HPCIaaSCluster.ps1 –Version`aus, um die Version des Skripts zu überprüfen. Dieser Artikel basiert auf der Skriptversion 4.5.0 oder höher.

**Erstellen der Konfigurationsdatei**

 Das HPC Pack-IaaS-Bereitstellungsskript verwendet als Eingabe eine XML-Konfigurationsdatei mit der Beschreibung der Infrastruktur des HPC-Clusters. Geben Sie in der folgenden Beispielkonfigurationsdatei Werte für Ihre Umgebung ein, um einen Cluster mit einem Haupt- und 18 Serverknoten bereitzustellen, die auf der Grundlage des Serverknotenimage mit Microsoft Excel erstellt werden. Weitere Informationen zur Konfigurationsdatei finden Sie in der Datei „Manual.rtf“ im Skriptordner und unter [Erstellen eines HPC-Clusters mit dem HPC Pack-IaaS-Bereitstellungsskript](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Hinweise zur Konfigurationsdatei**

* Der Wert für **VMName** des Hauptknotens **MUSS** dem Wert für **ServiceName** entsprechen, oder SOA-Aufträge können nicht ausgeführt werden.
* Geben Sie unbedingt **EnableWebPortal** an, damit das Hauptknotenzertifikat generiert und exportiert wird.
* Die Datei gibt ein Post-Konfigurations-PowerShell-Skript „PostConfig.ps1“ an, das auf dem Hauptknoten ausgeführt wird. Das folgende Beispielskript konfiguriert die Azure-Speicher-Verbindungszeichenfolge, entfernt die Serverknotenrolle vom Hauptknoten und bringt alle Knoten bei deren Bereitstellung online. 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Ausführen des Skripts**

1. Öffnen Sie die PowerShell-Konsole auf dem Clientcomputer als Administrator.
2. Navigieren Sie zum Skriptordner (in diesem Beispiel „E:\IaaSClusterScript“).
   
   ```
   cd E:\IaaSClusterScript
   ```
3. Um den HPC Pack-Cluster bereitzustellen, führen Sie den folgenden Befehl aus. In diesem Beispiel wird davon ausgegangen, dass sich die Konfigurationsdatei unter „E:\HPCDemoConfig.xml“ befindet.
   
   ```
   .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
   ```

Das HPC Pack-Bereitstellungsskript wird ausgeführt. Dieser Vorgang kann eine Weile dauern. Durch das Skript wird unter anderem das Clusterzertifikat exportiert, heruntergeladen und auf dem Clientcomputer im Ordner „Dokumente“ des aktuellen Benutzers gespeichert. Das Skript generiert eine Meldung wie die folgende. In einem anderen Schritt wird das Zertifikat dann in den entsprechenden Zertifikatspeicher importiert.    

    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Schritt 2: Abladen von Excel-Arbeitsmappen und Ausführen von UDFs über einen lokalen Client
### <a name="excel-activation"></a>Excel-Aktivierung
Wenn Sie das ComputeNodeWithExcel-VM-Image für Produktionsworkloads verwenden, müssen Sie eine gültige Microsoft Office-Lizenz angeben, um Excel auf den Serverknoten zu aktivieren. Andernfalls läuft die Evaluierungsversion von Excel nach 30 Tagen ab, und bei der Ausführung der Excel-Arbeitsmappen würde der Ausnahmefehler „COMException“ (0x800AC472) auftreten. 

Sie können Excel für einen weiteren Evaluierungszeitraum von 30 Tagen neu aktivieren: Melden Sie sich bei dem Hauptknoten an, und nehmen Sie eine Clusrun-Ausführung von `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` für alle Excel-Serverknoten mit HPC Cluster Manager vor. Sie können maximal zwei Mal neu aktivieren. Danach müssen Sie einen gültigen Lizenzschlüssel für Office bereitstellen.

Die auf dem VM-Image installierte Edition von Office Professional Plus 2013 ist eine Volumenedition mit einem Generic Volume License Key (GVLK). Sie können sie über Schlüsselverwaltungsdienst (Key Management Service, KMS)/Aktivierung über Active Directory (Active Directory-Based Activation, AD-BA) oder Mehrfachaktivierungsschlüssel (Multiple Activation Key, MAK) aktivieren. 

    * Um KMS/AD-BA zu verwenden, setzen Sie einen vorhandenen KMS-Server ein, oder richten Sie einen neuen mithilfe des Microsoft Office 2013-Volumenlizenzpakets ein. (Falls gewünscht, richten Sie den Server auf dem Hauptknoten ein.) Aktivieren Sie dann den KMS-Host-Schlüssel über das Internet oder telefonisch. Nehmen Sie dann eine Clusrun-Ausführung von `ospp.vbs` vor, um den KMS-Server und den Port einzurichten und Office auf allen Excel-Serverknoten zu aktivieren. 

    * Nehmen Sie zur Verwendung von MAK zuerst eine Clusrun-Ausführung von `ospp.vbs` vor, und aktivieren Sie dann alle Excel-Serverknoten über das Internet oder telefonisch. 

> [!NOTE]
> Retail Product Keys für Office Professsional Plus 2013 können mit diesem VM-Image nicht verwendet werden. Wenn Sie über gültige Schlüssel und Installationsmedien für andere Office- oder Excel-Editionen als diese Office Professional Plus 2013-Volumenedition verfügen, können Sie sie stattdessen verwenden. Deinstallieren Sie zunächst diese Volumenedition, und installieren Sie die Edition, die Sie besitzen. Der neu installierte Excel-Serverknoten kann als benutzerdefiniertes VM-Image zur Verwendung in einer skalierten Bereitstellung erfasst werden.
> 
> 

### <a name="offload-excel-workbooks"></a>Abladen von Excel-Arbeitsmappen
Führen Sie die folgenden Schritte durch, um eine Excel-Arbeitsmappe für die Ausführung auf dem HPC Pack-Cluster in Azure abzuladen. Hierzu muss Excel 2010 oder 2013 bereits auf dem Clientcomputer installiert sein.

1. Verwenden Sie eine der Optionen aus Schritt 1, um einen HPC Pack-Cluster mit dem Excel-Serverknotenimage bereitzustellen. Besorgen Sie sich die Clusterzertifikatsdatei (CER-Datei) sowie den Clusterbenutzernamen und das Kennwort.
2. Importieren Sie auf dem Clientcomputer das Clusterzertifikat unter „Cert:\<Aktueller Benutzer>\Root“.
3. Vergewissern Sie sich, dass Excel installiert ist. Erstellen Sie auf dem Clientcomputer in dem Verzeichnis, in dem sich auch die Datei „Excel.exe“ befindet, eine Datei mit der Bezeichnung „Excel.exe.config“ und dem folgenden Inhalt. Dieser Schritt stellt sicher, dass das Excel-COM-Add-In für HPC Pack 2012 R2 erfolgreich geladen wird.
   
    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
4. Richten Sie den Client zum Übermitteln von Aufträgen an den HPC Pack-Cluster ein. Eine Option ist, die vollständige [HPC Pack 2012 R2 Update 3-Installation](http://www.microsoft.com/download/details.aspx?id=49922) herunterzuladen und den HPC Pack-Client zu installieren. Alternativ können Sie auch die [HPC Pack 2012 R2 Update 3-Clienthilfsprogramme](https://www.microsoft.com/download/details.aspx?id=49923) und die entsprechende weitervertreibbare Visual C++ 2010-Komponente für Ihren Computer ([x64](http://www.microsoft.com/download/details.aspx?id=14632) oder [x86](https://www.microsoft.com/download/details.aspx?id=5555)) herunterladen und installieren.
5. In diesem Beispiel verwenden wir eine Excel-Beispielarbeitsmappe namens „ConvertiblePricing_Complete.xlsb“. Sie können es [hier](https://www.microsoft.com/en-us/download/details.aspx?id=2939)herunterladen.
6. Kopieren Sie die Excel-Arbeitsmappe in einen Ordner (beispielsweise „D:\Excel\Run“).
7. Öffnen Sie die Excel-Arbeitsmappe. Klicken Sie auf dem Menüband **Entwickeln** auf **COM-Add-Ins**, und vergewissern Sie sich, dass das HPC Pack-Excel-COM-Add-In erfolgreich geladen wurde.
   
   ![Excel-Add-In für HPC Pack][addin]
8. Bearbeiten Sie das VBA-Makro „HPCControlMacros“ in Excel. Ändern Sie hierzu die kommentierten Zeilen, wie im folgenden Skript zu sehen. Ersetzen Sie die Werte durch entsprechende Werte für Ihre Umgebung.
   
   ![Excel-Makro für HPC Pack][macro]
   
   ```
   'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
   Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"
   
   'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
   Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.Initialize ActiveWorkbook
   HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles
   
   'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
   HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
   HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
   ```
9. Kopieren Sie die Excel-Arbeitsmappe in ein Uploadverzeichnis, z.B. „D:\Excel\Upload“. Dieses Verzeichnis wird im VBA-Makro in der HPC_DependsFiles-Konstante angegeben.
10. Um die Arbeitsmappe auf dem Azure-Cluster auszuführen, klicken Sie auf dem Arbeitsblatt auf die Schaltfläche **Cluster** .

### <a name="run-excel-udfs"></a>Ausführen von Excel-UDFs
Wenn Sie Excel-UDFs verwenden möchten, führen Sie zur Einrichtung des Clientcomputers die weiter oben angegebenen Schritte 1 bis 3 durch. Für Excel-UDFs muss die Excel-Anwendung nicht auf den Serverknoten installiert sein. Wählen Sie also beim Erstellen des Clusterserverknotens ein normales Serverknotenimage anstelle des Serverknotenimages von Excel.

> [!NOTE]
> Im Clusterconnector-Dialogfeld von Excel 2010 und 2013 sind maximal 34 Zeichen zulässig. Sie verwenden dieses Dialogfeld, um den Cluster anzugeben, der die UDFs ausführt. Längere Clusternamen (z.B. „hpcexcelhn01.southeastasia.cloudapp.azure.com“) passen nicht in das Dialogfeld. Die Problemumgehung besteht darin, eine computerweite Variable, z.B. *CCP_IAASHN*, mit dem Wert des langen Namens des Clusters festzulegen. Geben Sie dann *%CCP_IAASHN%* im Dialogfeld als Namen des Clusterhauptknotens ein. 
> 
> 

Führen Sie nach erfolgreicher Bereitstellung des Clusters die folgenden Schritte durch, um eine integrierte Beispiel-Excel-UDF auszuführen. Wenn Sie angepasste Excel-UDFs benötigen, sehen Sie sich [diese Ressourcen](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) an, um die XLLs zu erstellen und auf dem IaaS-Cluster bereitzustellen.

1. Öffnen Sie eine neue Excel-Arbeitsmappe. Klicken Sie auf dem Menüband **Entwickeln** auf **Add-Ins**. Klicken Sie im Dialogfeld auf **Durchsuchen**, navigieren Sie zum Ordner „%CCP_HOME%Bin\XLL32“, und wählen Sie das Beispiel „ClusterUDF32.xll“ aus. Wenn ClusterUDF32 auf dem Clientcomputer nicht vorhanden ist, kopieren Sie es aus dem Ordner „%CCP_HOME%Bin\XLL32“ auf dem Hauptknoten.
   
   ![UDF auswählen][udf]
2. Klicken Sie auf **Datei** > **Optionen** > **Erweitert**. Aktivieren Sie unter **Formeln** das Kontrollkästchen **Ausführung von benutzerdefinierten XLL-Funktionen auf einem Computecluster zulassen**. Klicken Sie dann auf **Optionen**, und geben Sie den vollständigen **Clusternamen des Clusterhauptknotens** ein. (In diesem Eingabefeld sind wie bereits erwähnt maximal 34 Zeichen zulässig. Sie können hier computerweite Variablen für lange Clusternamen verwenden.)
   
   ![UDF konfigurieren][options]
3. Um die UDF-Berechnung auf dem Cluster auszuführen, klicken Sie auf die Zelle mit dem Wert „=XllGetComputerNameC()“, und drücken Sie die EINGABETASTE. Die Funktion ruft einfach den Namen des Serverknotens ab, auf dem die UDF ausgeführt wird. Bei der erstmaligen Ausführung erscheint ein Dialogfeld, in dem der Benutzer zur Eingabe von Benutzername und Kennwort für die IaaS-Clusterverbindung aufgefordert wird.
   
   ![UDF ausführen][run]
   
   Wenn Berechnungen für viele Zellen durchzuführen sind, drücken Sie STRG+ALT+UMSCHALT+F9, um die Berechnung für alle Zellen durchzuführen.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Schritt 3: Ausführen einer SOA-Workload über einen lokalen Client
Um allgemeine SOA-Anwendungen auf dem HPC Pack-IaaS-Cluster auszuführen, wenden Sie zuerst eine der Methoden in Schritt 1 zur Bereitstellung des Clusters an. Geben Sie in diesem Fall ein generisches Serverknotenimage an, da Sie Excel auf den Serverknoten nicht benötigen. Führen Sie dann die folgenden Schritte durch:

1. Rufen Sie das Clusterzertifikat ab, und importieren Sie es anschließend auf dem Clientcomputer unter „Cert:\<Aktueller Benutzer>\Root“.
2. Installieren Sie das [HPC Pack 2012 R2 Update 3-SDK](http://www.microsoft.com/download/details.aspx?id=49921) und die [HPC Pack 2012 R2 Update 3-Clienthilfsprogramme](https://www.microsoft.com/download/details.aspx?id=49923). Diese Tools ermöglichen Ihnen, SOA-Clientanwendungen zu entwickeln und auszuführen.
3. Laden Sie den [Beispielcode](https://www.microsoft.com/download/details.aspx?id=41633)„HelloWorldR2“ herunter. Öffnen Sie „HelloWorldR2.sln“ in Visual Studio 2010 oder 2012. (Dieses Beispiel ist derzeit nicht mit neueren Versionen von Visual Studio kompatibel.)
4. Erstellen Sie zuerst das EchoService-Projekt. Stellen Sie dann den Dienst auf dem IaaS-Cluster bereit. Gehen Sie dabei auf die gleiche Weise vor wie bei der Bereitstellung auf einem lokalen Cluster. Ausführliche Schritte finden Sie in der Infodatei in „HelloWordR2“. Ändern und erstellen Sie „HelloWorldR2“ und andere Projekte wie im folgenden Abschnitt beschrieben, um die SOA-Clientanwendungen zu generieren, die auf einem Azure IaaS-Cluster ausgeführt werden.

### <a name="use-http-binding-with-azure-storage-queue"></a>Verwenden der HTTP-Bindung mit einer Azure-Speicherwarteschlange
Wenn Sie die HTTP-Bindung mit einer Azure-Speicherwarteschlange verwenden möchten, müssen Sie den Beispielcode etwas verändern.

* Aktualisieren Sie den Clusternamen.
  
    ```
  // Before
  const string headnode = "[headnode]";
  // After e.g.
  const string headnode = "hpc01.eastus.cloudapp.azure.com";
  or
  const string headnode = "hpc01.cloudapp.net";
  ```
* Optional: Verwenden Sie für „SessionStartInfo“ das standardmäßige Transportschema, oder legen Sie es explizit auf „Http“ fest.

```
    info.TransportScheme = TransportScheme.Http;
```

* Verwenden Sie für „BrokerClient“ die Standardbindung.
  
    ```
  // Before
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
  // After
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
  ```
  
    Oder legen Sie den Wert mit „basicHttpBinding“ explizit fest.
  
    ```
  BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
  binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
  ```
* Optional: Legen Sie das Flag „UseAzureQueue“ in „SessionStartInfo“ auf „true“ fest. Ist das Flag nicht festgelegt, wird es standardmäßig auf „true“ festgelegt, wenn der Clustername Azure-Domänensuffixe enthält und das Transportschema „Http“ lautet.
  
    ```
    info.UseAzureQueue = true;
  ```

### <a name="use-http-binding-without-azure-storage-queue"></a>Verwenden der HTTP-Bindung ohne Azure-Speicherwarteschlange
Um die HTTP-Bindung ohne Azure-Speicherwarteschlange zu verwenden, müssen Sie das Flag UseAzureQueue in der SessionStartInfo explizit auf „False“ festlegen.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Verwenden der NetTcp-Bindung
Bei Verwendung der NetTcp-Bindung wird eine ähnliche Konfiguration verwendet wie bei der Verbindungsherstellung mit einem lokalen Cluster. Auf dem virtuellen Computer für den Hauptknoten müssen einige Endpunkte geöffnet werden. Wenn Sie z.B. das HPC Pack-IaaS-Bereitstellungsskript verwendet haben, um den Cluster zu erstellen, legen Sie die Endpunkte wie folgt im Azure-Portal fest.

1. Beenden Sie den virtuellen Computer.
2. Fügen Sie die TCP-Ports 9090, 9087, 9091, 9094 für die Sitzung, für den Broker, für den Brokerworkerdienst bzw. für den Brokerdatendienst hinzu.
   
    ![Endpunkte konfigurieren][endpoint-new-portal]
3. Starten Sie den virtuellen Computer.

Für die SOA-Clientanwendung muss lediglich der Hauptname auf den vollständigen Namen des IaaS-Clusters festgelegt werden.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Ausführen von Excel-Workloads mit HPC Pack finden Sie [hier](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) .
* Weitere Informationen zum Bereitstellen und Verwalten von SOA-Diensten mit HPC Pack finden Sie unter [Verwalten von SOA-Diensten in Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) .

<!--Image references-->
[scenario]: ./media/excel-cluster-hpcpack/scenario.png
[github]: ./media/excel-cluster-hpcpack/github.png
[template]: ./media/excel-cluster-hpcpack/template.png
[parameters]: ./media/excel-cluster-hpcpack/parameters.png
[parameters-new-portal]: ./media/excel-cluster-hpcpack/parameters-new-portal.png
[create]: ./media/excel-cluster-hpcpack/create.png
[connect]: ./media/excel-cluster-hpcpack/connect.png
[cert]: ./media/excel-cluster-hpcpack/cert.png
[addin]: ./media/excel-cluster-hpcpack/addin.png
[macro]: ./media/excel-cluster-hpcpack/macro.png
[options]: ./media/excel-cluster-hpcpack/options.png
[run]: ./media/excel-cluster-hpcpack/run.png
[endpoint]: ./media/excel-cluster-hpcpack/endpoint.png
[endpoint-new-portal]: ./media/excel-cluster-hpcpack/endpoint-new-portal.png
[udf]: ./media/excel-cluster-hpcpack/udf.png
