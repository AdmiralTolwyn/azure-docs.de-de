---
title: Zugreifen auf Daten in Azure Storage-Diensten
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie mithilfe von Datenspeichern während des Trainings mit Azure Machine Learning auf Azure Storage-Dienste zugreifen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 94cdf683bc8524786e1f32607ef18f976990ba07
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979120"
---
# <a name="access-data-in-azure-storage-services"></a>Zugreifen auf Daten in Azure Storage-Diensten
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In diesem Artikel erfahren Sie, wie Sie auf einfache Weise über Azure Machine Learning-Datenspeicher auf Ihre Daten in Azure Storage-Diensten zugreifen können. Datenspeicher werden zum Speichern von Verbindungsinformationen wie z. B. Ihrer Abonnement-ID und Tokenautorisierung verwendet. Mithilfe von Datenspeichern können Sie auf Ihren Speicher zugreifen, ohne die Verbindungsinformationen in Ihren Skripts hartcodieren zu müssen. Aus diesen [Azure Storage-Lösungen](#matrix) können Sie Datenspeicher erstellen. Für nicht unterstützte Speicherlösungen sowie um bei Machine Learning-Experimenten Kosten für ausgehende Daten zu sparen, empfiehlt es sich, Ihre Daten in unsere unterstützten Azure Storage-Lösungen zu verschieben. [Weitere Informationen zum Verschieben von Daten](#move). 

In dieser Vorgehensweise finden Sie Beispiele für die folgenden Aufgaben:
* Registrieren von Datenspeichern
* Abrufen von Datenspeichern aus Arbeitsbereichen
* Hochladen und Herunterladen von Daten mithilfe von Datenspeichern
* Zugreifen auf Daten während des Trainings
* Verschieben von Daten in einen Azure Storage-Dienst

## <a name="prerequisites"></a>Voraussetzungen
Sie benötigen
- Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) noch heute aus.

- Ein Azure-Speicherkonto mit einem [Azure-Blobcontainer](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) oder einer [Azure-Dateifreigabe](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- Das [Azure Machine Learning SDK für Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), oder greifen Sie auf [Azure Machine Learning-Studio](https://ml.azure.com/) zu.

- Ein Azure Machine Learning-Arbeitsbereich. 
    - Sie können wahlweise [einen Azure Machine Learning-Arbeitsbereich erstellen](how-to-manage-workspace.md) oder mithilfe des Python SDKs einen vorhandenen verwenden.

        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore
        
        ws = Workspace.from_config()
        ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Erstellen und Registrieren von Datenspeichern

Wenn Sie eine Azure Storage-Lösung als Datenspeicher registrieren, erstellen Sie diesen Datenspeicher automatisch in einem bestimmten Arbeitsbereich. Sie können Datenspeicher über das Python SDK oder über Azure Machine Learning-Studio in einem Arbeitsbereich registrieren.

### <a name="using-the-python-sdk"></a>Verwenden des Python SDK

Alle Registriermethoden befinden sich in der [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)-Klasse und weisen das Format „register_azure_*“ auf.

Die Informationen, die Sie zum Auffüllen der register()-Methode benötigen, finden Sie in [Azure Machine Learning Studio](https://ml.azure.com) und den folgenden Schritten.

1. Wählen Sie im linken Bereich **Speicherkonten** und dann das Speicherkonto aus, das Sie registrieren möchten. 
2. Die Seite **Übersicht** enthält Informationen wie den Kontonamen und den Namen des Containers oder der Dateifreigabe. 
3. Zum Abrufen von Authentifizierungsinformationen, wie dem Kontoschlüssel oder dem SAS-Token, navigieren Sie im Bereich **Einstellungen** auf der linken Seite zu **Kontoschlüssel**. 

>[WICHTIG] Wenn sich Ihr Speicherkonto in einem VNET befindet, wird nur die Erstellung von Azure-Blobdatenspeichern unterstützt. Legen Sie den-Parameter `grant_workspace_access` auf `True` fest, um für Ihren Arbeitsbereich Zugriff auf Ihr Speicherkonto zu gewähren.

In den folgenden Beispielen wird gezeigt, wie Sie einen Azure-Blobcontainer oder eine Azure-Dateifreigabe als Datenspeicher registrieren können.

+ Verwenden Sie [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) für einen **Azure-Blobcontainer-Datenspeicher**.

    Der folgende Code erstellt den Datenspeicher `my_datastore` und registriert ihn im Arbeitsbereich `ws`. Dieser Datenspeicher greift auf den Azure-Blobcontainer`my_blob_container` im Azure-Speicherkonto `my_storage_account` zu und verwendet dazu den angegebenen Kontoschlüssel.

    ```Python
       datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                          datastore_name='my_datastore', 
                                                          container_name='my_blob_container',
                                                          account_name='my_storage_account', 
                                                          account_key='your storage account key',
                                                          create_if_not_exists=True)
    ```

+ Verwenden Sie [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) für einen **Azure-Dateifreigabe-Datenspeicher**. 

    Der folgende Code erstellt den Datenspeicher `my_datastore` und registriert ihn im Arbeitsbereich `ws`. Dieser Datenspeicher greift auf die Azure-Dateifreigabe`my_file_share` im Azure-Speicherkonto `my_storage_account` zu und verwendet dazu den angegebenen Kontoschlüssel.

    ```Python
       datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                      datastore_name='my_datastore', 
                                                      file_share_name='my_file_share',
                                                      account_name='my_storage account', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
    ```

####  <a name="storage-guidance"></a>Leitfaden für Speicher

Wir empfehlen Azure-Blob-Container. Für Blobs stehen sowohl der Standard- als auch der Premiumspeicher zur Verfügung. Der Premiumspeicher ist zwar teurer, aber dennoch die bevorzugte Option, da er höhere Durchsätze ermöglicht. Dies kann sich vor allem bei einem Training mit einem großen Dataset positiv auf die Ausführungsgeschwindigkeit auswirken. Weitere Informationen zu den Kosten von Speicherkonten stellt der [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) bereit.

### <a name="using-azure-machine-learning-studio"></a>Arbeiten mit Azure Machine Learning-Studio 

Erstellen Sie einen neuen Datenspeicher in wenigen Schritten in Azure Machine Learning-Studio.

1. Melden Sie sich bei [Azure Machine Learning-Studio](https://ml.azure.com/)an.
1. Wählen Sie im linken Bereich unter **Verwalten** **Datenspeicher** aus.
1. Wählen Sie **+ Neuer Datenspeicher** aus.
1. Füllen Sie das Formular „Neuer Datenspeicher“ aus. Das Formular wird ausgehend von den gewählten Optionen für den Azure-Speichertyp und den Authentifizierungstyp intelligent aktualisiert.
  
Die Informationen, die Sie zum Ausfüllen des Formulars benötigen, lassen sich über das [Azure-Portal](https://portal.azure.com) ermitteln. Wählen Sie im linken Bereich **Speicherkonten** und dann das Speicherkonto aus, das Sie registrieren möchten. Die Seite **Übersicht** enthält Informationen wie den Kontonamen und den Namen des Containers oder der Dateifreigabe. Navigieren Sie zum Abrufen von Authentifizierungselementen wie Kontoschlüssel oder SAS-Token im Bereich **Einstellungen** auf der linken Seite zu **Kontoschlüssel**.

Das folgende Beispiel veranschaulicht das Aussehen des Formulars, wenn ein Azure-Blobdatenspeicher erstellt wird. 
    
 ![Neuer Datenspeicher](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Abrufen von Datenspeichern aus Ihrem Arbeitsbereich

Verwenden Sie die statische Methode [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) mit der Datastore-Klasse, um einen bestimmten Datenspeicher im aktuellen Arbeitsbereich zu registrieren:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Um die Liste der in einem bestimmten Arbeitsbereich registrierten Datenspeicher abzurufen, können Sie die [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores)-Eigenschaft für ein Arbeitsbereichsobjekt verwenden:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Wenn Sie einen Arbeitsbereich erstellen, werden ein Azure-Blob-Container und eine Azure-Dateifreigabe automatisch in dem Arbeitsbereich namens `workspaceblobstore` bzw. `workspacefilestore` registriert. Darin werden die Verbindungsinformationen des Blobcontainers und der Dateifreigabe, die im Speicherkonto bereitgestellt wird, das mit dem Arbeitsbereich verbunden ist, gespeichert. `workspaceblobstore` wird als Standarddatenspeicher festgelegt.

So rufen Sie den Standarddatenspeicher des Arbeitsbereichs ab:

```Python
datastore = ws.get_default_datastore()
```

Verwenden Sie die Methode [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) mit dem Arbeitsbereichsobjekt, um einen anderen Standarddatenspeicher für den aktuellen Arbeitsbereich zu definieren:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Hoch- und herunterladen von Daten
Die in den folgenden Beispielen beschriebenen [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-)- und [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)-Methoden sind für die [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)- und [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)-Klassen bestimmt und werden identisch ausgeführt.

### <a name="upload"></a>Hochladen

 Laden Sie entweder ein Verzeichnis oder einzelne Dateien mit dem Python SDK in den Datenspeicher hoch.

So laden Sie ein Verzeichnis in einen Datenspeicher `datastore` hoch:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Der Parameter `target_path` gibt den Speicherort in der Dateifreigabe (oder im Blobcontainer) für den Upload an. Standardmäßig lautet er `None`. In diesem Fall werden die Daten in den Stamm hochgeladen. Andernfalls werden bei `overwrite=True` alle in `target_path` vorhandenen Daten überschrieben.

Alternativ können Sie eine Liste einzelner Dateien mithilfe der `upload_files()`-Methode in den Datenspeicher hochladen.

### <a name="download"></a>Download

Ebenso lassen sich Daten aus einem Datenspeicher in Ihr lokales Dateisystem herunterladen.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Der Parameter `target_path` ist der Speicherort des lokalen Verzeichnisses, in das die Daten heruntergeladen werden sollen. Um einen Pfad zu dem Ordner in der Dateifreigabe (oder im Blobcontainer) für den Download anzugeben, geben Sie diesen Pfad mit `prefix` an. Wenn `prefix` gleich `None` ist, werden alle Inhalte Ihrer Dateifreigabe (oder Ihres Blobcontainers) heruntergeladen.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Zugreifen auf Ihre Daten während des Trainings

> [!IMPORTANT]
> Die Verwendung von [Azure Machine Learning-Datasets](how-to-create-register-datasets.md) ist die neue empfohlene Vorgehensweise für den Zugriff auf Ihre Daten im Training. Datasets bieten Funktionen, die tabellarische Daten in Pandas- oder Spark-Datenrahmen laden, sowie die Möglichkeit zum Herunterladen oder Einbinden von Dateien in beliebigen Formaten aus Azure-Blobs, Azure Files, Azure Data Lake Gen 1, Azure Data Lake Gen 2, Azure SQL und Azure PostgreSQL. Erfahren Sie mehr über das [Trainieren mit Datasets](how-to-train-with-datasets.md).

Die folgende Tabelle listet die Methoden auf, die dem Computeziel mitteilen, wie der Datenspeicher während der Ausführungen verwendet wird. 

Weg|Methode|BESCHREIBUNG|
----|-----|--------
Einbinden| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Wird zum Einbinden des Datenspeichers auf dem Computeziel verwendet. Wenn sie eingebunden sind, werden alle Dateien Ihres Datenspeicher für Ihr Computeziel zugänglich gemacht.
Download|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Wird zum Herunterladen der Inhalte Ihres Datenspeichers in den unter `path_on_compute` angegebenen Speicherort verwendet. <br><br> Dieser Download findet vor der Ausführung statt.
Hochladen|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Wird zum Hochladen einer Datei aus dem von `path_on_compute` angegebenen Speicherort in Ihren Datenspeicher verwendet. <br><br> Dieser Upload findet nach der Ausführung statt.

Um auf bestimmte Ordner oder Dateien in Ihrem Datenspeicher zu verweisen und sie im Computeziel verfügbar zu machen, verwenden Sie die Datenspeichermethode [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-).

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of only the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Jedes angegebene `datastore`- oder `datastore.path`-Objekt wird in den Namen einer Umgebungsvariable des Formats `"$AZUREML_DATAREFERENCE_XXXX"` aufgelöst, deren Wert für den Einbindungs-/Downloadpfad im Computeziel steht. Der Datenspeicherpfad auf dem Zielcompute stimmt möglicherweise nicht mit dem Ausführungspfad für das Trainingsskript überein.

### <a name="examples"></a>Beispiele 

Wir empfehlen die Verwendung der [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)-Klasse für den Zugriff auf Daten während des Trainings. 

Die Variable `script_params` ist ein Wörterbuch, das Parameter für entry_script enthält. Verwenden Sie es, um einen Datenspeicher als Eingabe zu übergeben und zu beschreiben, wie Daten auf dem Computeziel verfügbar gemacht werden. Weitere Informationen finden Sie in unserem End-to-End-[Tutorial](tutorial-train-models-with-aml.md).

```Python
from azureml.train.estimator import Estimator

# notice '/' is in front, this indicates the absolute path
script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Sie können auch eine Liste von Datenspeichern an den Parameter `inputs` des Estimatorkonstruktors übergeben, um Daten in oder aus Ihrem/n Datenspeicher(n) einzubinden oder zu kopieren. Dieses Codebeispiel:
* Lädt alle Inhalte in `datastore1` auf das Computeziel herunter, bevor das Trainingsskript `train.py` ausgeführt wird.
* Lädt den Ordner `'./foo'` in `datastore2` auf das Computeziel herunter, bevor `train.py` ausgeführt wird.
* Lädt die Datei `'./bar.pkl'` aus dem Computeziel in den `datastore3` hoch, nachdem Ihr Skript ausgeführt wurde.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
Wenn Sie lieber ein RunConfig-Objekt für das Training verwenden möchten, müssen Sie ein [DataReference](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)-Objekt einrichten. 

Der folgende Code zeigt, wie Sie mit einem DataReference-Objekt in einer Schätzpipeline arbeiten. Ein vollständiges Beispiel finden Sie in diesem [Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb).

```Python
from azureml.core import Datastore
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

def_blob_store = Datastore(ws, "workspaceblobstore")

input_data = DataReference(
       datastore=def_blob_store,
       data_reference_name="input_data",
       path_on_datastore="20newsgroups/20news.pkl")

   output = PipelineData("output", datastore=def_blob_store)
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Compute- und Datenspeichermatrix

Datenspeicher unterstützen derzeit das Speichern von Verbindungsinformationen in den Speicherdiensten, die in der folgenden Matrix aufgeführt sind. Diese Matrix zeigt die verfügbaren Datenzugriffsfunktionen für die verschiedenen Computeziele- und Datenspeicherszenarien. Weitere Informationen zu den [Computezielen für Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Compute|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Lokal|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|–         |–                                                                         |
| Azure Machine Learning Compute |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;Pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;Pipelines](concept-ml-pipelines.md)|–         |–                                                                         |
| Virtuelle Computer               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |–         |–                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |–         |–                                                                         |
| Datenübertragung                  |[ML&nbsp;Pipelines](concept-ml-pipelines.md)                                               |–                                           |[ML&nbsp;Pipelines](concept-ml-pipelines.md)            |[ML&nbsp;Pipelines](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;Pipelines](concept-ml-pipelines.md)                                              |–                                           |[ML&nbsp;Pipelines](concept-ml-pipelines.md)             |–                                                                         |
| Azure Batch                    |[ML&nbsp;Pipelines](concept-ml-pipelines.md)                                               |–                                           |–         |–                                                                         |
| Azure DataLake Analytics       |–                                           |–                                           |[ML&nbsp;Pipelines](concept-ml-pipelines.md)             |–                                                                         |

> [!NOTE]
> Es gibt möglicherweise Szenarios, in denen umfangreiche Datenverarbeitungsprozesse mit vielen Wiederholungen schneller mit `as_download()` anstelle von `as_mount()` ausgeführt werden. Dies können Sie in Experimenten überprüfen.

### <a name="accessing-source-code-during-training"></a>Zugreifen auf den Quellcode während des Trainings

Azure Blob Storage verfügt über eine höhere Durchsatzgeschwindigkeit als die Azure-Dateifreigabe und wird auf eine hohe Anzahl parallel gestarteter Aufträge skaliert. Aus diesem Grund empfiehlt es sich, Ausführungen so zu konfigurieren, dass für die Übertragung von Quellcodedateien Blobspeicher verwendet wird.

Im folgenden Codebeispiel wird in der Laufzeitkonfiguration angegeben, welcher Blobdatenspeicher für Quellcodeübertragungen verwendet werden soll.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Zugreifen auf Daten während der Bewertung

Azure Machine Learning bietet mehrere Möglichkeiten, Ihre Modelle zur Bewertung zu verwenden. Einige dieser Methoden bieten keinen Zugriff auf Datenspeicher. Verwenden Sie die folgende Tabelle, um zu verstehen, welche Methoden Ihnen ermöglichen, während der Bewertung auf Datenspeicher zuzugreifen:

| Methode | Datenspeicherzugriff | BESCHREIBUNG |
| ----- | :-----: | ----- |
| [Batchvorhersage](how-to-run-batch-predictions.md) | ✔ | Treffen Sie asynchron Vorhersagen für große Datenmengen. |
| [Webdienst](how-to-deploy-and-where.md) | &nbsp; | Stellen Sie ein Modell bzw. Modelle als Webdienst bereit. |
| [IoT Edge-Modul](how-to-deploy-and-where.md) | &nbsp; | Stellen Sie ein Modell bzw. Modelle auf IoT Edge-Geräten bereit. |

Für Situationen, in denen das SDK keinen Zugriff auf Datenspeicher bietet, können Sie möglicherweise benutzerdefinierten Code mit dem entsprechenden Azure-SDK erstellen, um auf die Daten zuzugreifen. Das [Azure Storage SDK für Python](https://github.com/Azure/azure-storage-python) ist beispielsweise eine Clientbibliothek, mit der Sie auf in Blobs oder Dateien gespeicherte Daten zugreifen können.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Verschieben von Daten in unterstützte Azure Storage-Lösungen

Azure Machine Learning unterstützt den Zugriff auf Daten aus Azure BLOB, Azure File, Azure Data Lake Gen 1, Azure Data Lake Gen 2, Azure SQL und Azure PostgreSQL. Für nicht unterstützte Speicherlösungen empfiehlt es sich, Ihre Daten mithilfe von Azure Data Factory in unsere unterstützten Azure Storage-Lösungen zu verschieben, um bei Machine Learning-Experimenten Kosten für ausgehende Daten  zu sparen. Azure Data Factory bietet effiziente und robuste Datenübertragung mit über 80 vordefinierten Connectors (z.B. Azure-Datendienste, lokale Datenquellen, Amazon S3 und Redshift sowie Google BigQuery) ohne zusätzliche Kosten. [Befolgen Sie die Schrittanleitung, um Ihre Daten mithilfe von Azure Data Factory zu verschieben](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Nächste Schritte

* [Trainieren eines Modells](how-to-train-ml-models.md).

* [Bereitstellen eines Modells](how-to-deploy-and-where.md)
