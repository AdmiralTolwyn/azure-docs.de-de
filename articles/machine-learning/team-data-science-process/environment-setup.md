---
title: Einrichten von Data Science-Umgebungen in Azure | Microsoft-Dokumentation
description: Einrichten von Data Science-Umgebungen in Azure für die Verwendung im Team Data Science-Prozess.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: c8154400621fae719c7097e36c8e14d3f946d7c2
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52445906"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Einrichten von Data Science-Umgebungen für die Verwendung im Team Data Science-Prozess
Der Team Data Science-Prozess verwendet verschiedene Data Science-Umgebungen für die Speicherung, Verarbeitung und Analyse von Daten. Dazu zählen Azure Blob Storage, verschiedene Typen von virtuellen Azure-Computern, HDInsight (Hadoop)-Cluster und Azure Machine Learning-Arbeitsbereiche. Die Entscheidung, welche Art von Umgebung Sie verwenden, hängt von der Art und der Menge der Daten ab, die modelliert werden sollen, sowie vom Ziel für die Daten in der Cloud. 

* Hilfe bei dieser Entscheidung finden Sie unter [Planen der Azure Machine Learning Data Science-Umgebung](plan-your-environment.md). 
* Einen Katalog mit einigen Szenarien für die erweiterte Analyse finden Sie unter [Szenarien für den Team Data Science-Prozess](plan-sample-scenarios.md)

In den folgenden Artikeln wird das Einrichten der verschiedenen Data Science-Umgebungen beschrieben, die vom Team Data Science-Prozess verwendet werden.

* [Azure Storage-Konto](../../storage/common/storage-quickstart-create-account.md)
* [HDInsight (Hadoop)-Cluster](customize-hadoop-cluster.md)
* [Azure Machine Learning Studio-Arbeitsbereich](../studio/create-workspace.md)

Die **Microsoft Data Science Virtual Machine (DSVM)** ist auch als Image des virtuellen Azure-Computers (VM) verfügbar. Diese VM ist vorinstalliert und mit einigen gängigen Tools konfiguriert, die häufig für Datenanalysen und Machine Learning verwendet werden. Die DSVM steht unter Windows und Linux zur Verfügung. Weitere Informationen finden Sie unter [Einführung in den cloudbasierten virtuellen Computer für Data Science für Linux und Windows](../data-science-virtual-machine/overview.md).

Informieren Sie sich darüber, wie Sie Folgendes erstellen:

- [DSVM unter Windows](../data-science-virtual-machine/provision-vm.md)
- [DSVM unter Ubuntu](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [DSVM unter CentOS](../data-science-virtual-machine/linux-dsvm-intro.md)
- [Virtueller Deep Learning-Computer](../data-science-virtual-machine/provision-deep-learning-dsvm.md)
