---
title: Dashboard des StorSimple Manager-Diensts | Microsoft Docs
description: "Beschreibt das Dashboard des StorSimple Manager-Diensts und erläutert, wie Sie den Zustand Ihrer StorSimple-Lösung über dieses Dashboard verwalten."
services: storsimple
documentationcenter: 
author: SharS
manager: carmonm
editor: 
ms.assetid: fb0f131d-d60b-45d7-ace2-56d0502e6627
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 62273c0093876f136d97eedf5a509f0b306a1379
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-dashboard"></a>Verwenden des Dashboards des StorSimple Manager-Diensts
> [!NOTE]
> Das klassische Portal für StorSimple ist veraltet. Ihre StorSimple-Geräte-Manager werden gemäß dem Zeitplan für die Abschaltung automatisch in das neue Azure-Portal verschoben. Sie erhalten zu dieser Verschiebung eine E-Mail und eine Portalbenachrichtigung. Dieses Dokument wird ebenfalls bald entfernt. Die Version dieses Artikel für das neue Azure-Portal finden Sie unter [Verwenden des Dienstübersichtsblatt für Geräte der StorSimple 8000-Serie](storsimple-8000-service-dashboard.md). Antworten auf Fragen zu dieser Verschiebung finden Sie unter [Verschieben des StorSimple Device Manager-Diensts vom klassischen Portal in das Azure-Portal: häufig gestellte Fragen (FAQ)](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Übersicht
Auf der Dashboard-Seite des StorSimple Manager-Diensts erhalten Sie einen Überblick über alle Geräte, die mit dem StorSimple Manager-Dienst verbunden sind. Dienste, die die Aufmerksamkeit des Systemadministrators erfordern, sind dabei markiert. Dieses Tutorial bietet eine Einführung zur Dashboardseite, erläutert die Dashboardinhalte und -funktion und beschreibt die Aufgaben, die Sie auf dieser Seite ausführen können.

![Dienstdashboard](./media/storsimple-service-dashboard/HCS_ServiceDashboard.png)

Das Dashboard des StorSimple Manager-Diensts zeigt die folgenden Informationen an:

* Im **Diagrammbereich** wird Ihnen das relevante Metrikdiagramm für Ihre Geräte präsentiert. Sie können den von allen Geräten über einen bestimmten Zeitraum verwendeten primären (lokalen und mehrstufigen) Speicher und Cloudspeicher anzeigen. Verwenden Sie die Steuerelemente oben rechts im Diagramm, um einen Zeitraum von einer Woche, einem Monat, drei Monaten oder einem Jahr anzugeben.
* Die **Nutzungsübersicht** zeigt den bereitgestellten primären Speicher, der von allen Geräten im Verhältnis zum Gesamtspeicher genutzt wird, der für alle Geräte zur Verfügung steht. **Bereitgestellt** bezieht sich auf die Menge an Speicherplatz, die für die Verwendung vorbereitet und zugewiesen wird. **Verwendet** bezieht sich auf die Verwendung von Volumes, wie sie von den Initiatoren angezeigt wird, die mit den Geräten verbunden sind.
* Der Bereich **Warnungen** stellt eine Momentaufnahme aller aktiven Warnungen auf sämtlichen Geräten bereit, gruppiert nach dem jeweiligen Schweregrad. Durch Klicken auf den Schweregrad wird die Seite mit den **Warnungen** geöffnet, auf der nur die betreffenden Warnungen angezeigt werden. Auf der Seite mit den **Warnungen** können Sie auf einzelne Warnungen klicken, um weitere Details zur jeweiligen Warnung anzuzeigen, einschließlich empfohlener Aktionen. Sie können die Warnung auch löschen, falls das Problem behoben wurde.
* Der Bereich **Aufträge** bietet eine Momentaufnahme der kürzlich ausgeführten Aufträge auf allen Geräten, die mit dem Dienst verbunden sind. Über die aufgeführten Links können Sie die Aufträge anzeigen, die derzeit ausgeführt werden, sowie Aufträge, bei denen innerhalb der letzten 24 Stunden Fehler aufgetreten sind, oder solche, die in den nächsten 24 Stunden ausgeführt werden sollen.
* Der **Schnellansichtsbereich** bietet nützliche Informationen wie den Dienststatus, die Anzahl der mit dem Dienst verbundenen Geräte, den Standort des Diensts und Details zum Abonnement, das dem Dienst zugeordnet ist. Es gibt auch ein Link zum Vorgangsprotokoll. Klicken Sie auf den Link, um eine Liste aller abgeschlossenen Vorgänge des StorSimple Manager-Diensts anzuzeigen.

Über das Dashboard des StorSimple Manager-Diensts können Sie die Ausführung der folgenden Aufgaben initiieren:

* Anzeigen oder Neugenerieren des Dienstregistrierungsschlüssels
* Ändern des Verschlüsselungsschlüssels für Dienstdaten
* Anzeigen der Vorgangsprotokolle

## <a name="view-or-regenerate-the-service-registration-key"></a>Anzeigen oder Neugenerieren des Dienstregistrierungsschlüssels
Der Dienstregistrierungsschlüssel wird verwendet, um ein Microsoft Azure StorSimple-Gerät mit dem StorSimple Manager-Dienst zu registrieren, damit das Gerät im klassischen Azure-Portal für weitere Verwaltungsvorgänge angezeigt wird. Der Schlüssel wird auf dem ersten Gerät erstellt und mit den übrigen Geräten gemeinsam genutzt.

Durch Klicken auf **Registrierungsschlüssel** (unten auf der Seite) wird das Dialogfeld **Dienstregistrierungsschlüssel** geöffnet, über das Sie den aktuellen Dienstregistrierungsschlüssel in die Zwischenablage kopieren oder den Dienstregistrierungsschlüssel neu generieren können.

Das erneute Generieren des Schlüssels wirkt sich nicht auf zuvor registrierte Geräte aus, sondern nur auf die Geräte, die nach der Neugenerierung des Schlüssels mit dem Dienst registriert werden.

Weitere Informationen über das Anzeigen und Generieren des Dienstregistrierungsschlüssel finden Sie unter [Abrufen des Dienstregistrierungsschlüssels](storsimple-manage-service.md#get-the-service-registration-key).

## <a name="change-the-service-data-encryption-key"></a>Ändern des Verschlüsselungsschlüssels für Dienstdaten
Dienstdaten-Verschlüsselungsschlüssel werden verwendet, um vertrauliche Kundendaten zu verschlüsseln, beispielsweise Anmeldeinformationen für das Speicherkonto, die vom StorSimple Manager-Dienst zum StorSimple-Gerät gesendet werden. Sie müssen diese Schlüssel regelmäßig ändern, wenn Ihre IT-Organisation über eine Richtlinie zur Schlüsselrotation für Speichergeräte verfügt. Das Verfahren zur Schlüsseländerung kann variieren, je nachdem, ob ein einzelnes Gerät oder mehrere Geräte vom StorSimple Manager-Dienst verwaltet werden.

Das Ändern den Verschlüsselungsschlüssel für Dienstdaten wird in drei Schritten vollzogen:

1. Sie autorisieren ein Gerät über das klassische Azure-Portal, um den Verschlüsselungsschlüssel für Dienstdaten zu ändern.
2. Sie verwenden Windows PowerShell für StorSimple, um die Änderung des Verschlüsselungsschlüssels für Dienstdaten zu initialisieren.
3. Wenn Sie über mehrere StorSimple-Geräte verfügen, aktualisieren Sie den Verschlüsselungsschlüssel für Dienstdaten auf den anderen Geräten.

Die folgenden Schritte beschreiben den Rolloverprozess für den Dienstdaten-Verschlüsselungsschlüssel.

[!INCLUDE [storsimple-change-data-encryption-key](../../includes/storsimple-change-data-encryption-key.md)]

## <a name="view-the-operations-logs"></a>Anzeigen der Vorgangsprotokolle
Sie können die Vorgangsprotokolle anzeigen, indem Sie auf den entsprechenden Link im **Schnellansichtsbereich** des Dashboards klicken. Dadurch gelangen Sie zur Verwaltungsdienste-Seite. Dort können Sie die Protokolle filtern und die für den StorSimple Manager-Dienst spezifischen Protokolle anzeigen.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie Sie [Probleme bei StorSimple-Geräten behandeln](storsimple-troubleshoot-operational-device.md).
* Erfahren Sie mehr über das [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple Manager-Diensts](storsimple-manager-service-administration.md).

