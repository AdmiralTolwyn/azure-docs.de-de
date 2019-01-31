---
title: Erstellen einer Java-App unter Service Fabric in Azure | Microsoft-Dokumentation
description: In diesem Schnellstart erstellen Sie eine Java-Anwendung für Azure, indem Sie eine Service Fabric Reliable Services-Beispielanwendung verwenden.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: ad14e552bd685c42289e7007002f5ddf039f8925
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55297955"
---
# <a name="quickstart-deploy-a-java-reliable-services-application-to-service-fabric"></a>Schnellstart: Bereitstellen einer Java Reliable Services-Anwendung in Service Fabric

Azure Service Fabric ist eine Plattform, mit der verteilte Systeme bereitgestellt und skalierbare und zuverlässige Microservices und Container verwaltet werden können.

In diesem Schnellstart wird veranschaulicht, wie Sie Ihre erste Java-Anwendung in Service Fabric mithilfe der Eclipse-IDE auf einem Linux-Entwicklercomputer bereitstellen. Das Ergebnis ist eine Anwendung mit einem Java-Web-Front-End, mit der Abstimmungsergebnisse im Cluster in einem zustandsbehafteten Back-End-Dienst gespeichert werden.

![Screenshot der Anwendung](./media/service-fabric-quickstart-java/votingapp.png)

In dieser Schnellstartanleitung wird Folgendes vermittelt:

* Verwenden von Eclipse als Tool für Ihre Service Fabric-Java-Anwendungen
* Bereitstellen der Anwendung im Cluster
* Horizontales Hochskalieren der Anwendung über mehrere Knoten hinweg

## <a name="prerequisites"></a>Voraussetzungen

So führen Sie diesen Schnellstart durch:

1. [Installieren Sie das Service Fabric SDK und die Service Fabric-Befehlszeilenschnittstelle (Command Line Interface, CLI).](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Installation von Git](https://git-scm.com/)
3. [Installieren Sie Eclipse.](https://www.eclipse.org/downloads/)
4. [Richten Sie eine Java-Umgebung ein](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development), und führen Sie auch die optionalen Schritte für die Installation des Eclipse-Plug-Ins aus.

## <a name="download-the-sample"></a>Herunterladen des Beispiels

Führen Sie in einem Befehlsfenster den folgenden Befehl aus, um das Beispiel-App-Repository auf Ihren lokalen Computer zu klonen.

```git
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Lokales Ausführen der Anwendung

1. Starten Sie Ihren lokalen Cluster mit dem folgenden Befehl:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Es kann einige Zeit dauern, bis der lokale Cluster startet. Um zu prüfen, ob der Cluster vollständig betriebsbereit ist, greifen Sie unter **http://localhost:19080** auf Service Fabric Explorer zu. Wenn die fünf Knoten fehlerfrei sind, wird der lokale Cluster ausgeführt.

    ![Fehlerfreier lokaler Cluster](./media/service-fabric-quickstart-java/localclusterup.png)

2. Öffnen Sie Eclipse.
3. Klicken Sie auf „Datei“ -> „Importieren“ -> „Gradle“ -> „Vorhandenes Gradle-Projekt“, und führen Sie die Schritte des Assistenten aus.
4. Klicken Sie auf „Directory“ (Verzeichnis), und wählen Sie im Ordner `service-fabric-java-quickstart` das Verzeichnis `Voting` aus, das Sie aus GitHub geklont haben. Klicken Sie auf Fertig stellen.

    ![Eclipse-Dialogfeld „Importieren“](./media/service-fabric-quickstart-java/eclipseimport.png)

5. Das Projekt `Voting` befindet sich nun im Paket-Explorer von Eclipse.
6. Klicken Sie mit der rechten Maustaste auf das Projekt und anschließend in der Dropdownliste **Service Fabric** auf **Publish Application...** (Anwendung veröffentlichen…). Wählen Sie **PublishProfiles/Local.json** als Zielprofil („Target Profile“) aus, und klicken Sie auf „Publish“ (Veröffentlichen).

    ![Lokales Dialogfeld „Veröffentlichen“](./media/service-fabric-quickstart-java/localjson.png)

7. Öffnen Sie Ihren bevorzugten Browser, und greifen Sie über **http://localhost:8080** auf die Anwendung zu.

    ![Lokales Front-End der Anwendung](./media/service-fabric-quickstart-java/runninglocally.png)

Sie können jetzt einen Satz mit Abstimmungsoptionen hinzufügen und die Abstimmung freigeben. Die Anwendung führt alle Daten in Ihrem Service Fabric-Cluster aus und speichert sie dort, ohne dass eine separate Datenbank verwendet werden muss.

## <a name="scale-applications-and-services-in-a-cluster"></a>Skalieren von Anwendungen und Diensten in einem Cluster

Dienste können clusterweit skaliert werden, um eine Änderung der Last für die Dienste auszugleichen. Sie skalieren einen Dienst, indem Sie die Anzahl von Instanzen ändern, die im Cluster ausgeführt werden. Dienste können auf unterschiedliche Weise skaliert werden – beispielsweise mithilfe von Skripts oder Befehlen der Service Fabric-Befehlszeilenschnittstelle (sfctl). In den folgenden Schritten wird Service Fabric Explorer verwendet.

Service Fabric Explorer wird in allen Service Fabric-Clustern ausgeführt und kann in einem Browser geöffnet werden. Navigieren Sie hierzu zum HTTP-Verwaltungsport des Clusters (19.080). Beispiel: `http://localhost:19080`.

Gehen Sie zum Skalieren des Web-Front-End-Diensts wie folgt vor:

1. Öffnen Sie Service Fabric Explorer in Ihrem Cluster, z.B. `https://localhost:19080`.
2. Klicken Sie auf das Auslassungszeichen (drei Punkte) neben dem Knoten **fabric:/Voting/VotingWeb** in der Strukturansicht, und wählen Sie **Scale Service** (Dienst skalieren).

    ![Service Fabric Explorer – Dienst skalieren](./media/service-fabric-quickstart-java/scaleservicejavaquickstart.png)

    Sie können jetzt angeben, dass die Anzahl von Instanzen des Web-Front-End-Diensts skaliert werden soll.

3. Ändern Sie die Anzahl in **2**, und klicken Sie auf **Scale Service** (Dienst skalieren).
4. Klicken Sie in der Strukturansicht auf den Knoten **fabric:/Voting/VotingWeb**, und erweitern Sie den Partitionsknoten (durch eine GUID dargestellt).

    ![Service Fabric Explorer: Dienstskalierung abgeschlossen](./media/service-fabric-quickstart-java/servicescaled.png)

    Sie sehen nun, dass der Dienst über zwei Instanzen verfügt, und in der Strukturansicht ist zu erkennen, auf welchen Knoten die Instanzen ausgeführt werden.

Mit dieser einfachen Verwaltungsaufgabe haben Sie die Ressourcen verdoppelt, die dem Front-End-Dienst zur Bewältigung der Benutzerauslastung zur Verfügung stehen. Es ist wichtig zu verstehen, dass Sie nicht mehrere Instanzen eines Diensts benötigen, damit er zuverlässig ausgeführt wird. Sollte ein Dienst ausfallen, stellt Service Fabric sicher, dass im Cluster eine neue Dienstinstanz ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie Folgendes gelernt:

* Verwenden von Eclipse als Tool für Ihre Service Fabric-Java-Anwendungen
* Bereitstellen von Java-Anwendungen im lokalen Cluster
* Horizontales Hochskalieren der Anwendung über mehrere Knoten hinweg

Weitere Informationen zur Verwendung von Java-Apps in Service Fabric finden Sie im Tutorial für Java-Apps.

> [!div class="nextstepaction"]
> [Bereitstellen einer Java-App](./service-fabric-tutorial-create-java-app.md)
