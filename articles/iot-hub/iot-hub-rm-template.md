---
title: Erstellen eines IoT Hubs mithilfe einer ARM-Vorlage und C# | Microsoft Docs
description: Nutzen Sie dieses Tutorial für erste Schritte mit Resource Manager-Vorlagen zum Erstellen eines IoT Hubs mit einem C#-Programm.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: dobett

---
# Erstellen eines IoT Hubs mithilfe eines C#-Programms mit einer Resource Manager-Vorlage | Microsoft Azure
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Einführung
Sie können den Azure-Ressourcen-Manager verwenden, um Azure IoT Hubs programmgesteuert zu erstellen und zu verwalten. In diesem Tutorial erfahren Sie, wie Sie anhand einer Resource Manager-Vorlage mit einem C#-Programm einen IoT Hub erstellen.

> [!NOTE]
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Ressourcen-Manager und klassische Bereitstellungen](../resource-manager-deployment-model.md). Dieser Artikel behandelt die Verwendung des Ressourcen-Manager-Bereitstellungsmodells.
> 
> 

Für dieses Tutorial benötigen Sie Folgendes:

* Microsoft Visual Studio 2015.
* Ein aktives Azure-Konto. <br/>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][lnk-free-trial].
* Ein [Azure-Speicherkonto][lnk-storage-account], wo Sie die Vorlagendateien speichern können.
* [Microsoft Azure PowerShell 1.0][lnk-powershell-install] oder höher.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Vorbereiten des Visual Studio-Projekts
1. Erstellen Sie in Visual Studio mithilfe der Projektvorlage **Konsolenanwendung** ein Visual C#-Windows-Projekt. Geben Sie dem Projekt den Namen **CreateIoTHub**.
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **NuGet-Pakete verwalten**.
3. Markieren Sie im NuGet-Paket-Manager die Option **Vorabversion einbeziehen**, und suchen Sie nach **Microsoft.Azure.Management.ResourceManager**. Klicken Sie auf **Installieren**, dann unter **Änderungen überprüfen** auf **OK**. Klicken Sie anschließend auf **Ich akzeptiere**, um die Lizenzen zu akzeptieren.
4. Suchen Sie im NuGet-Paket-Manager nach **Microsoft.IdentityModel.Clients.ActiveDirectory**. Klicken Sie auf **Installieren**, dann unter **Änderungen überprüfen** auf **OK**. Klicken Sie anschließend auf **Ich akzeptiere**, um die Lizenz zu akzeptieren.
5. Ersetzen Sie in „Program.cs“ die vorhandenen **using**-Anweisungen durch folgenden Text:
   
    ```
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
6. Fügen Sie in „Program.cs“ die folgenden statischen Variablen ein, mit denen die Platzhalterwerte ersetzt werden. Weiter oben in diesem Tutorial haben Sie sich die Werte für **ApplicationId**, **SubscriptionId**, **TenantId** und **Password** notiert. **Der Name Ihres Speicherkontos** ist der Name des Azure-Speicherkontos, in dem Sie die Vorlagendateien speichern. **Ressourcengruppenname** ist der Name der Ressourcengruppe, die beim Erstellen des IoT Hubs verwendet wird – diese Gruppe kann neu oder bereits vorhanden sein. **Bereitstellungsname** ist ein Name für die Bereitstellung, wie z. B. **Deployment\_01**.
   
    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## Einsenden einer Vorlage zum Erstellen eines IoT Hubs
Verwenden Sie eine JSON-Vorlage und eine Parameterdatei, um einen IoT Hub in der Ressourcengruppe zu erstellen. Sie können auch eine Vorlage verwenden, um Änderungen an einem vorhandenen IoT Hub vorzunehmen.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, klicken Sie auf **Hinzufügen** und dann auf **Neues Element**. Fügen Sie dem Projekt eine JSON-Datei namens **template.json** hinzu.
2. Ersetzen Sie den Inhalt von **template.json** mit der folgenden Ressourcendefinition, um der Region **USA, Osten** einen Standard-IoT Hub hinzuzufügen (eine aktuelle Liste der Standorte, die IoT Hub unterstützen, finden Sie unter [Azure-Status][lnk-status]):
   
    ```
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
3. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, klicken Sie auf **Hinzufügen** und dann auf **Neues Element**. Fügen Sie dem Projekt eine JSON-Datei namens **parameters.json** hinzu.
4. Ersetzen Sie den Inhalt von **parameters.json** mit den folgenden Parameterinformationen, die z.B. **{Ihre Initialen}mynewiothub** als Namen des neuen IoT Hubs festlegen. Beachten Sie, dass dieser Name global eindeutig sein muss und daher Ihren Namen oder Ihre Initialen enthalten sollte:
   
    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
