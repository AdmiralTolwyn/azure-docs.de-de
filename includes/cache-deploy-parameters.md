---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/21/2018
ms.author: wesmc
ms.openlocfilehash: dd9700c9472e07daf294eca12b766e3dc4832955
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111942"
---
### <a name="cacheskuname"></a>cacheSKUName
Der Tarif der neuen Azure Cache for Redis-Instanz.

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },

Die Vorlage definiert die Werte, die für diesen Parameter zulässig sind (Basic oder Standard), und weist einen Standardwert (Basic) zu, wenn kein Wert angegeben wird. "Basic" bietet einen einzelnen Knoten mit mehreren Größen bis zu 53 GB.
"Standard" bietet zwei Knoten, Primär/Replikat, mit mehreren Größen bis zu 53 GB und 99,9% SLA.

### <a name="cacheskufamily"></a>cacheSKUFamily
Die SKU-Familie.

    "cacheSKUFamily": {
      "type": "string",
      "allowedValues": [
        "C"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },


### <a name="cacheskucapacity"></a>cacheSKUCapacity
Die Größe der neuen Azure Cache for Redis-Instanz. 

    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }


Die Vorlage definiert die Werte, die für diesen Parameter zulässig sind (0, 1, 2, 3, 4, 5 oder 6), und weist einen Standardwert (0) zu, wenn kein Wert angegeben wird. Diese Zahlen entsprechen den folgenden Cachegrößen: 0 = 250 MB, 1 = 1 GB, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB

