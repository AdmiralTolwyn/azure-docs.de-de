---
title: Event Hubs-Messagingausnahmen | Microsoft Docs
description: "Liste mit Azure Event Hubs-Messagingausnahmen und vorgeschlagenen Maßnahmen"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 2c6273de-0106-47e5-b45d-59040e51f2c5
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c17328ffee6191022fcfd9588b3d30b0cea23148


---
# <a name="event-hubs-messaging-exceptions"></a>Event Hubs-Messagingausnahmen
In diesem Artikel werden einige Ausnahmen aufgelistet, die von den Azure Service Bus-Messaging-APIs generiert werden. Dieser API-Satz enthält Event Hubs. Diese Referenz kann geändert werden. Prüfen Sie darum bei Bedarf, ob Aktualisierungen vorgenommen wurden.

## <a name="exception-categories"></a>Ausnahmekategorien
Die von den Messaging-APIs generierten Ausnahmen können – zusammen mit den zugehörigen Korrekturmaßnahmen – zu den folgenden Kategorien gehören. Beachten Sie, dass die Bedeutung und die Ursachen einer Ausnahme je nach Art der Messagingentität (Relays, Warteschlangen/Topics, Event Hubs) variieren können:

1. Fehler der Benutzercodierung ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Allgemeine Maßnahme: Korrigieren Sie den Code, bevor Sie fortfahren.
2. Fehler bei der Einrichtung oder Konfiguration: ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitynotfoundexception.aspx), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Allgemeine Maßnahme: Überprüfen Sie die Konfiguration, und ändern Sie diese gegebenenfalls.
3. Vorübergehende Ausnahmen ([Microsoft.ServiceBus.Messaging.MessagingException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx), [Microsoft.ServiceBus.Messaging.ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingcommunicationexception.aspx)). Allgemeine Maßnahme: Wiederholen Sie den Vorgang, oder benachrichtigen Sie die Benutzer.
4. Andere Ausnahmen ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagelocklostexception.aspx), [Microsoft.ServiceBus.Messaging.SessionLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessionlocklostexception.aspx)). Allgemeine Aktion: spezifisch für den Typ der Ausnahme; zu finden in der Tabelle im folgenden Abschnitt. 

## <a name="exception-types"></a>Ausnahmetypen
In der folgenden Tabelle werden die Typen von Messagingausnahmen, ihre Ursachen und Vorschläge für Korrekturmaßnahmen aufgelistet.

