---
title: Verwenden von Azure Resource Manager-Vorlagen zum Erstellen und Konfigurieren eines Log Analytics-Arbeitsbereichs | Microsoft-Dokumentation
description: "Sie können Azure Resource Manager-Vorlagen zum Erstellen und Konfigurieren von Log Analytics-Arbeitsbereichen verwenden."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: d21ca1b0-847d-4716-bb30-2a8c02a606aa
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: json
ms.topic: article
ms.date: 12/06/2017
ms.author: richrund
ms.openlocfilehash: cea25429dc6e5f9f12f472d17e8743d272135257
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/10/2018
---
# <a name="manage-log-analytics-using-azure-resource-manager-templates"></a>Verwalten von Log Analytics mithilfe von Azure Resource Manager-Vorlagen
Sie können [Azure Resource Manager-Vorlagen](../azure-resource-manager/resource-group-authoring-templates.md) zum Erstellen und Konfigurieren von Log Analytics-Arbeitsbereichen verwenden. Beispiele für die Aufgaben, die Sie mit Vorlagen ausführen können:

* Erstellen eines Arbeitsbereichs
* Hinzufügen einer Lösung
* Erstellen gespeicherter Suchvorgänge
* Erstellen einer Computergruppe
* Aktivieren der Sammlung von IIS-Protokollen auf Computern mit installiertem Windows-Agent
* Sammeln von Leistungsindikatoren von Windows- und Linux-Computern
* Sammeln von Ereignissen aus Syslog auf Linux-Computern 
* Sammeln von Ereignissen aus Windows-Ereignisprotokollen
* Sammeln von benutzerdefinierten Ereignisprotokollen
* Hinzufügen des Log Analytics-Agents auf virtuellen Azure-Computern
* Konfiguration von Log Analytics zum Indizieren der Daten, die mit der Azure-Diagnose gesammelt werden

Dieser Artikel enthält Vorlagenbeispiele, die einen Teil der Konfiguration veranschaulichen, die Sie mit Vorlagen ausführen können.

## <a name="api-versions"></a>API-Versionen
Das Beispiel in diesem Artikel bezieht sich auf einen [aktualisierten Log Analytics-Arbeitsbereich](log-analytics-log-search-upgrade.md).  Um einen Legacy-Arbeitsbereich zu verwenden, müssten Sie die Syntax der Abfragen in die Legacy-Sprache übertragen und die API-Version für jede Ressource ändern.  Die folgende Tabelle enthält die API-Versionen für die Ressourcen, die in diesem Beispiel verwendet werden.

| Ressource | Ressourcentyp | Legacy-API-Version | Aktualisierte API-Version |
|:---|:---|:---|:---|
| Arbeitsbereich   | workspaces    | 2015-11-01-preview | 2017-03-15-preview |
| Suchen,      | savedSearches | 2015-11-01-preview | 2017-03-15-preview |
| Datenquelle | datasources   | 2015-11-01-preview | 2015-11-01-preview |
| Lösung    | solutions     | 2015-11-01-preview | 2015-11-01-preview |


## <a name="create-and-configure-a-log-analytics-workspace"></a>Erstellen und Konfigurieren eines Log Analytics-Arbeitsbereichs
Das folgende Vorlagenbeispiel veranschaulicht Folgendes:

1. Erstellen eines Arbeitsbereichs und Einrichten der Datenaufbewahrung
2. Hinzufügen von Lösungen zum Arbeitsbereich
3. Erstellen gespeicherter Suchvorgänge
4. Erstellen einer Computergruppe
5. Aktivieren der Sammlung von IIS-Protokollen auf Computern mit installiertem Windows-Agent
6. Sammeln von Leistungsindikatoren logischer Datenträger von Linux-Computern (Prozentsatz verwendeter I-Knoten, MB frei, Prozentsatz des verwendeten Speicherplatzes, Übertragungen/s, Lesevorgänge/s, Schreibvorgänge/s)
7. Sammeln von Syslog-Ereignissen auf Linux-Computern
8. Sammeln von Fehler- und Warnereignissen aus dem Anwendungsereignisprotokoll von Windows-Computern
9. Erfassen des Leistungsindikators „Verfügbarer Arbeitsspeicher in MB“ von Windows-Computern
10. Erfassen eines benutzerdefinierten Protokolls 
11. Sammeln von IIS-Protokollen und Windows-Ereignisprotokollen, die von der Azure-Diagnose in ein Speicherkonto geschrieben werden

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "workspaceName"
      }
    },
    "serviceTier": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Standalone",
        "PerNode"
      ],
      "metadata": {
        "description": "Service Tier: Free, Standalone, or PerNode"
    }
      },
    "dataRetention": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 7,
      "maxValue": 730,
      "metadata": {
        "description": "Number of days of retention. Free plans can only have 7 days, Standalone and OMS plans include 30 days for free"
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "East US",
        "West Europe",
        "Southeast Asia",
        "Australia Southeast"
      ]
    },
    "applicationDiagnosticsStorageAccountName": {
        "type": "string",
        "metadata": {
          "description": "Name of the storage account with Azure diagnostics output"
        }
    },
    "applicationDiagnosticsStorageAccountResourceGroup": {
        "type": "string",
        "metadata": {
          "description": "The resource group name containing the storage account with Azure diagnostics output"
        }
    }
  },
  "variables": {
    "Updates": {
      "Name": "[Concat('Updates', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "Updates"
    },
    "AntiMalware": {
      "Name": "[concat('AntiMalware', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "AntiMalware"
    },
    "SQLAssessment": {
      "Name": "[Concat('SQLAssessment', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "SQLAssessment"
    },
    "diagnosticsStorageAccount": "[resourceId(parameters('applicationDiagnosticsStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName'))]"
  },
  "resources": [
    {
      "apiVersion": "2017-03-15-preview",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "sku": {
          "Name": "[parameters('serviceTier')]"
        },
    "retentionInDays": "[parameters('dataRetention')]"
      },
      "resources": [
        {
          "apiVersion": "2017-03-15-preview",
          "name": "VMSS Queries2",
          "type": "savedSearches",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "Category": "VMSS",
            "ETag": "*",
            "DisplayName": "VMSS Instance Count",
            "Query": "Event | where Source == "ServiceFabricNodeBootstrapAgent" | summarize AggregatedValue = count() by Computer",
            "Version": 1
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsEvent1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "Application",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsPerfCounter1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsPerformanceCounter",
          "properties": {
            "objectName": "Memory",
            "instanceName": "*",
            "intervalSeconds": 10,
            "counterName": "Available MBytes"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleIISLog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "IISLogs",
          "properties": {
            "state": "OnPremiseEnabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslog",
          "properties": {
            "syslogName": "kern",
            "syslogSeverities": [
              {
                "severity": "emerg"
              },
              {
                "severity": "alert"
              },
              {
                "severity": "crit"
              },
              {
                "severity": "err"
              },
              {
                "severity": "warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslogCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslogCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerf1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceObject",
          "properties": {
            "performanceCounters": [
              {
                "counterName": "% Used Inodes"
              },
              {
                "counterName": "Free Megabytes"
              },
              {
                "counterName": "% Used Space"
              },
              {
                "counterName": "Disk Transfers/sec"
              },
              {
                "counterName": "Disk Reads/sec"
              },
              {
                "counterName": "Disk Writes/sec"
              }
            ],
            "objectName": "Logical Disk",
            "instanceName": "*",
            "intervalSeconds": 10
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerfCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleCustomLog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "CustomLog",
          "properties": {
            "customLogName": "sampleCustomLog1",
            "description": "test custom log datasources",
            "inputs": [
              {
                "location": {
                  "fileSystemLocations": {
                    "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                    "linuxFileTypeLogPaths": [ "/var/logs" ]
                  }
                },
                "recordDelimiter": {
                  "regexDelimiter": {
                    "pattern": "\\n",
                    "matchIndex": 0,
                    "matchIndexSpecified": true,
                    "numberedGroup": null
                  }
                }
              }
            ],
            "extractions": [
              {
                "extractionName": "TimeGenerated",
                "extractionType": "DateTime",
                "extractionProperties": {
                  "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                  }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleCustomLogCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "CustomLogCollection",
          "properties": {
            "state": "LinuxLogsEnabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('workspaceName'))]",
          "type": "storageinsightconfigs",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "containers": [ 
              "wad-iis-logfiles" 
            ],
            "tables": [
              "WADWindowsEventLogsTable"
            ],
            "storageAccount": {
              "id": "[variables('diagnosticsStorageAccount')]",
              "key": "[listKeys(variables('diagnosticsStorageAccount'),'2015-06-15').key1]"
            }
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('Updates').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('Updates').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('Updates').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('AntiMalware').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('AntiMalware').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('AntiMalware').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('AntiMalware').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('SQLAssessment').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('SQLAssessment').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('SQLAssessment').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('SQLAssessment').GalleryName)]",
            "promotionCode": ""
          }
        }
      ]
    }
  ],
  "outputs": {
    "workspaceName": {
      "type": "string",
      "value": "[parameters('workspaceName')]"
    },
    "provisioningState": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').provisioningState]"
    },
    "source": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').source]"
    },
    "customerId": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').customerId]"
    },
    "pricingTier": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').sku.name]"
    },
    "retentionInDays": {
      "type": "int",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').retentionInDays]"
    },
    "portalUrl": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').portalUrl]"
    }
  }
}

```
### <a name="deploying-the-sample-template"></a>Bereitstellen der Beispielvorlage
So stellen Sie die Beispielvorlage bereit

1. Speichern Sie das angefügte Beispiel in einer Datei, z.B. `azuredeploy.json`. 
2. Bearbeiten Sie die Vorlage, um die gewünschte Konfiguration zu erstellen.
3. Verwenden Sie PowerShell oder die Befehlszeile zum Bereitstellen der Vorlage.

#### <a name="powershell"></a>PowerShell
```powershell
New-AzureRmResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile azuredeploy.json
```

#### <a name="command-line"></a>Befehlszeile
```cmd
azure config mode arm
azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile azuredeploy.json
```


## <a name="example-resource-manager-templates"></a>Resource Manager-Beispielvorlagen
Der Katalog mit Azure-Schnellstartvorlagen enthält u.a. folgende Vorlagen für Log Analytics:

* [Bereitstellen eines virtuellen Computers unter Windows mit der Log Analytics-VM-Erweiterung](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Bereitstellen eines virtuellen Computers unter Linux mit der Log Analytics-VM-Erweiterung](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [Überwachen von Azure Site Recovery mit einem vorhandenen Log Analytics-Arbeitsbereich](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [Überwachen von Azure-Web-Apps mit einem vorhandenen Log Analytics-Arbeitsbereich](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [Überwachen von SQL Azure mit einem vorhandenen Log Analytics-Arbeitsbereich](https://azure.microsoft.com/documentation/templates/101-sqlazure-oms-monitoring/)
* [Bereitstellen eines Service Fabric-Clusters und Überwachen des Clusters mit einem vorhandenen Log Analytics-Arbeitsbereich](https://azure.microsoft.com/documentation/templates/service-fabric-oms/)
* [Bereitstellen eines Service Fabric-Clusters und Erstellen eines Log Analytics-Arbeitsbereichs für dessen Überwachung](https://azure.microsoft.com/documentation/templates/service-fabric-vmss-oms/)

## <a name="next-steps"></a>Nächste Schritte
* [Bereitstellen von Agents in Azure-VMs mit Resource Manager-Vorlagen](log-analytics-azure-vm-extension.md)

