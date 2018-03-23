---
title: Erste Schritte mit Aufträgen für die elastische Datenbank | Microsoft Docs
description: Verwenden Sie Aufträgen für elastische Datenbanken zum Ausführen von T-SQL-Skripts, die sich über mehrere Datenbanken erstrecken.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 09/06/2016
ms.author: sstein
ms.openlocfilehash: 5d55ae225ca1b550bbf70314e2f48fc0161ad107
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/16/2018
---
# <a name="getting-started-with-elastic-database-jobs"></a>Erste Schritte mit Aufträgen für die elastische Datenbank
Aufträge für die elastische Datenbank (Vorschau) für Azure SQL-Datenbank ermöglicht die zuverlässige Ausführung von T-SQL-Skripts, die mehrere Datenbanken überspannen, und stellt automatische Wiederholung und ggf. Abschlussgarantien bereit. Weitere Informationen zu den Features von Aufträgen für die elastische Datenbank finden Sie unter [Elastische Aufträge](sql-database-elastic-jobs-overview.md).

Dieser Artikel erweitert das Beispiel unter [Erste Schritte mit Tools für elastische Datenbanken](sql-database-elastic-scale-get-started.md). Wenn Sie das Beispiel durchgearbeitet haben, haben Sie gelernt, wie Sie Aufträge zum Verwalten einer Gruppe aufeinander bezogener Datenbanken erstellen und verwalten können. Es ist nicht erforderlich, die Tools für die elastische Skalierung zu verwenden, um die Vorteile der elastischen Aufträge zu nutzen.

## <a name="prerequisites"></a>Voraussetzungen
Laden Sie das Beispiel [Erste Schritte mit den Tools für die elastische Datenbank](sql-database-elastic-scale-get-started.md)herunter, und führen Sie es aus.

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Erstellen eines Shardzuordnungs-Managers mithilfe der Beispiel-App
Hier erstellen Sie einen Shardzuordnungs-Manager und mehrere Shards und fügen anschließend Daten in die Shards ein. Wenn Sie bereits über Shards verfügen, die Shardingdaten enthalten, können Sie die folgenden Schritte überspringen und zum nächsten Abschnitt wechseln.

1. Erstellen Sie die Beispielanwendung aus **Erste Schritte mit den Tools für die elastische Datenbank** , und führen Sie sie aus. Führen Sie die Schritte bis Schritt 7 im Abschnitt [Herunterladen und Ausführen der Beispiel-App](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) aus. Am Ende von Schritt 7 wird die folgende Eingabeaufforderung angezeigt:

   ![Eingabeaufforderung](./media/sql-database-elastic-query-getting-started/cmd-prompt.png)

