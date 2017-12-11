---
title: Erstellen eines Apache Spark-Clusters in Azure HDInsight | Microsoft-Dokumentation
description: HDInsight Spark-Schnellstartanleitung zum Erstellen eines Apache Spark-Clusters in HDInsight.
keywords: Spark Schnellstart,interaktiv Spark,interaktive Abfrage,HDInsight Spark,Azure Spark
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 516c48424ef5d1256296240541fb544c1e5d9205
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2017
---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Erstellen eines Apache Spark-Clusters in Azure HDInsight

Hier erfahren Sie, wie Sie einen Apache Spark-Cluster in Azure HDInsight erstellen und Spark SQL-Abfragen für Hive-Tabellen ausführen. Informationen zu Spark in HDInsight finden Sie unter [Übersicht: Apache Spark in Azure HDInsight](apache-spark-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Für dieses Tutorial wird ein Azure-Abonnement benötigt. Weitere Informationen finden Sie unter [Erstellen Sie noch heute Ihr kostenloses Azure-Konto](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>Erstellen eines HDInsight Spark-Clusters

Erstellen Sie einen HDInsight Spark-Cluster unter Verwendung einer [Azure Resource Manager-Vorlage](../hdinsight-hadoop-create-linux-clusters-arm-templates.md). Die Vorlage finden Sie in [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Andere Methoden zur Erstellung von Clustern finden Sie unter [Erstellen von Linux-basierten Hadoop-Clustern in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Klicken Sie auf die folgende Abbildung, um die Vorlage im Azure-Portal zu öffnen:         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Geben Sie die folgenden Werte ein:

    ![Erstellen eines HDInsight Spark-Clusters mit einer Azure Resource Manager-Vorlage](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Erstellen eines Spark-Clusters in HDInsight mit einer Azure Resource Manager-Vorlage")

    * **Abonnement**: Wählen Sie Ihr Azure-Abonnement für die Erstellung des Clusters aus.
    * **Ressourcengruppe**: Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine vorhandene aus. Die Ressourcengruppe wird verwendet, um Azure-Ressourcen für Ihre Projekte zu verwalten.
    * **Standort**: Wählen Sie einen Standort für die Ressourcengruppe aus. Die Vorlage verwendet diesen Standort sowohl für die Erstellung des Clusters als auch für den Standardclusterspeicher.
    * **Clustername**: Geben Sie einen Namen für den HDInsight-Cluster ein, den Sie erstellen möchten.
    * **Cluster-Benutzername und -Kennwort**: Der Standardname für die Anmeldung lautet „admin“.
    * **SSH-Benutzername und -Kennwort**.

3. Aktivieren Sie die Optionen **Ich stimme den oben genannten Geschäftsbedingungen zu** und **An Dashboard anheften**, und klicken Sie anschließend auf **Kaufen**. Daraufhin wird eine neue Kachel mit der Bezeichnung „Bereitstellung für Vorlagenbereitstellung wird gesendet“ angezeigt. Das Erstellen des Clusters dauert ca. 20 Minuten.

Sollte bei der HDInsight-Clustererstellung ein Problem auftreten, verfügen Sie unter Umständen nicht über die erforderlichen Berechtigungen. Weitere Informationen finden Sie unter [Voraussetzungen für die Zugriffssteuerung](../hdinsight-administer-use-portal-linux.md#create-clusters).

> [!NOTE]
> In diesem Artikel wird ein Spark-Cluster erstellt, der [Azure Storage-Blobs als Clusterspeicher](../hdinsight-hadoop-use-blob-storage.md) einsetzt. Sie können auch einen Spark-Cluster erstellen, der [Azure Data Lake Store](../hdinsight-hadoop-use-data-lake-store.md) als Standardspeicher verwendet. Anweisungen hierzu finden Sie unter [Erstellen eines HDInsight-Clusters mit Data Lake-Speicher](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

## <a name="create-a-jupyter-notebook"></a>Erstellen eines Jupyter Notebooks

[Jupyter Notebook](http://jupyter.org) ist eine interaktive Notebookumgebung, die verschiedene Programmiersprachen unterstützt, mit denen Sie mit Ihren Daten interagieren, Code mit Markdowntext kombinieren und einfache Visualisierungen durchführen können. Spark in HDInsight enthält auch [Zeppelin Notebook](apache-spark-zeppelin-notebook.md). In diesem Tutorial wird Jupyter Notebook verwendet.

**So erstellen Sie ein Jupyter Notebook**

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).

2. Öffnen Sie den Spark-Cluster, den Sie erstellt haben. Eine entsprechende Anleitung finden Sie unter [Auflisten und Anzeigen von Clustern](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

3. Klicken Sie unter **Quicklinks** auf **Clusterdashboards** und dann auf **Jupyter Notebook**. Geben Sie die Administratoranmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden.

   ![Öffnen von Jupyter Notebook zum Ausführen einer interaktiven Spark SQL-Abfrage](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Öffnen von Jupyter Notebook zum Ausführen einer interaktiven Spark SQL-Abfrage")

   > [!NOTE]
   > Sie können auch auf das Jupyter Notebook für Ihren Cluster zugreifen, indem Sie in Ihrem Browser die folgende URL öffnen. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres Clusters:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Klicken Sie auf **Neu** und anschließend auf **PySpark**, um ein Notebook zu erstellen. Jupyter Notebooks in HDInsight-Clustern unterstützen drei Kernels: **PySpark**, **PySpark3** und **Spark**. In diesem Tutorial wird der Kernel **PySpark** verwendet. Weitere Informationen zu den Kernels und den Vorteilen von **PySpark**finden Sie unter [Kernel für Jupyter-Notebook in Spark-Clustern in Azure HDInsight](apache-spark-jupyter-notebook-kernels.md).

   ![Erstellen eines Jupyter Notebooks zum Ausführen einer interaktiven Spark SQL-Abfrage](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Erstellen eines Jupyter Notebooks zum Ausführen einer interaktiven Spark SQL-Abfrage")

   Ein neues Notebook mit dem Namen „Untitled“ (Untitled.pynb) wird erstellt und geöffnet.

4. Klicken Sie oben auf den Namen des Notebooks, und geben Sie bei Bedarf einen Anzeigenamen ein.

    ![Eingeben eines Namens für das Jupyter Notebook, über das eine interaktive Spark-Abfrage ausgeführt werden soll](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Eingeben eines Namens für das Jupyter Notebook, über das eine interaktive Spark-Abfrage ausgeführt werden soll")

## <a name="run-spark-sql-statements-on-a-hive-table"></a>Ausführen von Spark SQL-Anweisungen in einer Hive-Tabelle

SQL (Structured Query Language) ist die gängigste und am häufigsten verwendete Sprache zum Abfragen und Definieren von Daten. Spark SQL fungiert als Erweiterung von Apache Spark für die Verarbeitung strukturierter Daten mit der vertrauten SQL-Syntax.

Spark SQL unterstützt sowohl SQL als auch HiveQL als Abfragesprache. Die Funktionen umfassen auch die Einbindung in Python, Scala und Java. Hiermit können Sie Daten an unterschiedlichen Orten abfragen, z.B. externe Datenbanken, strukturierte Datendateien (z.B. JSON) und Hive-Tabellen.

Ein Beispiel für das Lesen von Daten aus einer CSV-Datei anstelle einer Hive-Tabelle finden Sie unter [Ausführen interaktiver Abfragen in einem HDInsight Spark-Cluster](apache-spark-load-data-run-query.md).

**So führen Sie Spark SQL aus**

1.  Fügen Sie über das Notebook den folgenden Code in eine leere Zelle ein, und drücken Sie **UMSCHALT+EINGABE**, um den Code auszuführen. 

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    ![Hive-Abfrage in HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Hive-Abfrage in HDInsight Spark")

    Wenn Sie ein Jupyter Notebook mit Ihrem HDInsight Spark-Cluster verwenden, erhalten Sie einen vordefinierten `sqlContext`, den Sie zum Ausführen von Hive-Abfragen mit Spark SQL verwenden können. `%%sql` weist Jupyter Notebook an, den vordefinierten `sqlContext` für die Ausführung der Hive-Abfrage zu verwenden. Die Abfrage ruft die ersten zehn Zeilen aus einer Hive-Tabelle (**hivesampletable**) ab, die standardmäßig in allen HDInsight-Clustern enthalten ist. Weitere Informationen zu `%%sql` und den vordefinierten Kontexten finden Sie unter [Kernel für Jupyter-Notebook in Spark-Clustern in Azure HDInsight](apache-spark-jupyter-notebook-kernels.md).

    Bei jeder Ausführung einer Abfrage in Jupyter wird auf der Titelleiste Ihres Webbrowserfensters neben dem Notebooktitel der Status **(Beschäftigt)** angezeigt. Außerdem sehen Sie in der rechten oberen Ecke einen ausgefüllten Kreis neben dem Text **PySpark**. Wenn der Auftrag abgeschlossen ist, wird ein Kreis ohne Füllung angezeigt.
    
    Der Bildschirm wird aktualisiert, und die Ausgabe der Abfrage wird angezeigt.

    ![Hive-Abfrageausgabe in HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Hive-Abfrageausgabe in HDInsight Spark")

2. Klicken Sie hierzu im Menü **Datei** des Notebooks auf **Close and Halt** (Schließen und Anhalten). Durch Herunterfahren des Notebooks werden die Clusterressourcen freigegeben.

3. Falls Sie die nächsten Schritte erst zu einem späteren Zeitpunkt ausführen möchten, löschen Sie den in diesem Artikel erstellten HDInsight-Cluster wieder. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-step"></a>Nächster Schritt 

In diesem Artikel haben Sie gelernt, wie Sie einen HDInsight Spark-Cluster erstellen und eine einfache Spark SQL-Abfrage ausführen. Im nächsten Artikel erfahren Sie, wie Sie mithilfe eines HDInsight Spark-Clusters interaktive Abfragen für Beispieldaten ausführen.

> [!div class="nextstepaction"]
>[Ausführen interaktiver Abfragen in einem HDInsight Spark-Cluster](apache-spark-load-data-run-query.md)



