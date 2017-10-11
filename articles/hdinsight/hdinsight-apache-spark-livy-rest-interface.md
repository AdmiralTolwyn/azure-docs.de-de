---
title: "Übermittlung von Aufträgen an einen Spark-Cluster in Azure HDInsight mithilfe von Livy Spark | Microsoft-Dokumentation"
description: "Erfahren Sie, wie Sie die Apache Spark-REST-API verwenden, um Spark-Aufträge remote an einen Azure HDInsight-Cluster übermitteln."
keywords: Apache Spark-REST-API, Livy Spark
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2817b779-1594-486b-8759-489379ca907d
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: nitinme
ms.openlocfilehash: e1a28d69bbf40ea3134a7899a0d2fe70e5fc9e71
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/29/2017
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Übermitteln von Remoteaufträgen an einen HDInsight Spark-Cluster mithilfe der Apache Spark-REST-API

Erfahren Sie, wie Sie Livy, die Apache Spark-REST-API, verwenden, um Remoteaufträge an einen Azure HDInsight Spark-Cluster zu übermitteln. Eine ausführliche Dokumentation finden Sie unter [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server).

Mit Livy können Sie interaktive Spark-Shells ausführen oder Batchaufträge zur Ausführung in Spark übermitteln. In diesem Artikel wird die Übermittlung von Batchaufträgen mithilfe von Livy behandelt. Die Codeausschnitte in diesem Artikel verwenden cURL für an den Livy Spark-Endgerät gerichtete REST-API-Aufrufe.

**Voraussetzungen:**

