---
title: Einrichten eines Azure Service Fabric-Clusters | Microsoft-Dokumentation
description: "Schnellstart: Es wird beschrieben, wie Sie in Azure einen Service Fabric-Cluster für Windows oder Linux erstellen."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/24/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ecf9554554c8b7acbd8b8f5aa9122ce1678c6502
ms.contentlocale: de-de
ms.lasthandoff: 09/25/2017

---

# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Erstellen Ihres ersten Service Fabric-Clusters in Azure
Ein [Service Fabric-Cluster](service-fabric-deploy-anywhere.md) enthält eine per Netzwerk verbundene Gruppe von virtuellen oder physischen Computern, auf denen Ihre Microservices bereitgestellt und verwaltet werden. Mithilfe dieses Schnellstarts können Sie über [Azure PowerShell](https://msdn.microsoft.com/library/dn135248) oder das [Azure-Portal](http://portal.azure.com) in wenigen Minuten einen Cluster mit fünf Knoten für Windows oder Linux erstellen.  

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.


## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Melden Sie sich unter [http://portal.azure.com](http://portal.azure.com) am Azure-Portal an.

### <a name="create-the-cluster"></a>Erstellen des Clusters

1. Klicken Sie in der linken oberen Ecke des Azure-Portals auf die Schaltfläche **Neu**.
2. Wählen Sie auf dem Blatt **Neu** die Option **Compute** und dann auf dem Blatt **Compute** die Option **Service Fabric-Cluster**.
3. Füllen Sie das Formular mit den **Grundeinstellungen** für Service Fabric aus. Wählen Sie für **Betriebssystem** die Windows- oder Linux-Version aus, die für die Clusterknoten ausgeführt werden soll. Der hier eingegebene Benutzername und das Kennwort werden zum Anmelden am virtuellen Computer verwendet. Erstellen Sie für **Ressourcengruppe** eine neue Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen erstellt und kollektiv verwaltet werden. Klicken Sie zum Abschluss auf **OK**.

    ![Ausgabe bei der Clustereinrichtung][cluster-setup-basics]

4. Füllen Sie das Formular **Clusterkonfiguration** aus.  Geben Sie für **Anzahl von Knotentypen** den Wert „1“ ein.

5. Wählen Sie **Knotentyp 1 (Primär)**, und füllen Sie das Formular **Knotentypkonfiguration** aus.  Geben Sie einen Knotentypnamen ein, und legen Sie [Dauerhaftigkeitsstufe](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) auf „Bronze“ fest.  Wählen Sie eine VM-Größe aus.

    Anhand von Knotentypen werden die VM-Größe, die Anzahl von VMs, benutzerdefinierte Endpunkte und andere Einstellungen für die VMs des jeweiligen Typs festgelegt. Jeder definierte Knotentyp wird als separate VM-Skalierungsgruppe eingerichtet, die zum Bereitstellen und Verwalten von virtuellen Computern als Gruppe verwendet wird. Jeder Knotentyp kann einzeln zentral hoch- oder herunterskaliert werden, bei jedem Typ können unterschiedliche Portgruppen geöffnet sein, und die Typen können verschiedene Kapazitätsmetriken aufweisen.  Der erste bzw. primäre Knotentyp dient zum Hosten von Service Fabric-Systemdiensten und muss über mindestens fünf VMs verfügen.

    Die [Kapazitätsplanung](service-fabric-cluster-capacity.md) ist ein wichtiger Schritt bei jeder Produktionsbereitstellung.  Für diesen Schnellstart führen Sie aber keine Anwendungen aus, sodass Sie die VM-Größe *DS1_v2 Standard* wählen können.  Wählen Sie als [Zuverlässigkeitsstufe](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) die Einstellung „Silber“, und geben Sie als Anfangskapazität für die VM-Skalierungsgruppe den Wert „5“ an.  

    Für benutzerdefinierte Endpunkte werden Ports im Azure Load Balancer geöffnet, damit Sie Verbindungen mit Anwendungen herstellen können, die im Cluster ausgeführt werden.  Geben Sie „80, 8172“ ein, um die Ports 80 und 8172 zu öffnen.

    Achten Sie darauf, dass Sie das Kontrollkästchen **Erweiterte Einstellungen konfigurieren** nicht aktivieren. Es wird zum Anpassen von TCP/HTTP-Verwaltungsendpunkten, Anwendungsportbereichen, [Platzierungseinschränkungen](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints) und [Kapazitätseigenschaften](service-fabric-cluster-resource-manager-metrics.md) verwendet.    

    Klicken Sie auf **OK**.

6. Legen Sie im Formular **Clusterkonfiguration** die Option **Diagnose** auf **Ein** fest.  Für diesen Schnellstart müssen Sie keine Eigenschaften für [Fabric-Einstellung](service-fabric-cluster-fabric-settings.md) eingeben.  Wählen Sie unter **Fabricversion** den Upgrademodus **Automatisch** aus, damit die Version des im Cluster ausgeführten Fabric-Codes von Microsoft automatisch aktualisiert wird.  Legen Sie den Modus auf **Manuell** fest, wenn Sie selbst eine [unterstützte Version](service-fabric-cluster-upgrade.md) für das Upgrade auswählen möchten. 

    ![Knotentypkonfiguration][node-type-config]

    Klicken Sie auf **OK**.

7. Füllen Sie das Formular **Sicherheit** aus.  Wählen Sie für diesen Schnellstart die Option **Unsicher**.  Es wird dringend empfohlen, für Produktionsworkloads einen sicheren Cluster zu erstellen. Der Grund hierfür ist, dass mit einem unsicheren Cluster anonyme Verbindungen hergestellt und Verwaltungsvorgänge durchgeführt werden können.  

    Zertifikate werden in Service Fabric zur Authentifizierung und Verschlüsselung verwendet, um verschiedene Aspekte eines Clusters und der zugehörigen Anwendungen zu sichern. Weitere Informationen zur Verwendung von Zertifikaten in Service Fabric finden Sie unter [Szenarien für die Clustersicherheit in Service Fabric](service-fabric-cluster-security.md).  [Erstellen Sie einen Cluster mit einer Resource Manager-Vorlage](service-fabric-cluster-creation-via-arm.md), um die Benutzerauthentifizierung per Azure Active Directory zu ermöglichen oder Zertifikate für die Anwendungssicherheit einzurichten.

    Klicken Sie auf **OK**.

8. Überprüfen Sie die Zusammenfassung.  Wählen Sie die Option **Vorlage und Parameter herunterladen**, wenn Sie eine Resource Manager-Vorlage herunterladen möchten, die mit den von Ihnen eingegebenen Einstellungen erstellt wurde.  Wählen Sie **Erstellen**, um den Cluster zu erstellen.

    Sie können den Verlauf der Erstellung in den Benachrichtigungen finden. (Klicken Sie auf das Glockensymbol in der Nähe der Statusleiste am oberen rechten Bildschirmrand.) Wenn Sie beim Erstellen des Clusters auf **An Startmenü anheften** geklickt haben, wird im Menü **Start** der Hinweis **Deploying Service Fabric Cluster** (Service Fabric-Cluster wird bereitgestellt) angezeigt.

### <a name="connect-to-the-cluster-using-powershell"></a>Herstellen einer Verbindung mit dem Cluster über PowerShell
Stellen Sie sicher, dass der Cluster ausgeführt wird, indem Sie mit PowerShell eine Verbindung herstellen.  Das Service Fabric-PowerShell-Modul wird zusammen mit dem [Service Fabric-SDK](service-fabric-get-started.md) installiert.  Das Cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) stellt eine Verbindung mit dem Cluster her.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint quickstartcluster.westus2.cloudapp.azure.com:19000
```
Weitere Beispiele für die Herstellung einer Clusterverbindung finden Sie unter [Herstellen einer Verbindung mit einem sicheren Cluster](service-fabric-connect-to-secure-cluster.md). Verwenden Sie nach dem Herstellen einer Verbindung mit dem Cluster das Cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps), um eine Liste mit den Knoten des Clusters sowie Statusinformationen für die einzelnen Knoten anzuzeigen. **HealthState** muss für jeden Knoten *OK* lauten.

```powershell
PS C:\Users\sfuser> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
```

### <a name="remove-the-cluster"></a>Entfernen des Clusters
Ein Service Fabric-Cluster besteht, zusätzlich zu der Clusterressource selbst, aus weiteren Azure-Ressourcen. Daher müssen Sie alle Ressourcen löschen, aus denen der Cluster besteht, um einen Service Fabric-Cluster zu löschen. Die einfachste Möglichkeit zum Löschen des Clusters und aller darin genutzten Ressourcen besteht darin, die Ressourcengruppe zu löschen. Andere Möglichkeiten zum Löschen eines Clusters oder nur einiger Ressourcen einer Ressourcengruppe finden Sie unter [Löschen eines Clusters](service-fabric-cluster-delete.md).

Löschen Sie eine Ressourcengruppe im Azure-Portal:
1. Navigieren Sie zu dem Service Fabric-Cluster, den Sie löschen möchten.
2. Klicken Sie auf der Clusterseite „Essentials“ (Zusammenfassung) auf den Namen der **Ressourcengruppe**.
3. Klicken Sie auf der Seite **Essentials** (Zusammenfassung) der Ressourcengruppe auf **Ressourcengruppe löschen**, und befolgen Sie die Anleitung auf dieser Seite, um das Löschen der Ressourcengruppe abzuschließen.
    ![Löschen der Ressourcengruppe][cluster-delete]


## <a name="use-azure-powershell-to-deploy-a-secure-windows-cluster"></a>Verwenden von Azure PowerShell zum Bereitstellen eines sicheren Windows-Clusters
1. Laden Sie das [Azure PowerShell-Modul (Version 4.0 oder höher)](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) auf Ihren Computer herunter.

2. Öffnen Sie ein Windows PowerShell-Fenster, und führen Sie den folgenden Befehl aus. 
    
    ```powershell

    Get-Command -Module AzureRM.ServiceFabric 
    ```

    Es sollte in etwa folgende Ausgabe angezeigt werden:

    ![ps-list][ps-list]

3. Melden Sie sich an Azure an, und wählen Sie das Abonnement aus, für das Sie den Cluster erstellen möchten.

    ```powershell

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId "Subcription ID" 
    ```

4. Führen Sie den folgenden Befehl aus, um jetzt einen sicheren Cluster zu erstellen. Vergessen Sie nicht, die Parameter anzupassen. 

    ```powershell
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
    $RDPpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 
    $RDPuser="vmadmin"
    $RGname="mycluster" # this is also the name of your cluster
    $clusterloc="SouthCentralUS"
    $subname="$RGname.$clusterloc.cloudapp.azure.com"
    $certfolder="c:\mycertificates\"
    $clustersize=1 # can take values 1, 3-99

    New-AzureRmServiceFabricCluster -ResourceGroupName $RGname -Location $clusterloc -ClusterSize $clustersize -VmUserName $RDPuser -VmPassword $RDPpwd -CertificateSubjectName $subname -CertificatePassword $certpwd -CertificateOutputFolder $certfolder
    ```

    Bis zum Abschluss des Befehls kann es zwischen 10 und 30 Minuten dauern, und am Ende erhalten Sie eine Ausgabe, die wie unten angegeben aussehen sollte. Die Ausgabe enthält Informationen zum Zertifikat, zum KeyVault, in den der Upload durchgeführt wurde, und zum lokalen Ordner, in den das Zertifikat kopiert wurde. 

    ![ps-out][ps-out]

5. Kopieren Sie die gesamte Ausgabe, und speichern Sie sie in einer Textdatei, da wir sie später noch benötigen. Notieren Sie sich die folgenden Informationen der Ausgabe: 

    - **CertificateSavedLocalPath** : c:\mycertificates\mycluster20170504141137.pfx
    - **CertificateThumbprint** : C4C1E541AD512B8065280292A8BA6079C3F26F10
    - **ManagementEndpoint** : https://mycluster.southcentralus.cloudapp.azure.com:19080
    - **ClientConnectionEndpointPort** : 19000

### <a name="install-the-certificate-on-your-local-machine"></a>Installieren des Zertifikats auf dem lokalen Computer
  
Zum Herstellen einer Verbindung mit dem Cluster müssen Sie das Zertifikat im persönlichen Speicher (My) des aktuellen Benutzers installieren. 

Führen Sie den folgenden PowerShell-Code aus:

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\the name of the cert.pfx `
        -Password (ConvertTo-SecureString -String certpwd -AsPlainText -Force)
