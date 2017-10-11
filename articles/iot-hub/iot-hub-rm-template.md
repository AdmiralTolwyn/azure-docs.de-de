---
title: Erstellen einer Azure IoT Hub-Instanz mithilfe einer Vorlage (.NET) | Microsoft Docs
description: Erfahren Sie, wie Sie mithilfe einer Azure Resource Manager-Vorlage und einem C#-Programm eine IoT Hub-Instanz erstellen.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a447b40c-c728-487e-875d-db554db5adc3
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 0f197a28e0c51b06d0b47a03c29fe1fde0c6b78d
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/29/2017
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Erstellen einer IoT Hub-Instanz mithilfe einer Azure Resource Manager-Vorlage (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Sie können den Azure-Ressourcen-Manager verwenden, um Azure IoT Hubs programmgesteuert zu erstellen und zu verwalten. In diesem Tutorial erfahren Sie, wie Sie unter Verwendung einer Azure Resource Manager-Vorlage mit einem C#-Programm einen IoT Hub erstellen.

> [!NOTE]
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Azure Resource Manager-Bereitstellung und klassische Bereitstellung](../azure-resource-manager/resource-manager-deployment-model.md).  Dieser Artikel behandelt die Verwendung des Azure Resource Manager-Bereitstellungsmodells.

Für dieses Tutorial benötigen Sie Folgendes:

* Visual Studio 2015 oder Visual Studio 2017
* Ein aktives Azure-Konto. <br/>Wenn Sie kein Konto besitzen, können Sie in nur wenigen Minuten ein [kostenloses Konto][lnk-free-trial] erstellen.
* Ein [Azure Storage-Konto][lnk-storage-account] zum Speichern Ihrer Azure Resource Manager-Vorlagendateien
* [Azure PowerShell 1.0][lnk-powershell-install] oder höher.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Vorbereiten des Visual Studio-Projekts

1. Erstellen Sie in Visual Studio mithilfe der Projektvorlage **Konsolen-App (.NET Framework)** ein Visual C#-Projekt für den klassischen Windows-Desktop. Geben Sie dem Projekt den Namen **CreateIoTHub**.

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **NuGet-Pakete verwalten**.

3. Markieren Sie im NuGet-Paket-Manager die Option **Vorabversion einbeziehen**, und suchen Sie auf der Seite **Durchsuchen** nach **Microsoft.Azure.Management.ResourceManager**. Wählen Sie das Paket aus, klicken Sie auf **Installieren** und dann unter **Änderungen überprüfen** auf **OK**. Klicken Sie anschließend auf **Ich akzeptiere**, um die Lizenzen zu akzeptieren.

4. Suchen Sie im NuGet-Paket-Manager nach **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Klicken Sie auf **Installieren**, dann unter **Änderungen überprüfen** auf **OK**. Klicken Sie anschließend auf **Ich akzeptiere**, um die Lizenz zu akzeptieren.

5. Ersetzen Sie in „Program.cs“ die vorhandenen **using**-Anweisungen durch folgenden Code:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. Fügen Sie in „Program.cs“ die folgenden statischen Variablen ein, mit denen die Platzhalterwerte ersetzt werden. Weiter oben in diesem Tutorial haben Sie sich die Werte für **ApplicationId**, **SubscriptionId**, **TenantId** und **Password** notiert. Der **Name Ihres Azure Storage-Kontos** ist der Name des Azure Storage-Kontos, in dem Sie Ihre Azure Resource Manager-Vorlagendateien speichern. **Ressourcengruppenname** ist der Name der Ressourcengruppe, die beim Erstellen des IoT-Hubs verwendet wird. Der Name kann der Name einer neuen oder einer bereits vorhandenen Ressourcengruppe sein. **Bereitstellungsname** ist ein Name für die Bereitstellung (beispielsweise **Deployment_01**).

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>Einsenden einer Vorlage zum Erstellen eines IoT Hubs

Verwenden Sie eine JSON-Vorlage und eine Parameterdatei, um einen IoT Hub in der Ressourcengruppe zu erstellen. Sie können auch eine Azure Resource Manager-Vorlage verwenden, um Änderungen an einem vorhandenen IoT Hub vorzunehmen.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, klicken Sie auf **Hinzufügen** und anschließend auf **Neues Element**. Fügen Sie dem Projekt eine JSON-Datei namens **template.json** hinzu.

