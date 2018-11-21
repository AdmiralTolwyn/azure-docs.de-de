---
title: Migrieren lokaler Apache Hadoop-Cluster zu Azure HDInsight – Motivation und Vorteile
description: Erfahren Sie mehr über die Motivation und Vorteile einer Migration von lokalen Hadoop-Clustern zu Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 82792414da9df32524e48031089dc18d2dc521cc
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288117"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrieren lokaler Apache Hadoop-Cluster zu Azure HDInsight – Motivation und Vorteile

Dieser Artikel ist der erste in einer Reihe von Artikeln zu bewährten Methoden für die Migration lokaler Bereitstellungen eines Apache Hadoop-Ökosystems zu Azure HDInsight. Diese Artikelreihe richtet sich an Personen, die für den Entwurf, die Bereitstellung und die Migration von Apache Hadoop-Lösungen in Azure HDInsight zuständig sind. Zu den Personengruppen, die von diesen Artikeln profitieren können, gehören Cloud-Architekten, Hadoop-Administratoren und DevOps-Techniker. Auch Softwareentwickler, Datentechniker und Datenanalysten können von den Erläuterungen der Funktionsweise verschiedener Clustertypen in der Cloud profitieren.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Warum eine Migration zu Azure HDInsight?

Azure HDInsight ist eine Clouddistribution der Hadoop-Komponenten von  [Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/). Azure HDInsight ermöglicht die einfache, schnelle und kostengünstige Verarbeitung umfangreicher Datenmengen. HDInsight umfasst die beliebtesten Open-Source-Frameworks wie beispielsweise:

- Apache Hadoop
- Apache Spark
- Apache Hive mit LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="advantages-that-azure-hdinsight-offers-over-on-premises-hadoop"></a>Vorteile von Azure HDInsight gegenüber lokalem Hadoop

- **Niedrige Kosten**: Kosten können reduziert werden, indem [bedarfsgesteuerte Cluster erstellt](../hdinsight-hadoop-create-linux-clusters-adf.md) werden und nur für die tatsächliche Nutzung bezahlt wird. Die Entkoppelung von Compute und Speicher bietet Flexibilität, da das Datenvolumen unabhängig von der Clustergröße ist.

- **Automatisierte Clustererstellung**: Die automatisierte Clustererstellung erfordert minimalen Setup- und Konfigurationsaufwand. Automation kann für bedarfsgesteuerte Cluster verwendet werden.

- **Verwaltete Hardware und Konfiguration**: Bei einem HDInsight-Cluster müssen Sie sich keine Gedanken um die physische Hardware oder Infrastruktur machen. Geben Sie einfach die Konfiguration des Clusters an, und Azure übernimmt die Einrichtung.

- **Problemlose Skalierbarkeit**: HDInsight ermöglicht Ihnen das  [zentrale Herauf- oder Herunterskalieren](../hdinsight-administer-use-portal-linux.md) von Workloads. Azure übernimmt die Neuverteilung von Daten und Workloads, ohne dass Datenverarbeitungsaufträge unterbrochen werden.

