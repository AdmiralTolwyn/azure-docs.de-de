---
title: 'Tutorial: Erstellen einer CI/CD-Pipeline in Azure mit Azure DevOps Services | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie eine Azure DevOps Services-Pipeline für Continuous Integration und Continuous Delivery erstellen, die eine Web-App in IIS auf einem virtuellen Windows-Computer in Azure bereitstellt.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: zr-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: 4b4d514ec8bfd78b303a7f51c2a4072507da5be9
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50968988"
---
# <a name="tutorial-create-a-continuous-integration-pipeline-with-azure-devops-services-and-iis"></a>Tutorial: Erstellen einer Continuous Integration-Pipeline mit Azure DevOps Services und IIS
Sie können zum Automatisieren der Erstellungs-, Test- und Bereitstellungsphasen der Anwendungsentwicklung eine Pipeline für Continuous Integration und Continuous Deployment (CI/CD) verwenden. In diesem Tutorial erstellen Sie eine CI/CD-Pipeline mit Azure DevOps Services und einem virtuellen Windows-Computer in Azure, auf dem IIS ausgeführt wird. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Veröffentlichen einer ASP.NET-Webanwendung in einem Azure DevOps Services-Projekt
> * Erstellen einer Buildpipeline, die durch Codecommits ausgelöst wird
> * Installieren und Konfigurieren von IIS auf einem virtuellen Computer in Azure
> * Hinzufügen der IIS-Instanz zu einer Bereitstellungsgruppe in Azure DevOps Services
> * Erstellen einer Releasepipeline zum Veröffentlichen neuer Webbereitstellungspakete in IIS
> * Testen der CI/CD-Pipeline

Für dieses Tutorial ist das Azure PowerShell-Modul Version 5.7.0 oder höher erforderlich. Führen Sie `Get-Module -ListAvailable AzureRM` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-azurerm-ps) Informationen dazu.

