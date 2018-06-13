---
title: Hinzufügen zusätzlicher Azure-Speicherkonten zu HDInsight | Microsoft Docs
description: Erfahren Sie, wie Sie einem vorhandenen HDInsight-Cluster zusätzliche Azure-Speicherkonten hinzufügen.
services: hdinsight
documentationCenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 1404b37725362a71ccb4a0a84dff0c7c4ca591e2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32170100"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Hinzufügen zusätzlicher Speicherkonten zu HDInsight

Erfahren Sie, wie Sie Skriptaktionen verwenden, um HDInsight zusätzliche Azure-Speicherkonten hinzuzufügen. Mit den Schritten in diesem Dokument fügen Sie einem vorhandenen Linux-basierten HDInsight-Cluster ein Speicherkonto hinzu.

> [!IMPORTANT]
> Die Informationen in diesem Dokument beziehen sich auf das Hinzufügen zusätzlichen Speichers zu einem Cluster, nachdem es erstellt wurde. Informationen zum Hinzufügen von Speicherkonten während der Clustererstellung finden Sie unter [Einrichten von Clustern in HDInsight mit Hadoop, Spark, Kafka usw](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="how-it-works"></a>So funktioniert's

Diesem Skript werden die folgenden Parameter übergeben:

* __Azure-Speicherkontoname__: der Name des Speicherkontos, das dem HDInsight-Cluster hinzugefügt werden soll. Nach dem Ausführen des Skripts kann HDInsight Daten lesen und schreiben, die in diesem Speicherkonto gespeichert werden.

* __Azure-Speicherkontoschlüssel__: ein Schlüssel, der Zugriff auf das Speicherkonto gewährt.

* __-p__ (optional): Bei Angabe wird der Schlüssel nicht verschlüsselt und wird in der core-site.xml-Datei als Nur-Text gespeichert.

Während der Verarbeitung führt dieses Skript folgende Aktionen aus:

* Wenn das Speicherkonto bereits in der core-site.xml-Konfiguration für den Cluster vorhanden ist, wird das Skript beendet, und keine weiteren Aktionen werden ausgeführt.

* Es wird überprüft, ob das Speicherkonto vorhanden ist und mit dem Schlüssel darauf zugegriffen werden kann.

* Der Schlüssel wird mit den Clusteranmeldeinformationen verschlüsselt.

* Das Speicherkonto wird der core-site.xml-Datei hinzugefügt.

* Beendet die Dienste Oozie, YARN, MapReduce2 und HDFS und startet sie neu. Durch das Beenden und Starten dieser Dienste wird ihnen die Verwendung des neuen Speicherkontos ermöglicht.

> [!WARNING]
> Die Verwendung eines Speicherkontos an einem anderen Ort als dem HDInsight-Cluster wird nicht unterstützt.

## <a name="the-script"></a>Das Skript

__Skriptspeicherort__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Anforderungen__:

* Das Skript muss auf die __Hauptknoten__ angewendet werden.

## <a name="to-use-the-script"></a>So verwenden Sie das Skript

Das Skript kann über das Azure-Portal, Azure PowerShell oder die Azure CLI 1.0 verwendet werden. Weitere Informationen finden Sie im Dokument [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

> [!IMPORTANT]
> Verwenden Sie beim Ausführen der Schritte im Anpassungsdokument die folgenden Informationen, um dieses Skript anzuwenden:
>
> * Ersetzen Sie alle Beispielskriptaktions-URIs durch den URI für dieses Skript (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)).
> * Ersetzen Sie alle Beispiel-Parameter mit dem Azure-Speicherkontonamen und dem Schlüssel des Speicherkontos, das dem Cluster hinzugefügt werden soll. Bei Verwendung des Azure-Portals müssen diese Parameter durch eine Leerstelle getrennt werden.
> * Sie müssen dieses Skript nicht als __Persistent__ markieren, da es die Ambari-Konfiguration für den Cluster direkt aktualisiert.

## <a name="known-issues"></a>Bekannte Probleme

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Speicherkonten werden im Azure-Portal oder in Tools nicht angezeigt

Wenn Sie beim Anzeigen des HDInsight-Clusters im Azure-Portal den Eintrag __Speicherkonten__ unter __Eigenschaften__ auswählen, werden keine Speicherkonten angezeigt, die über diese Skriptaktion hinzugefügt wurden. Azure PowerShell und Azure CLI zeigen das zusätzliche Speicherkonto ebenfalls nicht an.

Die Speicherinformationen werden nicht angezeigt, da das Skript nur die core-site.xml-Konfiguration für den Cluster ändert. Diese Informationen werden nicht verwendet, wenn Sie die Clusterinformationen mit Azure-Verwaltungs-APIs abrufen.

Verwenden Sie die Ambari-REST-API, um Speicherkontoinformationen anzuzeigen, die dem Cluster mit diesem Skript hinzugefügt wurden. Verwenden Sie die folgenden Befehle, um diese Informationen für Ihren Cluster abzurufen:

```PowerShell
$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$storageAccountName.blob.core.windows.net"
```

> [!NOTE]
> Legen Sie `$clusterName` auf den Namen des HDInsight-Clusters fest. Legen Sie `$storageAccountName` auf den Namen des Speicherkontos fest. Geben Sie bei der entsprechenden Aufforderung das Anmeldekonto (Administrator) und für den Cluster das entsprechende Kennwort ein.

```Bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.$STORAGEACCOUNTNAME.blob.core.windows.net"] | select(. != null)'
```

> [!NOTE]
> Legen Sie `$PASSWORD` auf das Kennwort des Anmeldekontos (Administrator) für den Cluster fest. Legen Sie `$CLUSTERNAME` auf den Namen des HDInsight-Clusters fest. Legen Sie `$STORAGEACCOUNTNAME` auf den Namen des Speicherkontos fest.
>
> Dieses Beispiel verwendet [curl (http://curl.haxx.se/)](http://curl.haxx.se/)) und [jq (https://stedolan.github.io/jq/)](https://stedolan.github.io/jq/)) zum Abrufen und Analysieren von JSON-Daten.

Ersetzen Sie bei Verwendung dieses Befehls __CLUSTERNAME__ durch den Namen des HDInsight-Clusters. Ersetzen Sie __PASSWORD__ mit dem HTTP-Anmeldekennwort für den Cluster. Ersetzen Sie __STORAGEACCOUNT__ mit dem Namen des Speicherkontos, das mit der Skriptaktion hinzugefügt wurde. Die Ausgabe dieses Befehls entspricht in etwa dem folgenden Text:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Dieser Text ist ein Beispiel für einen verschlüsselten Schlüssel, der zum Zugriff auf das Speicherkonto verwendet wird.

### <a name="unable-to-access-storage-after-changing-key"></a>Zugriff auf den Speicher ist nach dem Ändern des Schlüssels nicht möglich

Wenn Sie den Schlüssel für ein Speicherkonto ändern, kann HDInsight nicht mehr auf das Speicherkonto zugreifen. HDInsight verwendet eine zwischengespeicherte Kopie des Schlüssels in der Datei „core-site.xml“ für den Cluster. Diese zwischengespeicherte Kopie muss mit dem neuen Schlüssel aktualisiert werden.

Das erneute Ausführen der Skriptaktion aktualisiert den Schlüssel __nicht__, da das Skript überprüft, ob bereits ein Eintrag für das Speicherkonto vorhanden ist. Wenn bereits ein Eintrag vorhanden ist, werden keine Änderungen vorgenommen.

Um dieses Problem zu umgehen, müssen Sie den vorhandenen Eintrag für das Speicherkonto entfernen. Führen Sie die folgenden Schritte aus, um den vorhandenen Eintrag zu entfernen:

1. Öffnen Sie in einem Webbrowser die Ambari-Webbenutzeroberfläche für Ihren HDInsight-Cluster. Der URI ist https://CLUSTERNAME.azurehdinsight.net. Ersetzen Sie __CLUSTERNAME__ durch den Namen Ihres Clusters.

    Geben Sie bei der entsprechenden Aufforderung den HTTP-Anmeldenamen und das Kennwort für den Cluster ein.

2. Wählen Sie aus der Liste der Dienste auf der linken Seite den Dienst __HDFS__ aus. Wählen Sie in der Mitte der Seite die Registerkarte __Configs__ aus.

3. Geben Sie im Feld __Filter...__ den Wert __fs.azure.account__ ein. Damit werden Einträge für alle zusätzlichen Speicherkonten zurückgegeben, die dem Cluster hinzugefügt wurden. Es gibt zwei Arten von Einträgen: __keyprovider__ und __key__. Beide enthalten den Namen des Speicherkontos als Teil des Schlüsselnamens.

    Im Folgenden sehen Sie Beispieleinträge für ein Speicherkonto namens __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Nachdem Sie die Schlüssel für das Speicherkonto identifiziert haben, das Sie entfernen müssen, verwenden Sie das rote "-"-Symbol rechts neben den Eintrag, um es zu löschen. Klicken Sie dann auf die Schaltfläche __Speichern__, um die Änderungen zu speichern.

5. Nachdem die Änderungen gespeichert wurden, fügen Sie das Speicherkonto und den neuen Schlüsselwert mit der Skriptaktion dem Cluster hinzu.

### <a name="poor-performance"></a>Schlechte Leistung

Wenn das Speicherkonto sich in einer anderen Region als der HDInsight-Cluster befindet, könnten Sie eine schlechte Leistung feststellen. Der Zugriff auf Daten in einer anderen Region ist mit Netzwerkdatenverkehr außerhalb des regionalen Azure-Rechenzentrums und über das öffentliche Internet verbunden, was zu Latenz führen kann.

> [!WARNING]
> Die Verwendung eines Speicherkontos in einer anderen Region als dem HDInsight-Cluster wird nicht unterstützt.

### <a name="additional-charges"></a>Zusätzliche Gebühren

Wenn das Speicherkonto sich in einer anderen Region als der HDInsight-Cluster befindet, fallen Ihnen möglicherweise zusätzliche Ausgangsgebühren in Ihrer Azure-Abrechnung auf. Eine Ausgangsgebühr wird fällig, wenn Daten ein regionales Rechenzentrum verlassen. Diese Gebühr fällt auch dann an, wenn das Ziel des Datenverkehrs ein anderes Azure-Rechenzentrum in einer anderen Region ist.

> [!WARNING]
> Die Verwendung eines Speicherkontos in einer anderen Region als dem HDInsight-Cluster wird nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, wie Sie einem vorhandenen HDInsight-Cluster zusätzliche Speicherkonten hinzufügen. Weitere Informationen zu Skriptaktionen finden Sie unter [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).
