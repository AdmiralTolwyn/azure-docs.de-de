<properties 
	pageTitle="Erstellen einer „Hello World“-Web-App für Azure in Eclipse" 
	description="In diesem Tutorial erfahren Sie, wie Sie mit dem Azure-Toolkit für Eclipse eine „Hello World“-Web-App für Azure erstellen." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/24/2016" 
	ms.author="robmcm"/>

# Erstellen einer „Hello World“-Web-App für Azure in Eclipse

Dieses Tutorial zeigt das Erstellen und Bereitstellen einer einfachen „Hello World“-Anwendung in Azure als Web-App mithilfe des [Azure-Toolkits für Eclipse]. Zur Vereinfachung wird ein grundlegendes JSP-Beispiel verwendet, aber mit einer ganz ähnlichen Vorgehensweise können Sie auch ein Java-Servlet in Azure bereitstellen.

Wenn Sie dieses Tutorial abgeschlossen haben, entspricht Ihre Anwendung bei der Anzeige in einem Webbrowser etwa der folgenden Abbildung:

![][01]
 
## Voraussetzungen

* Ein Java Developer Kit (JDK), Version 1.7 oder höher.
* Eclipse IDE für Java EE-Entwickler, Indigo oder höher. Dies kann von <http://www.eclipse.org/downloads/> heruntergeladen werden.
* Eine Verteilung eines Java-basierten Webservers oder Anwendungsservers, wie z. B. Apache Tomcat oder Jetty.
* Ein Azure-Abonnement, das von <https://azure.microsoft.com/de-DE/free/> oder <http://azure.microsoft.com/pricing/purchase-options/> bezogen werden kann.
* Das Azure-Toolkit für Eclipse. Weitere Informationen finden Sie unter [Installation des Azure Toolkit für Eclipse].

## So erstellen Sie eine „Hello World“-Anwendung

Zunächst beginnen wir mit der Erstellung eines Java-Projekts.

1. Starten Sie Eclipse, klicken Sie im Menü auf **File** (Datei), auf **New** (Neu) und anschließend auf **Dynamic Web Project** (Dynamisches Webprojekt). (Wenn **Dynamic Web Project** (Dynamisches Webprojekt) nach dem Klicken auf **File** (Datei) und **New** (Neu) nicht als verfügbares Projekt aufgeführt ist, gehen Sie wie folgt vor: Klicken Sie auf **File** (Datei), anschließend auf **New** (Neu) und dann auf **Project...** (Projekt...). Erweitern Sie die Option **Web**, klicken Sie auf **Dynamic Web Project** (Dynamisches Webprojekt) und dann auf **Next** (Weiter).)

1. Nennen Sie das Projekt für die Zwecke dieses Tutorials **MyHelloWorld**. Ihr Bildschirm sieht dann in etwa wie folgt aus:

    ![][02]

1. Klicken Sie auf **Fertig stellen**.

1. Erweitern Sie in der Projektexplorer-Ansicht von Eclipse die Option **MyHelloWorld**. Klicken Sie mit der rechten Maustaste auf **WebContent**, und klicken Sie dann auf **Neu** sowie auf **JSP-Datei**.

1. Geben Sie der Datei im Dialogfeld **Neue JSP-Datei** den Namen **index.jsp**. Nennen Sie den übergeordneten Ordner **MyHelloWorld/WebContent**.

1. Wählen Sie im Dialogfeld **Select JSP Template** (JSP-Vorlage auswählen) im Rahmen dieses Tutorials **New JSP File (html)** (Neue JSP-Datei (HTML)) aus, und klicken Sie dann auf **Fertig stellen**.

1. Wenn in Eclipse die Datei „index.jsp“ geöffnet wird, geben Sie den Text **Hello World!** ein, damit er dynamisch im vorhandenen `<body>`-Element angezeigt wird. Der aktualisierte `<body>`-Inhalt sollte in etwa wie folgt aussehen:

    `<body><b><% out.println("Hello World!"); %></b></body>` 

1. Speichern Sie die Datei „index.jsp“.

