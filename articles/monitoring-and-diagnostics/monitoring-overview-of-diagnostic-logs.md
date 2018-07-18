---
title: Übersicht über Azure-Diagnoseprotokolle
description: Hier erfahren Sie, worum es sich bei Azure-Diagnoseprotokollen handelt, und wie Sie mithilfe von Diagnoseprotokollen Ereignisse innerhalb einer Azure-Ressource nachvollziehen.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: a6435f74141429cbe4f9a169fd2f234161d486c4
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918739"
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Erfassen und Nutzen von Protokolldaten aus Ihren Azure-Ressourcen

## <a name="what-are-azure-resource-diagnostic-logs"></a>Was sind Diagnoseprotokolle auf Azure-Ressourcenebene?

**Diagnoseprotokolle auf Azure-Ressourcenebene** sind von einer Ressource ausgegebene Protokolle mit umfangreichen, in kurzen Abständen erfassten Betriebsdaten der Ressource. Der Inhalt dieser Protokolle variiert je nach Ressourcentyp. Beispielweise sind Netzwerksicherheitsgruppen-Regelzähler und Key Vault-Überwachungen zwei Kategorien von Ressourcenprotokollen.

Diagnoseprotokolle auf Ressourcenebene unterscheiden sich vom [Aktivitätsprotokoll](monitoring-overview-activity-logs.md). Das Aktivitätsprotokoll ermöglicht Einblicke in die Vorgänge, die für Ressourcen Ihres Abonnements mit dem Resource Manager durchgeführt wurden, z.B. das Erstellen eines virtuellen Computers oder das Löschen einer Logik-App. Das Aktivitätsprotokoll ist ein Protokoll auf Abonnementebene. Diagnoseprotokolle auf Ressourcenebene ermöglichen Einblicke in Vorgänge, die für die Ressource selbst durchgeführt wurden, z.B. das Abrufen eines Geheimnisses aus einem Key Vault.

Diagnoseprotokolle auf Ressourcenebene unterscheiden sich auch von Diagnoseprotokollen auf Gastbetriebssystemebene. Diagnoseprotokolle auf Gastbetriebssystemebene werden von einem Agent erfasst, der auf einem virtuellen Computer oder einem anderen Ressourcentyp ausgeführt wird. Für Diagnoseprotokolle auf Ressourcenebene ist kein Agent erforderlich, und ressourcenspezifische Daten werden von der Azure-Plattform selbst erfasst. Für Diagnoseprotokolle auf Gastbetriebssystemebene werden Daten dagegen aus dem Betriebssystem und den Anwendungen erfasst, die auf einem virtuellen Computer ausgeführt werden.

Die hier beschriebene neue Art von Diagnoseprotokollen für Ressourcen wird nicht von allen Ressourcen unterstützt. Dieser Artikel enthält einen Abschnitt, in dem aufgeführt ist, welche Ressourcentypen die neuen Diagnoseprotokolle der Ressourcenebene unterstützen.

