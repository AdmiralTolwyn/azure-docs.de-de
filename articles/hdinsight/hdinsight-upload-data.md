---
title: Hochladen von Daten für Apache Hadoop-Aufträge in HDInsight
description: Erfahren Sie, wie Sie Daten für Apache Hadoop-Aufträge in HDInsight hochladen und darauf zugreifen können. Verwenden Sie die klassische Azure-Befehlszeilenschnittstelle, Azure Storage-Explorer, Azure PowerShell, die Hadoop-Befehlszeile oder Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdiseo17may2017
ms.date: 04/07/2020
ms.openlocfilehash: c862633245e75613f9e4f9956486f872b96239f8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875009"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Hochladen von Daten für Apache Hadoop-Aufträge in HDInsight

HDInsight stellt über Azure Storage und Azure Data Lake Storage ein Hadoop Distributed File System (HDFS) zur Verfügung. Dieser Speicher umfasst Gen1 und Gen2. Azure Storage und Data Lake Storage Gen1 und Gen2 sind als HDFS-Erweiterungen konzipiert. Der vollständige Satz von Komponenten in der Hadoop-Umgebung kann direkt für die damit verwalteten Daten verwendet werden. Azure Storage, Data Lake Storage Gen1 und Gen2 sind unterschiedliche Dateisysteme. Die Systeme sind für die Speicherung von Daten und für Berechnungen mit diesen Daten optimiert. Die Vorteile der Verwendung von Azure Storage werden unter [Verwenden von Azure Storage mit HDInsight](hdinsight-hadoop-use-blob-storage.md) beschrieben. Weitere Informationen finden Sie auch unter [Verwenden von Data Lake Storage Gen1 mit HDInsight](hdinsight-hadoop-use-data-lake-store.md) und [Verwenden von Data Lake Storage Gen2 mit HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Voraussetzungen

Beachten Sie die folgenden Anforderungen, bevor Sie beginnen:

* Ein Azure HDInsight-Cluster. Anweisungen finden Sie in den Artikeln zu den [ersten Schritten mit Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) oder zum [Erstellen von HDInsight-Clustern](hdinsight-hadoop-provision-linux-clusters.md).
* Kenntnis der folgenden Artikel:
    * [Verwenden von Azure Storage mit HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Verwenden von Data Lake Storage Gen1 mit HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    * [Verwenden von Data Lake Storage Gen2 mit HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Hochladen von Daten in Azure Storage

## <a name="utilities"></a>Versorgungsunternehmen

Microsoft bietet die folgenden Hilfsprogramme für die Arbeit mit Azure Storage:

| Tool | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure-Befehlszeilenschnittstelle](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop-Befehl](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> Der Hadoop-Befehl ist nur für den HDInsight-Cluster verfügbar. Mit dem Befehl können nur Daten aus dem lokalen Dateisystem in Azure Storage geladen werden.  

## <a name="hadoop-command-line"></a>Hadoop-Befehlszeile

Die Hadoop-Befehlszeile eignet sich nur dann zum Speichern von Daten in Azure Storage Blob, wenn die Daten bereits auf dem Hauptknoten des Clusters vorhanden sind.

Um den Hadoop-Befehl verwenden zu können, müssen Sie zunächst mithilfe von [SSH oder PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md) eine Verbindung mit dem Hauptknoten herstellen.

Nachdem die Verbindung hergestellt wurde, verwenden Sie die folgende Syntax, um eine Datei in den Speicher hochzuladen.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Zum Beispiel, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Da sich das Standarddateisystem für HDInsight in Azure Storage befindet, befindet sich die Datei „/example/data/data.txt“ auch tatsächlich in Azure Storage. Sie können auch folgendermaßen auf die Datei verweisen:

    wasbs:///example/data/data.txt

oder

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Eine Liste mit weiteren Hadoop-Befehlen, die für Dateien verwendet werden können, finden Sie unter [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html).

> [!WARNING]  
> In Apache HBase-Clustern beträgt die standardmäßige Blockgröße beim Schreiben von Daten 256 KB. Bei Verwendung von HBase-APIs oder REST-APIs funktioniert dies einwandfrei. Die Nutzung der Befehle `hadoop` oder `hdfs dfs` zum Schreiben von Daten mit einem Umfang von mehr als ca. 12 GB führt allerdings zu einem Fehler. Weitere Informationen finden Sie im Abschnitt [Speicherausnahme beim Schreiben in ein Blob](#storage-exception-for-write-on-blob) dieses Artikels.

## <a name="graphical-clients"></a>Clients mit grafischer Benutzeroberfläche

Es gibt auch einige Anwendungen, die eine grafische Benutzeroberfläche für die Arbeit mit Azure Storage bereitstellen. In der folgenden Tabelle sind einige dieser Anwendungen aufgelistet:

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio-Tools für HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage-Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer für Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Einbinden von Azure Storage als lokales Laufwerk

Siehe [Mount Azure Storage as Local Drive](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx) (Einbinden von Azure Storage als lokales Laufwerk).

## <a name="upload-using-services"></a>Hochladen mithilfe von Diensten

### <a name="azure-data-factory"></a>Azure Data Factory

Der Azure Data Factory-Dienst ist ein vollständig verwalteter Dienst für das Kombinieren von Daten: Speicherung, Verarbeitung und Verschiebung von Diensten in optimierte, anpassbare und zuverlässige Datenproduktionspipelines.

|Speichertyp|Dokumentation|
|----|----|
|Azure Blob Storage|[Kopieren von Daten nach oder aus Azure Blob Storage mit Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Kopieren von Daten nach und aus Azure Data Lake Storage Gen1 mithilfe von Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Laden von Daten in Azure Data Lake Storage Gen2 mit Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop ist ein Tool zum Übertragen von Daten zwischen Hadoop und relationalen Datenbanken. Verwenden Sie es, um Daten aus einem Managementsystem für relationale Datenbanken (RDBMS) wie SQL Server, MySQL oder Oracle zu importieren. Importieren Sie sie dann in das Hadoop Distributed File System (HDFS). Transformieren Sie die Daten in Hadoop mit MapReduce oder Hive, und exportieren Sie die Daten dann wieder in ein RDBMS.

Weitere Informationen finden Sie unter [Verwenden von Sqoop mit HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>Entwicklungs-SDKs

Auf Azure Storage kann auch mithilfe eines Azure-SDK über die folgenden Programmiersprachen zugegriffen werden:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Weitere Informationen zum Installieren der Azure-SDKs finden Sie unter [Azure-Downloads](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Problembehandlung

### <a name="storage-exception-for-write-on-blob"></a>Speicherausnahme beim Schreiben in ein Blob

**Symptome:** Wenn Sie die Befehle `hadoop` oder `hdfs dfs` verwenden, um Dateien mit einer Größe von etwa 12 GB oder mehr in einem HBase-Cluster zu schreiben, wird möglicherweise der folgende Fehler angezeigt:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Ursache:** Für HBase in HDInsight-Cluster wird beim Schreiben in den Azure-Speicher standardmäßig eine Blockgröße von 256 KB verwendet. Dies funktioniert bei HBase-APIs oder REST-APIs, bei der Verwendung der Befehlszeilenprogramme `hadoop` oder `hdfs dfs` tritt jedoch ein Fehler auf.

**Lösung:** Verwenden Sie `fs.azure.write.request.size`, um eine größere Blockgröße anzugeben. Sie können diese Änderung auf Nutzungsbasis mithilfe des `-D`-Parameters erreichen. Im folgenden Beispielbefehl wird dieser Parameter mit dem Befehl `hadoop` verwendet:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Sie können auch den Wert von `fs.azure.write.request.size` mithilfe von Apache Ambari global erhöhen. Die folgenden Schritte können verwendet werden, um den Wert in der Ambari-Webbenutzeroberfläche zu ändern:

1. Öffnen Sie in Ihrem Browser die Ambari-Webbenutzeroberfläche für den Cluster. Die URL befindet sich unter `https://CLUSTERNAME.azurehdinsight.net`, wobei `CLUSTERNAME` der Name Ihres Clusters ist. Geben Sie bei entsprechender Aufforderung den Namen und das Kennwort des Administrators für den Cluster ein.
2. Wählen Sie auf der linken Bildschirmseite **HDFS** und dann die Registerkarte **Configs** aus.
3. Geben Sie in das Feld **Filter** den Text `fs.azure.write.request.size` ein.
4. Ändern Sie den Wert von 262144 (256 KB) in den neuen Wert. Beispiel: 4194304 (4 MB).

    ![Abbildung der Änderung des Werts über die Ambari-Webbenutzeroberfläche](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Weitere Informationen zur Verwendung von Ambari finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Nächste Schritte

Jetzt wissen Sie, wie Sie Daten in HDInsight importieren. Lesen Sie in den folgenden Artikeln, um mehr über die Analyse zu erfahren:

* [Erste Schritte mit Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Programmgesteuertes Übermitteln von Apache Hadoop-Aufträgen](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Verwenden von Apache Hive mit HDInsight](hadoop/hdinsight-use-hive.md)
* [Verwenden von Apache Pig mit HDInsight](./use-pig.md)
