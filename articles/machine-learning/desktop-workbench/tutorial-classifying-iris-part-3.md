---
title: Tutorial zum Bereitstellen eines Modells für Azure Machine Learning-Dienste
description: In diesem umfassenden Tutorial wird die Verwendung von Azure Machine Learning-Diensten erläutert. Dies ist der dritte Teil, in dem das Bereitstellen des Modells beschrieben wird.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/13/2018
ms.openlocfilehash: 2270080f8612c69a69955202ececab44136f335c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445535"
---
# <a name="tutorial-3-classify-iris-deploy-a-model"></a>Tutorial 3: Klassifizieren von Iris: Bereitstellen eines Modells
Bei Azure Machine Learning (Vorschauversion) handelt es sich um eine integrierte Data Science- und Advanced Analytics-End-to-End-Lösung für professionelle Data Scientists. Data Scientists können die Lösung nutzen, um Daten vorzubereiten, Experimente zu entwickeln und Modelle für die Cloud bereitzustellen.

Dieses Tutorial ist der **dritte Teil einer dreiteiligen Reihe**. In diesem Tutorial verwenden Sie Machine Learning (Vorschauversion) für folgende Zwecke:

> [!div class="checklist"]
> * Ermitteln der Modelldatei
> * Generieren eines Bewertungsskripts und einer Schemadatei
> * Vorbereiten der Umgebung
> * Erstellen eines Echtzeit-Webdiensts
> * Ausführen des Echtzeit-Webdiensts
> * Untersuchen der Ausgabeblobdaten 

