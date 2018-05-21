---
title: Speichern und Anzeigen von Diagnosedaten in Azure Storage | Microsoft-Dokumentation
description: Azure-Diagnosedaten in den Azure-Speicher abrufen und anzeigen
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: tysonn
ms.assetid: 18e0780d-43e7-41e4-b8e9-f1fb9a36eb03
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: adegeo
ms.openlocfilehash: 248e1c87ec244649a296233bf2aaa323d3475a79
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2018
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Speichern und Anzeigen von Diagnosedaten im Azure-Speicher
Diagnosedaten werden nicht dauerhaft gespeichert, wenn sie nicht in den Microsoft Azure-Speicheremulator oder den Azure-Speicher übertragen werden. Sobald die Daten gespeichert wurden, können sie mit einem der verschiedenen verfügbaren Tools angezeigt werden.

## <a name="specify-a-storage-account"></a>Festlegen eines Speicherkontos
Legen Sie das Speicherkonto fest, das Sie in der Datei „ServiceConfiguration.cscfg“ verwenden möchten. Die Kontoinformationen werden als Verbindungszeichenfolge in einer Konfigurationseinstellung definiert. Das folgende Beispiel zeigt die Standardverbindungszeichenfolge, die für ein neues Clouddienstprojekt in Visual Studio erstellt wurde:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Sie können diese Verbindungszeichenfolge zum Bereitstellen von Kontoinformationen für ein Azure-Speicherkonto ändern.

Je nach Art der gesammelten Diagnosedaten verwendet die Azure-Diagnose entweder den Blob-Dienst oder den Tabellenspeicherdienst. Die folgende Tabelle zeigt die beibehaltenen Datenquellen und deren Format.

| Datenquelle | Speicherformat |
| --- | --- |
| Azure-Protokolle |Table |
| IIS 7.0-Protokolle |Blob |
| Infrastrukturprotokolle der Azure-Diagnose |Table |
| Ablaufprotokolle für fehlgeschlagene Anforderungen |Blob |
| Windows-Ereignisprotokolle |Table |
| Leistungsindikatoren |Table |
| Absturzabbilder |Blob |
| Benutzerdefinierte Fehlerprotokolle |Blob |

## <a name="transfer-diagnostic-data"></a>Übertragen von Diagnosedaten
Bei SDK 2.5 und höher kann die Anforderung zum Übertragen von Diagnosedaten über die Konfigurationsdatei erfolgen. Sie können Diagnosedaten in geplanten Intervallen wie in der Konfiguration festgelegt übertragen.

Bei SDK 2.4 und früher kann die Anforderung zum Übertragen von Diagnosedaten sowohl über die Konfigurationsdatei als auch programmgesteuert erfolgen. Der programmgesteuerte Ansatz ermöglicht Ihnen auch bedarfsgesteuerte Übertragungen.

> [!IMPORTANT]
> Wenn Sie Diagnosedaten in ein Azure-Speicherkonto übertragen, fallen Kosten für die von den Diagnosedaten genutzten Speicherressourcen an.
> 
> 

## <a name="store-diagnostic-data"></a>Speichern von Diagnosedaten
Protokolldaten werden im Blob- oder Tabellenspeicher mit den folgenden Namen gespeichert:

**Tabellen**

* **WadLogsTable** – Mit dem Ablaufverfolgungslistener in Code erstellte Protokolle
* **WADDiagnosticInfrastructureLogsTable** – Diagnosemonitor und Konfigurationsänderungen
* **WADDirectoriesTable** – Vom Diagnosemonitor überwachte Verzeichnisse.  Dies umfasst IIS-Protokolle, Protokolle zu IIS-Anforderungsfehlern und benutzerdefinierte Verzeichnisse.  Der Speicherort der Blob-Protokolldatei ist im Feld „Container“ festgelegt, ihr Name im Feld „RelativePath“.  Das Feld „AbsolutePath“ gibt den Speicherort und Namen der Datei wie auf dem virtuellen Azure-Computer vorhanden an.
* **WADPerformanceCountersTable** – Leistungsindikatoren
* **WADWindowsEventLogsTable** – Windows-Ereignisprotokolle

**Blobs**

* **wad-control-container** – (Nur für SDK 2.4 und früher) Enthält die XML-Konfigurationsdateien, die die Azure-Diagnose steuern.
* **wad-iis-failedreqlogfiles** – Enthält Informationen aus den Protokollen zu IIS-Anforderungsfehlern.
* **wad-iis-logfiles:** enthält Informationen zu IIS-Protokollen.
* **"custom"** – Ein benutzerdefinierter Container basierend auf Konfigurationsverzeichnissen, die vom Diagnosemonitor überwacht werden.  Der Name dieses Blob-Containers wird in WADDirectoriesTable festgelegt.

## <a name="tools-to-view-diagnostic-data"></a>Tools zum Anzeigen von Diagnosedaten
Mehrere Tools stehen zum Anzeigen der Daten nach der Übertragung an den Speicher zur Verfügung. Beispiel: 

* Server-Explorer in Visual Studio – wenn Sie die Azure-Tools für Microsoft Visual Studio installiert haben, können Sie den Azure-Speicherknoten im Server-Explorer zum Anzeigen schreibgeschützter Blob- und Tabellendaten aus Ihren Azure-Speicherkonten verwenden. Sie können Daten aus dem lokalen Speicheremulatorkonto und auch aus den für Azure erstellten Speicherkonten anzeigen. Weitere Informationen finden Sie unter [Durchsuchen und Verwalten von Speicherressourcen mit dem Server-Explorer](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md).
* Beim [Microsoft Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) handelt es sich um eine eigenständige App, über die Sie ganz einfach mit Azure Storage-Daten arbeiten können – unter Windows, OSX und Linux.
* [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) enthält Azure Diagnostics Manager, mit dem Sie die Diagnosedaten anzeigen, herunterladen und verwalten können, die von den auf Azure ausgeführten Anwendungen gesammelt werden.

## <a name="next-steps"></a>Nächste Schritte
[Verfolgen des Ablaufs in einer Cloud Services-Anwendung mit der Azure-Diagnose](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)

