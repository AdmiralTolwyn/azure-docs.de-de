<properties 
	pageTitle="Verwenden des Ressourcen-Managers zum Zuteilen von Ressourcen an den Apache Spark-Cluster in HDInsight| Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Ressourcen-Manager für Spark-Cluster in HDInsight zum Verbessern der Leistung verwenden." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/22/2015" 
	ms.author="nitinme"/>


# Verwalten von Ressourcen für den Apache Spark-Cluster in HDInsight unter Windows (Vorschau)

> [AZURE.NOTE] HDInsight bietet jetzt Spark-Cluster unter Linux. Informationen zum Verwalten von Ressourcen für einen Spark-Cluster in HDInsight unter Linux finden Sie unter [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight (Linux)](hdinsight-apache-spark-resource-manager.md).

Der Ressourcen-Manager ist eine Komponente des Spark-Clusterdashboards, das zum Verwalten von Ressourcen wie z. B. Kernen und Arbeitsspeicher dient, die von den einzelnen Anwendungen im Cluster genutzt werden.

## <a name="launchrm"></a>So starten Sie den Ressourcen-Manager

1. Klicken Sie im [Azure-Portal](https://ms.portal.azure.com/) im Startmenü auf die Kachel für Ihren Spark-Cluster (sofern Sie die Kachel ans Startmenü angeheftet haben). Sie können auch unter **Alle durchsuchen** > **HDInsight-Cluster** zu Ihrem Cluster navigieren. 
 
2. Klicken Sie auf dem Blatt für den Spark-Cluster auf **Dashboard**. Geben Sie bei Aufforderung die Anmeldeinformationen für den Spark-Cluster ein.

	![Ressourcen-Manager starten](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.cluster.launch.dashboard.png "Ressourcen-Manager starten")

##<a name="scenariosrm"></a>So beheben Sie diese Probleme mit dem Ressourcen-Manager

Hier finden Sie einige gängige Szenarien, die bei Ihrem Spark Cluster ggf. auftreten, und Anweisungen dazu, wie sie diese mit dem Ressourcen-Manager behandeln.

### Mein Spark-Cluster in HDInsight ist langsam

Apache Spark-Cluster in HDInsight ist mehrinstanzenfähig, sodass Ressourcen über mehrere Komponenten (Notebooks, Auftragsserver usw.) aufgeteilt sind. Dadurch können Sie alle Spark-Komponenten gleichzeitig verwenden, ohne sich darüber Gedanken machen zu müssen, ob für eine Komponente die zum Ausführen erforderlichen Ressourcen nicht vorhanden sind. Die einzelnen Komponente sind jedoch langsamer, da Ressourcen fragmentiert sind. Dies kann Ihren Anforderungen entsprechend angepasst werden.


### Ich verwende das Jupyter Notebook nur mit dem Spark-Cluster. Wie kann ich ihm alle Ressourcen zuweisen?

1. Klicken Sie im **Spark-Dashboard** auf die Registerkarte **Spark-Benutzeroberfläche**, um die maximale Anzahl von Kernen und den maximalen Arbeitsspeicher zu ermitteln, die Sie den Anwendungen zuteilen können.

	![Ressourcenzuteilung](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.resource.png "Suchen nach Ressourcen, die einem Spark-Cluster zugeteilt sind")

	Laut der Bildschirmaufnahme oben können Sie höchstens 7 Kerne (insgesamt 8 Kerne, wovon einer in Gebrauch ist) und höchstens 9 GB Arbeitsspeicher (insgesamt 12 GB, wovon 2 GB vom System und 1 GB von anderen Anwendungen genutzt werden) zuteilen.

	Sie sollten auch alle ausgeführten Anwendungen berücksichtigen. Die ausgeführten Anwendungen können Sie auf der Registerkarte **Spark-Benutzeroberfläche** anzeigen.

	![Ausführen von Anwendungen](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.running.apps.png "Anwendungen, die auf dem Cluster ausgeführt werden")

	
2. Klicken Sie im HDInsight Spark-Dashboard auf die Registerkarte **Ressourcen-Manager**, und geben Sie die Werte für **Standardanzahl von Anwendungskernen** und **Standard-Executor-Arbeitsspeicher pro Workerknoten** an. Legen Sie andere Eigenschaften auf 0 fest.

	![Ressourcenzuteilung](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.allocate.resources.png "Zuteilen von Ressourcen zu Anwendungen")

### Ich verwende keine BI-Tools mit dem Spark-Cluster. Wie kann ich Ressourcen wieder zurücknehmen? 

Geben Sie als Anzahl von Thrift-Serverkernen und Thrift-Executor-Serverspeicher den Wert 0 an. Ohne Kern und zugeteiltem Arbeitsspeicher wechselt der Thrift-Server in den Zustand **WARTEN**.

![Ressourcenzuteilung](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.no.thrift.png "Keine Ressourcen an den Thrift-Server")

##<a name="seealso"></a>Weitere Informationen

* [Übersicht: Apache Spark in Azure HDInsight](hdinsight-apache-spark-overview-v1.md)
* [Erstellen eines Spark-Clusters in HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](hdinsight-apache-spark-use-bi-tools-v1.md)
* [Verwenden von Spark in HDInsight zum Erstellen von Machine Learning-Anwendungen](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [Verwenden von Spark in HDInsight zum Erstellen von Echtzeit-Streaminganwendungen](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0420_2016-->