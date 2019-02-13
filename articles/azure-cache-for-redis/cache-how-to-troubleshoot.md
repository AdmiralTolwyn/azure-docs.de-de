---
title: Problembehandlung für Azure Cache for Redis | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie häufige Probleme bei Azure Cache for Redis beheben.
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: wesmc
ms.openlocfilehash: d513825cad397763792fdc9ffb833ba54e957e7d
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55822662"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>Problembehandlung für Azure Cache for Redis
Dieser Artikel enthält Hinweise zur Behandlung der folgenden Kategorien von Azure Cache for Redis-Problemen.

* [Behandeln von clientseitigen Problemen](#client-side-troubleshooting): Dieser Abschnitt enthält Richtlinien zum Identifizieren und Beheben von Problemen, deren Ursache bei der mit Azure Cache for Redis verbundenen Anwendung liegt.
* [Behandeln von serverseitigen Problemen](#server-side-troubleshooting): Dieser Abschnitt enthält Richtlinien zum Identifizieren und Beheben von Problemen, deren Ursache auf der Serverseite von Azure Cache for Redis liegt.
* [StackExchange.Redis-Timeoutausnahmen](#stackexchangeredis-timeout-exceptions) : Dieser Abschnitt enthält Informationen zur Behebung von Problemen bei Verwendung des StackExchange.Redis-Clients.

> [!NOTE]
> Etliche Problembehandlungsschritte in diesem Leitfaden enthalten Anleitungen zum Ausführen von Redis-Befehlen und zum Überwachen verschiedener Leistungsmetriken. Weitere Informationen und Anleitungen finden Sie in den Artikeln im Abschnitt [Weitere Informationen](#additional-information) .
> 
> 

## <a name="client-side-troubleshooting"></a>Behandeln von clientseitigen Problemen
In diesem Abschnitt wird das Behandeln von Problemen beschrieben, die wegen eines Zustands in der Clientanwendung auftreten.

* [Hohe Speicherauslastung auf dem Client](#memory-pressure-on-the-client)
* [Datenverkehrsspitzen](#burst-of-traffic)
* [Hohe Auslastung der Client-CPU](#high-client-cpu-usage)
* [Bandbreitenüberschreitung auf der Clientseite](#client-side-bandwidth-exceeded)
* [Große Anforderungen/große Antworten](#large-requestresponse-size)
* [Was ist mit meinen Daten in Redis passiert?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Hohe Speicherauslastung auf dem Client
#### <a name="problem"></a>Problem
Wenn der Arbeitsspeicher auf dem Clientcomputer sehr stark ausgelastet ist, führt dies zu Leistungsproblemen aller Art und unter Umständen auch zu einer verzögerten Verarbeitung der von der Redis-Instanz gesendeten Daten. Bei vollkommen ausgelastetem Arbeitsspeicher muss das System in der Regel Daten aus dem physischen Speicher in den virtuellen Speicher auslagern, also auf den Datenträger. Diese sogenannten *Seitenfehler* bewirken eine deutliche Verlangsamung des Systems.

#### <a name="measurement"></a>Messung
1. Überwachen Sie die Speicherauslastung auf dem Computer und stellen Sie sicher, dass sie den verfügbaren Speicher nicht überschreitet. 
2. Überwachen Sie den Leistungsindikator `Page Faults/Sec`. Bei den meisten Systemen treten selbst im normalen Betrieb einige Seitenfehler auf. Achten Sie deshalb bei diesem Leistungsindikator für Seitenfehler besonders auf die Spitzen, denn diese entsprechen Timeouts.

#### <a name="resolution"></a>Lösung
Verwenden Sie für Ihren Client einen größeren virtuellen Computer mit mehr Arbeitsspeicher, oder analysieren Sie die Muster der Arbeitsspeichernutzung, um die Auslastung zu verringern.

### <a name="burst-of-traffic"></a>Datenverkehrsspitzen
#### <a name="problem"></a>Problem
Plötzliche Anstiege des Datenverkehrsvolumens in Verbindung mit unzureichenden `ThreadPool` -Einstellungen können zu Verzögerungen beim Verarbeiten der vom Redis-Server bereits gesendeten, aber auf der Clientseite noch nicht genutzten Daten führen.

#### <a name="measurement"></a>Messung
Überwachen Sie die Änderungen bei den `ThreadPool`-Statistiken im zeitlichen Verlauf. Verwenden Sie dazu Code wie [diesen](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Sie können sich auch die `TimeoutException`-Meldung von StackExchange.Redis ansehen. Beispiel: 

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

In der oben aufgeführten Meldung zeigen sich mehrere interessante Probleme:

1. Beachten Sie, dass in den Abschnitten `IOCP` und `WORKER` der `Busy`-Wert größer als der `Min`-Wert ist. Dieser Unterschied bedeutet, dass die `ThreadPool`-Einstellungen angepasst werden müssen.
2. Außerdem wird `in: 64221` angegeben. Das bedeutet, dass 64.211 Bytes auf der Kernelsocketebene empfangen, aber von der Anwendung (z.B. StackExchange.Redis) noch nicht gelesen wurden. Dieser Unterschied bedeutet in der Regel, dass Ihre Anwendung Daten aus dem Netzwerk nicht so schnell liest, wie sie vom Server gesendet werden.

#### <a name="resolution"></a>Lösung
Konfigurieren Sie Ihre [ThreadPool-Einstellungen](https://gist.github.com/JonCole/e65411214030f0d823cb) so, dass Ihr Threadpool bei Datenverkehrsspitzen schnell hochskaliert wird.

### <a name="high-client-cpu-usage"></a>Hohe Auslastung der Client-CPU
#### <a name="problem"></a>Problem
Eine hohe CPU-Auslastung auf dem Client weist darauf hin, dass das System das von ihm geforderte Arbeitsaufkommen nicht mehr bewältigen kann. Das bedeutet, dass der Client eine Antwort von Redis u.U. nicht rechtzeitig verarbeiten kann, obwohl Redis die Antwort schnell gesendet hat.

#### <a name="measurement"></a>Messung
Überwachen Sie die systemweite CPU-Auslastung im Azure-Portal oder mithilfe des entsprechenden Leistungsindikators. Achten Sie darauf, dass Sie nicht die *Prozess*-CPU überwachen. Für einen einzelnen Prozess kann die CPU-Auslastung gering sein, während gleichzeitig die Gesamtauslastung der System-CPU hoch ist. Beobachten Sie die Spitzen bei der CPU-Auslastung, denn sie korrespondieren mit Timeouts. Als Folge einer hohen CPU-Auslastung sehen Sie in den `TimeoutException`-Fehlermeldungen möglicherweise auch hohe Werte für `in: XXX`, wie unter [Datenverkehrsspitzen](#burst-of-traffic) beschrieben.

> [!NOTE]
> Ab StackExchange.Redis 1.1.603 ist in den `TimeoutException`-Fehlermeldungen die Metrik `local-cpu` enthalten. Verwenden Sie immer die neueste Version des [NuGet-Pakets für StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Es werden ständig Fehler im Code behoben, um ihn widerstandsfähiger gegen Timeouts zu machen. Das Nutzen der neuesten Version ist also wichtig.
> 
> 

#### <a name="resolution"></a>Lösung
Führen Sie ein Upgrade auf einen größeren virtuellen Computer mit mehr CPU-Kapazität durch, oder untersuchen Sie, was die CPU-Spitzen verursacht. 

### <a name="client-side-bandwidth-exceeded"></a>Bandbreitenüberschreitung auf der Clientseite
#### <a name="problem"></a>Problem
Je nach Architektur der Clientcomputer bestehen möglicherweise Einschränkungen im Hinblick auf die verfügbare Netzwerkbandbreite. Wenn auf dem Client durch Überladen der Netzwerkkapazität die verfügbare Bandbreite überschritten wird, werden die Daten auf der Clientseite nicht so schnell verarbeitet, wie sie vom Server gesendet werden. Diese Situation kann zu Timeouts führen.

#### <a name="measurement"></a>Messung
Überwachen Sie die Änderungen bei der Bandbreitennutzung im zeitlichen Verlauf. Verwenden Sie dazu Code [wie diesen](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Dieser Code wird in einigen Umgebungen mit eingeschränkten Berechtigungen (z.B. Azure-Websites) möglicherweise nicht erfolgreich ausgeführt.

#### <a name="resolution"></a>Lösung
Vergrößern Sie den virtuellen Computer für den Client, oder verringern Sie die in Anspruch genommene Netzwerkbandbreite.

### <a name="large-requestresponse-size"></a>Große Anforderungen/große Antworten
#### <a name="problem"></a>Problem
Eine große Anforderung oder eine große Antwort kann Timeouts verursachen. Nehmen Sie beispielsweise an, dass auf Ihrem Client ein Timeoutwert von einer Sekunde konfiguriert ist. Ihre Anwendung fordert (unter Verwendung derselben physischen Verbindung) zwei Schlüssel gleichzeitig an (z.B. „A“ und „B“). Die meisten Clients unterstützen das „Pipelining“ für solche Anforderungen. Das heißt: Die beiden Anforderungen „A“ und „B“ werden nacheinander übertragen, ohne dass auf Antworten gewartet wird. Der Server sendet die Antworten in der gleichen Reihenfolge zurück. Wenn die Antwort „A“ groß ist, kann sie den Großteil der Timeoutzeit für die folgenden Anforderungen in Anspruch nehmen. 

Das folgende Beispiel veranschaulicht dieses Szenario. In diesem Szenario werden die Anforderungen „A“ und „B“ schnell gesendet, und der Server beginnt schnell mit dem Senden der Antworten „A“ und „B“. Infolge der Datenübertragungszeiten bleibt „B“ jedoch hinter der anderen Anforderung mit Timeout stecken, obwohl der Server schnell geantwortet hat.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Messung
Dieses Anforderungs-/Antwortszenario ist schwer zu messen. Im Grunde müssen Sie Ihren Clientcode für die Nachverfolgung von großen Anfragen und Antworten instrumentieren. 

#### <a name="resolution"></a>Lösung
1. Redis ist für eine große Anzahl von kleinen Werten optimiert, nicht für wenige große Werte. Die bevorzugte Lösung besteht darin, Ihre Daten in verknüpfte kleinere Werte aufzuteilen. Ausführliche Informationen zu Gründen, warum kleinere Werte empfohlen werden, finden Sie im Beitrag [What is the ideal value size range for redis? Is 100 KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) (Welche Größe ist für Werte bei Redis ideal? Sind 100 KB zu viel?).
2. Vergrößern Sie Ihren virtuellen Computer (für den Client und den Azure Cache for Redis-Server), um höhere Bandbreitenkapazitäten zu erhalten und dadurch die Datenübertragungszeiten für größere Antworten zu verkürzen. Möglicherweise reicht es nicht aus, nur für den Server oder nur für den Client mehr Bandbreite zu erhalten. Messen Sie die Bandbreitennutzung, und vergleichen Sie sie mit der Kapazität, die der Größe des gegenwärtig verwendeten virtuellen Computers zugeordnet ist.
3. Erhöhen Sie die Anzahl der verwendeten `ConnectionMultiplexer` -Objekte und Roundrobinanforderungen über verschiedene Verbindungen.

### <a name="what-happened-to-my-data-in-redis"></a>Was ist mit meinen Daten in Redis passiert?
#### <a name="problem"></a>Problem
Ich habe erwartet, dass sich bestimmte Daten in meiner Azure Cache for Redis-Instanz befinden, aber sie sind scheinbar nicht dort.

#### <a name="resolution"></a>Lösung
Mögliche Ursachen und Lösungen finden Sie unter [What happened to my data in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) (Was ist mit meinen Daten in Redis passiert?)

## <a name="server-side-troubleshooting"></a>Behandeln von serverseitigen Problemen
In diesem Abschnitt wird das Behandeln von Problemen beschrieben, die wegen eines Zustands auf dem Cacheserver auftreten.

* [Hohe Speicherauslastung auf dem Server](#memory-pressure-on-the-server)
* Hohe CPU-Auslastung/hohe Serverauslastung
* [Bandbreitenüberschreitung auf der Serverseite](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Hohe Speicherauslastung auf dem Server
#### <a name="problem"></a>Problem
Wenn der Arbeitsspeicher auf der Serverseite sehr stark ausgelastet ist, führt dies zu Leistungsproblemen aller Art und unter Umständen auch zu einer verzögerten Verarbeitung von Anforderungen. Bei vollkommen ausgelastetem Arbeitsspeicher muss das System in der Regel Daten aus dem physischen Speicher in den virtuellen Speicher auslagern, also auf den Datenträger. Diese sogenannten *Seitenfehler* bewirken eine deutliche Verlangsamung des Systems. Eine zu hohe Speicherauslastung kann verschiedene Ursachen haben: 

1. Sie haben den Cache bis zur Kapazitätsgrenze mit Daten gefüllt. 
2. Der Speicher in Redis ist stark fragmentiert. Dies wird meist durch das Speichern großer Objekte verursacht (Redis ist für kleine Objekte optimiert – siehe [What is the ideal value size range for redis? Is 100 KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) [Welche Größe ist für Werte bei Redis ideal? Sind 100 KB zu viel?]). 

#### <a name="measurement"></a>Messung
Redis macht zwei Metriken verfügbar, mit deren Hilfe Sie das Problem identifizieren können. Bei der ersten handelt es sich um `used_memory` und bei der zweiten um `used_memory_rss`. [Diese Metriken](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) können im Azure-Portal oder über den Befehl [Redis INFO](https://redis.io/commands/info) genutzt werden.

#### <a name="resolution"></a>Lösung
Sie können mehrere Änderungen vornehmen, um die Speicherauslastung in einem gesunden Rahmen zu halten:

1. [Konfigurieren Sie eine Speicherrichtlinie](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , und legen Sie Ablauffristen für Ihre Schlüssel fest. Wenn Fragmentierung vorliegt, reicht diese Konfiguration möglicherweise nicht aus.
2. [Konfigurieren Sie einen Wert für „maxmemory-reserved“](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , der groß genug ist, um die Speicherfragmentierung zu kompensieren.
3. Teilen Sie große zwischengespeicherte Objekte in kleinere verknüpfte Objekte auf.
4. [Skalieren](cache-how-to-scale.md) Sie auf einen größeren Cache.
5. Wenn Sie einen [Premium-Cache mit aktiviertem Redis-Cluster](cache-how-to-premium-clustering.md) verwenden, können Sie die [Anzahl von Shards erhöhen](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Hohe CPU-Auslastung/hohe Serverauslastung
#### <a name="problem"></a>Problem
Eine hohe CPU-Auslastung kann bedeuten, dass die Clientseite eine Antwort von Redis u.U. nicht rechtzeitig verarbeiten kann, obwohl Redis die Antwort schnell gesendet hat.

#### <a name="measurement"></a>Messung
Überwachen Sie die systemweite CPU-Auslastung im Azure-Portal oder mithilfe des entsprechenden Leistungsindikators. Achten Sie darauf, dass Sie nicht die *Prozess*-CPU überwachen. Für einen einzelnen Prozess kann die CPU-Auslastung gering sein, während gleichzeitig die Gesamtauslastung der System-CPU hoch ist. Beobachten Sie die Spitzen bei der CPU-Auslastung, denn sie korrespondieren mit Timeouts.

#### <a name="resolution"></a>Lösung
* Lesen Sie die Empfehlungen und Warnungen im [Azure Cache for Redis-Ratgeber](cache-configure.md#azure-cache-for-redis-advisor).
* Lesen Sie auch die weiteren Empfehlungen in diesem Thema sowie die [Best Practices for Azure Redis](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f) (Bewährte Methoden für Azure Redis), um festzustellen, ob Sie alle Möglichkeiten ausgeschöpft haben, um Ihren Cache und Ihren Client weiter zu optimieren. 
* Sehen Sie sich die [Azure Cache for Redis-Leistungstabellen](cache-faq.md#azure-cache-for-redis-performance) an, um zu ermitteln, ob Sie sich möglicherweise dem oberen Schwellenwert Ihres aktuellen Tarifs nähern. [Skalieren](cache-how-to-scale.md) Sie Ihr System bei Bedarf auf einen größeren Cachetarif mit mehr CPU-Kapazität hoch. Wenn Sie bereits den Premium-Tarif verwenden, sollten Sie über eine [horizontale Hochskalierung mit Clustern](cache-how-to-premium-clustering.md) nachdenken.


### <a name="server-side-bandwidth-exceeded"></a>Bandbreitenüberschreitung auf der Serverseite
#### <a name="problem"></a>Problem
Je nach Größe der Cacheinstanzen bestehen möglicherweise Einschränkungen im Hinblick auf die verfügbare Netzwerkbandbreite. Wenn der Server die verfügbare Bandbreite überschreitet, werden die Daten nicht so schnell an den Client gesendet wie erwartet. Diese Situation kann zu Timeouts führen.

#### <a name="measurement"></a>Messung
Sie können die Metrik `Cache Read` überwachen. Dies ist die Menge an Daten in Megabyte pro Sekunde (MB/s), die während des angegebenen Berichtsintervalls aus dem Cache gelesen wurde. Dieser Wert entspricht der von diesem Cache verwendeten Netzwerkbandbreite. Wenn Sie Warnungen für serverseitige Grenzwerte bei der Netzwerkbandbreite einrichten möchten, können Sie diese mithilfe des Leistungsindikators `Cache Read` erstellen. Vergleichen Sie die gemessenen Werte mit den Werten in [dieser Tabelle](cache-faq.md#cache-performance), in der die beobachteten Bandbreitengrenzwerte für verschiedene Cachetarife und -größen aufgelistet sind.

#### <a name="resolution"></a>Lösung
Wenn bei Ihnen die Bandbreite regelmäßig in der Nähe der beobachteten maximalen Bandbreite für Ihren Tarif und Ihre Cachegröße liegt, sollten Sie eine [Skalierung](cache-how-to-scale.md) auf einen Tarif oder eine Größe mit mehr Netzwerkbandbreite ins Auge fassen. Nutzen Sie die Werte in [dieser Tabelle](cache-faq.md#cache-performance) als Richtschnur.

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis-Timeoutausnahmen
StackExchange.Redis verwendet für synchrone Vorgänge eine Konfigurationseinstellung mit dem Namen `synctimeout`. Der Standardwert für diese Einstellung lautet 1.000 ms. Wenn ein synchroner Aufruf nicht in der vorgesehenen Zeit abgeschlossen wird, löst der StackExchange.Redis-Client einen Timeoutfehler ähnlich dem folgenden Beispiel aus:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Diese Fehlermeldung enthält Metriken, mit deren Hilfe Sie die Ursache und die mögliche Lösung des Problems ermitteln können. In der folgenden Tabelle finden Sie Details zu den Metriken in der Fehlermeldung.

| Metrik in der Fehlermeldung | Details |
| --- | --- |
| inst |In der letzten Zeitscheibe: 0 Befehle wurden ausgegeben. |
| mgr |Der Socket-Manager führt `socket.select` aus. Das heißt, er fordert das Betriebssystem auf, einen beschäftigten Socket anzugeben. Dies bedeutet im Grunde: Der Reader liest nicht aktiv aus dem Netzwerk, weil er meint, dass nichts zu tun ist. |
| queue |Es sind insgesamt 73 Vorgänge in Bearbeitung. |
| qu |6 der in Bearbeitung befindlichen Vorgänge befinden sich in der Warteschlange für „Nicht gesendet“ und wurden noch nicht in das Netzwerk für ausgehenden Datenverkehr geschrieben. |
| qs |67 der in Bearbeitung befindlichen Vorgänge wurden an den Server gesendet, aber es ist noch keine Antwort verfügbar. Als Antwort ist Folgendes möglich: `Not yet sent by the server` oder `sent by the server but not yet processed by the client.`. |
| qc |Für 0 der in Bearbeitung befindlichen Vorgänge liegt eine Antwort vor, aber die Vorgänge wurden noch nicht als abgeschlossen markiert, weil noch auf die Abschlussschleife gewartet wird. |
| wr |Es ist ein aktiver Writer vorhanden (d.h., die 6 noch nicht gesendeten Anforderungen werden nicht ignoriert); Bytes/aktive Writer. |
| in |Es sind keine aktiven Reader vorhanden, und null Bytes sind zum Lesen auf der NIC (Network Interface Card, Netzwerkschnittstellenkarte) vorhanden; Bytes/aktive Reader |

### <a name="steps-to-investigate"></a>Untersuchungsschritte
1. Wenn Sie den StackExchange.Redis-Client verwenden, sollten Sie als bewährte Methode zum Herstellen der Verbindung das folgende Muster verwenden.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit dem Cache mithilfe von StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Stellen Sie sicher, dass sich Ihr Azure Cache for Redis und die Clientanwendung in der gleichen Azure-Region befinden. Wenn Ihr Cache z.B. in der Region „USA, Osten“ und Ihr Client in der Region „USA, Westen“ angesiedelt ist, erhalten Sie möglicherweise Timeouts, und die Anforderung wird nicht innerhalb des `synctimeout`-Intervalls abgeschlossen. Auch beim Debuggen über den lokalen Entwicklungscomputer können Timeouts auftreten. 
   
    Es wird dringend empfohlen, den Cache und den Client in der gleichen Azure-Region anzusiedeln. Wenn Ihr Szenario regionsübergreifende Aufrufe beinhaltet, sollten Sie das `synctimeout`-Intervall auf einen höheren Wert als die standardmäßigen 1.000 ms festlegen. Nehmen Sie zu diesem Zweck eine `synctimeout`-Eigenschaft in die Verbindungszeichenfolge auf. Das folgende Beispiel zeigt einen Codeausschnitt aus einer Verbindungszeichenfolge für einen StackExchange.Azure Cache for Redis, in der ein `synctimeout` von 2000 ms festgelegt ist.
   
        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
2. Verwenden Sie immer die neueste Version des [NuGet-Pakets für StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Es werden ständig Fehler im Code behoben, um ihn widerstandsfähiger gegen Timeouts zu machen. Das Nutzen der neuesten Version ist also wichtig.
3. Wenn Anforderungen durch Bandbreiteneinschränkungen auf dem Server oder dem Client behindert werden, dauert deren Ausführung länger, und es können Timeouts auftreten. Um festzustellen, ob ein Timeout durch die verfügbare Netzwerkbandbreite auf dem Server verursacht wird, befolgen Sie die Anweisungen unter [Bandbreitenüberschreitung auf der Serverseite](#server-side-bandwidth-exceeded). Um festzustellen, ob ein Timeout durch die verfügbare Netzwerkbandbreite auf dem Client verursacht wird, befolgen Sie die Anweisungen unter [Bandbreitenüberschreitung auf der Clientseite](#client-side-bandwidth-exceeded).
4. Werden Sie durch die CPU auf dem Server oder auf dem Client behindert?
   
   * Überprüfen Sie, ob es durch die CPU auf dem Client zu Einschränkungen kommt. Wenn dies der Fall ist, werden Anforderungen u.U. nicht innerhalb des `synctimeout`-Intervalls verarbeitet, was zu Timeouts führt. Dieses Problem lässt sich möglicherweise durch Verwendung eines größeren Clients oder durch Verteilung der Last lösen. 
   * Überprüfen Sie, ob Sie durch die CPU auf dem Server behindert werden. Überwachen Sie dazu die [Cacheleistungsmetrik](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `CPU`. Wenn Anforderungen eingehen, während Redis durch die CPU behindert wird, können für diese Anforderungen Timeouts auftreten. Sie können Abhilfe schaffen, indem Sie die Last auf mehrere Shards in einem Premium-Cache verteilen. Alternativ können Sie ein Upgrade zu einer größer dimensionierten CPU durchführen oder in einen höheren Tarif wechseln. Weitere Informationen finden Sie unter [Bandbreitenüberschreitung auf der Serverseite](#server-side-bandwidth-exceeded).
5. Dauert die Verarbeitung von Befehlen auf dem Server lange? Wenn die Verarbeitung und Ausführung von Befehlen auf dem Redis-Server lange dauert, kann dies zu Timeouts führen. Beispiele für Befehle mit langen Ausführungszeiten sind `mget` mit einer großen Anzahl von Schlüsseln, `keys *` oder schlecht geschriebene Lua-Skripts. Sie können mithilfe des redis-cli-Clients eine Verbindung mit Ihrer Azure Cache for Redis-Instanz herstellen oder die [Redis-Konsole](cache-configure.md#redis-console) verwenden und den Befehl [SlowLog](https://redis.io/commands/slowlog) ausführen, um festzustellen, ob für Anforderungen mehr Zeit als erwartet benötigt wird. Der Redis-Server und StackExchange.Redis sind für viele kleine Anforderungen optimiert, nicht für wenige große. Durch eine Aufteilung Ihrer Daten in kleinere Blöcke können Sie u.U. Verbesserungen erzielen. 
   
    Informationen zum Herstellen einer Verbindung mit dem Azure Cache for Redis-SSL-Endpunkt mithilfe von „redis-cli“ und „stunnel“ finden Sie im Blogbeitrag [Announcing ASP.NET Session State Provider for Redis Preview Release](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) (Ankündigung des ASP.NET-Sitzungszustandsanbieters für Redis – Vorschauversion). Weitere Informationen finden Sie unter [SlowLog](https://redis.io/commands/slowlog).
6. Eine hohe Auslastung des Redis-Servers kann zu Timeouts führen. Sie können die Serverauslastung überwachen, indem Sie die [Cacheleistungsmetrik](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Redis Server Load` überwachen. Eine Serverauslastung von 100 (Maximalwert) bedeutet, dass der Redis-Server mit der Verarbeitung von Anforderungen voll ausgelastet war und keine Leerlaufzeiten aufgetreten sind. Um festzustellen, ob bestimmte Anforderungen die gesamte Serverkapazität in Anspruch nehmen, führen Sie den Befehl „SlowLog“ aus, wie im vorherigen Absatz beschrieben. Weitere Informationen finden Sie unter „Hohe CPU-Auslastung/hohe Serverauslastung“.
7. Gab es ein anderes Ereignis auf der Clientseite, das ein Netzwerkflackern verursacht haben könnte? Überprüfen Sie auf dem Client (Web- oder Workerrolle oder IaaS-VM), ob die Anzahl von Clientinstanzen hoch- oder herunterskaliert oder eine neue Version des Clients bereitgestellt wurde oder die automatische Skalierung aktiviert ist. Bei unseren Tests haben wir festgestellt, dass beim automatischen Skalieren oder beim Hoch- oder Herunterskalieren die Verbindung mit dem Netzwerk für ausgehenden Datenverkehr mehrere Sekunden lang unterbrochen werden kann. Der StackExchange.Redis-Code ist unempfindlich gegen solche Ereignisse und stellt die Verbindung anschließend wieder her. Allerdings kann für Anforderungen, die sich während dieser Verbindungswiederherstellung in der Warteschlange befinden, ein Timeout auftreten.
8. Gab es vor mehreren kleineren Anforderungen eine große Anforderung an den Azure Cache for Redis, für die ein Timeout auftrat? Der Parameter `qs` in der Fehlermeldung teilt Ihnen mit, wie viele Anforderungen vom Client an den Server gesendet wurden, für die aber bisher keine Antwort verarbeitet wurde. Dieser Wert kann fortlaufend wachsen, weil StackExchange.Redis eine einzige TCP-Verbindung nutzt und zu einem Zeitpunkt jeweils immer nur eine Antwort lesen kann. Obwohl für den ersten Vorgang ein Timeout aufgetreten ist, können weiterhin Daten an den Server und vom Server gesendet werden. Weitere Anforderungen werden allerdings blockiert, bis die umfangreiche Anforderung verarbeitet ist, und dies führt zu weiteren Timeouts. Als mögliche Lösung können Sie die Wahrscheinlichkeit von Timeouts verringern. Stellen Sie zu diesem Zweck sicher, dass Ihr Cache für Ihre Workload groß genug ist, und teilen Sie große Werte in kleinere Blöcke auf. Eine weitere Lösung besteht darin, in Ihrem Client einen Pool von `ConnectionMultiplexer`-Objekten zu verwenden und beim Senden einer neuen Anforderung den am wenigsten ausgelasteten `ConnectionMultiplexer` zu verwenden. Dadurch können Sie verhindern, dass ein einzelnes Timeout weitere Timeouts für andere Anforderungen nach sich zieht.
9. Wenn Sie `RedisSessionStateProvider`verwenden, müssen Sie das Timeout für Wiederholungsversuche richtig festlegen. `retryTimeoutInMilliseconds` sollte höher sein als `operationTimeoutInMilliseconds`, andernfalls erfolgen keine Wiederholungsversuche. Im folgenden Beispiel ist `retryTimeoutInMilliseconds` auf 3000 festgelegt. Weitere Informationen finden Sie unter [ASP.NET-Sitzungszustandsanbieter für Azure Cache for Redis](cache-aspnet-session-state-provider.md) und [How to use configuration parameters of Session State Provider and Output Cache Provider](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration) (Verwenden der Konfigurationsparameter des Sitzungszustandsanbieters und des Ausgabecacheanbieters).

    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />


1. Überprüfen Sie die Speicherauslastung auf dem Azure Cache for Redis-Server, indem Sie `Used Memory RSS` und `Used Memory` [überwachen](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Wenn eine Entfernungsrichtlinie vorhanden ist und `Used_Memory` die Cachegröße erreicht, beginnt Redis mit dem Entfernen von Schlüsseln. Im Idealfall sollte `Used Memory RSS` nur geringfügig höher als `Used memory` sein. Ein großer Unterschied bedeutet, dass eine (interne oder externe) Speicherfragmentierung vorhanden ist. Wenn `Used Memory RSS` niedriger ist als `Used Memory`, bedeutet das, dass ein Teil des Cachespeichers vom Betriebssystem ausgelagert wurde. Im Fall einer solchen Auslagerung müssen Sie mit erheblichen Latenzen rechnen. Da Redis nicht steuern kann, wie seine Belegungen den Speicherseiten zugeordnet werden, ist ein hoher Wert für `Used Memory RSS` oft das Ergebnis einer Speicherauslastungsspitze. Wenn Redis Speicher freigibt, wird dieser Speicher dem Allocator zurückgegeben. Der Allocator kann den Speicher dem System zurückgeben, aber er muss dies nicht tun. Es kann zu einer Diskrepanz zwischen dem Wert für `Used Memory` und der vom Betriebssystem gemeldeten Speichernutzung kommen. Die Ursache dafür liegt möglicherweise darin, dass Speicher von Redis genutzt und freigegeben, aber nicht an das System zurückgegeben wurde. Um Speicherprobleme möglichst gering zu halten, können Sie die folgenden Schritte ausführen:
   
   * Führen Sie ein Upgrade auf einen größeren Cache durch, damit in Ihrem System genügend Speicher vorhanden ist und entsprechende Einschränkungen wegfallen.
   * Legen Sie Ablauffristen für die Schlüssel fest, damit ältere Werte proaktiv entfernt werden.
   * Überwachen Sie die Cachemetrik `used_memory_rss`. Wenn sich dieser Wert der Größe Ihres Caches nähert, sind Leistungsprobleme absehbar. Falls Sie einen Premium-Cache nutzen, verteilen Sie die Daten über mehrere Shards. Andernfalls führen Sie ein Upgrade auf einen größeren Cache durch.
   
   Weitere Informationen finden Sie unter [Hohe Speicherauslastung auf dem Server](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Zusätzliche Informationen
* [Welches Azure Cache for Redis-Angebot und welche Redis Cache-Größe sollte ich verwenden?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Wie kann ich die Leistung meines Caches messen und testen?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Wie führe ich Redis-Befehle aus?](cache-faq.md#how-can-i-run-redis-commands)
* [Überwachen von Azure Cache for Redis](cache-how-to-monitor.md)

