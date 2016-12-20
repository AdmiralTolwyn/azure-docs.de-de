---
title: "Richtlinienreferenz für Azure API Management"
description: "Erfahren Sie mehr über die verfügbaren Richtlinien zum Konfigurieren von API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 9d4ac609-b221-4032-93ae-e27a1254d43d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 97a6f253eda39d0573a2384691a6b294c3894c25


---
# <a name="azure-api-management-policy-reference"></a>Richtlinienreferenz für Azure API Management
Dieser Abschnitt stellt einen Index der Richtlinien bereit, die in der [API Management-Richtlinienreferenz][API Management-Richtlinienreferenz] enthalten sind. Weitere Informationen zum Hinzufügen und Konfigurieren von Richtlinien finden Sie unter [Richtlinien in API Management][Richtlinien in API Management].

Richtlinienausdrücke können als Attributwerte oder Textwerte in einer beliebigen API Management-Richtlinie verwendet werden, sofern in der Richtlinie nicht anders angegeben. Einige Richtlinien, beispielsweise [Ablaufsteuerung][Ablaufsteuerung] und [Variable festlegen][Variable festlegen], basieren auf Richtlinienausdrücken. Weitere Informationen finden Sie unter [Erweiterte Richtlinien][Erweiterte Richtlinien] und [Richtlinienausdrücke][Richtlinienausdrücke].

## <a name="policy-reference-index"></a>Richtlinienreferenz – Index
* [Richtlinien für die Zugriffsbeschränkung][Richtlinien für die Zugriffsbeschränkung]
  * [HTTP-Header überprüfen][HTTP-Header überprüfen] – erfordert das Vorhandensein und/oder einen Wert eines HTTP-Headers.
  * [Limit call rate (Aufrufrate begrenzen)][Limit call rate (Aufrufrate begrenzen)] – verhindert API-Lastspitzen, indem die Aufrufrate jeweils pro Abonnement beschränkt wird.
  * [Limit call rate (Aufrufrate nach Schlüssel begrenzen)](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) – verhindert API-Lastspitzen, indem die Aufrufrate jeweils pro Schlüssel beschränkt wird.
  * [Beschränkung für Aufrufer-IP][Beschränkung für Aufrufer-IP] – Filtert (erlaubt/blockiert) Aufrufe von bestimmten IP-Adressen und/oder Adressbereichen.
  * [Set usage quota by subscription (Nutzungskontingent nach Abonnement festlegen) (Nutzungskontingent nach Abonnement festlegen)][Set usage quota by subscription (Nutzungskontingent nach Abonnement festlegen) (Nutzungskontingent nach Abonnement festlegen)] – Ermöglicht die Durchsetzung eines erneuerbaren oder für die Lebensdauer gültigen Kontingents für Aufrufe und/oder Bandbreite auf Grundlage des Abonnements.
  * [Set usage quota by key (Nutzungskontingent nach Schlüssel festlegen)](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) – ermöglicht die Durchsetzung eines erneuerbaren oder für die Lebensdauer gültigen Kontingents für Aufrufe und/oder Bandbreite auf Grundlage des Schlüssels.
  * [JWT überprüfen][JWT überprüfen] – Erzwingt das Vorhandensein und die Gültigkeit eines JWT, das entweder aus einem angegebenen HTTP-Header oder aus einem angegebenen Abfrageparameter extrahiert wurde.
* [Erweiterte Richtlinien][Erweiterte Richtlinien]
  * [Ablaufsteuerung][Ablaufsteuerung] – Bedingte Anwendung von Richtlinienanweisungen basierend auf den Ergebnissen der Auswertung von booleschen [Ausdrücken][Ausdrücken].
  * [Anforderung weiterleiten][Anforderung weiterleiten] – leitet die Anforderung an den Back-End-Dienst.
  * [Protokoll an Event Hub][Protokoll an Event Hub] – Sendet Nachrichten im angegebenen Format an ein von einem [Protokollierungstool](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger) definiertes Nachrichtenziel.
  * [Wiederholen](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) – Wiederholt die Ausführung der eingeschlossenen Richtlinienanweisungen, falls und bis die Bedingung erfüllt ist. Die Ausführung wird mit den angegebenen Zeitintervallen und bis zur angegebenen Anzahl der Wiederholungsversuche wiederholt.
  * [Zurückgegebene Antwort](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) – bricht die Pipeline-Ausführung ab und gibt die angegebene Antwort unmittelbar an den Aufrufer zurück.
  * [Unidirektionale Anforderung senden](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) – sendet eine Anforderung an die angegebene URL, ohne auf eine Antwort zu warten.
  * [Sendeanforderung](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) – sendet eine Anforderung an die angegebene URL.
  * [Anforderungsmethode festlegen](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) – dient der Vornahme von Änderungen der HTTP-Anforderungsmethode.
  * [Statuscode festlegen](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) – ändert den HTTP-Statuscode auf den angegebenen Wert.
  * [Variable festlegen][Variable festlegen] – Speichert einen Wert in einer benannten [Kontextvariable][Kontextvariable], um später darauf zugreifen zu können.
  * [Ablaufverfolgung](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) – Fügt eine Zeichenfolge in der Ausgabe für den [API-Inspektor](api-management-howto-api-inspector.md) hinzu.
  * [Warten](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) : Wartet darauf, dass eingeschlossene Richtlinien für „Send request“ (Sendeanforderung), „Get value from cache“ (Wert aus dem Cache abrufen) oder „Control flow“ (Ablaufsteuerung) abgeschlossen werden, bevor der Vorgang fortgesetzt wird.