5. Verbinden Sie sich im **Server-Explorer** mit Ihrem Azure-Abonnement, und erstellen Sie in Ihrem Speicherkonto einen Container namens **templates**. Setzen Sie im Bereich **Eigenschaften** die Berechtigungen für den **Öffentlichen Lesezugriff** für den Container **templates** auf **Blob**.
6. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf den Container **templates**, und klicken Sie dann auf **BLOB-Container anzeigen**. Klicken Sie auf die Schaltfläche **Blob hochladen**, wählen Sie die beiden Dateien **parameters.json** und **templates.json**, und klicken Sie dann auf **Öffnen**, um die JSON-Dateien in den Container **templates** hochzuladen. Die URLs der Blobs mit den JSON-Daten lauten:
   
    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Fügen Sie "Program.cs" die folgende Methode hinzu:
   
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
   
    }
    ```
8. Fügen Sie den folgenden Code der Methode **CreateIoTHub** hinzu, um die Vorlagen- und Parameterdateien an den Azure Resource Manager zu senden:
   
    ```
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
   
    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);
   
    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## Fertigstellen und Ausführen der Anwendung
Sie können die Anwendung jetzt durch Aufrufen der Methode **CreateIoTHub** fertig stellen und das Projekt dann erstellen und ausführen.

1. Fügen Sie den folgenden Code am Ende der **Main**-Methode hinzu:
   
    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
2. Klicken Sie auf **Erstellen** und auf **Projektmappe erstellen**. Beheben Sie alle Fehler.
3. Klicken Sie auf **Debuggen** und dann auf **Debuggen starten**, um die Anwendung auszuführen. Es kann mehrere Minuten dauern, bis die Bereitstellung abgeschlossen ist.
4. Das Hinzufügen des neuen IoT Hubs durch die Anwendung lässt sich durch Anzeigen der Ressourcenliste im [Portal][lnk-azure-portal] oder mithilfe des PowerShell-Cmdlets **Get-AzureRmResource** überprüfen.

> [!NOTE]
> Diese Beispielanwendung fügt einen für Sie kostenpflichtigen S1-Standard-IoT Hub hinzu. Sie können den IoT Hub nach Abschluss des Beispiels über das [Portal][lnk-azure-portal] oder mithilfe des PowerShell-Cmdlets **Remove-AzureRmResource** löschen.
> 
> 

## Nächste Schritte
Nachdem Sie nun einen IoT Hub mithilfe einer Azure Resource Manager-Vorlage mit einem C#-Programm bereitgestellt haben, möchten Sie vielleicht mehr wissen:

* Informieren Sie sich über die Funktionen der [IoT Hub-Ressourcenanbieter-REST-API][lnk-rest-api].
* Weitere Informationen zu den Fähigkeiten des Azure-Ressourcen-Manager finden Sie unter [Übersicht über den Azure-Ressourcen-Manager][lnk-azure-rm-overview].

Weitere Informationen zum Entwickeln für IoT Hub finden Sie unter:

* [Einführung in das C SDK][lnk-c-sdk]
* [IoT Hub-SDKs][lnk-sdks]

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

* [Entwerfen Ihrer Lösung][lnk-design]
* [Erkunden der Geräteverwaltung mithilfe der Beispielbenutzeroberfläche][lnk-dmui]
* [Simulieren eines Geräts mit dem Gateway SDK][lnk-gateway]
* [Verwenden des Azure-Portals zur Verwaltung von IoT Hub][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-storage-account]: ../storage/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!----HONumber=AcomDC_0907_2016-->