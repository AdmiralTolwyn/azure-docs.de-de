---
title: Debuggen von Windows-Containern mit Service Fabric und VS | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Windows-Container in Azure Service Fabric mit Visual Studio 2017 debuggen.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/14/2018
ms.author: mikhegn
ms.openlocfilehash: 180bd3709cc9ffefb17f78e337e6f6995024fdcf
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523426"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2017"></a>Anleitung: Debuggen von Windows-Containern in Azure Service Fabric mit Visual Studio 2017

Mit Visual Studio 2017 Update 7 (15.7) können Sie .NET-Anwendungen in Containern als Service Fabric-Dienste debuggen. In diesem Artikel erfahren Sie, wie Sie Ihre Umgebung konfigurieren und anschließend eine .NET-Anwendung in einem Container debuggen, der in einem lokalen Service Fabric-Cluster ausgeführt wird.

## <a name="prerequisites"></a>Voraussetzungen

* Folgen Sie bei Windows 10 zum [Konfigurieren von Windows 10 zum Ausführen von Windows-Containern](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10) dieser Schnellstartanleitung
* Folgen Sie bei Windows Server 2016 zum [Konfigurieren von Windows 2016 zum Ausführen von Windows-Containern](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) dieser Schnellstartanleitung
* Richten Sie Ihre lokale Service Fabric-Umgebung ein, indem Sie die unter [Vorbereiten Ihrer Entwicklungsumgebung unter Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) beschriebenen Anweisungen ausführen.

## <a name="configure-your-developer-environment-to-debug-containers"></a>Konfigurieren der Entwicklerumgebung zum Debuggen von Containern

1. Stellen Sie sicher, dass der Docker für den Windows-Dienst ausgeführt wird, bevor Sie mit dem nächsten Schritt fortfahren.

1. Um die DNS-Auflösung zwischen den Containern zu unterstützen, müssen Sie Ihren lokalen Entwicklungscluster einrichten und dazu den Computernamen verwenden. Diese Schritte sind auch erforderlich, wenn Sie Dienste über den Reverseproxy addressieren möchten.
    1. Starten von PowerShell als Administrator
    2. Navigieren Sie zum Setupordner des SDK-Clusters, in der Regel `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`.
    3. Führen Sie das Skript `DevClusterSetup.ps1` mit dem Parameter `-UseMachineName` aus.

       ``` PowerShell
         C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1 -UseMachineName
       ```

    > [!NOTE]
    > Sie können `-CreateOneNodeCluster` zum Einrichten eines Einzelknotenclusters verwenden. Mit dem Standardwert wird ein lokaler Cluster mit fünf Knoten erstellt.
    >

    Weitere Informationen zum DNS-Dienst in Service Fabric finden Sie unter [DNS-Dienst in Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice). Weitere Informationen zur Verwendung des Service Fabric-Reverseproxys von Diensten aus, die in einem Container ausgeführt werden, finden Sie unter [Reverseproxy – spezieller Umgang mit Diensten, die in Containern ausgeführt werden](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Bekannte Einschränkungen beim Debuggen von Containern in Service Fabric

Es folgt eine Liste der bekannten Einschränkungen beim Debuggen von Containern in Service Fabric und mögliche Lösungen:

* Bei Verwendung von „localhost“ für „ClusterFQDNorIP“ wird die DNS-Auflösung in Containern nicht unterstützt.
    * Lösung: Richten Sie den lokalen Cluster unter Verwendung des Computernamens ein (siehe oben).
* Beim Ausführen von Windows 10 auf einem virtuellen Computer bekommt der Container keine DNS-Antwort zurück.
    * Lösung: Deaktivieren Sie die UDP-Prüfsummenabladung für IPv4 auf der NIC des virtuellen Computers.
    * Beachten Sie, dass dies die Netzwerkleistung auf dem Computer beeinträchtigt.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Das Auflösen von Diensten in derselben Anwendung mithilfe des DNS-Dienstnamens funktioniert nicht unter Windows 10, wenn die Anwendung mit Docker Compose bereitgestellt wurde.
    * Lösung: Verwenden Sie dienstname.anwendungsname zum Auflösen der Dienstendpunkte.
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Bei Verwendung der IP-Adresse für „ClusterFQDNorIP“ wird beim Ändern der primären IP-Adresse auf dem Host die DNS-Funktionalität unterbrochen.
    * Lösung: Erstellen Sie den Cluster mit der neuen primären IP-Adresse auf dem Host neu, oder verwenden Sie den Computernamen. Dies ist beabsichtigt.
* Wenn der vollständig qualifizierte Domänennamen (FQDN), mit dem der Cluster erstellt wurde, im Netzwerk nicht aufgelöst werden kann, schlägt DNS fehl.
    * Lösung: Erstellen Sie den lokalen Cluster neu, und verwenden Sie dabei die primäre IP-Adresse des Hosts. Dies ist beabsichtigt.
* Beim Debuggen eines Containers stehen Docker-Protokolle nur im Visual Studio-Ausgabefenster und nicht über Service Fabric-APIs (einschließlich Service Fabric Explorer) zur Verfügung.

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Debuggen einer .NET-Anwendung, die in Docker-Containern unter Service Fabric ausgeführt wird

1. Starten Sie Visual Studio als Administrator.

1. Öffnen Sie eine vorhandene .NET Anwendung, oder erstellen Sie eine neue.

1. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen -> Unterstützung für Containerorchestrator -> Service Fabric** aus.

1. Drücken Sie **F5** , um das Debuggen der Anwendung zu starten.

    Visual Studio unterstützt Konsolen- und ASP.NET-Projekttypen für .NET und .NET Core.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu den Funktionen von Service Fabric und Containern finden Sie über diesen Link: [Übersicht über Service Fabric-Container](service-fabric-containers-overview.md).
