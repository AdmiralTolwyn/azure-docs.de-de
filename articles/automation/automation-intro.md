---
title: Was ist Azure Automation? | Microsoft Docs
description: "Sie erfahren, welchen Nutzen Azure Automation hat, und erhalten Antworten auf häufig gestellte Fragen, damit Sie mit dem Erstellen und Verwenden von Runbooks und Azure Automation DSC beginnen können."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
keywords: was ist Automation, Azure Automation, Azure Automation Beispiele
ms.assetid: 0cf1f3e8-dd30-4f33-b52a-e148e97802a9
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2016
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 15456a887aab29ac6c4ee74d15b8b8ef8d3047d3
ms.openlocfilehash: 4e7488f954ce50298aba0da83ae07f7a0de6a73d


---
# <a name="azure-automation-overview"></a>Azure Automation – Übersicht
Microsoft Azure Automation bietet Benutzern die Möglichkeit, die manuellen, langfristigen, Fehler verursachenden und häufig wiederholten Aufgaben zu automatisieren, die für gewöhnlich in einer Cloud- und Unternehmensumgebung ausgeführt werden. Dies spart Zeit, erhöht die Zuverlässigkeit normaler Verwaltungsaufgaben und plant diese sogar so ein, dass sie in regelmäßigen Abständen automatisch ausgeführt werden. Sie können Prozesse mit Runbooks automatisieren oder die Konfigurationsverwaltung über die Konfiguration für den gewünschten Zustand automatisieren. Dieser Artikel enthält eine kurze Übersicht über Azure Automation und Antworten auf einige häufige Fragen. In anderen Artikeln dieser Bibliothek finden Sie ausführlichere Informationen zu den verschiedenen Themen.

## <a name="automating-processes-with-runbooks"></a>Automatisieren von Prozessen mit Runbooks
Ein Runbook umfasst eine Reihe von Aufgaben, mit denen in Azure Automation ein automatisierter Prozess durchgeführt wird. Dies kann ein einfacher Prozess sein, z. B. das Starten eines virtuellen Computers oder das Erstellen eines Protokolleintrags. Sie können auch ein komplexes Runbook nutzen, in dem andere kleinere Runbooks kombiniert sind, um einen komplexen Prozess für mehrere Ressourcen oder sogar mehrere Clouds und lokale Umgebungen durchzuführen.  

Beispielsweise verfügen Sie über einen vorhandenen manuellen Prozess zum Abschneiden einer SQL-Datenbank, wenn die Gefahr besteht, dass sie die maximale Größe erreicht. Dieser Prozess umfasst mehrere Schritte, z. B. das Herstellen der Verbindung mit dem Server, das Herstellen der Verbindung mit der Datenbank, das Abrufen der aktuellen Datenbankgröße, das Prüfen der Schwellenwertüberschreitung und dann das Abschneiden und das Benachrichtigen von Benutzern. Anstatt diese Schritte manuell auszuführen, können Sie ein Runbook erstellen, mit dem diese Aufgaben als einzelner Prozess ausgeführt werden. Sie starten dazu das Runbook, geben die erforderlichen Informationen an, z. B. den Namen des SQL-Servers, den Datenbanknamen und die E-Mail-Adresse des Empfängers, und können sich dann zurücklehnen, während der Prozess durchgeführt wird. 

## <a name="what-can-runbooks-automate"></a>Was kann mit Runbooks automatisiert werden?
Runbooks in Azure Automation basieren auf Windows PowerShell oder dem Windows PowerShell-Workflow, sodass alle Optionen von PowerShell verfügbar sind. Wenn eine Anwendung oder ein Dienst über eine API verfügt, kann ein Runbook damit arbeiten. Falls Sie ein PowerShell-Modul dafür haben, können Sie das Modul für die Anwendung in Azure Automation laden und diese Cmdlets in Ihr Runbook einbinden. Azure Automation-Runbooks werden in der Azure-Cloud ausgeführt und können auf alle Ressourcen in der Cloud oder externen Ressourcen zugreifen, auf die über die Cloud zugegriffen werden kann. Mit dem [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)können Runbooks in Ihrem lokalen Rechenzentrum ausgeführt werden, um lokale Ressourcen zu verwalten. 

