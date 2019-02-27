---
title: Hochladen von Daten für Apache Hadoop-Aufträge in HDInsight
description: Erfahren Sie, wie Sie Daten für Apache Hadoop-Aufträge in HDInsight mit der klassischen Azure-Befehlszeilenschnittstelle, Azure Storage-Explorer, Azure PowerShell, der Hadoop-Befehlszeile oder Sqoop hochladen und abrufen können.
keywords: ETL Hadoop, Importieren von Daten in Hadoop, Laden von Daten in Hadoop
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/08/2019
ms.openlocfilehash: 513cc1f0155c5e5499d0bf076d21aff46756d769
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312173"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Hochladen von Daten für Apache Hadoop-Aufträge in HDInsight

Azure HDInsight stellt über Azure Storage und Azure Data Lake Storage (Gen1 und Gen2) ein Hadoop Distributed File System (HDFS) mit vollem Funktionsumfang zur Verfügung. Azure Storage und Data Lake Storage Gen1 und Gen2 sind als HDFS-Erweiterungen konzipiert, die eine nahtlose Benutzererfahrung bieten. Der vollständige Satz von Komponenten im Hadoop-Ökosystem kann direkt für die damit verwalteten Daten verwendet werden. Azure Storage und Data Lake Storage Gen1 und Gen2 sind unterschiedliche Dateisysteme, die jeweils für die Datenspeicherung und Berechnungen dieser Daten optimiert sind. Die Vorteile der Verwendung von Azure Storage werden unter [Verwenden von Azure Storage mit HDInsight][hdinsight-storage], [Verwenden von Date Lake Storage Gen1 mit HDInsight](hdinsight-hadoop-use-data-lake-store.md) und [Verwenden von Data Lake Storage Gen2 mit HDInsight](../storage/blobs/data-lake-storage-use-hdi-cluster.md) beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

Beachten Sie die folgenden Anforderungen, bevor Sie beginnen:

* Ein Azure HDInsight-Cluster. Anweisungen finden Sie in den Artikeln zu den [ersten Schritten mit Azure HDInsight][hdinsight-get-started] oder zum [Erstellen von HDInsight-Clustern](hdinsight-hadoop-provision-linux-clusters.md).
* Kenntnis der folgenden Artikel:

    - [Verwenden von Azure Storage mit HDInsight][hdinsight-storage]
    - [Verwenden von Data Lake Storage Gen1 mit HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    - [Verwenden von Data Lake Storage Gen2 mit HDInsight](../storage/blobs/data-lake-storage-use-hdi-cluster.md)  

## <a name="upload-data-to-azure-storage"></a>Hochladen von Daten in Azure Storage

## <a name="utilities"></a>Versorgungsunternehmen
Microsoft bietet die folgenden Hilfsprogramme für die Arbeit mit Azure Storage:

| Tool | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure-Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure-Befehlszeilenschnittstelle](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop-Befehl](#commandline) |✔ |✔ |✔ |


> [!NOTE]  
> Der Hadoop-Befehl ist nur für den HDInsight-Cluster verfügbar. Mit dem Befehl können nur Daten aus dem lokalen Dateisystem in Azure Storage geladen werden.  


## <a id="commandline"></a>Hadoop-Befehlszeile
Die Hadoop-Befehlszeile eignet sich nur dann zum Speichern von Daten in Azure Storage Blob, wenn die Daten bereits auf dem Hauptknoten des Clusters vorhanden sind.

Um den Hadoop-Befehl verwenden zu können, müssen Sie zunächst mithilfe einer der folgenden Methoden eine Verbindung zum Hauptknoten herstellen:

* **HDInsight (Windows-basiert):** [Herstellen einer Remotedesktopverbindung](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **HDInsight (Linux-basiert):** Herstellen einer Verbindung mithilfe von [SSH oder PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md)

Nachdem die Verbindung hergestellt wurde, verwenden Sie die folgende Syntax, um eine Datei in den Speicher hochzuladen.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Zum Beispiel, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Da sich das Standarddateisystem für HDInsight in Azure Storage befindet, befindet sich die Datei „/example/data.txt“ auch tatsächlich in Azure Storage. Sie können auch folgendermaßen auf die Datei verweisen:

    wasb:///example/data/data.txt

oder

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Eine Liste mit weiteren Hadoop-Befehlen, die für Dateien verwendet werden können, finden Sie unter [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html).

> [!WARNING]  
> In Apache HBase-Clustern beträgt die standardmäßige Blockgröße beim Schreiben von Daten 256 KB. Bei Verwendung von HBase-APIs oder REST-APIs funktioniert dies einwandfrei. Die Nutzung der Befehle `hadoop` oder `hdfs dfs` zum Schreiben von Daten mit einem Umfang von mehr als ca. 12 GB führt allerdings zu einem Fehler. Weitere Informationen finden Sie im Abschnitt [Speicherausnahme beim Schreiben in ein Blob](#storageexception) dieses Artikels.

## <a name="graphical-clients"></a>Clients mit grafischer Benutzeroberfläche
Es gibt auch einige Anwendungen, die eine grafische Benutzeroberfläche für die Arbeit mit Azure Storage bereitstellen. In der folgenden Tabelle sind einige dieser Anwendungen aufgelistet:

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio-Tools für HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage-Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer für Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |


## <a name="mount-azure-storage-as-local-drive"></a>Einbinden von Azure Storage als lokales Laufwerk
Siehe [Mount Azure Storage as Local Drive](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx) (Einbinden von Azure Storage als lokales Laufwerk).

## <a name="upload-using-services"></a>Hochladen mithilfe von Diensten
### <a name="azure-data-factory"></a>Azure Data Factory
Der Azure Data Factory-Dienst ist ein vollständig verwalteter Dienst für das Kombinieren von Verarbeitungs-, Speicher- und Verschiebungsdienste für Daten in optimierten, skalierbaren und zuverlässigen Datenproduktions-Pipelines.

|Speichertyp|Dokumentation|
|----|----|
|Azure Blob Storage|[Kopieren von Daten nach oder aus Azure Blob Storage mit Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Kopieren von Daten nach und aus Azure Data Lake Storage Gen1 mithilfe von Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Laden von Daten in Azure Data Lake Storage Gen2 mit Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop
Sqoop ist ein Tool zum Übertragen von Daten zwischen Hadoop und relationalen Datenbanken. Sie können damit Daten aus einem Managementsystem für relationale Datenbanken (RDBMS) wie SQL Server, MySQL oder Oracle in das verteilte Dateisystem von Hadoop (HDFS) importieren, die Daten in Hadoop mit MapReduce oder Hive transformieren und sie anschließend wieder in ein RDBMS exportieren.

Weitere Informationen finden Sie unter [Verwenden von Sqoop mit HDInsight][hdinsight-use-sqoop].

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
### <a id="storageexception"></a>Speicherausnahme beim Schreiben in ein Blob
**Symptome:** Wenn Sie die Befehle `hadoop` oder `hdfs dfs` verwenden, um Dateien mit einer Größe von etwa 12 GB oder mehr in einem HBase-Cluster zu schreiben, wird möglicherweise der folgende Fehler angezeigt:

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

**Lösung:** Verwenden Sie `fs.azure.write.request.size`, um eine größere Blockgröße anzugeben. Sie können dies auf Nutzungsbasis mithilfe des `-D`-Parameters erreichen. Im folgenden Beispielbefehl wird dieser Parameter mit dem Befehl `hadoop` verwendet:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Sie können auch den Wert von `fs.azure.write.request.size` mithilfe von Apache Ambari global erhöhen. Die folgenden Schritte können verwendet werden, um den Wert in der Ambari-Webbenutzeroberfläche zu ändern:

1. Öffnen Sie in Ihrem Browser die Ambari-Webbenutzeroberfläche für den Cluster. Diese befindet sich unter https://CLUSTERNAME.azurehdinsight.net, wobei **CLUSTERNAME** der Name Ihres Clusters ist.

    Geben Sie bei entsprechender Aufforderung den Namen und das Kennwort des Administrators für den Cluster ein.
2. Wählen Sie auf der linken Bildschirmseite **HDFS** und dann die Registerkarte **Configs** aus.
3. Geben Sie in das Feld **Filter** den Text `fs.azure.write.request.size` ein. Dadurch werden das Feld und der aktuelle Wert in der Mitte der Seite angezeigt.
4. Ändern Sie den Wert von 262144 (256 KB) in den neuen Wert. Beispiel: 4194304 (4 MB).

![Abbildung der Änderung des Werts über die Ambari-Webbenutzeroberfläche](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Weitere Informationen zur Verwendung von Ambari finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Nächste Schritte
Jetzt wissen Sie, wie Sie Daten in HDInsight importieren. Lesen Sie in den folgenden Artikeln, wie Sie die Daten analysieren:

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Programmgesteuertes Übermitteln von Apache Hadoop-Aufträgen][hdinsight-submit-jobs]
* [Verwenden von Apache Hive mit HDInsight][hdinsight-use-hive]
* [Verwenden von Apache Pig mit HDInsight][hdinsight-use-pig]

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
