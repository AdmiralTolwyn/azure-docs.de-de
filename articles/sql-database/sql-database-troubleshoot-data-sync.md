---
title: Behandeln von Problemen mit der Azure SQL-Datensynchronisierung (Vorschauversion) | Microsoft-Dokumentation
description: "Hier erfahren Sie, wie Sie häufige Probleme mit der Azure SQL-Datensynchronisierung (Vorschauversion) behandeln."
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 50cabbaa584671e52c1ea7efbd2ad990b8438272
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2017
---
# <a name="troubleshoot-issues-with-sql-data-sync-preview"></a>Behandeln von Problemen mit der SQL-Datensynchronisierung (Vorschau)

Dieser Artikel beschreibt, wie Sie bekannte Probleme mit der Azure SQL-Datensynchronisierung (Vorschauversion) behandeln. Sofern eine Problemlösung verfügbar ist, ist sie hier angegeben.

Eine Übersicht über die SQL-Datensynchronisierung (Vorschauversion) finden Sie unter [Synchronisieren von Daten über mehrere Cloud- und lokale Datenbanken mit SQL-Datensynchronisierung (Vorschauversion)](sql-database-sync-data.md).

## <a name="sync-issues"></a>Probleme bei der Synchronisierung

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a>Die Synchronisierung lokaler Datenbanken, die dem Client-Agent zugeordnet sind, ist über die Portalbenutzeroberfläche nicht erfolgreich.

#### <a name="description-and-symptoms"></a>Beschreibung und Symptome

Die Synchronisierung lokaler Datenbanken, die dem Client-Agent zugeordnet sind, ist über die Portalbenutzeroberfläche der SQL-Datensynchronisierung-Vorschauversion nicht erfolgreich. Auf dem lokalen Computer, auf dem der Agent ausgeführt wird, enthält das Ereignisprotokoll Fehler vom Typ „System.IO.IOException“. Diese Fehler weisen darauf hin, dass auf dem Datenträger nicht genügend Speicherplatz zur Verfügung steht.

#### <a name="resolution"></a>Lösung

Schaffen Sie mehr Platz auf dem Laufwerk, auf dem sich das Verzeichnis „%TEMP%“ befindet.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a>Meine Synchronisierungsgruppe ist im Verarbeitungszustand hängengeblieben.

#### <a name="description-and-symptoms"></a>Beschreibung und Symptome

Eine Synchronisierungsgruppe in der SQL-Datensynchronisierung-Vorschauversion bleibt lange Zeit im Verarbeitungszustand. Sie reagiert nicht auf den **stop**-Befehl, und das Protokoll zeigt keine neuen Einträge.

#### <a name="cause"></a>Ursache

Folgende Bedingungen können dazu führen, dass eine Synchronisierungsgruppe im Verarbeitungszustand hängenbleibt:

-   **Der Client-Agent ist offline**. Vergewissern Sie sich, dass der Client-Agent online ist, und versuchen Sie es dann noch mal.

-   **Der Client-Agent wurde deinstalliert oder ist aus einem anderen Grund nicht vorhanden**. Falls der Client-Agent deinstalliert wurde oder nicht vorhanden ist, gehen Sie wie folgt vor:

    1. Entfernen Sie die Agent-XML-Datei aus dem Installationsordner der SQL-Datensynchronisierung-Vorschauversion (sofern die Datei vorhanden ist).
    2. Installieren Sie den Agent auf einem lokalen Computer (hierbei kann es sich um denselben oder einen anderen Computer handeln). Übermitteln Sie dann den Agent-Schlüssel, der im Portal für den als offline angezeigten Agent generiert wurde.

-   **Der SQL-Datensynchronisierungsdienst wurde beendet**.

    1. Suchen Sie im Menü **Start** nach **Dienste**.
    2. Klicken Sie in den Suchergebnissen auf **Dienste**.
    3. Suchen Sie den Dienst **SQL-Datensynchronisierung-Vorschauversion**.
    4. Falls der Status **Beendet** lautet, klicken Sie mit der rechten Maustaste auf den Dienstnamen, und wählen Sie dann **Starten** aus.

#### <a name="resolution"></a>Lösung

