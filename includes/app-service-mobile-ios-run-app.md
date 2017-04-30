
1. Besuchen Sie auf dem Mac das [Azure-Portal]. Klicken Sie auf **Alle durchsuchen** > **Mobile Apps** und dann auf das Back-End, das Sie gerade erstellt haben. Klicken Sie in den Einstellungen der mobilen App auf **Schnellstart** > **iOS (Objective-C)**. Wenn Sie Swift bevorzugen, klicken Sie stattdessen auf **Schnellstart** > **iOS (Swift)**. Klicken Sie unter **Download and run your iOS project** (iOS-Projekt herunterladen und ausführen) auf **Herunterladen**. Damit laden Sie ein vollständiges Xcode-Projekt für eine App herunter, das für eine Verbindung mit dem Back-End vorkonfiguriert ist. Öffnen Sie das Projekt in Xcode.
2. Klicken Sie auf die Schaltfläche **Ausführen** , um das Projekt zu erstellen und die App im iOS-Simulator zu starten.
3. Geben Sie in der App einen sinnvollen Text ein, wie z.B. *Tutorial abschließen*, und klicken Sie dann auf das Plus-Symbol (**+**). Damit wird eine POST-Anforderung an das Azure-Back-End gesendet, das Sie zuvor bereitgestellt haben. Die Back-End fügt Daten aus der Anforderung in die TodoItem-SQL-Tabelle ein und gibt Informationen über die neu gespeicherten Elemente an die mobile App zurück. Die mobile App zeigt diese Daten in der Liste an. 

   ![Schnellstart-App unter iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure-Portal]: https://portal.azure.com/
