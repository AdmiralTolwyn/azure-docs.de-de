---
title: Handbücher zur Problembehandlung für Azure HDInsight | Microsoft-Dokumentation
description: Führen Sie die Problembehandlung für Hadoop-Workloads mit Azure HDInsight aus. Anhand einer schrittweisen Dokumentation wird veranschaulicht, wie mithilfe von HDInsight häufig auftretende Probleme mit Hive, Spark, YARN, HBase, HDFS und Storm behoben werden.
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: ''
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: 785884b7fd03339d195fbd7853b17300effecc6a
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2017
ms.locfileid: "23984719"
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>Problembehandlung mit Azure HDInsight

| Apache-Workload | Wichtige Fragen |
|---|---|
|![HBase](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[Problembehandlung bei HBase](hbase/apache-troubleshoot-hbase.md)|<br>[Wie führe ich hbck-Befehlsberichte mit mehreren nicht zugewiesenen Regionen aus?](hbase/apache-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[Wie behebe ich Timeoutprobleme mit hbck-Befehlen für Regionszuweisungen?](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[Wie erzwinge ich das Deaktivieren des abgesicherten HDFS-Modus in einem Cluster?](hbase/apache-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)<br><br>[Wie behebe ich JDBC- oder SQLLine-Konnektivitätsprobleme mit Apache Phoenix?](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[Was ist die Ursache dafür, dass ein Masterserver nicht startet?](hbase/apache-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[Was verursacht einen Neustartfehler bei einem Regionsserver?](hbase/apache-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[Problembehandlung bei HDFS](hdinsight-troubleshoot-hdfs.md)|<br>[Wie greife ich aus einem Cluster heraus auf das lokale HDFS zu?](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Wie erzwinge ich das Deaktivieren des abgesicherten HDFS-Modus in einem Cluster?](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![Hive](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[Problembehandlung bei HBase](hdinsight-troubleshoot-hive.md)|<br>[Wie exportiere ich einen Hive-Metastore und importiere ihn in einen anderen Cluster?](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Wie finde ich Hive-Protokolle in einem Cluster?](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[Wie starte ich die Hive-Shell mit bestimmten Konfigurationen in einem Cluster?](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Wie analysiere ich Tez DAG-Daten (Directed Acyclic Graph, gerichteter azyklischer Graph) in einem clusterkritischen Pfad?](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Wie lade ich Tez DAG-Daten (Directed Acyclic Graph, gerichteter azyklischer Graph) aus einem Cluster herunter?](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![Spark](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Problembehandlung bei Spark](hdinsight-troubleshoot-SPARK.md)|<br>[Wie konfiguriere ich eine Spark-Anwendung über Ambari in Clustern?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-ambari-on-clusters)<br><br>[Wie konfiguriere ich eine Spark-Anwendung über ein Jupyter Notebook in Clustern?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[Wie konfiguriere ich eine Spark-Anwendung über Livy in Clustern?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-livy-on-clusters)<br><br>[Wie konfiguriere ich eine Spark-Anwendung über spark-submit in Clustern?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters)<br><br>[Wodurch wird eine OutOfMemoryError-Ausnahme in einer Spark-Anwendung verursacht?](spark/apache-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![Storm](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[Problembehandlung bei Storm](hdinsight-troubleshoot-STORM.md)|<br>[Wie greife ich auf die Storm-Benutzeroberfläche in einem Cluster zu?](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Wie übertrage ich Storm-EventHub-Spout-Prüfpunktinformationen von einer Topologie zu einer anderen?](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Wie finde ich Storm-Binärdateien in einem Cluster?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Wie bestimme ich die Bereitstellungstopologie eines Storm-Clusters?](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Wie finde ich Storm-EventHub-Spout-Binärdateien für die Entwicklung?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)<br><br>[Wie finde ich Storm-Log4J-Konfigurationsdateien in Clustern?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[Problembehandlung bei YARN](hdinsight-troubleshoot-YARN.md)|<br>[Wie erstelle ich eine neue YARN-Warteschlange in einem Cluster?](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Wie lade ich YARN-Protokolle aus einem Cluster herunter?](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Ressourcen zur HDInsight-Problembehandlung

| Informationen über | Lesen Sie diese Artikel |
| --- | --- |
| HDInsight unter Linux und Optimierung | - [Informationen zur Verwendung von HDInsight unter Linux](hdinsight-hadoop-linux-information.md)<br>- [Behandlung von Hadoop-Arbeitsspeicher- und Leistungsproblemen](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Leistung von Hive-Abfragen](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Protokolle und Speicherabbilder | - [Zugriff auf Hadoop YARN-Anwendungsprotokolle unter Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Aktivieren von Heapdumps für Hadoop-Dienste unter Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [Analysieren von HDInsight-Protokollen](hdinsight-debug-jobs.md)|
| Errors | - [Verstehen und Beheben von WebHCat-Fehlern](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [Hive-Einstellungen zum Beheben von OutofMemory-Fehlern](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Tools | - [Debuggen von Tez-Aufträgen mithilfe von Ambari-Ansichten](hdinsight-debug-ambari-tez-view.md)<br>- [Optimieren von Hive-Abfragen](hdinsight-hadoop-optimize-hive-query.md) |
