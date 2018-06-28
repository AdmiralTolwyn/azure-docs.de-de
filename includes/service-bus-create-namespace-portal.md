Um mit der Verwendung von Service Bus-Nachrichtenentitäten in Azure beginnen zu können, müssen Sie zuerst einen Namespace mit einem in Azure eindeutigen Namen erstellen. Ein Namespace ist ein Bereichscontainer für die Adressierung von Service Bus-Ressourcen innerhalb Ihrer Anwendung.

So erstellen Sie einen Namespace

1. Melden Sie sich beim [Azure-Portal][Azure portal] an.
2. Klicken Sie im linken Navigationsbereich des Portals auf **+ Ressource erstellen**, und klicken Sie anschließend auf **Enterprise Integration** > **Service Bus**.
3. Geben Sie im Dialogfeld **Namespace erstellen** einen Namen für den Namespace ein. Das System überprüft sofort, ob dieser Name verfügbar ist.
4. Ist der Name verfügbar, wählen Sie den Tarif („Basic“, „Standard“ oder Premium“) aus.
5. Wählen Sie im Feld **Abonnement** ein Azure-Abonnement aus, in dem der Namespace erstellt werden soll.
6. Wählen Sie im Feld **Ressourcengruppe** eine vorhandene Ressourcengruppe für den Namespace aus, oder erstellen Sie eine neue Ressourcengruppe.      
7. Wählen Sie im Feld **Standort** das Land oder die Region aus, in dem bzw. in der Ihr Namespace gehostet werden soll.
   
    ![Namespace erstellen][create-namespace]
8. Klicken Sie auf **Create**. Ihr Dienstnamespace wird nun erstellt und aktiviert. Ggf. müssen Sie einige Minuten warten, bis die Ressourcen für Ihr Konto durch das System bereitgestellt werden.

### <a name="obtain-the-management-credentials"></a>Abrufen der Verwaltungsanmeldeinformationen
Beim Erstellen eines neuen Namespace wird automatisch eine SAS-Regel (Shared Access Signature) mit einem zugeordneten Paar aus primären und sekundären Schlüsseln generiert, mit denen Sie jeweils die volle Kontrolle über sämtliche Aspekte des Namespace haben. Unter [Service Bus-Authentifizierung und -Autorisierung](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) erfahren Sie, wie Sie weitere Regeln mit stärker eingeschränkten Rechten für reguläre Absender und Empfänger erstellen. Führen Sie diese Schritte aus, um die erste Regel zu kopieren: 

1.  Klicken Sie auf **Alle Ressourcen** und dann auf den neu erstellten Namespacenamen.
2. Klicken Sie im Namespacefenster auf **Richtlinien für gemeinsamen Zugriff**.
3. Klicken Sie im Bildschirm **Richtlinien für gemeinsamen Zugriff** auf **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. Klicken Sie im Fenster **Richtlinie: RootManageSharedAccessKey** neben **Verbindungszeichenfolge – Primärschlüssel** auf die Kopierschaltfläche, um die Verbindungszeichenfolge zur späteren Verwendung in die Zwischenablage zu kopieren. Fügen Sie diesen Wert in den Editor oder an einem anderen temporären Speicherort ein.
   
    ![connection-string][connection-string]

5. Wiederholen Sie den vorherigen Schritt, um den Wert von **Primärschlüssel** zu kopieren und zur späteren Verwendung an einem temporären Speicherort einzufügen.

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
