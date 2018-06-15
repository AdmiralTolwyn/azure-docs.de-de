---
title: Azure Machine Learning-Experimentieren-Dienst – Konfigurationsdateien
description: In diesem Dokument werden die Konfigurationseinstellungen für den Azure ML-Experimentieren-Dienst erläutert.
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: 1a4b6b803687b2c433ad94a54f076f23fe63c350
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831311"
---
# <a name="azure-machine-learning-experimentation-service-configuration-files"></a>Azure Machine Learning-Experimentieren-Dienst – Konfigurationsdateien

Wenn Sie ein Skript in Azure Machine Learning Workbench (Azure ML) ausführen, wird das Verhalten der Ausführung von Dateien im **aml_config**-Ordner gesteuert. Dieser Ordner befindet sich in Ihrem Stammverzeichnis des Projekts. Es ist wichtig, den Inhalt dieser Dateien zu verstehen, um das gewünschte Ergebnis der Ausführung auf eine optimale Art zu erzielen.

Es folgen die relevanten Dateien in diesem Ordner:
- conda_dependencies.yml
- spark_dependencies.yml
- Computezieldateien
    - \<Computezielname> .compute
- Laufzeitkonfigurationsdateien
    - \<Name der Lauftzeitkonfigurationsdatei> runconfig

>[!NOTE]
>In der Regel verfügen Sie über eine Computezieldatei und eine Ausführungskonfigurationsdatei für jedes einzelne Computeziel, das Sie erstellen. Allerdings können Sie diese Dateien unabhängig erstellen und über mehrere Ausführungskonfigurationsdateien verfügen, die auf dasselbe Computeziel verweisen.

## <a name="condadependenciesyml"></a>conda_dependencies.yml
Diese Datei ist eine [Conda-Umgebungsdatei](https://conda.io/docs/using/envs.html#create-environment-file-by-hand), die die Laufzeitversion und Pakete von Python angibt, von denen Ihr Code abhängt. Wenn Azure ML Workbench ein Skript in einem Docker-Container oder einem HDInsight-Cluster ausführt, wird eine [Conda-Umgebung](https://conda.io/docs/using/envs.html) für das ausgeführte Skript erstellt. 

In dieser Datei geben Sie die Python-Pakete an, die das Skript für die Ausführung benötigt. Der Azure ML-Experimentieren-Dienst erstellt die Conda-Umgebung gemäß Ihrer Abhängigkeitsliste. Die hier aufgeführten Pakete müssen von der Ausführungs-Engine etwa über folgende Kanäle erreichbar sein:

* [continuum.io](https://anaconda.org/conda-forge/repo)
* [PyPI](https://pypi.python.org/pypi)
* ein öffentlich zugänglicher Endpunkt (URL)
* oder ein lokaler Dateipfad
* andere von der Ausführungs-Engine erreichbare Pakete

>[!NOTE]
>Bei der Ausführung in einem HDInsight-Cluster erstellt Azure ML Workbench eine Conda-Umgebung für Ihre spezifische Ausführung. Dadurch können verschiedene Benutzer auf verschiedenen Python-Umgebungen im gleichen Cluster ausgeführt werden.  

Hier ist ein Beispiel für eine typische **conda_dependencies.yml**-Datei.
```yaml
name: project_environment
dependencies:
  # Python version
  - python=3.5.2
  
  # some conda packages
  - scikit-learn
  - cryptography
  
  # use pip to install some more packages
  - pip:
     # a package in PyPi
     - azure-storage
     
     # a package hosted in a public URL endpoint
     - https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
     
     # a wheel file available locally on disk (this only works if you are executing against local Docker target)
     - C:\temp\my_private_python_pkg.whl
```

Azure ML Workbench verwendet die gleiche Conda-Umgebung ohne Neuerstellung, solange **conda_dependencies.yml** gleich bleibt. Wenn sich die Abhängigkeiten ändern, wird die Umgebung neu erstellt.

>[!NOTE]
>Wenn Sie die Ausführung im _lokalen_ Computekontext durchführen, wird die **conda_dependencies.yml**-Datei **nicht** verwendet. Sie müssen Paketabhängigkeiten für Ihre lokale Azure ML Workbench-Python-Umgebung manuell installieren.

## <a name="sparkdependenciesyml"></a>spark_dependencies.yml
Diese Datei gibt den Spark-Anwendungsnamen an, wenn Sie ein PySpark-Skript und Spark-Pakete übermitteln, die installiert werden müssen. Sie können auch ein öffentliches Maven-Repository sowie Spark-Pakete angeben, die sich in diesen Maven-Repositorys befinden.

Beispiel: 

```yaml
configuration:
  # Spark application name
  "spark.app.name": "ClassifyingIris"
  
repositories:
  # Maven repository hosted in Azure CDN
  - "https://mmlspark.azureedge.net/maven"
  
  # Maven repository hosted in spark-packages.org
  - "https://spark-packages.org/packages"
  
packages:
  # MMLSpark package hosted in the Azure CDN Maven
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.5"
    
  # spark-sklearn packaged hosted in the spark-packages.org Maven
  - group: "databricks"
    artifact: "spark-sklearn"
    version: "0.2.0"
```

>[!NOTE]
>Optimierungsparameter für Cluster wie Workergröße und Kerne gehören in den Abschnitt „Konfiguration“ in der Datei „spark_dependecies.yml“. 

>[!NOTE]
>Wenn Sie das Skript in der Python-Umgebung ausführen, wird die Datei *spark_dependencies.yml* ignoriert. Sie hat nur Auswirkungen, wenn Sie für Spark (entweder im Docker- oder HDInsight-Cluster) ausgeführt wird.

## <a name="run-configuration"></a>Laufzeitkonfiguration
Um eine bestimmte Ausführungskonfiguration anzugeben, benötigen Sie eine COMPUTE- und eine RUNCONFIG-Datei. Diese Dateien werden in der Regel mit einem CLI-Befehl generiert. Sie können jedoch auch bestehende klonen, neu benennen und bearbeiten.

```azurecli
# create a compute target pointing to a VM via SSH
$ az ml computetarget attach remotedocker -n <compute target name> -a <IP address or FQDN of VM> -u <username> -w <password>

# create a compute context pointing to an HDI cluster head-node via SSH
$ az ml computetarget attach cluster -n <compute target name> -a <IP address or FQDN of HDI cluster> -u <username> -w <password> 
```

Dieser Befehl erstellt ein Dateipaar auf der Grundlage des angegebenen Computeziels. Angenommen, Sie haben Ihr Computeziel _Foo_ genannt. Dieser Befehl generiert _foo.compute_ und _foo.runconfig_ in Ihrem **Aml_config**-Ordner.

>[!NOTE]
> _Lokale_ oder _Docker_-Namen für die Laufzeitkonfigurationsdateien sind beliebig. Azure ML Workbench fügt diese zwei Laufzeitkonfigurationen hinzu, wenn Sie der Einfachheit halber ein leeres Projekt erstellen. Sie können „<run configuration name>runconfig“-Dateien umbenennen, die aus der Projektvorlage stammen, oder Sie erstellen neue Dateien mit den gewünschten Namen.

### <a name="compute-target-namecompute"></a>\<Computezielname> .compute
Die Datei _\<Computezielname>.compute_ gibt Verbindungs- und Konfigurationsinformationen für das Computeziel an. Es ist eine Liste von Name/Wert-Paaren. Nachfolgend sehen Sie die unterstützten Einstellungen:

**type**: Typ der Computeumgebung. Folgende Werte werden unterstützt:
  - local
  - Remote
  - docker
  - remotedocker
  - cluster

**BaseDockerImage**: Das Docker-Image, das zum Ausführen des Python/PySpark-Skripts verwendet wird. Der Standardwert lautet _microsoft/mmlspark:plus-0.7.91_. Wir unterstützen auch ein weiteres Image: _microsoft/mmlspark:plus-gpu-0.7.91_. Dadurch erhalten Sie GPU-Zugriff auf den Hostcomputer (wenn GPU vorhanden ist).

**address**: Die IP-Adresse oder FQDN (vollständig qualifizierter Domänenname) des virtuellen Computers oder HDInsight-Clusterhauptknoten.

**username**: Der SSH-Benutzername für den Zugriff auf den virtuellen Computer oder den HDInsight-Hauptknoten.

**password**: Das verschlüsselte Kennwort für die SSH-Verbindung.

**sharedVolumes**: Flag, das signalisiert, dass die Ausführungs-Engine die Docker-Funktion „freigegebene Volumes“ verwenden sollte, um Projektdateien hin und her zu senden. Ist dieses Flag aktiviert, kann die Ausführung beschleunigt werden, da Docker direkt auf Projekte zugreifen kann, ohne sie kopieren zu müssen. Es wird empfohlen, _FALSE_ festzulegen, wenn die Docker-Engine unter Windows ausgeführt wird, da das Freigeben von Volumes für Docker unter Windows unzuverlässig sein kann. Legen Sie es auf _TRUE_ fest, wenn das Modul auf MacOS oder Linux ausgeführt wird.

**NvidiaDocker**: Wird dieses Flag auf _TRUE_ festgelegt, so weist es den Azure ML-Experimentieren-Dienst dazu an, den Befehl _nvidia-Docker_ anstelle des normalen _docker_-Befehls zu verwenden, um das Docker-Image zu starten. Die _Nvidia-Docker_-Engine gewährt dem Docker-Container Zugriff auf die GPU-Hardware. Die Einstellung ist erforderlich, wenn die GPU-Ausführung im Docker-Container stattfinden soll. Nur der Linux-Host unterstützt _Nvidia-docker_. Angenommen, die Linux-basierte DSVM in Azure wird mit _Nvidia-Docker_ bereitgestellt. _nvidia-docker_ wird ab jetzt unter Windows nicht unterstützt.

**NativeSharedDirectory**: Diese Eigenschaft gibt das Basisverzeichnis (z.B.: _~/.azureml/share/_) an, in dem Dateien gespeichert werden können, um auf mehreren Ausführungen auf dem gleichen Computeziel freigegeben werden zu können. Wenn diese Einstellung für die Ausführung auf einem Docker-Container verwendet wird, muss _SharedVolumes_ auf TRUE festgelegt werden. Andernfalls schlägt die Ausführung fehl.

**userManagedEnvironment**: Diese Eigenschaft gibt an, ob dieses Computeziel direkt vom Benutzer oder über den Experimentieren-Dienst verwaltet wird.  

**pythonLocation**: Diese Eigenschaft gibt den Speicherort der Python-Laufzeit an, die das Programm des Benutzers auf dem Computeziel ausführen soll. 

### <a name="run-configuration-namerunconfig"></a>\<Name der Lauftzeitkonfigurationsdatei> runconfig
_\<Ausführungskonfigurationsname>.runconfig_ gibt das Experimentausführungsverhalten von Azure ML an. Sie können verschiedene Ausführungsverhaltensweisen konfigurieren, etwa die Überwachung des Ausführungsverlaufs oder das zu verwendende Computeziel usw. Die Namen der Laufzeitkonfigurationsdateien werden zum Auffüllen der Dropdownliste des Ausführungskontexts in der Azure ML Workbench-Desktopanwendung verwendet.

**ArgumentVector**: In diesem Abschnitt werden das Skript, das als Teil dieser Ausführung ausgeführt werden soll und die Parameter für das Skript angegeben. Angenommen, der folgende Codeausschnitt befindet sich in Ihrer „<run configuration name>.runconfig“-Datei 

```
 "ArgumentVector":[
  - "myscript.py"
  - 234
  - "-v" 
 ] 
```
_"az ml Experiment übermitteln foo.runconfig"_ führt den Befehl mit der _myscript.py_-Datei automatisch aus. Diese übergibt 234 als Parameter und legt das –verbose-Flag fest.

**Target**: Dieser Parameter ist der Name der _.compute_-Datei, auf die die _runconfig_-Datei verweist. In der Regel verweist sie auf die _foo.compute_-Datei, aber Sie können sie bearbeiten, damit sie auf ein anderes Computeziel verweist.

**Environment Variables** (Umgebungsvariablen): In diesem Abschnitt können Benutzer Umgebungsvariablen als Teil ihrer Ausführungen festlegen. Benutzer können Umgebungsvariablen mit Name/Wert-Paaren im folgenden Format angeben:
```
EnvironmentVariables:
  "EXAMPLE_ENV_VAR1": "Example Value1"
  "EXAMPLE_ENV_VAR2": "Example Value2"
```

Im Benutzercode kann auf diese Umgebungsvariablen zugegriffen werden. Mit dem folgenden Python-Code wird beispielsweise die Umgebungsvariable „EXAMPLE_ENV_VAR“ ausgegeben:
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

**Framework**: Diese Eigenschaft gibt an, ob Azure ML Workbench eine Spark-Sitzung zum Ausführen des Skripts starten sollte. Der Standardwert ist _PySpark_. Legen Sie ihn auf _Python_ fest, wenn Sie den PySpark-Code nicht ausführen. Der Auftrag kann somit schneller und mit weniger Aufwand gestartet werden.

**CondaDependenciesFile**: Diese Eigenschaft verweist auf die Datei, die Abhängigkeiten der Conda-Umgebung im *Aml_config*-Ordner angibt. Wenn sie auf _NULL_ festgelegt ist, verweist sie auf die **conda_dependencies.yml**-Standarddatei.

**SparkDependenciesFile**: Diese Eigenschaft verweist auf die Datei, die Spark-Abhängigkeiten im **Aml_config**-Ordner angibt. Sie ist standardmäßig auf _NULL_ festgelegt und verweist auf die **spark_dependencies.yml**-Standarddatei.

**PrepareEnvironment**: Wenn diese Eigenschaft auf _TRUE_ festgelegt ist, weist sie den Experimentieren-Dienst dazu an, eine Conda-Umgebung auf der Grundlage der Conda-Abhängigkeiten vorzubereiten, die als Teil Ihrer ersten Ausführung angegeben sind. Diese Eigenschaft gilt nur, wenn sie gegen eine Docker-Umgebung ausgeführt wird. Diese Einstellung hat keine Auswirkung, wenn sie in einer _lokalen_ Umgebung ausgeführt wird. 

**TrackedRun**: Dieses Flag signalisiert dem Experimentieren-Dienst, ob die Ausführung in der Verlaufsinfrastruktur von Azure ML Workbench nachverfolgt werden soll oder nicht. Der Standardwert lautet _true_. 

**UseSampling**: _UseSampling_ gibt an, ob die aktiven Beispieldatasets für Datenquellen zur Ausführung verwendet werden. Wenn es auf _FALSE_ festgelegt ist, erfassen Datenquellen die vollständigen gelesenen Daten aus dem Datenspeicher und verwenden sie. Aktive Beispiele werden verwendet, wenn die Einstellung auf _TRUE_ festgelegt ist. Benutzer können **DataSourceSettings** verwenden, um anzugeben, welche bestimmten Beispieldatasets verwendet werden sollen, wenn sie das aktive Beispiel überschreiben möchten. 

**DataSourceSettings**: Dieser Konfigurationsabschnitt gibt die Einstellungen für die Datenquelle an. In diesem Abschnitt geben Benutzer an, welches vorhandene Datenbeispiel für eine bestimmte Datenquelle als Teil der Ausführung verwendet wird. 

Die folgende Konfigurationseinstellung gibt an, dass das Beispiel mit dem Namen „MySample“ für die Datenquelle mit dem Namen „MyDataSource“ verwendet wird:
```
DataSourceSettings:
    MyDataSource.dsource:
    Sampling:
    Sample: MySample
```

**DataSourceSubstitutions**: Es kann ein Ersatz einer Datenquelle verwendet werden, wenn der Benutzer aus einer Datenquelle zu einer anderen wechseln möchte, ohne ihren Code zu ändern. Benutzer können z.B. aus einer Stichprobe lokaler Dateien zum originalen, größeren Dataset wechseln, das in Azure Blob gespeichert wird, indem sie den Datenquellenverweis ändern. Wenn ein Ersatz verwendet wird, führt Azure ML Workbench die Datenquellen und Datenvorbereitungspakete aus, indem auf die Ersatzdatenquelle verwiesen wird.

Im folgende Beispiel ersetzen die „mylocal.datasource“-Verweise in Azure ML Datenquellen und Datenvorbereitungspakete mit „myremote.dsource“. 
 
```
DataSourceSubstitutions:
    mylocal.dsource: myremote.dsource
```

Auf der Grundlage des oben genannten Ersatzes liest das folgende Codebeispiel jetzt von „myremote.dsource“ anstelle von „mylocal.dsource“, ohne dass Benutzer ihren Code ändern.
```
df = datasource.load_datasource('mylocal.dsource')
```
## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur [Konfiguration des Experimentieren-Diensts](experimentation-service-configuration.md).
