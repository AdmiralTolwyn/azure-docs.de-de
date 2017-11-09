---
title: "Einrichten eines eigenständigen Azure Service Fabric-Clusters | Microsoft-Dokumentation"
description: "Erstellen Sie einen eigenständigen Entwicklungscluster mit drei Knoten, die auf dem gleichen Computer ausgeführt werden. Nach Abschluss dieser Einrichtung können Sie einen Cluster mit mehreren Computern erstellen."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 5438d8d366ef989d5ae29581477513f8c884c4b3
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2017
---
# <a name="create-your-first-service-fabric-standalone-cluster"></a>Erstellen Ihres ersten eigenständigen Service Fabric-Clusters
Sie können einen eigenständigen Service Fabric-Cluster auf beliebigen virtuellen oder physischen Computern unter Windows Server 2012 R2 oder Windows Server 2016 erstellen – ganz gleich ob lokal oder in der Cloud. In diesem Schnellstarttutorial erfahren Sie, wie Sie innerhalb weniger Minuten einen eigenständigen Entwicklungscluster erstellen.  Nach Abschluss des Tutorials verfügen Sie über einen Cluster mit drei Knoten, der auf einem einzelnen Computer ausgeführt wird und für den Sie Apps bereitstellen können.

## <a name="before-you-begin"></a>Voraussetzungen
Für Service Fabric steht ein Setuppaket zum Erstellen eigenständiger Service Fabric-Cluster bereit.  [Laden Sie das Setuppaket herunter.](http://go.microsoft.com/fwlink/?LinkId=730690)  Entzippen Sie das Setuppaket in einen Ordner auf dem Computer bzw. virtuellen Computer, auf dem Sie den Entwicklungscluster einrichten.  Der Inhalt des Setuppakets wird [hier](service-fabric-cluster-standalone-package-contents.md) ausführlich beschrieben.

Der Clusteradministrator, der den Cluster bereitstellt und konfiguriert, muss auf dem Computer über Administratorrechte verfügen. Service Fabric kann nicht auf einem Domänencontroller installiert werden.

## <a name="validate-the-environment"></a>Überprüfen der Umgebung
Das Skript *TestConfiguration.ps1* im eigenständigen Paket wird als Best Practices Analyzer verwendet, um zu prüfen, ob ein Cluster in einer bestimmten Umgebung bereitgestellt werden kann. Informationen zu den Voraussetzungen und den Anforderungen an die Umgebung finden Sie unter [Planen und Vorbereiten der Bereitstellung eines eigenständigen Service Fabric-Clusters](service-fabric-cluster-standalone-deployment-preparation.md). Führen Sie das Skript aus, um sich zu vergewissern, dass der Entwicklungscluster erstellt werden kann:

```powershell
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
```
## <a name="create-the-cluster"></a>Erstellen des Clusters
Mit dem Setuppaket werden mehrere Beispielkonfigurationsdateien für den Cluster installiert. Die einfachste Clusterkonfiguration ist *ClusterConfig.Unsecure.DevCluster.json*: ein unsicherer Cluster mit drei Knoten auf einem einzelnen Computer.  Andere Konfigurationsdateien beschreiben durch X.509-Zertifikate oder Windows-Sicherheit geschützte Cluster mit einem einzelnen Computer oder mit mehreren Computern.  Sie müssen für dieses Tutorial keine Standardkonfigurationseinstellungen ändern, aber es ist ratsam, sich die Konfigurationsdatei anzusehen und sich mit den Einstellungen vertraut zu machen.  Im Abschnitt **Knoten** wird für die drei Knoten des Clusters Folgendes beschrieben: Name, IP-Adresse, [Knotentyp, Fehlerdomäne und Upgradedomäne](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  Im Abschnitt **Eigenschaften** werden die [Sicherheit, Zuverlässigkeitsstufe, Diagnosesammlung und Knotentypen](service-fabric-cluster-manifest.md#cluster-properties) für den Cluster beschrieben.

Dieser Cluster ist unsicher.  Da es möglich ist, anonyme Verbindungen damit herzustellen und Verwaltungsvorgänge durchzuführen, sollten Cluster für die Produktion immer mit X.509-Zertifikaten oder per Windows-Sicherheit geschützt werden.  Die Sicherheit wird nur während der Clustererstellung konfiguriert, und nach der Erstellung des Clusters ist es nicht mehr möglich, die Sicherheit zu aktivieren.  Weitere Informationen zur Sicherheit von Service Fabric-Clustern finden Sie unter [Szenarien für die Clustersicherheit in Service Fabric](service-fabric-cluster-security.md).  

Führen Sie das Skript *CreateServiceFabricCluster.ps1* über eine PowerShell-Administratorsitzung aus, um den Entwicklungscluster mit drei Knoten zu erstellen:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Das Service Fabric-Laufzeitpaket wird zum Zeitpunkt der Clustererstellung automatisch heruntergeladen und installiert.

## <a name="connect-to-the-cluster"></a>Verbinden mit dem Cluster
Ihr Entwicklungscluster mit drei Knoten wird nun ausgeführt. Das ServiceFabric-PowerShell-Modul wird zusammen mit der Laufzeit installiert.  Die Überprüfung, ob der Cluster ausgeführt wird, kann über den gleichen Computer oder über einen Remotecomputer mit der Service Fabric-Laufzeit erfolgen.  Das Cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) stellt eine Verbindung mit dem Cluster her.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
Weitere Beispiele für die Herstellung einer Clusterverbindung finden Sie unter [Herstellen einer Verbindung mit einem sicheren Cluster](service-fabric-connect-to-secure-cluster.md). Verwenden Sie nach dem Herstellen einer Verbindung mit dem Cluster das Cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps), um eine Liste mit den Knoten des Clusters sowie Statusinformationen für die einzelnen Knoten anzuzeigen. **HealthState** muss für jeden Knoten *OK* lauten.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Visualisieren des Clusters mit Service Fabric Explorer
Mit [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) können Sie Ihren Cluster visualisieren und Anwendungen verwalten.  Der Service Fabric Explorer-Dienst wird im Cluster ausgeführt und kann über einen Browser unter [http://localhost:19080/Explorer](http://localhost:19080/Explorer) aufgerufen werden. 

Das Clusterdashboard bietet eine Übersicht über Ihren Cluster mit einer Zusammenfassung der Anwendungs- und Knotenintegrität. Die Knotenansicht zeigt das physische Layout des Clusters. Für einen Knoten können Sie überprüfen, für welche Anwendungen Code auf dem Knoten bereitgestellt wurde.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="remove-the-cluster"></a>Entfernen des Clusters
Führen Sie zum Entfernen eines Clusters das PowerShell-Skript *RemoveServiceFabricCluster.ps1* aus dem Paketordner aus, und übergeben Sie den Pfad zur JSON-Konfigurationsdatei. Sie können optional einen Speicherort für das Löschprotokoll angeben.

```powershell
# Removes Service Fabric cluster nodes from each computer in the configuration file.
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -Force
```

Führen Sie das folgende PowerShell-Skript aus dem Paketordner aus, um die Service Fabric-Laufzeit von dem Computer zu entfernen:

```powershell
# Removes Service Fabric from the current computer.
.\CleanFabric.ps1
```

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun einen eigenständigen Entwicklungscluster eingerichtet haben, können Sie sich in den folgenden Artikeln weiter informieren:
* [Einrichten eines eigenständigen Clusters mit mehreren Computern](service-fabric-cluster-creation-for-windows-server.md) und Aktivieren der Sicherheit
* [Bereitstellen von Apps mit PowerShell](service-fabric-deploy-remove-applications.md)

[service-fabric-explorer]: ./media/service-fabric-get-started-standalone-cluster/sfx.png
