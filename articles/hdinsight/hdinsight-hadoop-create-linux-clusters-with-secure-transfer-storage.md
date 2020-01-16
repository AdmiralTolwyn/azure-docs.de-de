---
title: Apache Hadoop und Speicher mit sicherer Übertragung – Azure HDInsight
description: Hier erfahren Sie, wie Sie HDInsight-Cluster mit Azure-Speicherkonten mit sicherer Übertragung erstellen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: bcb0e9551f4415b2aac9eb2d641c91df9f692437
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979117"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Erstellen von Apache Hadoop-Clustern mit Speicherkonten mit sicherer Übertragung in Azure HDInsight

Das Feature [Sichere Übertragung erforderlich](../storage/common/storage-require-secure-transfer.md) erhöht die Sicherheit Ihres Azure Storage-Kontos, indem alle Anforderungen für Ihr Konto über eine sichere Verbindung übertragen werden müssen. Dieses Feature und das WASB-Schema werden erst ab HDInsight-Clusterversion 3.6 unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

* Azure-Abonnement: Um ein kostenloses Testkonto für die Dauer eines Monats zu erstellen, navigieren Sie zu [azure.microsoft.com/free](https://azure.microsoft.com/free).
* Ein Azure Storage-Konto mit aktivierter sicherer Übertragung. Entsprechende Anleitungen finden Sie unter [Erstellen Sie ein Speicherkonto.](../storage/common/storage-account-create.md) sowie unter [Vorschreiben einer sicheren Übertragung](../storage/common/storage-require-secure-transfer.md). Die Aktivierung der sicheren Speicherübertragung nach dem Erstellen eines Clusters erfordert zusätzliche Schritte, die in diesem Artikel nicht behandelt werden.
* Ein Blobcontainer im Speicherkonto.

## <a name="create-cluster"></a>Cluster erstellen

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

In diesem Abschnitt erstellen Sie einen Hadoop-Cluster in HDInsight mit einer [Azure Resource Manager-Vorlage](../azure-resource-manager/templates/deploy-powershell.md). Die Vorlage befindet sich auf [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/). Für diesen Artikel ist keine Erfahrung mit Resource Manager-Vorlagen erforderlich. Andere Methoden zur Erstellung von Clustern und Informationen zu den in diesem Artikel verwendeten Eigenschaften finden Sie unter [Erstellen von HDInsight-Clustern](hdinsight-hadoop-provision-linux-clusters.md).

1. Klicken Sie auf die folgende Abbildung, um sich bei Azure anzumelden, und öffnen Sie die Resource Manager-Vorlage im Azure-Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Erstellen Sie den Cluster wie folgt:

    * Geben Sie die HDInsight-Version 3.6 an. Es wird mindestens Version 3.6 benötigt.
    * Geben Sie ein Speicherkonto mit sicherer Übertragung an.
    * Verwenden Sie einen kurzen Namen für das Speicherkonto.
    * Speicherkonto und Blobcontainer müssen vorab erstellt werden.

      Eine entsprechende Anleitung finden Sie unter [Cluster erstellen](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

Wenn Sie mithilfe der Skriptaktion Ihre eigenen Konfigurationsdateien bereitstellen, müssen Sie WASBS in den folgenden Einstellungen verwenden:

* fs.defaultFS (core-site)
* spark.eventLog.dir
* spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>Hinzufügen weiterer Speicherkonten

Weitere Speicherkonten mit sicherer Übertragung können auf unterschiedliche Weise hinzugefügt werden:

* Ändern Sie die Azure Resource Manager-Vorlage im letzten Abschnitt.
* Erstellen Sie über das [Azure-Portal](https://portal.azure.com) einen Cluster, und geben Sie ein verknüpftes Speicherkonto an.
* Verwenden Sie die Skriptaktion, um einem vorhandenen HDInsight-Cluster weitere Speicherkonten mit sicherer Übertragung hinzuzufügen. Weitere Informationen finden Sie unter [Hinzufügen zusätzlicher Speicherkonten zu HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie einen HDInsight-Cluster erstellen und die sichere Übertragung an die Speicherkonten aktivieren.

Weitere Informationen zur Datenanalyse mit HDInsight finden Sie in den folgenden Artikeln:

* Weitere Informationen zum Verwenden von [Apache Hive](https://hive.apache.org/) mit HDInsight (etwa zum Ausführen von Hive-Abfragen in Visual Studio) finden Sie unter [Verwenden von Apache Hive mit HDInsight](hadoop/hdinsight-use-hive.md).
* Informationen zu [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) (einer Möglichkeit zum Schreiben von Programmen, die Daten in Hadoop verarbeiten) finden Sie unter [Verwenden von Apache Hadoop MapReduce mit HDInsight](hadoop/hdinsight-use-mapreduce.md).
* Informationen zur Verwendung der HDInsight-Tools für Visual Studio zum Analysieren von Daten in HDInsight finden Sie unter [Erste Schritte bei der Verwendung von Apache Hadoop-Tools für Visual Studio für HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Weitere Informationen dazu, wie HDInsight Daten speichert und wie Sie Daten an HDInsight übertragen, finden Sie in den folgenden Artikeln:

* Informationen zur Verwendung von Azure Storage durch HDInsight finden Sie unter [Verwenden von Azure Storage mit HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Informationen zum Hochladen von Daten in HDInsight finden Sie im Artikel zum [Hochladen von Daten für Hadoop-Aufträge in HDInsight](hdinsight-upload-data.md).

Weitere Informationen zum Erstellen und Verwalten von HDInsight-Clustern finden Sie in den folgenden Artikeln:

* Informationen zum Verwalten eines Linux-basierten HDInsight-Clusters finden Sie unter [Verwalten von HDInsight-Clustern mit Apache Ambari](hdinsight-hadoop-manage-ambari.md).
* Informationen zu den Optionen, die Sie beim Erstellen eines HDInsight-Clusters auswählen können, finden Sie unter [Erstellen von HDInsight unter Linux mit benutzerdefinierten Optionen](hdinsight-hadoop-provision-linux-clusters.md).
* Wenn Sie mit Linux und Apache Hadoop vertraut sind und ausführliche Informationen zu Hadoop in HDInsight erhalten möchten, finden Sie diese unter [Arbeiten mit HDInsight unter Linux](hdinsight-hadoop-linux-information.md). Dieser Artikel enthält u.a. folgende Informationen:

  * URLs für im Cluster gehostete Dienste, z. B. [Apache Ambari](https://ambari.apache.org/) und [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)
  * Speicherort von [Apache Hadoop](https://hadoop.apache.org/)-Dateien und -Beispielen im lokalen Dateisystem
  * Verwendung von Azure Storage (WASB) anstelle von [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) als Standarddatenspeicher
