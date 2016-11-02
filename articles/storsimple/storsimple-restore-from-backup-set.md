<properties 
   pageTitle="Wiederherstellen eines StorSimple-Volumes aus einer Sicherung | Microsoft Azure"
   description="Erläutert, wie Sie die Seite ";Sicherungskatalog"; des StorSimple Manager-Diensts zum Wiederherstellen eines StorSimple-Volumes aus einem Sicherungssatz verwenden."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# Wiederherstellen eines StorSimple-Volumes aus einem Sicherungssatz

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## Übersicht

Auf der Seite **Sicherungskatalog** werden alle Sicherungssätze angezeigt, die mithilfe manueller oder automatisierter Sicherungen erstellt wurden. Sie können auf dieser Seite alle Sicherungen für eine Sicherungsrichtlinie oder ein Volume auflisten, Sicherungen auswählen oder löschen oder eine Sicherung zum Wiederherstellen oder Klonen eines Volumes verwenden.

 ![Seite "Sicherungskatalog"](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

In diesem Tutorial erfahren Sie, wie Sie ein Volume auf Ihrem Gerät mithilfe der Seite **Sicherungskatalog** aus einem Sicherungssatz wiederherstellen.

## So verwenden Sie den Sicherungskatalog 

Die Seite **Sicherungskatalog** bietet eine Abfrage, mit der Sie die Auswahl der Sicherungssätze einschränken können. Sie können die abgerufenen Sicherungssätze anhand der folgenden Parameter filtern:

- **Gerät** – das Gerät, auf dem der Sicherungssatz erstellt wurde.
- **Sicherungsrichtlinie** oder **Volume** – die Sicherungsrichtlinie oder das Volume, der oder dem dieser Sicherungssatz zugeordnet ist.
- **Von** und **Bis** – der Datums- und Uhrzeitbereich, in dem die Sicherung erstellt wurde.

Die gefilterten Sicherungssätze werden dann basierend auf den folgenden Attributen in Tabellenform angezeigt:

- **Name** – der Name der Sicherungsrichtlinie oder des Volumes, der oder dem dieser Sicherungssatz zugeordnet ist.
- **Größe** – die tatsächliche Größe des Sicherungssatzes.
- **Erstellt am** – das Datum und die Uhrzeit der Erstellung der Sicherungen.
- **Typ** – Sicherungssätze können lokale Momentaufnahmen oder Cloudmomentaufnahmen sein. Eine lokale Momentaufnahme ist eine Sicherung aller Volumedaten, die auf dem lokalen Gerät gespeichert ist, während die Sicherung von Volumedaten in der Cloud als Cloudmomentaufnahme bezeichnet wird. Lokale Momentaufnahmen bieten schnelleren Zugriff, während Cloudmomentaufnahmen für Datenstabilität ausgewählt werden.
- **Initiiert von** – die Sicherungen können automatisch nach einem Zeitplan oder manuell durch einen Benutzer initiiert werden. (Sie können eine Sicherungsrichtlinie verwenden, um Sicherungen zu planen. Es ist aber auch möglich, mithilfe der Option **Sicherung erstellen** eine interaktive Sicherung durchzuführen.)

## So stellen Sie Ihr StorSimple-Volume aus einer Sicherung wieder her

Sie können Ihr StorSimple-Volume auf der Seite **Sicherungskatalog** aus einer bestimmten Sicherung wiederherstellen.

> [AZURE.WARNING] Beim Wiederherstellen aus einer Sicherung werden die vorhandenen Volumes durch die Sicherung ersetzt. Dadurch können Daten verloren gehen, die nach dem Erstellen der Sicherung geschrieben wurden.

Bevor Sie eine Wiederherstellung auf einem Volume initiieren, stellen Sie sicher, dass das Volume offline ist. Sie müssen das Volume erst auf dem Host, dann auf dem Gerät offline schalten. Führen Sie die unter [Offlineschalten von Volumes](storsimple-manage-volumes.md#take-a-volume-offline) genannten Schritte aus. Führen Sie die folgenden Schritte aus, um ein Volume aus einem Sicherungssatz wiederherzustellen.

### So führen Sie eine Wiederherstellung aus einem Sicherungssatz durch

1. Klicken Sie auf der Seite des StorSimple-Manager-Diensts auf die Registerkarte **Sicherungskatalog**.

    ![Sicherungskatalog](./media/storsimple-restore-from-backup-set/HCS_Restore.png)

2. Wählen Sie wie folgt einen Sicherungssatz aus:
  1. Wählen Sie das entsprechende Gerät aus.
  2. Wählen Sie in der Dropdownliste das Volume oder die Sicherungsrichtlinie für die gewünschte Sicherung aus.
  3. Geben Sie den Zeitraum an.
  4. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png), um diese Abfrage durchzuführen.
 
    Die dem ausgewählten Volume oder der Sicherungsrichtlinie zugeordneten Sicherungen sollten in der Liste der Sicherungssätze angezeigt werden.

3. Erweitern Sie einen Sicherungssatz, um die zugehörigen Volumes anzuzeigen. Diese Volumes müssen auf dem Host und dem Gerät offline geschaltet werden, bevor sie wiederhergestellt werden können. Führen Sie die unter [Offlineschalten von Volumes](storsimple-manage-volumes.md#take-a-volume-offline) genannten Schritte aus.

    >  [AZURE.IMPORTANT] Vergewissern Sie sich, dass die Volumes auf dem Host offline sind, bevor Sie diese auf dem Gerät offline schalten. Wenn Sie die Volumes auf dem Host nicht offline schalten, kann es zur Beschädigung von Daten kommen.

4. Wählen Sie einen Sicherungssatz aus. Klicken Sie unten auf der Seite auf **Wiederherstellen**.

6. Sie werden aufgefordert, diesen Schritt zu bestätigen.

    ![Bestätigungsseite](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)

7. Überprüfen Sie die Informationen für die Wiederherstellung, und klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png). Damit initiieren Sie einen Wiederherstellungsauftrag, den Sie auf der Seite **Jobs** anzeigen können.

8. Nachdem die Wiederherstellung abgeschlossen ist, können Sie überprüfen, ob die Inhalte der Volumes durch die aus der Sicherung ersetzt wurden.

![Video verfügbar](./media/storsimple-restore-from-backup-set/Video_icon.png) **Video verfügbar**

Um ein Video zu schauen, in dem gezeigt wird, wie Sie mithilfe des Klons und Wiederherstellungsfunktionen in StorSimple gelöschte Dateien wiederherstellen können, klicken Sie [hier](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## Nächste Schritte

- Erfahren Sie, wie Sie [StorSimple-Volumes verwalten](storsimple-manage-volumes.md).

- Erfahren Sie, wie Sie [Ihr StorSimple-Gerät mithilfe des StorSimple Manager-Diensts verwalten](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0824_2016-->