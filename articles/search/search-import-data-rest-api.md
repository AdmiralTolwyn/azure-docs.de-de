---
title: Hochladen von Daten in den Code mit der Suchdienst-REST-API – Azure Search
description: Erfahren Sie, wie Sie mit HTTP-Anforderungen und der REST-API Daten in einen durchsuchbaren Volltextindex in Azure Search hochladen.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 04/20/2018
ms.custom: seodec2018
ms.openlocfilehash: b3044ec3fb21e77c5174ebd5a6b2dabd2282240f
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312849"
---
# <a name="upload-data-to-azure-search-using-the-rest-api"></a>Hochladen von Daten in Azure Search über die REST-API
> [!div class="op_single_selector"]
>
> * [Übersicht](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
>
>

In diesem Artikel erfahren Sie, wie Sie die [Azure Search REST-API](https://docs.microsoft.com/rest/api/searchservice/) zum Importieren von Daten in einen Azure Search-Index verwenden.

[Erstellen Sie einen Azure Search-Index](search-what-is-an-index.md), bevor Sie mit dieser exemplarischen Vorgehensweise beginnen.

Um Dokumente mit der REST-API mithilfe von Push in Ihren Index zu verschieben, geben Sie eine HTTP POST-Anforderung an das URL-Endpunkt Ihres Indexes aus. Der Hauptteil der HTTP-Anforderung ist ein JSON-Objekt, das die Dokumente enthält, die hinzugefügt, geändert oder gelöscht werden sollen.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Identifizieren des Admin-API-Schlüssels Ihres Azure Search-Diensts
Beim Ausgeben von HTTP-Anforderungen in Ihrem Dienst mithilfe der REST-API muss *jede* API-Anforderung den API-Schlüssel enthalten, der für den bereitgestellten Suchdienst erstellt wurde. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

1. Sie können sich am [Azure-Portal](https://portal.azure.com/) anmelden, um die API-Schlüssel für Ihren Dienst zu ermitteln.
2. Wechseln Sie zum Blatt Ihres Azure Search-Diensts.
3. Klicken Sie auf das Schlüsselsymbol.

Der Dienst enthält *Admin-Schlüssel* und *Abfrageschlüssel*.

* Die primären und sekundären *Admin-Schlüssel* gewähren Ihnen Vollzugriff auf alle Vorgänge. Dazu zählen die Dienstverwaltung und das Erstellen und Löschen von Indizes, Indexern und Datenquellen. Ihnen stehen zwei Schlüssel zur Verfügung, damit Sie den sekundären Schlüssel weiterhin nutzen können, wenn Sie den primären Schlüssel neu generieren möchten, und umgekehrt.
* Die *Abfrageschlüssel* gewähren Ihnen Lesezugriff auf Indizes und Dokumente. Diese werden in der Regel auf Clientanwendungen verteilt, die Suchanfragen ausgeben.

Verwenden Sie zum Importieren von Daten in einen Index entweder den primären oder den sekundären Admin-Schlüssel.

## <a name="decide-which-indexing-action-to-use"></a>Entscheiden Sie, welche Indizierungsaktion verwendet werden soll.
Wenn Sie die REST-API verwenden, werden HTTP POST-Anforderungen mit JSON-Anforderungstexten an die Endpunkt-URL Ihres Azure Search-Indexes ausgegeben. Das JSON-Objekt im HTTP-Anforderungstext enthält ein einzelnes JSON-Array namens „value“, das JSON-Objekte enthält. Diese stellen Dokumente dar, die Sie zum Index hinzufügen, aktualisieren oder löschen möchten.

Jedes JSON-Objekt im Array „value“ stellt ein zu indizierendes Dokument dar. Jedes der Objekte enthält den Schlüssel des Dokuments und bestimmt die gewünschte Indizierungsaktion (Hochladen, Zusammenführen, Löschen usw.). Je nachdem, welche der folgenden Aktionen Sie wählen, müssen für jedes Dokument nur bestimmte Felder eingefügt werden:

| @search.action | BESCHREIBUNG | Erforderliche Felder für jedes Dokument | Notizen |
| --- | --- | --- | --- |
| `upload` |Eine `upload` -Aktion entspricht „upsert“, wobei neue Dokumente eingefügt und bestehende Dokumente aktualisiert/ersetzt werden. |Schlüssel und alle anderen zu definierenden Felder |Wenn ein bestehendes Dokument aktualisiert/ersetzt wird, werden alle in der Anforderung nicht festgelegten Felder auf `null`festgelegt. Dies tritt auch auf, wenn das Feld zuvor auf einen Wert festgelegt wurde, der nicht Null ist. |
| `merge` |Aktualisiert ein bestehendes Dokument mit den angegebenen Feldern. Wenn das Dokument im Index nicht vorhanden ist, schlägt die Zusammenführung fehl. |Schlüssel und alle anderen zu definierenden Felder |Jedes Feld, das Sie in einer Zusammenführung angeben, ersetzt das vorhandene Feld im Dokument. Dies beinhaltet auch Felder vom Typ `Collection(Edm.String)`. Wenn das Dokument beispielsweise das Feld `tags` mit dem Wert `["budget"]` enthält und Sie eine Zusammenführung mit dem Wert `["economy", "pool"]` für `tags` durchführen, hat das Feld `tags` am Ende den Wert `["economy", "pool"]`. Der Wert lautet nicht `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Diese Aktion verhält sich wie `merge`, wenn im Index bereits ein Dokument mit dem entsprechenden Schlüssel vorhanden ist. Wenn das Dokument nicht vorhanden ist, verhält es sich wie `upload` mit einem neuen Dokument. |Schlüssel und alle anderen zu definierenden Felder |- |
| `delete` |Hiermit wird das angegebene Dokument aus dem Index gelöscht. |Nur Schlüssel |Mit Ausnahme des Schlüsselfelds werden alle angegebenen Felder ignoriert. Wenn Sie ein einzelnes Feld aus einem Dokument entfernen möchten, verwenden Sie stattdessen `merge` , und setzen Sie das Feld ausdrücklich auf Null. |

## <a name="construct-your-http-request-and-request-body"></a>Erstellen Sie die HTTP-Anforderung und den Anforderungstext
Da Sie nun die erforderlichen Feldwerte für Ihre Indexaktionen gesammelt haben, können Sie die HTTP-Anforderung und den JSON-Anforderungstext für den Datenimport erstellen.

#### <a name="request-and-request-headers"></a>Anforderung und Anforderungsheader
In der URL müssen Sie Ihren Dienstnamen, den Indexnamen (in diesem Fall „hotels“) sowie die entsprechende API-Version (die aktuelle Version der API zum Zeitpunkt der Veröffentlichung dieses Dokuments ist `2017-11-11` ) bereitstellen. Sie müssen die Anforderungsheader `Content-Type` und `api-key` festlegen. Verwenden Sie für letzteres einen der Admin-Schlüssel Ihres Diensts.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>Anforderungstext
```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

In diesem Fall verwenden wir `upload`, `mergeOrUpload` und `delete` als Suchaktionen.

Wir gehen davon aus, dass der Index in diesem Beispiel („hotels“) bereits mit einigen Dokumenten gefüllt ist. Beachten Sie, dass bei `mergeOrUpload` nicht alle möglichen Dokumentfelder festgelegt werden mussten und dass bei `delete` nur der Dokumentschlüssel (`hotelId`) definiert wurde.

Beachten Sie außerdem, dass nur bis zu 1000 Dokumente (oder 16 MB) in einer einzigen Indizierungsanforderung enthalten sein können.

## <a name="understand-your-http-response-code"></a>Erläuterungen zum HTTP-Antwortcode
#### <a name="200"></a>200
Nach der Übermittlung einer erfolgreichen Indizierungsanforderung erhalten Sie eine HTTP-Antwort mit dem Statuscode `200 OK`. Der JSON-Text der HTTP-Antwort lautet wie folgt:

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
Der Statuscode `207` wird zurückgegeben, wenn mindestens ein Element nicht erfolgreich indiziert wurde. Der JSON-Text der HTTP-Antwort enthält Informationen zu den fehlgeschlagenen Dokumenten.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [!NOTE]
> Dies bedeutet häufig, dass die Auslastung Ihres Suchdiensts einen Punkt erreicht, an dem Indizierungsanforderungen damit beginnen, `503`-Antworten zurückzugeben. In diesem Fall empfehlen wir ein Backoff des Clientcodes. Versuchen Sie es daraufhin nach einer Weile erneut. Dadurch hat das System ausreichend Zeit für eine Wiederherstellung, und die Wahrscheinlichkeit, dass spätere Anforderungen gelingen, steigt. Wenn Sie Ihre Anforderungen schnell wiederholen, dauert das Problem nur an.
>
>

#### <a name="429"></a>429
Der Statuscode `429` wird zurückgegeben, wenn Sie Ihr Kontingent hinsichtlich der Anzahl der Dokumente pro Index überschritten haben.

#### <a name="503"></a>503
Der Statuscode `503` wird zurückgegeben, wenn keines der Elemente in der Anforderung erfolgreich indiziert wurde. Dieser Fehler bedeutet, dass die Auslastung des Systems sehr hoch ist und Ihre Anforderungen aktuell nicht verarbeitet werden können.

> [!NOTE]
> In diesem Fall empfehlen wir ein Backoff des Clientcodes. Versuchen Sie es daraufhin nach einer Weile erneut. Dadurch hat das System ausreichend Zeit für eine Wiederherstellung, und die Wahrscheinlichkeit, dass spätere Anforderungen gelingen, steigt. Wenn Sie Ihre Anforderungen schnell wiederholen, dauert das Problem nur an.
>
>

Weitere Informationen zu Dokumentaktionen und Antworten bei Erfolg/Fehler finden Sie unter [Hinzufügen, Aktualisieren und Löschen von Dokumenten](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents). Weitere Informationen zu anderen HTTP-Statuscodes, die bei Fehlern ausgegeben werden, finden Sie unter [HTTP-Statuscodes (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

## <a name="next-steps"></a>Nächste Schritte
Nach dem Auffüllen des Azure Search-Indexes können Sie mit Abfragen für die Suche nach Dokumenten beginnen. Ausführliche Informationen finden Sie unter [Abfragen in Azure Search](search-query-overview.md) .
