<properties 
	pageTitle="Einführung in die App Service-Umgebung" 
	description="Lernen Sie die App Service-Umgebung kennen, die sichere, mit dem VNet verknüpfte, dedizierte Skalierungseinheiten für die Ausführung all Ihrer Apps bietet." 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/11/2016"
	ms.author="stefsch"/>

# Einführung in die App Service-Umgebung

## Übersicht ##
Eine App Service-Umgebung ist eine Option des [Premium][PremiumTier]-Tarifs von Azure App Service, die eine vollständig isolierte und dedizierte Umgebung zur sicheren Ausführung zahlreicher Azure App Service-Apps einschließlich [Web-Apps][WebApps], [Mobile Apps][MobileApps] und [API-Apps][APIApps] bereitstellt.

App Service-Umgebungen sind ideal für Anwendungsworkloads mit folgenden Anforderungen:

- Unterstützung sehr vieler Apps
- Isolierung und sicherer Netzwerkzugriff

Kunden können mehrere App Service-Umgebungen innerhalb einer einzelnen Azure-Region sowie über mehrere Azure-Regionen verteilt einrichten. Dadurch eignen sich App Service-Umgebungen hervorragend für die horizontale Skalierung zustandsloser Anwendungsebenen zur Unterstützung hoher RPS-Workloads.

Aufgrund der Isolierung werden in App Service-Umgebungen nur Anwendungen eines einzelnen Kunden ausgeführt. Die Umgebungen werden zudem immer in einem virtuellen Netzwerk bereitgestellt. Kunden haben eine genauere Kontrolle über den eingehenden und ausgehenden Anwendungs-Netzwerkdatenverkehr, und Anwendungen können über virtuelle Netzwerke sichere Hochgeschwindigkeitsverbindungen mit lokalen Unternehmensressourcen herstellen.

Alle Artikel und Anleitungen zu App Service-Umgebungen stehen in der [Dokumentation zu App Service-Umgebungen](../app-service/app-service-app-service-environments-readme.md) zur Verfügung.

Eine Übersicht darüber, wie App Service-Umgebungen hochskalierbaren und sicheren Netzwerkzugriff ermöglichen, finden Sie in der [ausführlichen Betrachtung zu AzureCon][AzureConDeepDive] in App Service-Umgebungen.

Eine ausführliche Betrachtung von horizontaler Skalierung mit mehreren App Service-Umgebungen finden Sie im Artikel zum Einrichten einer [geografisch verteilten App][GeodistributedAppFootprint].

Informationen darüber, wie die in AzureCon Deep Dive gezeigte Sicherheitsarchitektur konfiguriert wurde, finden Sie im Artikel über das Implementieren einer [Mehrschicht-Sicherheitsarchitektur](app-service-app-service-environment-layered-security.md) in App Service-Umgebungen.

Der Zugriff von Apps für App-Umgebungen kann durch Upstreamgeräte wie z. B. Web Application Firewalls (WAF) abgegrenzt werden. Im Artikel zum [Konfigurieren einer WAF für App-Umgebungen](app-service-app-service-environment-web-application-firewall.md) wird dieses Szenario behandelt.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Dedizierte Serverressourcen ##
Alle Serverressourcen in einer App Service-Umgebung sind ausschließlich für ein einzelnes Abonnement reserviert. Eine App Service-Umgebung kann mit bis zu fünfzig (50) Serverressourcen konfiguriert werden, die ausschließlich von einer einzelnen Anwendung genutzt werden.

Eine App Service-Umgebung besteht aus einem Front-End-Serverressourcenpool sowie ein bis drei Worker-Serverressourcenpools.

Der Front-End-Pool enthält Serverressourcen, die für die SSL-Beendigung sowie für den automatischen Lastenausgleich von App-Anforderungen in einer App Service-Umgebung zuständig sind.

Jeder Workerpool enthält Computeressourcen, die [App Service-Plänen][AppServicePlan] zugeordnet sind, die wiederum eine oder mehrere Azure App Service-Apps enthalten. Da in einer App Service-Umgebung bis zu drei verschiedenen Workerpools vorhanden sein können, können Sie flexibel verschiedene Serverressourcen für jeden Workerpool auswählen.

