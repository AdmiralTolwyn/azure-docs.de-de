---
title: 'MLOps: Verwalten, Bereitstellen und Überwachen von ML-Modellen'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie mit Azure Machine Learning Services für MLOps Ihre Modelle bereitstellen, verwalten und überwachen, um sie kontinuierlich zu verbessern. Sie können die Modelle, die Sie mit Azure Machine Learning Services trainiert haben, auf Ihrem lokalen Computer oder aus anderen Quellen bereitstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 270ebb6247e812d42ac84698d0073b41c639cb9a
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2019
ms.locfileid: "70011586"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Verwalten, Bereitstellen und Überwachen von Modellen mit Azure Machine Learning Services

In diesem Artikel erfahren Sie, wie Sie den Azure Machine Learning Service verwenden, um den Lebenszyklus Ihrer Modelle zu verwalten. Azure Machine Learning verwendet MLOps (Machine Learning Operations, Machine Learning-Vorgänge), wodurch sich die Qualität und Konsistenz Ihrer Machine Learning-Lösungen verbessern. 

Der Azure Machine Learning Service bietet die folgenden MLOps-Features:

- **Bereitstellen von ML-Projekten von jedem Ort aus**
- **Überwachen von ML-Anwendungen auf betriebs- und ML-bezogene Probleme**: Vergleichen Sie Modelleingaben für Training und Rückschlüsse, untersuchen Sie modellspezifische Metriken, und stellen Sie Überwachungsfunktionen und Warnungen für Ihre ML-Infrastruktur bereit.
- **Erfassen der erforderlichen Daten zur Einrichtung eines umfassenden Überwachungspfads des ML-Lebenszyklus**: Sie können beispielsweise überwachen, wer Modelle veröffentlicht, warum Änderungen vorgenommen werden und wann Modelle in der Produktion bereitgestellt oder verwendet wurden.
- **Automatisieren des End-to-End-ML-Lebenszyklus mit Azure Machine Learning und Azure DevOps**: Ermöglicht das häufige Aktualisieren von Modellen, Testen neuer Modelle und Durchführen fortlaufender Rollouts von neuen ML-Modellen zusätzlich zu Ihren anderen Anwendungen und Diensten.

Weitere Informationen zu den Konzepten von MLOps und der Anwendung dieser Vorgänge auf den Azure Machine Learning Service erhalten Sie in folgendem Video.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="deploy-ml-projects-from-anywhere"></a>Bereitstellen von ML-Projekten von jedem Ort aus

### <a name="turn-your-training-process-into-a-reproducible-pipeline"></a>Ändern Ihres Trainingsprozesses in eine reproduzierbare Pipeline
Verwenden Sie ML-Pipelines von Azure Machine Learning, um alle Schritte zu verknüpfen, die an Ihrem Trainingsprozess für Modelle beteiligt sind – von der Datenaufbereitung, Featureextraktion und Hyperparameteroptimierung bis hin zur Modellauswertung.

Weitere Informationen finden Sie im Artikel zu [ML-Pipelines](concept-ml-pipelines.md).

### <a name="register-and-track-ml-models"></a>Registrieren und Nachverfolgen von ML-Modellen

Die Modellregistrierung ermöglicht es Ihnen, Ihre Modelle in der Azure-Cloud in Ihrem Arbeitsbereich zu speichern und zu versionieren. Die Modellregistrierung erleichtert das Organisieren und Verwalten von trainierten Modellen.

> [!TIP]
> Ein registriertes Modell ist ein logischer Container für eine oder mehrere Dateien, aus denen Ihr Modell besteht. Wenn Sie beispielsweise ein Modell verwenden, das in mehreren Dateien gespeichert ist, können Sie diese als einzelnes Modell in Ihrem Azure Machine Learning-Arbeitsbereich registrieren. Nach der Registrierung können Sie das registrierte Modell dann herunterladen oder bereitstellen und alle Dateien empfangen, die registriert wurden.
 
Registrierte Modelle werden anhand des Namens und der Version identifiziert. Jedes Mal, wenn Sie ein Modell mit dem gleichen Namen wie ein bereits vorhandenes Modell registrieren, erhöht die Registrierung die Versionsnummer. Sie können beim Registrieren auch zusätzliche Metadatentags bereitstellen, die bei der Suche nach Modellen verwendet werden können. Azure Machine Learning Service unterstützt Modelle, die mit Python 3.5.2 oder höher geladen werden können.

> [!TIP]
> Sie können auch Modelle registrieren, die außerhalb von Azure Machine Learning Service trainiert wurden.

