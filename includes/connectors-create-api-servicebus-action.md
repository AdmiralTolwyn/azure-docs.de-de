Nachdem Sie nun einen Trigger hinzugefügt haben, ist es an der Zeit, die vom Trigger generierten Daten sinnvoll zu nutzen. Führen Sie die folgenden Schritte zum Hinzufügen der Aktion **Service Bus – Nachricht senden** aus. Diese Aktion sendet eine Nachricht an Service Bus.

Führen Sie die folgenden Schritte zum Erstellen einer Aktion „Nachricht senden“ aus:

1. Wählen Sie **+ Neuer Schritt** aus, um die Aktion hinzuzufügen.
- Wählen Sie **Aktion hinzufügen**. Daraufhin öffnet sich ein Suchfeld, in dem Sie nach der gewünschten Aktion suchen können. In diesem Beispiel sind Service Bus-Aktionen von Interesse. ![Service Bus-Aktion – Abbildung 1](./media/connectors-create-api-servicebus/action-1.png)
- Geben Sie *Service Bus* ein.
- Wählen Sie **Service Bus - Nachricht senden** als die auszuführende Aktion aus. ![Service Bus-Aktion – Abbildung 2](./media/connectors-create-api-servicebus/action-2.png)
- Geben Sie den Inhalt der Nachricht ein. Dies ist erforderlich.
- Geben Sie den Warteschlangen- oder Themennamen ein, an den die Nachricht gesendet wird. Dies ist auch erforderlich.
- Geben Sie weitere Informationen über die Nachricht ein. Dies ist optional. ![Service Bus-Aktion – Abbildung 3](./media/connectors-create-api-servicebus/action-3.png)
- Speichern Sie die Änderungen am Workflow. ![Service Bus-Aktion – Abbildung 4](./media/connectors-create-api-servicebus/action-4.png)

<!---HONumber=AcomDC_0810_2016-->