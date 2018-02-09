---
title: Abrufen der Anzahl von Azure Service Bus-Nachrichten | Microsoft-Dokumentation
description: Informationen zum Abrufen der Anzahl von Azure Service Bus-Nachrichten.
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: e6524fe056ee2a1d81c9cccf257008b2369352b1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2018
---
# <a name="message-counters"></a>Nachrichtenzähler

Sie können die Anzahl der Nachrichten, die in Warteschlangen und Abonnements gespeichert sind, mit Azure Resource Manager und den [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager)-APIs von Service Bus im .NET Framework SDK abrufen.

Mit PowerShell können Sie die Anzahl wie folgt abrufen:

```powershell
(Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Details zur Anzahl von Nachrichten

Die Anzahl aktiver Nachrichten zu kennen ist nützlich, um festzustellen, ob eine Warteschlange einen Rückstand aufbaut, der mehr Ressourcen für die Verarbeitung benötigt als diejenigen, die derzeit bereitgestellt sind. Die [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails)-Klasse bietet die folgenden Details zur Anzahl:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): Nachrichten in der Warteschlange oder im Abonnement, die sich im aktiven Zustand befinden und zustellbereit sind.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): Nachrichten in der Warteschlange für unzustellbare Nachrichten.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): Nachrichten mit geplantem Zustand.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): Nachrichten, die nicht in eine andere Warteschlange oder ein anderes Thema übertragen wurden und die in die Warteschlange für unzustellbare Nachrichten verschoben wurden.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): Nachrichten, deren Übertragung in eine andere Warteschlange oder ein anderes Thema aussteht.

Wenn eine Anwendung Ressourcen anhand der Länge der Warteschlange skalieren möchte, sollte sie dies mit sehr maßvollem Tempo tun. Die Erfassung der Nachrichtenzählern ist ein aufwendiger Vorgang innerhalb des Nachrichtenbrokers, dessen Ausführung häufig direkte und negative Auswirkungen auf die Leistung der Entität hat.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Service Bus-Messaging finden Sie in folgenden Themen:

* [Service Bus – Grundlagen](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Verwenden von Service Bus-Themen und -Abonnements](service-bus-dotnet-how-to-use-topics-subscriptions.md)