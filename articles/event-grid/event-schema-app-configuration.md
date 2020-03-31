---
title: Azure Event Grid-Ereignisschema für Azure App Configuration
description: Beschreibt die Eigenschaften, die mit Azure Event Grid für Azure App Configuration-Ereignisse bereitgestellt werden.
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66735897"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Azure Event Grid-Ereignisschema für Azure App Configuration

In diesem Artikel werden die Eigenschaften und das Schema für Azure App Configuration-Ereignisse beschrieben. Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md).

Eine Liste von Beispielskripts und Tutorials finden Sie in den Informationen zur [Ereignisquelle für Azure App Configuration](event-sources.md#app-configuration).

## <a name="available-event-types"></a>Verfügbare Ereignistypen

Azure App Configuration gibt die folgenden Ereignistypen aus:

| Ereignistyp | BESCHREIBUNG |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Wird ausgelöst, wenn ein Schlüssel-Wert-Paar erstellt oder ersetzt wird. |
| Microsoft.AppConfiguration.KeyValueDeleted | Wird ausgelöst, wenn ein Schlüssel-Wert-Paar gelöscht wird. |

## <a name="example-event"></a>Beispielereignis

Das folgende Beispiel zeigt das Schema eines Ereignisses aufgrund eines geänderten Schlüssel-Wert-Paars: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Das Schema für ein Ereignis aufgrund eines gelöschten Schlüssel-Wert-Paars sieht ähnlich aus: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
 
## <a name="event-properties"></a>Ereigniseigenschaften

Ein Ereignis weist die folgenden Daten auf oberster Ebene aus:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| topic | string | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| subject | string | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| eventType | string | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| eventTime | string | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| id | string | Eindeutiger Bezeichner für das Ereignis. |
| data | Objekt (object) | App Configuration-Ereignisdaten. |
| dataVersion | string | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| metadataVersion | string | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| Schlüssel | string | Der Schlüssel des Schlüssel-Wert-Paars, das geändert oder gelöscht wurde. |
| label | string | Die Bezeichnung (sofern vorhanden) des Schlüssel-Wert-Paars, das geändert oder gelöscht wurde. |
| etag | string | Für `KeyValueModified`: Das ETag des neuen Schlüssel-Wert-Paars. Für `KeyValueDeleted`: Das ETag des gelöschten Schlüssel-Wert-Paars. |
 
## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).
* Eine Einführung zum Arbeiten mit Azure App Configuration-Ereignissen finden Sie unter [Weiterleiten von Azure App Configuration-Ereignissen – Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 