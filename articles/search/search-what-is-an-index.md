---
title: Erstellen eines Azure Search-Indexes | Microsoft Azure | Gehosteter Cloudsuchdienst
description: Was ist ein Azure Search-Index, und wie wird er verwendet?
services: search
documentationcenter: 
author: ashmaka
ms.assetid: a395e166-bf2e-4fca-8bfc-116a46c5f7b1
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/08/2016
ms.author: ashmaka
ms.openlocfilehash: 7fc45273c0f71c727b7087949cc63bbb4111f866
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2017
---
# <a name="create-an-azure-search-index"></a>Erstellen eines Azure Search-Index
> [!div class="op_single_selector"]
> * [Übersicht](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

## <a name="what-is-an-index"></a>Was ist ein Index?
Ein *Index* ist ein dauerhafter Speicher von *Dokumenten* und anderen Konstrukten, die von einem Azure Search-Dienst verwendet werden. Ein Dokument ist eine einzelne Einheit mit durchsuchbaren Daten im Index. Ein Internetversandhändler hat beispielsweise ein Dokument für jeden angebotenen Artikel, eine Nachrichtenagentur hat ein Dokument pro Zeitungsartikel usw. So lassen sich diese Konzepte vertrauteren Entsprechungen in der Datenbank zuordnen: Ein *Index* entspricht etwa einer *Tabelle*, und *Dokumente* entsprechen ungefähr den *Zeilen* einer Tabelle.

Wenn Sie Dokumente hinzufügen/hochladen und Suchabfragen an Azure Search übermitteln, übermitteln Sie Ihre Anforderungen an einen bestimmten Index in Ihrem Suchdienst.

## <a name="field-types-and-attributes-in-an-azure-search-index"></a>Feldtypen und Attribute in einem Azure Search-Index
Wenn Sie Ihr Schema definieren, müssen Sie den Namen, den Typ und die Attribute jedes Felds in Ihrem Index festlegen. Der Feldtyp klassifiziert die Daten, die in dem Feld gespeichert sind. Attribute werden für einzelne Felder festlegen, um anzugeben, wie das Feld verwendet wird. Die folgende Tabelle listet die Typen und Attribute auf, die Sie angeben können.

### <a name="field-types"></a>Feldtypen
| Typ | Beschreibung |
| --- | --- |
| *Edm.String* |Text, der optional für die Volltextsuche (Worttrennung, Wortstammerkennung usw.) mit einem Token versehen werden kann |
| *Collection(Edm.String)* |Eine Liste von Zeichenfolgen, die für die Volltextsuche mit einem Token versehen werden können. Für die Anzahl der Elemente in einer Sammlung gibt es keine Obergrenze, allerdings gilt die Obergrenze für die Größe der Nutzlast von 16 MB auch für Sammlungen. |
| *Edm.Boolean* |Enthält TRUE/FALSE-Werte |
| *Edm.Int32* |32-Bit-Ganzzahlwerte |
| *Edm.Int64* |64-Bit-Ganzzahlwerte |
| *Edm.Double* |Numerische Daten mit doppelter Genauigkeit |
| *Edm.DateTimeOffset* |Datums-/Uhrzeitwerte im OData V4-Format (z. B. `yyyy-MM-ddTHH:mm:ss.fffZ` oder `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`) |
| *Edm.GeographyPoint* |Ein Punkt, der einen weltweiten geografischen Standort darstellt |

Ausführlichere Informationen zu den von Azure Search unterstützten Datentypen finden Sie [hier](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types).

### <a name="field-attributes"></a>Feldattribute
| Attribut | Beschreibung |
| --- | --- |
| *Schlüssel* |Eine Zeichenfolge, die die eindeutige ID der einzelnen Dokumente darstellt und für die Dokumentsuche verwendet wird. Jeder Index muss über einen Schlüssel verfügen. Als Schlüssel kann immer nur ein einzelnes Feld fungieren, und sein Typ muss auf „Edm.String“ festgelegt sein. |
| *Abrufbar* |Gibt an, ob ein Feld in einem Suchergebnis zurückgegeben werden kann. |
| *Filterbar* |Ermöglicht die Verwendung des Felds in Filterabfragen. |
| *Sortierbar* |Ermöglicht einer Abfrage das Sortieren von Suchergebnissen mithilfe dieses Felds. |
| *Facettierbar* |Ermöglicht die Verwendung eines Felds in einer [Facettennavigationsstruktur](search-faceted-navigation.md) für benutzerdefiniertes Filtern. Repetitive Werte, mit denen sich mehrere Dokumente zu einer Gruppe zusammenfassen lassen (etwa mehrere Dokumente der gleichen Marken- oder Dienstleistungskategorie), sind in der Regel am besten für die Verwendung als Facetten geeignet. |
| *Durchsuchbar* |Markiert das Feld als in die Volltextsuche einbeziehbar. |

Ausführlichere Informationen zu den Indexattributen von Azure Search finden Sie [hier](https://docs.microsoft.com/rest/api/searchservice/Create-Index).

## <a name="guidance-for-defining-an-index-schema"></a>Anleitung zum Definieren eines Indexschemas
Nehmen Sie sich beim Entwerfen Ihres Indexes in der Planungsphase genügend Zeit, um jede Entscheidung sorgfältig zu durchdenken. Berücksichtigen Sie beim Gestalten des Indexes die Benutzerfreundlichkeit und die geschäftlichen Anforderungen, da jedem Feld die [richtigen Attribute](https://docs.microsoft.com/rest/api/searchservice/Create-Index)zugewiesen sein müssen. Das Ändern eines Indexes nach seiner Bereitstellung erfordert dessen Neuerstellung und das erneute Laden der Daten.

Wenn sich die Datenspeicheranforderungen mit der Zeit ändern, können Sie die Kapazität erhöhen oder verringern, indem Sie Partitionen hinzufügen oder entfernen. Weitere Informationen finden Sie unter [Verwalten Ihres Suchdiensts in Azure](search-manage.md) oder [Grenzwerte für Dienste](search-limits-quotas-capacity.md).

