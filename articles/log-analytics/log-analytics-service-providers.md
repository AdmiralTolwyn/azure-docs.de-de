---
title: Log Analytics für Dienstanbieter | Microsoft-Dokumentation
description: Log Analytics unterstützt Managed Service Provider (MSPs), Großunternehmen, unabhängige Softwarehersteller (Independent Software Vendors, ISVs) und Hosting-Anbieter beim Verwalten und Überwachen von Servern in der lokalen oder Cloudinfrastruktur des Kunden.
services: log-analytics
documentationcenter: ''
author: MeirMen
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: meirm
ms.component: na
ms.openlocfilehash: ad0a3b8e0ee5f1114ea1db95cfe2f4176b8e2ddb
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931989"
---
# <a name="log-analytics-for-service-providers"></a>Log Analytics für Dienstanbieter
Log Analytics unterstützt Managed Service Provider (MSPs), Großunternehmen, unabhängige Softwarehersteller (Independent Software Vendors, ISVs) und Hosting-Anbieter beim Verwalten und Überwachen von Servern in der lokalen oder Cloudinfrastruktur des Kunden. 

Zwischen Großunternehmen und Dienstanbietern bestehen viele Gemeinsamkeiten, insbesondere wenn ein zentrales IT-Team für die Verwaltung der IT vieler unterschiedlicher Geschäftseinheiten verantwortlich ist. Der Einfachheit halber wird in diesem Dokument der Begriff *Dienstanbieter* verwendet, jedoch ist die gleiche Funktionalität für Unternehmen und andere Kunden verfügbar.

