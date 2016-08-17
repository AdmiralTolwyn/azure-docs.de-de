<properties
	pageTitle="Erste Schritte mit Azure Mobile App Service-Apps für Xamarin.iOS-Apps | Microsoft Azure"
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Mobile Apps für die Xamarin.iOS-Entwicklung."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/04/2016"
	ms.author="normesta"/>


#Erstellen einer Xamarin.iOS-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Übersicht

In diesem Lernprogramm erfahren Sie, wie Sie mithilfe eines mobilen Azure-App-Back-Ends einen cloudbasierten Back-End-Dienst einer mobilen Xamarin.iOS-App hinzufügen können. Sie erstellen ein neues mobiles App-Back-End sowie eine einfache Xamarin.iOS-_Todo list_-App, die App-Daten in Azure speichert.

Das Absolvieren dieses Lernprogramms ist Voraussetzung für alle anderen Xamarin.iOS-Lernprogramme zur Verwendung des Features "Mobile Apps" von Azure App Service.

##Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Apps, die Sie auch nach Ablauf der Testversion weiter nutzen können. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio mit Xamarin. Anweisungen finden Sie unter [Setup und Installation für Visual Studio und Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).

* Ein Mac, auf dem Xcode Version 7.0 oder höher und Xamarin Studio Community installiert ist. Informationen finden Sie unter [Setup und Installation für Visual Studio und Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) und [Setup, Installation und Überprüfungen für Mac-Benutzer](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, besuchen Sie [Azure App Service-App erstellen](https://tryappservice.azure.com/?appServiceName=mobile). Dort können Sie direkt eine kurzzeitige mobile Start-App in App Service erstellen – keine Kreditkarte erforderlich, keine weiteren Verpflichtungen.

## Erstellen eines neuen Azure Mobile App-Back-Ends

Führen Sie die folgenden Schritte aus, um ein neues mobiles App-Back-End zu erstellen.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Konfigurieren des Serverprojekts

Sie haben nun ein Azure-Back-End für mobile Apps bereitgestellt, das von Ihren mobilen Clientanwendungen verwendet werden kann. Als Nächstes laden Sie ein Serverprojekt für ein einfaches "Aufgabenlisten"-Back-End herunter und veröffentlichen es in Azure.

Befolgen Sie die nachstehenden Anweisungen zum Konfigurieren des Serverprojekts für die Verwendung des Node.js- oder .NET-Back-Ends.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## Herunterladen und Ausführen der Xamarin.iOS-App

1. Öffnen Sie das [Azure-Portal] in einem Browserfenster.

2. Klicken Sie auf dem Blatt „Einstellungen“ für Ihre mobile App auf **Erste Schritte** > **Xamarin.iOS**. Klicken Sie unter Schritt 3 auf **Neue App erstellen**, falls noch nicht ausgewählt. Klicken Sie dann auf die Schaltfläche **Herunterladen**.

  	Dadurch wird ein Projekt heruntergeladen, das eine Clientanwendung enthält, die mit der mobilen App verbunden ist. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

3. Extrahieren Sie das heruntergeladene Projekt, und öffnen Sie es dann in Xamarin Studio (oder Visual Studio).

	![][9]

	![][8]

4. Drücken Sie F5, um das Projekt zu erstellen und die App im iPhone-Simulator zu starten.

5. Geben Sie in der App einen sinnvollen Text ein, z. B. _Xamarin kennenlernen_, und klicken Sie dann auf die Schaltfläche **+**.

	![][10]

	Dadurch wird eine POST-Anforderung an das neue in Azure gehostete mobile App-Back-End gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden vom mobilen App-Back-End zurückgegeben, und die Daten werden in der Liste angezeigt.

>[AZURE.NOTE]Sie können den Code überprüfen, der auf das mobile App-Back-End zum Abfragen und Einfügen von Daten zugreift. Sie finden ihn in der C#-Datei "QSTodoService.cs C#".

##Nächste Schritte

* [Hinzufügen der Authentifizierung zur App](app-service-mobile-xamarin-ios-get-started-users.md) <br/>Erfahren Sie, wie Sie Benutzer der App mit einem Identitätsanbieter authentifizieren.

* [Hinzufügen von Pushbenachrichtigungen zur App](app-service-mobile-xamarin-ios-get-started-push.md) <br/>Erfahren Sie, wie Sie eine einfache Pushbenachrichtigung an Ihre App senden können.

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Azure-Portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0810_2016-->