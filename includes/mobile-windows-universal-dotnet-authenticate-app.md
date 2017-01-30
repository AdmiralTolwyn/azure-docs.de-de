
1. Öffnen Sie die freigegebene Projektdatei "MainPage.cs", und fügen Sie der MainPage-Klasse den folgenden Codeausschnitt hinzu:
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    Dieser Code authentifiziert den Benutzer mit einer Facebook-Anmeldung. Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den Wert für **MobileServiceAuthenticationProvider** oben entsprechend Ihrem Anbieter.
2. Löschen Sie in der vorhandenen Methodenüberschreibung von **OnNavigatedTo** den Aufruf der Methode **RefreshTodoItems**, oder kommentieren Sie diesen Aufruf aus.
   
    Dadurch wird sichergestellt, dass die Daten erst geladen werden, nachdem der Benutzer authentifiziert wurde. Als Nächstes fügen Sie der App die Schaltfläche **Sign in** hinzu, die die Authentifizierung auslöst.
3. Fügen Sie den folgenden Codeausschnitt zur MainPage-Klasse hinzu:
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Hide the login button and load items from the mobile app.
                ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. Öffnen Sie im Windows Store-App-Projekt die Projektdatei „MainPage.xaml“, und fügen Sie unmittelbar vor dem Element, das die Schaltfläche **Save** definiert, das folgende **Button**-Element hinzu:
   
        <Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>
5. Fügen Sie in das Windows Phone Store-App-Projekt das folgende **Button**-Element im **ContentPanel** hinter dem **TextBlock**-Element ein:
   
        <Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
            Margin="10, 0, 0, 0" Visibility="Visible">Sign in</Button>
6. Öffnen Sie die gemeinsam genutzte Projektdatei "App.xaml.cs", und fügen Sie folgenden Code hinzu:
   
        protected override void OnActivated(IActivatedEventArgs args)
        {
            // Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Completes the sign-in process started by LoginAsync.
                // Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif
   
            base.OnActivated(args);
        }
   
    Wenn die **OnActivated**-Methode bereits vorhanden ist, fügen Sie lediglich den Codeabschnitt `#if...#endif` hinzu.
7. Drücken Sie F5, um die Windows Store-App auszuführen, und klicken Sie auf die Schaltfläche **Sign in** , und sich mit dem von Ihnen ausgewählten Identitätsanbieter bei der App anzumelden. 
   
       When you are successfully logged-in, the app should run without errors, and you should be able to query your backend and make updates to data.
8. Klicken Sie mit der rechten Maustaste auf das Windows Phone Store-App-Projekt, klicken Sie auf **Als Startprojekt festlegen**, und führen Sie dann den obigen Schritt erneut aus, um sicherzustellen, dass die Windows Phone Store-App ebenfalls ordnungsgemäß ausgeführt wird.  



<!--HONumber=Jan17_HO3-->


