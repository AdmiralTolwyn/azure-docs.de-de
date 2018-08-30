---
title: Verwenden von Apache Spark zum Analysieren von Daten in Azure Data Lake Store
description: Ausführen von Spark-Aufträgen zum Analysieren von in Azure Data Lake Store gespeicherten Daten
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.openlocfilehash: aae63b06999c0b8eafa0d42608d32a4d467a900c
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041060"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-store"></a>Verwenden des HDInsight Spark-Clusters, um Daten in Data Lake Store zu analysieren

In diesem Tutorial verwenden Sie das mit HDInsight Spark-Clustern verfügbare Jupyter Notebook, um einen Auftrag auszuführen, der Daten aus einem Data Lake Store-Konto liest.

## <a name="prerequisites"></a>Voraussetzungen

* Azure Data Lake Store-Konto. Befolgen Sie die Anweisungen unter [Erste Schritte mit Azure Data Lake Store über das Azure-Portal](../../data-lake-store/data-lake-store-get-started-portal.md).

* Azure HDInsight Spark-Cluster mit Data Lake Store als Speicher. Befolgen Sie die Anweisungen unter [Schnellstart: Einrichten von Hadoop-Clustern in HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    
## <a name="prepare-the-data"></a>Vorbereiten der Daten

> [!NOTE]
> Sie müssen diesen Schritt nicht ausführen, wenn Sie den HDInsight-Cluster mit Data Lake Store als Standardspeicher erstellt haben. Beim Clustererstellungsvorgang werden im Data Lake Store-Konto, das Sie beim Erstellen des Clusters angeben, einige Beispieldaten hinzugefügt. Fahren Sie mit dem Abschnitt [Verwenden eines HDInsight Spark-Clusters mit Data Lake Store](#use-an-hdinsight-spark-cluster-with-data-lake-store) fort.
>
>

Wenn Sie einen HDInsight-Cluster mit Data Lake Store als zusätzlichen Speicher und Azure Storage Blob als Standardspeicher erstellt haben, sollten Sie zuerst einige Beispieldaten in das Data Lake Store-Konto kopieren. Sie können die Beispieldaten aus dem Azure Storage Blob verwenden, die dem HDInsight-Cluster zugeordnet sind. Sie können hierfür das [AdlCopy-Tool](http://aka.ms/downloadadlcopy) verwenden. Laden Sie das Tool über den Link herunter, und installieren Sie es.

1. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie zu dem Verzeichnis, in dem „AdlCopy“ installiert ist, in der Regel `%HOMEPATH%\Documents\adlcopy`.

2. Führen Sie den folgenden Befehl zum Kopieren eines bestimmten Blobs aus dem Quellcontainer in einen Data Lake-Speicher aus:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Kopieren Sie die Beispieldatendatei **HVAC.csv** aus dem Pfad **/HdiSamples/HdiSamples/SensorSampleData/hvac/** in das Azure Data Lake Store-Konto. Der Codeausschnitt sollte folgendermaßen aussehen:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]
   > Stellen Sie sicher, dass die Groß-/Kleinschreibung für die Datei- und Pfadnamen beachtet wurde.
   >
   >
3. Sie werden aufgefordert, die Anmeldeinformationen für das Azure-Abonnement einzugeben, dem Ihr Data Lake Store-Konto zugeordnet ist. Es wird ungefähr folgender Codeausschnitt angezeigt:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Die Datendatei (**HVAC.csv**) wird in den Ordner **/hvac** im Data Lake Store-Konto kopiert.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-store"></a>Verwenden eines HDInsight Spark-Clusters mit Data Lake Store

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/)im Startmenü auf die Kachel für Ihren Spark-Cluster (sofern Sie die Kachel ans Startmenü angeheftet haben). Sie können auch unter **Alle durchsuchen** > **HDInsight-Cluster** zu Ihrem Cluster navigieren.

2. Klicken Sie auf dem Blatt für den Spark-Cluster auf **Quicklinks** und anschließend auf dem Blatt **Cluster-Dashboard** auf **Jupyter Notebook**. Geben Sie die Administratoranmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden.

   > [!NOTE]
   > Sie können auch das Jupyter Notebook für Ihren Cluster aufrufen, indem Sie in Ihrem Browser die folgende URL öffnen. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres Clusters:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Erstellen Sie ein neues Notebook. Klicken Sie auf **Neu** und dann auf **PySpark**.

    ![Erstellen eines neuen Jupyter Notebooks](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Erstellen eines neuen Jupyter Notebooks")

4. Da Sie ein Notebook mit dem PySpark-Kernel erstellt haben, müssen Sie keine Kontexte explizit erstellen. Die Spark-, und Hive-Kontexte werden automatisch für Sie erstellt, wenn Sie die erste Codezelle ausführen. Sie können zunächst die Typen importieren, die für dieses Szenario erforderlich sind. Fügen Sie dazu den folgenden Codeausschnitt in eine Zelle ein, und drücken Sie **UMSCHALT+EINGABE**.

        from pyspark.sql.types import *

    Bei jedem Ausführen eines Auftrags in Jupyter wird in der Titelleiste Ihres Webbrowserfensters neben dem Notebooktitel der Status **(Beschäftigt)** angezeigt. Außerdem sehen Sie in der rechten oberen Ecke einen ausgefüllten Kreis neben dem Text **PySpark** . Wenn der Auftrag abgeschlossen ist, wird ein Kreis ohne Füllung angezeigt.

     ![Status eines Jupyter Notebook-Auftrags](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Status eines Jupyter Notebook-Auftrags")

5. Verwenden Sie die Datei **HVAC.csv** , die Sie in das Data Lake Store-Konto kopiert haben, um Beispieldaten in eine temporäre Tabelle zu laden. Sie können mithilfe des folgenden URL-Musters auf die Daten im Data Lake-Speicherkonto zugreifen.

    * Wenn Sie Data Lake Store als Standardspeicher verwenden, befindet sich „HVAC.csv“ in dem Pfad, der folgender URL entspricht:

            adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

        Alternativ können Sie auch ein verkürztes Format wie das folgende verwenden:

            adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

    * Wenn Sie Data Lake Store als zusätzlichen Speicher verwenden, befindet sich „HVAC.csv“ dort, wohin Sie sie kopiert haben, z.B.:

            adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     Fügen Sie das folgende Codebeispiel in eine leere Zelle ein, ersetzen Sie **MYDATALAKESTORE** durch den Namen Ihres Data Lake Store-Kontos, und drücken Sie **UMSCHALT+EINGABETASTE**. Mit diesem Beispielcode werden die Daten in einer temporären Tabelle mit dem Namen **hvac**registriert.

            # Load the data. The path below assumes Data Lake Store is default storage for the Spark cluster
            hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            # Create the schema
            hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

            # Parse the data in hvacText
            hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

            # Create a data frame
            hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

            # Register the data fram as a table to run queries against
            hvacdf.registerTempTable("hvac")

6. Da Sie einen PySpark-Kernel verwenden, können Sie jetzt direkt eine SQL-Abfrage für die temporäre Tabelle **hvac** ausführen, die Sie gerade mit der Magic `%%sql` erstellt haben. Weitere Informationen zur `%%sql` -Magic sowie anderen für den PySpark-Kernel verfügbaren Magics finden Sie unter [Verfügbare Kernels für Jupyter Notebooks mit Spark-Clustern unter HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. Nachdem der Auftrag erfolgreich abgeschlossen wurde, wird die folgende tabellarische Ausgabe standardmäßig angezeigt.

      ![Tabellenausgabe des Abfrageergebnisses](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Tabellenausgabe des Abfrageergebnisses")

     Sie können die Ergebnisse auch in anderen Visualisierungen anzeigen. Beispielsweise würde ein Bereichsdiagramm für dieselbe Ausgabe wie folgt aussehen.

     ![Bereichsdiagramm des Abfrageergebnisses](./media/apache-spark-use-with-data-lake-store/jupyter-area-output.png "Bereichsdiagramm des Abfrageergebnisses")

8. Nach Ausführen der Anwendung empfiehlt es sich, das Notebook herunterzufahren, um die Ressourcen freizugeben. Klicken Sie hierzu im Menü **Datei** des Notebooks auf die Option zum **Schließen und Anhalten**. Dadurch wird das Notebook heruntergefahren und geschlossen.


## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer eigenständigen Scala-Anwendung zur Ausführung in einem Apache Spark-Cluster](apache-spark-create-standalone-application.md)
* [Verwenden der HDInsight-Tools im Azure-Toolkit für IntelliJ zum Erstellen von Spark-Anwendungen für HDInsight Spark-Cluster unter Linux](apache-spark-intellij-tool-plugin.md)
* [Verwenden der HDInsight-Tools im Azure-Toolkit für Eclipse zum Erstellen von Spark-Anwendungen für HDInsight Spark-Cluster unter Linux](apache-spark-eclipse-tool-plugin.md)
