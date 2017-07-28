---
title: "Programmierhandbuch für Azure Event Hubs | Microsoft Docs"
description: "Schreiben von Code für Azure Event Hubs unter Verwendung des Azure .NET SDK."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 64cbfd3d-4a0e-4455-a90a-7f3d4f080323
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 05/17/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: f65b992297c429eda2090f744b9b88b1ede39533
ms.contentlocale: de-de
ms.lasthandoff: 06/09/2017


---
# <a name="event-hubs-programming-guide"></a>Programmierleitfaden für Event Hubs

Dieser Artikel erörtert einige gängige Szenarien zum Schreiben von Code mit Azure Event Hubs und dem Azure .NET SDK. Hierbei wird ein grundlegendes Verständnis von Event Hubs vorausgesetzt. Eine konzeptuelle Übersicht über Event Hubs finden Sie unter [Übersicht über Event Hubs](event-hubs-what-is-event-hubs.md).

## <a name="event-publishers"></a>Ereignisherausgeber

Sie senden Ereignisse entweder mit HTTP POST oder über eine AMQP 1.0-Verbindung an einen Event Hub. Welches Verfahren gewählt wird, hängt vom jeweils vorliegenden Szenario ab. AMQP 1.0-Verbindungen werden als vermittelte Verbindungen in Service Bus gemessen und sind besser für Fälle mit häufigeren höheren Nachrichtenvolumen und geringeren Latenzanforderungen geeignet, da sie über einen dauerhaften Messagingkanal verfügen.

Event Hubs werden mit der [NamespaceManager][] -Klasse erstellt und verwaltet. Beim Verwenden der per .NET verwalteten APIs sind die Hauptkonstrukte für die Veröffentlichung von Daten auf Event Hubs die Klassen [EventHubClient](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) und [EventData][]. [EventHubClient][] stellt den AMQP-Kommunikationskanal bereit, über den Ereignisse an Event Hub gesendet werden. Die [EventData][]-Klasse stellt ein Ereignis dar und wird verwendet, um Nachrichten auf einem Event Hub zu veröffentlichen. Diese Klasse enthält den Text, einige Metadaten sowie Headerinformationen zum Ereignis. Dem [EventData][]-Objekt werden weitere Eigenschaften hinzugefügt, wenn es einen Event Hub durchläuft.

## <a name="get-started"></a>Erste Schritte

