---
title: Azure Mobile Engagement Web SDK-APIs| Microsoft Docs
description: "Die neuesten Updates und Verfahren für das Web SDK für Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8a87d5ac-d8b7-4a0d-bdee-414dbcc561b2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: 54c22ce6a03e382b1bbde102bccc97deec249b30
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>Verwenden der Azure Mobile Engagement-API in einer Webanwendung
Dieses Dokument ist ein Zusatz für das Dokument über die Vorgehensweise zum [Integrieren von Azure Mobile Engagement in eine Webanwendung](mobile-engagement-web-integrate-engagement.md). Es enthält ausführlichere Informationen dazu, wie Sie mithilfe der Azure Mobile Engagement-API Ihre Anwendungsstatistik melden können.

Die Azure Mobile Engagement-API wird vom `engagement.agent` -Objekt bereitgestellt. Das standardmäßige Alias des Azure Mobile Engagement Web SDK lautet `engagement`. Die Definition dieses Alias kann über die SDK-Konfiguration geändert werden.

## <a name="mobile-engagement-concepts"></a>Mobile Engagement-Konzepte
In den folgenden Abschnitten werden allgemeine [Mobile Engagement-Konzepte](mobile-engagement-concepts.md) für die Webplattform ausführlicher behandelt.

### <a name="session-and-activity"></a>`Session` und `Activity`
Wenn der Benutzer zwischen zwei Aktivitäten mehr als zwei Sekunden untätig bleibt, wird diese Folge von Aktivitäten in zwei einzelne Sitzungen unterteilt. Diese Sekunden werden als Sitzungstimeout bezeichnet.

Wenn Ihre Webanwendung das Ende der Benutzeraktivitäten nicht selbst deklariert (durch Aufrufen der `engagement.agent.endActivity` -Funktion), lässt der Mobile Engagement-Server die Benutzersitzung automatisch drei Minuten nach dem Schließen der Anwendungsseite ablaufen. Dies wird als Sitzungstimeout des Servers bezeichnet.

### `Crash`
Standardmäßig werden keine automatisierten Berichte für nicht abgefangene JavaScript-Ausnahmen erstellt. Abstürze können jedoch mithilfe der `sendCrash`-Funktion manuell gemeldet werden. (Weitere Informationen finden Sie im Abschnitt zum Melden von Abstürzen).

## <a name="reporting-activities"></a>Melden von Aktivitäten
Zu den gemeldeten Benutzeraktivitäten zählt etwa das Starten einer neuen Aktivität oder das Beenden der aktuellen Aktivität durch einen Benutzer.

### <a name="user-starts-a-new-activity"></a>Benutzer startet eine neue Aktivität
    engagement.agent.startActivity("MyUserActivity");

Bei jeder Änderung der Benutzeraktivität muss `startActivity()` aufgerufen werden. Der erste Aufruf dieser Funktion startet eine neue Benutzersitzung.

### <a name="user-ends-the-current-activity"></a>Benutzer beendet die aktuelle Aktivität
    engagement.agent.endActivity();

`endActivity()` muss mindestens einmal aufgerufen werden, wenn der Benutzer seine letzte Aktivität beendet. Dadurch wird dem Mobile Engagement Web SDK mitgeteilt, dass der Benutzer derzeit untätig ist und die Benutzersitzung nach Ablauf des Sitzungstimeouts geschlossen werden muss. Wenn Sie vor Ablauf des Sitzungstimeouts `startActivity()` aufrufen, wird die Sitzung fortgesetzt.

Da es für das Schließen des Navigatorfensters keinen verlässlichen Aufruf gibt, ist es innerhalb einer Webumgebung oft schwierig bis unmöglich, das Ende von Benutzeraktivitäten abzufangen. Deshalb wird die Benutzersitzung vom Mobile Engagement-Server automatisch binnen drei Minuten nach Schließen der Anwendungsseite beendet.

## <a name="reporting-events"></a>Melden von Ereignissen
Zu den gemeldeten Ereignissen zählen Sitzungsereignisse und eigenständige Ereignisse.

### <a name="session-events"></a>Sitzungsereignisse
Sitzungsereignisse werden normalerweise verwendet, um die Aktionen eines Benutzers während dessen Sitzung zu melden.

**Beispiel ohne zusätzliche Daten:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**Beispiel mit zusätzlichen Daten:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>Eigenständige Ereignisse
Im Gegensatz zu Sitzungsereignissen können eigenständige Ereignisse außerhalb eines Sitzungskontextes auftreten.

Verwenden Sie in diesem Fall ``engagement.agent.sendEvent`` anstelle von ``engagement.agent.sendSessionEvent``.

## <a name="reporting-errors"></a>Melden von Fehlern
Zu den gemeldeten Fehlern zählen Sitzungsfehler und eigenständige Fehler.

