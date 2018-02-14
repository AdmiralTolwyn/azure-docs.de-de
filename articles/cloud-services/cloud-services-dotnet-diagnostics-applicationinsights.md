---
title: Problembehandlung bei Clouddiensten mit Application Insights | Microsoft-Dokumentation
description: "Erfahren Sie mehr über das Beheben von Clouddienstproblemen mithilfe von Application Insights zum Verarbeiten von Daten aus der Azure-Diagnose."
services: cloud-services
documentationcenter: .net
author: sbtron
manager: timlt
editor: tysonn
ms.assetid: e93f387b-ef29-4731-ae41-0676722accb6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: saurabh
ms.openlocfilehash: 4001ca908ff00b1a40829d687589080e9b07b18a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-cloud-services-using-application-insights"></a>Problembehandlung bei Clouddiensten mit Application Insights
Mit [Azure SDK 2.8](https://azure.microsoft.com/downloads/) und Azure-Diagnose-Erweiterung 1.5 können Azure-Diagnose-Daten für Ihren Clouddienst direkt an Application Insights senden. Die Protokolle, die von der Azure-Diagnose &mdash;einschließlich Anwendungsprotokolle, Windows-Ereignisprotokolle, ETW-Protokolle und Leistungsindikatoren&mdash; gesammelt wurden, können an Application Insights gesendet werden. Sie können dann diese Informationen auf der Benutzeroberfläche des Application Insights-Portals visuell darstellen. Sie können das Application Insights SDK verwenden, um Einblicke in die Metriken und Protokolle von der Anwendung sowie in die Daten der System- und Infrastrukturebene von Azure-Diagnose zu erhalten.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Konfigurieren der Azure-Diagnose zum Senden von Daten an Application Insights
Führen Sie diese Schritte aus, um Ihr Clouddienstprojekt einzurichten, um Azure-Diagnose-Daten an Application Insights zu senden.

1. Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf eine Rolle, und wählen Sie **Eigenschaften** aus, um den Rollendesigner zu öffnen.

    ![Rolleneigenschaften des Projektmappen-Explorers][1]

2. Wählen Sie im **Diagnoseabschnitt** des Rollen-Designers die Option **Diagnosedaten an Application Insights senden** aus.

    ![Rollendesigner sendet Diagnosedaten an Application Insights][2]

3. Wählen Sie im angezeigten Dialogfeld die Application Insights-Ressource aus, an die Sie die Azure-Diagnosedaten senden möchten. Im Dialogfeld können Sie eine vorhandene Application Insights-Ressource aus Ihrem Abonnement auswählen oder manuell einen Instrumentierungsschlüssel für eine Application Insights-Ressource angeben. Weitere Informationen zum Erstellen einer Application Insights-Ressource finden Sie unter [Erstellen einer neuen Application Insights-Ressource](../application-insights/app-insights-create-new-resource.md).

    ![Auswählen einer Application Insights-Ressource][3]

    Wenn Sie die Application Insights-Ressource hinzugefügt haben, wird der Instrumentierungsschlüssel für diese Ressource als Dienstkonfigurationseinstellung mit dem Namen **APPINSIGHTS_INSTRUMENTATIONKEY** gespeichert. Sie können diese Konfigurationseinstellung für jede Dienstkonfiguration oder Umgebung ändern. Zu diesem Zweck wählen Sie eine andere Konfiguration aus der Liste **Dienstkonfiguration** aus und geben einen neuen Instrumentationsschlüssel für diese Konfiguration an.

    ![Auswählen einer Dienstkonfiguration][4]

    Die **APPINSIGHTS_INSTRUMENTATIONKEY**-Konfigurationseinstellung wird von Visual Studio zum Konfigurieren der Diagnoseerweiterung mit den entsprechenden Application Insights-Ressourceninformationen während der Veröffentlichung verwendet. Die Konfigurationseinstellung ist eine bequeme Möglichkeit, die verschiedenen Instrumentierungsschlüssel für verschiedene Dienstkonfigurationen zu definieren. Visual Studio übersetzt diese Einstellung und fügt sie beim Veröffentlichen in die öffentliche Konfiguration der Diagnose-Erweiterung ein. Um die Konfiguration der Diagnose-Erweiterung mit PowerShell zu vereinfachen, enthält die Paketausgabe von Visual Studio auch die öffentliche Konfigurations-XML mit dem entsprechenden Application Insights-Instrumentierungsschlüssel. Die öffentlichen Konfigurationsdateien werden im Ordner „Extensions“ erstellt und haben das folgende Namensgebungsmuster: *PaaSDiagnostics.&lt;Rollenname&gt;.PubConfig.xml*. Dieses Muster kann von allen PowerShell-basierten Bereitstellungen verwendet werden, um die einzelnen Konfigurationen einer Rolle zuzuordnen.

4) Durch Aktivieren von **Diagnosedaten an Application Insights senden** wird Azure-Diagnose automatisch konfiguriert, damit alle Leistungsindikatoren und Fehlerstufenprotokolle, die vom Azure-Diagnose-Agent gesammelt werden, an Application Insights gesendet werden. 

    Wenn Sie zusätzlich konfigurieren möchten, welche Daten an Application Insights gesendet werden, müssen Sie die *diagnostics.wadcfgx*-Datei für jede Rolle manuell bearbeiten. Weitere Informationen zum manuellen Aktualisieren der Konfiguration finden Sie unter [Konfigurieren von Azure-Diagnose zum Senden von Daten an Application Insights](#configure-azure-diagnostics-to-send-data-to-application-insights) .

Sobald der Clouddienst so konfiguriert wurde, dass Azure-Diagnose-Daten an Application Insights gesendet werden, können Sie ihn wie gewohnt in Azure bereitstellen. Stellen Sie dabei sicher, dass die Azure-Diagnose-Erweiterung aktiviert ist. Weitere Informationen finden Sie unter [Veröffentlichen eines Clouddiensts mit Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Anzeigen von Azure-Diagnose-Daten in Application Insights
Die Azure-Diagnose-Telemetrie wird in der Application Insights-Ressource angezeigt, die für den Clouddienst konfiguriert wurde.

Protokolltypen der Azure-Diagnose werden auf folgende Weise Application Insights zugeordnet:

* Leistungsindikatoren werden als benutzerdefinierte Metriken in Application Insights angezeigt.
* Windows-Ereignisprotokolle werden als Ablaufverfolgungen und benutzerdefinierte Ereignisse in Application Insights angezeigt.
* Anwendungsprotokolle, ETW-Protokolle und alle Diagnose-Infrastrukturprotokolle werden als Ablaufverfolgungen in Application Insights angezeigt.

Führen Sie einen der folgenden Schritte aus, um Azure-Diagnosedaten in Application Insights anzuzeigen:

* Verwenden Sie den [Metrik-Explorer](../application-insights/app-insights-metrics-explorer.md), um alle benutzerdefinierten Leistungsindikatoren oder die Anzahl verschiedener Arten von Windows-Ereignisprotokollereignissen zu visualisieren.

    ![Benutzerdefinierte Metriken im Metrik-Explorer][5]

* Verwenden Sie die [Suche](../application-insights/app-insights-diagnostic-search.md), um die Ablaufverfolgungsprotokolle zu durchsuchen, die von Azure-Diagnose gesendet werden. Wenn beispielsweise eine Rolle durch einen Ausnahmefehler abgestürzt ist und wieder aktiviert wurde, wird die Information über die Ausnahme im *Anwendungskanal* des *Windows-Ereignisprotokolls* angezeigt. Sie können das Windows-Ereignisprotokoll mithilfe der Suchfunktion untersuchen und die vollständige Stapelüberwachung für die Ausnahme abrufen, um den Grund für das Problem herauszufinden.

    ![Durchsuchen von Ablaufverfolgungen][6]

## <a name="next-steps"></a>Nächste Schritte
* [Fügen Sie dem Clouddienst das Application Insights-SDK hinzu](../application-insights/app-insights-cloudservices.md) , um Daten zu Anforderungen, Ausnahmen, Abhängigkeiten und einer benutzerdefinierten Telemetrie aus Ihrer Anwendung zu senden. Zusammen mit den Azure-Diagnose-Daten erhalten Sie eine umfassende Ansicht Ihrer Anwendung und des Systems in der gleichen Application Insights-Ressource.  

<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
