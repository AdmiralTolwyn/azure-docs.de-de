---
title: "Erstellen von Wiederherstellungsplänen für Failover und Wiederherstellung in Azure Site Recovery | Microsoft-Dokumentation"
description: "Beschreibt das Erstellen und Anpassen von Wiederherstellungsplänen in Azure Site Recovery für Failover und Wiederherstellung von virtuellen Computern und physischen Servern"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/25/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: 81d8a6e3015ddc4241cce8e888d51d6e2b2cb173
ms.contentlocale: de-de
ms.lasthandoff: 09/27/2017

---
# <a name="create-recovery-plans"></a>Erstellen von Wiederherstellungsplänen


Dieser Artikel enthält Informationen zum Erstellen und Anpassen von Wiederherstellungsplänen in [Azure Site Recovery](site-recovery-overview.md).

Kommentare oder Fragen können Sie am Ende dieses Artikels oder im [Forum zu Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)veröffentlichen.

 Erstellen Sie Wiederherstellungspläne für folgende Anwendungsbereiche:

* Definieren von Gruppen von Computern, für die das Failover und das anschließende Starten zusammen ausgeführt werden.
* Modellieren von Abhängigkeiten zwischen Computern, indem sie in einer Wiederherstellungsplangruppe zusammengefasst werden Beispiel: Zum Ausführen eines Failovers und Erstellen einer spezifischen Anwendung gruppieren Sie alle virtuellen Computer für diese Anwendung in derselben Wiederherstellungsplangruppe.
* Ausführen eines Failovers. Sie können für einen Wiederherstellungsplan ein Testfailover oder ein geplantes bzw. ungeplantes Failover durchführen.


## <a name="create-a-recovery-plan"></a>Erstellen eines Wiederherstellungsplans

1. Klicken Sie auf **Wiederherstellungspläne** > **Wiederherstellungsplan erstellen**.
   Geben Sie einen Namen für den Wiederherstellungsplan sowie die Quelle und das Ziel an. Der Quellspeicherort muss virtuelle Computer aufweisen, die für Failover und Wiederherstellung aktiviert sind.

    - Zur Replikation von VMM zu VMM wählen Sie **Quelltyp** > **VMM** und die VMM-Quell- und -Zielserver aus. Klicken Sie auf **Hyper-V**, um geschützte Clouds anzuzeigen.
    - Für VMM zu Azure wählen Sie **Quelltyp** > **VMM**.  Wählen Sie den VMM-Quellserver und als Ziel **Azure** aus.
    - Zur Hyper-V-Replikation zu Azure (ohne VMM) wählen Sie **Quelltyp** > **Hyper-V-Site** aus. Wählen Sie den Standort als Quelle und als Ziel **Azure**  aus.
    - Bei der Replikation eines virtuellen VMware-Computers oder eines physischen lokalen Servers zu Azure wählen Sie einen Konfigurationsserver als Quelle und als Ziel **Azure** aus.
    - Wählen Sie für einen Azure-zu-Azure-Wiederherstellungsplan eine Azure-Region als Quelle und eine sekundäre Azure-Region als Ziel aus. Die sekundären Azure-Regionen sind nur die Regionen, die zum Schutz virtueller Computer dienen.
2. Wählen Sie unter **Virtuelle Computer auswählen** die virtuellen Computer (oder die Replikationsgruppe) aus, die Sie der Standardgruppe (Gruppe 1) im Wiederherstellungsplan hinzufügen möchten.

## <a name="customize-and-extend-recovery-plans"></a>Anpassen und Erweitern von Wiederherstellungsplänen

Sie können Wiederherstellungspläne anpassen und erweitern:

- **Hinzufügen neuer Gruppen:** Fügen Sie (bis zu sieben) zusätzliche Wiederherstellungsplangruppen zur Standardgruppe hinzu, und fügen Sie dann weitere Computer oder Replikationsgruppen zu diesen Wiederherstellungsplangruppen hinzu. Die Gruppen sind in der Reihenfolge nummeriert, in der sie von Ihnen hinzugefügt werden. Ein virtueller Computer oder eine Replikationsgruppe kann nur in eine Wiederherstellungsplangruppe eingefügt werden.
- **Hinzufügen einer manuellen Aktion**: Sie können manuelle Aktionen hinzufügen, die vor oder nach einer Wiederherstellungsplangruppe ausgeführt werden. Wenn der Wiederherstellungsplan ausgeführt wird, wird er an dem Punkt angehalten, an dem Sie die manuelle Aktion eingefügt haben. Ein Dialogfeld fordert Sie auf anzugeben, dass die manuelle Aktion abgeschlossen wurde.
- **Hinzufügen eines Skripts:** Sie können Skripts für die Ausführung vor oder nach einer Wiederherstellungsplangruppe hinzufügen. Hierbei wird für die Gruppe ein neuer Satz mit Aktionen hinzugefügt. Eine Gruppe von Vorabschritten für „Group 1“ wird beispielsweise mit dem folgenden Namen erstellt: „Group 1: Pre-steps“. Alle Vorabschritte werden in diesem Satz aufgelistet. Sie können am primären Standort nur dann ein Skript hinzufügen, wenn Sie einen VMM-Server bereitgestellt haben.
- **Hinzufügen von Azure-Runbooks:** Sie können Wiederherstellungspläne mit Azure-Runbooks erweitern, beispielsweise zum Automatisieren von Aufgaben oder zum Erstellen einer Wiederherstellung in einem Schritt. [detaillierte Kapazitätsplanung](site-recovery-runbook-automation.md)

## <a name="add-scripts"></a>Hinzufügen von Skripts

Sie können PowerShell-Skripts in Ihren Wiederherstellungsplänen verwenden.

 - Achten Sie darauf, dass in Skripts try/catch-Blöcke verwendet werden, damit die Ausnahmen ordnungsgemäß behandelt werden.
    - Falls im Skript eine Ausnahme auftritt, wird die Ausführung gestoppt, und für die Aufgabe wird ein Fehler angezeigt.
    - Wenn ein Fehler auftritt, werden keine verbleibenden Teile des Skripts ausgeführt.
    - Wenn beim Ausführen eines ungeplanten Failovers ein Fehler auftritt, wird der Wiederherstellungsplan fortgesetzt.
    - Wenn beim Ausführen eines geplanten Failovers ein Fehler auftritt, wird der Wiederherstellungsplan angehalten. Sie müssen das Skript korrigieren, überprüfen, ob es wie erwartet ausgeführt wird, und den Wiederherstellungsplan dann erneut ausführen.
- Der Write-Host-Befehl funktioniert in einem Wiederherstellungsplanskript nicht, und die Ausführung des Skripts ist nicht erfolgreich. Um die Ausgabe zu erstellen, erstellen Sie ein Proxyskript, das wiederum Ihr Hauptskript ausführt. Stellen Sie sicher, dass alle Ausgaben mithilfe des Befehls „>>“ weitergeleitet werden.
  * Die Zeitüberschreitung für das Skript wird erreicht, falls die Rückgabe nicht innerhalb von 600 Sekunden erfolgt.
  * Falls etwas in STDERR geschrieben wird, wird das Skript als nicht erfolgreich klassifiziert. Diese Informationen werden in den Details zur Skriptausführung angezeigt.

Wenn Sie VMM in Ihrer Bereitstellung verwenden:

* Skripts in einem Wiederherstellungsplan werden im Kontext des VMM-Dienstkontos ausgeführt. Stellen Sie sicher, dass dieses Konto über Leseberechtigungen für die Remotefreigabe verfügt, auf der sich das Skript befindet. Testen Sie, ob das Skript auf der Berechtigungsstufe für VMM-Dienstkonten ausgeführt werden kann.
* VMM-Cmdlets werden in einem Windows PowerShell-Modul bereitgestellt. Das Modul wird installiert, wenn Sie die VMM-Konsole installieren. Es kann mit folgendem Befehl in Ihr Skript geladen werden:
   - Import-Module -Name virtualmachinemanager. [detaillierte Kapazitätsplanung](https://technet.microsoft.com/library/hh875013.aspx)
* Stellen Sie sicher, dass Ihre VMM-Bereitstellung mindestens einen Bibliothekserver enthält. Standardmäßig befindet sich der Bibliotheksfreigabepfad für einen VMM-Server lokal auf dem VMM-Server mit dem Ordnernamen „MSCVMMLibrary“.
    * Wenn Ihr Bibliotheksfreigabepfad remote vorhanden ist (oder lokal, aber nicht für „MSCVMMLibrary“ freigegeben), konfigurieren Sie die Freigabe wie folgt (hier dient „\\libserver2.contoso.com\share\“ als Beispiel):
      * Öffnen Sie den Registrierungseditor, und navigieren Sie zu **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.
      * Bearbeiten Sie den Wert **ScriptLibraryPath**, indem Sie dafür \\libserver2.contoso.com\share\. festlegen. Geben Sie den vollqualifizierten Domänennamen vollständig ein. Geben Sie die Berechtigungen für den Speicherort der Freigabe an. Beachten Sie, dass dies der Stammknoten der Freigabe ist. **Sie können die Bibliothek im Stammknoten in VMM öffnen, um dies zu überprüfen. Der Pfad, der geöffnet wird, ist der Stamm des Pfads, den Sie in der Variablen verwenden müssen**.
      * Stellen Sie sicher, dass Sie das Skript mit einem Benutzerkonto testen, das über dieselben Berechtigungen wie das VMM-Dienstkonto verfügt. Damit wird überprüft, ob eigenständige getestete Skripts auf dieselbe Weise wie in Wiederherstellungsplänen ausgeführt werden. Legen Sie für die Ausführungsrichtlinie auf dem VMM-Server wie folgt eine Umleitung fest:
        * Öffnen Sie die **Windows PowerShell-Konsole (64 Bit)** mit erweiterten Berechtigungen.
        * Geben Sie Folgendes ein: **Set-executionpolicy bypass**. [Weitere Informationen](https://technet.microsoft.com/library/ee176961.aspx)

> [!IMPORTANT]
> Sie sollten nur für 64-Bit-PowerShell für die Ausführungsrichtlinie „Bypass“ festlegen. Wenn dies für das 32-Bit-PowerShell festgelegt wurde, werden die Skripts nicht ausgeführt.

## <a name="add-a-script-or-manual-action-to-a-plan"></a>Hinzufügen eines Skripts oder einer manuellen Aktion zu einem Plan

Sie können ein Skript zur standardmäßigen Wiederherstellungsplangruppe hinzufügen, nachdem Sie die virtuellen Computer oder Replikationsgruppen hinzugefügt und den Plan erstellt haben.

1. Öffnen Sie den Wiederherstellungsplan.
2. Klicken Sie auf ein Element in der Liste **Schritte**, und klicken Sie dann auf **Skript** oder **Manuelle Aktion**.
3. Geben Sie an, ob Sie das Skript oder die Aktion vor oder nach dem ausgewählten Eintrag hinzufügen möchten. Verwenden Sie die Schaltflächen **Nach oben** und **Nach unten**, um die Position des Skripts nach oben oder unten zu verschieben.
4. Wenn Sie ein VMM-Skript hinzufügen, wählen Sie **Failover to VMM script** (Failover zu VMM-Skript) aus. Geben Sie unter **Skriptpfad** den relativen Pfad zur Freigabe ein. Geben Sie im VMM-Beispiel unten folgenden Pfad an: **\RPScripts\RPScript.PS1**.
5. Wenn Sie ein Azure-Automatisierungsrunbook hinzufügen, geben Sie das Azure Automation-Konto an, unter dem sich das Runbook befindet, und wählen das gewünschte Azure-Runbookskript aus.
6. Führen Sie ein Failover für den Wiederherstellungsplan aus, um sicherzustellen, dass das Skript wie erwartet funktioniert.


### <a name="add-a-vmm-script"></a>Hinzufügen eines VMM-Skripts

Falls Sie über eine VMM-Quellwebsite verfügen, können Sie ein Skript auf dem VMM-Server erstellen und es in Ihren Wiederherstellungsplan einfügen.

1. Erstellen Sie einen neuen Ordner in der Bibliothekfreigabe. Beispiel: \<VMMServerName>\MSSCVMMLibrary\RPScripts. Platzieren Sie diese Datei auf dem VMM-Quell- und -Zielserver.
2. Erstellen Sie das Skript (z. B. RPScript), und überprüfen Sie, ob es wie erwartet funktioniert.
3. Platzieren Sie das Skript am Speicherort „\<VMMServerName>\MSSCVMMLibrary“ auf den Quell- und Ziel-VMM-Servern.


## <a name="next-steps"></a>Nächste Schritte

[Weitere Informationen](site-recovery-failover.md) zum Ausführen von Failovern