## <a name="create-a-project-in-azure-devops-services"></a>Erstellen eines Projekts in Azure DevOps Services
Azure DevOps Services ermöglicht eine einfache Zusammenarbeit und Entwicklung ohne Verwendung einer lokalen Codeverwaltungslösung. Azure DevOps Services bietet Cloudcodetests und Erkenntnisse zu Builds und Anwendungen. Sie können das Repository für die Versionskontrolle und eine IDE auswählen, die sich am besten für die Codeentwicklung eignen. Für dieses Tutorial können Sie eine kostenlose Organisation nutzen, um eine grundlegende ASP.NET-Web-App und CI/CD-Pipeline zu erstellen. Wenn Sie noch nicht über eine Azure DevOps Services-Organisation verfügen, [erstellen Sie eine](http://go.microsoft.com/fwlink/?LinkId=307137).

Erstellen Sie zum Verwalten des Codecommitprozesses, der Buildpipelines und der Releasepipelines wie folgt ein Projekt in Azure DevOps Services:

1. Öffnen Sie Ihr Azure DevOps Services-Dashboard in einem Webbrowser, und wählen Sie **Neues Projekt** aus.
2. Geben Sie für **Projektname** den Namen *myWebApp* ein. Übernehmen Sie alle anderen Standardwerte, um die *Git*-Versionskontrolle und den Arbeitselementprozess *Agile* zu verwenden.
3. Wählen Sie die Option für **die Freigabe** für *Teammitglieder*, und wählen Sie anschließend **Erstellen**.
5. Nachdem das Projekt erstellt wurde, wählen Sie die Option **Initialize with a README or gitignore** (Mit einer INFODATEI oder gitignore initialisieren) und anschließend **Initialisieren**.
6. Klicken Sie im neuen Projekt im oberen Bereich auf **Dashboards** und anschließend auf **In Visual Studio öffnen**.


## <a name="create-aspnet-web-application"></a>Erstellen einer ASP.NET-Webanwendung
Im vorherigen Schritt haben Sie ein Projekt in Azure DevOps Services erstellt. Im letzten Schritt wird Ihr neues Projekt in Visual Studio geöffnet. Sie verwalten Ihre Codecommits im **Team Explorer**-Fenster. Erstellen Sie eine lokale Kopie des neuen Projekts und anschließend wie folgt eine ASP.NET-Webanwendung aus einer Vorlage:

1. Wählen Sie **Klonen** aus, um ein lokales Git-Repository Ihres Azure DevOps Services-Projekts zu erstellen.
    
    ![Klonen eines Repositorys aus einem Azure DevOps Services-Projekt](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. Klicken Sie unter **Projektmappen** auf **Neu**.

    ![Erstellen der Projektmappe für die Webanwendung](media/tutorial-vsts-iis-cicd/new_solution.png)

3. Wählen Sie die **Webvorlagen** und dann die Vorlage für die **ASP.NET-Webanwendung** aus.
    1. Geben Sie einen Namen für Ihre Anwendung ein, z.B. *myWebApp*, und deaktivieren Sie das Kontrollkästchen für **Projektmappenverzeichnis erstellen**.
    2. Wenn die Option verfügbar ist, deaktivieren Sie das Kontrollkästchen **Application Insights zum Projekt hinzufügen**. Application Insights setzt voraus, dass Sie Ihre Webanwendung bei Azure Application Insights autorisieren. Überspringen Sie zur Vereinfachung dieses Tutorials diesen Prozess.
    3. Klicken Sie auf **OK**.
4. Wählen Sie in der Vorlagenliste **MVC** aus.
    1. Klicken Sie nacheinander auf **Authentifizierung ändern**, **Keine Authentifizierung** und **OK**.
    2. Klicken Sie auf **OK**, um die Projektmappe zu erstellen.
5. Klicken Sie im **Team Explorer**-Fenster auf **Änderungen**.

    ![Übernehmen lokaler Änderungen am Azure Repos-Git-Repository](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. Geben Sie in das Textfeld für den Commit eine Nachricht ein, beispielsweise *Commit initialisieren*. Wählen Sie im Dropdownmenü die Option **Commit für alle und Sync** aus.


## <a name="create-build-pipeline"></a>Erstellen der Buildpipeline
In Azure DevOps Services geben Sie mithilfe einer Buildpipeline an, wie Ihre Anwendung erstellt werden soll. In diesem Tutorial erstellen Sie eine grundlegende Pipeline, die den Quellcode verwendet und die Projektmappe und dann das Webbereitstellungspaket erstellt, mit dem Sie die Web-App auf einem IIS-Server ausführen können.

1. Wählen Sie in Ihrem Azure DevOps Services-Projekt im oberen Bereich **Build und Release** und anschließend **Builds** aus.
3. Klicken Sie auf **+ Neue Definition**.
4. Wählen Sie die Vorlage **ASP.NET (VORSCHAU)** und anschließend **Übernehmen**.
5. Übernehmen Sie alle Standardaufgabenwerte. Vergewissern Sie sich unter **Get sources** (Quellen abrufen), dass das Repository *myWebApp* und die *Hauptverzweigung* ausgewählt sind.

    ![Erstellen einer Buildpipeline im Azure DevOps Services-Projekt](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. Schieben Sie auf der Registerkarte **Trigger** den Schieberegler für **Diesen Auslöser aktivieren** auf *Aktiviert*.
7. Speichern Sie die Buildpipeline, und stellen Sie einen neuen Build in die Warteschlange, indem Sie **Speichern und in Warteschlange einreihen** und dann erneut **Speichern und in Warteschlange einreihen** auswählen. Übernehmen Sie die Standardwerte, und klicken Sie auf **Warteschlange**.

Sie können zusehen, wie der Build auf einem gehosteten Agent geplant und dann erstellt wird. Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

![Erfolgreiche Erstellung des Azure DevOps Services-Projekts](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers
Um eine Plattform für die Ausführung Ihrer ASP.NET-Web-App zur Verfügung zu stellen, benötigen Sie einen virtuellen Windows-Computer mit IIS. Azure DevOps Services verwendet einen Agent für die Interaktion mit der IIS-Instanz, wenn Sie für Ihren Code einen Commit ausführen und Builds ausgelöst werden.

Erstellen Sie mit [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) einen virtuellen Computer unter Windows Server 2016. Im folgenden Beispiel wird ein virtueller Computer mit dem Namen *myVM* am Standort *USA, Osten* erstellt. Zudem werden die Ressourcengruppe *myResourceGroupAzureDevOpsServices* und unterstützende Netzwerkressourcen erstellt. Um Webdatenverkehr zu ermöglichen, ist TCP-Port *80* für den virtuellen Computer geöffnet. Geben Sie bei entsprechender Aufforderung einen Benutzernamen und ein Kennwort als Anmeldeinformationen für den virtuellen Computer ein:

```powershell
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine
New-AzureRmVM `
  -ResourceGroupName "myResourceGroupAzureDevOpsServices" `
  -Name "myVM" `
  -Location "East US" `
  -ImageName "Win2016Datacenter" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -SecurityGroupName "myNetworkSecurityGroup" `
  -PublicIpAddressName "myPublicIp" `
  -Credential $cred `
  -OpenPorts 80
```

Rufen Sie zum Herstellen einer Verbindung mit dem virtuellen Computer mithilfe von [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) wie folgt die öffentliche IP-Adresse ab:

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Erstellen Sie eine Remotedesktopsitzung für Ihren virtuellen Computer:

```cmd
mstsc /v:<publicIpAddress>
```

Öffnen Sie auf dem virtuellen Computer eine **PowerShell-Administratoreingabeaufforderung**. Installieren Sie wie folgt IIS und die erforderlichen .NET-Features:

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>Erstellen einer Bereitstellungsgruppe

Zum Übertragen des Webbereitstellungspakets an den IIS-Server per Push definieren Sie eine Bereitstellungsgruppe in Azure DevOps Services. Mit dieser Gruppe können Sie angeben, welche Server als Ziel der neuen Builds verwendet werden, wenn Sie für Ihren Code einen Commit in Azure DevOps Services ausführen und Builds abgeschlossen werden.

1. Wählen Sie in Azure DevOps Services auf **Build und Release** und dann **Bereitstellungsgruppen** aus.
2. Klicken Sie auf **Bereitstellungsgruppe hinzufügen**.
3. Geben Sie einen Namen für die Gruppe an, z.B. *myIIS*, und klicken Sie dann auf **Erstellen**.
4. Vergewissern Sie sich im Abschnitt **Computer registrieren**, dass *Windows* ausgewählt ist, und aktivieren Sie das Kästchen **Use a personal access token in the script for authentication** (Persönliches Zugriffstoken im Skript zur Authentifizierung verwenden).
5. Klicken Sie auf **Copy script to clipboard** (Skript in Zwischenablage kopieren).


### <a name="add-iis-vm-to-the-deployment-group"></a>Hinzufügen des virtuellen IIS-Computers zur Bereitstellungsgruppe
Fügen Sie nach Erstellung der Bereitstellungsgruppe die einzelnen IIS-Instanzen zur Gruppe hinzu. Azure DevOps Services generiert ein Skript, das auf dem virtuellen Computer einen Agent herunterlädt und konfiguriert, der wiederum neue Webbereitstellungspakete empfängt und dann auf IIS anwendet.

1. Kehren Sie zurück zur **PowerShell-Administratorsitzung** auf dem virtuellen Computer, fügen Sie dort das aus Azure DevOps Services kopierte Skript ein, und führen Sie es aus.
2. Wenn Sie aufgefordert werden, Tags für den Agent zu konfigurieren, klicken Sie auf *J*, und geben Sie *Web* ein.
3. Wenn Sie zur Eingabe des Benutzerkontos aufgefordert werden, drücken Sie die *EINGABETASTE* , um die Standardwerte zu übernehmen.
4. Warten Sie, bis die Ausführung des Skripts mit der Meldung *Service vstsagent.account.computername started successfully* (Dienst „vstsagent.account.computername“ wurde erfolgreich gestartet.) abgeschlossen wird.
5. Öffnen Sie im Menü **Build und Release** auf der Seite **Bereitstellungsgruppen** die Bereitstellungsgruppe *myIIS*. Überprüfen Sie auf der Registerkarte **Computer**, ob Ihr virtueller Computer aufgeführt ist.

    ![Der virtuelle Computer wurde der Azure DevOps Services-Bereitstellungsgruppe hinzugefügt.](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-pipeline"></a>Erstellen einer Releasepipeline
Erstellen Sie zum Veröffentlichen Ihrer Builds eine Releasepipeline in Azure DevOps Services. Diese Pipeline wird bei der erfolgreichen Erstellung Ihrer Anwendung automatisch ausgelöst. Sie wählen die Bereitstellungsgruppe aus, an die Ihr Webbereitstellungspaket per Push übertragen werden soll, und definieren die entsprechenden IIS-Einstellungen.

1. Klicken Sie auf **Build und Release** und dann auf **Builds**. Wählen Sie die Buildpipeline aus, die Sie in einem vorherigen Schritt erstellt haben.
2. Wählen Sie unter **Kürzlich abgeschlossen** den zuletzt abgeschlossenen Build aus, und klicken Sie dann auf **Release**.
3. Wählen Sie **Ja** aus, um eine Releasepipeline zu erstellen.
4. Wählen Sie die Vorlage **Leer** und dann **Weiter**.
5. Überprüfen Sie, ob das Projekt und die Quellbuildpipeline mit Ihrem Projekt ausgefüllt werden.
6. Aktivieren Sie das Kontrollkästchen **Continuous Deployment**, und klicken Sie dann auf **Erstellen**.
7. Klicken Sie auf das Dropdownfeld neben **+ Aufgaben hinzufügen** und dann auf *Bereitstellungsgruppenphase hinzufügen*.
    
    ![Hinzufügen einer Aufgabe zur Releasepipeline in Azure DevOps Services](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. Klicken Sie neben **IIS Web App Deploy(Preview)** (IIS-Web-App-Bereitstellung (Vorschau)) auf **Hinzufügen** und danach auf **Schließen**.
9. Wählen Sie die übergeordnete Aufgabe **Für Bereitstellungsgruppe ausführen**.
    1. Wählen Sie für **Bereitstellungsgruppe** die zuvor erstellte Bereitstellungsgruppe, z.B. *myIIS*.
    2. Klicken Sie im Feld **Computertags** auf **Hinzufügen**, und wählen Sie dann das Tag *Web* aus.
    
    ![Releasepipeline-Bereitstellungsgruppenaufgabe für IIS](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. Wählen Sie die Aufgabe **Deploy: IIS Web App Deploy** (Bereitstellen: IIS-Web-App-Bereitstellung), um die Einstellungen der IIS-Instanz wie folgt zu konfigurieren:
    1. Geben Sie unter **Websitename** den Namen *Standardwebsite* ein.
    2. Übernehmen Sie alle anderen Standardeinstellungen.
12. Klicken Sie auf **Speichern** und anschließend zweimal auf **OK**.


## <a name="create-a-release-and-publish"></a>Erstellen und Veröffentlichen eines Release
Sie können nun Ihr Webbereitstellungspaket als neues Release übertragen. In diesem Schritt erfolgt die Kommunikation mit dem Agent der einzelnen Instanzen, die Teil der Bereitstellungsgruppe sind. Darüber hinaus wird das Webbereitstellungspaket übertragen und IIS zur Ausführung der aktualisierten Webanwendung konfiguriert.

1. Wählen Sie in Ihrer Releasepipeline **+ Release** und anschließend **Release erstellen** aus.
2. Stellen Sie sicher, dass in der Dropdownliste der aktuelle Build und **Automatisierte Bereitstellung: nach der Releaseerstellung** ausgewählt sind. Klicken Sie auf **Erstellen**.
3. Im oberen Bereich der Releasepipeline wird ein kleines Banner angezeigt, etwa *Release „Release-1“ wurde erstellt*. Klicken Sie auf den Releaselink.
4. Öffnen Sie die Registerkarte **Protokolle**, um den Releasestatus anzuzeigen.
    
    ![Erfolgreiche Übertragung des Azure DevOps Services-Release- und -Webbereitstellungspakets](media/tutorial-vsts-iis-cicd/successful_release.png)

5. Öffnen Sie nach Abschluss des Release einen Webbrowser, und geben Sie die öffentliche IP-Adresse Ihres virtuellen Computers ein. Ihre ASP.NET-Webanwendung wird ausgeführt.

    ![Auf dem virtuellen IIS-Computer ausgeführte ASP.NET-Web-App](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>Testen der gesamten CI/CD-Pipeline
Ihre Webanwendung wird unter IIS ausgeführt. Testen Sie nun die gesamte CI/CD-Pipeline. Nachdem Sie eine Änderung in Visual Studio vorgenommen und für Ihren Code einen Commit ausgeführt haben, wird ein Build ausgelöst, was wiederum eine Veröffentlichung Ihres aktualisierten Webbereitstellungspakets in IIS auslöst:

1. Öffnen Sie in Visual Studio das Fenster **Projektmappen-Explorer**.
2. Navigieren Sie zu *myWebApp | Ansichten | Start | Index.cshtml*, und öffnen Sie die Datei.
3. Bearbeiten Sie die Zeile 6 wie folgt:

    `<h1>ASP.NET with Azure DevOps Services and CI/CD!</h1>`

4. Speichern Sie die Datei .
5. Öffnen Sie das **Team Explorer**-Fenster, wählen Sie das Projekt *myWebApp* aus, und klicken Sie dann auf **Änderungen**.
6. Geben Sie eine Commitnachricht ein, z.B. *Testen der CI/CD-Pipeline*, und wählen Sie dann im Dropdownmenü **Commit für alle und Sync**.
7. In einem Azure DevOps Services-Arbeitsbereich wird durch den Codecommit ein neuer Build ausgelöst. 
    - Klicken Sie auf **Build und Release** und dann auf **Builds**. 
    - Wählen Sie Ihre Buildpipeline aus, und wählen Sie dann den Build **In Warteschlange und aktuell ausgeführt** aus, um den Status des Builds zu überwachen.
9. Nachdem der Build erfolgreich erstellt wurde, wird ein neues Release ausgelöst.
    - Klicken Sie auf **Build und Release** und dann auf **Releases**, um das Webbereitstellungspaket per Push an den virtuellen IIS-Computer zu übertragen. 
    - Klicken Sie auf das Symbol **Aktualisieren**, um den Status zu aktualisieren. Wenn in der Spalte *Umgebungen* ein grünes Häkchen angezeigt wird, wurde das Release erfolgreich in IIS bereitgestellt.
11. Aktualisieren Sie die IIS-Website in einem Browser, um die Änderungen zu übernehmen.

    ![ASP.NET-Web-App, die über die CI/CD-Pipeline auf dem virtuellen IIS-Computer ausgeführt wird](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine ASP.NET-Webanwendung in Azure DevOps Services erstellt und Build- und Releasepipelines konfiguriert, um mit jedem Codecommit neue Webbereitstellungspakete in IIS bereitzustellen. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Veröffentlichen einer ASP.NET-Webanwendung in einem Azure DevOps Services-Projekt
> * Erstellen einer Buildpipeline, die durch Codecommits ausgelöst wird
> * Installieren und Konfigurieren von IIS auf einem virtuellen Computer in Azure
> * Hinzufügen der IIS-Instanz zu einer Bereitstellungsgruppe in Azure DevOps Services
> * Erstellen einer Releasepipeline zum Veröffentlichen neuer Webbereitstellungspakete in IIS
> * Testen der CI/CD-Pipeline

Wechseln Sie zum nächsten Tutorial, um zu erfahren, wie Sie einen SQL&#92;IIS&#92;.NET-Stapel auf einem Paar von Windows-VMs installieren.

> [!div class="nextstepaction"]
> [Install a SQL\IIS\.NET stack in Azure](tutorial-iis-sql.md) (Installieren eines SQL\IIS\.NET-Stapels in Azure)
