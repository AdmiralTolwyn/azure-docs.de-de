---
title: Indexdefinition und Konzepte – Azure Search
description: Einführung in Indexbegriffe und Konzepte in Azure Search, einschließlich der physischen Struktur eines invertierten Index.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/08/2017
ms.custom: seodec2018
ms.openlocfilehash: 40291b105eb39b44da0b0697f5808d819291e457
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630212"
---
# <a name="indexes-in-azure-search"></a>Indizes in Azure Search
> [!div class="op_single_selector"]
> * [Übersicht](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

In Azure Search ist ein *Index* ein dauerhafter Speicher mit *Dokumenten* und anderen Konstrukten, die von einem Azure Search-Dienst verwendet werden. Ein Dokument ist eine einzelne Einheit mit durchsuchbaren Daten im Index. Ein Internetversandhändler verfügt beispielsweise über ein Dokument für jeden angebotenen Artikel, eine Nachrichtenagentur über ein Dokument pro Zeitungsartikel usw. So lassen sich diese Konzepte vertrauteren Entsprechungen in der Datenbank zuordnen: Ein *Index* entspricht etwa einer *Tabelle*, und *Dokumente* entsprechen ungefähr den *Zeilen* einer Tabelle.

Wenn Sie Dokumente hinzufügen/hochladen und Suchabfragen an Azure Search übermitteln, übermitteln Sie Ihre Anforderungen an einen bestimmten Index in Ihrem Suchdienst.

## <a name="field-types-and-attributes-in-an-azure-search-index"></a>Feldtypen und Attribute in einem Azure Search-Index
Wenn Sie Ihr Schema definieren, müssen Sie den Namen, den Typ und die Attribute jedes Felds in Ihrem Index festlegen. Der Feldtyp klassifiziert die Daten, die in dem Feld gespeichert sind. Attribute werden für einzelne Felder festlegen, um anzugeben, wie das Feld verwendet wird. Die folgende Tabelle listet die Typen und Attribute auf, die Sie angeben können.

### <a name="field-types"></a>Feldtypen
| Typ | BESCHREIBUNG |
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
| Attribut | BESCHREIBUNG |
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

