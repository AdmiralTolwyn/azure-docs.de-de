---
title: Einführung in R Server in Azure HDInsight | Microsoft Docs
description: Erfahren Sie, wie Sie R Server in HDInsight zum Erstellen von Anwendungen für Big Data-Analysen verwenden.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 19334e78124d1e388bc760659385388d89953644
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2018
---
# <a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>Einführung in R Server und Open-Source-R-Funktionen in HDInsight

Microsoft R Server (auch als Microsoft Machine Learning Server bekannt) ist beim Erstellen von HDInsight-Clustern in Azure als Bereitstellungsoption verfügbar. Der Clustertyp, der diese Option bietet, heißt **R Server**. Diese Funktion ermöglicht Datenanalysten, Statistikern und R-Programmierern bei Bedarf den Zugriff auf skalierbare, verteilte Analysemethoden in HDInsight.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

R Server in HDInsight bietet die neuesten Funktionen für die R-basierte Analyse praktisch beliebig großer Datasets, die entweder in Azure-Blobspeicher oder Data Lake Store geladen wurden. Da der R Server-Cluster auf Open-Source R basiert, stehen Ihnen bei der Erstellung R-basierter Anwendungen alle über 8000 Open-Source R-Pakete zur Verfügung. Die Routinen in ScaleR, dem in R Server enthaltenen Big Data-Analysepaket von Microsoft, stehen auch zur Verfügung.

Der Edgeknoten eines Clusters ist ein praktischer Ort für die Verbindungsherstellung mit dem Cluster und die Ausführung Ihrer R-Skripts. Mit einem Edgeknoten haben Sie die Möglichkeit, die parallelisierten verteilten Funktionen von ScaleR in allen Kernen der Edgeknotenserver auszuführen. Außerdem können Sie sie in allen Knoten des Clusters ausführen, indem Sie Hadoop MapReduce von ScaleR oder Spark-Rechenkontexte verwenden.

