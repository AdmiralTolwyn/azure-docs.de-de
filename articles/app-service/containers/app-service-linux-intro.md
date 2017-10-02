---
title: "Einführung in Azure-Web-App für Container | Microsoft-Docs"
description: "Erfahren Sie mehr über Azure-Web-App für Container."
keywords: Azure App Service, Linux, OSS
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/16/2017
ms.author: wesmc
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: bc1658fa49677386c8e4697221f7269ceec3533d
ms.contentlocale: de-de
ms.lasthandoff: 09/20/2017

---
# <a name="introduction-to-azure-web-app-for-containers"></a>Einführung in Azure-Web-App für Container

Bei einer [Web-App](../app-service-web-overview.md) handelt es sich um eine vollständig verwaltete Computeplattform, die für das Hosten von Websites und Webanwendungen optimiert ist. Kunden können Web-App für Container verwenden, um Web-Apps für unterstützte Anwendungsstapel nativ unter Linux zu hosten. In den folgenden Abschnitten sind die Anwendungsstapel aufgeführt, die derzeit unterstützt werden.

## <a name="languages"></a>Sprachen

|Node.js|PHP|.NET Core|Ruby|
|:------------------:|:---:|:---------:|:----:|
|4.4, 4.5|5.6|1.0-1.1|2.3|
|6.2, 6.6, 6.9-6.11|7.0|||
|8.0-8.1||||

## <a name="deployments"></a>Bereitstellungen

* FTP
* Lokales Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Stagingumgebungen
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) und CI/CD in DockerHub

## <a name="console-publishing-and-debugging"></a>Konsole, Veröffentlichen und Debuggen

* Umgebungen
* Bereitstellungen
* Grundlegende Konsole
* SSH

## <a name="scaling"></a>Skalieren

* Kunden können Web-Apps zentral hoch- und herunterskalieren, indem sie den Tarif ihres [App Service-Plans](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json) ändern.

## <a name="locations"></a>Standorte

Überprüfen Sie das [Dashboard zum Azure-Status](https://azure.microsoft.com/status).

## <a name="limitations"></a>Einschränkungen

Im Azure-Portal werden nur Features angezeigt, die derzeit für Web-App für Container funktionieren. Sobald wir weitere Features aktivieren, werden sie im Portal angezeigt.

Einige Features, z.B. die Integration virtueller Netzwerke, Azure Active Directory-/Drittanbieterauthentifizierung oder Kudu-Websiteerweiterungen, sind noch nicht verfügbar. Sobald diese Features verfügbar sind, aktualisieren wir die Dokumentation entsprechend und veröffentlichen Blog-Beiträge zu den Änderungen.

Web-App für Container wird nur in den App Service-Plänen [Basic und Standard](https://azure.microsoft.com/pricing/details/app-service/plans/) unterstützt und verfügt nicht über einen [Free- oder Shared](https://azure.microsoft.com/pricing/details/app-service/plans/)-Tarif. Im Folgenden werden auch wichtige Einschränkungen für Web-App für Container aufgeführt:

* Sie können keine Web-App für Container in einem App Service-Plan erstellen, in dem bereits Web-Apps unter anderen Betriebssystemen als Linux gehostet werden.
* Beim Erstellen einer Web-App für Container in einer Ressourcengruppe mit Web-Apps unter anderen Betriebssystemen als Linux müssen Sie einen App Service-Plan in einer anderen Region als der Region des vorhandenen App Service-Plans erstellen.

## <a name="troubleshooting"></a>Problembehandlung

Wenn Ihre Anwendung nicht gestartet werden kann oder Sie die Protokolle Ihrer App überprüfen möchten, sehen Sie sich die Docker-Protokolle im Verzeichnis „LogFiles“ an. Sie können auf dieses Verzeichnis entweder über Ihre SCM-Website oder per FTP zugreifen.
Zum Protokollieren von `stdout` und `stderr` in Ihrem Container müssen Sie unter **Diagnoseprotokolle** die **Protokollierung von Docker-Containern** aktivieren.

![Aktivieren der Protokollierung][2]

![Verwenden von Kudu zum Anzeigen von Docker-Protokollen][1]

Sie können auf die SCM-Website über die Option **Erweiterte Tools** im Menü **Entwicklungstools** zugreifen.

## <a name="next-steps"></a>Nächste Schritte

Unter den folgenden Links erhalten Sie Informationen zu den ersten Schritten mit App Service unter Linux. In [unserem Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview) können Sie Fragen stellen und Antworten auf Probleme erhalten.

* [Informationen zum Verwenden eines benutzerdefinierten Docker-Images für Azure-Web-Apps für Container](quickstart-custom-docker-image.md)
* [Verwenden von .NET Core in Azure App Service-Web-App für Container](quickstart-dotnetcore.md)
* [Verwendung von Ruby in Azure App Service-Web-App für Container](quickstart-ruby.md)
* [Azure App Service-Web-App für Container – FAQs](app-service-linux-faq.md)
* [SSH-Unterstützung für Azure-Web-App für Container](app-service-linux-ssh-support.md)
* [Einrichten von Stagingumgebungen in Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Continuous Deployment von Docker-Hubs mit Azure-Web-App für Container](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png