Die .NET-Klassen, die Event Hubs unterstützen, werden in der Assembly „Microsoft.ServiceBus.dll“ bereitgestellt. Der einfachste Weg zum Verweisen auf die Service Bus-API und Konfigurieren Ihrer Anwendung mit allen Service Bus-Abhängigkeiten ist das Herunterladen des [Service Bus-NuGet-Pakets](https://www.nuget.org/packages/WindowsAzure.ServiceBus). Alternativ dazu können Sie die [Paket-Manager-Konsole](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) in Visual Studio verwenden. Geben Sie hierzu im Fenster der [Paket-Manager-Konsole](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) den folgenden Befehl ein:

```
Install-Package WindowsAzure.ServiceBus
```

## <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs
Sie können die [NamespaceManager][] -Klasse verwenden, um Event Hubs zu erstellen. Beispiel:

```csharp
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

In den meisten Fällen ist es ratsam, die [CreateEventHubIfNotExists][] -Methoden zu verwenden, um bei einem Neustart des Diensts die Generierung von Ausnahmen zu vermeiden. Beispiel:

```csharp
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

Alle Event Hubs-Erstellungsvorgänge, z.B. [CreateEventHubIfNotExists][], erfordern für den jeweiligen Namespace Berechtigungen zum **Verwalten**. Falls Sie die Berechtigungen für Ihre Veröffentlichungs- oder Consumeranwendungen beschränken möchten, können Sie diese Aufrufe von Erstellungsvorgängen im Produktionscode vermeiden, wenn Sie Anmeldeinformationen mit begrenzten Berechtigungen verwenden.

Die [EventHubDescription](/dotnet/api/microsoft.servicebus.messaging.eventhubdescription)-Klasse enthält Details zu einem Event Hub, z.B. Autorisierungsregeln, Intervall der Nachrichtenaufbewahrung, Partitions-IDs, Status und Pfad. Sie können diese Klasse verwenden, um die Metadaten für einen Event Hub zu aktualisieren.

## <a name="create-an-event-hubs-client"></a>Erstellen eines Event Hubs-Clients
Die Hauptklasse für die Interaktion mit Event Hubs ist [Microsoft.ServiceBus.Messaging.EventHubClient][EventHubClient]. Mit dieser Klasse werden Funktionen für Absender und Empfänger bereitgestellt. Sie können diese Klasse mit der [Create](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.create)-Methode instanziieren. Dies wird im folgenden Beispiel veranschaulicht.

```csharp
var client = EventHubClient.Create(description.Path);
```

In dieser Methode werden die Service Bus-Verbindungsinformationen aus dem Abschnitt `appSettings` der Datei „App.config“ verwendet. Ein Beispiel für den `appSettings` -XML-Code, der zum Speichern der Service Bus-Verbindungsinformationen verwendet wird, finden Sie in der Dokumentation zur [Microsoft.ServiceBus.Messaging.EventHubClient.Create(System.String)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Create_System_String_) -Methode.

Eine weitere Option ist die Erstellung des Clients über eine Verbindungszeichenfolge. Diese Option funktioniert gut, wenn Azure-Workerrollen verwendet werden, weil Sie die Zeichenfolge in den Konfigurationseigenschaften für den Worker speichern können. Beispiel:

```csharp
EventHubClient.CreateFromConnectionString("your_connection_string");
```

Die Verbindungszeichenfolge hat das gleiche Format wie in der Datei „App.config“ für die vorherigen Methoden:

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[key]
```

Schließlich ist es auch möglich, ein [EventHubClient][]-Objekt aus einer [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory)-Instanz zu erstellen. Dies ist im folgenden Beispiel dargestellt.

```csharp
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

Es ist wichtig zu beachten, dass für zusätzliche [EventHubClient][] -Objekte, die aus einer Messagingfactory-Instanz erstellt werden, die gleiche zugrunde liegende TCP-Verbindung wiederverwendet wird. Daher verfügen diese Objekte über einen clientseitigen Grenzwert für den Durchsatz. Bei der [Create](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Create_System_String_) -Methode wird eine einzelne Messagingfactory wiederverwendet. Wenn Sie für einen einzelnen Absender einen sehr hohen Durchsatz benötigen, können Sie mehrere Messagingfactorys und ein [EventHubClient][] -Objekt aus jeder Messagingfactory erstellen.

## <a name="send-events-to-an-event-hub"></a>Senden von Ereignissen an einen Event Hub
Sie senden Ereignisse an einen Event Hub, indem Sie eine [EventData][]-Instanz erstellen und diese mit der [Send](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_)-Methode senden. Diese Methode verwendet einen einzelnen [EventData][]-Instanzparameter und sendet ihn synchron an einen Event Hub.

## <a name="event-serialization"></a>Ereignisserialisierung
Die [EventData][]-Klasse verfügt über [vier überladene Konstruktoren](/dotnet/api/microsoft.servicebus.messaging.eventdata#constructors_), für die verschiedene Parameter verwendet werden können, z.B. ein Objekt und Serialisierungsprogramm, ein Bytearray oder ein Datenstrom. Es ist auch möglich, die [EventData][]-Klasse zu instanziieren und den Textdatenstrom danach festzulegen. Wenn Sie JSON mit [EventData][] verwenden, können Sie mit **Encoding.UTF8.GetBytes()** das Bytearray für eine JSON-codierte Zeichenfolge abrufen.

## <a name="partition-key"></a>Partitionsschlüssel
Die [EventData][]-Klasse enthält eine [PartitionKey][]-Eigenschaft, mit der der Absender einen Wert angeben kann, der gehasht ist, um eine Partitionsanweisung zu erzeugen. Mit einem Partitionsschlüssel wird sichergestellt, dass alle Ereignisse mit dem gleichen Schlüssel an dieselbe Partition im Event Hub gesendet werden. Gemeinsame Partitionsschlüssel enthalten Benutzersitzungs-IDs und eindeutige Absender-IDs. Die [PartitionKey][]-Eigenschaft ist optional und kann bereitgestellt werden, wenn die Methode [Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) oder [Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendAsync_Microsoft_ServiceBus_Messaging_EventData_) verwendet wird. Wenn Sie keinen Wert für [PartitionKey][]angeben, werden gesendete Ereignisse per Roundrobin-Modell an Partitionen verteilt.

### <a name="availability-considerations"></a>Überlegungen zur Verfügbarkeit

Die Verwendung eines Partitionsschlüssels ist optional, und Sie sollten dies sorgfältig abwägen. In vielen Fällen ist die Verwendung eines Partitionsschlüssels empfehlenswert, wenn die Ereignisreihenfolge wichtig ist. Wenn Sie einen Partitionsschlüssel verwenden, ist für diese Partitionen die Verfügbarkeit auf einem einzelnen Knoten erforderlich, und mit der Zeit kann es zu Ausfällen kommen, beispielsweise beim Neustart und Patchen von Computeknoten. Wenn Sie daher eine Partitions-ID festlegen und diese Partition aus irgendeinem Grund nicht verfügbar ist, führt der Zugriff auf die Daten in dieser Partition zu einem Fehler. Wenn hohe Verfügbarkeit am erster Stelle steht, geben Sie keinen Partitionsschlüssel an. In diesem Fall werden Ereignisse über das oben beschriebene Roundrobin-Modell an Partitionen gesendet. In diesem Szenario treffen Sie explizit die Wahl zwischen Verfügbarkeit (keine Partitions-ID) und Konsistenz (Anheften von Ereignissen an eine Partitions-ID).

Ein weiterer Aspekt ist der Umgang mit Verzögerungen bei der Ereignisverarbeitung. In einigen Fällen kann es besser sein, Daten zu löschen und den Vorgang zu wiederholen, als zu versuchen, mit der Verarbeitung Schritt zu halten, wodurch möglicherweise weitere Verzögerungen bei der Downstreamverarbeitung verursacht werden. Bei einem Börsenticker ist es z.B. besser, auf vollständige aktuelle Daten zu warten. In einem Livechat oder VOIP-Szenario, möchten Sie die Daten hingegen schnell erhalten, selbst wenn sie nicht vollständig sind.

Vor dem Hintergrund dieser Überlegungen zur Verfügbarkeit können Sie eine der folgenden Strategien zur Fehlerbehandlung wählen:

- Beenden (Auslesen von Event Hubs beenden, bis die Fehler behoben sind)
- Löschen (Nachrichten sind unwichtig und können gelöscht werden)
- Wiederholen (Nachrichtenübermittlung angemessen wiederholen)
- [Unzustellbare Nachrichten](../service-bus-messaging/service-bus-dead-letter-queues.md) (Warteschlange oder anderen Event Hub verwenden, um nur die Nachrichten als unzustellbar zu kennzeichnen, die nicht verarbeitet werden konnten)

Weitere Informationen und eine Erläuterung zu den Vor-und Nachteilen zwischen Verfügbarkeit und Konsistenz finden Sie unter [Verfügbarkeit und Konsistenz in Event Hubs](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Sendevorgänge für Batchereignisse
Das Senden von Ereignissen in Batches kann den Durchsatz erheblich erhöhen. Die [SendBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendBatch_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__)-Methode verwendet einen **IEnumerable**-Parameter vom Typ [EventData][] und sendet den gesamten Batch als atomischen Vorgang an den Event Hub.

```csharp
public void SendBatch(IEnumerable<EventData> eventDataList);
```

Beachten Sie, dass ein einzelner Batch den Grenzwert von 256 KB für ein Ereignis nicht überschreiten darf. Darüber hinaus wird für jede Nachricht im Batch die gleiche Herausgeberidentität (Publisher Identity) verwendet. Der Absender ist dafür verantwortlich sicherzustellen, dass die maximale Ereignisgröße für den Batch nicht überschritten wird. Bei einer Überschreitung wird ein **Send** -Fehler für den Client generiert.

## <a name="send-asynchronously-and-send-at-scale"></a>Asynchrones Senden und Senden mit Skalierung
Sie können Ereignisse auch asynchron an einen Event Hub senden. Beim asynchronen Senden kann die Rate erhöht werden, mit der ein Client Ereignisse senden kann. Sowohl die [Send](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_)-Methode als auch die [SendBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendBatch_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__)-Methode ist in asynchronen Versionen verfügbar, die ein [Task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx)-Objekt zurückgeben. Mit dieser Vorgehensweise kann zwar der Durchsatz erhöht werden, aber sie kann auch dazu führen, dass der Client auch dann weiter Ereignisse sendet, während er durch den Event Hubs-Dienst gedrosselt wird. Dies kann bei einer falschen Implementierung zur Folge haben, dass für den Client Fehler oder verloren gegangene Nachrichten auftreten. Darüber hinaus können Sie die [RetryPolicy](/dotnet/api/microsoft.servicebus.messaging.cliententity#Microsoft_ServiceBus_Messaging_ClientEntity_RetryPolicy)-Eigenschaft auf dem Client verwenden, um Wiederholungsversuche des Clients zu steuern.

## <a name="create-a-partition-sender"></a>Erstellen eines Elements für das Senden an Partitionen
Am häufigsten werden Ereignisse zwar ohne Partitionsschlüssel an einen Event Hub gesendet, aber es kann auch vorkommen, dass Sie Ereignisse direkt an eine bestimmte Partition senden möchten. Beispiel:

```csharp
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

[CreatePartitionedSender](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_CreatePartitionedSender_System_String_) gibt ein [EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender)-Objekt zurück, das Sie verwenden können, um Ereignisse auf einer bestimmten Event Hub-Partition zu veröffentlichen.

## <a name="event-consumers"></a>Ereignisconsumer
Der Event Hubs-Dienst verfügt über zwei primäre Modelle für die Ereignisnutzung: direkte Empfänger und allgemeinere Abstraktionen, z.B. [EventProcessorHost][]. Direkte Empfänger sind verantwortlich für ihre eigene Koordinierung des Zugriffs auf Partitionen innerhalb einer Consumergruppe.

### <a name="direct-consumer"></a>Direkter Consumer
Die direkteste Möglichkeit zum Lesen aus einer Partition innerhalb einer Consumergruppe ist die Verwendung der [EventHubReceiver](/dotnet/apie/microsoft.servicebus.messaging.eventhubreceiver) -Klasse. Um eine Instanz dieser Klasse zu erstellen, müssen Sie eine Instanz der [EventHubConsumerGroup](/dotnet/api/microsoft.servicebus.messaging.eventhubconsumergroup) -Klasse verwenden. Im folgenden Beispiel muss die Partitions-ID angegeben werden, wenn der Empfänger für die Consumergruppe erstellt wird.

```csharp
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

Die [CreateReceiver](/dotnet/api/microsoft.servicebus.messaging.eventhubconsumergroup#methods_summary)-Methode verfügt über mehrere Überladungen, mit denen die Erstellung der Leseeinheit gesteuert wird. Diese Methoden umfassen das Angeben eines Offsets als Zeichenfolge oder Zeitstempel sowie die Möglichkeit zum Festlegen, ob der angegebene Offset in den zurückgegebenen Datenstrom eingebunden oder ob er danach gestartet werden soll. Nach der Erstellung des Empfängers können Sie beginnen, Ereignisse für das zurückgegebene Objekt zu empfangen. Die [Receive](/dotnet/api/microsoft.servicebus.messaging.eventhubreceiver#methods_summary)-Methode hat vier Überladungen, mit denen die Parameter des Empfangsvorgangs gesteuert werden, z.B. Batchgröße und Wartezeit. Sie können die asynchronen Versionen dieser Methoden verwenden, um den Durchsatz eines Consumers zu erhöhen. Beispiel:

```csharp
bool receive = true;
string myOffset;
while(receive)
{
    var message = receiver.Receive();
    myOffset = message.Offset;
    string body = Encoding.UTF8.GetString(message.GetBytes());
    Console.WriteLine(String.Format("Received message offset: {0} \nbody: {1}", myOffset, body));
}
```

Im Hinblick auf eine bestimmte Partition werden die Nachrichten in der Reihenfolge empfangen, in der sie an den Event Hub gesendet wurden. Der Offset ist ein Zeichenfolgentoken, das zum Identifizieren einer Nachricht in einer Partition verwendet wird.

Beachten Sie, dass mit einer einzelnen Partition innerhalb einer Consumergruppe nicht mehr als fünf Leser gleichzeitig verbunden sein können. Wenn Leseeinheiten verbunden oder getrennt werden, können die dazugehörigen Sitzungen noch einige Minuten lang aktiv bleiben, bevor der Dienst die Trennung erkennt. Während dieses Zeitraums kann für die erneute Herstellung der Verbindung mit einer Partition ein Fehler auftreten. Ein vollständiges Beispiel für das Schreiben eines direkten Empfängers für Event Hubs finden Sie im Beispiel [Event Hubs – Direkte Empfänger](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6).

### <a name="event-processor-host"></a>Ereignisprozessorhost
Die [EventProcessorHost][] -Klasse verarbeitet Daten aus Event Hubs. Sie sollten diese Implementierung verwenden, wenn Sie Ereignisleser auf der .NET-Plattform erstellen. [EventProcessorHost][] wird eine threadsichere Laufzeitumgebung mit mehreren Prozessen für Ereignisprozessorimplementierungen bereitgestellt, die auch die Erstellung von Prüfpunkten und die Leaseverwaltung für Partitionen ermöglicht.

Sie können [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) implementieren, um die [EventProcessorHost][]-Klasse zu verwenden. Diese Schnittstelle enthält drei Methoden:

* [OpenAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_OpenAsync_Microsoft_ServiceBus_Messaging_PartitionContext_)
* [CloseAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_CloseAsync_Microsoft_ServiceBus_Messaging_PartitionContext_Microsoft_ServiceBus_Messaging_CloseReason_)
* [ProcessEventsAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_ProcessEventsAsync_Microsoft_ServiceBus_Messaging_PartitionContext_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__)

Instanziieren Sie zum Starten der Ereignisverarbeitung die [EventProcessorHost][]-Klasse, und geben Sie die entsprechenden Parameter für Ihren Event Hub an. Rufen Sie anschließend [RegisterEventProcessorAsync](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost#Microsoft_ServiceBus_Messaging_EventProcessorHost_RegisterEventProcessorAsync__1) auf, um Ihre [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor)-Implementierung bei der Laufzeit zu registrieren. An diesem Punkt versucht der Host, einen Lease für jede Partition im Event Hub zu erhalten, indem er einen „gierigen“ Algorithmus verwendet. Diese Leases gelten für einen bestimmten Zeitraum und müssen anschließend erneuert werden. Wenn neue Knoten (hier: Workerinstanzen) in den Onlinezustand versetzt werden, geben sie Leasereservierungen heraus. Im Laufe der Zeit wird die Arbeitsauslastung dann auf die Knoten verteilt, da jeder Knoten versucht, mehr Leases zu erlangen.

![Ereignisprozessorhost](./media/event-hubs-programming-guide/IC759863.png)

Im Laufe der Zeit wird somit ein Gleichgewicht erreicht. Diese dynamische Funktion ermöglicht, dass die CPU-basierte automatische Skalierung sowohl beim zentralen Herunterskalieren als auch beim zentralen Hochskalieren auf Consumer angewendet wird. Da Event Hubs nicht über ein direktes Konzept in Bezug auf die Nachrichtenanzahl verfügen, ist die durchschnittliche CPU-Auslastung häufig das beste Verfahren zum Messen der Back-End- bzw. Consumerskalierung. Wenn Herausgeber beginnen, mehr Ereignisse zu veröffentlichen, als von den Consumern verarbeitet werden können, kann der CPU-Anstieg auf den Consumern verwendet werden, um eine automatische Skalierung in Bezug auf die Anzahl der Workerinstanzen auszulösen.

Außerdem implementiert die [EventProcessorHost][] -Klasse ein auf Azure Storage-basiertes Verfahren für die Prüfpunktausführung. Bei diesem Verfahren wird der Offset pro Partition gespeichert, damit jeder Consumer ermitteln kann, wie der letzte Prüfpunkt des vorherigen Consumers lautete. Da Partitionen per Lease zwischen Knoten wechseln, ist dies das Synchronisierungsverfahren, das die Auslastungsverteilung ermöglicht.

## <a name="publisher-revocation"></a>Herausgebersperrung
Zusätzlich zu den erweiterten Laufzeitfunktionen von [EventProcessorHost][] ermöglichen Event Hubs die Herausgebersperrung, damit bestimmte Herausgeber blockiert werden und keine Ereignisse an einen Event Hub senden können. Diese Funktionen sind besonders nützlich, wenn das Token eines Herausgebers gefährdet ist oder ein Softwareupdate ein unangemessenes Verhalten bewirkt. In diesen Fällen kann die Identität des Herausgebers, die Teil des SAS-Tokens ist, blockiert werden, um das Veröffentlichen von Ereignissen zu verhindern.

Weitere Informationen zum Sperren von Herausgebern und zum Senden an Event Hubs als Herausgeber finden Sie im Beispiel [Service Bus Event Hubs Large Scale Secure Publishing](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) (Service Bus Event Hubs – Sicheres Veröffentlichen in größerem Umfang).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Event Hubs-Szenarien finden Sie unter diesen Links:

* [Übersicht über die Event Hubs-API](event-hubs-api-overview.md)
* [Was sind Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Verfügbarkeit und Konsistenz in Event Hubs](event-hubs-availability-and-consistency.md)
* [Referenz zur Ereignisprozessorhost-API](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.servicebus.messaging.eventhubclient
[EventData]: /dotnet/api/microsoft.servicebus.messaging.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost

