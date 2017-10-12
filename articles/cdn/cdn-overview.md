---
title: "Übersicht über Azure CDN | Microsoft Docs"
description: "Erfahren Sie, was das Azure Content Delivery Network (CDN, Netzwerk für die Inhaltsübermittlung) ist und wie es für die Übermittlung breitbandiger Inhalte eingesetzt wird, indem Blobs und statische Inhalte zwischengespeichert werden."
services: cdn
documentationcenter: 
author: smcevoy
manager: akucer
editor: 
ms.assetid: 866e0c30-1f33-43a5-91f0-d22f033b16c6
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/08/2017
ms.author: v-semcev
ms.openlocfilehash: 909c4dc3feaeaedf56ecacc78f4b7e0e15d98875
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-the-azure-content-delivery-network-cdn"></a>Übersicht über das Azure Content Delivery Network (CDN)
> [!NOTE]
> In diesem Dokument wird beschrieben, worum es sich beim Azure Content Delivery Network (CDN) handelt, wie es funktioniert und über welche Features die einzelnen Azure CDN-Produkte verfügen.  Wenn Sie diese Informationen überspringen und direkt auf ein Tutorial zum Erstellen eines CDN-Endpunkts zugreifen möchten, helfen Ihnen die Informationen unter [Verwenden von Azure CDN](cdn-create-new-endpoint.md)weiter.  Eine Liste mit den aktuellen CDN-Knotenstandorten finden Sie unter [POP-Standorte von Azure Content Delivery Network (CDN)](cdn-pop-locations.md).
> 
> 

Das Azure Content Delivery Network (CDN) speichert statische Webinhalte an strategisch platzierten Standorten zwischen, um beim Bereitstellen von Inhalten für Benutzer einen maximalen Durchsatz zu ermöglichen.  Das CDN bietet Entwicklern eine globale Lösung für die Übermittlung von Inhalten mit hoher Bandbreite durch Zwischenspeichern der Inhalte auf physischen Knoten auf der ganzen Welt. 

Die Verwendung des CDN zum Zwischenspeichern von Websiteobjekten bietet folgende Vorteile:

* Bessere Leistung und höhere Benutzerfreundlichkeit für Endbenutzer, vor allem bei Verwendung von Anwendungen, für die zum Laden von Inhalten mehrere Roundtrips erforderlich sind
* Umfassende Skalierung, um hohe Lasten zu Beginn eines Ereignisses, z.B. bei einer Produkteinführung, besser verarbeiten zu können
* Durch das Verteilen der Benutzeranforderungen und die Bereitstellung von Inhalten über Edgeserver entfällt weniger Datenverkehr auf den Ursprung der Inhalte

## <a name="how-it-works"></a>So funktioniert's
![Übersicht über CDN](./media/cdn-overview/cdn-overview.png)

1. Ein Benutzer (Alice) fordert eine Datei (auch als „Asset“ bezeichnet) über eine URL mit einem speziellen Domänennamen an, z.B. `<endpointname>.azureedge.net`.  DNS leitet die Anforderung an den Point-of-Presence-Standort (POP) mit der besten Leistung weiter.  In der Regel ist dies der POP, der dem Benutzer geografisch am nächsten liegt.
2. Wenn die Datei nicht im Cache der Edgeserver am POP enthalten ist, fordert der Edgeserver die Datei vom Ursprung an.  Beim Ursprung kann es sich um eine Azure-Web-App, einen Azure Cloud Service, ein Azure Storage-Konto oder einen beliebigen öffentlich zugänglichen Webserver handeln.
3. Der Ursprung gibt die Datei an den Edgeserver zurück, und zwar einschließlich der optionalen HTTP-Header, in denen die Lebensdauer (Time-To-Live, TTL) beschrieben wird.
4. Der Edgeserver speichert die Datei zwischen und gibt sie an die ursprüngliche anfordernde Person (Alice) zurück.  Die Datei bleibt auf dem Edgeserver so lange zwischengespeichert, bis die Lebensdauer abläuft.  Falls vom Ursprung kein TTL-Wert angegeben wurde, wird der Standardwert von sieben Tagen verwendet.
5. Weitere Benutzer können die Datei dann über dieselbe URL anfordern und werden ggf. auch an denselben POP geleitet.
6. Sofern die Lebensdauer der Datei noch nicht abgelaufen ist, gibt der Edgeserver die Datei aus dem Cache zurück.  Dies führt zu schnellen Reaktionen und somit zu einer höheren Benutzerfreundlichkeit.

## <a name="azure-cdn-features"></a>Azure CDN-Features
Es gibt drei Azure CDN-Produkte: **Azure CDN Standard von Akamai**, **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon**.  Die folgende Tabelle listet die Features auf, die bei jedem Produkt zur Verfügung stehen.

