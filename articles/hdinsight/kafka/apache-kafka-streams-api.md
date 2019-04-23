---
title: 'Tutorial: Verwenden der Apache Kafka Streams-API – Azure HDInsight '
description: Erfahren Sie, wie Sie die Apache Kafka Streams-API mit Kafka in HDInsight verwenden. Mit dieser API können Sie eine Streamverarbeitung zwischen Themen in Kafka ausführen.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/02/2019
ms.openlocfilehash: 1e02eaeae4757a9a41ec59be81c3d9510d035232
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273820"
---
# <a name="tutorial-apache-kafka-streams-api"></a>Tutorial: Apache Kafka Streams-API

Hier erfahren Sie, wie Sie eine Anwendung erstellen, die die Apache Kafka Streams-API verwendet, und diese mit Kafka in HDInsight ausführen. 

Die in diesem Tutorial verwendete Anwendung ist eine Streamingwörterzählung. Sie liest Textdaten aus einem Kafka-Thema, extrahiert einzelne Wörter, und speichert dann Word und Anzahl in einem anderen Kafka-Thema.

> [!NOTE]  
> Die Kafka-Streamverarbeitung erfolgt häufig über Apache Spark oder Apache Storm. In der Kafka-Version 1.1.0 (HDInsight 3.5 und 3.6) wurde die Kafka Streams-API eingeführt. Mit dieser API können Sie Datenstreams zwischen Eingabe- und Ausgabethemen transformieren. In einigen Fällen kann dies eine Alternative zum Erstellen einer Spark- oder Storm-Streaminglösung sein. 
>
> Weitere Informationen zu Kafka Streams finden Sie in der Dokumentation [Intro to Streams](https://kafka.apache.org/10/documentation/streams/) (Einführung in Streams) auf Apache.org.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Grundlegendes zum Code
> * Erstellen und Bereitstellen der Anwendung
> * Konfigurieren von Kafka-Themen
> * Ausführen des Codes

## <a name="prerequisites"></a>Voraussetzungen

* Ein Kafka-auf-HDInsight-3.6-Cluster. Informationen zum Erstellen eines Clusters für Kafka in HDInsight finden Sie im Dokument [Schnellstart: Erstellen eines Apache Kafka-Clusters in HDInsight](apache-kafka-get-started.md).

* Führen Sie die im Dokument [Apache Kafka Producer- und Consumer-APIs](apache-kafka-producer-consumer-api.md) beschriebenen Schritte aus. Die Schritte in diesem Dokument verwenden die Beispielanwendung und die Themen, die in diesem Tutorial erstellt werden.

* [Version 8 des Java Developer Kit (JDK)](https://aka.ms/azure-jdks) oder eine vergleichbare Lösung (etwa OpenJDK).

* Ordnungsgemäße [Installation](https://maven.apache.org/install.html) von [Apache Maven](https://maven.apache.org/download.cgi) (gemäß Apache).  Maven ist ein Projekterstellungssystem für Java-Projekte.

* Einen SSH-Client. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Grundlegendes zum Code

Die exemplarische Anwendung befindet sich unter [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) im `Streaming`-Unterverzeichnis. Die Anwendung besteht aus zwei Dateien:

* `pom.xml`: Diese Datei definiert die Projektabhängigkeiten, Java-Version und Verpackungsmethoden.
* `Stream.java`: Diese Datei implementiert die Streaminglogik.

### <a name="pomxml"></a>Pom.Xml

Wichtige Informationen zur `pom.xml`-Datei:

* Abhängigkeiten: Dieses Projekt hängt von der Kafka Streams-API ab, die im `kafka-clients`-Paket bereitgestellt wird. Der folgende XML-Code definiert diese Abhängigkeit:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    > [!NOTE]  
    > Der `${kafka.version}`-Eintrag wird im `<properties>..</properties>`-Abschnitt von `pom.xml` deklariert und ist für die Kafka-Version des HDInsight-Clusters konfiguriert.

* Plug-Ins: Maven-Plug-Ins bieten verschiedene Funktionen. In diesem Projekt werden die folgenden Plug-Ins verwendet:

    * `maven-compiler-plugin`: Wird verwendet, um die vom Projekt verwendete Java-Version auf 8 festzulegen. Java 8 ist für HDInsight 3.6 erforderlich.
    * `maven-shade-plugin`: Wird zum Generieren einer Uber-JAR-Datei verwendet, die diese Anwendung sowie alle Abhängigkeiten enthält. Es wird auch zum Festlegen des Einstiegspunkts der Anwendung verwendet, damit Sie die JAR-Datei direkt ausführen können, ohne die Hauptklasse angeben zu müssen.

### <a name="streamjava"></a>Stream.Java

Die Datei [Stream.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Streaming/src/main/java/com/microsoft/example/Stream.java) implementiert mit der Streams-API eine Wörterzählanwendung. Sie liest Daten aus einem Kafka-Thema mit dem Namen `test` und schreibt die Wörterzahlen in ein Thema namens `wordcounts`.

Der folgende Code definiert die Wörterzählanwendung:

```java
package com.microsoft.example;

import org.apache.kafka.common.serialization.Serde;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KStreamBuilder;

import java.util.Arrays;
import java.util.Properties;

public class Stream
{
    public static void main( String[] args ) {
        Properties streamsConfig = new Properties();
        // The name must be unique on the Kafka cluster
        streamsConfig.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-example");
        // Brokers
        streamsConfig.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, args[0]);
        // SerDes for key and values
        streamsConfig.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        streamsConfig.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());

        // Serdes for the word and count
        Serde<String> stringSerde = Serdes.String();
        Serde<Long> longSerde = Serdes.Long();

        KStreamBuilder builder = new KStreamBuilder();
        KStream<String, String> sentences = builder.stream(stringSerde, stringSerde, "test");
        KStream<String, Long> wordCounts = sentences
                .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))
                .map((key, word) -> new KeyValue<>(word, word))
                .countByKey("Counts")
                .toStream();
        wordCounts.to(stringSerde, longSerde, "wordcounts");

        KafkaStreams streams = new KafkaStreams(builder, streamsConfig);
        streams.start();

        Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
    }
}
```

## <a name="build-and-deploy-the-example"></a>Erstellen und Bereitstellen des Beispiels

Führen Sie die folgenden Schritte aus, um das Projekt in Ihrem Cluster für Kafka in HDInsight zu erstellen und bereitzustellen:

1. Legen Sie Ihr aktuelles Verzeichnis auf den Speicherort des Verzeichnisses `hdinsight-kafka-java-get-started-master\Streaming` fest, und erstellen Sie dann mithilfe des folgenden Befehls ein JAR-Paket:

    ```cmd
    mvn clean package
    ```

    Dieser Befehl erstellt das Paket unter `target/kafka-streaming-1.0-SNAPSHOT.jar`.

2. Ersetzen Sie `sshuser` durch den SSH-Benutzer für Ihren Cluster und `clustername` durch den Namen Ihres Clusters. Verwenden Sie den folgenden Befehl, um die Datei `kafka-streaming-1.0-SNAPSHOT.jar` in Ihren HDInsight-Cluster zu kopieren. Geben Sie bei entsprechender Aufforderung das Kennwort für das SSH-Benutzerkonto ein.

    ```cmd
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```

## <a name="create-apache-kafka-topics"></a>Erstellen von Apache Kafka-Themen

1. Ersetzen Sie `sshuser` durch den SSH-Benutzer für Ihren Cluster und `CLUSTERNAME` durch den Namen Ihres Clusters. Geben Sie den folgenden Befehl ein, um eine SSH-Verbindung mit dem Cluster zu öffnen. Geben Sie bei entsprechender Aufforderung das Kennwort für das SSH-Benutzerkonto ein.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Installieren Sie den JSON-Befehlszeilenprozessor [jq](https://stedolan.github.io/jq/). Geben Sie über die geöffnete SSH-Verbindung den folgenden Befehl ein, um `jq` zu installieren:

    ```bash
    sudo apt -y install jq
    ```

3. Richten Sie Umgebungsvariablen ein. Ersetzen Sie `PASSWORD` und `CLUSTERNAME` durch das Kennwort für die Clusteranmeldung bzw. durch den Clusternamen, und geben Sie anschließend den folgenden Befehl ein:

    ```bash
    export password='PASSWORD'
    export clusterNameA='CLUSTERNAME'
    ```

4. Extrahieren Sie den Clusternamen mit korrekter Groß-/Kleinschreibung. Die tatsächliche Schreibweise des Clusternamens kann je nach Clustererstellung anders sein als erwartet. Dieser Befehl ruft den Namen mit korrekter Groß-/Kleinschreibung ab, speichert ihn in einer Variablen und zeigt anschließend den Namen mit korrekter Groß-/Kleinschreibung sowie den Namen an, den Sie zuvor angegeben haben. Geben Sie den folgenden Befehl ein:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "https://$clusterNameA.azurehdinsight.net/api/v1/clusters" \
  	| jq -r '.items[].Clusters.cluster_name')
    echo $clusterName, $clusterNameA
    ```

5. Um die Kafka-Brokerhosts und die Apache Zookeeper-Hosts abzurufen, verwenden Sie die folgenden Befehle. Geben Sie bei der entsprechenden Aufforderung das Kennwort des Anmeldekontos (Administrator) für den Cluster ein. Sie werden zweimal aufgefordert, das Kennwort einzugeben.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G \
    https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER \
  	| jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`;
    export KAFKABROKERS=`curl -sS -u admin:$password -G \
    https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER \
  	| jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`;
    ```

6. Um die vom Streamingvorgang verwendeten Themen zu erstellen, verwenden Sie die folgenden Befehle:

    > [!NOTE]  
    > Sie erhalten möglicherweise eine Fehlermeldung, dass das `test`-Thema bereits vorhanden ist. Dies ist in Ordnung, da es im Producer- und Consumer-API-Tutorial erstellt worden sein könnte.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    Die Themen werden für folgende Zwecke verwendet:

   * `test`: In diesem Thema werden Datensätze empfangen. Die Streaminganwendung liest von dort.
   * `wordcounts`: In diesem Thema speichert die Streaminganwendung ihre Ausgabe.
   * `RekeyedIntermediateTopic`: Mit diesem Thema werden Daten neu partitioniert, wenn die Anzahl vom `countByKey`-Operator aktualisiert wird.
   * `wordcount-example-Counts-changelog`: Dieses Thema ist ein Zustandsspeicher, den der `countByKey`-Vorgang verwendet.

     > [!IMPORTANT]  
     > Kafka in HDInsight kann auch für das automatische Erstellen von Themen konfiguriert werden. Weitere Informationen finden Sie im Dokument [Konfigurieren von Apache Kafka in HDInsight zum automatischen Erstellen von Themen](apache-kafka-auto-create-topics.md).

## <a name="run-the-code"></a>Ausführen des Codes

1. Um die Streaminganwendung als Hintergrundprozess zu starten, verwenden Sie den folgenden Befehl:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    > [!NOTE]  
    > Möglicherweise erhalten Sie eine Warnung über Apache log4j. Sie können dies ignorieren.

2. Um Datensätze an das `test`-Thema zu senden, starten Sie die Producer-Anwendung mit folgendem Befehl:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

3. Nachdem der Producer abgeschlossen ist, zeigen Sie die im `wordcounts`-Thema gespeicherten Informationen mit folgendem Befehl an:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    > [!NOTE]  
    > Die `--property`-Parameter teilen dem Konsolenconsumer mit, dass der Schlüssel (Wort) zusammen mit der Anzahl (Wert) ausgegeben werden soll. Diese Parameter konfigurieren auch das Deserialisierungsprogramm, das beim Lesen dieser Werte aus Kafka verwendet werden soll.

    Die Ausgabe sieht in etwa wie folgender Text aus:
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    > [!NOTE]  
    > Der Parameter `--from-beginning` konfiguriert den Consumer so, dass er am Anfang der im Thema gespeicherten Datensätze beginnt. Die Anzahl erhöht sich jedes Mal, wenn ein Wort angetroffen wird, sodass das Thema mehrere Einträge für jedes Wort enthält – mit zunehmender Anzahl.

4. Drücken Sie __STRG+C__, um den Producer zu beenden. Drücken Sie __STRG+C__ erneut, um die Anwendung und den Consumer zu beenden.

5. Verwenden Sie die folgenden Befehle, um die vom Streamingvorgang verwendeten Themen zu löschen:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie die Apache Kafka Streams-API mit Kafka in HDInsight verwenden. Verwenden Sie Folgendes, um weitere Informationen zur Verwendung von Kafka zu erhalten:

* [Analysieren von Apache Kafka-Protokollen](apache-kafka-log-analytics-operations-management.md)
* [Replizieren von Daten zwischen Apache Kafka-Clustern](apache-kafka-mirroring.md)
