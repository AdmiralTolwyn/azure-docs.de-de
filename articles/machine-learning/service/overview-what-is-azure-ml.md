---
title: Was ist Azure Machine Learning Service?
description: 'Übersicht über den Azure Machine Learning-Dienst: eine integrierte End-to-End-Lösung, mit der professionelle Data Scientists erweiterte Analyseanwendungen für die Cloud entwickeln und bereitstellen und Experimente durchführen können.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 08/05/2019
ms.custom: seodec18
ms.openlocfilehash: 822bbd004035d9745535942c508cae6702e5b99b
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278128"
---
# <a name="what-is-azure-machine-learning-service"></a>Was ist der Azure Machine Learning-Dienst?

Der Azure Machine Learning-Dienst ist ein Clouddienst, mit dem Sie Modelle für maschinelles Lernen trainieren, bereitstellen, automatisieren und verwalten und dabei von den Vorteilen der Cloud profitieren können.

## <a name="what-is-machine-learning"></a>Was ist Machine Learning?

Machine Learning ist ein Data Science-Verfahren, mit dem Computer aus vorhandenen Daten lernen können, um zukünftiges Verhalten, Ergebnisse und Trends vorherzusagen. Durch den Einsatz von maschinellem Lernen können Computer lernen, ohne konkret programmiert worden zu sein.

Dank solcher Vorhersagen oder Prognosen aus Machine Learning können Apps und Geräte „intelligenter“ werden. Wenn Sie beispielsweise online einkaufen, trägt maschinelles Lernen dazu bei, dass Ihnen anhand der gekauften Produkte weitere Produkte empfohlen werden, die Ihnen gefallen könnten. Ein weiteres Beispiel ist die Nutzung Ihrer Kreditkarte. Dabei wird mithilfe von maschinellem Lernen die Transaktion mit einer Transaktionsdatenbank verglichen, wodurch Betrugsfälle erkannt werden können. Auch wenn ein automatischer Staubsauger ein Zimmer saugt, wird mit maschinellem Lernen entschieden, ob die Arbeit erledigt ist.

## <a name="what-is-azure-machine-learning-service"></a>Was ist der Azure Machine Learning-Dienst?

Azure Machine Learning Service umfasst eine cloudbasierte Umgebung, auf die Sie zum Aufbereiten von Daten sowie zum Trainieren, Testen, Bereitstellen, Verwalten und Nachverfolgen von Machine Learning-Modellen zurückgreifen können. Beginnen Sie auf Ihrem lokalen Computer mit dem Training, und führen Sie dann eine horizontale Hochskalierung auf die Cloud durch. Der Dienst unterstützt Open-Source-Technologien wie PyTorch, TensorFlow und Scikit-learn vollständig und kann für jede Art von maschinellem Lernen eingesetzt werden, vom klassischen maschinellen Lernen bis hin zu Deep Learning, beaufsichtigtem und unbeaufsichtigtem Lernen.

