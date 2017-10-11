---
title: "Sicherheit für Notification Hubs"
description: "In diesem Thema wird Sicherheit für Azure Notification Hubs erläutert."
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: 
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 7c3283799806135060bb8ca57ea398c93d1106bb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2017
---
# <a name="security"></a>Sicherheit
## <a name="overview"></a>Übersicht
Dieses Thema beschreibt das Sicherheitsmodell von Azure Notification Hubs. Da Notification Hubs eine Service Bus-Entität sind, wird das gleiche Sicherheitsmodell wie bei Service Bus implementiert. Weitere Informationen finden Sie unter [Service Bus Authentication](https://msdn.microsoft.com/library/azure/dn155925.aspx) (in englischer Sprache).

## <a name="shared-access-signature-security-sas"></a>Shared Access Signature-Sicherheit (SAS)
Notification Hubs implementieren ein Sicherheitsschema auf Entitätsebene, das SAS (Shared Access Signature) genannt wird. Dieses Schema ermöglicht Messagingentitäten das Deklarieren von bis zu 12 Autorisierungsregeln in ihrer Beschreibung, die Zugriffsrechte für diese Entität gewähren.

Jede Regel enthält einen Namen, einen Schlüsselwert (gemeinsamer geheimer Schlüssel) und eine Reihe von Rechten, wie im Abschnitt „Sicherheitsansprüche“ erläutert. Beim Erstellen eines Notification Hubs werden automatisch zwei Regeln erstellt: eine mit Lauschberechtigung (von der Clientanwendung verwendet) und eine mit allen Berechtigungen (vom Back-End verwendet).

Wenn eine Registrierungsverwaltung von Client-Apps aus durchgeführt wird und die über Benachrichtigungen gesendeten Informationen nicht vertraulich sind (z. B. aktuelle Wetterdaten), besteht eine gebräuchliche Möglichkeit zum Zugreifen auf einen Notification Hub darin, dem Schlüsselwert der Regel nur Lauschzugriff für die Client-App zu erteilen und dem Schlüsselwert der Regel Vollzugriff auf das Back-End zu gewähren.

Es wird nicht empfohlen, den Schlüsselwert in Windows Store-Client-Apps einzubetten. Eine Möglichkeit, das Einbetten des Schlüsselwerts zu vermeiden, besteht darin, ihm beim Start der Client-App durch diese aus dem App-Back-End abrufen zu lassen.

Beachten Sie unbedingt, dass der Schlüssel mit Lauschzugriff einer Clientanwendung die Möglichkeit gibt, sich für jedes beliebige Tag zu registrieren. Wenn Ihre App Registrierungen auf bestimmte Tags für bestimmte Clients einschränken muss (z. B., wenn Tags für Benutzer-IDs stehen), muss das App-Back-End die Registrierungen ausführen. Weitere Informationen finden Sie unter „Registrierungsverwaltung“. Beachten Sie, dass die Clientanwendung auf diese Weise keinen direkten Zugriff auf Notification Hubs hat.

## <a name="security-claims"></a>Sicherheitsansprüche
Ähnlich wie bei anderen Entitäten sind Notification Hub-Vorgänge für drei Sicherheitsansprüche zulässig: Lauschen, Senden und Verwalten.

| Anspruch | Beschreibung | Zulässige Vorgänge |
| --- | --- | --- |
| Empfangen |Erstellen/Aktualisieren, Lesen und Löschen einzelner Registrierungen |Erstellen/Aktualisieren einer Registrierung<br><br>Lesen der Registrierung<br><br>Lesen aller Registrierungen für ein Handle<br><br>Löschen einer Registrierung |
| Send |Senden von Nachrichten an den Notification Hub |Nachricht senden |
| Verwalten |CRUDs für Notification Hubs (einschließlich des Aktualisierens von PNS-Anmeldeinformationen und Sicherheitsschlüsseln) und Lesen von Registrierungen basierend auf Tags |Erstellen/Aktualisieren/Lesen/Löschen von Notification Hubs<br><br>Lesen von Registrierungen nach Tag |

Notification Hubs akzeptieren Ansprüche, die durch Microsoft Azure Access Control-Token und von Signaturtoken mit freigegebenen Schlüsseln, die direkt auf dem Notification Hub konfiguriert wurden, erteilt werden.