Für Partner und Dienstanbieter, die am Programm [Cloud-Lösungsanbieter (Cloud Solution Provider, CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) teilnehmen, ist Log Analytics einer der in einem [Azure CSP-Abonnement](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview) verfügbaren Azure-Dienste. 

## <a name="architectures-for-service-providers"></a>Architekturen für Dienstanbieter

Log Analytics-Arbeitsbereiche stellen für Administratoren ein Verfahren zum Steuern von Protokollfluss und -isolierung und zum Erstellen einer Protokollarchitektur bereit, die den spezifischen Geschäftsanforderungen Rechnung trägt. [In diesem Artikel](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-manage-access) werden die allgemeinen Überlegungen zur Verwaltung von Arbeitsbereichen erläutert. Dienstanbieter müssen sich mit zusätzlichen Überlegungen befassen.

Für Dienstanbieter gibt es im Hinblick auf Log Analytics-Arbeitsbereiche drei mögliche Architekturen:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Verteilt: Protokolle werden in Arbeitsbereichen gespeichert, die sich im Mandanten des Kunden befinden 

Bei dieser Architektur wird der Arbeitsbereich in dem Mandanten des Kunden bereitgestellt, der für alle Protokolle dieses Kunden verwendet wird. Den Administratoren des Dienstanbieters wird mithilfe der [Azure Active Directory-Gastbenutzer (B2B)](https://docs.microsoft.com/en-us/azure/active-directory/b2b/what-is-b2b) Zugriff auf diesen Arbeitsbereich erteilt. Der Administrator des Dienstanbieters muss im Azure-Portal zum Verzeichnis seines Kunden wechseln, um Zugriff auf diese Arbeitsbereiche zu erhalten.

Diese Architektur bietet diese Vorzüge:
* Der Kunde kann den Zugriff auf die Protokolle mithilfe seines eigenen [rollenbasierten Zugriffs](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview) steuern.
* Jeder Kunde kann andere Einstellungen für seinen Arbeitsbereich verwenden, etwa hinsichtlich Vermerkdauer und Datenbegrenzung.
* Isolation zwischen Kunden im Hinblick auf behördliche Bestimmungen und Compliance.
* Die Gebühr für jeden Arbeitsbereich wird im Abonnement des Kunden erfasst.
* Protokolle können von Ressourcen jeder Art erfasst werden, nicht nur auf Agent-basierte Weise. Beispielsweise durch Azure-Überwachung.

Die Nachteile dieser Architektur sind:
* Die gleichzeitige Verwaltung einer großen Anzahl von Kundenmandanten ist für den Dienstanbieter schwieriger.
* Die Administratoren des Dienstanbieters müssen im Verzeichnis des Kunden bereitgestellt werden.
* Der Dienstanbieter kann Daten nicht kundenübergreifend analysieren.

### <a name="2-central---logs-are-stored-in-workspace-located-in-the-service-provider-tenant"></a>2. Zentral: Protokolle werden in einem Arbeitsbereich gespeichert, der sich im Mandanten des Dienstanbieters befindet

Bei dieser Architektur werden die Protokolle nicht in den Mandanten des Kunden gespeichert, sondern nur an einem zentralen Speicherort in einem der Abonnements des Dienstanbieters. Die Agents, die auf den VMs des Kunden installiert sind, sind dafür konfiguriert, ihre Protokolle unter Verwendung der Arbeitsbereich-ID und des geheimen Schlüssels an diesen Arbeitsbereich zu senden.

Diese Architektur bietet diese Vorzüge:
* Es ist einfach, eine große Anzahl Kunden zu verwalten und sie in verschiedene Back-End-Systeme zu integrieren.
* Der Dienstanbieter ist im vollständigen Besitz der Protokolle und der verschiedenen Artefakte, wie etwa Funktionen und gespeicherte Abfragen.
* Der Dienstanbieter kann Analysen übergreifend über alle Kunden ausführen.

Die Nachteile dieser Architektur sind:
* Diese Architektur eignet sich nur für Agent-basierte VM-Daten, sie deckt PaaS-, SaaS- und Azure-Fabric-Datenquellen nicht ab.
* Es kann schwierig sein, die Daten der Kunden zu trennen, wenn sie in einem einzelnen Arbeitsbereich zusammengeführt werden. Das einzig gute Verfahren dazu ist die Verwendung des FQDNs (vollqualifizierten Domänennamens) des Computers oder der Azure-Abonnement-ID. 
* Alle Daten aller Kunden werden im gleichen Bereich mit einer einzelnen Rechnung und mit gleicher Vermerkdauer und gleichen Konfigurationseinstellungen gespeichert.
* Azure-Fabric- und PaaS-Dienste, wie Azure-Diagnose und Azure-Überwachung, erfordern einen Arbeitsbereich im gleichen Mandanten wie die Ressource, sie können also keine Protokolle an den zentralen Arbeitsbereich senden.
* Alle VM-Agents von allen Kunden werden beim zentralen Arbeitsbereich mit der gleichen Arbeitsbereichs-ID und dem gleichen Schlüssel authentifiziert. Es gibt kein Verfahren, Protokolle eines bestimmten Kunden zu blockieren, ohne andere Kunden zu unterbrechen.


### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybrid: Protokolle werden in einem Arbeitsbereich gespeichert, der sich im Mandanten des Kunden befindet, und einige von ihnen werden per Pull an einen zentralen Speicherort übertragen.

Die dritte Architektur stellt eine Mischung der zwei Optionen dar. Sie basiert auf der ersten, verteilten Architektur, in der die Protokolle lokal bei jedem Kunden sind, verwendet aber einen Mechanismus zur Erstellung eines zentralen Repositorys für Protokolle. Ein Teil der Protokolle wird zur Berichterstellung und Analyse per Pull an einen zentralen Speicherort übertragen. Bei diesem Teil kann es sich um eine kleine Anzahl Datentypen oder um eine Zusammenfassung der Aktivitäten handeln, wie etwa eine tägliche Statistik.

Es bestehen zwei Optionen zum Implementieren des zentralen Speicherorts in Log Analytics:

1. Zentraler Arbeitsbereich: Der Dienstanbieter kann einen Arbeitsbereich in seinem Mandanten erstellen und ein Skript verwenden, das die [Abfrage-API](https://dev.loganalytics.io/) in Kombination mit der [Datensammlungs-API](log-analytics-data-collector-api.md) nutzt, um die Daten aus den verschiedenen Arbeitsbereichen an diesen zentralen Speicherort zu bringen. Eine weitere Option, die kein Skript einsetzt, ist die Verwendung der [Azure-Logik-App](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview).

2. Power BI als zentraler Speicherort: Power BI kann als zentraler Speicherort dienen, wenn die verschiedenen Arbeitsbereiche mithilfe der Integration zwischen Log Analytics und [Power BI](log-analytics-powerbi.md) Daten an Power BI exportieren. 


## <a name="next-steps"></a>Nächste Schritte
* Automatisieren Sie mithilfe von [Resource Manager-Vorlagen](log-analytics-template-workspace-configuration.md) die Erstellung und Konfiguration von Arbeitsbereichen.
* Automatisieren Sie mit [PowerShell](log-analytics-powershell-workspace-configuration.md) die Erstellung von Arbeitsbereichen. 
* Verwenden Sie [Warnungen](log-analytics-alerts.md) für die Integration in vorhandene Systeme.
* Generieren Sie Zusammenfassungsberichte mit [Power BI](log-analytics-powerbi.md).