* [Authentifizierungsrichtlinien][Authentifizierungsrichtlinien]
  * [Standardauthentifizierung][Standardauthentifizierung] – Authentifizierung mit einem Back-End-Dienst unter Verwendung der Standardauthentifizierung.
  * [Authentifizierung mit Clientzertifikat][Authentifizierung mit Clientzertifikat] – Authentifizierung mit einem Back-End-Dienst unter Verwendung von Clientzertifikaten.
* [Cachingrichtlinien][Cachingrichtlinien] 
  * [Aus Cache abrufen][Aus Cache abrufen] – Führt eine Cachesuche aus und gibt ggf. eine gültige Antwort aus dem Cache zurück.
  * [In Cache ablegen][In Cache ablegen] – Cacheantwort gemäß der angegebenen Konfiguration für die Cachesteuerung.
  * [Get value from cache (Wert aus dem Cache abrufen)](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) – ruft ein zwischengespeichertes Element nach Schlüssel ab.
  * [Store value in cache (Wert im Cache speichern)](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) – speichert ein Element im Cache auf Basis des Schlüssels.
  * [Wert aus dem Cache entfernen](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) – Entfernt ein Element im Cache nach Schlüssel.
* [Domänenübergreifende Richtlinien][Domänenübergreifende Richtlinien] 
  * [Domänenübergreifende Aufrufe zulassen][Domänenübergreifende Aufrufe zulassen] – Erlaubt API-Aufrufe aus browserbasierten Clients, die Adobe Flash und Microsoft Silverlight verwenden.
  * [CORS][CORS] – Fügt Unterstützung für Cross-Origin Resource Sharing (CORS) zu einer Operation oder einer API hinzu, um domänenübergreifende Aufrufe aus browserbasierten Clients zu ermöglichen.
  * [JSONP][JSONP] – Fügt Unterstützung für JSON mit Padding (JSONP) zu einer Operation oder einer API hinzu, um domänenübergreifende Aufrufe aus browserbasierten Clients mit JavaScript zu ermöglichen.
* [Transformationsrichtlinien][Transformationsrichtlinien] 
  * [JSON in XML konvertieren][JSON in XML konvertieren] – Konvertiert den Anforderungs- oder Antworttext von JSON in XML.
  * [XML in JSON konvertieren][XML in JSON konvertieren] – Konvertiert den Anforderungs- oder Antworttext von XML in JSON.
  * [Zeichenfolge in Text ersetzen][Zeichenfolge in Text ersetzen] – Sucht nach einer Zeichenfolge in Antwort oder Anforderung und ersetzt diese durch eine andere Teilzeichenfolge.
  * [URLs in Inhalt maskieren][URLs in Inhalt maskieren] – Ändert (maskiert) Links im Antworttext, sodass diese über das Gateway auf den äquivalenten Link zeigen.
  * [Back-End-Dienst festlegen][Back-End-Dienst festlegen] – Ändert den Back-End-Dienst für eine eingehende Anforderung.
  * [Text festlegen][Text festlegen] – Legt den Nachrichtentext für eingehende und ausgehende Anforderungen fest.
  * [HTTP-Header setzen][HTTP-Header setzen] – Weist einem vorhandenen Antwort- und/oder Anforderungsheader einen Wert zu oder fügt einen neuen Antwort- und/oder Anforderungsheader hinzu.
  * [Abfrageparameter setzen][Abfrageparameter setzen] – Fügt Abfrageparameter hinzu, löscht diese oder ersetzt deren Werte.
  * [URL umschreiben][URL umschreiben] – Konvertiert eine Anforderungs-URL von der öffentlichen Form in die vom Webdienst erwartete Form.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Richtlinienausdrücken finden Sie im folgenden Video.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Richtlinien für die Zugriffsbeschränkung]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[HTTP-Header überprüfen]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Limit call rate (Aufrufrate begrenzen)]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Beschränkung für Aufrufer-IP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Set usage quota by subscription (Nutzungskontingent nach Abonnement festlegen) (Nutzungskontingent nach Abonnement festlegen)]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[JWT überprüfen]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Erweiterte Richtlinien]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Ablaufsteuerung]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Variable festlegen]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Ausdrücken]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[Kontextvariable]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Anforderung weiterleiten]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Protokoll an Event Hub]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Authentifizierungsrichtlinien]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Standardauthentifizierung]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Authentifizierung mit Clientzertifikat]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Cachingrichtlinien]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Aus Cache abrufen]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[In Cache ablegen]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Domänenübergreifende Richtlinien]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Domänenübergreifende Aufrufe zulassen]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Transformationsrichtlinien]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[JSON in XML konvertieren]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[XML in JSON konvertieren]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Zeichenfolge in Text ersetzen]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[URLs in Inhalt maskieren]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Back-End-Dienst festlegen]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Text festlegen]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[HTTP-Header setzen]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Abfrageparameter setzen]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[URL umschreiben]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Richtlinien in API Management]: api-management-howto-policies.md
[API Management-Richtlinienreferenz]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Richtlinienausdrücke]: https://msdn.microsoft.com/library/azure/dn910913.aspx





<!--HONumber=Nov16_HO3-->


