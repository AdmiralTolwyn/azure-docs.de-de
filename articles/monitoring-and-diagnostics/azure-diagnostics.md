---
title: Übersicht zur Azure-Diagnoseerweiterung
description: Verwenden Sie die Azure-Diagnose zur Problembehandlung, zur Leistungsmessung, zur Überwachung und zur Datenverkehrsanalyse in Clouddiensten, virtuellen Maschinen und Service Fabric.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: a40917ec24ede1107f7d8ae7f5fb2f0f03d1094c
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278326"
---
# <a name="what-is-azure-diagnostics-extension"></a>Was ist die Azure-Diagnoseerweiterung?
Die Azure-Diagnoseerweiterung ist ein Agent innerhalb von Azure, mit dem Diagnosedaten für eine bereitgestellte Anwendung erfasst werden können. Sie können die Diagnoseerweiterung von einer Reihe verschiedener Quellen aus verwenden. Derzeit werden die Web- und Workerrollen des Azure-Clouddiensts (klassisch), Virtual Machines, Virtual Machine Scale Sets und Service Fabric unterstützt. Andere Azure-Dienste haben unterschiedliche Diagnosemethoden. Siehe [Übersicht über die Überwachung in Microsoft Azure](../azure-monitor/overview.md).

## <a name="linux-agent"></a>Linux-Agent
Für virtuelle Computer mit Linux ist eine [Linux-Version der Erweiterung](../virtual-machines/extensions/diagnostics-linux.md) verfügbar. Die gesammelten Statistiken und das Verhalten unterscheiden sich von der Windows-Version.

## <a name="data-you-can-collect"></a>Erfassbare Daten
Die Azure-Diagnoseerweiterung kann die folgenden Datentypen erfassen:

| Data source | BESCHREIBUNG |
| --- | --- |
| Leistungsindikatoren |Leistungsindikatoren des Betriebssystems und benutzerdefinierte Leistungsindikatoren |
| Anwendungsprotokolle |Von Ihrer Anwendung geschriebene Ablaufverfolgungsmeldungen |
| Windows-Ereignisprotokolle |An das Windows-System für die Ereignisprotokollierung gesendete Informationen |
| .NET-Ereignisquelle |Code zum Schreiben von Ereignissen mit der .NET-Klasse [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) |
| IIS-Protokolle |Informationen zu IIS-Websites |
| Manifestbasiertes ETW |Von einem beliebigen Prozess generierte Ereignisse der Ereignisablaufverfolgung für Windows.(1) |
| Absturzabbilder |Informationen zum Status des Prozesses im Fall eines Anwendungsabsturzes |
| Benutzerdefinierte Fehlerprotokolle |Von Ihrer Anwendung oder Ihrem Dienst erstellte Protokolle |
| Infrastrukturprotokolle der Azure-Diagnose |Informationen zur Diagnose selbst |

(1) Um eine Liste der ETW-Anbieter zu erhalten, führen Sie `c:\Windows\System32\logman.exe query providers` in einem Konsolenfenster auf dem Computer aus, von dem Sie die Informationen sammeln möchten.

## <a name="data-storage"></a>Datenspeicher
Die Erweiterung speichert ihre Daten in einem von Ihnen angegebenen [Azure Storage-Konto](azure-diagnostics-storage.md).

Sie können auch an [Application Insights](../application-insights/app-insights-cloudservices.md) gesendet werden. Als weitere Option können sie auch an [Event Hub](../event-hubs/event-hubs-about.md) gestreamt werden, was Ihnen wiederum ermöglicht, sie an Überwachungsdienste außerhalb von Azure zu senden.

