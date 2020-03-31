---
title: Veröffentlichen eines Angebots | Azure Marketplace
description: API zum Veröffentlichen des angegebenen Angebots.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 4163bf5727c327d559b81db42f99684aa0cc8d5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280523"
---
<a name="publish-an-offer"></a>Veröffentlichen eines Angebots
================

Startet den Veröffentlichungsprozess für das angegebene Angebot. Bei diesem Aufruf handelt es sich um einen Vorgang mit langer Ausführungsdauer.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-Parameter
--------------

|  **Name**      |    **Beschreibung**                               |  **Datentyp** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Herausgeber-ID, z.B. `contoso`      |   String       |
|  offerId       | Angebots-ID                                 |   String       |
|  api-version   | Aktuelle Version der API                        |   Date         |
|  |  |


<a name="header"></a>Header
------

|  **Name**        |    **Wert**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Authorization   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Beispiel für Hauptteil
------------

### <a name="request"></a>Anforderung

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Eigenschaften des Anforderungstexts

|  **Name**               |   **Beschreibung**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  notification-emails    | Kommagetrennte Liste der E-Mail-Adressen, die über den Fortschritt des Veröffentlichungsvorgangs benachrichtigt werden sollen. |
|  |  |


### <a name="response"></a>Antwort

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Antwortheader

|  **Name**             |    **Wert**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operation-Location    | URL, die abgefragt werden kann, um den aktuellen Status des Vorgangs zu ermitteln.    |
|  |  |


### <a name="response-status-codes"></a>Antwortstatuscodes

| **Code** |  **Beschreibung**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`: Die Anforderung wurde erfolgreich angenommen. Die Antwort enthält einen Speicherort, der zum Nachverfolgen des gestarteten Vorgangs verwendet werden kann. |
| 400   | `Bad/Malformed request`: Der Fehlerantworttext stellt möglicherweise weitere Informationen bereit.                                                               |
| 422   | `Un-processable entity`: Gibt an, dass die Validierung der zu veröffentlichenden Entität fehlgeschlagen ist.                                                        |
| 404   | `Not found`: Die vom Client angegebene Entität ist nicht vorhanden.                                                                              |
|  |  |
