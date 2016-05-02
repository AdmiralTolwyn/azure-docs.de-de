<properties 
	pageTitle="Transformationsaktivitäten von Daten | Microsoft Azure" 
	description="Erfahren Sie, wie Sie die Azure Data Factory verwenden können, um Daten zu transformieren und zu analysieren." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/05/2016" 
	ms.author="spelluru"/>

# Transformation und Analyse mit Azure Data Factory

## Übersicht
Anhand von Transformationsaktivitäten werden in Azure Data Factory Ihre Rohdaten zu Vorhersagen und Erkenntnissen umgewandelt und verarbeitet. Sie werden in einer Compute-Umgebung wie Azure HDInsight-Cluster oder einem Azure Batch ausgeführt. Azure Data Factory unterstützt die folgenden Transformationsaktivitäten, die [Pipelines](data-factory-create-pipelines.md) entweder einzeln oder mit einer anderen Aktivität verkettet hinzugefügt werden können.


Transformationsaktivität | Compute-Umgebung 
:----------------------- | :--------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] [Pig](data-factory-pig-activity.md) | HDInsight [Hadoop] [MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop] [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop] [Machine Learning-Aktivitäten: Batchausführung und Ressourcenaktualisierung](data-factory-azure-ml-batch-execution-activity.md) | Azure-VM 
[Gespeicherte Prozedur](data-factory-stored-proc-activity.md) | Azure SQL, Azure SQL Data Warehouse oder SQL Server |
[Data Lake Analytics U-SQL](data-factory-usql-activity.md) | Azure Data Lake Analytics 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] oder Azure Batch
   
> [AZURE.NOTE] 
Sie können die MapReduce-Aktivität verwenden, um Spark-Programme in Ihrem HDInsight Spark-Cluster auszuführen. Weitere Informationen finden Sie unter [Invoke Spark programs from Azure Data Factory](data-factory-spark.md) (Aufrufen von Spark-Programmen aus Azure Data Factory). Sie können eine benutzerdefinierte Aktivität erstellen, um R-Skripts in Ihrem HDInsight-Cluster mit installiertem R auszuführen. Informationen hierzu finden Sie unter [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) (Ausführen von R-Skripts mithilfe von Azure Data Factory).
 

Sie müssen einen verknüpften Dienst für die Compute-Umgebung erstellen und dann den verknüpften Dienst verwenden, wenn Sie eine Transformationsaktivität definieren. Es gibt zwei Typen von Compute-Umgebungen, die von Data Factory unterstützt werden.

1. **Bei Bedarf**: In diesem Fall wird die Compute-Umgebung vollständig von Data Factory verwaltet. Der Data Factory-Dienst erstellt diese Umgebung automatisch, bevor ein Auftrag zur Verarbeitung von Daten übermittelt wird. Sobald der Auftrag abgeschlossen wurde, wird die Umgebung entfernt. Benutzer können differenzierte Einstellungen für die bedarfsgesteuerte Compute-Umgebung zur Auftragsausführung, Clusterverwaltung sowie für Bootstrappingaktionen konfigurieren und steuern. 
2. **Eigene verwenden**: In diesem Fall können Sie Ihre eigene Compute-Umgebung (z. B. HDInsight-Cluster) als verknüpften Dienst in Data Factory registrieren. Die Compute-Umgebung wird von Ihnen verwaltet und von Data Factory zum Ausführen von Aktivitäten verwendet. 

Unter dem Artikel [verknüpfte Compute-Dienste](data-factory-compute-linked-services.md) finden Sie Informationen zu verknüpften Compute-Diensten, die von Data Factory unterstützt werden.

<!---HONumber=AcomDC_0420_2016-->