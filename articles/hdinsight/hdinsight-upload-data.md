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
ms.date: 11/06/2018
ms.openlocfilehash: a54c47c0f67052f2ce486a97e009293a118919d4
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994112"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Hochladen von Daten für Apache Hadoop-Aufträge in HDInsight

Azure HDInsight stellt über Azure Storage und Azure Data Lake Storage (Gen1 und Gen2) ein Hadoop Distributed File System (HDFS) mit vollem Funktionsumfang zur Verfügung. Azure Storage und Data Lake Storage Gen1 und Gen2 sind als HDFS-Erweiterungen konzipiert, die eine nahtlose Benutzererfahrung bieten. Der vollständige Satz von Komponenten im Hadoop-Ökosystem kann direkt für die damit verwalteten Daten verwendet werden. Azure Storage und Data Lake Storage Gen1 und Gen2 sind unterschiedliche Dateisysteme, die jeweils für die Datenspeicherung und Berechnungen dieser Daten optimiert sind. Die Vorteile der Verwendung von Azure Storage werden unter [Verwenden von Azure Storage mit HDInsight][hdinsight-storage], [Verwenden von Date Lake Storage Gen1 mit HDInsight](hdinsight-hadoop-use-data-lake-store.md) und [Verwenden von Data Lake Storage Gen2 mit HDInsight](../storage/data-lake-storage/use-hdi-cluster.md) beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

Beachten Sie die folgenden Anforderungen, bevor Sie beginnen:

* Ein Azure HDInsight-Cluster. Anweisungen finden Sie in den Artikeln zu den [ersten Schritten mit Azure HDInsight][hdinsight-get-started] oder zum [Erstellen von HDInsight-Clustern](hdinsight-hadoop-provision-linux-clusters.md).
* Kenntnis der folgenden zwei Artikel:

    - [Verwenden von Azure Storage mit HDInsight][hdinsight-storage]
    - [Verwenden von Data Lake Storage Gen1 mit HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    - [Verwenden von Data Lake Storage Gen2 mit HDInsight](../storage/data-lake-storage/use-hdi-cluster.md)   

## <a name="upload-data-to-azure-storage"></a>Hochladen von Daten in Azure Storage

### <a name="command-line-utilities"></a>Befehlszeilenprogramme
Microsoft bietet die folgenden Hilfsprogramme für die Arbeit mit Azure Storage:

| Tool | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Klassische Azure CLI][azurecli] |✔ |✔ |✔ |
| [Azure PowerShell][azure-powershell] | | |✔ |
| [AzCopy][azure-azcopy] |✔ | |✔ |
| [Hadoop-Befehl](#commandline) |✔ |✔ |✔ |

> [!NOTE]  
> Während die klassische Azure CLI, Azure PowerShell und AzCopy außerhalb von Azure verwendet werden können, ist der Hadoop-Befehl nur im HDInsight-Cluster verfügbar. Zudem können mit dem Befehl nur Daten aus dem lokalen Dateisystem in Azure Storage geladen werden.
>
>

#### <a id="xplatcli"></a>Klassische Azure CLI
Die klassische Azure CLI ist ein plattformübergreifendes Tool zur Verwaltung von Azure-Diensten. Verwenden Sie zum Hochladen von Daten in Azure Storage die folgenden Schritte:

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. [Installieren und konfigurieren Sie die klassische Azure CLI für Mac, Linux und Windows](../cli-install-nodejs.md).
2. Öffnen Sie eine Eingabeaufforderung, Bash oder eine andere Shell, und authentifizieren Sie Ihr Azure-Abonnement wie folgt.

    ```cli
    azure login
    ```

    Geben Sie nach Aufforderung den Benutzernamen und das Kennwort für Ihr Abonnement ein.
3. Geben Sie den folgenden Befehl ein, um die Speicherkonten für Ihr Abonnement aufzulisten:

    ```cli
    azure storage account list
    ```

4. Wählen Sie das Speicherkonto mit dem zu verwendenden Blob aus, und rufen Sie mit dem folgenden Befehl den Schlüssel für dieses Konto ab:

    ```cli
    azure storage account keys list <storage-account-name>
    ```

    Dieser Befehl gibt die **primären** und **sekundären** Schlüssel zurück. Kopieren Sie den Wert des **primären** Schlüssels für die Verwendung in den nächsten Schritten.
5. Rufen Sie mit dem folgenden Befehl eine Liste der Blobcontainer im Speicherkonto ab:

    ```cli
    azure storage container list -a <storage-account-name> -k <primary-key>
    ```

6. Verwenden Sie die folgenden Befehle zum Hoch- und Herunterladen von Dateien im Blob:

   * So laden Sie eine Datei hoch:

        ```cli
        azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>
        ```

   * So laden Sie eine Datei herunter:

        ```cli
        azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>
        ```
    
> [!NOTE]  
> Wenn Sie immer mit demselben Speicherkonto arbeiten, können Sie die folgenden Umgebungsvariablen festlegen, anstatt das Konto und den Schlüssel für jeden Befehl anzugeben:
>
> * **AZURE\_STORAGE\_ACCOUNT**: Speicherkontoname
> * **AZURE\_STORAGE\_ACCESS\_KEY**: Speicherkontoschlüssel
>
>

#### <a id="powershell"></a>Azure PowerShell
Azure PowerShell ist eine Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Workloads in Azure steuern und automatisieren können. Informationen zum Konfigurieren der Arbeitsstation für die Ausführung von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**So laden Sie eine lokale Datei in Azure Storage hoch**

1. Öffnen Sie die Azure PowerShell-Konsole entsprechend den Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview).
2. Legen Sie die Werte der ersten fünf Variablen im folgenden Skript fest:

    ```powershell
    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $fileName ="<LocalFileName>"
    $blobName = "<BlobName>"

    # Get the storage account key
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    # Create the storage context object
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    # Copy the file from local workstation to the Blob container
    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
    ```

