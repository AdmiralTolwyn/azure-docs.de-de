---
title: "Bewährte Methoden zur Verbesserung der Leistung mit Service Bus | Microsoft Docs"
description: Beschreibt, wie Azure Service Bus verwendet wird, um die Leistung zu optimieren, wenn Brokernachrichten ausgetauscht werden.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e756c15d-31fc-45c0-8df4-0bca0da10bb2
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5402481a0adc27474f7ddd9b5be1d71dc2c91d44


---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Bewährte Methoden für Leistungsoptimierungen mithilfe von Service Bus Messaging
In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Service Bus Messaging die Leistung beim Austausch von Brokernachrichten optimieren. Der erste Teil befasst sich mit den verschiedenen Mechanismen zur Leistungssteigerung. Der zweite Teil bietet eine Anleitung zur Verwendung von Service Bus auf eine Weise, die die beste Leistung in einem bestimmten Szenario ermöglichen kann.

Im Rahmen dieses Themas bezieht sich der Begriff „Client“ auf eine Entität, die auf Service Bus zugreift. Ein Client kann die Rolle eines Absenders oder eines Empfängers annehmen. Der Begriff „Absender“ wird für einen Service Bus-Warteschlangenclient oder für einen Service Bus-Themenclient verwendet, der Nachrichten an eine Service Bus-Warteschlange oder an ein Service Bus-Thema sendet. Der Begriff „Empfänger“ bezieht sich auf einen Service Bus-Warteschlangenclient oder einen auf einen Service Bus-Abonnementclient, der Nachrichten von einer Service Bus-Warteschlange oder einem Service Bus-Abonnement empfängt.

In diesen Abschnitten werden einige Konzepte erläutert, die Service Bus verwendet, um die Performance zu steigern.

## <a name="protocols"></a>Protokolle
Service Bus ermöglicht Clients das Senden und Empfangen von Nachrichten über drei Protokolle.

1. Advanced Message Queuing Protocol (AMQP)
2. Service Bus Messaging Protocol (SBMP)
3. HTTP

AMQP und SBMP sind effizienter, da sie die Verbindung mit Service Bus aufrecht erhalten, solange die Messaging-Factory vorhanden ist. Außerdem implementiert es die Batchverarbeitung und Vorabrufe. Sofern nicht anders angegeben, wird für alle Inhalte in diesem Thema AMQP oder SBMP verwendet.

## <a name="reusing-factories-and-clients"></a>Wiederverwenden von Factorys und Clients
Service Bus-Clientobjekte wie [QueueClient][QueueClient] oder [MessageSender][MessageSender] werden durch ein [MessagingFactory][MessagingFactory]-Objekt erstellt, das auch die interne Verwaltung von Verbindungen ermöglicht. Es empfiehlt sich, Messagingfactorys oder Warteschlangen-, Themen- und Abonnementclients nicht zu schließen, nachdem Sie eine Nachricht gesendet haben, und dann erneut zu erstellen, wenn Sie die nächste Nachricht senden. Durch Schließen einer Messagingfactory wird die Verbindung mit dem Service Bus-Dienst gelöscht, und eine neue Verbindung wird beim erneuten Erstellen der Factory hergestellt. Das Herstellen einer Verbindung ist ein aufwändiger Vorgang, den Sie vermeiden können, indem Sie eine Factory und die Clientobjekte für mehrere Vorgänge verwenden. Sie können das [QueueClient][QueueClient]-Objekt gefahrlos zum Senden von Nachrichten von gleichzeitigen asynchronen Vorgängen und mehreren Threads verwenden. 

## <a name="concurrent-operations"></a>Parallele Vorgänge
Das Ausführen eines Vorgangs (Senden, Empfangen, Löschen usw.) nimmt einige Zeit in Anspruch. Dieser Zeitraum umfasst die Verarbeitung des Vorgangs durch den Service Bus-Dienst sowie die Latenz der Anforderung und der Antwort. Die Vorgänge müssen parallel ausgeführt werden, um die Anzahl von Vorgängen pro Zeitraum zu erhöhen. Hierzu können Sie wie folgt vorgehen:

* **Asynchrone Vorgänge**: Der Client plant Vorgänge durch Ausführen von asynchronen Vorgängen. Die nächste Anforderung wird gestartet, bevor die vorherige Anforderung abgeschlossen ist. Im Folgenden sehen Sie ein Beispiel für einen asynchronen Sendevorgang:
  
  ```
  BrokeredMessage m1 = new BrokeredMessage(body);
  BrokeredMessage m2 = new BrokeredMessage(body);
  
  Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #1");
    });
  Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #2");
    });
  Task.WaitAll(send1, send2);
  Console.WriteLine("All messages sent");
  ```
  
  Dies ist ein Beispiel für einen asynchronen Empfangsvorgang:
  
  ```
  Task receive1 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
  Task receive2 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
  
  Task.WaitAll(receive1, receive2);
  Console.WriteLine("All messages received");
  
  async void ProcessReceivedMessage(Task<BrokeredMessage> t)
  {
    BrokeredMessage m = t.Result;
    Console.WriteLine("{0} received", m.Label);
    await m.CompleteAsync();
    Console.WriteLine("{0} complete", m.Label);
  }
  ```
* **Mehrere Factorys**: Alle Clients (Absender und Empfänger), die von der gleichen Factory erstellt werden, verwenden die gleiche TCP-Verbindung. Der maximale Nachrichtendurchsatz wird durch die Anzahl der Vorgänge beschränkt, die diese TCP-Verbindung durchlaufen können. Der Durchsatz, der mit einer einzelnen Factory erzielt werden kann, hängt stark von der TCP-Roundtripzeit und der Nachrichtengröße ab. Um bessere Durchsatzraten zu erzielen, sollten Sie mehrere Messagingfactorys verwenden.

## <a name="receive-mode"></a>Empfangsmodus
Beim Erstellen eines Warteschlangen- oder Abonnementclients können Sie einen Empfangsmodus angeben: *PeekLock* oder *ReceiveAndDelete*. Der Standardempfangsmodus ist [PeekLock][PeekLock]. Beim Betrieb in diesem Modus sendet der Client eine Anforderung zum Empfangen einer Nachricht von Service Bus. Nachdem der Client die Nachricht empfangen hat, sendet er eine Anforderung zum Abschließen der Nachricht.

Wenn der Empfangsmodus auf [ReceiveAndDelete][ReceiveAndDelete] festgelegt wird, werden beide Schritte in einer einzelnen Anforderung kombiniert. Dies reduziert die Anzahl der Vorgänge und kann den Gesamtnachrichtendurchsatz verbessern. Diese Leistungssteigerung kann zu Nachrichtenverlusten führen.

Service Bus unterstützt keine Transaktionen für ReceiveAndDelete-Vorgänge. Darüber hinaus ist die PeekLock-Semantik für alle Szenarios erforderlich, in denen der Client eine Nachricht zurückstellen oder [in eine Warteschlange für unzustellbare Nachrichten](service-bus-dead-letter-queues.md) verschieben möchte.

## <a name="client-side-batching"></a>Clientseitige Batchverarbeitung
Durch die clientseitige Batchverarbeitung kann ein Warteschlangen- oder Themenclient das Senden einer Nachricht für einen bestimmten Zeitraum verzögern. Wenn der Client während dieses Zeitraums weitere Nachrichten sendet, werden die Nachrichten in einem einzigen Batch übertragen. Die clientseitige Batchverarbeitung bewirkt auch, dass ein Warteschlangen-/Abonnementclient mehrere Anforderungen zum **Abschließen** als Batch in eine einzelne Anforderung aufnehmen kann. Die Batchverarbeitung ist nur für asynchrone Vorgänge zum **Senden** und **Abschließen** verfügbar. Synchrone Vorgänge werden sofort an den Service Bus-Dienst gesendet. Die Batchverarbeitung erfolgt nicht für Vorschau- oder Empfangsvorgänge und auch nicht clientübergreifend.