Sie können kein registriertes Modell löschen, das in einer aktiven Bereitstellung verwendet wird.
Weitere Informationen finden Sie im Abschnitt „Registrieren eines Modells“ von [Bereitstellen von Modellen](how-to-deploy-and-where.md#registermodel).

### <a name="package-and-debug-models"></a>Packen und Debuggen von Modellen

Vor dem Bereitstellen eines Modells in der Produktion wird es in ein Docker-Image gepackt. In den meisten Fällen erfolgt die Imageerstellung während der Bereitstellung automatisch im Hintergrund. In erweiterten Szenarien können Sie das Image manuell angeben.

Wenn Probleme bei der Bereitstellung auftreten, können Sie ein Modell für Problembehandlung und Debugging in Ihrer lokalen Entwicklungsumgebung bereitstellen.

Weitere Informationen finden Sie unter [Bereitstellen von Modellen](how-to-deploy-and-where.md#registermodel) und [Problembehandlung von Bereitstellungen](how-to-troubleshoot-deployment.md).

### <a name="validate-and-profile-models"></a>Validierung und Profilerstellung von Modellen

Der Azure Machine Learning Service kann die Profilerstellung verwenden, um die idealen CPU- und Arbeitsspeichereinstellungen für die Bereitstellung Ihres Modells zu ermitteln. Die Modellvalidierung erfolgt im Rahmen dieses Prozesses unter Verwendung von Daten, die Sie für den Profilerstellungsvorgang bereitstellen.

### <a name="convert-and-optimize-models"></a>Konvertieren und Optimieren von Modellen

Die Konvertierung Ihres Modells zu [Open Neural Network Exchange](https://onnx.ai) (ONNX) kann die Leistung verbessern. Im Durchschnitt lässt sich durch eine Konvertierung zu ONNX eine 2-fache Leistungssteigerung erzielen.

Weitere Informationen zu ONNX mit Azure Machine Learning Service finden Sie im Artikel [Erstellen und Beschleunigen von ML-Modellen](concept-onnx.md).

### <a name="use-models"></a>Verwenden von Modellen

Trainierte Machine Learning-Modelle können als Webdienste in der Cloud oder lokal in Ihrer Entwicklungsumgebung bereitgestellt werden. Sie können Modelle auch auf Azure IoT Edge-Geräten bereitstellen. Bereitstellungen können CPUs, GPUs oder Field Programmable Gate Arrays (FPGAs) für das Ziehen von Rückschlüssen verwenden. Sie können auch Modelle aus Power BI verwenden.

Wenn Sie ein Modell als Webdienst oder auf einem IoT Edge-Gerät verwenden, stellen Sie die folgenden Elemente bereit:

* Die Modelle, die zum Bewerten der Daten dienen, die an den Dienst bzw. das Gerät übermittelt werden.
* Ein Eingabeskript. Dieses Skript akzeptiert Anforderungen, verwendet die Modelle zum Bewerten der Daten und gibt eine Antwort zurück.
* Eine Conda-Umgebungsdatei, die die Abhängigkeiten beschreibt, die von den Modellen und vom Eingabeskript benötigt werden.
* Zusätzliche Ressourcen wie Text, Daten usw., die von den Modellen und vom Eingabeskript benötigt werden.

Diese Ressourcen werden in ein Docker-Image gepackt und als Webdienst oder auf einem IoT Edge-Modul bereitgestellt.

Optional können Sie die folgenden Parameter verwenden, um die Bereitstellung weiter zu optimieren:

* GPU aktivieren: Wird verwendet, um die GPU-Unterstützung im Docker-Image zu aktivieren. Das Image muss in Microsoft Azure-Diensten wie Azure Container Instances, Azure Kubernetes Service, Azure Machine Learning Compute oder Azure Virtual Machines verwendet werden.
* Docker-Datei für zusätzliche Schritte: Eine Datei, die zusätzliche Docker-Schritte enthält, die beim Erstellen des Docker-Images ausgeführt werden sollen.
* Basisimage: Ein benutzerdefiniertes Image, das als Basisimage verwendet werden soll. Wenn Sie kein benutzerdefiniertes Image verwenden, wird das Basisimage vom Azure Machine Learning Service bereitgestellt.

Sie stellen auch die Konfiguration der Zielbereitstellungsplattform bereit. Beispielsweise den Typ der VM-Familie, den verfügbaren Arbeitsspeicher und die Anzahl von Kernen bei der Bereitstellung in Azure Kubernetes Service.

Wenn das Image erstellt wird, werden auch die Komponenten hinzugefügt, die vom Azure Machine Learning Service benötigt werden. Beispielsweise die Ressourcen, die erforderlich sind, um den Webdienst auszuführen und mit IoT Edge zu interagiere.

> [!NOTE]
> Sie können die im Docker-Image verwendeten Webserver- oder IoT Edge-Komponenten nicht ändern. Der Azure Machine Learning Service verwendet Webserverkonfigurationen und IoT Edge-Komponenten, die von Microsoft getestet wurden und unterstützt werden.

#### <a name="web-service"></a>Webdienst

Sie können Ihre Modelle in **Webdiensten** mit den folgenden Computezielen verwenden:

* Azure Container Instances
* Azure Kubernetes Service
* Lokale Entwicklungsumgebung

Um das Modell als Webdienst bereitzustellen, müssen Sie folgende Elemente zur Verfügung stellen:

* Das Modell oder ein Ensemble von Modellen.
* Abhängigkeiten, die zur Nutzung des Modells erforderlich sind. Beispielsweise ein Skript, das Anforderungen akzeptiert und das Modell aufruft, Conda-Abhängigkeiten, usw.
* Eine Bereitstellungskonfiguration, die beschreibt, wie und wo das Modell bereitgestellt werden soll.

Weitere Informationen finden Sie unter [Bereitstellen von Modellen](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>IoT Edge-Geräte

Sie können Modelle mit IoT-Geräten über **Azure IoT Edge-Module** verwenden. IoT Edge-Module werden auf einem Hardwaregerät bereitgestellt, wodurch Rückschlüsse bzw. Modellbewertungen auf dem Gerät ermöglicht werden.

Weitere Informationen finden Sie unter [Bereitstellen von Modellen](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analytics

Microsoft Power BI unterstützt die Verwendung von Machine Learning-Modellen für Datenanalysen. Weitere Informationen finden Sie unter [Integration von Azure Machine Learning in Power BI (Vorschau)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).


## <a name="monitor-ml-applications-for-operational-and-ml-related-issues"></a>Überwachen von ML-Anwendungen auf betriebs- und ML-bezogene Probleme

Die Überwachung ermöglicht Ihnen zu verstehen, welche Daten an Ihr Modell gesendet werden und welche Vorhersagen es zurückgibt.

Diese Informationen helfen Ihnen zu verstehen, wie Ihr Modell genutzt wird. Die gesammelten Eingabedaten können auch zum Trainieren künftiger Versionen des Modells nützlich sein.

Weitere Informationen finden Sie unter [Gewusst wie: Aktivieren der Modelldatensammlung](how-to-enable-data-collection.md).


## <a name="capture-an-end-to-end-audit-trail-of-the-ml-lifecycle"></a>Erfassen eines End-to-End-Überwachungspfads für den ML-Lebenszyklus

Bei Azure ML können Sie den End-to-End-Überwachungspfad Ihrer gesamten ML-Ressourcen nachverfolgen. Dies gilt insbesondere in folgenden Fällen:

- Azure ML ist [mit Git integriert](how-to-set-up-training-targets.md#gitintegration), um nachverfolgen zu können, von welchem Repository, welcher Branch oder welchem Commit Ihr Code stammt.
- Mit [Azure ML-Datasets](how-to-create-register-datasets.md) können Sie Daten nachverfolgen und mit einer Versionsangabe versehen.
- Azure ML-Ausführungsverlauf speichert eine Momentaufnahme des Codes, der Daten und des Computevorgangs, mit denen ein Modell trainiert wurde.
- Die Azure ML-Modellregistrierung erfasst alle Metadaten, die Ihrem Modell zugeordnet sind (welches Experiment wurde für das Training verwendet, wo wird es bereitgestellt und ob die Bereitstellungen fehlerfrei sind).

## <a name="automate-the-end-to-end-ml-lifecycle"></a>Automatisieren des End-to-End-ML-Lebenszyklus 

Sie können GitHub und Azure Pipelines verwenden, um einen Continuous Integration-Prozess zu erstellen, der ein Modell trainiert. In einem typischen Szenario, in dem ein Data Scientist eine Änderung in das Git-Repository für ein Projekt eincheckt, startet die Azure-Pipeline einen Trainingsdurchlauf. Die Ergebnisse dieses Durchlaufs können dann untersucht werden, um die Leistungsmerkmale des trainierten Modells zu ermitteln. Sie können auch eine Pipeline erstelle, die das Modell als Webdienst bereitstellt.

Die [Azure Machine Learning-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) erleichtert die Arbeit mit Azure Pipelines. Sie bietet folgende Verbesserungen für Azure Pipelines:

* Ermöglicht beim Definieren einer Dienstverbindung die Auswahl eines Arbeitsbereichs.
* Ermöglicht das Auslösen von Releasepipelines durch trainierte Modelle, die in einer Trainingspipeline erstellt wurden.

Weitere Informationen zum Verwenden von Azure Pipelines mit Azure Machine Learning finden Sie im Artikel [Continuous Integration und Continuous Deployment von ML-Modellen mit Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) und im Repository [Azure Machine Learning Service MLOps](https://aka.ms/mlops).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr darüber [wie und auf welchen Instanzen Modelle mit dem Azure Machine Learning-Dienst bereitgestellt werden können](how-to-deploy-and-where.md). Ein Bereitstellungsbeispiel finden Sie unter [Tutorial: Bereitstellen eines Bildklassifizierungsmodells in Azure Container Instances](tutorial-deploy-models-with-aml.md).

Erfahren Sie, wie Sie [durchgängige Integration und Bereitstellung von ML-Modellen mit Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) erstellen. 

Erfahren Sie, wie Sie Clientanwendungen und -dienste erstellen, die [ein Modell verwenden, das als Webdienst bereitgestellt wird](how-to-consume-web-service.md).
