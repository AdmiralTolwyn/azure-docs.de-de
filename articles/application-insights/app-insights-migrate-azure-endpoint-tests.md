---
title: "Migrieren von Azure-Endpunkttests zu Application Insights-Verfügbarkeitstests"
description: "Migrieren der klassischen Azure-Endpunktüberwachungs-Tests zu neuen Application Insights-Verfügbarkeitstests bis zum 31. Oktober 2016."
services: application-insights
documentationcenter: 
author: soubhagyadash
manager: douge
ms.assetid: 74cc1ddf-1b70-4ad2-bdb5-1bd895937861
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: 9b3cf88473cd3f4e4e5a383dfdadfe06d4f34f36


---
# <a name="moving-from-azure-endpoint-monitoring-to-application-insights-availability-tests"></a>Verschieben von Azure-Endpunktüberwachung nach Application Insights-Verfügbarkeitstests
Verwenden Sie die [Endpunktüberwachung](https://blogs.msdn.microsoft.com/mast/2013/03/03/windows-azure-portal-update-configure-web-endpoint-status-monitoring-preview/) für Ihre Azure-Apps? Bis zum *31. Oktober 2016* wird sie durch die neuen und leistungsfähigeren [Verfügbarkeitstests](app-insights-monitor-web-app-availability.md) ersetzt. Wir haben bereits einige neue Tests erstellt, obwohl sie bis zum 31. Oktober 2016 deaktiviert sind. 

Sie können die neuen Tests bearbeiten und den Wechsel selbst vollziehen, wenn Sie möchten. Sie finden sie im [Azure-Portal](https://portal.azure.com) in der Default-ApplicationInsights-CentralUS-Ressourcengruppe.

## <a name="what-are-availability-tests"></a>Was sind Verfügbarkeitstests?
Verfügbarkeitstests sind ein Feature von Azure, das fortlaufend überprüft, ob eine Website oder ein Dienst ausgeführt wird, indem von bis zu 16 Standorten weltweit HTTP-Anforderungen (einzelne Ping-Tests oder Visual Studio-Webtests) an die Website bzw. den Dienst gesendet werden. 

Im [klassischen Azure-Portal](https://manage.windowsazure.com)wurden diese Tests als „Endpunktüberwachung“ bezeichnet. Sie waren im Umfang stärker beschränkt. Die neuen Verfügbarkeitstests wurden wesentlich verbessert:

* Bis zu 10 Visual Studio-Webtests oder Ping-Tests pro Application Insights-Ressource. 
* Bis zu 16 Standorte weltweit zum Senden von Testanforderungen an Ihre Web-App. Bessere Steuerung der Testerfolgskriterien. 
* Jede Website bzw. jeder Dienst wird getestet – nicht nur Azure-Web-Apps.
* Erneute Testversuche: Falsch positive Warnungen aufgrund vorübergehender Netzwerkprobleme werden reduziert. 
* Webhooks können HTTP POST-Benachrichtigungen für die Warnungen empfangen.

![](./media/app-insights-migrate-azure-endpoint-tests/16-1test.png)

Weitere Informationen zu Verfügbarkeitstests finden Sie [hier](app-insights-monitor-web-app-availability.md).

Verfügbarkeitstests sind Teil von [Azure Application Insights](app-insights-overview.md), einem erweiterbaren Analysedienst für jede Webanwendung.

## <a name="so-whats-happening-to-my-endpoint-tests"></a>Was passiert denn nun mit meinen Endpunkttests?
* Wir haben Ihre Endpunktüberwachungs-Tests in die neuen Application Insights-Verfügbarkeitstests kopiert.
* Die neuen Verfügbarkeitstests sind derzeit deaktiviert, und die alten Endpunkttests werden weiterhin ausgeführt.
* Die Warnungsregeln wurden *nicht* migriert. Die neuen Tests wurden ursprünglich mit einer Standardregel eingerichtet:
  * Auslösung einer Warnung erfolgt, wenn mehrere Standorte in 5 Minuten Fehler melden.
  * An die Abonnementadministratoren wird eine E-Mail gesendet.

Im [Azure-Portal](https://portal.azure.com)finden Sie die migrierten Tests in der Default-ApplicationInsights-CentralUS-Ressourcengruppe. Die Testnamen tragen das Präfix „Migrated-“. 

## <a name="what-do-i-need-to-do"></a>Was muss ich tun?
* Wenn wir versäumt haben, Ihre Tests zu migrieren, lassen sich die neuen Verfügbarkeitstests [mühelos einrichten](app-insights-monitor-web-app-availability.md).

### <a name="option-a-do-nothing-leave-it-to-us"></a>Option A: Sie unternehmen gar nichts. Überlassen Sie alles uns.
**Am 31. Oktober 2016** werden wir:

* Die alten Endpunkttests deaktivieren.
* Die migrierten Verfügbarkeitstests aktivieren.

### <a name="option-b-you-manage-andor-enable-the-new-tests"></a>Option B: Sie verwalten und/oder aktivieren die neuen Tests.
* Überprüfen und bearbeiten Sie die neuen Verfügbarkeitstests im neuen [Azure-Portal](https://portal.azure.com). 
  * Überprüfen Sie die Auslösekriterien
  * Überprüfen Sie die E-Mail-Empfänger
* Aktivieren Sie die neuen Tests
* Wir deaktivieren die veralteten Endpunktüberwachungs-Tests am 31. Oktober 2016. 

### <a name="option-c-opt-out"></a>Option C: Sie lehnen ab.
Wenn Sie keine Verfügbarkeitstests verwenden möchten, können Sie sie im [Azure-Portal](https://portal.azure.com)löschen. Sie finden auch einen Link zum Abbestellen am unteren Rand der E-Mail-Benachrichtigungen.

Wir werden die alten Endpunkttests am 31. Oktober 2016 dennoch löschen. 

## <a name="how-do-i-edit-the-new-tests"></a>Wie bearbeite ich die neuen Tests?
Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu den „Migrated-“-Webtests: 

![Wählen Sie „Ressourcengruppen“, „Default-ApplicationInsights-CentralUS“, und öffnen Sie die „migrated-“-Tests.](./media/app-insights-migrate-azure-endpoint-tests/20.png)

Bearbeiten und/oder aktivieren Sie den Test:

![](./media/app-insights-migrate-azure-endpoint-tests/21.png)

## <a name="why-is-this-happening"></a>Warum ist das so?
Besserer Service. Der alte Endpunktdienst war viel begrenzter. Sie konnten nur zwei URLs für einfache Ping-Tests von drei geografischen Standorten für eine Azure-VM oder Web-App angeben. Die neuen Tests können mehrstufige Webtests von bis zu 16 Standorten ausführen, und Sie können bis zu 10 Tests für eine Anwendung angeben. Sie können jede beliebige URL testen – es muss keine Azure-Website sein.

Die neuen Tests werden separat von der Web-App oder VM aus konfiguriert, die Sie testen. 

Wir migrieren die Tests, um sicherzustellen, dass Sie bei der Verwendung des neuen Portals die Kontrolle über sie behalten. 

## <a name="what-is-application-insights"></a>Was ist Application Insights?
Die neuen Verfügbarkeitstests sind Teil von [Azure Application Insights](app-insights-overview.md). Hier ist ein [2-Minuten-Video](http://go.microsoft.com/fwlink/?LinkID=733921).

## <a name="am-i-paying-for-the-new-tests"></a>Muss ich für die neuen Tests bezahlen?
Die migrierten Tests werden in einer Application Insights-Ressource im standardmäßigen Plan für kostenlose Leistungen eingerichtet. Dies ermöglicht die Erfassung von bis zu 5 Millionen Datenpunkten. Damit ist das Datenvolumen, mit dem Ihre Tests derzeit arbeiten, mühelos abgedeckt. 

Wenn Sie natürlich Application Insights gern einsetzen und weitere Verfügbarkeitstests erstellen, oder weitere seiner Leistungsüberwachungs- und Diagnosefunktionen übernehmen, generieren Sie mehr Datenpunkte.  Das Ergebnis wäre jedoch nur, dass Sie das Kontingent für den kostenlosen Plan ausschöpfen könnten. Eine Rechnung erhalten Sie nur, wenn Sie sich für die Standard- oder Premium-Pläne entscheiden. 

[Erfahren Sie mehr über Application Insights-Preise und Überwachung des Kontingents](app-insights-pricing.md). 

## <a name="what-is-and-isnt-migrated"></a>Was wird migriert – und was nicht?
Dies wird von Ihren alten Endpunkttests beibehalten:

* Endpunkt-URL, die getestet werden soll.
* Geografische Standorte, von denen aus die Anforderungen gesendet werden.
* Die Testhäufigkeit bleibt bei 5 Minuten.
* Die Testzeitüberschreitung bleibt bei 30 Sekunden. 

Nicht migriert:

* Warnungsauslöseregel. Die Regel, die wir eingerichtet haben, löst eine Warnung aus, wenn 1 Standort mehrere Fehler in 5 Minuten meldet.
* Warnungsempfänger. E-Mail-Benachrichtigungen werden an die Abonnementbesitzer und Mitbesitzer gesendet. 

## <a name="how-do-i-find-the-new-tests"></a>Wie finde ich die neuen Tests?
Sie können jeden der neuen Tests jetzt bearbeiten, wenn Sie möchten. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, öffnen Sie **Ressourcengruppen**, und wählen Sie **Default-ApplicationInsights-CentralUS**. In dieser Gruppe finden Sie die neuen Webtests. [Erfahren Sie mehr über die neuen Verfügbarkeitstests](app-insights-monitor-web-app-availability.md).

Beachten Sie, dass die neuen Warnungs-E-Mails von dieser Adresse gesendet werden: App Insights Alerts (ai-noreply@microsoft.com).

## <a name="what-happens-if-i-do-nothing"></a>Was geschieht, wenn ich nichts unternehme?
Option A gilt. Wir aktivieren die migrierten Tests und richten die Standardwarnungsregeln wie oben erwähnt ein. Sie müssen benutzerdefinierte Warnregeln, Empfänger wie oben erwähnt hinzufügen. Wir deaktivieren die veralteten Endpunktüberwachungs-Tests. 

## <a name="where-can-i-provide-feedback-on-this"></a>Wo kann ich Feedback hierzu abgeben?
Wir schätzen Ihr Feedback! Bitte [senden Sie uns eine E-Mail](mailto:vsai@microsoft.com). 




<!--HONumber=Nov16_HO3-->


