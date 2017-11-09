---
title: Erweiterte Verwendung von Reliable Services | Microsoft Docs
description: "Erfahren Sie mehr über die erweiterte Verwendung der Reliable Services von Service Fabric für mehr Flexibilität für Ihre Dienste."
services: Service-Fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: masnider
ms.assetid: f2942871-863d-47c3-b14a-7cdad9a742c7
ms.service: Service-Fabric
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 694d75807d978ece6296b945bf348f08688d3b5d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2017
---
# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>Erweiterte Verwendung des Reliable Services-Programmiermodells
Azure Service Fabric vereinfacht das Schreiben und Verwalten zuverlässiger zustandsloser und zustandsbehafteter Dienste (Reliable Services). In diesem Handbuch wird die erweiterte Verwendung von Reliable Services erläutert, die Ihnen mehr Kontrolle über und Flexibilität für Ihre Dienste ermöglicht. Machen Sie sich vor dem Lesen dieses Handbuchs mit dem [Reliable Services-Programmiermodell](service-fabric-reliable-services-introduction.md)vertraut.

Sowohl zustandsbehaftete als auch zustandslose Dienste weisen zwei primäre Einstiegspunkte für Benutzercode auf:

* `RunAsync(C#) / runAsync(Java)` ist ein allgemein gültiger Einstiegspunkt für Ihren Dienstcode.
* `CreateServiceReplicaListeners(C#)` und `CreateServiceInstanceListeners(C#) / createServiceInstanceListeners(Java)` dienen dazu, Kommunikationslistener für Clientanforderungen zu öffnen.

Für die meisten Dienste sind diese zwei Einstiegspunkte ausreichend. In den seltenen Fällen, in denen mehr Kontrolle über den Lebenszyklus eines Diensts erforderlich ist, sind zusätzliche Lebenszyklusereignisse verfügbar.

## <a name="stateless-service-instance-lifecycle"></a>Lebenszyklus der Instanz eines zustandslosen Diensts
Der Lebenszyklus eines zustandslosen Diensts ist sehr einfach. Ein zustandsloser Dienst kann nur geöffnet, geschlossen oder abgebrochen werden. `RunAsync` wird in einem zustandslosen Dienst ausgeführt, wenn eine Dienstinstanz geöffnet wird, und abgebrochen, wenn eine Dienstinstanz geschlossen oder abgebrochen wird.

Obwohl `RunAsync` in fast allen Fällen ausreichen sollte, sind die Ereignisse „Öffnen“, „Schließen“ und „Abbrechen“ auch in zustandslosen Diensten verfügbar:

* `Task OnOpenAsync(IStatelessServicePartition, CancellationToken) - C# / CompletableFuture<String> onOpenAsync(CancellationToken) - Java` „OnOpenAsync“ wird zur Verwendung der Instanz des zustandslosen Diensts aufgerufen. Erweiterte Serviceinitialisierungsaufgaben können zu diesem Zeitpunkt gestartet werden.
* `Task OnCloseAsync(CancellationToken) - C# / CompletableFuture onCloseAsync(CancellationToken) - Java` „OnCloseAsync“ wird aufgerufen, wenn die Instanz des zustandslosen Diensts ordnungsgemäß beendet wird. Dies kann der Fall sein, wenn Code für den Dienst aktualisiert, die Dienstinstanz aufgrund des Lastenausgleichs verschoben oder ein vorübergehender Fehler erkannt wird. "OnCloseAsync" kann verwendet werden, um Ressourcen sicher zu schließen, die Hintergrundverarbeitung anzuhalten, das Speichern des externen Status zu beenden oder bestehende Verbindungen zu deaktivieren.
* `void OnAbort() - C# / void onAbort() - Java` „OnAbort“ wird aufgerufen, wenn das Herunterfahren der Instanz des zustandslosen Diensts erzwungen wird. Diese Methode wird im Allgemeinen verwendet, wenn auf dem Knoten ein dauerhafter Fehler erkannt wird oder Service Fabric den Lebenszyklus der Dienstinstanz aufgrund von internen Fehlern nicht zuverlässig verwalten kann.

## <a name="stateful-service-replica-lifecycle"></a>Lebenszyklus des zustandsbehafteten Dienstreplikats

> [!NOTE]
> Zustandsbehaftete zuverlässige Dienste werden in Java noch nicht unterstützt.
>
>

Der Lebenszyklus eines zustandsbehafteten Dienstsreplikats ist viel komplizierter als der einer zustandslosen Dienstinstanz. Zusätzlich zu den Ereignissen „Öffnen“, „Schließen“ und „Abbrechen“ durchläuft ein zustandsbehafteter Dienst während seiner Lebensdauer Rollenänderungen. Wenn ein zustandsbehaftetes Dienstreplikat die Rolle wechselt, wird das Ereignis `OnChangeRoleAsync` ausgelöst:

* `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync wird immer dann aufgerufen, wenn das zustandsbehaftete Dienstreplikat die Rolle wechselt und beispielsweise ein primäres oder sekundäres Replikat wird. Primäre Replikate erhalten Schreibstatus (mit Erlaubnis zum Erstellen und Schreiben in Reliable Collections). Sekundäre Replikate erhalten Lesestatus (können nur aus vorhandenen Reliable Collections lesen). Die meisten Aufgaben in einem zustandsbehafteten Dienst werden im primären Replikat ausgeführt. Sekundäre Replikate können schreibgeschützte Überprüfungen durchführen, Berichte generieren und Data Mining oder andere schreibgeschützte Aufträge ausführen.

In einem zustandsbehafteten Dienst verfügt nur das primäre Replikat über Schreibzugriff auf den Zustand. Daher ist dies in der Regel wo der Dienst die eigentliche Arbeit ausführt. Die `RunAsync`-Methode wird in einem zustandsbehafteten Dienst nur ausgeführt, wenn das Replikat des zustandsbehafteten Diensts primär ist. Die `RunAsync`-Methode wird abgebrochen, wenn ein primäres Replikat seine Rolle ändert und kein primäres Replikat mehr ist, oder während der Ereignisse „Schließen“ oder „Abbrechen“.

Die Verwendung des Ereignisses `OnChangeRoleAsync` erlaubt Ihnen die Ausführung der Arbeitsschritte, abhängig von der Replikatsrolle sowie als Reaktion auf Rollenwechsel.

Ein zustandsbehafteter Dienst bietet außerdem die gleichen vier Lebenszyklusereignisse wie ein zustandsloser Dienst, mit derselben Semantik und identischen Anwendungsfällen:

```csharp
* Task OnOpenAsync(IStatefulServicePartition, CancellationToken)
* Task OnCloseAsync(CancellationToken)
* void OnAbort()
```

## <a name="next-steps"></a>Nächste Schritte
Erweiterte Themen im Zusammenhang mit Service Fabric finden Sie in den folgenden Artikeln.

* [Konfigurieren zustandsbehafteter Reliable Services](service-fabric-reliable-services-configuration.md)
* [Einführung in Service Fabric-Integrität](service-fabric-health-introduction.md)
* [Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Konfigurieren von Diensten mit dem Clusterressourcen-Manager von Service Fabric](service-fabric-cluster-resource-manager-configure-services.md)
