---
title: Gespeicherte Suchen und Warnungen in Verwaltungslösungen | Microsoft-Dokumentation
description: Verwaltungslösungen enthalten in der Regel gespeicherte Suchen in Log Analytics zum Analysieren der von der Lösung erfassten Daten. Sie können auch Warnungen zur Benachrichtigung des Benutzers definieren oder als Reaktion auf ein schwerwiegendes Problem automatisch Maßnahmen ergreifen. Dieser Artikel beschreibt das Definieren von in Log Analytics gespeicherten Suchen und Warnungen in einer Resource Manager-Vorlage, damit sie in Verwaltungslösungen aufgenommen werden können.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/18/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c46c65fe7e720e3afdd976e2480e8f183cc4398d
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54901189"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Hinzufügen von gespeicherten Log Analytics-Suchen und -Warnungen in der Verwaltungslösung (Vorschau)

> [!NOTE]
> Dies ist die vorläufige Dokumentation für das Erstellen von Verwaltungslösungen, die sich derzeit in der Vorschau befinden. Jedes unten beschriebene Schema kann sich ändern.

[Verwaltungslösungen](solutions.md) enthalten in der Regel [gespeicherte Suchen](../../azure-monitor/log-query/log-query-overview.md) in Log Analytics zum Analysieren der von der Lösung erfassten Daten. Sie können auch [Warnungen](../../azure-monitor/platform/alerts-overview.md) zur Benachrichtigung des Benutzers definieren oder als Reaktion auf ein schwerwiegendes Problem automatisch Maßnahmen ergreifen. Dieser Artikel beschreibt das Definieren von in Log Analytics gespeicherten Suchen und Warnungen in einer [Ressourcenverwaltungsvorlage](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md), damit sie in [Verwaltungslösungen](solutions-creating.md) aufgenommen werden können.

> [!NOTE]
> Die Beispiele in diesem Artikel verwenden Parameter und Variablen, die entweder erforderlich sind oder für Verwaltungslösungen gelten und unter [Entwerfen und Erstellen einer Verwaltungslösung in Azure](solutions-creating.md) beschrieben sind.

## <a name="prerequisites"></a>Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie schon mit der [Erstellung einer Verwaltungslösung](solutions-creating.md) und der Struktur einer [Resource Manager-Vorlage](../../azure-resource-manager/resource-group-authoring-templates.md) und Lösungsdatei vertraut sind.


## <a name="log-analytics-workspace"></a>Log Analytics-Arbeitsbereich
Alle Ressourcen in Log Analytics befinden sich in einem [Arbeitsbereich](../../azure-monitor/platform/manage-access.md). Wie unter [Log Analytics-Arbeitsbereich und Automation-Konto](solutions.md#log-analytics-workspace-and-automation-account) beschrieben, ist der Arbeitsbereich nicht in der Verwaltungslösung enthalten, muss aber vor der Installation der Lösung vorhanden sein. Ist er nicht verfügbar, schlägt die Installation der Lösung fehl.

Der Name des Arbeitsbereichs ist im Namen jeder Log Analytics-Ressource enthalten. Dafür sorgt wie in diesem Beispiel einer SavedSearch-Ressource der Parameter **workspace** in der Lösung.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Log Analytics-API-Version
Alle in einer Resource Manager-Vorlage definierten Log Analytics-Ressourcen verfügen über die Eigenschaft **apiVersion**, die die Version der API definiert, die von der Ressource verwendet werden soll.

Die folgende Tabelle enthält die API-Versionen für die Ressource, die in diesem Beispiel verwendet wird.

| Ressourcentyp | API-Version | Abfragen |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Event &#124; where EventLevelName == "Error"  |


## <a name="saved-searches"></a>Gespeicherte Suchen
Schließen Sie [gespeicherte Suchen](../../azure-monitor/log-query/log-query-overview.md) in eine Lösung ein, um Benutzern das Abfragen der von der Lösung erfassten Daten zu ermöglichen. Gespeicherte Suchen werden im Azure-Portal unter **Gespeicherte Suchen** angezeigt. Eine gespeicherte Suche ist zudem für jede Warnung erforderlich.

Ressourcen für [Gespeicherte Suchen in Log Analytics](../../azure-monitor/log-query/log-query-overview.md) weisen den Typ `Microsoft.OperationalInsights/workspaces/savedSearches` und folgende Struktur auf. Dies schließt allgemeine Variablen und Parameter ein, sodass Sie diesen Codeausschnitt kopieren, in Ihre Lösungsdatei einfügen und die Parameternamen ändern können.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

Die Eigenschaften einer gespeicherten Suche sind in der folgenden Tabelle beschrieben.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| category | Die Kategorie für die gespeicherte Suche.  Alle gespeicherten Suchen in derselben Lösung verwenden häufig gemeinsam eine einzige Kategorie, sodass sie gemeinsam in der Konsole gruppiert werden. |
| displayname | Name, der für die gespeicherte Suche im Portal angezeigt wird |
| query | Die auszuführende Abfrage. |

> [!NOTE]
> Sie müssen möglicherweise Escape-Zeichen in der Abfrage verwenden, wenn diese Zeichen enthält, die als JSON interpretiert werden könnten. Falls Ihre Abfrage **AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write"** lautete, sollte in der Lösungsdatei Folgendes stehen: **AzureActivity | OperationName:\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Alerts
[Azure-Protokollwarnungen](../../azure-monitor/platform/alerts-unified-log.md) werden von Azure-Warnungregeln erstellt, die in regelmäßigen Abständen automatisch angegebene Protokollabfragen auszuführen. Wenn die Ergebnisse der Abfrage mit den angegebenen Kriterien übereinstimmen, wird eine Warnung erstellt und mindestens eine Aktion mithilfe von [Aktionsgruppen](../../azure-monitor/platform/action-groups.md) ausgeführt.

> [!NOTE]
> Seit dem 14. Mai 2018 werden alle Warnungen in einem Log Analytics-Arbeitsbereich in einer öffentlichen Instanz der Azure Cloud auf Azure ausgedehnt. Weitere Informationen finden Sie unter [Erweitern von Warnungen nach Azure](../../azure-monitor/platform/alerts-extend.md). Bei Benutzern, die Warnungen auf Azure erweitern, werden Aktionen nun in Azure-Aktionsgruppen gesteuert. Wenn ein Arbeitsbereich und die zugehörigen Warnungen auf Azure ausgedehnt werden, können Sie über die [Azure Resource Manager-Vorlage für Aktionsgruppen](../../azure-monitor/platform/action-groups-create-resource-manager-template.md) Aktionen abrufen oder hinzufügen.

Warnungsregeln in einer Verwaltungslösung bestehen aus den folgenden drei verschiedenen Ressourcen:

- **Gespeicherte Suche.** Definiert die Protokollsuche, die ausgeführt wird Mehrere Warnungsregeln können gemeinsam eine einzelne gespeicherte Suche verwenden.
- **Zeitplan.** Definiert, wie oft die Protokollsuche ausgeführt wird Jede Warnungsregel weist nur einen einzigen Zeitplan auf.
- **Warnungsaktion.** Jede Warnregel weist eine Aktionsgruppenressource oder Aktionsressource (veraltet) mit dem Typ **Warnung** auf, die die Details der Warnung, etwa die Kriterien dafür, wann ein Warnungsdatensatz erstellt wird, und den Schweregrad der Warnung definiert. Die Ressource für eine [Aktionsgruppe](../../azure-monitor/platform/action-groups.md) kann eine Liste der konfigurierten Aktionen aufweisen, die durchzuführen sind, wenn die Warnung ausgelöst wird – z.B. Anruf, SMS, E-Mail, Webhook, ITSM-Tool, Automation-Runbook, Logik-App usw.

Die Aktionsressource (veraltet) definiert optional eine Antwort per E-Mail und Runbook.
- **Webhookaktion (veraltet)** Wenn die Warnungsregel einen Webhook aufruft, ist eine zusätzliche Aktionsressource vom Typ **Webhook** erforderlich.

Gespeicherte Suchressourcen sind oben beschrieben. Die anderen Ressourcen sind nachfolgend beschrieben.

### <a name="schedule-resource"></a>Zeitplanressource

Eine gespeicherte Suche kann einen oder mehrere Zeitpläne aufweisen, wobei jeder Zeitplan eine separate Warnungsregel darstellt. Der Zeitplan definiert, wie oft die Suche durchgeführt wird und welches Zeitintervall für das Abrufen der Daten verwendet wird. Zeitplanressourcen weisen den Typ `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` und die folgende Struktur auf. Dies schließt allgemeine Variablen und Parameter ein, sodass Sie diesen Codeausschnitt kopieren, in Ihre Lösungsdatei einfügen und die Parameternamen ändern können.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }

Die Eigenschaften für Zeitplanressourcen werden in der folgenden Tabelle beschrieben.

| Elementname | Erforderlich | BESCHREIBUNG |
|:--|:--|:--|
| Aktiviert       | JA | Gibt an, ob die Warnung beim Erstellen aktiviert wird. |
| interval      | JA | Abfrageintervall in Minuten |
| queryTimeSpan | JA | Länge in Minuten für die Auswertung der Ergebnisse |

Die Zeitplanressource muss von der gespeicherten Suche abhängig sein, damit sie vor dem Zeitplan erstellt wird.

> [!NOTE]
> Der Name des Zeitplans muss in einem bestimmten Arbeitsbereich eindeutig sein; zwei Zeitpläne können nicht dieselbe ID haben, auch wenn sie unterschiedlichen gespeicherten Suchvorgängen zugeordnet sind. Auch die Namen aller gespeicherten Suchvorgänge, Zeitpläne und Aktionen, die mit der Log Analytics-API erstellt werden, müssen in Kleinbuchstaben geschrieben werden.

### <a name="actions"></a>Aktionen
Ein Zeitplan kann mehrere Aktionen umfassen. Mit einer Aktion können ein oder mehrere durchzuführende Prozesse definiert werden, z.B. das Senden einer E-Mail oder das Starten eines Runbooks. Es kann auch ein Schwellenwert definiert werden, der bestimmt, wann die Ergebnisse einer Suche eine Übereinstimmung mit Kriterien ergeben. Mit einigen Aktionen wird beides definiert, sodass die Prozesse durchgeführt werden, wenn der Schwellenwert erreicht ist.

Aktionen können mithilfe der [Aktionsgruppenressource] oder Aktionsressource definiert werden.

> [!NOTE]
> Seit dem 14. Mai 2018 werden alle Warnungen in einem Log Analytics-Arbeitsbereich in einer öffentlichen Instanz der Azure Cloud automatisch auf Azure ausgedehnt. Weitere Informationen finden Sie unter [Erweitern von Warnungen nach Azure](../../azure-monitor/platform/alerts-extend.md). Bei Benutzern, die Warnungen auf Azure erweitern, werden Aktionen nun in Azure-Aktionsgruppen gesteuert. Wenn ein Arbeitsbereich und die zugehörigen Warnungen auf Azure ausgedehnt werden, können Sie über die [Azure Resource Manager-Vorlage für Aktionsgruppen](../../azure-monitor/platform/action-groups-create-resource-manager-template.md) Aktionen abrufen oder hinzufügen.


Es gibt zwei Arten von Aktionsressourcen, die von der **Typ**-Eigenschaft festgelegt werden. Für einen Zeitplan ist eine Aktion vom Typ **Warnung** erforderlich, die die Details der Warnungsregel und die Aktionen definiert, die beim Erstellen einer Warnung ausgeführt werden. Aktionsressourcen weisen den Typ `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions` auf.

Warnungsaktionen weisen die folgende Struktur auf: Dies schließt allgemeine Variablen und Parameter ein, sodass Sie diesen Codeausschnitt kopieren, in Ihre Lösungsdatei einfügen und die Parameternamen ändern können.

```json
{
    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
    "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
    ],
    "properties": {
        "etag": "*",
        "type": "Alert",
        "name": "[variables('Alert').Name]",
        "description": "[variables('Alert').Description]",
        "severity": "[variables('Alert').Severity]",
        "threshold": {
            "operator": "[variables('Alert').Threshold.Operator]",
            "value": "[variables('Alert').Threshold.Value]",
            "metricsTrigger": {
                "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                "operator": "[variables('Alert').Trigger.Operator]",
                "value": "[variables('Alert').Trigger.Value]"
            },
        },
        "AzNsNotification": {
            "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
            "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
            "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
    }
}
```

Die Eigenschaften für Warnungsaktionsressourcen werden in den folgenden Tabellen beschrieben:

| Elementname | Erforderlich | BESCHREIBUNG |
|:--|:--|:--|
| Type | JA | Der Typ der Aktion.  Dieser lautet bei Warnungsaktionen **Warnung**. |
| NAME | JA | Der Anzeigename für die Warnung.  Dies ist der Name, der in der Konsole für die Warnungsregel angezeigt wird. |
| BESCHREIBUNG | Nein  | Eine optionale Beschreibung der Warnung |
| Schweregrad | JA | Schweregrad des Warnungsdatensatzes aus den folgenden Werten:<br><br> **Kritisch**<br>**Warnung**<br>**Information**


#### <a name="threshold"></a>Schwellenwert
Dieser Abschnitt ist ein Pflichtabschnitt. Er definiert die Eigenschaften für den Warnungsschwellenwert.

| Elementname | Erforderlich | BESCHREIBUNG |
|:--|:--|:--|
| Operator | JA | Operator für den Vergleich der folgenden Werte:<br><br>**gt = Greater Than (Größer als)<br>lt = Less Than (Kleiner als)** |
| Wert | JA | Der Wert zum Vergleich der Ergebnisse |

##### <a name="metricstrigger"></a>MetricsTrigger
Dieser Abschnitt ist optional. Fügen Sie ihn für eine Warnung aufgrund metrischer Messungen ein.

> [!NOTE]
> Warnungen aufgrund metrischer Messungen befinden sich derzeit in der öffentlichen Vorschau.

| Elementname | Erforderlich | BESCHREIBUNG |
|:--|:--|:--|
| TriggerCondition | JA | Gibt an, ob der Schwellenwert für die Gesamtanzahl der Verstöße oder für aufeinander folgende Verstöße gegen folgende Werte steht:<br><br>**Insgesamt<br>Aufeinander folgende** |
| Operator | JA | Operator für den Vergleich der folgenden Werte:<br><br>**gt = Greater Than (Größer als)<br>lt = Less Than (Kleiner als)** |
| Wert | JA | Häufigkeit, mit der die Kriterien erfüllt werden müssen, um die Warnung auszulösen. |


#### <a name="throttling"></a>Drosselung
Dieser Abschnitt ist optional. Beziehen Sie diesen Abschnitt mit ein, wenn nach dem Erstellen einer Warnung Warnungen von derselben Regel eine bestimmte Zeit lang unterdrückt werden sollen.

| Elementname | Erforderlich | BESCHREIBUNG |
|:--|:--|:--|
| DurationInMinutes | Ja, wenn das Drosselungselement enthalten ist | Dauer der Unterdrückung von Warnungen in Minuten, wenn eine Warnung aufgrund derselben Regel erstellt wird. |

#### <a name="azure-action-group"></a>Azure-Aktionsgruppe
Alle Warnungen in Azure verwenden Aktionsgruppen als Standardmechanismus für die Behandlung von Aktionen. Mit Aktionsgruppen können Sie Ihre Aktionen einmal angeben und die Aktionsgruppe dann mehreren Warnungen zuordnen. Dies gilt für sämtliche Bereiche in Azure, sodass Sie dieselbe Aktion nicht mehrmals deklarieren müssen. Aktionsgruppen unterstützen mehrere Aktionen – einschließlich E-Mails, SMS, Sprachanrufen, ITSM-Verbindungen, Automation-Runbooks, Webhook-URIs und anderen.

Für Benutzer, die ihre Warnungen auf Azure erweitert haben, sollten bei Zeitplänen jetzt zusammen mit dem Schwellenwert auch Aktionsgruppendetails übergeben werden, um eine Warnung erstellen zu können. E-Mail-Details, Webhook-URLs, Details zur Runbookautomatisierung und weitere Aktionen müssen vor dem Erstellen einer Warnung in einer Aktionsgruppe definiert werden; es ist möglich, eine [Aktionsgruppe von Azure Monitor aus](../../azure-monitor/platform/action-groups.md) im Portal zu erstellen, oder die [Aktionsgruppen-Ressourcenvorlage](../../azure-monitor/platform/action-groups-create-resource-manager-template.md) zu verwenden.

| Elementname | Erforderlich | BESCHREIBUNG |
|:--|:--|:--|
| AzNsNotification | JA | Die Ressourcen-ID der Azure-Aktionsgruppe, die der Warnung zugeordnet werden soll, um erforderliche Aktionen auszuführen, wenn Warnungskriterien zutreffen. |
| CustomEmailSubject | Nein  | Benutzerdefinierte Betreffzeile der E-Mail, die an alle Adressen gesendet wird, die in der zugeordneten Aktionsgruppe angegeben sind. |
| CustomWebhookPayload | Nein  | Benutzerdefinierte Nutzlast, die an alle Webhookendpunkte gesendet wird, die in der zugeordneten Aktionsgruppe definiert sind. Das Format hängt davon ab, was der Webhook erwartet, und muss ein gültiges serialisiertes JSON-Format sein. |

#### <a name="actions-for-oms-legacy"></a>Aktionen für OMS (veraltet)

Jeder Zeitplan weist eine Aktion vom Typ **Warnung** auf. Dadurch werden die Details der Warnung und optional die Aktionen zur Benachrichtigung und Wartung definiert. Eine Benachrichtigung sendet eine E-Mail an mindestens eine Adresse. Eine Wartung startet ein Runbook in Azure Automation und versucht, das erkannte Problem zu beheben.

> [!NOTE]
> Seit dem 14. Mai 2018 werden alle Warnungen in einem Log Analytics-Arbeitsbereich in einer öffentlichen Instanz der Azure Cloud automatisch auf Azure ausgedehnt. Weitere Informationen finden Sie unter [Erweitern von Warnungen nach Azure](../../azure-monitor/platform/alerts-extend.md). Bei Benutzern, die Warnungen auf Azure erweitern, werden Aktionen nun in Azure-Aktionsgruppen gesteuert. Wenn ein Arbeitsbereich und die zugehörigen Warnungen auf Azure ausgedehnt werden, können Sie über die [Azure Resource Manager-Vorlage für Aktionsgruppen](../../azure-monitor/platform/action-groups-create-resource-manager-template.md) Aktionen abrufen oder hinzufügen.

##### <a name="emailnotification"></a>EmailNotification
 Dieser Abschnitt ist optional. Schließen Sie ihn ein, wenn bei der Warnung eine E-Mail an mindestens einen Empfänger gesendet werden soll.

| Elementname | Erforderlich | BESCHREIBUNG |
|:--|:--|:--|
| Empfänger | JA | Durch Kommas getrennte Liste der E-Mail-Adressen, an die eine Benachrichtigung gesendet wird, wenn eine Warnung wie im folgenden Beispiel erstellt wird.<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| Antragsteller | JA | Die Betreffzeile der E-Mail |
| Anhang | Nein  | Anlagen werden derzeit nicht unterstützt. Wenn dieses Element enthalten ist, muss es **Keine** lauten. |

##### <a name="remediation"></a>Wiederherstellung
Dieser Abschnitt ist optional. Schließen Sie ihn ein, wenn als Reaktion auf die Warnung ein Runbook gestartet werden soll. |

| Elementname | Erforderlich | BESCHREIBUNG |
|:--|:--|:--|
| RunbookName | JA | Der Name des Runbooks, das gestartet werden soll |
| WebhookUri | JA | Der URI des Webhooks für das Runbook |
| Expiry | Nein  | Datum und Uhrzeit des Ablaufs der Wiederherstellung |

##### <a name="webhook-actions"></a>Webhookaktionen

Bei Webhookaktionen wird ein Prozess gestartet, indem eine URL aufgerufen und optional eine zu sendende Nutzlast bereitgestellt wird. Diese Aktionen sind mit Aktionen zur Problembehebung vergleichbar. Sie sind aber für Webhooks bestimmt, mit denen andere Prozesse als Azure Automation-Runbooks aufgerufen werden können. Außerdem verfügen sie über die zusätzliche Option zum Angeben einer Nutzlast, die für den Remoteprozess bereitgestellt wird.

Wenn die Warnung einen Webhook aufruft, benötigt sie eine Aktionsressource mit einem **Webhook**-Typ zusätzlich zur Aktionsressource vom Typ **Warnung**.

    {
      "name": "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Webhook').Name)]",
      "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions/",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
      ],
      "properties": {
        "etag": "*",
        "type": "[variables('Alert').Webhook.Type]",
        "name": "[variables('Alert').Webhook.Name]",
        "webhookUri": "[variables('Alert').Webhook.webhookUri]",
        "customPayload": "[variables('Alert').Webhook.CustomPayLoad]"
      }
    }

