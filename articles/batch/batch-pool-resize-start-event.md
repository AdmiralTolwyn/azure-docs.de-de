---
title: 'Azure Batch: Ereignis zum Starten der Größenänderung von Pools'
description: Referenz zum Batch-Ereignis zum Starten der Größenänderung von Pools. Das Beispiel zeigt den Text eines Ereignisses zum Starten der Größenänderung von Pools für einen Pool, der manuell von 0 auf 2 Knoten vergrößert wurde.
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: b4aa503c5dfbe00a77216277bdaf7e4c0dc3b1bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115872"
---
# <a name="pool-resize-start-event"></a>Ereignis zum Starten der Größenänderung von Pools: Azure

 Dieses Ereignis wird ausgegeben, wenn die Größenänderung eines Pools gestartet wurde. Da das Ändern der Größe des Pools ein asynchrones Ereignis ist, können Sie damit rechnen, dass ein Ereignis zum Abschluss der Größenänderung von Pools ausgegeben wird, sobald der Größenänderungsvorgang abgeschlossen ist.

 Das folgende Beispiel zeigt den Text eines Ereignisses zum Starten der Größenänderung von Pools für einen Pool, der manuell von 0 auf 2 Knoten vergrößert wurde.

```
{
    "id": "myPool1",
    "nodeDeallocationOption": "Invalid",
    "currentDedicatedNodes": 0,
    "targetDedicatedNodes": 2,
    "currentLowPriorityNodes": 0,
    "targetLowPriorityNodes": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Element|type|Notizen|
|-------------|----------|-----------|
|`id`|String|Die ID des Pools.|
|`nodeDeallocationOption`|String|Gibt ab, ob Knoten ggf. aus dem Pool entfernt werden müssen, wenn sich die Poolgröße verringert.<br /><br /> Mögliche Werte:<br /><br /> **requeue**: Beendet ausgeführte Tasks, die erneut in die Warteschlange gestellt werden. Die Tasks werden erneut ausgeführt, sobald der Auftrag aktiviert ist. Entfernen Sie Knoten, sobald Tasks abgeschlossen wurden.<br /><br /> **terminate**: Beendet derzeit ausgeführte Tasks. Die Tasks werden nicht erneut ausgeführt. Entfernen Sie Knoten, sobald Tasks abgeschlossen wurden.<br /><br /> **taskcompletion**: Lässt das Abschließen aktuell ausgeführter Tasks zu. Planen Sie beim Warten keine neuen Tasks. Entfernen Sie Knoten, sobald alle Aufgaben abgeschlossen sind.<br /><br /> **Retaineddata**: Lässt das Abschließen aktuell ausgeführter Tasks zu und wartet dann, bis die Aufbewahrungszeiträume aller Taskdaten abgelaufen sind. Planen Sie beim Warten keine neuen Tasks. Entfernen Sie Knoten, sobald die Aufbewahrungszeiträume aller Tasks abgelaufen sind.<br /><br /> Der Standardwert ist „requeue“.<br /><br /> Wenn die Poolgröße zunimmt, wird der Wert auf **invalid** festgelegt.|
|`currentDedicatedNodes`|Int32|Die Anzahl der Computeknoten, die dem Pool derzeit zugewiesen sind.|
|`targetDedicatedNodes`|Int32|Die Anzahl der Computeknoten, die für den Pool angefordert werden.|
|`currentLowPriorityNodes`|Int32|Die Anzahl der Computeknoten, die dem Pool derzeit zugewiesen sind.|
|`targetLowPriorityNodes`|Int32|Die Anzahl der Computeknoten, die für den Pool angefordert werden.|
|`enableAutoScale`|Bool|Gibt an, ob die Poolgröße mit der Zeit automatisch angepasst wird.|
|`isAutoPool`|Bool|Gibt an, ob der Pool über den AutoPool-Mechanismus eines Auftrags erstellt wurde.|
