---
title: Datenreplikation in Azure Storage | Microsoft-Dokumentation
description: "Die Daten in Ihrem Microsoft Azure Storage-Konto werden stets repliziert, um Beständigkeit und Hochverfügbarkeit sicherzustellen. Die Redundanzoptionen umfassen den lokal redundanten Speicher (LRS), den zonenredundanten Speicher (ZRS), den georedundanten Speicher (GRS) und den georedundanten Speicher mit Lesezugriff (RA-GRS)."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 86bdb6d4-da59-4337-8375-2527b6bdf73f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: 45883d59e5fe9ab2b7a09bfdc6c11a681bd43d0b
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-storage-replication"></a>Azure Storage-Replikation
Die Daten in Ihrem Microsoft Azure-Speicherkonto werden stets repliziert, um Beständigkeit und Hochverfügbarkeit sicherzustellen. Bei der Replikation werden Ihre Daten kopiert, und zwar entweder innerhalb desselben Rechenzentrums, oder in ein zweites Rechenzentrum. Dies hängt davon ab, welche Replikationsoption Sie wählen. Mit der Replikation werden Ihre Daten geschützt, und die Betriebszeit der Anwendung wird hoch gehalten, falls es zu vorübergehenden Hardwareausfällen kommt. Wenn Ihre Daten in einem zweiten Rechenzentrum repliziert werden, sind Ihre Daten vor einem Ausfall aufgrund einer Katastrophe am primären Standort geschützt.

