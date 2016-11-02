 <properties
	pageTitle="Was ist Hadoop in der Cloud? Einführung in HDInsight | Microsoft Azure"
	description="Was ist Hadoop in der Cloud, und wie wird es in Microsoft Azure HDInsight verwaltet? Eine Einführung in Hadoop-Komponenten und die Analyse großer Datenbestände (Big Data)."
	keywords="Big Data-Analyse,Einführung in Hadoop,Was ist Hadoop,Hadoop in der Cloud,Hadoop-Technologiestapel,Hadoop-Ökosystem"
	services="hdinsight"
	documentationCenter=""
	authors="cjgronlund"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/21/2016"
   ms.author="cgronlun"/>


# Was ist Hadoop in der Cloud? Enthält eine Einführung in das Hadoop-Ökosystem in HDInsight.

Lesen Sie eine Einführung in Hadoop, sein Ökosystem und Big Data in Azure HDInsight, und erfahren Sie, was Hadoop in HDInsight ist und welche Hadoop-Komponenten es gibt. Außerdem werden die gängige Terminologie und Szenarien für die Big Data-Analyse vorgestellt. Lernen Sie außerdem die Tutorials zu Hadoop, die Dokumentation und die Ressourcen für die Verwendung von Hadoop in der Cloud in HDInsight kennen.

## Was ist Hadoop in HDInsight?

Azure HDInsight verwendet die Hadoop-Distribution **Hortonworks Data Platform (HDP)**. HDInsight sorgt für die Bereitstellung und Zuteilung verwalteter Apache Hadoop-Cluster in der Cloud und stellt damit ein Softwareframework für die zuverlässige und hoch verfügbare Verarbeitung, Analyse und Berichterstellung für große Datenbestände (Big Data) bereit.

Hadoop bezieht sich häufig auf das gesamte Hadoop-Ökosystem mit all seinen Komponenten, einschließlich Apache HBase, Apache Spark und Apache Storm sowie anderer Techniken unter dem Oberbegriff Hadoop. Weitere Informationen finden Sie nachfolgend unter [Übersicht über das Hadoop-Ökosystem in HDInsight](#overview).

## Was versteht man unter "Big Data"?

„Big Data“ beschreibt beliebig großen Mengen digitaler Informationen – vom Text in einem Twitter-Feed über Sensordaten von Industrieanlagen bis hin zu Daten über die Navigation und Einkäufe von Kunden auf einer Website. "Big Data" kann sich auf historische (gespeicherte) Daten oder auf Echtzeit-Daten (direkt von der Quelle gestreamt) beziehen. Big Data wird in immer größeren Mengen, mit immer höheren Geschwindigkeiten und in immer mehr Formaten erfasst.

Um neue Erkenntnisse und Einblicke aus großen Datenmengen (Big Data) zu gewinnen, müssen nicht nur relevante Daten gesammelt werden und die richtigen Fragen gestellt werden. Die Daten müssen außerdem zugänglich gemacht, bereinigt, analysiert und auf nützliche Weise dargestellt werden. Hierbei kann die Big Data-Analyse mit Hadoop in HDInsight hilfreich sein.

## <a name="overview"></a>Übersicht über das Hadoop-Ökosystem in HDInsight

HDInsight ist eine Cloudimplementierung in Microsoft Azure des schnell wachsenden Apache Hadoop-Technikstapels, der die bevorzugte Lösung für die Analyse von Big Data darstellt. HDInsight enthält Implementierungen von Apache Spark, HBase, Storm, Pig, Hive, Sqoop, Oozie, Ambari usw. HDInsight kann außerdem mit Business Intelligence-Tools (BI) wie z. B. Power BI, Excel, SQL Server Analysis Services oder SQL Server Reporting Services integriert werden.

### Hadoop, HBase, Spark, Storm und angepasste Cluster

HDInsight bietet Clusterkonfigurationen für Apache Hadoop, Spark, HBase und Storm. Alternativ können Sie [Cluster mit Skriptaktionen anpassen](hdinsight-hadoop-customize-cluster-linux.md).

* **Hadoop** (die "Abfrage"-Arbeitslast): Bietet eine zuverlässige Datenspeicherung mithilfe von [HDFS](#hdfs) und ein einfaches [MapReduce](#mapreduce)-Programmiermodell zum parallelen Verarbeiten und Analysieren von Daten.

* **<a target="_blank" href="http://spark.apache.org/">Apache Spark</a>** – Apache Spark ist ein Open-Source-Framework für die Parallelverarbeitung, das die arbeitsspeicherinterne Verarbeitung unterstützt, um die Leistung von Anwendungen zur Analyse von großen Datenmengen zu steigern. Spark funktioniert mit SQL, Datenströmen und maschinellem Lernen. Weitere Informationen finden Sie unter [Übersicht: Was ist Apache Spark in HDInsight?](hdinsight-apache-spark-overview.md)

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>** (die "NoSQL"-Arbeitslast): Eine auf Hadoop basierende NoSQL-Datenbank, die wahlfreien Zugriff und starke Konsistenz für große Mengen unstrukturierter und teilstrukturierter Daten bietet – in einer Dimension von Milliarden von Zeilen multipliziert mit Milliarden von Spalten. Siehe [Übersicht über HBase in HDInsight](hdinsight-hbase-overview.md).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>** (die "Stream"-Arbeitslast): Ein verteiltes Echtzeitberechnungssystem für die schnelle Verarbeitung großer Datenströme. Storm wird als verwalteter Cluster in HDInsight angeboten. Siehe [Analysieren von Echtzeit-Sensordaten mit Storm und Hadoop](hdinsight-storm-sensor-data-analysis.md).

### Beispielskripts für die Anpassung

Bei Skriptaktionen handelt es sich um Skripts, die während der Clusterbereitstellung ausgeführt werden und zur Installation zusätzlicher Komponenten auf dem Cluster verwendet werden können. Für Linux-basierte Cluster sind dies die Bash-Skripts.

Die folgenden Beispielskripts werden vom HDInsight-Team bereitgestellt:

* [Hue](hdinsight-hadoop-hue-linux.md) – eine Reihe von Webanwendungen zur Interaktion mit einem Hadoop-Cluster. Nur Linux-Cluster.

* [Giraph](hdinsight-hadoop-giraph-install-linux.md) – Graphenverarbeitung zum Modellieren von Beziehungen zwischen Dingen oder Personen.

* [R](hdinsight-hadoop-r-scripts-linux.md) – eine Open-Source-Sprache und -Umgebung für statistische Berechnungen beim maschinellen Lernen.

* [Solr](hdinsight-hadoop-solr-install-linux.md) – eine Unternehmensplattform für die leistungsstarke Volltextsuche nach Daten.

Informationen zum Entwickeln eigener Skriptaktionen finden Sie unter [Entwickeln von Skriptaktionen mit HDInsight](hdinsight-hadoop-script-actions-linux.md).


## Aus welchen Komponenten und Dienstprogrammen besteht Hadoop?

Die folgenden Komponenten und Dienstprogramme sind in HDInsight-Clustern enthalten.

* **[Ambari:](#ambari)** Bereitstellung, Verwaltung und Überwachung von Clustern sowie Hilfsprogramme für Cluster.

* **[Avro](#avro)** (Microsoft .NET Library für Avro): Datenserialisierung für die Microsoft .NET-Umgebung.

* **[Hive und HCatalog](#hive)**: SQL-ähnliche Abfragen und eine Tabellen- und Speicherverwaltungsschicht.

* **[Mahout](#mahout)**: Maschinelles Lernen.

* **[MapReduce:](#mapreduce)** Legacy-Framework für verteilte Verarbeitung und Ressourcenverwaltung für Hadoop. Informationen finden Sie unter [YARN](#yarn), dem Ressourcenframework der nächsten Generation.

* **[Oozie](#oozie)**: Workflowverwaltung.

* **[Phoenix](#phoenix)**: Relationale Datenbankschicht über HBase.

* **[Pig](#pig)**: Einfachere Skripts für MapReduce-Transformationen.

* **[Sqoop](#sqoop)**: Import und Export von Daten.

* **[Tez](#tez)**: Effiziente Ausführung datenintensiver Prozesse.

* **[YARN:](#yarn)** Teil der Hadoop-Kernbibliothek und nächste Generation des MapReduce-Softwareframeworks.

* **[ZooKeeper](#zookeeper)**: Koordination von Prozessen in verteilten Systemen.

> [AZURE.NOTE] Informationen zu den jeweiligen Komponenten und Versionsinformationen finden Sie unter [Was sind die verschiedenen Hadoop-Komponenten, die in HDInsight verfügbar sind?][component-versioning].

### <a name="ambari"></a>Ambari

Apache Ambari dient zur Bereitstellung, Verwaltung und Überwachung von Apache Hadoop-Clustern. Es umfasst eine Sammlung intuitiver Operatortools und eine Reihe robuster APIs, welche die Komplexität von Hadoop verbergen und den Betrieb von Clustern vereinfachen. Linux-basierte HDInsight-Cluster stellen sowohl die Ambari-Webbenutzeroberfläche als auch die Ambari REST-API bereit, während Windows-basierte Cluster eine Teilmenge der REST-API bereitstellen. Ambari-Ansichten von HDInsight-Clustern ermöglichen Plug-In-Benutzerschnittstellenfunktionen.

Siehe [Verwalten von HDInsight-Clustern mit Ambari](hdinsight-hadoop-manage-ambari.md) (nur Linux), [Überwachen von Hadoop-Clustern in HDInsight mit der Ambari-API](hdinsight-monitor-use-ambari-api.md) und <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari-API-Referenz</a>.

### <a name="avro"></a>Avro (Microsoft .NET-Bibliothek für Avro)

Die Microsoft .NET-Bibliothek für Avro implementiert das kompakte Apache Avro-Datenaustauschformat für Binärdaten für die Microsoft .NET-Umgebung. Verwendet <a target="_blank" href="http://www.json.org/">JavaScript Object Notation (JSON)</a> zum Definieren eines sprachunabhängigen Schemas, das die Interoperabilität von Sprachen sicherstellt, sodass die in einer Sprache serialisierten Daten in einer anderen Sprache gelesen werden können. Detaillierte Informationen zum Format finden Sie in der <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Apache Avro-Spezifikation</a>. Das Format von Avro-Dateien unterstützt das verteilte MapReduce-Programmiermodell. Die Dateien sind "teilbar", d. h. es kann nach einem beliebigen Punkt in der Datei gesucht und ab einem beliebigen Block gelesen werden. Weitere Informationen hierzu finden Sie unter [Serialisieren von Daten mit der Microsoft .NET-Bibliothek für Avro](hdinsight-dotnet-avro-serialization.md).


### <a name="hdfs"></a>HDFS

Hadoop Distributed File System (HDFS) ist ein verteiltes Dateisystem, das zusammen mit MapReduce und YARN den Kern des Hadoop-Ökosystems bildet. HDFS ist das Standard-Dateisystem für Hadoop-Cluster in HDInsight.

### <a name="hive"></a>Hive und HCatalog

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> ist eine auf Hadoop basierende Data Warehouse-Software, mit der Sie große Datasets in verteilten Speicherformen mithilfe einer SQL-ähnlichen Sprache namens HiveQL abfragen und verwalten können. Hive ist ebenso wie Pig eine auf MapReduce aufsetzende Abstraktion. Beim Ausführen übersetzt Hive Abfragen in eine Serie von MapReduce-Aufträgen. Das Hive-Konzept ähnelt eher einem Managementsystem für relationale Datenbanken als Pig und ist daher besser für strukturierte Daten geeignet. Für unstrukturierte Daten ist Pig die bessere Wahl. Siehe [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> ist eine Tabellen- und Speicherverwaltungsschicht für Hadoop, die Benutzern eine relationale Datenansicht bereitstellt. In HCatalog können Sie Dateien in jedem Format lesen und schreiben, für das ein Hive-SerDe (Serialisierungs-/Deserialisierungsprogramm) geschrieben werden kann.

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> ist eine auf Hadoop basierende skalierbare Bibliothek mit Algorithmen für maschinelles Lernen. Anwendungen für maschinelles Lernen verwenden Grundsätze der Statistik, um aus Daten zu lernen und um zukünftiges Verhalten anhand vergangener Daten zu bestimmen. Siehe [Erstellen von Filmempfehlungen mithilfe von Mahout und Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
MapReduce ist das Legacy-Softwareframework für Hadoop zum Schreiben von Anwendungen für die parallele Stapelverarbeitung großer Datasets. Ein MapReduce-Auftrag unterteilt große Datasets und verwaltet die Daten in Schlüssel-Wert-Paaren für die Verarbeitung.

[YARN](#yarn) ist das Ressourcen-Manager- und Anwendungsframework der nächsten Generation für Hadoop und wird als MapReduce 2.0 bezeichnet. MapReduce-Aufträge können mit YARN ausgeführt werden.

Weitere Informationen zu MapReduce finden Sie unter <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> im Hadoop-Wiki.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> ist ein Koordinationssystem für Workflows zur Verwaltung von Hadoop-Aufträgen. Es ist in den Hadoop-Stack integriert und unterstützt Hadoop-Jobs für MapReduce, Pig, Hive und Sqoop. Es kann auch dazu verwendet werden, bestimmte Jobs für ein System zu planen, beispielsweise Java-Programme oder Shellskripts. Weitere Informationen finden Sie unter [Verwenden von Oozie mit Hadoop](hdinsight-use-oozie.md).

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> ist eine relationale Datenbankschicht über HBase. Phoenix umfasst einen JDBC-Treiber, mit dem Benutzer SQL-Tabellen direkt abfragen und verwalten können. Anstatt MapReduce zu verwenden, übersetzt Phoenix Abfragen und andere Anweisungen in systemeigene NoSQL-API-Aufrufe und ermöglicht so auf NoSQL-Speicher aufsetzend schnellere Anwendungen. Siehe [Verwenden von Apache Phoenix und SQuirreL mit HBase-Clustern](hdinsight-hbase-phoenix-squirrel.md).


### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> ist eine allgemeine Plattform zum Anwenden komplexer MapReduce-Transformationen auf extrem große Datasets mithilfe einer einfachen Skriptsprache namens Pig Latin. Pig übersetzt Pig Latin-Skripts für die Ausführung in Hadoop. Sie können benutzerdefinierte Funktionen erstellen, um Pig Latin zu erweitern. Weitere Informationen finden Sie unter [Verwenden von Pig mit Hadoop](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> ist ein Tool für die möglichst effektive Übertragung großer Datenmengen zwischen Hadoop und relationalen Datenbanken wie SQL oder anderen strukturierten Datenspeichern. Siehe [Verwenden von Sqoop mit Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> ist ein auf Hadoop YARN aufsetzendes Anwendungsframework für die Erstellung komplexer, azyklischer Diagramme der allgemeinen Datenverarbeitung. Es handelt sich hier um einen flexibleren und leistungsfähigeren Nachfolger des MapReduce-Frameworks, der auch bei datenintensiven Prozessen wie Hive eine effiziente Ausführung zulässt. Siehe ["Verwenden von Apache Tez zur Verbesserung der Leistung" unter "Verwenden von Hive und HiveQL"](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>YARN
Apache YARN ist die nächste Generation von MapReduce (MapReduce 2.0 bzw. MRv2) und unterstützt Datenverarbeitungsszenarien, die über die Stapelverarbeitung von MapReduce hinausgehen. YARN bietet mehr Skalierbarkeit und bessere Echtzeitverarbeitung. YARN bietet Ressourcenmanagement und ein verteiltes Anwendungsframework. MapReduce-Aufträge können mit YARN ausgeführt werden.

Weitere Informationen zu YARN finden Sie unter <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache Zookeeper</a> koordiniert Prozesse in großen verteilten Systemen mithilfe eines hierarchischen Namespace von Datenregistern (znodes). Znodes enthalten kleine Mengen an Metainformationen, die für die Koordination von Prozessen erforderlich sind: Status, Speicherort, Konfiguration usw.

## Programmiersprachen in HDInsight

HDInsight-Cluster – Hadoop-, HBase-, Storm- und Spark-Cluster – unterstützen eine Vielzahl von Programmiersprachen, einige davon sind jedoch standardmäßig nicht installiert. Verwenden Sie eine Skriptaktion, um Bibliotheken, Module oder Pakete zu installieren, die standardmäßig nicht installiert sind. Weitere Informationen finden Sie unter [Entwickeln von Skriptaktionen mit HDInsight](hdinsight-hadoop-script-actions-linux.md).

### Standardmäßige Unterstützung für Programmiersprachen

Standardmäßig unterstützen HDInsight-Cluster folgende Sprachen:

* Java

* Python

Zusätzliche Sprachen können mithilfe von Skriptaktionen installiert werden: [Entwickeln von Skriptaktionen mit HDInsight](hdinsight-hadoop-script-actions-linux.md).

### JVM-Sprachen (Java Virtual Machine)

Neben Java können viele weitere Sprachen mithilfe einer JVM (Java Virtual Machine) ausgeführt werden. Für einige dieser Sprachen müssen jedoch möglicherweise zusätzliche Komponenten im Cluster installiert werden.

Diese JVM-basierten Sprachen werden in HDInsight-Clustern unterstützt:

* Clojure

* Jython (Python für Java)

* Scala

### Hadoop-spezifische Sprachen

HDInsight-Cluster bieten Unterstützung für die folgenden, für das Hadoop-Ökosystem spezifischen, Sprachen:

* Pig Latin für Pig-Aufträge

* HiveQL für Hive-Aufträge und SparkSQL


## <a name="advantage"></a>Vorzüge von Hadoop in der Cloud

Hadoop in HDInsight ist Teil des Azure Cloud-Ökosystems und bietet eine Vielzahl von Vorteilen, zum Beispiel:

* Automatische Bereitstellung von Hadoop-Clustern. HDInsight-Cluster sind viel einfacher zu erstellen als manuell konfigurierte Hadoop-Cluster. Weitere Informationen finden Sie unter [Bereitstellen von Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

* Modernste Hadoop-Komponenten. Weitere Informationen finden Sie unter [Was sind die verschiedenen Hadoop-Komponenten, die in HDInsight verfügbar sind?][component-versioning].

* Hohe Verfügbarkeit und Zuverlässigkeit von Clustern. Weitere Details finden Sie unter [Verfügbarkeit und Zuverlässigkeit von Hadoop-Clustern in HDInsight](hdinsight-high-availability-linux.md).

* Effiziente und wirtschaftliche Datenspeicherung im Azure-Blobspeicher, eine Hadoop-kompatible Option. Siehe [Verwenden des Azure-Blobspeichers mit Hadoop in HDInsight](hdinsight-hadoop-use-blob-storage.md).

* Integration mit anderen Azure-Diensten wie [Web-Apps](https://azure.microsoft.com/documentation/services/app-service/web/) und [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).

* Zusätzliche VM-Größen und -Typen für die Ausführung von HDInsight-Clustern. Ausführliche Informationen finden Sie unter [Was sind die verschiedenen Hadoop-Komponenten, die in HDInsight verfügbar sind?][component-versioning].

* Clusterskalierung. Mithilfe der Clusterskalierung können Sie die Anzahl der Knoten eines ausgeführten HDInsight-Clusters ändern, ohne ihn löschen oder neu erstellen zu müssen.

* Unterstützung für virtuelle Netzwerke. HDInsight-Cluster können mit virtuellen Azure-Netzwerken verwendet werden, um Cloud-Ressourcen zu isolieren oder um Hybrid-Lösungen zu unterstützen, die Cloud-Ressourcen mit Ressourcen in Ihrem Rechenzentrum verbinden.

* Niedrige Einstiegskosten. Probieren Sie die [kostenlose Testversion](https://azure.microsoft.com/free/) aus oder entdecken Sie die [HDInsight-Preisdetails](https://azure.microsoft.com/pricing/details/hdinsight/).

Weitere Informationen zu den Vorteilen von Hadoop in HDInsight finden Sie in der [Azure-Features-Seite für HDInsight][marketing-page].

## HDInsight Standard und HDInsight Premium

HDInsight bietet Cloudlösungen für Big Data in zwei Kategorien an: Standard und Premium. HDInsight Standard stellt einen Unternehmenscluster bereit, in dem Organisationen ihre Big Data-Workloads ausführen können. HDInsight Premium baut darauf auf und bietet erweiterte Analyse- und Sicherheitsfunktionen für einen HDInsight-Cluster. Weitere Informationen finden Sie unter [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium).


## <a id="resources"></a>Ressourcen zum Veranschaulichen von Big Data-Analysen, Hadoop und HDInsight

Lesen Sie im Anschluss an diese Einführung in Hadoop in der Cloud und die Big Data-Analyse nachstehende Ressourcen.


### Hadoop-Dokumentation für HDInsight

* [HDInsight-Dokumentation](https://azure.microsoft.com/documentation/services/hdinsight/): Die Dokumentationsseite für Hadoop in Azure HDInsight mit Links zu Artikeln, Videos und weiteren Ressourcen.

* [Erste Schritte mit Hadoop in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): Ein Lernprogramm für die schnelle Bereitstellung von HDInsight-Hadoop-Clustern und Ausführung von Hive-Beispielabfragen.

* [Erste Schritte mit Spark in HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): Ein Schnellstart-Tutorial zum Erstellen eines Spark-Clusters und Ausführen von interaktiven Spark SQL-Abfragen.

* [Erste Schritte mit R Server in HDInsight (Vorschau)](hdinsight-hadoop-r-server-get-started.md): Informationen zum Einstieg in die Verwendung von R Server in HDInsight Premium.

* [Bereitstellen von HDInsight-Clustern](hdinsight-hadoop-provision-linux-clusters.md): Erfahren Sie, wie ein HDInsight Hadoop-Cluster über das Azure-Portal, die Azure-Befehlszeilenschnittstelle (Azure-CLI) oder Azure PowerShell bereitgestellt wird.


### Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: Erfahren Sie mehr über die Apache Hadoop-Softwarebibliothek, ein Framework, das die verteilte Verarbeitung großer Datasets in Computerclustern ermöglicht.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: Erfahren Sie mehr über die Architektur und das Design von Hadoop Distributed File System (HDFS), dem primären Speichersystem, das von Hadoop-Anwendungen verwendet wird.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce Tutorial</a>: Erfahren Sie mehr über das Programmierframework für das Schreiben von Hadoop-Anwendungen, die riesige Datenmengen auf großen Compute-Knoten parallel verarbeiten.


### Microsoft Business Intelligence

Bekannte Business-Intelligence-Tools wie Excel, PowerPivot, SQL Server Analysis Services und SQL Server Reporting Services rufen Daten, die in HDInsight integriert sind, entweder über das Add-In Power Query oder den Microsoft Hive-ODBC-Treiber ab, analysieren sie und erstellen einen Bericht.

Diese BI-Tools sind hilfreich für Ihre Big Data-Analyse:

* [Verbinden von Excel mit Hadoop über Power Query](hdinsight-connect-excel-power-query.md): Erfahren Sie, wie Sie Excel mithilfe von Microsoft Power Query mit dem Azure-Speicherkonto verbinden, in dem die Daten für Ihren HDInsight-Cluster gespeichert werden. Windows-Arbeitsstation ist erforderlich. Funktioniert mit Windows- oder Linux-basiertem Cluster.

* [Verbinden von Excel über den Microsoft Hive-ODBC-Treiber mit Hadoop](hdinsight-connect-excel-hive-odbc-driver.md): Informationen zum Importieren von Daten aus HDInsight mit den Microsoft Hive-ODBC-Treiber. Windows-Arbeitsstation ist erforderlich. Funktioniert mit Windows- oder Linux-basiertem Cluster.

* [Microsoft-Cloudplattform](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): Entdecken Sie Power BI für Office 365, laden Sie die Testversion von SQL Server herunter, und richten Sie SharePoint Server 2013 und SQL Server BI ein.

* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx).

* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx).




[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/

<!---HONumber=AcomDC_1005_2016-->