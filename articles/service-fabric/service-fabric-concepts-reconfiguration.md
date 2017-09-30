---
title: Neukonfiguration in Azure Service Fabric | Microsoft-Dokumentation
description: Grundlegendes zur Neukonfiguration von Partitionen in Service Fabric
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: 
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: aprameyr
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 39f9bec12d6fde1576f283ac80f72e62581efc9c
ms.contentlocale: de-de
ms.lasthandoff: 09/25/2017

---

# <a name="reconfiguration-in-azure-service-fabric"></a>Neukonfiguration in Azure Service Fabric
Eine *Konfiguration* ist definiert als die Replikate und ihre Rollen für eine Partition eines zustandsbehafteten Diensts.

Eine *Neukonfiguration* ist der Prozess der Verschiebung einer *Konfiguration* zu einer anderen *Konfiguration*. Sie bewirkt eine Änderung der Replikatgruppe für eine Partition eines zustandsbehafteten Diensts. Die alte Konfiguration wird als *vorherige Konfiguration (Previous Configuration, PC)* und die neue Konfiguration als *aktuelle Konfiguration (Current Configuration, CC)* bezeichnet. Das Neukonfigurationsprotokoll in Service Fabric behält die Konsistenz bei und hält die Verfügbarkeit während jeglicher Änderungen an der Replikatgruppe aufrecht.

Der Failover-Manager initiiert Neukonfigurationen als Reaktion auf verschiedene Ereignisse im System. Beispiel: Wenn das primäre Replikat ausfällt, wird eine Neukonfiguration initiiert, um ein aktives sekundäres Replikat zu einem primären herauf zu stufen. Ein weiteres Beispiel ist die Reaktion auf Anwendungsupgrades, wenn es möglicherweise notwendig ist, das primäre Replikat auf einen anderen Knoten zu verschieben, um den Knoten zu aktualisieren.

## <a name="reconfiguration-types"></a>Neukonfigurationstypen
Neukonfigurationen können in zwei Typen klassifiziert werden:

- Neukonfigurationen, in denen sich das primäre Replikat ändert
    - **Failover**: Failover sind Neukonfigurationen als Reaktion auf den Ausfall eines ausgeführten primären Replikats
    - **SwapPrimary**: Austausche sind Neukonfigurationen, bei denen Service Fabric ein ausgeführtes primäres Replikat in der Regel als Reaktion auf einen Lastenausgleich, ein Upgrade etc. von einem Knoten zu einem anderen verschieben muss.

- Neukonfigurationen, in denen sich das primäre Replikat nicht ändert

## <a name="reconfiguration-phases"></a>Neukonfigurationsphasen
Eine Neukonfiguration erfolgt in mehreren Phasen:

- **Phase 0**: Diese Phase wird in Austauschneukonfigurationen primärer Replikate durchgeführt, wo das aktuelle primäre Replikat seinen Zustand auf das neue primäre Replikat überträgt und in das aktive sekundäre Replikat übergeht.

- **Phase 1**: Diese Phase erfolgt während der Neukonfigurationen, in denen sich das primäre Replikat ändert. Während dieser Phase identifiziert Service Fabric unter den aktuellen Replikaten das richtige primäre Replikat. Diese Phase ist bei Austauschneukonfigurationen primärer Replikate nicht erforderlich, da das neue primäre Replikat bereits ausgewählt wurde. 

- **Phase 2**: In dieser Phase stellt Service Fabric sicher, dass alle Daten in der Mehrheit der Replikate der aktuellen Konfiguration verfügbar sind.

Es gibt mehrere andere Phasen, die nur zur internen Verwendung bestimmt sind.

## <a name="stuck-reconfigurations"></a>Hängen gebliebene Neukonfigurationen
Neukonfigurationen können aus einer Vielzahl von Gründen *hängen bleiben*. Einige der häufigsten Gründe sind:

- **Inaktive Replikate**: Einige Neukonfigurationsphasen erfordern, dass eine Mehrheit der Replikate in der Konfiguration aktiv ist.
- **Netzwerk- oder Kommunikationsprobleme**: Neukonfigurationen erfordern Netzwerkkonnektivität zwischen verschiedenen Knoten.
- **API-Fehler**: Das Neukonfigurationsprotokoll erfordert, dass Dienstimplementierungen bestimmte APIs abschließen. Beispiel: Nichtbeachtung des Abbruchtokens in einem zuverlässigen Dienst führt dazu, dass SwapPrimary-Neukonfigurationen hängen bleiben.

Integritätsberichte von Systemkomponenten wie System.FM, System.RA, System.RAP können verwendet werden, um zu diagnostizieren, wo eine Neukonfiguration hängen geblieben ist. Unter [Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) werden diese Integritätsberichte beschrieben.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu den Service Fabric-Konzepten finden Sie in den folgenden Artikeln:

- [Reliable Services-Lebenszyklus – C#](service-fabric-reliable-services-lifecycle.md)
- [Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Replikate und Instanzen](service-fabric-concepts-replica-lifecycle.md)
