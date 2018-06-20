---
title: Azure App Service, Azure Virtual Machines, Service Fabric und Azure Cloud Services im Vergleich | Microsoft Docs
description: Erfahren Sie, wann Sie Azure App Service, Virtual Machines, Service Fabric und Cloud Services für das Hosten von Webanwendungen wählen sollten.
services: app-service\web, virtual-machines, cloud-services
documentationcenter: ''
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: 7d346a23-532a-42a9-98a8-23b7286d32a8
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/07/2016
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 1f7396ac761ce5eeb5a671d3b04aabf944c361b8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597925"
---
# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Azure App Service, Azure Virtual Machines, Service Fabric und Azure Cloud Services im Vergleich
## <a name="overview"></a>Übersicht
Azure bietet verschiedene Möglichkeiten zum Hosten von Websites: [Azure App Service][Azure App Service], [Virtual Machines][Virtual Machines], [Service Fabric][Service Fabric] und [Cloud Services][Cloud Services]. Dieser Artikel unterstützt Sie dabei, die verfügbaren Optionen zu verstehen und die richtige Wahl für Ihre Webanwendung zu treffen.

Azure App Service stellt für die meisten Web-Apps die beste Wahl dar. Die Bereitstellungs- und die Verwaltungsfunktionen sind in die Plattform integriert. Websites können rasch skaliert werden, um hohe Datenaufkommen zu bewältigen. Außerdem bieten der integrierte Lastenausgleich und Traffic Manager Hochverfügbarkeit. Sie können vorhandene Websites mit einem [Online-Migrationstool][migrate-tool] problemlos in Azure App Service verschieben, eine Open Source-App aus dem Web-App-Katalog verwenden oder eine neue Website mithilfe des Frameworks und der Tools Ihrer Wahl erstellen. Das Feature [WebJobs][WebJobs] erleichtert das Hinzufügen der Hintergrundauftragsverarbeitung zu Ihrer App Service-Web-App.

Service Fabric ist eine gute Wahl, wenn Sie eine neue App erstellen oder eine vorhandene App so umgestalten, dass eine Architektur aus Microservices verwendet werden kann. Apps, die in einem gemeinsam genutzten Pool von Computern ausgeführt werden, können klein anfangen und bei Bedarf auf Hunderte oder Tausende VMs skaliert werden. Zustandsbehaftete Dienste erleichtern das konsistente und zuverlässige Speichern des App-Zustands. Darüber hinaus verwaltet Service Fabric die Partitionierung, Skalierung und Verfügbarkeit für Sie.  Service Fabric unterstützt auch WebAPI mit Open Web Interface für .NET (OWIN) und ASP.NET Core.  Im Vergleich mit App Service bietet Service Fabric außerdem mehr Kontrolle über oder direkten Zugriff auf die zugrunde liegende Infrastruktur. Sie können eine Remoteverbindung mit Ihren Servern herstellen oder Serverstartaufgaben konfigurieren. Cloud Services entspricht Service Fabric hinsichtlich des Grads der Kontrolle im Vergleich mit der Bedienungsfreundlichkeit, gilt aber mittlerweile als veraltet, weshalb Service Fabric für Neuentwicklungen empfohlen wird.

Falls Sie über eine Anwendung verfügen, die wesentlich geändert werden müsste, um sie in App Service oder Service Fabric auszuführen, können Sie die Migration zur Cloud mithilfe von Azure Virtual Machines vereinfachen. Das richtige Konfigurieren, Schützen und Warten von VMs erfordert im Vergleich zu Azure App Service und Service Fabric jedoch weitaus mehr Zeit und IT-Kenntnisse. Wenn Sie die Nutzung von Azure Virtual Machines in Erwägung ziehen, müssen Sie den fortlaufenden Wartungsaufwand berücksichtigen, der für das Patchen, Aktualisieren oder Verwalten der VM-Umgebung erforderlich ist. Azure Virtual Machines wird als IaaS-Modell (Infrastructure-as-a-Service) bereitgestellt, App Service und Service Fabric hingegen als PaaS-Modell (Platform-as-a-Service). 

