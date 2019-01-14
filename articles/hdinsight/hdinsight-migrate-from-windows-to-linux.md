---
title: Migrieren von Windows-basierten HDInsight-Clustern zu Linux-basierten HDInsight-Clustern – Azure
description: Erfahren Sie, wie sie von einem Windows-basierten HDInsight-Cluster zu einem Linux-basierten HDInsight-Cluster migrieren.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: hrasheed
ms.openlocfilehash: ea808609add942c5cac36e7f0306e4a27ac3bb3a
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53743645"
---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migrieren von einem Windows-basierten HDInsight-Cluster zu einem Linux-basierten Cluster

In diesem Artikel erhalten Sie nähere Informationen zu den Unterschieden zwischen HDInsight unter Windows und Linux. Zudem enthält er Anweisungen zum Migrieren vorhandener Workloads zu einem Linux-basierten Cluster.

Auch wenn Windows-basiertes HDInsight eine einfache Möglichkeit zur Verwendung von Apache Hadoop in der Cloud darstellt, müssen Sie möglicherweise eine Migration zu einem Linux-basierten Cluster durchführen. Dies ist beispielsweise erforderlich, um Linux-basierte Tools und Technologien zu nutzen, die für Ihre Lösung erforderlich sind. Vieles im Hadoop-Ökosystem wird in Linux-basierten Systemen entwickelt. Einiges ist für die Nutzung mit HDInsight (Windows-basiert) möglicherweise nicht verfügbar. In vielen Büchern, Videos und anderem Trainingsmaterial wird davon ausgegangen, dass Sie beim Arbeiten mit Hadoop ein Linux-System verwenden.

> [!NOTE]  
> HDInsight-Cluster verwenden Ubuntu LTS-Versionen (Long-Term Support) als Betriebssystem für die Knoten im Cluster. Informationen zur Version von Ubuntu, die für HDInsight verfügbar ist, sowie weitere Informationen zu Versionen von Komponenten finden Sie unter [HDInsight-Komponentenversionen](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Migrationsaufgaben

Der allgemeine Workflow für die Migration sieht folgendermaßen aus:

![Migrations-Workflow-Diagramm](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Lesen Sie den gesamten Artikel, um zu erfahren, welche Änderungen möglicherweise erforderlich sind, wenn Sie eine Migration durchführen.

2. Erstellen Sie einen Linux-basierten Cluster als Test-/Qualitätssicherungsumgebung. Weitere Informationen zur Erstellung eines Linux-basierten Clusters finden Sie unter [Erstellen von Linux-basierten Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3. Kopieren Sie vorhandene Aufträge, Datenquellen und Senken in die neue Umgebung.

4. Führen Sie Überprüfungstests durch, um sicherzustellen, dass Ihre Aufträge im neuen Cluster wie erwartet funktionieren.

Nachdem Sie überprüft haben, dass alles wie erwartet funktioniert, planen Sie für die Migration eine Downtime. Führen Sie während dieser Downtime die folgenden Aktionen durch:

1. Sichern Sie alle Daten, die vorübergehend lokal auf dem Clusterknoten gespeichert sind. zum Beispiel direkt auf einem Hauptknoten gespeicherte Daten.

2. Löschen Sie den Windows-basierten Cluster.

3. Erstellen Sie einen Linux-basierten Cluster mit demselben Standarddatenspeicher, der vom Windows-basierten Cluster verwendet wurde. Der Linux-basierte Cluster kann so weiterhin mit Ihren vorhandenen Produktionsdaten arbeiten.

4. Importieren Sie alle vorübergehenden Daten, die Sie gesichert haben.

5. Starten Sie Aufträge/Verarbeiten Sie weiterhin mithilfe des neuen Clusters.

### <a name="copy-data-to-the-test-environment"></a>Kopieren von Daten in die Testumgebung

Es existieren viele Methoden zum Kopieren von Daten und Aufträgen. Die in diesem Abschnitt beschriebenen sind jedoch die einfachsten Methoden, um Dateien direkt in einen Testcluster zu verschieben.

#### <a name="hdfs-copy"></a>HDFS-Kopie

Kopieren Sie anhand folgender Schritte Daten aus dem Produktionscluster in das Testcluster. In diesen Schritten wird das in HDInsight enthaltene `hdfs dfs`-Hilfsprogramm verwendet.

1. Suchen Sie die Informationen zum Speicherkonto und Standardcontainer für Ihren vorhandenen Cluster. Im folgenden Beispiel wird PowerShell zum Abrufen dieser Informationen verwendet:

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. Folgen Sie der Anleitung im Dokument „Erstellen von Linux-basierten Hadoop-Clustern in HDInsight“, um eine neue Testumgebung zu erstellen. Bevor Sie den Cluster erstellen, wählen Sie stattdessen **Optionale Konfiguration**aus.

3. Klicken Sie im Bereich „Optionale Konfiguration“ auf **Verknüpfte Speicherkonten**.

4. Wählen Sie **Einen Speicherschlüssel hinzufügen**aus, und wählen Sie, wenn Sie dazu aufgefordert werden, das Speicherkonto aus, das in Schritt 1 vom PowerShell-Skript zurückgegeben wurde. Klicken Sie in jedem Bereich auf **Auswählen**. Erstellen Sie schließlich den Cluster.

5. Nachdem der Cluster erstellt wurde, verbinden Sie sich mithilfe von **SSH** mit dem Cluster. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

6. Verwenden Sie über die SSH-Sitzung den folgenden Befehl, um Daten aus dem verknüpften Speicherkonto in das neue Standardspeicherkonto zu kopieren. Ersetzen Sie CONTAINER durch die von PowerShell zurückgegebenen Containerinformationen. Ersetzen Sie __KONTO__ durch den Kontonamen. Ersetzen Sie den Datenpfad mit dem Datendateipfad.

    ```bash
    hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location
    ```

    > [!NOTE]  
    > Wenn die Verzeichnisstruktur, die die Daten enthält, in der Testumgebung nicht existiert, können Sie diese mithilfe des folgenden Befehls erstellen:

    ```bash
    hdfs dfs -mkdir -p /new/path/to/create
    ```

    Der Schalter `-p` ermöglicht die Erstellung aller Verzeichnisse im Pfad.

#### <a name="direct-copy-between-blobs-in-azure-storage"></a>Direktes Kopieren zwischen BLOBs in Azure Storage

Alternativ können Sie das Azure PowerShell-Cmdlet `Start-AzureStorageBlobCopy` verwenden, um Blobs zwischen Speicherkonten außerhalb von HDInsight zu kopieren. Weitere Informationen finden Sie im Abschnitt „Verwalten von Azure-Blobs“ im Artikel „Verwenden von Azure PowerShell mit Azure Storage“.

## <a name="client-side-technologies"></a>Clientseitige Technologien

Clientseitige Technologien wie [Azure PowerShell-Cmdlets](/powershell/azureps-cmdlets-docs), die [klassische Azure CLI](../cli-install-nodejs.md) oder das [.NET SDK für Apache Hadoop](https://hadoopsdk.codeplex.com/) funktionieren in Linux-basierten Clustern weiterhin. Diese Technologien basieren auf REST-APIs, die in beiden Cluster-Betriebssystemtypen identisch sind.

## <a name="server-side-technologies"></a>Serverseitige Technologien

Die folgende Tabelle enthält eine Hilfestellung zum Migrieren von serverseitigen Komponenten, die Windows-spezifisch sind.

| Wenn Sie folgende Technologie verwenden ... | Führen Sie diese Aktion durch... |
| --- | --- |
| **PowerShell** (serverseitige Skripts, einschließlich Skriptaktionen, die während der Clustererstellung verwendet werden) |Umschreiben zu Bash-Skripts. Informationen zu Skriptaktionen finden Sie unter [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md) und [Entwickeln von Skriptaktionen mit HDInsight](hdinsight-hadoop-script-actions-linux.md). |
| **Klassische Azure CLI** (serverseitige Skripts) |Die klassische Azure CLI ist für Linux verfügbar, auf den Hauptknoten des HDInsight-Clusters jedoch nicht vorinstalliert. Weitere Informationen zum Installieren der klassischen Azure CLI finden Sie unter [Erste Schritte mit der Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli). |
| **.NET-Komponenten** |.NET wird auf Linux-basierten HDInsight-Clustern über [Mono](https://mono-project.com) unterstützt. Weitere Informationen finden Sie unter [Migrieren von .NET-Lösungen in Linux-basiertes HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md). |
| **Win32-Komponenten oder andere reine Windows-Technologie** |Die Anleitung hängt von der Komponente oder der Technologie ab. Möglicherweise finden Sie eine mit Linux kompatible Version. Wenn dies nicht möglich ist, müssen Sie eine andere Lösung finden oder diese Komponente erneut schreiben. |

> [!IMPORTANT]  
> Das HDInsight-Verwaltungs-SDK ist nicht vollständig kompatibel mit Mono. Verwenden Sie es nicht in Lösungen, die für den HDInsight-Cluster bereitgestellt werden.

## <a name="cluster-creation"></a>Clustererstellung

Dieser Abschnitt enthält Informationen zu Unterschieden beim Erstellen von Clustern.

### <a name="ssh-user"></a>SSH-Benutzer

Linux-basierte HDInsight-Cluster nutzen **Secure Shell (SSH)** als Protokoll, um Clusterknoten Remotezugriff zu gewähren. Im Gegensatz zu Remotedesktop für Windows-basierte Cluster enthalten die meisten SSH-Clients keine grafische Benutzeroberfläche. Stattdessen stellt SSH-Clients eine Befehlszeile bereit, über die Sie Befehlen im Cluster ausführen können. Einige Clients (z.B. [MobaXterm](https://mobaxterm.mobatek.net/)) bieten zusätzlich zu einer Remotebefehlszeile einen grafischen Dateisystembrowser.

Während der Clustererstellung müssen Sie einen SSH-Benutzer und entweder ein **Kennwort** oder ein **öffentliches Schlüsselzertifikat** für die Authentifizierung bereitstellen.

Es empfiehlt sich, ein Public Key-Zertifikat zu verwenden, da dies sicherer ist als ein Kennwort. Die Zertifikatsauthentifizierung funktioniert, indem ein signiertes öffentliches/privates Schlüsselpaar generiert und anschließend beim Erstellen des Clusters der öffentliche Schlüssel bereitgestellt wird. Beim Verbinden zum Server mithilfe von SSH erfolgt die Authentifizierung für die Verbindung durch den privaten Schlüssel auf dem Client.

Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="cluster-customization"></a>Clusteranpassung

Mit Linux-basierten Clustern verwendete **Skriptaktionen** müssen in Bash-Skripts geschrieben sein. Auf Linux basierte Cluster können Skriptaktionen während oder nach der Clustererstellung verwenden. Weitere Informationen finden Sie unter [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md) und [Entwickeln von Skriptaktionen mit HDInsight](hdinsight-hadoop-script-actions-linux.md).

Eine weitere Anpassungsfunktion ist **bootstrap**. Diese Funktion ermöglicht Ihnen, bei Windows-Clustern den Speicherort zusätzlicher Bibliotheken zum Verwenden mit Hive anzugeben. Diese Bibliotheken sind nach der Clustererstellung automatisch für die Verwendung mit Hive-Abfragen verfügbar, ohne dass `ADD JAR`verwendet werden muss.

Die Bootstrap-Funktion für Linux-basierte Cluster bietet diese Funktionalität nicht. Verwenden Sie stattdessen eine Skriptaktion, wie in [Hinzufügen von Apache Hive-Bibliotheken während der Erstellung des HDInsight-Clusters](hdinsight-hadoop-add-hive-libraries.md)beschrieben.

### <a name="virtual-networks"></a>Virtuelle Netzwerke

Windows-basierte HDInsight-Cluster funktionieren nur mit klassischen virtuellen Netzwerken, während für Linux-basierte HDInsight-Cluster virtuelle Resource Manager-Netzwerke erforderlich sind. Wenn Sie über Ressourcen in einem klassischen virtuellen Netzwerk verfügen, mit dem sich der Linux-basierte HDInsight-Cluster verbinden muss, finden Sie weitere Informationen dazu unter [Verbinden von virtuellen Netzwerken aus unterschiedlichen Bereitstellungsmodellen im Portal](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Weitere Informationen zu den Konfigurationsanforderungen finden Sie in der Dokumentation [Erweitern der HDInsight-Funktionen mit Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).

## <a name="management-and-monitoring"></a>Verwaltung und Überwachung

Viele der Web-Benutzeroberflächen, die Sie möglicherweise mit Windows-basiertem HDInsight verwendet haben, wie Job History (Auftragsverlauf) oder die Yarn-Benutzeroberfläche, sind über Apache Ambari verfügbar. Darüber hinaus bietet die Ambari-Hive-Ansicht eine Möglichkeit zum Ausführen von Hive-Abfragen mithilfe Ihres Webbrowsers. Die Ambari-Webbenutzeroberfläche ist in Linux-basierten Clustern unter https://CLUSTERNAME.azurehdinsight.net verfügbar.

Weitere Informationen zum Arbeiten mit Ambari finden Sie in den folgenden Dokumenten:

* [Apache Ambari-Web](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari-REST-API](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Ambari-Warnungen

Ambari verfügt über ein Warnsystem, das Sie über potenzielle Probleme mit dem Cluster informiert. Warnungen werden als rote oder gelbe Einträge in der Ambari-Webbenutzeroberfläche angezeigt, Sie können sie aber auch über die REST-API abrufen.

> [!IMPORTANT]  
> Ambari-Warnungen weisen darauf hin, dass *möglicherweise* ein Problem besteht, nicht, dass ein Problem *tatsächlich* besteht. Zum Beispiel erhalten Sie möglicherweise eine Warnung, dass der Zugriff auf HiveServer2 nicht möglich ist, obwohl Sie normal darauf zugreifen können.
>
> Viele Warnungen werden als intervallbasierte Abfragen bei einem Dienst implementiert und erwarten innerhalb eines bestimmten Zeitrahmens eine Antwort. Die Warnung bedeutet also nicht zwingend, dass der Dienst nicht verfügbar ist, sondern nur, dass im erwarteten Zeitrahmen keine Ergebnisse zurückgegeben wurden.

## <a name="file-system-locations"></a>Dateisystem-Speicherorte

Das Linux-Cluster-Dateisystem ist anders angeordnet als Windows-basierte HDInsight-Cluster. Verwenden Sie die folgende Tabelle, um häufig verwendete Dateien zu suchen.

| Ich muss ... finden | Sie befinden/befindet sich unter ... |
| --- | --- |
| Konfiguration |`/etc`. Beispiel: `/etc/hadoop/conf/core-site.xml` |
| Protokolldateien |`/var/logs` |
| Hortonworks Data Platform (HDP) |`/usr/hdp`. Hier befinden sich zwei Verzeichnisse, die aktuelle HDP-Version und `current`. Das `current`-Verzeichnis enthält symbolische Verknüpfungen zu Dateien und Verzeichnissen im Versionsnummernverzeichnis. Das `current`-Verzeichnis wird als einfache Möglichkeit des Zugriffs auf HDP-Dateien bereitgestellt, da sich die Versionsnummer beim Aktualisieren der HDP-Version ändert. |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

Im Allgemeinen sollten Sie, falls Sie den Namen der Datei kennen, in einer SSH-Sitzung den folgenden Befehl verwenden, um den Dateipfad zu suchen:

    find / -name FILENAME 2>/dev/null

Sie können auch Platzhalter mit dem Dateinamen verwenden. Beispiel: `find / -name *streaming*.jar 2>/dev/null` gibt den Pfad für alle JAR-Dateien zurück, die als Teil des Dateinamens das Wort „Streaming“ enthalten.

## <a name="apache-hive-apache-pig-and-mapreduce"></a>Apache Hive, Apache Pig und MapReduce

Pig- und MapReduce-Workloads sind auf Linux-basierten Clustern ähnlich. Linux-basierte HDInsight-Cluster können jedoch mit neueren Versionen von Hadoop, Hive und Pig erstellt werden. Diese Versionsunterschiede können Änderungen in der Funktionsweise vorhandener Lösungen mit sich bringen. Weitere Informationen zu den mit HDInsight bereitgestellten Versionen von Komponenten finden Sie unter [HDInsight-Komponentenversionen](hdinsight-component-versioning.md).

Linux-basiertes HDInsight bietet keine Remotedesktopfunktionen. Stattdessen können Sie SSH verwenden, um eine Remoteverbindung mit den Clusterhauptknoten herzustellen. Weitere Informationen finden Sie in den folgenden Dokumenten:

* [Verwenden von Apache Hive mit SSH](hdinsight-hadoop-use-hive-ssh.md)
* [Verwenden von Apache Pig mit SSH](hadoop/apache-hadoop-use-pig-ssh.md)
* [Verwenden von MapReduce mit SSH](hadoop/apache-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

> [!IMPORTANT]  
> Wenn Sie einen externen Hive-Metastore verwenden, sollten Sie den Metastore vor der Verwendung mit Linux-basiertem HDInsight sichern. Linux-basiertes HDInsight wird mit neueren Versionen von Hive bereitgestellt, die möglicherweise Inkompatibilitäten mit in früheren Versionen erstellten Metastores aufweisen.

Das folgende Diagramm enthält Hilfestellungen zum Migrieren Ihrer Hive-Workloads:

| Auf Windows-basierten Clustern verwende ich ... | Auf Linux-basierten Clustern verwende ich ... |
| --- | --- |
| **Hive-Editor** |[Apache Hive-Ansicht in Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` zum Aktivieren von Tez |Apache Tez ist die Standard-Ausführungs-Engine für Linux-basierte Cluster. Die SET-Anweisung wird also nicht mehr benötigt. |
| Benutzerdefinierte C#-Funktionen | Informationen zum Überprüfen von C#-Komponenten mit Linux-basiertem HDInsight finden Sie unter [Migrieren von .NET-Lösungen in Linux-basiertes HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md). |
| CMD-Dateien oder -Skripts auf dem Server, die als Teil eines Hive-Auftrags aufgerufen wurden |Verwenden von Bash-Skripts |
| `hive` -Befehl von Remotedesktop |Verwenden von [Apache Hive Beeline](hadoop/apache-hadoop-use-hive-beeline.md) oder [Apache Hive in einer SSH-Sitzung](hdinsight-hadoop-use-hive-ssh.md) |

### <a name="pig"></a>Pig

| Auf Windows-basierten Clustern verwende ich ... | Auf Linux-basierten Clustern verwende ich ... |
| --- | --- |
| Benutzerdefinierte C#-Funktionen | Informationen zum Überprüfen von C#-Komponenten mit Linux-basiertem HDInsight finden Sie unter [Migrieren von .NET-Lösungen in Linux-basiertes HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md). |
| CMD-Dateien oder -Skripts auf dem Server, die als Teil eines Pig-Auftrags aufgerufen wurden |Verwenden von Bash-Skripts |

### <a name="mapreduce"></a>MapReduce

| Auf Windows-basierten Clustern verwende ich ... | Auf Linux-basierten Clustern verwende ich ... |
| --- | --- |
| C#-Mapper- und -Reducerkomponenten | Informationen zum Überprüfen von C#-Komponenten mit Linux-basiertem HDInsight finden Sie unter [Migrieren von .NET-Lösungen in Linux-basiertes HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md). |
| CMD-Dateien oder -Skripts auf dem Server, die als Teil eines Hive-Auftrags aufgerufen wurden |Verwenden von Bash-Skripts |

## <a name="apache-oozie"></a>Apache Oozie

> [!IMPORTANT]  
> Wenn Sie einen externen Oozie-Metastore verwenden, sollten Sie den Metastore vor der Verwendung mit Linux-basiertem HDInsight sichern. Linux-basiertes HDInsight wird mit neueren Versionen von Oozie bereitgestellt, die möglicherweise Inkompatibilitäten mit in früheren Versionen erstellten Metastores aufweisen.

Oozie-Workflows erlauben Shellaktionen. Bei Shellaktionen wird die Standardshell für das Betriebssystem zum Ausführen von Befehlszeilenbefehlen verwendet. Wenn Sie über Oozie-Workflows verfügen, die auf der Windows-Shell beruhen, müssen Sie die Workflows so umschreiben, dass sie auf der Linux-Shellumgebung (Bash) basieren. Weitere Informationen zur Verwendung von Shellaktionen mit Oozie finden Sie unter [Oozie shell action extension](https://oozie.apache.org/docs/3.3.0/DG_ShellActionExtension.html) (Erweiterung für Oozie-Shellaktionen).

Wenn C#-Anwendungen in Ihrem Workflow verwendet werden, überprüfen Sie diese Anwendungen in einer Linux-Umgebung. Weitere Informationen finden Sie unter [Migrieren von .NET-Lösungen in Linux-basiertes HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="storm"></a>Storm

| Auf Windows-basierten Clustern verwende ich ... | Auf Linux-basierten Clustern verwende ich ... |
| --- | --- |
| Storm-Dashboard |Das Storm-Dashboard ist nicht verfügbar. Methoden zum Übermitteln von Topologien finden Sie unter [Bereitstellen und Verwalten von Apache Storm-Topologien in HDInsight unter Linux](storm/apache-storm-deploy-monitor-topology-linux.md). |
| Storm-Benutzeroberfläche |Die Storm-Benutzeroberfläche ist unter https://CLUSTERNAME.azurehdinsight.net/stormui verfügbar. |
| Visual Studio zum Erstellen, Bereitstellen und Verwalten von C#- oder Hybridtopologien |Visual Studio kann verwendet werden, um C#-Topologien (SCP.NET) oder Hybridtopologien in Linux-basiertem Storm in HDInsight zu erstellen, bereitzustellen und zu verwalten. Es kann nur mit Clustern verwendet werden, die nach dem 28.10.2016 erstellt wurden. |

## <a name="apache-hbase"></a>Apache HBase

Auf Linux-basierten Clustern ist `/hbase-unsecure`der übergeordnete ZNode für HBase. Legen Sie dies in der Konfiguration für alle Java-Clientanwendungen fest, die native HBase-Java-API verwenden.

Einen Beispielclient, der diesen Wert festlegt, finden Sie unter [Erstellen einer Java-basierten Apache HBase-Anwendung](hdinsight-hbase-build-java-maven.md).

## <a name="spark"></a>Spark

Spark-Cluster waren während der Vorschau auf Windows-Clustern verfügbar. Spark GA ist nur mit Linux-basierten Clustern verfügbar. Es existiert kein Migrationspfad von einer Vorabversion eines Windows-basierten Spark-Clusters zu einem veröffentlichten Linux-basierten Spark-Cluster.

## <a name="known-issues"></a>Bekannte Probleme

### <a name="azure-data-factory-custom-net-activities"></a>Benutzerdefinierte Azure Data Factory-.NET-Aktivitäten

Benutzerdefinierte Azure Data Factory-.NET-Aktivitäten werden auf Linux-basierten HDInsight-Clustern derzeit nicht unterstützt. Stattdessen sollten Sie eine der folgenden Methoden zum Implementieren von benutzerdefinierten Aktivitäten als Teil Ihrer ADF-Pipeline verwenden.

* Führen Sie .NET-Aktivitäten im Azure Batch-Pool aus. Informationen hierzu finden Sie im Abschnitt „Verwenden des mit Azure Batch verknüpften Dienstes“ im Artikel [Verwenden von benutzerdefinierten Aktivitäten in einer Azure Data Factory-Pipeline](../data-factory/transform-data-using-dotnet-custom-activity.md)
* Implementieren Sie die Aktivität als MapReduce-Aktivität. Weitere Informationen finden Sie unter [Aufrufen von MapReduce-Programmen über Data Factory](../data-factory/transform-data-using-hadoop-map-reduce.md).

### <a name="line-endings"></a>Zeilenenden

Im Allgemeinen verwenden Zeilenenden in Windows-basierten Systemen CRLF und in Linux-basierten Systemen LF. Möglicherweise müssen Sie die vorhandenen Datenproduzenten und -consumer so anpassen, dass Sie mit LF funktionieren.

Zum Beispiel werden Daten beim Verwenden von Azure PowerShell zum Abfragen von HDInsight auf einem Windows-basierten Cluster Daten mit CRLF zurückgeben. Dieselbe Abfrage gibt bei einem Linux-basierten Cluster LF zurück. Führen Sie vor der Migration zu einem Linux-basierten Cluster einen Test durch, um zu sehen, ob die Zeilenenden zu einem Problem mit Ihrer Lösung führen.

Verwenden Sie LF immer als Zeilenende für Skripts, die auf den Clusterknoten ausgeführt werden. Wenn Sie CRLF verwenden, entdecken Sie möglicherweise Fehler beim Ausführen der Skripts auf einem Linux-basierten Cluster.

Wenn die Skripts keine Zeichenfolgen mit eingebetteten CR-Zeichen enthalten, können Sie die Zeilenenden massenweise mithilfe einer der folgenden Methoden ändern:

* **Vor dem Hochladen in den Cluster**: Verwenden Sie die folgenden PowerShell-Anweisungen zum Ändern der Zeilenenden von CRLF zu LF, bevor Sie das Skript in den Cluster hochladen.

    ```powershell
    $original_file ='c:\path\to\script.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)
    ```

* **Nach dem Hochladen in den Cluster**: Verwenden Sie den folgenden Befehl aus einer SSH-Sitzung im Linux-basierten Cluster, um das Skript zu ändern.

    ```bash
    hdfs dfs -get wasb:///path/to/script.py oldscript.py
    tr -d '\r' < oldscript.py > script.py
    hdfs dfs -put -f script.py wasb:///path/to/script.py
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Linux-basierte HDInsight-Cluster erstellen](hdinsight-hadoop-provision-linux-clusters.md)
* [Verwenden von SSH für das Herstellen von Verbindungen mit HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Verwalten eines Linux-basierten Clusters mithilfe von Apache Ambari](hdinsight-hadoop-manage-ambari.md)
