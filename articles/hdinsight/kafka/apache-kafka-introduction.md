---
title: "Einführung in Apache Kafka in HDInsight – Azure | Microsoft-Dokumentation"
description: "Informationen zu Apache Kafka in HDInsight: Es wird beschrieben, worum es sich handelt, welche Funktion erfüllt wird und wo Sie Beispiele und Informationen zu den ersten Schritten finden."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/04/2017
ms.author: larryfr
ms.openlocfilehash: 945b16553d56d5138b17e7768e43a298b310551d
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2018
---
# <a name="introducing-apache-kafka-on-hdinsight"></a>Einführung in Apache Kafka in HDInsight

[Apache Kafka](https://kafka.apache.org) ist eine verteilte Open Source-Streamingplattform, die zum Erstellen von Datenpipelines und Anwendungen mit Echtzeitstreaming verwendet werden kann. Kafka verfügt auch über Nachrichtenbrokerfunktionen, die einer Nachrichtenwarteschlange ähneln, über die Sie benannte Datenströme veröffentlichen und diese abonnieren können. Bei Kafka in HDInsight erhalten Sie einen verwalteten, hoch skalierbaren und hoch verfügbaren Dienst in der Microsoft Azure-Cloud.

## <a name="why-use-kafka-on-hdinsight"></a>Gründe für Kafka in HDInsight

Kafka in HDInsight umfasst die folgenden Features:

* __Vereinbarung zum Servicelevel (SLA) mit 99% Verfügbarkeit von Kafka__: Weitere Informationen finden Sie im Dokument [SLA für HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* __Fehlertoleranz und Rackinformationen__: Kafka basiert auf einem Konzept mit einem eindimensionalen Rack, das in bestimmten Umgebungen gut funktioniert. Doch in Umgebungen wie Azure ist ein Rack in zwei Dimensionen unterteilt – in Updatedomänen (UDs) und Fehlerdomänen (FDs). Microsoft stellt Tools bereit, mit denen sichergestellt werden kann, dass für Kafka-Partitionen und -Replikate in Bezug auf die UDs und FDs ein Ausgleich erzielt wird. 

    Weitere Informationen finden Sie unter [Hohe Verfügbarkeit Ihrer Daten mit Apache Kafka in HDInsight](apache-kafka-high-availability.md).

* **Integration von Azure Managed Disks**: Verwaltete Datenträger bieten eine bessere Skalierung und einen höheren Durchsatz für die Datenträger, die für Kafka in HDInsight verwendet werden (bis zu 16 TB pro Knoten im Cluster).

    Informationen zum Konfigurieren von verwalteten Datenträgern mit Kafka in HDInsight finden Sie unter [Erhöhen der Skalierbarkeit von Kafka in HDInsight](apache-kafka-scalability.md).

    Weitere Informationen zu verwalteten Datenträgern finden Sie unter [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* **Warnungen, Überwachung und Predictive Maintenance**: Azure Log Analytics kann zum Überwachen von Kafka in HDInsight verwendet werden. Log Analytics stellt Informationen zur VM-Ebene bereit, z.B. Datenträger- und NIC-Metriken sowie JMX-Metriken aus Kafka.

    Weitere Informationen finden Sie unter [Analysieren von Protokollen für Apache Kafka in HDInsight](apache-kafka-log-analytics-operations-management.md).

* **Replikation von Kafka-Daten**: Kafka verfügt über das MirrorMaker-Hilfsprogramm, mit dem Daten zwischen Kafka-Clustern repliziert werden.

    Informationen zur Verwendung von MirrorMaker finden Sie unter [Verwenden von MirrorMaker zum Replizieren von Apache Kafka-Themen mit Kafka in HDInsight](apache-kafka-mirroring.md).

* **Clusterskalierung**: Mit HDInsight können Sie die Anzahl von Workerknoten (zum Hosten des Kafka-Brokers) nach der Clustererstellung ändern. Skalieren Sie einen Cluster zentral hoch, wenn der Umfang der Workloads zunimmt, oder zentral herunter, um Kosten zu sparen. Die Skalierung kann über das Azure-Portal, Azure PowerShell und andere Azure-Verwaltungsoberflächen durchgeführt werden. Für Kafka sollten Sie für Partitionsreplikate nach Skalierungsvorgängen einen Ausgleichsvorgang durchführen. Durch das Ausgleichen von Partitionen kann für Kafka die neue Anzahl von Workerknoten genutzt werden.

    Weitere Informationen finden Sie unter [Hohe Verfügbarkeit Ihrer Daten mit Apache Kafka in HDInsight](apache-kafka-high-availability.md).

* **Veröffentlichen-Abonnieren-Messagingmuster**: Kafka umfasst eine Producer-API zum Veröffentlichen von Datensätzen in einem Kafka-Thema. Die Consumer-API wird verwendet, wenn Sie ein Thema abonnieren.

    Weitere Informationen finden Sie unter [Einstieg in Apache Kafka in HDInsight](apache-kafka-get-started.md).

* **Datenstromverarbeitung**: Kafka wird häufig zusammen mit Apache Storm oder Spark für die Echtzeit-Datenstromverarbeitung eingesetzt. Mit Kafka 0.10.0.0 (HDInsight-Version 3.5 und 3.6) wurde eine Streaming-API eingeführt, mit der Sie Streaminglösungen erstellen können, ohne dass Sie dafür Storm oder Spark benötigen.

    Weitere Informationen finden Sie unter [Einstieg in Apache Kafka in HDInsight](apache-kafka-get-started.md).

* **Horizontale Skalierung**: Bei Kafka werden Datenströme über die Knoten im HDInsight-Cluster hinweg partitioniert. Consumerprozesse können einzelnen Partitionen zugeordnet werden, um beim Nutzen von Datensätzen für einen Lastenausgleich zu sorgen.

    Weitere Informationen finden Sie unter [Einstieg in Apache Kafka in HDInsight](apache-kafka-get-started.md).

* **Geordnete Bereitstellung**: In jeder Partition werden die Datensätze im Datenstrom in der Reihenfolge gespeichert, in der sie empfangen wurden. Indem ein Consumerprozess pro Partition zugeordnet wird, können Sie sicherstellen, dass die Datensätze in der richtigen Reihenfolge verarbeitet werden.

    Weitere Informationen finden Sie unter [Einstieg in Apache Kafka in HDInsight](apache-kafka-get-started.md).

## <a name="use-cases"></a>Anwendungsfälle

* **Messaging**: Da das Veröffentlichen-Abonnieren-Messagingmuster unterstützt wird, wird Kafka häufig als Nachrichtenbroker genutzt.

* **Aktivitätsüberwachung**: Da Kafka die geordnete Protokollierung von Datensätzen unterstützt, kann die Anwendung zum Nachverfolgen und Neuerstellen von Aktivitäten verwendet werden. Beispiele hierfür sind Benutzeraktionen auf einer Website oder in einer Anwendung.

* **Aggregation**: Mit der Datenstromverarbeitung können Sie Informationen aus unterschiedlichen Datenströmen aggregieren, um die Informationen zu operativen Daten zu kombinieren und zu zentralisieren.

* **Transformation**: Mit der Datenstromverarbeitung können Sie Daten aus mehreren Eingabethemen zu einem oder mehreren Ausgabethemen kombinieren und erweitern.

## <a name="architecture"></a>Architecture

![Kafka-Clusterkonfiguration](./media/apache-kafka-introduction/kafka-cluster.png)

In diesem Diagramm ist eine typische Kafka-Konfiguration dargestellt, für die Consumergruppen, Partitionierung und Replikation verwendet werden, um eine parallele Ablesung von Ereignissen mit Fehlertoleranz zu ermöglichen. Apache ZooKeeper ist für gleichzeitige, robuste Transaktionen mit geringer Wartezeit ausgelegt, da darüber der Zustand des Kafka-Clusters verwaltet wird. Bei Kafka werden Datensätze in *Themen* gespeichert. Datensätze werden von *Producern* erstellt und von *Consumern* genutzt. Producer rufen Datensätze von Kafka-*Brokern* ab. Jeder Workerknoten in Ihrem HDInsight-Cluster ist ein Kafka-Broker. Für jeden Consumer wird eine Partition erstellt, um die parallele Verarbeitung der Streamingdaten zu ermöglichen. Die Replikation wird genutzt, um die Partitionen auf Knoten zu verteilen und für den Schutz vor Ausfällen von Knoten (Brokern) zu sorgen. Eine Partition, die mit einem *(L)* gekennzeichnet ist, ist jeweils die führende Partition. Producer-Datenverkehr wird an die führende Komponente jedes Knotens weitergeleitet, indem der von ZooKeeper verwaltete Zustand verwendet wird.

Jeder Kafka-Broker verwendet Azure Managed Disks. Die Anzahl von Datenträgern wird vom Benutzer festgelegt, und pro Broker können bis zu 16 TB Speicher bereitgestellt werden.

> [!IMPORTANT]
> Kafka ist sich der zugrunde liegenden Hardware (Rack) im Azure-Rechenzentrum nicht bewusst. Informationen zur Sicherstellung, dass Partitionen für die zugrunde liegende Hardware richtig verteilt sind, finden Sie im Dokument [Konfigurieren von Hochverfügbarkeit (Kafka)](apache-kafka-high-availability.md).

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die folgenden Links, um Informationen zur Verwendung von Apache Kafka unter HDInsight zu erhalten:

* [Erste Schritte mit Apache Kafka (Vorschau) in HDInsight](apache-kafka-get-started.md)

* [Verwenden von MirrorMaker zum Erstellen eines Replikats von Kafka in HDInsight](apache-kafka-mirroring.md)

* [Verwenden von Apache Storm mit Kafka in HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Verwenden von Apache Spark mit Kafka in HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Herstellen einer Verbindung mit Kafka über eine Azure Virtual Network-Instanz](apache-kafka-connect-vpn-gateway.md)