2. Geben Sie im Befehlsfenster „1“ ein, und drücken Sie die **EINGABETASTE**. Dadurch wird der Shardzuordnungs-Manager erstellt, und es werden zwei Shards zum Server hinzugefügt. Geben Sie dann „3“ ein, und drücken Sie die **Eingabetaste**. Wiederholen Sie diesen Vorgang viermal. Dadurch werden Beispieldatenzeilen in die Shards eingefügt.
3. Im [Azure-Portal](https://portal.azure.com) sollten drei neue Datenbanken angezeigt werden:

   ![Visual Studio-Bestätigung](./media/sql-database-elastic-query-getting-started/portal.png)

   An diesem Punkt erstellen wir eine benutzerdefinierte Datenbanksammlung, die alle Datenbanken in der Shardzuordnung berücksichtigt. Auf diese Weise können wir einen Auftrag erstellen und ausführen, mit dem eine neue, Shards übergreifende Tabelle hinzugefügt wird.

Wir würden hier normalerweise ein Shardzuordnungsziel mithilfe des Cmdlets **New-AzureSqlJobTarget** erstellen. Die Datenbank des Shardzuordnungs-Managers muss dann als Datenbankziel festgelegt werden, anschließend wird die bestimmte Shardzuordnung als Ziel angegeben. Stattdessen zählen wir alle Datenbanken auf dem Server auf, und fügen die Datenbanken mit Ausnahme der Masterdatenbank der neuen benutzerdefinierten Sammlung hinzu.

## <a name="creates-a-custom-collection-and-add-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Erstellt eine benutzerdefinierte Sammlung und fügt ihr alle Datenbanken auf dem Server mit Ausnahme der Masterdatenbank hinzu.
   ```
    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName
    $dbsinserver | %{
    $currentdb = $_.DatabaseName
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target."
        }

        else
        {
            throw $_
        }

    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
   }
   ```
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Erstellen eines T-SQL-Skripts für die datenbankübergreifende Ausführung
   ```
    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script
   ```

## <a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Erstellen Sie den Auftrag zum Ausführen eines Skripts über die gesamte benutzerdefinierte Gruppe von Datenbanken.

   ```
    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="execute-the-job"></a>Führen Sie den Auftrag aus.
Das folgende PowerShell-Skript kann verwendet werden, um einen vorhandenen Auftrag auszuführen:

Aktualisieren Sie die folgende Variable, sodass sie den gewünschten Auftragsnamen für die Ausführung angibt:

   ```
    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="retrieve-the-state-of-a-single-job-execution"></a>Abrufen des Ausführungsstatus eines einzelnen Auftrags
Verwenden Sie das gleiche Cmdlet **Get-AzureSqlJobExecution** mit dem Parameter **IncludeChildren**, um den Ausführungsstatus untergeordneter Aufträge anzuzeigen, insbesondere den spezifischen Status für die Ausführung der einzelnen Aufträge in jeder der im Auftrag enthaltenen Datenbanken.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions
   ```

## <a name="view-the-state-across-multiple-job-executions"></a>Übergreifendes Anzeigen des Ausführungsstatus über mehrere Aufträge
Das Cmdlet **Get-AzureSqlJobExecution** weist mehrere optionale Parameter auf, die zum Anzeigen der Ausführung mehrerer Aufträge verwendet werden können, die anhand der angegebenen Parameter gefiltert werden. Die folgenden Beispiele zeigen einige der möglichen Verwendungsweisen von „Get-AzureSqlJobExecution“:

Abrufen aller aktiven Auftragsausführungen auf der obersten Ebene:

   ```
    Get-AzureSqlJobExecution
   ```

Abrufen aller Auftragsausführungen auf der obersten Ebene, einschließlich der inaktiven Auftragsausführungen:

   ```
    Get-AzureSqlJobExecution -IncludeInactive
   ```

Abrufen der Ausführung aller untergeordneten Aufträge mit einer angegebenen Auftragsausführungs-ID, einschließlich inaktiver Auftragsausführungen:

   ```
    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren
   ```

Abrufen aller Auftragsausführungen, die eine bestimmte Kombination aus Zeitplan und Auftrag verwenden, einschließlich inaktiver Aufträge:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive
   ```

Abrufen aller Aufträge, die eine angegebene Shardzuordnung als Ziel haben, einschließlich inaktiver Aufträge:

   ```
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Abrufen aller Aufträge, die eine angegebene benutzerdefinierte Sammlung als Ziel haben, einschließlich inaktiver Aufträge:

   ```
    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Abrufen der Liste der Ausführungen von Auftragsaufgaben innerhalb einer bestimmten Auftragsausführung:

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions
   ```

Abrufen von Ausführungsdetails zu Auftragsaufgaben:

Das folgende PowerShell-Skript kann zum Anzeigen der Ausführungsdetails einer Auftragsaufgabe verwendet werden, was besonders beim Debuggen von Ausführungsfehlern nützlich ist.
   ```
    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution
   ```

## <a name="retrieve-failures-within-job-task-executions"></a>Abrufen von Ausführungsfehlern innerhalb von Auftragsaufgaben
Das Objekt „JobTaskExecution“ enthält eine Eigenschaft für den Lebenszyklus der Aufgabe und darüber hinaus eine Message-Eigenschaft. Bei einem Ausführungsfehler für eine Auftragsaufgabe wird die Eigenschaft „Lifecycle“ auf *Failed* und die Eigenschaft „Message“ auf die sich ergebende Ausnahmemeldung und deren Stapel festgelegt. Wenn ein Auftrag nicht erfolgreich abgeschlossen wurde, müssen die Details der Auftragsaufgaben angezeigt werden, die für einen bestimmten Auftrag nicht erfolgreich abgeschlossen wurden.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions)
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }
   ```

## <a name="waiting-for-a-job-execution-to-complete"></a>Warten auf den Abschluss einer Auftragsausführung
Das folgende PowerShell-Skript kann verwendet werden, um auf den Abschluss einer Auftragsaufgabe zu warten:

   ```
    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="create-a-custom-execution-policy"></a>Erstellen einer benutzerdefinierten Ausführungsrichtlinie
Aufträge für die elastische Datenbank unterstützen das Erstellen benutzerdefinierter Ausführungsrichtlinien, die beim Starten von Aufträgen angewendet werden können.

Ausführungsrichtlinien lassen aktuell die folgenden Definitionen zu:

* Name: ID der Ausführungsrichtlinie.
* Auftragstimeout: Gesamtzeit bis zum Abbruch eines Auftrags durch die Aufträge für die elastische Datenbank.
* Anfängliches Wiederholungsintervall: Wartezeit vor dem ersten Wiederholungsversuch.
* Maximales Wiederholungsintervall: Höchstmenge der zu durchlaufenden Wiederholungsintervalle.
* Backoffkoeffizient des Wiederholungsintervalls: Koeffizient zur Berechnung des nächsten Intervalls zwischen Wiederholungsversuchen.  Dazu wird diese Formel verwendet: (Anfängliches Wiederholungsintervall) * Math.pow((Intervallbackoffkoeffizient), (Anzahl der Wiederholungsversuche) - 2).
* Versuche maximal: Die Höchstzahl der im Rahmen eines Auftrags auszuführenden Wiederholungsversuche.

Für die standardmäßige Ausführungsrichtlinie werden diese Werte verwendet:

* Name: Standardausführungsrichtlinie
* Auftragstimeout: 1 Woche
* Anfängliches Wiederholungsintervall: 100 Millisekunden
* Maximales Wiederholungsintervall: 30 Minuten
* Wiederholungsintervallkoeffizient: 2
* Versuche maximal: 2.147.483.647

Erstellen Sie die gewünschte Ausführungsrichtlinie:

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy
   ```

### <a name="update-a-custom-execution-policy"></a>Aktualisieren einer benutzerdefinierten Ausführungsrichtlinie
Aktualisieren Sie die gewünschte Ausführungsrichtlinie:

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
   ```

## <a name="cancel-a-job"></a>Abbrechen eines Auftrags
Aufträge für die elastische Datenbank unterstützen Abbruchanforderungen für Aufträge.  Wenn Aufträge für die elastische Datenbank eine Abbruchanforderung für einen aktuell ausgeführten Auftrag erkennen, wird versucht, den Auftrag zu beenden.

Der Abbruch kann von Aufträgen für die elastische Datenbank auf zwei verschiedene Arten ausgeführt werden:

1. Abbruch aktuell ausgeführter Aufgaben: Wenn ein Abbruch erkannt wird, während eine Aufgabe aktuell ausgeführt wird, wird ein Abbruch innerhalb des aktuell ausgeführten Aspekts der Aufgabe versucht.  Beispiel: Wenn ein versuchter Abbruch sich auf eine aktuell ausgeführte Abfrage mit langer Laufzeit bezieht, wird versucht, die Abfrage abzubrechen.
2. Abbruch von Aufgabenwiederholungen: Wird vom Steuerthread ein Abbruch erkannt, bevor die Ausführung einer Aufgabe gestartet wurde, verhindert der Steuerthread das Starten der Aufgabe und erklärt die Anforderung für abgebrochen.

Wenn für einen übergeordneten Auftrag ein Auftragsabbruch angefordert wird, wird die Abbruchanforderung für den übergeordneten Auftrag und für alle seine untergeordneten Aufträge berücksichtigt.

Verwenden Sie zum Senden von Abbruchanforderungen das Cmdlet **Stop-AzureSqlJobExecution**, und legen Sie den Parameter **JobExecutionId** fest.

   ```
    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Löschen eines Auftrags anhand seines Namens und seines Verlaufs
Aufträge für die elastische Datenbank unterstützen die asynchrone Löschung von Aufträgen. Ein Auftrag kann für die Löschung markiert werden, und das System löscht den Auftrag und seinen gesamten Verlauf, nachdem die gesamte Ausführung von Unteraufträgen für den Auftrag abgeschlossen wurde. Das System bricht aktiv ausgeführte Aufträge nicht automatisch ab.  

Stattdessen muss „Stop-AzureSqlJobExecution“ aufgerufen werden, um die Ausführung aktiver Aufträge abzubrechen.

Verwenden Sie zum Auslösen der Löschung von Aufträgen das Cmdlet **Remove-AzureSqlJob**, und legen Sie den Parameter **JobName** fest.

   ```
    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
   ```

## <a name="create-a-custom-database-target"></a>Erstellen eines benutzerdefinierten Datenbankziels
In Aufträgen für die elastische Datenbank können benutzerdefinierte Datenbankziele definiert werden, die entweder direkt für die Ausführung oder für die Aufnahme in eine benutzerdefinierte Datenbankgruppe verwendet werden können. Da **Pools für elastische Datenbanken** durch die PowerShell-APIs noch nicht direkt unterstützt werden, erstellen Sie einfach ein benutzerdefiniertes Datenbankziel und ein benutzerdefiniertes Datenbanksammlungsziel, das sämtliche Datenbanken im Pool enthält.

Legen Sie die folgenden Variablen fest, um die gewünschten Datenbankinformationen anzugeben:

   ```
    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName
   ```

## <a name="create-a-custom-database-collection-target"></a>Erstellen eines benutzerdefinierten Datenbanksammlungsziels
Um die übergreifende Ausführung für mehrere definierte Datenbankziele zu ermöglichen, kann ein benutzerdefiniertes Datenbanksammlungsziel definiert werden. Nach dem Erstellen einer Datenbankgruppe können Datenbanken dem benutzerdefinierten Sammlungsziel zugeordnet werden.

Legen Sie die folgenden Variablen fest, um die gewünschte Konfiguration für das benutzerdefinierte Sammlungsziel anzugeben:

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="add-databases-to-a-custom-database-collection-target"></a>Hinzufügen von Datenbanken zu einem benutzerdefinierten Datenbanksammlungsziel
Datenbankziele können benutzerdefinierten Datenbanksammlungszielen zugeordnet werden, um eine Gruppe von Datenbanken zu erstellen. Jeder erstellte Auftrag mit einem benutzerdefinierten Datenbanksammlungsziel als Ziel wird bei der Ausführung auf die der Gruppe zugeordneten Datenbanken erweitert.

Fügen Sie die gewünschte Datenbank einer bestimmten benutzerdefinierten Sammlung hinzu:

   ```
    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName
   ```

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Überprüfen der Datenbanken in einem benutzerdefinierten Datenbanksammlungsziel
Verwenden Sie das Cmdlet **Get-AzureSqlJobTarget** , um die untergeordneten Datenbanken in einem benutzerdefinierten Datenbankziel abzurufen.

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets
   ```

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Erstellen eines Auftrags zum Ausführen eines Skripts für ein benutzerdefiniertes Datenbanksammlungsziel
Verwenden Sie das Cmdlet **New-AzureSqlJob** , um einen Auftrag für eine Gruppe von Datenbanken zu erstellen, die durch ein benutzerdefiniertes Datenbanksammlungsziel definiert ist. Aufträge für die elastische Datenbank erweitern den Auftrag in mehrere untergeordnete Aufträge, von denen jeder einer dem benutzerdefinierten Datenbanksammlungsziel zugeordneten Datenbank entspricht, und stellen sicher, dass das Skript für jede der Datenbanken ausgeführt wird. Um Wiederholungsresilienz sicherzustellen, müssen die Skripts idempotent sein.

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="data-collection-across-databases"></a>Datenbankübergreifende Datensammlung
**Aufträge für die elastische Datenbank** unterstützt das übergreifende Ausführen einer Abfrage für eine Gruppe von Datenbanken und sendet die Ergebnisse an eine Tabelle einer angegebenen Datenbank zurück. Anschließend kann eine Abfrage für die Tabelle ausgeführt werden, um die Ergebnisse der Abfrage für die einzelnen Datenbanken anzuzeigen. Dadurch wird ein asynchrones Verfahren zum übergreifenden Ausführen einer Abfrage in vielen Datenbanken bereitgestellt. Auftretende Fehler, wie die vorübergehende Nichtverfügbarkeit einer Datenbank, werden automatisch mithilfe von Wiederholungsversuchen behandelt.

Die angegebene Zieltabelle wird automatisch erstellt, wenn sie noch nicht vorhanden ist, und entspricht dem Schema der zurückgegebenen Ergebnismenge. Wenn bei einer Skriptausführung mehrere Resultsets zurückgegeben werden, senden Aufträge für die elastische Datenbank nur das erste Resultset an die angegebene Zieltabelle.

Das folgende PowerShell-Skript kann verwendet werden, um ein Skript auszuführen, das die Ergebnisse in einer angegebenen Tabelle sammelt. Das Skript geht davon aus, dass ein T-SQL-Skript erstellt wurde, das eine einzelne Ergebnismenge ausgibt, und ein benutzerdefiniertes Datenbanksammlungsziel erstellt wurde.

Legen Sie die folgenden Werte fest, um das gewünschte Skript, die Anmeldeinformationen und das Ausführungsziel anzugeben:

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Erstellen und Starten eines Auftrags für Datensammlungsszenarien
   ```
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Erstellen eines Zeitplans für die Auftragsausführung mithilfe eines Auftragsauslösers
Das folgende PowerShell-Skript kann zum Erstellen eines wiederkehrenden Zeitplans verwendet werden. Dieses Skript verwendet ein einminütiges Intervall, „New-AzureSqlJobSchedule“ unterstützt aber außerdem auch die Parameter „-DayInterval“, „-HourInterval“, „-MonthInterval“ und „-WeekInterval“. Zeitpläne, die nur einmalig ausgeführt werden sollen, können durch Angeben von „-OneTime“ erstellt werden.

Erstellen Sie einen neuen Zeitplan:
   ```
    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime
    Write-Output $schedule
   ```

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Erstellen eines Auftragsauslösers, um einen Auftrag nach einem Zeitplan auszuführen
Ein Auftragsauslöser kann so definiert werden, dass ein Auftrag gemäß einem Zeitplan ausgeführt wird. Das folgenden PowerShell-Skript kann zum Erstellen eines Auftragsauslösers verwendet werden.

Legen Sie die folgenden Variablen entsprechend dem gewünschten Auftrag und Zeitplan fest:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
    Write-Output $jobTrigger
   ```

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Entfernen einer Zeitplanzuordnung, um die Ausführung eines Auftrags gemäß einem Zeitplan zu beenden
Um die Fortsetzung der Auftragsausführung durch einen Auftragsauslöser aufzuheben, kann der Auftragsauslöser entfernt werden.
Entfernen Sie einen Auftragsauslöser, um die fortlaufende Ausführung eines Auftrags gemäß einem Zeitplan zu beenden, mithilfe des Cmdlets **Remove-AzureSqlJobTrigger** .

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
   ```

## <a name="import-elastic-database-query-results-to-excel"></a>Importieren der Ergebnisse der Abfrage für die elastische Datenbank in Excel
 Sie können die Ergebnisse einer Abfrage in eine Excel-Datei importieren.

1. Öffnen Sie Excel 2013.
2. Navigieren Sie zum Menüband **Daten** .
3. Klicken Sie auf **Aus anderen Quellen** und dann auf **Von SQL Server**.

   ![Excel-Import aus anderen Quellen](./media/sql-database-elastic-query-getting-started/exel-sources.png)

4. Geben Sie im **Datenverbindungs-Assistenten** den Servernamen und die Anmeldeinformationen ein. Klicken Sie auf **Weiter**.
5. Wählen Sie im Dialogfeld **Wählen Sie die Datenbank, die die benötigten Daten enthält** die Datenbank **ElasticDBQuery** aus.
6. Wählen Sie in der Listenansicht die Tabelle **Customers** aus, und klicken Sie auf **Weiter**. Klicken Sie auf **Fertig stellen**.
7. Wählen Sie im Formular **Daten importieren** unter **Wählen Sie das Format aus, in dem Sie diese Daten in der Arbeitsmappe anzeigen möchten** die Option **Tabelle** aus, und klicken Sie auf **OK**.

Die Excel-Tabelle wird mit allen Zeilen aus der Tabelle **Customers** gefüllt, die in verschiedenen Shards gespeichert sind.

## <a name="next-steps"></a>Nächste Schritte
Sie können jetzt die Datenfunktionen von Excel verwenden. Verwenden Sie die Verbindungszeichenfolge mit dem Servernamen, dem Datenbanknamen und den Anmeldeinformationen zum Verbinden der BI und der Datenintegrationstools mit der elastischen Abfragedatenbank. Stellen Sie sicher, dass SQL Server als Datenquelle für das Tool unterstützt wird. Verweisen Sie auf die elastische Abfragedatenbank und die externen Tabellen wie auf jede andere SQL Server-Datenbank und SQL Server-Tabelle, zu denen Sie mit dem Tool eine Verbindung herstellen würden.

### <a name="cost"></a>Kosten
Es gibt keine zusätzlichen Gebühren für die Verwendung der Abfragefunktion für elastische Datenbanken. Zum jetzigen Zeitpunkt steht dieses Feature nur für Premium-Datenbanken als Endpunkt zur Verfügung, die Shards können jedoch aus jeder Dienstebene stammen.

Preisinformationen finden Sie in der [SQL-Datenbank – Preisdetails](https://azure.microsoft.com/pricing/details/sql-database/).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