2. Ersetzen Sie den Inhalt von **template.json** durch die folgende Ressourcendefinition, um der Region **USA, Osten** einen Standard-IoT-Hub hinzuzufügen: Die aktuelle Liste der Regionen, die IoT Hub unterstützen, finden Sie unter [Azure-Status][lnk-status]:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, klicken Sie auf **Hinzufügen** und anschließend auf **Neues Element**. Fügen Sie dem Projekt eine JSON-Datei namens **parameters.json** hinzu.

4. Ersetzen Sie den Inhalt von **parameters.json** durch die folgenden Parameterinformationen, um einen Namen für den neuen IoT Hub festzulegen (beispielsweise **{Ihre Initialen}mynewiothub**). Der Name des IoT-Hubs muss global eindeutig sein und sollte daher Ihren Namen oder Ihre Initialen enthalten:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. Stellen Sie über den **Server-Explorer** eine Verbindung mit Ihrem Azure-Abonnement her, und erstellen Sie in Ihrem Azure Storage-Konto einen Container namens **templates**. Legen Sie im Bereich **Eigenschaften** die Berechtigungen vom Typ **Öffentlicher Lesezugriff** für den Container **templates** auf **Blob** fest.

6. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf den Container **templates**, und klicken Sie dann auf **BLOB-Container anzeigen**. Klicken Sie auf die Schaltfläche **Blob hochladen**, wählen Sie die beiden Dateien **parameters.json** und **templates.json** aus, und klicken Sie dann auf **Öffnen**, um die JSON-Dateien in den Container **templates** hochzuladen. Die URLs der Blobs mit den JSON-Daten lauten:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Fügen Sie "Program.cs" die folgende Methode hinzu:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Fügen Sie den folgenden Code der Methode **CreateIoTHub** hinzu, um die Vorlagen- und Parameterdateien an den Azure Resource Manager zu senden:

    ```csharp
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

9. Fügen Sie den folgenden Code der Methode **CreateIoTHub** hinzu, um den Status und die Schlüssel für den neuen IoT Hub anzuzeigen:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Fertigstellen und Ausführen der Anwendung

Sie können die Anwendung jetzt durch Aufrufen der Methode **CreateIoTHub** fertig stellen und das Projekt dann erstellen und ausführen.

1. Fügen Sie den folgenden Code am Ende der **Main** -Methode hinzu:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Klicken Sie auf **Erstellen** und auf **Projektmappe erstellen**. Beheben Sie alle Fehler.

3. Klicken Sie auf **Debuggen** und dann auf **Debuggen starten**, um die Anwendung auszuführen. Es kann mehrere Minuten dauern, bis die Bereitstellung abgeschlossen ist.

4. Um zu überprüfen, ob Ihre Anwendung den neuen IoT-Hub hinzugefügt hat, besuchen Sie das [Azure-Portal][lnk-azure-portal], und zeigen Sie die Liste der Ressourcen an. Verwenden Sie alternativ das PowerShell-Cmdlet **Get-AzureRmResource**.

> [!NOTE]
> Diese Beispielanwendung fügt einen für Sie kostenpflichtigen S1-Standard-IoT Hub hinzu. Sie können den IoT-Hub nach Abschluss des Beispiels über das [Azure-Portal][lnk-azure-portal] oder mithilfe des PowerShell-Cmdlets **Remove-AzureRmResource** löschen.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun einen IoT Hub mithilfe einer Azure Resource Manager-Vorlage mit einem C#-Programm bereitgestellt haben, möchten Sie vielleicht mehr wissen:

* Informieren Sie sich über die Funktionen der [IoT Hub-Ressourcenanbieter-REST-API][lnk-rest-api].
* Weitere Informationen zu den Funktionen des Azure Resource Manager finden Sie unter [Übersicht über Azure Resource Manager][lnk-azure-rm-overview].

Weitere Informationen zum Entwickeln für IoT Hub finden Sie in folgenden Artikeln:

* [Einführung in das C SDK][lnk-c-sdk]
* [Azure IoT SDKs][lnk-sdks]

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

* [Simulieren eines Geräts mit Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