- **Globale Verfügbarkeit**: HDInsight ist in mehr [Regionen](https://azure.microsoft.com/regions/services/) verfügbar als jede andere Big Data-Analyselösung. Zudem steht Azure HDInsight für Azure Government, China und Deutschland zur Verfügung, was die Erfüllung geschäftlicher Anforderungen in zentralen unabhängigen Bereichen ermöglicht.

- **Sicher und konform**: Mit HDInsight können Sie die Datenressourcen Ihres Unternehmens durch die Verwendung von  [Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md),  [Verschlüsselung](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md) und Integration von  [Azure Active Directory](../domain-joined/apache-domain-joined-introduction.md) schützen. Darüber hinaus erfüllt HDInsight die gängigsten branchen- und behördenspezifischen  [Compliancestandards](https://azure.microsoft.com/overview/trusted-cloud).

- **Vereinfachte Versionsverwaltung**: Azure HDInsight verwaltet die Versionen von Komponenten des Hadoop-Ökosystems und hält sie auf dem neuesten Stand. Bei lokalen Bereitstellungen stellen Softwareupdates in der Regel ein komplexes Verfahren dar.

- **Kleinere Cluster, die für bestimmte Workloads mit weniger Abhängigkeiten zwischen Komponenten optimiert sind**: Bei einer üblichen lokalen Hadoop-Einrichtung wird ein einzelner Cluster verwendet, der vielen Zwecken dient. Mit Azure HDInsight können workloadspezifische Cluster erstellt werden. Durch das Erstellen von Clustern für bestimmte Workloads erübrigt sich die komplexe Verwaltung eines einzelnen Clusters mit wachsender Komplexität.

- **Produktivität**: Sie können verschiedene Tools für Hadoop und Spark in Ihrer bevorzugten Entwicklungsumgebung verwenden.

- **Erweiterbarkeit mit benutzerdefinierten Tools oder Anwendungen von Drittanbietern**: HDInsight-Cluster können mit installierten Komponenten erweitert und auch mithilfe von [One-Click](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) -Bereitstellungen aus dem Azure Marketplace in die anderen Big Data-Lösungen integriert werden.

- **Einfache Verwaltung, Administration und Überwachung**: Dank  [Azure Log Analytics](../hdinsight-hadoop-oms-log-analytics-tutorial.md) -Integration bietet Azure HDInsight eine zentrale Oberfläche für die Überwachung Ihrer gesamten Cluster.

- **Integration in andere Azure-Dienste**: HDInsight kann problemlos in andere beliebte Azure-Dienste wie den folgenden integriert werden:

    - Azure Data Factory (ADF)
    - Azure Blob Storage
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Azure SQL-Datenbank
    - Azure Analysis Services

- **Prozesse und Komponenten mit Selbstreparatur**: HDInsight überprüft ständig die Infrastruktur und Open-Source-Komponenten anhand einer eigenen Überwachungsinfrastruktur. Es werden auch automatisch kritische Fehler behoben, wie z.B. die Nichtverfügbarkeit von Open Source-Komponenten und Knoten. Wenn bei einer der OSS-Komponenten ein Fehler auftritt, werden Warnungen in Ambari ausgelöst.

Weitere Informationen finden Sie im Artikel [Was sind Azure HDInsight und der Hadoop-Technologiestapel?](../hadoop/apache-hadoop-introduction.md)

## <a name="migration-planning-process"></a>Migrationsplanungsprozess

Die folgenden Schritte werden zur Planung einer Migration von lokalen Hadoop-Clustern zu Azure HDInsight empfohlen:

1. Verstehen der aktuellen lokalen Bereitstellung und Topologien
2. Verstehen des aktuellen Projektumfangs, der Zeitpläne und Kenntnisse des Teams
3. Verstehen der Anforderungen für Azure
4. Erstellen eines detaillierten Plans basierend auf bewährten Methoden

## <a name="gathering-details-to-prepare-for-a-migration"></a>Sammeln von Informationen zur Vorbereitung einer Migration

Dieser Abschnitt enthält Musterfragebögen zum Sammeln wichtiger Informationen zu den folgenden Punkten:

- Lokale Bereitstellung
- Projektdetails
- Anforderungen für Azure

### <a name="on-premises-deployment-questionnaire"></a>Fragebogen zur lokalen Bereitstellung

| **Frage** | **Beispiel** | **Antwort** |
|---|---|---|
|**Thema**: **Umgebung**|||
|Typ der Clusterdistribution|Hortonworks, Cloudera, MapR| |
|Version der Clusterdistribution|HDP 2.6.5, CDH 5.7|
|Komponenten des Big Data-Ökosystems|HDFS, Yarn, Hive, LLAP, Impala, Kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, Atlas, Falcon, Zeppelin, R|
|Clustertypen|Hadoop, Spark, Confluent Kafka, Storm, Solr|
|Anzahl der Cluster|4|
|Anzahl der Masterknoten|2|
|Anzahl der Workerknoten|100|
|Anzahl der Edgeknoten| 5|
|Gesamtspeicherplatz|100 TB|
|Masterknotenkonfiguration|m/y, CPU, Datenträger usw.|
|Datenknotenkonfiguration|m/y, CPU, Datenträger usw.|
|Edgeknotenkonfiguration|m/y, CPU, Datenträger usw.|
|HDFS-Verschlüsselung?|JA|
|Hochverfügbarkeit|HDFS-Hochverfügbarkeit, Metastore-Hochverfügbarkeit|
|Notfallwiederherstellung/Sicherung|Cluster sichern?|  
|Systeme, die vom Cluster abhängig sind|SQL Server, Teradata, Power BI, MongoDB|
|Drittanbieterintegrationen|Tableau, GridGain, Qubole, Informatica, Splunk|
|**Thema**: **Sicherheit**|||
|Umgebungssicherheit|Firewalls|
|Authentifizierung und Autorisierung für Cluster|Active Directory, Ambari, Cloudera Manager, keine Authentifizierung|
|HDFS-Zugriffssteuerung|  Manuell, SSH-Benutzer|
|Authentifizierung und Autorisierung für Hive|Sentry, LDAP, AD mit Kerberos, Ranger|
|Überwachung|Ambari, Cloudera Navigator, Ranger|
|Überwachung|Graphite, collectd, statsd, Telegraf, InfluxDB|
|Warnungen|Kapacitor, Prometheus, Datadog|
|Dauer der Datenaufbewahrung| 3 Jahre, 5 Jahre|
|Clusteradministratoren|Einzelner Administrator, mehrere Administratoren|

### <a name="project-details-questionnaire"></a>Fragebogen zu Projektdetails

|**Frage**|**Beispiel**|**Antwort**|
|---|---|---|
|**Thema**: **Workloads und Häufigkeit**|||
|MapReduce-Aufträge|10 Aufträge – zweimal täglich||
|Hive-Aufträge|100 Aufträge – stündlich||
|Spark-Batchaufträge|50 Aufträge – alle 15 Minuten||
|Spark Streaming-Aufträge|5 Aufträge – alle 3 Minuten||
|Structured Streaming-Aufträge|5 Aufträge – minütlich||
|ML-Modell-Trainingsaufträge|2 Aufträge – einmal pro Woche||
|Programmiersprachen|Python, Scala, Java||
|Skripterstellung|Shell, Python||
|**Thema**: **Daten**|||
|Datenquellen|Flatfiles, Json, Kafka, RDBMS||
|Datenorchestrierung|Oozie-Workflows, Airflow||
|Suchvorgänge im Arbeitsspeicher|Apache Ignite, Redis||
|Datenziele|HDFS, RDBMS, Kafka, MPP ||
|**Thema**: **Metadaten**|||
|Hive-Datenbanktyp|Mysql, Postgres||
| Nein. der Hive-Metastores|2||
| Nein. der Hive-Tabellen|100||
| Nein. der Ranger-Richtlinien|20||
| Nein. der Oozie-Workflows|100||
|**Thema**: **Skalierung**|||
|Datenvolumen einschließlich Replikation|100 TB||
|Tägliches Datenerfassungsvolumen|50 GB||
|Wachstumsrate – Daten|10 % pro Jahr||
|Wachstumsrate – Clusterknoten|5 % pro Jahr
|**Thema**: **Clusterauslastung**|||
|Durchschnittliche CPU-Nutzung in %|60 %||
|Durchschnittliche Arbeitsspeichernutzung in %|75 %||
|Speicherplatznutzung|75 %||
|Durchschnittliche Netzwerknutzung in %|25%
|**Thema**: **Personal**|||
| Nein. der Administratoren|2||
| Nein. der Entwickler|10||
| Nein. der Endbenutzer|100||
|Fähigkeiten|Hadoop, Spark||
| Nein. der verfügbaren Ressourcen für Migrationsaufwand|2||
|**Thema**: **Einschränkungen**|||
|Aktuelle Einschränkungen|Latenz ist hoch||
|Aktuelle Herausforderungen|Parallelitätsproblem||

### <a name="azure-requirements-questionnaire"></a>Fragebogen zu Anforderungen für Azure

|**Thema**: **Infrastruktur** |||
|---|---|---|
|**Frage**|**Beispiel**|**Antwort**|
| Bevorzugte Region|USA, Osten||
|VNet bevorzugt?|JA||
|Hochverfügbarkeit/Notfallwiederherstellung erforderlich?|JA||
|Integration in andere Clouddienste?|ADF, CosmosDB||
|**Thema**: **Datenverschiebung**  |||
|Einstellung für ersten Ladevorgang|DistCp, Data Box, ADF, WANDisco||
|Deltadatenübertragung|DistCp, AzCopy||
|Fortlaufende inkrementelle Datenübertragung|DistCp, Sqoop||
|**Thema**: **Überwachung und Warnung** |||
|Azure-Überwachung und -Warnungen oder Integration einer Drittanbieter-Überwachungslösung|Azure-Überwachung und -Warnungen||
|**Thema**: **Sicherheitspräferenzen** |||
|Private und geschützte Datenpipeline?|JA||
|In die Domäne eingebundener Cluster (ESP)?|     JA||
|Synchronisierung von lokalem AD mit Cloud?|     JA||
| Nein. zu synchronisierender AD-Benutzer?|          100||
|Dürfen Kennwörter in Cloud synchronisiert werden?|    JA||
|Nur Cloudbenutzer?|                 JA||
|MFA erforderlich?|                       Nein || 
|Anforderungen an die Datenautorisierung?|  JA||
|Rollenbasierte Zugriffssteuerung?|        JA||
|Überwachung erforderlich?|                  JA||
|Datenverschlüsselung ruhender Daten?|          JA||
|Datenverschlüsselung während der Übertragung?|       JA||
|**Thema**: **Präferenzen für Umgestaltung der Architektur** |||
|Einzelner Cluster oder bestimmte Clustertypen|Bestimmte Clustertypen||
|Am gleichen Ort vorhandener Speicher oder Remotespeicher?|Remotespeicher||
|Kleinere Clustergröße, da Daten remote gespeichert werden?|Kleinere Clustergröße||
|Verwendung mehrerer kleinerer Cluster anstelle eines einzelnen großen Clusters?|Verwendung mehrerer kleinerer Cluster||
|Verwendung eines Remote-Metastore?|JA||
|Freigeben von Metastores zwischen verschiedenen Clustern?|JA||
|Dekonstruieren von Workloads?|Ersetzen von Hive-Aufträgen durch Spark-Aufträge||
|Verwendung von ADF zur Datenorchestrierung?|Nein ||
|HDInsight oder Hortonworks Data Platform in IaaS?|HDInsight||

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den nächsten Artikel in dieser Reihe:

- [Bewährte Methoden für die Architektur bei der Migration von lokalen Hadoop-Clustern zu Azure HDInsight](apache-hadoop-on-premises-migration-best-practices-architecture.md)