Mit der Replikation wird sichergestellt, dass Ihr Speicherkonto auch bei Ausfällen die [Servicelevelvereinbarung (SLA) für Storage](https://azure.microsoft.com/support/legal/sla/storage/) erfüllt. Die Servicelevelvereinbarung enthält Informationen zu Azure Storage-Garantien in Bezug auf Dauerhaftigkeit und Verfügbarkeit.

Wenn Sie ein Speicherkonto erstellen, können Sie eine der folgenden Replikationsoptionen auswählen:

* [Lokal redundanter Speicher (LRS)](#locally-redundant-storage)
* [Zonenredundanter Speicher (ZRS)](#zone-redundant-storage)
* [Georedundanter Speicher (GRS)](#geo-redundant-storage)
* [Georedundanter Speicher mit Lesezugriff (RA-GRS)](#read-access-geo-redundant-storage)

Georedundanter Speicher mit Lesezugriff (RA-GRS) ist die Standardoption bei der Erstellung eines Speicherkontos.

Die folgende Tabelle bietet eine schnelle Übersicht über die Unterschiede zwischen LRS, ZRS, GRS und RA-GRS. In den nachfolgenden Abschnitten werden die einzelnen Replikationstypen ausführlicher behandelt.

| Replikationsstrategie | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Daten werden in mehreren Datencentern repliziert. |Nein |Ja |Ja |Ja |
| Daten können von einem sekundären Standort sowie vom primären Standort gelesen werden. |Nein |Nein |Nein |Ja |
| Konzipiert, um ___ Dauerhaftigkeit von Objekten über ein bestimmtes Jahr zu bieten. |mindestens 99,999999999 % (11 mal die 9)|mindestens 99,9999999999 % (12 mal die 9)|mindestens 99,99999999999999 % (16 mal die 9)|mindestens 99,99999999999999 % (16 mal die 9)|

Informationen zu den Preisen für die verschiedenen Redundanzoptionen finden Sie unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/) .

> [!NOTE]
> Storage Premium unterstützt nur lokal redundanten Speicher (Locally Redundant Storage, LRS). Informationen zu Storage Premium finden Sie unter [Storage Premium: Hochleistungsspeicher für Workloads virtueller Azure-Computer](../../virtual-machines/windows/premium-storage.md).
>

## <a name="locally-redundant-storage"></a>Lokal redundanter Speicher
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## <a name="zone-redundant-storage"></a>Zonenredundanter Speicher
Zonenredundanter Speicher (ZRS) ist so konzipiert, dass er eine Dauerhaftigkeit von mindestens 99,99999999999999 % (12 mal die 9) der Objekte über ein gegebenes Jahr bietet, indem er Ihre Daten asynchron über Rechenzentren in ein oder zwei Regionen repliziert und somit eine höhere Dauerhaftigkeit als LRS bietet. Per ZRS gespeicherte Daten bleiben auch dann dauerhaft vorhanden, wenn das primäre Datencenter nicht verfügbar oder nicht mehr wiederherstellbar ist.
Kunden, die die Verwendung von ZRS planen, sollten Folgendes beachten:

* ZRS ist nur für Blockblobs in Speicherkonten für allgemeine Zwecke verfügbar und wird nur für die Speicherdienstversionen 2014-02-14 und höher unterstützt.
* Da eine asynchrone Replikation eine Verzögerung einschließt, gehen bei einem lokalen Notfall Änderungen, die noch nicht am sekundären Ort repliziert wurden, unter Umständen verloren, wenn die Daten am primären Ort nicht wiederhergestellt werden können.
* Das Replikat ist ggf. erst wieder verfügbar, wenn von Microsoft das Failover an den sekundären Ort initiiert wird.
* ZRS-Konten können später nicht in LRS oder GRS konvertiert werden. Ebenso kann ein vorhandenes LRS- oder GRS-Konto nicht in ein ZRS-Konto konvertiert werden.
* ZRS-Konten verfügen nicht über Metriken oder eine Protokollierungsfunktion.

## <a name="geo-redundant-storage"></a>Georedundanter Speicher
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="read-access-geo-redundant-storage"></a>Georedundanter Speicher mit Lesezugriff
Georedundanter Speicher mit Lesezugriff (RA-GRS) maximiert die Verfügbarkeit für das Speicherkonto, indem ein schreibgeschützter Zugriff auf Daten am sekundären Standort zusätzlich zur von GRS gebotenen Replikation in zwei Regionen bereitgestellt wird.

Wenn Sie den schreibgeschützten Zugriff auf Ihre Daten in der sekundären Region aktivieren, sind Ihre Daten zusätzlich zum primären Endpunkt für Ihr Speicherkonto auf einem sekundären Endpunkt verfügbar. Der sekundäre Endpunkt ähnelt dem primären Endpunkt, wobei das Suffix `–secondary` an den Kontonamen angefügt wird. Wenn Ihr primärer Endpunkt für den Blob-Dienst z. B. `myaccount.blob.core.windows.net` ist, dann ist Ihr sekundärer Endpunkt `myaccount-secondary.blob.core.windows.net`. Die Zugriffsschlüssel für das Speicherkonto sind für die primären und sekundären Endpunkte identisch.

Überlegungen:

* Von Ihrer Anwendung muss verwaltet werden, mit welchem Endpunkt sie bei Verwendung von RA-GRS interagieren muss.
* Da eine asynchrone Replikation eine Verzögerung einschließt, gehen bei einem regionalen Notfall Änderungen, die noch nicht in der sekundären Region repliziert wurden, unter Umständen verloren, wenn die Daten nicht aus der primären Region wiederhergestellt werden können.
* Wenn Microsoft ein Failover auf die sekundäre Region initiiert, erhalten Sie nach Abschluss des Failovers Lese- und Schreibzugriff auf diese Daten. Weitere Informationen finden Sie im [Leitfaden zur Notfallwiederherstellung](../storage-disaster-recovery-guidance.md).
* RA-GRS ist für hohe Verfügbarkeit ausgelegt. Eine Anleitung zur Skalierbarkeit finden Sie in der [Checkliste zur Leistung](../storage-performance-checklist.md).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1. Wie kann ich den Georeplikationstyp meines Speicherkontos ändern?

   Sie können den Georeplikationstyp Ihres Speicherkontos in LRS, GRS und RA-GRS ändern, indem Sie das [Azure-Portal](https://portal.azure.com/) oder [Azure PowerShell](storage-powershell-guide-full.md) verwenden, oder Sie können programmgesteuert vorgehen, indem Sie eine unserer vielen Speicherclientbibliotheken verwenden.
Beachten Sie, dass ZRS-Konten nicht in LRS oder GRS konvertiert werden können. Ebenso kann ein vorhandenes LRS- oder GRS-Konto nicht in ein ZRS-Konto konvertiert werden.

<a id="changedowntime"></a>
#### <a name="2-will-there-be-any-down-time-if-i-change-the-replication-type-of-my-storage-account"></a>2. Kommt es zu Ausfallzeiten, wenn ich den Replikationstyp meines Speicherkontos ändere?

   Nein, es kommt nicht zu Ausfallzeiten.

<a id="changecost"></a>
#### <a name="3-will-there-be-any-additional-cost-if-i-change-the-replication-type-of-my-storage-account"></a>3. Fallen zusätzliche Kosten an, wenn ich den Replikationstyp meines Speicherkontos ändere?

   Ja. Wenn Sie für Ihr Speicherkonto die Umstellung von LRS auf GRS (oder RA-GRS) durchführen, fallen Zusatzgebühren für den Datenverkehr in ausgehender Richtung an, der mit dem Kopieren von vorhandenen Daten vom primären Standort an den sekundären Standort verbunden ist. Nachdem die anfänglichen Daten kopiert wurden, fallen keine zusätzlichen Ausgangsgebühren für die Georeplikation der Daten vom primären zum sekundären Standort an. Die Details zu Bandbreitengebühren finden Sie unter [Azure Blob Storage – Preise](https://azure.microsoft.com/pricing/details/storage/blobs/).
Wenn Sie von GRS zu LRS wechseln, entstehen keine zusätzlichen Kosten, aber Ihre Daten werden am sekundären Standort gelöscht.

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4. Welche Vorteile bietet RA-GRS?

   Georedundanter Speicher (GRS) ermöglicht die Replikation Ihrer Daten aus einer primären in einer sekundären Region, die Hunderte Kilometer weit von der primären Region entfernt ist. In diesem Fall sind Ihre Daten auch bei einem regionalen Komplettausfall oder einem Notfall beständig gespeichert, nach dem die primäre Region nicht mehr wiederherstellbar ist. Georedundanter Speicher mit Lesezugriff (RA-GRS) umfasst diese Optionen und bietet zusätzlich die Möglichkeit, Daten am sekundären Standort zu lesen. Einige Ideen zur Nutzung dieser Möglichkeit finden Sie unter [Entwerfen hochverfügbarer Anwendungen mithilfe von RA-GRS-Speicher](../storage-designing-ha-apps-with-ragrs.md).

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-for-me-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5. Lässt sich herausfinden, wie lange es dauert, meine Daten aus der primären Region in der sekundären Region zu replizieren?

   Wenn Sie RA-GRS nutzen, können Sie für Ihr Speicherkonto den Zeitpunkt der letzten Synchronisierung überprüfen. Der Zeitpunkt der letzten Synchronisierung ist ein Datums-/Uhrzeitwert in GMT (Greenwich Mean Time). Alle primären Schreibvorgänge vor dem Zeitpunkt der letzten Synchronisierung wurden erfolgreich an den sekundären Standort geschrieben. Dies bedeutet, dass sie am sekundären Standort zur Verfügung stehen. Primäre Schreibvorgänge, die nach dem Zeitpunkt der letzten Synchronisierung stattgefunden haben, stehen unter Umständen noch nicht zum Lesen zur Verfügung. Sie können diesen Wert mit dem [Azure-Portal](https://portal.azure.com/) oder [Azure PowerShell](storage-powershell-guide-full.md) abfragen, oder Sie können programmgesteuert vorgehen, indem Sie die REST-API oder eine unserer Speicherclientbibliotheken verwenden.

<a id="outage"></a>
#### <a name="6-how-can-i-switch-to-the-secondary-region-if-there-is-an-outage-in-the-primary-region"></a>6. Wie kann ich zur sekundären Region wechseln, wenn es in der primären Region zu einem Ausfall kommt?

   Weitere Informationen finden Sie im Artikel [Vorgehensweise beim Ausfall von Azure Storage](../storage-disaster-recovery-guidance.md).

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7. Was ist die RPO und RTO in Bezug auf GRS?

   Recovery Point Objective (RPO): Bei GRS und RA-GRS führt der Speicherdienst eine asynchrone Georeplikation der Daten vom primären zum sekundären Standort durch. Wenn es zu einer größeren regionalen Katastrophe kommt und ein Failover durchgeführt werden muss, gehen die letzten Deltaänderungen, für die noch keine Georeplikation erfolgt ist, unter Umständen verloren. Die Anzahl von Minuten des Zeitraums, für den ein potenzieller Datenverlust auftritt, wird als RPO bezeichnet (der Zeitpunkt, bis zu dem Daten wiederhergestellt werden können). Der RPO-Wert beträgt normalerweise weniger als 15 Minuten, aber es gibt derzeit keine SLA dazu, wie lange die Georeplikation dauert.

   Recovery Time Objective (RTO): Dies ist eine Kennzahl dafür, wie lange es dauert, bis das Failover durchgeführt wurde und das Speicherkonto wieder online ist, wenn ein Failover erforderlich ist. Die Zeit für das Failover umfasst Folgendes:
    * Der Zeitraum für die Untersuchung und Ermittlung, ob die Daten am primären Standort wiederhergestellt werden können oder ob ein Failover durchgeführt werden muss.
    * Die Zeit für das Failover des Kontos, indem die primären DNS-Einträge so geändert werden, dass sie auf den sekundären Standort verweisen.

   Wir nehmen die Verantwortung für die Bewahrung Ihrer Daten sehr ernst. Falls also die Chance besteht, die Daten wiederherzustellen, stoppen wir die Durchführung des Failovers und konzentrieren uns auf die Wiederherstellung der Daten am primären Standort. Für die Zukunft ist die Bereitstellung einer API geplant, damit Sie ein Failover auf Kontoebene auslösen können, um die RTO selbst steuern zu können. Diese Option ist momentan aber noch nicht verfügbar.

## <a name="next-steps"></a>Nächste Schritte
* [Entwerfen hochverfügbarer Anwendungen mithilfe von RA-GRS-Speicher](../storage-designing-ha-apps-with-ragrs.md)
* [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Informationen zu Azure-Speicherkonten](../storage-create-storage-account.md)
* [Skalierbarkeits- und Leistungsziele für Azure-Speicher](storage-scalability-targets.md)
* [Microsoft Azure Storage Redundancy Options and Read Access Geo Redundant Storage (in englischer Sprache) ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [SOSP Paper - Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency (SOSP-Dokument – Azure Storage: ein hochverfügbarer Cloudspeicherdienst mit starker Konsistenz)](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