Wenn die obigen Informationen nicht ausreichen, um Ihre Synchronisierungsgruppe aus dem Verarbeitungszustand zu lösen, kann der Microsoft-Support den Status der Synchronisierungsgruppe zurücksetzen. Um den Status der Synchronisierungsgruppe zurücksetzen zu lassen, erstellen Sie einen Beitrag im [Forum zu Azure SQL-Datenbank](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted). Teilen Sie in diesem Beitrag Ihre Abonnement-ID und die ID der Synchronisierungsgruppe mit, die zurückgesetzt werden soll. Ein Microsoft-Supportmitarbeiter antwortet auf Ihren Beitrag und informiert Sie, wenn der Status zurückgesetzt wurde.

### <a name="i-see-erroneous-data-in-my-tables"></a>Meine Tabellen enthalten fehlerhafte Daten.

#### <a name="description-and-symptoms"></a>Beschreibung und Symptome

Wenn Tabellen, die den gleichen Namen aufweisen, aber aus verschiedenen Datenbankschemas stammen, in eine Synchronisierung eingeschlossen werden, werden nach der Synchronisierung falsche Daten in den Tabellen angezeigt.

#### <a name="cause"></a>Ursache

Der Bereitstellungsprozess der SQL-Datensynchronisierung-Vorschauversion verwendet die gleichen Nachverfolgungstabellen für Tabellen mit gleichem Namen, aber unterschiedlichen Schemas. Aus diesem Grund spiegeln sich Änderungen in beiden Tabellen in der gleichen Nachverfolgungstabelle wider. Dies führt zu falschen Daten während der Synchronisierung.

#### <a name="resolution"></a>Lösung

Achten Sie darauf, dass sich die Namen der zu synchronisierenden Tabellen unterscheiden, auch wenn sie zu unterschiedlichen Schemas in der Datenbank gehören.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a>Nach einer erfolgreichen Synchronisierung sind inkonsistente Primärschlüsseldaten vorhanden.

#### <a name="description-and-symptoms"></a>Beschreibung und Symptome

Eine Synchronisierung wird als erfolgreich gemeldet, und das Protokoll enthält keine fehlerhaften oder übersprungenen Zeilen enthält, aber Sie stellen fest, dass Primärschlüsseldaten in den Datenbanken in der Synchronisierungsgruppe nicht konsistent sind.

#### <a name="cause"></a>Ursache

Dieses Ergebnis ist programmbedingt. Änderungen in einer Primärschlüsselspalte führen zu inkonsistenten Daten in den Zeilen, in denen der Primärschlüssel geändert wurde.

#### <a name="resolution"></a>Lösung

Achten Sie zur Vermeidung dieses Problems darauf, dass keine Daten in einer Primärschlüsselspalte geändert werden.

Um das Problem zu beheben, löschen Sie die Zeile mit den inkonsistenten Daten von allen Endpunkten in der Synchronisierungsgruppe. Fügen Sie die Zeile dann erneut ein.

### <a name="i-see-a-significant-degradation-in-performance"></a>Die Leistung hat sich erheblich verschlechtert.

#### <a name="description-and-symptoms"></a>Beschreibung und Symptome

Die Leistung verschlechtert sich deutlich – unter Umständen so weit, dass Sie nicht einmal mehr die Benutzeroberfläche der Datensynchronisierung starten können.

#### <a name="cause"></a>Ursache

Dies ist höchstwahrscheinlich auf eine Synchronisierungsschleife zurückzuführen. Eine Synchronisierungsschleife tritt auf, wenn eine Synchronisierung von Synchronisierungsgruppe A eine Synchronisierung von Synchronisierungsgruppe B auslöst, die dann eine Synchronisierung von Synchronisierungsgruppe A auslöst. Die tatsächliche Situation kann komplexer sein und mehr als zwei Synchronisierungsgruppen in der Schleife umfassen. Das Problem ist hier ein zirkuläres Auslösen der Synchronisierung, das dadurch verursacht wird, dass Synchronisierungsgruppen sich überlappen.

#### <a name="resolution"></a>Lösung

