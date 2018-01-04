---
title: "Übersicht über Azure Service Bus-Messaging | Microsoft-Dokumentation"
description: "Enthält eine Beschreibung des Service Bus-Messaging und von Azure Relay."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: e299ccfe587d37757cd67cb4367f019b21a09b4a
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/22/2017
---
# <a name="service-bus-messaging-flexible-data-delivery-in-the-cloud"></a>Service Bus Messaging: Flexible Datenübermittlung in der Cloud

Microsoft Azure Service Microsoft Azure Service Bus ist ein zuverlässiger Dienst für die Übermittlung von Informationen. Der Zweck dieses Dienstes ist es, die Kommunikation zu erleichtern. Wenn zwei oder mehr Parteien Informationen austauschen möchten, benötigen sie einen Kommunikationsmechanismus. Service Bus ist ein Kommunikationsmechanismus, der auf einem Broker oder Drittanbieter basiert. Der Dienst funktioniert ähnlich wie ein Postdienst in der realen Welt. Über Postdienste lassen sich verschiedene Arten von Briefen und Paketen mit unterschiedlichen Übermittlungsgarantien überall in der Welt versenden.

Bei Service Bus geht es, ähnlich wie bei einem Postdienst, der Briefe zustellt, um eine flexible Übermittlung von Informationen zwischen Absender und Empfänger. Der Messagingdienst stellt sicher, dass die Informationen übermittelt werden, selbst wenn Absender und Empfänger niemals gleichzeitig online oder zum exakt gleichen Zeitpunkt verfügbar sind. Messaging entspricht also dem Senden eines Briefes. Eine Kommunikation ohne Broker dagegen lässt sich eher mit einem Telefonanruf vergleichen (zumindest Anrufen in früheren Zeiten, als es noch keine Anklopffunktion und Anruferkennung gab - diese Funktionen entsprechen eher der brokerbasierten Nachrichtenübermittlung).

Der Absender der Nachricht kann auch eine Vielzahl von Übermittlungsmerkmalen anfordern, wie z. B. Transaktionen, Duplikaterkennung, Ablaufdatum und -uhrzeit und Batchverarbeitung. Auch diese Muster weisen Analogien mit einem Postdienst auf: wiederholte Übermittlung, erforderliche Signatur, Adressänderung oder Rückruf.

Service Bus unterstützt zwei unterschiedliche Muster für das Messaging: *Azure Relay* und *Service Bus-Messaging*.

## <a name="azure-relay"></a>Azure Relay

Die [WCF Relay-Komponente](../service-bus-relay/relay-what-is-it.md) von Azure Relay ist ein zentralisierter Dienst (aber mit starkem Lastenausgleich), der viele verschiedene Transportprotokolle und Webdienststandards unterstützt. Dies schließt SOAP, WS-* und sogar REST ein. Der [Relaydienst](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md) stellt unterschiedliche Relayverbindungsoptionen bereit und kann bei der Aushandlung von direkten Peer-to-Peer-Verbindungen behilflich sein, sofern diese möglich sind. Service Bus ist für .NET-Entwickler optimiert, die die Windows Communication Foundation (WCF) verwenden, und zwar sowohl im Hinblick auf die Leistung als auch auf die Nutzbarkeit. Der vollständige Zugriff auf den dazugehörigen Relaydienst wird über SOAP- und REST-Schnittstellen bereitgestellt. So kann jede SOAP- oder REST-Programmierumgebung für die Integration in Service Bus integriert werden.

