---
title: Problembehandlung für Azure Statusmonitor v2 und bekannte Probleme | Microsoft-Dokumentation
description: Bekannte Probleme bei Statusmonitor v2 und Beispiele für die Problembehandlung Überwachen Sie die Websiteleistung ohne erneute Bereitstellung der Website. Funktioniert mit ASP.NET-Web-Apps, die lokal, auf virtuellen Computern oder in Azure gehostet werden.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: f2115ee14b58030f695c9410870615f03d353cd2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734960"
---
# <a name="troubleshooting-status-monitor-v2"></a>Problembehandlung bei Statusmonitor v2

Wenn Sie die Überwachung aktivieren, können Probleme auftreten, die die Datensammlung verhindern.
In diesem Artikel sind alle bekannten Probleme und Beispiele für die Problembehandlung aufgeführt.
Wenn bei Ihnen ein Problem auftritt, das hier nicht aufgeführt ist, können Sie mit uns über [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues) Kontakt aufnehmen.


> [!IMPORTANT]
> Statusmonitor v2 ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Einige Features werden möglicherweise nicht unterstützt oder bieten möglicherweise nur eingeschränkte Funktionen.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="known-issues"></a>Bekannte Probleme

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>In Konflikt stehende DLLs im Verzeichnis „bin“ einer App

Wenn eine der folgenden DLLs im Verzeichnis „bin“ vorhanden ist, kann die Überwachung fehlschlagen:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Einige dieser DLLs werden in die App-Standardvorlagen von Visual Studio sogar dann einbezogen, wenn Ihre App sie nicht verwendet.
Sie können mithilfe von Problembehandlungstools symptomatisches Verhalten anzeigen:

- PerfView:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset und app load (ohne Telemetrie). Führen Sie eine Untersuchung mit Sysinternals (Handle.exe und ListDLLs.exe) durch:
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Konflikt mit IIS-Freigabekonfiguration

Wenn Sie über einen Cluster von Webservern verfügen, verwenden Sie möglicherweise eine [Freigabekonfiguration](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Das HttpModule kann in diese Freigabekonfiguration nicht eingefügt werden.
Führen Sie den Befehl „Enable“ auf jedem Webserver aus, um die DLL im GAC jedes Servers zu installieren.

Führen Sie nach Ausführung des Befehls „Enable“ die folgenden Schritte aus:
1. Wechseln Sie zum Verzeichnis für die Freigabekonfiguration, und suchen Sie die Datei „applicationHost.config“.
2. Fügen Sie im Modulabschnitt Ihrer Konfiguration folgende Zeile hinzu:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
## <a name="troubleshooting"></a>Problembehandlung
    
### <a name="troubleshooting-powershell"></a>Problembehandlung bei PowerShell

#### <a name="determine-which-modules-are-available"></a>Ermitteln, welche Module verfügbar sind
Mit dem Befehl `Get-Module -ListAvailable` können Sie ermitteln, welche Module installiert sind.

#### <a name="import-a-module-into-the-current-session"></a>Importieren eines Moduls in die aktuelle Sitzung
Wenn ein Modul noch nicht in eine PowerShell-Sitzung geladen wurde, können Sie es mit dem Befehl `Import-Module <path to psd1>` manuell laden.


### <a name="troubleshooting-the-status-monitor-v2-module"></a>Problembehandlung beim Statusmonitor v2-Modul

#### <a name="list-the-commands-available-in-the-status-monitor-v2-module"></a>Auflisten der Befehle, die im Statusmonitor v2-Modul verfügbar sind
Führen Sie den Befehl `Get-Command -Module Az.ApplicationMonitor` aus, um die verfügbaren Befehle anzuzeigen:

```
Command type     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.2.1      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.2.1      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.2.1      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.2.1      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.2.1      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-status-monitor-v2-module"></a>Ermitteln der aktuellen Version des Statusmonitor v2-Moduls
Führen Sie den Befehl `Get-ApplicationInsightsMonitoringStatus` aus, um folgende Informationen zum Modul anzuzeigen:
   - PowerShell-Modulversion
   - Version des Application Insights SDK
   - Dateipfade des PowerShell-Moduls
    
Lesen Sie die [API-Referenz](status-monitor-v2-api-get-status.md) mit einer ausführlichen Beschreibung zur Verwendung dieses Cmdlets.


### <a name="troubleshooting-running-processes"></a>Problembehandlung bei aktuell ausgeführten Prozessen

Sie können die Prozesse auf dem instrumentierten Computer überprüfen, um zu ermitteln, ob alle DLLs geladen wurden.
Wenn die Überwachung funktioniert, sollten mindestens 12 DLLS geladen worden sein.

Verwenden Sie den Befehl `Get-ApplicationInsightsMonitoringStatus -InspectProcess` zum Überprüfen der DLLs.

Lesen Sie die [API-Referenz](status-monitor-v2-api-get-status.md) mit einer ausführlichen Beschreibung zur Verwendung dieses Cmdlets.


### <a name="collect-etw-logs-by-using-perfview"></a>Sammeln von ETW-Protokollen mit PerfView

#### <a name="setup"></a>Einrichtung

1. Laden Sie PerfView.exe und PerfView64.exe aus [GitHub](https://github.com/Microsoft/perfview/releases) herunter.
2. Starten Sie PerfView64.exe.
3. Erweitern Sie **Erweiterte Optionen**.
4. Aktivieren Sie diese Kontrollkästchen:
    - **Zip**
    - **Merge** (Zusammenführen)
    - **.NET-Symbolsammlung**
5. Legen Sie folgende **Zusätzliche Anbieter** fest: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Sammeln von Protokollen

1. Führen Sie in einer Befehlskonsole mit Administratorrechten den Befehl `iisreset /stop` aus, um IIS und alle Web-Apps zu deaktivieren.
2. Wählen Sie in PerfView **Sammlung starten** aus.
3. Führen Sie in einer Befehlskonsole mit Administratorrechten den Befehl `iisreset /start` aus, um IIS zu starten.
4. Versuchen Sie, zu Ihrer App zu navigieren.
5. Nachdem Ihre App geladen wurde, kehren Sie zu PerfView zurück, und wählen Sie **Sammlung beenden** aus.



## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich in der [API-Referenz](status-monitor-v2-overview.md#powershell-api-reference) über Parameter, die Sie möglicherweise übersehen haben.
- Wenn bei Ihnen ein Problem auftritt, das hier nicht aufgeführt ist, können Sie mit uns über [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues) Kontakt aufnehmen.
