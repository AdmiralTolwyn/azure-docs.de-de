---
title: Entwickeln von Python MapReduce-Aufträgen für das Streaming mit HDInsight – Azure
description: Erfahren Sie, wie Sie Python für Streaming-MapReduce-Aufträge verwenden. Hadoop stellt eine Streaming-API für MapReduce zur Verfügung, mit der Sie in anderen Sprachen als Java schreiben können.
services: hdinsight
keyword: mapreduce python,python map reduce,python mapreduce
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 5ed61eb05d0aa4360e042097d6b8c632c17ea59c
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51008724"
---
# <a name="develop-python-streaming-mapreduce-programs-for-hdinsight"></a>Entwickeln von Streaming-MapReduce-Programmen für HDInsight mit Python

Erfahren Sie, wie Sie Python für Vorgänge in Streaming-MapReduce-Aufträgen verwenden. Hadoop stellt eine Streaming-API für MapReduce zur Verfügung, mit der Sie Map- und Reduce-Funktionen in anderen Sprache als Java schreiben können. Die Schritte in diesem Dokument haben die Komponenten für Mapper und Reducer in Python implementiert.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Linux-basierter Hadoop-Cluster in HDInsight

  > [!IMPORTANT]
  > Die Schritte in diesem Dokument erfordern einen HDInsight-Cluster mit Linux. Linux ist das einzige Betriebssystem, das unter HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [Welche Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Ein Texteditor

  > [!IMPORTANT]
  > Im Text-Editor muss LF als Zeilenende verwendet werden. Wenn ein Zeilenende von CRLF verwendet wird, verursacht dies beim Ausführen des MapReduce-Auftrags auf Linux-basierten HDInsight-Clustern Fehler.

* Die Befehle `ssh` und `scp` oder [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-3.8.0)

## <a name="word-count"></a>Wortzählung

Dieses Beispiel ist eine einfache Wortzahlermittlung, das in eine Mapper- und Reducer-Funktion von Python implementiert wird. Die Mapper-Funktion teilt Sätze in einzelne Wörter auf und die Reducer-Funktion aggregiert die Wörter und Zähler, um die Ausgabe zu erzeugen.

Im folgenden Flußdiagramm ist dargestellt, was in den Mapper- und Reducer-Funktionen geschieht.

![Darstellung des MapReduce-Prozesses](./media/apache-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>Streaming-MapReduce

Mit Hadoop können Sie eine Datei angeben, die die von einem Auftrag verwendete Logik für den Mapper und Reducer enthält. Bestimmte Anforderungen an die Logik für Mapper und Reducer:

* **Eingabe**: Die Komponenten für Mapper und Reducer müssen Eingabedaten von STDIN lesen.
* **Ausgabe**: Die Komponenten für Mapper und Reducer müssen Ausgabedaten an STDOUT schreiben.
* **Datenformat**: Die verwendeten und erzeugten Daten müssen ein Schlüssel-/Wertpaar darstellen, das durch ein Tabulatorzeichen getrennt werden muss.

Python kann diese Anforderungen einfach mithilfe des Moduls `sys` zum Lesen von STDIN und mithilfe des Moduls `print` für die Ausgabe an STDOUT behandeln. Der Rest besteht einfach aus der Formatierung der Daten mit einem Tabulatorzeichen (`\t`) zwischen Schlüssel und Wert.

## <a name="create-the-mapper-and-reducer"></a>Erstellen von Mapper und Reducer

1. Erstellen Sie eine Datei mit dem Namen `mapper.py`, und verwenden Sie den folgenden Inhalt für die Datei:

   ```python
   #!/usr/bin/env python

   # Use the sys module
   import sys

   # 'file' in this case is STDIN
   def read_input(file):
       # Split each line into words
       for line in file:
           yield line.split()

   def main(separator='\t'):
       # Read the data using read_input
       data = read_input(sys.stdin)
       # Process each word returned from read_input
       for words in data:
           # Process each word
           for word in words:
               # Write to STDOUT
               print '%s%s%d' % (word, separator, 1)

   if __name__ == "__main__":
       main()
   ```

2. Erstellen Sie eine Datei mit dem Namen **reducer.py**, und verwenden Sie den folgenden Inhalt für die Datei:

   ```python
   #!/usr/bin/env python

   # import modules
   from itertools import groupby
   from operator import itemgetter
   import sys

   # 'file' in this case is STDIN
   def read_mapper_output(file, separator='\t'):
       # Go through each line
       for line in file:
           # Strip out the separator character
           yield line.rstrip().split(separator, 1)

   def main(separator='\t'):
       # Read the data using read_mapper_output
       data = read_mapper_output(sys.stdin, separator=separator)
       # Group words and counts into 'group'
       #   Since MapReduce is a distributed process, each word
       #   may have multiple counts. 'group' will have all counts
       #   which can be retrieved using the word as the key.
       for current_word, group in groupby(data, itemgetter(0)):
           try:
               # For each word, pull the count(s) for the word
               #   from 'group' and create a total count
               total_count = sum(int(count) for current_word, count in group)
               # Write to stdout
               print "%s%s%d" % (current_word, separator, total_count)
           except ValueError:
               # Count was not a number, so do nothing
               pass

   if __name__ == "__main__":
       main()
   ```

## <a name="run-using-powershell"></a>Ausführen mithilfe von PowerShell

Stellen Sie mithilfe des folgenden PowerShell-Skripts sicher, dass Ihre Dateien über die richtigen Zeilenenden verfügen:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=138-140)]

Verwenden Sie das folgende PowerShell-Skript, um Dateien hochzuladen, den Auftrag auszuführen und die Ausgabe anzuzeigen:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=5-134)]

## <a name="run-from-an-ssh-session"></a>Ausführen in einer SSH-Sitzung

1. Verwenden Sie in Ihrer Entwicklungsumgebung den folgenden Befehl im gleichen Verzeichnis, in dem sich auch `mapper.py` und `reducer.py` befinden:

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    Ersetzen Sie `username` durch den SSH-Benutzernamen für Ihren Cluster und `clustername` durch den Namen Ihres Clusters.

    Dieser Befehl kopiert die Dateien aus dem lokalen System auf den Stammknoten.

    > [!NOTE]
    > Wenn Sie zum Schutz Ihres SSH-Kontos ein Kennwort verwendet haben, werden Sie zur Eingabe dieses Kennworts aufgefordert. Wenn Sie einen SSH-Schlüssel verwendet haben, müssen Sie möglicherweise den `-i` -Parameter und den Pfad zum privaten Schlüssel angeben. Beispiel: `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

2. Herstellen einer Verbindung zum Cluster mithilfe von SSH:

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Um sicherzustellen, dass „mapper.py“ und „reducer.py“ über die richtigen Zeilenenden verfügen, verwenden Sie folgende Befehle:

    ```bash
    perl -pi -e 's/\r\n/\n/g' mapper.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. Verwenden Sie zum Starten des MapReduce-Auftrags den folgenden Befehl.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    Dieser Befehl besteht aus den folgenden Komponenten:

   * **hadoop-streaming.jar**: Wird verwendet, wenn Streaming-MapReduce-Vorgänge ausgeführt werden. Es verbindet Hadoop mit dem von Ihnen bereitgestellten externen MapReduce-Code.

   * **-files**: Fügt die bestimmten Dateien dem MapReduce-Auftrag hinzu.

   * **-mapper**: Teilt Hadoop mit, welche Datei als Mapper verwendet werden soll.

   * **-reducer**: Teilt Hadoop mit, welche Datei als Reducer verwendet werden soll.

   * **-input**: Die Eingabedatei, deren Wörter gezählt werden sollen.

   * **-output**: Das Verzeichnis, in das die Ausgabe geschrieben wird.

    Sobald der MapReduce-Auftrag ausgeführt wird, wird der Prozess als Prozentsatz dargestellt.

        15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%


5. Verwenden Sie den folgenden Befehl, um die Ausgabe anzuzeigen:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Dieser Befehl zeigt eine Liste mit Wörtern und der Häufigkeit ihres Auftretens.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Streaming-MapReduce-Aufträge mit HDInsight verwenden, können Sie mithilfe der nachfolgenden Links andere Möglichkeiten für die Arbeit mit Azure HDInsight untersuchen.

* [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md)
* [Verwenden von Pig mit HDInsight](hdinsight-use-pig.md)
* [Verwenden von MapReduce-Aufträgen mit HDInsight](hdinsight-use-mapreduce.md)
