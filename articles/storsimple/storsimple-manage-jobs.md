---
title: "Anzeigen und Verwalten von StorSimple-Aufträgen | Microsoft Docs"
description: "Beschreibt die Seite „Aufträge“ des StorSimple Manager-Diensts und ihre Verwendung zum Nachverfolgen kürzlich ausgeführter, aktueller und geplanter Sicherungsaufträge."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 55922cd0-d490-48eb-938a-012a67c1c09e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 7d9377bb8f3cb8c587823c2d71d61dfb9b50f52f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs"></a>Anzeigen und Verwalten von StorSimple-Aufträgen mithilfe des StorSimple Manager-Diensts
[!INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Übersicht
Die Seite **Aufträge** stellt ein zentrales Portal zum Anzeigen und Verwalten von Aufträgen bereit, die für Geräte gestartet wurden, die mit dem StorSimple Manager-Dienst verbunden sind. Sie können geplante, aktive, abgeschlossene und fehlgeschlagene Aufträge für mehrere Geräte anzeigen. Die jeweiligen Ergebnisse werden in einem Tabellenformat angezeigt. 

![Seite „Aufträge“](./media/storsimple-manage-jobs/HCS_JobsPage.png)

Sie können die Aufträge, an denen Sie interessiert sind, schnell finden, indem Sie nach folgenden Feldern filtern:

* **Status** – Ein Auftrag kann einen der folgenden Status haben: „Wird ausgeführt“, „Geplant“, „Fehler“, „Abgeschlossen“, „Wird abgebrochen“ oder „Abgebrochen“.
* **Typ** – Aufträge können wegen einer geplanten oder On-Demand-Sicherung (**Sicherung anlegen**), Klonen, einer Gerätewiederherstellung oder einem Updatevorgang erstellt werden.
* **Geräte** – Aufträge werden für ein bestimmtes Gerät ausgelöst, das mit Ihrem Dienst verbunden ist.
* **Von und Bis** – Aufträge können entsprechend einem Datums- und Zeitbereich gefiltert werden.

Die gefilterten Aufträge werden dann anhand der folgenden Attribute tabellarisch aufgelistet:

* **Typ** – „Sicherung“, „Klonen“, „Wiederherstellung“, „Failover“ oder „Update“.
* **Status** –  „Wird ausgeführt“, „Geplant“, „Fehler“, „Abgeschlossen“, „Wird abgebrochen“ oder „Abgebrochen“.
* **Entität** – Die Aufträge können einem Volume, einer Sicherungsrichtlinie oder einem Gerät zugeordnet sein. Ein Klonauftrag ist einem Volume zugeordnet, während ein geplanter Sicherungsauftrag einer Sicherungsrichtlinie zugeordnet ist. Ein Geräteauftrag wird aufgrund eines Notfallwiederherstellungs- oder eines Wiederherstellungsvorgangs erstellt.
* **Gerät** – Der Name des Geräts, für den der Auftrag gestartet wurde.
* **Gestartet am** – Der Zeitpunkt, zu dem der Auftrag gestartet wurde.
* **Status** – Der Prozentsatz, bis zu dem ein Auftrag abgearbeitet ist, der ausgeführt wird. Für einen abgeschlossenen Auftrag muss dieser Wert immer gleich 100 % sein.

Die Liste der Aufträge wird alle 30 Sekunden aktualisiert.

Sie können auf dieser Seite die folgenden auftragsbezogenen Aktionen ausführen:

* Anzeigen von Auftragsdetails
* Abbrechen eines Auftrags

## <a name="view-job-details"></a>Anzeigen von Auftragsdetails
Führen Sie die folgenden Schritte aus, um die Details eines Auftrags anzuzeigen.

#### <a name="to-view-job-details"></a>So zeigen Sie Auftragsdetails an
1. Zeigen Sie auf der Seite **Aufträge** die Aufträge an, an denen Sie interessiert sind, indem Sie eine Abfrage mit entsprechenden Filtern ausführen. Sie können nach abgeschlossenen, ausführenden oder abgebrochenen Aufträgen suchen.
2. Wählen Sie einen Auftrag aus.
3. Klicken Sie unten auf der Seite auf **Details**.
4. Im Dialogfeld **Sicherung Auftragsdetails** können Sie den Status, die Details, Zeitstatistiken und Datenstatistiken sehen.

## <a name="cancel-a-job"></a>Abbrechen eines Auftrags
Führen Sie die folgenden Schritte aus, um einen Auftrag abzubrechen, der momentan ausgeführt wird.

### <a name="to-cancel-a-job"></a>So brechen Sie einen Auftrag ab
1. Zeigen Sie auf der Seite **Aufträge** die Aufträge an, die momentan ausgeführt werden und die Sie abbrechen möchten. Führen Sie dazu eine Abfrage mit entsprechenden Filtern aus.
2. Wählen Sie den Auftrag aus.
3. Klicken Sie unten auf der Seite auf **Abbrechen**.
4. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**.

Dieser Auftrag wird nun abgebrochen.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über das [Verwalten von StorSimple-Sicherungsrichtlinien](storsimple-manage-backup-policies.md).
* Erfahren Sie mehr über das [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple Manager-Diensts](storsimple-manager-service-administration.md).

