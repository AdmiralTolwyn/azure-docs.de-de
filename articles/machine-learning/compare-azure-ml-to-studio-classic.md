---
title: Azure Machine Learning und Machine Learning Studio (klassisch)
description: Unterschiede zwischen Azure Machine Learning und Machine Learning Studio (klassisch)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: a808387c891b4078092c5cac1bf95df8472ff0f6
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529547"
---
# <a name="how-azure-machine-learning-differs-from-machine-learning-studio-classic"></a>Unterschiede zwischen Azure Machine Learning und Machine Learning Studio (klassisch)

Dieser Artikel enthält einen Vergleich der Features, der Funktionen und der Oberfläche von Azure Machine Learning und Machine Learning Studio (klassisch). 

## <a name="about-machine-learning-studio-classic"></a>Informationen zu Machine Learning Studio (klassisch)
[Machine Learning Studio (klassisch)](studio/what-is-ml-studio.md) ist ein visueller Arbeitsbereich mit Drag & Drop-Funktionalität, der sich für die Zusammenarbeit mit anderen Entwicklern und das Erstellen, Testen und Bereitstellen von Lösungen für maschinelles Lernen eignet, ohne dass dafür Code geschrieben werden muss. Es verwendet vorgefertigte und vorkonfigurierte Algorithmen für maschinelles Lernen und Module für die Verarbeitung von Daten sowie eine proprietäre Compute-Plattform.

## <a name="about-azure-machine-learning"></a>Über Azure Machine Learning

[Azure Machine Learning](overview-what-is-azure-ml.md) verfügt sowohl über eine Weboberfläche, die als Designer (Vorschauversion) bezeichnet wird, **als auch** über mehrere SDKs und eine CLI zum schnellen Aufbereiten von Daten und Trainieren und Bereitstellen von Machine Learning-Modellen. Mit Azure Machine Learning erhalten Sie Skalierbarkeit, Unterstützung für mehrere Frameworks sowie erweiterte ML-Funktionen, z. B. automatisiertes Machine Learning und Pipelineunterstützung.

Der Azure Machine Learning-Designer verfügt über eine ähnliche Drag & Drop-Umgebung wie Studio (klassisch). Im Gegensatz zur proprietären Computeplattform von Studio (klassisch) nutzt der Designer aber Ihre eigenen Computeressourcen und ist skalierbar und vollständig in Azure Machine Learning integriert.  

> [!TIP]
> Kunden, die derzeit Machine Learning Studio (klassisch) verwenden oder evaluieren, wird empfohlen, den [Azure Machine Learning-Designer](https://docs.microsoft.com/azure/machine-learning/service/ui-concept-visual-interface) (Vorschauversion) auszuprobieren. Er bietet Drag & Drop-ML-Module und __zusätzlich__ Skalierbarkeit, Versionskontrolle und Unternehmenssicherheit.

## <a name="comparison-azure-machine-learning-vs-machine-learning-studio-classic"></a>Vergleich: Azure Machine Learning und Machine Learning Studio (klassisch)

Hier ist ein kurzer Vergleich:

||  Azure Machine Learning-Designer|Studio (klassisch) |
|---| --- | --- |
||Der Designer befindet sich in der Vorschauphase, Azure Machine Learning ist allgemein verfügbar.|Allgemein verfügbar (Generally Available, GA) | 
|Drag & Drop-Oberfläche| Ja | Ja|
|Experiment| Skalieren mit Computeziel|Skalieren (Limit für Trainingsdaten: 10 GB) | 
|Module für Schnittstellen| [Viele beliebte Module](algorithm-module-reference/module-reference.md) | Mehrere |
|Trainieren von Computezielen| AML-Compute (GPU/CPU)|Proprietäres Computeziel, nur CPU|
|Rückschlusscomputeziele| Azure Kubernetes Service für Rückschlüsse in Echtzeit <br/>AML-Compute für Batchrückschluss|Proprietäres Webdienstformat, nicht anpassbar | 
|ML-Pipeline| Pipelineerstellung <br/> Veröffentlichte Pipeline <br/> Pipelineendpunkt <br/> [Weitere Informationen zur ML-Pipeline](concept-ml-pipelines.md)|Nicht unterstützt | 
|ML-Vorgänge| Konfigurierbare Versionsverwaltung für Bereitstellung, Modell und Pipeline|Einfache Modellverwaltung und -bereitstellung | 
|Modell| Standardformat. Variiert abhängig vom Trainingsauftrag.|Proprietäres Format (nicht portabel).| 
|Automatisiertes Trainieren von Modellen|Noch nicht im Designer verfügbar, aber über die Oberfläche und die SDKs möglich.| Nein | 

## <a name="get-started-with-azure-machine-learning"></a>Erste Schritte mit Azure Machine Learning

Die folgenden Ressourcen dienen Ihnen als Hilfe beim Einstieg in Azure Machine Learning.

- Lesen Sie die [Übersicht über Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 

- [Erstellen Sie Ihre erste Designer-Pipeline](tutorial-designer-automobile-price-train-score.md) zum Vorhersagen von Fahrzeugpreisen.

![Azure Machine Learning-Designer: Beispiel](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Nächste Schritte

Zusätzlich zu den Drag & Drop-Funktionen im Designer verfügt Azure Machine Learning noch über weitere Tools:  
  + [Verwenden von Python-Notebooks zum Trainieren und Bereitstellen von ML-Modellen](tutorial-1st-experiment-sdk-setup.md)
  + [Verwenden von R Markdown zum Trainieren und Bereitstellen von ML-Modellen](tutorial-1st-r-experiment.md) 
  + [Verwenden von automatisiertem Machine Learning zum Trainieren und Bereitstellen von ML-Modellen](tutorial-designer-automobile-price-train-score.md) 
  + [Verwenden der Machine Learning-CLI zum Trainieren und Bereitstellen eines Modells](tutorial-train-deploy-model-cli.md)