In diesem Tutorial wird das zeitlose Schwertlilien-Dataset ([Iris flower data set](https://en.wikipedia.org/wiki/Iris_flower_data_set)) verwendet. 

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:
- Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 
- Ein Experimentierkonto und eine Installation von Azure Machine Learning Workbench, wie in dieser [Schnellstartanleitung](../service/quickstart-installation.md) beschrieben.
- Das Klassifizierungsmodell aus dem [zweiten Teil des Tutorials](tutorial-classifying-iris-part-2.md).
- Eine lokal installierte und ausgeführte Docker-Engine.

## <a name="download-the-model-pickle-file"></a>Herunterladen der pickle-Modelldatei
Im vorherigen Teil des Tutorials wurde das Skript **iris_sklearn.py** lokal in Machine Learning Workbench ausgeführt. Mit dieser Aktion wurde das Modell für die logistische Regression über das beliebte Python-Paket [pickle](https://docs.python.org/3/library/pickle.html) für die Objektserialisierung serialisiert. 

1. Öffnen Sie die Machine Learning Workbench-Anwendung. Öffnen Sie anschließend das Projekt **myIris**, das Sie in den vorherigen Teilen der Tutorialreihe erstellt haben.

1. Wählen Sie nach dem Öffnen des Projekts im linken Bereich die Schaltfläche **Dateien** (Ordnersymbol), um die Dateiliste in Ihrem Projektordner zu öffnen.

1. Wählen Sie die Datei **iris_sklearn.py** aus. Der Python-Code wird in Workbench in einer neuen Registerkarte des Text-Editors geöffnet.

1. Sehen Sie in der Datei **iris_sklearn.py** nach, um zu ermitteln, wo die pickle-Datei generiert wurde. Drücken Sie STRG+F, um das Dialogfeld **Suchen** zu öffnen, und suchen Sie im Python-Code nach dem Wort **pickle**.

   In diesem Codeausschnitt ist dargestellt, wie die pickle-Ausgabedatei generiert wurde. Die pickle-Ausgabedatei hat auf dem Datenträger den Namen **model.pkl**. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

1. Suchen Sie in den Ausgabedateien eines vorherigen Ausführungslaufs nach der pickle-Modelldatei.
   
   Beim Ausführen des Skripts **iris_sklearn.py** wurde die Modelldatei unter dem Namen **model.pkl** in den Ordner **outputs** geschrieben. Dieser Ordner befindet sich in der Ausführungsumgebung, die Sie für die Ausführung des Skripts auswählen, und nicht in Ihrem lokalen Projektordner. 
   
   a. Wählen Sie zum Suchen nach der Datei im linken Bereich die Schaltfläche **Ausführungen** (Uhrsymbol), um die Liste **All Runs** (Alle Ausführungen) zu öffnen. 

   b. Die Registerkarte **All Runs** (Alle Ausführungen) wird geöffnet. Wählen Sie in der Tabelle mit den Ausführungen eine der letzten Ausführungen aus, für die das Ziel **local** und der Skriptname **iris_sklearn.py** lauten. 

   c. Der Bereich **Run Properties** (Ausführungseigenschaften) wird geöffnet. Oben rechts im Bereich befindet sich der Abschnitt **Ausgaben**.

   d. Aktivieren Sie zum Herunterladen der pickle-Datei das Kontrollkästchen neben der Datei **model.pkl**, und wählen Sie anschließend **Herunterladen**. Speichern Sie die Datei im Stammverzeichnis Ihres Projektordners. Die Datei wird in den nächsten Schritten benötigt.

   ![Herunterladen der pickle-Datei](media/tutorial-classifying-iris/download_model.png)

   Weitere Informationen zum Ordner `outputs` finden Sie unter [Lesen und Schreiben großer Datendateien](how-to-read-write-files.md).

## <a name="get-the-scoring-script-and-schema-files"></a>Abrufen des Bewertungsskripts und von Schemadateien
Zum Bereitstellen des Webdiensts zusammen mit der Modelldatei benötigen Sie auch ein Bewertungsskript. Optional benötigen Sie ein Schema für die Webdienst-Eingabedaten. Mit dem Bewertungsskript wird die Datei **model.pkl** aus dem aktuellen Ordner geladen und zum Erzeugen neuer Vorhersagen verwendet.

1. Öffnen Sie die Machine Learning Workbench-Anwendung. Öffnen Sie anschließend das Projekt **myIris**, das Sie im vorherigen Teil der Tutorialreihe erstellt haben.

1. Wählen Sie nach dem Öffnen des Projekts im linken Bereich die Schaltfläche **Dateien** (Ordnersymbol), um die Dateiliste in Ihrem Projektordner zu öffnen.

1. Wählen Sie die Datei **score_iris.py** aus. Das Python-Skript wird geöffnet. Diese Datei wird als Bewertungsdatei verwendet.

   ![Bewertungsdatei](media/tutorial-classifying-iris/model_data_collection.png)

1. Führen Sie das Skript aus, um die Schemadatei abzurufen. Wählen Sie in der Befehlsleiste die Umgebung **local** und das Skript **score_iris.py** und anschließend **Ausführen**. 

   Mit diesem Skript wird im Abschnitt **outputs** eine JSON-Datei erstellt, mit der das für das Modell benötigte Eingabedatenschema erfasst wird.

1. Beachten Sie den Bereich **Aufträge** rechts im Bereich **Projektdashboard**. Warten Sie, bis für den aktuellen Auftrag **score_iris.py** in Grün der Status **Abgeschlossen** angezeigt wird. Wählen Sie anschließend den Hyperlink **score_iris.py** für die letzte Auftragsausführung aus, um die Ausführungsdetails anzuzeigen. 

1. Wählen Sie im Bereich **Run Properties** (Ausführungseigenschaften) im Abschnitt **Ausgaben** die neu erstellte Datei **service_schema.json** aus. Aktivieren Sie das Kontrollkästchen neben dem Dateinamen, und wählen Sie anschließend **Herunterladen**. Speichern Sie die Datei im Stammverzeichnis Ihres Projekts.

1. Wechseln Sie zurück zur vorherigen Registerkarte, auf der Sie das Skript **score_iris.py** geöffnet haben. Mit der Datensammlung können Sie Modelleingaben und Vorhersagen für den Webdienst erfassen. Die folgenden Schritte sind für die Datensammlung von besonderem Interesse.

1. Sehen Sie sich den Code oben in der Datei an, mit dem die **ModelDataCollector**-Klasse importiert wird, da darin die Funktionalität für die Modelldatensammlung enthalten ist:

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

1. Die folgenden Codezeilen in der Funktion **init()** dienen zum Instanziieren von **ModelDataCollector**:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
    ```

1. Die folgenden Codezeilen in der Funktion **run(input_df)** dienen zum Sammeln der Eingabe- und Vorhersagedaten:

    ```python
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

Als Nächstes können Sie Ihre Umgebung für die Operationalisierung des Modells vorbereiten.

## <a name="prepare-to-operationalize-locally-for-development-and-testing-your-service"></a>Vorbereiten der lokalen Operationalisierung [Für Entwicklung und Tests Ihres Diensts]
Verwenden Sie für Docker-Container auf Ihrem lokalen Computer die Bereitstellung vom Typ _Lokaler Modus_.

Sie können _Lokaler Modus_ für Entwicklungs- und Testzwecke nutzen. Die Docker-Engine muss lokal ausgeführt werden, um die folgenden Schritte zum Operationalisieren des Modells auszuführen. Sie können das Flag `-h` am Ende jedes Befehls verwenden, um die entsprechende Hilfemeldung anzuzeigen.

>[!NOTE]
>Falls Sie nicht über eine lokale Docker-Engine verfügen, können Sie trotzdem fortfahren, indem Sie in Azure einen Cluster für die Bereitstellung erstellen. Sie können den Cluster beibehalten und weiterverwenden oder ihn nach Abschluss des Tutorials löschen, damit keine weiteren Gebühren anfallen.

>[!NOTE]
>Lokal bereitgestellte Webdienste werden im Azure-Portal nicht in der Liste mit den Diensten angezeigt. Sie werden in Docker auf dem lokalen Computer ausgeführt.

1. Öffnen Sie die Befehlszeilenschnittstelle (CLI).
   Wählen Sie in der Anwendung Machine Learning Workbench im Menü **Datei** die Option **Eingabeaufforderung öffnen**.

   Die Eingabeaufforderung wird geöffnet und zeigt den aktuellen Speicherort Ihres Projektordners an: **c:\temp\myIris>**.


1. Stellen Sie sicher, dass der Azure-Ressourcenanbieter **Microsoft.ContainerRegistry** unter Ihrem Abonnement registriert ist. Sie müssen diesen Ressourcenanbieter registrieren, um in Schritt 3 eine Umgebung erstellen zu können. Sie können überprüfen, ob die Registrierung bereits durchgeführt wurde, indem Sie den folgenden Befehl verwenden:
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   Es sollte in etwa folgende Ausgabe angezeigt werden: 
   ```
   Provider                                  Status 
   --------                                  ------
   Microsoft.Authorization                   Registered 
   Microsoft.ContainerRegistry               Registered 
   microsoft.insights                        Registered 
   Microsoft.MachineLearningExperimentation  Registered 
   ... 
   ```
   
   Wenn **Microsoft.ContainerRegistry** nicht registriert ist, können Sie dies mit dem folgenden Befehl durchführen:
   ``` 
   az provider register --namespace Microsoft.ContainerRegistry 
   ```
   Die Registrierung kann einige Minuten dauern. Sie können den Status überprüfen, indem Sie den obigen Befehl **az provider list** oder den folgenden Befehl verwenden:
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

   In der dritten Zeile der Ausgabe wird **"registrationState": "Registering"** angezeigt. Warten Sie kurz ab, und wiederholen Sie dann den Befehl **show**, bis in der Ausgabe **"registrationState": "Registered"** angezeigt wird.

   >[!NOTE] 
   Beim Bereitstellen in einem ACS-Cluster folgen Sie genau demselben Ansatz, um den Ressourcenanbieter **Microsoft.ContainerService** zu registrieren.

1. Erstellen Sie die Umgebung. Sie müssen diesen Schritt einmal pro Umgebung ausführen. Führen Sie ihn beispielsweise einmal für die Entwicklungsumgebung und einmal für die Produktionsumgebung aus. Verwenden Sie den _lokalen Modus_ für diese erste Umgebung. Sie können den Switch `-c` oder `--cluster` im folgenden Befehl später ausprobieren, um eine Umgebung im _Clustermodus_ einzurichten.

   Für den folgenden Setupbefehl ist der Zugriffstyp „Mitwirkender“ für das Abonnement erforderlich. Wenn Sie nicht über die erforderlichen Berechtigungen verfügen, benötigen Sie zumindest Zugriff als Mitwirkender auf die Ressourcengruppe, in der die Bereitstellung erfolgt. Im letzteren Fall müssen Sie den Ressourcengruppennamen mithilfe des Flags `-g` als Teil des Setupbefehls angeben. 

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Befolgen Sie die Anweisungen auf dem Bildschirm, um ein Speicherkonto zum Speichern von Docker-Images, eine Azure Container Registry zum Auflisten von Docker-Images und ein Azure Application Insights-Konto zum Erfassen von Telemetriedaten bereitzustellen. Wenn Sie den Switch `-c` verwenden, wird mit dem Befehl zusätzlich ein Container Service-Cluster erstellt.
   
   Der Clustername ist eine Möglichkeit, wie Sie die Umgebung identifizieren können. Der Standort sollte dem Standort des Kontos für die Modellverwaltung entsprechen, das Sie über das Azure-Portal erstellt haben.

   Überprüfen Sie den Status mithilfe des folgenden Befehls, um sicherzustellen, dass die Umgebung erfolgreich eingerichtet wurde:

   ```azurecli
   az ml env show -n <deployment environment name> -g <existing resource group name>
   ```

   Achten Sie darauf, dass „Bereitstellungsstatus“ wie hier gezeigt den Wert „Erfolgreich“ enthält, bevor Sie die Umgebung in Schritt 5 festlegen:

   ![Bereitstellungsstatus](media/tutorial-classifying-iris/provisioning_state.png)
 
1. Erstellen Sie jetzt ein Konto für die Modellverwaltung, falls Sie dies nicht bereits in den vorherigen Teilen dieses Tutorials durchgeführt haben. Dies ist eine einmalige Aufgabe.
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
1. Legen Sie das Modellverwaltungskonto fest.
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

1. Richten Sie die Umgebung ein.

   Verwenden Sie nach Abschluss der Einrichtung den folgenden Befehl, um die Umgebungsvariablen festzulegen, die zum Operationalisieren der Umgebung erforderlich sind. Verwenden Sie den gleichen Umgebungsnamen, den Sie zuvor in Schritt 3 verwendet haben. Verwenden Sie den gleichen Ressourcengruppennamen, der im Befehlsfenster ausgegeben wurde, nachdem der Setupprozess abgeschlossen war.

   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

1. Geben Sie den folgenden Befehl ein, um zu überprüfen, ob Sie Ihre operationalisierte Umgebung für die lokale Webdienstbereitstellung richtig konfiguriert haben:

   ```azurecli
   az ml env show
   ```

Sie können nun den Echtzeit-Webdienst erstellen.

>[!NOTE]
>Für nachfolgende Webdienstbereitstellungen können Sie das Konto und die Umgebung für die Modellverwaltung wiederverwenden. Sie müssen sie nicht für jeden Webdienst erstellen. Einem Konto oder einer Umgebung können mehrere Webdienste zugeordnet sein.

## <a name="create-a-real-time-web-service-in-one-command"></a>Erstellen eines Echtzeit-Webdiensts mit einem Befehl
1. Verwenden Sie den folgenden Befehl, um einen Echtzeit-Webdienst zu erstellen:

   ```azurecli
   az ml service create realtime -f score_iris.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true -c aml_config\conda_dependencies.yml
   ```
   Bei diesem Befehl wird eine Webdienst-ID zur späteren Verwendung generiert.

   Die folgenden Switches werden für den Befehl **az ml service create realtime** verwendet:

   * `-f`: Der Dateiname des Bewertungsskripts.

   * `--model-file`: Die Modelldatei. In diesem Fall ist dies die pickle-Datei „model.pkl“.

   * `-s`: Das Dienstschema. Es wurde im vorherigen Schritt generiert, indem das Skript **score_iris.py** lokal ausgeführt wurde.

   * `-n`: Der App-Name, der nur Kleinbuchstaben enthalten darf.

   * `-r`: Die Runtime des Modells. In diesem Fall ist es ein Python-Modell. Gültige Runtimes sind `python` und `spark-py`.

   * `--collect-model-data true`: Mit diesem Switch wird die Datensammlung aktiviert.

   * `-c`: Pfad zur Datei mit den Conda-Abhängigkeiten, in der zusätzliche Pakete angegeben werden

   >[!IMPORTANT]
   >Der Dienstname, bei dem es sich auch um den neuen Docker-Imagenamen handelt, darf nur Kleinbuchstaben enthalten. Andernfalls erhalten Sie eine Fehlermeldung. 

1. Wenn Sie den Befehl ausführen, werden das Modell und die Bewertungsdateien in das Speicherkonto hochgeladen, das Sie bei der Umgebungseinrichtung erstellt haben. Während des Bereitstellungsprozesses wird ein Docker-Image mit Ihrem Modell, dem Schema und der Bewertungsdatei erstellt und per Pushvorgang in die Azure Container Registry übertragen: **\<ACR-Name\>.azureacr.io/\<Imagename\>:\<Version\>**. 

   Der Befehl überträgt dieses Image lokal auf Ihren Computer und startet basierend auf diesem Image dann einen Docker-Container. Wenn Ihre Umgebung im Clustermodus konfiguriert ist, wird der Docker-Container stattdessen im Azure Cloud Services-Kubernetes-Cluster bereitgestellt.

   Im Rahmen der Bereitstellung wird auf Ihrem lokalen Computer ein HTTP-REST-Endpunkt für den Webdienst erstellt. Der Befehl sollte nach einigen Minuten mit einer Erfolgsmeldung abgeschlossen werden. Ihr Webdienst ist nun bereit für die Verwendung!

1. Verwenden Sie den Befehl **docker ps**, um den ausgeführten Docker-Container anzuzeigen:

   ```azurecli
   docker ps
   ```

## <a name="optional-alternative-create-a-real-time-web-service-by-using-separate-commands"></a>[Optionale Alternative] Erstellen eines Webdiensts in Echtzeit mithilfe von separaten Befehlen
Alternativ zum obigen Befehl **az ml service create realtime** können Sie die Schritte auch separat ausführen. 

Registrieren Sie zuerst das Modell. Generieren Sie anschließend das Manifest, erstellen Sie das Docker-Image, und erstellen Sie den Webdienst. Bei diesem Schritt-für-Schritt-Ansatz können Sie bei jedem Schritt flexibler vorgehen. Außerdem können Sie die Entitäten wiederverwenden, die in den vorherigen Schritten generiert wurden, und müssen die Entitäten nur bei Bedarf neu erstellen.

1. Registrieren Sie das Modell, indem Sie den Namen der pickle-Datei angeben.

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   Mit diesem Befehl wird eine Modell-ID generiert.

1. Erstellen Sie ein Manifest.

   Verwenden Sie zum Erstellen eines Manifests den folgenden Befehl, und geben Sie die Modell-ID aus dem vorherigen Schritt an:

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json -c aml_config\conda_dependencies.yml
   ```
   Mit diesem Befehl wird eine Manifest-ID generiert.

1. Erstellen Sie ein Docker-Image.

   Verwenden Sie zum Erstellen eines Docker-Images den folgenden Befehl, und geben Sie den Manifest-ID-Wert aus dem vorherigen Schritt an. Sie können die Conda-Abhängigkeiten optional auch mit dem Switch `-c` einbinden.

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID> 
   ```
   Mit diesem Befehl wird eine Docker-Image-ID generiert.
   
1. Erstellen Sie den Dienst.

   Verwenden Sie zum Erstellen eines Diensts den folgenden Befehl, und geben Sie die Image-ID aus dem vorherigen Schritt an:

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   Mit diesem Befehl wird eine Webdienst-ID generiert.

Sie können den Webdienst jetzt ausführen.

## <a name="run-the-real-time-web-service"></a>Ausführen des Echtzeit-Webdiensts

Verwenden Sie einen JSON-codierten Datensatz, der ein Array mit vier Zufallszahlen enthält, um den ausgeführten Webdienst **irisapp** zu testen.

1. Der Webdienst enthält Beispieldaten. Bei Ausführung im lokalen Modus können Sie den Befehl **az ml service usage realtime** aufrufen. Mit diesem Aufruf wird ein Beispielbefehl für die Ausführung aufgerufen, den Sie zum Testen des Diensts verwenden können. Außerdem wird mit diesem Aufruf die Bewertungs-URL abgerufen, mit der Sie den Dienst in Ihre eigene benutzerdefinierte App einbinden können.

   ```azurecli
   az ml service usage realtime -i <web service ID>
   ```

1. Führen Sie den zurückgegebenen Befehl zum Ausführen des Diensts aus, um den Dienst zu testen:
    
   ```azurecli
   az ml service run realtime -i <web service ID> -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```

   Die Ausgabe lautet **"Iris-setosa"**. Dies ist die vorhergesagte Klasse. (Sie erhalten unter Umständen ein anderes Ergebnis.) 

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Anzeigen der gesammelten Daten in Azure Blob Storage

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie nach Ihren Speicherkonten. Wählen Sie hierzu **Alle Dienste**.

1. Geben Sie im Suchfeld **Speicherkonten** ein, und drücken Sie anschließend die EINGABETASTE.

1. Wählen Sie im Suchfeld **Speicherkonten** die Ressource **Speicherkonto** für Ihre Umgebung aus. 

   > [!TIP]
   > Ermitteln Sie wie folgt, welches Speicherkonto verwendet wird:
   > 1. Öffnen Sie Machine Learning Workbench.
   > 1. Wählen Sie das Projekt aus, an dem Sie arbeiten.
   > 1. Öffnen Sie im Menü **Datei** eine Eingabeaufforderung.
   > 1. Geben Sie an der Eingabeaufforderung `az ml env show -v` ein, und überprüfen Sie den Wert von *storage_account*. Dies ist der Name Ihres Speicherkontos.

1. Wählen Sie nach dem Öffnen des Bereichs **Speicherkonto** im Abschnitt **Dienste** die Option **Blobs**. Suchen Sie nach dem Container mit dem Namen **modeldata**. 
 
   Falls keine Daten angezeigt werden, müssen Sie nach der ersten Webdienstanforderung unter Umständen bis zu zehn Minuten warten, bis die Daten in das Speicherkonto übertragen wurden und angezeigt werden.

   Die Daten fließen in Blobs mit dem folgenden Containerpfad:

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

1. Sie können diese Daten über Azure Blob Storage nutzen. Hierfür gibt es viele verschiedene Tools (Microsoft-Software und Open-Source-Tools). Beispiele:

   * Machine Learning: Öffnen Sie die CSV-Datei, indem Sie die CSV-Datei als Datenquelle hinzufügen.

   * Excel: Öffnen Sie die täglichen CSV-Dateien als Tabelle.

   * [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): Erstellen Sie Diagramme, für die Daten aus den CSV-Daten in Blobs genutzt werden.

   * [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): Laden Sie die CSV-Daten in eine Hive-Tabelle, und führen Sie SQL-Abfragen direkt für die Blobs durch.

   * [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): Erstellen Sie einen Datenrahmen mit einem hohen Anteil von CSV-Daten.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte
In diesem dritten Teil der dreiteiligen Tutorialreihe wurde beschrieben, wie Sie Machine Learning für folgende Zwecke verwenden:
> [!div class="checklist"]
> * Ermitteln der Modelldatei
> * Generieren eines Bewertungsskripts und einer Schemadatei
> * Vorbereiten der Umgebung
> * Erstellen eines Echtzeit-Webdiensts
> * Ausführen des Echtzeit-Webdiensts
> * Untersuchen der Ausgabeblobdaten 

Sie haben ein Trainingsskript erfolgreich in verschiedenen Computeumgebungen ausgeführt. Außerdem haben Sie ein Modell über einen Docker-basierten Webdienst erstellt, serialisiert und operationalisiert. 

Nun können Sie die erweiterte Datenvorbereitung durchführen:
> [!div class="nextstepaction"]
> [Tutorial 4: Erweiterte Datenvorbereitung](tutorial-bikeshare-dataprep.md)
