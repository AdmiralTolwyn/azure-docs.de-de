---
title: Verwalten von Updates für mehrere virtuelle Azure-Computer
description: In diesem Artikel wird beschrieben, wie Sie Updates für virtuelle Azure-Computer verwalten.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e06db4e356de6a4572721d1652d6a2666e7cfefc
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42143129"
---
# <a name="manage-updates-for-multiple-machines"></a>Verwalten von Updates für mehrere Computer

Sie können die Updateverwaltungslösung verwenden, um Updates und Patches für Ihre virtuellen Windows- und Linux-Computer zu verwalten. Über Ihr [Azure Automation](automation-offering-get-started.md)-Konto können Sie:

- Virtuelle Computer integrieren
- Den Status verfügbarer Updates bewerten
- Die Installation erforderlicher Updates planen
- Bereitstellungsergebnisse überprüfen, um sicherzustellen, dass Updates erfolgreich auf alle virtuellen Computer angewendet wurden, für die die Updateverwaltung aktiviert ist

## <a name="prerequisites"></a>Voraussetzungen

Zum Verwenden der Updateverwaltung benötigen Sie Folgendes:

- Ein ausführendes Azure Automation-Konto. Informationen zur Erstellung finden Sie unter [Erste Schritte mit Azure Automation](automation-offering-get-started.md).
- Einen virtuellen Computer bzw. einen Computer, auf dem eines der unterstützten Betriebssysteme installiert ist.

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Die Updateverwaltung wird für folgende Betriebssysteme unterstützt:

|Betriebssystem  |Notizen  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Unterstützt nur Updatebewertungen.         |
|Windows Server 2008 R2 SP1 und höher     |Windows PowerShell 4.0 oder höher ist erforderlich. ([WMF 4.0 herunterladen](https://www.microsoft.com/download/details.aspx?id=40855))</br> Für eine höhere Zuverlässigkeit wird Windows PowerShell 5.1 empfohlen. ([WMF 5.1 herunterladen](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) und 7 (x64)      | Für Linux-Agents muss Zugriff auf ein Updaterepository bestehen.        |
|Red Hat Enterprise 6 (x86/x64) und 7 (x64)     | Für Linux-Agents muss Zugriff auf ein Updaterepository bestehen.        |
|SUSE Linux Enterprise Server 11 (x86/x64) und 12 (x64)     | Für Linux-Agents muss Zugriff auf ein Updaterepository bestehen.        |
|Ubuntu 12.04 LTS, 14.04 LTS und 16.04 LTS (x86/x64)      |Für Linux-Agents muss Zugriff auf ein Updaterepository bestehen.         |

> [!NOTE]
> Damit unter Ubuntu keine Updates außerhalb der Wartungsfenster angewendet werden, konfigurieren Sie das „Unattended-Upgrade“-Paket erneut, um automatische Updates zu deaktivieren. Weitere Informationen finden Sie im [Thema zu automatischen Updates im Ubuntu-Serverhandbuch](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Für Linux-Agents muss Zugriff auf ein Updaterepository bestehen.

Diese Lösung unterstützt keinen OMS-Agent (Operations Management Suite) für Linux, der für die Berichterstattung an mehrere Azure Log Analytics-Arbeitsbereiche konfiguriert ist.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Aktivieren der Updateverwaltung für virtuelle Azure-Computer

Öffnen Sie im Azure-Portal Ihr Automation-Konto, und wählen Sie dann **Updateverwaltung** aus.

Wählen Sie **Azure-VMs hinzufügen** aus.

![Registerkarte „Azure-VM hinzufügen“](./media/manage-update-multi/update-onboard-vm.png)

Wählen Sie einen virtuellen Computer aus, der integriert werden soll. 

Wählen Sie unter **Updateverwaltung aktivieren** die Option **Aktivieren** aus, um den virtuellen Computer zu integrieren.

![Dialogfeld „Updateverwaltung aktivieren“](./media/manage-update-multi/update-enable.png)

Nach Abschluss der Integration ist die Updateverwaltung für Ihren virtuellen Computer aktiviert.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Aktivieren der Updateverwaltung für VMs und Computer, die nicht Azure unterstehen

Informationen zum Aktivieren der Updateverwaltung für Windows-VMs und -Computer, die nicht Azure unterstehen, finden Sie unter [Verbinden von Windows-Computern mit dem Log Analytics-Dienst in Azure](../log-analytics/log-analytics-windows-agent.md).

Informationen zum Aktivieren der Updateverwaltung für Azure-fremde Linux-VMs und -Computer finden Sie unter [Verbinden Ihrer Linux-Computer mit Log Analytics](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Anzeigen von Computern, die an Ihr Automation-Konto angefügt sind

Nachdem Sie die Updateverwaltung für Ihre Computer aktiviert haben, können Sie Computerinformationen anzeigen, indem Sie **Computer** auswählen. Folgende Informationen werden für Ihre Computer angezeigt: *Computername*, *Konformitätsstatus*, *Umgebung*, *Betriebssystemtyp*, *installierte kritische Updates und Sicherheitsupdates*, *andere installierte Updates* und *Bereitschaft des Update-Agents*.

  ![Registerkarte „Computer“](./media/manage-update-multi/update-computers-tab.png)

Für Computer, für die die Updateverwaltung erst vor Kurzem aktiviert wurde, ist ggf. noch keine Bewertung vorhanden. Der Konformitätsstatus dieser Computer lautet **Nicht bewertet**. Es folgt eine Liste mit möglichen Werten für den Konformitätsstatus:

- **Konform**: Computer, für die keine kritischen Updates oder Sicherheitsupdates fehlen.

- **Nicht konform**: Computer, für die mindestens ein kritisches Update oder Sicherheitsupdate fehlt.

- **Nicht bewertet**: Die Daten für die Updatebewertung wurden vom Computer nicht innerhalb des erwarteten Zeitrahmens empfangen. Bei Linux-Computer liegt der erwartete Zeitrahmen in den letzten 3 Stunden. Bei Windows-Computer liegt der erwartete Zeitrahmen in den letzten 12 Stunden.

Klicken Sie zum Anzeigen des Agent-Status auf den Link in der Spalte **BEREITSCHAFT DES UPDATE-AGENTS**. Bei Auswahl dieser Option wird der Bereich **Hybrid Worker** geöffnet und der Status des Hybrid Worker angezeigt. In der folgenden Abbildung wird ein Beispiel für einen Agent gezeigt, der über einen längeren Zeitraum nicht mit der Updateverwaltung verbunden war:

![Registerkarte „Computer“](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Anzeigen einer Updatebewertung

Sobald die Updateverwaltung aktiviert ist, wird der Bereich **Updateverwaltung** angezeigt. Auf der Registerkarte **Fehlende Updates** wird eine Liste der fehlenden Updates angezeigt.

## <a name="collect-data"></a>Sammeln von Daten

Mit Agents, die auf VMs und Computern installiert sind, werden Daten zu Updates gesammelt. Die Agents senden die Daten an die Azure-Updateverwaltung.

### <a name="supported-agents"></a>Unterstützte Agents

In der folgenden Tabelle sind die verbundenen Quellen beschrieben, die von dieser Lösung unterstützt werden:

| Verbundene Quelle | Unterstützt | BESCHREIBUNG |
| --- | --- | --- |
| Windows-Agents |JA |Die Updateverwaltung sammelt Informationen zu Systemupdates von Windows-Agents und initiiert dann die Installation von erforderlichen Updates. |
| Linux-Agents |JA |Die Updateverwaltung sammelt Informationen zu Systemupdates von Linux-Agents und initiiert dann die Installation von erforderlichen Updates für unterstützte Distributionen. |
| Operations Manager-Verwaltungsgruppe |JA |Die Updateverwaltung sammelt Informationen zu Systemupdates von Agents in einer verbundenen Verwaltungsgruppe. |
| Azure-Speicherkonto |Nein  |Azure Storage enthält keine Informationen zu Systemupdates. |

### <a name="collection-frequency"></a>Sammlungshäufigkeit

Zweimal pro Tag wird ein Scanvorgang für jeden verwalteten Windows-Computer ausgeführt. Alle 15 Minuten wird die Windows-API aufgerufen, um den letzten Updatezeitpunkt abzufragen und zu ermitteln, ob sich der Status geändert hat. Wenn sich der Status geändert hat, wird ein Konformitätsscan gestartet. Alle drei Stunden wird ein Scanvorgang für jeden verwalteten Linux-Computer ausgeführt.

Es kann zwischen 30 Minuten und sechs Stunden dauern, bis im Dashboard aktualisierte Daten von verwalteten Computern angezeigt werden.

## <a name="schedule-an-update-deployment"></a>Planen einer Updatebereitstellung

Planen Sie zum Installieren von Updates eine Bereitstellung, die Ihrem Releasezeitplan und Wartungsfenster entspricht. Sie können auswählen, welche Updatetypen in die Bereitstellung eingeschlossen werden sollen. Beispielsweise können Sie kritische oder Sicherheitsupdates einschließen und Updaterollups ausschließen.

Um eine neue Updatebereitstellung für einen oder mehrere virtuelle Computer zu planen, wählen Sie unter **Updateverwaltung** die Option **Updatebereitstellung planen** aus.

Geben Sie im Bereich **Neue Updatebereitstellung** die folgenden Informationen ein:

- **Name**: Geben Sie einen eindeutigen Namen zur Identifizierung der Updatebereitstellung ein.
- **Betriebssystem**: Wählen Sie **Windows** oder **Linux** aus.
- **Zu aktualisierende Computer**: Wählen Sie eine gespeicherte Suche, eine importierte Gruppe oder Computer aus, um die Computer auszuwählen, die Sie aktualisieren möchten. Wenn Sie **Computer** auswählen, wird die Bereitschaft des Computers in der Spalte **BEREITSCHAFT DES UPDATE-AGENTS** angezeigt. Sie können den Integritätsstatus des Computers sehen, bevor Sie die Updatebereitstellung planen. Weitere Informationen zu den verschiedenen Methoden zum Erstellen von Computergruppen in Log Analytics finden Sie unter [Computergruppen in Log Analytics](../log-analytics/log-analytics-computer-groups.md).

  ![Bereich „Neue Updatebereitstellung“](./media/manage-update-multi/update-select-computers.png)

- **Updateklassifizierung**: Wählen Sie die Softwaretypen aus, die in die Updatebereitstellung eingeschlossen werden sollen. Eine Beschreibung der Klassifizierungstypen finden Sie unter [Updateklassifizierungen](automation-update-management.md#update-classifications). Es gibt die folgenden Klassifizierungstypen:
  - Kritische Updates
  - Sicherheitsupdates
  - Updaterollups
  - Feature Packs
  - Service Packs
  - Definitionsupdates
  - Tools
  - Aktualisierungen

- **Auszuschließende Updates**: Bei Auswahl dieser Option wird die Seite **Ausschließen** geöffnet. Geben Sie die auszuschließenden KB-Artikel oder Paketnamen ein.

- **Zeitplaneinstellungen**: Sie können das Standarddatum und die Standarduhrzeit (30 Minuten nach der aktuellen Zeit) übernehmen. Sie können auch eine andere Zeit angeben.

   Sie können auch angeben, ob die Bereitstellung einmalig erfolgt, oder einen sich wiederholenden Zeitplan einrichten. Wählen Sie zum Einrichten eines sich wiederholenden Zeitplans unter **Wiederholung** die Option **Serie** aus.

   ![Dialogfeld „Zeitplaneinstellungen“](./media/manage-update-multi/update-set-schedule.png)
- **Wartungsfenster (Minuten)**: Geben Sie den Zeitraum an, in dem die Updatebereitstellung stattfinden soll. Mit dieser Einstellung können Sie sicherstellen, dass Änderungen in den von Ihnen festgelegten Wartungsfenstern ausgeführt werden.

- **Neustartsteuerung**: Diese Einstellung bestimmt, wie Neustarts für die Updatebereitstellung behandelt werden.

   |Option|BESCHREIBUNG|
   |---|---|
   |Neustart bei Bedarf| **(Standard)**: Bei Bedarf wird ein Neustart eingeleitet, wenn das Wartungsfenster dies zulässt.|
   |Immer neu starten|Ein Neustart wird unabhängig davon eingeleitet, ob er erforderlich ist. |
   |Nie neu starten|Unabhängig davon, ob ein Neustart erforderlich ist, werden Neustarts unterdrückt.|
   |Nur neu starten – Updates werden nicht installiert|Diese Option ignoriert die Installation von Updates und leitet nur einen Neustart ein.|

Nachdem Sie die Konfiguration des Zeitplans abgeschlossen haben, klicken Sie auf die Schaltfläche **Erstellen**, um zum Statusdashboard zurückzukehren. Die Tabelle **Geplant** zeigt den von Ihnen erstellten Bereitstellungszeitplan.

## <a name="view-results-of-an-update-deployment"></a>Anzeigen der Ergebnisse einer Updatebereitstellung

Nach dem Start der geplanten Bereitstellung sehen Sie den Status der Bereitstellung auf der Registerkarte **Updatebereitstellungen** unter **Updateverwaltung**.

Wenn die Bereitstellung derzeit ausgeführt wird, lautet der Status **In Bearbeitung**. Nachdem die Bereitstellung erfolgreich abgeschlossen wurde, ändert sich der Status in **Erfolgreich**.

Wenn bei einem oder mehreren Updates in der Bereitstellung ein Fehler auftritt, wird der Status **Der Vorgang ist teilweise fehlgeschlagen** angezeigt.

![Status der Updatebereitstellung](./media/manage-update-multi/update-view-results.png)

Klicken Sie auf die abgeschlossene Bereitstellung, um das Dashboard für eine Updatebereitstellung anzuzeigen.

Im Bereich **Updateergebnisse** werden die Gesamtzahl von Updates und die Ergebnisse der Bereitstellung für den virtuellen Computer angezeigt. Die Tabelle rechts enthält eine detaillierte Aufschlüsselung der einzelnen Updates und die Installationsergebnisse. Bei den Installationsergebnissen kann es sich um einen der folgenden Werte handeln:

- **Kein Versuch erfolgt**: Das Update wurde nicht installiert, da aufgrund des definierten Wartungsfensters nicht genügend Zeit zur Verfügung stand.
- **Erfolgreich**: Das Update wurde erfolgreich ausgeführt.
- **Fehler**: Beim Update ist ein Fehler aufgetreten.

Klicken Sie auf **Alle Protokolle**, um alle von der Bereitstellung erstellten Protokolleinträge anzuzeigen.

Klicken Sie auf die Ausgabekachel, um den Auftragsdatenstrom des Runbooks anzuzeigen, das die Updatebereitstellung auf der Ziel-VM verwaltet.

Klicken Sie auf **Fehler**, um ausführliche Informationen zu Fehlern bei der Bereitstellung anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Updateverwaltung, einschließlich der Protokolle, Ausgabe und Fehler, finden Sie unter [Lösung für die Updateverwaltung in Azure](../operations-management-suite/oms-solution-update-management.md).