Die beste Lösung ist Prävention. Vergewissern Sie sich, dass Ihre Synchronisierungsgruppen keine Zirkelbezüge enthalten. Eine Zeile, die von einer Synchronisierungsgruppe synchronisiert wird, darf nicht von einer anderen Synchronisierungsgruppe synchronisiert werden.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a>Mit wird eine Meldung angezeigt, die besagt, dass ich den Wert NULL nicht in die Spalte \<Spalte\> einfügen kann, da die Spalte keine NULL-Werte zulässt. Was bedeutet dies, und wie kann ich das Problem beheben? 
Diese Fehlermeldung besagt, dass eines der beiden folgenden Probleme aufgetreten ist:
-  Eine Tabelle weist keinen Primärschlüssel auf. Um dieses Problem zu beheben, fügen Sie allen Tabellen, die Sie synchronisieren möchten, einen Primärschlüssel hinzu.
-  Es gibt eine WHERE-Klausel in Ihrer CREATE INDEX-Anweisung. Die Datensynchronisierung (Vorschauversion) verarbeitet diese Bedingung nicht. Um dieses Problem zu beheben, entfernen Sie die WHERE-Klausel, oder nehmen Sie die Änderungen an allen Datenbanken manuell vor. 
 
### <a name="how-does-data-sync-preview-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>Wie verarbeitet die Datensynchronisierung (Vorschau) Zirkelbezüge? Diese liegen vor, wenn dieselben Daten in mehreren Synchronisierungsgruppen synchronisiert werden und sich daher ständig ändern.
Die Datensynchronisierung (Vorschauversion) verarbeitet Zirkelbezüge nicht. Vermeiden Sie sie deshalb unbedingt. 

## <a name="client-agent-issues"></a>Probleme mit dem Client-Agent

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a>Der Client-Agent kann nicht installiert, deinstalliert oder repariert werden.

#### <a name="cause"></a>Ursache

Viele Szenarien können diesen Fehler verursachen. Um die genaue Ursache für diesen Fehler zu ermitteln, sehen Sie sich die Protokolle an.

#### <a name="resolution"></a>Lösung

Generieren Sie die Windows Installer-Protokolle, und untersuchen Sie diese, um die genaue Fehlerursache zu finden. Sie können die Protokollierung über eine Eingabeaufforderung aktivieren. Wenn es sich bei der heruntergeladenen AgentServiceSetup.msi-Datei um „LocalAgentHost.msi“ handelt, generieren und untersuchen Sie die Protokolldateien mithilfe der folgenden Befehlszeilen:

-   Für Installationen: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
-   Für Deinstallationen: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

Sie können die Protokollierung auch für alle mit Windows Installer durchgeführten Installationen aktivieren. Im Microsoft Knowledge Base-Artikel [Aktivieren der Windows Installer-Protokollierung](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) erfahren Sie, wie Sie die Protokollierung für Windows Installer mit nur einem Klick aktivieren. Dort finden Sie auch Informationen zum Speicherort der Protokolle.

### <a name="my-client-agent-doesnt-work"></a>Der Client-Agent funktioniert nicht.

#### <a name="description-and-symptoms"></a>Beschreibung und Symptome

Beim Versuch, den Client-Agent zu verwenden, werden folgende Meldungen angezeigt:

„Fehler beim Deserialisieren von Parameter www.microsoft.com/.../05:GetBatchInfoResult. Weitere Details finden Sie in der InnerException.“

„Meldung der inneren Ausnahme: Der Typ "Microsoft.Synchronization.ChangeBatch" ist ein ungültiger Sammlungstyp, da er keinen Standardkonstruktor besitzt.“

#### <a name="cause"></a>Ursache

Dies ist ein bekanntes Problem bei der Installation der SQL-Datensynchronisierung (Vorschauversion). Die wahrscheinlichste Ursache für die Meldung ist eine der folgenden:

-   Sie führen Windows 8 Developer Preview aus.
-   Sie haben .NET Framework 4.5 installiert.

#### <a name="resolution"></a>Lösung

Stellen Sie sicher, dass der Client-Agent auf einem Computer installiert ist, auf dem weder Windows 8 Developer Preview ausgeführt wird noch .NET Framework 4.5 installiert ist.

### <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>Der Client-Agent funktioniert nach dem Abbrechen der Deinstallation nicht mehr.

#### <a name="description-and-symptoms"></a>Beschreibung und Symptome

Der Client-Agent funktioniert nicht mehr, auch nachdem Sie die Deinstallation abgebrochen haben.

#### <a name="cause"></a>Ursache

Dieses Problem tritt auf, weil der Client-Agent der SQL-Datensynchronisierung-Vorschauversion keine Anmeldeinformationen speichert.

#### <a name="resolution"></a>Lösung

Sie können die folgenden beiden Lösungen ausprobieren:

-   Verwenden Sie „services.msc“, um die Anmeldeinformationen für den Client-Agent erneut einzugeben.
-   Deinstallieren Sie den Client-Agent, und installieren Sie einen neuen. Den neuesten Client-Agent können Sie aus dem [Download Center](http://go.microsoft.com/fwlink/?linkid=221479) herunterladen und installieren.

### <a name="my-database-isnt-listed-in-the-agent-list"></a>Meine Datenbank ist in der Agent-Liste nicht enthalten.

#### <a name="description-and-symptoms"></a>Beschreibung und Symptome

Wenn Sie versuchen, einer Synchronisierungsgruppe eine bereits vorhandene SQL Server-Datenbank hinzuzufügen, wird die Datenbank nicht in der Agent-Liste angezeigt.

#### <a name="cause"></a>Ursache

Folgende Szenarien können diesen Fehler verursachen:

-   Client-Agent und Synchronisierungsgruppe befinden sich in unterschiedlichen Rechenzentren.
-   Die Datenbankliste des Client-Agents ist nicht auf dem neuesten Stand.

#### <a name="resolution"></a>Lösung

Die Lösung hängt von der Ursache ab.

- **Client-Agent und Synchronisierungsgruppe befinden sich in unterschiedlichen Rechenzentren.**

    Der Client-Agent und die Synchronisierungsgruppe müssen sich im gleichen Rechenzentrum befinden. Hierfür stehen Ihnen zwei Möglichkeiten zur Verfügung:

    -   Erstellen Sie einen neuen Agent in dem Rechenzentrum, in dem sich die Synchronisierungsgruppe befindet. Registrieren Sie anschließend die Datenbank bei diesem Agent.
    -   Löschen Sie die aktuelle Synchronisierungsgruppe, Erstellen Sie die Synchronisierungsgruppe dann in dem Rechenzentrum neu, in dem sich der Agent befindet.

- **Die Datenbankliste des Client-Agents ist nicht auf dem neuesten Stand.**

    Beenden Sie den Client-Agent-Dienst, und starten Sie ihn neu.

    Der lokale Agent lädt die Liste mit den zugeordneten Datenbanken nur bei der ersten Übermittlung des Agent-Schlüssels herunter. Bei nachfolgenden Übermittlungen wird die Liste nicht heruntergeladen. Daher werden Datenbanken, die während der Verschiebung eines Agents registriert wurden, in der ursprünglichen Agent-Instanz nicht angezeigt.

### <a name="client-agent-doesnt-start-error-1069"></a>Der Client-Agent startet nicht. (Fehler 1069)

#### <a name="description-and-symptoms"></a>Beschreibung und Symptome

Sie stellen fest, dass der Agent auf einem Computer, der SQL Server hostet, nicht ausgeführt wird. Wenn Sie versuchen, den Agent manuell zu starten, wird ein Dialogfeld mit folgender Fehlermeldung angezeigt: „Fehler 1069: Der Dienst konnte wegen einer fehlerhaften Anmeldung nicht gestartet werden.“

![Dialogfeld mit dem Datensynchronisierungsfehler 1069](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

#### <a name="cause"></a>Ursache

Eine wahrscheinliche Ursache für diesen Fehler ist, dass sich das Kennwort auf dem lokalen Server geändert hat, seit Sie den Agent und das Agent-Kennwort erstellt haben.

#### <a name="resolution"></a>Lösung

Legen Sie das Kennwort des Agents auf Ihr aktuelles Serverkennwort fest:

1. Suchen Sie den Client-Agent-Dienst (Vorschauversion) der SQL-Datensynchronisierung-Vorschauversion.  
    a. Wählen Sie **Starten** aus.  
    b. Geben Sie **services.msc** in das Suchfeld ein.  
    c. Klicken Sie in den Suchergebnissen auf **Dienste**.  
    d. Scrollen Sie im Fenster **Dienste** zum Eintrag **SQL Data Sync (Preview) Agent Preview** (SQL-Datensynchronisierung-Vorschauversion: Agent-Vorschau).  
2. Klicken Sie mit der rechten Maustaste auf **SQL-Datensynchronisierung-Vorschauversion: Agent-Vorschau**, und wählen Sie **Beenden** aus.
3. Klicken Sie mit der rechten Maustaste auf **SQL-Datensynchronisierung-Vorschauversion: Agent-Vorschau**, und wählen Sie **Eigenschaften** aus.
4. Klicken Sie im Fenster **Eigenschaften von SQL-Datensynchronisierung-Vorschauversion: Agent-Vorschau** auf die Registerkarte **Anmelden**.
5. Geben Sie Ihr Kennwort in das Textfeld **Kennwort** ein.
6. Geben Sie das Kennwort im Feld **Kennwort bestätigen** noch einmal ein.
7. Klicken Sie auf **Apply** (Anwenden) und dann auf **OK**.
8. Klicken Sie im Fenster **Dienste** mit der rechten Maustaste auf den Dienst **SQL-Datensynchronisierung-Vorschauversion: Agent-Vorschau**, und klicken Sie anschließend auf **Starten**.
9. Schließen Sie das Fenster **Dienste**.

### <a name="i-cant-submit-the-agent-key"></a>Ich kann den Agent-Schlüssel nicht übermitteln.

#### <a name="description-and-symptoms"></a>Beschreibung und Symptome

Sie versuchen, einen erstellten oder neu erstellten Schlüssel für einen Agent über die SqlAzureDataSyncAgent-Anwendung zu übermitteln. Die Übermittlung kann jedoch nicht abgeschlossen werden.

![Dialogfeld mit Synchronisierungsfehler: Agent-Schlüssel kann nicht übermittelt werden.](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

Bevor Sie fortfahren, überprüfen Sie die folgenden Bedingungen:

-   Der Windows-Dienst für die SQL-Datensynchronisierung-Vorschauversion wird ausgeführt.  
-   Das Dienstkonto für den Windows-Dienst „SQL Data Sync (Preview) Preview“ (SQL-Datensynchronisierung-Vorschauversion: Vorschauversion) verfügt über Netzwerkzugriff.    
-   Der Client-Agent kann den Locatordienst kontaktieren. Vergewissern Sie sich, dass der folgende Registrierungsschlüssel den Wert „https://locator.sync.azure.com/LocatorServiceApi.svc“ aufweist:  
    -   x86-Computer: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`  
    -   x64-Computer: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

#### <a name="cause"></a>Ursache

Durch den Agent-Schlüssel wird jeder lokale Agent eindeutig identifiziert. Der Schlüssel muss zwei Bedingungen erfüllen:

-   Der Client-Agent-Schlüssel auf dem Server für die SQL-Datensynchronisierung-Vorschauversion und auf dem lokalen Computer muss identisch sein.
-   Der Client-Agent-Schlüssel kann nur einmal verwendet werden.

#### <a name="resolution"></a>Lösung

Sollte der Agent nicht funktionieren, ist mindestens eine dieser Bedingungen nicht erfüllt. So beheben Sie das Problem mit dem Agent:

1. Generieren Sie einen neuen Schlüssel.
2. Wenden Sie den neuen Schlüssel auf den Agent an.

So wenden Sie den neuen Schlüssel auf den Agent an:

1. Wechseln Sie im Explorer zum Installationsverzeichnis Ihres Agents. Das Standardinstallationsverzeichnis lautet C:\\Programme (x86)\\Microsoft SQL Data Sync.
2. Doppelklicken Sie auf das Unterverzeichnis „bin“.
3. Öffnen Sie die SqlAzureDataSyncAgent-Anwendung.
4. Klicken Sie auf **Agent-Schlüssel übermitteln**.
5. Fügen Sie den Schlüssel aus der Zwischenablage in das dafür vorgesehene Feld ein.
6. Klicken Sie auf **OK**.
7. Schließen Sie das Programm.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>Der Client-Agent kann nicht aus dem Portal gelöscht werden, wenn die zugeordnete lokale Datenbank nicht erreichbar ist.

#### <a name="description-and-symptoms"></a>Beschreibung und Symptome

Wenn ein lokaler, bei einem Client-Agent der SQL-Datensynchronisierung-Vorschauversion registrierter Endpunkt (also eine Datenbank) nicht erreichbar ist, kann der Client-Agent nicht gelöscht werden.

#### <a name="cause"></a>Ursache

Der lokale Agent kann nicht gelöscht werden, da die nicht erreichbare Datenbank noch bei dem Agent registriert ist. Wenn Sie versuchen, den Agent zu löschen, versucht der Löschprozess erfolglos, die Datenbank zu erreichen.

#### <a name="resolution"></a>Lösung

Verwenden Sie „force delete“, um die nicht erreichbare Datenbank zu löschen.

> [!NOTE]
> Wenn nach der Verwendung von „force delete“ Tabellen mit Synchronisierungsmetadaten zurückbleiben, verwenden Sie „deprovisioningutil.exe“, um diese zu bereinigen.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a>Die lokale Synchronisierungs-Agent-App kann keine Verbindung mit dem lokalen Synchronisierungsdienst herstellen.

#### <a name="resolution"></a>Lösung

Probieren Sie die folgenden Schritte aus:

1. Beenden Sie die App.  
2. Öffnen Sie den Bereich „Komponentendienste“.  
    a. Geben Sie **services.msc** in das Suchfeld der Taskleiste ein.  
    b. Doppelklicken Sie in den Suchergebnissen auf **Dienste**.  
3. Beenden Sie den Dienst **SQL-Datensynchronisierung-Vorschauversion: Vorschauversion**.
4. Starten Sie den Dienst **SQL-Datensynchronisierung-Vorschauversion: Vorschauversion** neu.  
5. Öffnen Sie die App erneut.

## <a name="setup-and-maintenance-issues"></a>Probleme bei Setup und Wartung

### <a name="i-get-a-disk-out-of-space-message"></a>Ich erhalte eine Meldung mit dem Hinweis, dass auf dem Datenträger kein Speicherplatz mehr zur Verfügung steht.

#### <a name="cause"></a>Ursache

Die Meldung, dass auf dem Datenträger kein Speicherplatz mehr zur Verfügung steht, kann angezeigt werden, wenn übrig gebliebene Dateien gelöscht werden müssen. Dies kann durch eine Virenschutzsoftware verursacht werden oder daran liegen, dass während Löschversuchen Dateien geöffnet waren.

#### <a name="resolution"></a>Lösung

Löschen Sie die Synchronisierungsdateien, die sich im Ordner „%temp%“ befinden, manuell (`del \*sync\* /s`). Löschen Sie dann die Unterverzeichnisse im Ordner „%temp%“.

> [!IMPORTANT]
> Löschen Sie keine Dateien, während eine Synchronisierung ausgeführt wird.

### <a name="i-cant-delete-my-sync-group"></a>Ich kann meine Synchronisierungsgruppe nicht löschen.

#### <a name="description-and-symptoms"></a>Beschreibung und Symptome

Der Versuch, eine Synchronisierungsgruppe zu löschen, ist nicht erfolgreich.

#### <a name="causes"></a>Ursachen

Jedes der folgenden Szenarien kann dazu führen, dass eine Synchronisierungsgruppe sich nicht löschen lässt:

-   Der Client-Agent ist offline.
-   Der Client-Agent wurde deinstalliert oder ist aus einem anderen Grund nicht vorhanden. 
-   Eine Datenbank ist offline. 
-   Die Synchronisierungsgruppe führt einen Bereitstellungs- oder Synchronisierungsvorgang aus. 

#### <a name="resolution"></a>Lösung

So lösen Sie das Problem, dass eine Synchronisierungsgruppe nicht gelöscht werden kann:

-   Vergewissern Sie sich, dass der Client-Agent online ist, und versuchen Sie es dann noch mal.
-   Falls der Client-Agent deinstalliert wurde oder nicht vorhanden ist, gehen Sie wie folgt vor:  
    a. Entfernen Sie die Agent-XML-Datei aus dem Installationsordner der SQL-Datensynchronisierung-Vorschauversion (sofern die Datei vorhanden ist).  
    b. Installieren Sie den Agent auf einem lokalen Computer (hierbei kann es sich um denselben oder einen anderen Computer handeln). Übermitteln Sie dann den Agent-Schlüssel, der im Portal für den als offline angezeigten Agent generiert wurde.
-   Stellen Sie sicher, dass der Dienst für die SQL-Datensynchronisierung-Vorschauversion ausgeführt wird:  
    a. Suchen Sie im Menü **Start** nach **Dienste**.  
    b. Klicken Sie in den Suchergebnissen auf **Dienste**.  
    c. Suchen Sie den Dienst **SQL Data Sync (Preview) Preview** (SQL-Datensynchronisierung-Vorschauversion: Vorschauversion).  
    d. Falls der Status **Beendet** lautet, klicken Sie mit der rechten Maustaste auf den Dienstnamen, und wählen Sie dann **Starten** aus.
-   Vergewissern Sie sich, dass alle SQL-Datenbanken und SQL Server-Datenbanken online sind.
-   Warten Sie, bis der Bereitstellungs- oder Synchronisierungsvorgang beendet ist, und versuchen Sie dann erneut, die Synchronisierungsgruppe zu löschen.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>Ich kann die Registrierung einer lokalen SQL Server-Datenbank nicht aufheben.

#### <a name="cause"></a>Ursache

Wahrscheinlich versuchen Sie, die Registrierung einer bereits gelöschten Datenbank aufzuheben.

#### <a name="resolution"></a>Lösung

Wenn Sie die Registrierung einer lokalen SQL Server-Datenbank aufheben möchten, wählen Sie die Datenbank aus, und klicken Sie anschließend auf **Löschen erzwingen**.

Sollte die Datenbank dadurch nicht aus der Synchronisierungsgruppe entfernt werden, gehen Sie folgendermaßen vor:

1. Beenden Sie den Client-Agent-Hostdienst, und starten Sie ihn neu:  
    a. Klicken Sie auf das Menü **Start**.  
    b. Geben Sie **services.msc** in das Suchfeld ein.  
    c. Doppelklicken Sie im Ergebnisbereich im Abschnitt **Programme** auf **Dienste**.  
    d. Klicken Sie mit der rechten Maustaste auf den Dienst **SQL-Datensynchronisierung-Vorschauversion**.  
    e. Falls der Dienst ausgeführt wird, beenden Sie ihn.  
    f. Klicken Sie mit der rechten Maustaste auf den Dienst, und wählen Sie **Starten** aus.  
    g. Überprüfen Sie, ob die Datenbank immer noch registriert ist. Falls sie nicht mehr registriert ist, sind keine weiteren Schritte erforderlich. Fahren Sie andernfalls mit dem nächsten Schritt fort.
2. Öffnen Sie die Client-Agent-App (SqlAzureDataSyncAgent).
3. Wählen Sie **Anmeldeinformationen bearbeiten** aus, und geben Sie die Anmeldeinformationen für die Datenbank ein.
4. Fahren Sie mit der Aufhebung der Registrierung fort.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a>Ich bin nicht zum Starten von Systemdiensten berechtigt.

#### <a name="cause"></a>Ursache

Dieser Fehler tritt in zwei Situationen auf:
-   Der Benutzername/das Kennwort ist falsch.
-   Das angegebene Benutzerkonto verfügt nicht über ausreichende Berechtigungen für die Anmeldung als Dienst.

#### <a name="resolution"></a>Lösung

Erteilen Sie dem Benutzerkonto die Berechtigung für die Anmeldung als Dienst.

1. Wechseln Sie zu **Start** > **Systemsteuerung** > **Verwaltung** > **Lokale Sicherheitsrichtlinie** > **Lokale Richtlinien** > **Zuweisen von Benutzerrechten**.
2. Wählen Sie **Anmelden als Dienst** aus.
3. Klicken Sie im Dialogfeld **Eigenschaften** auf das Benutzerkonto.
4. Klicken Sie auf **Übernehmen** und dann auf **OK**.
5. Schließen Sie alle Fenster.

### <a name="a-database-has-an-out-of-date-status"></a>Eine Datenbank hat den Status „Veraltet“.

#### <a name="cause"></a>Ursache

Datenbanken, die mindestens 45 Tage vom Dienst getrennt waren (gemessen ab dem Zeitpunkt, zu dem die Datenbank offline geschaltet wurde), werden von der SQL-Datensynchronisierung-Vorschauversion entfernt. Eine Datenbank, die online geschaltet wird, nachdem sie mindestens 45 Tage offline war, weist den Status **Veraltet** auf.

#### <a name="resolution"></a>Lösung

Sie können den Status **Veraltet** vermeiden, indem Sie sicherstellen, dass keine Ihrer Datenbanken 45 Tage oder länger offline ist.

Wenn eine Datenbank den Status **Veraltet** aufweist, gehen Sie folgendermaßen vor:

1. Entfernen Sie die Datenbank mit dem Status **Veraltet** aus der Synchronisierungsgruppe.
2. Fügen Sie die Datenbank wieder der Synchronisierungsgruppe hinzu.

> [!WARNING]
> Änderungen, die an der Datenbank vorgenommen wurden, während sie offline war, gehen verloren.

### <a name="a-sync-group-has-an-out-of-date-status"></a>Eine Synchronisierungsgruppe hat den Status „Veraltet“.

#### <a name="cause"></a>Ursache

Falls eine Änderung innerhalb der gesamten Aufbewahrungszeit von 45 Tagen nicht angewendet werden kann, kann eine Synchronisierungsgruppe den Status „Veraltet“ erhalten.

#### <a name="resolution"></a>Lösung

Um den Status **Veraltet** zu vermeiden, überprüfen Sie regelmäßig die Ergebnisse Ihrer Synchronisierungsaufträge in der Verlaufsanzeige. Untersuchen Sie alle Änderungen, die nicht angewendet werden konnten, und behandeln Sie die entsprechenden Probleme.

Wenn eine Synchronisierungsgruppe den Status **Veraltet** aufweist, löschen Sie die Gruppe und erstellen sie neu.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a>Eine Synchronisierungsgruppe kann innerhalb von drei Minuten nach dem Deinstallieren oder Beenden des Agents nicht gelöscht werden.

#### <a name="description-and-symptoms"></a>Beschreibung und Symptome

Sie können eine Synchronisierungsgruppe nach dem Deinstallieren oder Beenden des zugeordneten Client-Agents der SQL-Datensynchronisierung-Vorschauversion drei Minuten lang nicht löschen.

#### <a name="resolution"></a>Lösung

1. Entfernen Sie eine Synchronisierungsgruppe, während die zugeordneten Synchronisierungs-Agents online sind (empfohlen).
2. Falls der Agent installiert, aber offline ist, schalten Sie ihn auf dem lokalen Computer online. Warten Sie, bis der Agent im Portal der SQL-Datensynchronisierung-Vorschauversion als **online** angezeigt wird. Entfernen Sie dann die Synchronisierungsgruppe.
3. Ist der Agent offline, weil er deinstalliert wurde, gehen Sie folgendermaßen vor:  
    a.  Entfernen Sie die Agent-XML-Datei aus dem Installationsordner der SQL-Datensynchronisierung-Vorschauversion (sofern die Datei vorhanden ist).  
    b.  Installieren Sie den Agent auf einem lokalen Computer (hierbei kann es sich um denselben oder einen anderen Computer handeln). Übermitteln Sie dann den Agent-Schlüssel, der im Portal für den als offline angezeigten Agent generiert wurde.  
    c. Versuchen Sie, die Synchronisierungsgruppe zu löschen.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a>Was geschieht beim Wiederherstellen einer verloren gegangenen oder beschädigten Datenbank?

Wenn Sie eine verloren gegangene oder beschädigte Datenbank aus einer Sicherung wiederherstellen, stellt sich möglicherweise keine Konvergenz der Daten in den Synchronisierungsgruppen ein, zu denen die Datenbank gehört.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur SQL-Datensynchronisierung (Vorschauversion) finden Sie unter:

-   [Synchronisieren von Daten über mehrere Cloud- und lokale Datenbanken mit SQL-Datensynchronisierung (Vorschauversion)](sql-database-sync-data.md)  
-   [Einrichten der Azure SQL-Datensynchronisierung (Vorschauversion)](sql-database-get-started-sql-data-sync.md)  
-   [Bewährte Methoden für die Azure SQL-Datensynchronisierung (Vorschauversion)](sql-database-best-practices-data-sync.md)  
-   [Überwachen der Azure SQL-Datensynchronisierung (Vorschauversion) mit OMS Log Analytics](sql-database-sync-monitor-oms.md)  
-   Vollständige PowerShell-Beispiele, die die Konfiguration der SQL-Datensynchronisierung (Vorschauversion) veranschaulichen:  
    -   [Verwenden von PowerShell zum Synchronisieren von Daten zwischen mehreren Azure SQL-­Datenbanken](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Verwenden von PowerShell zum Synchronisieren zwischen einer Azure SQL-Datenbank und einer lokalen SQL Server-Datenbank](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Herunterladen der Dokumentation zur REST-API der SQL-Datensynchronisierung (Vorschauversion)](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Weitere Informationen zu SQL-Datenbank finden Sie hier:

-   [Übersicht über die SQL-Datenbank](sql-database-technical-overview.md)
-   [Datenbank-Lebenszyklusverwaltung](https://msdn.microsoft.com/library/jj907294.aspx)
