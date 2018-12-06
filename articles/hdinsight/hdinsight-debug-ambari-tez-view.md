---
title: Verwenden der Apache Ambari-Tez-Ansicht mit HDInsight – Azure
description: Erfahren Sie, wie Sie die Apache Ambari-Tez-Ansicht zum Debuggen von Tez-Aufträgen in HDInsight verwenden.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 0d2f55538517881ce6cc237885f3bcadfa084520
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52494972"
---
# <a name="use-apache-ambari-views-to-debug-apache-tez-jobs-on-hdinsight"></a>Debuggen von Apache Tez-Aufträgen in HDInsight mithilfe von Apache Ambari-Ansichten

Die [Apache Ambari](https://ambari.apache.org/)-Webbenutzeroberfläche für HDInsight enthält eine [Apache Tez](https://tez.apache.org/)-Ansicht, mit der Sie Aufträge, die Tez nutzen, besser verstehen und debuggen können. Die Tez-Ansicht ermöglicht Ihnen das Visualisieren des Auftrags als Graphen verbundener Elemente, einen Drilldown in die einzelnen Elemente und das Abrufen von Statistiken und Protokollinformationen.

> [!IMPORTANT]
> Die Schritte in diesem Dokument erfordern einen HDInsight-Cluster mit Linux. Linux ist das einzige Betriebssystem, das unter HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [HDInsight-Komponentenversionen](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Linux-basierter HDInsight-Cluster. Anweisungen zum Erstellen eines Clusters finden Sie unter [Erste Schritte mit Linux-basierten HDInsight-Clustern](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Ein zeitgemäßer Webbrowser, der HTML5 unterstützt.

## <a name="understanding-apache-tez"></a>Grundlegendes zu Apache Tez

Tez ist ein erweiterbares Framework für die Datenverarbeitung in Apache Hadoop, das eine schnellere Verarbeitung als übliche MapReduce-Verfahren bietet. Bei Linux-basierten Clustern ist Tez die Standard-Engine für Hive.

Tez erstellt einen gerichteten azyklischen Graphen (Directed Acyclic Graph, DAG), der die Reihenfolge der Aktionen beschreibt, die für Aufgaben erforderlich sind. Einzelne Aktionen werden auch Scheitelpunkte genannt und führen einen Teil des gesamten Auftrags aus. Die tatsächliche Ausführung des Auftrags, die von einem Scheitelpunkt beschrieben wird, heißt Aufgabe und kann auf mehrere Knoten im Cluster verteilt werden.

### <a name="understanding-the-tez-view"></a>Grundlegendes zur Tez-Ansicht

Die Tez-Ansicht stellt Verlaufsdaten und Informationen zu laufenden Prozessen bereit. Diese Informationen verdeutlichen, wie ein Auftrag auf Clustern verteilt ist. Außerdem werden Indikatoren, die von Aufgaben und Scheitelpunkten verwendet werden, sowie Fehlerinformationen, die im Zusammenhang mit dem Auftrag stehen, angezeigt. Diese Ansicht kann in den folgenden Szenarien nützliche Informationen bieten:

* Überwachen lang andauernder Prozesse und Anzeigen des Status von Map/Reduce-Aufgaben.
* Analysieren von Verlaufsdaten erfolgreicher oder fehlerhafter Prozesse, um zu erfahren, wie die Verarbeitung verbessert werden kann oder warum sie misslungen ist.

## <a name="generate-a-dag"></a>Generieren eines gerichteten azyklischen Graphen (DAG)

Die Tez-Ansicht enthält nur Daten, wenn ein Auftrag, der die Tez-Engine verwendet, derzeit ausgeführt wird oder zuvor ausgeführt wurde. Einfache Hive-Abfragen können ohne Tez aufgelöst werden. Komplexere Abfragen mit Filterung, Gruppierung, Sortierung, Joins usw. verwenden die Tez-Engine.

Gehen Sie folgendermaßen vor, um eine Hive-Abfrage mit Tez auszuführen:

1. Öffnen Sie einen Webbrowser https://CLUSTERNAME.azurehdinsight.net, wobei **CLUSTERNAME** der Name des HDInsight-Clusters ist.

2. Wählen im Menü oben auf der Seite das Symbol **Ansichten** aus. Das Symbol sieht aus wie eine Reihe von Quadraten. Wählen Sie in der eingeblendeten Dropdownliste **Hive-Ansicht** aus.

    ![Auswählen der Hive-Ansicht](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Fügen Sie in der geladenen Hive-Ansicht die folgende Abfrage in den Abfrage-Editor ein, und klicken Sie dann auf **Ausführen**.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    Sobald der Auftrag abgeschlossen ist, sollte die Ausgabe im Abschnitt **Abfrageprozessergebnisse** angezeigt werden. Die Ergebnisse sollten in etwa wie der folgende Text aussehen:

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. Wählen Sie die Registerkarte **Protokoll** aus. Die angezeigten Informationen sehen in etwa folgendermaßen aus:

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Speichern Sie den Wert von **App-ID**, da dieser im nächsten Abschnitt verwendet wird.

## <a name="use-the-tez-view"></a>Verwenden der Tez-Ansicht

1. Wählen im Menü oben auf der Seite das Symbol **Ansichten** aus. Wählen Sie in der eingeblendeten Dropdownliste **Tez-Ansicht** aus.

    ![Auswählen der Tez-Ansicht](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Nach dem Laden der Tez-Ansicht sehen Sie eine Liste der Hive-Abfragen, die derzeit im Cluster ausgeführt werden oder zuvor ausgeführt wurden.

    ![Alle DAGs](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. Wenn nur ein Eintrag vorhanden ist, gehört dieser zur Abfrage, die Sie im vorherigen Abschnitt ausgeführt haben. Bei mehreren Einträgen können Sie mithilfe der Felder oben auf der Seite eine Suche durchführen.

4. Wählen Sie die **Abfrage-ID** einer Hive-Abfrage aus. Es werden Informationen zu dieser Abfrage angezeigt.

    ![DAG-Details](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. Auf den Registerkarten auf dieser Seite können Sie die folgenden Informationen anzeigen:

    * **Abfragedetails:** Details zu der Hive-Abfrage
    * **Zeitachse:** Informationen zur Ausführungsdauer der einzelnen Verarbeitungsphasen
    * **Konfigurationen:** die für diese Abfrage verwendete Konfiguration

    Sie können über die Links in __Abfragedetails__ Informationen zur __Anwendung__ oder dem __DAG__ (gerichteter azyklischer Graph) für diese Abfrage suchen.
    
    * Der Link __Anwendung__ zeigt Informationen zur YARN-Anwendung für diese Abfrage an. Von hier aus können Sie auf die YARN-Anwendungsprotokolle zugreifen.
    * Der Link __DAG__ zeigt Informationen zum gerichteten azyklischen Graph für diese Abfrage an. Sie können sich hier eine grafische Darstellung des gerichteten azyklischen Graphen (DAG) ansehen. Sie erhalten auch Informationen zu den Scheitelpunkten im gerichteten azyklischen Graph.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mit der Verwendung der Apache Tez-Ansicht vertraut sind, können Sie sich als Nächstes genauer über die [Verwendung von Apache Hive in HDInsight](hadoop/hdinsight-use-hive.md) informieren.

Ausführlichere technische Informationen zu Apache Tez finden Sie auf der [Seite zu Apache Tez bei Hortonworks](http://hortonworks.com/hadoop/tez/).

Weitere Informationen zur Verwendung von Apache Ambari mit HDInsight finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md).
