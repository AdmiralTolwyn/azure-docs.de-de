---
title: Erstellen von Hadoop-Clustern mit PowerShell – Azure HDInsight | Microsoft-Dokumentation
description: Erfahren Sie, wie Hadoop-, HBase-, Storm- oder Spark-Cluster unter Linux für HDInsight mithilfe von Azure PowerShell erstellt werden.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4208deca-d64a-45e1-8948-2673d5d7678c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 57ae7c3cab6c0084bb43585f65d79ae6cc265356
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400451"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Erstellen von Linux-basierten Clustern in HDInsight mit Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Workloads in Microsoft Azure steuern und automatisieren können. Dieses Dokument enthält Informationen zum Erstellen eines Linux-basierten HDInsight-Clusters mit Azure PowerShell. Darüber hinaus ist ein Beispielskript enthalten.

> [!NOTE]
> Azure PowerShell ist nur auf Windows-Clients verfügbar. Wenn Sie einen Linux-, Unix- oder Mac OS X-Client verwenden, finden Sie im Artikel zum [Erstellen eines Linux-basierten HDInsight-Clusters mit der Azure-Befehlszeilenschnittstelle](hdinsight-hadoop-create-linux-clusters-azure-cli.md) Informationen zum Erstellen eines Clusters mit der Azure-CLI.

## <a name="prerequisites"></a>Voraussetzungen
Sie benötigen Folgendes, bevor Sie mit diesem Verfahren beginnen können:

* Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-azurerm-ps)

    > [!IMPORTANT]
    > Die Azure PowerShell-Unterstützung zum Verwalten von HDInsight-Ressourcen mithilfe von Azure Service Manager ist **veraltet** und wurde zum 1. Januar 2017 eingestellt. Die Schritte in diesem Dokument zeigen die neuen HDInsight-Cmdlets, die mit Azure Resource Manager genutzt werden können.
    >
    > Führen Sie zur Installation der neuesten Version von Azure PowerShell die unter [Installieren von Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) beschriebenen Schritte aus. Wenn Sie über Skripts verfügen, die für die Verwendung der neuen Cmdlets für Azure Resource Manager geändert werden müssen, finden Sie unter [Migrieren zu Azure Resource Manager-basierten Entwicklungstools für HDInsight-Cluster](hdinsight-hadoop-development-using-azure-resource-manager.md) weitere Informationen.

## <a name="create-cluster"></a>Cluster erstellen

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Für die Erstellung eines HDInsight-Clusters mithilfe von Azure PowerShell müssen die folgenden Prozeduren ausgeführt werden:

* Erstellen einer Azure-Ressourcengruppe
* Erstellen eines Azure-Speicherkontos
* Erstellen eines Azure-Blobcontainers
* Erstellen eines HDInsight-Clusters

Das folgende Skript veranschaulicht das Erstellen eines neuen Clusters:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Die Werte, die Sie für die Clusteranmeldung angeben, werden verwendet, um das Hadoop-Benutzerkonto für den Cluster zu erstellen. Verwenden Sie dieses Konto, um eine Verbindung mit Diensten herzustellen, die auf dem Cluster gehostet werden, z.B. Webbenutzeroberflächen oder REST-APIs.

Die Werte, die Sie für den SSH-Benutzer angeben, werden verwendet, um den SSH-Benutzer für den Cluster zu erstellen. Verwenden Sie dieses Konto zum Starten einer SSH-Remotesitzung mit dem Cluster und zum Ausführen von Aufträgen. Weitere Informationen finden Sie im Dokument [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Wenn Sie die Verwendung von mehr als 32 Workerknoten planen (entweder bei Erstellung des Clusters oder durch eine Skalierung des Clusters nach der Erstellung), müssen Sie auch eine Hauptknotengröße von mindestens 8 Kernen und 14 GB Arbeitsspeicher (RAM) angeben.
>
> Weitere Informationen zu Knotengrößen und den damit verbundenen Kosten finden Sie unter [HDInsight – Preise](https://azure.microsoft.com/pricing/details/hdinsight/).

Das Erstellen eines Clusters kann bis zu 20 Minuten dauern.

## <a name="create-cluster-configuration-object"></a>Erstellen eines Clusters: Konfigurationsobjekt

Sie können auch ein HDInsight-Konfigurationsobjekt mit dem Cmdlet `New-AzureRmHDInsightClusterConfig` erstellen. Anschließend können Sie dieses Konfigurationsobjekt ändern, um zusätzliche Konfigurationsoptionen für Ihren Cluster zu aktivieren. Verwenden Sie abschließend den Parameter `-Config` des Cmdlets `New-AzureRmHDInsightCluster`, um die Konfiguration zu verwenden.

Das folgende Skript erstellt ein Konfigurationsobjekt zum Konfigurieren eines Clustertyps R-Server in HDInsight. Die Konfiguration aktiviert einen Edge-Knoten, RStudio, sowie ein zusätzliches Speicherkonto.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-98)]

> [!WARNING]
> Die Verwendung eines Speicherkontos an einem anderen Ort als dem HDInsight-Cluster wird nicht unterstützt. Wenn Sie dieses Beispiel verwenden möchten, erstellen Sie das zusätzliche Speicherkonto am gleichen Speicherort wie den Server.

## <a name="customize-clusters"></a>Anpassen von Clustern

* Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Löschen des Clusters

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Problembehandlung

Falls beim Erstellen von HDInsight-Clustern Probleme auftreten, sehen Sie sich die [Voraussetzungen für die Zugriffssteuerung](hdinsight-administer-use-portal-linux.md#create-clusters) an.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie einen HDInsight-Cluster erfolgreich erstellt haben, nutzen Sie die folgenden Ressourcen, um zu erfahren, wie Sie mit Ihrem Cluster arbeiten.

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

### <a name="spark-clusters"></a>Spark-Cluster

* [Erstellen einer eigenständigen Anwendung mit Scala](spark/apache-spark-create-standalone-application.md)
* [Remoteausführung von Aufträgen in einem Spark-Cluster mithilfe von Livy](spark/apache-spark-livy-rest-interface.md)
* [Spark mit BI: Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](spark/apache-spark-use-bi-tools.md)
* [Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](spark/apache-spark-machine-learning-mllib-ipython.md)