## <a name="features"></a>Funktionsvergleich
In der folgenden Tabelle werden die Funktionen von App Service, Cloud Services, Virtual Machines und Service Fabric verglichen, um Ihnen die Auswahl der richtigen Option zu erleichtern. Aktuelle Informationen über die Vereinbarung zum Servicelevel (SLA) in Bezug auf die jeweilige Option finden Sie unter [Azure-Vereinbarung zum Servicelevel](https://azure.microsoft.com/support/legal/sla/).

| Feature | App Service (Web-Apps) | Cloud Services (Webrollen) | Virtual Machines | Service Fabric | Notizen |
| --- | --- | --- | --- | --- | --- |
| Zeitnahe Bereitstellung |X | | |X |Die Bereitstellung einer Anwendung oder einer Anwendungsaktualisierung in Cloud Services oder das Erstellen eines virtuellen Computers nimmt mindestens einige Minuten in Anspruch. Die Bereitstellung einer Anwendung in einer Web-App dauert wenige Sekunden. |
| Skalierung auf größere Computer ohne erneute Bereitstellung |X | | |X | |
| Webserverinstanzen weisen gemeinsame Inhalte und eine gemeinsame Konfiguration auf. Dies bedeutet, dass Sie sie bei einer Skalierung nicht neu bereitstellen oder neu konfigurieren müssen. |X | | |X | |
| Mehrere Bereitstellungsumgebungen (Produktion und Staging) |X |X | |X |Mit Service Fabric können Sie mehrere Umgebungen für Ihre Apps erstellen oder parallel verschiedene Versionen einer App bereitstellen. |
| Automatische Aktualisierungsverwaltung für das Betriebssystem |X |X | | |Teilweise durch die Patch Orchestration Application (POA) und in der Zukunft vollständig. |
| Nahtloser Plattformwechsel (einfacher Wechsel zwischen 32 Bit und 64 Bit) |X |X | | | |
| Bereitstellung von Code mit GIT, FTP |X | |X | | |
| Bereitstellung von Code mit Web Deploy |X | |X | |Cloud Services unterstützt die Verwendung von Web Deploy, um Updates für einzelne Rolleninstanzen bereitzustellen. Ein Einsatz für die Erstbereitstellung einer Rolle ist jedoch nicht möglich. Wenn Sie Web Deploy für eine Aktualisierung verwenden, müssen Sie jeweils eine separate Bereitstellung für jede Instanz einer Rolle vornehmen. Es sind mehrere Instanzen sind erforderlich, um für die Cloud Services-SLA für Produktionsumgebungen qualifiziert zu sein. |
| Unterstützung von WebMatrix |X | |X | | |
| Zugriff auf Dienste wie Service Bus, Storage und SQL-Datenbank |X |X |X |X | |
| Hosten der Web- oder Webdienstebene einer mehrschichtigen Architektur |X |X |X |X | |
| Hosten der mittleren Ebene einer mehrschichtigen Architektur |X |X |X |X |App Service kann problemlos für das Hosten einer REST-API der mittleren Ebene genutzt werden, über die Funktion [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) können Hintergrundverarbeitungsaufträge gehostet werden. Sie können WebJobs auf einer dedizierten Website ausführen, um eine unabhängige Skalierbarkeit für die entsprechende Ebene zu erzielen. |
| Integrierte Unterstützung von MySQL-as-a-Service |X |X | | | |
| Unterstützung von ASP.NET, klassischem ASP, Node.js, PHP, Python |X |X |X |X |Service Fabric unterstützt die Erstellung eines Web-Front-Ends mit [ASP.NET 5](../service-fabric/service-fabric-reliable-services-communication-aspnetcore.md). Zudem können Sie jede Art von Anwendung (Node.js, Java usw.) als [ausführbare Gastanwendungsdatei](../service-fabric/service-fabric-guest-executables-introduction.md) bereitstellen. |
| Horizontale Skalierung auf mehrere Instanzen ohne erneute Bereitstellung |X |X |X |X |Bei virtuellen Computern ist eine horizontale Skalierung auf mehrere Instanzen möglich. Die Dienste, die auf diesen Computern ausgeführt werden, müssen jedoch so geschrieben werden, dass sie diese horizontale Skalierung unterstützen. Sie müssen einen Lastenausgleich konfigurieren, um Anforderungen über die Computer weiterzuleiten. Zudem müssen Sie eine Affinitätsgruppe erstellen, um gleichzeitigen Neustarts aller Instanzen aufgrund von Wartungs- oder Hardware-Fehlern vorzubeugen. |
| SSL-Unterstützung |X |X |X |X |Für App Service-Web-Apps wird SSL für benutzerdefinierte Domänen nur in den Modi "Basic" und "Standard" unterstützt. Weitere Informationen zur Verwendung von SSL mit Web-Apps finden Sie unter [Konfigurieren eines SSL-Zertifikats für eine Azure-Website](app-service-web-tutorial-custom-ssl.md). |
| Visual Studio-Integration |X |X |X |X | |
| Remotedebugging |X |X |X | | |
| Bereitstellung von Code mit TFS |X |X |X |X | |
| Netzwerkisolierung mit [Azure Virtual Network](/azure/virtual-network/) |X |X |X |X |Siehe auch [Virtual Network-Integration in Azure Websites](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) |
| Unterstützung für [Azure Traffic Manager](/azure/traffic-manager/) |X |X |X |X | |
| Integrierte Endpunktüberwachung |X |X |X | | |
| Remotedesktopzugriff auf Server | |X |X |X | |
| Installieren einer beliebigen benutzerdefinierten MSI | |X |X |X |Mit Service Fabric können Sie jede ausführbare Datei als [ausführbare Gastanwendungsdatei](../service-fabric/service-fabric-guest-executables-introduction.md) hosten oder jede App auf den virtuellen Computern installieren. |
| Möglichkeit zum Definieren/Ausführen von Startaufgaben | |X |X |X | |
| Kann ETW-Ereignisse abhören | |X |X |X | |

## <a name="scenarios"></a>Szenarien und Empfehlungen
Im Folgenden sind einige Anwendungsszenarien mit Empfehlungen zur Auswahl der geeigneten Azure-Webhostingoption aufgeführt.

* [Ich benötige ein Web-Front-End mit Hintergrundverarbeitung und Datenbank-Back-End, um Geschäftsanwendungen mit integrierten lokalen Ressourcen auszuführen.](#onprem)
* [Ich benötige eine zuverlässige Möglichkeit zum Hosten meiner Unternehmenswebsite, die eine problemlose Skalierung und einen globalen Einsatz ermöglicht.](#corp)
* [Ich verfüge über eine IIS6-Anwendung, die unter Windows Server 2003 ausgeführt wird.](#iis6)
* [Ich bin Inhaber eines kleinen Unternehmens und suche nach einem kostengünstigen Weg, meine Website zu hosten. Dabei sollen zukünftige Wachstumsmöglichkeiten berücksichtigt werden.](#smallbusiness)
* [Ich bin Web-/Grafikdesigner und möchte für meine Kunden Websites entwerfen und erstellen.](#designer)
* [Ich migriere meine mehrstufige Webanwendung mit einem Web-Front-End zur Cloud.](#multitier)
* [Meine Anwendung hängt von stark angepassten Windows- oder Linux-Umgebungen ab, und ich möchte die Anwendung in die Cloud verschieben.](#custom)
* [Meine Website verwendet Open-Source-Software, und ich möchte sie in Azure hosten.](#oss)
* [Ich verfüge über eine Branchenanwendung, die eine Verbindung mit dem Unternehmensnetzwerk herstellen muss.](#lob)
* [Ich möchte eine REST-API oder einen Webdienst für mobile Clients hosten.](#mobile)

### <a id="onprem"></a> Ich benötige ein Web-Front-End mit Hintergrundverarbeitung und Datenbank-Back-End, um Geschäftsanwendungen mit integrierten lokalen Ressourcen auszuführen.
Azure App Service stellt eine hervorragende Lösung für komplexe Geschäftsanwendungen dar. Mit Azure App Service können Sie Apps entwickeln, die automatisch auf einer Lastenausgleichsplattform skaliert, mit Active Directory geschützt und mit Ihren lokalen Ressourcen verbunden werden können. Das Verwalten dieser Apps wird dank eines erstklassigen Portals und mithilfe von APIs erleichtert. Sie erhalten außerdem mithilfe von App Insight-Tools Einblicke darin, wie Kunden die Apps verwenden. Mithilfe des Features [WebJobs][Webjobs] können Sie Hintergrundprozesse und Aufgaben als Teil der Webebene ausführen, während Hybridkonnektivität und VNET-Funktionen die erneute Verbindung mit lokalen Ressourcen ermöglichen. Azure App Service bietet eine SLA mit 99,9 % Verfügbarkeit für Web-Apps und stellt folgende Möglichkeiten bereit:

* Zuverlässige Ausführung Ihrer Anwendungen auf einer Cloudplattform mit Selbstreparatur und automatischem Patching.
* Automatische Skalierung in einem globalen Netzwerk von Datencentern.
* Sicherung und Wiederherstellung für die Notfallwiederherstellung.
* Konformität mit ISO, SOC2 und PCI.
* Integration in Active Directory.

### <a id="corp"></a> Ich benötige eine zuverlässige Möglichkeit zum Hosten meiner Unternehmenswebsite, die eine problemlose Skalierung und einen globalen Einsatz ermöglicht.
Azure App Service stellt eine hervorragende Lösung für das Hosten von Unternehmenswebsites dar. Azure App Service ermöglicht ein rasches und problemloses Skalieren von Web-Apps, um die Anforderungen in einem globalen Netzwerk von Datencentern zu erfüllen. Azure App Service bietet lokalen Zugriff, Fehlertoleranz und eine intelligente Verwaltung des Datenverkehrs. Dies alles auf einer Plattform, die erstklassige Verwaltungstools bereitstellt und es Ihnen ermöglicht, rasch und problemlos Einblicke in den Zustand und das Datenaufkommen der Website zu erhalten. Azure App Service bietet eine SLA mit 99,9 % Verfügbarkeit für Web-Apps und stellt folgende Möglichkeiten bereit:

* Führen Sie Ihre Websites auf einer Cloudplattform mit Selbstreparatur und automatischem Patching zuverlässig aus.
* Automatische Skalierung in einem globalen Netzwerk von Datencentern.
* Sicherung und Wiederherstellung für die Notfallwiederherstellung.
* Verwalten Sie mithilfe integrierter Tools Protokolle und Datenaufkommen.
* Konformität mit ISO, SOC2 und PCI.
* Integration in Active Directory.

### <a id="iis6"></a> Ich verfüge über eine IIS6-Anwendung, die unter Windows Server 2003 ausgeführt wird.
Mit Azure App Service lassen sich die mit dem Migrieren von älteren IIS6-Anwendungen verbundenen Infrastrukturkosten ganz einfach vermeiden. Microsoft hat [einfach zu verwendende Migrationstools und eine detaillierte Migrationsanleitung][migrate-tool] erstellt, mit deren Hilfe Sie die Kompatibilität überprüfen und etwaige vorzunehmende Änderungen identifizieren können. Die Integration in Visual Studio, TFS oder allgemeine CMS-Tools erleichtert die Bereitstellung von IIS6-Anwendungen direkt in der Cloud. Sobald eine Bereitstellung erfolgt ist, stellt das Azure-Portal stabile Verwaltungstools zur Verfügung. Mit diesen können Sie eine Skalierung nach unten oder oben vornehmen, um je nach Bedarf Kosten zu verwalten oder Anforderungen zu erfüllen. Mit dem Migrationstool können Sie folgende Aktionen vornehmen:

* Schnelles und einfaches Migrieren der Windows Server 2003 Legacy-Webanwendungen zur Cloud.
* Auswählen der lokalen angefügten SQL-Datenbank für die Erstellung einer Hybridanwendung
* Automatisches Verschieben der SQL-Datenbank zusammen mit der Legacyanwendung.

### <a id="smallbusiness"></a>Ich bin Inhaber eines kleinen Unternehmens und suche nach einem kostengünstigen Weg, meine Website zu hosten. Dabei sollen zukünftige Wachstumsmöglichkeiten berücksichtigt werden.
Azure App Service ist eine hervorragende Lösung für dieses Szenario, da Sie mit einer kostenlosen Version beginnen und später bei Bedarf weitere Funktionen hinzufügen können. Jede kostenlose Web-App wird mit einer Domäne von Azure bereitgestellt (*Ihr_Unternehmen*.azurewebsites.net). Die Plattform umfasst integrierte Bereitstellungs- und Verwaltungstools sowie einen Anwendungskatalog, der Ihnen den Einstieg erleichtert. Es gibt viele weitere Dienste und Skalierungsoptionen, dank derer Sie die Website mit steigenden Benutzeranforderungen weiterentwickeln können. Mit Azure App Service haben Sie folgende Möglichkeiten:

* Starten Sie mit der kostenlosen Version, und führen Sie nach Bedarf eine Skalierung durch.
* Verwenden Sie den Web-App-Katalog, um rasch gängige Webanwendungen wie z. B. WordPress einzurichten.
* Fügen Sie Ihrer Anwendung nach Bedarf zusätzliche Azure-Dienste und -Funktionen hinzu.
* Schützen Sie Ihre Web-App mit HTTPS.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a id="designer"></a> Ich bin Web-/Grafikdesigner und möchte für meine Kunden Websites entwerfen und erstellen.
Für Webentwickler und -designer lässt sich Azure App Service leicht in eine Reihe von Frameworks und Tools integrieren. Dazu gehören eine Bereitstellungsunterstützung für Git und FTP sowie eine nahtlose Integration in Tools und Dienste wie Visual Studio oder SQL-Datenbank. Mit Azure App Service haben Sie folgende Möglichkeiten:

* Verwenden von Befehlszeilentools für [automatisierte Aufgaben][scripting]
* Arbeiten mit gängigen Sprachen wie [.NET][dotnet], [PHP][PHP], [Node.js][nodejs] und [Python][Python]
* Wählen Sie aus drei verschiedener Skalierungsebenen, um eine Skalierung für sehr hohe Kapazitäten zu unterstützen.
* Integration in andere Azure-Dienste, wie [SQL-Datenbank][sqldatabase], [Service Bus][servicebus] und [Storage][Storage], oder Partnerangebote aus dem [Azure Store][azurestore], beispielsweise MySQL und MongoDB
* Integration in Tools wie Visual Studio, Git, WebMatrix, WebDeploy, TFS und FTP.

### <a id="multitier"></a>Ich migriere meine mehrstufige Webanwendung mit einem Web-Front-End zur Cloud.
Wenn Sie eine mehrstufige Anwendung ausführen, z. B. einen Webserver, der eine Verbindung mit einer Datenbank herstellt, ist Azure App Service eine gute Option, die eine enge Integration in Azure SQL-Datenbank bietet. Außerdem können Sie die WebJob-Funktion zum Ausführen von Hintergrundprozessen verwenden.

Wählen Sie Service Fabric für eine oder mehrere der Ebenen, wenn Sie mehr Kontrolle über die Serverumgebung benötigen. Dies kann beispielsweise die Fähigkeit betreffen, remote auf Ihren Server zuzugreifen oder Serverstartaufgaben zu konfigurieren.

Wählen Sie Azure Virtual Machines für eine oder mehrere Ihrer Ebenen, wenn Sie ein eigenes Computerimage verwenden oder Serversoftware oder Dienste ausführen möchten, die Sie nicht in Service Fabric konfigurieren können.

### <a id="custom"></a>Meine Anwendung hängt von stark angepassten Windows- oder Linux-Umgebungen ab, und ich möchte die Anwendung in die Cloud verschieben.
Wenn Ihre Anwendung eine komplexe Installation oder die Konfiguration von Software und Betriebssystem erfordert, ist Virtual Machines wahrscheinlich die beste Lösung. Virtual Machines ermöglichen Ihnen Folgendes:

* Verwendne Sie den Virtual Machines-Katalog, um mit einem Betriebssystem wie Windows oder Linux zu beginnen und dieses später an Ihre Anwendungsanforderungen anzupassen.
* Erstellen Sie ein maßgeschneiderten Image eines bereits vorhandenen lokalen Servers für die Ausführung auf einem virtuellen Computer in Azure, und laden Sie dieses hoch.

### <a id="oss"></a>Meine Website verwendet Open-Source-Software, und ich möchte sie in Azure hosten.
Wenn Ihr Open-Source-Framework in App Service unterstützt wird, werden die von Ihrer Anwendung benötigten Sprachen und Frameworks automatisch für Sie konfiguriert. App Service ermöglicht Ihnen Folgendes:

* Arbeiten Sie mit vielen gängigen Open-Source-Sprachen wie [.NET][dotnet], [PHP][PHP], [Node.js][nodejs] und [Python][Python].
* Richten Sie WordPress, Drupal, Umbraco, DNN und viele weitere Webanwendungen von Drittanbietern ein.
* Migrieren Sie vorhandene Anwendungen, oder Erstellen Sie neue Anwendungen über den Web-App-Katalog.

Wenn Ihr Open-Source-Framework in App Service nicht unterstützt wird, ist eine Ausführung über eine der weiteren Azure-Webhostingoptionen möglich. Mit Virtual Machines können Sie die Software auf dem Image eines virtuellen Computers installieren und konfigurieren. Dieser kann Windows- oder Linux-basiert sein.

### <a id="lob"></a>Ich verfüge über eine Branchenanwendung, die mit dem Unternehmensnetzwerk verbunden sein muss.
Wenn Sie eine Branchenanwendung erstellen möchten, benötigt Ihre Website möglicherweise direkten Zugriff auf Dienste oder Daten in Ihrem Unternehmensnetzwerk. Dies ist in App Service, Service Fabric und Virtual Machines über den [Azure Virtual Network-Dienst](/azure/virtual-network/)möglich. In App Service können Sie mithilfe der [VNET-Integrationsfunktion](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)eine Azure-Anwendung so ausführen, als befände sie sich in Ihrem Unternehmensnetzwerk.

### <a id="mobile"></a>Ich möchte eine REST-API oder einen Webdienst für mobile Clients hosten.
HTTP-basierte Webdienste ermöglichen die Unterstützung vieler unterschiedlicher Clients, einschließlich mobiler Clients. Frameworks wie ASP.NET-Web-API können in Visual Studio integriert werden. Dies erleichtert die Erstellung und Verwendung von REST-Diensten.  Diese Dienste werden von einem Webendpunkt verfügbar gemacht, sodass es möglich ist, eine beliebige Webhostingmethode in Azure anzuwenden, um dieses Szenario zu unterstützen. App Service ist jedoch auch eine hervorragende Option für das Hosten von REST-APIs. Mit Azure App Service haben Sie folgende Möglichkeiten:

* Schnelles Erstellen einer [mobilen App](../app-service-mobile/app-service-mobile-value-prop.md) oder API-App zum Hosten des HTTP-Webdiensts in einem der global verteilten Azure-Datencenter.
* Migrieren von vorhandenen Diensten oder Erstellen von neuen Diensten.
* Erfüllen von SLAs zur Verfügbarkeit mit einer einzelnen Instanz oder durch eine horizontale Skalierung auf mehrere dedizierte Computer.
* Verwenden der veröffentlichten Website, um RESTP-APIs für beliebige HTTP-Clients bereitzustellen, auch für mobile Clients.

> [!NOTE]
> Wenn Sie Wenn Sie Azure Website ausprobieren möchten, ehe Sie sich für ein Konto anmelden, besuchen Sie <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, wo Sie sofort kostenlos eine kurzlebige ASP.NET-Starter-App in Azure App Service erstellen können. Keine Kreditkarte erforderlich, keine Verpflichtungen.
> 
> 

## <a id="nextsteps"></a> Nächste Schritte
Weitere Informationen zu den drei Webhostingoptionen finden Sie unter [Einführung in Azure](../fundamentals-introduction-to-azure.md).

Informationen zum Einstieg in die ausgewählte(n) Option(en) für Ihre Anwendung finden Sie in den folgenden Ressourcen:

* [Azure App Service](/azure/app-service/)
* [Azure Cloud Services](/azure/cloud-services/)
* [Dokumentation zu virtuellen Computern](/azure/virtual-machines/)
* [Service Fabric](/azure/service-fabric/)

<!-- URL List -->

[Azure App Service]: /azure/app-service/
[Cloud Services]: /azure/cloud-services/
[Virtual Machines]: /azure/virtual-machines/
[Service Fabric]: /azure/service-fabric/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: app-service-web-tutorial-custom-ssl.md
[azurestore]: https://azuremarketplace.microsoft.com/en-us/marketplace/apps
[scripting]: https://azure.microsoft.com/documentation/scripts/?services=web-sites
[dotnet]: https://azure.microsoft.com/develop/net/
[nodejs]: https://azure.microsoft.com/develop/nodejs/
[PHP]: https://azure.microsoft.com/develop/php/
[Python]: https://azure.microsoft.com/develop/python/
[servicebus]: /azure/service-bus/
[sqldatabase]: /azure/sql-database/
[Storage]: /azure/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
[migrate-tool]: https://www.movemetothecloud.net/
