<properties
pageTitle="So löschen Sie einen HDInsight-Cluster | Azure"
description="Informationen zu den verschiedenen Möglichkeiten, einen HDInsight-Cluster zu löschen."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/09/2016"
ms.author="larryfr"/>

#So löschen Sie einen HDInsight-Cluster

Die HDInsight-Clusterabrechnung beginnt mit der Erstellung eines Clusters und endet mit seiner Löschung. Die Gebühren werden anteilig pro Minute berechnet, daher sollten Sie Cluster immer löschen, wenn sie nicht mehr verwendet werden. In diesem Artikel erfahren Sie, wie Sie einen Cluster mithilfe des Azure-Portals, Azure PowerShell und der Azure-Befehlszeilenschnittstelle löschen.

> [AZURE.IMPORTANT] Das Löschen eines HDInsight-Clusters führt nicht zum Löschen des zugeordneten Azure-Speicherkontos bzw. der zugeordneten Azure-Speicherkonten. Dadurch können Sie alle vom Cluster gespeicherten Daten erhalten und wiederverwenden.

##Azure-Portal

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com)an, und wählen Sie Ihren HDInsight-Cluster aus. Wenn Ihr HDInsight-Cluster nicht mit dem Dashboard verknüpft ist, können Sie ihn mithilfe des Suchfelds (Lupensymbol) auf der rechten Seite der Navigationsleiste durch Eingabe des Namens suchen.

    ![Portalsuche](./media/hdinsight-delete-cluster/navbar.png)

2. Sobald das Blatt für den Cluster geöffnet ist, wählen Sie das __Löschen__-Symbol. Wenn Sie dazu aufgefordert werden, klicken Sie __Ja__, um den Cluster zu löschen.

    ![Löschen-Symbol](./media/hdinsight-delete-cluster/deletecluster.png)

##Azure PowerShell

Verwenden Sie an einer PowerShell-Eingabeaufforderung den folgenden Befehl zum Löschen des Clusters:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Ersetzen Sie __CLUSTERNAME__ durch den Namen Ihres HDInsight-Clusters.

##Azure-Befehlszeilenschnittstelle

Verwenden Sie an einer Eingabeaufforderung den folgenden Befehl zum Löschen des Clusters:

    azure hdinsight cluster delete CLUSTERNAME
    
Ersetzen Sie __CLUSTERNAME__ durch den Namen Ihres HDInsight-Clusters.

<!---HONumber=AcomDC_0914_2016-->