In der folgenden Tabelle sind die Kontingente und Grenzwerte aufgelistet, die für [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) gelten. Informationen zu den Preisen von Event Hubs finden Sie unter [Event Hubs – Preise](https://azure.microsoft.com/pricing/details/event-hubs/).

| Begrenzung | Umfang | Typ | Verhalten beim Überschreiten | Wert |
| --- | --- | --- | --- | --- |
| Anzahl von Event Hubs pro Namespace |Namespace |statischen |Nachfolgende Anforderungen für die Erstellung eines neuen Event Hub werden zurückgewiesen. |10 |
| Anzahl von Partitionen pro Event Hub |Entität |Statisch |- |32 |
| Anzahl von Consumergruppen pro Event Hub |Entität |Statisch |- |20 |
| Anzahl von AMQP-Verbindungen pro Namespace |Namespace |Statisch |Nachfolgende Anforderungen für zusätzliche Verbindungen werden abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen. |5.000 |
| Maximale Größe des Event Hubs-Ereignisses|Systemweit |Statisch |- |256 KB |
| Maximale Größe eines Event Hub-Namens |Entität |Statisch |- |50 Zeichen |
| Anzahl nicht epochenbezogener Empfänger pro Consumergruppe |Entität |Statisch |- |5 |
| Maximale Aufbewahrungsdauer von Ereignisdaten |Entität |Statisch |- |1–7 Tage |
| Maximale Durchsatzeinheiten |Namespace |Statisch |Bei einer Überschreitung des Grenzwerts für Durchsatzeinheiten werden Ihre Daten gedrosselt, und eine **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**-Ausnahme wird ausgelöst. Sie können im Tarif „Standard“ eine höhere Anzahl von Durchsatzeinheiten anfordern, indem Sie eine [Supportanfrage](/azure/azure-supportability/how-to-create-azure-support-request) erstellen. [Zusätzliche Durchsatzeinheiten](../articles/event-hubs/event-hubs-auto-inflate.md) sind für einen festgelegten Kaufpreis in 20er-Blöcken verfügbar. |20 |
| Anzahl von Autorisierungsregeln pro Namespace |Namespace|statischen |Nachfolgende Anforderungen zur Erstellung von Autorisierungsregeln werden abgelehnt.|12 |
