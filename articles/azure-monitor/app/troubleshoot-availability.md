---
title: Behandeln von Problemen mit Azure Application Insights-Verfügbarkeitstests | Microsoft-Dokumentation
description: Behandeln von Problemen mit Webtests in Azure Application Insights Erhalten Sie Benachrichtigungen, wenn eine Website nicht mehr zur Verfügung steht oder langsam reagiert.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: 87bc87d7d105d581f0143e87044fb0337c0fd7f6
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304754"
---
# <a name="troubleshooting"></a>Problembehandlung

Dieser Artikel soll Ihnen beim Behandeln von häufig bei der Verfügbarkeitsüberwachung auftretenden Problemen helfen.

## <a name="ssltls-errors"></a>SSL-/TLS-Fehler

|Symptom/Fehlermeldung| Mögliche Ursachen|
|--------|------|
|Es konnte kein sicherer SSL-/TLS-Kanal erstellt werden.  | SSL-Version. Es werden nur die TLS-Versionen 1.0, 1.1 und 1.2 unterstützt **SSLv3 wird nicht unterstützt.**
|TLSv1.2-Datensatzebene: Warnung (Stufe: Schwerwiegend, Beschreibung: Ungültiger MAC-Datensatz)| [Weitere Informationen](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake) finden Sie im StackExchange-Thread.
|Fehlgeschlagene URL verweist auf ein CDN (Content Delivery Network) | Dieses Fehler kann durch einen Konfigurationsfehler auf Ihrem CDN hervorgerufen werden. |  

### <a name="possible-workaround"></a>Mögliche Problemumgehung

* Wenn die URLs, für die die Probleme bestehen, immer auf Ressourcen verweisen, wird empfohlen, die Option **Abhängige Anforderungen analysieren** für Webtests zu deaktivieren.

## <a name="test-fails-only-from-certain-locations"></a>Der Test schlägt nur fehl, wenn er an bestimmten Orten ausgeführt wird

|Symptom/Fehlermeldung| Mögliche Ursachen|
|----|---------|
|Ein Verbindungsversuch ist fehlgeschlagen, weil die Partei, mit der eine Verbindung hergestellt werden sollte, in einem bestimmten Zeitraum nicht reagiert hat.  | Test-Agents werden an bestimmten Orten von einer Firewall blockiert.|
|    |Die Umleitung von bestimmten IP-Adressen erfolgt über (Lastenausgleichsmodule, Geo-Traffic-Manager, Azure ExpressRoute) 
|    |Bei der Verwendung von Azure ExpressRoute können Pakete im Fall von [asymmetrischem Routing](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) gelöscht werden.|

## <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Ein zeitweiliger Testfehler aufgrund einer Protokollverletzung

|Symptom/Fehlermeldung| Mögliche Ursachen|
|----|---------|
Verletzung des Protokolls „Auf CR muss LF folgen“ | Dieses Problem entsteht, wenn falsch formatierte Header gefunden werden. Genauer gesagt geben einige Header unter Umständen das Zeilenende nicht mit CRLF an, was gegen die HTTP-Spezifikation verstößt. Daher schlägt die Validierung auf .NET-WebRequest-Ebene fehl.
 || Der Fehler kann außerdem durch Lastenausgleichsmodule oder CDNs verursacht werden.

> [!NOTE]
> Bei der URL tritt in Browsern mit einer nicht so strengen Validierung von HTTP-Headern unter Umständen kein Fehler auf. Eine ausführliche Erläuterung des Problems finden Sie in diesem Blogbeitrag: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/.  

