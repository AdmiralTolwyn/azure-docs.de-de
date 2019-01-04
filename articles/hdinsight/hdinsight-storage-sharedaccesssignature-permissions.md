---
title: Beschränken des Zugriffs mit Shared Access Signatures – Azure HDInsight
description: Erfahren Sie, wie Sie mit Shared Access Signatures den HDInsight-Zugriff auf Daten in Azure-Speicherblobs einschränken.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 100c9266718d618b8b00a3169c3d88ac7d501791
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409920"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Verwenden von Azure Storage Shared Access Signatures zum Einschränken des Zugriffs auf Daten mit HDInsight

HDInsight hat vollen Zugriff auf Daten in Azure Storage-Konten, die mit dem Cluster verbunden sind. Sie können Shared Access Signatures für den Blobcontainer verwenden, um den Zugriff auf die Daten einzuschränken. Shared Access Signatures (SAS) sind ein Feature von Azure Storage-Konten, das das Einschränken des Zugriffs auf Daten ermöglicht. Sie können beispielsweise einen schreibgeschützten Zugriff auf Daten bieten.

> [!IMPORTANT]  
> Erwägen Sie für eine Lösung mit Apache Ranger die Verwendung von in die Domäne eingebundenem HDInsight. Weitere Informationen finden Sie im Dokument [Konfigurieren von in die Domäne eingebundenen HDInsight-Clustern (Vorschau)](./domain-joined/apache-domain-joined-configure.md).

> [!WARNING]  
> HDInsight benötigt vollen Zugriff auf den Standardspeicher für den Cluster.

## <a name="requirements"></a>Requirements (Anforderungen)

* Ein Azure-Abonnement
* C# oder Python. C#-Beispielcode wird als Visual Studio-Projektmappe bereitgestellt.

  * Die Visual Studio-Version 2013, 2015 oder 2017 ist erforderlich.
  * Die Python-Version 2.7 oder höher ist erforderlich.

* Linux-basierter HDInsight-Cluster ODER [Azure PowerShell][powershell]. Wenn Sie bereits über einen Linux-basierten Cluster verfügen, können dem Cluster mit Apache Ambari eine Shared Access Signature hinzufügen. Falls nicht, können Sie mit Azure PowerShell einen Cluster erstellen und während der Clustererstellung eine Shared Access Signature hinzufügen.

    > [!IMPORTANT]  
    > Linux ist das einzige Betriebssystem, das unter HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [Welche Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Die Beispieldateien von [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Dieses Repository enthält die folgenden Elemente:

  * Ein Visual Studio-Projekt, in dem ein Speichercontainer, eine gespeicherte Richtlinie und SAS für die Verwendung mit HDInsight erstellt werden kann.
  * Ein Python-Skript zum Erstellen eines Speichercontainers, einer gespeicherten Richtlinie und einer SAS für die Verwendung mit HDInsight.
  * Ein PowerShell-Skript zum Erstellen eines HDInsight-Clusters und seiner Konfiguration für die Verwendung von SAS.

## <a name="shared-access-signatures"></a>Shared Access Signatures

Es gibt zwei Arten von Shared Access Signatures:

* Ad-hoc: Startzeit, Ablaufzeit und Berechtigungen für die SAS werden direkt im SAS-URI angegeben.

* Gespeicherte Zugriffsrichtlinie: Eine gespeicherte Zugriffsrichtlinie wird für einen Ressourcencontainer definiert (also etwa für einen Blobcontainer). Eine Richtlinie kann verwendet werden, um Einschränkungen für eine oder mehrere SAS zu verwalten. Wenn Sie eine SAS mit einer gespeicherten Zugriffsrichtlinie verknüpfen, erbt die SAS die Einschränkungen (Startzeit, Ablaufzeit und Berechtigungen) dieser gespeicherten Zugriffsrichtlinie.

Der Unterschied zwischen diesen beiden Formen ist wichtig für ein Schlüsselszenario: Widerruf. Eine SAS ist eine URL und kann daher von beliebiger Stelle verwendet werden, unabhängig davon, wer die SAS ursprünglich angefordert hatte. Wenn eine SAS veröffentlicht wird, kann diese von beliebiger Stelle weltweit verwendet werden. Auf diese Weise verteilte SAS sind gültig, bis eines von vier Ereignissen eintritt:

1. Die Ablaufzeit der SAS wird erreicht.

2. Die angegebene Ablaufzeit für die gespeicherte Zugriffsrichtlinie, auf die SAS verweist, wurde erreicht. Folgende Szenarien bewirken, dass die Ablaufzeit erreicht wird:

    * Das Zeitintervall ist abgelaufen.
    * Die gespeicherte Zugriffsrichtlinie wird so geändert, dass die Ablaufzeit auf einen Zeitpunkt in der Vergangenheit gesetzt ist. Die Änderung der Ablaufzeit ist eine Möglichkeit zum Widerrufen der SAS.

3. Die von der SAS referenzierte gespeicherte Zugriffsrichtlinie wird gelöscht, wodurch die SAS ebenfalls widerrufen wird. Wenn Sie die gespeicherte Zugriffsrichtlinie mit dem gleichen Namen neu erstellen, sind alle SAS-Token für die vorherige Richtlinie gültig (wenn die Ablaufzeit für die SAS nicht überschritten wurde). Falls Sie die SAS widerrufen möchten, müssen Sie einen anderen Namen verwenden, wenn Sie die Zugriffsrichtlinie mit einer Ablaufzeit in der Zukunft erneut erstellen.

4. Der Kontoschlüssel, mit dem die SAS erstellt wurde, wird erneut generiert. Das Neugenerieren des Schlüssels lässt die Authentifizierung bei allen Anwendungen fehlschlagen, die den vorherigen Schlüssel verwenden. Aktualisieren Sie alle Komponenten auf den neuen Schlüssel.

> [!IMPORTANT]  
> Ein SAS-URI wird dem Kontoschlüssel, mit dem die Signatur erstellt wurde, und der zugehörigen gespeicherten Zugriffsrichtlinie (sofern vorhanden) zugeordnet. Wenn keine gespeicherte Zugriffsrichtlinie angegeben wird, kann eine SAS nur durch Änderung des Kontoschlüssels aufgehoben werden.

Es wird empfohlen, stets gespeicherte Zugriffsrichtlinien zu verwenden. Bei Verwendung gespeicherter Richtlinien können Sie Signaturen aufheben oder bei Bedarf das Ablaufdatum verlängern. In den Schritten in diesem Dokument werden gespeicherte Zugriffsrichtlinien zum Generieren von SAS verwendet.

Weitere Informationen zu Shared Access Signatures finden Sie unter [Grundlagen zum SAS-Modell](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="create-a-stored-policy-and-sas-using-c"></a>Erstellen einer gespeicherte Richtlinie und SAS mit C\#

1. Öffnen Sie die Projektmappe in Visual Studio.

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **SASToken**, und wählen Sie **Eigenschaften** aus.

3. Wählen Sie **Einstellungen** aus, und fügen Sie Werte für die folgenden Einträge hinzu:

   * StorageConnectionString: Die Verbindungszeichenfolge für das Speicherkonto, für das eine gespeicherte Richtlinie und SAS erstellt werden soll. Das Format muss `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` sein, wobei `myaccount` der Name Ihres Speicherkontos und `mykey` der Schlüssel des Speicherkontos ist.

   * ContainerName: Der Container im Speicherkonto, auf den Sie den Zugriff beschränken möchten.

   * SASPolicyName: Der zu verwendende Name für die gespeicherte Richtlinie, die erstellt wird.

   * FileToUpload: Der Pfad zu einer Datei, die in den Container hochgeladen wird.

4. Führen Sie das Projekt aus. Informationen ähnlich dem folgenden Text werden angezeigt, nachdem die SAS generiert wurde:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Speichern Sie die SAS-Richtlinientoken, den Speicherkontonamen und den Containernamen. Diese Werte werden verwendet, wenn Sie das Speicherkonto mit Ihrem HDInsight-Cluster verknüpfen.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Erstellen einer gespeicherte Richtlinie und SAS mit Python

1. Öffnen Sie die Datei „SASToken.py“, und ändern Sie die folgenden Werte:

   * policy\_name: Der zu verwendende Name für die gespeicherte Richtlinie, die erstellt wird.

   * storage\_account\_name: Der Name Ihres Speicherkontos.

   * storage\_account\_key: Der Schlüssel für das Speicherkonto.

   * storage\_container\_name: Der Container im Speicherkonto, auf den Sie den Zugriff beschränken möchten.

   * example\_file\_path: Der Pfad zu einer Datei, die in den Container hochgeladen wird.

2. Führen Sie das Skript aus. Nach Abschluss des Skripts wird ein SAS-Token ähnlich dem folgenden Text angezeigt:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Speichern Sie die SAS-Richtlinientoken, den Speicherkontonamen und den Containernamen. Diese Werte werden verwendet, wenn Sie das Speicherkonto mit Ihrem HDInsight-Cluster verknüpfen.

## <a name="use-the-sas-with-hdinsight"></a>Verwenden der SAS mit HDInsight

Wenn Sie einen HDInsight-Cluster erstellen, müssen Sie ein primäres Speicherkonto angeben. Optional können Sie zusätzliche Speicherkonten angeben. Beide Methoden zum Hinzufügen von Speicher benötigen Vollzugriff auf die Speicherkonten und Container, die verwendet werden.

Fügen Sie der Konfiguration für den Cluster von **core-site** einen benutzerdefinierten Eintrag hinzu, um eine Shared Access Signature zum Begrenzen des Zugriffs auf einen Container zu verwenden.

* Für **Windows-basierte** oder **Linux-basierte** HDInsight-Cluster kann dies während der Erstellung des Clusters mithilfe von PowerShell erfolgen.
* Für **Linux-basierte** HDInsight-Cluster ändern Sie die Konfiguration nach der Erstellung des Clusters mit Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Erstellen eines Clusters, der die SAS verwendet

Ein Beispiel zum Erstellen eines HDInsight-Clusters, der die SAS verwendet, befindet sich im Verzeichnis `CreateCluster` des Repositorys. Führen Sie zu dessen Nutzung die folgenden Schritte aus:

1. Öffnen Sie die Datei `CreateCluster\HDInsightSAS.ps1` in einem Text-Editor, und ändern Sie am Anfang des Dokuments die folgenden Werte.

    ```powershell
    # Replace 'mycluster' with the name of the cluster to be created
    $clusterName = 'mycluster'
    # Valid values are 'Linux' and 'Windows'
    $osType = 'Linux'
    # Replace 'myresourcegroup' with the name of the group to be created
    $resourceGroupName = 'myresourcegroup'
    # Replace with the Azure data center you want to the cluster to live in
    $location = 'North Europe'
    # Replace with the name of the default storage account to be created
    $defaultStorageAccountName = 'mystorageaccount'
    # Replace with the name of the SAS container created earlier
    $SASContainerName = 'sascontainer'
    # Replace with the name of the SAS storage account created earlier
    $SASStorageAccountName = 'sasaccount'
    # Replace with the SAS token generated earlier
    $SASToken = 'sastoken'
    # Set the number of worker nodes in the cluster
    $clusterSizeInNodes = 3
    ```

    Ändern Sie z. B. `'mycluster'` in den Namen des Clusters, den Sie erstellen möchten. Die SAS-Werte müssen den Werten in den vorherigen Schritten zum Erstellen eines Speicherkontos und SAS-Tokens entsprechen.

    Nachdem Sie die Werte geändert haben, speichern Sie die Datei.

2. Öffnen Sie eine neue Azure PowerShell-Eingabeaufforderung. Wenn Sie mit Azure PowerShell nicht vertraut sind oder dieses Feature nicht installiert haben, lesen Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell] nach.

1. Führen Sie an der Eingabeaufforderung folgenden Befehl aus, um sich bei Ihrem Azure-Abonnement zu authentifizieren:

    ```powershell
    Connect-AzureRmAccount
    ```

    Melden Sie sich bei Aufforderung mit dem Konto Ihres Azure-Abonnements an.

    Wenn Ihr Konto mehreren Azure-Abonnements zugeordnet ist, müssen Sie möglicherweise `Select-AzureRmSubscription` zur Auswahl des gewünschten Abonnements verwenden.

4. Wechseln Sie an der Eingabeaufforderung zum Verzeichnis `CreateCluster` , das die Datei „HDInsightSAS.ps1“ enthält. Führen Sie anschließend den folgenden Befehl aus, um das Skript auszuführen:

    ```powershell
    .\HDInsightSAS.ps1
    ```

    Bei der Ausführung des Skripts zum Erstellen der Ressourcengruppe und Speicherkonten wird die Ausgabe an der PowerShell-Eingabeaufforderung protokolliert. Es fordert Sie auf, den HTTP-Benutzer für den HDInsight-Cluster einzugeben. Dieses Konto wird verwendet, um den HTTP/S-Zugriff auf den Cluster zu schützen.

    Wenn Sie einen Linux-basierten Cluster erstellen, werden Sie zur Angabe des Namens und Kennworts eines SSH-Benutzerkontos aufgefordert. Dieses Konto wird für eine Remoteanmeldung am Cluster verwendet.

   > [!IMPORTANT]  
   > Wenn Sie zur Angabe des HTTP/S- oder SSH-Benutzernamens und Kennworts aufgefordert werden, müssen Sie ein Kennwort angeben, das die folgenden Kriterien erfüllt:
   >
   > * Mindestens 10 Zeichen
   > * Mindestens eine Ziffer
   > * Mindestens ein nicht alphanumerisches Zeichen
   > * Mindestens ein Groß- oder Kleinbuchstaben

Die Ausführung dieses Skripts dauert eine Weile, meist etwa 15 Minuten. Wenn das Skript ohne Fehler abgeschlossen wird, wurde der Cluster erstellt.

### <a name="use-the-sas-with-an-existing-cluster"></a>Verwenden des SAS mit einem bestehenden Cluster

Wenn Sie bereits einen Linux-basierten Cluster haben, können Sie die SAS der **core-site** -Konfiguration mithilfe der folgenden Schritte hinzufügen:

1. Öffnen Sie die Ambari-Webbenutzeroberfläche für den Cluster. Die Adresse für diese Seite lautet https://YOURCLUSTERNAME.azurehdinsight.net. Authentifizieren Sie sich bei Aufforderung am Cluster mithilfe des Administratornamens (admin) und des Kennworts, das Sie beim Erstellen des Clusters verwendet haben.

2. Wählen Sie links auf der Ambari-Webbenutzeroberfläche **HDFS** und dann in der Mitte der Seite die Registerkarte **Configs** aus.

3. Wählen Sie die Registerkarte **Advanced** aus, und scrollen Sie zum Abschnitt **Custom core-site**.

4. Erweitern Sie den Abschnitt **Custom core-site**, scrollen Sie zum Seitenende, und klicken Sie auf den Link **Add property...**. Verwenden Sie für die Felder **Key** und **Value** die folgenden Werte:

   * **Key**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **Value**: Die SAS, die von der zuvor ausgeführten C#- oder Python-Anwendung zurückgegeben wurde.

     Ersetzen Sie **CONTAINERNAME** durch den Containernamen, den Sie mit der C#- oder SAS-Anwendung verwendet haben. Ersetzen Sie **STORAGEACCOUNTNAME** durch den Namen des verwendeten Speicherkontos.

5. Klicken Sie auf die Schaltfläche **Add**, um diesen Schlüssel und Wert zu speichern. Klicken Sie dann auf die Schaltfläche **Save**, um die Konfigurationsänderungen zu speichern. Fügen Sie bei Aufforderung eine Beschreibung der Änderung hinzu (z.B. „Hinzufügen des SAS-Speicherzugriffs“), und klicken Sie anschließend auf **Speichern**.

    Klicken Sie auf **OK** , wenn die Änderungen abgeschlossen sind.

   > [!IMPORTANT]  
   > Sie müssen mehrere Dienste neu starten, damit die Änderung wirksam wird.

6. Wählen Sie auf der Ambari-Webbenutzeroberfläche in der Liste auf der linken Seite **HDFS** und dann in der Dropdownliste **Service Actions** (Dienstaktionen) auf der rechten Seite **Restart All Affected** (Alle betroffenen Dienste neu starten) aus. Klicken Sie bei entsprechender Aufforderung auf __Confirm Restart All__ (Neustart aller Dienste bestätigen).

    Wiederholen Sie diesen Vorgang für MapReduce2 und YARN.

7. Sobald diese Dienste neu gestartet wurden, wählen Sie sie nacheinander aus und deaktivieren den Wartungsmodus in der Dropdownliste **Service Actions** (Dienstaktionen).

## <a name="test-restricted-access"></a>Testen des eingeschränkten Zugriffs

Um sicherzustellen, dass der Zugriff eingeschränkt wird, verwenden Sie die folgenden Methoden:

* Nutzen Sie für **Windows-basierte** -Cluster Remotedesktop zur Verbindung mit dem Cluster. Weitere Informationen finden Sie unter [Connect to HDInsight using RDP (Herstellen einer Verbindung mit HDInsight mithilfe von RDP)](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

    Nachdem die Verbindung aufgebaut wurde, verwenden Sie das Symbol **Hadoop-Befehlszeile** auf dem Desktop, um eine Eingabeaufforderung zu öffnen.

* Verwenden Sie für **Linux-basierte** -Cluster SSH zur Verbindung mit dem Cluster. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

Nachdem die Verbindung mit dem Cluster hergestellt wurde, befolgen Sie die folgenden Schritte zum Überprüfen, ob Sie Elemente im SAS-Speicherkonto ausschließlich lesen und auflisten können:

1. Um die Inhalte des Containers aufzulisten, führen Sie an der Eingabeaufforderung den folgenden Befehl aus: 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Ersetzen Sie **SASCONTAINER** durch den Namen des Containers, den Sie für das SAS-Speicherkonto erstellt haben. Ersetzen Sie **SASACCOUNTNAME** durch den Namen des Speicherkontos, das für die SAS verwendet wird.

    Die Liste enthält die Datei, die bei der Erstellung des Containers und der SAS hochgeladen wurde.

2. Geben Sie den folgenden Befehl an, um sicherzustellen, dass Sie den Inhalt der Datei lesen können. Ersetzen Sie **SASCONTAINER** und **SASACCOUNTNAME** wie im vorigen Schritt. Ersetzen Sie **FILENAME** durch den Namen der Datei, die im vorherigen Befehl angezeigt wurde:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    Dieser Befehl listet den Inhalt der Datei auf.

3. Geben Sie den folgenden Befehl an, um die Datei in das lokale Dateisystem herunterzuladen:

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    Damit wird die Datei in eine lokale Datei mit dem Namen **testfile.txt** heruntergeladen.

4. Geben Sie zum Hochladen der lokalen Datei in eine neue Datei mit dem Namen **testupload.txt** in den SAS-Speicher folgenden Befehl an:

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Sie erhalten eine Meldung wie den folgenden Text:

        put: java.io.IOException

    Dieser Fehler tritt auf, weil der Speicherort nur einen Lese- und Auflistungszugriff zulässt. Geben Sie den folgenden Befehl an, um die Daten im Standardspeicher des Clusters abzulegen, der Schreibzugriff zulässt:

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

    Dieses Mal sollte der Vorgang erfolgreich abgeschlossen werden.

## <a name="troubleshooting"></a>Problembehandlung

### <a name="a-task-was-canceled"></a>Eine Aufgabe wurde abgebrochen.

**Symptome:** Beim Erstellen eines Clusters mithilfe des PowerShell-Skripts wird unter Umständen folgende Fehlermeldung angezeigt:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**Ursache:** Dieser Fehler kann auftreten, wenn Sie ein Kennwort für den Administrator/HTTP-Benutzer für den Cluster oder (bei Linux-basierten Clustern) für den SSH-Benutzer verwenden.

**Lösung:** Verwenden Sie ein Kennwort, das folgende Kriterien erfüllt:

* Mindestens 10 Zeichen
* Mindestens eine Ziffer
* Mindestens ein nicht alphanumerisches Zeichen
* Mindestens ein Groß- oder Kleinbuchstaben

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Ihrem HDInsight-Cluster Speicher mit eingeschränktem Zugriff hinzufügen, können Sie sich mit anderen Möglichkeiten des Arbeitens mit Daten in Ihrem Cluster vertraut machen:

* [Verwenden von Apache Hive mit HDInsight](hadoop/hdinsight-use-hive.md)
* [Verwenden von Apache Pig mit HDInsight](hadoop/hdinsight-use-pig.md)
* [Verwenden von MapReduce mit HDInsight](hadoop/hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs
