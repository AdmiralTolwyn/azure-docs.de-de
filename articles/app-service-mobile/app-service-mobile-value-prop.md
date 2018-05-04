---
title: Informationen zu Mobile Apps in Azure App Service
description: Hier erfahren Sie, welche Vorteile App Service für Ihre mobilen Unternehmens-Apps bietet.
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: yochayk
editor: ''
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: e84ac98508b791b4617ead2b6bf3b0edc549bdb6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2018
---
# <a name="getting-started"></a>Informationen zu Mobile Apps in Azure App Service
Azure App Service ist ein vollständig verwaltetes PaaS-Angebot ([Platform-as-a-Service](https://azure.microsoft.com/overview/what-is-paas/)) für professionelle Entwickler. Der Dienst stellt umfangreiche Funktionen für Web- und Integrationsszenarien sowie für mobile Szenarien bereit. 

Das Mobile Apps-Feature von Azure App Service bietet Unternehmensentwicklern und Systemintegratoren eine hochgradig skalierbare, global verfügbare Entwicklungsplattform für mobile Anwendungen.

![Übersicht über Mobile Apps-Funktionen](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>Warum Mobile Apps?
Das Mobile Apps-Feature ermöglicht Folgendes:

* **Entwickeln nativer und plattformübergreifender Apps:** Nutzen Sie die Vorteile von App Service mithilfe nativer SDKs – ob Sie nun native iOS-, Android- und Windows-Apps oder plattformübergreifende Xamarin- oder Cordova-Apps (PhoneGap) erstellen.
* **Herstellen einer Verbindung mit Ihren Unternehmenssystemen:** Mit dem Mobile Apps-Feature können Sie die Unternehmensanmeldung innerhalb von Minuten hinzufügen und eine Verbindung mit Ihren lokalen Unternehmensressourcen oder Cloudressourcen herstellen.
* **Erstellen offlinefähiger Apps mit Datensynchronisierung:** Steigern Sie die Produktivität Ihrer mobilen Mitarbeiter mithilfe von Apps, die auch offline verwendet werden können, und nutzen Sie Mobile Apps zur Datensynchronisierung im Hintergrund, wenn eine Verbindung mit Ihren Unternehmensdatenquellen oder SaaS-APIs (Software-as-a-Service) besteht.
* **Senden von Pushbenachrichtigungen an Millionen von Benutzer in Sekundenschnelle:** Sprechen Sie Ihre Kunden auf beliebigen Geräten genau zum richtigen Zeitpunkt mit personalisierten Pushbenachrichtigungen an.

## <a name="mobile-apps-features"></a>Mobile Apps-Features
Die folgenden Features sind für die cloudfähige mobile Entwicklung wichtig:

* **Authentifizierung und Autorisierung:** Unterstützung von Identitätsanbietern (z.B. Azure Active Directory für die Authentifizierung von Unternehmen) sowie Anbietern von sozialen Netzwerken wie Facebook, Google, Twitter und Microsoft-Konten. Mobile Apps bietet für jeden Anbieter einen OAuth 2.0-Dienst. Sie können auch das SDK für den Identitätsanbieter integrieren, um anbieterspezifische Funktionen zu erhalten.

    Erfahren Sie mehr über die [Authentifizierungsfeatures].

* **Datenzugriff:** Mobile Apps stellt eine für Mobilgeräte geeignete OData v3-Datenquelle bereit, die mit Azure SQL-Datenbank oder einer lokalen SQL Server-Instanz verknüpft ist. Da dieser Dienst auf Entity Framework basieren kann, ermöglicht er die einfache Integration in andere NoSQL- und SQL-Datenanbieter wie [Azure Table Storage], MongoDB, [Azure Cosmos DB] und SaaS-API-Anbieter wie Office 365 und Salesforce.com.

* **Offlinesynchronisierung:** Mit den Client-SDKs können Sie komfortabel robuste und reaktionsfähige mobile Anwendungen mit einem Offlinedataset erstellen. Dieses Dataset kann automatisch mit den Back-End-Daten synchronisiert werden, und es steht eine Unterstützung bei der Konfliktlösung zur Verfügung.

  Erfahren Sie mehr über die [Datenfeatures].

* **Pushbenachrichtigungen:** Die Client-SDKs lassen sich nahtlos in die Registrierungsfunktionen von Azure Notification Hubs integrieren, sodass Sie Pushbenachrichtigungen gleichzeitig an Millionen von Benutzer senden können.

  Erfahren Sie mehr über die [Pushbenachrichtigungsfeatures].

* **Client-SDKs:** Es wird ein umfassender Satz von Client-SDKs für die native Entwicklung ([iOS], [Android] und [Windows]), plattformübergreifende Entwicklung [(Xamarin.iOS und Xamarin.Android], [Xamarin.Forms]) und die Entwicklung von Hybridanwendungen ([Apache Cordova]) bereitgestellt. Jedes Client-SDK ist mit einer MIT-Lizenz erhältlich und ist ein Open-Source-SDK.

## <a name="azure-app-service-features"></a>Azure App Service-Features
Die folgenden Plattformfeatures sind für mobile Produktionswebsites nützlich:

* **Automatische Skalierung:** Mit App Service können Sie schnell zentral oder horizontal hochskalieren, um beliebige eingehende Datenlasten von Kunden zu bewältigen. Wählen Sie Anzahl und Größe der virtuellen Computer manuell aus, oder richten Sie eine automatische Skalierung zur last- oder zeitplanbasierten Skalierung Ihres mobilen App-Back-Ends ein.

  Weitere Informationen zur automatischen Skalierung finden Sie [hier].

* **Stagingumgebungen:** App Service kann mehrere Versionen Ihrer Website ausführen, um A/B-Tests, Produktionstests im Rahmen eines umfassenderen DevOps-Plans und direktes Staging eines neuen Back-Ends zu ermöglichen.

  Erfahren Sie mehr über [Stagingumgebungen].

* **Continuous Deployment**: App Service kann in gängige Systeme für die _Quellcodeverwaltung_ (SCM) integriert werden, sodass Sie leicht eine neue Version Ihres Back-Ends bereitstellen können.

  Erfahren Sie mehr über [Bereitstellungsoptionen](../app-service/app-service-deploy-local-git.md).

* **Virtuelles Netzwerk:** App Service kann eine Verbindung mit lokalen Ressourcen über ein virtuelles Netzwerk, über Azure ExpressRoute oder über Hybridverbindungen herstellen.

  Erfahren Sie mehr über [Hybridverbindungen], [virtuelle Netzwerke] und [ExpressRoute].

* **Isolierte und dedizierte Umgebungen:** Zur sicheren Ausführung von Azure App Service-Apps können Sie App Service in einer vollständig isolierten und dedizierten Umgebung betreiben. Diese Umgebung eignet sich perfekt für Anwendungsworkloads, die eine umfassende Skalierung, Isolierung oder sicheren Netzwerkzugriff benötigen.

  Weitere Informationen zu App Service-Umgebungen finden Sie [hier].

## <a name="next-steps"></a>Nächste Schritte

Absolvieren Sie das [Einstiegstutorial], um mit der Verwendung von Mobile Apps in Azure App Service zu beginnen. Dieses Tutorial vermittelt die Grundlagen für die Erstellung eines mobilen Back-Ends und Clients Ihrer Wahl. Außerdem erfahren Sie, wie Sie Authentifizierung, Offlinesynchronisierung und Pushbenachrichtigungen integrieren. Das Tutorial kann mehrmals durchlaufen werden, um die einzelnen Clientanwendungen abzudecken.

Weitere Informationen zu Mobile Apps finden Sie in unserem [Lernpfad].
Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service].

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Einstiegstutorial]: app-service-mobile-ios-get-started.md
[Azure Table Storage]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/sql-api-get-started.md
[Authentifizierungsfeatures]: ./app-service-mobile-auth.md
[Datenfeatures]: ./app-service-mobile-offline-data-sync.md
[Pushbenachrichtigungsfeatures]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[(Xamarin.iOS und Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[hier]: ../app-service/web-sites-scale.md
[Stagingumgebungen]: ../app-service/web-sites-staged-publishing.md
[Hybridverbindungen]: ../biztalk-services/integration-hybrid-connection-overview.md
[virtuelle Netzwerke]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[hier]: ../app-service/environment/intro.md
[Lernpfad]: https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/