Die Modelle oder Vorhersagen, die sich aus der Analyse ergeben, können für die lokale Verwendung heruntergeladen werden. Sie können auch an anderer Stelle in Azure operationalisiert werden, insbesondere über den [Azure Machine Learning Studio](http://studio.azureml.net)-[Webdienst](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-r-server-on-hdinsight"></a>Erste Schritte mit R Server in HDInsight

Um in Azure HDInsight einen R Server-Cluster zu erstellen, wählen Sie beim Erstellen eines HDInsight-Clusters im Azure-Portal den Clustertyp **R Server** aus. Der Typ des R Server-Clusters bezieht R Server auf den Datenknoten des Clusters und auf einem Edgeknoten ein, der als Landezone für R Server-basierte Analysen dient. Eine Anleitung zum Erstellen eines Clusters finden Sie unter [Erste Schritte mit R Server in HDInsight](r-server-get-started.md).

## <a name="why-choose-r-server-in-hdinsight"></a>Was spricht für R Server in HDInsight?

R Server in HDInsight gibt Ihnen die Möglichkeit, den R Server in Azure zu verwenden. R Server umfasst:

+ **Optimale AI-Innovation von Microsoft und Open Source**

  Microsoft ist bestrebt, AI für jede Einzelperson und jede Organisation verfügbar zu machen. R Server beinhaltet einen umfassenden Satz hochgradig skalierbarer, verteilter Algorithmen, wie etwa [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) und [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package), die auf Datenvolumen angewendet werden können, die die Größe des Arbeitsspeichers übersteigen, und sich in verteilter Form auf einer Vielzahl von Plattformen ausführen lassen. Erfahren Sie mehr über die Sammlung der angepassten [R-Pakete](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) und [Python-Pakete](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) von Microsoft, die im Lieferumfang des Produkts enthalten sind.
  
  R Server verbindet diese Microsoft-Innovationen mit denen der Open-Source-Community (R-, Python- und AI-Toolkits) auf einer einzelnen Plattform auf Unternehmensniveau. Jedes Open-Source-R- oder -Python-Machine Learning-Paket funktioniert parallel mit jeder proprietären Innovation von Microsoft. 

+ **Einfache, sichere und hochgradig skalierbare Operationalisierung und Verwaltung**

  Unternehmen, die auf traditionelle Operationalisierungsparadigmen und -umgebungen bauen, müssen letztlich viel Zeit und Mühe in diesen Bereich investieren. Zu den häufig mit überhöhten Kosten und Verzögerungen verbundenen Problemen gehören: die Übersetzungszeit für Modelle, Iterationen, um sie gültig und aktuell zu halten, behördliche Genehmigungen, Verwaltung von Berechtigungen durch Operationalisierung.

  R Server bietet die beste [Operationalisierung](https://docs.microsoft.com/machine-learning-server/what-is-operationalization) seiner Klasse: Ab der Fertigstellung eines Machine Learning-Modells braucht es nur noch ein paar Klicks, um Webdienst-APIs zu erstellen. Diese [Webdienste](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) werden lokal auf einem Serverraster oder in der Cloud gehostet und können mit Branchenanwendungen integriert werden. Durch die Möglichkeit der Bereitstellung in einem elastischen Raster können Sie nahtlos mit den Anforderungen Ihres Unternehmens skalieren, sowohl für Batch- als auch für Echtzeitbewertung. Anweisungen finden Sie unter [Operationalize R Server on HDInsight](r-server-operationalize.md) (Operationalisieren von R Server in HDInsight).

+ **Tiefgreifendes Engagement im Ökosystem für den Kundenerfolg bei optimalen Gesamtbetriebskosten**

  Menschen, die sich auf den Weg machen, ihre Anwendungen mit Intelligenz zu versehen oder einfach nur die neue Welt von KI und Machine Learning kennenlernen wollen, brauchen die richtigen Ressourcen für den Einstieg. Über diese Dokumentation hinaus stellt Microsoft verschiedene Lernressourcen zur Verfügung und hat eine Reihe von Schulungspartnern verpflichtet, die Ihnen helfen, schnell fit und produktiv zu werden.


## <a name="key-features-of-r-server-on-hdinsight"></a>Wichtige Features von R Server in HDInsight

Die folgenden Funktionen sind in R-Server in HDInsight enthalten.

| Featurekategorie | BESCHREIBUNG |
|------------------|-------------|
| R-fähig | [R-Pakete](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) für in R erstellte Lösungen mit einer Open-Source-Distribution von R und einer Laufzeitinfrastruktur für die Skriptausführung. |
| Python-fähig | [Python-Module](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) für in Python erstellte Lösungen mit einer Open-Source-Distribution von Python und einer Laufzeitinfrastruktur für die Skriptausführung.  
| [Vortrainierte Modelle](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Für visuelle Analyse und Textempfindungsanalyse, bereit zur Bewertung der Daten, die Sie zur Verfügung stellen. |
| [Bereitstellen und Nutzen](r-server-operationalize.md) | Operationalisieren Sie Ihren Server, und stellen Sie Lösungen als Webdienst bereit. |
| [Remoteausführung](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-server-or-client) | Starten Sie Remotesitzungen auf R Server in Ihrem Netzwerk von Ihrer Clientarbeitsstation aus. |


## <a name="data-storage-options-for-r-server-on-hdinsight"></a>Datenspeicheroptionen für R Server in HDInsight

Standardspeicher für das HDFS-Dateisystem von HDInsight-Clustern können entweder einem Azure Storage-Konto oder einem Azure Data Lake Store zugeordnet werden. Durch diese Zuordnung wird sichergestellt, dass alle Daten, die während einer Analyse in den Clusterspeicher hochgeladen werden, persistent gemacht werden und selbst nach der Löschung des Clusters noch verfügbar sind. Es gibt verschiedene Tools für die Datenübertragung in die von Ihnen ausgewählte Speicheroption, einschließlich der portalbasierten Hochladefunktion für das Speicherkonto und des [AzCopy](../../storage/common/storage-use-azcopy.md)-Hilfsprogramms.

Unabhängig von der verwendeten primären Speicheroption haben Sie die Möglichkeit, während des Clusterbereitstellungsvorgangs Zugriff auf zusätzliche Blobspeicher und Data Lake-Speicher hinzuzufügen. Informationen zum Hinzufügen von Zugriff auf zusätzliche Konten finden Sie unter [Erste Schritte mit R Server in HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started). Weitere Informationen zur Verwendung von mehreren Speicherkonten finden Sie im ergänzenden Artikel [Azure Storage-Optionen für R Server in HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage).

Außerdem können Sie [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) als Speicheroption für den Edgeknoten wählen. Mit Azure Files können Sie eine Dateifreigabe, die in Azure Storage erstellt wurde, für das Linux-Dateisystem bereitstellen. Weitere Informationen zu diesen Datenspeicheroptionen für R Server in HDInsight-Clustern finden Sie unter [Azure Storage-Optionen für R Server auf HDInsight](r-server-storage.md).

## <a name="access-machine-learning-server-on-the-cluster"></a>Zugriff auf Machine Learning-Server im Cluster

Sie können über einen Browser eine Verbindung mit Microsoft Machine Learning Server auf dem Edgeknoten herstellen. R Server wird standardmäßig bei der Clustererstellung installiert. Weitere Informationen finden Sie unter [Erste Schritte mit R Server in HDInsight](r-server-get-started.md). Sie können auch über die Befehlszeile eine Verbindung mit dem ML-Server herstellen, indem Sie mithilfe von SSH/PuTTY auf die R-Konsole zugreifen. 

## <a name="develop-and-run-r-scripts"></a>Entwickeln und Ausführen von R-Skripts

Für von Ihnen geschriebene und ausgeführte R-Skripts können zusätzlich zu den parallelisierten und verteilten Routinen in der ScaleR-Bibliothek mehr als 8000 Open-Source-R-Pakete verwendet werden. Im Allgemeinen wird ein Skript, das mit R Server auf dem Edgeknoten ausgeführt wird, im R-Interpreter auf diesem Knoten ausgeführt. Ausgenommen hiervon sind die Schritte, die eine ScaleR-Funktion mit einem Rechenkontext aufrufen müssen, der auf Hadoop MapReduce (RxHadoopMR) oder Spark (RxSpark) festgelegt ist. In diesem Fall wird die Funktion über diejenigen Daten(aufgabe)knoten des Clusters verteilt ausgeführt, die den Daten zugeordnet sind, auf die verwiesen wird. Weitere Informationen zu den verschiedenen Optionen für Berechnungskontexte finden Sie unter [Rechenkontextoptionen für R Server in HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operationalisieren eines Modells

Nach Abschluss der Datenmodellierung können Sie das Modell operationalisieren, um entweder in Azure oder lokal Vorhersagen über neue Daten treffen zu können. Dieser Prozess wird als Bewertung bezeichnet. Eine Bewertung kann in HDInsight, Azure Machine Learning oder lokal ausgeführt werden.

### <a name="score-in-hdinsight"></a>Bewertung in HDInsight
Schreiben Sie für die Bewertung in HDInsight eine R-Funktion, mit der Ihr Modell aufgerufen wird, um Vorhersagen für eine neue Datendatei zu treffen, die Sie in Ihr Speicherkonto geladen haben. Speichern Sie die Vorhersagen dann wieder im Speicherkonto. Sie können diese Routine bedarfsgesteuert auf dem Edgeknoten Ihres Clusters oder mithilfe eines geplanten Auftrags ausführen.

### <a name="score-in-azure-machine-learning-aml"></a>Bewertung in Azure Machine Learning (AML)
Verwenden Sie für die Bewertung mit Azure Machine Learning das Open-Source-Azure Machine Learning-R-Paket namens [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html), um Ihr Modell als Azure-Webdienst zu veröffentlichen. Der Einfachheit halber ist dieses Paket auf dem Edgeknoten vorinstalliert. Verwenden Sie als Nächstes die Funktionen in Azure Machine Learning zum Erstellen einer Benutzeroberfläche für den Webdienst, und rufen Sie den Webdienst dann nach Bedarf für die Bewertung auf.

Wenn Sie diese Option auswählen, müssen Sie zur Verwendung mit dem Webdienst alle ScaleR-Modellobjekte in entsprechende Open-Source-Modellobjekte umwandeln. Verwenden Sie für diese Umwandlung ScaleR-Koersionsfunktionen wie `as.randomForest()` für ensemblebasierte Modelle.

### <a name="score-on-premises"></a>Lokale Bewertung
Um nach der Erstellung Ihres Modells eine lokale Bewertung durchzuführen, können Sie das Modell in R serialisieren, herunterladen, deserialisieren und anschließend für die Bewertung neuer Daten verwenden. Sie können die Bewertung für neue Daten durchführen, indem Sie den weiter oben unter [Bewertung in HDInsight](#scoring-in-hdinsight) beschriebenen Ansatz verwenden oder [DeployR](https://deployr.revolutionanalytics.com/) nutzen.

## <a name="maintain-the-cluster"></a>Verwalten des Clusters

### <a name="install-and-maintain-r-packages"></a>Installieren und Verwalten von R-Paketen

Die meisten R-Pakete, die Sie verwenden, sind auf dem Edgeknoten erforderlich, da ein Großteil der Schritte Ihrer R-Skripts dort ausgeführt wird. Um auf dem Edgeknoten zusätzliche R-Pakete zu installieren, verwenden Sie in R die Methode `install.packages()` .

Wenn Sie nur Routinen aus der ScaleR-Bibliothek auf den Cluster anwenden, müssen Sie in der Regel keine zusätzlichen R-Pakete auf den Datenknoten installieren. Sie benötigen aber unter Umständen zusätzliche Pakete, um die Verwendung der **rxExec**- oder **RxDataStep**-Ausführung auf den Datenknoten zu unterstützen.

In solchen Fällen können die zusätzlichen Pakete nach dem Erstellen des Clusters mithilfe einer Skriptaktion installiert werden. Weitere Informationen finden Sie unter [Verwalten von R Server in HDInsight-Clustern](r-server-hdinsight-manage.md).

### <a name="change-hadoop-mapreduce-memory-settings"></a>Ändern der Hadoop MapReduce-Speichereinstellungen

Ein Cluster kann dahingehend geändert werden, dass er die Speichermenge, die R Server bei der Ausführung eines MapReduce-Auftrags zur Verfügung steht, ändern kann. Verwenden Sie zum Ändern eines Clusters die Apache Ambari-Benutzeroberfläche, die über das Blatt des Clusters im Azure-Portal verfügbar ist. Informationen zum Zugriff auf die Ambari-Benutzeroberfläche für Ihren Cluster finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Ambari-Webbenutzeroberfläche](../hdinsight-hadoop-manage-ambari.md).

Sie können den für R Server zur Verfügung stehenden Speicher ändern, indem Sie Hadoop-Switches beim Aufruf von **RxHadoopMR** wie folgt verwenden:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Skalieren des Clusters

Ein vorhandener R Server-Cluster in HDInsight kann über das Portal zentral hoch- oder herunterskaliert werden. Durch das vertikale Skalieren erzielen Sie die zusätzliche Kapazität, die Sie für umfangreichere Verarbeitungsaufgaben benötigen, oder Sie können einen im Leerlauf befindlichen Cluster wieder herunterskalieren. Eine Anleitung zum Skalieren eines Clusters finden Sie unter [Verwalten von HDInsight-Clustern](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Warten des Systems

Die Wartung zur Anwendung von Betriebssystempatches und anderer Updates wird auf den zugrundeliegenden Linux-VMs in einem HDInsight-Cluster außerhalb der Arbeitszeiten durchgeführt. Normalerweise wird die Wartung jeden Montag und Donnerstag um 3:30 Uhr (Ortszeit der VM) durchgeführt. Die Updates werden so geplant, dass jeweils maximal ein Viertel des Clusters betroffen ist.  

Da die Hauptknoten redundant sind und nicht alle Datenknoten betroffen sind, werden alle Aufträge, die während dieses Zeitraums ausgeführt werden, unter Umständen verlangsamt. Sie sollten aber trotzdem vollständig ausgeführt werden. Jegliche benutzerdefinierte Software oder lokalen Daten, über die Sie ggf. verfügen, werden über diese Wartungsereignisse hinweg erhalten – sofern kein schwerwiegender Fehler auftritt, der eine Neuerstellung des Clusters erfordert.

## <a name="ide-options-for-r-server-on-an-hdinsight-cluster"></a>IDE-Optionen für R Server in einem HDInsight-Cluster

Der Linux-Edgeknoten auf einem HDInsight-Cluster stellt die Landezone für R-basierte Analysen dar. Neuere Versionen von HDInsight bieten eine Standardinstallation von RStudio Server als browserbasierte IDE auf dem Edgeknoten. Die Verwendung von RStudio Server als IDE für die Entwicklung und Ausführung von R-Skripts kann erheblich produktiver sein als die ausschließliche Verwendung der R-Konsole.

Darüber hinaus können Sie eine Desktop-IDE installieren und sie verwenden, um mithilfe eines Remote-MapReduce- oder Spark-Rechenkontexts auf den Cluster zuzugreifen. Zu den Optionen gehören [R Tools für Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS) von Microsoft, RStudio und das auf Eclipse basierende [StatET](http://www.walware.de/goto/statet) von Walware.

Schließlich können Sie nach dem Herstellen einer Verbindung über SSH oder PuTTY durch Eingabe von **R** an der Linux-Eingabeaufforderung auf die R-Konsole auf dem Edgeknoten zugreifen. Wenn Sie die Konsolenschnittstelle verwenden, ist es sinnvoll, einen Texteditor für die R-Skriptentwicklung in einem anderen Fenster auszuführen und Abschnitte Ihres Skripts nach Bedarf auszuschneiden und in die R-Konsole einzufügen.

## <a name="pricing"></a>Preise

Die Preise für einen HDInsight-Cluster mit R Server sind ähnlich strukturiert wie die Preise für die HDInsight-Standardcluster. Sie basieren auf der Größe der zugrundeliegenden VMs des Clusters sowie der Daten und Edgeknoten. Hinzu kommt eine stündliche Zusatzgebühr pro Kern. Weitere Informationen zu den Preisen von HDInsight finden Sie unter [HDInsight – Preise](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von R Server in HDInsight-Clustern finden Sie unter den folgenden Themen:

* [Erste Schritte mit R Server-Clustern in HDInsight (Vorschau)](r-server-get-started.md)
* [Rechenkontextoptionen für R Server in HDInsight](r-server-compute-contexts.md)
* [Azure Storage-Lösungen für R Server in HDInsight](r-server-storage.md)