## <a name="common-troubleshooting-questions"></a>Allgemeine Fragen zur Problembehandlung

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Die Website sieht korrekt aus, aber ich sehe Testfehler. Warum warnt mich Application Insights?

   * Ist für Ihren Test die Option **Abhängige Anforderungen analysieren** aktiviert? Dies führt zu einer strengen Überprüfung von Ressourcen wie Skripts, Bildern usw. Diese Typen von Fehlern machen sich in einem Browser möglicherweise nicht bemerkbar. Überprüfen Sie alle Bilder, Skripts, Stylesheets und anderen Dateien, die von der Seite geladen werden. Wenn eines dieser Elemente einen Fehler verursacht, wird der Test auch dann als fehlerhaft gemeldet, wenn die HTML-Hauptseite problemlos geladen wird. Deaktivieren Sie einfach in der Testkonfiguration die Option „Abhängige Anforderungen analysieren“, um solche Ressourcenfehler vom Test auszuschließen.

   * Stellen Sie sicher, dass die Konfigurationsoption „Enable retries for test failures“ (Weitere Versuche bei Testfehlern zulassen) aktiviert ist, um Störungen infolge vorübergehender Netzwerkprobleme zu verringern. Sie können den Test auch an mehreren Standorten durchführen und den Schwellenwert der Warnungsregel entsprechen verwalten, um zu verhindern, dass standortspezifische Probleme übermäßige Warnungen auslösen.

   * Klicken Sie auf einen der roten Punkte in der Verfügbarkeitserfahrung oder auf einen Verfügbarkeitsfehler im Such-Explorer, um die Details anzuzeigen, warum wir den Fehler gemeldet haben. Das Testergebnis sollte, zusammen mit der korrelierten serverseitigen Telemetrie (sofern aktiviert), Aufschluss darüber geben, warum der Test nicht erfolgreich war. Häufige Ursachen für vorübergehende Probleme sind Netzwerk- oder Verbindungsprobleme.

   * Kam es beim Test zum Timeout? Wir brechen Tests nach 2 Minuten ab. Wenn Ihr Ping- oder mehrstufiger Test länger als zwei Minuten dauert, melden wir ihn als Fehler. Erwägen Sie, den Test in mehrere aufzuteilen, die sich schneller abschließen lassen.

   * Haben allel Standorte Fehler gemeldet oder nur einige? Wenn nur einige Standorte Fehler gemeldet haben, kann es an Netzwerk-/CDN-Problemen liegen. Auch in diesem Fall sollte das Klicken auf die roten Punkte Ihnen zu einem besseren Verständnis verhelfen, warum der Standort Fehler gemeldet hat.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Ich habe keine E-Mail erhalten, als die Warnung ausgelöst und/oder behoben wurde.

Überprüfen Sie die klassische Warnungskonfiguration, um zu bestätigen, dass Ihre E-Mail-Adresse direkt aufgeführt ist, oder dass eine Verteilerliste, deren Mitglied Sie sind, für den Empfang von Benachrichtigungen konfiguriert ist. Ist dies der Fall, überprüfen Sie die Konfiguration der Verteilerliste, um zu bestätigen, dass sie externe E-Mails empfangen kann. Überprüfen Sie außerdem, ob Ihr E-Mail-Administrator eventuell Richtlinien konfiguriert hat, die dieses Problem verursachen können.

### <a name="i-did-not-receive-the-webhook-notification"></a>Ich habe die Webhookbenachrichtigung nicht empfangen.

Überprüfen Sie, ob die Anwendung, die die Webhookbenachrichtigung empfängt, verfügbar ist und die Webhookanforderungen erfolgreich verarbeitet. Weitere Informationen finden Sie [hier](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook).

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Ein zeitweiliger Testfehler aufgrund einer Protokollverletzung ist aufgetreten.

Der Fehler („protocol violation..CR must be followed by LF“) weist auf ein Problem mit dem Server (oder mit Abhängigkeiten) hin. Er wird angezeigt, wenn falsch formatierte Header in der Antwort festgelegt werden. Der Fehler kann durch Lastenausgleichsmodule oder CDNs verursacht werden. Genauer gesagt geben einige Header unter Umständen das Zeilenende nicht mit CRLF an, was gegen die HTTP-Spezifikation verstößt. Daher schlägt die Validierung auf .NET-WebRequest-Ebene fehl. Überprüfen Sie die Antwort, um Header zu ermitteln, die möglicherweise gegen diese Spezifikation verstoßen.

> [!NOTE]
> Bei der URL tritt in Browsern mit einer nicht so strengen Validierung von HTTP-Headern unter Umständen kein Fehler auf. Eine ausführliche Erläuterung des Problems finden Sie in diesem Blogbeitrag: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/.  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Ich sehe keine zugehörigen serverseitigen Telemetriedaten zum Diagnostizieren von Testfehlern.*

Wenn Sie Application Insights für Ihre serverseitige Anwendung eingerichtet haben, liegt dies möglicherweise daran, dass [Sampling](../../azure-monitor/app/sampling.md) in Betrieb ist. Wählen Sie ein anderes Verfügbarkeitsergebnis aus.

### <a name="can-i-call-code-from-my-web-test"></a>Kann ich Code aus meinem Webtest aufrufen?

Nein. Die Schritte des Tests müssen in der Webtest-Datei enthalten sein. Und Sie können keine anderen Webtests aufrufen oder Schleifen verwenden. Aber es gibt mehrere hilfreiche Plug-Ins.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Gibt es einen Unterschied zwischen „Webtests“ und „Verfügbarkeitstests“?

