<properties
	pageTitle="Ich verwende Mobile Services – welche Vorteile bietet App Service?"
	description="Erfahren Sie, welche Vorteile App Service für Ihre vorhandenen Mobile Services-Projekte bietet."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/03/2016"
	ms.author="krisragh"/>

# <a name="getting-started"> </a>Ich verwende Mobile Services. Welche Vorteile bietet App Service?

##Übersicht
Ihr vorhandener Mobile Service ist sicher und wird weiterhin unterstützt. Die *Azure App Service*-Plattform bietet für Ihre mobile App jedoch eine Reihe von Vorteilen, die mit Mobile Services derzeit nicht verfügbar sind:

- Einfacheres und kostengünstigeres Angebot für Apps, die sowohl Web- als auch mobile Clients enthalten
- Neue Hostfunktionen, einschließlich Webaufträgen, benutzerdefinierter CNames, besserer Überwachung
- Gebrauchsfertige Integration in Traffic Manager
- Konnektivität mit lokalen Ressourcen und VPNs mit VNet zusätzlich zu Hybridverbindungen
- Überwachung, Warnungen und Problembehandlung für Ihre App mit NewRelic oder AppInsights
- Umfangreicheres Spektrum an zugrunde liegenden Computeressourcen und Preisen
- Integrierte automatische Skalierung, Lastenausgleich und Leistungsüberwachung
- Integrierte Funktionen für Staging, Sicherung, Rollback und Produktionstests

## Neue Hostingfunktionen
In *Azure App Service* wird der *Mobile App*-Back-End-Code in demselben Container ausgeführt wie Web-App und API-App. So können Sie alle Funktionen in diesem Container nutzen, einschließlich einiger, die derzeit nicht in Mobile Services vorhanden sind:

- Hinzufügen ständig ausgeführter Back-End-Logik über Webaufträge
- Sicherstellen der ständigen Ausführung des Back-End-Codes
- Verwenden benutzerdefinierter CNames zum Bereitstellen benutzerfreundlicher und stabiler Namen für Ihre mobilen Back-End-Endpunkte
- Geoskalierung Ihrer App mit Traffic Manager
- Fügen Sie alle Bibliotheken und Pakete hinzu, die Sie möchten.
- (Für .NET) Nutzen Sie alle Features von ASP.NET, einschließlich MVC
- (For Node.js) Nutzen Sie jede reine JavaScript-Bibliothek des Knotenökosystems, einschließlich gemeinsamer MVC-Bibliotheken.

##Zugriff auf lokale Daten über VNet
Mit Mobile Services können Sie schon heute Hybridverbindungen für den Zugriff auf lokale Ressourcen verwenden. Es gibt jedoch Situationen, in denen eine VPN-Lösung bevorzugt wird. Mit *Azure App Service* können Sie Azure-VNet für Mobile App-Back-End-Code verwenden.

##Verwenden Ihrer bevorzugten Back-End-Sprache
*Azure App Service* bietet umfangreichere Unterstützung mit mehr Funktionen für ASP.NET- und Node.js-Plattformen, einschließlich Zugriff auf die neuesten Laufzeiten.

##Einrichten der automatischen Skalierung
Mit Mobile Services wurden alle Instanzen des Back-End-Codes auf kleinen virtuellen Computern ausgeführt. Mit *Azure App Service* können Sie die Größe der virtuellen Computer aus einem umfassenderen Satz von Optionen auswählen. Sie können zudem schnell zentral oder horizontal hochskalieren, um die gesamte von den Kunden eingehende Workload basierend auf verschiedenen Leistungsmetriken zu bewältigen.

##Auf dem Laufenden bleiben
Reagieren Sie auf Probleme in Echtzeit mithilfe von Überwachung und Warnungen, durch die Sie und Ihr Team automatisch benachrichtigt werden. Integrieren Sie die erweiterten App-Analyse- und Überwachungsfunktionen von New Relic und AppInsights, und erhalten Sie noch umfangreichere Informationen zur Leistung Ihrer mobilen App. Mit *Azure App Service* können Sie jetzt programmgesteuert oder über das Azure-Portal Warnungen basierend auf verschiedenen Leistungsmetriken einrichten.

##Schützen Ihrer Ressourcen
Sichern Sie automatisch Back-End und Datenbank. Ihr Code und Ihre Daten werden in Notfällen geschützt und können problemlos wiederhergestellt werden, sodass Sie sich keine Sorgen um Ihr Unternehmen machen müssen.

##Auf die Plätze, Staging, los!
Mit *Azure App Service* können Sie jetzt mehrere private Test- und Stagingumgebungen für Ihre mobilen Apps erstellen. Verwenden Sie sie zum Durchführen von Tests vor der Bereitstellung. Wechseln Sie ohne Ausfallzeiten in die Produktion. Web-Apps werden vorab geladen, um höchste Kundenzufriedenheit zu gewährleisten.

Sie können mithilfe dieses [Tutorials](app-service-mobile-migrating-from-mobile-services.md) die Vorteile von *App Service* für Ihre vorhandenen Mobile Service-Projekte nutzen.

<!---HONumber=AcomDC_0511_2016-->