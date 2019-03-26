---
title: Azure Monitor – PowerShell-Schnellstartbeispiele
description: Verwenden Sie PowerShell für den Zugriff auf Azure Monitor-Features wie die automatische Skalierung, Warnungen, Webhooks und die Suche in Aktivitätsprotokollen.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/14/2018
ms.author: robb
ms.subservice: ''
ms.openlocfilehash: ae06fae8aa7706428a71b8069eff58ba8bf6abb1
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307512"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Azure Monitor – PowerShell-Schnellstartbeispiele
In diesem Artikel werden PowerShell-Beispielbefehle beschrieben, mit denen Sie auf Azure Monitor-Features zugreifen können.

> [!NOTE]
> Azure Monitor ist der neue Name für den Dienst, der bis 25. September 2016 als „Azure Insights“ bezeichnet wurde. Die Namespaces und somit die folgenden Befehle enthalten jedoch weiterhin das Wort „insights“.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>Einrichten von PowerShell
Sofern dies noch nicht geschehen ist, richten Sie PowerShell auf Ihrem Computer ein. Weitere Informationen finden Sie unter [Vorgehensweise zum Installieren und Konfigurieren von PowerShell](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>Beispiele in diesem Artikel
Mit den Beispielen in diesem Artikel wird veranschaulicht, wie Sie Azure Monitor-Cmdlets verwenden können. Die vollständige Liste der PowerShell-Cmdlets für Azure Monitor finden Sie unter [Azure Monitor-Cmdlets (Insights)](https://docs.microsoft.com/powershell/module/azurerm.insights).

## <a name="sign-in-and-use-subscriptions"></a>Anmelden und Verwenden von Abonnements
Melden sich zuerst bei Ihrem Azure-Abonnement an.

```PowerShell
Connect-AzAccount
```

Es wird ein Anmeldebildschirm angezeigt. Nach der Anmeldung werden Ihr Konto, die Mandanten-ID und die ID des Standardabonnements angezeigt. Alle Azure-Cmdlets werden im Kontext des Standardabonnements verwendet. Verwenden Sie den folgenden Befehl, um die Liste der Abonnements anzuzeigen, auf die Sie zugreifen können:

```PowerShell
Get-AzSubscription
```

Verwenden Sie den folgenden Befehl, um den Arbeitskontext in ein anderes Abonnement zu ändern:

```PowerShell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Abrufen des Aktivitätsprotokolls für ein Abonnement
Verwenden Sie das Cmdlet `Get-AzLog` .  Im Folgenden sind einige allgemeine Beispiele aufgeführt.

Abrufen von Protokolleinträgen ab dieser Uhrzeit-/Datumsangabe bis heute:

```PowerShell
Get-AzLog -StartTime 2016-03-01T10:30
```

Abrufen von Protokolleinträgen im Uhrzeit-/Datumsbereich:

```PowerShell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Abrufen von Protokolleinträgen aus einer bestimmten Ressourcengruppe:

```PowerShell
Get-AzLog -ResourceGroup 'myrg1'
```

Abrufen von Protokolleinträgen von einem bestimmten Ressourcenanbieter im Uhrzeit-/Datumsbereich:

```PowerShell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Abrufen aller Protokolleinträge mit einem bestimmten Aufrufer:

```PowerShell
Get-AzLog -Caller 'myname@company.com'
```

Der folgende Befehl ruft die letzten 1.000 Ereignisse aus dem Aktivitätsprotokoll ab:

```PowerShell
Get-AzLog -MaxEvents 1000
```

`Get-AzLog` unterstützt viele weitere Parameter. Weitere Informationen finden Sie in der `Get-AzLog` -Referenz.

> [!NOTE]
> `Get-AzLog` stellt nur den Verlauf für 15 Tage bereit. Mithilfe des **-MaxEvents** -Parameters können Sie die letzten N Ereignisse für mehr als 15 Tage abfragen. Verwenden Sie für den Zugriff auf Ereignisse, die älter als 15 Tage sind, die REST-API oder das SDK (C#-Beispiel mit dem SDK). Wenn Sie **StartTime** nicht angeben, ist der Standardwert **EndTime** minus 1 Stunde. Wenn Sie **EndTime**nicht angeben, ist der Standardwert die aktuelle Zeit. Alle Zeitangaben sind in UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Abrufen des Verlaufs von Warnungen
Um alle Warnereignisse anzuzeigen, können Sie die Azure Resource Manager-Protokolle mit den folgenden Beispielen abfragen.

```PowerShell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Um den Verlauf einer bestimmten Warnungsregel anzuzeigen, können Sie das Cmdlet `Get-AzAlertHistory` verwenden und dabei die Ressourcen-ID der Warnungsregel übergeben.

```PowerShell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

Das Cmdlet `Get-AzAlertHistory` unterstützt verschiedene Parameter. Weitere Informationen finden Sie unter [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Abrufen von Informationen zu Warnungsregeln
Die folgenden Befehle beziehen sich auf eine Ressourcengruppe mit dem Namen „montest“.

Anzeigen aller Eigenschaften der Warnungsregel:

```PowerShell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Abrufen aller Warnungen für eine Ressourcengruppe:

```PowerShell
Get-AzAlertRule -ResourceGroup montest
```

Abrufen aller Warnungsregeln, die für eine Zielressource festgelegt wurden. Beispiel: Alle Warnungsregeln, die für einen virtuellen Computer festgelegt wurden.

```PowerShell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule` unterstützt weitere Parameter. Weitere Informationen finden Sie unter [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) .

## <a name="create-metric-alerts"></a>Erstellen von Metrikwarnungen
Sie können das Cmdlet `Add-AlertRule` zum Erstellen, Aktualisieren oder Deaktivieren einer Warnungsregel verwenden.

Sie können die Eigenschaften für E-Mails und Webhooks mit `New-AzAlertRuleEmail` bzw. `New-AzAlertRuleWebhook` erstellen. Weisen Sie diese Eigenschaften im Cmdlet für die Warnungsregel als Aktionen der **Actions**-Eigenschaft der Warnungsregel zu.

In der folgenden Tabelle werden die Parameter und Werte beschrieben, die zum Erstellen einer Warnung mithilfe einer Metrik verwendet werden.

| Parameter | value |
| --- | --- |
| NAME |simpletestdiskwrite |
| Standort für diese Warnungsregel |USA (Ost) |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| „MetricName“ für die Warnung, die erstellt wird |\PhysicalDisk(_Total)\Disk Writes/sec. Unter den Informationen zum Cmdlet `Get-MetricDefinitions` finden Sie Details dazu, wie Sie die genauen Metriknamen abrufen. |
| operator |GreaterThan |
| Schwellenwert (Anzahl/s für diese Metrik) |1 |
| WindowSize (Format: hh:mm:ss) |00:05:00 |
| Aggregator (Statistik der Metrik, die in diesem Fall die durchschnittliche Anzahl verwendet) |Durchschnitt |
| Benutzerdefinierte E-Mail-Adressen (Zeichenfolgenarray) |'foo@example.com','bar@example.com' |
| E-Mail an Besitzer, Mitwirkende und Leser senden |-SendToServiceOwners |

Erstellen einer E-Mail-Aktion

```PowerShell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Erstellen einer Webhookaktion

```PowerShell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Erstellen der Warnungsregel für die Metrik zum CPU-Prozentsatz auf einem klassischen virtuellen Computer

```PowerShell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Abrufen der Warnungsregel

```PowerShell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Das Cmdlet zum Hinzufügen einer Warnung aktualisiert die Regel auch, wenn für die angegebenen Eigenschaften bereits eine Warnungsregel vorhanden ist. Um eine Warnungsregel zu deaktivieren, fügen Sie den **-DisableRule**-Parameter hinzu.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Abrufen einer Liste der verfügbaren Metriken für Warnungen
Sie können das Cmdlet `Get-AzMetricDefinition` zum Anzeigen der Liste aller Metriken für eine bestimmte Ressource verwenden.

```PowerShell
Get-AzMetricDefinition -ResourceId <resource_id>
```

Im folgenden Beispiel wird eine Tabelle mit den zugehörigen Metriken „Name“ und „Unit“ erstellt.

```PowerShell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Eine vollständige Liste der verfügbaren Optionen für `Get-AzMetricDefinition` finden Sie unter [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-activity-log-alerts"></a>Erstellen und Verwalten von Aktivitätsprotokollwarnungen
Sie können mit dem Cmdlet `Set-AzActivityLogAlert` eine Aktivitätsprotokollwarnung festlegen. Eine Aktivitätsprotokollwarnung erfordert, dass Sie zuerst Ihre Bedingungen als Wörterbuch der Bedingungen definieren und dann eine Warnung erstellen, die diese Bedingungen verwendet.

```PowerShell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

Die weiteren Webhookeigenschaften sind optional. Mit `Get-AzActivityLogAlert` erhalten Sie wieder den Inhalt einer Aktivitätsprotokollwarnung.

## <a name="create-and-manage-autoscale-settings"></a>Erstellen und Verwalten von Einstellungen zur automatischen Skalierung
Für eine Ressource (Web-App, virtueller Computer, Clouddienst oder VM-Skalierungsgruppe) kann nur eine Einstellung zur automatischen Skalierung konfiguriert werden.
Allerdings kann jede Einstellung zur automatischen Skalierung mehrere Profile aufweisen. Beispielsweise kann ein Profil ein leistungsbasiertes Skalierungsprofil und ein zweites ein zeitplanbasiertes Profil sein. Für jedes Profil können mehrere Regeln konfiguriert werden. Weitere Informationen zur automatischen Skalierung finden Sie unter [Automatisches Skalieren einer Anwendung](../../cloud-services/cloud-services-how-to-scale-portal.md).

Gehen Sie wie folgt vor:

1. Erstellen Sie Regeln.
2. Erstellen Sie Profile, und ordnen Sie die zuvor erstellten Regeln den Profilen zu.
3. Optional: Erstellen Sie Benachrichtigungen für die automatische Skalierung, indem Sie Webhook- und E-Mail-Eigenschaften konfigurieren.
4. Erstellen Sie eine Einstellung zur automatischen Skalierung mit einem Namen für die Zielressource, indem Sie die Profile und Benachrichtigungen zuordnen, die Sie in den vorherigen Schritten erstellt haben.

Mit den folgenden Beispielen wird veranschaulicht, wie Sie eine Einstellung zur automatischen Skalierung einer VM-Skalierungsgruppe für ein Windows-Betriebssystem basierend auf der Metrik für die CPU-Auslastung erstellen können.

Erstellen Sie zunächst eine Regel für das horizontale Hochskalieren mit einer Erhöhung der Anzahl der Instanzen.

```PowerShell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Erstellen Sie dann eine Regel für das horizontale Herunterskalieren mit einer Verringerung der Anzahl der Instanzen.

```PowerShell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Erstellen Sie anschließend ein Profil für die Regeln.

```PowerShell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Erstellen Sie eine Webhookeigenschaft.

```PowerShell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Erstellen Sie die Benachrichtigungseigenschaft für die Einstellung zur automatischen Skalierung, binden Sie dabei die zuvor erstellen Eigenschaften für E-Mail-Adresse und Webhook ein.

```PowerShell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Erstellen Sie schließlich die Einstellung zur automatischen Skalierung, um das soeben erstellte Profil hinzuzufügen. 

```PowerShell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Weitere Informationen zum Verwalten von Einstellungen zur automatischen Skalierung finden Sie unter [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Verlauf der automatischen Skalierung
Im folgende Beispiel wird gezeigt, wie Sie aktuelle Ereignisse zur automatischen Skalierung und Warnereignisse anzeigen können. Zeigen Sie über die Suche im Aktivitätsprotokoll den Verlauf der automatischen Skalierung an.

```PowerShell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Sie können das Cmdlet `Get-AzAutoScaleHistory` zum Abrufen des Verlaufs der automatischen Skalierung verwenden.

```PowerShell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Weitere Informationen finden Sie unter [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Anzeigen der Details für eine Einstellung zur automatischen Skalierung
Sie können das Cmdlet `Get-Autoscalesetting` zum Abrufen von weiteren Informationen über die Einstellung zur automatischen Skalierung verwenden.

Mit dem folgenden Beispiel werden die Details über alle Einstellungen zur automatischen Skalierung in der Ressourcengruppe „myrg1“ angezeigt.

```PowerShell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

Mit dem folgenden Beispiel werden die Details über alle Einstellungen zur automatischen Skalierung in der Ressourcengruppe „myrg1“ angezeigt, insbesondere die Einstellung mit dem Namen „MyScaleVMSSSetting“.

```PowerShell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Entfernen einer Einstellung zur automatischen Skalierung
Sie können das Cmdlet `Remove-Autoscalesetting` zum Löschen einer Einstellung zur automatischen Skalierung verwenden.

```PowerShell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Verwalten von Protokollprofilen für das Aktivitätsprotokoll
Sie können ein *Protokollprofil* erstellen, Daten aus Ihren Aktivitätsprotokollen in ein Speicherkonto exportieren und die Aufbewahrungsdauer für diese festlegen. Optional können Sie die Daten auch an Ihren Event Hub streamen. Dieses Feature befindet sich derzeit in der Vorschauphase, sodass Sie nur ein Protokollprofil pro Abonnement erstellen können. Sie können die folgenden Cmdlets mit Ihrem aktuellen Abonnement verwenden, um Protokollprofile zu erstellen und zu verwalten. Sie können auch ein bestimmtes Abonnement auswählen. Obwohl in PowerShell standardmäßig das aktuelle Abonnement verwendet wird, können Sie dies jederzeit mit `Set-AzContext`ändern. Sie können das Aktivitätsprotokoll so konfigurieren, dass Daten an ein beliebiges Speicherkonto oder einen Event Hub im Abonnement weitergeleitet werden. Daten werden als Blobdateien im JSON-Format geschrieben.

### <a name="get-a-log-profile"></a>Abrufen eines Protokollprofils
Verwenden Sie zum Abrufen Ihrer vorhandenen Protokollprofile das Cmdlet `Get-AzLogProfile` .

### <a name="add-a-log-profile-without-data-retention"></a>Hinzufügen eines Protokollprofils ohne Datenaufbewahrung
```PowerShell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Entfernen eines Protokollprofils
```PowerShell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Hinzufügen eines Protokollprofils mit Datenaufbewahrung
Sie können die **-RetentionInDays** -Eigenschaft mit der Anzahl von Tagen (als positive ganze Zahl) für die Aufbewahrung der Daten angeben.

```PowerShell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Hinzufügen eines Protokollprofils mit Aufbewahrung und Event Hub
Neben dem Weiterleiten Ihrer Daten an ein Speicherkonto können Sie sie auch an einen Event Hub streamen. In dieser Vorschauversion ist die Konfiguration des Speicherkontos obligatorisch, die Event Hub-Konfiguration ist jedoch optional.

```PowerShell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Konfigurieren von Diagnoseprotokollen
Viele Azure-Dienste stellen zusätzliche Protokolle und Telemetrie bereit, die eine oder mehrere der folgenden Möglichkeiten bieten: 
 - Konfigurieren der Speicherung von Daten in Ihrem Azure Storage-Konto
 - Senden an Event Hubs
 - Senden an einen Log Analytics-Arbeitsbereich 

Der Vorgang kann nur auf Ressourcenebene ausgeführt werden. Das Speicherkonto oder der Event Hub sollte in der gleichen Region wie die Zielressource, in der die Diagnoseeinstellung konfiguriert ist, vorhanden sein.

### <a name="get-diagnostic-setting"></a>Abrufen der Diagnoseeinstellung
```PowerShell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Deaktivieren der Diagnoseeinstellung

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Aktivieren der Diagnoseeinstellung ohne Aufbewahrung

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Aktivieren der Diagnoseeinstellung mit Aufbewahrung

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Aktivieren der Diagnoseeinstellung mit Aufbewahrung für eine bestimmte Protokollkategorie

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Aktivieren der Diagnoseeinstellung für Event Hubs

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Aktivieren der Diagnoseeinstellung für Log Analytics

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Beachten Sie, dass für die WorkspaceId-Eigenschaft die *Ressourcen-ID* des Arbeitsbereichs verwendet wird. Sie können die Ressourcen-ID mit dem folgenden Befehl aus Ihrem Log Analytics-Arbeitsbereich abrufen:

```PowerShell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Diese Befehle können kombiniert werden, um Daten an mehrere Ziele zu senden.

