#### Voraussetzungen
* Ein Azure-Konto. Sie können ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen.
* Ein [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3)-Konto.

Bevor Sie Ihr OneDrive-Konto in einer Logik-App verwenden können, autorisieren Sie die Logik-App für Verbindungen mit Ihrem OneDrive-Konto. Sie können dies ganz einfach in Ihrer Logik-App im Azure-Portal durchführen.

Führen Sie folgende Schritte aus, um Ihre Logik-App für die Verbindung mit Ihrem OneDrive-Konto zu autorisieren:

1. Erstellen Sie eine Logik-App. Wählen Sie im Logic Apps-Designer in der Dropdownliste **Von Microsoft verwaltete APIs anzeigen** aus, und geben Sie dann „OneDrive“ in das Suchfeld ein. Wählen Sie einen Trigger oder eine Aktion aus: ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Wenn Sie bisher noch keine Verbindung mit OneDrive hergestellt haben, werden Sie aufgefordert, sich mit Ihren OneDrive-Anmeldeinformationen anzumelden: ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Wählen Sie **Anmelden** aus, und geben Sie Ihren Benutzernamen und Ihr Kennwort ein. Wählen Sie **Anmelden**: ![](./media/connectors-create-api-onedrive/onedrive-3.png)
   
    Mithilfe dieser Anmeldeinformationen wird Ihre Logik-App dazu autorisiert, eine Verbindung mit Ihrem OneDrive-Konto herzustellen und auf die darin gespeicherten Daten zuzugreifen.
4. Wählen Sie **Ja**, um die Logik-App für die Verwendung Ihres OneDrive-Kontos zu autorisieren: ![](./media/connectors-create-api-onedrive/onedrive-4.png)
5. Beachten Sie, dass die Verbindung erstellt wurde. Fahren Sie nun mit den weiteren Schritten in Ihrer Logik-App fort: ![](./media/connectors-create-api-onedrive/onedrive-5.png)

<!---HONumber=AcomDC_0727_2016-->