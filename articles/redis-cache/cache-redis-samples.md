---
title: "Azure Redis Cache – Beispiele | Microsoft Docs"
description: Erfahren Sie, wie Sie Azure Redis Cache verwenden.
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 1f8d210c-ee09-4fe2-b63f-1e69246a27d8
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2017
ms.author: sdanie
ms.openlocfilehash: 7841fcf0b5f4dcb409abf8bfb804c2e03dad6d3a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2017
---
# <a name="azure-redis-cache-samples"></a>Azure Redis Cache – Beispiele
Dieses Thema enthält eine Liste von Azure Redis Cache-Beispielen für Szenarien wie z.B. das Herstellen einer Verbindung mit einem Cache, das Lesen und Schreiben von Daten in und aus einem Cache und die Verwendung der ASP.NET Redis Cache-Anbieter. Einige der Beispiele sind herunterladbare Projekte und einige bieten eine schrittweise Anleitung und Codeausschnitte, ohne mit einem herunterladbaren Projekt verknüpft zu sein.

## <a name="hello-world-samples"></a>Hello World-Beispiele
Die Beispiele in diesem Abschnitt veranschaulichen die Grundlagen des Herstellens einer Verbindung mit einer Azure Redis Cache-Instanz und zum Lesen und Schreiben von Daten in dem Cache mithilfe einer Vielzahl von Sprachen und Redis-Clients.

Das [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)-Beispiel zeigt, wie verschiedene Cachevorgänge mithilfe des [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis)-.NET-Clients ausgeführt werden.

Dieses Beispiel zeigt Folgendes:

* Verwenden von verschiedenen Verbindungsoptionen
* Lesen und Schreiben von Objekten aus dem und in den Cache mithilfe von synchronen und asynchronen Vorgängen
* Verwenden der Redis-MGET/MSET-Befehle zum Zurückgeben von Werten aus angegebenen Schlüsseln
* Ausführen von Redis-Transaktionsvorgängen
* Arbeiten mit Listen und sortierten Sätzen von Redis
* Speichern von .NET-Objekten mithilfe von JsonConvert-Serialisierungsprogrammen
* Verwenden von Redis-Sätzen zum Implementieren des Taggings
* Arbeiten mit dem Redis-Cluster

Weitere Informationen finden Sie in der [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis)-Dokumentation auf GitHub, weitere Verwendungsszenarien finden Sie in den [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests)-Einheitentests.

[Verwenden von Azure Redis Cache mit Python](cache-python-get-started.md) zeigt die ersten Schritte mit Azure Redis Cache mit Python und dem [redis-py](https://github.com/andymccurdy/redis-py)-Client.

[Arbeiten mit .NET-Objekten im Cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) zeigt Ihnen eine Möglichkeit zum Serialisieren von .NET-Objekten, um sie aus einer Azure Redis Cache-Instanz zu lesen und darin zu schreiben. 

## <a name="use-redis-cache-as-a-scale-out-backplane-for-aspnet-signalr"></a>Verwenden von Redis Cache als horizontal hochskalierbare Backplane für ASP.NET SignalR
Das Beispiel [Verwenden von Redis Cache als horizontal hochskalierbare Backplane für ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) veranschaulicht die Verwendung von Azure Redis Cache als SignalR-Backplane. Weitere Informationen zur Backplane finden Sie unter [Horizontales Hochskalieren von SignalR mit Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="redis-cache-customer-query-sample"></a>Redis-Cache-Beispiel für Kundenabfrage
Dieses Beispiel veranschaulicht Leistungsvergleiche zwischen den Zugriff auf Daten aus einem Cache und dem Zugriff auf Daten in persistentem Speicher. Dieses Beispiel enthält zwei Projekte.

* [Demo: Verbessern der Leistung durch Zwischenspeichern von Daten in Redis Cache](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Seeding der Datenbank und des Caches für die Demo](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET-Sitzungszustand und Ausgabezwischenspeicherung
Das Beispiel [Verwenden von Azure Redis Cache zum Speichern von ASP.NET SessionState und OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) veranschaulicht die Nutzung von Azure Redis Cache zum Speichern des ASP.NET-Sitzungs- und Ausgabecaches mit den SessionState- und OutputCache-Anbietern für Redis.

## <a name="manage-azure-redis-cache-with-maml"></a>Verwalten von Azure Redis Cache mithilfe von Microsoft Azure-Verwaltungsbibliotheken
Das Beispiel [Verwalten von Azure Redis Cache mithilfe von Microsoft Azure-Verwaltungsbibliotheken](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) veranschaulicht, wie Sie Azure-Verwaltungsbibliotheken zum Verwalten (Erstellen/Aktualisieren/Löschen) Ihres Caches nutzen können. 

## <a name="custom-monitoring-sample"></a>Beispiel einer benutzerdefinierten Überwachung
Das Beispiel [Access Redis Cache Monitoring data](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) (in englischer Sprache) demonstriert, wie Sie außerhalb des Azure-Portals auf Überwachungsdaten für Ihren Azure Redis Cache zugreifen können.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Ein Klon im Stil von Twitter, geschrieben mit PHP und Redis
Das Beispiel [Retwis](https://github.com/SyntaxC4-MSFT/retwis) ist die Hello-World-Variante von Redis. Es handelt sich um den minimalen Klon eines sozialen Netzwerks im Twitter-Stil, der mithilfe von Redis und PHP mit dem [Predis](https://github.com/nrk/predis) -Client geschrieben wurde. Der Quellcode ist sehr einfach gehalten und zeigt dennoch verschiedene Redis-Datenstrukturen.

## <a name="bandwidth-monitor"></a>Bandbreitenüberwachung
Das Beispiel [Bandbreitenüberwachung](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) ermöglicht die Überwachung der auf dem Client genutzten Bandbreite. Um die Bandbreite zu messen, führen Sie das Beispiel auf dem Cacheclientcomputer aus, rufen Sie den Cache auf, und beobachten Sie die durch das Beispiel gemeldete Bandbreite.