![Ressourcendiagnoseprotokolle im Vergleich zu anderen Protokolltypen ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>Möglichkeiten mit Diagnoseprotokollen auf Ressourcenebene
Hier sind einige Verwendungsmöglichkeiten für Diagnoseprotokolle auf Ressourcenebene aufgeführt:

![Logische Anordnung von Diagnoseprotokollen für Ressourcen](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)

* Diagnoseprotokolle können zur Überwachung oder manuellen Überprüfung in einem [**Speicherkonto**](monitoring-archive-diagnostic-logs.md) gespeichert werden. Mithilfe der **Diagnoseeinstellungen für Ressourcen** können Sie eine Aufbewahrungsdauer (in Tagen) angeben.
* [Sie können Diagnoseprotokolle zur Erfassung durch einen Drittanbieterdienst oder durch eine benutzerdefinierte Analyselösung wie Power BI an **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md) streamen.
* Analysieren Sie sie mit [Log Analytics](../log-analytics/log-analytics-azure-storage.md).

Sie können ein Speicherkonto oder Event Hubs-Namespace verwenden, das sich nicht im gleichen Abonnement befindet wie das, das Protokolle angibt. Der Benutzer, der die Einstellung konfiguriert, benötigt den entsprechenden RBAC-Zugriff auf beide Abonnements.

> [!NOTE]
>  Sie können derzeit keine Daten in einem Speicherkonto archivieren, das sich hinter einem geschützten virtuellen Netzwerk befindet.

> [!WARNING]
> Das Format der Protokolldaten im Speicherkonto wird am 1. November 2018 in JSON Lines geändert. [Dieser Artikel enthält eine Beschreibung der Auswirkungen und der Aktualisierung Ihrer Tools zur Verarbeitung des neuen Formats.](./monitor-diagnostic-logs-append-blobs.md) 
>
> 

## <a name="resource-diagnostic-settings"></a>Diagnoseeinstellungen für Ressourcen

Ressourcendiagnoseprotokolle für computefremde Ressourcen werden mithilfe von Diagnoseeinstellungen für Ressourcen konfiguriert. Mit **Diagnoseeinstellungen für Ressourcen** für ein Ressourcensteuerelement können Sie Folgendes festlegen:

* Wohin Ressourcendiagnoseprotokolle und Metriken gesendet werden sollen (Speicherkonto, Event Hubs und/oder Log Analytics).
* Welche Protokollkategorien gesendet werden, und ob auch Metrikdaten gesendet werden.
* Wie lange die einzelnen Protokollkategorien in einem Speicherkonto beibehalten werden sollen
    - Wenn für die Beibehaltungsdauer 0 Tage festgelegt sind, bedeutet dies, dass Protokolle unbegrenzt beibehalten werden. Andernfalls kann als Wert die Anzahl von Tagen (1 bis 2.147.483.647) festgelegt werden.
    - Wenn Aufbewahrungsrichtlinien festgelegt wurden, aber das Speichern von Protokollen in einem Speicherkonto deaktiviert ist (etwa, wenn nur die Optionen „Event Hubs“ oder „Log Analytics“ ausgewählt sind), werden die Aufbewahrungsrichtlinien ignoriert.
    - Aufbewahrungsrichtlinien werden pro Tag angewendet, sodass Protokolle am Ende eines Tages (UTC) ab dem Tag, der nun außerhalb der Aufbewahrungsrichtlinie liegt, gelöscht werden. Beispiel: Wenn Sie eine Aufbewahrungsrichtlinie für einen Tag verwenden, werden heute am Anfang des Tages die Protokolle von vorgestern gelöscht. Der Löschvorgang beginnt um Mitternacht (UTC), jedoch kann es bis zu 24 Stunden dauern, bis die Protokolle aus Ihrem Speicherkonto gelöscht werden.

Diese Einstellungen können für eine Ressource ganz einfach über die Diagnoseeinstellungen im Azure-Portal, mithilfe von Azure PowerShell oder CLI-Befehlen oder über die [Azure Monitor-REST-API](https://msdn.microsoft.com/library/azure/dn931943.aspx) konfiguriert werden.

> [!NOTE]
> Das Senden mehrdimensionaler Metriken über die Diagnoseeinstellungen wird derzeit nicht unterstützt. Metriken mit Dimensionen werden als vereinfachte eindimensionale Metriken exportiert und dimensionswertübergreifend aggregiert.
>
> *Beispiel:* Die Metrik „Eingehende Nachrichten“ eines Event Hubs kann auf einer warteschlangenspezifischen Ebene untersucht und in einem Diagramm dargestellt werden. Wenn Sie die Metrik allerdings über die Diagnoseeinstellungen exportieren, umfasst die Darstellung alle eingehenden Nachrichten für alle Warteschlangen im Event Hub.
>
>

> [!WARNING]
> Bei Diagnoseprotokollen und Metriken für Computeressourcen aus der Gastbetriebssystemebene (etwa VMs oder Service Fabric) wird [ein separater Mechanismus für die Konfiguration und Auswahl von Ausgaben](../azure-diagnostics.md) verwendet.

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>Gewusst wie: Aktivieren der Erfassung von Diagnoseprotokollen für Ressourcen

Die Erfassung von Diagnoseprotokollen für Ressourcen kann [im Zuge der Ressourcenerstellung in einer Resource Manager-Vorlage](./monitoring-enable-diagnostic-logs-using-template.md) oder später im Portal über die Seite der Ressource aktiviert werden. Darüber hinaus können Sie die Erfassung jederzeit mithilfe von Azure PowerShell oder CLI-Befehlen oder mithilfe der Azure Monitor-REST-API aktivieren.

> [!TIP]
> Diese Anweisungen lassen sich unter Umständen nicht immer direkt auf jede Ressource anwenden. Informationen zu Sonderschritten, die ggf. für bestimmte Ressourcentypen erforderlich sind, finden Sie unter den Schemalinks am Ende dieser Seite.

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>Aktivieren der Erfassung von Diagnoseprotokollen für Ressourcen im Portal

Sie können die Erfassung von Diagnoseprotokollen für Ressourcen nach dem Erstellen einer Ressource im Azure-Portal aktivieren, indem Sie entweder zu einer bestimmten Ressource oder zu Azure Monitor navigieren. So aktivieren Sie dies über Azure Monitor:

1. Navigieren Sie im [Azure-Portal](http://portal.azure.com) zu Azure Monitor, und klicken Sie auf **Diagnoseeinstellungen**.

    ![Abschnitt „Überwachung“ von Azure Monitor](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

2. Filtern Sie optional die Liste nach Ressourcengruppe oder Ressourcentyp, und klicken Sie auf die Ressource, für die Sie eine Diagnoseeinstellung festlegen möchten.

3. Wenn keine Einstellungen für die Ressource vorhanden sind, die Sie ausgewählt haben, werden Sie aufgefordert, eine Einstellung zu erstellen. Klicken Sie auf „Diagnose aktivieren“.

   ![Diagnoseeinstellung hinzufügen – keine Einstellungen vorhanden](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-none.png)

   Wenn Einstellungen für die Ressource vorhanden sind, sehen Sie eine Liste der Einstellungen, die bereits für diese Ressource konfiguriert sind. Klicken Sie auf „Diagnoseeinstellung hinzufügen“.

   ![Diagnoseeinstellung hinzufügen – Einstellungen vorhanden](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-multiple.png)

3. Geben Sie Ihrer Einstellung einen Namen, aktivieren Sie die Kontrollkästchen für die einzelnen Ziele, an die Sie Daten senden möchten, und konfigurieren Sie, welche Ressourcen für die einzelnen Ziele verwendet werden. Legen Sie optional eine Anzahl von Tagen für die Aufbewahrung dieser Protokolle fest. Verwenden Sie dazu die Schieberegler unter **Aufbewahrung (Tage)** (gilt nur für das Speicherkontoziel). Bei einer Aufbewahrung von 0 Tagen werden die Protokolle dauerhaft gespeichert.

   ![Diagnoseeinstellung hinzufügen – Einstellungen vorhanden](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-configure.png)

4. Klicken Sie auf **Speichern**.

Nach einigen Augenblicken wird die neue Einstellung in der Liste der Einstellungen für diese Ressource angezeigt, und Diagnoseprotokolle werden an die angegebenen Ziele gesendet, sobald neue Ereignisdaten generiert werden.

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Aktivieren der Erfassung von Diagnoseprotokollen für Ressourcen mit PowerShell

Verwenden Sie die folgenden Befehle, um die Erfassung von Diagnoseprotokollen für Ressourcen mit Azure PowerShell zu aktivieren:

Verwenden Sie den folgenden Befehl, um das Speichern von Diagnoseprotokollen in einem Speicherkonto zu aktivieren:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

Die Speicherkonto-ID ist die Ressourcen-ID für das Speicherkonto, an das die Protokolle gesendet werden sollen.

Verwenden Sie den folgenden Befehl, um das Streamen von Diagnoseprotokollen an eine Event Hub-Instanz zu aktivieren:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

Die Service Bus-Regel-ID ist eine Zeichenfolge mit dem folgenden Format: `{Service Bus resource ID}/authorizationrules/{key name}`.

Verwenden Sie den folgenden Befehl, um das Senden von Diagnoseprotokollen an einen Log Analytics-Arbeitsbereich zu aktivieren:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Sie können die Ressourcen-ID mit dem folgenden Befehl aus Ihrem Log Analytics-Arbeitsbereich abrufen:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Sie können diese Parameter miteinander kombinieren, um mehrere Ausgabeoptionen zu aktivieren.

### <a name="enable-collection-of-resource-diagnostic-logs-via-azure-cli-20"></a>Aktivieren der Erfassung von Diagnoseprotokollen für Ressourcen per Azure CLI 2.0

Zum Aktivieren der Auflistung von Ressourcendiagnoseprotokollen über die Azure CLI 2.0 verwenden Sie den Befehl [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

Aktivieren der Speicherung von Diagnoseprotokollen in einem Speicherkonto:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Das `--resource-group`-Argument ist nur erforderlich, wenn `--storage-account` keine Objekt-ID ist.

Aktivieren des Streamings von Diagnoseprotokollen an eine Event Hub-Instanz:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Die Regel-ID ist eine Zeichenfolge in folgendem Format: `{Service Bus resource ID}/authorizationrules/{key name}`

Aktivieren der Sendung von Diagnoseprotokollen an einen Log Analytics-Arbeitsbereich:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Das `--resource-group`-Argument ist nur erforderlich, wenn `--workspace` keine Objekt-ID ist.

Sie können das Diagnoseprotokoll mithilfe eines beliebigen Befehls mit weiteren Kategorien ergänzen, indem Sie dem JSON-Array, das als der `--logs`-Parameter übergeben wird, Wörterbücher hinzufügen. Sie können Parameter `--storage-account`, `--event-hub` und `--workspace` miteinander kombinieren, um mehrere Ausgabeoptionen zu aktivieren.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Aktivieren der Erfassung von Diagnoseprotokollen für Ressourcen per REST-API

Informationen zum Ändern der Diagnoseeinstellungen mithilfe der Azure Monitor-REST-API finden Sie in [diesem Dokument](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Verwalten von Diagnoseeinstellungen für Ressourcen im Portal

Stellen Sie sicher, dass für alle Ihre Ressourcen Diagnoseeinstellungen festgelegt sind. Navigieren Sie im Portal zu **Überwachen**, und öffnen Sie **Diagnoseeinstellungen**.

![Das Blatt „Diagnoseprotokolle“ im Portal](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-nav.png)

Möglicherweise müssen Sie auf „Alle Dienste“ klicken, um den Abschnitt „Überwachen“ zu finden.

Hier können Sie alle Ressourcen anzeigen und filtern, die Diagnoseeinstellungen unterstützen, um zu ermitteln, ob die Diagnose dafür aktiviert ist. Sie können auch einen Drilldown ausführen, um zu sehen, ob mehrere Einstellungen für eine Ressource festgelegt sind, und überprüfen, zu welchem Speicherkonto, Event Hubs-Namespace und/oder Log Analytics-Arbeitsbereich die Daten fließen.

![Ergebnisse von Diagnoseprotokollen im Portal](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

Wenn Sie eine Diagnoseeinstellung hinzufügen, wird das Blatt „Diagnoseeinstellungen“ angezeigt, auf dem Sie die Diagnoseeinstellungen für die ausgewählte Ressource aktivieren, deaktivieren oder ändern können.

## <a name="supported-services-categories-and-schemas-for-resource-diagnostic-logs"></a>Unterstützte Dienste, Kategorien und Schemas für Ressourcendiagnoseprotokolle

[In diesem Artikel](monitoring-diagnostic-logs-schema.md) finden Sie eine vollständige Liste der unterstützten Dienste, Protokollkategorien und Schemas, die von diesen Diensten verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

* [Streamen von Diagnoseprotokollen für Ressourcen an **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* Ändern der Diagnoseeinstellungen für Ressourcen mithilfe der Azure Monitor-REST-API ([Service Diagnostic Settings](https://msdn.microsoft.com/library/azure/dn931931.aspx) (Diagnoseeinstellungen für Dienste))
* [Analysieren von Protokollen aus Azure Storage mit Log Analytics](../log-analytics/log-analytics-azure-storage.md)
