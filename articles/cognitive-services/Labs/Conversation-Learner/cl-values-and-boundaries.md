---
title: Standardkonfiguration des Unterhaltungslernmoduls – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Weitere Informationen zur Standardkonfiguration des Unterhaltungslernmoduls
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c0ad9f71665e503fe794c68200b90a8474750823
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173624"
---
# <a name="default-values-and-boundaries"></a>Standardwerte und Grenzen

Dieser Artikel behandelt die Standardkonfiguration des Unterhaltungslernmoduls und die wichtigsten Dienstgrenzen.

## <a name="default-configuration"></a>Standardkonfiguration

Parameter | Standardwert
--- | --- 
Standardwert für Sitzungstimeout | 30 Minuten

## <a name="boundaries"></a>Grenzen

Parameter | Begrenzung
--- | --- 
Erstellungs-API, max. HTTP-Aufrufe pro Monat | 5M
Erstellungs-API, max. HTTP-Aufrufe pro Sekunde | 25
Sitzungs-API, max. HTTP-Aufrufe pro Monat | 500K
Sitzungs-API, max. HTTP-Aufrufe pro Sekunde | 10
Max. Anzahl benutzerdefinierter (nicht programmgesteuerter) Entitäten pro Modell | Weitere Informationen im Artikel [LUIS-Grenzen](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-boundaries); in der Praxis kann die tatsächliche Anzahl etwas kleiner ausfallen
Max. Anzahl vorgefertigter Entitäten pro Modell | Weitere Informationen im Artikel [LUIS-Grenzen](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-boundaries)
Max. Anzahl von Entitäten (insgesamt) pro Modell | 100
Max. Anzahl von Aktionen pro Modell | 32
Max. Anzahl von Trainingsdialogen pro Modell | 1000
Max. Anzahl von Benutzeraktionen pro Trainingsdialog | 100
Max. Anzahl von protokollierten Dialogen pro Modell | Kein voreingestelltes Limit, allerdings werden protokollierte Dialoge nur für einen bestimmten Zeitraum beibehalten, bevor Sie verworfen werden.  Außerdem zeigt die Benutzeroberfläche des Unterhaltungslernmoduls 100 protokollierte Dialoge gleichzeitig an. 
Maximale Anzahl von Modellen pro Benutzer | Kein voreingestelltes Limit
Max. Anzahl sequenzieller Aktionen ohne Wartezeit | 5 (*)

(*) Nach 5 sequentiellen Aktionen ohne Wartezeit werden alle Aktionen ohne Wartezeit maskiert und das Unterhaltungslernmodul wählt eine Aktion aus den verfügbaren Aktionen mit Wartezeit aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte mit dem Unterhaltungslernmodul](./quickstart.md)
