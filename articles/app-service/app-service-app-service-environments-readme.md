---
title: App Service-Umgebung | Microsoft Docs
description: Was ist eine Azure App Service-Umgebung? Einführung in die App Service-Umgebung.
keywords: Azure App Service-Umgebung, virtuelles Netzwerk, sicheres Netzwerk
services: app-service
documentationcenter: ''
author: yochay
manager: wpickett
editor: ''

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2016
ms.author: stefsch

---
# Dokumentation zur App Service-Umgebung
Eine App Service-Umgebung ist eine Option des [Premium][PremiumTier]-Tarifs von Azure App Service, die eine vollständig isolierte und dedizierte Umgebung zur sicheren Ausführung zahlreicher Azure App Service-Apps einschließlich [Web-Apps][WebApps], [Mobile Apps][MobileApps] und [API-Apps][APIApps] bereitstellt.

App Service-Umgebungen sind ideal für Anwendungsworkloads mit folgenden Anforderungen:

* Unterstützung sehr vieler Apps
* Isolierung und sicherer Netzwerkzugriff

Kunden können mehrere App Service-Umgebungen innerhalb einer einzelnen Azure-Region sowie über mehrere Azure-Regionen verteilt einrichten. Dadurch eignen sich App Service-Umgebungen hervorragend für die horizontale Skalierung zustandsloser Anwendungsebenen zur Unterstützung hoher RPS-Workloads.

Aufgrund der Isolierung werden in App Service-Umgebungen nur Anwendungen eines einzelnen Kunden ausgeführt. Die Umgebungen werden zudem immer in einem virtuellen Netzwerk bereitgestellt. Kunden haben präzise Kontrolle über den eingehenden und ausgehenden Netzwerkdatenverkehr der Anwendung, indem sie [Netzwerksicherheitsgruppen][NetworkSecurityGroups] verwenden. Anwendungen können auch schnelle, sichere Verbindungen über virtuelle Netzwerke zu lokalen Unternehmensressourcen einrichten.

Apps müssen häufig auf Unternehmensressourcen wie interne Datenbanken und Webdienste zugreifen. In App Service-Umgebungen ausgeführte Apps haben Zugriff auf Ressourcen über [Standort-zu-Standort-][SiteToSite] VPN und [Azure ExpressRoute][ExpressRoute]-Verbindungen.

* [Was ist eine App Service-Umgebung?](../app-service-web/app-service-app-service-environment-intro.md)
* [Erstellen einer App Service-Umgebung](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Erstellen von Apps in einer App Service-Umgebung](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Erstellen und Verwenden eines internen Load Balancers mit App Service-Umgebungen](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [Konfigurieren einer App Service-Umgebung](../app-service-web/app-service-web-configure-an-app-service-environment.md)
* [Skalieren von Apps in einer App Service-Umgebung](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [Netzwerksicherheit und -architektur](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## Anleitungen zur Vorgehensweise
[!INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]

## Videos
[!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON325/player]


[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3715/player]



<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

<!---HONumber=AcomDC_0720_2016-->