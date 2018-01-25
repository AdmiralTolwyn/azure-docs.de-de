---
title: "Hinzufügen einer Verzögerung in Logik-Apps | Microsoft Docs"
description: "Übersicht über die Aktionen „Verzögern“ und „Verzögern bis“ sowie über deren Verwendung in einer Azure-Logik-App."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 915f48bf-3bd8-4656-be73-91a941d0afcd
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 6cde5b8ba8d770a07199816286b666e952394de1
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Erste Schritte mit den Aktionen „Verzögern“ und „Verzögern bis“
Die Aktionen „Verzögern“ und „Verzögern bis“ eignen sich zur Durchführung von Workflowszenarien.

Sie haben beispielsweise folgende Möglichkeiten:

* Versand einer E-Mail mit einem Statusupdate bis zum Erreichen eines Werktags verzögern.
* Workflow verzögern, bis ein HTTP-Aufruf abgeschlossen werden konnte, und erst dann den Vorgang fortsetzen und das Ergebnis abrufen.

Wenn Sie die Verzögerungsaktion in einer Logik-App verwenden möchten, müssen Sie zunächst eine [Logik-App erstellen](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-delay-actions"></a>Verwenden der Verzögerungsaktionen
Eine Aktion ist ein Vorgang, der durch den in einer Logik-App definierten Workflow ausgeführt wird. Weitere Informationen zu Aktionen finden Sie [hier](connectors-overview.md).

Im Anschluss sehen Sie eine Beispielsequenz für die Verwendung eines Verzögerungsschritts in einer Logik-App:

1. Klicken Sie nach dem Hinzufügen eines Triggers auf **Neuer Schritt** , um eine Aktion hinzuzufügen.
2. Suchen Sie nach **verzögern** , um die Verzögerungsaktionen anzuzeigen. In diesem Beispiel wählen wir **Verzögern**aus.
   
    ![Verzögern von Aktionen](./media/connectors-native-delay/using-action-1.png)
3. Geben Sie die gewünschten Aktionseigenschaften an, um die Verzögerung zu konfigurieren.
   
    ![Verzögerungskonfiguration](./media/connectors-native-delay/using-action-2.png)
4. Klicken Sie auf **Speichern** , um die Logik-App zu veröffentlichen und zu aktivieren.

## <a name="action-details"></a>Aktionsdetails
Der Wiederholungstrigger verfügt über folgende konfigurierbare Eigenschaften:

### <a name="delay-action"></a>Aktion „Verzögern“
Diese Aktion verzögert die Ausführung für ein bestimmtes Zeitintervall.
Ein * bedeutet, dass es sich um ein Pflichtfeld handelt.

| Anzeigename | Eigenschaftenname | BESCHREIBUNG |
| --- | --- | --- |
| Anzahl* |count |Die Anzahl von Zeiteinheiten für die Verzögerung. |
| Einheit* |unit |Die Zeiteinheit: `Second`, `Minute`, `Hour` oder `Day` |

<br>

### <a name="delay-until-action"></a>Aktion „Verzögern bis“
Diese Aktion verzögert die Ausführung bis zum angegebenen Zeitpunkt (Datum/Uhrzeit).
Ein * bedeutet, dass es sich um ein Pflichtfeld handelt.

| Anzeigename | Eigenschaftenname | BESCHREIBUNG |
| --- | --- | --- |
| Jahr* |timestamp |Das Jahr, bis zu dem verzögert werden soll. (GMT) |
| Monat* |timestamp |Der Monat, bis zu dem verzögert werden soll. (GMT) |
| Tag* |timestamp |Der Tag, bis zu dem verzögert werden soll. (GMT) |

<br>

## <a name="next-steps"></a>Nächste Schritte
Testen Sie nun die Plattform, und [erstellen Sie eine Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Machen Sie sich ggf. anhand unserer [API-Liste](apis-list.md) mit den anderen verfügbaren Connectors für Logik-Apps vertraut.

