---
title: Includedatei
description: Includedatei
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 02/26/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 9d6b54027adcf2b12c6ca4081a11208a31f620e8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804095"
---
In der folgenden Tabelle sind die Kontingente und Grenzwerte aufgelistet, die für [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) gelten. Informationen zu den Preisen von Event Hubs finden Sie unter [Event Hubs – Preise](https://azure.microsoft.com/pricing/details/event-hubs/).

| Begrenzung | Bereich | Notizen | Wert |
| --- | --- | --- | --- |
| Anzahl von Event Hubs-Namespaces pro Abonnement |Abonnement |- |100 |
| Anzahl von Event Hubs pro Namespace |Namespace |Nachfolgende Anforderungen für die Erstellung eines neuen Event Hub werden zurückgewiesen. |10 |
| Anzahl von Partitionen pro Event Hub |Entität |- |32 |
| Anzahl von Consumergruppen pro Event Hub |Entität |- |20 |
| Anzahl von AMQP-Verbindungen pro Namespace |Namespace |Nachfolgende Anforderungen für zusätzliche Verbindungen werden abgelehnt, und der aufrufende Code empfängt eine Ausnahme. |5.000 |
| Maximale Größe des Event Hubs-Ereignisses|Entität |- |1 MB |
| Maximale Größe eines Event Hub-Namens |Entität |- |50 Zeichen |
| Anzahl nicht epochenbezogener Empfänger pro Consumergruppe |Entität |- |5 |
| Maximale Aufbewahrungsdauer von Ereignisdaten |Entität |- |1–7 Tage |
| Maximale Durchsatzeinheiten |Namespace |Bei einer Überschreitung des Grenzwerts für Durchsatzeinheiten werden Ihre Daten gedrosselt, und es wird eine [ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) ausgelöst. Um eine höhere Anzahl von Durchsatzeinheiten für den Tarif „Standard“ anzufordern, erstellen Sie eine [Supportanfrage](/azure/azure-supportability/how-to-create-azure-support-request). [Zusätzliche Durchsatzeinheiten](../articles/event-hubs/event-hubs-auto-inflate.md) sind für einen festgelegten Kaufpreis in 20er-Blöcken verfügbar. |20 |
| Anzahl von Autorisierungsregeln pro Namespace |Namespace|Nachfolgende Anforderungen zur Erstellung von Autorisierungsregeln werden abgelehnt.|12 |