3. Fügen Sie das Skript in die Azure PowerShell-Konsole ein, um es auszuführen und die Datei zu kopieren.

PowerShell-Skripts, die für die Verwendung mit HDInsight erstellt wurden, finden Sie z.B. unter [HDInsight-Tools](https://github.com/blackmist/hdinsight-tools).

#### <a id="azcopy"></a>AzCopy
AzCopy ist ein Befehlszeilenprogramm, das den Austausch von Daten mit einem Azure Storage-Konto vereinfachen soll. Sie können dieses Dienstprogramm als eigenständiges Tool verwenden oder in eine vorhandene Anwendung integrieren. [Laden Sie AzCopy herunter][azure-azcopy-download].

Die AzCopy-Syntax lautet folgendermaßen:

```command
AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]
```

Weitere Informationen finden Sie unter [AzCopy – Hochladen/Herunterladen von Dateien für Azure-Blobs][azure-azcopy].

Die Vorschau von AzCopy unter Linux ist verfügbar.  Siehe [Announcing AzCopy on Linux Preview](https://blogs.msdn.microsoft.com/windowsazurestorage/2017/05/16/announcing-azcopy-on-linux-preview/) (Ankündigung der Vorschau von AzCopy unter Linux).

#### <a id="commandline"></a>Hadoop-Befehlszeile
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

Eine Liste mit weiteren Hadoop-Befehlen, die für Dateien verwendet werden können, finden Sie unter [https://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html).

> [!WARNING]  
> In Apache HBase-Clustern beträgt die standardmäßige Blockgröße beim Schreiben von Daten 256 KB. Bei Verwendung von HBase-APIs oder REST-APIs funktioniert dies einwandfrei. Die Nutzung der Befehle `hadoop` oder `hdfs dfs` zum Schreiben von Daten mit einem Umfang von mehr als ca. 12 GB führt allerdings zu einem Fehler. Weitere Informationen finden Sie im Abschnitt [Speicherausnahme beim Schreiben in ein Blob](#storageexception) dieses Artikels.

### <a name="graphical-clients"></a>Clients mit grafischer Benutzeroberfläche
Es gibt auch einige Anwendungen, die eine grafische Benutzeroberfläche für die Arbeit mit Azure Storage bereitstellen. In der folgenden Tabelle sind einige dieser Anwendungen aufgelistet:

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio-Tools für HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage-Explorer](https://storageexplorer.com/) |✔ |✔ |✔ |
| [Cloud Storage Studio 2](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Azure Explorer](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

#### <a name="visual-studio-tools-for-hdinsight"></a>Visual Studio-Tools für HDInsight
Weitere Informationen finden Sie unter [Navigieren durch die verknüpften Ressourcen](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources).

#### <a id="storageexplorer"></a>Azure-Speicher-Explorer
*Azure Storage-Explorer* ist ein hilfreiches Tool zum Prüfen und Ändern der Daten in Blobs. Dieses kostenlose Open Source-Tool kann unter [https://storageexplorer.com/](https://storageexplorer.com/) heruntergeladen werden. Der Quellcode ist über diesen Link ebenfalls verfügbar.

Bevor Sie das Tool verwenden können, müssen Sie den Namen Ihres Azure-Speicherkontos und den Kontoschlüssel kennen. Anweisungen zum Abrufen dieser Informationen finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos][azure-create-storage-account] im Abschnitt „Verwalten von Speicherzugriffsschlüsseln“. Dort wird erläutert, wie Sie Speicherzugriffsschlüssel anzeigen, kopieren und erneut generieren.

1. Führen Sie den Azure Storage-Explorer aus. Wenn Sie den Storage-Explorer erstmals ausführen, werden Sie zur Eingabe des **Speicherkontonamens** und **Speicherkontoschlüssels** aufgefordert. Wenn Sie den Speicher-Explorer zuvor bereits ausgeführt haben, wählen Sie die Schaltfläche **Hinzufügen** aus, um einen neuen Speicherkontonamen und -schlüssel hinzuzufügen.

    Geben Sie den Namen und Schlüssel für das vom HDInsight-Cluster verwendete Speicherkonto ein, und wählen Sie dann **SPEICHERN UND ÖFFNEN** aus.

    ![HDI.AzureStorageExplorer][image-azure-storage-explorer]
2. Klicken Sie in der Liste der Container links auf der Benutzeroberfläche auf den Namen des Containers, der Ihrem HDInsight-Cluster zugeordnet ist. Standardmäßig handelt es sich dabei um den Namen des HDInsight-Clusters, der jedoch abweichen kann, wenn Sie bei der Clustererstellung einen bestimmten Namen eingegeben haben.
3. Wählen Sie auf der Symbolleiste das Uploadsymbol aus.

    ![Symbolleiste mit hervorgehobenem Uploadsymbol](./media/hdinsight-upload-data/toolbar.png)
4. Geben Sie eine hochzuladende Datei an, und klicken Sie auf **Öffnen**. Sobald Sie dazu aufgefordert werden, wählen Sie **Hochladen** aus, um die Dateien in das Stammverzeichnis des Speichercontainers hochzuladen. Wenn Sie die Datei in einen bestimmten Pfad hochladen möchten, geben Sie den Pfad im Feld **Ziel** ein, und wählen Sie dann **Hochladen** aus.

    ![Dateiupload (Dialogfeld)](./media/hdinsight-upload-data/fileupload.png)

    Nachdem der Upload der Datei abgeschlossen wurde, können Sie sie in den Aufträgen des HDInsight-Clusters verwenden.

### <a name="mount-azure-storage-as-local-drive"></a>Einbinden von Azure Storage als lokales Laufwerk
Siehe [Mount Azure Storage as Local Drive](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx) (Einbinden von Azure Storage als lokales Laufwerk).

### <a name="upload-using-services"></a>Hochladen mithilfe von Diensten
#### <a name="azure-data-factory"></a>Azure Data Factory
Der Azure Data Factory-Dienst ist ein vollständig verwalteter Dienst für das Kombinieren von Verarbeitungs-, Speicher- und Verschiebungsdienste für Daten in optimierten, skalierbaren und zuverlässigen Datenproduktions-Pipelines.

Mit Azure Data Factory können Daten in Azure Storage verschoben oder Datenpipelines erstellt werden, die HDInsight-Funktionen wie Hive und Pig direkt verwenden.

Weitere Informationen finden Sie in der [Dokumentation zu Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

#### <a id="sqoop"></a>Apache Sqoop
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

### <a name="troubleshooting"></a>Problembehandlung
#### <a id="storageexception"></a>Speicherausnahme beim Schreiben in ein Blob
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

**Ursache:** Für HBase in HDInsight-Clustern wird beim Schreiben in den Azure-Speicher standardmäßig eine Blockgröße von 256 KB verwendet. Dies funktioniert bei HBase-APIs oder REST-APIs, bei der Verwendung der Befehlszeilenprogramme `hadoop` oder `hdfs dfs` tritt jedoch ein Fehler auf.

**Lösung:** Verwenden Sie `fs.azure.write.request.size`, um eine größere Blockgröße anzugeben. Sie können dies auf Nutzungsbasis mithilfe des `-D`-Parameters erreichen. Im folgenden Beispielbefehl wird dieser Parameter mit dem Befehl `hadoop` verwendet:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Sie können auch den Wert von `fs.azure.write.request.size` mithilfe von Apache Ambari global erhöhen. Die folgenden Schritte können verwendet werden, um den Wert in der Ambari-Webbenutzeroberfläche zu ändern:

1. Öffnen Sie in Ihrem Browser die Ambari-Webbenutzeroberfläche für den Cluster. Diese befindet sich unter https://CLUSTERNAME.azurehdinsight.net, wobei **CLUSTERNAME** der Name Ihres Clusters ist.

    Geben Sie bei entsprechender Aufforderung den Namen und das Kennwort des Administrators für den Cluster ein.
2. Wählen Sie auf der linken Bildschirmseite **HDFS** und dann die Registerkarte **Configs** aus.
3. Geben Sie in das Feld **Filter** den Text `fs.azure.write.request.size` ein. Dadurch werden das Feld und der aktuelle Wert in der Mitte der Seite angezeigt.
4. Ändern Sie den Wert von 262144 (256 KB) in den neuen Wert. Beispiel: 4194304 (4 MB).

![Abbildung der Änderung des Werts über die Ambari-Webbenutzeroberfläche](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Weitere Informationen zur Verwendung von Ambari finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Nächste Schritte
Jetzt wissen Sie, wie Sie Daten in HDInsight importieren. Lesen Sie in den folgenden Artikeln, wie Sie die Daten analysieren:

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Programmgesteuertes Übermitteln von Apache Hadoop-Aufträgen][hdinsight-submit-jobs]
* [Verwenden von Apache Hive mit HDInsight][hdinsight-use-hive]
* [Verwenden von Apache Pig mit HDInsight][hdinsight-use-pig]

[azure-management-portal]: https://porta.azure.com
[azure-powershell]: https://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]:../storage/common/storage-create-storage-account.md
[azure-azcopy-download]:../storage/common/storage-use-azcopy.md
[azure-azcopy]:../storage/common/storage-use-azcopy.md

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-adls-gen1]: hdinsight-hadoop-use-data-lake-store.md
[hdinsight-adls-gen2]: ../storage/data-lake-storage/use-hdi-cluster.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[azurecli]: ../cli-install-nodejs.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
