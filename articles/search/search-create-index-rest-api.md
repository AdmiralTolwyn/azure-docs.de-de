---
title: Erstellen eines Index im Code mithilfe der REST-API – Azure Search
description: Erstellen Sie einen durchsuchbaren Volltextindex im Code mithilfe von HTTP-Anforderungen und der Azure Search REST-API.
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: f47aead95d7135e2528fea11c116effa93df4c4c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309211"
---
# <a name="create-an-azure-search-index-using-the-rest-api"></a>Erstellen eines Azure Search-Indexes mit der REST-API
> [!div class="op_single_selector"]
>
> * [Übersicht](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
>
>

In diesem Artikel lernen Sie, wie Sie einen Azure Search- [Index](https://docs.microsoft.com/rest/api/searchservice/Create-Index) mithilfe der Azure Search REST-API erstellen.

Bevor Sie dieser Anleitung folgen und einen Index erstellen, müssen Sie einen [Azure Search-Dienst erstellen](search-create-service-portal.md).

Zum Erstellen des Azure Search-Indexes mithilfe der REST-API können Sie eine einzelne HTTP POST-Anforderung an den URL-Endpunkt Ihres Azure Search-Diensts ausgeben. Ihre Indexdefinition ist im Anforderungstext als richtig formatierter JSON-Inhalt enthalten.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Identifizieren des Admin-API-Schlüssels Ihres Azure Search-Diensts
Nachdem Sie einen Azure Search-Dienst bereitgestellt haben, können Sie HTTP-Anforderungen für den URL-Endpunkt Ihres Diensts mithilfe der REST-API ausgeben. *Alle* API-Anforderungen müssen den API-Schlüssel enthalten, der für den bereitgestellten Suchdienst erstellt wurde. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, um die API-Schlüssel für Ihren Dienst zu ermitteln.
2. Wechseln Sie zum Blatt Ihres Azure Search-Diensts.
3. Klicken Sie auf das Schlüsselsymbol.

Der Dienst enthält *Admin-Schlüssel* und *Abfrageschlüssel*.

* Die primären und sekundären *Admin-Schlüssel* gewähren Ihnen Vollzugriff auf alle Vorgänge. Dazu zählen die Dienstverwaltung und das Erstellen und Löschen von Indizes, Indexern und Datenquellen. Ihnen stehen zwei Schlüssel zur Verfügung, damit Sie den sekundären Schlüssel weiterhin nutzen können, wenn Sie den primären Schlüssel neu generieren möchten, und umgekehrt.
* Die *Abfrageschlüssel* gewähren Ihnen Lesezugriff auf Indizes und Dokumente. Diese werden in der Regel auf Clientanwendungen verteilt, die Suchanfragen ausgeben.

Verwenden Sie zum Erstellen eines Indexes entweder den primären oder den sekundären Admin-Schlüssel.

## <a name="define-your-azure-search-index-using-well-formed-json"></a>Definieren des Azure Search-Indexes mithilfe richtig formatierter JSON
Eine einzelne HTTP POST-Anforderung an Ihren Dienst erstellt den Index. Der Hauptteil der HTTP POST-Anforderung enthält ein einzelnes JSON-Objekt, das den Azure Search-Index definiert.

1. Die erste Eigenschaft des JSON-Objekts ist der Name des Indexes.
2. Die zweite Eigenschaft des JSON-Objekts ist ein JSON-Array mit dem Namen `fields` , das ein separates JSON-Objekt für jedes Feld im Index enthält. Diese JSON-Objekte enthalten mehrere Name-Wert-Paare für jedes Feldattribut, einschließlich Name, Typ usw.

Berücksichtigen Sie beim Gestalten des Indexes die Benutzerfreundlichkeit und die geschäftlichen Anforderungen, da jedem Feld die [richtigen Attribute](https://docs.microsoft.com/rest/api/searchservice/Create-Index)zugewiesen sein müssen. Diese Attribute steuern, welche Suchfunktionen (Filtern, Facettierung, Sortieren der Volltextsuche usw.) für welche Felder gelten. Für jedes nicht festgelegte Attribut ist die entsprechende Suchfunktion standardmäßig aktiviert, sofern Sie sie nicht explizit deaktivieren.

In unserem Beispiel hat der Index den Namen „hotels“. Die Felder wurden wie folgt definiert:

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Wir haben die Indexattribute für jedes Feld ausgehend davon ausgewählt, wie sie unseres Erachtens in einer Anwendung verwendet werden. `hotelId` ist beispielsweise ein eindeutiger Schlüssel, den die Benutzer bei der Hotelsuche wahrscheinlich nicht kennen. Daher haben wir die Volltextsuche für dieses Feld deaktiviert, indem wir für `searchable` den Wert `false` festgelegt haben, was im Index Platz spart.

In Ihrem Index des Typs `Edm.String` muss genau ein Feld als „key“ bestimmt sein.

Diese Indexdefinition verwendet für das Feld `description_fr` eine Sprachanalyse, da es für Text in französischer Sprache vorgesehen ist. Weitere Informationen zu Sprachanalysen finden Sie im [Thema zur Sprachunterstützung](https://docs.microsoft.com/rest/api/searchservice/Language-support) sowie im entsprechenden [Blogbeitrag](https://azure.microsoft.com/blog/language-support-in-azure-search/).

## <a name="issue-the-http-request"></a>Stellen der HTTP-Anforderung
1. Stellen Sie eine HTTP POST-Anforderung an die Endpunkt-URL des Azure Search-Diensts, indem Sie Ihre Indexdefinition als Anforderungstext verwenden. Verwenden Sie in der URL Ihren Dienstnamen als Hostnamen, und geben Sie die richtige `api-version` als Abfragezeichenfolgeparameter ein (zum Zeitpunkt der Veröffentlichung dieses Dokuments ist `2017-11-11` die aktuelle API-Version).
2. Legen Sie im Anforderungsheader `Content-Type` als `application/json` fest. Sie müssen außerdem den Admin-Schlüssel des Diensts angeben, den Sie in Schritt I im Header `api-key` identifiziert haben.

Zum Übermitteln der folgenden Anforderung müssen Sie Ihren Dienstnamen und API-Schlüssel angeben:

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [api-key]


Bei einer erfolgreichen Anforderung erscheint der Statuscode 201 (erstellt). Weitere Informationen zum Erstellen eines Index über die REST-API finden Sie in der [API-Referenz](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Weitere Informationen zu anderen HTTP-Statuscodes, die bei Fehlern ausgegeben werden, finden Sie unter [HTTP-Statuscodes (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

Wenn Sie einen Index nicht mehr benötigen und ihn löschen möchten, stellen Sie einfach eine HTTP DELETE-Anforderung. Der Index „hotels“ wird beispielsweise wie folgt gelöscht:

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11
    api-key: [api-key]


## <a name="next-steps"></a>Nächste Schritte
Nach dem Erstellen eines Azure Search-Indexes können Sie [Ihre Inhalte in den Index hochladen](search-what-is-data-import.md) und mit dem Durchsuchen der Daten beginnen.
