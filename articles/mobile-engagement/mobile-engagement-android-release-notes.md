---
title: Integration des Azure Mobile Engagement Android SDKs
description: "Neueste Updates und Verfahren für das Android SDK für Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 585341f9-3f39-459a-af42-864e400a0128
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: c179c39a43da0aa35e945acceacbf27fe8e328f3
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2017
---
# <a name="release-notes"></a>Versionshinweise

## <a name="431-07172017"></a>4.3.1 (07/17/2017)
* Beheben eines Absturzes, der in seltenen Fällen beim Abrufen von `EngagementAgentUtils.isInDedicatedEngagementProcess` auftritt, worauf ebenfalls die `EngagementApplication`-Klasse zugreift.

## <a name="430-06272017"></a>4.3.0 (06/27/2017)
* Android 8-Unterstützung (frühere Versionen des SDK funktionieren nicht auf Android 8).
* Keine Abhängigkeit von der Support-Bibliothek mehr.
* Entfernen der `EngagementFragmentActivity`-Klasse.
* Wegen der [Einschränkungen von Ausführungen im Hintergrund](https://developer.android.com/preview/features/background.html) auf Android 8 können Protokolle im Hintergrund verzögert werden, bis der Benutzer mit dem Gerät interagiert. Dies wirkt sich auf die Statistiken der Pushkampagnen **Delivered** (Übermittelt) und **System notification displayed** (Systembenachrichtigung angezeigt) aus, die verzögert werden, wenn sich das Gerät im Ruhezustand befindet (die Benachrichtigung wird dennoch angezeigt und in Echtzeit ohne Probleme klingeln und vibrieren).
* Wegen der [Einschränkungen des Standorts im Hintergrund](https://developer.android.com/preview/features/background-location-limits.html), wird der Aufenthaltsort in Echtzeit auf Android 8 im Hintergrund nicht regelmäßig aktualisiert.

## <a name="424-03302017"></a>4.2.4 (30.03.2017)
* Die Textfarben von In-App-Benachrichtigungen unter Android 7 wurden so korrigiert, dass sie denen in älteren Android-Versionen entsprechen.

## <a name="423-08102016"></a>4.2.3 (08/10/2016)
* Keine WLAN-Sperre mehr.
* Behebung eines Deadlocks, der auftritt, wenn „getDeviceId“ vor „init“ aufgerufen wird (Fehler seit 4.2.0).

## <a name="422-05172016"></a>4.2.2 (17.05.2016)
* Verbesserungen der Stabilität.

## <a name="421-05102016"></a>4.2.1 (10.05.2016)
* Sicherheit: Der lokale Dateizugriff für die Webansicht wurde deaktiviert.
* Sicherheit: Die Klasse `EngagementPreferenceActivity`, die die veraltete und nicht sichere Klasse `PreferenceActivity` erweitert, wurde entfernt.
* Sicherheit: Für Reach-Aktivitäten kann nun `exported="false"` verwendet werden. Dieses Flag kann auch in vorherigen SDK-Versionen verwendet werden.

## <a name="420-03112016"></a>4.2.0 (03/11/2016)
* Das SDK ist jetzt unter MIT lizenziert.
* Erlauben Sie die Angabe eines benutzerdefinierten Gerätebezeichners während der SDK-Initialisierung.

## <a name="415-02012016"></a>4.1.5 (01.02.2016)
* Verbesserungen der Stabilität.

## <a name="414-01262016"></a>4.1.4 (26.01.2016)
* Verbesserungen der Stabilität.

## <a name="413-1292015"></a>4.1.3 (9.12.2015)
* Verbesserungen der Stabilität.

## <a name="412-11252015"></a>4.1.2 (25.11.2015)
* Verbesserungen der Stabilität.

## <a name="411-11042015"></a>4.1.1 (04.11.2015)
* Verbesserungen der Stabilität.

## <a name="410-08252015"></a>4.1.0 (25.08.2015)
* Behandlung eines neuen Berechtigungsmodells für Android M.
* Konfiguration von Standortfunktionen zur Laufzeit ist nun möglich, anstatt `AndroidManifest.xml` zu verwenden.
* Korrektur eines Berechtigungsfehlers: bei Verwendung von `ACCESS_FINE_LOCATION` wird `ACCESS_COARSE_LOCATION` nicht mehr benötigt.
* Verbesserungen der Stabilität.

## <a name="400-07062015"></a>4.0.0 (06.07.2015)
* Interne Protokolländerungen zur Steigerung der Zuverlässigkeit von Analysen und Push
* Ein systemeigener Push (GCM/ADM) wird jetzt auch für In-App-Benachrichtigungen verwendet, Sie müssen daher die Anmeldeinformationen für den systemeigenen Push für jeden Push-Kampagnentyp konfigurieren.
* Korrektur für Überblick-Benachrichtigung: Es wurde nach dem Push nur 10 angezeigt.
* Beheben eines Fehlers in der Webansicht: durch Klicken auf einen Link wurde auch die Standardaktions-URL ausgeführt.
* Korrektur für seltenen Absturz im Zusammenhang mit der lokalen Speicherverwaltung.
* Korrektur der Zeichenfolgenverwaltung für die dynamische Konfiguration.
* Aktualisierung des Endbenutzer-Lizenzvertrags.

## <a name="300-02172015"></a>3.0.0 (17.02.2015)
* Erste Version von Azure Mobile Engagement
* Die „appId“-Konfiguration wird durch eine Verbindungszeichenfolgen-Konfiguration ersetzt.
* API entfernt, um beliebige XMPP-Nachrichten über beliebige XMPP-Entitäten zu senden und zu empfangen.
* API entfernt, um Nachrichten zwischen Geräten zu senden und zu empfangen.
* Verbesserungen der Sicherheit
* Google Play und die SmartAd-Nachverfolgung wurden entfernt.

