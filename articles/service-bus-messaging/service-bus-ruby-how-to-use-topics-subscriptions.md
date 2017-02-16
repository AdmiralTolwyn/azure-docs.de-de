---
title: Verwenden von Service Bus-Themen (Ruby) | Microsoft Docs
description: "Erfahren Sie mehr zur Verwendung von Service Bus-Themen und -Abonnements in Azure. Die Codebeispiele wurden für Ruby-Anwendungen geschrieben."
services: service-bus-messaging
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 01/11/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 43197f7402795c37fa7ed43658bc3b8858a41080
ms.openlocfilehash: c083d8ac0d16de40de4a2a9908cdcf2e02ed3d6a


---
# <a name="how-to-use-service-bus-topicssubscriptions"></a>Verwenden von Servicebus-Themen und -Abonnements
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In diesem Artikel erfahren Sie, wie Sie Service Bus-Themen und -Abonnements aus Ruby-Anwendungen verwenden. Die behandelten Szenarios umfassen **das Erstellen von Themen und Abonnements, das Erstellen von Abonnementfiltern, das Senden von Nachrichten** an ein Thema, **das Empfangen von Nachrichten von einem Abonnement** und **das Löschen von Themen und Abonnements**. Weitere Informationen zu Themen und Abonnements finden Sie im Abschnitt [Nächste Schritte](#next-steps).

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-ruby-application"></a>Erstellen einer Ruby-Anwendung
Anweisungen finden Sie unter [Ruby on Rails-Webanwendung auf Azure VM](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus
Um Service Bus zu verwenden, müssen Sie das Ruby-Azure-Paket herunterladen und verwenden. Dieses enthält eine Reihe von Bibliotheken, die mit den Speicher-REST-Diensten kommunizieren.

### <a name="use-rubygems-to-obtain-the-package"></a>Verwenden von RubyGems zum Abrufen des Pakets
1. Verwenden Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).
2. Geben Sie "gem install azure" in das Befehlsfenster ein, um das Gem und Abhängigkeiten zu installieren.

### <a name="import-the-package"></a>Importieren des Pakets
Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie den Speicher verwenden möchten:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Einrichten einer Service Bus-Verbindung
Das Azure-Modul liest die Umgebungsvariablen **AZURE\_SERVICEBUS\_NAMESPACE** und **AZURE\_SERVICEBUS\_ACCESS\_KEY** nach Informationen aus, die für eine Verbindung zu Ihrem Namespace benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt werden, müssen Sie die Kontoinformationen vor dem Verwenden von **Azure::ServiceBusService** mit dem folgenden Code angeben:

```ruby
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Legen Sie den Wert für den Namespace auf den von Ihnen erstellten Wert statt auf die gesamte URL fest. Verwenden Sie beispielsweise **Beispielnamespace**, anstelle von „Beispielnamespace.servicebus.windows.net“.

## <a name="create-a-topic"></a>Erstellen eines Themas
Das **Azure::ServiceBusService**-Objekt ermöglicht es Ihnen, mit Themen zu arbeiten. Der folgende Code erstellt ein **Azure::ServiceBusService**-Objekt. Verwenden Sie die Methode **create\_topic()**, um ein Thema zu erstellen. Im folgenden Beispiel wird ein Thema erstellt oder ggf. ein Fehler ausgegeben.

```ruby
azure_service_bus_service = Azure::ServiceBusService.new
begin
  topic = azure_service_bus_service.create_queue("test-topic")
rescue
  puts $!
end
```

Sie können außerdem ein **Azure::ServiceBus::Topic**-Objekt mit weiteren Optionen übergeben, mit denen Sie die Standardthemeneinstellungen, wie etwa Nachrichtenlebensdauer oder maximale Warteschlangengröße, überschreiben können. Das folgende Beispiel zeigt, wie Sie die maximale Warteschlangengröße auf 5 GB bei einer Gültigkeitsdauer von 1 Minute festlegen:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Erstellen von Abonnements
Themenabonnements werden ebenfalls mit dem **Azure::ServiceBusService**-Objekt erstellt. Abonnements werden benannt und können einen optionalen Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle Warteschlange des Abonnements übermittelt werden.

Abonnements sind persistent und bleiben erhalten, bis sie selbst oder die mit ihnen verbundenen Themen gelöscht werden. Wenn Ihre Anwendung Logik beinhaltet, sollte sie bei der Erstellung eines Abonnements zuerst mithilfe der getSubscription-Methode überprüfen, ob das Abonnement bereits vorhanden ist.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Erstellen eines Abonnements mit dem Standardfilter (MatchAll)
**MatchAll** ist der Standardfilter, der verwendet wird, wenn beim Erstellen eines neuen Abonnements kein Filter angegeben wird. Wenn der Filter **MatchAll** verwendet wird, werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Mit dem folgenden Beispiel wird ein Abonnement namens „all-messages“ erstellt, für das der Standardfilter **MatchAll** verwendet wird.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Erstellen von Abonnements mit Filtern
Sie können auch Filter definieren, durch die Sie angeben können, welche an ein Thema gesendeten Nachrichten in einem bestimmten Abonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist **Azure::ServiceBus::SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet werden können, finden Sie in der Syntax [SqlFilter.SqlExpression](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx).

Sie können einem Abonnement mithilfe der Methode **create\_rule()**-Methode des **Azure::ServiceBusService**-Objekts Filter hinzufügen. Durch diese Methode können Sie neue Filter einem vorhandenen Abonnement hinzufügen.

Da der Standardfilter automatisch auf alle neuen Abonnements angewendet wird, müssen Sie zuerst den Standardfilter entfernen. Ansonsten überschreibt **MatchAll** alle Filter, die Sie angeben. Sie können die Standardregel mithilfe der Methode **delete\_rule()** des **Azure::ServiceBusService**-Objekts entfernen.

Mit dem folgenden Beispiel wird ein Abonnement namens „high-messages“ mit einem **Azure::ServiceBus::SqlFilter**-Filter erstellt, der nur Nachrichten auswählt, deren benutzerdefinierter Eigenschaftswert **message\_number** größer ist als 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Ebenso wird im folgenden Beispiel ein Abonnement namens „low-messages“ mit einem **Azure::ServiceBus::SqlFilter**-Filter erstellt, der nur Nachrichten auswählt, deren Eigenschaftswert **message_number** kleiner oder gleich 3 ist:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Wenn eine Nachricht an „test-topic“ gesendet wird, wird diese nun stets den Empfängern des Themenabonnements „all-messages“ zugestellt. Sie wird selektiv den Empfängern der Themenabonnements „high-messages“ und „low-messages“ zugestellt (je nach Inhalt der Nachricht).

## <a name="send-messages-to-a-topic"></a>Senden von Nachrichten an ein Thema
Um eine Nachricht an ein Service Bus-Thema zu senden, muss die Anwendung die Methode **send\_topic\_message()** des **Azure::ServiceBusService**-Objekts verwenden. Nachrichten, die an Service Bus-Themen gesendet werden, sind **Azure::ServiceBus::BrokeredMessage**-Objekte. **Azure::ServiceBus::BrokeredMessage**-Objekte weisen eine Reihe von Standardeigenschaften (wie etwa **label** und **time\_to\_live**), ein Wörterbuch zur Aufnahme benutzerdefinierter, anwendungsspezifischer Eigenschaften und einen aus Zeichenfolgendaten bestehenden Nachrichtentext auf. Eine Anwendung kann den Nachrichtentext festlegen, indem sie einen Zeichenkettenwert an die Methode **send\_topic\_message()** weitergibt. Erforderliche Standardeigenschaften werden mit den Standardwerten gefüllt.

Das folgende Beispiel zeigt, wie Sie fünf Testnachrichten an "test-topic" senden. Beachten Sie, dass der benutzerdefinierte Eigenschaftswert **message_number** jeder Nachricht gemäß der Iteration der Schleife variiert (dadurch wird bestimmt, welches Abonnement die Nachricht erhält):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Service Bus-Themen unterstützen eine maximale Nachrichtengröße von 256 KB im [Standard-Tarif](service-bus-premium-messaging.md) und 1 MB im [Premium-Tarif](service-bus-premium-messaging.md). Der Header, der die standardmäßigen und benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben. Es gibt keine Beschränkung für die Anzahl der Nachrichten, die ein Thema enthält. Es gibt jedoch eine Obergrenze für die Gesamtgröße der Nachrichten eines Themas. Die Themengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Empfangen von Nachrichten aus einem Abonnement
Nachrichten werden von einem Abonnement über die Methode **receive\_subscription\_message** auf dem **Azure::ServiceBusService**-Objekt empfangen. Standardmäßig werden Nachrichten gelesen(peak) und gesperrt, ohne aus dem Abonnement gelöscht zu werden. Sie können die Nachricht aus dem Abonnement löschen, indem Sie die Option **peek\_lock** auf **FALSE** setzen.

Im Standardverhalten sind Empfang und Löschung von Nachrichten zweistufig. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufruf der Methode **delete\_subscription\_message()** und Bereitstellen der Nachricht durch, die als Parameter gelöscht wird. Die Methode **delete\_subscription\_message()** markiert die Nachricht als verarbeitet und entfernt sie aus dem Abonnement.

Wenn der **:peek\_lock**-Parameter auf **FALSE** festgelegt ist, wird zum Lesen und Löschen der Nachricht das einfachste Modell verwendet. Dieses sollte für Anwendungen eingesetzt werden, die damit umgehen können, wenn Nachrichten bei Auftreten eines Fehlers nicht verarbeitet werden. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Service Bus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Das folgende Beispiel zeigt, wie Nachrichten mithilfe von **receive\_subscription\_message()** empfangen und verarbeitet werden können. In diesem Beispiel wird zuerst eine Nachricht des Abonnements „low-messages“ mit **:peek\_lock** gleich **FALSE** empfangen und gelöscht, und anschließend eine Nachricht des Abonnements „high-messages“ empfangen und mit **delete\_subscription\_message()** gelöscht:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten
Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, so kann sie die Methode **unlock\_subscription\_message()** für das **Azure::ServiceBusService**-Objekt aufrufen. Dies führt dazu, dass Service Bus die Nachricht innerhalb des Abonnements entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Zudem wird der im Abonnement gesperrten Anwendung ein Timeout zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Timeouts nicht verarbeiten kann (zum Beispiel, wenn die Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor Abrufen der Methode **delete\_subscription\_message()** abstürzt, wird die Nachricht erneut der Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (Mindestens einmal verarbeiten) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Diese Logik wird häufig durch die Verwendung der Eigenschaft **Message\_id** der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

## <a name="delete-topics-and-subscriptions"></a>Löschen von Themen und Abonnements
Themen und Abonnements sind persistent und müssen über das [Azure-Portal][Azure portal] oder programmgesteuert explizit gelöscht werden. Das folgende Beispiel zeigt, wie Sie das Thema namens "test-topic" löschen.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert sind. Abonnements können auch unabhängig gelöscht werden. Der folgende Code zeigt, wie Sie ein Abonnement namens "high-messages" aus dem Thema "test-topic" löschen:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun mit den Grundlagen der Servicebus-Themen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

* Siehe [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md).
* API-Referenz für [SqlFilter](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx).
* Besuchen Sie das [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby)-Repository auf GitHub.

[Azure portal]: https://portal.azure.com



<!--HONumber=Jan17_HO2-->


