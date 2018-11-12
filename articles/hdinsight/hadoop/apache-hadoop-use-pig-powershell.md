---
title: Verwenden von Hadoop Pig mit PowerShell in HDInsight – Azure
description: Erfahren Sie, wie Sie mit Azure PowerShell Pig-Aufträge an einen Hadoop-Cluster in HDInsight übermitteln.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: e50c551381a065e8651ab7970ef9cf18c6c3a765
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011016"
---
# <a name="use-azure-powershell-to-run-pig-jobs-with-hdinsight"></a>Ausführen von Pig-Aufträgen in HDInsight mithilfe von Azure PowerShell

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Dieses Dokument enthält ein Beispiel zur Verwendung von Azure PowerShell zum Übermitteln von Pig-Aufträgen an einen Hadoop-Cluster in HDInsight. Mithilfe von Pig können Sie MapReduce-Aufträge in einer Sprache (Pig Latin) verfassen, die Datentransformationen anstelle von Map- und Reduce-Funktionen modelliert.

> [!NOTE]
> Dieses Dokument bietet keine detaillierten Beschreibungen zu den in diesem Beispiel verwendeten Pig Latin-Anweisungen. Informationen zu den in diesem Beispiel verwendeten Pig Latin-Anweisungen finden Sie unter [Verwenden von Pig Latin mit Hadoop in HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Voraussetzungen

* **Einen Azure HDInsight-Cluster**

  > [!IMPORTANT]
  > Linux ist das einzige Betriebssystem, das unter HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [Welche Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Eine Arbeitsstation mit Azure PowerShell**.

## <a id="powershell"></a>Ausführen eines Pig-Auftrags

Azure PowerShell stellt *Cmdlets* bereit, mit denen Sie Pig-Aufträge in HDInsight remote ausführen können. Intern verwendet PowerShell REST-Aufrufe von [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat), das im HDInsight-Cluster ausgeführt wird.

Die folgenden Cmdlets werden zum Ausführen der Pig-Aufträge in einem HDInsight-Remotecluster verwendet:

* **Connect-AzureRmAccount**: Authentifiziert Azure PowerShell für Ihr Azure-Abonnement.
* **New-AzureRmHDInsightPigJobDefinition:** Erstellt mithilfe der angegebenen Pig Latin-Anweisungen eine *Auftragsdefinition*.
* **Start-AzureRmHDInsightJob**: Sendet die Auftragsdefinition an HDInsight und startet den Auftrag. Ein *Auftragsobjekt* wird zurückgegeben.
* **Wait-AzureRmHDInsightJob**: verwendet das Auftragsobjekt, um den Status des Auftrags zu prüfen. Es wird gewartet, bis der Auftrag abgeschlossen oder die Wartezeit abgelaufen ist.
* **Get-AzureRmHDInsightJobOutput**: Wird zum Abrufen der Ausgabe des Auftrags verwendet.

Die folgenden Schritte veranschaulichen, wie diese Cmdlets zum Ausführen eines Auftrags auf dem HDInsight-Cluster verwendet werden.

1. Speichern Sie den folgenden Code mithilfe eines Editors als **pigjob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Öffnen Sie eine neue Windows PowerShell-Eingabeaufforderung. Navigieren Sie zum Speicherort der Datei **pigjob.ps1** , und verwenden Sie folgenden Befehl zum Ausführen des Skripts:

        .\pigjob.ps1

    Sie werden aufgefordert, sich bei Ihrem Azure-Abonnement anzumelden. Außerdem werden Sie aufgefordert, den Namen und das Kennwort für das HTTPS/Administratorkonto für den HDInsight-Cluster einzugeben.

2. Nachdem der Auftrag abgeschlossen ist, muss er Informationen zurückgeben, die folgendem Text ähneln:

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="troubleshooting"></a>Problembehandlung

Wenn keine Informationen zurückgegeben werden, wenn der Auftrag abgeschlossen ist, zeigen Sie die Fehlerprotokolle an. Um Fehlerinformationen für diesen Auftrag anzuzeigen, fügen Sie folgenden Befehl am Ende der Datei **pigjob.ps1** hinzu. Anschließend speichern Sie die Datei und führen sie erneut aus.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Mit diesem Cmdlet werden die Informationen zurückgegeben, die bei der Auftragsverarbeitung in STDERR geschrieben wurden.

## <a id="summary"></a>Zusammenfassung
Wie Sie sehen können, bietet Azure PowerShell eine einfache Möglichkeit, um Pig-Aufträge auf einem HDInsight-Cluster auszuführen, den Auftragsstatus zu überwachen und die Ausgabe abzurufen.

## <a id="nextsteps"></a>Nächste Schritte
Allgemeine Informationen zu Pig in HDInsight:

* [Verwenden von Pig mit Hadoop in HDInsight](hdinsight-use-pig.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md)
* [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md)
