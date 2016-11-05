Die App verwendet nun mobile Dienste als Back-End-Speicher, und Sie können sie entweder im Android-Emulator oder auf einem Android-Telefon gegen den mobilen Dienst testen.

1. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um das Projekt zu starten.
   
    Dadurch wird Ihre mit dem Android-SDK erstellte App ausgeführt, die mithilfe der Clientbibliothek Elemente aus Ihrem mobilen Dienst abfragt.
2. Geben Sie wie zuvor einen lesbaren Text ein und klicken Sie auf **Hinzufügen**.
   
       Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.
   
    Sie können die App neu starten, um zu sehen, dass die Änderungen in die Datenbank in Azure übernommen wurden. Außerdem können Sie die Datenbank im klassischen Azure-Portal überprüfen: In den nächsten zwei Schritten werden auf diese Weise die Änderungen in Ihrer Datenbank angezeigt.
3. Klicken Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/) auf die Option zum Verwalten der Datenbank, die mit Ihrem mobilen Dienst verknüpft ist.
   
    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png)
4. Führen Sie im klassischen Azure-Portal eine Abfrage aus, um die von der Windows Store-App vorgenommenen Änderungen anzuzeigen. Ihre Abfrage sieht wie folgt aus, nur dass Sie Ihren Datenbanknamen anstelle von `todolist` verwenden.
   
        SELECT * FROM [todolist].[todoitems]
   
    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png)

Damit ist das Android-Lernprogramm **Erste Schritte mit Daten** abgeschlossen.

<!---HONumber=AcomDC_1203_2015-->