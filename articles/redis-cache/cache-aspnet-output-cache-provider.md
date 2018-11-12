---
title: Cache – ASP.NET-Ausgabecacheanbieter
description: Erfahren Sie, wie Sie die ASP.NET-Seitenausgabe mit Azure Redis Cache zwischenspeichern.
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/14/2017
ms.author: wesmc
ms.openlocfilehash: 6ea237c406a9d09b500a12755cd1fa99bb7d41cb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234424"
---
# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>ASP.NET-Ausgabecacheanbieter für Azure Redis Cache
Der Redis-Ausgabecacheanbieter ist ein prozessunabhängiger Speichermechanismus für Ausgabecachedaten. Diese Daten sind für vollständige HTTP-Antworten bestimmt (Zwischenspeichern von Seitenausgaben). Der Anbieter wird zum neuen Erweiterungspunkt des Ausgabecacheanbieters hinzugefügt, der in ASP.NET 4 eingeführt wurde.

Zum Verwenden des Redis-Ausgabecacheanbieters konfigurieren Sie zunächst den Cache. Danach konfigurieren Sie Ihre ASP.NET-Anwendung mithilfe des Redis-Ausgabecacheanbieter-NuGet-Pakets. In diesem Thema wird erläutert, wie Sie Ihre Anwendung für die Verwendung des Redis-Ausgabecacheanbieters konfigurieren. Informationen über das Erstellen und Konfigurieren einer Azure Redis Cache-Instanz finden Sie unter [Erstellen eines Caches](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Speichern der ASP.NET-Seitenausgabe im Cache
Klicken Sie zum Konfigurieren einer Clientanwendung in Visual Studio mit dem Redis Cache-Sitzungszustands-NuGet-Paket im Menü **Extras** auf **NuGet-Paket-Manager** > **Paket-Manager-Konsole**.

Führen Sie im Fenster `Package Manager Console` den folgenden Befehl aus.
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Das Redis-Ausgabecacheanbieter-NuGet-Paket steht in einer Abhängigkeitsbeziehung zum Paket „StackExchange.Redis.StrongName“. Wenn das Paket „StackExchange.Redis.StrongName“ nicht in Ihrem Projekt vorhanden ist, wird es installiert. Weitere Informationen zum Redis-Ausgabecacheanbieter-NuGet-Paket finden Sie auf der NuGet-Seite [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/).

>[!NOTE]
>Neben dem Paket „StackExchange.Redis.StrongName“ mit starkem Namen gibt es zudem noch das Paket „StackExchange.Redis“ als Version ohne starken Namen. Wenn in Ihrem Projekt die Version „StackExchange.Redis“ ohne starken Namen verwendet wird, müssen Sie sie deinstallieren. Andernfalls treten Namenskonflikte in Ihrem Projekt auf. Weitere Informationen über diese Pakete finden Sie unter [Konfigurieren der .NET-Cacheclients](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

Mit dem heruntergeladenen NuGet-Paket werden die erforderlichen Assemblyverweise sowie der folgende Abschnitt in Ihrer Datei „web.config“ hinzugefügt. Dieser Abschnitt enthält die Konfiguration, die in Ihrer ASP.NET-Anwendung für die Verwendung des Redis-Ausgabecacheanbieters erforderlich ist.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <!-- For more details check https://github.com/Azure/aspnet-redis-providers/wiki -->
      <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
      <!-- 'databaseId' and 'applicationName' can be used with both options. -->
      <!--
      <add name="MyRedisOutputCache" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "1000" [number]
        connectionString = "<Valid StackExchange.Redis connection string>" [String]
        settingsClassName = "<Assembly qualified class name that contains settings method specified below. Which basically return 'connectionString' value>" [String]
        settingsMethodName = "<Settings method should be defined in settingsClass. It should be public, static, does not take any parameters and should have a return type of 'String', which is basically 'connectionString' value.>" [String]
        loggingClassName = "<Assembly qualified class name that contains logging method specified below>" [String]
        loggingMethodName = "<Logging method should be defined in loggingClass. It should be public, static, does not take any parameters and should have a return type of System.IO.TextWriter.>" [String]
        redisSerializerType = "<Assembly qualified class name that implements Microsoft.Web.Redis.ISerializer>" [String]
      />
      -->
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Im kommentierten Bereich finden Sie Beispiele der Attribute sowie Beispieleinstellungen für jedes Attribut.

Konfigurieren Sie die Attribute mit den Werten vom Blatt „Cache“ im Microsoft Azure-Portal, und konfigurieren Sie die restlichen Werte wie gewünscht. Anweisungen zum Zugreifen auf die Cacheeigenschaften finden Sie unter [Konfigurieren von Redis-Cacheeinstellungen](cache-configure.md#configure-redis-cache-settings).

* **host** : Geben Sie den Cacheendpunkt an.
* **port** : Verwenden Sie abhängig von Ihren SSL-Einstellungen entweder den Nicht-SSL-Port oder den SSL-Port.
* **accessKey** : Verwenden Sie den primären oder sekundären Schlüssel für Ihren Cache.
* **ssl** : Geben Sie „true“ an, wenn Sie die Kommunikation zwischen Cache und Client mit SSL absichern möchten, andernfalls „false“. Achten Sie darauf, dass Sie den richtigen Port angeben.
  * Der Nicht-SSL-Port ist für neue Caches standardmäßig deaktiviert. Geben Sie für diese Einstellung „true“ an, wenn Sie den SSL-Port verwenden möchten. Weitere Informationen zum Aktivieren des Nicht-SSL-Ports finden Sie im Abschnitt [Zugriffsports](cache-configure.md#access-ports) des Themas [Konfigurieren eines Caches](cache-configure.md).
* **databaseId** : Geben Sie an, welche Datenbank für die Cacheausgabedaten verwendet werden soll. Wenn Sie hier nichts angeben, wird der Standardwert 0 verwendet.
* **applicationName**: Schlüssel werden in Redis als `<AppName>_<SessionId>_Data` gespeichert. Durch dieses Benennungsschema wird ermöglicht, dass mehrere Anwendungen denselben Schlüssel gemeinsam nutzen. Dieser Parameter ist optional, und wenn Sie ihn nicht angeben, wird der Standardwert verwendet.
* **connectionTimeoutInMilliseconds** : Diese Einstellung ermöglicht Ihnen, die connectTimeout-Einstellung im StackExchange.Redis-Client zu überschreiben. Wenn Sie sie nicht angegeben, wird die connectTimeout-Standardeinstellung „5000“ verwendet. Weitere Informationen finden Sie unter [StackExchange.Redis-Konfigurationsmodell](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** : Diese Einstellung ermöglicht Ihnen, die syncTimeout-Einstellung im StackExchange.Redis-Client zu überschreiben. Wenn Sie sie nicht angegeben, wird die syncTimeout-Standardeinstellung „1000“ verwendet. Weitere Informationen finden Sie unter [StackExchange.Redis-Konfigurationsmodell](https://go.microsoft.com/fwlink/?LinkId=398705).

Fügen Sie jeder Seite, für die Sie die Ausgabe zwischenspeichern möchten, eine OutputCache-Direktive hinzu.

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

Im vorherigen Beispiel verbleiben die zwischengespeicherten Seitendaten 60 Sekunden lang im Cache. Für jede Parameterkombination wird eine andere Version der Seite zwischengespeichert. Weitere Informationen über die OutputCache-Direktive finden Sie unter [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837).

Nach Abschluss dieser Schritte ist Ihre Anwendung für die Verwendung des Redis-Ausgabecacheanbieters konfiguriert.

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die Seite [ASP.NET-Sitzungszustandsanbieter für Azure Redis Cache](cache-aspnet-session-state-provider.md).

