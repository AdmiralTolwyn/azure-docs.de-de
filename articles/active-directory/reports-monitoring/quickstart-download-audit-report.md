---
title: 'Schnellstart: Herunterladen eines Überwachungsberichts über das Azure-Portal | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie einen Überwachungsbericht über das Azure-Portal herunterladen.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d702810ae7de03a14e50f56c2b8e819c31f3ea9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56192709"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Schnellstart: Herunterladen eines Überwachungsberichts über das Azure-Portal

In dieser Schnellstartanleitung erfahren Sie, wie Sie die Überwachungsprotokolle der letzten 24 Stunden für Ihren Mandanten herunterladen. Sie können bis zu 5000 Datensätze aus dem Azure-Portal herunterladen. Die Datensätze werden nach Aktualität sortiert, und standardmäßig erhalten Sie die neuesten 5.000 Datensätze. 

## <a name="prerequisites"></a>Voraussetzungen

Erforderlich:

* Ein Azure Active Directory-Mandant. 
* Ein Benutzer, der über die Rolle **Sicherheitsadministrator**, **Benutzer mit Leseberechtigung für Sicherheitsfunktionen** oder **Globaler Administrator** für den Mandanten verfügt. Darüber hinaus kann jeder Benutzer im Mandanten auf die eigenen Überwachungsprotokolle zugreifen.

## <a name="quickstart-download-an-audit-report"></a>Schnellstart: Herunterladen eines Überwachungsberichts

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
2. Wählen Sie im linken Navigationsbereich die Option **Azure Active Directory** aus, und klicken Sie auf die Schaltfläche **Verzeichnis wechseln**, um Ihr Active Directory-Verzeichnis auszuwählen.
3. Wählen Sie im Dashboard **Azure Active Directory** und dann **Überwachungsprotokolle** aus. 
4. Wählen Sie in der Dropdownliste **Datumsbereich** den Eintrag **Letzte 24 Stunden** aus, und klicken Sie auf **Anwenden**, um die Überwachungsprotokolle für die letzten 24 Stunden anzuzeigen. 
5. Wählen Sie die Schaltfläche **Herunterladen** aus, um eine CSV-Datei mit den gefilterten Datensätzen herunterzuladen. 

![Berichterstellung](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Nächste Schritte

* [Berichte zu Anmeldeaktivitäten im Azure Active Directory-Portal](concept-sign-ins.md)
* [Vermerkdauer für Azure Active Directory-Berichte](reference-reports-data-retention.md)
* [Latenzen bei Azure Active Directory-Berichten – Vorschau](reference-reports-latencies.md)
