---
title: Offlineauswertung
titleSuffix: Personalizer - Azure Cognitive Services
description: Erfahren Sie, wie Sie Ihre Lernschleife mit einer Offlineauswertung analysieren können.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: e99a8242e438ef5a8ab7fd917724450f8080bb41
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025413"
---
# <a name="how-to-analyze-your-learning-loop-with-an-offline-evaluation"></a>Gewusst wie: Analysieren Ihrer Lernschleife mit einer Offlineauswertung.


Erfahren Sie, wie Sie eine Offlineauswertung ausführen und die Ergebnisse verstehen können.

Mit Offlineauswertungen können Sie messen, wie effektiv die Personalisierung verglichen mit dem Standardverhalten Ihrer Anwendung ist, erfahren, welche Features am meisten zur Personalisierung beitragen, und automatisch neue Einstellungen für das Machine Learning ermitteln.

Weitere Informationen finden Sie unter [Offlineauswertung](concepts-offline-evaluation.md).


## <a name="prerequisites"></a>Voraussetzungen

1. Sie müssen eine Personalisierungsschleife konfiguriert haben.
1. Die Protokolle der Personalisierungsschleife müssen mindestens 50.000 Ereignisse enthalten, um aussagekräftige Ergebnisse zu erzielen.

Optional haben Sie möglicherweise auch zuvor _Lernrichtliniendateien_ exportiert, die Sie in der gleichen Auswertung vergleichen und testen können.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Schritte zum Starten einer neuen Offlineauswertung

1. Suchen Sie Ihre Personalisierungsschleifenressource im Azure-Portal.
1. Navigieren Sie zum Abschnitt „Evaluation“ (Auswertung).
1. Klicken Sie auf „New Evaluation“ (Neue Auswertung).
1. Wählen Sie ein Start- und Enddatum für die Offlineauswertung aus. Hierbei handelt es sich um Datumsangaben in der Vergangenheit, die den Bereich der zur Auswertung zu verwendenden Daten angeben. Diese Daten müssen gemäß der Einstellung [Datenaufbewahrung](how-to-settings.md) in den Protokollen vorliegen.
1. Optional können Sie Ihre eigene Lernrichtlinie hochladen. 
1. Geben Sie an, ob die Personalisierung basierend auf dem in diesem Zeitraum beobachteten Benutzerverhalten eine optimierte Lernrichtlinie erstellen soll.
1. Starten der Auswertung

## <a name="results"></a>Ergebnisse

Auswertungen können je nach der Menge zu verarbeitender Daten, der Anzahl der zu vergleichenden Lernrichtlinien und abhängig davon, ob eine Optimierung angefordert wurde, sehr lange dauern.

Danach können Sie die folgenden Ergebnisse sehen:

1. Vergleiche von Lernrichtlinien einschließlich:
    * **Online Policy** (Onlinerichtlinie): Die derzeit in der Personalisierung verwendete Lernrichtlinie
    * **Baseline**: Der Standard der Anwendung (wie durch die erste in Rangfolgeaufrufen gesendete Aktion bestimmt),
    * **Random Policy** (Zufallsrichtlinie): Ein imaginäres Rangfolgeverhalten, das immer ein zufällige Auswahl von Aktionen aus den angegebenen zurückgibt.
    * **Custom Policies** (Benutzerdefinierte Richtlinien): Zusätzliche, beim Starten der Auswertung hochgeladene Lernrichtlinien.
    * **Optimized Policy** (Optimierte Richtlinie): Wenn die Auswertung mit der Option zum Ermitteln einer optimierten Richtlinie gestartet wurde, wird sie auch verglichen, und Sie können sie herunterladen oder zur Online-Lernrichtlinie machen, wobei die aktuelle ersetzt wird.

1. Effektivität der [Features](concepts-features.md) für Aktionen und Kontext.


## <a name="more-information"></a>Weitere Informationen

* Erfahren Sie, [wie Offlineauswertungen funktionieren](concepts-offline-evaluation.md).