## So stellen Sie Ihre Anwendung in einem Azure-Web-App-Container bereit

Es gibt mehrere Möglichkeiten, eine Java-Webanwendung in Azure bereitzustellen. In diesem Tutorial wird eine der einfachsten beschrieben: Ihre Anwendung wird in einem Azure-Web-App-Container bereitgestellt – weder ein spezieller Projekttyp noch zusätzliche Tools sind erforderlich. Das JDK und die Webcontainersoftware werden von Azure für Sie bereitgestellt, sodass Sie weder ein eigenes JDK noch eigene Webcontainersoftware hochladen müssen; Sie benötigen lediglich Ihre Java Web-App. Darum dauert der Veröffentlichungsprozess für Ihre Anwendung nur Sekunden, nicht Minuten.

1. Klicken Sie im Projektexplorer von Eclipse mit der rechten Maustaste auf **MyHelloWorld**.

1. Wählen Sie im Kontextmenü **Azure** aus, und klicken Sie dann auf **Publish as Azure Web App...** (Als Azure-Web-App veröffentlichen...).

    ![][03]
   
    Alternativ können Sie auch das Webanwendungsprojekt im Projektexplorer auswählen und auf der Symbolleiste auf die Dropdownschaltfläche **Veröffentlichen** klicken. Wählen Sie anschließend **Publish as Azure Web App** (Als Azure-Web-App veröffentlichen) aus:
   
    ![][14]
   
1. Wenn Sie sich noch nicht von Eclipse aus bei Azure angemeldet haben, werden Sie aufgefordert, sich bei Ihrem Azure-Konto anzumelden:

    ![][04]
   
    Hinweis: Wenn Sie über mehrere Azure-Konten verfügen, können einige der Eingabeaufforderungen während des Anmeldeprozesses mehrmals angezeigt werden, auch wenn sie scheinbar identisch sind. Befolgen Sie in diesem Fall weiterhin die Anweisungen zur Anmeldung.
1. Nachdem Sie sich erfolgreich bei Ihrem Azure-Konto angemeldet haben, wird im Dialogfeld **Manage Subscriptions** (Abonnements verwalten) eine Liste der Abonnements angezeigt, die mit Ihren Anmeldeinformationen verknüpft sind. Wenn mehrere Abonnements aufgeführt sind und Sie nur mit einer bestimmten Teilmenge davon arbeiten möchten, können Sie optional die deaktivieren, die Sie nicht verwenden möchten. Wenn Sie Ihre Abonnements ausgewählt haben, klicken Sie auf **Close** (Schließen).

    ![][05]
   
1. Wenn das Dialogfeld **Deploy to Azure Web App Container** (In Azure-Web-App-Container bereitstellen) angezeigt wird, werden alle Web-App-Container angezeigt, die Sie zuvor erstellt haben. Wenn Sie keine Container erstellt haben, ist die Liste leer.

    ![][06]
   