Die beiden Begriffe sind austauschbar. „Verfügbarkeitstests“ ist ein allgemeinerer Begriff, der neben den mehrstufigen Webtests auch die einzelnen URL-Pingtests enthält.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Ich möchte Verfügbarkeitstests auf unserem internen Server verwenden, der hinter einer Firewall ausgeführt wird.

   Es gibt zwei mögliche Lösungen:

   * Konfigurieren Sie die Firewall so, dass eingehende Anforderungen von den [IP-Adressen der Webtest-Agents](../../azure-monitor/app/ip-addresses.md) zugelassen werden.
   * Schreiben Sie eigenen Code zum regelmäßigen Testen Ihres internen Servers. Führen Sie den Code als Hintergrundprozess auf einem Testserver hinter Ihrer Firewall aus. Die Ergebnisse des Testvorgangs können an Application Insights gesendet werden, indem die [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)-API im Core SDK-Paket verwendet wird. Hierfür ist es erforderlich, dass Ihr Testserver Zugriff in ausgehender Richtung auf den Application Insights-Erfassungsendpunkt hat. Dies ist aber ein deutlich geringeres Sicherheitsrisiko als bei der Alternativlösung, bei der eingehende Anforderungen zugelassen werden. Die Ergebnisse werden nicht auf den Blättern der Verfügbarkeitswebtests angezeigt, sondern als Verfügbarkeitsergebnisse in Analytics, Search und Metrik-Explorer.

### <a name="uploading-a-multi-step-web-test-fails"></a>Fehler beim Hochladen eines mehrstufigen Webtests

Einige Gründe, warum dies geschehen kann:
   * Die Größenbeschränkung beträgt 300 K.
   * Schleifen werden nicht unterstützt.
   * Verweise auf andere Webtests werden nicht unterstützt.
   * Datenquellen werden nicht unterstützt.

### <a name="my-multi-step-test-doesnt-complete"></a>Mein mehrstufiger Test wird nicht abgeschlossen.

Pro Test können maximal 100 Anforderungen verwendet werden. Der Test wird außerdem beendet, wenn seine Ausführung länger als zwei Minuten dauert.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Wie kann ich einen Test mit Clientzertifikaten durchführen?

Dies wird derzeit nicht unterstützt.

## <a name="who-receives-the-classic-alert-notifications"></a>Wer erhält die (klassischen) Warnungsbenachrichtigungen?

Dieser Abschnitt gilt nur für klassische Benachrichtigungen und hilft Ihnen, Ihre Warnungsbenachrichtigungen zu optimieren, um sicherzustellen, dass nur die gewünschten Empfänger Benachrichtigungen erhalten. Um mehr über den Unterschied zwischen [klassischen Benachrichtigungen](../platform/alerts-classic.overview.md) und der neuen Benutzeroberfläche für Warnungen zu erfahren, lesen Sie den Artikel [Überblick über Warnungen in Microsoft Azure](../platform/alerts-overview.md). Um die Warnungsbenachrichtigung in der neuen Benutzeroberfläche für Warnungen zu steuern, verwenden Sie [Aktionsgruppen](../platform/action-groups.md).

* Wir empfehlen die Verwendung bestimmter Empfänger für klassische Warnungsbenachrichtigungen.

* Für Warnungen zu Fehlern bei X von Y Standorten werden bei aktivierter Option **Massenversand/Gruppe** Benutzer mit Administrator-/Co-Administratorrolle benachrichtigt.  Im Wesentlichen erhalten _alle_ Administratoren des _Abonnements_ Benachrichtigungen.

* Für Warnungen zu Verfügbarkeitsmetriken werden bei aktivierter Option **bulk/group** (Masse/Gruppe) Benachrichtigungen an Benutzer mit der Rolle „Besitzer“, „Mitwirkender“ oder „Leser“ im Abonnement gesendet. Tatsächlich sind _alle_ Benutzer mit Zugriff auf das Abonnement der Application Insights-Ressource im Umfang enthalten und erhalten Benachrichtigungen. 

> [!NOTE]
> Wenn Sie aktuell die Option **Massenversand/Gruppe** verwenden und diese deaktivieren, können Sie die Änderung nicht rückgängig machen.

Verwenden Sie die neue Benutzeroberfläche für Warnungen/Warnungen nahezu in Echtzeit, wenn Sie Benutzer basierend auf ihren Rollen benachrichtigen müssen. Mit [Aktionsgruppen](../platform/action-groups.md) können Sie E-Mail-Benachrichtigungen für Benutzer mit den Rollen „Mitwirkender“, „Besitzer“, „Leser“ konfigurieren (nicht kombiniert als eine einzige Option).

## <a name="next-steps"></a>Nächste Schritte

* [Multi-step web testing (Mehrstufiger Webtest)](availability-multistep.md)
* [URL ping tests (URL-Pingtests)](monitor-web-app-availability.md)
