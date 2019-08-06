---
title: 'Tutorial: Trainieren Ihres ersten Modells'
titleSuffix: Azure Machine Learning service
description: In diesem Tutorial werden die grundlegenden Entwurfsmuster in Azure Machine Learning Service vorgestellt, und es wird ein einfaches Scikit-learn-Modell auf der Grundlage des Diabetes-Datasets trainiert.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 07/20/2019
ms.openlocfilehash: 8b89f5572ae94986590fe76096daeb0ad088fe59
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668577"
---
# <a name="tutorial-train-your-first-model"></a>Tutorial: Trainieren des ersten Modells

Dieses Tutorial ist der **zweite Teil einer zweiteiligen Reihe**. Im vorherigen Tutorial haben Sie [einen Arbeitsbereich erstellt und eine Entwicklungsumgebung ausgewählt](tutorial-1st-experiment-sdk-setup.md). In diesem Tutorial werden die grundlegenden Entwurfsmuster in Azure Machine Learning Service vorgestellt, und es wird ein einfaches Scikit-learn-Modell auf der Grundlage des Diabetes-Datasets trainiert. Nach Abschluss dieses Tutorials verfügen Sie über die praktischen Kenntnisse für das SDK, die Sie für komplexere Experimente und Workflows benötigen.

In diesem Tutorial lernen Sie Folgendes:

> * Herstellen einer Verbindung für Ihren Arbeitsbereich und Erstellen eines Experiments
> * Laden von Daten und Trainieren von Scikit-learn-Modellen
> * Anzeigen der Trainingsergebnisse im Portal
> * Abrufen des besten Modells

## <a name="prerequisites"></a>Voraussetzungen

Die einzige Voraussetzung ist die Absolvierung des vorherigen Tutorials: [Einrichten der Umgebung und des Arbeitsbereichs](tutorial-1st-experiment-sdk-setup.md).

## <a name="connect-workspace-and-create-experiment"></a>Herstellen einer Verbindung für den Arbeitsbereich und Erstellen des Experiments

Importieren Sie die Klasse `Workspace`, und laden Sie Ihre Abonnementinformationen aus der Datei `config.json` mithilfe der Funktion `from_config().` Diese Funktion durchsucht standardmäßig das aktuelle Verzeichnis nach der JSON-Datei. Sie können jedoch auch einen Pfadparameter angeben, um auf die Datei zu verweisen: `from_config(path="your/file/path")`. Wenn Sie dieses Notebook auf einem cloudbasierten Notebook-Server in Ihrem Arbeitsbereich ausführen, befindet sich die Datei automatisch im Stammverzeichnis.

Falls durch den folgenden Code eine zusätzliche Authentifizierung angefordert wird, fügen Sie den Link einfach in einen Browser ein, und geben Sie das Authentifizierungstoken ein.


```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Erstellen Sie als Nächstes ein Experiment in Ihrem Arbeitsbereich. Bei einem Experiment handelt es sich um eine weitere grundlegende Cloudressource, die eine Sammlung von Tests (einzelne Modellausführungen) darstellt. In diesem Tutorial wird das Experiment verwendet, um Ausführungen zu erstellen und das Modelltraining im Azure-Portal nachzuverfolgen. Die Parameter umfassen einen Verweis auf Ihren Arbeitsbereich sowie einen Zeichenfolgennamen für das Experiment.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Laden von Daten und Vorbereiten des Trainings

In diesem Tutorial wird das Diabetes-Dataset verwendet – ein vornormalisiertes Dataset aus Scikit-learn. Dieses Dataset dient zur Vorhersage des Diabetesverlaufs auf der Grundlage von Merkmalen wie Alter, Geschlecht und BMI. Laden Sie die Daten über die statische Funktion `load_diabetes()`, und teilen Sie sie mithilfe von `train_test_split()` in Trainings- und Testsätze auf. Diese Funktion trennt die Daten, sodass das Modell über unbekannte Daten verfügt, die nach dem Trainieren zu Testzwecken verwenden werden können.


```python
from sklearn.datasets import load_diabetes
from sklearn.model_selection import train_test_split

