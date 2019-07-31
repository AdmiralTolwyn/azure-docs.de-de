---
title: Hinzufügen von Datasets zu einer vorhandenen Datenfreigabe in Azure Data Share (Vorschauversion)
description: Hinzufügen von Datasets zu einer vorhandenen Datenfreigabe
author: madams0013
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: t-maadam
ms.openlocfilehash: 01197d91277c59b58c5ab841dfc2abfb78be71de
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877408"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share-preview"></a>Hinzufügen von Datasets zu einer vorhandenen Datenfreigabe in Azure Data Share (Vorschauversion)

In diesem Artikel wird erläutert, wie Sie einer vorhandenen Datenfreigabe mithilfe von Azure Data Share (Vorschauversion) Datasets hinzufügen. Auf diese Weise können Sie mehr Daten für dieselben Empfänger freigeben, ohne eine neue Freigabe zu erstellen.

Informationen zum Hinzufügen von Datasets beim Erstellen einer Freigabe finden Sie im Tutorial [Freigeben von Daten](share-your-data.md).

## <a name="navigate-to-a-sent-data-share"></a>Navigieren zu einer gesendeten Datenfreigabe

Navigieren Sie in Azure Data Share (Vorschauversion) zur gesendeten Freigabe, und klicken Sie auf die Registerkarte **Datasets**. Klicken Sie auf die Schaltfläche **+ Datasets hinzufügen**, um weitere Datasets hinzuzufügen.

![Hinzufügen von Datasets](./media/how-to/how-to-add-datasets/add-datasets.png)

Wählen Sie im Panel auf der rechten Seite den Datasettyp aus, den Sie hinzufügen möchten, und klicken Sie dann auf **Weiter**. Wählen Sie das Abonnement und die Ressourcengruppe der Daten aus, die Sie hinzufügen möchten. Suchen Sie mithilfe der nach unten weisenden Pfeile nach den Daten, die hinzugefügt werden sollen, und aktivieren Sie dann das Feld neben den Daten.

![Hinzufügen von Datasets](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Sobald Sie auf **Datasets hinzufügen** klicken, werden die Datasets zu Ihrer Freigabe hinzugefügt. Hinweis: Ihre Consumer müssen eine Momentaufnahme auslösen, damit ihnen die neuen Datasets angezeigt werden. Wenn Einstellungen für Momentaufnahmen konfiguriert sind, sehen die Consumer die neuen Datasets, sobald die nächste geplante Momentaufnahme abgeschlossen wurde. Sind keine Einstellungen für Momentaufnahmen konfiguriert, muss der Consumer manuell eine vollständige oder inkrementelle Kopie der Daten auslösen, um die Aktualisierungen zu erhalten. Weitere Informationen zu Momentaufnahmen finden Sie unter [Momentaufnahmen](terminology.md).

## <a name="next-steps"></a>Nächste Schritte
Informationen zum [Hinzufügen von Empfängern zu einer vorhandenen Datenfreigabe](how-to-add-recipients.md)