Erkunden und Vorbereiten von Daten, Trainieren und Testen von Modellen und Bereitstellen dieser Modelle mit umfangreichen Tools wie z. B.:
+ Eine [grafische Benutzeroberfläche](ui-tutorial-automobile-price-train-score.md), in der Sie Module per Drag & Drop ziehen können, um Ihre Experimente zu erstellen und dann Modelle bereitzustellen
+ [Jupyter-Notebooks](https://jupyter.org), in denen Sie die [SDKs](https://docs.microsoft.com/azure/machine-learning/service/#reference) verwenden, um Ihren eigenen Code zu schreiben, wie etwa [diese Beispiel-Notebooks](https://aka.ms/aml-notebooks)
+ [Visual Studio Code-Erweiterung](how-to-vscode-tools.md)


> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Wofür kann ich Azure Machine Learning Service verwenden?

Verwenden Sie das <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Azure Machine Learning SDK für Python</a> mit Open-Source-Paketen für Python oder die [grafische Benutzeroberfläche (Vorschauversion)](ui-tutorial-automobile-price-train-score.md), um selbst äußerst genaue Machine Learning- und Deep Learning-Modelle in einem Arbeitsbereich für Azure Machine Learning Service zu erstellen und zu trainieren.

Sie können aus mehreren Komponenten für maschinelles Lernen wählen, die in Open-Source-Paketen für Python enthalten sind. Hierzu zählen beispielsweise <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>, <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>, <a href="https://pytorch.org" target="_blank">PyTorch</a> und <a href="https://mxnet.io" target="_blank">MXNet</a>.

Egal, ob Sie Code schreiben oder die grafische Benutzeroberfläche verwenden, Sie können mehrere Ausführungen nachverfolgen, während Sie experimentieren, um die beste Lösung zu finden und die bereitgestellten Modelle zu verwalten.

### <a name="code-first-experience"></a>Code First-Ansatz

Beginnen Sie auf Ihrem lokalen Computer mithilfe des <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Azure Machine Learning Python SDK</a> mit dem Training, und führen Sie dann eine horizontale Hochskalierung auf die Cloud durch. Dank zahlreicher verfügbarer [Compute-Ziele](how-to-set-up-training-targets.md) wie Azure Machine Learning Compute und [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) sowie [Diensten für die erweiterte Hyperparameteroptimierung](how-to-tune-hyperparameters.md) können Sie mithilfe der Cloud schneller bessere Modelle erstellen.

Mit dem SDK können Sie auch [Modelltraining und -optimierung](tutorial-auto-train-models.md) automatisieren.

### <a name="ui-based-low-code-experience"></a>Benutzeroberflächenbasierte Erstellung mit sehr wenig Code

Für codefreies Training testen Sie Folgendes:

+ Erstellen Sie [automatisierte ML-Experimente](tutorial-first-experiment-automated-ml.md) auf der benutzerfreundlichen Oberfläche.
+ Erstellen Sie [Experimente per Drag & Drop auf der grafischen Benutzeroberfläche](ui-tutorial-automobile-price-train-score.md).
  ![Grafische Benutzeroberfläche für Azure Machine Learning Service](media/overview-what-is-azure-ml/visual-interface.png)



### <a name="operationalization-mlops"></a>Operationalisierung (MLOps)

Wenn Sie das richtige Modell haben, können Sie es ganz einfach in einem Webdienst, auf einem IoT-Gerät oder in Power BI verwenden. Weitere Informationen finden Sie im Artikel [Bereitstellen: wie und wo](how-to-deploy-and-where.md).

Die bereitgestellten Modelle können dann über das [Azure Machine Learning SDK für Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) oder über das [Azure-Portal](https://portal.azure.com/) verwaltet werden.

Diese Modelle können genutzt werden und Prognosen [in Echtzeit](how-to-consume-web-service.md) oder [asynchron](how-to-run-batch-predictions.md) (für große Datenmengen) zurückgeben.

Und dank fortschrittlicher[Machine Learning-Pipelines](concept-ml-pipelines.md) können Sie bei jedem einzelnen Schritt – von der Datenaufbereitung über Training und Bewertung von Modellen bis hin zur Bereitstellung – zusammenarbeiten. Pipelines ermöglichen Folgendes:

* Automatisieren des End-to-End-Prozesses für maschinelles Lernen in der Cloud
* Wiederverwenden von Komponenten und Wiederholen von Schritten nur bei Bedarf
* Verwenden verschiedener Computeressourcen in jedem Schritt
* Ausführen von Batchbewertungsaufgaben

Informationen zu den ersten Schritten mit dem Azure Machine Learning-Dienst finden Sie [hier](#next-steps).

## <a name="how-does-azure-machine-learning-service-differ-from-studio"></a>Inwiefern unterscheidet sich Azure Machine Learning Service von Studio?

[Machine Learning Studio](../studio/what-is-ml-studio.md) ist ein visueller Arbeitsbereich mit Drag & Drop-Funktionalität, der sich für die Zusammenarbeit mit anderen Entwicklern und das Erstellen, Testen und Bereitstellen von Lösungen für maschinelles Lernen eignet. Dafür muss kein Code geschrieben werden. Es verwendet vorgefertigte und vorkonfigurierte Algorithmen für maschinelles Lernen und Module für die Verarbeitung von Daten sowie eine proprietäre Compute-Plattform.

Azure Machine Learning Service bietet sowohl SDKs **als auch** eine grafische Benutzeroberfläche (Vorschauversion) zum schnellen Vorbereiten von Daten sowie zum Trainieren und Bereitstellen von Machine Learning-Modellen. Diese grafische Benutzeroberfläche (Vorschauversion) bietet eine ähnliche Drag & Drop-Funktionalität wie Studio. Im Gegensatz zur proprietären Compute-Plattform von Studio verwendet die grafische Benutzeroberfläche jedoch Ihre eigenen Compute-Ressourcen und ist vollständig in Azure Machine Learning Service integriert.

Hier ist ein kurzer Vergleich:

|| Machine Learning Studio | Azure Machine Learning Service:<br/>Grafische Benutzeroberfläche|
|---| --- | --- |
|| Allgemein verfügbar (Generally Available, GA) | In der Vorschau|
|Module für Schnittstellen| Mehrere | Anfänglicher Satz von beliebten Modulen|
|Trainieren von Computezielen| Proprietäres Computeziel, nur CPU-Unterstützung| Unterstützt Azure Machine Learning-Compute, -GPU oder -CPU.<br/>(Weitere Computes in SDK unterstützt)|
|Computeziele für die Bereitstellung| Proprietäres Webdienstformat, nicht anpassbar | Unternehmenssicherheitsoptionen und Azure Kubernetes Service <br/>([Weitere Computes](how-to-deploy-and-where.md) in SDK unterstützt) |
|Automatisiertes Modelltraining und Optimieren von Hyperparametern | Nein | Noch nicht auf der grafischen Benutzeroberfläche. <br/> (Im SDK und Azure-Portal unterstützt.) |

Testen Sie die grafische Benutzeroberfläche (Vorschauversion) in [Tutorial: Prognostizieren von Automobilpreisen mithilfe der grafischen Benutzeroberfläche](ui-tutorial-automobile-price-train-score.md).

> [!NOTE]
> Modelle, die in Studio erstellt werden, können nicht von Azure Machine Learning Service bereitgestellt und verwaltet werden. Modelle, die auf der grafischen Benutzeroberfläche erstellt und bereitgestellt werden, können dagegen über den Azure Machine Learning Service-Arbeitsbereich verwaltet werden.

## <a name="free-trial"></a>Kostenlose Testversion

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie heute die [kostenlose oder kostenpflichtige Version des Azure Machine Learning Service](https://aka.ms/AMLFree) aus.

Sie erhalten dann eine Gutschrift, die Sie für Azure-Dienste einlösen können. Wenn das Guthaben aufgebraucht ist, können Sie das Konto behalten und weiterhin [kostenlose Azure-Dienste](https://azure.microsoft.com/free/) nutzen. Ihre Kreditkarte wird nur dann belastet, wenn Sie Ihre Einstellungen explizit ändern und mit der Berechnung von Gebühren einverstanden sind. Alternativ können Sie Ihre [Vorteile für MSDN-Abonnenten aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Dadurch erhalten Sie ein monatliches Guthaben, das Sie für zahlungspflichtige Azure-Dienste verwenden können.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Sie einen Machine Learning Service-Arbeitsbereich](how-to-manage-workspace.md), um loszulegen.

- Umfassende Tutorials hierzu:
  + [Tutorial: Erste Schritte beim Erstellen Ihres ersten ML-Experiments](tutorial-1st-experiment-sdk-setup.md)
  + [Tutorial: Trainieren eines Bildklassifizierungsmodells mit dem Azure Machine Learning Service](tutorial-train-models-with-aml.md)


- Erfahren Sie mehr über [Machine Learning-Pipelines](/azure/machine-learning/service/concept-ml-pipelines) zum Erstellen, Optimieren und Verwalten Ihrer Machine Learning-Szenarien.

- Lesen Sie den ausführlichen Artikel [Funktionsweise des Azure Machine Learning-Diensts: Architektur und Konzepte](concept-azure-machine-learning-architecture.md).

- Weitere Informationen finden Sie unter [Welche Machine Learning-Produkte bietet Microsoft?](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning).