## <a name="getting-runbooks-from-the-community"></a>Abrufen von Runbooks aus der Community
Der [Runbookkatalog](automation-runbook-gallery.md#runbooks-in-runbook-gallery) enthält Runbooks von Microsoft und der Community, die Sie entweder unverändert in Ihrer Umgebung verwenden oder für Ihre eigenen Zwecke anpassen können. Außerdem sind sie als Referenz hilfreich, um zu lernen, wie Sie eigene Runbooks erstellen. Sie können auch Ihre eigenen Runbooks im Katalog bereitstellen, die für andere Benutzer nützlich sein können. 

## <a name="creating-runbooks-with-azure-automation"></a>Erstellen von Runbooks mit Azure Automation
Sie können völlig neue [eigene Runbooks erstellen](automation-creating-importing-runbook.md) oder Runbooks aus dem [Runbookkatalog](http://msdn.microsoft.com/library/azure/dn781422.aspx) an Ihre eigenen Anforderungen anpassen. Sie können basierend auf Ihren Anforderungen und Ihrer Erfahrung mit PowerShell zwischen vier verschiedenen [Runbooktypen](automation-runbook-types.md) wählen. Wenn Sie lieber direkt mit PowerShell-Code arbeiten, können Sie ein [PowerShell-Runbook](automation-runbook-types.md#powershell-runbooks) oder [PowerShell-Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks) verwenden, das Sie offline oder mit dem [Text-Editor](http://msdn.microsoft.com/library/azure/dn879137.aspx) im Azure-Portal bearbeiten. Wenn Sie ein Runbook lieber bearbeiten möchten, ohne sich mit dem zugrunde liegenden Code zu befassen, können Sie mit dem [Grafik-Editor](automation-graphical-authoring-intro.md) im Azure-Portal ein [grafisches Runbook](automation-runbook-types.md#graphical-runbooks) erstellen. 

Möchten Sie sich lieber ein Video ansehen? Sehen Sie sich das folgende Video aus einer Microsoft Ignite-Veranstaltung im Mai 2015 an. Hinweis: Wenngleich die Konzepte und Features, die in diesem Video erläutert werden, weiter zutreffend sind, hat sich Azure Automation seit der Aufzeichnung des Videos wesentlich weiterentwickelt. Die Lösung bietet nun im Azure-Portal eine umfangreichere Benutzeroberfläche und unterstützt weitere Funktionen.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3451/player]
> 
> 

## <a name="automating-configuration-management-with-desired-state-configuration"></a>Automatisieren der Konfigurationsverwaltung mit der Konfiguration für den gewünschten Zustand
[PowerShell DSC](https://technet.microsoft.com/library/dn249912.aspx) ist eine Verwaltungsplattform, mit der Sie die Konfiguration physischer Hosts und virtueller Computer mit einer deklarativen PowerShell-Syntax verwalten, bereitstellen und erzwingen können. Sie können Konfigurationen auf einem zentralen DSC-Pullserver definieren, die von Zielcomputern automatisch abgerufen und angewendet werden können. Für DSC (Desired State Configuration = Konfiguration für den gewünschten Zustand) wird eine Gruppe von PowerShell-Cmdlets bereitgestellt, die Sie zum Verwalten von Konfigurationen und Ressourcen verwenden können.  

[Azure Automation DSC](automation-dsc-overview.md) ist eine cloudbasierte Lösung für PowerShell DSC, mit der für Unternehmensumgebungen erforderliche Dienste bereitgestellt werden.  Sie können Ihre DSC-Ressourcen in Azure Automation verwalten und Konfigurationen auf virtuelle oder physische Computer anwenden, mit denen sie von einem DSC-Pullserver in der Azure-Cloud abgerufen werden.  Außerdem werden darüber Berichtdienste bereitgestellt, mit denen Sie über wichtige Ereignisse informiert werden, z. B. bei einer Abweichung der Knoten von ihrer zugewiesenen Konfiguration und bei Anwendung einer neuen Konfiguration. 

## <a name="creating-your-own-dsc-configurations-with-azure-automation"></a>Erstellen eigener DSC-Konfigurationen mit Azure Automation
[DSC-Konfigurationen](automation-dsc-overview.md#azure-automation-dsc-terms) wird der gewünschte Zustand eines Knotens angegeben.  Für mehrere Knoten kann die gleiche Konfiguration angewendet werden, um sicherzustellen, dass alle über einen identischen Zustand verfügen.  Sie können eine Konfiguration mit einem beliebigen Text-Editor auf Ihrem lokalen Computer erstellen und dann in Azure Automation importieren, wo Sie sie kompilieren und auf Knoten anwenden können.

## <a name="getting-modules-and-configurations"></a>Abrufen von Modulen und Konfigurationen
Sie können [PowerShell-Module](automation-runbook-gallery.md#modules-in-powershell-gallery) mit Cmdlets abrufen, die Sie in Ihren Runbooks und DSC-Konfigurationen aus dem [PowerShell-Katalog](http://www.powershellgallery.com/) verwenden können. Sie starten diesen Katalog über das Azure-Portal und importieren Module direkt in Azure Automation. Sie können sie auch herunterladen und manuell importieren. Die Module können nicht direkt über das Azure-Portal installiert werden, Sie können sie jedoch herunterladen und installieren wie alle anderen Module auch. 

## <a name="example-practical-applications-of-azure-automation"></a>Beispiel: Praktische Anwendung von Azure Automation
Unten sind nur einige Beispiele angegeben, welche Automatisierungsszenarien mit Azure Automation möglich sind. 

* Erstellen und Kopieren von virtuellen Computern in unterschiedlichen Azure-Abonnements 
* Planen von Dateikopiervorgängen von einem lokalen Computer in einen Azure Blob Storage-Container 
* Automatisieren von Sicherheitsfunktionen, z. B. Ablehnung von Anforderungen von einem Client bei Erkennung eines Denial of Service-Angriffs 
* Sicherstellen, dass Computer ständig an der konfigurierten Sicherheitsrichtlinie ausgerichtet sind
* Verwalten der kontinuierlichen Bereitstellung von Anwendungscode für die Cloud- und lokale Infrastruktur 
* Erstellen einer Active Directory-Gesamtstruktur in Azure für Ihre Testumgebung 
* Abschneiden einer Tabelle in einer SQL-Datenbank, wenn die maximale Größe der Datenbank bald erreicht ist 
* Aktualisieren der Umgebungseinstellungen für eine Azure-Website per Remotezugriff 

## <a name="how-does-azure-automation-relate-to-other-automation-tools"></a>Wie steht Azure Automation im Vergleich mit anderen Automatisierungstools da?
[Service Management Automation (SMA)](http://technet.microsoft.com/library/dn469260.aspx) wird zum Automatisieren von Verwaltungsaufgaben in der privaten Cloud verwendet. Es wird lokal in Ihrem Rechenzentrum als Komponente von [Microsoft Azure Pack](https://www.microsoft.com/en-us/server-cloud/)installiert. Für SMA und Azure Automation wird dasselbe Runbookformat genutzt, das auf Windows PowerShell und dem Windows PowerShell-Workflow basiert, aber bei SMA werden keine [grafischen Runbooks](automation-graphical-authoring-intro.md)unterstützt.  

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) dient zur Automatisierung von lokalen Ressourcen. Hierbei wird ein anderes Runbookformat als für Azure Automation und Service Management Automation verwendet, und die Anwendung verfügt über eine grafische Oberfläche zum Erstellen von Runbooks ohne Skripts. Die Runbooks setzen sich aus den Aktivitäten der Integrationspakete zusammen, die speziell für Orchestrator geschrieben wurden. 

## <a name="where-can-i-get-more-information"></a>Wo erhalte ich weitere Informationen?
Um mehr über Azure Automation zu erfahren und Ihre eigenen Runbooks zu erstellen, steht Ihnen eine Vielzahl von Ressourcen zur Verfügung. 

* **Azure Automation-Bibliothek** . Die Artikel in dieser Bibliothek enthalten die vollständige Dokumentation für die Konfiguration und Verwaltung von Azure Automation und für die Erstellung Ihrer eigenen Runbooks. 
* [Azure PowerShell-Cmdlets](http://msdn.microsoft.com/library/jj156055.aspx) finden Sie Informationen zur Automatisierung von Azure-Vorgängen mit Windows PowerShell. Runbooks verwenden diese Cmdlets zum Arbeiten mit Azure-Ressourcen. 
* [Management-Blog](https://azure.microsoft.com/blog/tag/azure-automation/) enthält die neuesten Informationen über Azure Automation und andere Verwaltungstechnologien von Microsoft. Es ist ratsam, diesen Blog zu abonnieren, um stets die neuesten Informationen vom Azure Automation-Team zu erhalten. 
* [Automation-Forum](http://go.microsoft.com/fwlink/p/?LinkId=390561) können Sie Fragen über Azure Automation stellen, um sie von Microsoft und der Automation-Community beantworten zu lassen. 
* [Azure Automation-Cmdlets](https://msdn.microsoft.com/library/mt244122.aspx) werden Informationen zum Automatisieren von Verwaltungsaufgaben bereitgestellt. Die Anwendung enthält Cmdlets zum Verwalten von Automation-Konten, -Assets, -Runbooks und Automation DSC.

## <a name="can-i-provide-feedback"></a>Kann ich Feedback geben?
**Bitte geben Sie uns Feedback!** Wenn Sie nach einer Azure Automation-Runbook-Lösung oder einem Integrationsmodul suchen, senden Sie im Script Center eine Skriptanforderung. Wenn Sie Feedback oder Vorschläge zu Features für Azure Automation haben, veröffentlichen Sie sie auf [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback). Vielen Dank! 




<!--HONumber=Jan17_HO3-->


