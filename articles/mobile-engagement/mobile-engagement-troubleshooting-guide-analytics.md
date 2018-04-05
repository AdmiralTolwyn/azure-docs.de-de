---
title: Azure Mobile Engagement – Handbuch zur Problembehandlung – Analyse
description: Handbuch zur Problembehandlung bei Analyse, Überwachung, Segmentierung und Dashboard in Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 04a7020a-ad74-4491-be69-0bd574890029
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: b19d018b83ee8b3d5848d29afff190d3dcaf3fde
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-analytics-monitoring-segmentation-and-dashboard-issues"></a>Handbuch zur Problembehandlung bei Analyse, Überwachung, Segmentierung und Dashboard
> [!IMPORTANT]
> Azure Mobile Engagement wird am 31.3.2018 außer Kraft gesetzt. Diese Seite wird kurz danach gelöscht.
> 

Im Folgenden finden Sie mögliche Probleme, die in Bezug auf die Erfassung von Informationen zu Anwendungen, Geräten und Benutzern durch Azure Mobile Engagement auftreten können.

## <a name="missingdelayed-information"></a>Informationen fehlen/werden verzögert angezeigt
### <a name="issue"></a>Problem
* Informationen werden in Analyse, Segmentierung oder Dashboard verzögert angezeigt.
* Informationen fehlen in der Überwachung.
* Informationen fehlen in Analyse, Segmentierung oder Dashboard.
* Segmentierungsgrenzwerte werden erreicht.

### <a name="causes"></a>Ursachen
* Sie können mithilfe der APIs für Analyse, Überwachung und Segmentierung prüfen, ob fehlende Daten in der Benutzeroberfläche über die APIs sichtbar sind.
* Wenn das Azure Mobile Engagement-SDK nicht ordnungsgemäß in Ihre App integriert ist, können in Analyse, Segmentierung, Überwachung oder Dashboards keine Informationen angezeigt werden.
* Segmente können nach der Erstellung nicht geändert, sondern nur "geklont" (kopiert) oder "zerstört" (gelöscht) werden. Segmente können nur 10 Kriterien umfassen.
* Die beste Möglichkeit, auf fehlende Informationen in der Überwachung zu testen, besteht darin, ein Testgerät einzurichten und die App zu deinstallieren und/oder auf dem Testgerät neu zu installieren.
* Informationen für Analyse, Segmentierung oder Dashboards werden alle 24 Stunden aktualisiert.
* Informationen in neuen Segmenten werden möglicherweise erst 24 Stunden nach ihrer Erstellung angezeigt, selbst dann, wenn das Segment auf vorherigen Informationen basiert.
* Beim Filtern Ihre Analysedaten auf der Benutzeroberfläche werden alle Beispiele dieses Typs unabhängig von der Version Ihrer App angezeigt ( "Abstürzt" nach Name gefiltert zeigt beispielsweise Daten aus Version 1 und Version 2 der App an).
* Der Analysezeitraum basiert auf dem Datum in den Benutzergeräteeinstellungen. Wenn ein Benutzer also das Datum für sein Gerät falsch eingestellt hat, könnte dieses im falschen Zeitraum angezeigt werden.
* Es werden keine serverseitigen Daten protokolliert, wenn Sie die Schaltfläche zum Testen von Pushvorgängen verwenden. Daten werden nur für reale Pushkampagnen protokolliert.

## <a name="cant-locate-items-in-ui"></a>Elemente sind nicht in der Benutzeroberfläche auffindbar
### <a name="issue"></a>Problem
* Segmente können nicht basierend auf bestimmten integrierten oder benutzerdefinierten Infotagkriterien der App erstellt werden.
* Bestimmte integrierte oder benutzerdefinierte Infotagkriterien der App sind in Analyse, Überwachung oder Dashboards nicht auffindbar.
* Daten in Analyse, Überwachung, Segmentierung oder Dashboards können nicht interpretiert werden.

### <a name="causes"></a>Ursachen
* Einige integrierte Elemente und Infotags der App sind nur als Pushkriterien verfügbar. Sie können weder einem Segment hinzugefügt werden, noch sind sie in Analyse, Überwachung oder Dashboard sichtbar. 
* Für integrierte Elemente und Infotags der App, die keinem Segment hinzugefügt werden können, müssen Sie in jeder Kampagne eine Liste der Zielkriterien einrichten, um eine wie auf Segmenten basierende Zielgruppenadressierung zu erreichen.
* Weitere Informationen und Anleitungen finden Sie in den Kontextmenüs von Analyse, Überwachung und Dashboards der Azure Mobile Engagement-Benutzeroberfläche.

## <a name="crash-troubleshooting"></a>Problembehandlung von Abstürzen
### <a name="issue"></a>Problem
* In Analyse, Überwachung oder Dashboard werden Anwendungsabstürze angezeigt.

### <a name="causes"></a>Ursachen
* Zur Problembehandlung von Anwendungsabstürzen, die in Analyse, Überwachung oder Dashboard angezeigt werden, prüfen Sie die Versionshinweise auf bekannte Probleme mit vorherigen Versionen des SDK.
* Generieren Sie zur erweiterten Problembehandlung von Anwendungsabstürzen ein Ereignis auf einem Testgerät, auf dem die Anwendung installiert ist. Suchen Sie anschließend im Abschnitt "Überwachen – Ereignisse" der Azure Mobile Engagement-Benutzeroberfläche nach Ihrer Geräte-ID. Lösen Sie dann das Ereignis aus, das Ihre Anwendung zum Absturz bringt, und prüfen Sie die zusätzlichen Informationen im Abschnitt "Überwachen – Absturz" der Azure Mobile Engagement-Benutzeroberfläche. 