Wenn ein Batch die maximale Nachrichtengröße überschreitet, wird die letzte Nachricht aus dem Batch entfernt, und der Client sendet das Batch sofort. Die letzte Nachricht wird die erste Nachricht im nächsten Batch. Ein Client verwendet standardmäßig ein Batchintervall von 20 ms. Sie können das Batchintervall ändern, indem Sie die Eigenschaft [BatchFlushInterval][BatchFlushInterval] vor dem Erstellen der Messagingfactory festlegen. Diese Einstellung wirkt sich auf alle Clients aus, die von dieser Factory erstellt werden.

Legen Sie zum Deaktivieren der Batchverarbeitung die Eigenschaft [BatchFlushInterval][BatchFlushInterval] auf **TimeSpan.Zero** fest. Beispiel:

```
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Die Batchverarbeitung wirkt sich nicht auf die Anzahl der abrechenbaren Messagingvorgänge aus und ist nur für das Service Bus-Clientprotokoll verfügbar. Das HTTP-Protokoll unterstützt keine Batchverarbeitung.

## <a name="batching-store-access"></a>Batchverarbeitung des Speicherzugriffs
Um den Durchsatz von Warteschlangen/Themen/Abonnements zu erhöhen, verarbeitet Service Bus beim Schreiben in den internen Speicher mehrere Nachrichten als Batch. Ist diese Funktion für eine Warteschlange oder ein Thema aktiviert, erfolgt das Schreiben von Nachrichten in den Speicher als Batch. Ist diese Funktion für eine Warteschlange oder ein Abonnement aktiviert, erfolgt das Löschen von Nachrichten aus dem Speicher als Batch. Wenn für eine Entität Speicherzugriff als Batch aktiviert ist, verzögert Service Bus einen Schreibvorgang in den Speicher für diese Entität für bis zu 20 ms. Weitere Speichervorgänge, die während dieses Intervalls auftreten, werden dem Batch hinzugefügt. Speicherzugriff als Batch wirkt sich nur auf Vorgänge zum **Senden** und **Abschließen** aus, Empfangsvorgänge sind nicht betroffen. Speicherzugriff als Batch ist eine Eigenschaft einer Entität. Die Batchverarbeitung erfolgt für alle Entitäten, die Speicherzugriff als Batch ermöglichen.

Beim Erstellen neuer Warteschlangen, Themen oder Abonnements ist der Speicherzugriff als Batch standardmäßig aktiviert. Um den Speicherzugriff als Batch zu deaktivieren, legen Sie vor dem Erstellen der Entität die Eigenschaft [EnableBatchedOperations][EnableBatchedOperations] auf **false** fest. Beispiel:

```
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Der Speicherzugriff als Batch wirkt sich nicht auf die Anzahl abrechenbarer Messagingvorgänge aus und ist eine Eigenschaft von Warteschlangen, Themen oder Abonnements. Er ist unabhängig vom Empfangsmodus und dem Protokoll, das zwischen einem Client und dem Service Bus-Dienst verwendet wird.

## <a name="prefetching"></a>Vorabrufe
Vorabrufe ermöglichen es dem Warteschlangen- oder Abonnementclient, weitere Nachrichten aus dem Dienst zu laden, wenn er einen Empfangsvorgang ausführt. Der Client speichert diese Nachrichten in einem lokalen Cache. Die Cachegröße wird durch die Eigenschaften [QueueClient.PrefetchCount][QueueClient.PrefetchCount] oder [SubscriptionClient.PrefetchCount][SubscriptionClient.PrefetchCount] bestimmt. Jeder Client, der Vorabrufe ermöglicht, verwaltet seinen eigenen Cache. Ein Cache wird nicht für andere Clients freigegeben. Wenn der Client einen Empfangsvorgang initiiert und sein Cache leer ist, überträgt der Dienst ein Nachrichtenbatch. Die Größe des Batches entspricht der Größe des Caches oder 256 KB (dem jeweils kleinerem Wert). Wenn der Client einen Empfangsvorgang initiiert und der Cache eine Nachricht enthält, wird die Nachricht aus dem Cache abgerufen.

