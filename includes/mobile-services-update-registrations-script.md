

1. Klicken Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/) auf die Registerkarte **Daten** und dann auf die Tabelle **Registrations**. 
   
    ![](./media/mobile-services-update-registrations-script/mobile-portal-data-tables-registrations.png)
2. Klicken Sie unter **Registrations** auf die Registerkarte **Skript** und wählen Sie **Einfügen** aus.
   
    ![](./media/mobile-services-update-registrations-script/mobile-insert-script-registrations.png)
   
    Auf diese Weise wird die Funktion angezeigt, die aufgerufen wird, wenn in der Tabelle **Registrations** eine Einfügung auftritt.
3. Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:
   
        function insert(item, user, request) {
            var registrationTable = tables.getTable('Registrations');
            registrationTable
                .where({ handle: item.handle })
                .read({ success: insertChannelIfNotFound });
            function insertChannelIfNotFound(existingRegistrations) {
                if (existingRegistrations.length > 0) {
                    request.respond(200, existingRegistrations[0]);
                } else {
                    request.execute();
                }
            }
        }
   
   Auf diese Weise wird ein neues Einfügeskript registriert, das die Registrierungsinformationen in der neuen Tabelle speichert.

<!---HONumber=AcomDC_1203_2015-->