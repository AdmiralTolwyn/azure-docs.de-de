---
title: Verwenden von Hadoop Pig in HDInsight
description: Erfahren Sie, wie Sie Pig mit Hadoop in HDInsight verwenden.
services: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.openlocfilehash: 7f469efb536f8c2dfc95cfe1770544b93c06b29f
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597258"
---
# <a name="use-pig-with-hadoop-on-hdinsight"></a>Verwenden von Pig mit Hadoop in HDInsight

Erfahren Sie, wie [Apache Pig](http://pig.apache.org/) mit HDInsight verwendet wird.

Pig ist eine Plattform zum Erstellen von Programmen für Hadoop mithilfe der prozeduralen Sprache *Pig Latin*. Pig ist eine Alternative zu Java für das Erstellen von *MapReduce* -Lösungen, und es ist in Azure HDInsight enthalten. In der folgenden Tabelle finden Sie verschiedene Möglichkeiten der Verwendung von Pig mit HDInsight:

| **Verwenden Sie dies**, wenn Sie Folgendes wünschen: | ... eine **interaktive** Shell | ...**Batchverarbeitung** | ...mit diesem **Clusterbetriebssystem** | ...von diesem **Clientbetriebssystem** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X oder Windows |
| [REST-API](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux oder Windows |Linux, Unix, Mac OS X oder Windows |
| [.NET SDK für Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux oder Windows |Windows (vorläufig) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux oder Windows |Windows |

> [!IMPORTANT]
> Linux ist das einzige Betriebssystem, das unter HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [Welche Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="why"></a>Warum Pig?

Eine der Herausforderungen der Verarbeitung von Daten mithilfe von MapReduce in Hadoop ist die Implementierung der Verarbeitungslogik mit nur einer Karte und einer Reduce-Funktion. Für die komplexe Verarbeitung muss die diese häufig in mehrere MapReduce-Vorgänge unterteilt werden, die miteinander verkettet sind, um das gewünschte Ergebnis zu erzielen.

Pig ermöglicht Ihnen, die Verarbeitung als eine Reihe von Transformationen zu definieren, die die Daten durchlaufen, um die gewünschte Ausgabe zu erzeugen.

Mit der Pig Latin-Sprache können Sie den Datenfluss von Rohdaten durch eine oder mehrere Transformationen beschreiben, um die gewünschte Ausgabe zu erzeugen. Pig Latin-Programme folgen diesem allgemeinen Muster:

* **Laden**: Die zu verändernden Daten werden aus dem Dateisystem gelesen

* **Transformieren**: Manipulation der Daten

* **Ablage oder Speicherung**: Ausgabe der Daten auf dem Bildschirm oder Speicherung zur Weiterverarbeitung

### <a name="user-defined-functions"></a>Benutzerdefinierte Funktionen

Pig Latin unterstützt auch benutzerdefinierte Funktionen (UDF), wodurch Sie externe Komponenten aufrufen können, die in Pig Latin schwer zu modellierende Logik implementieren.

Weitere Informationen zu Pig Latin finden Sie unter [Pig Latin-Referenzhandbuch 1](http://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) und [Pig Latin-Referenzhandbuch 2](http://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

Ein Beispiel für benutzerdefinierte Funktionen mit Pig finden Sie in den folgenden Dokumenten:

* [Verwenden von DataFu mit Pig in HDInsight](apache-hadoop-use-pig-datafu-udf.md) – DataFu ist eine Sammlung nützlicher UDFs, die von Apache verwaltet werden.
* [Verwenden von Python mit Pig und Hive in HDInsight](python-udf-hdinsight.md)
* [Verwenden von C# mit Hive und Pig in HDInsight](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>Beispieldaten

HDInsight enthält verschiedene Beispieldatasets, die in den Verzeichnissen `/example/data` und `/HdiSamples` gespeichert sind. Diese Verzeichnisse befinden sich im Standardspeicher für den Cluster. Im Pig-Beispiel in diesem Dokument wird die Datei *log4j* aus `/example/data/sample.log` verwendet.

Jedes Protokoll innerhalb der Datei besteht aus einer Reihe von Feldern, unter denen sich ein Feld namens `[LOG LEVEL]` befindet, das die Art und den Schweregrad des jeweiligen Fehlers anzeigt, beispielsweise:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Die Protokollebene im vorherigen Beispiel ist ERROR.

> [!NOTE]
> Sie können auch eine log4j-Datei generieren, indem Sie das [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) -Protokollierungstool verwenden und die Datei dann in Ihren Blob hochladen. Anweisungen hierzu finden Sie unter [Hochladen von Daten in HDInsight](../hdinsight-upload-data.md) . Weitere Informationen zur Verwendung von Blobs in Azure Storage mit HDInsight finden Sie unter [Verwenden von Azure Blob Storage mit HDInsight](../hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Beispielauftrag

Mit dem folgenden Pig Latin-Auftrag wird die Datei `sample.log` aus dem Standardspeicher für den HDInsight-Cluster geladen. Er führt dann eine Reihe von Transformationen durch, die als Ergebnis anzeigen, wie oft jede Protokollebene in den Eingabedaten aufgetreten ist. Die Ergebnisse werden an STDOUT geschrieben.

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

In der folgenden Abbildung ist zusammengefasst, wie sich die einzelnen Transformationen auf die Daten auswirken.

![Grafische Darstellung der Transformationen][image-hdi-pig-data-transformation]

## <a id="run"></a>Pig Latin-Auftrag ausführen

HDInsight kann Pig Latin-Aufträge mithilfe verschiedener Methoden ausführen. Die folgende Tabelle hilft Ihnen bei der Entscheidung, welche Methode für Sie geeignet ist. Folgen Sie anschließend dem Link für eine exemplarische Vorgehensweise.

| **Verwenden Sie dies**, wenn Sie Folgendes wünschen: | ... eine **interaktive** Shell | ...**Batchverarbeitung** | ...mit diesem **Clusterbetriebssystem** | ... von diesem **Client** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X oder Windows |
| [Curl](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux oder Windows |Linux, Unix, Mac OS X oder Windows |
| [.NET SDK für Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux oder Windows |Windows (vorläufig) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux oder Windows |Windows |

> [!IMPORTANT]
> Linux ist das einzige Betriebssystem, das unter HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [Welche Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="pig-and-sql-server-integration-services"></a>Pig und SQL Server Integration Services

Sie können mit SQL Server Integration Services (SSIS) einen Pig-Auftrag ausführen. Das Azure Feature Pack für SSIS bietet die folgenden Komponenten, die mit Pig-Aufträgen in HDInsight funktionieren.

* [Pig-Aufgabe in Azure HDInsight][pigtask]

* [Verbindungs-Manager für Azure-Abonnements][connectionmanager]

[Hier][ssispack] erfahren Sie mehr zum Azure Feature Pack für SSIS.

## <a id="nextsteps"></a>Nächste Schritte
Nachdem Sie erfahren haben, wie Sie Pig mit HDInsight verwenden, können Sie mithilfe der nachfolgenden Links andere Möglichkeiten für die Arbeit mit Azure HDInsight untersuchen.

* [Hochladen von Daten in HDInsight](../hdinsight-upload-data.md)
* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
* [Verwenden von Sqoop mit HDInsight](hdinsight-use-sqoop.md)
* [Verwenden von Oozie mit HDInsight](../hdinsight-use-oozie.md)
* [Verwenden von MapReduce-Aufträgen mit HDInsight][hdinsight-use-mapreduce]

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
