---
title: include file
description: include file
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4d0a61bf7f4ee9e441a49e21ce6535dc3bff0edc
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608195"
---
Gibt an, wie viele Funktionsaufrufe aggregiert werden, wenn [Metriken für Application Insights berechnet werden](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Eigenschaft |Standard  | BESCHREIBUNG |
|---------|---------|---------| 
|batchSize|1000|Maximale Anzahl der zu aggregierenden Anforderungen.| 
|flushTimeout|00:00:30|Maximal zu aggregierender Zeitraum.| 

Funktionsaufrufe werden aggregiert, wenn der erste der beiden Grenzwerte erreicht wird.