Wenn eine Nachricht vorab abgerufen wird, sperrt der Dienst die vorab abgerufene Nachricht. Auf diese Weise kann die vorab abgerufene Nachricht nicht von einem anderen Empfänger empfangen werden. Wenn der Empfänger die Nachricht nicht abschließen kann, bevor die Sperre abläuft, wird die Nachricht für andere Empfänger verfügbar gemacht. Die vorab abgerufene Kopie der Nachricht verbleibt im Cache. Der Empfänger, der die abgelaufene Kopie aus dem Cache verarbeitet, empfängt eine Ausnahme, wenn er versucht, diese Nachricht abzuschließen. Standardmäßig läuft die Nachrichtensperrre nach 60 Sekunden ab. Dieser Wert kann auf 5 Minuten erhöht werden. Um die Nutzung abgelaufener Nachrichten zu verhindern, sollten die Cachegröße immer kleiner als die Anzahl der Nachrichten sein, die von einem Client innerhalb des Sperrintervalls genutzt werden können.

Bei Verwendung des standardmäßigen Ablaufs der Sperre (60 Sekunden) hat sich als Wert für [SubscriptionClient.PrefetchCount][SubscriptionClient.PrefetchCount] das 20-Fache der maximalen Verarbeitungsraten aller Empfänger der Factory bewährt. Beispiel: Eine Factory erstellt 3 Empfänger, und jeder Empfänger kann bis zu 10 Nachrichten pro Sekunde verarbeiten. Der Wert für den Vorabruf sollte 20\*3\*10 = 600 nicht überschreiten. Standardmäßig ist [QueueClient.PrefetchCount][QueueClient.PrefetchCount] auf 0 festgelegt, was bedeutet, dass keine weiteren Nachrichten vom Dienst abgerufen werden.

Der Vorabruf von Nachrichten vergrößert den Gesamtdurchsatz für eine Warteschlange oder ein Abonnement, da sich dadurch die Gesamtzahl von Nachrichtenvorgängen bzw. Roundtrips verringert. Das Abrufen der ersten Nachricht dauert jedoch länger (aufgrund der gestiegenen Nachrichtengröße). Das Empfangen vorab abgerufener Nachrichten ist schneller, weil diese Nachrichten bereits vom Client heruntergeladen wurden.

Die Eigenschaft für die Gültigkeitsdauer (Time-to-Live, TTL) einer Nachricht wird vom Server zu dem Zeitpunkt überprüft, wenn der Server die Nachricht an den Client sendet. Der Client überprüft die TTL-Eigenschaft der Nachricht nicht, wenn die Nachricht empfangen wird. Stattdessen kann die Nachricht empfangen werden, auch wenn die Gültigkeitsdauer der Nachricht abgelaufen ist, während sich die Nachricht im Cache des Clients befand.

Der Vorabruf wirkt sich nicht auf die Anzahl der abrechenbaren Messagingvorgänge aus und ist nur für das Service Bus-Clientprotokoll verfügbar. Das HTTP-Protokoll unterstützt keinen Vorabruf. Vorabrufe sind für synchrone und asynchrone Empfangsvorgänge verfügbar.

## <a name="express-queues-and-topics"></a>Express-Warteschlangen und -Themen
Express-Entitäten ermöglichen Szenarien mit hohem Durchsatz und reduzierter Latenz. Wenn bei Express-Entitäten eine Nachricht an eine Warteschlange oder an ein Thema gesendet wird, wird sie nicht sofort im Messagingspeicher gespeichert. Stattdessen wird sie im Arbeitsspeicher zwischengespeichert. Wenn eine Nachricht mehr als ein paar Sekunden in der Warteschlange bleibt, wird sie automatisch in einen stabilen Speicher geschrieben und so vor Datenverlusten aufgrund eines Ausfalls geschützt. Das Schreiben der Nachricht in einen Speichercache erhöht den Durchsatz und verringert die Latenz, da zu dem Zeitpunkt, zu dem die Nachricht gesendet wird, kein Zugriff auf den stabilen Speicher erfolgt. Nachrichten, die innerhalb weniger Sekunden genutzt werden, werden nicht in den Messagingspeicher geschrieben. Das folgende Beispiel erstellt ein Express-Thema.

```
TopicDescription td = new TopicDescription(TopicName);
td.EnableExpress = true;
namespaceManager.CreateTopic(td);
```

Wenn eine Nachricht mit wichtigen Informationen, die nicht verloren gehen darf, an eine Express-Entität gesendet wird, kann der Absender in Service Bus erzwingen, dass die Nachricht sofort in den stabilen Speicher übertragen wird, indem die Eigenschaft [ForcePersistence][ForcePersistence] auf **true** festgelegt wird.

## <a name="use-of-partitioned-queues-or-topics"></a>Verwenden partitionierter Warteschlangen oder Themen
Intern verwendet Service Bus den gleichen Knoten und den gleichen Messagingspeicher, um sämtliche Nachrichten für eine Messagingentität (Warteschlange oder Thema) zu verarbeiten und zu speichern. Partitionierte Warteschlangen oder Themen sind hingegen auf mehrere Knoten und Messagingspeicher verteilt. Sie bieten einen höheren Durchsatz als reguläre Warteschlangen und Themen und zeichnen sich zudem durch eine höhere Verfügbarkeit aus. Um eine partitionierte Entität zu erstellen, legen Sie die Eigenschaft [EnablePartitioning][EnablePartitioning] auf **true** fest, wie im folgenden Beispiel gezeigt. Weitere Informationen zu partitionierten Entitäten finden Sie unter [Partitionierte Messagingentitäten][Partitionierte Messagingentitäten].

```
// Create partitioned queue.
QueueDescription qd = new QueueDescription(QueueName);
qd.EnablePartitioning = true;
namespaceManager.CreateQueue(qd);
```

## <a name="use-of-multiple-queues"></a>Verwenden mehrerer Warteschlangen
Falls keine partitionierten Warteschlangen oder Themen verwendet werden können oder die erwartete Last nicht von einer partitionierten Warteschlange oder von einem partitionierten Thema bewältigt werden kann, müssen mehrere Messagingentitäten verwendet werden. Wenn Sie mehrere Entitäten verwenden, erstellen Sie einen dedizierten Client für jede Entität, statt den gleichen Client für alle Entitäten zu nutzen.

## <a name="development-testing-features"></a>Entwicklungs- und Testfunktionen
Service Bus verfügt über eine Funktion, die speziell für die Entwicklung verwendet wird und **nie in Produktionskonfigurationen eingesetzt werden sollte**.

[TopicDescription.EnableFilteringMessagesBeforePublishing](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing.aspx)

* Wenn dem Thema neue Regeln oder Filter hinzugefügt werden, kann mit „EnableFilteringMessagesBeforePublishing“ sichergestellt werden, dass der neue Filterausdruck wie erwartet funktioniert.

## <a name="scenarios"></a>Szenarios
In den folgenden Abschnitten werden normale Messagingszenarien und die bevorzugten Service Bus-Einstellungen beschrieben. Durchsatzraten werden als klein (weniger als 1 Nachricht/Sekunde), mittel (1 Nachricht/Sekunde oder mehr, aber weniger als 100 Nachrichten/Sekunde) und hoch (100 Nachrichten/Sekunde oder mehr) klassifiziert. Die Anzahl der Clients wird als klein (5 oder weniger), mittel (mehr als 5, aber weniger als oder gleich 20) und groß (mehr als 20) klassifiziert.

### <a name="high-throughput-queue"></a>Warteschlange mit hohem Durchsatz
Ziel: Maximieren des Durchsatzes einer einzelnen Warteschlange. Die Anzahl der Absender und Empfänger ist klein.

