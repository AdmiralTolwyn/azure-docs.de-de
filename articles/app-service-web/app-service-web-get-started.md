<properties 
	pageTitle="Erste Schritte mit Web-Apps in Azure App Service" 
	description="Erfahren Sie, wie einfach es ist, Ihre Web-App live in App Service auszuführen. Sie können innerhalb von fünf Minuten mit der Entwicklung beginnen und sofort Ergebnisse erzielen." 
	services="app-service\web"
	documentationCenter=""
	authors="cephalin" 
	manager="wpickett" 
	editor="" 
/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article"
	ms.date="04/04/2016" 
	ms.author="cephalin"
/>
	
# Erste Schritte mit Web-Apps in Azure App Service

In diesem Tutorial werden die ersten Schritte als schneller Start für die Bereitstellung von Web-Apps für [Azure App Service](../app-service/app-service-value-prop-what-is.md) beschrieben. Sie führen ohne großen Aufwand Folgendes durch:

- Bereitstellen einer Beispiel-Web-App (Auswahl: ASP.NET, PHP, Node.js, Java oder Python)
- Liveschalten Ihrer App in wenigen Sekunden
- Aktualisieren der Web-App auf dieselbe Weise wie bei der Pushübertragung von [Git](http://www.git-scm.com/)-Commits

Außerdem erhalten Sie einen ersten Einblick in das [Azure-Portal](https://portal.azure.com) und die darin verfügbaren Features.

## Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Git. Sie können die Binärdateien für die Installation [hier](http://www.git-scm.com/downloads) herunterladen. Sie sollten `git --version` über das Befehlszeilenterminal Ihrer Wahl ausführen können. 
- Git-Grundkenntnisse.
- Azure-Befehlszeilenschnittstelle. Eine Installationsanleitung finden Sie [hier](../xplat-cli-install.md). Sie sollten `azure --version` über das Befehlszeilenterminal Ihrer Wahl ausführen können.
- Ein Microsoft Azure-Konto. Wenn Sie kein Konto haben, können Sie sich [für eine kostenlose Testversion anmelden](/pricing/free-trial/?WT.mc_id=A261C142F) oder [Ihre Visual Studio-Abonnentenvorteile aktivieren](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Unter [Azure App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) können Sie Azure App Service in Aktion erleben, bevor Sie sich für ein Azure-Konto registrieren. Dort können Sie sofort eine kurzzeitige Start-App in App Service erstellen – ohne Kreditkarte und weitere Verpflichtungen.

## Bereitstellen einer Web-App

Wir stellen jetzt eine Web-App unter Azure App Service bereit.

1. Öffnen Sie eine neue Windows-Eingabeaufforderung, eine Linux-Shell oder ein OS X-Terminal, und wechseln Sie mit `CD` in ein Arbeitsverzeichnis. Darin können Sie die Beispiel-App wie folgt klonen:

        git clone <github_sample_url>

    Verwenden Sie für *&lt;github\_sample\_url>* je nach gewünschtem Framework eine der folgenden URLs:

    - ASP.NET: [https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git](https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git)
    - PHP (CodeIgniter): [https://github.com/Azure-Samples/app-service-web-php-get-started.git](https://github.com/Azure-Samples/app-service-web-php-get-started.git)
    - Node.js (Express): [https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git](https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git) 
    - Java: [https://github.com/Azure-Samples/app-service-web-java-get-started.git](https://github.com/Azure-Samples/app-service-web-java-get-started.git)
    - Python (Django): [https://github.com/Azure-Samples/app-service-web-python-get-started.git](https://github.com/Azure-Samples/app-service-web-python-get-started.git)

2. Wechseln Sie mit `CD` in das Stammverzeichnis Ihrer Beispiel-App. Beispiel:

        cd app-service-web-dotnet-get-started

3. Melden Sie sich wie folgt an Azure an:

        azure login
    
    Befolgen Sie die Aufforderung, um die Anmeldung in einem Browser mit einem Microsoft-Konto fortzusetzen, das über Ihr Azure-Abonnement verfügt.

4. Erstellen Sie die App Service-App-Ressource in Azure mit einem eindeutigen App-Namen mit dem nächsten Befehl.

        azure site create --git <app_name>
      
    >[AZURE.NOTE] Wenn Sie für Ihr Azure-Abonnement noch nie zuvor Anmeldeinformationen für die Bereitstellung eingerichtet haben, werden Sie aufgefordert, diese zu erstellen. Diese Anmeldeinformationen und nicht Ihre Anmeldeinformationen für das Azure-Konto werden von App Service ausschließlich für Git-Bereitstellungen und FTP-Anmeldungen verwendet.
    
    Ihre App wird jetzt in Azure erstellt. Ihr aktuelles Verzeichnis wird außerdem für Git initialisiert und mit der neuen App Service-App als Git-Remoteelement verbunden. Sie können zur URL der App (http://&lt;app_name>.azurewebsites.net) navigieren, um die schöne HTML-Standardseite anzuzeigen, aber wir möchten nun erreichen, dass dort Ihr eigener Code verwendet wird.

4. Stellen Sie Ihren Beispielcode jetzt für die neue App Service-App bereit, wie Sie dies auch für anderen Code per Pushübertragung mit Git durchführen würden:

        git push azure master 
    
    >[AZURE.NOTE] Sie werden zum Eingeben Ihres Bereitstellungskennworts aufgefordert. Wenn App Service neu für Sie ist, können Sie das eben erstellte Bereitstellungskennwort eingeben, um zu beginnen.
    
    Mit `git push` wird nicht nur Code in Azure eingefügt, sondern es werden auch Bereitstellungsaufgaben im Bereitstellungsmodul ausgelöst. Falls Ihr Projektstamm (bzw. Repositorystamm) das Element „package.json“ (Node.js) oder „requirements.txt“ (Python) enthält oder falls Ihr ASP.NET-Projekt das Element „packages.config“ enthält, stellen die Bereitstellungsskripts die erforderlichen Pakete wieder für Sie her. Sie können auch die [Composer-Erweiterung aktivieren](web-sites-php-mysql-deploy-use-git.md#composer), um Dateien vom Typ „composer.json“ in Ihrer PHP-App automatisch zu verarbeiten.

Herzlichen Glückwunsch! Sie haben Ihre App in Azure App Service bereitgestellt.

## Verfolgen der Liveausführung der App

Führen Sie den folgenden Befehl aus einem beliebigen Verzeichnis in Ihrem Repository aus, um die Liveausführung der App in Azure zu verfolgen:

    azure site browse

## Durchführen von Updates für die App

Sie können jetzt Git verwenden, um aus Ihrem Projektstamm (Repositorystamm) jederzeit einen Pushvorgang durchzuführen und so ein Update für die Live-Website vorzunehmen. Dies entspricht der Vorgehensweise, die Sie bei der ersten Bereitstellung Ihrer App für Azure genutzt haben. Wenn Sie beispielsweise eine neue Änderung übertragen möchten, die Sie lokal getestet haben, führen Sie einfach die folgenden Befehle in Ihrem Projektstamm (Repositorystamm) aus:
    
    git add .
    git commit -m "<your_message>"
    git push azure master

## Andere Bereitstellungsmöglichkeiten

Sie haben verschiedene Möglichkeiten, wie Sie Ihre Web-App bereitstellen. Die Git-Bereitstellung aus einem lokalen Repository ist nur eine dieser Möglichkeiten. Sie können die Bereitstellung direkt aus Visual Studio oder fortlaufend über GitHub durchführen, über DropBox oder OneDrive synchronisieren, Dateien per FTP hochladen usw. Weitere Informationen zu Bereitstellungsoptionen finden Sie unter [Bereitstellen der App in Azure App Service](../app-service-web/web-sites-deploy.md).

## Anzeigen der App im Azure-Portal

Wir wechseln jetzt zum Azure-Portal, um anzuzeigen, was Sie erstellt haben:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) mit einem Microsoft-Konto an, das über Ihr Azure-Abonnement verfügt.

2. Klicken Sie in der Leiste auf der linken Seite auf **App Services**.

3. Klicken Sie auf die App, die Sie gerade erstellt haben, um die dazugehörige Seite im Portal (als [Blatt](../azure-portal-overview.md) bezeichnet) zu öffnen. Das Blatt **Einstellungen** wird ebenfalls standardmäßig geöffnet, um die Benutzerfreundlichkeit zu erhöhen.

    ![Anzeige der ersten App im Portal unter Azure App Service](./media/app-service-web-get-started/portal-view.png)

Das Portalblatt Ihrer App Service-App enthält viele Einstellungen und Tools, mit denen Sie die App konfigurieren, überwachen und schützen und die Problembehandlung durchführen können. Machen Sie sich kurz mit dieser Oberfläche vertraut, indem Sie einige einfache Aufgaben durchführen:

- Beenden Sie die App.
- Starten Sie die App neu.
- Klicken Sie auf den Link **Ressourcengruppe**, um alle in der Ressourcengruppe bereitgestellten Ressourcen anzuzeigen.
- Klicken Sie auf **Einstellungen** > **Eigenschaften**, um weitere Informationen zur App anzuzeigen.
- Klicken Sie auf **Extras**, um auf nützliche Tools zur Überwachung und für die Problembehandlung zuzugreifen.  

## Nächste Schritte

Erweitern Sie die bereitgestellte App auf die nächste Ebene. Schützen Sie die App per Authentifizierung. Skalieren Sie die App je nach Bedarf. Richten Sie einige Leistungswarnungen ein. Es sind jeweils nur wenige Klickvorgänge erforderlich. Weitere Informationen finden Sie unter [Erste Schritte mit Azure App Service – Teil 2](app-service-web-get-started-2.md).

Sie können sich auch weiter darüber informieren, wie Sie eine Web-App für App Service mit einem speziellen Sprachframework erstellen:

- [Erstellen von ASP.NET-Web-Apps in Azure App Service](web-sites-dotnet-get-started.md)
- [Erstellen einer PHP-Web-App in Azure App Service](web-sites-php-mysql-deploy-use-git.md)
- [Erstellen einer Node.js-Web-App in Azure App Service](web-sites-nodejs-develop-deploy-mac.md)
- [Erstellen einer Java Web-App in Azure App Service](web-sites-java-get-started.md)
- [Erstellen einer Python-Web-App in Azure App Service](web-sites-python-ptvs-django-mysql.md)

Es sind auch weitere Inhalte dazu verfügbar, welche unterschiedlichen Apps Sie mit Azure App Service erstellen können, z.B. Web-Apps, Back-Ends für mobile Apps und API-Apps.

- [Erstellen von Web-Apps](/documentation/learning-paths/appservice-webapps/)
- [Erstellen von mobilen Apps](/documentation/learning-paths/appservice-mobileapps/)
- [Erstellen von API-Apps](../app-service-api/app-service-api-apps-why-best-platform.md)

<!----HONumber=AcomDC_0420_2016-->