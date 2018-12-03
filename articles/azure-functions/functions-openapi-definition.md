---
title: Erstellen einer OpenAPI-Definition für eine Funktion | Microsoft-Dokumentation
description: Erstellen Sie eine OpenAPI-Definition, die anderen Apps und Diensten das Aufrufen Ihrer Funktion in Azure ermöglicht.
services: functions
keywords: OpenAPI, Swagger, Cloud-Apps, Clouddienste,
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: glenga
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 2d50e4c2352444d29bdb090bc9a2a7947ecc6a50
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52496029"
---
# <a name="create-an-openapi-definition-for-a-function"></a>Erstellen einer OpenAPI-Definition für eine Funktion

REST-APIs werden häufig mithilfe einer OpenAPI-Definition beschrieben (früher bezeichnet als [Swagger](http://swagger.io/)-Datei). Diese Definition enthält Informationen zu den in einer API verfügbaren Vorgängen sowie zur Strukturierung der Anforderungs- und Antwortdaten für die API.

In diesem Tutorial erstellen Sie eine Funktion, die ermittelt, ob eine Notfallreparatur einer Windturbine kosteneffizient ist. Anschließend erstellen Sie eine OpenAPI-Definition für die Funktionen-App, damit die Funktion von anderen Apps und Diensten aufgerufen werden kann.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Funktion in Azure
> * Generieren einer OpenAPI-Definition mit OpenAPI-Tools
> * Ändern der Definition zum Bereitstellen zusätzlicher Metadaten
> * Testen der Definition durch Aufrufen der Funktion

> [!IMPORTANT]
> Das OpenAPI-Feature befindet sich derzeit in der Vorschauphase und ist nur für Version 1.x der Azure Functions-Runtime verfügbar.

## <a name="create-a-function-app"></a>Erstellen einer Funktionen-App

Sie müssen über eine Funktionen-App verfügen, die die Ausführung Ihrer Funktionen in Azure hostet. Sie können mit einer Funktions-App Funktionen zu logischen Einheiten gruppieren. Dies erleichtert die Verwaltung, Bereitstellung, Skalierung und Freigabe von Ressourcen. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="set-the-functions-runtime-version"></a>Festlegen der Version der Functions-Runtime

Die von Ihnen erstellte Funktions-App verwendet standardmäßig Version 2.x der Runtime. Sie müssen die Runtimeversion zurück auf 1.x setzen, bevor Sie die Funktion erstellen.

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

## <a name="create-the-function"></a>Erstellen der Funktion

In diesem Tutorial wird eine von HTTP ausgelöste Funktion verwendet, die zwei Parameter akzeptiert: die geschätzte Zeit zum Durchführen einer Turbinenreparatur (in Stunden) und die Kapazität der Turbine (in Kilowatt). Die Funktion berechnet dann die Kosten einer Reparatur und den Umsatzerlös, der in einem Zeitraum von 24 Stunden von der Turbine generiert werden könnte.

1. Erweitern Sie die Funktionen-App, und wählen Sie die Schaltfläche **+** neben **Functions** aus. Wenn dies die erste Funktion in Ihrer Funktionen-App ist, wählen Sie **Benutzerdefinierte Funktion**. Hiermit wird der vollständige Satz von Funktionsvorlagen angezeigt. 

    ![Schnellstartseite für Funktionen im Azure-Portal](media/functions-openapi-definition/add-first-function.png)

1. Geben Sie `http` in das Suchfeld ein, und wählen Sie dann **C#** für die HTTP-Triggervorlage aus. 

    ![Auswählen des HTTP-Triggers](./media/functions-openapi-definition/select-http-trigger-portal.png)

1. Geben Sie `TurbineRepair` als **Namen** der Funktion ein, wählen Sie `Function` als **[Authentifizierungsebene](functions-bindings-http-webhook.md#http-auth)** aus, und wählen Sie dann **Erstellen** aus.  

    ![Erstellen der durch HTTP ausgelösten Funktion](./media/functions-openapi-definition/select-http-trigger-portal-2.png)

1. Ersetzen Sie den Inhalt der Datei „run.csx“ durch den folgenden Code, und klicken Sie auf **Speichern**:

    ```csharp
    using System.Net;

    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        //Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();
        int hours = data.hours;
        int capacity = data.capacity;

        //Formulas to calculate revenue and cost
        double revenueOpportunity = capacity * revenuePerkW * 24;  
        double costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;

        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        }

        return req.CreateResponse(HttpStatusCode.OK, new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    Dieser Funktionscode gibt Folgendes zurück: eine Meldung `Yes` oder `No`, um anzugeben, ob eine Notfallreparatur kosteneffizient ist, den möglichen Umsatzerlös der Turbine und die Kosten für die Reparatur der Turbine. 

1. Klicken Sie zum Testen der Funktion ganz rechts auf **Testen**, um die Registerkarte „Testen“ zu erweitern. Geben Sie den folgenden Wert für **Anforderungstext** ein, und klicken Sie dann auf **Ausführen**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Testen der Funktion im Azure-Portal](media/functions-openapi-definition/test-function.png)

    Im Text der Antwort wird der folgende Wert zurückgegeben.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Sie haben jetzt eine Funktion, die die Kosteneffizienz von Notfallreparaturen ermittelt. Als Nächstes generieren und ändern Sie eine OpenAPI-Definition für die Funktionen-App.

## <a name="generate-the-openapi-definition"></a>Generieren der OpenAPI-Definition

Jetzt können Sie die OpenAPI-Definition generieren. Diese Definition kann von anderen Microsoft-Technologien verwendet werden, beispielsweise von API-Apps, [PowerApps](functions-powerapps-scenario.md) und [Microsoft Flow](../azure-functions/app-service-export-api-to-powerapps-and-flow.md) sowie von Entwicklertools von Drittanbietern wie [Postman](https://www.getpostman.com/docs/importing_swagger) und [vielen weiteren Paketen](http://swagger.io/tools/).

1. Wählen Sie nur die *Verben* aus, die von Ihrer API (in diesem Fall POST) unterstützt werden. Dadurch wird die generierte API-Definition übersichtlicher.

    1. Ändern Sie auf der Registerkarte **Integrieren** der neuen HTTP-Triggerfunktion die Einstellung **Zulässige HTTP-Methoden** in **Ausgewählte Methoden**.

    1. Deaktivieren Sie unter **Ausgewählte HTTP-Methoden** alle Optionen außer **POST**, und klicken Sie dann auf **Speichern**.

        ![Ausgewählte HTTP-Methoden](media/functions-openapi-definition/selected-http-methods.png)

1. Klicken Sie auf den Namen Ihrer Funktionen-App (z. B. **function-demo-energy**) > **Plattformfeatures** > **API-Definition**.

    ![API-Definition](media/functions-openapi-definition/api-definition.png)

1. Klicken Sie auf der Registerkarte **API-Definition** auf **Funktion**.

    ![API-Definitionsquelle](media/functions-openapi-definition/api-definition-source.png)

    In diesem Schritt wird eine Reihe von OpenAPI-Optionen für Ihre Funktionen-App aktiviert. Dazu zählen ein Endpunkt zum Hosten einer OpenAPI-Datei aus der Domäne Ihrer Funktionen-App, eine Inlinekopie des [OpenAPI-Editors](http://editor.swagger.io) und ein Vorlagengenerator für API-Definitionen.

1. Klicken Sie auf **API-Definitionsvorlage generieren** > **Speichern**.

    ![API-Definitionsvorlage generieren](media/functions-openapi-definition/generate-template.png)

    Azure sucht in Ihrer Funktionen-App nach HTTP-Triggerfunktionen und verwendet die Informationen in „functions.json“, um eine OpenAPI-Definition zu generieren. Folgende Definition wird generiert:

    ```yaml
    swagger: '2.0'
    info:
    title: function-demo-energy.azurewebsites.net
    version: 1.0.0
    host: function-demo-energy.azurewebsites.net
    basePath: /
    schemes:
    - https
    - http
    paths:
    /api/TurbineRepair:
        post:
        operationId: /api/TurbineRepair/post
        produces: []
        consumes: []
        parameters: []
        description: >-
            Replace with Operation Object
            #http://swagger.io/specification/#operationObject
        responses:
            '200':
            description: Success operation
        security:
            - apikeyQuery: []
    definitions: {}
    securityDefinitions:
    apikeyQuery:
        type: apiKey
        name: code
        in: query
    ```

    Diese Definition wird als _Vorlage_ beschrieben, da für eine vollständige OpenAPI-Definition weitere Metadaten erforderlich sind. Sie ändern die Definition im nächsten Schritt.

## <a name="modify-the-openapi-definition"></a>Ändern der OpenAPI-Definition

Nun ändern Sie die generierte Vorlagendefinition, um zusätzliche Metadaten zu den API-Vorgängen und Datenstrukturen bereitzustellen. Löschen Sie in der **API-Definition** die generierte Definition von `post` bis zum Ende der Definition, fügen Sie den unten stehenden Inhalt ein, und klicken Sie auf **Speichern**.

```yaml
    post:
      operationId: CalculateCosts
      description: Determines if a technician should be sent for repair
      summary: Calculates costs
      x-ms-summary: Calculates costs
      x-ms-visibility: important
      produces:
        - application/json
      consumes:
        - application/json
      parameters:
        - name: body
          in: body
          description: Hours and capacity used to calculate costs
          x-ms-summary: Hours and capacity
          x-ms-visibility: important
          required: true
          schema:
            type: object
            properties:
              hours:
                description: The amount of effort in hours required to conduct repair
                type: number
                x-ms-summary: Hours
                x-ms-visibility: important
              capacity:
                description: The max output of a turbine in kilowatts
                type: number
                x-ms-summary: Capacity
                x-ms-visibility: important
      responses:
        200:
          description: Message with cost and revenue numbers
          x-ms-summary: Message
          schema:
           type: object
           properties:
            message:
              type: string
              description: Returns Yes or No depending on calculations
              x-ms-summary: Message 
            revenueOpportunity:
              type: string
              description: The revenue opportunity cost
              x-ms-summary: RevenueOpportunity 
            costToFix:
              type: string
              description: The cost in $ to fix the turbine
              x-ms-summary: CostToFix
      security:
        - apikeyQuery: []
definitions: {}
securityDefinitions:
  apikeyQuery:
    type: apiKey
    name: code
    in: query
```

In diesem Fall konnten Sie einfach die aktualisierten Metadaten einfügen, es ist jedoch wichtig, dass Sie die Änderungen verstehen, die wir an der Standardvorlage vorgenommen haben:

* Wir haben angegeben, dass die API Daten in einem JSON-Format erzeugt und verwendet.

* Wir haben die erforderlichen Parameter sowie ihre Namen und Datentypen angegeben.

* Wir haben die Rückgabewerte für eine erfolgreiche Antwort sowie ihre Namen und Datentypen angegeben.

* Wir haben benutzerfreundliche Zusammenfassungen und Beschreibungen für die API sowie die zugehörigen Vorgänge und Parameter bereitgestellt. Dies ist wichtig für die Benutzer, die diese Funktion verwenden werden.

* Wir haben die Elemente „x-ms-summary“ und „x-ms-visibility“ hinzugefügt, die in der Benutzeroberfläche für Microsoft Flow und Logic Apps verwendet werden. Weitere Informationen finden Sie unter [OpenAPI-Erweiterungen für benutzerdefinierte Connectors in Microsoft Flow](https://preview.flow.microsoft.com/documentation/customapi-how-to-swagger/).

> [!NOTE]
> Für die Sicherheitsdefinition haben wir die Standardauthentifizierungsmethode (API-Schlüssel) übernommen. Wenn Sie einen anderen Authentifizierungstyp verwenden, würden Sie diesen Abschnitt der Definition ändern.

Weitere Informationen zum Definieren von API-Vorgängen finden Sie unter [Open API specification](https://swagger.io/specification/#operationObject) (Open-API-Spezifikation).

## <a name="test-the-openapi-definition"></a>Testen der OpenAPI-Definition

Bevor Sie die API-Definition verwenden, empfiehlt es sich, sie in der Azure Functions-Benutzeroberfläche zu testen.

1. Kopieren Sie auf der Registerkarte **Verwalten** Ihrer Funktion unter **Hostschlüssel (alle Funktionen)** den Schlüssel **default**.

    ![Kopieren des API-Schlüssels](media/functions-openapi-definition/copy-api-key.png)

    > [!NOTE]
    >Diesen Schlüssel verwenden Sie zum Testen und Aufrufen der API über eine App oder einen Dienst.

1. Kehren Sie zur API-Definition zurück: **function-demo-energy** > **Plattformfeatures** > **API-Definition**.

1. Klicken Sie im rechten Bereich auf **Authenticate** (Authentifizieren), geben Sie den kopierten API-Schlüssel ein, und klicken Sie auf **Authentifizieren**.

    ![Authentifizieren mit einem API-Schlüssel](media/functions-openapi-definition/authenticate-api-key.png)

1. Führen Sie einen Bildlauf nach unten durch, und klicken Sie auf **Try this operation** (Diesen Vorgang testen).

    ![Try this operation (Diesen Vorgang testen)](media/functions-openapi-definition/try-operation.png)

1. Geben Sie Werte für **hours** und **capacity** ein.

    ![Parameter eingeben](media/functions-openapi-definition/parameters.png)

    Beachten Sie, dass in der Benutzeroberfläche die Beschreibungen aus der API-Definition verwendet werden.

1. Klicken Sie auf **Send request** (Anforderung senden) und anschließend auf die Registerkarte **Pretty** (Schöndruck), um die Ausgabe anzuzeigen.

    ![Send a request (Anforderung senden)](media/functions-openapi-definition/send-request.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Funktion in Azure
> * Generieren einer OpenAPI-Definition mit OpenAPI-Tools
> * Ändern der Definition zum Bereitstellen zusätzlicher Metadaten
> * Testen der Definition durch Aufrufen der Funktion

Fahren Sie mit dem nächsten Thema fort, um zu erfahren, wie Sie eine PowerApps-App erstellen, die die von Ihnen erstellte OpenAPI-Definition verwendet.

> [!div class="nextstepaction"]
> [Call a function from PowerApps](functions-powerapps-scenario.md) (Aufrufen einer Funktion über PowerApps)