* Ein Apache Spark-Cluster unter HDInsight. Eine Anleitung finden Sie unter [Erstellen von Apache Spark-Clustern in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

* [cURL](http://curl.haxx.se/). Dieser Artikel zeigt mit cURL, wie Sie REST-API-Aufrufe für einen HDInsight Spark-Cluster ausführen.

## <a name="submit-a-livy-spark-batch-job"></a>Übermitteln eines Livy Spark-Batchvorgangs
Vor dem Übermitteln eines Batchauftrags muss die JAR-Anwendungsdatei an den Clusterspeicher hochgeladen werden, der dem Cluster zugeordnet ist. Hierzu können Sie das Befehlszeilenprogramm [**AzCopy**](../storage/common/storage-use-azcopy.md) verwenden. Die Daten können aber auch mit verschiedenen anderen Clients hochgeladen werden. Weitere Informationen finden Sie unter [Hochladen von Daten für Hadoop-Aufträge in HDInsight](hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Beispiele**:

* In diesem Beispiel befindet sich die JAR-Datei im Clusterspeicher (WASB):
  
        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"
* In diesem Beispiel möchten Sie den Namen der JAR-Datei und den Klassennamen als Teil der Eingabedatei „input.txt“ übergeben:
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Abrufen von Informationen zu im Cluster ausgeführten Livy Spark-Batches
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Beispiele**:

* In diesem Beispiel sollen alle im Cluster ausgeführten Livy Spark-Batches abgerufen werden:
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
* In diesem Beispiel möchten Sie einen spezifischen Batch mit einer bestimmten Batch-ID abrufen:
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-livy-spark-batch-job"></a>Löschen eines Livy Spark-Batchvorgangs
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Beispiel**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-spark-and-high-availability"></a>Livy Spark und hohe Verfügbarkeit
Livy bietet hohe Verfügbarkeit für im Cluster ausgeführte Spark-Aufträge. Hier sind einige Beispiele angegeben:

* Wenn der Livy-Dienst nach der Übermittlung eines Remoteauftrags an einen Spark-Cluster abstürzt, wird der Auftrag weiterhin im Hintergrund ausgeführt. Wenn Livy wieder funktioniert, stellt es den Status des Auftrags wieder her und erstattet Bericht.
* Jupyter Notebooks für HDInsight wird am Back-End durch Livy unterstützt. Wenn ein Notebook einen Spark-Auftrag ausführt und der Livy-Dienst neu gestartet wird, führt das Notebook weiterhin die Codezellen aus. 

## <a name="show-me-an-example"></a>Beispiele
In diesem Abschnitt erfahren Sie anhand von Beispielen, wie Sie mit Livy Spark einen Batchvorgang übermitteln, den Status des Vorgangs überwachen und den Vorgang anschließend löschen. Dabei verwenden wir die im Artikel [Erstellen einer eigenständigen Scala-Anwendung zur Ausführung in HDInsight Spark-Clustern](hdinsight-apache-spark-create-standalone-application.md)entwickelte Anwendung. Bei den folgenden Schritten wird von Folgendem ausgegangen:

* Die JAR-Anwendungsdatei wurde bereits in das dem Cluster zugeordnete Speicherkonto kopiert.
* Auf dem Computer, auf dem die Schritte ausgeführt werden, ist cURL installiert.

Führen Sie die folgenden Schritte aus:

1. Überprüfen Sie zunächst, ob Livy Spark im Cluster ausgeführt wird. Dazu können Sie eine Liste mit ausgeführten Batches abrufen. Bei der erstmaligen Ausführung eines Auftrags mithilfe von Livy wird Null zurückgegeben.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Die Ausgabe sollte in etwa wie folgender Codeausschnitt aussehen:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Beachten Sie, dass in der letzten Zeile der Ausgabe **total:0** angegeben ist. Das bedeutet, dass keine Batches ausgeführt werden.

2. Übermitteln wir nun einen Batchauftrag. Im folgenden Codeausschnitt werden mithilfe einer Eingabedatei namens „input.txt“ der JAR-Name und der Klassenname als Parameter übergeben. Beim Ausführen der Schritte auf einem Windows-Computer wird die Verwendung einer Eingabedatei empfohlen.
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Die Parameter in der Datei **input.txt** sind wie folgt definiert:
   
        { "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
   
    Die Ausgabe sollte in etwa wie folgender Codeausschnitt aussehen:
   
        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Beachten Sie die Angabe **state:starting**in der letzten Zeile der Ausgabe. Und die Angabe **id:0**. **0** ist hier die Batch-ID.

3. Anhand der Batch-ID können Sie nun den Status dieses speziellen Batchs abrufen.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Die Ausgabe sollte in etwa wie folgender Codeausschnitt aussehen:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Die Ausgabe enthält nun **state:success**. Der Auftrag wurde also erfolgreich abgeschlossen.

4. Wenn Sie möchten, können Sie den Batch jetzt löschen.
   
        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Die Ausgabe sollte in etwa wie folgender Codeausschnitt aussehen:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Die letzte Zeile der Ausgabe zeigt, dass der Batch erfolgreich gelöscht wurde. Beim Löschen eines ausgeführten Auftrags wird dieser gleichzeitig beendet. Beim Löschen eines (erfolgreich oder anderweitig) abgeschlossenen Auftrags werden die Auftragsinformationen vollständig gelöscht.

## <a name="using-livy-spark-on-hdinsight-35-clusters"></a>Verwenden von Livius Spark in HDInsight 3.5-Clustern

Der HDInsight 3.5-Cluster deaktiviert standardmäßig die Verwendung der lokalen Dateipfade für den Zugriff auf Beispieldatendateien oder JAR-Dateien. Wir empfehlen Ihnen stattdessen die Verwendung des `wasb://`-Pfads, um aus dem Cluster auf Beispieldatendateien oder JAR-Dateien zuzugreifen. Wenn Sie den lokalen Pfad verwenden möchten, müssen Sie die Ambari-Konfiguration entsprechend aktualisieren. Gehen Sie dazu wie folgt vor:

1. Besuchen Sie das Ambari-Portal für den Cluster. Die Ambari-Webbenutzeroberfläche ist in Ihrem HDInsight-Cluster unter „https://**CLUSTERNAME**.azurehdidnsight.net“ verfügbar, wobei CLUSTERNAME der Name Ihres Clusters ist.

2. Klicken Sie im linken Navigationsbereich auf **Livy** und dann auf **Configs**.

3. Fügen Sie unter **livy-default** den Eigenschaftennamen `livy.file.local-dir-whitelist` hinzu, und legen Sie dessen Wert auf **"/"** fest, wenn Sie uneingeschränkten Zugriff auf das Dateisystem zulassen möchten. Wenn Sie nur den Zugriff auf ein bestimmtes Verzeichnis zulassen möchten, geben Sie den Pfad für dieses Verzeichnis als Wert an.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Übermitteln von Livy-Aufträgen für einen Cluster in einem virtuellen Azure-Netzwerk

Wenn Sie aus einer Azure Virtual Network-Instanz eine Verbindung mit einem HDInsight Spark-Cluster herstellen, können Sie direkt eine Verbindung mit Livy im Cluster herstellen. In diesem Fall lautet die URL für den Livy-Endpunkt `http://<IP address of the headnode>:8998/batches`. **8998** ist der Port, an dem Livy im Clusterhauptknoten ausgeführt wird. Weitere Informationen zum Zugreifen auf Dienste an nicht öffentlichen Ports finden Sie unter [Ports für Hadoop-Dienste in HDInsight](hdinsight-hadoop-port-settings-for-services.md).

## <a name="troubleshooting"></a>Problembehandlung

Im Folgenden finden Sie einige Probleme, die bei der Verwendung von Livy für die Remoteauftragsübermittlung an Spark-Cluster auftreten können.

### <a name="using-an-external-jar-from-the-additional-storage-is-not-supported"></a>Keine Unterstützung für die Verwendung einer externen JAR-Datei aus zusätzlichem Speicher

**Problem:** Wenn Ihr Livy Spark-Auftrag auf eine externe JAR-Datei aus dem zusätzlichen, dem Cluster zugeordneten Speicherkonto verweist, tritt ein Fehler auf.

**Lösung:** Stellen Sie sicher, dass die JAR-Datei, die Sie verwenden möchten, im Standardspeicher verfügbar ist, der dem HDInsight-Cluster zugeordnet ist.





## <a name="next-step"></a>Nächster Schritt

* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight(Nachverfolgen und Debuggen von Aufträgen in einem Apache Spark-Cluster unter HDInsight)](hdinsight-apache-spark-job-debugging.md)