| **Ausnahmetyp** | **Beschreibung/Ursache/Beispiele** | **Vorgeschlagene Maßnahme** | **Hinweis zur automatischen/sofortigen Wiederholung** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Der Server hat nicht innerhalb der von [OperationTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx)vorgegebenen Zeit auf den angeforderten Vorgang reagiert. Unter Umständen hat der Server den angeforderten Vorgang abgeschlossen. Diese Ausnahme kann aufgrund von Verzögerungen im Netzwerk oder in der Infrastruktur auftreten. |Überprüfen Sie den Systemzustand auf Konsistenz, und wiederholen Sie den Vorgang bei Bedarf. |In einigen Fällen kann eine Wiederholung helfen. Fügen Sie dem Code eine Wiederholungslogik hinzu. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Der angeforderte Benutzervorgang ist auf dem Server oder innerhalb des Diensts nicht zulässig. Sehen Sie sich die Details in der Ausnahmemeldung an. Beispielsweise generiert [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) diese Ausnahme, wenn die Meldung im **ReceiveAndDelete** -Modus empfangen wurde. |Überprüfen Sie den Code und die Dokumentation. Stellen Sie sicher, dass der angeforderte Vorgang gültig ist. |Eine Wiederholung hilft nicht. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Es wurde versucht, einen Vorgang für ein Objekt aufzurufen, das bereits geschlossen, abgebrochen oder verworfen wurde. In seltenen Fällen wurde die Ambient-Transaktion bereits verworfen. |Überprüfen Sie den Code, und stellen Sie sicher, dass keine Vorgänge für verworfene Objekte aufgerufen werden. |Eine Wiederholung hilft nicht. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Das [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) -Objekt konnte kein Token abrufen, das Token ist ungültig, oder das Token enthält nicht die Ansprüche, die zum Ausführen des Vorgangs erforderlich sind. |Stellen Sie sicher, dass der Tokenanbieter mit den richtigen Werten erstellt wird. Überprüfen Sie die Konfiguration für den Access Control Service. |In einigen Fällen kann eine Wiederholung helfen. Fügen Sie dem Code eine Wiederholungslogik hinzu. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Mindestens eines der für die Methode bereitgestellten Argumente ist ungültig. Der für [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) oder [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx) bereitgestellte URI enthält Pfadsegmente. Das für [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) oder für [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx) bereitgestellte URI-Schema ist ungültig. Der Eigenschaftswert ist größer als 32KB. |Überprüfen Sie den aufrufenden Code, und stellen Sie sicher, dass die Argumente richtig sind. |Eine Wiederholung hilft nicht. |
| [MessagingEntityNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitynotfoundexception.aspx) |Die dem Vorgang zugeordnete Entität ist nicht vorhanden oder wurde gelöscht. |Stellen Sie sicher, dass die Entität vorhanden ist. |Eine Wiederholung hilft nicht. |
| [MessageNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagenotfoundexception.aspx) |Es wurde versucht, eine Nachricht mit einer bestimmten Sequenznummer zu empfangen. Diese Nachricht wurde nicht gefunden. |Stellen Sie sicher, dass die Nachricht nicht bereits empfangen wurde. Überprüfen Sie in der Warteschlange für unzustellbare Nachrichten, ob die Nachricht als unzustellbar gekennzeichnet wurde. |Eine Wiederholung hilft nicht. |
| [MessagingCommunicationException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingcommunicationexception.aspx) |Der Client kann keine Verbindung mit Event Hub herstellen. |Stellen Sie sicher, dass der angegebene Hostname richtig und der Host erreichbar ist. |Eine Wiederholung kann helfen, wenn zeitweilige Verbindungsprobleme vorliegen. |
| [ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx) |Der Dienst kann die Anforderung derzeit nicht verarbeiten. |Der Client kann eine gewisse Zeit warten und dann den Vorgang wiederholen. |Der Client kann den Vorgang nach einer gewissen Zeitspanne wiederholen. Wenn die Wiederholung zu einer anderen Ausnahme führt, überprüfen Sie das Wiederholungsverhalten dieser Ausnahme. |
| [MessageLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagelocklostexception.aspx) |Das der Nachricht zugeordnete Sperrtoken ist abgelaufen, oder das Sperrtoken wurde nicht gefunden. |Verwerfen Sie die Nachricht. |Eine Wiederholung hilft nicht. |
| [SessionLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessionlocklostexception.aspx) |Die dieser Sitzung zugeordnete Sperre ist verloren gegangen. |Brechen Sie das [MessageSession](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.aspx) -Objekt ab. |Eine Wiederholung hilft nicht. |
| [MessagingException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx) |Eine allgemeine Messagingausnahme, die in folgenden Fällen ausgelöst werden kann: Es wird versucht, ein [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx)-Element mit einem Namen oder einem Pfad zu erstellen, der zu einem anderen Entitätstyp gehört (z.B. zu einem Thema). Es wird versucht, eine Nachricht zu senden, die größer als 256 KB ist. Der Server oder der Dienst hat beim Verarbeiten der Anforderung einen Fehler festgestellt. Sehen Sie sich die Details in der Ausnahmemeldung an. Dies ist meist eine vorübergehende Ausnahme. |Überprüfen Sie den Code, und stellen Sie sicher, dass nur serialisierbare Objekte für den Nachrichtentext verwendet werden (oder verwenden Sie ein benutzerdefiniertes Serialisierungsprogramm). Überprüfen Sie die Dokumentation für die unterstützten Werttypen der Eigenschaften, und verwenden Sie nur unterstützte Typen. Überprüfen Sie die [IsTransient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx) -Eigenschaft. Wenn sie den Wert **True**aufweist, können Sie versuchen, den Vorgang zu wiederholen. |Das Verhalten einer Wiederholung ist nicht definiert, u. U. hilft sie nicht. |
| [MessagingEntityAlreadyExistsException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentityalreadyexistsexception.aspx) |Es wurde versucht, eine Entität mit einem Namen zu erstellen, der bereits von einer anderen Entität in diesem Dienstnamespace verwendet wird. |Löschen Sie die vorhandene Entität, oder wählen Sie einen anderen Namen für die zu erstellende Entität. |Eine Wiederholung hilft nicht. |
| [QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) |Die Messagingentität hat die maximal zulässige Größe erreicht. Dies kann passieren, wenn bereits die maximale Anzahl von fünf Empfängern für eine bestimmte Consumergruppe geöffnet wurde. |Schaffen Sie Platz in der Entität, indem Sie Nachrichten aus der Entität oder ihren Unterwarteschlangen empfangen. |Eine Wiederholung kann helfen, wenn in der Zwischenzeit Nachrichten entfernt wurden. |
|  | | | |
| [SessionCannotBeLockedException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessioncannotbelockedexception.aspx) |Es wurde versucht, eine Sitzung mit einer bestimmten Sitzungs-ID zu akzeptieren, aber die Sitzung wird derzeit durch einen anderen Client gesperrt. |Stellen Sie sicher, dass die Sperre für die Sitzung von den anderen Clients aufgehoben wird. |Eine Wiederholung kann helfen, wenn die Sitzung in der Zwischenzeit freigegeben wurde. |
| [TransactionSizeExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transactionsizeexceededexception_methods.aspx) |Die Transaktion umfasst zu viele Vorgänge. |Reduzieren Sie die Anzahl der Vorgänge, die Teil dieser Transaktion sind. |Eine Wiederholung hilft nicht. |
| [MessagingEntityDisabledException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitydisabledexception.aspx) |Es wurde ein Laufzeitvorgang für eine deaktivierte Entität angefordert. |Aktivieren Sie die Entität. |Eine Wiederholung kann helfen, wenn die Entität in der Zwischenzeit aktiviert wurde. |
| [MessageSizeExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesizeexceededexception.aspx) |Eine Nachrichtennutzlast überschreitet den Grenzwert von 256 KB. Beachten Sie, dass der Grenzwert von 256 KB für die Gesamtgröße der Nachricht gilt, zu der auch Systemeigenschaften und .NET-Mehraufwand gehören. |Reduzieren Sie die Größe der Nachrichtennutzlast, und wiederholen Sie den Vorgang. |Eine Wiederholung hilft nicht. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |Die Ambient-Transaktion (*Transaction.Current*) ist ungültig. Sie wurde möglicherweise abgeschlossen oder abgebrochen. Unter Umständen enthält die innere Ausnahme weitere Informationen. | |Eine Wiederholung hilft nicht. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Es wurde versucht, einen Vorgang für eine unsichere Transaktion auszuführen, oder es wurde versucht, ein Commit für die Transaktion auszuführen, und die Transaktion wurde unsicher. |Die Anwendung muss diese Ausnahme (als Sonderfall) behandeln, da für die Transaktion u. U. bereits ein Commit ausgeführt wurde. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) gibt an, dass das Kontingent für eine bestimmte Entität überschritten wurde.

