---
title: "Kapazitätsplanung für Azure App Service-Serverrollen in Azure Stack | Microsoft-Dokumentation"
description: "Kapazitätsplanung für Azure App Service-Serverrollen in Azure Stack"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 8926955d5e0260b5971e07b6988bb21df9980847
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/21/2018
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Kapazitätsplanung für Azure App Service-Serverrollen in Azure Stack
*Gilt für: integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Um eine produktionsfertige Bereitstellung von Azure App Service in Azure Stack bereitzustellen, müssen Sie die Kapazität planen, die Sie für das System erwarten.  Im Folgenden finden Sie Hilfestellungen für die Mindestanzahl von Instanzen und Compute-SKUs, die Sie für jede Produktionsbereitstellung verwenden sollten.

Sie können Ihre Strategie für die App Service-Kapazität mithilfe dieser Richtlinien planen. Zukünftige Versionen von Azure Stack bieten Optionen für Hochverfügbarkeit für App Service.

| App Service-Serverrolle | Empfohlene Mindestanzahl von Instanzen | Empfohlene Compute-SKUs|
| --- | --- | --- |
| Controller | 2 | A1 |
| Front-End | 2 | A1 |
| Verwaltung | 2 | A3 |
| Herausgeber | 2 | A1 |
| Webworker – freigegeben | 2 | A1 |
| Webworker – dediziert | 2 pro Ebene | A1 |

## <a name="controller-role"></a>Controllerrolle

**Empfohlene Mindestanzahl:** zwei Instanzen von A1 Standard

Der Azure App Service-Controller verbraucht in der Regel nur wenig CPU-, Arbeitsspeicher- und Netzwerkressourcen. Für Hochverfügbarkeit benötigen Sie jedoch zwei Controller. Zwei Controller sind auch die maximale Anzahl zulässiger Controller. Sie können den zweiten Websitecontroller während der Bereitstellung direkt im Installationsprogramm erstellen.

## <a name="front-end-role"></a>Front-End-Rolle

**Empfohlene Mindestanzahl:** zwei Instanzen von A1 Standard

Das Front-End leitet Anforderungen abhängig von der Verfügbarkeit der Webworker an Webworker weiter. Für Hochverfügbarkeit sollten Sie über mehrere Front-Ends verfügen, auch mehr als zwei sind möglich. Beachten Sie bei der Kapazitätsplanung, dass jeder Kern ungefähr 100 Anforderungen pro Sekunde verarbeiten kann.

## <a name="management-role"></a>Verwaltungsrolle

**Empfohlene Mindestanzahl:** zwei A3-Instanzen

Die Azure App Service-Verwaltungsrolle ist für die Azure Resource Manager- und API-Endpunkte, die Portalerweiterungen (Administrator-, Mandanten-, Functions-Portal) und den Datendienst von App Service zuständig. Die Verwaltungsserverrolle erfordert in einer Produktionsumgebung in der Regel nur etwa 4 GB RAM. Allerdings kann es zu einer hohen CPU-Auslastung kommen, wenn viele Verwaltungsaufgaben (z.B. Websiteerstellungen) ausgeführt werden. Für Hochverfügbarkeit sollten Sie dieser Rolle mehrere Server sowie jedem Server mindestens zwei Kerne zuweisen.

## <a name="publisher-role"></a>Herausgeberrolle

**Empfohlene Mindestanzahl:** zwei A1-Instanzen

Wenn viele Benutzer gleichzeitig veröffentlichen, kann die Herausgeberrolle eine hohe CPU-Auslastung verursachen. Für Hochverfügbarkeit stellen Sie mehr als eine Herausgeberrolle zur Verfügung.  Der Herausgeber verarbeitet nur FTP/FTPS-Datenverkehr.

## <a name="web-worker-role"></a>Webworkerrolle

**Empfohlene Mindestanzahl:** zwei A1-Instanzen

Für Hochverfügbarkeit sollten Sie über mindestens vier Webworkerrollen verfügen – zwei für den Websitemodus „Shared“ und zwei für jede dedizierte Workerebene, die Sie anbieten möchten. Die freigegebenen und dedizierten Computemodi stellen Mandanten verschiedene Dienstebenen bereit. In folgenden Fällen benötigen Sie eventuell mehr Webworker:
 - Wenn viele Ihrer Kunden dedizierte Workerebenen im Computemodus (die sehr ressourcenintensiv sind) verwenden
 - Wenn viele Ihrer Kunden im freigegebenen Computemodus arbeiten

