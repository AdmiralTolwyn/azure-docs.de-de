---
title: Visualisieren von Experimenten mit TensorBoard
titleSuffix: Azure Machine Learning service
description: Starten Sie TensorBoard, um die Historie der Experimentläufe zu visualisieren und potenzielle Bereiche für Hyperparameter-Tuning und -Training zu identifizieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: maxluk
ms.author: maxluk
ms.date: 06/28/2019
ms.openlocfilehash: f65882cb851f8e35bb1d6c319d52fcfadb36ae91
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2019
ms.locfileid: "68772715"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Visualisieren Sie Experimentabläufe und Metriken mit TensorBoard und Azure Machine Learning

In diesem Artikel erfahren Sie, wie Sie Ihre Experimentläufe und Metriken in TensorBoard mit [dem `tensorboard`Paket](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) im Azure Machine Learning Service SDK anzeigen können. Sobald Sie Ihre Experimentabläufe überprüft haben, können Sie Ihre Modelle für das maschinelle Lernen besser abstimmen und trainieren.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) ist eine Sammlung von Webanwendungen zur Überprüfung und zum Verständnis Ihrer Experimentstruktur und -leistung.

Wie Sie TensorBoard mit Azure Machine Learning Experimenten starten, hängt von der Art des Experiments ab:
+ Wenn Ihr Experiment nativ Protokolldateien ausgibt, die von TensorBoard verarbeitet werden können, wie z.B. PyTorch-, Chainer- und TensorFlow-Experimente, dann können Sie [TensorBoard direkt aus dem Laufverlauf des Experiments starten](#direct). 

+ Für Experimente, die keine TensorBoard-Verbrauchsdateien ausgeben, wie z.B. Scikit-Learn oder Azure Machine Learning Experimente, verwenden Sie [die`export_to_tensorboard()` Methode](#export), um die Verläufe als TensorBoard-Protokolle zu exportieren und TensorBoard von dort aus zu starten. 

## <a name="prerequisites"></a>Voraussetzungen

* Um TensorBoard zu starten und Ihre Experimentablaufhistorie anzuzeigen, müssen Ihre Experimente zuvor die Protokollierung aktiviert haben, um ihre Metriken und Leistungen zu verfolgen.  

* Der Code in dieser Anleitung kann in einer der folgenden Umgebungen ausgeführt werden: 

    * Azure Machine Learning Notebook VM: keine Downloads oder Installationen erforderlich

        * Absolvieren Sie [Tutorial: Einrichten von Umgebung und Arbeitsbereich](tutorial-1st-experiment-sdk-setup.md), um einen dedizierten Notebookserver zu erstellen, auf dem das SDK und Beispielrepository vorinstalliert sind.

        * Suchen Sie im Ordner Samples auf dem Notebook-Server zwei fertige und erweiterte Notebooks, indem Sie zu diesem Verzeichnis navigieren: **how-to-use azureml > training-with-deep-learning**.
        * export-run-history-to-run-history.ipynb
        * tensorboard.ipynb

    * Ihr eigener Jupyter Notebook-Server
      * Verwenden Sie den Artikel [Create a workspace article](setup-create-workspace.md), um
          * [Installieren Sie das Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) mit dem `tensorboard` Zusatz
          * Erstellen eines Arbeitsbereichs und seiner Konfigurationsdatei (config.json)
  
<a name="direct"></a>
## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Option 1: Direkte Anzeige des Verlaufs in TensorBoard

Diese Option eignet sich für Experimente, die nativ Protokolldateien ausgeben, die von TensorBoard verwendet werden, wie z.B. PyTorch, Chainer und TensorFlow-Experimente. Wenn dies bei Ihrem Experiment nicht der Fall ist, verwenden Sie stattdessen[ die `export_to_tensorboard()`Methode](#export).

Der folgende Beispielcode verwendet das [MNIST-Demo-Experiment](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) aus dem Repository von TensorFlow in einem Remote-Compute-Ziel, Azure Machine Learning Compute. Als nächstes trainieren wir unser Modell mit dem benutzerdefinierten [TensorFlow-Schätzer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) des SDK und testen dann TensorBoard gegen dieses TensorFlow-Experiment, d.h. ein Experiment, das nativ TensorBoard-Ereignisdateien ausgibt.

### <a name="set-experiment-name-and-create-project-folder"></a>Festlegen des Experimentnamens und Erstellen des Projektordners

Hier benennen wir das Experiment und erstellen seinen Ordner. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>TensorFlow Experiment Democode herunterladen

Das Repository von TensorFlow verfügt über eine MNIST-Demo mit umfangreicher TensorBoard-Instrumentierung. Wir ändern keinen Code dieser Demo, damit sie mit dem Azure Machine Learning Service funktioniert. Im folgenden Code laden wir den MNIST-Code herunter und speichern ihn in unserem neu erstellten Experimentierordner.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
Beachten Sie in der MNIST-Code-Datei mnist_with_summaries.py, dass es Zeilen gibt, die `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()` etc. aufrufen. Diese Methoden gruppieren, protokollieren und markieren Schlüsselmetriken Ihrer Experimente im Verlauf. Die `tf.summary.FileWriter()` ist besonders wichtig, da es die Daten aus Ihren protokollierten Experimentmetriken serialisiert, was es TensorBoard ermöglicht, aus ihnen Visualisierungen zu generieren.

 ### <a name="configure-experiment"></a>Konfigurieren des Experiments

Im Folgenden konfigurieren wir unser Experiment und richten Verzeichnisse für Protokolle und Daten ein. Diese Protokolle werden in den Artifact Service hochgeladen, auf den TensorBoard später zugreifen wird.

>[!Note]
> Für dieses TensorFlow-Beispiel müssen Sie TensorFlow auf Ihrem lokalen Rechner installieren. Außerdem muss das TensorBoard-Modul (d.h. das in TensorFlow enthaltene) für den Kernel dieses Notebooks zugänglich sein, da auf der lokalen Maschine TensorBoard läuft.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Erstellen Sie einen Cluster für Ihr Experiment
Wir erstellen einen AmlCompute-Cluster für dieses Experiment, aber Ihre Experimente können in jeder Umgebung erstellt werden und Sie können TensorBoard trotzdem anhand der Experimentverlauf starten. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>Mit TensorFlow-Schätzer durchführen senden

Der TensorFlow-Schätzer bietet eine einfache Möglichkeit, einen TensorFlow-Trainingsauftrag auf einem Rechenziel zu starten. Es wird durch die generische [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) Klasse implementiert, mit der jedes Framework unterstützt werden kann. Weitere Informationen zum Trainieren von Modellen mit dem generischen Estimator finden Sie unter [Trainieren von Azure Machine Learning-Modellen mit einem Estimator](how-to-train-ml-models.md).

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>TensorBoard starten

Sie können TensorBoard während oder nach dem Lauf starten. Im Folgenden erstellen wir eine TensorBoard-Objektinstanz, `tb`, die den Experimentverlauf in die `run` lädt, und dann TensorBoard mit der `start()` Methode startet. 
  
Der T[ensorBoard-Konstruktor](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) nimmt ein Array von Durchläufen an, also stellen Sie sicher, dass Sie es als Array mit nur einem Element übergeben.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Option 2: Historie als Protokoll zur Ansicht in TensorBoard exportieren

Der folgende Code richtet ein Beispielexperiment ein, beginnt den Protokollierungsprozess mit Hilfe der Azure Machine Learning Verlaufshistorie APIs und exportiert die Experimentablaufhistorie in Protokolle, die von TensorBoard zur Visualisierung verwendet werden können. 

### <a name="set-up-experiment"></a>Einrichten des Experiments

Der folgende Code richtet ein neues Experiment ein und benennt das Arbeitsverzeichnis`root_run`. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Hier laden wir den Diabetes-Datensatz – einen eingebauten kleinen Datensatz, der mit Scikit-Learning geliefert wird, und teilen ihn in Test- und Trainingssets auf.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Durchführung von Experiment und Protokollierung von Metriken

Für diesen Code trainieren wir ein lineares Regressionsmodell und protokollieren Schlüsselmetriken, den Alpha-Koeffizienten `alpha`und den mittleren quadratischen Fehler `mse`, in der Laufhistorie.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Durchgänge in TensorBoard exportieren

Mit der [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py)-Methode des SDK können wir die Laufhistorie unseres Azure-Maschinenlernexperiments in TensorBoard-Protokolle exportieren, so dass wir sie über TensorBoard betrachten können.  

Im folgenden Code erstellen wir den Ordner`logdir` in unserem aktuellen Arbeitsverzeichnis. In diesem Ordner exportieren wir unsere Experimentablaufhistorie und Protokolle vpn `root_run` und markieren diese dann als abgeschlossen. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 Sie können einen bestimmten Lauf auch in TensorBoard exportieren, indem Sie den Namen des Laufs angeben`export_to_tensorboard(run_name, logdir)`

Starten und Stoppen von TensorBoard Sobald unsere Laufhistorie für dieses Experiment exportiert wurde, können wir TensorBoard mit der [start().](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-)-Methode starten. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Wenn Sie fertig sind, stellen Sie sicher, dass Sie die [stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--)-Methode des TensorBoard-Objekts aufrufen. Andernfalls läuft TensorBoard weiter, bis Sie den Notebook-Kernel herunterfahren. 

```python
tb.stop()
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Anleitung haben Sie zwei Experimente erstellt und gelernt, wie Sie TensorBoard gegen ihre Laufhistorie starten können, um Bereiche für potenzielles Tuning und Training zu identifizieren. 

* Wenn Sie mit Ihrem Modell zufrieden sind, lesen Sie bitte unseren [Wie Sie ein Modell bereitstellen](how-to-deploy-and-where.md) Artikel. 
* Erfahren Sie mehr über [Hyperparameter-Tuning](how-to-tune-hyperparameters.md). 