Dies kann passieren, wenn bereits die maximale Anzahl von fünf Empfängern für eine bestimmte Consumergruppe geöffnet wurde.

### <a name="event-hubs"></a>Event Hubs
Für Event Hubs gilt ein Limit von 20 Verbrauchergruppen pro Event Hub. Wenn Sie versuchen, weitere zu erstellen, erhalten Sie eine [QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx). 

## <a name="timeoutexception"></a>TimeoutException
Eine [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) zeigt an, dass ein von einem Benutzer initiierter Vorgang länger als das Timeout des Vorgangs dauert. 

### <a name="event-hubs"></a>Event Hubs
Für Event Hubs wird das Zeitlimit entweder als Teil der Verbindungszeichenfolge oder über [ServiceBusConnectionStringBuilder](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.aspx)angegeben. Die Fehlermeldung selbst kann variieren, aber sie enthält immer den Timeoutwert für den aktuellen Vorgang. 

### <a name="common-causes"></a>Häufige Ursachen
Es gibt zwei häufige Ursachen für diesen Fehler: eine falsche Konfiguration oder ein vorübergehender Dienstfehler.

1. **Falsche Konfiguration**
    Das Zeitlimit für den Vorgang ist möglicherweise zu klein für die Betriebsbedingung. Der Standardwert für das Zeitlimit für den Vorgang im Client-SDK beträgt 60 Sekunden. Überprüfen Sie, ob der Wert in Ihrem Code auf einen zu geringen Wert festgelegt wurde. Beachten Sie, dass sich die Netzwerk- und CPU-Auslastung auf die Zeit auswirken kann, die zum Abschließen eines bestimmten Vorgangs erforderlich ist. Daher sollte das Zeitlimit für den Vorgang nicht auf einen sehr kleinen Wert festgelegt werden.
2. **Vorübergehender Dienstfehler**
    Gelegentlich kann es beim Event Hubs-Dienst zu Verzögerungen bei der Verarbeitung von Anforderungen kommen, z.B. in Zeiten mit hohem Datenverkehr. In solchen Fällen können Sie den Vorgang nach einer kurzen Verzögerung so lange wiederholen, bis der Vorgang erfolgreich ist. Wenn der gleiche Vorgang auch nach mehreren Versuchen nicht erfolgreich ist, besuchen Sie die Website mit dem [Azure-Status](https://azure.microsoft.com/status/), um zu überprüfen, ob Dienstausfälle bekannt sind.

## <a name="next-steps"></a>Nächste Schritte
Die vollständige Referenz zu den .NET-APIs für Service Bus und Event Hubs finden Sie in der [Azure-Referenz auf MSDN](https://msdn.microsoft.com/library/azure/mt419900.aspx).




<!--HONumber=Nov16_HO3-->