### <a name="azure-monitor"></a>Azure Monitor
Sie können Ihre Daten auch an Azure Monitor senden. Zur Zeit ist diese Senke nur für Leistungsindikatoren anwendbar. Sie ermöglicht es Ihnen, Leistungsindikatoren, die auf Ihrem virtuellen Computer, Ihrer VMSS oder in Ihrem Clouddienst gesammelt wurden, als benutzerdefinierte Metriken an Azure Monitor zu senden. Die Azure Monitor-Senke unterstützt Folgendes:
* Abrufen aller Leistungsindikatoren, die über die [APIs der Azure Monitor-Metriken](https://docs.microsoft.com/rest/api/monitor/) an Azure Monitor gesendet wurden.
* Benachrichtigung für alle Leistungsindikatoren, die über die neue [einheitliche Oberfläche für Warnungen](monitoring-overview-unified-alerts.md) in Azure Monitor an Azure Monitor gesendet werden.
* Behandeln von Platzhalteroperatoren in Leistungsindikatoren als die Dimension „Instanz“ in Ihrer Metrik.  Wenn Sie z. B. den Indikator „LogicalDisk(\*)/DiskWrites/Sek.“ erfasst haben, können Sie die Dimension „Instanz“ filtern und aufteilen, um für jeden logischen Datenträger auf dem virtuellen Computer (C:, D: usw.) Schreibvorgänge pro Sekunde darzustellen oder hinsichtlich dieser Vorgänge Warnungen auszugeben.

Weitere Informationen zur Konfiguration dieser Senke finden Sie in der Dokumentation zum [Azur-Diagnoseschema](azure-diagnostics-schema-1dot3-and-later.md).

## <a name="versioning-and-configuration-schema"></a>Schema für Versionsverwaltung und Konfiguration
Siehe [Azure-Diagnoseerweiterung – Versionen und Verlauf des Konfigurationsschemas](azure-diagnostics-schema.md).


## <a name="next-steps"></a>Nächste Schritte
Wählen Sie, zu welchem Dienst Sie Diagnoseinformationen erfassen möchten, und lesen Sie zum Einstieg die folgenden Artikel. Verwenden Sie die allgemeinen Links zur Azure-Diagnose als Referenz für bestimmte Aufgaben.

## <a name="cloud-services-using-azure-diagnostics"></a>Clouddienste mit der Azure-Diagnose
* Wenn Sie Visual Studio verwenden, finden Sie unter [Verfolgen einer Cloud Services-Anwendung mit Visual Studio](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) Informationen für den Einstieg. Andernfalls finden Sie weitere Informationen in folgenden Artikeln:
* [Überwachen von Clouddiensten mit der Azure-Diagnose](../cloud-services/cloud-services-how-to-monitor.md)
* [Einrichten der Azure-Diagnose in einer Cloud Services-Anwendung](../cloud-services/cloud-services-dotnet-diagnostics.md)

Fortgeschrittenere Themen finden Sie in folgenden Artikeln:

* [Verwenden der Azure-Diagnose mit Application Insights für Cloud Services](../application-insights/app-insights-cloudservices.md)
* [Verfolgen des Ablaufs einer Cloud Services-Anwendung mit der Azure-Diagnose](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Verwenden von PowerShell zum Einrichten der Diagnose für Cloud Services](../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtual Machines
* Wenn Sie Visual Studio verwenden, finden Sie unter [Verfolgen von Azure Virtual Machines mit Visual Studio](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) Informationen für den Einstieg. Andernfalls finden Sie weitere Informationen in folgenden Artikeln:
* [Einrichten der Azure-Diagnose auf einem virtuellen Azure-Computer](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

Fortgeschrittenere Themen finden Sie in folgenden Artikeln:

* [Verwenden von PowerShell zum Einrichten der Diagnose für Azure Virtual Machines](../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Erstellen eines virtuellen Windows-Computers mit Überwachung und Diagnose mithilfe von Azure-Ressourcen-Manager-Vorlagen](../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Erste Schritte finden Sie unter [Überwachen einer Service Fabric-Anwendung](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Viele weitere Artikel zur Service Fabric-Diagnose stehen in der Navigationsstruktur auf der linken Seite zur Verfügung, nachdem Sie diesen Artikel aufgerufen haben.

## <a name="general-articles"></a>Allgemeine Artikel
* Hier erhalten Sie Informationen zur Verwendung von [Leistungsindikatoren in der Azure-Diagnose](../cloud-services/diagnostics-performance-counters.md).
* Wenn bei der Diagnose Probleme mit dem Starten oder dem Finden von Daten in Azure-Speichertabellen auftreten, finden Sie weitere Informationen unter [Problembehandlung mit Azure-Diagnose](azure-diagnostics-troubleshooting.md).
