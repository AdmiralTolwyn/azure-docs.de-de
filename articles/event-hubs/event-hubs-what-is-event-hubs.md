---
title: Definition und Vorteile von Azure Event Hubs | Microsoft-Dokumentation
description: "Übersicht und Einführung in Azure Event Hubs – Telemetrieeingang auf Cloudebene von Websites, Apps und Geräten"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: 9b7be70d726edfa9dbda3719d431e532106724b6
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2018
---
# <a name="what-is-event-hubs"></a>Was ist Event Hubs?

Azure Event Hubs ist eine hochgradig skalierbare Datenstreamingplattform und ein Dienst zur Erfassung von Ereignissen, der pro Sekunde Millionen von Ereignissen empfangen und verarbeiten kann. Event Hubs kann Ereignisse, Daten oder Telemetriedaten, die von verteilter Software und verteilten Geräten erzeugt wurden, verarbeiten und speichern. An einen Event Hub gesendete Daten können transformiert und mit einem beliebigen Echtzeitanalyse-Anbieter oder Batchverarbeitungs-/Speicheradapter gespeichert werden. Durch die Möglichkeit, [Funktionen zum Veröffentlichen/Abonnieren](/biztalk/core/publish-and-subscribe-architecture) mit niedriger Latenz und umfangreicher Skalierung anzubieten, fungiert Event Hubs als Einstiegspunkt für Big Data.

## <a name="why-use-event-hubs"></a>Vorteile von Event Hubs

Die Ereignis- und Telemetrieverarbeitungsfunktionen von Event Hubs machen ihn besonders hilfreich für:

* Anwendungsinstrumentation
* Benutzerumgebung oder Workflowverarbeitung
* Internet der Dinge (IoT)-Szenarien

Beispielsweise bietet Event Hubs die Nachverfolgung von Verhaltensweisen in mobilen Apps, Informationen zum Datenverkehr von Webfarmen, Ereigniserfassung in Konsolenspielen oder von Industriemaschinen, vernetzten Fahrzeugen oder anderen Geräten erfasste Telemetriedaten.

## <a name="azure-event-hubs-overview"></a>Übersicht über Event Hubs

In Lösungsarchitekturen übernimmt Event Hubs im Allgemeinen eine Funktion als „Eingangstür“ für eine Ereignispipeline, häufig als *Ereigniserfassung* bezeichnet. Ein Ereigniserfasser ist eine Komponente oder ein Dienst zwischen Ereignisherausgeber und Ereignisverarbeitung zum Entkoppeln der Erzeugung eines Ereignisstreams von der Verarbeitung dieser Ereignisse. Diese Architektur ist in der folgenden Abbildung dargestellt:

![Event Hubs](./media/event-hubs-what-is-event-hubs/event_hubs_full_pipeline.png)

Event Hubs bietet eine Funktion zur Abwicklung von Nachrichtendatenströmen, verfügt jedoch über Eigenschaften, die sich von herkömmlichen Diensten zur Unternehmenskommunikation unterscheiden. Event Hubs-Funktionen werden für Szenarien mit hohem Durchsatz und Ereignisverarbeitung erstellt. Infolgedessen unterscheidet sich Event Hubs von [Azure Service Bus](https://azure.microsoft.com/services/service-bus/)-Messaging. Zudem werden von Event Hubs einige der Funktionen, die für [Service Bus-Messaging-Entitäten](/azure/service-bus-messaging/) wie Themen verfügbar sind, nicht implementiert.

## <a name="event-hubs-features"></a>Event Hubs-Funktionen

Event Hubs bietet folgende Schlüsselelemente:

- [**Ereignisersteller/-herausgeber**](event-hubs-features.md#event-publishers): Eine Entität, die Daten an einen Event Hub sendet. Ein Ereignis wird über AMQP 1.0 oder HTTPS veröffentlicht.
- [**Erfassung**](event-hubs-features.md#capture): Ermöglicht Ihnen die Erfassung von Event Hubs-Streamingdaten und ihre Speicherung in einem Azure Blob-Speicherkonto.
- [**Partitionen**](event-hubs-features.md#partitions): Ermöglicht jedem Consumer, nur eine bestimmte Teilmenge oder Partition des Ereignisdatenstroms zu lesen.
- [**SAS-Token**](event-hubs-features.md#sas-tokens): Identifizieren und Authentifizieren den Ereignisherausgeber.
- [**Ereignisconsumer**](event-hubs-features.md#event-consumers): Eine Entität, die Ereignisdaten von einem Event Hub liest. Event Hubs-Consumer stellen Verbindungen über AMQP 1.0 her. 
- [**Consumergruppen**](event-hubs-features.md#consumer-groups): Stellen eine separate Ansicht vom Ereignisdatenstrom für jede aus mehreren Consumeranwendungen bestehende Gruppe bereit, wodurch diese Consumer unabhängig Vorgänge durchführen können.
- [**Durchsatzeinheiten**](event-hubs-features.md#capacity): Werden vorab als Kapazitätseinheiten erworben. Eine einzelne Partition weist eine maximale Skalierung von einer Durchsatzeinheit auf.

Technische Informationen zu diesen und anderen Event Hubs-Funktionen finden Sie im [Überblick über Event Hubs-Funktionen](event-hubs-features.md). 

## <a name="next-steps"></a>Nächste Schritte

Ausführliche Informationen zu Event Hubs-Preisen finden Sie unter [Event Hubs-Preise](https://azure.microsoft.com/pricing/details/event-hubs/).

Weitere Informationen zu Event Hubs erhalten Sie unter den folgenden Links:

* Erste Schritte mit einem [Event Hubs-Tutorial](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs – häufig gestellte Fragen](event-hubs-faq.md)
* [Beispielanwendungen mit Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
 
 