Der Relaydienst unterstützt herkömmliches unidirektionales Messaging, Anforderungs-/Antwortmessaging und Peer-to-Peer-Messaging. Er unterstützt außerdem die Ereignisverteilung im Internetumfang, um Veröffentlichen-/Abonnieren-Szenarios und bidirektionale Socketkommunikation für erhöhte Punkt-zu-Punkt-Effizienz zu ermöglichen. Im Relaymessaging stellt ein lokaler Dienst eine Verbindung zu dem Relaydienst über einen ausgehenden Port her und erstellt einen bidirektionalen Socket für die mit einer bestimmten Rendezvous-Adresse gekoppelte Kommunikation. Der Client kann dann mit dem lokalen Dienst durch das Senden von Nachrichten an den Relaydienst unter Verwendung der Rendezvousadresse kommunizieren. Der Relaydienst leitet dann Nachrichten an den lokalen Dienst über den bidirektionalen, bereits vorhandenen Socket weiter. Der Client benötigt keine direkte Verbindung zum lokalen Dienst und muss auch nicht wissen, wo der Dienst angesiedelt ist, und für den lokalen Dienst sind keine geöffneten eingehenden Ports in der Firewall notwendig.

Sie initiieren die Verbindung zwischen dem lokalen Dienst und dem Relaydienst mithilfe einer Sammlung von WCF-Bindungen vom Typ „Relay“. Im Prinzip werden die Relaybindungen Transportbindungselementen zugeordnet, die entwickelt wurden, um WCF-Kanalkomponenten zu erstellen, die in der Cloud in Service Bus integriert werden.

WCF Relay bietet viele Vorteile. Dafür ist es aber erforderlich, dass der Server und der Client gleichzeitig online sind, damit Nachrichten gesendet und empfangen werden können. Dies ist für die Kommunikation im HTTP-Stil nicht optimal, da die Anforderungen meist keine sehr lange Lebensdauer aufweisen. Ebenso ist dies nicht gut für Clients geeignet, die nur gelegentlich eine Verbindung herstellen, z. B. Browser, mobile Anwendungen usw. Beim Brokermessaging wird die entkoppelte Kommunikation unterstützt, und es ergeben sich einige besondere Vorteile. Clients und Server können bei Bedarf jederzeit eine Verbindung herstellen und ihre Vorgänge auf asynchrone Weise durchführen.

## <a name="brokered-messaging"></a>Brokermessaging

Im Gegensatz zum Relayschema können Sie sich das Service Bus-Messaging mit [Warteschlangen, Themen und Abonnements](service-bus-queues-topics-subscriptions.md) als asynchron oder „temporär entkoppelt“ vorstellen. Produzent (Absender) und Konsumenten (Empfänger) müssen nicht gleichzeitig online sein. Die Messaginginfrastruktur speichert Nachrichten zuverlässig in einem „Broker“ (z.B. einer Warteschlange), bis die Empfängerseite für den Empfang bereit ist. Auf diese Weise können die Komponenten verteilter Anwendungen voneinander entkoppelt werden, und zwar entweder gewollt (z.B. zu Wartungszwecken) oder aufgrund eines Komponentenausfalls, ohne das Gesamtsystem zu beeinträchtigen. Außerdem muss die empfangende Anwendung ggf. nur zu bestimmten Tageszeiten online sein, z. B. bei einem System für die Bestandsverwaltung, das nur am Ende eines Arbeitstages ausgeführt wird.

Die Hauptkomponenten der Service Bus-Messaginginfrastruktur sind Warteschlangen, Themen und Abonnements. Der Hauptunterschied besteht darin, dass Themen Funktionen zum Veröffentlichen/Abonnieren unterstützen, die für anspruchsvolle inhaltsbasierte Routing- und Übermittlungslogik verwendet werden können, wie z. B. zum Senden an mehrere Empfänger. Diese Komponenten ermöglichen neue Szenarien für asynchrones Messaging, z. B. temporäre Entkopplung, Veröffentlichen/Abonnieren und Lastenausgleich. Weitere Informationen zu diesen Messagingentitäten finden Sie unter [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md).

Wie bei der WCF Relay-Infrastruktur wird auch das Brokermessaging für WCF- und .NET Framework-Programmierer und über REST bereitgestellt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Service Bus Messaging finden Sie in folgenden Themen.

* [Service Bus – Grundlagen](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Verwenden von Service Bus-Themen und -Abonnements](service-bus-dotnet-how-to-use-topics-subscriptions.md)

