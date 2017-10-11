---
title: "Erstellen einer Scala-App zur Ausführung in Spark-Clustern – Azure HDInsight | Microsoft-Dokumentation"
description: "Erstellen Sie eine Spark-Anwendung, die in Scala mit Apache Maven als Buildsystem mit einem vorhandenen von IntelliJ IDEA bereitgestellten Maven-Archetyp für Scala geschrieben ist."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: nitinme
ms.openlocfilehash: 95dba08744357f8800b05e3d4b892e3a363d5985
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/29/2017
---
# <a name="create-a-scala-maven-application-to-run-on-apache-spark-cluster-on-hdinsight"></a>Erstellen einer Scala Maven-Anwendung zur Ausführung in einem Apache Spark-Cluster unter HDInsight

Hier erfahren Sie, wie Sie eine in Scala geschriebene Spark-Anwendung erstellen, die Maven mit IntelliJ IDEA nutzt. Dabei wird Apache Maven als Buildsystem verwendet. Ausgangspunkt ist ein vorhandener, von IntelliJ IDEA bereitgestellter Maven-Archetyp für Scala.  Das Erstellen einer Scala-Anwendung in IntelliJ IDEA umfasst die folgenden Schritte:

* Verwenden von Maven als Buildsystem
* Aktualisieren der Projektobjektmodell-Datei (POM-Datei) zum Auflösen von Spark-Modulabhängigkeiten
* Schreiben der Anwendung in Scala
* Generieren einer JAR-Datei, die an HDInsight Spark-Cluster übermittelt werden kann
* Ausführen der Anwendung in einem Spark-Cluster mithilfe von Livy

