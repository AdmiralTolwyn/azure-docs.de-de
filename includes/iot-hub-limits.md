In der folgenden Tabelle sind die Grenzwerte aufgeführt, die den verschiedenen Dienstebenen (S1, S2, S3, F1) zugeordnet sind. Informationen zu den Kosten jeder *Einheit* finden Sie unter [IoT Hub – Preise](https://azure.microsoft.com/pricing/details/iot-hub/).

| Ressource | S1 Standard | S2 Standard | S3 Standard | F1 Free |
| -------- | ----------- | ----------- | ----------- | ------- |
| Nachrichten/Tag | 400\.000 | 6\.000.000 | 300\.000.000 | 8\.000 |
| Maximale Anzahl der Einheiten | 200 | 200 | 200 | 1 |

> [AZURE.NOTE] Wenden Sie sich an den Microsoft-Support, wenn Sie voraussichtlich mehr als 200 Einheiten mit einem Hub im Tarif S1 oder S2 verwenden.

Die folgende Tabelle enthält die für IoT Hub-Ressourcen geltenden Grenzwerte:

| Ressource | Begrenzung |
| -------- | ----- |
| Maximale Anzahl von IoT Hubs pro Azure-Abonnement | 10 |
| Maximale Anzahl von Geräte-Identitäten,<br/> die bei einem einzelnen Aufruf zurückgegeben wird | 1000 |
| Maximale Beibehaltungsdauer von IoT Hub-Nachrichten | 7 Tage |
| Maximale Größe einer Nachricht von einem Gerät an die Cloud | 256 KB |
| Maximale Größe eines Batches, das vom Gerät an die Cloud gesendet wird | 256 KB |
| Maximale Anzahl von Nachrichten im Batch, das vom Gerät an die Cloud gesendet wird | 500 |
| Maximale Größe einer Nachricht von der Cloud an das Gerät | 64 KB |
| Maximale Gültigkeitsdauer von Nachrichten von der Cloud an das Gerät | 2 Tage |
| Maximale Übermittlungsanzahl von Nachrichten von der<br/> Cloud an das Gerät | 100 |
| Maximale Übermittlungsanzahl von Feedbacknachrichten<br/> als Reaktion auf eine Nachricht von der Cloud an das Gerät | 100 |
| Maximale Gültigkeitsdauer von Feedbacknachrichten<br/> als Reaktion auf eine Nachricht von der Cloud an das Gerät | 2 Tage |

> [AZURE.NOTE] Wenn Sie mehr als 10 IoT Hubs in einem Azure-Abonnement benötigen, wenden Sie sich an den Microsoft Support.

Der IoT Hub-Dienst drosselt Anforderungen, wenn die folgenden Kontingente überschritten werden:

| Drosselung | Wert pro Hub |
| -------- | ------------- |
| Vorgänge in der Identitätsregistrierung <br/> (Erstellen, Abrufen, Aktualisieren, Löschen), <br/> einzelne Import-/Exportvorgänge oder Massenimport/-export | 100/Minute/Einheit, bis zu 5.000/Minute |
| Geräteverbindungen | 120/Sekunden/Einheit (für S2), 12/Sekunden/Einheit (für S1); mindestens 100/Sekunde |
| Senden von Nachrichten von Geräten an die Cloud | 120/Sekunden/Einheit (für S2), 12/Sekunden/Einheit (für S1) <br/> Mindestens 100/Sekunde |
| C2D-Sendevorgänge | 100/Minute/Einheit |
| C2D-Empfangsvorgänge | 1000/Minuten/Einheit |

<!---HONumber=AcomDC_0706_2016-->