```

Sie können nun die Verbindung mit Ihrem sicheren Cluster herstellen.

### <a name="connect-to-a-secure-cluster"></a>Herstellen einer Verbindung mit einem sicheren Cluster 

Führen Sie den folgenden PowerShell-Befehl aus, um eine Verbindung mit einem sicheren Cluster herzustellen. Die Zertifikatdetails müssen mit einem Zertifikat übereinstimmen, das zum Einrichten des Clusters verwendet wurde. 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```


Das folgende Beispiel enthält die fertigen Parameter: 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mycluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Führen Sie den folgenden Befehl aus, um sicherzustellen, dass die Verbindung hergestellt wurde und der Cluster fehlerfrei ist.

```powershell

Get-ServiceFabricClusterHealth

```
### <a name="remove-the-cluster"></a>Entfernen des Clusters
Ein Cluster besteht neben der Clusterressource selbst noch aus weiteren Azure-Ressourcen. Die einfachste Möglichkeit zum Löschen des Clusters und aller darin genutzten Ressourcen besteht darin, die Ressourcengruppe zu löschen. 

```powershell

Remove-AzureRmResourceGroup -Name $RGname -Force

```
## <a name="use-azure-cli-to-deploy-a-secure-linux-cluster"></a>Verwenden der Azure CLI zum Bereitstellen eines sicheren Linux-Clusters

