---
title: 'Verwenden von Apache Beeline mit Apache Hive: Azure HDInsight'
description: Erfahren Sie, wie Sie den Beeline-Client verwenden, um Hive-Abfragen mit Hadoop in HDInsight auszuführen. Beeline ist ein Dienstprogramm zum Arbeiten mit HiveServer2 über JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 39217a883863fd663b02cafea699dcbc4e070dfb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435725"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Verwenden des Apache Beeline-Clients mit Apache Hive

Hier erfahren Sie, wie Sie [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) verwenden, um Hive-Abfragen unter HDInsight auszuführen.

Beeline ist ein Hive-Client, der auf den Hauptknoten des HDInsight-Clusters enthalten ist. Informationen zum lokalen Installieren von Beeline finden Sie weiter unten im Abschnitt [Installieren des Beeline-Clients](#install-beeline-client). Beeline verwendet JDBC, um eine Verbindung mit HiveServer2 herzustellen, einem Dienst, der in Ihrem HDInsight-Cluster gehostet wird. Mit Beeline können Sie auch remote über das Internet auf Hive unter HDInsight zugreifen. Die folgenden Beispiele zeigen die am häufigsten verwendeten Verbindungszeichenfolgen zum Herstellen einer Verbindung mit HDInsight aus Beeline:

## <a name="types-of-connections"></a>Arten von Verbindungen

### <a name="from-an-ssh-session"></a>Über eine SSH-Sitzung

Beim Herstellen einer Verbindung aus einer SSH-Sitzung mit einem Clusterhauptknoten können Sie anschließend eine Verbindung mit der `headnodehost`-Adresse an Port `10001` herstellen:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Über ein virtuelles Azure-Netzwerk

Beim Herstellen einer Verbindung von einem Client zu HDInsight über ein virtuelles Azure-Netzwerk müssen Sie den vollqualifizierten Domänennamen (FQDN) eines Clusterhauptknotens angeben. Da diese Verbindung direkt mit den Clusterknoten hergestellt wird, wird für die Verbindung Port `10001` verwendet:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Ersetzen Sie `<headnode-FQDN>` durch den vollqualifizierten Domänennamen eines Clusterhauptknotens. Verwenden Sie die Informationen im Dokument [Verwalten von HDInsight mithilfe der Apache Ambari-REST-API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes), um den vollqualifizierten Domänennamen eines Hauptknotens zu ermitteln.

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Mit einem Cluster des HDInsight-Enterprise-Sicherheitspakets (ESP) mit Kerberos

Beim Herstellen einer Verbindung vom Client mit einem ESP-Cluster (Enterprise-Sicherheitspaket), der mit Azure Active Directory (AAD)-DS auf einem Computer im selben Bereich des Clusters verknüpft ist, müssen Sie auch den Domänennamen `<AAD-Domain>` und den Namen eines Domänenbenutzerkontos mit Berechtigungen für den Zugriff auf den Cluster `<username>` angeben:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Ersetzen Sie `<username>` durch den Namen eines Kontos in der Domäne mit der Berechtigung für den Zugriff auf den Cluster. Ersetzen Sie `<AAD-DOMAIN>` durch den Namen der Azure Active Directory-Instanz (AAD), der der Cluster angehört. Verwenden Sie eine Zeichenfolge aus Großbuchstaben für den Wert `<AAD-DOMAIN>`, da die Anmeldeinformationen sonst nicht gefunden werden. Überprüfen Sie `/etc/krb5.conf` bei Bedarf auf die Bereichsnamen.

---

### <a name="over-public-or-private-endpoints"></a>Über öffentliche oder private Endpunkte

Wenn Sie eine Verbindung zu einem Cluster über öffentliche oder private Endpunkte herstellen möchten, müssen Sie den Kontonamen für die Clusteranmeldung (Standard ist `admin`) und das Kennwort angeben. Beispiel: Verwenden von Beeline von einem Clientsystem zum Herstellen einer Verbindung mit der `clustername.azurehdinsight.net`-Adresse. Diese Verbindung erfolgt über Port `443` und wird mithilfe von SSL verschlüsselt:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Oder beim privaten Endpunkt:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Ersetzen Sie `clustername` durch den Namen Ihres HDInsight-Clusters. Ersetzen Sie `admin` durch das Anmeldekonto für Ihren Cluster. Verwenden Sie bei ESP-Clustern den vollständigen Benutzerprinzipalnamen (z. B. user@domain.com). Ersetzen Sie `password` durch das Kennwort des Anmeldekontos für den Cluster.

Private Endpunkte zeigen auf einen Load Balancer im Tarif „Basic“, auf den nur über die VNETs zugegriffen werden kann, die mittels Peering in derselben Region verknüpft sind. Weitere Informationen finden Sie unter den [Einschränkungen im Zusammenhang mit globalem VNET-Peering und Load Balancern](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Sie können den `curl`-Befehl mit der Option `-v` verwenden, um Konnektivitätsprobleme mit öffentlichen oder privaten Endpunkten vor der Verwendung von Beeline zu beheben.

---

### <a id="sparksql"></a>Verwenden von Beeline mit Apache Spark

Apache Spark stellt eine eigene Implementierung von HiveServer2 bereit, die manchmal als Spark Thrift-Server bezeichnet wird. Bei diesem Dienst wird Spark SQL anstelle von Hive zum Auflösen von Abfragen verwendet und ermöglicht je nach Abfrage ggf. eine bessere Leistung.

#### <a name="through-public-or-private-endpoints"></a>Durch öffentliche oder private Endpunkte

Die verwendete Verbindungszeichenfolge ist etwas anders. Sie enthält `httpPath/sparkhive2` anstelle von `httpPath=/hive2`:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Oder beim privaten Endpunkt:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Ersetzen Sie `clustername` durch den Namen Ihres HDInsight-Clusters. Ersetzen Sie `admin` durch das Anmeldekonto für Ihren Cluster. Verwenden Sie bei ESP-Clustern den vollständigen Benutzerprinzipalnamen (z. B. user@domain.com). Ersetzen Sie `password` durch das Kennwort des Anmeldekontos für den Cluster.

Private Endpunkte zeigen auf einen Load Balancer im Tarif „Basic“, auf den nur über die VNETs zugegriffen werden kann, die mittels Peering in derselben Region verknüpft sind. Weitere Informationen finden Sie unter den [Einschränkungen im Zusammenhang mit globalem VNET-Peering und Load Balancern](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Sie können den `curl`-Befehl mit der Option `-v` verwenden, um Konnektivitätsprobleme mit öffentlichen oder privaten Endpunkten vor der Verwendung von Beeline zu beheben.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Vom Clusterhauptknoten oder in Azure Virtual Network mit Apache Spark

Wenn Sie direkt vom Clusterhauptknoten oder von einer Ressource, die sich in der gleichen Azure Virtual Network-Instanz wie der HDInsight-Cluster befindet, eine Verbindung herstellen, muss für den Spark Thrift-Server Port `10002` anstelle von Port `10001` verwendet werden. Das folgende Beispiel zeigt, wie eine direkte Verbindung mit dem Hauptknoten hergestellt wird:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Voraussetzungen

* Ein Hadoop-Cluster in HDInsight. Weitere Informationen finden Sie unter [Erste Schritte mit HDInsight unter Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Beachten Sie das [URI-Schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) für den primären Speicher Ihres Clusters. Beispiele: `wasb://` für Azure Storage, `abfs://` für Azure Data Lake Storage Gen2 oder `adl://` für Azure Data Lake Storage Gen1. Wenn die sichere Übertragung für Azure Storage aktiviert ist, lautet der URI `wasbs://`. Weitere Informationen finden Sie unter [Sichere Übertragung](../../storage/common/storage-require-secure-transfer.md).

* Option 1: Einen SSH-Client. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). In den meisten Schritten in diesem Dokument wird davon ausgegangen, dass Sie Beeline aus einer SSH-Sitzung für den Cluster verwenden.

* Option 2:  Ein lokaler Beeline-Client.

## <a id="beeline"></a>Ausführen einer Hive-Abfrage

Dieses Beispiel basiert auf der Verwendung des Beeline-Clients über eine SSH-Verbindung.

1. Öffnen Sie über den nachstehenden Code eine SSH-Verbindung mit dem Cluster. Ersetzen Sie `sshuser` durch den SSH-Benutzer für Ihren Cluster und `CLUSTERNAME` durch den Namen Ihres Clusters. Geben Sie bei entsprechender Aufforderung das Kennwort für das SSH-Benutzerkonto ein.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Stellen Sie mithilfe des folgenden Befehls mit Ihrem Beeline-Client eine Verbindung mit HiveServer2 aus Ihrer Open SSH-Sitzung her:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline-Befehle beginnen mit dem Zeichen `!`, z.B. `!help` zum Anzeigen der Hilfe. Jedoch kann `!` bei einigen Befehlen ausgelassen werden. `help` funktioniert beispielsweise auch.

    Es gibt ein `!sql`, das zum Ausführen von HiveQL-Anweisungen verwendet wird. HiveQL wird aber so häufig genutzt, dass Sie das vorangestellte `!sql` weglassen können. Die folgenden beiden Anweisungen sind gleichwertig:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    In einem neuen Cluster wird nur eine Tabelle aufgelistet: **hivesampletable**.

4. Verwenden Sie folgenden Befehl, um das Schema für „hivesampletable“ anzuzeigen:

    ```hiveql
    describe hivesampletable;
    ```

    Dieser Befehl gibt folgende Information zurück:

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Diese Information beschreibt die Spalten in der Tabelle.

5. Geben Sie die folgenden Anweisungen ein, um eine Tabelle mit dem Namen **log4jLogs** zu erstellen, indem Sie die über das HDInsight-Cluster bereitgestellten Beispieldaten verwenden: (Überarbeiten Sie dies je nach Bedarf basierend auf Ihrem [URI-Schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
        GROUP BY t4;
    ```

    Diese Anweisungen führen die folgenden Aktionen aus:

    * `DROP TABLE`: Wenn die Tabelle vorhanden ist, wird sie gelöscht.

    * `CREATE EXTERNAL TABLE`: Erstellt eine **externe** Tabelle in Hive. Externe Tabellen speichern nur die Tabellendefinition in Hive. Die Daten verbleiben an ihrem ursprünglichen Speicherort.

    * `ROW FORMAT`: Gibt an, wie die Daten formatiert werden. In diesem Fall werden die Felder in den einzelnen Protokollen durch Leerzeichen getrennt.

    * `STORED AS TEXTFILE LOCATION`: Wo die Daten gespeichert werden und in welchem Dateiformat.

    * `SELECT`: Wählt die Anzahl aller Zeilen aus, bei denen die Spalte **t4** den Wert **[ERROR]** enthält. Diese Abfrage gibt den Wert **3** zurück, da dieser Wert in drei Zeilen enthalten ist.

    * `INPUT__FILE__NAME LIKE '%.log'`: Hive versucht, das Schema auf alle Dateien im Verzeichnis anzuwenden. In diesem Fall enthält das Verzeichnis Dateien, die dem Schema nicht entsprechen. Um überflüssige Daten in den Ergebnissen zu vermeiden, weist diese Anweisung Hive an, nur Daten aus Dateien zurückzugeben, die auf „.log“ enden.

   > [!NOTE]  
   > Externe Tabellen sollten Sie verwenden, wenn Sie erwarten, dass die zugrunde liegenden Daten aus einer externen Quelle aktualisiert werden. Das könnte z.B. ein automatisierter Datenupload oder ein MapReduce-Vorgang sein.
   >
   > Durch das Löschen einer externen Tabelle werden **nicht** die Daten, sondern nur die Tabellendefinitionen gelöscht.

    Die Ausgabe dieses Befehls ähnelt dem folgenden Text:

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

6. Verwenden Sie `!exit`, um Beeline zu beenden.

## <a name="run-a-hiveql-file"></a>Ausführen einer HiveQL-Datei

Dies ist eine Fortsetzung des vorherigen Beispiels. Verwenden Sie die folgenden Schritte, um eine Datei zu erstellen und sie dann mit Beeline auszuführen.

1. Verwenden Sie den folgenden Befehl, um eine Datei mit dem Namen **query.hql**zu erstellen:

    ```bash
    nano query.hql
    ```

2. Verwenden Sie als Inhalt der Datei den folgenden Text. Diese Abfrage erstellt eine neue „interne“ Tabelle mit dem Namen **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Diese Anweisungen führen die folgenden Aktionen aus:

   * **CREATE TABLE IF NOT EXISTS**: Wenn die Tabelle noch nicht vorhanden ist, wird sie erstellt. Da das **EXTERNAL**-Schlüsselwort nicht verwendet wird, erstellt diese Anweisung eine interne Tabelle. Interne Tabellen werden im Hive-Data Warehouse gespeichert und vollständig von Hive verwaltet.
   * **ALS ORC GESPEICHERT** – Speichert die Daten im ORC-Format (Optimized Row Columnar). ORC ist ein stark optimiertes und effizientes Format zum Speichern von Hive-Daten.
   * **ÜBERSCHREIBEN EINFÜGEN ... SELECT**: Wählt die Zeilen aus der Tabelle **log4jLogs** aus, die den Wert **[ERROR]** enthalten. Dann werden die Daten in die Tabelle **errorLogs** eingefügt.

    > [!NOTE]  
    > Anders als bei externen Tabellen werden beim Löschen von internen Tabellen auch die zugrunde liegenden Daten gelöscht.

3. Verwenden Sie **STRG**+**X**, um die Datei zu speichern. Geben Sie dann **Y** ein, und drücken Sie die **EINGABETASTE**.

4. Verwenden Sie Folgendes, um die Datei mit Beeline auszuführen:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Der `-i`-Parameter startet Beeline und führt die Anweisungen in der Datei `query.hql` aus. Nach Abschluss der Abfrage wird die Eingabeaufforderung `jdbc:hive2://headnodehost:10001/>` angezeigt. Sie können eine Datei auch mit dem `-f`-Parameter ausführen, der Beeline beendet, nachdem die Abfrage abgeschlossen ist.

5. Um zu überprüfen, ob die Tabelle **errorLogs** erstellt wurde, verwenden Sie die folgende Anweisung, um alle Zeilen aus **errorLogs** zurückzugeben:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Es sollten drei Datenzeilen zurückgegeben werden, die alle in Spalte „t4“ den Wert **[FEHLER]** enthalten:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (0.813 seconds)

## <a name="install-beeline-client"></a>Installieren des Beeline-Clients

Zwar ist Beeline auf den Hauptknoten Ihres HDInsight-Clusters enthalten, doch möchten Sie den Client möglicherweise auf einem lokalen Computer installieren.  Die nachfolgenden Schritte zum Installieren von Beeline auf einem lokalen Computer basieren auf einem [Windows-Subsystem für Linux](https://docs.microsoft.com/windows/wsl/install-win10).

1. Aktualisieren Sie die Paketlisten. Geben Sie in der Bash-Shell den folgenden Befehl ein:

    ```bash
    sudo apt-get update
    ```

1. Installieren Sie Java, wenn dies noch nicht geschehen ist. Das können Sie mit dem Befehl `which java` überprüfen.

    1. Wenn kein Java-Paket installiert ist, geben Sie den folgenden Befehl ein:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Ergänzen Sie die BASHRC-Datei (normalerweise in „~/.bashrc“). Öffnen Sie die Datei mit `nano ~/.bashrc`, und fügen Sie dann am Ende der Datei die folgende Zeile hinzu:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Drücken Sie **STRG+X**, dann **Y** und schließlich die EINGABETASTE.

1. Geben Sie zum Herunterladen der Hadoop- und Beeline-Archive die folgenden Befehle ein:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Geben Sie zum Entpacken der Archive die folgenden Befehle ein:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Nehmen Sie weitere Änderungen an der BASHRC-Datei vor. Sie müssen den Pfad angeben, in den die Archive entpackt wurden. Wenn Sie das [Windows-Subsystem für Linux](https://docs.microsoft.com/windows/wsl/install-win10) verwenden und die Schritte genau befolgt haben, lautet der Pfad `/mnt/c/Users/user/`, wobei `user` für Ihren Benutzernamen steht.

    1. Öffnen Sie die Datei mit `nano ~/.bashrc`.
    1. Ändern Sie die folgenden Befehle mit dem entsprechenden Pfad, und geben Sie diese dann am Ende der BASHRC-Datei ein:

        ```bash
        export HADOOP_HOME=/$(path_where_the_archives_were_unpacked)/hadoop-2.7.3
        export HIVE_HOME=/$(path_where_the_archives_were_unpacked)/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Drücken Sie **STRG+X**, dann **Y** und schließlich die EINGABETASTE.

1. Schließen Sie die Bash-Sitzung, und öffnen Sie sie erneut.

1. Testen Sie die Verbindung. Verwenden Sie das Verbindungsformat aus dem Abschnitt [Über öffentliche oder private Endpunkte](#over-public-or-private-endpoints) weiter oben.

## <a name="next-steps"></a>Nächste Schritte

* Allgemeinere Informationen zu Hive in HDInsight finden Sie unter [Verwenden von Apache Hive mit Apache Hadoop in HDInsight](hdinsight-use-hive.md).

* Weitere Informationen zu anderen Methoden zur Verwendung von Hadoop in HDInsight finden Sie unter [Verwenden von MapReduce mit Apache Hadoop in HDInsight](hdinsight-use-mapreduce.md).
