---
title: Analysieren von Daten zu Flugverspätungen mit Hive in HDInsight – Azure | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Flugverspätungsdaten mithilfe von Hive in einem Linux-basierten HDInsight-Cluster analysieren und anschließend die Daten mithilfe von Sqoop in SQL-Datenbank exportieren.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0c23a079-981a-4079-b3f7-ad147b4609e5
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: cc5d48b881ba59679c19baa3506c3c14c0db8048
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2018
---
# <a name="analyze-flight-delay-data-by-using-hive-on-linux-based-hdinsight"></a>Analysieren von Flugverspätungsdaten mit Hive in Linux-basiertem HDInsight

Hier erfahren Sie, wie Sie Flugverspätungsdaten mithilfe von Hive in einem Linux-basierten HDInsight-Cluster analysieren und anschließend die Daten mithilfe von Sqoop in Azure SQL-Datenbank exportieren.

> [!IMPORTANT]
> Die Schritte in diesem Dokument erfordern einen HDInsight-Cluster mit Linux. Linux ist das einzige Betriebssystem, das unter Azure HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [Welche Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Voraussetzungen

* **Einen HDInsight-Cluster**. Schritte zum Erstellen eines neuen Linux-basierten HDInsight-Clusters finden Sie unter [Hadoop-Tutorial: Erste Schritte bei der Verwendung von Hadoop in HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Azure SQL-Datenbank**. Sie verwenden eine Azure SQL-Datenbank als Zieldatenspeicher. Wenn Sie keine SQL-Datenbank besitzen, finden Sie Informationen unter [Erstellen einer Azure SQL-Datenbank im Azure-Portal](../sql-database/sql-database-get-started.md).

* **Azure-Befehlszeilenschnittstelle**. Wenn Sie die Azure-Befehlszeilenschnittstelle noch nicht installiert haben, finden Sie alle erforderlichen Informationen unter [Installieren von Azure CLI 1.0](../cli-install-nodejs.md).

## <a name="download-the-flight-data"></a>Herunterladen der Flugdaten

1. Rufen Sie die Website von [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (RITA) auf.

2. Wählen Sie auf der Website die folgenden Werte aus:

   | NAME | Wert |
   | --- | --- |
   | Filter Year |2013 |
   | Filter Period |January |
   | Felder |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   Entfernen Sie die Häkchen bei allen anderen Feldern. 

3. Wählen Sie **Herunterladen** aus.

## <a name="upload-the-data"></a>Hochladen der Daten

1. Verwenden Sie den folgenden Befehl, um die ZIP-Datei in den Hauptknoten des HDInsight-Clusters hochzuladen:

    ```
    scp FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

    Ersetzen Sie *FILENAME* durch den Namen der ZIP-Datei. Ersetzen Sie *USERNAME* durch die SSH-Anmeldedaten für den HDInsight-Cluster. Ersetzen Sie *CLUSTERNAME* durch den Namen des HDInsight-Clusters.

   > [!NOTE]
   > Wenn Sie für die Authentifizierung Ihrer SSH-Anmeldung ein Kennwort verwenden, werden Sie zur Eingabe dieses Kennworts aufgefordert. Wenn Sie einen öffentlichen Schlüssel verwendet haben, müssen Sie möglicherweise den Parameter `-i` verwenden und den Pfad zum passenden privaten Schlüssel angeben. Beispiel: `scp -i ~/.ssh/id_rsa FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Wenn der Upload abgeschlossen ist, stellen Sie über SSH eine Verbindung mit dem Cluster her:

    ```ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net```

    Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Extrahieren Sie die ZIP-Datei mit folgendem Befehl:

    ```
    unzip FILENAME.zip
    ```

    Dieser Befehl extrahiert eine CSV-Datei, die ungefähr 60 MB groß ist.

4. Verwenden Sie den folgenden Befehl, um ein Verzeichnis in HDInsight-Speicher zu erstellen, und kopieren Sie dann die Datei in das Verzeichnis:

    ```
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="create-and-run-the-hiveql"></a>Erstellen und Ausführen von HiveQL

Importieren Sie mit den folgenden Schritten Daten aus der CSV-Datei in eine Hive-Tabelle namens **Delays**.

1. Erstellen und bearbeiten Sie mit dem folgenden Befehl eine neue Datei mit dem Namen **flightdelays.hql**:

    ```
    nano flightdelays.hql
    ```

    Verwenden Sie als Inhalt der Datei den folgenden Text:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

2. Drücken Sie zum Speichern der Datei STRG+X und anschließend Y.

3. Starten Sie Hive mit dem folgenden Befehl, und führen Sie die Datei **flightdelays.hql** aus:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. Öffnen Sie nach der Ausführung des Skripts __flightdelays.hql__ mithilfe des folgenden Befehls eine interaktive Beeline-Sitzung:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. Wenn Sie die `jdbc:hive2://localhost:10001/>`-Eingabeaufforderung erhalten, rufen Sie die Daten mit der folgenden Abfrage aus den importierten Flugverspätungsdaten ab:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Mit dieser Abfrage werden eine Liste von Orten, in denen Verspätungen infolge des Wetters auftraten, sowie die durchschnittliche Verspätung abgerufen. Die Liste wird anschließend in `/tutorials/flightdelays/output` gespeichert. Sqoop liest später die Daten an diesem Speicherort und exportiert sie in die Azure SQL-Datenbank.

6. Geben Sie zum Beenden von Beeline `!quit` an der Eingabeaufforderung ein.

## <a name="create-a-sql-database"></a>Erstellen einer SQL-Datenbank

Wenn Sie bereits über eine SQL-Datenbank verfügen, müssen Sie den Namen des Servers abrufen. Um den Servernamen im [Azure-Portal](https://portal.azure.com) zu finden, wählen Sie **SQL-Datenbanken**, und filtern Sie dann nach dem Namen der Datenbank, die Sie verwenden möchten. Der Name des Servers ist in der Spalte **SERVER** aufgelistet.

Wenn Sie noch nicht über eine SQL-Datenbank verfügen, nutzen Sie die Informationen in [Erstellen einer Azure SQL-Datenbank im Azure-Portal](../sql-database/sql-database-get-started.md), um eine Datenbank zu erstellen. Speichern Sie den für die Datenbank verwendeten Servernamen.

## <a name="create-a-sql-database-table"></a>Erstellen einer SQL-Datenbanktabelle

> [!NOTE]
> Es gibt viele Möglichkeiten, eine Verbindung mit der SQL-Datenbank herzustellen und eine Tabelle zu erstellen. Die folgenden Schritte verwenden [FreeTDS](http://www.freetds.org/) aus dem HDInsight-Cluster.


1. Verwenden Sie zum Installieren von FreeTDS den folgenden Befehl über eine SSH-Verbindung mit dem Cluster:

    ```
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. Sobald die Installation abgeschlossen ist, verwenden Sie den folgenden Befehl, um eine Verbindung mit dem SQL-Datenbank-Server herzustellen. Ersetzen Sie **serverName** durch den Namen des SQL-Datenbank-Servers. Ersetzen Sie **adminLogin** und **adminPassword** durch die Anmeldung für die SQL-Datenbank. Ersetzen Sie **databaseName** mit dem Datenbanknamen.

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -p 1433 -D <databaseName>
    ```

    Geben Sie nach entsprechender Aufforderung das Kennwort für die SQL-Datenbank-Administratoranmeldung ein.

    Eine Ausgabe ähnlich folgendem Text wird angezeigt:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. Geben Sie bei der Eingabeaufforderung `1>` folgende Zeilen ein:

    ```
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    Nach Eingabe der Anweisung `GO` werden die vorherigen Anweisungen ausgewertet. Diese Abfrage erstellt eine Tabelle mit dem Namen **delays** mit einem gruppierten Index.

    Stellen Sie mit der folgenden Abfrage sicher, dass die Tabelle erstellt wurde:

    ```
    SELECT * FROM information_schema.tables
    GO
    ```

    Die Ausgabe sieht in etwa wie folgender Text aus:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo     delays      BASE TABLE
    ```

5. EINGABE `exit` at the `1>` ein.

## <a name="export-data-with-sqoop"></a>Exportieren von Daten mit Sqoop

1. Verwenden Sie den folgenden Befehl, um zu überprüfen, ob Sqoop Ihre SQL-Datenbank erreichen kann:

    ```
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    Dieser Befehl gibt eine Liste mit Datenbanken zurück, in der auch die Datenbank enthalten ist, in der Sie bereits die Tabelle „delays“ erstellt haben.

2. Verwenden Sie den folgenden Befehl, um Daten aus „hivesampletable“ in die Tabelle „delays“ zu exportieren:

    ```
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop stellt eine Verbindung mit der Datenbank her, die die Tabelle „delays“ enthält, und exportiert Daten aus dem Verzeichnis `/tutorials/flightdelays/output` in die Tabelle „delays“.

3. Nach Abschluss des Sqoop-Befehls stellen Sie wie folgt über das Hilfsprogramm „tsql“ eine Verbindung mit der Datenbank her:

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    Überprüfen Sie mithilfe der folgenden Anweisungen, ob die Daten in die Tabelle „delays“ exportiert wurden:

    ```
    SELECT * FROM delays
    GO
    ```

    Es sollte eine Liste der Tabellendaten angezeigt werden. Geben Sie `exit` ein, um das Dienstprogramm tsql zu beenden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Arbeiten mit Daten in HDInsight finden Sie in den folgenden Artikeln:

* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
* [Verwenden von Oozie mit HDInsight][hdinsight-use-oozie]
* [Verwenden von Sqoop mit HDInsight][hdinsight-use-sqoop]
* [Verwenden von Pig mit HDInsight][hdinsight-use-pig]
* [Entwickeln von Java MapReduce-Programmen für Hadoop in HDInsight][hdinsight-develop-mapreduce]
* [Entwickeln von Streaming-MapReduce-Programmen für HDInsight mit Python][hdinsight-develop-streaming]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-develop-streaming]:hadoop/apache-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
