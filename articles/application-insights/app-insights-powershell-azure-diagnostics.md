---
title: Einrichten von Application Insights in Azure mit PowerShell | Microsoft Docs
description: Automatisieren der Konfiguration der Azure-Diagnose zum Senden von Daten an Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 4ac803a8-f424-4c0c-b18f-4b9c189a64a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/17/2015
ms.author: mbullwin
ms.openlocfilehash: 5e3a577f8c4759dec2d85a431ae31b61b2d582e0
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35636352"
---
# <a name="using-powershell-to-set-up-application-insights-for-an-azure-web-app"></a>Einrichten von Application Insights für eine Azure-Web-App mit PowerShell
[Microsoft Azure](https://azure.com) kann zum [Senden von Azure-Diagnosedaten](app-insights-azure-diagnostics.md) an [Azure Application Insights](app-insights-overview.md) konfiguriert werden. Die Diagnosedaten beziehen sich auf Azure Cloud Services und Azure VMs. Sie ergänzen die Telemetriedaten, die Sie mit dem Application Insights SDK aus der App senden. Als Teil der Automatisierung des Prozesses zum Erstellen neuer Ressourcen in Azure können Sie die Diagnose mit PowerShell konfigurieren.

## <a name="azure-template"></a>Azure-Vorlage
Wenn sich die Web-App in Azure befindet und Sie Ihre Ressourcen mit einer Azure Resource Manager-Vorlage erstellen, können Sie Application Insights konfigurieren, indem Sie dem Ressourcenknoten Folgendes hinzufügen:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource` : Name für die Application Insights-Ressource
* `myWebAppName`: die ID der Web-App

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Aktivieren der Diagnoseerweiterung bei der Bereitstellung eines Clouddiensts
Das Cmdlet `New-AzureDeployment` hat den `ExtensionConfiguration`-Parameter, der ein Array von Diagnosekonfigurationen verwendet. Diese können mit dem Cmdlet `New-AzureServiceDiagnosticsExtensionConfig` erstellt werden. Beispiel: 

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Aktivieren der Diagnoseerweiterung für einen vorhandenen Clouddienst
Verwenden Sie bei einem vorhandenen Dienst `Set-AzureServiceDiagnosticsExtension`.

```ps

    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>Abrufen der aktuellen Konfiguration der Diagnoseerweiterung
```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>Entfernen der Diagnoseerweiterung
```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Wenn Sie die Diagnoseerweiterung entweder mit `Set-AzureServiceDiagnosticsExtension` oder `New-AzureServiceDiagnosticsExtensionConfig` ohne den „Role“-Parameter aktiviert haben, können Sie die Erweiterung mit `Remove-AzureServiceDiagnosticsExtension` ohne den „Role“-Parameter entfernen. Wenn beim Aktivieren der Erweiterung der „Role“-Parameter verwendet wurde, muss er auch zum Entfernen der Erweiterung verwendet werden.

So entfernen Sie die Diagnoseerweiterung aus jeder einzelnen Rolle

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>Weitere Informationen
* [Überwachen von Azure Cloud Services-Apps mit Application Insights](app-insights-cloudservices.md)
* [Senden von Azure-Diagnosedaten an Application Insights](app-insights-azure-diagnostics.md)
* [Automatisieren der Warnungskonfiguration](app-insights-powershell-alerts.md)

