<properties
   pageTitle="Zustandsbehaftete Reliable Services-Diagnose | Microsoft Azure"
   description="Diagnosefunktionen für zustandsbehaftete Reliable Services"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/17/2016"
   ms.author="alanwar"/>

# Diagnosefunktionen für zustandsbehaftete Reliable Services
Die Klasse StatefulServiceBase der zustandsbehafteten Reliable Services gibt [EventSource-Ereignisse](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) aus. Diese können verwendet werden, um den Dienst zu debuggen, Einblicke in den Laufzeitbetrieb zu erhalten und Fehler zu beheben.

## EventSource-Ereignisse
Die EventSource für die Klasse StatefulServiceBase des zustandsbehafteten Reliable Services heißt "Microsoft-ServiceFabric-Services". Ereignisse aus dieser Ereignisquelle werden im Fenster für [Diagnoseereignisse](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) beim [Debuggen des Diensts in Visual Studio](service-fabric-debugging-your-application.md) angezeigt.

Beispiele für Tools und Technologien, mit deren Hilfe EventSource-Ereignisse erfasst und/oder angezeigt werden können, sind [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Microsoft Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) und [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## Ereignisse

|Ereignisname|Ereignis-ID|Ebene|Ereignisbeschreibung|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|Information|Wird beim Start von RunAsync ausgegeben|
|StatefulRunAsyncCancellation|2|Information|Wird beim Abbrechen von RunAsync ausgegeben|
|StatefulRunAsyncCompletion|3|Information|Wird nach Abschluss von RunAsync ausgegeben|
|StatefulRunAsyncSlowCancellation|4|Warnung|Wird ausgegeben, wenn das Beenden von RunAsync zu lange dauert|
|StatefulRunAsyncFailure|5|Fehler|Wird bei einer Ausnahme von RunAsync ausgegeben|

## Interpretieren von Ereignissen

Die Ereignisse StatefulRunAsyncInvocation, StatefulRunAsyncCompletion und StatefulRunAsyncCancellation sind für den Dienstautor nützlich, um den Lebenszyklus eines Diensts sowie die zeitliche Steuerung des Starts, Abbruchs oder Abschlusses eines Diensts zu verstehen. Dies kann hilfreich sein, um Probleme zu debuggen oder den Dienstlebenszyklus zu verstehen.

Dienstautoren sollten unbedingt auf die Ereignisse StatefulRunAsyncSlowCancellation und StatefulRunAsyncFailure achten, da sie auf Probleme mit dem Dienst hinweisen.

StatefulRunAsyncFailure wird ausgegeben, wenn RunAsync() eine Ausnahme ausgibt. In der Regel weist eine Ausnahme auf einen Fehler oder Bug im Dienst hin. Darüber hinaus bewirkt die Ausnahme, dass der Dienst nicht ausgeführt wird, sodass er auf einen anderen Knoten verschoben wird. Dies kann ein kostspieliges Unterfangen sein und eingehende Anforderungen verzögern, während der Dienst verschoben wird. Dienstautoren sollten nach Möglichkeit die Ursache der Ausnahme ermitteln und beheben.

StatefulRunAsyncSlowCancellation wird ausgegeben, wenn eine Abbruchanforderung für RunAsync länger als vier Sekunden dauert. Wenn das Beenden eines Diensts zu lange dauert, beeinträchtigt dies seine Fähigkeit, umgehend auf einem anderen Knoten neu gestartet werden zu können. Dies kann sich auf die allgemeine Verfügbarkeit des Diensts auswirken.

<!---HONumber=AcomDC_0518_2016-->