Nachdem ein Benutzer einen App Service-Plan für eine dedizierte SKU im Computemodus erstellt hat, steht die Anzahl von Webworkern, die im App Service-Plan angegeben wurde, nicht mehr für Benutzer zur Verfügung.

Für die Bereitstellung von Azure Functions für Benutzer im Verbrauchsplanmodell müssen Sie die freigegebene Webworker bereitstellen.

Beachten Sie bei der Entscheidung über die Anzahl der zu verwendenden freigegebenen Webworkerrollen folgende Überlegungen:

- **Arbeitsspeicher:** Arbeitsspeicher ist die wichtigste Ressource für eine Webworkerrolle. Unzureichender Arbeitsspeicher wirkt sich auf die Websiteleistung aus, wenn virtueller Arbeitsspeicher vom Datenträger ausgelagert wird. Jeder Server benötigt ungefähr 1,2 GB Arbeitsspeicher für das Betriebssystem. Arbeitsspeicher oberhalb dieses Schwellenwerts kann für das Ausführen von Websites verwendet werden.
- **Prozentsatz der aktiven Websites:** Normalerweise sind bei einer Azure Stack-Bereitstellung ungefähr 5 % der Anwendungen in Azure App Service aktiv. Der Prozentsatz der Anwendungen, die in einem gegebenen Moment aktiv sind, kann jedoch höher oder niedriger sein. Bei einer aktiven Anwendungsrate von 5 % sollte die maximale Anzahl von Anwendungen in Azure App Service bei einer Azure Stack-Bereitstellung kleiner sein als:
    - 20-mal die Anzahl der aktiven Websites (5 x 20 = 100).
- **Durchschnittliche Arbeitsspeicheranforderung:** Die in Produktionsumgebungen ermittelte durchschnittliche Arbeitsspeicheranforderung für Anwendungen beträgt ungefähr 70 MB. Daher kann der allen Computern oder VMs der Webworkerrolle zugewiesene Arbeitsspeicher wie folgt berechnet werden:

    *Anzahl der bereitgestellten Anwendungen * 70 MB * 5 % – (Anzahl der Webworkerrollen * 1.044 MB)*

   Wenn beispielsweise 5.000 Anwendungen in einer Umgebung mit 10 Webworkerrollen ausgeführt werden, sollte jeder VM für eine Webworkerrolle ein Arbeitsspeicher von 7.060 MB zur Verfügung stehen:

   5.000 * 70 * 0,05 – (10 * 1.044) = 7.060 MB (= ca. 7 GB)

   Weitere Informationen zum Hinzufügen weiterer Workerinstanzen finden Sie unter [Hinzufügen weiterer Workerrollen](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Dateiserverrolle

Für die Rolle „Dateiserver“ können Sie einen eigenständigen Dateiserver für Entwicklung und Tests verwenden, z.B. können Sie bei der Bereitstellung von Azure App Service auf dem Azure Stack Development Kit die Vorlage „https://aka.ms/appsvconmasdkfstemplate“ verwenden. Für den Produktionseinsatz sollten Sie einen vorkonfigurierten Windows-Dateiserver oder einen vorkonfigurierten Nicht-Windows-Dateiserver verwenden.

In Produktionsumgebungen verursacht die Dateiserverrolle hohe Datenträger-E/A-Lasten. Da sie alle Inhalts- und Anwendungsdateien für Benutzerwebsites enthält, sollten Sie eine der folgenden Komponenten für diese Rolle vorkonfigurieren:
- einen Windows-Dateiserver
- Dateiservercluster
- einen Nicht-Windows-Dateiserver
- Dateiservercluster
- NAS-Gerät (Network Attached Storage). Weitere Informationen finden Sie unter [Bereitstellen eines Dateiservers](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Nächste Schritte

[Vor den ersten Schritten mit App Service in Azure Stack](azure-stack-app-service-before-you-get-started.md)
