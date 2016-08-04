<properties
	pageTitle="Erste Schritte mit Mobile Services für Xamarin Android | Microsoft Azure"
	writer="craigd"
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Services mit Ihrer Xamarin.Android-App."
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="07/21/2016"
	ms.author="donnam"/>

# <a name="getting-started"></a>Erste Schritte mit Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)] &nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Informationen für die entsprechende Mobile Apps-Version dieses Themas finden Sie unter [Erstellen einer Xamarin.Android-App](../app-service-mobile/app-service-mobile-xamarin-android-get-started.md).

In diesem Lernprogramm erfahren Sie, wie Sie mit den Azure Mobile Services einen cloudbasierten Back-End-Dienst zu einer Xamarin.Android-App hinzufügen können. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache *To-Do-Listen*-App, die App-Daten im neuen mobilen Dienst speichert.

Wenn Sie lieber ein Video zu diesem Thema ansehen möchten, können Sie den Clip unten auswählen, in dem dieselben Schritte wie in diesem Tutorial behandelt werden.

Video: "Erste Schritte mit Xamarin und Azure Mobile Services" mit Craig Dunn, Developer Evangelist für Xamarin (Dauer: 10:05 Minuten)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

Unten sehen Sie einen Screenshot aus der fertigen App:

![][0]

Für das Abschließen dieses Tutorials sind XCode und Xamarin Studio für OS X oder Visual Studio unter Windows mit einem vernetzten Mac erforderlich. Vollständige Installationsanweisungen finden Sie unter [Setup und Installation für Visual Studio und Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).

Das heruntergeladene Schnellstart-Projekt enthält die Azure Mobile Services-Komponente für Xamarin.Android. Während dieses Projekt auf Android 4.2 oder eine höhere Version abzielt, benötigt das Mobile Services SDK lediglich Android 2.2 oder eine höhere Version.

> [AZURE.IMPORTANT] Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5).

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen Dienstes

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Erstellen einer neuen Xamarin.Android-App

Sobald Sie den mobilen Dienst erstellt haben, können Sie einer einfachen Schnellstartanleitung im klassischen Azure-Portal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung mit dem mobilen Dienst zu ändern.

In diesem Abschnitt erstellen Sie eine neue Xamarin.Android-App, die mit dem mobilen Dienst verbunden ist.

1.  Klicken Sie im [klassischen Azure-Portal] auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie auf der Schnellstartregisterkarte unter **Plattform auswählen** auf **Xamarin.Android**, und erweitern Sie **Create a new Android app**.

	![][6]

	Dadurch werden drei einfache Schritte zum Erstellen einer Xamarin.Android-App angezeigt, die mit dem mobilen Dienst verbunden wird.

	![][7]

3. Klicken Sie auf **TodoItem-Tabelle erstellen**, um eine Tabelle zum Speichern von App-Daten zu erstellen.

4. Klicken Sie unter **App herunterladen und ausführen** auf **Herunterladen**.

	Das Projekt für die beispielhafte _To-Do-Listen_-Anwendung, die mit dem mobilen Dienst verbunden ist, wird heruntergeladen. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

## Ausführen Ihrer Android-App

Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1. Navigieren Sie zum Speicherort der komprimierten Projektdateien, und entpacken Sie die Dateien auf Ihrem Computer.

2. Klicken Sie in Xamarin Studio oder Visual Studio auf **Datei** und danach auf **Öffnen**. Navigieren Sie zu den nicht komprimierten Beispieldateien, und wählen Sie **XamarinTodoQuickStart.Android.sln** zum Öffnen aus.

3. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen und die App zu starten. Sie werden zur Auswahl eines Emulators oder eines angeschlossenen USB-Geräts aufgefordert.

	> [AZURE.NOTE] Sie müssen mindestens ein virtuelles Android-Gerät (AVD) definieren, um das Projekt im Android-Emulator auszuführen. Verwenden Sie den AVD Manager, um diese Geräte zu erstellen und zu verwalten.

4. Geben Sie in der App einen sinnvollen Text ein, beispielsweise _Lernprogramm beenden_, und klicken Sie dann auf **Hinzufügen**.

	![][10]

	Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

	> [AZURE.NOTE]
   	Sie können den Code überprüfen, der zum Abfragen und Einfügen von Daten auf den mobilen Dienst zugreift. Der Code befindet sich in der C#-Datei "ToDoActivity.cs".

6. Zurück im [klassischen Azure-Portal] klicken Sie auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItems**.

	![][11]

	Nun können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

	![][12]

## <a name="next-steps"> </a>Nächste Schritte
Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden:

* [Erste Schritte mit der Synchronisierung von Offlinedaten] Erfahren Sie, wie Sie die Offlinedatensynchronisierung nutzen, um eine flexible und stabile App zu erstellen.

* [Erste Schritte mit der Authentifizierung] Informationen zur Authentifizierung von Benutzern Ihrer App bei einem Identitätsanbieter.

* [Erste Schritte mit Pushbenachrichtigungen] Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.



[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page1.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page2.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started/obile-services-selection.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
[7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
[10]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-services-diagram.png


<!-- URLs. -->
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[Erste Schritte mit der Synchronisierung von Offlinedaten]: mobile-services-xamarin-android-get-started-offline-data.md
[Erste Schritte mit der Authentifizierung]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[Erste Schritte mit Pushbenachrichtigungen]: /develop/mobile/tutorials/get-started-with-push-xamarin-android
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Azure]: http://azure.microsoft.com/
[klassischen Azure-Portal]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0727_2016-->