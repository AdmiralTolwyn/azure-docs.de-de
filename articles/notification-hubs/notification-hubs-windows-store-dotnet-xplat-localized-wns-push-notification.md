<properties
	pageTitle="Lernprogramm zu lokalisierten aktuellen Nachrichten in Notification Hubs"
	description="Erfahren Sie mehr über die Verwendung von Azure Notification Hubs zum Senden von Benachrichtigungen zu lokalisierten aktuellen Nachrichten."
	services="notification-hubs"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/29/2016" 
	ms.author="wesmc"/>

# Verwenden von Notification Hubs zum Senden lokalisierter Nachrichten

> [AZURE.SELECTOR]
- [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
- [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

##Übersicht

In diesem Thema wird gezeigt, wie Sie mit der **Vorlagen**-Funktion von Azure Notification Hubs aktuelle Nachrichten senden können, die je nach Sprache und Gerät lokalisiert wurden. In diesem Lernprogramm beginnen Sie mit der Windows Store-App, die Sie in [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] erstellt haben. Sie werden anschließend in der Lage sein, sich für Kategorien zu registrieren, die Sie interessieren, eine Sprache für die Benachrichtigungen auszuwählen und nur Pushbenachrichtigungen für diese Kategorien in der jeweiligen Sprache zu empfangen.


Dieses Szenario besteht aus zwei Teilen:

- In der Windows Store-App können Client-Geräte eine Sprache auswählen und verschiedene Nachrichtenkategorien abonnieren;

- Das Back-End verteilt die Benachrichtigungen mithilfe der **tag**- und **template**-Features von Azure Notification Hubs.



##Voraussetzungen

Sie müssen zuvor das Lernprogramm [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] abschließen und den Code verfügbar haben, da dieses Lernprogramm direkt auf dem Code aufbaut.

Außerdem benötigen Sie Visual Studio 2012 oder höher.


##Konzepte von Vorlagen

Im Lernprogramm [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] haben Sie eine App erstellt, in der Benutzer mit **tags** Nachrichten aus verschiedenen Kategorien abonnieren können. Viele Apps richten sich jedoch an verschiedene Märkte und müssen lokalisiert werden. In diesen Fällen muss auch der Inhalt der Benachrichtigungen lokalisiert und an die korrekten Geräte ausgeliefert werden. In diesem Artikel erfahren Sie, wie Sie mit dem **template**-Feature von Notification Hubs lokalisierte Benachrichtigungen für Nachrichten verschicken können.

Hinweis: Sie können mehrere Versionen der einzelnen Tags erstellen, um lokalisierte Benachrichtigungen zu verschicken. Für Englisch, Französisch und Mandarin müssen Sie z. B. drei verschiedene Markierungen für Weltnachrichten erstellen: "world\_en", "world\_fr" und "world\_ch". Anschließend müssten Sie eine lokalisierte Version der Nachrichten an die einzelnen Tags schicken. Dieser Artikel verwendet Vorlagen, um die Anzahl der Tags einzugrenzen und den Versand mehrerer Nachrichten zu vermeiden.

Mit Vorlagen können Sie auf einer hohen Ebene festlegen, wie ein bestimmtes Gerät eine Benachrichtigung empfangen soll. Die Vorlage gibt das exakte Format der Nutzlast anhand von Eigenschaften an, die Teil der von Ihrem Back-End verschickten Nachricht sind. In unserem Fall senden wir eine sprachenunabhängige Nachricht, die alle unterstützten Sprachen enthält:

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

Anschließend stellen wir sicher, dass sich die Geräte mit einer Vorlage registrieren, die auf die korrekte Eigenschaft verweist. Eine Windows Store-App, die einfache Popupmeldungen empfangen möchte, registriert sich z. B. mit beliebigen zugehörigen Tags für die folgende Vorlage:

	<toast>
	  <visual>
	    <binding template="ToastText01">
	      <text id="1">$(News_English)</text>
	    </binding>
	  </visual>
	</toast>



Vorlagen sind ein leistungsstarkes Feature, über das Sie weitere Informationen in unserem Artikel [Vorlagen](notification-hubs-templates-cross-platform-push-messages.md) finden.


##Die App-Benutzeroberfläche

Wir werden nun die App zu aktuellen Nachrichten aus dem Thema [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] so modifizieren, dass aktuelle Nachrichten mit Vorlagen verschickt werden.

In Ihrer Windows Store-App:

Fügen Sie ein Sprachen-Kombinationsfeld zu Ihrer MainPage.xaml hinzu:

	<Grid Margin="120, 58, 120, 80"  
			Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>

##Erstellen der Windows Store-Client-App

1. Fügen Sie in Ihrer Notifications-Klasse einen locale-Parameter zu den Methoden *StoreCategoriesAndSubscribe* und *SubscribeToCateories* hinzu.

        public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            return await SubscribeToCategories(categories);
        }

        public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration. This makes supporting notifications across other platforms much easier.
            // Using the localized tags based on locale selected.
            string templateBodyWNS = String.Format("<toast><visual><binding template="ToastText01"><text id="1">$(News_{0})</text></binding></visual></toast>", locale);

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
        }

	Beachten Sie, dass wir anstelle der Methode *RegisterNativeAsync* nun *RegisterTemplateAsync* aufrufen: Wir registrieren ein bestimmtes Benachrichtigungsformat, dessen Vorlage von der Sprache abhängt. Außerdem geben wir einen Namen für die Vorlage an („localizedWNSTemplateExample“), da wir möglicherweise mehrere Vorlagen registrieren möchten (z. B. eine für Popupbenachrichtigungen und eine für Kacheln). In diesem Fall wird der Name benötigt, um die Registrierungen ändern oder löschen zu können.

	Wenn sich ein Gerät für mehrere Vorlagen mit demselben Tag registriert, werden bei einer eingehenden Nachricht für das entsprechende Tag mehrere Benachrichtigungen an das Gerät verschickt (eine pro Vorlage). Dies ist hilfreich, um dieselbe logische Nachricht in mehreren visuellen Darstellungen anzuzeigen, z. B. als Signal und als Popupbenachrichtigung in einer Windows Store-App.

2. Fügen Sie die folgende Methode hinzu, um die gespeicherte Sprache abzurufen:

		public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. Aktualisieren Sie in Ihrer Datei „MainPage.xaml.cs“ den Click-Handler, indem Sie den aktuellen Wert des Sprachenkombinationsfelds abrufen und im Aufruf an die Notifications-Klasse angeben:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var locale = (string)Locale.SelectedItem;

            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
				 categories);

            var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
				string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }


4. Aktualisieren Sie schließlich unbedingt in der Datei „App.Xaml.cs“ Ihre `InitNotificationsAsync`-Methode, damit das Gebietsschema abgerufen und für Abonnements verwendet wird:

        private async void InitNotificationsAsync()
        {
            var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


##Senden von lokalisierten Benachrichtigungen von Ihrem Back-End aus

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]






<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten]: /manage/services/notification-hubs/breaking-news-dotnet

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx

<!---HONumber=AcomDC_0907_2016-->