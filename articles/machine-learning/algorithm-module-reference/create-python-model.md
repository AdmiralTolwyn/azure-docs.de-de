---
title: 'Create Python Model: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mithilfe des Moduls Create Python Model in Azure Machine Learning ein benutzerdefiniertes Modellierungs- oder Datenverarbeitungsmodul erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 26bcd855dbe2ea85df9fb6aca74f8b52e8ce8e46
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268920"
---
# <a name="create-python-model"></a>Erstellen eines Python-Modells

In diesem Artikel wird ein Modul in Azure Machine Learning-Designer (Vorschauversion) beschrieben.

Erfahren Sie, wie Sie das **Create Python Model**-Modul verwenden, um anhand eines Python-Skripts ein untrainiertes Modell zu erstellen. Sie können das Modell auf der Grundlage jedes Lernmodells erstellen, das in einem Python-Paket in der Azure Machine Learning-Designer-Umgebung enthalten ist. 

Nachdem Sie das Modell erstellt haben, können Sie es mithilfe von [Train Model](train-model.md) wie alle anderen Learner in Azure Machine Learning mit einem Dataset trainieren. Das trainierte Modell kann an [Score Model](score-model.md) übergeben werden, um es zum Treffen von Vorhersagen zu verwenden. Anschließend können Sie das trainierte Modell speichern und den Bewertungsworkflow als Webdienst veröffentlichen.

> [!WARNING]
> Derzeit ist es nicht möglich, die bewerteten Ergebnisse eines Python-Modells an [Evaluate Model](evaluate-model.md) zu übergeben. Wenn Sie ein Modell auswerten müssen, können Sie ein benutzerdefiniertes Python-Skript schreiben und es mit dem [Execute Python Script](execute-python-script.md)-Modul ausführen.  


## <a name="how-to-configure-create-python-model"></a>Konfigurieren von „Create Python Model“

Die Verwendung dieses Moduls setzt gute oder fortgeschrittene Kenntnisse von Python voraus. Das Modul unterstützt die Verwendung jedes Learners, der in den bereits in Azure Machine Learning installierten Python-Paketen enthalten ist. Die Liste der vorinstallierten Python-Pakete finden Sie unter [Execute Python Script](execute-python-script.md).
  

In diesem Artikel wird anhand einer einfachen Pipeline erläutert, wie Sie **Create Python Model** verwenden. Unten ist der Graph zur Pipeline angegeben.

![create-python-model](./media/module/create-python-model.png)

1.  Klicken Sie auf **Create Python Model**, und bearbeiten Sie das Skript zum Implementieren Ihres Modellierungs- oder Datenverwaltungsprozesses. Sie können das Modell auf Grundlage jedes Learners erstellen, der in einem Python-Paket in der Azure Machine Learning-Umgebung enthalten ist.


    Hier sehen Sie einen Beispielcode für die Naive Bayes-Klassifizierung mit zwei Klassen, in dem das beliebte *sklearn*-Paket verwendet wird.

```Python

# The script MUST define a class named AzureMLModel.
# This class MUST at least define the following three methods:
    # __init__: in which self.model must be assigned,
    # train: which trains self.model, the two input arguments must be pandas DataFrame,
    # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
# The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


import pandas as pd
from sklearn.naive_bayes import GaussianNB


class AzureMLModel:
    def __init__(self):
        self.model = GaussianNB()
        self.feature_column_names = list()

    def train(self, df_train, df_label):
        self.feature_column_names = df_train.columns.tolist()
        self.model.fit(df_train, df_label)

    def predict(self, df):
        return pd.DataFrame(
            {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
             'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
        )


```


2. Verbinden Sie das soeben erstellte **Create Python Model**-Modul mit **Train Model** und **Score Model**.

3. Wenn Sie das Modell auswerten müssen, fügen Sie ein [Execute Python Script](execute-python-script.md)-Modul hinzu, und bearbeiten Sie das Python-Skript zum Implementieren der Auswertung.

Hier sehen Sie Beispielcode für die Auswertung.

```Python


# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to 
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):
    
    from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
    import pandas as pd
    import numpy as np
    
    scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
    ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
    ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
    probabilities = scores["probabilities"]
    
    accuracy, precision, recall, auc = \
    accuracy_score(ytrue, ypred),\
    precision_score(ytrue, ypred),\
    recall_score(ytrue, ypred),\
    roc_auc_score(ytrue, probabilities)
    
    metrics = pd.DataFrame();
    metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
    metrics["Value"] = [accuracy, precision, recall, auc]
    
    return metrics,

```