|  | Standard Akamai | Standard Verizon | Premium Verizon |
| --- | --- | --- | --- |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  __Leistungsfeatures und -optimierungen__ |
| [Beschleunigung dynamischer Websites](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration) | **&amp;#x2713;**  | **&amp;#x2713;** | **&amp;#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  [Beschleunigung dynamischer Websites – Adaptive Bildkomprimierung](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-akamai-only) | **&amp;#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  [Beschleunigung dynamischer Websites – Objektvorabruf](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-akamai-only) | **&amp;#x2713;**  |  |  |
| [Videostreaming-Optimierung](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization) | **&amp;#x2713;**  | \* |  \* |
| [Optimierung großer Dateien](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization) | **&amp;#x2713;**  | \* |  \* |
| [Globaler Serverlastenausgleich (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure) |**&amp;#x2713;** |**&amp;#x2713;** |**&amp;#x2713;** |
| [Schnelles Löschen](cdn-purge-endpoint.md) |**&amp;#x2713;** |**&amp;#x2713;** |**&amp;#x2713;** |
| [Vorabladen von Assets](cdn-preload-endpoint.md) | |**&amp;#x2713;** |**&amp;#x2713;** |
| [Zwischenspeicherung von Abfragezeichenfolgen](cdn-query-string.md) |**&amp;#x2713;** |**&amp;#x2713;** |**&amp;#x2713;** |
| Dualer IPv4-/IPv6-Stapel |**&amp;#x2713;** |**&amp;#x2713;** |**&amp;#x2713;** |
| [HTTP/2-Unterstützung](cdn-http2.md) |**&amp;#x2713;** |**&amp;#x2713;** |**&amp;#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  __Sicherheit__ |
| Unterstützung von HTTPS mit CDN-Endpunkt |**&amp;#x2713;** |**&amp;#x2713;** |**&amp;#x2713;** |
| [HTTPS für benutzerdefinierte Domänen](cdn-custom-ssl.md) | |**&amp;#x2713;** |**&amp;#x2713;** |
| [Unterstützung benutzerdefinierter Domänennamen](cdn-map-content-to-custom-domain.md) |**&amp;#x2713;** |**&amp;#x2713;** |**&amp;#x2713;** |
| [Geofilterung](cdn-restrict-access-by-country.md) |**&amp;#x2713;** |**&amp;#x2713;** |**&amp;#x2713;** |
| [Tokenauthentifizierung](cdn-token-auth.md)|  |  |**&amp;#x2713;**| 
| [DDOS-Schutz](https://www.us-cert.gov/ncas/tips/ST04-015) |**&amp;#x2713;** |**&amp;#x2713;** |**&amp;#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  __Analysen und Berichte__ |
| [Wichtige Analysen](cdn-analyze-usage-patterns.md) | **&amp;#x2713;** |**&amp;#x2713;** |**&amp;#x2713;** |
| [Erweiterte HTTP-Berichte](cdn-advanced-http-reports.md) | | |**&amp;#x2713;** |
| [Echtzeitstatistiken](cdn-real-time-stats.md) | | |**&amp;#x2713;** |
| [Warnungen in Echtzeit](cdn-real-time-alerts.md) | | |**&amp;#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  __Einfache Bedienung__ |
| Einfache Integration in Azure-Dienste wie [Storage](cdn-create-a-storage-account-with-cdn.md), [Cloud Services](cdn-cloud-service-with-cdn.md), [Web-Apps](../app-service/app-service-web-tutorial-content-delivery-network.md) und [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md) |**&amp;#x2713;** |**&amp;#x2713;** |**&amp;#x2713;** |
| Verwaltung per [REST-API](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) oder [PowerShell](cdn-manage-powershell.md). |**&amp;#x2713;** |**&amp;#x2713;** |**&amp;#x2713;** |
| [Anpassbares, regelbasiertes Modul zur Inhaltsübermittlung](cdn-rules-engine.md) | | |**&amp;#x2713;** |
| Cache-/Headereinstellungen (mit [Regelmodul](cdn-rules-engine.md)) | | |**&amp;#x2713;** |
| URL-Umleitung/-Umschreibung (mit [Regelmodul](cdn-rules-engine.md)) | | |**&amp;#x2713;** |
| Regeln für mobile Geräte (mit [Regelmodul](cdn-rules-engine.md)) | | |**&amp;#x2713;** |

\* Verizon unterstützt die Übermittlung von umfangreichen Dateien und Medien direkt über die allgemeine Webbereitstellung.


> [!TIP]
> Gibt es eine Funktion, die Sie sich für Azure CDN wünschen?  [Geben Sie uns Feedback!](https://feedback.azure.com/forums/169397-cdn) 
> 
> 

## <a name="next-steps"></a>Nächste Schritte
Informationen zu den ersten Schritten mit CDN finden Sie unter [Verwenden von Azure CDN](cdn-create-new-endpoint.md).

Wenn Sie bereits CDN-Kunde sind, können Sie jetzt Ihre CDN-Endpunkte über das [Microsoft Azure-Portal](https://portal.azure.com) oder mit [PowerShell](cdn-manage-powershell.md) verwalten.

Im [Video zu unserer Build 2016-Veranstaltung](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/)sehen Sie das CDN in Aktion.

Erfahren Sie, wie Sie Azure CDN mit [.NET](cdn-app-dev-net.md) oder [Node.js](cdn-app-dev-node.md) automatisieren.

Preisinformationen finden Sie unter [Content Delivery Network (CDN) – Preise](https://azure.microsoft.com/pricing/details/cdn/).

