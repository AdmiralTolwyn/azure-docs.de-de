---
title: "Anleitung zur Verwendung einer Hadoop-Sandbox – Emulator – Azure HDInsight | Microsoft-Dokumentation"
description: "Um sich mit dem Hadoop-Ökosystem vertraut zu machen, können Sie eine Hadoop-Sandbox von Hortonworks auf einem virtuellen Azure-Computer einrichten. "
keywords: hadoop emulator,hadoop sandbox
editor: cgronlun
manager: jhubbard
services: hdinsight
author: nitinme
documentationcenter: 
tags: azure-portal
ms.assetid: 6ad5bb58-8215-4e3d-a07f-07fcd8839cc6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 77973ac4224e439377f011a77106534b325b97f9
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2017
---
# <a name="get-started-with-a-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Erste Schritte mit einer Hadoop-Sandbox, einem Emulator auf einem virtuellen Computer

Hier erfahren Sie, wie Sie die Hadoop-Sandbox von Hortonworks auf einem virtuellen Computer installieren, um sich mit dem Hadoop-Ökosystem vertraut zu machen. Die Sandbox bietet eine lokale Entwicklungsumgebung, in der Sie Hadoop, Hadoop Distributed File System (HDFS) und die Übermittlung von Aufträgen kennenlernen. Nachdem Sie sich mit Hadoop vertraut gemacht haben, können Sie Hadoop in Azure verwenden und einen HDInsight-Cluster erstellen. Weitere Informationen für Ihren Einstieg finden Sie unter [Erste Schritte mit Hadoop in HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Voraussetzungen
* [Oracle VirtualBox](https://www.virtualbox.org/). Sie können die Software [hier](https://www.virtualbox.org/wiki/Downloads) herunterladen und installieren.



## <a name="download-and-install-the-virtual-machine"></a>Herunterladen und Installieren des virtuellen Computers
1. Navigieren Sie zur [Downloadseite von Hortonworks](http://hortonworks.com/downloads/#sandbox).

2. Klicken Sie auf **DOWNLOAD FOR VIRTUALBOX** (FÜR VIRTUALBOX HERUNTERLADEN), um die neueste Hortonworks Sandbox auf einen virtuellen Computer herunterzuladen. Vor Beginn des Downloads werden Sie aufgefordert, sich bei Hortonworks zu registrieren. Das Herunterladen dauert je nach Geschwindigkeit des Netzwerks ein bis zwei Stunden.
   
    ![Abbildung des Links zum Herunterladen von Hortonworks Sandbox für VirtualBox](./media/apache-hadoop-emulator-get-started/download-sandbox.png)
3. Klicken Sie auf der gleichen Webseite auf den Link **Import on Virtual Box** (In VirtualBox importieren), um eine PDF-Datei mit Installationsanweisungen für den virtuellen Computer herunterzuladen.

Erweitern Sie das Archiv, um eine Sandbox einer älteren HDP-Version herunterzuladen:

![Hortonworks Sandbox archive (Hortonworks Sandbox-Archiv)](./media/apache-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>Starten des virtuellen Computers

1. Öffnen Sie Oracle VirtualBox.
2. Klicken Sie im Menü **File** (Datei) auf **Import Appliance** (Appliance importieren), und geben Sie dann das Bild Hortonworks Sandbox-Image an.
1. Wählen Sie „Hortonworks Sandbox“, **Start** und anschließend **Normal Start** (Normaler Start) aus. Nach Abschluss des Startvorgangs für den virtuellen Computer werden Anmeldeanweisungen angezeigt.
   
    ![Normaler Start](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Öffnen Sie einen Webbrowser, und navigieren Sie zur angezeigten URL (in der Regel http://127.0.0.1:8888).

## <a name="set-sandbox-passwords"></a>Festlegen der Sandbox-Kennwörter

1. Wählen Sie im Schritt **Get Started** (Erste Schritte) der Seite „Hortonworks Sandbox“ die Option **View Advanced Options** (Erweiterte Optionen anzeigen) aus. Verwenden Sie die Informationen auf dieser Seite, um sich mittels SSH bei der Sandbox anzumelden. Verwenden Sie den angegebenen Namen und das angegebene Kennwort.
   
   > [!NOTE]
   > Sollte kein SSH-Client installiert sein, können Sie die webbasierte SSH verwenden, die vom virtuellen Computer unter **http://localhost:4200/** bereitgestellt wird.
   > 
   
    Beim erstmaligen Herstellen einer Verbindung über SSH werden Sie aufgefordert, das Kennwort für das Stammkonto zu ändern. Geben Sie ein neues Kennwort ein, das für die Anmeldung mithilfe von SSH verwenden.

2. Geben Sie nach der Anmeldung den folgenden Befehl ein:
   
        ambari-admin-password-reset
   
    Geben Sie ein Kennwort für das Ambari-Administratorkonto ein, wenn Sie dazu aufgefordert werden. Dieses wird verwendet, wenn Sie auf die Ambari-Webbenutzeroberfläche zugreifen.

## <a name="use-hive-commands"></a>Verwenden von Hive-Befehlen

1. Verwenden Sie bei einer SSH-Verbindung mit der Sandbox den folgenden Befehl, um die Hive-Shell zu starten:
   
        hive
2. Verwenden Sie nach dem Start der Shell Folgendes, um die Tabellen anzuzeigen, die zusammen mit der Sandbox bereitgestellt werden:
   
        show tables;
3. Verwenden Sie Folgendes, um zehn Zeilen aus der Tabelle `sample_07` abzurufen:
   
        select * from sample_07 limit 10;

## <a name="next-steps"></a>Nächste Schritte
* [Verwenden von Visual Studio mit der Sandbox von Hortonworks](../hdinsight-hadoop-emulator-visual-studio.md)
* [Learning the ropes of the Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop-Tutorial – Erste Schritte mit HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

