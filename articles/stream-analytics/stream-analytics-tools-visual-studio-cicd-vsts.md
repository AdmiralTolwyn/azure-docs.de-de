---
title: 'Tutorial: Bereitstellen eines Azure Stream Analytics-Auftrags mit CI/CD mithilfe von Azure DevOps Services'
description: In diesem Artikel wird beschrieben, wie Sie einen Azure Stream Analytics-Auftrag mit CI/CD mithilfe von Azure DevOps Services bereitstellen.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 07/10/2018
ms.openlocfilehash: 0f729725a04b19a513ca92953e997b51e4558884
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986264"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-azure-pipelines"></a>Tutorial: Bereitstellen eines Azure Stream Analytics-Auftrags mit CI/CD mithilfe von Azure Pipelines
In diesem Tutorial wird beschrieben, wie Continuous Integration und Continuous Deployment für einen Azure Stream Analytics-Auftrag mithilfe von Azure-Pipelines eingerichtet werden. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen der Quellcodeverwaltung zum Projekt
> * Erstellen einer Buildpipeline in Azure-Pipelines
> * Erstellen einer Releasepipeline in Azure-Pipelines
> * Automatisches Bereitstellen und Durchführen von Upgrades einer Anwendung

## <a name="prerequisites"></a>Voraussetzungen
Stellen Sie zunächst sicher, dass Sie über Folgendes verfügen:

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.
* Installieren Sie [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) und die Workload **Azure-Entwicklung** oder **Datenspeicherung und -verarbeitung**.
* Erstellen Sie ein [Stream Analytics-Projekt in Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs).
* Erstellen Sie eine [Azure DevOps-Organisation](https://visualstudio.microsoft.com/team-services/).

## <a name="configure-nuget-package-dependency"></a>Konfigurieren der NuGet-Paketabhängigkeit
Um auf einem beliebigen Computer eine automatische Erstellung und Bereitstellung auszuführen, müssen Sie das NuGet-Paket `Microsoft.Azure.StreamAnalytics.CICD` verwenden. Es bietet Tools für MSBuild, lokales Ausführen und Bereitstellung zur Unterstützung von Continuous Integration und Continuous Deployment für Stream Analytics Visual Studio-Projekte. Weitere Informationen finden Sie unter [Kontinuierliche Integration und Entwicklung mit Stream Analytics-Tools](stream-analytics-tools-for-visual-studio-cicd.md).

Fügen Sie **packages.config** zu Ihrem Projektverzeichnis hinzu.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-azure-repos-git-repo"></a>Freigeben der Visual Studio-Projektmappe in einem neuen Git-Repository von Azure Repos.

Teilen Sie Ihre Anwendungsquelldateien für ein Projekt in Azure DevOps, damit Sie Builds erstellen können.  

1. Erstellen Sie ein neues lokales Git-Repository für das Projekt, indem Sie auf der Statusleiste in der unteren rechten Ecke von Visual Studio **Zur Quellcodeverwaltung hinzufügen** und dann **Git** auswählen. 

2. Wählen Sie in der Ansicht **Synchronisation** in **Team Explorer** unter **Mithilfe von Push in Azure DevOps Services übertragen** die Schaltfläche **Git-Repository veröffentlichen**.

   ![Git-Repository pushen](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishgitrepo.png)

3. Überprüfen Sie Ihre E-Mail-Adresse, und wählen Sie in der Dropdownliste **Azure DevOps Services-Domäne** Ihr Konto aus. Geben Sie den Repositorynamen ein, und wählen Sie **Repository veröffentlichen** aus.

   ![Git-Repository pushen](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishcode.png)

    Durch das Veröffentlichen des Repositorys wird in Ihrer Organisation ein neues Projekt mit dem gleichen Namen wie das lokale Repository erstellt. Um das Repository in einem vorhandenen Projekt zu erstellen, klicken Sie neben **Repositoryname** auf **Erweitert**, und wählen Sie ein Projekt aus. Sie können den Code im Browser anzeigen, indem Sie **Im Web anzeigen** auswählen.
 
## <a name="configure-continuous-delivery-with-azure-devops"></a>Konfigurieren von Continuous Delivery mit Azure DevOps
Eine Azure Pipelines-Buildpipeline beschreibt einen Workflow, der aus Buildschritten besteht, die nacheinander ausgeführt werden. Erfahren Sie mehr über [Azure Pipelines-Buildpipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav). 

Eine Azure Pipelines-Releasepipeline beschreibt einen Workflow, der ein Anwendungspaket in einem Cluster bereitstellt. Bei gemeinsamer Verwendung führen die Buildpipeline und Releasepipeline den gesamten Workflow aus, und zwar beginnend mit den Quelldateien und endend mit einer im Cluster ausgeführten Anwendung. Erfahren Sie mehr über [Azure Pipelines-Releasepipelines](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts).

### <a name="create-a-build-pipeline"></a>Erstellen einer Buildpipeline
Öffnen Sie einen Webbrowser, und navigieren Sie zu dem Projekt, das Sie gerade in [Azure DevOps](https://app.vsaex.visualstudio.com/) erstellt haben. 

1. Wählen Sie auf der Registerkarte **Build und Release** die Option **Builds** und dann **+Neu** aus.  Wählen Sie **Azure DevOps Services-Git** und **Weiter**.
    
    ![Quelle auswählen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source.png)

2. Klicken Sie unter **Vorlage auswählen** auf **Leerer Prozess**, um mit einer leeren Pipeline zu beginnen.
    
    ![Auswählen der Buildvorlage](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template.png)

3. Aktivieren Sie unter **Trigger** Continuous Integration, indem Sie den Triggerstatus **Continuous Integration aktivieren** auswählen.  Klicken Sie auf **Speichern und in Warteschlange einreihen**, um manuell einen Buildvorgang zu starten. 
    
    ![Triggerstatus](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger.png)

4. Buildvorgänge werden auch beim Pushvorgang oder Einchecken ausgelöst. Wechseln Sie zum Überprüfen des Buildstatus zur Registerkarte **Builds**.  Nachdem Sie überprüft haben, ob der Build erfolgreich ausgeführt wird, müssen Sie eine Releasepipeline festlegen, mit der die Anwendung in einem Cluster bereitgestellt wird. Klicken Sie mit der rechten Maustaste auf die Auslassungszeichen neben der Buildpipeline, und wählen Sie **Bearbeiten** aus.

5.  Geben Sie unter **Aufgaben** für **Agent-Warteschlange** die Zeichenfolge „Hosted“ ein.
    
    ![Agent-Warteschlange auswählen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue.png) 

6. Klicken Sie im Abschnitt **Phase 1** auf **+**, und fügen Sie eine Aufgabe vom Typ **NuGet** hinzu.
    
    ![NuGet-Aufgabe hinzufügen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget.png)

7. Erweitern Sie den Bereich **Erweitert**, und fügen Sie `$(Build.SourcesDirectory)\packages` zu **Zielverzeichnis** hinzu. Behalten Sie die übrigen Standardwerte für die NuGet-Konfiguration bei.

   ![NuGet-Aufgabe konfigurieren](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-config.png)

8. Klicken Sie im Abschnitt **Phase 1** auf **+**, und fügen Sie eine Aufgabe vom Typ **MSBuild** hinzu.

   ![MSBuild-Aufgabe hinzufügen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild-task.png)

9. Ändern Sie die **MSBuild-Argumente** wie folgt:

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![MSBuild-Aufgabe konfigurieren](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild.png)

10. Klicken Sie im Abschnitt **Phase 1** auf **+**, und fügen Sie eine Aufgabe vom Typ **Bereitstellung einer Azure-Ressourcengruppe** hinzu. 
    
    ![Aufgabe „Bereitstellung einer Azure-Ressourcengruppe“ hinzufügen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy.png)

11. Erweitern Sie **Azure-Details**, und tragen Sie Folgendes in die Konfiguration ein:
    
    |**Einstellung**  |**Empfohlener Wert**  |
    |---------|---------|
    |Abonnement  |  Wählen Sie Ihr Abonnement aus.   |
    |Aktion  |  Erstellen oder aktualisieren Sie eine Ressourcengruppe.   |
    |Ressourcengruppe  |  Geben Sie einen Ressourcengruppennamen ein.   |
    |Vorlage  | [Ihr Projektmappenpfad]\bin\Debug\Deploy\\[Ihr Projektname].JobTemplate.json   |
    |Vorlagenparameter  | [Ihr Projektmappenpfad]\bin\Debug\Deploy\\[Ihr Projektname].JobTemplate.parameters.json   |
    |Vorlagenparameter überschreiben  | Geben Sie die zu überschreibenden Vorlagenparameter in das Textfeld ein. Beispiel: –storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre). Diese Eigenschaft ist optional, doch ergibt der Build Fehler, wenn Schlüsselparameter nicht überschrieben werden.    |
    
    ![Eigenschaften festlegen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy-2.png)

12. Klicken Sie auf **Speichern und in Warteschlange einreihen**, um die Buildpipeline zu testen.
    
    ![Parameterüberschreibung festlegen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-queue.png)

### <a name="failed-build-process"></a>Fehlerhafter Buildprozess
Möglicherweise werden Ihnen Fehler für NULL-Bereitstellungsparameter angezeigt, wenn Sie Vorlagenparameter in der Aufgabe **Bereitstellung einer Azure-Ressourcengruppe** Ihrer Buildpipeline nicht überschrieben haben. Kehren Sie zur Buildpipeline zurück, und überschreiben Sie die NULL-Parameter, um den Fehler zu beheben.

   ![Buildprozess fehlerhaft](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Committen und Pushen von Änderungen zum Auslösen eines Release
Checken Sie einige Codeänderungen in Azure DevOps ein, um zu überprüfen, ob die Continuous Integration-Pipeline ordnungsgemäß ausgeführt wird.    

Während Sie den Code schreiben, werden Ihre Änderungen automatisch von Visual Studio nachverfolgt. Führen Sie den Commit der Änderungen in das lokale Git-Repository aus, indem Sie auf der Statusleiste unten rechts das Symbol „Ausstehende Änderungen“ auswählen.

1. Fügen Sie in der Ansicht **Änderungen** in Team Explorer eine Nachricht hinzu, die die Aktualisierung beschreibt, und führen Sie den Commit der Änderungen aus.

    ![Änderungen committen und pushen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes.png)

2. Wählen Sie das Statusleistensymbol für nicht veröffentlichte Änderungen oder die Synchronisierungsansicht in Team Explorer aus. Wählen Sie **Push** aus, um den Code in Azure DevOps zu aktualisieren.

    ![Änderungen committen und pushen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-2.png)

Durch das Pushen der Änderungen nach Azure DevOps wird automatisch ein Build ausgelöst.  Nach erfolgreichem Abschluss der Buildpipeline wird automatisch ein Release erstellt, und es wird eine Aktualisierung des Auftrags im Cluster gestartet.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, den Streamingauftrag und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Durch das Löschen des Auftrags verhindern Sie, dass Kosten für die vom Auftrag verbrauchten Streamingeinheiten anfallen. Wenn Sie den Auftrag später erneut verwenden möchten, können Sie ihn beenden und bei Bedarf neu starten. Wenn Sie diesen Auftrag nicht mehr verwenden möchten, löschen Sie alle Ressourcen, die im Rahmen dieses Tutorials erstellt wurden, mithilfe der folgenden Schritte:

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Ressourcengruppen**, und klicken Sie auf den Namen der erstellten Ressource.  
2. Klicken Sie auf der Seite mit Ihrer Ressourcengruppe auf **Löschen**, geben Sie im Textfeld den Namen der zu löschenden Ressource ein, und klicken Sie dann auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

Um weitere Informationen zur Verwendung von Azure Stream Analytics-Tools für Visual Studio zum Einrichten von Continuous Integration und Continuous Deployment zu erhalten, fahren Sie mit dem Artikel zum Einrichten von CI-/CD-Pipelines fort:

> [!div class="nextstepaction"]
> [Kontinuierliche Integration und Entwicklung mit Stream Analytics-Tools](stream-analytics-tools-for-visual-studio-cicd.md)