1. Installieren Sie [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) auf Ihrem Computer.
2. Melden Sie sich an Azure an, und wählen Sie das Abonnement aus, unter dem Sie den Cluster erstellen möchten.
   ```azurecli
   az login
   az account set --subscription <GUID>
   ```
3. Führen Sie den Befehl [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) aus, um einen sicheren Cluster zu erstellen.

    ```azurecli
    #!/bin/bash

    # Variables
    ResourceGroupName="aztestclustergroup" 
    ClusterName="aztestcluster" 
    Location="southcentralus" 
    Password="q6D7nN%6ck@6" 
    Subject="aztestcluster.southcentralus.cloudapp.azure.com" 
    VaultName="aztestkeyvault" 
    VaultGroupName="testvaultgroup"
    VmPassword="Mypa$$word!321"
    VmUserName="sfadminuser"

    # Create resource groups
    az group create --name $ResourceGroupName --location $Location 
    az group create --name $VaultGroupName --location $Location

    # Create secure five node Linux cluster. Creates a key vault in a resource group
    # and creates a certficate in the key vault. The certificate's subject name must match 
    # the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
    az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . \
        --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName \
        --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $VaultGroupName \
        --vm-password $VmPassword --vm-user-name $VmUserName
    ```
    
### <a name="connect-to-the-cluster"></a>Verbinden mit dem Cluster
Führen Sie den folgenden CLI-Befehl aus, um mit dem Zertifikat eine Verbindung mit dem Cluster herzustellen.  Wenn ein Clientzertifikat zur Authentifizierung verwendet wird, müssen die Details des Zertifikats mit einem Zertifikat überstimmen, das dem Clusterknoten bereitgestellt wird.  Verwenden Sie die Option `--no-verify` für ein selbstsigniertes Zertifikat.

```azurecli
az sf cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 --pem ./linuxcluster201709161647.pem --no-verify
```

Führen Sie den folgenden Befehl aus, um sicherzustellen, dass die Verbindung hergestellt wurde und der Cluster fehlerfrei ist.

```azurecli
az sf cluster health
```

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun einen Entwicklungscluster eingerichtet haben, können Sie Folgendes ausprobieren:
* [Visualisieren Ihres Clusters mit Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* [Entfernen eines Clusters](service-fabric-cluster-delete.md) 
* [Bereitstellen von Apps mit PowerShell](service-fabric-deploy-remove-applications.md)
* [Bereitstellen von Apps per CLI](service-fabric-application-lifecycle-sfctl.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
[ps-list]: ./media/service-fabric-get-started-azure-cluster/pslist.PNG
[ps-out]: ./media/service-fabric-get-started-azure-cluster/psout.PNG