Die Eigenschaften für Webhook-Aktionsressourcen werden in den folgenden Tabellen beschrieben:

| Elementname | Erforderlich | BESCHREIBUNG |
|:--|:--|:--|
| type | JA | Der Typ der Aktion. Dieser lautet für Webhookaktionen **Webhook**. |
| name | JA | Der Anzeigename für die Aktion. Dieser wird nicht in der Konsole angezeigt. |
| webhookUri | JA | URI für den Webhook |
| customPayload | Nein  | Benutzerdefinierte Nutzlast, die an den Webhook gesendet wird. Das Format hängt davon ab, was vom Webhook erwartet wird. |

## <a name="sample"></a>Beispiel

Es folgt ein Beispiel für eine Lösung, die die folgenden Ressourcen enthält:

- Gespeicherte Suche
- Schedule
- Aktionsgruppe

Das Beispiel verwendet Variablen für [Standardlösungsparameter]( solutions-solution-file.md#parameters), die im Gegensatz zu hartcodierten Werten in Ressourcendefinitionen häufig in einer Lösung verwendet werden.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "Description": "Name of Log Analytics workspace"
            }
        },
        "workspaceregionId": {
            "type": "string",
            "metadata": {
                "Description": "Region of Log Analytics workspace"
            }
        },
        "actiongroup": {
            "type": "string",
            "metadata": {
                "Description": "List of action groups for alert actions separated by semicolon"
            }
        }
    },
    "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
        "LogAnalyticsApiVersion-Search": "2017-03-15-preview",
        "LogAnalyticsApiVersion-Solution": "2015-11-01-preview",
        "MySearch": {
            "displayName": "Error records by hour",
            "query": "MyRecord_CL | summarize AggregatedValue = avg(Rating_d) by Instance_s, bin(TimeGenerated, 60m)",
            "category": "Samples",
            "name": "Samples-Count of data"
        },
        "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert. Fires when 3 error records found over hour interval.",
            "Severity": "critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
                "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
                "Interval": 15,
                "TimeSpan": 60
            },
            "MetricsTrigger": {
                "TriggerCondition": "Consecutive",
                "Operator": "gt",
                "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
                "GroupIds": [
                    "[parameters('actiongroup')]"
                ],
                "CustomEmailSubject": "Sample alert"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Solution')]",
            "dependsOn": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
            ],
            "properties": {
                "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
                "referencedResources": [
                ],
                "containedResources": [
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
                ]
            },
            "plan": {
                "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
                "Version": "[variables('SolutionVersion')]",
                "product": "[variables('ProductName')]",
                "publisher": "[variables('SolutionPublisher')]",
                "promotionCode": ""
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
                "etag": "*",
                "query": "[variables('MySearch').query]",
                "displayName": "[variables('MySearch').displayName]",
                "category": "[variables('MySearch').category]"
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
                "etag": "*",
                "interval": "[variables('MyAlert').Schedule.Interval]",
                "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
                "enabled": true
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
                "etag": "*",
                "Type": "Alert",
                "Name": "[variables('MyAlert').DisplayName]",
                "Description": "[variables('MyAlert').Description]",
                "Severity": "[variables('MyAlert').Severity]",
                "Threshold": {
                    "Operator": "[variables('MyAlert').ThresholdOperator]",
                    "Value": "[variables('MyAlert').ThresholdValue]",
                    "MetricsTrigger": {
                        "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                        "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                        "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                    }
                },
                "Throttling": {
                    "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
                },
                "AzNsNotification": {
                    "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
                    "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
                }
            }
        }
    ]
}
```

Die folgende Parameterdatei stellt Beispielwerte für diese Lösung bereit:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspacename": {
            "value": "myWorkspace"
        },
        "accountName": {
            "value": "myAccount"
        },
        "workspaceregionId": {
            "value": "East US"
        },
        "regionId": {
            "value": "East US 2"
        },
        "pricingTier": {
            "value": "Free"
        },
        "actiongroup": {
            "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
* [Hinzufügen von Ansichten](solutions-resources-views.md) zu Ihrer Verwaltungslösung
* [Hinzufügen von Automation-Runbooks und anderen Ressourcen](solutions-resources-automation.md) zu Ihrer Verwaltungslösung
