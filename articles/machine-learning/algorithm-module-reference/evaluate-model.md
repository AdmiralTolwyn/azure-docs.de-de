---
title: 'Evaluate Model: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie das Modul „Evaluate Model“ (Auswerten des Modells) in Azure Machine Learning verwenden können, um die Qualität eines trainierten Modells zu messen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/24/2020
ms.openlocfilehash: cf9597f4a722ff9cda68e87b31db77c989afcb0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82129851"
---
# <a name="evaluate-model-module"></a>Modul „Evaluate Model“

In diesem Artikel wird ein Modul in Azure Machine Learning-Designer (Vorschauversion) beschrieben.

Mithilfe dieses Moduls können Sie die Genauigkeit eines trainierten Modells messen. Sie stellen ein Dataset zur Verfügung, das die anhand eines Modells generierten Ergebnisse enthält. Anschließend berechnet das Modul **Evaluate Model** eine Reihe branchenüblicher Auswertungsmetriken.
  
 Die Metriken, die vom Modul **Evaluate Model** zurückgegeben werden, hängen vom Typ des Modells ab, das Sie auswerten möchten:  
  
-   **Klassifizierungsmodelle**    
-   **Regressionsmodelle**  
-   **Clusteringmodelle**  


> [!TIP]
> Wenn Sie mit der Modellauswertung noch nicht vertraut sind, empfehlen wir Ihnen die Videoreihe von Dr. Stephen Elston im Rahmen des [Kurses zum maschinellen Lernen](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) von EdX. 


## <a name="how-to-use-evaluate-model"></a>Verwenden von Evaluate Model
1. Verbinden Sie die Ausgabe **Scored dataset** (Dataset mit Score) des Moduls [Score Model](./score-model.md) mit dem linken Eingang des Moduls **Evaluate Model**. 

2. [Optional:] Verbinden Sie die Ausgabe **Scored dataset** (Dataset mit Score) von [Score Model](./score-model.md) für das zweite Modell mit dem **rechten** Eingang von **Evaluate Model**. Sie können die Ergebnisse zweier verschiedener Modelle auf der Grundlage derselben Daten einfach vergleichen. Die beiden Eingabealgorithmen sollten denselben Algorithmustyp aufweisen. Sie können auch Ergebnisse zweier verschiedener Ausführungen über dieselben Daten mit unterschiedlichen Parametern vergleichen.

    > [!NOTE]
    > Als Algorithmustyp sind zweiklassige Klassifizierung, mehrklassige Klassifizierung, Regression oder Clustering unter den Algorithmen für maschinelles Lernen möglich. 

3. Übermitteln Sie die Pipeline, um die Bewertungsscores zu generieren.

## <a name="results"></a>Ergebnisse

Klicken Sie nach der Ausführung von **Evaluate Model** mit der rechten Maustaste auf das Modul, und wählen Sie **Visualize Evaluation results** (Auswertungsergebnisse visualisieren) aus, um die Ergebnisse anzuzeigen.

Wenn Sie Datasets mit beiden Eingaben von **Evaluate Model** verbinden, enthalten die Ergebnisse Metriken für beide Datasets bzw. beide Modelle.
Das Modell oder die Daten, die an den linken Port angefügt wurden, werden zuerst im Bericht dargestellt, gefolgt von den Metriken für das Dataset oder das Modell, das an den rechten Port angefügt wurde.  

So stellt beispielsweise die folgende Abbildung einen Vergleich der Ergebnisse zweier Clusteringmodelle dar, die anhand derselben Daten, aber mit unterschiedlichen Parametern erstellt wurden.  

![Comparing2Models](media/module/evaluate-2-models.png)  

Da es sich um ein Clusteringmodell handelt, sind die Auswertungsergebnisse anders als wenn Sie Ergebnisse zweier Regressionsmodelle vergleichen oder zwei Klassifizierungsmodelle miteinander vergleichen. Die Gesamtpräsentation ist jedoch identisch. 

## <a name="metrics"></a>Metriken

Dieser Abschnitt beschreibt die Metriken, die für die bestimmten Arten von Modellen zurückgegeben werden, die für den Einsatz mit **Evaluate Model** unterstützt werden:

+ [Klassifizierungsmodelle](#metrics-for-classification-models)
+ [Regressionsmodelle](#metrics-for-regression-models)
+ [Clusteringmodelle](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Metriken für Klassifizierungsmodelle

Die folgenden Metriken werden bei der Auswertung von Klassifizierungsmodellen erfasst.
  
-   **Accuracy** (Treffergenauigkeit) misst die Güte eines Klassifizierungsmodells als das Verhältnis der wahren Ergebnisse zur Gesamtheit der Fälle.  
  
-   **Precision** (Genauigkeit) ist der Anteil wahrer Ergebnisse an allen positiven Ergebnissen.  
  
-   **Recall** (Trefferquote) ist der Anteil aller richtigen Ergebnisse, die vom Modell zurückgegeben werden.  
  
-   **F-Score** (F-Maß) wird als gewichteter Durchschnitt von Genauigkeit und Trefferquote von 0 bis 1 berechnet, wobei 1 der ideale Wert für F-Maß ist.  
  
-   **AUC** (Fläche unter der Kurve) misst den Bereich unter der Kurve, der mit wahren positiven Werten auf der y-Achse und falschen positiven Werten auf der x-Achse gezeichnet wurde. Diese Metrik ist nützlich, da sie einen einzelnen Wert liefert, mit dem Sie Modelle verschiedener Typen vergleichen können.  
  
- **Average log loss** (logarithmische Durchschnittsdämpfung) ist ein Einzelwert, der verwendet wird, um die Strafterme für falsche Ergebnisse auszudrücken. Er wird als Differenz zwischen zwei Wahrscheinlichkeitsverteilungen berechnet – der tatsächlichen und der im Modell.  
  
- **Training log loss** (logarithmische Trainingsdämpfung) ist ein einzelner Wert, der den Vorteil des Klassifizierers gegenüber einer Zufallsvorhersage wiedergibt. Die Trainingsdämpfung misst die Unsicherheit Ihres Modells, indem die ausgegebenen Wahrscheinlichkeiten mit den bekannten Werten (Ground Truth) in den Bezeichnungen verglichen werden. Die Trainingsdämpfung sollte für das Modell als Ganzes minimiert werden.

### <a name="metrics-for-regression-models"></a>Metriken für Regressionsmodelle
 
Die für Regressionsmodelle zurückgegebenen Metriken sind so gestaltet, dass sie die Fehlerquote schätzen.  Ein Modell passt gut zu den Daten, wenn der Unterschied zwischen beobachteten und vorhergesagten Werten gering ist. Wenn Sie sich jedoch das Muster der Residuen (die Differenz zwischen einem beliebigen vorhergesagten Punkt und seinem entsprechenden Istwert) ansehen, können Sie viel über eine mögliche Verzerrung im Modell erfahren.  
  
 Die folgenden Metriken werden für die Auswertung von Regressionsmodellen herangezogen.
  
- **Mean absolute error (MAE)** (mittlerer absoluter Fehler) misst, wie nah die Vorhersagen an den tatsächlichen Ergebnissen sind, weshalb ein niedrigerer Wert besser ist.  
  
- **Root mean squared error (RMSE)** (mittlere quadratische Abweichung ) erzeugt einen Einzelwert, der den Fehler im Modell zusammenfasst. Durch die Quadrierung der Differenz ignoriert die Metrik den Unterschied zwischen Über- und Unterprognose.  
  
- **Relative absolute error (RAE)** (relativer absoluter Fehler) ist die relative absolute Differenz zwischen erwartetem und tatsächlichem Wert; relativ, weil die mittlere Differenz durch das arithmetische Mittel dividiert wird.  
  
- **Relative squared error (RSE)** (relativer quadratischer Fehler) normalisiert ebenfalls den gesamten quadrierten Fehler der vorhergesagten Werte durch Division durch den gesamten quadrierten Fehler der Istwerte.  
  

  
- **Coefficient of determination** (Bestimmtheitsmaß), oft auch als R<sup>2</sup> bezeichnet, stellt die Vorhersagekraft des Modells als Wert von 0 bis 1 dar. 0 bedeutet, dass das Modell zufällig ist (also nichts erklärt). 1 bedeutet, dass es eine perfekte Anpassung gibt. Bei der Interpretation der R<sup>2</sup>-Werte ist jedoch Vorsicht geboten, da niedrige Werte völlig normal und hohe Werte verdächtig sein können.

###  <a name="metrics-for-clustering-models"></a>Metriken für Clusteringmodelle

Da sich Clusteringmodelle in vielerlei Hinsicht deutlich von Klassifizierungs- und Regressionsmodellen unterscheiden, gibt [Evaluate Model](evaluate-model.md) (Modell bewerten) auch eine andere Menge an Statistiken für Clusteringmodelle zurück.  
  
 Die für ein Clusteringmodell zurückgegebene Statistik beschreibt, wie viele Datenpunkte jedem Cluster zugeordnet wurden, wie groß die Trennung zwischen den Clustern ist und wie eng die Datenpunkte innerhalb jedes Clusters gebündelt sind.  
  
 Die Statistiken für das Clusteringmodell werden über das gesamte Dataset gemittelt, wobei clusterbezogene Statistiken in zusätzlichen Zeilen enthalten sind.  
  
Die folgenden Metriken werden für die Auswertung von Clusteringmodellen herangezogen.
    
-   Die Werte in der Spalte **Average Distance to Other Center** (Durchschnittlicher Abstand zum anderen Zentrum) geben an, wie nahe jeder Punkt im Cluster im Durchschnitt an den Schwerpunkten aller anderen Cluster liegt.   

-   Die Werte in der Spalte **Average Distance to Cluster Center** (Durchschnittlicher Abstand zum Clusterzentrum) stellen die Nähe aller Punkte in einem Cluster zum Schwerpunkt dieses Clusters dar.  
  
-   Die Spalte **Number of Points** (Anzahl der Punkte) zeigt, wie viele Datenpunkte jedem Cluster zugewiesen wurden, sowie die Gesamtanzahl der Datenpunkte in jedem Cluster.  
  
     Wenn die Anzahl der den Clustern zugeordneten Datenpunkte geringer ist als die Gesamtanzahl der verfügbaren Datenpunkte, bedeutet dies, dass die Datenpunkte keinem Cluster zugeordnet werden konnten.  
  
-   Die Scores in der Spalte **Maximal Distance to Cluster Center** (Maximaler Abstand zum Clusterzentrum) stellen die Summen der Abstände zwischen jedem Punkt und dem Schwerpunkt des Clusters des betreffenden Punkts dar.  
  
     Wenn dieser Wert hoch ist, kann dies bedeuten, dass der Cluster weit verstreut ist. Überprüfen Sie diese Statistik zusammen mit **Average Distance to Cluster Center** (Durchschnittlicher Abstand zum Clusterzentrum), um die Streuung des Clusters zu ermitteln.   

-   Der Wert **Combined Evaluation** (Kombinierte Bewertung) am Ende jedes Ergebnisabschnitts listet die gemittelten Werte für die in diesem bestimmten Modell erstellten Cluster auf.  
  

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 