* Verwenden Sie eine partitionierte Warteschlange für verbesserte Leistung und Verfügbarkeit.
* Verwenden Sie zum Erhöhen der Gesamtsenderate an die Warteschlange mehrere Messagingfactorys, um Absender zu erstellen. Verwenden Sie für jeden Absender asynchrone Vorgänge oder mehrere Threads.
* Verwenden Sie zum Erhöhen der Gesamtempfangsrate von der Warteschlange mehrere Messagingfactorys, um Empfänger zu erstellen.
* Verwenden Sie asynchrone Vorgänge, um die clientseitige Batchverarbeitung zu nutzen.
* Legen Sie das Batchintervall auf 50 ms fest, um die Anzahl der Service Bus-Clientprotokollübertragungen zu verringern. Wenn mehrere Absender verwendet werden, erhöhen Sie das Batchintervall auf 100 ms.
* Lassen Sie den Speicherzugriff als Batch aktiviert. Dies erhöht die Gesamtrate, mit der Nachrichten in die Warteschlange geschrieben werden können.
* Legen Sie den Wert für Vorabrufe auf das 20-fache der maximalen Verarbeitungsraten aller Empfänger einer Factory fest. Dies verringert die Anzahl der Service Bus-Clientprotokollübertragungen.

### <a name="multiple-high-throughput-queues"></a>Mehrere Warteschlangen mit hohem Durchsatz
Ziel: Maximieren des Gesamtdurchsatzes mehrerer Warteschlangen. Der Durchsatz einer einzelnen Warteschlange ist mittel oder hoch.

Um den maximalen Durchsatz für mehrere Warteschlangen zu erhalten, verwenden Sie die beschriebenen Einstellungen, um den Durchsatz einer einzelnen Warteschlange zu maximieren. Verwenden Sie außerdem unterschiedliche Factorys zum Erstellen von Clients, die an verschiedene Warteschlangen senden bzw. von ihnen empfangen.

### <a name="low-latency-queue"></a>Warteschlange mit niedriger Latenz
Ziel: Minimieren der End-to-End-Latenz einer Warteschlange oder eines Themas. Die Anzahl der Absender und Empfänger ist klein. Der Durchsatz der Warteschlange ist klein oder mittel.

* Verwenden Sie eine partitionierte Warteschlange für verbesserte Verfügbarkeit.
* Deaktivieren Sie die clientseitige Batchverarbeitung. Der Client sendet eine Nachricht sofort.
* Deaktivieren Sie den Speicherzugriff als Batch. Der Dienst schreibt die Nachricht sofort in den Speicher.
* Wenn Sie einen einzelnen Client verwenden, legen Sie den Wert für den Vorabruf auf das 20-fache der Verarbeitungsrate des Empfängers fest. Wenn mehrere Nachrichten gleichzeitig in der Warteschlange ankommen, überträgt das Service Bus-Clientprotokoll sie alle zur gleichen Zeit. Wenn der Client die nächste Nachricht empfängt, befindet sich diese Nachricht bereits im lokalen Cache. Der Cache sollte klein sein.
* Wenn mehrere Clients verwendet werden, legen Sie den Wert für den Vorabruf auf 0 fest. Auf diese Weise kann der zweite Client die zweite Nachricht empfangen, während der erste Client noch die erste Nachricht verarbeitet.

### <a name="queue-with-a-large-number-of-senders"></a>Warteschlange mit einer großen Anzahl von Absendern
Ziel: Maximieren des Durchsatzes einer Warteschlange oder eines Themas mit einer großen Anzahl von Absendern. Jeder Absender sendet Nachrichten mit einer mittleren Rate. Die Anzahl der Empfänger ist klein.

