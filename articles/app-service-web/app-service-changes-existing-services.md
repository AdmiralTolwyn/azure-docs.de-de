<properties
	pageTitle="Azure App Service und seine Auswirkungen auf vorhandene Azure-Dienste"
	description="Erläutert, wie sich der neue Azure App Service und seine Funktionen auf vorhandene Dienste in Azure auswirken."
	services="app-service"
    documentationCenter=""
	authors="yochay"
	manager="nirma"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/12/2016"
	ms.author="yochayk"/>


# Azure App Service und vorhandene Azure-Dienste

Dieser Artikel beschreibt die Änderungen an vorhandenen Azure-Diensten, die im Rahmen der Zusammenfassung verschiedener Azure-Dienste im neuen integrierten Angebot [Azure App Service](https://azure.microsoft.com/services/app-service/) erfolgen.

[AZURE.INCLUDE [app-service-mobile-to-web-and-api](../../includes/app-service-web-to-api-and-mobile.md)]

## Übersicht

[Azure App Service](https://azure.microsoft.com/services/app-service/) ist ein neuer, besonderer Clouddienst, der es Entwicklern ermöglicht, Web- und mobile Anwendungen für beliebige Plattformen und Geräte zu entwickeln. App Service ist eine integrierte Lösung für die Optimierung wiederholt ausgeführter Codierungsaufgaben, die Integration in Unternehmens- und SaaS-Systeme und die Automatisierung von Geschäftsprozessen bei gleichzeitiger Erfüllung Ihrer Anforderungen an Sicherheit, Zuverlässigkeit und Skalierbarkeit.

App Service vereint verschiedene vorhandene Azure-Dienste – [Websites](https://azure.microsoft.com/services/websites/), [Mobile Services](https://azure.microsoft.com/services/mobile-services/) und [Biztalk Services](https://azure.microsoft.com/services/biztalk-services/) – zu einem einzelnen kombinierten Dienst, der leistungsfähige neue Funktionen bietet. Mit App Service können Sie die folgenden App-Typen hosten:

-   Web-Apps
-   Mobile Apps
-   API-Apps
-   Logik-Apps

In der folgenden Tabelle wird erläutert, wie vorhandene Azure-Dienste App Service und den verfügbaren App-Typen zugeordnet sind.

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">Vorhandener Azure-Dienst</th>
<th align="left", style="width:10%">Azure App Service</th>
<th align="left", style="width:80%">Änderung</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Azure Websites</td>
<td align="left">Web-Apps</td>
<td align="left"><li>Für Azure Websites beschränkt sich die Änderung in App Service auf die Änderung des Namens "Websites" in "Web-Apps".
<p><li>Alle vorhandenen Instanzen von Websites heißen in App Service jetzt Web-Apps.</p>
<p><li>Sie können auf Ihre vorhandenen Websites über das <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Azure-Portal</a> zugreifen. Sie finden alle vorhandenen Websites unter <em>Web-Apps</em>.</p>
<p><li><em>Webhostingpläne</em> sind jetzt <em>App Service-Pläne</em>. Ein <em>App Service-Plan</em> kann beliebige App-Typen von App Service hosten, z. B. Web-Apps, mobile Apps, Logik-Apps oder API-Apps.</p>
<p><li>Azure App Service-Web-Apps ist jetzt allgemein verfügbar.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">Weitere Informationen zu Web-Apps</a></p></td>
</tr>
<tr class="even">
<td align="left">Azure Mobile Services</td>
<td align="left">Mobile Apps</td>
<td align="left"><p><li>Mobile Services steht weiterhin als eigenständiger Dienst zur Verfügung und wird weiter vollständig unterstützt.</p>
<p><li>Mobile Apps ist ein App-Typ in App Service, der die gesamte Funktionalität von Mobile Services und vieles mehr integriert.</p>
<p><li>Eine <a href="http://go.microsoft.com/fwlink/?LinkID=724279&clcid=0x409">Migration von Mobile Services zu Mobile Apps</a> ist einfach durchführbar.</p>
<p><li>Als Bestandteil von App Service umfasst Mobile Apps Features, die über Mobile Services hinausgehen. Dazu gehören beispielsweise die Integration in lokale und SaaS-Systeme, Stagingslots, WebJobs, bessere Skalierungsoptionen und vieles mehr.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">Weitere Informationen zu Mobile Apps</a>.</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">API-Apps</td>
<td align="left">
<p><li>API-Apps ist ein neuer App-Typ in App Service, mit dem APIs einfach erstellt und in der Cloud genutzt werden können.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">Weitere Informationen zu API-Apps</a>.</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Logik-Apps</td>
<td align="left">
<p><li>Logik-Apps ist ein neuer App-Typ in App Service, mit dem Sie Geschäftsprozesse mühelos automatisieren können.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">Weitere Informationen zu Logik-Apps</a>.</p></td>
</tr>
<tr class="odd">
<td align="left">Azure BizTalk Services</td>
<td align="left">BizTalk-API-Apps</td>
<td align="left">
<li><p>BizTalk Services steht weiterhin als eigenständiger Dienst zur Verfügung und wird weiter vollständig unterstützt.</p>
<li><p>Alle Funktionen von BizTalk Services sind in App Service als API-Apps integriert. Dies ermöglicht Benutzern die Integration von Unternehmensanwendungen und B2B-Integrationsszenarien mit beliebigen App-Typen in App Service.</p>
<li><p>Mit Logik-Apps können Sie nun Geschäftsprozesse mithilfe einer visuellen Entwurfsumgebung zum Erstellen von Workflows automatisieren.</p></td>
</tr>
</tbody>
</table>

Weitere Informationen finden Sie in der[ App Service-Dokumentation](https://azure.microsoft.com/documentation/services/app-service/).

<!---HONumber=AcomDC_0413_2016-->