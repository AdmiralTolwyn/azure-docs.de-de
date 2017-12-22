---
title: "Erstellen von Warnungen für Azure-Dienste – plattformübergreifende Befehlszeilenschnittstelle | Microsoft-Dokumentation"
description: "E-Mails, Benachrichtigungen oder Automatisierung werden ausgelöst und URLs (Webhooks) von Websites aufgerufen, wenn die von Ihnen angegebenen Bedingungen erfüllt sind."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 5c6a2d27-7dcc-4f89-8752-9bb31b05ff35
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: robb
ms.openlocfilehash: 92246a8da73a244a1c9a924bed55711d71a20fd8
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/21/2017
---
# <a name="create-metric-alerts-in-azure-monitor-for-azure-services---cross-platform-cli"></a>Erstellen von Metrikwarnungen in Azure Monitor für Azure-Dienste – plattformübergreifende Befehlszeilenschnittstelle
> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Übersicht
In diesem Artikel erfahren Sie, wie Sie Azure-Metrikwarnungen mit der plattformübergreifenden Befehlszeilenschnittstelle (CLI) einrichten können.

> [!NOTE]
> Azure Monitor ist der neue Name für den Dienst, der bis 25. September 2016 als „Azure Insights“ bezeichnet wurde. Die Namespaces und somit die folgenden Befehle enthalten jedoch weiterhin den Bezeichner „insights“.
>
>

Sie können auf der Grundlage von Überwachungsmetriken für Ihre Azure-Services oder von Ereignissen, die bei diesen auftreten, eine Warnung empfangen.

* **Metrikwerte** : Die Warnung wird ausgelöst, wenn der Wert einer angegebenen Metrik einen von Ihnen festgelegten Schwellenwert in beliebiger Richtung überschreitet. Das Auslösen erfolgt sowohl, wenn die Bedingung erstmals erfüllt wird, als auch danach, wenn diese Bedingung nicht mehr erfüllt wird.    
* **Aktivitätsprotokollereignisse**: Eine Warnung kann für *jedes* Ereignis oder nur dann ausgelöst werden, wenn ein bestimmtes Ereignis auftritt. [Klicken Sie hier](monitoring-activity-log-alerts.md), um weitere Informationen zu Aktivitätsprotokollwarnungen zu erhalten.

Sie können konfigurieren, dass bei einer Metrikwarnung Folgendes erfolgt, wenn sie ausgelöst wird:

* Senden von E-Mail-Benachrichtigungen an den Dienstadministrator und Co-Administratoren
* Senden von E-Mal an weitere von Ihnen angegebene Adressen
* Aufrufen eines Webhooks
* Starten der Ausführung eines Azure-Runbooks (derzeit nur über das Azure-Portal)

Sie haben folgende Möglichkeiten zum Konfigurieren von Metrikwarnregeln und Abrufen zugehöriger Informationen:

* [Azure-Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Befehlszeilenschnittstelle (CLI)](insights-alerts-command-line-interface.md)
* [Azure Monitor-REST-API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Sie können stets Hilfe zu Befehlen erhalten, indem Sie einen Befehl eingeben und am Ende „-help“ hinzufügen. Beispiel:

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>Erstellen von Warnregeln mithilfe der CLI
1. Erfüllen Sie die Voraussetzungen, und melden Sie sich bei Azure an. Siehe [CLI-Beispiele für Azure Monitor](insights-cli-samples.md). Installieren Sie also die CLI, und führen Sie diese Befehle aus. Über diese Befehle werden Sie angemeldet und über das verwendete Abonnement informiert. Außerdem wird die Ausführung von Azure Monitor-Befehlen vorbereitet.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. Um vorhandene Regeln für eine Ressourcengruppe aufzulisten, verwenden Sie das folgende Format: **azure insights alerts rule list** *[Optionen] &lt;resourceGroup&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. Um eine Regel zu erstellen, benötigen Sie zunächst verschiedene wichtige Informationen.
  * Die **Ressourcen-ID** der Ressource, für die Sie eine Warnung festlegen möchten
  * Die für diese Ressource verfügbaren **Metrikdefinitionen**

     Eine Möglichkeit zum Abrufen der Ressourcen-ID ist das Azure-Portal. Falls die Ressource bereits erstellt wurde, wählen Sie sie im Portal aus. Wählen Sie dann auf dem nächsten Blatt im Abschnitt *Einstellungen* die Option *Eigenschaften* aus. Die *Ressourcen-ID* ist ein Feld auf dem nächsten Blatt. Eine andere Möglichkeit ist der [Azure-Ressourcen-Explorer](https://resources.azure.com/).

     Es folgt ein Beispiel einer Ressourcen-ID für eine Web-App:

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Um eine Liste der verfügbaren Metriken und Einheiten für diese Metriken für das vorherige Ressourcenbeispiel zu erhalten, geben Sie den folgenden CLI-Befehl ein:  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* ist die Granularität der verfügbaren Messung (1-Minuten-Intervalle). Durch Verwenden verschiedener Granularitäten erhalten Sie verschiedene Metrikoptionen.
4. Geben Sie zum Erstellen einer metrikbasierten Warnregel einen Befehl im folgenden Format ein:

    **azure insights alerts rule metric set***[Optionen] &lt;ruleName&gt;&lt;location&gt;&lt;resourceGroup&gt;&lt;windowSize&gt;&lt;operator&gt;&lt;threshold&gt;&lt;targetResourceId&gt;&lt;metricName&gt;&lt;timeAggregationOperator&gt;*

    Im folgenden Beispiel wird eine Warnung für eine Websiteressource eingerichtet. Die Warnung wird ausgelöst, wenn fünf Minuten durchgängig Datenverkehr empfangen wird. Sie wird erneut ausgelöst, wenn fünf Minuten lang kein Datenverkehr empfangen wird.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. Um einen Webhook zu erstellen oder eine E-Mail zu senden, wenn eine Metrikwarnung ausgelöst wird, müssen Sie zunächst die E-Mail und/oder Webhooks erstellen. Erstellen Sie dann die Regel unmittelbar danach. Über die CLI können Sie bereits erstellten Regeln keine Webhooks oder E-Mails zuordnen.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. Sie können überprüfen, ob Warnungen ordnungsgemäß erstellt wurden, indem Sie sich eine einzelne Regel ansehen.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. Verwenden Sie zum Löschen von Regeln einen Befehl mit dem folgenden Format:

    **insights alerts rule delete** [Optionen] &lt;resourceGroup&gt;&lt;ruleName&gt;

    Mit diesen Befehlen werden die zuvor in diesem Artikel erstellten Regeln gelöscht.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>Nächste Schritte
* [Übersicht über die Azure-Überwachung](monitoring-overview.md) , einschließlich der Typen von Informationen, die Sie sammeln und überwachen können.
* Erfahren Sie mehr über das [Konfigurieren von Webhooks in Warnungen](insights-webhooks-alerts.md).
* Erfahren Sie mehr über das [Konfigurieren von Warnungen zu Aktivitätsprotokollereignissen](monitoring-activity-log-alerts.md).
* Erfahren Sie mehr zu [Azure Automation-Runbooks](../automation/automation-starting-a-runbook.md).
* Verschaffen Sie sich einen [Überblick über das Sammeln von Diagnoseprotokollen](monitoring-overview-of-diagnostic-logs.md) , um detaillierte Hochfrequenzmetriken für Ihren Dienst zu erfassen.
* Verschaffen Sie sich einen Überblick über das [Sammeln von Dienstmetriken](insights-how-to-customize-monitoring.md) , um sicherzustellen, dass Ihr Dienst verfügbar und reaktionsfähig ist.
