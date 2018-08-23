---
title: Importieren von Daten in Azure Machine Learning Studio aus einem anderen Experiment | Microsoft Docs
description: Speichern von Trainingsdaten in Azure Machine Learning Studio und Verwenden der Daten in einem anderen Experiment.
keywords: Importieren von Daten,Daten,Datenquellen,Trainingsdaten
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 7da9dcec-5693-4bb6-8166-15904e7f75c3
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 5007657da11cb45c6511780398c5425d14dca900
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246571"
---
# <a name="import-your-data-into-azure-machine-learning-studio-from-another-experiment"></a>Importieren der Daten aus einem anderen Experiment in Azure Machine Learning Studio
[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

Hin und wieder wird ein Zwischenergebnis aus einem Experiment benötigt, um es als Teil eines anderen Experiments zu verwenden. Dazu speichern Sie das Modul als Dataset:

1. Klicken Sie auf die Ausgabe des Moduls, die Sie als Dataset speichern möchten.
2. Klicken Sie auf **Save as Dataset**.
3. Wenn Sie dazu aufgefordert werden, geben Sie einen Namen und eine Beschreibung ein, mit denen das Dataset leicht wiederzuerkennen ist.
4. Klicken Sie auf das Häkchen **OK** .

Wenn der Speichervorgang abgeschlossen ist, ist das Dataset für die Verwendung in allen Experimenten in Ihrem Arbeitsbereich verfügbar. Sie finden es in der Liste **Saved Datasets** in der Modulpalette.

