---
title: "Bereitstellung einer Azure Service Fabric-Anwendung für einen Partycluster | Microsoft-Dokumentation"
description: "Erfahren Sie, wie Sie eine Anwendung für einen Partycluster bereitstellen können."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: 91d4398589707e8007c4b93639ddb568e39f51a7
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-an-application-to-a-party-cluster-in-azure"></a>Bereitstellen einer Anwendung für einen Partycluster in Azure
Dieses Tutorial ist der zweite Teil einer Serie und zeigt, wie Sie eine Azure Service Fabric-Anwendung für einen Partycluster in Azure bereitstellen.

Im zweiten Teil der Tutorialserie lernen Sie Folgendes:
> [!div class="checklist"]
> * [Erstellen einer .NET Service Fabric-Anwendung](service-fabric-tutorial-create-dotnet-app.md)
> * Bereitstellen der Anwendung in einem Remotecluster
> * [Konfigurieren von CI/CD mit Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Einrichten der Überwachung und Diagnose für die Anwendung](service-fabric-tutorial-monitoring-aspnet.md)

In dieser Tutorialserie lernen Sie Folgendes:
> [!div class="checklist"]
> * Bereitstellung einer Anwendung in einem Remotecluster mithilfe von Visual Studio
> * Entfernen einer Anwendung aus einem Cluster mithilfe von Service Fabric Explorer

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:
- Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Installieren Sie Visual Studio 2017](https://www.visualstudio.com/) und die Workloads **Azure-Entwicklung** und **ASP.NET und Webentwicklung**.
- [Installieren Sie das Service Fabric SDK](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Laden Sie die Beispielanwendung „Voting“ herunter.
Falls Sie die Beispielanwendung „Voting“ aus [Teil 1 dieser Tutorialreihe](service-fabric-tutorial-create-dotnet-app.md) nicht erstellt haben, können Sie sie herunterladen. Führen Sie in einem Befehlsfenster den folgenden Befehl aus, um das Beispiel-App-Repository auf Ihren lokalen Computer zu klonen.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-a-party-cluster"></a>Einrichten eines Partyclusters
Partycluster sind kostenlose, zeitlich begrenzte Service Fabric-Cluster, die in Azure gehostet und vom Service Fabric-Team ausgeführt werden, in denen jeder Benutzer Anwendungen bereitstellen und mehr über die Plattform erfahren kann. Kostenlos!

Um Zugriff auf einen Partycluster zu erhalten, navigieren Sie zu dieser Website: http://aka.ms/tryservicefabric und befolgen Sie die Anweisungen. Sie benötigen ein Facebook- oder GitHub-Konto für den Zugriff auf einen Partycluster.

Sie können einen eigenen Cluster statt des Partyclusters verwenden, wenn Sie möchten.  Das Web-Front-End von ASP.NET Core verwendet den Reverseproxy, um mit dem zustandsbehafteten Dienst-Back-End zu kommunizieren.  Für Partycluster und lokale Bereitstellungscluster ist der Reverseproxy standardmäßig aktiviert.  Wenn Sie die Beispielanwendung „Voting“ für Ihren eigenen Cluster bereitstellen, müssen Sie [den Reverseproxy im Cluster aktivieren](service-fabric-reverseproxy.md#setup-and-configuration).

> [!NOTE]
> Partycluster werden nicht gesichert, sodass Ihre Anwendungen und Daten, die Sie in diesem abgelegt haben, möglicherweise für andere Personen sichtbar sind. Stellen Sie nichts bereit, das nicht von anderen gesehen werden soll. Lesen Sie die Nutzungsbedingungen für alle weiteren Details.

Melden Sie sich an, und [treten Sie einem Windows-Cluster bei](http://aka.ms/tryservicefabric). Klicken Sie auf den Link **PFX**, um das PFX-Zertifikat auf Ihren Computer herunterzuladen. Das Zertifikat und der Wert für **Verbindungsendpunkt** werden in den folgenden Schritten verwendet.

![PFX-Zertifikat und Verbindungsendpunkt](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

Installieren Sie das PFX-Zertifikat auf einem Windows-Computer im Zertifikatspeicher *CurrentUser\My*.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:
\CurrentUser\My


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```


## <a name="deploy-the-app-to-the-azure"></a>Bereitstellen der Anwendung in Azure
Nachdem die Anwendung nun bereit ist, können Sie sie direkt aus Visual Studio für den Partycluster bereitstellen.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Voting**, und wählen Sie **Veröffentlichen**. 

    ![Dialogfeld „Veröffentlichen“](./media/service-fabric-quickstart-containers/publish-app.png)

2. Kopieren Sie den **Verbindungsendpunkt** von der Seite des Partyclusters in das Feld **Verbindungsendpunkt**. Beispiel: `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Klicken Sie auf **Erweiterte Verbindungsparameter**, und geben Sie die folgenden Informationen an.  Die Werte *FindValue* und *ServerCertThumbprint* müssen dem Fingerabdruck des im vorherigen Schritt installierten Zertifikats entsprechen. Klicken Sie auf **Veröffentlichen**. 

    Sobald die Veröffentlichung abgeschlossen ist, sollten Sie über den Browser eine Anforderung an die Anwendung senden können.

3. Öffnen Sie Ihren bevorzugten Browser, und geben Sie die Clusteradresse ein (den Verbindungsendpunkt ohne die Portinformationen – also beispielsweise „win1kw5649s.westus.cloudapp.azure.com“).

    Ihnen sollte nun das gleiche Ergebnis angezeigt werden wie bei der lokalen Ausführung der Anwendung.

    ![API-Antwort aus dem Cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="remove-the-application-from-a-cluster-using-service-fabric-explorer"></a>Entfernen einer Anwendung aus einem Cluster mithilfe von Service Fabric Explorer
Service Fabric Explorer ist eine grafische Benutzeroberfläche, um Ihre Anwendungen in einem Service Fabric Cluster zu durchsuchen und zu verwalten.

So entfernen Sie die Anwendung aus dem Partycluster:

1. Navigieren Sie über den von der Party Cluster-Anmeldeseite bereitgestellten Link zu Service Fabric Explorer. Beispiel: „https://win1kw5649s.westus.cloudapp.azure.com:19080/Explorer/index.html“.

2. Navigieren Sie in Service Fabric Explorer in der Strukturansicht auf der linken Seite zum Knoten **fabric:/Voting**.

3. Klicken Sie auf der rechten Seite des Bereichs **Zusammenfassung** auf die Schaltfläche **Aktion**, und klicken Sie auf **Anwendung löschen**. Bestätigen Sie die Löschung der Anwendungsinstanz, um die Instanz Ihrer Anwendung zu entfernen, die im Cluster ausgeführt wird.

![Anwendung in Service Fabric Explorer löschen](./media/service-fabric-tutorial-deploy-app-to-party-cluster/delete-application.png)

## <a name="remove-the-application-type-from-a-cluster-using-service-fabric-explorer"></a>Entfernen des Anwendungstyps aus einem Cluster mithilfe von Service Fabric Explorer
Anwendungen werden als Anwendungstypen in einem Service Fabric-Cluster bereitgestellt. Dadurch können Sie mehrere Instanzen und Versionen der Anwendung innerhalb des Clusters ausführen. Nachdem Sie die ausgeführte Instanz der Anwendung entfernt haben, kann auch der Typ entfernt werden, um die Bereinigung der Bereitstellung abzuschließen.

Weitere Informationen zum Anwendungsmodell in Service Fabric finden Sie unter [Modellieren von Anwendungen in Service Fabric](service-fabric-application-model.md).

1. Navigieren Sie in der Strukturansicht zum Knoten **VotingType**.

2. Klicken Sie auf der rechten Seite des Bereichs **Zusammenfassung** auf die Schaltfläche **Aktion**, und klicken Sie auf **Unprovision Type** (Bereitstellung des Typs aufheben). Bestätigen Sie die Aufhebung der Bereitstellung des Anwendungstyps.

![Aufhebung der Bereitstellung des Anwendungstyps in Service Fabric Explorer](./media/service-fabric-tutorial-deploy-app-to-party-cluster/unprovision-type.png)

Damit ist das Lernprogramm beendet.

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Bereitstellung einer Anwendung in einem Remotecluster mithilfe von Visual Studio
> * Entfernen einer Anwendung aus einem Cluster mithilfe von Service Fabric Explorer

Fahren Sie mit dem nächsten Tutorial fort:
> [!div class="nextstepaction"]
> [Einrichten der Continuous Integration in Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
