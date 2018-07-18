---
title: Verwalten von Azure Recovery Services-Tresoren und -Servern
description: Verwenden Sie diesen Artikel zum Verwalten von Azure Recovery Services-Tresoren und -Servern.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: markgal
ms.openlocfilehash: 3d0404654631520909e63853d47b7de2b6cb4361
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606527"
---
# <a name="monitor-and-manage-azure-recovery-services-vaults-and-servers-for-windows-machines"></a>Überwachen und Verwalten von Azure Recovery Services-Tresoren und -Servern für Windows-Computer

Dieser Artikel enthält eine Übersicht über die Aufgaben im Rahmen der Sicherungsüberwachung und -verwaltung, die im Azure-Portal und über den Microsoft Azure Backup-Agent zur Verfügung stehen. Es wird vorausgesetzt, dass Sie bereits über ein Azure-Abonnement verfügen und mindestens einen Recovery Services-Tresor erstellt haben.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Öffnen eines Recovery Services-Tresors

Das Dashboard des Recovery Services-Tresors enthält die Details oder Attribute eines Recovery Services-Tresors.

1. Melden Sie sich mit Ihrem Azure-Abonnement am [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie auf **Alle Dienste**. 

3. Sie möchten einen Recovery Services-Tresor öffnen. Beginnen Sie im Dialogfeld mit der Eingabe der Zeichenfolge **Recovery Services**. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Klicken Sie auf **Recovery Services-Tresore**, um die Liste mit den Recovery Services-Tresoren in Ihrem Abonnement anzuzeigen.

     ![Öffnen der Liste mit den Recovery Services-Tresoren – Schritt 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png) <br/>

    Die Liste mit den Recovery Services-Tresoren wird geöffnet.

    ![Erstellen eines Recovery Services-Tresors – Schritt 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Wählen Sie in der Tresorliste den Namen des Recovery Services-Tresors aus, den Sie öffnen möchten. Das Menü mit dem Dashboard des Recovery Services-Tresors wird geöffnet.

    ![Dashboard des Recovery Services-Tresors](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Nachdem Sie den Recovery Services-Tresor geöffnet haben, können Sie eine beliebige Überwachungs- oder Verwaltungsaufgabe ausprobieren.

## <a name="monitor-backup-jobs-and-alerts"></a>Überwachen von Sicherungsaufträgen und Warnungen

Sie überwachen Aufträge und Warnungen über das Dashboard des Recovery Services-Tresors, in dem Folgendes angezeigt wird:

* Details von Sicherungswarnungen
* Dateien und Ordner sowie in der Cloud geschützte virtuelle Azure-Computer
* Insgesamt in Azure verwendeter Speicher
* Status des Sicherungsauftrags

![Aufgaben im Sicherungsdashboard](./media/backup-azure-manage-windows-server/dashboard-tiles.png)

Wenn Sie auf die Informationen auf diesen Kacheln klicken, wird das zugeordnete Menü geöffnet, mit dem Sie die dazugehörigen Aufgaben verwalten.

Am oberen Rand des Dashboards:

* Einstellungen: Über die Einstellungen besteht Zugriff auf die verfügbaren Sicherungsaufgaben.
* Sicherung: Ermöglicht das Sichern von neuen Dateien und Ordnern (oder Azure-VMs) im Recovery Services-Tresor.
* Löschen: Wenn ein Recovery Services-Tresor nicht mehr verwendet wird, sollten Sie ihn löschen, um Speicherplatz freizugeben. „Löschen“ wird erst aktiviert, nachdem alle geschützten Server aus dem Tresor gelöscht wurden.

![Aufgaben im Sicherungsdashboard](./media/backup-azure-manage-windows-server/dashboard-tasks.png)

## <a name="alerts-for-backups-using-azure-backup-agent"></a>Warnungen für Sicherungen mit dem Azure Backup-Agent:
| Warnstufe | Gesendete Warnungen |
| --- | --- |
| Kritisch | Für Sicherungsfehler, Wiederherstellungsfehler und verzögerte Löschvorgänge, z.B. beim Beenden des Schutzes durch das Löschen von Daten |
| Warnung | Für abgeschlossene Sicherung mit Warnungen (wenn weniger als 100 Dateien aufgrund von Beschädigung nicht gesichert und mehr als 1.000.000 Dateien erfolgreich gesichert wurden) |
| Information | Aktuell sind keine Informationsmeldungen für den Azure Backup-Agent verfügbar. |

## <a name="manage-backup-alerts"></a>Verwalten von Sicherungswarnungen
Klicken Sie auf die Kachel **Sicherungswarnungen**, um das Menü **Sicherungswarnungen** zu öffnen und Warnungen zu verwalten.

![Sicherungswarnungen](./media/backup-azure-manage-windows-server/manage-backup-alerts.png)

Auf der Kachel „Sicherungswarnungen“ wird die Anzahl für Folgendes angezeigt:

* Kritische Warnungen, die innerhalb der letzten 24 Stunden nicht gelöst wurden
* Warnungen, die innerhalb der letzten 24 Stunden nicht gelöst wurden

Klicken Sie auf den Link zum Anzeigen des Menüs **Sicherungswarnungen** mit einer gefilterten Ansicht dieser Warnungen („Kritisch“ oder „Warnung“).

Gehen Sie im Menü „Sicherungswarnungen“ wie folgt vor:

* Wählen Sie die entsprechenden Informationen zum Einbinden in Ihre Warnungen aus.

    ![Spalten auswählen](./media/backup-azure-manage-windows-server/choose-alerts-colunms.png)
* Filtern Sie Warnungen nach Schweregrad, Status und Start-/Endzeiten.

    ![Warnungen filtern](./media/backup-azure-manage-windows-server/filter-alerts.png)
* Konfigurieren Sie Benachrichtigungen in Bezug auf Schweregrad, Häufigkeit und Empfänger, und aktivieren oder deaktivieren Sie Warnungen.

    ![Warnungen filtern](./media/backup-azure-manage-windows-server/configure-notifications.png)

Wenn **Pro Warnung** als Häufigkeit für **Benachrichtigen** ausgewählt ist, wird in E-Mails keine Gruppierung oder Reduzierung durchgeführt. Jede Warnung führt zu einer Benachrichtigung (Standardeinstellung), und es wird sofort eine Lösungs-E-Mail gesendet.

Wenn **Stündliche Übersicht** als Häufigkeit für **Benachrichtigen** gewählt wird, erhält der Benutzer eine E-Mail mit dem Hinweis, dass innerhalb der letzten Stunde ungelöste Warnungen generiert wurden. Nach einer Stunde wird jeweils eine Lösungs-E-Mail gesendet.

Warnungen können für die folgenden Schweregrade gesendet werden:

* Kritisch
* Warnung
* Information

Sie deaktivieren die Warnung im Menü mit den Auftragsdetails mit der Schaltfläche **Deaktivieren**. Wenn Sie auf „Deaktivieren“ klicken, können Sie Lösungshinweise angeben.

Sie wählen die Spalten, die im Rahmen der Warnung angezeigt werden sollen, mit der Schaltfläche **Spalten auswählen** aus.

> [!NOTE]
> Im Menü **Einstellungen** verwalten Sie Sicherungswarnungen, indem Sie **Überwachung und Berichte > Warnungen und Ereignisse > Sicherungswarnungen** wählen und dann auf **Filter** oder **Benachrichtigungen konfigurieren** klicken.
>
>

## <a name="manage-backup-items"></a>Verwalten von Sicherungselementen
Die Verwaltung von lokalen Sicherungen ist jetzt im Verwaltungsportal möglich. Im Abschnitt „Sicherung“ des Dashboards wird auf der Kachel **Sicherungselemente** die Anzahl von Sicherungselementen im Tresor angezeigt.

Klicken Sie auf der Kachel „Sicherungselemente“ auf **Dateien/Ordner** .

![Kachel „Sicherungselemente“](./media/backup-azure-manage-windows-server/backup-items-tile.png)

Das Menü „Sicherungselemente“ wird geöffnet, und der Filter ist auf „Dateien/Ordner“ festgelegt, sodass die einzelnen Sicherungselemente angezeigt werden.

![Sicherungselemente](./media/backup-azure-manage-windows-server/backup-item-list.png)

Wenn Sie in der Liste ein bestimmtes Sicherungselement auswählen, werden die wichtigen Details des Elements angezeigt.

> [!NOTE]
> Im Menü **Einstellungen** verwalten Sie Dateien und Ordner, indem Sie **Geschützte Elemente > Sicherungselemente** wählen und dann im Dropdownmenü auf **Dateien/Ordner** klicken.
>
>

![Sicherungselemente aus Einstellungen](./media/backup-azure-manage-windows-server/backup-files-and-folders.png)

## <a name="manage-backup-jobs"></a>Verwalten von Sicherungsaufträgen
Sicherungsaufträge für lokale Sicherungen (wenn der lokale Server in Azure sichert) und Azure-Sicherungen werden im Dashboard angezeigt.

Im Abschnitt „Sicherung“ des Dashboards wird auf der Kachel „Sicherung“ die Anzahl von Aufträgen angezeigt:

* In Arbeit
* Fehler in den letzten 24 Stunden

Klicken Sie zum Verwalten Ihrer Sicherungsaufträge auf die Kachel **Sicherungsaufträge**, um das Menü „Sicherungsaufträge“ zu öffnen.

![Sicherungselemente aus Einstellungen](./media/backup-azure-manage-windows-server/backup-jobs.png)

Sie ändern die Informationen, die im Menü „Sicherungsaufträge“ verfügbar sind, mit der Schaltfläche **Spalten auswählen** oben auf der Seite.

Verwenden Sie die Schaltfläche **Filter** , um zwischen „Dateien und Ordner“ und „Sicherung eines virtuellen Azure-Computers“ zu wählen.

Gehen Sie wie folgt vor, wenn Ihre Sicherungsdateien und -ordner nicht angezeigt werden: Klicken Sie oben auf der Seite auf die Schaltfläche **Filter**, und wählen Sie im Menü „Elementtyp“ die Option **Dateien und Ordner**.

> [!NOTE]
> Im Menü **Einstellungen** verwalten Sie Sicherungsaufträge, indem Sie **Überwachung und Berichte > Aufträge > Sicherungsaufträge** und dann im Dropdownmenü die Option **Dateien/Ordner** wählen.
>
>

## <a name="monitor-backup-usage"></a>Überwachen der Sicherungsverwendung
Im Dashboardabschnitt „Sicherung“ wird auf der Kachel „Sicherungsverwendung“ der in Azure genutzte Speicher angezeigt. Die Speicherverwendung wird für Folgendes bereitgestellt:

* Cloud-LRS-Speicherverwendung des Tresors
* Cloud-GRS-Speicherverwendung des Tresors

## <a name="manage-your-production-servers"></a>Verwalten Ihrer Produktionsserver
Klicken Sie zum Verwalten Ihrer Produktionsserver auf **Einstellungen**.

Klicken Sie unter „Verwalten“ auf **Sicherungsinfrastruktur > Produktionsserver**.

Im Menü „Produktionsserver“ sind alle verfügbaren Produktionsserver aufgelistet. Klicken Sie in der Liste auf einen Server, um die Serverdetails zu öffnen.

![Geschützte Elemente](./media/backup-azure-manage-windows-server/production-server-list.png)


## <a name="open-the-azure-backup-agent"></a>Öffnen des Azure Backup-Agents
Öffnen Sie den **Microsoft Azure Backup-Agent**. (Sie finden den Agent, indem Sie auf Ihrem Computer nach *Microsoft Azure Backup* suchen.)

![Planen einer Windows Server-Sicherung](./media/backup-azure-manage-windows-server/snap-in-search.png)

Über die im rechten Bereich der Backup-Agent-Konsole angezeigten **Aktionen** führen Sie die folgenden Verwaltungsaufgaben aus:

* Server registrieren
* Sicherung planen
* Jetzt sichern
* Eigenschaften ändern

![Aktionen in der Microsoft Azure Backup-Agent-Konsole](./media/backup-azure-manage-windows-server/console-actions.png)

> [!NOTE]
> Informationen zum **Wiederherstellen von Daten**finden Sie unter [Wiederherstellen von Dateien auf einem Windows-Server- oder Windows-Clientcomputer mit dem Resource Manager-Bereitstellungsmodell](backup-azure-restore-windows-server.md).
>
>

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="modify-the-backup-schedule"></a>Ändern des Sicherungszeitplans
1. Klicken Sie im Microsoft Azure Backup-Agent auf **Sicherung planen**.

    ![Planen einer Windows Server-Sicherung](./media/backup-azure-manage-windows-server/schedule-backup.png)
2. Lassen Sie im **Assistenten zum Planen von Sicherungen** die Option **Sicherungselemente oder -zeiten ändern** aktiviert, und klicken Sie auf **Weiter**.

    ![Planen einer Windows Server-Sicherung](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
3. Wenn Sie Elemente hinzufügen oder ändern möchten, klicken Sie auf dem Bildschirm **Elemente für die Sicherung auswählen** auf **Elemente hinzufügen**.

    Sie können auf dieser Seite des Assistenten auch **Ausschlusseinstellungen** festlegen. Wenn Sie Dateien oder Dateitypen ausschließen möchten, lesen Sie das Verfahren zum Hinzufügen von [Ausschlusseinstellungen](#manage-exclusion-settings).
4. Wählen Sie die Dateien und Ordner aus, die Sie sichern möchten, und klicken Sie auf **OK**.

    ![Planen einer Windows Server-Sicherung](./media/backup-azure-manage-windows-server/add-items-modify.png)
5. Geben Sie den **Sicherungszeitplan** an, und klicken Sie auf **Weiter**.

    Sie können tägliche (maximal drei pro Tag) oder wöchentliche Sicherungen planen.

    ![Elemente einer Windows Server-Sicherung](./media/backup-azure-manage-windows-server/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > Die Festlegung des Sicherungszeitplans wird in diesem [Artikel](backup-azure-backup-cloud-as-tape.md)ausführlich beschrieben.
   >

6. Wählen Sie die **Aufbewahrungsrichtlinie** für die Sicherungskopie aus, und klicken Sie auf **Weiter**.

    ![Elemente einer Windows Server-Sicherung](./media/backup-azure-manage-windows-server/select-retention-policy-modify.png)
7. Lesen Sie sich die Informationen auf dem Bildschirm **Bestätigung** durch, und klicken Sie auf **Fertig stellen**.
8. Nachdem der Assistent die Erstellung des **Sicherungszeitplans** abgeschlossen hat, klicken Sie auf **Schließen**.

    Nach dem Ändern der Einstellungen können Sie die richtige Auslösung der Sicherungen überprüfen, indem Sie zur Registerkarte **Aufträge** wechseln und sich vergewissern, dass die Änderungen in den Sicherungsaufträgen übernommen wurden.

## <a name="enable-network-throttling"></a>Aktivieren der Netzwerkdrosselung

Der Azure Backup-Agent verfügt über eine Registerkarte für die Drosselung, mit der Sie steuern können, wie die Netzwerkbandbreite während der Datenübertragung verwendet wird. Diese Steuerungsmöglichkeit kann hilfreich sein, wenn Sie Daten während der Geschäftszeiten sichern möchten, der Sicherungsprozess aber keine Auswirkung auf den weiteren Internetdatenverkehr haben soll. Die Drosselung der Datenübertragung gilt für Sicherungs- und Wiederherstellungsaktivitäten.  

So aktivieren Sie die Drosselung

1. Klicken Sie im **Backup-Agent** auf **Eigenschaften ändern**.
2. Wählen Sie auf der Registerkarte **Drosselung **Internet-Bandbreiteneinschränkung für Sicherungsvorgänge aktivieren**.

    ![Netzwerkdrosselung](./media/backup-azure-manage-windows-server/throttling-dialog.png)

    Nachdem Sie die Drosselung aktiviert haben, geben Sie die zulässige Bandbreite für die Sicherungsdatenübertragung für **Arbeitsstunden** und **Arbeitsfreie Stunden** an.

    Die Bandbreitenwerte beginnen bei 512 Kilobytes pro Sekunde (KBit/s) und können mit bis zu 1023 Megabytes pro Sekunde (MBit/s) angegeben werden. Sie können außerdem den Start und das Ende für die **Arbeitsstunden**angeben und festlegen, welche Tage der Woche als Arbeitstage betrachtet werden. Die Zeiten außerhalb der angegebenen Arbeitsstunden werden als arbeitsfreie Stunden betrachtet.
3. Klicken Sie auf **OK**.

## <a name="manage-exclusion-settings"></a>Verwalten von Ausschlusseinstellungen
1. Öffnen Sie den **Microsoft Azure Backup-Agent**. (Sie finden den Agent, indem Sie auf Ihrem Computer nach *Microsoft Azure Backup* suchen.)

    ![Planen einer Windows Server-Sicherung](./media/backup-azure-manage-windows-server/snap-in-search.png)
2. Klicken Sie im Microsoft Azure Backup-Agent auf **Sicherung planen**.

    ![Planen einer Windows Server-Sicherung](./media/backup-azure-manage-windows-server/schedule-backup.png)
3. Lassen Sie im Assistenten zum Planen von Sicherungen die Option **Sicherungselemente oder -zeiten ändern** aktiviert, und klicken Sie auf **Weiter**.

    ![Planen einer Windows Server-Sicherung](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
4. Klicken Sie auf **Ausschlusseinstellungen**.

    ![Planen einer Windows Server-Sicherung](./media/backup-azure-manage-windows-server/exclusion-settings.png)
5. Klicken Sie auf **Ausschluss hinzufügen**.

    ![Planen einer Windows Server-Sicherung](./media/backup-azure-manage-windows-server/add-exclusion.png)
6. Wählen Sie den Speicherort aus, und klicken Sie dann auf **OK**.

    ![Planen einer Windows Server-Sicherung](./media/backup-azure-manage-windows-server/exclusion-location.png)
7. Fügen Sie im Feld **Dateityp** die Dateierweiterung hinzu.

    ![Planen einer Windows Server-Sicherung](./media/backup-azure-manage-windows-server/exclude-file-type.png)

    Hinzufügen einer MP3-Erweiterung

    ![Planen einer Windows Server-Sicherung](./media/backup-azure-manage-windows-server/exclude-mp3.png)

    Um eine weitere Erweiterung hinzuzufügen, klicken Sie auf **Ausschluss hinzufügen** und geben eine weitere Dateityperweiterung ein (hier wird die JPEG-Erweiterung hinzugefügt).

    ![Planen einer Windows Server-Sicherung](./media/backup-azure-manage-windows-server/exclude-jpg.png)
8. Wenn Sie alle Erweiterungen hinzugefügt haben, klicken Sie auf **OK**.
9. Setzen Sie den Assistenten zum Planen von Sicherungen fort, indem Sie auf **Weiter** klicken, bis die Seite **Bestätigung** angezeigt wird. Klicken Sie anschließend auf **Fertig stellen**.

    ![Planen einer Windows Server-Sicherung](./media/backup-azure-manage-windows-server/finish-exclusions.png)

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen
**F1. Der Status des Sicherungsauftrags wird im Azure Backup-Agent als „Abgeschlossen“ angezeigt. Warum ist dies im Portal nicht sofort sichtbar?**

A1. Zwischen dem Anzeigen des Sicherungsauftragsstatus im Azure Backup-Agent und im Azure-Portal kommt es zu einer Verzögerung von maximal 15 Minuten.

**F2. Wie lange dauert es, bis eine Warnung ausgelöst wird, wenn ein Sicherungsauftrag nicht erfolgreich ist?**

A2. Innerhalb von 20 Minuten nach dem Azure-Sicherungsfehler wird eine Warnung ausgelöst.

**F3. Kann es vorkommen, dass eine E-Mail nicht gesendet wird, wenn Benachrichtigungen konfiguriert sind?**

A3. Nachfolgend sind Fälle aufgeführt, in denen die Benachrichtigung nicht gesendet wird, um das Warnungsaufkommen zu verringern:

* Stündliche Benachrichtigungen wurden konfiguriert, und eine Warnung wird ausgelöst und innerhalb dieser Stunde gelöst.
* Der Auftrag wird abgebrochen.
* Bei der zweiten Sicherung ist ein Fehler aufgetreten, weil der ursprüngliche Sicherungsauftrag noch ausgeführt wird.

## <a name="troubleshooting-monitoring-issues"></a>Problembehandlung bei der Überwachung
**Problem:** Aufträge und/oder Warnungen vom Azure Backup-Agent werden im Portal nicht angezeigt.

**Schritte zur Problembehandlung:** Der Prozess ```OBRecoveryServicesManagementAgent``` sendet den Auftrag und die Warnungsdaten an den Azure Backup-Dienst. Gelegentlich kann es bei diesem Prozess zu Unterbrechungen kommen, oder er kann ungewollt beendet werden.

1. Um sicherzustellen, dass der Prozess nicht gerade ausgeführt wird, öffnen Sie den **Task-Manager**, und überprüfen Sie, ob der Prozess ```OBRecoveryServicesManagementAgent``` gerade ausgeführt wird.
2. Wenn der Prozess nicht ausgeführt wird, öffnen Sie die **Systemsteuerung**, und durchsuchen Sie die Liste der Dienste. Starten Sie den **Microsoft Azure Recovery Services Management-Agent** bzw. starten Sie ihn neu.

    Weitere Informationen finden Sie in den Protokollen unter:<br/>
   Beispiel: `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*`<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Nächste Schritte
* [Wiederherstellen von Windows-Servern oder Windows-Clients aus Azure](backup-azure-restore-windows-server.md)
* Weitere Informationen zu Azure Backup finden Sie unter [Azure Backup – Übersicht](backup-introduction-to-azure-backup.md)
* Besuchen Sie das [Azure Backup-Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933)