> [!NOTE]
> HDInsight stellt auch ein IntelliJ IDEA-Plug-In bereit, um die Erstellung und Übermittlung von Anwendungen an einen HDInsight Spark-Cluster unter Linux zu vereinfachen. Weitere Informationen finden Sie unter [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Erstellen von Spark Scala-Anwendungen für HDInsight Spark-Cluster unter Linux (Vorschau)](hdinsight-apache-spark-intellij-tool-plugin.md).
> 
> 

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Siehe [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)(in englischer Sprache).
* Ein Apache Spark-Cluster unter HDInsight. Eine Anleitung finden Sie unter [Erstellen von Apache Spark-Clustern in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit. Das Installationspaket finden Sie [hier](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Eine Java-IDE. In diesem Artikel wird IntelliJ IDEA 15.0.1 verwendet. Das Installationspaket finden Sie [hier](https://www.jetbrains.com/idea/download/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Installieren des Scala-Plug-Ins für IntelliJ IDEA
Falls Sie beim Installieren von IntelliJ IDEA nicht zum Aktivieren des Scala-Plug-Ins aufgefordert wurden, starten Sie IntelliJ IDEA, und führen Sie die folgenden Schritte aus, um das Plug-In zu installieren:

1. Klicken Sie auf der Willkommensseite von IntelliJ IDEA auf **Konfigurieren** und anschließend auf **Plug-Ins**.
   
    ![Scala-Plug-In aktivieren](./media/hdinsight-apache-spark-create-standalone-application/enable-scala-plugin.png)
2. Klicken Sie im nächsten Bildschirm links unten auf **JetBrains-Plug-In installieren** . Suchen Sie im daraufhin erscheinenden Dialogfeld **JetBrains-Plug-Ins durchsuchen** nach „Scala“, und klicken Sie anschließend auf **Installieren**.
   
    ![Scala-Plug-In installieren](./media/hdinsight-apache-spark-create-standalone-application/install-scala-plugin.png)
3. Klicken Sie nach erfolgreicher Installation des Plug-Ins auf die Schaltfläche **IntelliJ IDEA neu starten** , um die IDE neu zu starten.

## <a name="create-a-standalone-scala-project"></a>Erstellen eines eigenständigen Scala-Projekts
1. Starten Sie IntelliJ IDEA, und erstellen Sie ein neues Projekt. Wählen Sie im Dialogfeld für das neue Projekt Folgendes aus, und klicken Sie anschließend auf **Weiter**:
   
    ![Maven-Projekt erstellen](./media/hdinsight-apache-spark-create-standalone-application/create-maven-project.png)
   
   * Wählen Sie den Projekttyp **Maven** aus.
   * Geben Sie ein **Projekt-SDK**an. Klicken Sie auf „Neu“, und navigieren Sie zum Installationsverzeichnis von Java (üblicherweise `C:\Program Files\Java\jdk1.8.0_66`).
   * Aktivieren Sie das Kontrollkästchen **Archetypbasierte Erstellung** .
   * Wählen Sie in der Liste mit den Archetypen die Option **org.scala-tools.archetypes:scala-archetype-simple**aus. Dadurch wird die passende Verzeichnisstruktur erstellt, und die erforderlichen Abhängigkeiten zum Schreiben des Scala-Programms werden heruntergeladen.
2. Geben Sie passende Werte für **GroupId**, **ArtifactId** und **Version** an. Klicken Sie auf **Weiter**.
3. Übernehmen Sie im nächsten Dialogfeld, in dem Sie das Maven-Basisverzeichnis und andere Benutzereinstellungen angeben können, die Standardeinstellungen, und klicken Sie auf **Weiter**.
4. Geben Sie im letzten Dialogfeld einen Projektnamen und einen Speicherort an, und klicken Sie anschließend auf **Fertig stellen**.
5. Löschen Sie die Datei **MySpec.Scala** unter **Src\test\scala\com\microsoft\spark\example**. Sie wird für die Anwendung nicht benötigt.
6. Benennen Sie ggf. die standardmäßigen Quell- und Testdateien um. Navigieren Sie im linken Bereich von IntelliJ IDEA zu **src\main\scala\com.microsoft.spark.example**. Klicken Sie mit der rechten Maustaste auf **App.scala**, klicken Sie auf **Umgestalten**, klicken Sie auf „Datei umbenennen“, geben Sie im Dialogfeld den neuen Namen für die Anwendung an, und klicken Sie anschließend auf **Umgestalten**.
   
    ![Dateien umbenennen](./media/hdinsight-apache-spark-create-standalone-application/rename-scala-files.png)  
7. In den folgenden Schritten wird die Datei „pom.xml“ aktualisiert, um die Abhängigkeiten für die Spark Scala-Anwendung zu definieren. Damit diese Abhängigkeiten automatisch heruntergeladen und aufgelöst werden, muss Maven entsprechend konfiguriert sein.
   
    ![Maven für automatische Downloads konfigurieren](./media/hdinsight-apache-spark-create-standalone-application/configure-maven.png)
   
   1. Klicken Sie im Menü **Datei** auf **Einstellungen**.
   2. Navigieren Sie im Dialogfeld **Einstellungen** zu **Erstellung, Ausführung, Bereitstellung** > **Buildtools** > **Maven** > **Importieren**.
   3. Aktivieren Sie das Kontrollkästchen **Maven-Projekte automatisch importieren**.
   4. Klicken Sie auf **Übernehmen** und anschließend auf **OK**.
8. Aktualisieren Sie die Scala-Quelldatei mit Ihrem Anwendungscode. Öffnen Sie den vorhandenen Beispielcode, ersetzen Sie ihn durch den folgenden Code, und speichern Sie die Änderungen. Dieser Code liest die Daten aus der Datei „HVAC.csv“ (für alle HDInsight Spark-Cluster verfügbar), ruft die Zeilen ab, die nur eine Ziffer in der sechsten Spalte enthalten, und schreibt die Ausgabe in **/HVACOut** unter dem Standardspeichercontainer für den Cluster.
   
        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
9. Aktualisieren Sie die Datei „pom.xml“.
   
   1. Fügen Sie in `<project>\<properties>` Folgendes hinzu:
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. Fügen Sie in `<project>\<dependencies>` Folgendes hinzu:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Speichern Sie die Änderungen in „pom.xml“.
10. Erstellen Sie die JAR-Datei. IntelliJ IDEA ermöglicht die Erstellung von JAR als Projektartefakt. Führen Sie die folgenden Schritte aus:
    
    1. Klicken Sie im Menü **Datei** auf **Projektstruktur**.
    2. Klicken Sie im Dialogfeld **Projektstruktur** auf **Artefakte** und anschließend auf das Pluszeichen. Klicken Sie im Popupdialogfeld auf **JAR** und anschließend auf **From modules with dependencies** (Aus Modulen mit Abhängigkeiten).
       
        ![Erstellen einer JAR-Datei](./media/hdinsight-apache-spark-create-standalone-application/create-jar-1.png)
    3. Klicken Sie im Dialogfeld **Create JAR from Modules** (JAR aus Modulen erstellen)auf die Auslassungspunkte (![Ellipse](./media/hdinsight-apache-spark-create-standalone-application/ellipsis.png)) für die **Hauptklasse**.
    4. Wählen Sie im Dialogfeld **Hauptklasse auswählen** die standardmäßig angezeigte Klasse aus, und klicken Sie anschließend auf **OK**.
       
        ![Erstellen einer JAR-Datei](./media/hdinsight-apache-spark-create-standalone-application/create-jar-2.png)
    5. Vergewissern Sie sich im Dialogfeld **Create JAR from Modules**, dass die Option zum **Extrahieren in die JAR-Zieldatei** aktiviert ist, und klicken Sie anschließend auf **OK**. Dadurch wird eine einzelne JAR-Datei mit allen Abhängigkeiten erstellt.
       
        ![Erstellen einer JAR-Datei](./media/hdinsight-apache-spark-create-standalone-application/create-jar-3.png)
    6. Die Ausgabelayout-Registerkarte führt alle JAR-Dateien des Maven-Projekts auf. Dateien ohne direkte Abhängigkeit für die Scala-Anwendung können ausgewählt und gelöscht werden. Bei der hier erstellten Anwendung können Sie bis auf**SparkSimpleApp compile output**alle entfernen. Wählen Sie die zu löschenden JAR-Dateien aus, und klicken Sie anschließend auf das Symbol **Löschen** .
       
        ![Erstellen einer JAR-Datei](./media/hdinsight-apache-spark-create-standalone-application/delete-output-jars.png)
       
        Vergewissern Sie sich, dass das Kontrollkästchen **Build on make** aktiviert ist, damit die JAR-Datei bei jeder Projekterstellung oder -aktualisierung erstellt wird. Klicken Sie auf **Übernehmen** und dann auf **OK**.
    7. Klicken Sie auf der Menüleiste auf **Build** (Erstellen) und anschließend auf **Make Project** (Projekt erstellen). Die JAR-Datei kann auch durch Klicken auf **Build Artifacts** erstellt werden. Die JAR-Ausgabe wird unter **\out\artifacts** erstellt.
       
        ![Erstellen einer JAR-Datei](./media/hdinsight-apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Ausführen der Anwendung im Spark-Cluster
Gehen Sie wie folgt vor, um die Anwendung im Cluster auszuführen:

* **Kopieren Sie die JAR-Anwendungsdatei in das dem Cluster zugeordnete Azure-Speicher-BLOB**. Hierzu können Sie das Befehlszeilenprogramm [**AzCopy**](../storage/common/storage-use-azcopy.md) verwenden. Daneben gibt es aber auch noch zahlreiche andere Clients, die Sie zum Hochladen von Daten verwenden können. Weitere Informationen finden Sie unter [Hochladen von Daten für Hadoop-Aufträge in HDInsight](hdinsight-upload-data.md).
* **Verwenden Sie Livy, um einen Anwendungsauftrag remote an den Spark-Cluster zu übermitteln**. Spark-Cluster in HDInsight enthalten Livy, um REST-Endpunkte für die Remoteübermittlung von Spark-Aufträgen verfügbar zu machen. Weitere Informationen finden Sie unter [Remoteübermittlung von Spark-Aufträgen unter Verwendung von Livy mit Spark-Clustern in HDInsight](hdinsight-apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Nächster Schritt

In diesem Artikel haben Sie gelernt, wie eine Spark Scala-Anwendung erstellt wird. Wechseln Sie zur nächsten Artikel Informationen zum Ausführen dieser Anwendung in einem HDInsight Spark-Cluster mit Livius.

> [!div class="nextstepaction"]
>[Remoteausführung von Aufträgen in einem Spark-Cluster mithilfe von Livy](hdinsight-apache-spark-livy-rest-interface.md)

