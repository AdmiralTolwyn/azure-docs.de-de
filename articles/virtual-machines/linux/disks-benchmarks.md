---
title: Benchmarktests für Ihre Anwendung in Azure Disk Storage
description: Erhalten Sie Informationen zu Benchmarktests für Ihre Anwendung in Azure.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: conceptual
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 640ec54e9634751d05c2cea90d7c03d02e7a3387
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75720054"
---
# <a name="benchmarking-a-disk"></a>Benchmarktests für einen Datenträger

Bei Benchmarktests werden verschiedene Workloads Ihrer Anwendung simuliert und die Anwendungsleistung für jeden Workload gemessen. Verwenden Sie die im [Artikel „Entwerfen für hohe Leistung“](premium-storage-performance.md) beschriebenen Schritte. Durch Ausführen von Benchmarktools in den VMs, die die Anwendung hosten, können Sie die Leistungsgrade bestimmen, die Ihre Anwendung mit Storage Premium erreichen kann. In diesem Artikel bieten wir Beispiele für Benchmarktests einer Standard-VM der DS14-Serie, die mit Azure Storage Premium-Datenträgern bereitgestellt wurde.

Wir haben für Windows und Linux gängige Benchmarktools wie Iometer und FIO verwendet. Diese Tools erzeugen mehrere Threads zum Simulieren eines produktionsähnlichen Workloads und Messen der Systemleistung. Mithilfe der Tools können Sie auch Parameter wie Blockgröße und Warteschlangenlänge konfigurieren, die Sie normalerweise nicht für eine Anwendung ändern können. Dadurch können Sie flexibler die Maximalleistung einer Hochleistungs-VM ermitteln, die für verschiedene Typen von Anwendungsworkloads mit Premium-Datenträgern bereitgestellt ist. Weitere Informationen zu den einzelnen Benchmarktools finden Sie unter [Iometer](http://www.iometer.org/) und [FIO](http://freecode.com/projects/fio).

Erstellen Sie zum Befolgen der nachstehenden Beispiele eine Standard-VM vom Typ DS14, an die Sie 11 Storage Premium-Datenträger anfügen. Konfigurieren Sie 10 der 11 Datenträger mit der Hostcache-Einstellung „None“, und fügen Sie sie einem Stripeset mit dem Namen „NoCacheWrites“ hinzu. Konfigurieren Sie auf dem verbleibenden Datenträger die Hostcache-Einstellung „ReadOnly“, und erstellen Sie ein Volume mit dem Namen „CacheReads“ mit diesem Datenträger. Mithilfe dieser Einrichtung können Sie die maximale Lese- und Schreibleistung einer Standard-VM vom Typ DS14 ermitteln. Ausführliche Schritte zum Erstellen einer VM vom Typ DS14 mit Premium-Datenträgern finden Sie unter [Entwerfen für hohe Leistung](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie als nächstes unseren Artikel [Entwerfen für hohe Leistung](premium-storage-performance.md).

In diesem Artikel erstellen Sie eine Prüfliste ähnlich derjenigen für die vorhandene Anwendung für den Prototyp. Mithilfe von Benchmarktools können Sie die Workloads simulieren und die Leistung der Prototypanwendung messen. Auf diese Weise können Sie bestimmen, mit welchem Datenträgerangebot die Leistungsanforderungen Ihrer Anwendung erfüllt oder gar übertroffen werden. Anschließend können Sie dieselben Richtlinien für Ihre Produktionsanwendung implementieren.