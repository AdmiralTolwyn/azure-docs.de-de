---
title: "Übersicht über Event Hubs-Authentifizierung und -Sicherheitsmodell | Microsoft Docs"
description: "Übersicht über Event Hubs-Authentifizierung und -Sicherheitsmodell."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: sethm;clemensv
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9447b863b62fc71d2619591f78be0494711dc6a3


---
# <a name="event-hubs-authentication-and-security-model-overview"></a>Event Hubs-Authentifizierung und -Sicherheitsmodell (Übersicht)
Das Sicherheitsmodell von Event Hubs erfüllt die folgenden Voraussetzungen:

* Nur Geräte, die über gültige Anmeldeinformationen verfügen, können Daten an einen Event Hub senden.
* Ein Gerät kann kein anderes Gerät imitieren.
* Ein nicht autorisiertes Gerät kann vom Senden von Daten an einen Event Hub blockiert werden.

## <a name="device-authentication"></a>Geräte-Authentifizierung
Das Event Hubs-Sicherheitsmodell basiert auf einer Kombination aus [Shared Access Signature (SAS)](../service-bus-messaging/service-bus-shared-access-signature-authentication.md)-Token und *Ereignisherausgebern*. Ein Ereignisherausgeber definiert einen virtuellen Endpunkt für einen Event Hub. Der Herausgeber kann nur zum Senden von Nachrichten an einen Event Hub verwendet werden. Es ist nicht möglich, von einem Herausgeber Nachrichten zu empfangen.

In der Regel setzt ein Event Hub einen Herausgeber pro Gerät ein. Alle Nachrichten, die an einen der Herausgeber eines Event Hubs gesendet werden, werden in die Warteschlange innerhalb dieses Event Hubs eingereiht. Herausgeber ermöglichen die präzise Access Control und Zugriffsdrosselung.

Jedes Gerät erhält ein eindeutiges Token, das auf dem Gerät hochgeladen wird. Token werden so erzeugt, sodass jedes eindeutige Token Zugriff auf einen anderen eindeutigen Herausgeber gewährt. Ein Gerät, das ein Token besitzt, kann nur an einen Herausgeber senden und zu keinem anderen. Wenn mehrere Geräte das gleiche Token gemeinsam nutzen, teilt jedes dieser Geräte einen Herausgeber.

Obwohl dies nicht empfohlen wird, ist es möglich, Geräte mit Token auszustatten, die direkten Zugriff auf einen Event Hub gewähren. Alle Geräte, die solche Token enthalten, können Nachrichten direkt an den Event Hub senden. Solche Geräte unterliegen keiner Drosselung. Darüber hinaus kann das Gerät nicht davon gesperrt werden, an den betreffenden Event Hub zu senden.

Alle Token werden mit einem SAS-Schlüssel signiert. Alle Token werden in der Regel mit demselben Schlüssel signiert. Geräte kennen den Schlüssel nicht. Dadurch wird verhindert, dass Geräte Token erzeugen.

### <a name="create-the-sas-key"></a>Erstellen des SAS-Schlüssels
Wenn Sie einen Event Hubs-Namespace erstellen, generiert Azure Event Hubs einen 256-Bit-SAS-Schlüssel mit dem Namen **RootManageSharedAccessKey**. Dieser Schlüssel erteilt die Rechte zum Senden, Überwachen und Verwalten für den Namespace. Sie können zusätzliche Schlüssel erstellen. Es wird empfohlen, dass Sie einen Schlüssel erzeugen, der Berechtigungen zum Senden an den bestimmten Event Hub erteilt. Für den Rest dieses Themas wird davon ausgegangen, dass Sie diesen Schlüssel `EventHubSendKey`benannt haben.

Das folgende Beispiel erstellt einen reinen Sende-Schlüssel beim Erstellen des Event Hubs:

