---
title: "Azure Batch: Ereignis zum Abschluss der Größenänderung von Pools | Microsoft-Dokumentation"
description: "Referenz zum Batch-Ereignis zum Abschluss der Größenänderung von Pools."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
ms.openlocfilehash: 7072293d98526812cb42ce9c2f8e33bfcafaa149
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="pool-resize-complete-event"></a>Ereignis zum Abschluss der Größenänderung von Pools

 Dieses Ereignis wird ausgegeben, wenn die Größenänderung eines Pools abgeschlossen wurde oder fehlgeschlagen ist.

 Das folgende Beispiel zeigt den Text eines Ereignisses zum Abschluss der Größenänderung von Pools für einen Pool, dessen Größe erfolgreich erhöht wurde.

```
{
    "id": "p_1_0_01503750-252d-4e57-bd96-d6aa05601ad8",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 4,
    "targetDedicated": 4,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "result": "Success",
    "resizeError": "The operation succeeded"
}
```

|Element|Typ|Hinweise|
|-------------|----------|-----------|
|id|String|Die ID des Pools.|
|nodeDeallocationOption|String|Gibt ab, ob Knoten ggf. aus dem Pool entfernt werden müssen, wenn sich die Poolgröße verringert.<br /><br /> Mögliche Werte:<br /><br /> **requeue**: Beendet ausgeführte Tasks, die erneut in die Warteschlange gestellt werden. Die Tasks werden erneut ausgeführt, sobald der Auftrag aktiviert ist. Entfernen Sie Knoten, sobald Tasks abgeschlossen wurden.<br /><br /> **terminate**: Beendet derzeit ausgeführte Tasks. Die Tasks werden nicht erneut ausgeführt. Entfernen Sie Knoten, sobald Tasks abgeschlossen wurden.<br /><br /> **taskcompletion**: Lässt das Abschließen aktuell ausgeführter Tasks zu. Planen Sie beim Warten keine neuen Tasks. Entfernen Sie Knoten, sobald alle Aufgaben abgeschlossen sind.<br /><br /> **Retaineddata**: Lässt das Abschließen aktuell ausgeführter Tasks zu und wartet dann, bis die Aufbewahrungszeiträume aller Taskdaten abgelaufen sind. Planen Sie beim Warten keine neuen Tasks. Entfernen Sie Knoten, sobald die Aufbewahrungszeiträume aller Tasks abgelaufen sind.<br /><br /> Der Standardwert ist „requeue“.<br /><br /> Wenn die Poolgröße zunimmt, wird der Wert auf **invalid** festgelegt.|
|currentDedicated|Int32|Die Anzahl der Computeknoten, die dem Pool derzeit zugewiesen sind.|
|targetDedicated|Int32|Die Anzahl der Computeknoten, die für den Pool angefordert werden.|
|enableAutoScale|Bool|Gibt an, ob die Poolgröße mit der Zeit automatisch angepasst wird.|
|isAutoPool|Bool|Gibt an, ob der Pool über den AutoPool-Mechanismus eines Auftrags erstellt wurde.|
|startTime|DateTime|Die Startzeit der Größenänderung des Pools.|
|endTime|DateTime|Die Uhrzeit des Abschlusses der Größenänderung des Pools.|
|resultCode|String|Das Ergebnis der Größenänderung.|
|resultMessage|String|Der Größenänderungsfehler enthält die Details des Ergebnisses.<br /><br /> Wenn die Größenänderung erfolgreich abgeschlossen wurde, wird angegeben, dass der Vorgang erfolgreich war.|