1. Wenn Sie zuvor keinen Azure-Web-App-Container erstellt haben, oder Sie Ihre Anwendung in einem neuen Container veröffentlichen möchten, führen Sie die folgenden Schritte aus. Wählen Sie andernfalls einen vorhandenen Web-App-Container, und fahren Sie mit Schritt 7 fort.

    1. Klicken Sie auf **New...** (Neu...).

    1. Das Dialogfeld **New Web App Container** (Neuer Web-App-Container) wird angezeigt:

        ![][07]

    1. Geben Sie eine DNS-Bezeichnung für Ihren Web-App-Container ein. Hierbei handelt es sich um die Blatt-DNS-Bezeichnung der Host-URL für Ihre Webanwendung in Azure. Hinweis: Der Name muss verfügbar sein und Azure-Web-App-Namenskonventionen entsprechen.

    1. Wählen Sie im Dropdownmenü **Web Container** (Webcontainer) die passende Software für Ihre Anwendung aus.

        Aktuell können Sie zwischen Tomcat 8, Tomcat 7 und Jetty 9 wählen. Azure stellt eine aktuelle Distribution der gewählten Software bereit und diese wird auf einer aktuellen Distribution von JDK 8 ausgeführt, die von Oracle erstellt und von Azure bereitgestellt wird.

    1. Wählen Sie im Dropdownmenü **Subscription** (Abonnement) das Abonnement aus, das Sie für diese Bereitstellung verwenden möchten.

    1. Wählen Sie im Dropdownmenü **Resource Group** (Ressourcengruppe) die Ressourcengruppe aus, der Sie Ihre Web-App zuordnen möchten.

        Hinweis: Mithilfe von Azure-Ressourcengruppen können Sie verwandte Ressourcen in Gruppen zusammenfassen, um diese z. B. gemeinsam löschen zu können.

        Sie können eine vorhandene Ressourcengruppe auswählen (sofern vorhanden) und direkt mit Schritt g unten fortfahren oder die folgenden Schritte ausführen, um eine neue Ressourcengruppe zu erstellen:

        * Klicken Sie auf **New...** (Neu...).

        * Das Dialogfeld **New Resource Group** (Neue Ressourcengruppe) wird angezeigt:

            ![][08]

        * Geben Sie im Textfeld **Name** einen Namen für die neue Ressourcengruppe ein.

        * Wählen Sie im Dropdownmenü **Region** den entsprechenden Azure-Rechenzentrumsstandort für Ihre Ressourcengruppe aus.

        * Klicken Sie auf **OK**.

    1. Im Dropdownmenü **App Service Plan** (App Service-Plan) werden die App Service-Pläne aufgelistet, die der ausgewählten Ressourcengruppe zugeordnet sind.

        Hinweis: Der App Service-Plan enthält verschiedene Informationen wie z. B. den Speicherort Ihrer Web-App, den Tarif und die Größe der Compute-Instanz. Da ein App Service-Plan für mehrere Web-Apps verwendet werden kann, wird er getrennt von einer bestimmten Web-App-Bereitstellung verwaltet.

        Sie können einen vorhandenen App Service-Plan auswählen (sofern vorhanden) und direkt mit Schritt h unten fortfahren oder die folgenden Schritte ausführen, um einen neuen App Service-Plan zu erstellen:

        * Klicken Sie auf **New...** (Neu...).

        * Das Dialogfeld **New App Service Plan** (Neuer App Service-Plan) wird angezeigt:

            ![][09]

        * Geben Sie im Textfeld **Name** einen Namen für den neuen App Service-Plan ein.

        * Wählen Sie im Dropdownmenü **Location** (Standort) den entsprechenden Azure-Rechenzentrumsstandort für den Plan aus.

        * Wählen Sie im Dropdownmenü **Pricing Tier** (Tarif) die entsprechenden Preise für den Plan aus. Zu Testzwecken können Sie **Free** (Kostenlos) auswählen.

        * Wählen Sie im Dropdownmenü **Instance Size** (Instanzgröße) die entsprechende Instanzgröße für den Plan aus. Zu Testzwecken können Sie **Small** (Klein) wählen.

    1. Wenn Sie alle oben genannten Schritte abgeschlossen haben, sollte das Dialogfeld „New Web App Container“ (Neuer Web-App-Container) folgender Abbildung ähneln:

        ![][10]

    1. Klicken Sie auf **OK**, um die Erstellung Ihres neuen Web-App-Containers abzuschließen.

        Warten Sie einige Sekunden, bis die Liste der Web-App-Container aktualisiert wurde. Ihr neu erstellter Web-App-Container sollte nun in der Liste angezeigt werden und markiert sein.

1. Sie können jetzt die erste Bereitstellung Ihrer Web-App in Azure abschließen:

    ![][11]

    Klicken Sie auf **OK**, um Ihre Java-Anwendung im ausgewählten Web-App-Container bereitzustellen.

    Hinweis: Standardmäßig wird die Anwendung als Unterverzeichnis des Anwendungsservers bereitgestellt. Wenn sie als Stammanwendung bereitgestellt werden soll, aktivieren Sie das Kontrollkästchen **Deploy to root** (In Stamm bereitstellen), bevor Sie auf **OK** klicken.

1. Als Nächstes sollte das **Azure-Aktivitätsprotokoll** mit dem Bereitstellungsstatus der Web-App angezeigt werden.

    ![][12]

    Die Bereitstellung Ihrer Web-App in Azure sollte nur einige Sekunden dauern. Wenn Ihre Anwendung bereit ist, wird in der Spalte **Status** der Link **Veröffentlicht** angezeigt. Wenn Sie auf den Link klicken, gelangen Sie zur Startseite Ihrer bereitgestellten Web-App.

## Aktualisieren Ihrer Web-App

Das Aktualisieren einer vorhandenen, ausgeführten Azure-Web-App ist ein schneller und einfacher Prozess, und Ihnen stehen zwei Optionen für die Aktualisierung zur Verfügung:

* Sie können die Bereitstellung einer vorhandenen Java-Web-App aktualisieren.
* Sie können eine weitere Java-Anwendung in dem gleichen Web-App-Container veröffentlichen.

In beiden Fällen ist der Prozess identisch und dauert nur wenige Sekunden:

1. Klicken Sie im Projektexplorer von Eclipse mit der rechten Maustaste auf die Java-Anwendung, die Sie aktualisieren oder einem vorhandenen Web-App-Container hinzufügen möchten.

2. Wählen Sie im Kontextmenü **Azure** und dann **Publish as Azure Web App...** (Als Azure-Web-App veröffentlichen) aus.

3. Da Sie sich bereits zuvor angemeldet haben, sehen Sie eine Liste Ihrer vorhandenen Web-App-Container. Wählen Sie den Container aus, in dem Sie Ihre Java-Anwendung veröffentlichen oder erneut veröffentlichen möchten, und klicken Sie auf **OK**.

Wenige Sekunden später wird Ihre aktualisierte Bereitstellung in der Ansicht **Azure-Aktivitätsprotokoll** als **Veröffentlicht** angezeigt, und Sie können die aktualisierte Anwendung in einem Webbrowser überprüfen.

## Beenden einer vorhandenen Web-App

Zum Beenden eines vorhandenen Azure-Web-App-Containers (einschließlich aller darin bereitgestellten Java-Anwendungen) können Sie die Ansicht **Azure Explorer** verwenden.

Wenn die Ansicht **Azure Explorer** noch nicht geöffnet ist, können Sie sie öffnen, indem Sie in Eclipse auf das Menü **Window** (Fenster) und dann auf **Show View** (Ansicht anzeigen), **Other...** (Andere...), **Azure** und **Azure Explorer** klicken. Wenn Sie sich nicht bereits angemeldet haben, werden sie dazu aufgefordert.

Wenn die Ansicht **Azure Explorer** angezeigt wird, beenden Sie Ihre Web-App mit folgenden Schritten:

1. Erweitern Sie den Knoten **Azure**.

1. Erweitern Sie den Knoten **Web Apps** (Web-Apps).

1. Klicken Sie mit der rechten Maustaste auf die gewünschte Web-App.

1. Wenn das Kontextmenü angezeigt wird, klicken Sie auf **Stop** (Beenden).

    ![][13]

## Nächste Schritte

Weitere Informationen finden Sie unter den folgenden Links:

* [Java Developer Center](/develop/java/).
* [Web-Apps – Übersicht](app-service-web-overview.md)

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure-Toolkits für Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Installation des Azure Toolkit für Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[01]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/01-Web-Page.png
[02]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/02-Dynamic-Web-Project.png
[03]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/03-Context-Menu.png
[04]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/04-Log-In-Dialog.png
[05]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/05-Manage-Subscriptions-Dialog.png
[06]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/06-Deploy-To-Azure-Web-Container.png
[07]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/07-New-Web-App-Container-Dialog.png
[08]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/08-New-Resource-Group-Dialog.png
[09]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/09-New-Service-Plan-Dialog.png
[10]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/11-Completed-Deploy-Dialog.png
[12]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/12-Activity-Log-View.png
[13]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/13-Azure-Explorer-Web-App.png
[14]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/publishDropdownButton.png

<!---HONumber=AcomDC_0629_2016-->