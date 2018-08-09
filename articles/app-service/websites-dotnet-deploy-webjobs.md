---
title: Entwickeln und Bereitstellen von WebJobs mit Visual Studio – Azure
description: Erfahren Sie, wie Sie Azure WebJobs mithilfe von Visual Studio in Azure App Service entwickeln und bereitstellen.
services: app-service
documentationcenter: ''
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: ccbf1241012fbac184de2fb0109dab4b5e618a52
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579118"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Entwickeln und Bereitstellen von WebJobs mit Visual Studio – Azure App Service

## <a name="overview"></a>Übersicht

In diesem Thema wird erläutert, wie Sie Visual Studio zum Bereitstellen eines Konsolenanwendungsprojekts in einer [App Service](app-service-web-overview.md)-Web-App als [Azure-WebJob](http://go.microsoft.com/fwlink/?LinkId=390226) verwenden. Weitere Informationen zum Bereitstellen von WebJobs mit dem [Azure-Portal](https://portal.azure.com) finden Sie unter [Ausführen von Hintergrundaufgaben mit WebJobs](web-sites-create-web-jobs.md).

Wenn mithilfe von Visual Studio ein webauftragsfähiges Konsolenanwendungsprojekt bereitgestellt wird, werden zwei Aufgaben ausgeführt:

* Laufzeitdateien werden in den entsprechenden Ordner der Web-App (*App_Data/jobs/continuous* für fortlaufende WebJobs, *App_Data/jobs/triggered* für geplante und bedarfsabhängige WebJobs) kopiert.
* Es werden [Azure Scheduler-Aufträge](https://docs.microsoft.com/azure/scheduler/) für WebJobs eingerichtet, die für die Ausführung zu bestimmten Zeiten geplant sind. (Dies ist für fortlaufende Webaufträge nicht erforderlich.)

Einem webauftragsfähigen Projekt werden die folgenden Elemente hinzugefügt:

* Das NuGet-Paket [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/)
* Die Datei [webjob-publish-settings.json](#publishsettings) mit Bereitstellungs- und Zeitplanungseinstellungen 

![Abbildung, die zeigt, was einer Konsolenanwendung zum Ermöglichen der Bereitstellung als Webauftrag hinzugefügt wird](./media/websites-dotnet-deploy-webjobs/convert.png)

Sie können diese Elemente einem vorhandenen Konsolenanwendungsprojekt hinzufügen oder eine Vorlage nutzen, um ein neues webauftragsfähiges Konsolenanwendungsprojekt zu erstellen. 

Sie können ein Projekt als eigenständigen Webauftrag bereitstellen oder es mit einem Webprojekt verknüpfen, sodass es automatisch bereitgestellt wird, wenn Sie das Webprojekt bereitstellen. Zum Verknüpfen von Projekten fügt Visual Studio den Namen des webauftragsfähigen Projekts der Datei [webjobs-list.json](#webjobslist) im Webprojekt hinzu.

![Abbildung der Verknüpfung eines Webauftragsprojekts mit einem Webprojekt](./media/websites-dotnet-deploy-webjobs/link.png)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie Visual Studio 2015 verwenden, installieren Sie das [Azure SDK für .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Wenn Sie Visual Studio 2017 verwenden, installieren Sie die [Workload „Azure-Entwicklung“](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads).

## <a id="convert"></a> Aktivieren der WebJobs- Bereitstellung für ein vorhandenes Konsolenanwendungsprojekt

Sie haben zwei Möglichkeiten:

* [Aktivieren der automatischen Bereitstellung mit einem Webprojekt](#convertlink).

  Konfigurieren Sie ein vorhandenes Konsolenanwendungsprojekt so, dass es automatisch als Webauftrag bereitgestellt wird, wenn Sie ein Webprojekt bereitstellen. Wählen Sie diese Option, wenn Sie Ihren Webauftrag in derselben Web-App ausführen möchten, in der die dazugehörige Webanwendung ausgeführt wird.

* [Ermöglichen der Bereitstellung ohne Webprojekt](#convertnolink).

  Konfigurieren Sie ein vorhandenes Konsolenanwendungsprojekt für die Bereitstellung als eigenständiger Webauftrag ohne Verknüpfung mit einem Webprojekt. Wählen Sie diese Option, wenn Sie einen Webauftrag eigenständig ausführen möchten, ohne dass eine Webanwendung in der Web-App ausgeführt wird. Dies empfiehlt sich, wenn Sie Ihre Webauftragsressourcen unabhängig von Ihren Webanwendungsressourcen skalieren möchten.

### <a id="convertlink"></a> Aktivieren der automatischen Bereitstellung von Webaufträgen mit einem Webprojekt

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Webprojekt. Klicken Sie dann auf **Hinzufügen** > **Vorhandenes Projekt als Azure WebJob**.
   
    ![Vorhandenes Projekt als Azure-Webauftrag](./media/websites-dotnet-deploy-webjobs/eawj.png)
   
    Das Dialogfeld [Azure-Webauftrag hinzufügen](#configure) wird angezeigt.
2. Wählen Sie in der Dropdownliste **Projektname** das Konsolenanwendungsprojekt aus, das als Webauftrag hinzugefügt werden soll.
   
    ![Auswählen des Projekts im Dialogfeld "Azure-Webauftrag hinzufügen"](./media/websites-dotnet-deploy-webjobs/aaw1.png)
3. Vervollständigen Sie das Dialogfeld [Azure-Webauftrag hinzufügen](#configure) , und klicken Sie dann auf **OK**. 

### <a id="convertnolink"></a> Aktivieren der Bereitstellung von Webaufträgen ohne Webprojekt
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Konsolenanwendungsprojekt, und klicken Sie dann auf **Als Azure-WebJob veröffentlichen...**. 
   
    ![Als Azure-Webauftrag veröffentlichen](./media/websites-dotnet-deploy-webjobs/paw.png)
   
    Das Dialogfeld [Azure-Webauftrag hinzufügen](#configure) wird mit dem im Feld **Projektname** ausgewählten Projekt angezeigt.
2. Vervollständigen Sie das Dialogfeld [Azure-Webauftrag hinzufügen](#configure) , und klicken Sie dann auf **OK**.
   
   Der Assistent **Web veröffentlichen** wird geöffnet.  Wenn Sie keine sofortige Veröffentlichung wünschen, schließen Sie den Assistenten. Die eingegebenen Einstellungen werden für den Zeitpunkt der gewünschten [Bereitstellung des Projekts](#deploy)gespeichert.

## <a id="create"></a>Erstellen eines neuen webauftragsfähigen Projekts
Zum Erstellen eines neuen webauftragsfähigen Projekts können Sie die Vorlage für Konsolenanwendungsprojekte verwenden und die Bereitstellung von Webaufträgen gemäß den Erläuterungen im [vorherigen Abschnitt](#convert)ermöglichen. Alternativ können Sie die Webauftragsvorlage "new-project" nutzen:

* [Verwenden der Webauftragsvorlage "new-project" für einen unabhängigen Webauftrag](#createnolink)
  
    Erstellen Sie ein Projekt, und konfigurieren Sie es für die Bereitstellung als eigenständiger Webauftrag ohne Verknüpfung mit einem Webprojekt. Wählen Sie diese Option, wenn Sie einen Webauftrag eigenständig ausführen möchten, ohne dass eine Webanwendung in der Web-App ausgeführt wird. Dies empfiehlt sich, wenn Sie Ihre Webauftragsressourcen unabhängig von Ihren Webanwendungsressourcen skalieren möchten.
* [Verwenden der Webauftragsvorlage "new-project" für einen mit einem Webprojekt verknüpften Webauftrag](#createlink)
  
    Erstellen Sie ein Projekt, das für eine automatische Bereitstellung als Webauftrag konfiguriert ist, wenn in derselben Projektmappe ein Webprojekt bereitgestellt wird. Wählen Sie diese Option, wenn Sie Ihren Webauftrag in derselben Web-App ausführen möchten, in der die dazugehörige Webanwendung ausgeführt wird.

> [!NOTE]
> Die WebJobs-Vorlage „new-project“ installiert automatisch NuGet-Pakete und enthält in *Program.cs* Code für das [WebJobs-SDK](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Wenn Sie das WebJobs-SDK nicht verwenden möchten, entfernen oder ändern Sie die `host.RunAndBlock`-Anweisung in *Program.cs*.
> 
> 

### <a id="createnolink"></a> Verwenden der Webauftragsvorlage "new-project" für einen unabhängigen Webauftrag
1. Klicken Sie auf **Datei** > **Neues Projekt** und anschließend im Dialogfeld **Neues Projekt** auf **Cloud** > **Azure WebJob (.NET Framework)**.
   
    ![Dialogfeld "Neues Projekt" mit Webauftragsvorlage](./media/websites-dotnet-deploy-webjobs/np.png)
2. Befolgen Sie die zuvor gezeigten Anweisungen, um [das Konsolenanwendungsprojekt als unabhängiges Webauftragsprojekt zu erstellen](#convertnolink).

### <a id="createlink"></a> Verwenden der Webauftragsvorlage "new-project" für einen mit einem Webprojekt verknüpften Webauftrag
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Webprojekt. Klicken Sie dann auf **Hinzufügen** > **Neues Azure WebJob-Projekt**.
   
    ![Menüeintrag "Neues Azure-Webauftragsprojekt"](./media/websites-dotnet-deploy-webjobs/nawj.png)
   
    Das Dialogfeld [Azure-Webauftrag hinzufügen](#configure) wird angezeigt.
2. Vervollständigen Sie das Dialogfeld [Azure-Webauftrag hinzufügen](#configure) , und klicken Sie dann auf **OK**.

## <a id="configure"></a>Das Dialogfeld "Azure-Webauftrag hinzufügen"
Im Dialogfeld **Azure-WebJob hinzufügen** können Sie den WebJob-Namen eingeben und die Moduseinstellung für Ihren WebJob ausführen. 

![Dialogfeld "Azure-Webauftrag hinzufügen"](./media/websites-dotnet-deploy-webjobs/aaw2.png)

Die Felder in diesem Dialogfeld entsprechen den Feldern im Dialogfeld **WebJob hinzufügen** im Azure-Portal. Weitere Informationen finden Sie unter [Ausführen von Hintergrundaufgaben mit Webaufträgen](web-sites-create-web-jobs.md).

> [!NOTE]
> * Informationen zur Befehlszeilenbereitstellung finden Sie unter [Aktivieren der befehlszeilengesteuerten oder kontinuierlichen Bereitstellung von Azure-Webaufträgen](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Wenn Sie einen WebJob bereitstellen und dann entscheiden, den Typ des WebJobs zu ändern und diesen erneut bereitzustellen, müssen Sie die Datei *webjobs-publish-settings.json* löschen. Dies veranlasst Visual Studio, die Optionen für die Veröffentlichung erneut anzeigen, sodass Sie den Typ des Webauftrags ändern können.
> * Wenn Sie einen Webauftrag bereitstellen und den Ausführungsmodus später von fortlaufend in nicht fortlaufend ändern oder umgekehrt, erstellt Visual Studio bei der erneuten Bereitstellung einen Webauftrag in Azure. Wenn Sie andere Zeitplaneinstellungen ändern, ohne den Ausführungsmodus zu wechseln, oder zwischen "Geplant" und "Bedarfsgesteuert" wechseln, aktualisiert Visual Studio den vorhandenen Auftrag, ohne einen neuen zu erstellen.
> 
> 

## <a id="publishsettings"></a>webjob-publish-settings.json
Wenn Sie eine Konsolenanwendung für die Bereitstellung von WebJobs konfigurieren, installiert Visual Studio das NuGet-Paket [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) und speichert Zeitplanungsinformationen in der Datei *webjob-publish-settings.json* im Ordner *Eigenschaften* des WebJob-Projekts. Hier ist ein Beispiel dieser Datei:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Sie können diese Datei direkt bearbeiten, und Visual Studio stellt IntelliSense zur Verfügung. Das Dateischema wird unter [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) gespeichert und kann dort angezeigt werden.  

## <a id="webjobslist"></a>webjobs-list.json
Wenn Sie ein WebJob-fähiges Projekt mit einem Webprojekt verknüpfen, speichert Visual Studio den Namen des WebJob-Projekts in der Datei *webjobs-list.json* im Ordner *Eigenschaften* des Webprojekts. Die Liste kann mehrere WebJobs-Projekte umfassen, wie im folgenden Beispiel gezeigt wird:

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

Sie können diese Datei direkt bearbeiten, und Visual Studio stellt IntelliSense zur Verfügung. Das Dateischema wird unter [http://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) gespeichert und kann dort angezeigt werden.

## <a id="deploy"></a>Bereitstellen eines Webauftragsprojekts
Ein Webauftragsprojekt, das Sie mit einem Webprojekt verknüpft haben, wird automatisch mit dem Webprojekt bereitstellt. Informationen zur Bereitstellung von Webprojekten finden Sie unter **Anleitungen** > **App bereitstellen** im linken Navigationsbereich.

Klicken Sie zum Bereitstellen eines WebJobs-Projekts im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt. Klicken Sie dann auf **Als Azure-WebJob veröffentlichen...**. 

![Als Azure-Webauftrag veröffentlichen](./media/websites-dotnet-deploy-webjobs/paw.png)

Bei einem unabhängigen Webauftrag wird derselbe Assistent **Web veröffentlichen** wie bei Webprojekten angezeigt, wobei allerdings weniger Einstellungen geändert werden können.