Beispielsweise können Sie einen Workerpool mit weniger leistungsfähigen Serverressourcen für App Service-Pläne erstellen, die für Entwicklungs- oder Test-Apps vorgesehen sind. Ein zweiter (oder sogar dritter) Workerpool kann leistungsfähigere Serverressourcen für App Service-Pläne nutzen, die für Produktions-Apps ausgeführt werden.

Ausführliche Informationen über die Menge von Computeressourcen, die den Front-End- und Workerpools zur Verfügung stehen, finden Sie unter [Konfigurieren einer App Service-Umgebung][HowToConfigureanAppServiceEnvironment].

Weitere Informationen zu den verfügbaren Computeressourcengrößen, die in einer App Service-Umgebung unterstützt werden, finden Sie auf der Seite [App Service-Preise][AppServicePricing]. Sehen Sie sich die verfügbaren Optionen für App Service-Umgebungen im Premium-Tarif an.

## Unterstützung für virtuelle Netzwerke ##
Eine App Service-Umgebung kann **entweder** in einem virtuellen Netzwerk von Azure Resource Manager **oder** einem virtuellen Netzwerk eines klassischen Bereitstellungsmodells ([weitere Informationen zu virtuellen Netzwerken][MoreInfoOnVirtualNetworks]) erstellt werden. Da eine App Service-Umgebung sich immer in einem virtuellen Netzwerk, genauer gesagt, in einem Subnetz eines virtuellen Netzwerks befindet, können Sie die Sicherheitsfunktionen virtueller Netzwerke zum Steuern sowohl der eingehenden als auch der ausgehenden Netzwerkkommunikation nutzen.

Mithilfe von [Netzwerksicherheitsgruppen][NetworkSecurityGroups] können Sie die eingehende Netzwerkkommunikation mit dem Subnetz einschränken, das eine App Service-Umgebung enthält. Dadurch können Sie Apps hinter Upstreamgeräten und -diensten ausführen wie z. B. Web Application Firewalls und Netzwerk-SaaS-Anbietern.

Apps müssen häufig auch auf Unternehmensressourcen wie interne Datenbanken und Webdienste zugreifen. Ein gängiger Ansatz besteht darin, diese Endpunkte nur für internen Netzwerkdatenverkehr verfügbar zu machen, der innerhalb eines virtuellen Azure-Netzwerks fließt. Sobald eine App Service-Umgebung demselben virtuellen Netzwerk angeschlossen wird wie die internen Dienste, können die in der Umgebung ausgeführten Apps auf diese zugreifen. Dies gilt auch für Endpunkte, die über [Site-to-Site-][SiteToSite] und [Azure ExpressRoute][ExpressRoute]-Verbindungen erreichbar sind.

Für weitere Details zur Funktionsweise der App Service-Umgebungen mit virtuellen und lokalen Netzwerken finden Sie in den folgenden Artikeln [Übersicht über die Netzwerkarchitektur von App Service-Umgebungen][NetworkArchitectureOverview], [Steuern von eingehendem Datenverkehr in eine App Service-Umgebung][ControllingInboundTraffic] und [Sicheres Verbinden mit Back-End-Ressourcen von einer App Service-Umgebung aus][SecurelyConnectingToBackends].

## Erste Schritte

Informationen zum Einstieg in App Service-Umgebungen finden Sie unter [Erstellen einer App Service-Umgebung][HowToCreateAnAppServiceEnvironment]

Alle Artikel und Anleitungen zu App Service-Umgebungen stehen in der [Dokumentation zur App Service-Umgebung](../app-service/app-service-app-service-environments-readme.md) zur Verfügung.

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service][AzureAppService].

Eine Übersicht über die Netzwerkarchitektur der App Service-Umgebung finden Sie im Artikel [Übersicht über die Netzwerkarchitektur von App Service-Umgebungen][NetworkArchitectureOverview].

Informationen zur Verwendung einer App Service-Umgebung mit ExpressRoute finden Sie im folgenden Artikel [Details zur Netzwerkkonfiguration für App Service-Umgebungen mit ExpressRoute][NetworkConfigDetailsForExpressRoute].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]: https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/

<!-- IMAGES -->

 

<!---HONumber=AcomDC_0713_2016-->