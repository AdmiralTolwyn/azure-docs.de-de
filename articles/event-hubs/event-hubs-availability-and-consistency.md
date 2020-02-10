---
title: 'Verfügbarkeit und Konsistenz: Azure Event Hubs | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie maximale Verfügbarkeit und Konsistenz in Azure Event Hubs mit Partitionen erzielen.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2020
ms.author: shvija
ms.openlocfilehash: 808e813ad90626acec893a021634566f091c895f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904486"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Verfügbarkeit und Konsistenz in Event Hubs

## <a name="overview"></a>Übersicht
Azure Event Hubs verwendet ein [Partitionierungsmodell](event-hubs-scalability.md#partitions) zur Verbesserung der Verfügbarkeit und Parallelisierung innerhalb eines einzelnen Event Hubs. Wenn ein Event Hub z.B. über vier Partitionen verfügt und eine dieser Partitionen im Rahmen eines Lastenausgleichsvorgangs von einem Server zu einem anderen verschoben wird, können Sie weiterhin an drei andere Partitionen Daten senden und von dort empfangen. Darüber hinaus ermöglichen mehr Partitionen, dass mehr gleichzeitige Leser Ihre Daten verarbeiten können, wodurch sich der aggregierte Durchsatz verbessert. Die Kenntnis der Auswirkungen von Partitionierung und Reihenfolge in einem verteilten System ist ein wichtiger Aspekt des Lösungsentwurfs.

Um den Kompromiss zwischen Reihenfolge und Verfügbarkeit zu erklären, betrachten Sie das [CAP-Theorem](https://en.wikipedia.org/wiki/CAP_theorem), auch bekannt als Brewers Theorem. Dieses Theorem erörtert die Wahl zwischen Konsistenz, Verfügbarkeit und Ausfalltoleranz. Es besagt, dass bei Systemen, die durch ein Netzwerk partitioniert werden, immer Kompromisse zwischen Konsistenz und Verfügbarkeit gemacht werden müssen.

Brewers Theorem definiert Konsistenz und Verfügbarkeit wie folgt:
* Ausfalltoleranz: Die Fähigkeit eines Datenverarbeitungssystems, die Datenverarbeitung auch dann fortzusetzen, wenn ein Partitionsausfall auftritt.
* Verfügbarkeit: Von einem nicht fehlerhaften Knoten erfolgt innerhalb eines akzeptablen Zeitraums eine angemessene Reaktion (ohne Fehler oder Timeouts).
* Konsistenz: Ein Lesevorgang gibt garantiert den letzten Schreibvorgang eines bestimmten Clients zurück.

## <a name="partition-tolerance"></a>Ausfalltoleranz
Event Hubs basiert auf einem partitionierten Datenmodell. Sie können die Anzahl der Partitionen in Ihrem Event Hub während des Setups konfigurieren, aber Sie können diesen Wert später nicht ändern. Da Sie Partitionen mit Event Hubs verwenden müssen, müssen Sie nur eine Entscheidung hinsichtlich Verfügbarkeit und Konsistenz der Anwendung treffen.

## <a name="availability"></a>Verfügbarkeit
Die einfachste Möglichkeit, erste Schritte mit Event Hubs auszuführen, ist die Verwendung des Standardverhaltens. Wenn Sie ein neues **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** -Objekt erstellen und die **[Send](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** -Methode verwenden, werden die Ereignisse automatisch auf die Partitionen Ihres Event Hubs verteilt. Dieses Verhalten ermöglicht das größte Maß an Betriebszeit.

Für Anwendungsfälle, die maximale Betriebszeit erfordern, wird dieses Modell bevorzugt.

## <a name="consistency"></a>Konsistenz
In manchen Szenarios kann die Reihenfolge der Ereignisse wichtig sein. Nehmen Sie beispielsweise an, dass Ihr Back-End-System einen Updatebefehl vor einem Löschbefehl ausführen soll. In diesem Fall können Sie entweder den Partitionsschlüssel für ein Ereignis festlegen, oder ein `PartitionSender`-Objekt verwenden, um nur Ereignisse an eine bestimmte Partition zu senden. Auf diese Weise wird sichergestellt, dass diese Ereignisse ggf. in der richtigen Reihenfolge aus der Partition gelesen werden.

Bedenken Sie bei dieser Konfiguration, dass Sie eine Fehlerantwort erhalten, wenn die Partition, an die Sie senden, nicht verfügbar ist. Zum Vergleich: Wenn Sie keine bestimmte Partition bevorzugen, sendet der Event Hubs-Dienst das Ereignis an die nächste verfügbare Partition.

Eine mögliche Lösung, um die Reihenfolge sicherzustellen und dabei auch die Betriebszeit zu maximieren, wäre das Aggregieren der Ereignisse als Teil Ihrer Anwendung zur Ereignisverarbeitung. Die einfachste Möglichkeit, dies zu erreichen, besteht darin, das Ereignis mit einer benutzerdefinierten Sequenznummerneigenschaft zu stempeln. Der folgende Code zeigt ein Beispiel:

#### <a name="azuremessagingeventhubs-500-or-latertablatest"></a>[Azure.Messaging.EventHubs (5.0.0 oder höher](#tab/latest))

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earliertabold"></a>[Microsoft.Azure.EventHubs (4.1.0 oder früher)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

Bei diesem Beispiel wird das Ereignis an eine der verfügbaren Partitionen in Ihrem Event Hub gesendet und die entsprechende Sequenznummer aus Ihrer Anwendung festgelegt. Diese Lösung erfordert, dass der Status von der Verarbeitungsanwendung beibehalten werden muss, aber sie bietet Ihren Absendern einen Endpunkt, dessen Verfügbarkeit wahrscheinlicher ist.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Event Hubs finden Sie unter den folgenden Links:

* [Event Hubs-Dienst: Übersicht](event-hubs-what-is-event-hubs.md)
* [Erstellen eines Event Hubs](event-hubs-create.md)
