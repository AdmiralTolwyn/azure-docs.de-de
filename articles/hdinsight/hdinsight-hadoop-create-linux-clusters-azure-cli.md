---
title: Erstellen von Hadoop-Clustern mit der klassischen Azure CLI – Azure HDInsight
description: Erfahren Sie, wie Sie HDInsight-Cluster mit der plattformübergreifenden klassischen Azure CLI erstellen.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: jasonh
ms.openlocfilehash: 84b352fea0c5b9c98cd3b4e814e448cf8b706402
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992812"
---
# <a name="create-hdinsight-clusters-using-the-azure-classic-cli"></a>Erstellen von HDInsight-Clustern mit der klassischen Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Anhand der Schritte in diesem Dokument werden Sie durch die Erstellung eines HDInsight 3.5-Clusters mithilfe der klassischen Azure CLI geführt.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Die klassische Azure CLI**. Die Schritte in diesem Dokument wurden mit der neuesten Version der klassischen Azure CLI (0.10.14) getestet.

## <a name="log-in-to-your-azure-subscription"></a>Melden Sie sich bei Ihrem Azure-Abonnement an.

Führen Sie die Schritte aus, die unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli) dokumentiert sind, und stellen Sie über die **login**-Methode eine Verbindung mit Ihrem Abonnement her.

## <a name="create-a-cluster"></a>Erstellen eines Clusters

Die folgenden Schritte sollten an einer Befehlszeile (z.B. PowerShell oder Bash) ausgeführt werden.

1. Führen Sie den folgenden Befehl aus, um sich bei Ihrem Azure-Abonnement zu authentifizieren:

        azure login

    Sie werden aufgefordert, Ihren Namen und das Kennwort anzugeben. Wenn Sie über mehrere Azure-Abonnements verfügen, verwenden Sie `azure account set <subscriptionname>`, um das Abonnement festzulegen, das von den Befehlen der klassischen CLI genutzt werden soll.

2. Wechseln Sie mit dem folgenden Befehl in den Azure-Ressourcen-Manager-Modus:

        azure config mode arm

3. Erstellen Sie eine Ressourcengruppe. Diese Ressourcengruppe umfasst den HDInsight-Cluster und das zugeordnete Speicherkonto.

        azure group create groupname location

    * Ersetzen Sie `groupname` durch einen eindeutigen Gruppenamen.

    * Ersetzen Sie `location` durch die geografische Region, in der die Gruppe erstellt werden soll.

       Eine Liste der gültigen Orte können Sie mithilfe des Befehls `azure location list` erzeugen. Verwenden Sie anschließend einen der Orte aus der Spalte `Name`.

4. Erstellen Sie ein Speicherkonto. Dieses Speicherkonto wird als Standardspeicher für den HDInsight-Cluster verwendet.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Ersetzen Sie `groupname` durch den Namen der im vorherigen Schritt erstellten Gruppe.

    * Ersetzen Sie `location` durch den gleichen Ort, den Sie im vorherigen Schritt verwendet haben.

    * Ersetzen Sie `storagename` durch einen eindeutigen Namen für das Speicherkonto.

        > [!NOTE]
        > Verwenden Sie `azure storage account create -h`, um sich in der Hilfe zu diesem Befehl weitere Informationen zu den verwendeten Parametern anzeigen zu lassen.

5. Rufen Sie den Schlüssel ab, der für den Zugriff auf das Speicherkonto verwendet wird.

        azure storage account keys list -g groupname storagename

    * Ersetzen Sie `groupname` durch den Namen der Ressourcengruppe.
    * Ersetzen Sie `storagename` durch den Namen des Speicherkontos.

     Speichern Sie von den zurückgegebenen Daten den `key`-Wert für `key1`.

6. Erstellen Sie ein HDInsight-Cluster.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Ersetzen Sie `groupname` durch den Namen der Ressourcengruppe.

    * Ersetzen Sie `Hadoop` durch den zu erstellenden Clustertyp. Beispiel: `Hadoop`, `HBase`, `Kafka`, `Spark` oder `Storm`.

     > [!IMPORTANT]
     > HDInsight-Cluster gibt es in verschiedenen Typen, die der Workload oder Technologie entsprechen, für die der Cluster optimiert ist. Es ist keine unterstützte Methode zum Erstellen eines Clusters vorhanden, bei der mehrere Typen kombiniert werden, z.B. Storm und HBase in einem Cluster.

    * Ersetzen Sie `location` durch den gleichen Ort, den Sie in den vorherigen Schritten verwendet haben.

    * Ersetzen Sie `storagename` durch den Namen des Speicherkontos.

    * Ersetzen Sie `storagekey` durch den Schlüssel, den Sie im vorherigen Schritt abgerufen haben.

    * Verwenden Sie für den `--defaultStorageContainer` -Parameter den gleichen Namen wie für den Cluster.

    * Ersetzen Sie `admin` und `httppassword` durch den Namen und das Kennwort, die Sie für den Zugriff auf den Cluster über HTTPS verwenden möchten.

    * Ersetzen Sie `sshuser` und `sshuserpassword` durch den Benutzernamen und das Kennwort, die Sie für den Zugriff auf den Cluster per SSH verwenden möchten.

    > [!IMPORTANT]
    > In diesem Beispiel wird ein Cluster mit zwei Workerknoten erstellt. Sie können die Anzahl der Workerknoten auch nach der Erstellung ändern, indem Sie Skalierungsvorgänge durchführen. Wenn Sie die Verwendung von mehr als 32 Workerknoten planen, müssen Sie eine Hauptknotengröße von mindestens 8 Kernen und 14 GB Arbeitsspeicher (RAM) auswählen. Sie können die Hauptknotengröße während der Clustererstellung mit dem Parameter `--headNodeSize` festlegen.
    >
    > Weitere Informationen zu Knotengrößen und den damit verbundenen Kosten finden Sie unter [HDInsight – Preise](https://azure.microsoft.com/pricing/details/hdinsight/).

    Die Fertigstellung der Clustererstellung kann möglicherweise einige Minuten dauern. In der Regel dauert es etwa 15 Minuten.

## <a name="troubleshoot"></a>Problembehandlung

Falls beim Erstellen von HDInsight-Clustern Probleme auftreten, sehen Sie sich die [Voraussetzungen für die Zugriffssteuerung](hdinsight-administer-use-portal-linux.md#create-clusters) an.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie einen HDInsight-Cluster erfolgreich mithilfe der klassischen CLI erstellt haben, nutzen Sie die folgenden Informationen, um mehr über die Arbeit mit Ihrem Cluster zu erfahren:

### <a name="hadoop-clusters"></a>Hadoop-Cluster

* [Verwenden von Hive mit HDInsight](hadoop/hdinsight-use-hive.md)
* [Verwenden von Pig mit HDInsight](hadoop/hdinsight-use-pig.md)
* [Verwenden von MapReduce mit HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase-Cluster

* [Erste Schritte mit HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Entwickeln von Java-Anwendungen für HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm-Cluster

* [Entwickeln von Java-Topologien für Storm in HDInsight](storm/apache-storm-develop-java-topology.md)
* [Verwenden von Python-Komponenten in Storm in HDInsight](storm/apache-storm-develop-python-topology.md)
* [Bereitstellen und Überwachen von Topologien mit Storm in HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