X, y = load_diabetes(return_X_y = True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Trainieren eines Modells

Ein einfaches Scikit-learn-Modell mit geringem Trainingsaufwand kann problemlos lokal trainiert werden. Wenn Sie allerdings zahlreiche Iterationen mit vielen unterschiedlichen Merkmalspermutationen und Hyperparametereinstellungen trainieren, kann es leicht passieren, dass Sie den Überblick darüber verlieren, welche Modelle Sie wie trainiert haben. Das folgende Entwurfsmuster zeigt, wie Sie mithilfe des SDK ganz einfach den Überblick über Ihr Training in der Cloud behalten.

Erstellen Sie ein Skript, das Ridge-Modelle unter Verwendung verschiedener Hyperparameter-Alphawerte in einer Schleife trainiert.


```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.externals import joblib
import math

alphas = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]

for alpha in alphas:
    run = experiment.start_logging()
    run.log("alpha_value", alpha)

    model = Ridge(alpha=alpha)
    model.fit(X=X_train, y=y_train)
    y_pred = model.predict(X=X_test)
    rmse = math.sqrt(mean_squared_error(y_true=y_test, y_pred=y_pred))
    run.log("rmse", rmse)

    model_name = "model_alpha_" + str(alpha) + ".pkl"
    filename = "outputs/" + model_name

    joblib.dump(value=model, filename=filename)
    run.upload_file(name=model_name, path_or_stream=filename)
    run.complete()
```

Der obige Code bewirkt Folgendes:

1. Für jeden Hyperparameter-Alphawert im Array `alphas` wird im Experiment eine neue Ausführung erstellt. Der Alphawert wird protokolliert, um zwischen den einzelnen Ausführungen zu unterscheiden.
1. In jeder Ausführung wird ein Ridge-Modell instanziiert, trainiert und für Vorhersagen verwendet. Der Root Mean Squared Error (RMSE) wird für die tatsächlichen und die vorhergesagten Werte berechnet und anschließend für die Ausführung protokolliert. Der Ausführung sind nun Metadaten für Alphawert und RMSE-Genauigkeit zugeordnet.
1. Als Nächstes wird das Modell für die jeweilige Ausführung serialisiert und in die Ausführung hochgeladen. Dadurch können Sie die Modelldatei der Ausführung im Portal herunterladen.
1. Am Ende jeder Iteration wird die Ausführung durch Aufrufen von `run.complete()` abgeschlossen.

Rufen Sie nach Abschluss des Trainings die Variable `experiment` auf, um im Portal einen Link zu dem Experiment abzurufen.

```python
experiment
```

<table style="width:100%"><tr><th>Name</th><th>Arbeitsbereich</th><th>Berichtseite</th><th>Dokumentseite</th></tr><tr><td>diabetes-experiment</td><td><Name Ihres Arbeitsbereichs></td><td><a href="" target="_blank" rel="noopener">Link zum Azure-Portal</a></td><td><a href="" target="_blank" rel="noopener">Link zur Dokumentation</a></td></tr></table>

## <a name="view-training-results-in-portal"></a>Anzeigen der Trainingsergebnisse im Portal

Über den **Link zum Azure-Portal** gelangen Sie zur Hauptseite des Experiments. Dort werden alle einzelnen Ausführungen des Experiments angezeigt. Alle benutzerdefiniert protokollierten Werte (in diesem Fall `alpha_value` und `rmse`) werden zu Feldern für die jeweilige Ausführung und stehen für die Diagramme und Kacheln im oberen Bereich der Experimentseite zur Verfügung. Wenn Sie einem Diagramm oder einer Kachel eine protokollierte Metrik hinzufügen möchten, zeigen Sie darauf, klicken Sie auf die Bearbeitungsschaltfläche, und suchen Sie nach der benutzerdefiniert protokollierten Metrik.

Wenn Sie Modelle mit mehreren hundert oder tausend separaten Ausführungen trainieren, können Sie auf dieser Seite ganz einfach die einzelnen trainierten Modelle anzeigen und insbesondere nachvollziehen, wie sie trainiert wurden und wie sich Ihre eindeutigen Metriken im Laufe der Zeit verändert haben.

![Hauptseite des Experiments im Portal](./media/tutorial-quickstart/experiment-main.png)

Wenn Sie in der Spalte `RUN NUMBER` auf eine als Link dargestellte Ausführungsnummer klicken, gelangen Sie zur Seite der jeweiligen Ausführung. Die Standardregisterkarte **Details** enthält ausführlichere Informationen zur jeweiligen Ausführung. Auf der Registerkarte **Ausgaben** finden Sie die Datei `.pkl` für das Modell, das im Rahmen der jeweiligen Trainingsiteration in die Ausführung hochgeladen wurde. Hier können Sie die Modelldatei herunterladen, anstatt sie manuell neu trainieren zu müssen.

![Seite mit Ausführungsdetails im Portal](./media/tutorial-quickstart/model-download.png)

## <a name="get-the-best-model"></a>Abrufen des besten Modells

Neben der Möglichkeit, Modelldateien aus dem Experiment im Portal herunterzuladen, haben Sie auch die Möglichkeit, Modelldateien programmgesteuert herunterzuladen. Der folgende Code durchläuft die einzelnen Ausführungen des Experiments und greift sowohl auf die protokollierten Ausführungsmetriken als auch auf die Ausführungsdetails zu (in denen sich die Ausführungs-ID befindet). Dadurch wird die beste Ausführung ermittelt. Im vorliegenden Fall ist das die Ausführung mit dem niedrigsten Root Mean Squared Error.

```python
minimum_rmse_runid = None
minimum_rmse = None

for run in experiment.get_runs():
    run_metrics = run.get_metrics()
    run_details = run.get_details()
    # each logged metric becomes a key in this returned dict
    run_rmse = run_metrics["rmse"]
    run_id = run_details["runId"]

    if minimum_rmse is None:
        minimum_rmse = run_rmse
        minimum_rmse_runid = run_id
    else:
        if run_rmse < minimum_rmse:
            minimum_rmse = run_rmse
            minimum_rmse_runid = run_id

print("Best run_id: " + minimum_rmse_runid)
print("Best run_id rmse: " + str(minimum_rmse))
```

    Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
    Best run_id rmse: 57.234760283951765

Rufen Sie anhand der ID der besten Ausführung die entsprechende Ausführung ab. Verwenden Sie dazu den Konstruktor `Run` zusammen mit dem Experimentobjekt. Rufen Sie anschließend `get_file_names()` auf, um alle Dateien anzuzeigen, die für diese Ausführung zum Download zur Verfügung stehen. In diesem Fall wurde im Rahmen des Trainings für jede Ausführung nur eine einzelne Datei hochgeladen.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Rufen Sie `download()` für das Ausführungsobjekt auf, und geben Sie dabei den Namen der herunterzuladenden Modelldatei an. Diese Funktion verwendet standardmäßig das aktuelle Verzeichnis als Zielverzeichnis für den Download.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Überspringen Sie diesen Abschnitt, wenn Sie weitere Azure Machine Learning Service-Tutorials absolvieren möchten.

### <a name="stop-the-notebook-vm"></a>Beenden der Notebook-VM

Sollten Sie einen cloudbasierten Notebook-Server verwendet haben, beenden Sie aus Kostengründen den virtuellen Computer, wenn Sie ihn nicht verwenden.

1. Wählen Sie in Ihrem Arbeitsbereich **Notebook-VMs** aus.

   ![Beenden des VM-Servers](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Wählen Sie den virtuellen Computer in der Liste aus.

1. Wählen Sie **Stop** (Beenden) aus.

1. Wenn Sie bereit sind, den Server erneut zu verwenden, wählen Sie **Starten** aus.

### <a name="delete-everything"></a>Alles löschen

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Sie können die Ressourcengruppe auch behalten und einen einzelnen Arbeitsbereich löschen. Zeigen Sie die Eigenschaften des Arbeitsbereichs an, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie folgende Aufgaben ausgeführt:

> [!div class="checklist"]
> * Herstellen einer Verbindung für Ihren Arbeitsbereich und Erstellen eines Experiments
> * Laden von Daten und Trainieren von Scikit-learn-Modellen
> * Anzeigen von Trainingsergebnissen im Portal und Abrufen von Modellen

Als Nächstes können Sie [Ihr Modell mit Azure Machine Learning bereitstellen](tutorial-deploy-models-with-aml.md).
Machen Sie sich mit der Entwicklung von Experimenten mit [automatisiertem Machine Learning](tutorial-auto-train-models.md) vertraut.
