---
Titel: Erneutes Trainieren eines klassischen Webdiensts titleSuffix: Azure Machine Learning Studio-Beschreibung: Erfahren Sie, wie Sie ein Modell programmgesteuert erneut trainieren und den Webdienst aktualisieren, sodass er das neu trainierte Modell in Azure Azure Machine Learning verwendet.
Dienste: machine-learning ms.service: machine-learning ms.subservice: studio ms.topic: Artikel

Autor: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT ms.date: 19.04.2017
---
# <a name="retrain-a-classic-azure-machine-learning-studio-web-service"></a>Erneutes Trainieren eines klassischen Azure Machine Learning Studio-Webdiensts
Der von Ihnen bereitgestellte Vorhersagewebdienst ist der Standardbewertungsendpunkt. Standardendpunkte werden mit den ursprünglichen Trainings- und Bewertungsexperimenten synchronisiert. Daher kann das trainierte Modell für den Standardendpunkt nicht ersetzt werden. Zum erneuten Trainieren des Webdiensts müssen Sie dem Webdienst einen neuen Endpunkt hinzufügen.

## <a name="prerequisites"></a>Voraussetzungen
Sie benötigen ein Trainingsexperiment und ein Vorhersageexperiment (wie unter [Programmgesteuertes erneutes Trainieren von Machine Learning-Modellen](retrain-models-programmatically.md) beschrieben).

> [!IMPORTANT]
> Das Vorhersageexperiment muss als klassischer Machine Learning-Webdienst bereitgestellt werden.
>
>

Weitere Informationen zum Bereitstellen von Webdiensten finden Sie unter [Bereitstellen von Azure Machine Learning-Webdiensten](publish-a-machine-learning-web-service.md).

## <a name="add-a-new-endpoint"></a>Hinzufügen eines neuen Endpunkts
Der von Ihnen bereitgestellte Vorhersagewebdienst enthält einen standardmäßigen Bewertungsendpunkt, der mit den ursprünglichen Trainings- und Bewertungsexperimenten des trainierten Modells synchronisiert wird. Erstellen Sie zum Aktualisieren des Webdiensts mit einem neuen trainierten Modell einen neuen Bewertungsendpunkt.

Gehen Sie wie im Folgenden beschrieben vor, um einen neuen Bewertungsendpunkt für den Vorhersagewebdienst zu erstellen, der mit dem trainierten Modell aktualisiert werden kann:

> [!NOTE]
> Achten Sie darauf, dass Sie den Endpunkt dem Vorhersagewebdienst hinzufügen und nicht dem Trainingswebdienst. Wenn Sie sowohl einen Trainings- als auch einen Vorhersagewebdienst korrekt bereitgestellt haben, werden zwei separate Webdienste aufgeführt. Der Vorhersagewebdienst sollte mit „[predictive exp.]“ enden.
>
>

Es gibt zwei Möglichkeiten zum Hinzufügen eines neuen Endpunkts zu einem Webdienst:

1. Programmgesteuert
2. Verwenden des Portals für Microsoft Azure-Webdienste

### <a name="programmatically-add-an-endpoint"></a>Programmgesteuertes Hinzufügen eines Endpunkts
Sie können Bewertungsendpunkte mithilfe des Beispielcodes in diesem [GitHub-Repository](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint)hinzufügen.

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Verwenden des Portals für Microsoft Azure-Webdienste zum Hinzufügen eines Endpunkts
1. Klicken Sie in Machine Learning Studio links auf „Webdienste“.
2. Klicken Sie unten auf dem Dashboards des Webdiensts auf **Manage endpoints preview**(Endpunktvorschau verwalten).
3. Klicken Sie auf **Hinzufügen**.
4. Geben Sie einen Namen und eine Beschreibung für den neuen Endpunkt ein. Wählen Sie die Protokollierungsstufe aus, und legen Sie fest, ob Beispieldaten aktiviert sind. Weitere Informationen zur Protokollierung finden Sie unter [Aktivieren der Protokollierung für Machine Learning-Webdienste](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Aktualisieren des trainierten Modells des hinzugefügten Endpunkts
Um das erneute Training abzuschließen, müssen Sie das trainierte Modell des von Ihnen hinzugefügten neuen Endpunkts aktualisieren.

Wenn Sie den Endpunkt mithilfe des Beispielcodes hinzugefügt haben, ist der Speicherort der Hilfe-URL im Wert *HelpLocationURL* in der Ausgabe angegeben.

So rufen Sie die Pfad-URL ab

1. Kopieren Sie die URL, und fügen Sie sie in Ihren Browser ein.
2. Klicken Sie auf den Link „Ressource aktualisieren“.
3. Kopieren Sie die POST-URL der PATCH-Anforderung. Beispiel: 
   
     PATCH-URL: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

Sie können das trainierte Modell jetzt verwenden, um den zuvor erstellten Bewertungsendpunkt zu aktualisieren.

Der folgende Beispielcode zeigt, wie Sie *BaseLocation*, *RelativeLocation*, *SasBlobToken* und die PATCH-URL zum Aktualisieren des Endpunkts verwenden.

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
                    }
                }
            }
        };

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);

                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }

                // Do what you want with a successful response here.
            }
        }
    }

Der *apiKey* und die *endpointUrl* für den Aufruf werden im Dashboard des Endpunkts angezeigt.

Der Wert des Parameters *Name* in *Resources* muss mit dem Ressourcennamen des gespeicherten trainierten Modells im Vorhersageexperiment übereinstimmen. Gehen Sie wie folgt vor, um den Ressourcennamen abzurufen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie im linken Menü auf **Machine Learning**.
3. Klicken Sie unter „Name“ auf Ihren Arbeitsbereich und dann auf **Web Services**.
4. Klicken Sie unter „Name“ auf **Census Model [predictive exp.]**.
5. Klicken Sie auf den neuen Endpunkt, den Sie hinzugefügt haben.
6. Klicken Sie im Dashboard des Endpunkts auf **Ressource aktualisieren**.
7. Auf der Dokumentationsseite der API zur Ressourcenaktualisierung für den Webdienst wird der **Ressourcenname** unter **Updatable Resources** angezeigt.

Wenn Ihr SAS-Token abläuft, bevor Sie die Aktualisierung des Endpunkts abgeschlossen haben, müssen Sie einen GET-Befehl mit der Einzelvorgangs-ID ausführen, um ein neues Token abzurufen.

Nach der erfolgreichen Ausführung des Codes sollte der neue Endpunkt nach ungefähr 30 Sekunden mit der Verwendung des neuen trainierten Modells beginnen.

## <a name="summary"></a>Zusammenfassung
Mithilfe der APIs zum erneuten Trainieren können Sie das trainierte Modell eines Vorhersagewebdiensts aktualisieren und so z.B. folgende Szenarien ermöglichen:

* Regelmäßiges erneutes Trainieren des Modells mit neuen Daten
* Verteilung eines Modells an Kunden, sodass sie das Modell mit ihren eigenen Daten erneut trainieren können

## <a name="next-steps"></a>Nächste Schritte
[Problembehandlung für das erneute Trainieren eines klassischen Azure Machine Learning-Webdiensts](troubleshooting-retraining-models.md)

