---
title: "Hinzufügen von Hive-Bibliotheken während der Erstellung von HDInsight-Clustern – Azure | Microsoft-Dokumentation"
description: "Erfahren Sie, wie Sie Hive-Bibliotheken (JAR-Dateien) einem HDInsight-Cluster während der Erstellung des Clusters hinzufügen."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 2fd74b8d-c006-45c6-a9e2-72ff5d2d978a
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 434c7d24f55c20cc763df3b83429e039bb5e8541
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/28/2018
---
# <a name="add-custom-hive-libraries-when-creating-your-hdinsight-cluster"></a>Hinzufügen benutzerdefinierter Hive-Bibliotheken beim Erstellen des HDInsight-Clusters

Erfahren Sie, wie Sie Hive-Bibliotheken in HDInsight vorab laden. In diesem Dokument erfahren Sie, wie Sie eine Skriptaktion zum Vorabladen von Bibliotheken während der Clustererstellung verwenden. Bibliotheken, die anhand der Schritte in diesem Dokument hinzugefügt werden, stehen global in Hive zur Verfügung. Sie müssen zum Laden nicht [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) verwenden.

## <a name="how-it-works"></a>So funktioniert's

Beim Erstellen eines Clusters können Sie eine Skriptaktion verwenden, um Clusterknoten während der Erstellung zu bearbeiten. Das Skript in diesem Dokument akzeptiert einen einzelnen Parameter, der den Speicherort der Bibliotheken darstellt. Dieser Speicherort muss sich in einem Azure-Speicherkonto befinden. Die Bibliotheken müssen als JAR-Dateien gespeichert werden.

Während der Clustererstellung zählt das Skript die Dateien auf, kopiert diese auf Haupt- und Workerknoten in das Verzeichnis `/usr/lib/customhivelibs/` und fügt sie dann der `hive.aux.jars.path`-Eigenschaft in der Datei `core-site.xml` hinzu. Bei Linux-basierten Clustern aktualisiert es außerdem die Datei `hive-env.sh` mit dem Speicherort der Dateien.

> [!NOTE]
> Mithilfe der Skriptaktionen in diesem Artikel werden die Bibliotheken in den folgenden Szenarien verfügbar:
>
> * **Linux-basierte HDInsight-Cluster** (bei Verwendung eines Hive-Clients), **WebHCat** und **HiveServer2**.
> * **Windows-basierte HDInsight-Cluster** (bei Verwendung des Hive-Clients und **WebHCat**.

## <a name="the-script"></a>Das Skript

**Skriptspeicherort**

Bei **Linux-basierten Clustern**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Bei **Windows-basierten Clustern**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

> [!IMPORTANT]
> Linux ist das einzige Betriebssystem, das unter HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [Welche Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](hdinsight-component-versioning.md#hdinsight-windows-retirement).

**Anforderungen**

* Die Skripts müssen sowohl auf die **Hauptknoten** als auch die **Workerknoten** angewendet werden.

* Die JAR-Dateien, die Sie installieren möchten, müssen in Azure Blob Storage in einem **einzelnen Container** gespeichert werden.

* Das Speicherkonto mit der Bibliothek von JAR-Dateien **muss** bei der Erstellung mit dem HDInsight-Cluster verknüpft werden. Dies muss entweder das Standardspeicherkonto sein oder ein über die __optionale Konfiguration__ hinzugefügtes Konto.

* Der WASB-Pfad zum Container muss als Parameter für die Skriptaktion angegeben werden. Wenn sich die JAR-Dateien beispielsweise in einem Container namens **libs** in einem Speicherkonto namens **mystorage** befinden, lautet der Parameter **wasb://libs@mystorage.blob.core.windows.net/**.

  > [!NOTE]
  > Es wird vorausgesetzt, dass Sie bereits ein Speicherkonto und einen Blobcontainer erstellt und die Dateien darin hochgeladen haben.
  >
  > Wenn Sie noch kein Speicherkonto erstellt haben, ist dies über das [Azure-Portal](https://portal.azure.com) möglich. Anschließend können Sie in einem Hilfsprogramm wie dem [Azure Storage-Explorer](http://storageexplorer.com/) einen Container im Konto erstellen und Dateien in diesen hochladen.

## <a name="create-a-cluster-using-the-script"></a>Erstellen eines Clusters mithilfe des Skripts

> [!NOTE]
> Gehen Sie wie folgt vor, um einen Linux-basierten HDInsight-Cluster zu erstellen. Wählen Sie zum Erstellen eines Windows-basierten Clusters während der Clustererstellung **Windows** als Clusterbetriebssystem aus, und verwenden Sie das Windows PowerShell-Skript und nicht das Bash-Skript.
>
> Sie können auch Azure PowerShell oder das HDInsight .NET SDK zum Erstellen eines Clusters mit diesem Skript verwenden. Weitere Informationen zur Verwendung dieser Methoden finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

1. Beginnen Sie die Bereitstellung eines Clusters anhand der Schritte in [Bereitstellen von HDInsight-Clustern unter Linux](hdinsight-hadoop-provision-linux-clusters.md), schließen Sie sie jedoch nicht ab.

2. Wählen Sie im Abschnitt **Optionale Konfiguration** die Option **Skriptaktionen** aus, und geben Sie die folgenden Informationen an:

   * **NAME**: Geben Sie einen Anzeigenamen für die Skriptaktion ein.

   * **SCRIPT URI**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh

   * **HEAD:** Aktivieren Sie diese Option.

   * **WORKER**: Aktivieren Sie diese Option.

   * **ZOOKEEPER**: Lassen Sie diese Option deaktiviert.

   * **PARAMETER**: Geben Sie die WASB-Adresse des Containers und Speicherkontos an, das die JAR-Dateien enthält. Beispiel: **wasb://libs@mystorage.blob.core.windows.net/**

3. Verwenden Sie am unteren Rand der **Skriptaktionen** die Schaltfläche **Auswählen**, um die Konfiguration zu speichern.

4. Wählen Sie im Abschnitt **Optionale Konfiguration** die Option **Verknüpfte Speicherkonten** und den Link **Speicherschlüssel hinzufügen** aus. Wählen Sie das Speicherkonto aus, das die JAR-Dateien enthält. Klicken Sie zum Speichern der Einstellungen auf die Schaltfläche **Auswählen**, und gehen Sie zurück zu **Optionale Konfiguration**.

5. Klicken Sie unten im Abschnitt **Optionale Konfiguration** auf die Schaltfläche **Auswählen**, um die optionale Konfiguration zu speichern.

6. Setzen Sie die Bereitstellung des Clusters entsprechend der Beschreibung unter [Bereitstellen von HDInsight-Clustern unter Linux](hdinsight-hadoop-provision-linux-clusters.md) fort.

Nach der Erstellung des Clusters können Sie die durch dieses Skript hinzugefügten JAR-Dateien in Hive verwenden, ohne die `ADD JAR`-Anweisung verwenden zu müssen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Arbeiten mit Hive finden Sie unter [Verwenden von Hive mit HDInsight](hadoop/hdinsight-use-hive.md)
