---
title: Verwenden von Hadoop Oozie-Workflows in Linux-basiertem HDInsight | Microsoft-Dokumentation
description: "Verwenden von Hadoop Oozie in Linux-basiertem HDInsight Erfahren Sie, wie Sie einen Oozie-Workflow definieren und einen Oozie-Auftrag übermitteln können."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 9d20050dada974c0c2a54399e2db7b9a289f7e89
ms.openlocfilehash: 48cc9c7181d83d2fe851b454eaf887b0483d3a01
ms.lasthandoff: 02/08/2017


---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-hdinsight"></a>Verwenden von Oozie mit Hadoop zum Definieren und Ausführen eines Workflows in Linux-basiertem HDInsight

[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Informationen zum Verwenden von Apache Oozie zum Definieren eines Workflows, der Hive und Sqoop verwendet, und anschließenden Ausführen des Workflows in einem Linux-basierten HDInsight-Cluster.

Apache Oozie ist ein Workflow-/Koordinationssystem zur Verwaltung von Hadoop-Jobs. Es ist in den Hadoop-Stapel integriert und unterstützt Hadoop-Aufträge für Apache MapReduce, Apache Pig, Apache Hive und Apache Sqoop. Oozie kann auch dazu verwendet werden, bestimmte Aufträge für ein System zu planen, beispielsweise Java-Programme oder Shellskripts.

> [!NOTE]
> Eine weitere Option zum Definieren von Workflows mit HDInsight ist Azure Data Factory. Weitere Informationen zu Azure Data Factory finden Sie unter [Verwenden von Pig und Hive mit Data Factory][azure-data-factory-pig-hive].

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, benötigen Sie Folgendes:

* **Azure-Befehlszeilenschnittstelle**: Siehe [Install and Configure the Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md)

* **Einen HDInsight-Cluster**: Siehe [Erste Schritte mit HDInsight unter Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

  > [!IMPORTANT]
  > Die Schritte in diesem Dokument erfordern einen HDInsight-Cluster mit Linux. Linux ist das einzige Betriebssystem, das unter HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [Ende des Lebenszyklus von HDInsight unter Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* **Eine Azure SQL-Datenbank** wird anhand der in diesem Dokument beschriebenen Schritte erstellt.

## <a name="example-workflow"></a>Beispielworkflow

Der in diesem Dokument verwendeten Workflows weist zwei Aktionen auf. Aktionen sind Definitionen von Aufgaben, z. B. das Ausführen von Hive, Sqoop, MapReduce oder anderen Prozessen:

![Workflowdiagramm][img-workflow-diagram]

1. Eine Hive-Aktion führt ein HiveQL-Skript zum Extrahieren von Datensätzen aus der in HDInsight enthaltenen Tabelle **hivesampletable** aus. Jede Datenzeile beschreibt einen Besuch eines bestimmten Mobilgeräts. Das Format des Eintrags sieht wie folgt aus:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Das in diesem Dokument verwendete Hive-Skript zählt die gesamten Besuche für jede Plattform (z. B. Android oder iPhone) und speichert die Werte in einer neuen Hive-Tabelle.

    Weitere Informationen zu Hive finden Sie unter [Verwenden von Hive mit HDInsight][hdinsight-use-hive].

2. Die Sqoop-Aktion exportiert den Inhalt der neuen Hive-Tabelle in eine Tabelle in einer Azure SQL-Datenbank. Weitere Informationen über Sqoop finden Sie unter [Verwenden von Hadoop Sqoop mit HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Informationen zu den unterstützten Oozie-Versionen in HDInsight-Clustern finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen][hdinsight-versions].

## <a name="create-the-working-directory"></a>Erstellen des Arbeitsverzeichnisses

Oozie erwartet, dass die für einen Auftrag erforderlichen Ressourcen im selben Verzeichnis gespeichert werden. In diesem Beispiel wird **wasbs:///tutorials/useoozie** verwendet. Geben Sie den folgenden Befehl zum Erstellen dieses Verzeichnisses und des Datenverzeichnisses ein, das die mit diesem Workflow erstellte neue Hive-Tabelle enthält:

```
hdfs dfs -mkdir -p /tutorials/useoozie/data
```

> [!NOTE]
> Der Parameter `-p` bewirkt, dass alle Verzeichnisse im Pfad erstellt werden, sofern sie nicht bereits vorhanden sind. Das Verzeichnis **data** dient zum Speichern von Daten, die vom Skript **useooziewf.hql** verwendet werden.

Führen Sie auch den folgenden Befehl aus, der sicherstellt, dass Oozie die Identität Ihres Benutzerkontos annehmen kann, wenn Hive- und Sqoop-Aufträge ausgeführt werden. Ersetzen Sie **USERNAME** durch Ihren Benutzernamen:

```
sudo adduser USERNAME users
```

Wenn Sie die Fehlermeldung erhalten, dass der Benutzer bereits zu den Benutzern gehört, können Sie diese einfach ignorieren.

## <a name="add-a-database-driver"></a>Hinzufügen eines Datenbanktreibers

Da dieser Workflow Sqoop zum Exportieren von Daten in die SQL-Datenbank verwendet, müssen Sie eine Kopie des JDBC-Treibers bereitstellen, der für die Kommunikation mit der SQL-Datenbank genutzt wird. Verwenden Sie den folgenden Befehl, um ihn in das Arbeitsverzeichnis zu kopieren:

```
hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/
```

Wenn Ihr Workflow andere Ressourcen, wie z. B. eine JAR-Datei mit einer MapReduce-Anwendung, verwendet hat, müssen Sie diese auch hinzufügen.

## <a name="define-the-hive-query"></a>Definieren der Hive-Abfrage

Führen Sie die folgenden Schritte aus, um ein HiveQL-Skript zu erstellen, das eine Abfrage definiert, die weiter unten in diesem Dokument in einem Oozie-Workflow verwendet wird.

1. Stellen Sie mithilfe von SSH eine Verbindung zum Cluster her. Der folgende Befehl ist ein Beispiel der Verwendung des Befehls `ssh`. Ersetzen Sie __USERNAME__ durch den SSH-Benutzer für den Cluster. Ersetzen Sie __CLUSTERNAME__ durch den Namen des HDInsight-Clusters.

    ```
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie in den folgenden Dokumenten:

    * [Verwenden von SSH mit Linux-basierten Hadoop in HDInsight unter Linux, OS X, UNIX oder Windows](hdinsight-hadoop-linux-use-ssh-unix.md): Dieses Dokument setzt voraus, dass Sie Zugriff auf den Befehl `ssh` haben.

    * [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows mit PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md): Dieses Dokument setzt voraus, dass Sie den SSH-Client von PuTTY verwenden.

2. Führen Sie über die SSH-Verbindung den folgenden Befehl aus, um eine neue Datei zu erstellen:

    ```
    nano useooziewf.hql
    ```

3. Verwenden Sie, sobald der Nano-Editor geöffnet ist, Folgendes als Inhalt der Datei:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Im Skript werden zwei Variablen verwendet:

    * **${hiveTableName}**: Enthält den Namen der zu erstellenden Tabelle

    * **${hiveDataFolder}**: Enthält den Speicherort der Datendateien für die Tabelle

    Die Workflowdefinitionsdatei („workflow.xml“ in diesem Tutorial) übergibt diese Werte zur Laufzeit an das HiveQL-Skript.

4. Drücken Sie STRG+X, um den Editor zu verlassen. Wählen Sie bei entsprechender Aufforderung **Y** aus, um die Datei zu speichern. Drücken Sie dann die **EINGABETASTE**, um den Dateinamen **useooziewf.hql** zu verwenden.
5. Verwenden Sie die folgenden Befehle, um **useooziewf.hql** nach **wasbs:///tutorials/useoozie/useooziewf.hql** zu kopieren:

    ```
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Diese Befehle dienen zum Speichern der Datei **useooziewf.hql** im Azure Storage-Konto, das diesem Cluster zugeordnet ist. Dadurch bleibt die Datei erhalten, selbst wenn der Cluster gelöscht wird. Dadurch können Sie Kosten sparen, indem Sie Cluster löschen, wenn sie nicht verwendet werden, während gleichzeitig Ihre Aufträge und Workflows erhalten bleiben.

## <a name="define-the-workflow"></a>Definieren des Workflows

Definitionen von Oozie-Workflows werden in hPDL (einer XML-Prozessdefinitionssprache) geschrieben. Führen Sie zum Definieren des Workflows die folgenden Schritte aus:

1. Verwenden Sie die folgende Anweisung zum Erstellen und Bearbeiten einer neuen Datei:

    ```
    nano workflow.xml
    ```

2. Sobald der Nano-Editor geöffnet ist, geben Sie Folgendes als Inhalt der Datei ein:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
        </hive>
        <ok to="RunSqoopExport"/>
        <error to="fail"/>
        </action>
        <action name="RunSqoopExport">
        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.compress.map.output</name>
                <value>true</value>
            </property>
            </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveDataFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\t"</arg>
            <archive>sqljdbc41.jar</archive>
            </sqoop>
        <ok to="end"/>
        <error to="fail"/>
        </action>
        <kill name="fail">
        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

    Im Workflow sind zwei Aktionen definiert:

   * **RunHiveScript**: Dies ist die Startaktion, die das Hive-Skript **useooziewf.hql** ausführt.

   * **RunSqoopExport**: Diese Aktion exportiert die vom Hive-Skript erstellten Daten mithilfe von Sqoop in die SQL-Datenbank. Dies wird nur ausgeführt, wenn die Aktion **RunHiveScript** erfolgreich war.

     > [!NOTE]
     > Weitere Informationen über den Oozie-Workflow und die Verwendung von Workflowaktionen finden Sie in der [Apache Oozie 4.0-Dokumentation][apache-oozie-400] (für HDInsight der Version 3.0) oder in der [Apache Oozie 3.3.2-Dokumentation][apache-oozie-332] (für HDInsight der Version 2.1).

     Beachten Sie, dass der Workflow mehrere Einträge hat (z.B. `${jobTracker}`), die durch Werte ersetzt werden, die Sie in der Auftragsdefinition weiter unten in diesem Dokument verwenden.

     Beachten Sie auch den Eintrag `<archive>sqljdbc4.jar</arcive>` im Abschnitt „Sqoop“. Dieser weist Oozie an, dieses Archiv für Sqoop zur Verfügung zu stellen, wenn diese Aktion ausgeführt wird.

3. Drücken Sie zum Speichern der Datei STRG+X, **Y** und dann die **EINGABETASTE**.

4. Verwenden Sie den folgenden Befehl zum Kopieren der Datei **workflow.xml** nach **/tutorials/useoozie/workflow.xml**:

    ```
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-the-database"></a>Erstellen der Datenbank

Befolgen Sie die Schritte unter [Erstellen einer SQL-Datenbank](../sql-database/sql-database-get-started.md), um eine neue Datenbank zu erstellen. Verwenden Sie beim Erstellen der Datenbank den Namen **oozietest** als Datenbanknamen. Notieren Sie sich zudem den Namen des Datenbankservers, der im nächsten Abschnitt benötigt wird.

### <a name="create-the-table"></a>Erstellen der Tabelle

> [!NOTE]
> Es gibt viele Möglichkeiten, zum Erstellen einer Tabelle eine Verbindung mit SQL Database herzustellen. Die folgenden Schritte verwenden [FreeTDS](http://www.freetds.org/) aus dem HDInsight-Cluster.


1. Verwenden Sie den folgenden Befehl, um FreeTDS im HDInsight-Cluster zu installieren:

    ```
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Sobald FreeTDS installiert wurde, verwenden Sie folgenden Befehl, um sich mit dem zuvor erstellten SQL-Datenbank-Server zu verbinden.

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest
    ```

    Eine Ausgabe ähnlich der folgenden wird angezeigt:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. Geben Sie bei der Eingabeaufforderung `1>` folgende Zeilen ein:

    ```
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Nach Eingabe der Anweisung `GO` werden die vorherigen Anweisungen ausgewertet. Dadurch wird eine neue Tabelle namens **mobiledata** erstellt, in die von Sqoop Daten geschrieben werden.

    Stellen Sie wie folgt sicher, dass die Tabelle erstellt wurde:

    ```
    SELECT * FROM information_schema.tables
    GO
    ```

    Eine Ausgabe ähnlich der folgenden sollte angezeigt werden:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    oozietest       dbo     mobiledata      BASE TABLE
    ```

4. EINGABE `exit` at the `1>` ein.

## <a name="create-the-job-definition"></a>Erstellen der Auftragsdefinition

Die Auftragsdefinition beschreibt den Speicherort der Datei "workflow.xml" sowie anderer Dateien, die vom Workflow verwendet werden (Beispiel: useooziewf.hql). Sie definiert auch die Werte für Eigenschaften, die innerhalb des Workflows verwendet werden, und zugeordnete Dateien.

1. Geben Sie den folgenden Befehl ein, um die vollständige WASB-Adresse des Standardspeichers abzurufen. Diese wird in Kürze weiter unten in der Konfigurationsdatei verwendet:

    ```
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Die Ausgabe dieses Befehls sollte etwa so aussehen:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]
    > Falls der HDInsight-Cluster Azure Storage als Standardspeicher verwendet, beginnt der Inhalt des Elements `<value>` mit `wasbs://`. Wenn stattdessen Azure Data Lake Store verwendet wird, beginnt er mit `adl://`.

    Speichern Sie den Inhalt des Elements `<value>`, da er im nächsten Schritt verwendet wird.

2. Rufen Sie den folgenden Befehl auf, um den FQDN des Cluster-Hauptknotens abzurufen. Dieser wird für die JobTracker-Adresse des Clusters verwendet. Diese wird in Kürze weiter unten in der Konfigurationsdatei verwendet:

    ```
    hostname -f
    ```

    Die Ausgabe sieht in etwa wie folgt aus:

    ```hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net```

    Der Port für JobTracker ist 8050, weshalb die vollständige Adresse für JobTracker `hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8050` lautet.

3. Verwenden Sie zum Erstellen der Konfiguration der Oozie-Auftragsdefinition Folgendes:

    ```
    nano job.xml
    ```

4. Verwenden Sie, sobald der Nano-Editor geöffnet ist, Folgendes als Inhalt der Datei:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>JOBTRACKERADDRESS</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>YourName</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

   * Ersetzen Sie alle Vorkommen von **wasbs://mycontainer@mystorageaccount.blob.core.windows.net** durch den Wert, den Sie zuvor für Standardspeicher erhalten haben.

     > [!WARNING]
     > Wenn der Pfad ein Pfad des Typs `wasb` ist, müssen Sie den vollständigen Pfad verwenden. Verkürzen Sie ihn nicht bloß auf `wasb:///`.

   * Ersetzen Sie **JOBTRACKERADDRESS** durch die JobTracker/ResourceManager-Adresse, die Sie zuvor erhalten haben.
   * Ersetzen Sie **YourName** durch Ihren Anmeldenamen für den HDInsight-Cluster.
   * Ersetzen Sie **serverName**, **adminLogin** und **adminPassword** durch die Informationen für Ihre Azure SQL-Datenbank.

     Die meisten der Informationen in dieser Datei dienen zum Auffüllen der Werte, die in der Datei "workflow.xml" oder "ooziewf.hql" (z. B. ${nameNode}) verwendet werden.

     > [!NOTE]
     > Der Eintrag **oozie.wf.application.path** definiert den Speicherort der Datei „workflow.xml“, die den von diesem Auftrag ausgeführten Workflow enthält.

5. Drücken Sie zum Speichern der Datei STRG+X, **Y** und dann die **EINGABETASTE**.

## <a name="submit-and-manage-the-job"></a>Übermitteln und Verwalten des Auftrags

Die folgenden Schritte verwenden den Oozie-Befehl zum Übermitteln und Verwalten von Oozie-Workflows im Cluster. Der Oozie-Befehl ist eine benutzerfreundliche Schnittstelle, die über die [Oozie-REST-API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html)zur Verfügung steht.

> [!IMPORTANT]
> Wenn Sie den Oozie-Befehl verwenden, müssen Sie den vollqualifizierten Domänennamen (FQDN) des HDInsight-Hauptknotens verwenden. Auf diesen FQDN kann nur innerhalb des Clusters zugegriffen werden. Wenn der Cluster sich in einem virtuellen Azure-Netzwerk befindet, ist der Zugriff von anderen Computern im selben Netzwerk aus möglich.


1. Geben Sie zum Abrufen der URL des Oozie-Diensts Folgendes an:

    ```
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Die Ausgabe sieht in etwa wie folgt aus:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    Der Teil `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` ist die mit dem Oozie-Befehl zu verwendende URL.

2. Geben Sie Folgendes an, um eine Umgebungsvariable für die URL erstellen, damit Sie sie nicht für jeden Befehl eingeben müssen:

    ```
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

    Ersetzen Sie die URL durch diejenige, die Sie zuvor erhalten haben.
3. Geben Sie den folgenden Befehl an, um den Auftrag zu übermitteln:

    ```
    oozie job -config job.xml -submit
    ```

    Dadurch werden die Auftragsinformationen aus der Datei **job.xml** geladen und an Oozie übermittelt, ohne dass die Datei ausgeführt wird.

    Nach Abschluss des Befehls sollte die ID des Auftrags zurückgegeben werden. Beispiel: `0000005-150622124850154-oozie-oozi-W`. Wird verwendet, um den Auftrag zu verwalten.

4. Zeigen Sie den Status des Auftrags mit dem folgenden Befehl an. Geben Sie die vom vorherigen Befehl zurückgegebene Auftrags-ID ein:

    ```
    oozie job -info <JOBID>
    ```

    Die Ausgabe sieht in etwa wie folgt aus.

    ```
    Job ID : 0000005-150622124850154-oozie-oozi-W
    ------------------------------------------------------------------------------------------------------------------------------------
    Workflow Name : useooziewf
    App Path      : wasbs:///tutorials/useoozie
    Status        : PREP
    Run           : 0
    User          : USERNAME
    Group         : -
    Created       : 2015-06-22 15:06 GMT
    Started       : -
    Last Modified : 2015-06-22 15:06 GMT
    Ended         : -
    CoordAction ID: -
    ------------------------------------------------------------------------------------------------------------------------------------
    ```

    Dieser Auftrag hat den Status `PREP`. Das bedeutet, dass er übermittelt, aber noch nicht gestartet wurde.

5. Starten Sie den Auftrag mit folgendem Befehl:

    ```
    oozie job -start JOBID
    ```

    Wenn Sie nach diesem Befehl den Status überprüfen, lautet dieser „Wird ausgeführt“, und Informationen für die Aktionen innerhalb des Auftrags werden zurückgegeben.

6. Sobald die Aufgabe erfolgreich abgeschlossen wurde, können Sie mit den folgenden Befehlen überprüfen, ob die Daten generiert und die SQL-Datenbanktabelle exportiert wurden:

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest
    ```

    Geben Sie an der `1>` -Eingabeaufforderung Folgendes ein:

    ```
    SELECT * FROM mobiledata
    GO
    ```

    Die zurückgegebenen Informationen ähneln den folgenden:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Weitere Informationen zum Oozie-Befehl finden Sie unter [Oozie-Befehlszeilentool](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie-REST-API

Mit der Oozie-REST-API können Sie eigene Tools erstellen, die mit Oozie arbeiten. Es folgen HDInsight-spezifische Informationen zum Verwenden der Oozie-REST-API:

* **URI**: Auf die REST-API kann von außerhalb des Clusters unter `https://CLUSTERNAME.azurehdinsight.net/oozie` zugegriffen werden.

* **Authentifizierung**: Sie müssen sich bei der API mit dem HTTP-Clusterkonto (admin) und -kennwort authentifizieren. Beispiel:

    ```
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Weitere Informationen zur Verwendung der Oozie-REST-API finden Sie unter [Oozie-Webdienste-API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Oozie-Webbenutzeroberfläche

Die Oozie-Webbenutzeroberfläche bietet eine webbasierte Anzeige des Status von Oozie-Aufträgen im Cluster. Sie können den Auftragsstatus, die Auftragsdefinition, die Konfiguration, ein Diagramm mit den Aktionen im Auftrag und Protokolle für den Auftrag anzeigen. Sie können auch ausführliche Informationen zu Aktionen innerhalb eines Auftrags anzeigen.

Um auf die Oozie-Webbenutzeroberfläche zuzugreifen, gehen Sie folgendermaßen vor:

1. Erstellen Sie einen SSH-Tunnel zum HDInsight-Cluster. Informationen zur Vorgehensweise finden Sie unter [Verwenden von SSH-Tunneling zum Zugriff auf die Ambari-Webbenutzeroberfläche, ResourceManager, JobHistory, NameNode, Oozie und andere Webbenutzeroberflächen](hdinsight-linux-ambari-ssh-tunnel.md).

2. Nachdem der Tunnel erstellt wurde, öffnen Sie die Ambari-Webbenutzeroberfläche in Ihrem Webbrowser. Der URI für die Ambari-Website lautet **https://CLUSTERNAME.azurehdinsight.net**. Ersetzen Sie **CLUSTERNAME** durch den Namen des Linux-basierten HDInsight-Clusters.

3. Wählen Sie auf der linken Seite der Seite **Oozie**, dann **QuickLinks** und schließlich **Oozie Web UI** aus.

    ![Abbildung der Menüs](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. Die Oozie-Webbenutzeroberfläche zeigt standardmäßig aktive Workflowaufträge an. Wählen Sie zum Anzeigen aller Workflowaufträge **Alle Aufträge**aus.

    ![Alle angezeigten Aufträge](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Wählen Sie einen Auftrag aus, um weitere Informationen dazu anzuzeigen.

    ![Auftragsinformationen](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. Auf der Registerkarte "Auftragsinformationen" können Sie grundlegende Auftragsinformationen sowie die einzelnen Aktionen innerhalb des Auftrags anzeigen. Auf den Registerkarten am oberen Rand können Sie die Auftragsdefinition und Auftragskonfiguration anzeigen, auf das Auftragsprotokoll zugreifen oder einen gerichteten azyklischen Graph (Directed Acyclic Graph, DAG) des Auftrags anzeigen.

   * **Auftragsprotokoll**: Klicken Sie auf die Schaltfläche **GetLogs**, um alle Protokolle für den Auftrag abzurufen, oder verwenden Sie zum Filtern von Protokollen das Feld **Suchfilter eingeben**.

       ![Auftragsprotokoll](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **JobDAG**: Der DAG ist eine grafische Übersicht über die Datenpfade, die im Workflow gewählt wurden.

       ![DAG des Auftrags](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Bei Auswahl einer der Aktionen auf der Registerkarte **Auftragsinformationen** werden Informationen zur Aktion eingeblendet. Wählen Sie z. B. die Aktion **RunHiveScript** aus.

    ![Aktionsinformationen](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Sie können Details zur Aktion anzeigen, einschließlich eines Links zur **Konsolen-URL**, die zum Anzeigen von JobTracker-Informationen für den Auftrag dient.

## <a name="scheduling-jobs"></a>Planen von Aufträgen

Der Koordinator ermöglicht Ihnen das Angeben des Starts, Endes und der Häufigkeit von Aufträgen, damit sie für bestimmte Zeiten geplant werden können.

Um einen Zeitplan für den Workflow zu definieren, führen Sie die folgenden Schritte aus:

1. Verwenden Sie den folgenden Befehl, um eine neue Datei namens **coordinator.xml**zu erstellen:

    ```
    nano coordinator.xml
    ```

    Fügen Sie Folgendes als Inhalt der Datei hinzu:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    Beachten Sie die Variablen des Typs `${...}`, die zur Laufzeit durch Werte in der Auftragsdefinition ersetzt werden. Die Variablen heißen wie folgt:

   * **${coordFrequency}**: Zeit zwischen ausgeführten Instanzen des Auftrags.

   * **${coordStart}**: Startzeit des Auftrags.

   * **${coordEnd}**: Endzeit des Auftrags.

   * **${coordTimezone}**: Koordinatoraufträge erfolgen in einer festen Zeitzone ohne Sommerzeit (in der Regel unter Verwendung von UTC dargestellt). Diese Zeitzone wird als „Oozie-Verarbeitungszeitzone“ bezeichnet.

   * **${wfPath}**: Pfad zur Datei „workflow.xml“.

2. Drücken Sie zum Speichern der Datei STRG+X, **Y** und dann die **EINGABETASTE**.

3. Verwenden Sie den folgenden Befehl, um die Datei in das Arbeitsverzeichnis dieses Auftrags zu kopieren:

    ```
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Verwenden Sie den folgenden Befehl zum Ändern der Datei **job.xml** :

    ```
    nano job.xml
    ```

    Nehmen Sie die folgenden Änderungen vor:

   * Ändern Sie `<name>oozie.wf.application.path</name>` in `<name>oozie.coord.application.path</name>`. Dies weist Oozie an, die Koordinatordatei statt der Workflowdatei auszuführen.

   * Fügen Sie folgenden Code hinzu, mit dem eine Variable in der Datei „coordinator.xml“ so festgelegt wird, dass sie auf den Speicherort der Datei „workflow.xml“ verweist:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Ersetzen Sie den Text `wasbs://mycontainer@mystorageaccount.blob.core.windows` durch den Wert, der in weiteren Einträgen in der Datei „job.xml“ verwendet wird.

   * Fügen Sie die folgenden Code hinzu, mit dem Start, Ende und Häufigkeit für die Datei "coordinator.xml" definiert werden:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2017-02-07T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2017-02-09T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Hiermit wird die Startzeit auf 12:00 Uhr am 7. Februar 2017, die Endzeit auf den 9. Februar 2017 und das Intervall für die Ausführung dieses Auftrags auf „Täglich“ festgelegt. Die Häufigkeit wird in Minuten angegeben. Daher gilt 24 Stunden x 60 Minuten = 1.440 Minuten. Schließlich wird die Zeitzone auf UTC festgelegt.

5. Drücken Sie zum Speichern der Datei STRG+X, **Y** und dann die **EINGABETASTE**.

6. Um den Auftrag auszuführen, geben Sie den folgenden Befehl ein:

    ```
    oozie job -config job.xml -run
    ```

    Hiermit wird der Auftrag übermittelt und gestartet.

7. Wenn Sie die Oozie-Webbenutzeroberfläche besuchen und die Registerkarte **Koordinatoraufträge** auswählen, werden die folgenden Informationen angezeigt:

    ![Registerkarte "Koordinatoraufträge"](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    Beachten Sie den Eintrag **Nächste Materialisierung**, der angibt, wann der Auftrag das nächste Mal ausgeführt wird.

8. Ähnlich wie beim vorherigen Workflowauftrag werden beim Auswählen des Auftragseintrags auf der Webbenutzeroberfläche Informationen zum Auftrag angezeigt:

    ![Informationen zu Koordinatoraufträgen](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    Beachten Sie, dass nur erfolgreiche Ausführungen des Auftrags und nicht einzelne Aktionen innerhalb des geplanten Workflows angezeigt werden. Um diese anzuzeigen, wählen Sie einen der Einträge vom Typ **Aktion** aus. Dadurch werden ähnliche abgerufene Informationen wie beim vorherigen Workflowauftrag angezeigt.

    ![Aktionsinformationen](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>Problembehandlung

Bei der Behandlung von Problemen mit Oozie-Aufträgen ist die Oozie-Benutzeroberfläche sehr hilfreich, da sie Ihnen problemlos ermöglicht, sowohl Oozie-Protokolle als auch JobTracker-Protokolle für MapReduce-Aufgaben wie beispielsweise Hive-Abfragen anzuzeigen. Im Allgemeinen sollten Sie zur Problembehandlung wie folgt vorgehen:

1. Zeigen Sie den Auftrag auf der Oozie-Webbenutzeroberfläche an.

2. Wenn eine bestimmte Aktion nicht erfolgreich war, wählen Sie die Aktion aus, um festzustellen, ob das Feld **Fehlermeldung** weitere Informationen zum Fehler enthält.

3. Falls verfügbar, verwenden Sie die URL der Aktion, um weitere Details (z. B. JobTracker-Protokolle) für die Aktion anzuzeigen.

Es folgen Fehlermeldungen, die auftreten können, und Möglichkeiten zu ihrer Behebung.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Cluster kann nicht initialisiert werden.

**Symptome**: Der Auftragsstatus ändert sich in **SUSPENDED**. In den Details für den Auftrag wird für den Status von RunHiveScript **START_MANUAL** angezeigt. Bei Auswahl der Aktion wird die folgende Fehlermeldung angezeigt:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Ursache**: Die in der Datei **job.xml** verwendeten WASB-Adressen enthalten nicht den Namen des Speichercontainers oder des Speicherkontos. Das WASB-Adressformat muss wie folgt lauten: `wasbs://containername@storageaccountname.blob.core.windows.net`.

**Lösung**: Ändern Sie die vom Auftrag verwendeten WASB-Adressen.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002: Oozie darf nicht die Identität von &lt;USER> annehmen.

**Symptome**: Der Auftragsstatus ändert sich in **SUSPENDED**. In den Details für den Auftrag wird für den Status von RunHiveScript **START_MANUAL** angezeigt. Bei Auswahl der Aktion wird die folgende Fehlermeldung angezeigt:

    JA002: User: oozie is not allowed to impersonate <USER>

**Ursache**: Die aktuellen Berechtigungseinstellungen lassen nicht zu, dass Oozie die Identität des angegebenen Benutzerkontos annimmt.

**Lösung**: Oozie darf die Identität von Benutzern in der Gruppe **users** annehmen. Verwenden Sie `groups USERNAME` , um die Gruppen anzuzeigen, denen das Benutzerkonto als Mitglied angehört. Wenn der Benutzer nicht Mitglied der Gruppe **users** ist, verwenden Sie den folgenden Befehl, um den Benutzer der Gruppe hinzuzufügen:

    sudo adduser USERNAME users

> [!NOTE]
> Es dauert einige Minuten, bis HDInsight erkennt, dass der Benutzer der Gruppe hinzugefügt wurde.

### <a name="launcher-error-sqoop"></a>Launcher ERROR (Sqoop)

**Symptome**: Der Auftragsstatus ändert sich in **KILLED**. In den Details zum Auftrag wird der Status von „RunSqoopExport“ als **ERROR**angezeigt. Bei Auswahl der Aktion wird die folgende Fehlermeldung angezeigt:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Ursache**: Sqoop kann den Datenbanktreiber nicht laden, der für den Zugriff auf die Datenbank erforderlich ist.

**Lösung**: Bei Verwendung von Sqoop in einem Oozie-Auftrag müssen Sie den Datenbanktreiber mit anderen vom Auftrag verwendeten Ressourcen (z. B. „workflow.xml“) angeben.

Sie müssen auch im Abschnitt `<sqoop>...</sqoop>` der Datei "workflow.xml" auf das Archiv mit dem Datenbanktreiber verweisen.

Für den Auftrag in diesem Dokument würden Sie z. B. folgendermaßen die folgenden Schritte ausführen:

1. Kopieren Sie die Datei „sqljdbc4.1.jar“ in das Verzeichnis „/tutorials/useoozie“:

    ```
    hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar
    ```

2. Ändern Sie die Datei "workflow.xml" durch Hinzufügen des folgenden Codes in eine neue Zeile über `</sqoop>`:

    ```xml
    <archive>sqljdbc41.jar</archive>
    ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Lernprogramm haben Sie gelernt, wie ein Oozie-Workflow definiert und ein Oozie-Auftrag ausgeführt wird. Weitere Informationen zum Arbeiten mit HDInsight finden Sie in den folgenden Artikeln:

* [Verwenden des zeitbasierten Oozie-Koordinators mit HDInsight][hdinsight-oozie-coordinator-time]
* [Hochladen von Daten für Hadoop-Aufträge in HDInsight][hdinsight-upload-data]
* [Verwenden von Sqoop mit Hadoop in HDInsight][hdinsight-use-sqoop]
* [Verwenden von Hive mit Hadoop in HDInsight][hdinsight-use-hive]
* [Verwenden von Pig mit Hadoop in HDInsight][hdinsight-use-pig]
* [Entwickeln von Java MapReduce-Programmen für HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/data-factory-data-transformation-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

