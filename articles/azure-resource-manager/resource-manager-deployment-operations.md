---
title: Bereitstellungsvorgänge mit Azure Resource Manager | Microsoft Docs
description: Erfahren Sie, wie Sie Azure Resource Manager-Bereitstellungsvorgänge mit dem Portal, mit PowerShell, mit der Azure-Befehlszeilenschnittstelle (CLI) und der REST-API anzeigen.
services: azure-resource-manager,virtual-machines
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 04/23/2018
ms.author: tomfitz
ms.openlocfilehash: 523ea3bf5d41231ab3281f9d8eb1fac8c3dfb55f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2018
---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Anzeigen von Bereitstellungsvorgängen mit Azure Resource Manager

Sie können die Vorgänge für eine Bereitstellung im Azure-Portal anzeigen. Die Anzeige der Vorgänge ist wahrscheinlich dann am interessantesten, wenn während der Bereitstellung ein Fehler auftritt. Daher konzentriert sich dieser Artikel auf das Anzeigen von fehlerhaften Vorgängen. Das Portal bietet eine Schnittstelle, mit der Sie die Fehler leicht finden und potenzielle Korrekturen ermitteln können.

Sie können die Fehler in Ihrer Bereitstellung anhand der Überwachungsprotokolle oder der Bereitstellungsvorgänge beheben. In diesem Artikel werden beide Methoden beschrieben. Unterstützung beim Beheben bestimmter Bereitstellungsfehler finden Sie unter [Beheben von häufigen Fehlern beim Bereitstellen von Ressourcen in Azure mit Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portal
Um die Bereitstellungsvorgänge anzuzeigen, gehen Sie folgendermaßen vor:

1. Beachten Sie den Status der letzten Bereitstellung für die an der Bereitstellung beteiligte Ressourcengruppe. Sie können diesen Status auswählen, um weitere Details anzuzeigen.
   
    ![Bereitstellungsstatus](./media/resource-manager-deployment-operations/deployment-status.png)
2. Der Verlauf mit den letzten Bereitstellungen wird angezeigt. Wählen Sie die fehlerhafte Bereitstellung aus.
   
    ![Bereitstellungsstatus](./media/resource-manager-deployment-operations/select-deployment.png)
3. Klicken Sie auf den Link, um eine Beschreibung der Ursache für die fehlerhafte Bereitstellung anzuzeigen. In der folgenden Abbildung ist der DNS-Eintrag nicht eindeutig.  
   
    ![Bereitstellungsfehler anzeigen](./media/resource-manager-deployment-operations/view-error.png)
   
    Diese Fehlermeldung sollte ausreichen, damit Sie mit der Problembehandlung beginnen können. Wenn Sie jedoch weitere Details zu den abgeschlossenen Tasks benötigen, können Sie die Vorgänge wie in den folgenden Schritten erläutert anzeigen.
4. Sie können alle Bereitstellungsvorgänge anzeigen. Wählen Sie einen Vorgang, um weitere Details anzuzeigen.
   
    ![Vorgänge anzeigen](./media/resource-manager-deployment-operations/view-operations.png)
   
    In diesem Fall sehen Sie, dass das Speicherkonto, das virtuelle Netzwerk und die Verfügbarkeitsgruppe erfolgreich erstellt wurden. Bei der öffentlichen IP-Adresse ist ein Fehler aufgetreten, und für andere Ressourcen wurden keine Versuche unternommen.
5. Sie können Ereignisse für die Bereitstellung anzeigen, indem Sie **Ereignisse**wählen.
   
    ![Ereignisse anzeigen](./media/resource-manager-deployment-operations/view-events.png)
6. Sie sehen alle Ereignisse für die Bereitstellung und können zum Anzeigen weiterer Einzelheiten ein Ereignis auswählen. Beachten Sie die Korrelations-IDs. Dieser Wert kann hilfreich sein, wenn Sie bei der Problembehandlung für eine Bereitstellung mit dem technischen Support zusammenarbeiten.
   
    ![Ereignisse anzeigen](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. Verwenden Sie den Befehl **Get-AzureRmResourceGroupDeployment** , um den allgemeinen Status einer Bereitstellung abzurufen. 

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup
  ```

   Sie können die Ergebnisse auch nach den Bereitstellungen filtern, die nicht erfolgreich waren.

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
  ```
   
2. Jede Bereitstellung umfasst mehrere Vorgänge. Jeder Vorgang repräsentiert einen Schritt des Bereitstellungsvorgangs. Um einen Bereitstellungsfehler zu ermitteln, benötigen Sie in der Regel Einzelheiten zu den Bereitstellungsvorgängen. Den Status der Vorgänge können Sie mit **Get-AzureRmResourceGroupDeploymentOperation** abrufen.

  ```powershell 
  Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
  ```

    Mit diesem Befehl werden mehrere Vorgänge im folgenden Format zurückgegeben:

  ```powershell
  Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
  OperationId    : A3EB2DA598E0A780
  Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
  PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
  ```

3. Um weitere Informationen zu fehlgeschlagenen Vorgängen zu erhalten, rufen Sie die Eigenschaften für Vorgänge mit dem Zustand **Fehler** ab.

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
  ```
   
    Dadurch werden alle fehlerhaften Vorgänge im folgenden Format zurückgegeben:

  ```powershell
  provisioningOperation : Create
  provisioningState     : Failed
  timestamp             : 2016-06-14T21:54:55.1468068Z
  duration              : PT3.1449887S
  trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
  serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
  statusCode            : BadRequest
  statusMessage         : @{error=}
  targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                          Microsoft.Network/publicIPAddresses/myPublicIP;
                          resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
  ```

    Beachten Sie die serviceRequestId und die trackingId für den Vorgang. Die serviceRequestId kann hilfreich sein, wenn Sie bei der Problembehandlung für eine Bereitstellung mit dem technischen Support zusammenarbeiten. Sie verwenden die trackingId im nächsten Schritt, um sich auf einen bestimmten Vorgang zu konzentrieren.
4. Verwenden Sie den folgenden Befehl, um die Statusmeldung für einen bestimmten fehlerhaften Vorgang abzurufen:

  ```powershell
  ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
  ```

    Ausgabe des Befehls:

  ```powershell
  code           message                                                                        details
  ----           -------                                                                        -------
  DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
  ```
4. Jeder Bereitstellungsvorgang in Azure enthält Inhalte für Anforderung und Antwort. Der Inhalt der Anforderung ist das, was Sie während der Bereitstellung an Azure senden (z.B. erstellen einer VM, eines Betriebssystemdatenträgers und andere Ressourcen). Der Inhalts der Antwort ist das, was Azure aus der Bereitstellungsanforderung zurücksendet. Während der Bereitstellung können Sie den Parameter **DeploymentDebugLogLevel** verwenden, um anzugeben, dass die Anforderung und/oder Antwort im Protokoll beibehalten werden. 

  Sie erhalten diese Information aus dem Protokoll, und speichern sie lokal mit den folgenden PowerShell-Befehlen:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
  ```

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

1. Rufen Sie den allgemeinen Status einer Bereitstellung mit dem Befehl **azure group deployment show** ab.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment
  ```
  
1. Einer der zurückgegebenen Werte ist die **correlationId**. Dieser Wert wird verwendet, um verknüpfte Ereignisse nachzuverfolgen. Er kann hilfreich sein, wenn Sie mit dem technischen Support an der Problembehandlung für eine Bereitstellung zusammenarbeiten.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
  ```

1. Verwenden Sie Folgendes, um die Vorgänge einer Bereitstellung anzuzeigen:

  ```azurecli
  az group deployment operation list -g ExampleGroup -n ExampleDeployment
  ```

## <a name="rest"></a>REST

1. Rufen Sie Informationen über eine Bereitstellung mit dem Vorgang zum Abrufen von Informationen zu einer Vorlage mithilfe des Befehls [Get or Update](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get) ab.

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
  ```

    Beachten Sie bei der Antwort insbesondere die Elemente **provisioningState**, **correlationId** und **error**. Die **correlationId** wird verwendet, um verknüpfte Ereignisse nachzuverfolgen. Sie kann hilfreich sein, wenn Sie mit dem technischen Support an der Problembehandlung für eine Bereitstellung zusammenarbeiten.

  ```json
  { 
    ...
    "properties": {
      "provisioningState":"Failed",
      "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
      ...
      "error":{
        "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
        "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
      }  
    }
  }
  ```

2. Rufen Sie Informationen zu Bereitstellungsvorgängen mit [Alle Vorlagenbereitstellungsvorgänge auflisten](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_List) ab. 

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
  ```
   
    Die Antwort enthält Anforderungs- und/oder Antwortinformationen, basierend darauf, was Sie während der Bereitstellung in der **debugSetting**-Eigenschaft angegeben haben.

  ```json
  {
    ...
    "properties": 
    {
      ...
      "request":{
        "content":{
          "location":"West US",
          "properties":{
            "accountType": "Standard_LRS"
          }
        }
      },
      "response":{
        "content":{
          "error":{
            "message":"Conflict","code":"Conflict"
          }
        }
      }
    }
  }
  ```


## <a name="next-steps"></a>Nächste Schritte
* Unterstützung beim Beheben bestimmter Bereitstellungsfehler finden Sie unter [Beheben von häufigen Fehlern beim Bereitstellen von Ressourcen in Azure mit Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Weitere Informationen zur Verwendung der Aktivitätsprotokolle für die Überwachung anderer Arten von Aktionen finden Sie unter [Anzeigen von Aktivitätsprotokolle zum Verwalten von Azure-Ressourcen](resource-group-audit.md).
* Informationen zum Überprüfen der Bereitstellung vor der Ausführung finden Sie unter [Bereitstellen einer Ressourcengruppe mit Azure Resource Manager-Vorlagen](resource-group-template-deploy.md).

