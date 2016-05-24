<properties
	pageTitle="SharePoint Server 2013-Farmen in Azure | Microsoft Azure"
	description="In den hier genannten Artikeln erfahren Sie, wie Sie eine Entwicklungs-/Testumgebung oder eine SharePoint Server 2013-Produktionsfarm in Microsoft Azure einrichten."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="04/25/2016"
	ms.author="josephd"/>

# In Azure-Infrastrukturdiensten gehostete SharePoint-Farmen

[AZURE.INCLUDE [learn-about-deployment-models-both-include](../../includes/learn-about-deployment-models-both-include.md)]

Richten Sie Ihre erste oder nächste SharePoint-Server 2013-Farm für Entwicklung/Tests und Produktion in Microsoft Azure-Infrastrukturdiensten ein. Dadurch erhalten Sie eine einfache Konfiguration und die Möglichkeit, die Farm schnell mit neuen Kapazitäten zu erweitern oder die wichtigsten Funktionen zu optimieren.

## Grundlegende SharePoint-Farm für Entwicklung/Tests

Diese automatisch erstellte Umgebung besteht aus drei Servern in einem virtuellen Cloud-Netzwerk in Azure: einem Domänencontroller, einem SQL-Server und dem SharePoint-Server.

Weitere Informationen finden Sie unter [SharePoint 2013 nicht hoch verfügbare Farm](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/) im Azure Marketplace des Azure-Portals. Damit wird eine grundlegende Test-/Entwicklungsfarm für eine SharePoint-Website mit Internetverbindung erstellt. Weitere Informationen finden Sie unter [SharePoint-Serverfarm](virtual-machines-windows-sharepoint-farm.md).

Sie können zudem eine Azure-Ressourcen-Manager-Vorlage verwenden. Weitere Informationen finden Sie unter [SharePoint](virtual-machines-linux-app-frameworks.md).

> [AZURE.NOTE] Das Element **SharePoint-Serverfarm** wurde aus dem Azure Marketplace des Azure-Portals entfernt.

## Hochverfügbare SharePoint-Test-/Entwicklungsfarm

Die automatisch erstellte Umgebung besteht aus neun Servern in einem virtuellen Cloud-Netzwerk in Azure: zwei Server für Domänencontroller, drei Server für einen SQL Server-Cluster, zwei SharePoint-Server auf Anwendungsebene und zwei SharePoint-Server auf Webebene.

Weitere Informationen finden Sie unter [SharePoint 2013 hoch verfügbare Farm](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/) im Azure Marketplace des Azure-Portals. Damit wird eine hoch verfügbare Entwicklungs-/Testfarm für eine SharePoint-Website mit Internetverbindung erstellt. Weitere Informationen finden Sie unter [Erstellen von SharePoint-Serverfarmen](virtual-machines-windows-sharepoint-farm.md).

Sie können zudem eine Azure-Ressourcen-Manager-Vorlage verwenden. Weitere Informationen finden Sie unter [Bereitstellen einer SharePoint-Farm mit neun Servern](virtual-machines-windows-app-frameworks.md#deploy-a-nine-server-sharepoint-farm).

> [AZURE.NOTE] Das Element **SharePoint-Serverfarm** wurde aus dem Azure Marketplace des Azure-Portals entfernt.

## Hybrid-Cloud-Entwicklungs-/Testfarm

Mit der [SharePoint-Intranetfarm in einer Hybrid-Cloud-Entwicklungs-/Testumgebung](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md) erstellen Sie eine simulierte Hybrid-Cloud-Konfiguration, in der eine einfache, zweischichtige SharePoint-Farm gehostet wird. Sie können diese dazu verwenden, eine Intranet-SharePoint-Serverfarm zu testen, die in Azure an Ihrem Speicherort im Internet gehostet wird.

## Intranet-SharePoint-Produktionsfarm mit hoher Verfügbarkeit

Mit der Bereitstellung von [SharePoint 2013 mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-windows-sp-intranet-overview.md) erstellen Sie eine einsatzbereite Intranet-SharePoint Server 2013-Farm mit hoher Verfügbarkeit in Azure.

## Nächster Schritt

- Lernen Sie weitere Konfigurationen von [SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) in Azure-Infrastrukturdiensten kennen.

<!---HONumber=AcomDC_0511_2016-->