Service Bus ermöglicht bis zu 1000 gleichzeitige Verbindungen mit einer Messagingentität (oder 5000 mithilfe von AMQP). Dieser Grenzwert wird auf Namespace-Ebene erzwungen, und Warteschlangen/Themen/Abonnements werden durch den Grenzwert für gleichzeitige Verbindungen pro Namespace begrenzt. Bei Warteschlangen wird diese Anzahl zwischen Absendern und Empfängern aufgeteilt. Wenn alle 1000 Verbindungen für Absender benötigt werden, sollten Sie die Warteschlange durch ein Thema und ein einzelnes Abonnement ersetzen. Ein Thema akzeptiert bis zu 1000 gleichzeitige Verbindungen von Absendern, während das Abonnement zusätzlich 1000 gleichzeitige Verbindungen von Empfängern akzeptiert. Wenn mehr als 1000 gleichzeitige Absender erforderlich sind, sollten die Absender über HTTP Nachrichten an das Service Bus-Protokoll senden.

Gehen Sie wie folgt vor, um den Durchsatz zu maximieren:

* Verwenden Sie eine partitionierte Warteschlange für verbesserte Leistung und Verfügbarkeit.
* Wenn sich jeder Absender in einem anderen Prozess befindet, verwenden Sie nur eine Factory pro Prozess.
* Verwenden Sie asynchrone Vorgänge, um die clientseitige Batchverarbeitung zu nutzen.
* Verwenden Sie das standardmäßige Batchintervall von 20 ms, um die Anzahl der Service Bus-Clientprotokollübertragungen zu verringern.
* Lassen Sie den Speicherzugriff als Batch aktiviert. Dies erhöht die Gesamtrate, mit der Nachrichten in die Warteschlange oder in das Thema geschrieben werden können.
* Legen Sie den Wert für Vorabrufe auf das 20-fache der maximalen Verarbeitungsraten aller Empfänger einer Factory fest. Dies verringert die Anzahl der Service Bus-Clientprotokollübertragungen.

### <a name="queue-with-a-large-number-of-receivers"></a>Warteschlange mit einer großen Anzahl von Empfängern
Ziel: Maximieren der Empfangsrate einer Warteschlange oder eines Abonnements mit einer großen Anzahl von Empfängern. Jeder Empfänger empfängt Nachrichten mit einer mittleren Rate. Die Anzahl der Absender ist klein.

Service Bus ermöglicht bis zu 1000 gleichzeitige Verbindungen mit einer Messagingentität. Wenn für eine Warteschlange mehr als 1000 Empfänger erforderlich sind, sollten Sie die Warteschlange durch ein Thema und mehrere Abonnements ersetzen. Jedes Abonnement kann bis zu 1000 gleichzeitige Verbindungen unterstützen. Alternativ können Empfänger über das HTTP-Protokoll auf die Warteschlange zugreifen.

Gehen Sie wie folgt vor, um den Durchsatz zu maximieren:

* Verwenden Sie eine partitionierte Warteschlange für verbesserte Leistung und Verfügbarkeit.
* Wenn sich jeder Empfänger in einem anderen Prozess befindet, verwenden Sie nur eine Factory pro Prozess.
* Empfänger können synchrone oder asynchrone Vorgänge verwenden. Bei der mittleren Empfangsrate eines einzelnen Empfängers wirkt sich die clientseitige Batchverarbeitung einer Anforderung zum Abschließen nicht auf den Empfängerdurchsatz aus.
* Lassen Sie den Speicherzugriff als Batch aktiviert. Dies verringert die Gesamtlast der Entität. Es verringert zudem die Gesamtrate, mit der Nachrichten in die Warteschlange oder in das Thema geschrieben werden können.
* Legen Sie den Wert für den Vorabruf auf einen kleinen Wert fest (z. B. "PrefetchCount" = 10). Dadurch wird verhindert, dass Empfänger über freie Kapazitäten verfügen, während andere Empfänger eine große Anzahl von Nachrichten zwischengespeichert haben.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Thema mit einer geringen Anzahl von Abonnements
Ziel: Maximieren des Durchsatzes eines Themas mit einer geringen Anzahl von Abonnements. Eine Nachricht wird von zahlreichen Abonnements empfangen, was bedeutet, dass die kombinierte Empfangsrate aller Abonnements größer als die Senderate ist. Die Anzahl der Absender ist klein. Die Empfängeranzahl pro Abonnement ist gering.