### <a name="session-errors"></a>Sitzungsfehler
Sitzungsfehler werden normalerweise verwendet, um Fehler zu melden, die Auswirkungen auf den Benutzer während dessen Sitzung haben.

**Beispiel ohne zusätzliche Daten:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**Beispiel mit zusätzlichen Daten:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>Eigenständige Fehler
Im Gegensatz zu Sitzungsfehlern können eigenständige Fehler außerhalb des Kontexts einer Sitzung auftreten.

Verwenden Sie in diesem Fall `engagement.agent.sendError` anstelle von `engagement.agent.sendSessionError`.

## <a name="reporting-jobs"></a>Melden von Aufträgen
Meldungen für Aufträge umfassen das Melden von Fehlern und Ereignissen, die während eines Auftrags auftreten, sowie das Melden von Abstürzen.

**Beispiel:**

Wenn Sie eine AJAX-Anforderung überwachen möchten, verwenden Sie Folgendes:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>Melden von Fehlern während eines Auftrags
Fehler können mit einem ausgeführten Auftrag (anstatt mit der aktuellen Benutzersitzung) in Zusammenhang stehen.

**Beispiel:**

Sie möchten einen Fehler melden, wenn eine AJAX-Anforderung nicht erfolgreich ist:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>Melden von Ereignissen während eines Auftrags
Ereignisse können dank der `engagement.agent.sendJobEvent` -Funktion mit einem ausgeführten Auftrag (anstatt mit der aktuellen Benutzersitzung) in Zusammenhang stehen.

Diese Funktion funktioniert genau wie `engagement.agent.sendJobError`.

### <a name="reporting-crashes"></a>Melden von Abstürzen
Mit der `sendCrash` -Funktion können Sie Abstürze manuell melden.

Das `crashid` -Argument ist eine Zeichenfolge zum Angeben der Absturzart.
Das `crash` -Argument ist üblicherweise die Stapelüberwachung für den Absturz als Zeichenfolge.

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>Zusätzliche Parameter
An Ereignisse, Fehler, Aktivitäten oder Aufträge können beliebige Daten angefügt werden.

Bei diesen Daten kann es sich um ein beliebiges JSON-Objekt (aber nicht um ein Array oder einen primitiven Typ) handeln.

**Beispiel:**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>Grenzen
Für zusätzliche Parameter geltende Grenzwerte betreffen reguläre Ausdrücke für Schlüssel, Werttypen und Größe.

#### <a name="keys"></a>Schlüssel
Jeder Schlüssel in dem Objekt muss mit dem folgenden regulären Ausdruck übereinstimmen:

    ^[a-zA-Z][a-zA-Z_0-9]*

Das bedeutet, dass Schlüssel mit mindestens einem Buchstaben beginnen und mit Buchstaben, Ziffern oder Unterstrichen (\_) fortgesetzt werden müssen.

#### <a name="values"></a>Werte
Werte sind auf Zeichenfolgen, Zahlen und boolesche Werte beschränkt.

#### <a name="size"></a>Größe
Zusätzliche Daten sind auf 1024 Zeichen pro Aufruf beschränkt (nachdem das Mobile Engagement Web SDK sie in JSON codiert hat).

## <a name="reporting-application-information"></a>Melden von Anwendungsinformationen
Mithilfe der `sendAppInfo()` -Funktion können Sie manuell Nachverfolgungsinformationen (oder andere anwendungsspezifische Informationen) melden.

Diese Informationen können inkrementell gesendet werden. Für ein spezifisches Gerät wird immer nur der letzte Wert für einen spezifischen Schlüssel beibehalten.

Genau wie bei den zusätzlichen Daten zu Ereignissen können Sie zum Abstrahieren der Anwendungsinformationen ein beliebiges JSON-Objekt verwenden. Dabei werden Arrays und untergeordnete Objekte als flache Zeichenfolgen (mit JSON-Serialisierung) behandelt.

**Beispiel:**

Hier sehen Sie ein Codebeispiel für die Übermittlung von Geschlecht und Geburtsdatum des Benutzers:

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>Grenzen
Für Anwendungsinformationen geltende Grenzwerte betreffen reguläre Ausdrücke für Schlüssel und die Größe.

#### <a name="keys"></a>Schlüssel
Jeder Schlüssel in dem Objekt muss mit dem folgenden regulären Ausdruck übereinstimmen:

    ^[a-zA-Z][a-zA-Z_0-9]*

Das bedeutet, dass Schlüssel mit mindestens einem Buchstaben beginnen und mit Buchstaben, Ziffern oder Unterstrichen (\_) fortgesetzt werden müssen.

#### <a name="size"></a>Größe
Anwendungsinformationen sind auf 1024 Zeichen pro Aufruf beschränkt (nachdem das Mobile Engagement Web SDK sie in JSON codiert hat).

Im vorherigen Beispiel umfasst der an den Server gesendete JSON-Code 44 Zeichen:

    {"birthdate":"1983-12-07","gender":"female"}
