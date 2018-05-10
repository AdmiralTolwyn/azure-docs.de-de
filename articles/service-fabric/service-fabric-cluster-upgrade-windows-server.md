---
title: Upgraden eines eigenständigen Azure Service Fabric-Clusters unter Windows Server | Microsoft-Dokumentation
description: Upgraden des Azure Service Fabric-Codes und/oder Konfiguration für die Ausführung eines eigenständigen Service Fabric-Clusters und Festlegen des Clusteraktualisierungsmodus
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 3354fab5f664391520e721586712e5035ff79cb4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2018
---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>Upgraden des eigenständigen Azure Service Fabric-Clusters unter Windows Server 
> [!div class="op_single_selector"]
> * [Azure-Cluster](service-fabric-cluster-upgrade.md)
> * [Eigenständiger Cluster](service-fabric-cluster-upgrade-windows-server.md)
>
>

Für alle modernen Systeme ist die Möglichkeit eines Upgrades der Schlüssel zum langfristigen Erfolg des Produkts. Ein Azure Service Fabric-Cluster ist eine Ressource, die sich in Ihrem Besitz befindet. In diesem Artikel wird beschrieben, wie Sie sicherstellen, dass der Cluster immer unterstützte Versionen des Service Fabric-Codes und von Konfigurationen ausführt.

## <a name="control-the-service-fabric-version-that-runs-on-your-cluster"></a>Steuern der in Ihrem Cluster ausgeführten Service Fabric-Version
Um festzulegen, dass Ihr Cluster Updates von Service Fabric herunterlädt, wenn Microsoft eine neue Version herausgibt, setzen Sie die Clusterkonfiguration „fabricClusterAutoupgradeEnabled“ auf *true*. Um eine unterstützte Version von Service Fabric auszuwählen, bei der Ihr Cluster bleiben soll, setzen Sie die Clusterkonfiguration „fabricClusterAutoupgradeEnabled“ auf *false*.