Gehen Sie wie folgt vor, um den Durchsatz zu maximieren:

* Verwenden Sie ein partitioniertes Thema, um die Leistung und die Verfügbarkeit zu verbessern.
* Verwenden Sie zur Erhöhung der Gesamtsenderate für das Thema mehrere Messagingfactorys, um Absender zu erstellen. Verwenden Sie für jeden Absender asynchrone Vorgänge oder mehrere Threads.
* Verwenden Sie zur Erhöhung der Gesamtempfangsrate aus einem Abonnement mehrere Messagingfactorys, um Empfänger zu erstellen. Verwenden Sie für jeden Empfänger asynchrone Vorgänge oder mehrere Threads.
* Verwenden Sie asynchrone Vorgänge, um die clientseitige Batchverarbeitung zu nutzen.
* Verwenden Sie das standardmäßige Batchintervall von 20 ms, um die Anzahl der Service Bus-Clientprotokollübertragungen zu verringern.
* Lassen Sie den Speicherzugriff als Batch aktiviert. Dies erhöht die Gesamtrate, mit der Nachrichten in das Thema geschrieben werden können.
* Legen Sie den Wert für Vorabrufe auf das 20-fache der maximalen Verarbeitungsraten aller Empfänger einer Factory fest. Dies verringert die Anzahl der Service Bus-Clientprotokollübertragungen.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Thema mit einer großen Anzahl von Abonnements
Ziel: Maximieren des Durchsatzes eines Themas mit einer großen Anzahl von Abonnements. Eine Nachricht wird von zahlreichen Abonnements empfangen, was bedeutet, dass die kombinierte Empfangsrate aller Abonnements viel größer als die Senderate ist. Die Anzahl der Absender ist klein. Die Empfängeranzahl pro Abonnement ist gering.

Themen mit einer großen Anzahl von Abonnements weisen normalerweise einen geringen Gesamtdurchsatz auf, wenn alle Nachrichten an alle Abonnements weitergeleitet werden. Der Grund: Jede Nachricht wird mehrmals empfangen, und alle in einem Thema enthaltenen Nachrichten sowie dessen Abonnements werden im gleichen Speicher gespeichert. Es wird von einer geringen Anzahl von Absendern und Empfängern pro Abonnement ausgegangen. Service Bus unterstützt bis zu 2000 Abonnements pro Thema.

Gehen Sie wie folgt vor, um den Durchsatz zu maximieren:

* Verwenden Sie ein partitioniertes Thema, um die Leistung und die Verfügbarkeit zu verbessern.
* Verwenden Sie asynchrone Vorgänge, um die clientseitige Batchverarbeitung zu nutzen.
* Verwenden Sie das standardmäßige Batchintervall von 20 ms, um die Anzahl der Service Bus-Clientprotokollübertragungen zu verringern.
* Lassen Sie den Speicherzugriff als Batch aktiviert. Dies erhöht die Gesamtrate, mit der Nachrichten in das Thema geschrieben werden können.
* Legen Sie den Wert für den Vorabruf auf das 20-fache der erwarteten Empfangsrate in Sekunden fest. Dies verringert die Anzahl der Service Bus-Clientprotokollübertragungen.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Optimieren der Service Bus-Leistung finden Sie unter [Partitionierte Messagingentitäten][Partitionierte Messagingentitäten].

[QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
[MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
[MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
[PeekLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
[ReceiveAndDelete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
[BatchFlushInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.netmessagingtransportsettings.batchflushinterval.aspx
[EnableBatchedOperations]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations.aspx
[QueueClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.prefetchcount.aspx
[SubscriptionClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.prefetchcount.aspx
[ForcePersistence]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.forcepersistence.aspx
[EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
[Partitionierte Messagingentitäten]: service-bus-partitioning.md



<!--HONumber=Nov16_HO3-->


