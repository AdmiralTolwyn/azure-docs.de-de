---
title: "Verwenden von Log Analytics mit einer mehrinstanzenfähigen App für SQL-Datenbank | Microsoft-Dokumentation"
description: "Einrichten und Verwenden von Log Analytics (OMS) mit einer mehrinstanzenfähigen SaaS-App für Azure SQL-Datenbank"
keywords: Tutorial zur SQL-Datenbank
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: billgib; sstein
ms.openlocfilehash: c036901bde5fff0d63ee2494de87e49410662099
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2017
---
# <a name="setup-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>Einrichten und Verwenden von Log Analytics (OMS) mit einer mehrinstanzenfähigen SaaS-App für Azure SQL-Datenbank

In diesem Tutorial richten Sie *Log Analytics([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* ein und verwenden es zum Überwachen von Pools für elastische Datenbanken sowie Datenbanken. Dieses Tutorial baut auf dem Tutorial [Überwachen der Leistung der SaaS-Anwendung Wingtip](saas-dbpertenant-performance-monitoring.md) auf. Es zeigt, wie Sie mit *Log Analytics* die im Azure-Portal bereitgestellte Überwachungs- und Warnungsfunktionalität erweitern können. Log Analytics eignet sich für umfassende Überwachung und Warnungen, da Hunderte von Pools und Hunderttausende Datenbanken unterstützt werden. Zudem verfügen Sie damit über eine einzige Überwachungslösung, in der die Überwachung unterschiedlicher Anwendungen und Azure-Dienste für mehrere Azure-Abonnements integriert werden kann.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Installieren und Konfigurieren von Log Analytics (OMS)
> * Überwachen von Pools und Datenbanken mithilfe von Log Analytics

Stellen Sie zum Durchführen dieses Tutorials sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Die App Wingtip Tickets SaaS Database Per Tenant wird bereitgestellt. Unter [Bereitstellen und Kennenlernen der App Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-get-started-deploy.md) finden Sie Informationen dazu, wie Sie die App in weniger als fünf Minuten bereitstellen.
* Azure PowerShell wurde installiert. Weitere Informationen hierzu finden Sie unter [Erste Schritte mit Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Im [Tutorial zum Überwachen und Verwalten der Leistung](saas-dbpertenant-performance-monitoring.md) finden Sie eine Beschreibung der SaaS-Szenarien und -Muster, und es wird erläutert, wie sie die Anforderungen an eine Überwachungslösung beeinflussen.

## <a name="monitoring-and-managing-performance-with-log-analytics-oms"></a>Überwachen und Verwalten der Leistung mit Log Analytics (OMS)

In SQL-Datenbank sind Überwachung und Warnung für Datenbanken und Pools verfügbar. Diese integrierte Überwachung und Warnungen sind ressourcenspezifisch und eignen sich für eine kleine Anzahl von Ressourcen. Sie sind weniger geeignet, um umfassendere Installationen zu überwachen oder einen einheitlichen Überblick über verschiedene Ressourcen und Abonnements zu liefern.

Für Szenarien mit hoher Nutzung kann Log Analytics verwendet werden. Dies ist ein separater Azure-Dienst, der Analysen ausgegebener Diagnoseprotokolle und Telemetriedaten bietet, die im Log Analytics-Arbeitsbereich gesammelt werden, der Telemetriedaten aus vielen Diensten sammeln und zum Abfragen und Festlegen von Warnungen verwendet werden kann. Log Analytics bietet eine integrierte Abfragesprache und Tools zur Datenvisualisierung, welche die Analyse und Visualisierung operativer Daten ermöglichen. Die SQL-Analyse-Lösung bietet mehrere vordefinierte Überwachungs- und Warnungsansichten für Pools für elastische Datenbanken und Datenbanken. Zudem können Sie eigene Ad-hoc-Abfragen hinzufügen und diese ggf. speichern. OMS bietet außerdem einen Designer für benutzerdefinierte Ansichten.

Sowohl Log Analytics-Arbeitsbereiche als auch Analyselösungen können im Azure-Portal und in OMS geöffnet werden. Das Azure-Portal stellt den neueren Zugangspunkt dar, bleibt jedoch möglicherweise in einigen Bereichen hinter dem OMS-Portal zurück.

### <a name="create-data-by-starting-the-load-generator"></a>Erstellen von Daten durch Starten des Lastengenerators 

1. Öffnen Sie **Demo-PerformanceMonitoringAndManagement.ps1** in der **PowerShell ISE**. Lassen Sie dieses Skript geöffnet, da Sie während dieses Szenarios u.U. mehrere Lastgenerierungsszenarien ausführen.
1. Wenn Sie über weniger als fünf Mandanten verfügen, stellen Sie einen Batch von Mandanten bereit, um einen interessanteren Überwachungskontext herzustellen:
   1. Legen Sie **$DemoScenario = 1** fest, **Bereitstellen eines Batchs von Mandanten**
   1. Betätigen Sie **F5**, um das Skript auszuführen.

1. Legen Sie **$DemoScenario** = 2 fest, **Generieren einer Last mit normaler Intensität (ca. 40 DTUs)**.
1. Betätigen Sie **F5**, um das Skript auszuführen.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Abrufen der Skripts zur Anwendung Wingtip Tickets SaaS Database Per Tenant

Die Skripts und der Quellcode der Anwendung Wingtip Tickets SaaS Database Per Tenant stehen im GitHub-Repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) zur Verfügung. Skriptdateien befinden sich im Ordner [Learning Modules](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/tree/master/Learning%20Modules) (Lernmodule). Laden Sie den Ordner **Learning Modules** auf den lokalen Computer herunter, wobei Sie dessen Ordnerstruktur beibehalten.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Installieren und Konfigurieren von Log Analytics und der Azure SQL-Analyselösung

Log Analytics ist ein separater Dienst, der konfiguriert werden muss. Log Analytics erfasst Protokolldaten sowie Telemetriedaten und Metriken in einem Log Analytics-Arbeitsbereich. Ein Arbeitsbereich ist eine Ressource und muss wie andere Ressourcen in Azure erstellt werden. Der Arbeitsbereich muss zwar nicht in der Ressourcengruppe erstellt werden, in der sich auch die überwachte(n) Anwendung(en) befinden, dies ist jedoch häufig am sinnvollsten. Für die Wingtip Tickets SaaS Database Per Tenant SaaS-App kann der Arbeitsbereich auf einfache Weise zusammen mit der Anwendung gelöscht werden, indem die Ressourcengruppe gelöscht wird.

1. Öffnen Sie ...\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\*Demo-LogAnalytics.ps1* in der **PowerShell ISE**.
1. Betätigen Sie **F5**, um das Skript auszuführen.

An dieser Stelle sollten Sie in der Lage sein, Log Analytics im Azure-Portal (oder im OMS-Portal) zu öffnen. Es dauert einige Minuten, bis Telemetriedaten im Log Analytics-Arbeitsbereich erfasst und angezeigt werden. Je länger Sie dem System Zeit zum Sammeln von Daten lassen, desto interessanter wird das Ergebnis. Dies ist ein guter Zeitpunkt, eine Pause einzulegen – vergewissern Sie sich nur, dass der Last-Generator immer noch ausgeführt wird!


## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Überwachen von Pools und Datenbanken mithilfe von Log Analytics und der SQL-Analyselösung


Öffnen Sie in dieser Übung Log Analytics und das OMS-Portal, um die Telemetriedaten zu untersuchen, die für die Datenbanken und -Pools gesammelt werden.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), und öffnen Sie Log Analytics, indem Sie auf „Weitere Dienste“ klicken und nach Log Analytics suchen:

   ![Öffnen von Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Wählen Sie den Arbeitsbereich *wtploganalytics-&lt;BENUTZER&gt;* aus.

1. Wählen Sie **Übersicht** aus, um die Log Analytics-Lösung im Azure-Portal zu öffnen.
   ![Link „Übersicht“](media/saas-dbpertenant-log-analytics/click-overview.png)

    **WICHTIG**: Es kann einige Minuten dauern, bis die Lösung aktiv ist. Haben Sie etwas Geduld!

1. Klicken Sie auf die Kachel „Azure SQL-Analyse“, um sie zu öffnen.

    ![Übersicht](media/saas-dbpertenant-log-analytics/overview.png)

    ![Analytics](media/saas-dbpertenant-log-analytics/analytics.png)

1. Die Ansicht im Blatt mit der Lösung kann seitwärts gescrollt werden, und es ist eine eigene Bildlaufleiste am unteren Rand vorhanden (ggf. muss das Blatt aktualisiert werden).

1. Durchsuchen Sie die verschiedenen Ansichten, indem Sie darauf klicken. Sie können auch auf einzelne Ressourcen klicken, um einen Drilldown-Explorer zu öffnen, in dem Sie oben links den Zeit-Schieberegler verwenden oder auf eine vertikale Leiste klicken können, um einen kürzeren Zeitraum zu untersuchen. In dieser Ansicht können Sie einzelne Datenbanken oder Pools auswählen, um sich auf bestimmte Ressourcen zu konzentrieren:

    ![Diagramm](media/saas-dbpertenant-log-analytics/chart.png)

1. Wenn Sie zurück zum Blatt mit der Lösung wechseln und ans äußerste rechte Ende scrollen, sehen Sie dort gespeicherte Abfragen. Sie können darauf klicken, um sie zu öffnen und zu untersuchen. Sie können mit diesen experimentieren und Änderungen daran vornehmen, und Sie können interessante selbst erstellte Abfragen speichern. Diese können Sie später erneut öffnen und mit anderen Ressourcen verwenden.

1. Wechseln Sie zurück zum Blatt mit dem Log Analytics-Arbeitsbereich, und wählen Sie dort „OMS-Portal“, um die Lösung dort zu öffnen.

    ![OMS](media/saas-dbpertenant-log-analytics/oms.png)

1. Im OMS-Portal können Sie Warnungen konfigurieren. Klicken Sie auf den Warnungsbereich der DTU-Ansicht für Datenbanken.

1. Die Ansicht „Protokollsuche“ wird geöffnet. Dort sehen Sie ein Balkendiagramm der dargestellten Metriken.

    ![Protokollsuche](media/saas-dbpertenant-log-analytics/log-search.png)

1. Wenn Sie auf der Symbolleiste auf „Warnung“ klicken, wird die Warnungskonfiguration angezeigt, die Sie ändern können.

    ![Hinzufügen einer Warnungsregel](media/saas-dbpertenant-log-analytics/add-alert.png)

Überwachung und Warnungen in Log Analytics und OMS basieren auf Abfragen der Daten im Arbeitsbereich – im Gegensatz zu den Warnungen auf den Blättern für einzelne Ressourcen, die ressourcenspezifisch sind. Somit können Sie beispielsweise eine Warnung definieren, die alle Datenbanken abdeckt, anstatt eine Warnung pro Datenbank zu definieren. Sie können auch eine Warnung schreiben, die eine zusammengesetzte Abfrage für mehrere Ressourcentypen verwendet. Abfragen sind nur durch die im Arbeitsbereich verfügbaren Daten beschränkt.

Die Rechnungsstellung für Log Analytics für SQL-Datenbank basiert auf dem jeweiligen Datenvolumen im Arbeitsbereich. In diesem Tutorial haben Sie einen kostenlosen Arbeitsbereich erstellt, der auf 500 MB pro Tag beschränkt ist. Sobald dieser Grenzwert erreicht wurde, werden dem Arbeitsbereich keine Daten mehr hinzugefügt.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Installieren und Konfigurieren von Log Analytics (OMS)
> * Überwachen von Pools und Datenbanken mithilfe von Log Analytics

[Tutorial zu Mandantenanalysen](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Weitere Tutorials, die auf der anfänglichen Anwendungsbereitstellung von Wingtip Tickets SaaS Database Per Tenant aufbauen](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
