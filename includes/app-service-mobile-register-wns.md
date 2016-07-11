
1. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf das Windows Store-App-Projekt, und klicken Sie dann auf **Store** > **App mit Store verknüpfen...**

    ![Zuordnen der App zu Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
    
2. Klicken Sie im Assistenten auf **Weiter**, melden Sie sich mit Ihrem Microsoft-Konto an, geben Sie unter **App-Namen reservieren** einen Namen für Ihre App ein, und klicken Sie dann auf **Reservieren**.

3. Nachdem die App-Registrierung erfolgreich erstellt wurde, wählen Sie den Namen der neuen App aus, klicken Sie auf **Weiter** und dann auf **Zuordnen**. Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.

7. Wiederholen Sie die Schritte 1 und 3 für das Windows Phone Store-App-Projekt mithilfe der zuvor erstellten Registrierung für die Windows Store-App.

7. Navigieren Sie zum [Windows-Entwicklungscenter](https://dev.windows.com/de-DE/overview), melden Sie sich mit Ihrem Microsoft-Konto an, klicken Sie auf die neue App-Registrierung unter **Meine Apps**, und erweitern Sie dann **Dienste** > **Pushbenachrichtigungen**.

8. Klicken Sie auf der Seite **Pushbenachrichtigungen** unter **Windows-Pushbenachrichtigungsdienste (WNS) und Microsoft Azure Mobile Services** auf **Live Services-Website**, notieren Sie die **Paket-SID** und den *aktuellen* und den **geheimen Schlüssel der Anwendung**.

    ![App-Einstellung im Developer Center](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

    > [AZURE.IMPORTANT] Der geheime Schlüssel der Anwendung und die Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Werte nicht weiter, und verteilen Sie sie nicht mit Ihrer Anwendung.

<!---HONumber=AcomDC_0629_2016-->