```csharp
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event Hub with a SAS rule that enables sending to that Event Hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Generieren von Token
Sie können Token mit dem SAS-Schlüssel generieren. Sie müssen nur ein Token pro Gerät erzeugen. Token können dann mit dem folgenden Verfahren erstellt werden. Alle Token werden mit dem Schlüssel **EventHubSendKey** generiert. Jedes Token erhält einen eindeutigen URI.

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Beim Aufrufen dieser Methode sollte der URI als `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`angegeben werden. Der URI ist für alle Token nahezu identisch, mit Ausnahme von `PUBLISHER_NAME`, der für jedes Token unterschiedlich sein sollte. Im Idealfall stellt `PUBLISHER_NAME` die ID des Geräts dar, das dieses Token erhält.

Diese Methode generiert ein Token mit der folgenden Struktur:

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

Die Tokengültigkeitsdauer wird als Zeit in Sekunden ab dem 1. Januar 1970 angegeben. Es folgt ein Beispiel für ein Token:

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

In der Regel müssen die Token eine Lebensdauer besitzen, der der Lebensdauer des Geräts entspricht oder diese übersteigt. Wenn das Gerät die Möglichkeit hat, ein neues Token zu erhalten, können Token mit einer kürzeren Lebensdauer verwendet werden.

### <a name="devices-sending-data"></a>Geräte, die Daten senden
Sobald die Token erstellt wurden, wird jedes Gerät mit eigenem eindeutigen Token bereitgestellt.

Wenn das Gerät Daten an einen Event Hub sendet, markiert das Gerät sein Token mit der Sendeanforderung. Um Lauschangriffe und Diebstahl von Token von einem Angreifer zu verhindern, muss die Kommunikation zwischen dem Gerät und dem Event Hub über einen verschlüsselten Kanal erfolgen.

### <a name="blacklisting-devices"></a>Geräte blockieren
Wenn ein Token von einem Angreifer gestohlen wird, kann der Angreifer das Gerät imitieren, dessen Token gestohlen wurde. Das Blockieren eines Geräts rendert das Gerät als unverwendbar, bis das Gerät ein neues Token erhält, das einen anderen Herausgeber verwendet.

## <a name="authentication-of-back-end-applications"></a>Authentifizierung von Back-End-Anwendungen
Um Back-End-Anwendungen zu authentifizieren, die von Geräten generierte Daten nutzen, setzen Event Hubs ein Sicherheitsmodell ähnlich dem Modell ein, das für Service Bus-Themen verwendet wird. Eine Event Hubs-Consumergruppe entspricht einem Abonnement für ein Service Bus-Thema. Ein Client kann eine Consumergruppe erstellen, wenn die Anforderung zum Erstellen der Consumergruppe von einem Token begleitet wird, der Berechtigungen zum Verwalten des Event Hubs gewährt, oder für den Namespace, zu dem das Event Hub gehört. Ein Client kann Daten aus einer Consumergruppe nutzen, wenn die Empfangsanforderung von einem Token begleitet wird, die der Gruppe, dem Event Hub oder dem Namespace, zu dem das Event Hub gehört, Empfangsrechte erteilt.

Die aktuelle Version des Service Bus unterstützt nicht die SAS-Regeln für einzelne Abonnements. Dasselbe gilt für Event Hubs-Consumergruppen. SAS-Support wird in Zukunft für beide Funktionen hinzugefügt werden.

In Ermangelung einer SAS-Authentifizierung für einzelne Consumergruppen können Sie SAS-Schlüssel nutzen, um alle Consumergruppen mit einem gemeinsamen Schlüssel zu sichern. Dieser Ansatz ermöglicht einer Anwendung, Daten von allen Consumergruppen eines Event Hubs zu verwenden.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Event Hubs finden Sie unter den folgenden Themen:

* [Übersicht über Event Hubs]
* Eine [Messaginglösung mit Warteschlange] unter Verwendung von Service Bus-Warteschlangen.
* Eine vollständige [Beispielanwendung mit Verwendung von Ereignis-Hubs].

[Übersicht über Event Hubs]: event-hubs-overview.md
[Beispielanwendung mit Verwendung von Ereignis-Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Messaginglösung mit Warteschlange]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md




<!--HONumber=Nov16_HO3-->


