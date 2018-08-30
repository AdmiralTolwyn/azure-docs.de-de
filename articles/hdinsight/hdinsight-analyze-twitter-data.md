---
title: Analysieren von Twitter-Daten mit Hadoop in HDInsight – Azure
description: Erfahren Sie, wie Sie Twitterdaten mit Hive in Hadoop und HDInsight analysieren können, um die Häufigkeit bestimmter Wörter zu ermitteln.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: abf9cd311af141a646c56f452ded77a914bc1d2f
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43093297"
---
# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analysieren von Twitter-Daten mit Hive in HDInsight
Soziale Netzwerke sind einer der Hauptfaktoren für die Akzeptanz von Big Data. Öffentliche APIs von Websites wie Twitter sind eine nützliche Datenquelle für Analyse und Verständnis beliebter Trends.
In diesem Lernprogramm werden Sie mit der Twitter-Streaming-API Tweets abrufen und dann mithilfe von Apache Hive in Azure HDInsight eine Liste der Twitter-Benutzer abrufen, die die meisten Tweets gesendet haben, die ein bestimmtes Wort enthalten.

> [!IMPORTANT]
> Die Schritte in diesem Dokument erfordern einen Windows-basierten HDInsight-Cluster. Linux ist das einzige Betriebssystem, das unter HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [Welche Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](hdinsight-component-versioning.md#hdinsight-windows-retirement). Die Schritte für einen Linux-basierten Cluster finden Sie unter [Analysieren von Twitter-Daten mit Hive in HDInsight (Linux)](hdinsight-analyze-twitter-data-linux.md).

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Tutorial beginnen können, benötigen Sie Folgendes:

* **Eine Arbeitsstation** , auf der Azure PowerShell installiert und konfiguriert ist.

    Um PowerShell-Skripts ausführen zu können, müssen Sie Azure PowerShell als Administrator ausführen und die Ausführungsrichtlinie auf *RemoteSigned*setzen. Siehe [Ausführen von Windows PowerShell-Skripts][powershell-script].

    Stellen Sie vor dem Ausführen von PowerShell-Skripts mithilfe des folgenden Cmdlets sicher, dass eine Verbindung mit Ihrem Azure-Abonnement besteht:

    ```powershell
    Connect-AzureRmAccount
    ```

    Wenn Sie mehrere Azure-Abonnements haben, legen Sie das aktuelle Abonnement mit dem folgenden Cmdlet fest:

    ```powershell
    Select-AzureRmSubscription -SubscriptionID <Azure Subscription ID>
    ```

    > [!IMPORTANT]
    > Die Azure PowerShell-Unterstützung zum Verwalten von HDInsight-Ressourcen mithilfe von Azure Service Manager ist **veraltet** und wurde zum 1. Januar 2017 eingestellt. Die Schritte in diesem Dokument zeigen die neuen HDInsight-Cmdlets, die mit Azure Resource Manager genutzt werden können.
    >
    > Führen Sie zur Installation der neuesten Version von Azure PowerShell die unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azureps-cmdlets-docs) beschriebenen Schritte aus. Wenn Sie über Skripts verfügen, die für die Verwendung der neuen Cmdlets für Azure Resource Manager geändert werden müssen, finden Sie unter [Migrieren zu Azure Resource Manager-basierten Entwicklungstools für HDInsight-Cluster](hdinsight-hadoop-development-using-azure-resource-manager.md) weitere Informationen.

* **Ein Azure HDInsight-Cluster**. Anweisungen zur Bereitstellung von Clustern finden Sie unter [Erste Schritte mit HDInsight][hdinsight-get-started] oder unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision]. Später in diesem Lernprogramm benötigen Sie den Namen des Clusters.

In der folgenden Tabelle sind die in diesem Lernprogramm verwendeten Dateien aufgelistet:

| Dateien | BESCHREIBUNG |
| --- | --- |
| /tutorials/twitter/data/tweets.txt |Die Quelldaten für den Hive-Job. |
| /tutorials/twitter/output |Der Ausgabeordner für den Hive-Job. Der Standardname der Ausgabedatei des Hive-Auftrags lautet **000000_0**. |
| tutorials/twitter/twitter.hql |Die HiveQL-Skriptdatei. |
| /tutorials/twitter/jobstatus |Der Status des Hadoop-Jobs. |

## <a name="get-twitter-feed"></a>Abrufen des Twitter-Feeds
In diesem Tutorial verwenden Sie die [Twitter-Streaming-APIs][twitter-streaming-api]. Die spezielle Twitter-Streaming-API, die Sie verwenden, heißt [statuses/filter][twitter-statuses-filter].

> [!NOTE]
> Eine Datei mit 10.000 Tweets und die Hive-Skriptdatei (siehe nächster Abschnitt) wurden in einen öffentlichen Blobcontainer hochgeladen. Wenn Sie die hochgeladenen Dateien verwenden möchten, können Sie diesen Abschnitt überspringen.

Tweets-Daten werden im JSON-Format (JavaScript Object Notation) in einer komplex verschachtelten Struktur gespeichert. Anstatt viele Codezeilen in einer herkömmlichen Programmiersprache zu schreiben, können Sie diese verschachtelte Struktur in eine Hive-Tabelle transformieren, die anschließend mit einer SQL-ähnlichen Sprache (Structured Query Language) namens HiveQL abgefragt werden kann.

Twitter ermöglicht über OAuth den autorisierten Zugriff auf seine API. OAuth ist ein Authentifizierungsprotokoll, mit dem Benutzer es Anwendungen gestatten können, in ihrem Namen zu handeln, ohne ihr Kennwort weitergeben zu müssen. Weitere Informationen finden Sie unter [oauth.net](http://oauth.net/) oder im ausgezeichneten [Beginner's Guide to OAuth](http://hueniverse.com/oauth/) (OAuth-Einführungshandbuch) von Hueniverse.

Um OAuth zu verwenden, müssen Sie zunächst auf der Twitter-Entwicklerwebsite eine neue Anwendung erstellen.

**So erstellen Sie eine Twitter-Anwendung**

1. Melden Sie sich bei [https://apps.twitter.com/](https://apps.twitter.com/) an. Klicken Sie auf den Link **Registriere Dich jetzt** , wenn Sie noch kein Twitter-Konto haben.
2. Klicken Sie auf **Create New App**.
3. Geben Sie **Name**, **Description** und **Website** ein. Für das Feld **Website** können Sie eine URL erfinden. Die folgende Tabelle zeigt einige mögliche Beispielwerte:

   | Feld | Wert |
   | --- | --- |
   |  NAME |MyHDInsightApp |
   |  BESCHREIBUNG |MyHDInsightApp |
   |  Website |http://www.myhdinsightapp.com |
4. Aktivieren Sie **Yes, I agree**, und klicken Sie dann auf **Create your Twitter application**.
5. Klicken Sie auf die Registerkarte **Permissions** . Die Standardberechtigung ist **Read only**. Diese Berechtigung reicht für dieses Lernprogramm aus.
6. Klicken Sie auf die Registerkarte **Keys and Access Tokens** .
7. Klicken Sie auf **Create my access token**.
8. Klicken Sie oben rechts auf der Seite auf **Test OAuth** .
9. Notieren Sie **consumer key**, **Consumer secret**, **Access token** und **Access token secret**. Sie benötigen diese Werte später im Lernprogramm.

In diesem Tutorial verwenden Sie Windows PowerShell, um einen Webdienstaufruf zu tätigen. Ein weiteres beliebtes Tool zum Erstellen von Webdienstaufrufen ist [*Curl*][curl]. Curl können Sie [hier][curl-download] herunterladen.

> [!NOTE]
> Wenn Sie den Curl-Befehl in Windows verwenden, geben Sie die Optionswerte in doppelten anstelle von einfachen Anführungszeichen ein.

**So rufen Sie Tweets ab**

1. Öffnen Sie die Windows PowerShell Integrated Scripting Environment (ISE): (Geben Sie im Windows 8-Startbildschirm **PowerShell_ISE** ein, und klicken Sie dann auf **Windows PowerShell ISE**. Weitere Informationen finden Sie unter [Starten von Windows PowerShell unter Windows 8 und Windows](https://docs.microsoft.com/en-us/powershell/scripting/setup/starting-windows-powershell?view=powershell-6).
2. Kopieren Sie das folgende Skript in den Skriptbereich:

    ```powershell
    #region - variables and constants
    $clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name

    # Enter the OAuth information for your Twitter application
    $oauth_consumer_key = "<TwitterAppConsumerKey>";
    $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
    $oauth_token = "<TwitterAppAccessToken>";
    $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

    $destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob.

    $trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
    $track = [System.Uri]::EscapeDataString($trackString);
    $lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
    #endregion

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    Connect-AzureRmAccount
    #endregion

    #region - Create a block blob object for writing tweets into Blob storage
    Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -Name $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $storageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $containerName = $myCluster.DefaultStorageContainer
    Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

    Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
    Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow

    Write-Host "Create block blob object ..." -ForegroundColor Green
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($containerName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
    #end region

    # region - Format OAuth strings
    Write-Host "Format oauth strings ..." -ForegroundColor Green
    $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
    $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
    $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

    $signature = "POST&";
    $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
    $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
    $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
    $signature += [System.Uri]::EscapeDataString("track=" + $track);

    $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

    $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
    $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
    $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

    $oauth_authorization = 'OAuth ';
    $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
    $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
    $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
    $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
    $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
    $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
    $oauth_authorization += 'oauth_version="1.0"';

    $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track);
    #endregion

    #region - Read tweets
    Write-Host "Create HTTP web request ..." -ForegroundColor Green
    [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
    $request.Method = "POST";
    $request.Headers.Add("Authorization", $oauth_authorization);
    $request.ContentType = "application/x-www-form-urlencoded";
    $body = $request.GetRequestStream();

    $body.write($post_body, 0, $post_body.length);
    $body.flush();
    $body.close();
    $response = $request.GetResponse() ;

    Write-Host "Start stream reading ..." -ForegroundColor Green

    Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream

    $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

    $inrec = $sReader.ReadLine()
    $count = 0
    while (($inrec -ne $null) -and ($count -le $lineMax))
    {
        if ($inrec -ne "")
        {
            Write-Host "`n`t $count tweets received." -ForegroundColor Yellow

            $writeStream.WriteLine($inrec)
            $count ++
        }

        $inrec=$sReader.ReadLine()
    }
    #endregion

    #region - Write tweets to Blob storage
    Write-Host "Write to the destination blob ..." -ForegroundColor Green
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)

    $sReader.close()
    #endregion

    Write-Host "Completed!" -ForegroundColor Green
    ```

3. Legen Sie die ersten fünf bis acht Variablen im Skript fest:

    Variable|BESCHREIBUNG
    ---|---
    $clusterName|Der Name des HDInsight-Clusters, in dem die Anwendung ausgeführt werden soll.
    $oauth_consumer_key|Dies ist der **Verbraucherschlüssel** der Twitter-Anwendung, den Sie beim Erstellen der Twitter-Anwendung notiert haben.
    $oauth_consumer_secret|Dies ist der zuvor notierte **consumer secret** -Schlüssel der Twitter-Anwendung.
    $oauth_token|Dies ist das zuvor notierte **access token** der Twitter-Anwendung.
    $oauth_token_secret|Dies ist der zuvor notierte **access token secret** -Schlüssel der Twitter-Anwendung.
    $destBlobName|Dies ist der Name des Ausgabe-Blobs. Der Standardwert lautet **tutorials/twitter/data/tweets.txt**. Wenn Sie den Standardwert ändern, müssen Sie die Windows PowerShell-Skripts entsprechend aktualisieren.
    $trackString|Der Webdienst gibt Tweets zurück, die mit diesen Schlüsselwörtern verknüpft sind. Der Standardwert lautet **Azure, Cloud, HDInsight**. Wenn Sie den Standardwert ändern, müssen Sie die Windows PowerShell-Skripts entsprechend aktualisieren.
    $lineMax|Der Wert bestimmt, wie viele Tweets das Skript liest. Das Lesen von 100 Tweets dauert etwa drei Minuten. Sie können einen größeren Wert festlegen, dann nimmt das Herunterladen jedoch mehr Zeit in Anspruch.

1. Drücken Sie **F5** , um das Skript auszuführen. Falls Probleme auftreten, markieren Sie als Behelfslösung alle Zeilen, und drücken Sie anschließend **F8**.
2. Am Ende der Ausgabe wird „Complete!“ angezeigt. Jede Fehlermeldung wird rot dargestellt.

Zur Validierung können Sie die Ausgabedatei **/tutorials/twitter/data/tweets.txt** in Azure Blob Storage mit dem Azure Storage-Explorer oder mit Azure PowerShell prüfen. Ein Windows PowerShell-Beispielskript zum Auflisten von Dateien finden Sie unter [Verwenden von Blob Storage mit HDInsight][hdinsight-storage-powershell].

## <a name="create-hiveql-script"></a>Erstellen eines HiveQL-Skripts
Mit Azure PowerShell können Sie mehrere HiveQL-Anweisungen gleichzeitig ausführen oder die HiveQL-Anweisung in eine Skriptdatei paketieren. In diesem Lernprogramm erstellen Sie ein HiveQL-Skript. Die Skriptdatei muss in Azure Blob Storage hochgeladen werden. Im nächsten Abschnitt wird die Skriptdatei mit Azure PowerShell ausgeführt.

> [!NOTE]
> Die Hive-Skriptdatei und eine Datei mit 10.000 Tweets wurden in einen öffentlichen Blobcontainer hochgeladen. Wenn Sie die hochgeladenen Dateien verwenden möchten, können Sie diesen Abschnitt überspringen.

Das HiveQL-Skript führt Folgendes durch:

1. **Ablegen der Tabelle tweets_raw**, falls die Tabelle bereits vorhanden ist.
2. **Erstellen der Hive-Tabelle tweets_raw**. Diese temporäre strukturierte Hive-Tabelle enthält die Daten für eine weitere ETL-Verarbeitung (Extrahieren, Transformieren und Laden). Informationen zu Partitionen finden Sie im [Hive-Tutorial][apache-hive-tutorial].
3. **Laden der Daten** aus dem Quellordner /tutorials/twitter/data. Der große Tweets-Dataset im verschachtelten JSON-Format wurde nun in eine temporäre Hive-Tabellenstruktur transformiert.
4. **Löschen der Tweets-Tabelle** , falls die Tabelle bereits vorhanden ist.
5. **Erstellen der Tweets-Tabelle**. Bevor Sie eine Abfrage im Tweets-Dataset mit Hive durchführen können, müssen Sie einen weiteren ETL-Prozess ausführen. In diesem ETL-Prozess wird ein detaillierteres Tabellenschema für die Daten definiert, die Sie in der Tabelle "twitter_raw" gespeichert haben.
6. **Einfügen der overwrite-Tabelle**. Dieses komplexe Hive-Skript löst eine Reihe langer MapReduce-Aufträge im Hadoop-Cluster aus. Je nach Datensatz und Größe Ihres Clusters sollte dieser Vorgang ca. 10 Minuten dauern.
7. **Einfügen des overwrite-Verzeichnisses**. Führen Sie eine Abfrage aus, und geben Sie den Datensatz in einer Datei aus. Diese Abfrage gibt eine Liste von Twitter-Benutzern zurück, die die meisten Tweets mit dem Wort "Azure" gesendet haben.

**So erstellen Sie ein Hive-Skript und laden es zu Azure hoch**

1. Öffnen Sie Windows PowerShell ISE.
2. Kopieren Sie das folgende Skript in den Skriptbereich:

    ```powershell
    #region - variables and constants
    $clusterName = "<Existing HDInsight Cluster Name>" # Enter your HDInsight cluster name
    $subscriptionID = "<Azure Subscription ID>"

    $sourceDataPath = "/tutorials/twitter/data"
    $outputPath = "/tutorials/twitter/output"
    $hqlScriptFile = "tutorials/twitter/twitter.hql"

    $hqlStatements = @"
    set hive.exec.dynamic.partition = true;
    set hive.exec.dynamic.partition.mode = nonstrict;

    DROP TABLE tweets_raw;
    CREATE EXTERNAL TABLE tweets_raw (
        json_response STRING
    )
    STORED AS TEXTFILE LOCATION '$sourceDataPath';

    DROP TABLE tweets;
    CREATE TABLE tweets
    (
        id BIGINT,
        created_at STRING,
        created_at_date STRING,
        created_at_year STRING,
        created_at_month STRING,
        created_at_day STRING,
        created_at_time STRING,
        in_reply_to_user_id_str STRING,
        text STRING,
        contributors STRING,
        retweeted STRING,
        truncated STRING,
        coordinates STRING,
        source STRING,
        retweet_count INT,
        url STRING,
        hashtags array<STRING>,
        user_mentions array<STRING>,
        first_hashtag STRING,
        first_user_mention STRING,
        screen_name STRING,
        name STRING,
        followers_count INT,
        listed_count INT,
        friends_count INT,
        lang STRING,
        user_location STRING,
        time_zone STRING,
        profile_image_url STRING,
        json_response STRING
    );

    FROM tweets_raw
    INSERT OVERWRITE TABLE tweets
    SELECT
        cast(get_json_object(json_response, '$.id_str') as BIGINT),
        get_json_object(json_response, '$.created_at'),
        concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
        substr (get_json_object(json_response, '$.created_at'),27,4)),
        substr (get_json_object(json_response, '$.created_at'),27,4),
        case substr (get_json_object(json_response, '$.created_at'),5,3)
            when "Jan" then "01"
            when "Feb" then "02"
            when "Mar" then "03"
            when "Apr" then "04"
            when "May" then "05"
            when "Jun" then "06"
            when "Jul" then "07"
            when "Aug" then "08"
            when "Sep" then "09"
            when "Oct" then "10"
            when "Nov" then "11"
            when "Dec" then "12" end,
        substr (get_json_object(json_response, '$.created_at'),9,2),
        substr (get_json_object(json_response, '$.created_at'),12,8),
        get_json_object(json_response, '$.in_reply_to_user_id_str'),
        get_json_object(json_response, '$.text'),
        get_json_object(json_response, '$.contributors'),
        get_json_object(json_response, '$.retweeted'),
        get_json_object(json_response, '$.truncated'),
        get_json_object(json_response, '$.coordinates'),
        get_json_object(json_response, '$.source'),
        cast (get_json_object(json_response, '$.retweet_count') as INT),
        get_json_object(json_response, '$.entities.display_url'),
        array(
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
        array(
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
        trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
        trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
        get_json_object(json_response, '$.user.screen_name'),
        get_json_object(json_response, '$.user.name'),
        cast (get_json_object(json_response, '$.user.followers_count') as INT),
        cast (get_json_object(json_response, '$.user.listed_count') as INT),
        cast (get_json_object(json_response, '$.user.friends_count') as INT),
        get_json_object(json_response, '$.user.lang'),
        get_json_object(json_response, '$.user.location'),
        get_json_object(json_response, '$.user.time_zone'),
        get_json_object(json_response, '$.user.profile_image_url'),
        json_response
    WHERE (length(json_response) > 500);

    INSERT OVERWRITE DIRECTORY '$outputPath'
    SELECT name, screen_name, count(1) as cc
        FROM tweets
        WHERE text like "%Azure%"
        GROUP BY name,screen_name
        ORDER BY cc DESC LIMIT 10;
    "@
    #endregion

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green

    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Connect-AzureRmAccount
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    #endregion

    #region - Create a block blob object for writing the Hive script file
    Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow

    Write-Host "Define the connection string ..." -ForegroundColor Green
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

    Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
    $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)

    Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream
    $writeStream.Writeline($hqlStatements)
    #endregion

    #region - Write the Hive script file to Blob storage
    Write-Host "Write to the destination blob ... " -ForegroundColor Green
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $hqlScriptBlob.UploadFromStream($memStream)
    #endregion

    Write-Host "Completed!" -ForegroundColor Green
    ```

3. Legen Sie die ersten zwei Variablen in dem Skript fest:

   | Variable | BESCHREIBUNG |
   | --- | --- |
   |  $clusterName |Geben Sie den Namen des HDInsight-Clusters ein, in dem Sie die Anwendung ausführen möchten. |
   |  $subscriptionID |Geben Sie Ihre Azure-Abonnement-ID ein. |
   |  $sourceDataPath |Der Speicherort in Azure Blob Storage, aus dem Hive-Abfragen Daten lesen. Sie müssen diese Variable nicht ändern. |
   |  $outputPath |Der Speicherort in Azure Blob Storage, an den Hive-Abfragen die Ergebnisse ausgeben. Sie müssen diese Variable nicht ändern. |
   |  $hqlScriptFile |Der Speicherort und der Dateinamen der HiveQL-Skriptdatei. Sie müssen diese Variable nicht ändern. |
4. Drücken Sie **F5** , um das Skript auszuführen. Falls Probleme auftreten, markieren Sie als Behelfslösung alle Zeilen, und drücken Sie anschließend **F8**.
5. Am Ende der Ausgabe wird „Complete!“ angezeigt. Jede Fehlermeldung wird rot dargestellt.

Zur Validierung können Sie die Ausgabedatei **/tutorials/twitter/twitter.hql** in Azure Blob Storage mit dem Azure Storage-Explorer oder mit Azure PowerShell prüfen. Ein Windows PowerShell-Beispielskript zum Auflisten von Dateien finden Sie unter [Verwenden von Blob Storage mit HDInsight][hdinsight-storage-powershell].

## <a name="process-twitter-data-by-using-hive"></a>Verarbeiten von Twitter-Daten mit Hive
Damit haben Sie sämtliche Vorbereitungen abgeschlossen. Jetzt können Sie das Hive-Skript aufrufen und die Ergebnisse prüfen.

### <a name="submit-a-hive-job"></a>Hive-Auftrag senden
Verwenden Sie das folgende Windows PowerShell-Skript, um das Hive-Skript auszuführen. Sie müssen die erste Variable festlegen.

> [!NOTE]
> Um die Tweets und das HiveQL-Skript, das Sie in den letzten beiden Abschnitten hochgeladen haben, zu verwenden, legen Sie "$hqlScriptFile" auf "/ tutorials/twitter/twitter.hql" fest. Um diejenigen zu verwenden, die in ein öffentliches Blob hochgeladen wurden, legen Sie für „$hqlScriptFile“ Folgendes fest: „wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql“.

```powershell
#region variables and constants
$clusterName = "<Existing Azure HDInsight Cluster Name>"
$httpUserName = "admin"
$httpUserPassword = "<HDInsight Cluster HTTP User Password>"

#use one of the following
$hqlScriptFile = "wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
$hqlScriptFile = "/tutorials/twitter/twitter.hql"

$statusFolder = "/tutorials/twitter/jobstatus"
#endregion

$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $myCluster.ResourceGroup
$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value

$defaultBlobContainerName = $myCluster.DefaultStorageContainer

#region - Invoke Hive
Write-Host "Invoke Hive ... " -ForegroundColor Green

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpUserPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

Use-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $httpCredential
$response = Invoke-AzureRmHDInsightHiveJob -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -file $hqlScriptFile -StatusFolder $statusFolder #-OutVariable $outVariable

Write-Host "Display the standard error log ... " -ForegroundColor Green
$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace �\s*$� -replace �.*\s�
Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $jobID -DefaultContainer $defaultBlobContainerName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -HttpCredential $httpCredential
#endregion
```

### <a name="check-the-results"></a>Prüfen der Ergebnisse
Verwenden Sie das folgende Windows PowerShell-Skript, um die Ausgabe des Hive-Auftrags zu prüfen. Sie müssen die ersten beiden Variablen festlegen.

```powershell
#region variables and constants
$clusterName = "<Existing Azure HDInsight Cluster Name>"

$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
#endregion

#region - Create an Azure storage context object
Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $myCluster.ResourceGroup
$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
$defaultBlobContainerName = $myCluster.DefaultStorageContainer

Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow

Write-Host "Create a context object ... " -ForegroundColor Green
$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
#endregion

#region - Download blob and display blob
Write-Host "Download the blob ..." -ForegroundColor Green
cd $HOME
Get-AzureStorageBlobContent -Container $defaultBlobContainerName -Blob $blob -Context $storageContext -Force

Write-Host "Display the output ..." -ForegroundColor Green
Write-Host "==================================" -ForegroundColor Green
cat "./$blob"
Write-Host "==================================" -ForegroundColor Green
#end region
```

> [!NOTE]
> In der Hive-Tabelle wird \001 als Feldtrennzeichen verwendet. Das Trennzeichen ist in der Ausgabe nicht sichtbar.

Nachdem die Analyseergebnisse in Azure Blob Storage abgelegt wurden, können Sie die Daten in Azure SQL-Datenbank bzw. auf einen SQL-Server exportieren, die Daten mit Power Query nach Excel exportieren oder Ihre Anwendung mit dem Hive ODBC-Treiber mit den Daten verbinden. Weitere Informationen finden Sie unter [Verwenden von Sqoop mit HDInsight][hdinsight-use-sqoop], [Analysieren von Daten zu Flugverspätungen mit HDInsight][hdinsight-analyze-flight-delay-data], [Verbinden von Excel mit HDInsight mithilfe von Power Query][hdinsight-power-query] und [Verbinden von Excel mit HDInsight mithilfe des Microsoft Hive ODBC-Treibers][hdinsight-hive-odbc].

## <a name="next-steps"></a>Nächste Schritte
In diesem Lernprogramm haben Sie erfahren, wie Sie ein unstrukturiertes JSON-Dataset in eine strukturierte Hive-Tabelle umwandeln und Daten aus Twitter mithilfe von Azure HDInsight abfragen, anzeigen und analysieren können. Weitere Informationen finden Sie unter:

* [Erste Schritte mit HDInsight][hdinsight-get-started]
* [Analysieren von Daten zu Flugverspätungen mit HDInsight][hdinsight-analyze-flight-delay-data]
* [Verbinden von Excel mit HDInsight mithilfe von Power Query][hdinsight-power-query]
* [Verbinden von Excel mit HDInsight mithilfe des Microsoft Hive ODBC-Treibers][hdinsight-hive-odbc]
* [Verwenden von Sqoop mit HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://developer.twitter.com/en/docs/api-reference-index
[twitter-statuses-filter]: https://developer.twitter.com/en/docs/tweets/filter-realtime/api-reference/post-statuses-filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]:hdinsight-hadoop-use-blob-storage.md#access-blobs-using-azure-powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-power-query]:hadoop/apache-hadoop-connect-excel-power-query.md
[hdinsight-hive-odbc]:hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md