> [!NOTE]
> Achten Sie darauf, dass in Ihrem Cluster immer eine unterstützte Service Fabric-Version ausgeführt wird. Wenn Microsoft eine neue Service Fabric-Version ankündigt, beträgt die verbleibende Supportdauer der vorherigen Version noch mindestens 60 Tage ab dem Datum der Ankündigung. Neue Releases werden im [Blog des Service Fabric-Teams](https://blogs.msdn.microsoft.com/azureservicefabric/) angekündigt. Ab dann kann das neue Release ausgewählt werden.
>
>

Sie können Ihren Cluster nur auf die neue Version upgraden, wenn Sie eine Knotenkonfiguration im Produktionsformat verwenden, bei der jeder Service Fabric-Knoten einem separaten physischen oder virtuellen Computer zugeordnet ist. Wenn Sie einen Entwicklungscluster mit mehreren Service Fabric-Knoten auf einem physischen oder virtuellen Computer haben, müssen Sie Ihren Cluster mit der neuen Version neu erstellen.

Für das Upgrade Ihres Clusters auf die aktuelle oder eine unterstützte Service Fabric-Version gibt es zwei unterschiedliche Workflows. Ein Workflow ist für Cluster vorgesehen, die über Konnektivität verfügen, um die neuesten Versionen automatisch herunterzuladen. Der andere Workflow ist für Cluster vorgesehen, die nicht über Konnektivität verfügen, um die neueste Service Fabric-Version automatisch herunterzuladen.

### <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Aktualisieren der Cluster mit Konnektivität zum Herunterladen des aktuellen Codes und der Konfiguration
Führen Sie diese Schritte aus, um Ihren Cluster auf eine unterstützte Version upzugraden, wenn Ihre Clusterknoten eine Internetverbindung mit dem [Microsoft Download Center](http://download.microsoft.com) haben.

Für Cluster mit Verbindung zum [Microsoft Download Center](http://download.microsoft.com) überprüft Microsoft in regelmäßigen Abständen die Verfügbarkeit neuer Service Fabric-Versionen.

Wenn eine neue Service Fabric-Version verfügbar ist, wird das Paket lokal in den Cluster heruntergeladen und für das Upgrade bereitgestellt. Um den Kunden außerdem über diese neue Version zu informieren, zeigt das System eine explizite Clusterintegritätswarnung ähnlich der folgenden an:

„Der Support für die aktuelle Clusterversion [Versionsnummer] endet am [Datum].“

Wenn der Cluster die neueste Version ausführt, wird diese Warnung nicht mehr angezeigt.

#### <a name="cluster-upgrade-workflow"></a>Workflow für das Upgrade des Clusters
Wenn die Clusterintegritätswarnung angezeigt wird, führen Sie folgende Schritte aus:

1. Stellen Sie auf einem Computer mit Administratorzugriffsrechten auf alle Computer, die als Knoten in der Clusterkonfigurationsdatei aufgeführt sind, eine Verbindung mit dem Cluster her. Der Computer, auf dem dieses Skript ausgeführt wird, muss nicht Teil des Clusters sein.

    ```powershell

    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Rufen Sie die Liste der Service Fabric-Versionen ab, auf die Sie ein Upgrade durchführen können.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Die Ausgabe sollte in etwa wie folgt aussehen:

    ![Abrufen von Service Fabric-Versionen][getfabversions]
3. Starten Sie mithilfe des Windows PowerShell-Befehls [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx) ein Clusterupgrade auf eine verfügbare Version.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Um den Fortschritt des Upgrades zu verfolgen, können Sie Service Fabric Explorer nutzen oder den folgenden Windows PowerShell-Befehl ausführen:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Wenn die Integritätsrichtlinien des Clusters nicht erfüllt sind, wird für das Upgrade ein Rollback ausgeführt. Wie Sie benutzerdefinierte Integritätsrichtlinien für den Befehl „Start-ServiceFabricClusterUpgrade“ angeben, erfahren Sie in der Dokumentation zu [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Nachdem Sie die Probleme behoben haben, die zu dem Rollback geführt haben, initiieren Sie das Upgrade erneut, indem Sie die gleichen Schritte wie zuvor beschrieben ausführen.

### <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Aktualisieren der Cluster *ohne Konnektivität* zum Herunterladen des aktuellen Codes und der Konfiguration
Führen Sie diese Schritte aus, um Ihren Cluster auf eine unterstützte Version upzugraden, wenn zwischen Ihrem Clusterknoten und dem [Microsoft Download Center](http://download.microsoft.com) keine Internetverbindung besteht.

> [!NOTE]
> Wenn Sie einen Cluster ohne Internetverbindung ausführen, müssen Sie dem Service Fabric-Teamblog entnehmen, ob ein neues Release verfügbar ist. Das System zeigt keine Clusterintegritätswarnung an, um Sie über neue Releases zu unterrichten.  
>
>

#### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatische Bereitstellung im Vergleich zur manuellen Bereitstellung
Richten Sie den Service Fabric-Updatedienst ein, um das automatische Herunterladen und Registrieren für die neueste Codeversion zu aktivieren. Anweisungen finden Sie in der Datei „Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt“ im [eigenständigen Paket](service-fabric-cluster-standalone-package-contents.md).
Befolgen Sie für die manuelle Vorgehensweise die Anweisungen unten.

Ändern Sie die Clusterkonfiguration, um die folgende Eigenschaft auf *false* festzulegen, bevor Sie mit dem Konfigurationsupgrade beginnen:

        "fabricClusterAutoupgradeEnabled": false,

Nutzungsdetails finden Sie in den Informationen zum PowerShell-Befehl [Start-ServiceFabricClusterConfigurationUpgrade](https://msdn.microsoft.com/library/mt788302.aspx). Achten Sie darauf, den Wert für „clusterConfigurationVersion“ in der JSON-Datei zu aktualisieren, bevor Sie das Konfigurationsupgrade starten.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>Workflow für das Upgrade des Clusters

1. Führen Sie „Get-ServiceFabricClusterUpgrade“ auf einem der Knoten im Cluster aus, und notieren Sie den TargetCodeVersion-Wert.

2. Führen Sie Folgendes auf einem mit dem Internet verbundenen Computer aus, um alle mit dem Upgrade kompatiblen Versionen mit der aktuellen Version aufzulisten und das entsprechende Paket über die zugehörigen Downloadlinks herunterzuladen:

    ```powershell

    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Stellen Sie auf einem Computer mit Administratorzugriffsrechten auf alle Computer, die als Knoten in der Clusterkonfigurationsdatei aufgeführt sind, eine Verbindung mit dem Cluster her. Der Computer, auf dem dieses Skript ausgeführt wird, muss nicht Teil des Clusters sein.

    ```powershell

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```
4. Kopieren Sie das heruntergeladene Paket in den Clusterimagespeicher.

5. Registrieren Sie das kopierte Paket.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
6. Starten Sie ein Clusterupgrade auf eine verfügbare Version.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Sie können den Fortschritt des Upgrades in Service Fabric Explorer oder durch Ausführen des folgenden PowerShell-Befehls verfolgen:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Wenn die Integritätsrichtlinien des Clusters nicht erfüllt sind, wird für das Upgrade ein Rollback ausgeführt. Wie Sie benutzerdefinierte Integritätsrichtlinien für den Befehl „Start-ServiceFabricClusterUpgrade“ angeben, erfahren Sie in der Dokumentation zu [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Nachdem Sie die Probleme behoben haben, die zu dem Rollback geführt haben, initiieren Sie das Upgrade erneut, indem Sie die gleichen Schritte wie zuvor beschrieben ausführen.


## <a name="upgrade-the-cluster-configuration"></a>Upgraden der Clusterkonfiguration
Bevor Sie das Konfigurationsupgrade initiieren, können Sie die neue JSON-Datei für die Clusterkonfiguration durch Ausführen des folgenden PowerShell-Skripts im eigenständigen Paket testen:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>

```
Oder verwenden Sie dieses Skript:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>

```

Einige Konfigurationen können nicht aktualisiert werden, z.B. Endpunkte, Clusternamen, Knoten-IP-Adressen usw. Die neue JSON-Datei für die Clusterkonfiguration wird anhand der alten getestet, und es werden Fehler im PowerShell-Fenster ausgegeben, wenn es Probleme gibt.

Um die Clusterkonfiguration upzugraden, führen Sie den Befehl „Start-ServiceFabricClusterConfigurationUpgrade“ aus. Das Konfigurationsupgrade wird Upgradedomäne für Upgradedomäne verarbeitet.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

### <a name="cluster-certificate-config-upgrade"></a>Konfigurationsupgrade für Clusterzertifikate  
Für die Authentifizierung zwischen Clusterknoten wird ein Clusterzertifikat verwendet. Der Zertifikatrollover sollte daher mit besonderer Vorsicht ausgeführt werden, da Fehler die Kommunikation zwischen Clusterknoten blockieren könnten.

Technisch gesehen werden vier Optionen unterstützt:  

* Einzelnes Zertifikatupgrade: Der Upgradepfad lautet „Zertifikat A (primär) > Zertifikat B (primär) > Zertifikat C (primär) > ...“.

* Doppeltes Zertifikatupgrade: Der Upgradepfad lautet „Zertifikat A (primär) > Zertifikat A (primär) und B (sekundär) > Zertifikat B (primär) > Zertifikat B (primär) und C (sekundär) > Zertifikat C (primär) > ...“.

* Zertifikattypupgrade: Fingerabdruckbasierte Zertifikatkonfiguration <-> auf allgemeinem Namen basierende Zertifikatkonfiguration. Beispiel: Zertifikatfingerabdruck A (primär) und Fingerabdruck B (sekundär) -> allgemeiner Name des Zertifikats C.

* Fingerabdruckupgrade des Zertifikatausstellers: Der Upgradepfad lautet „Allgemeiner Name des Zertifikats=A,IssuerThumbprint=IT1 (primär) -> Allgemeiner Name des Zertifikats=A,IssuerThumbprint=IT1,IT2 (primär) -> Allgemeiner Name des Zertifikats=A,IssuerThumbprint=IT2 (primär)“.


## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich über das [Anpassen von Service Fabric-Clustereinstellungen und der Fabric-Upgraderichtlinie](service-fabric-cluster-fabric-settings.md).
* Machen Sie sich mit der Vorgehensweise zum [Skalieren Ihres Clusters](service-fabric-cluster-scale-up-down.md) vertraut.
* Machen Sie sich mit [Anwendungsupgrades](service-fabric-application-upgrade.md) vertraut.

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
