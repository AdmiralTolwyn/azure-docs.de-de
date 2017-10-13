---
title: "Apache Sqoop mit Hadoop – Azure HDInsight | Microsoft-Dokumentation"
description: "Erfahren Sie mehr darüber, wie Sie mithilfe von Apache Sqoop Daten zwischen Hadoop unter HDInsight und einer Azure SQL-Datenbank importieren und exportieren."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
author: Blackmist
tags: azure-portal
keywords: Hadoop Sqoop, Sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: larryfr
ms.openlocfilehash: 35dcbb91e6af1480685c9fd5b829c54277c1c605
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Importieren und Exportieren von Daten zwischen Hadoop unter HDInsight und einer SQL-Datenbank mithilfe von Apache Sqoop

[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Erfahren Sie, wie Sie Apache Sqoop zum Importieren und Exportieren von Daten zwischen einem Hadoop-Cluster unter Azure HDInsight und einer Azure SQL- oder Microsoft SQL Server-Datenbank verwenden. Die Schritte in diesem Artikel verwenden den Befehl `sqoop` direkt vom Hauptknoten des Hadoop-Clusters aus. Sie können SSH verwenden, um die Verbindung zum Hauptknoten herzustellen und die Befehle in diesem Artikel auszuführen.

> [!IMPORTANT]
> Die Schritte in diesem Dokument funktionieren nur mit einem HDInsight-Cluster unter Linux. Linux ist das einzige Betriebssystem, das unter HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [Welche Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="install-freetds"></a>Installieren von FreeTDS

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her. Der folgende Befehl stellt z.B. eine Verbindung zum primären Hauptknoten eines Clusters mit dem Namen `mycluster` her:

    ```bash
    ssh CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Geben Sie den folgenden Befehl für die Installation von FreeTDS ein:

    ```bash
    sudo apt --assume-yes install freetds-dev freetds-bin
    ```

    FreeTDS wird in mehreren Schritten verwendet, um eine Verbindung zur SQL-Datenbank herzustellen.

## <a name="create-the-table-in-sql-database"></a>Erstellen der Tabelle in einer SQL-Datenbank

> [!IMPORTANT]
> Wenn Sie den HDInsight-Cluster und die SQL-Datenbank verwenden, die Sie unter [Erstellen des Clusters und der SQL-Datenbank](hdinsight-use-sqoop.md) erstellt haben, überspringen Sie die Schritte in diesem Abschnitt. Die Datenbank und die Tabelle wurden im Rahmen der Schritte im Artikel [Erstellen des Clusters und der SQL-Datenbank](hdinsight-use-sqoop.md) erstellt.

1. Stellen Sie in der SSH-Sitzung mit dem folgenden Befehl eine Verbindung zum SQL-Datenbankserver her.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Eine Ausgabe ähnlich folgendem Text wird angezeigt:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

2. Geben Sie bei der Eingabeaufforderung `1>` folgende Abfrage ein:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
    GO
    ```

    Nach Eingabe der Anweisung `GO` werden die vorherigen Anweisungen ausgewertet. Zunächst wird die Tabelle **mobiledata** erstellt und dieser dann ein gruppierter Index hinzugefügt (für SQL-Datenbank erforderlich).

    Stellen Sie mit der folgenden Abfrage sicher, dass die Tabelle erstellt wurde:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Ihnen wird daraufhin eine Ausgabe angezeigt, die in etwa wie folgt aussieht:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

3. EINGABE `exit` at the `1>` ein.

## <a name="sqoop-export"></a>Sqoop-Export

1. Verwenden Sie den folgenden Befehl über die SSH-Verbindung zum Cluster, um sicherzustellen, dass Sqoop Ihre SQL-Datenbank sehen kann:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    Geben Sie nach Aufforderung das Kennwort für die SQL-Datenbank-Anmeldung ein.

    Der Befehl gibt eine Liste mit Datenbanken zurück, in der auch die zuvor erstellte Datenbank **sqooptest** enthalten ist.

2. Verwenden Sie den folgenden Befehl, um Daten aus **hivesampletable** in die Tabelle **mobiledata** zu exportieren:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1
    ```

    Dieser Befehl weist Sqoop an, eine Verbindung mit der **sqooptest**-Datenbank herzustellen. Sqoop exportiert dann Daten aus **wasb:///hive/warehouse/hivesampletable** in die Tabelle **mobiledata**.

    > [!IMPORTANT]
    > Verwenden Sie `wasb:///`, wenn der Standardspeicher für Ihren Cluster ein Azure Storage-Konto ist. Verwenden Sie bei einem Azure Data Lake Store `adl:///`.

3. Nach Abschluss des Befehls, verwenden Sie den folgenden Befehl, um über TSQL eine Verbindung mit der Datenbank herzustellen:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P -p 1433 -D sqooptest
    ```

    Nachdem die Verbindung hergestellt wurde, überprüfen Sie mithilfe der folgenden Anweisungen, ob die Daten in die Tabelle **mobiledata** exportiert wurden:

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata
    GO
    ```

    Es sollte eine Liste der Tabellendaten angezeigt werden. Geben Sie `exit` ein, um das Dienstprogramm tsql zu beenden.

## <a name="sqoop-import"></a>Sqoop-Import

1. Importieren Sie mit dem folgenden Befehl Daten aus der Tabelle **mobiledata** in der SQL-Datenbank in das Verzeichnis **wasb:///tutorials/usesqoop/importeddata** in HDInsight:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    In den Daten sind die Felder durch ein Tabstoppzeichen getrennt und die Zeilen durch ein Zeilenumbruchzeichen abgeschlossen.

2. Sobald der Import abgeschlossen ist, verwenden Sie den folgenden Befehl zum Auflisten der Daten in dem neuen Verzeichnis:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Verwenden von SQL Server

Sie können auch Sqoop zum Importieren und Exportieren von Daten aus SQL-Server verwenden, entweder in Ihrem Rechenzentrum oder auf einem in Azure gehosteten virtuellen Computer. Die Unterschiede zwischen der Verwendung von SQL-Datenbank und SQL Server sind:

* Sowohl HDInsight als auch SQL Server müssen sich im selben virtuellen Azure-Netzwerk befinden.

    Ein Beispiel finden Sie im Dokument [Connect HDInsight to your on-premises network](./connect-on-premises-network.md) (Herstellen einer Verbindung von HDInsight mit Ihrem lokalen Netzwerk).

    Weitere Informationen zur Verwendung von HDInsight mit einem virtuellen Azure-Netzwerk finden Sie im Dokument [Erweitern der HDInsight-Funktionen mit Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md). Weitere Informationen zu Azure Virtual Network erhalten Sie im Dokument [Übersicht über virtuelle Netzwerke](../virtual-network/virtual-networks-overview.md).

* SQL Server muss so konfiguriert sein, dass SQL-Authentifizierung erlaubt ist. Weitere Informationen finden Sie im Artikel [Auswählen eines Authentifizierungsmodus](https://msdn.microsoft.com/ms144284.aspx).

* Sie müssen SQL Server möglicherweise für Remoteverbindungen konfigurieren. Weitere Informationen finden Sie im Artikel [How to Troubleshoot Connecting to the SQL Server Database Engine](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) (Beheben von Fehlern bei der Verbindung zum SQL Server-Datenbankmodul).

* Erstellen Sie die Datenbank **sqooptest** in SQL Server mithilfe eines Hilfsprogramms wie **SQL Server Management Studio** oder **tsql**. Die Schritte zur Verwendung der Azure-Befehlszeilenschnittstelle funktionieren nur für die Azure SQL-Datenbank.

    Verwenden Sie die folgenden Transact-SQL-Anweisungen zum Erstellen der Tabelle **mobiledata**:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    ```

* Beim Herstellen einer Verbindung mit SQL Server von HDInsight müssen Sie möglicherweise die IP-Adresse der SQL Server-Instanz verwenden. Beispiel:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Einschränkungen

* Massenexport: Bei Linux-basiertem HDInsight unterstützt der zum Exportieren von Daten nach Microsoft SQL Server oder Azure SQL-Datenbank verwendete Sqoop-Connector derzeit keine Masseneinfügungen.

* Batchverarbeitung: Wenn Sie in Linux-basiertem HDInsight zum Ausführen von Einfügevorgängen den Schalter `-batch` verwenden, führt Sqoop mehrere Einfügevorgänge aus, anstatt diese zu einem Batch zusammenzufassen.

## <a name="next-steps"></a>Nächste Schritte

Nun wissen Sie, wie Sqoop verwendet haben. Weitere Informationen finden Sie unter:

* [Verwenden von Oozie mit HDInsight][hdinsight-use-oozie]: Verwenden der Sqoop-Aktion in einem Oozie-Workflow.
* [Analysieren von Daten zu Flugverspätungen mit HDInsight][hdinsight-analyze-flight-data]: Verwenden von Hive zur Analyse von Daten zu Flugverspätungen und Verwenden von Sqoop zum Exportieren von Daten in Azure SQL-Datenbank.
* [Hochladen von Daten in HDInsight][hdinsight-upload-data]: Andere Methoden zum Hochladen von Daten in HDInsight/Azure Blob Storage.

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
