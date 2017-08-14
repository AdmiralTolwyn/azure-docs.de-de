---
title: Was sind Mobile Apps?
description: "Erfahren Sie, welche Vorteile App Service für Ihre mobilen Unternehmens-Apps bietet."
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: yochayk
editor: 
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: dd405eefbd69e1ed2268152017bc1f9992619c5f
ms.contentlocale: de-de
ms.lasthandoff: 08/05/2017

---
# <a name="getting-started"></a>Was ist Mobile Apps?
Azure App Service ist ein vollständig verwaltetes [PaaS](https://azure.microsoft.com/overview/what-is-paas/)-Angebot (Platform as a Service) für professionelle Entwickler, das einen umfangreichen Satz von Funktionen für Web- und Integrationsszenarien sowie mobile Szenarien bereitstellt. *Mobile Apps* in *Azure App Service* bieten eine überaus skalierbare, global verfügbare Entwicklungsplattform für mobile Anwendungen für Unternehmensentwickler und Systemintegratoren, die umfassende Funktionen für mobile Entwickler bereitstellt.

![Mobile Apps](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>Warum Mobile Apps?
*Mobile Apps* in *Azure App Service* bieten eine überaus skalierbare, global verfügbare Entwicklungsplattform für mobile Anwendungen für Unternehmensentwickler und Systemintegratoren, die umfassende Funktionen für mobile Entwickler bereitstellt. Mobile Apps bietet folgende Möglichkeiten:

* **Entwickeln systemeigener und plattformübergreifender Apps** – Unabhängig davon, ob Sie systemeigene iOS-, Android- und Windows-Apps oder plattformübergreifende Xamarin- oder Cordova-Apps (Phonegap) erstellen, können Sie mithilfe systemeigener SDKs die Vorteile von App Service nutzen.
* **Herstellen einer Verbindung mit Ihren Unternehmenssystemen** – Mit Mobile Apps können Sie die Unternehmensanmeldung innerhalb von Minuten hinzufügen und eine Verbindung mit Ihren lokalen Unternehmensressourcen oder Cloudressourcen herstellen.
* **Erstellen von offlinefähigen Apps mit Datensynchronisierung** – Steigern Sie die Produktivität Ihrer mobilen Mitarbeiter mithilfe von Apps, die auch offline verwendet werden können, und nutzen Sie Mobile Apps zur Datensynchronisierung im Hintergrund, wenn eine Verbindung vorhanden ist – für jede der von Ihnen verwendeten Unternehmensdatenquellen oder SaaS-APIs.
* **Pushbenachrichtigungen an Millionen Benutzer innerhalb von Sekunden** – Beteiligen Sie Ihre Kunden mit Pushbenachrichtigungen an beliebige Geräte – personalisiert, auf ihre Anforderungen zugeschnitten und zum richtigen Zeitpunkt gesendet.

## <a name="mobile-app-features"></a>Mobile App-Features
Die folgenden Features sind für die cloudfähige mobile Entwicklung wichtig:

* **Authentifizierung und Autorisierung** – Wählen Sie einen Eintrag aus der ständig wachsenden Liste mit Identitätsanbietern (wie etwa Azure Active Directory für die Authentifizierung von Unternehmen) sowie mit Anbietern von sozialen Netzwerken wie Facebook, Google, Twitter und Microsoft-Konto aus.  Azure Mobile Apps stellt einen OAuth 2.0-Dienst für jeden Anbieter bereit.  Sie können auch das SDK für den Identitätsanbieter integrieren, um anbieterspezifische Funktionen zu erhalten.

  Erfahren Sie mehr über unsere [Authentifizierungsfeatures].
* **Datenzugriff** – Azure Mobile Apps stellt eine für Mobilgeräte geeignete OData v3-Datenquelle bereit, die mit SQL Azure oder einer lokalen SQL Server-Instanz verknüpft ist.  Dieser Dienst kann auf Entity Framework basieren, sodass für Sie die einfache Integration in andere NoSQL- und SQL-Datenanbieter möglich ist, z.B. [Azure Table Storage], MongoDB, [DocumentDB] und SaaS-API-Anbieter wie Office 365 und Salesforce.com.
* **Offlinesynchronisierung** – Mit unseren Client-SDKs ist es für Sie einfach, robuste und reaktionsfähige mobile Anwendungen zu erstellen. Diese Anwendungen arbeiten mit einem Offlinedataset, das automatisch mit den Back-End-Daten synchronisiert werden kann, einschließlich Unterstützung der Konfliktlösung.

  Erfahren Sie mehr über unsere [Datenfeatures].
* **Pushbenachrichtigungen** – Unsere Client-SDKs können nahtlos in die Registrierungsfunktionen von Azure Notification Hubs integriert werden, sodass Sie Pushbenachrichtigungen gleichzeitig an Millionen von Benutzern senden können.

  Erfahren Sie mehr über unsere [Pushbenachrichtigungsfeatures].
* **Client-SDKs** – Wir stellen einen vollständigen Satz mit Client-SDKs bereit, mit denen die native Entwicklung ([iOS], [Android] und [Windows]), plattformübergreifende Entwicklung [(Xamarin für iOS und Android], [Xamarin Forms]) und Entwicklung von Hybridanwendungen ([Apache Cordova]) abgedeckt wird.  Jedes Client-SDK ist mit einer MIT-Lizenz erhältlich und ist ein Open-Source-SDK.

## <a name="azure-app-service-features"></a>Azure App Service-Features
Die folgenden Plattformfeatures sind nützlich für mobile Produktionswebsites.

* **Automatische Skalierung** – App Service ermöglicht Ihnen eine schnelle vertikale oder horizontale Skalierung, um beliebige eingehende Datenlasten zu verarbeiten. Wählen Sie die Anzahl und Größe der VMs manuell aus, oder legen Sie eine automatische Skalierung fest, damit Ihr mobiles App-Back-End basierend auf der Datenlast oder einem Zeitplan automatisch skaliert wird.

  Erfahren Sie mehr über die [automatische Skalierung].
* **Stagingumgebungen** – App Service kann mehrere Versionen Ihrer Website ausführen, sodass Sie A/B-Tests, Tests in der Produktion im Rahmen eines umfassenderen DevOps-Plans und das direkte Staging eines neuen Back-Ends durchführen können.

  Erfahren Sie mehr über [Stagingumgebungen].
* **Continuous Deployment** (Fortlaufende Bereitstellung) – App Service kann in gängige SCM-Systeme integriert werden, sodass Sie automatisch eine neue Version Ihres Back-Ends bereitstellen können, indem Sie den Pushvorgang zu einer neuen Verzweigung Ihres SCM-Systems durchführen.

  Erfahren Sie mehr über [Bereitstellungsoptionen].
* **Virtuelles Netzwerk** – App Service kann die Verbindung mit lokalen Ressourcen per virtuellem Netzwerk, ExpressRoute oder Hybridverbindungen herstellen.

  Erfahren Sie mehr über [Hybridverbindungen], [virtuelle Netzwerke] und [ExpressRoute].
* **Isolierte/Dedizierte Umgebungen** – App Service kann in einer vollständig isolierten und dedizierten Umgebung ausgeführt werden, um Azure App Service-Apps in großem Umfang sicher auszuführen.  Dies ist für Anwendungsworkloads ideal, für die die Unterstützung vieler Apps, eine Isolierung oder der sichere Netzwerkzugriff erforderlich ist.

  Erfahren Sie mehr über [App Service-Umgebungen].

## <a name="getting-started"></a>Erste Schritte
Absolvieren Sie zum Einstieg in Mobile Apps das Lernprogramm [Erste Schritte] .  Hierin werden die Grundlagen der Erstellung eines mobilen Back-Ends und Clients Ihrer Wahl beschrieben, und anschließend geht es um die Integration von Authentifizierung, Offlinesynchronisierung und Pushbenachrichtigungen.  Sie können das Tutorial [Erste Schritte] mehrfach durchgehen, z.B. einmal pro Clientanwendung.

Weitere Informationen zu Azure Mobile Apps finden Sie in unserem [Lernpfad].
Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service].

<!-- URLs. -->
[Migrate your Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[Erste Schritte]: app-service-mobile-ios-get-started.md
[Azure Table Storage]: ../storage/storage-dotnet-how-to-use-tables.md
[DocumentDB]: ../documentdb/documentdb-get-started.md
[Authentifizierungsfeatures]: ./app-service-mobile-auth.md
[Datenfeatures]: ./app-service-mobile-offline-data-sync.md
[Pushbenachrichtigungsfeatures]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[(Xamarin für iOS und Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[automatische Skalierung]: ../app-service-web/web-sites-scale.md
[Stagingumgebungen]: ../app-service-web/web-sites-staged-publishing.md
[Bereitstellungsoptionen]: ../app-service-web/web-sites-deploy.md
[Hybridverbindungen]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[virtuelle Netzwerke]: ../app-service-web/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service-web/app-service-app-service-environment-network-configuration-expressroute.md
[App Service-Umgebungen]: ../app-service-web/app-service-app-service-environment-intro.md
[Lernpfad]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/

