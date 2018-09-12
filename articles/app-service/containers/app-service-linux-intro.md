---
title: Einführung in App Service unter Linux | Microsoft Docs
description: Informationen zu Azure App Service unter Linux.
keywords: Azure App Service, Linux, OSS
services: app-service
documentationcenter: ''
author: naziml
manager: cfowler
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/16/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 5ab5452aec5b0371caaf437b6e364ed7b922db3a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162256"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Einführung in Azure App Service unter Linux

Bei einer [Web-App](../app-service-web-overview.md) handelt es sich um eine vollständig verwaltete Computeplattform, die für das Hosten von Websites und Webanwendungen optimiert ist. Kunden können App Service unter Linux verwenden, um Web-Apps für unterstützte Anwendungsstapel nativ unter Linux zu hosten. In den folgenden Abschnitten sind die Anwendungsstapel aufgeführt, die derzeit unterstützt werden.

## <a name="languages"></a>Sprachen

App Service unter Linux unterstützt eine Reihe von integrierten Images, um die Produktivität der Entwickler zu steigern. Wenn die Laufzeit, die Anwendung Ihre erfordert, nicht in den integrierten Images unterstützt wird, sind Anweisungen zum [Erstellen eines eigenen Docker-Images](tutorial-custom-docker-image.md) verfügbar, das in Web-App für Container bereitgestellt werden kann.

| Sprache | Unterstützte Versionen |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 9.4 |
| Java* | 8.0 |
| PHP | 5.6, 7.0, 7.2 |
| .NET Core | 1.0, 1.1, 2.0 |
| Ruby | 2.3 |
| Go | 1.0 |
| Apache Tomcat | 8.5, 9.0 |

Ausführlichere Informationen finden Sie unter [Vorschauversion: Erstellen einer Java-Web-App in App Service unter Linux](https://docs.microsoft.com/azure/app-service/containers/quickstart-java).

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

App Service unter Linux wird nur in den App Service-Plänen [Basic, Standard und Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) unterstützt und verfügt nicht über einen [Free- oder Shared](https://azure.microsoft.com/pricing/details/app-service/plans/)-Tarif. Sie können keine Web-App für Container in einem App Service-Plan erstellen, in dem bereits Web-Apps unter anderen Betriebssystemen als Linux gehostet werden. Derzeit besteht auch die Beschränkung, dass Windows- und Linux-Apps nicht in derselben Ressourcengruppe gemischt werden dürfen.

## <a name="troubleshooting"></a>Problembehandlung

Wenn Ihre Anwendung nicht gestartet werden kann oder Sie die Protokolle Ihrer App überprüfen möchten, sehen Sie sich die Docker-Protokolle im Verzeichnis „LogFiles“ an. Sie können auf dieses Verzeichnis entweder über Ihre SCM-Website oder per FTP zugreifen.
Zum Protokollieren von `stdout` und `stderr` in Ihrem Container müssen Sie unter **Diagnoseprotokolle** die **Protokollierung von Docker-Containern** aktivieren.

![Aktivieren der Protokollierung][2]

![Verwenden von Kudu zum Anzeigen von Docker-Protokollen][1]

Sie können auf die SCM-Website über die Option **Erweiterte Tools** im Menü **Entwicklungstools** zugreifen.

## <a name="next-steps"></a>Nächste Schritte

Unter den folgenden Links erhalten Sie Informationen zu den ersten Schritten mit App Service unter Linux. In [unserem Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview) können Sie Fragen stellen und Antworten auf Probleme erhalten.

* [Verwenden eines benutzerdefinierten Docker-Images für Web-App für Container](quickstart-docker-go.md)
* [Verwenden von .NET Core in Azure App Service unter Linux](quickstart-dotnetcore.md)
* [Verwenden von Ruby in Azure App Service unter Linux](quickstart-ruby.md)
* [Azure App Service-Web-App für Container – FAQs](app-service-linux-faq.md)
* [SSH-Unterstützung für Azure App Service unter Linux](app-service-linux-ssh-support.md)
* [Einrichten von Stagingumgebungen in Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Continuous Deployment von Docker-Hubs mit Web-App für Container](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
