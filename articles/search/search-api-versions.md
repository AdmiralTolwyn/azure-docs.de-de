---
title: API-Versionen von Azure Search | Microsoft Docs
description: Versionsrichtlinie für Azure Search-REST-APIs und die Clientbibliothek im .NET SDK.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: 7754242aa79a2ba7931a6d80a7a12a0858c6f260
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776395"
---
# <a name="api-versions-in-azure-search"></a>API-Versionen in Azure Search
Für Azure Search sind regelmäßig Featureupdates verfügbar. Manchmal, aber nicht immer, erfordern diese Updates die Veröffentlichung einer neuen Version unserer API, um die Abwärtskompatibilität zu gewährleisten. Die Veröffentlichung einer neuen Version ermöglicht Ihnen, zu steuern, wann und wie Sie Suchdienstupdates in Ihren Code integrieren.

In der Regel versuchen wir, neue Versionen nur im Bedarfsfall zu veröffentlichen, da es einen gewissen Aufwand erfordern kann, wenn Sie Ihren Code aktualisieren, um eine neue API-Version zu verwenden. Wir veröffentlichen nur dann eine neue Version, wenn wir einen Aspekt der API in einer Weise ändern müssen, die die Abwärtskompatibilität beeinträchtigt. Ursache hierfür können Fehlerbehebungen bei vorhandenen Funktionen sein, oder neue Funktionen, die vorhandene API-Oberflächenbereiche ändern.

Wir befolgen die gleiche Regel bei SDK-Updates. Das Azure Search-SDK folgt den Regeln der [semantischen Versionierung](http://semver.org/), d.h., dass seine Version aus drei Teilen besteht: Haupt-, Neben- und Buildnummer (z.B. 1.1.0). Wir veröffentlichen nur dann eine neue Hauptversion des SDK, wenn Änderungen die Abwärtskompatibilität beeinträchtigen. Bei Funktionsupdates ohne Beeinträchtigung der Abwärtskompatibilität setzen wir die Nebenversion herauf, und für Programmfehlerbehebungen setzen wir nur die Buildversion herauf.

> [!NOTE]
> Ihre Azure Search-Dienstinstanz unterstützt mehrere REST-API-Versionen, einschließlich der neuesten. Sie können auch ältere Versionen weiterhin verwenden, aber es wird empfohlen, den Code zur neuesten Version zu migrieren. Wenn Sie die REST-API verwenden, müssen Sie die API-Version bei jeder Anforderung über den api-version-Parameter angeben. Wenn Sie das .NET SDK verwenden, bestimmt die Version des von Ihnen verwendeten SDK die Version der REST-API. Wenn Sie ein älteres SDK verwenden, können Sie diesen Code weiterhin ohne Änderungen ausführen, selbst wenn der Dienst aktualisiert wird, um eine neuere Version der API zu unterstützen.

## <a name="snapshot-of-current-versions"></a>Momentaufnahme aktueller Versionen
Unten sehen Sie eine Momentaufnahme der aktuellen Versionen aller Programmierschnittstellen zu Azure Search.

| Schnittstellen | Letzte Hauptversion | Status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |5.0 |Allgemein verfügbar, veröffentlicht April 2018 |
| [.NET SDK-Vorschau](https://aka.ms/search-sdk-preview) |4.0.1-preview |Vorschau, veröffentlicht im Mai 2017 |
| [Dienst-REST-API](https://docs.microsoft.com/rest/api/searchservice/) |2016-09-01 |Allgemein verfügbar |
| [Dienst-REST-API 2016-09-01-Preview](search-api-2016-09-01-preview.md) |2016-09-01-Preview |Vorschau |
| [Dienst-REST-API 2017-11-11-Preview](search-api-2017-11-11-preview.md) |Vorschauversion vom 11.11.2017 |Vorschau |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |2.0 |Allgemein verfügbar |
| [Verwaltungs-REST-API](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Allgemein verfügbar |

Für die REST-APIs muss bei jedem Aufruf die `api-version` einbezogen werden. Dies erleichtert das Ansprechen einer bestimmten Version, z. B. einer Vorschau-API. Das folgende Beispiel veranschaulicht, wie der Parameter `api-version` angegeben wird:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2017-11-11

> [!NOTE]
> Obwohl jede Anforderung über eine `api-version` verfügt, sollten Sie für alle API-Anforderungen die gleiche Version verwenden. Dies gilt insbesondere dann, wenn neue API-Versionen Attribute oder Vorgänge einführen, die von früheren Versionen nicht erkannt werden. Das Mischen von API-Versionen kann unerwartete Konsequenzen haben und sollte vermieden werden.
>
> Die Dienst-REST-API und Verwaltungs-REST-API werden voneinander unabhängig versioniert. Jede Ähnlichkeit von Versionsnummern ist zufällig.

Allgemein verfügbare APIs (oder GA) können in der Produktion verwendet werden und unterliegen den Vereinbarungen zum Servicelevel von Azure. Vorschauversionen weisen experimentelle Funktionen auf, die nicht immer zu GA-Versionen migriert werden. **Wir raten dringend davon ab, Vorschau-APIs in Produktionsanwendungen einzusetzen.**

## <a name="about-preview-and-generally-available-versions"></a>Informationen zu Vorschau- und allgemein verfügbaren Versionen
Azure Search bietet immer zuerst Vorabversionen experimenteller Funktionen über die REST-API, dann über Vorabversionen von .NET SDK.

Previewfunktionen werden nicht unbedingt zu einer GA-Version migriert. Während bei Funktionen in einer GA-Version vorausgesetzt wird, dass sie stabil sind und es unwahrscheinlich ist, dass sie geändert werden – mit Ausnahme von kleinen abwärtskompatiblen Korrekturen und Verbesserungen – stehen Previewfunktionen für Tests und Experimente zur Verfügung, mit dem Ziel, Feedback zu Entwurf und Implementierung der Funktionen zu sammeln.

Aber weil Änderungen bei Previewfunktionen vorbehalten sind, raten wir davon ab, Produktionscode zu schreiben, der von Vorschauversionen abhängig ist. Wenn Sie eine ältere Vorschauversion verwenden, sollten Sie zur allgemein verfügbaren Version (GA) migrieren.

Für das .NET SDK: Anleitung zur Codemigration finden Sie unter [Upgrade auf Version 1.1 des Azure Search .NET SDK](search-dotnet-sdk-migration.md).

Allgemeine Verfügbarkeit bedeutet, dass Azure Search jetzt der Vereinbarung zum Servicelevel (SLA) unterliegt. Die SLA finden Sie unter [SLA für Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
