---
title: Ausführen von Runbooks in Azure Automation
description: Beschreibt ausführlich, wie ein Runbook in Azure Automation verarbeitet wird.
services: automation
documentationcenter: ''
author: mgoedtel
manager: stevenka
editor: tysonn

ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2016
ms.author: bwren

---
# Ausführen von Runbooks in Azure Automation
Wenn Sie ein Runbook in Azure Automation starten, wird ein Auftrag erstellt. Ein Auftrag ist eine einzelne Ausführungsinstanz eines Runbooks. Für die Ausführung jedes Auftrags wird ein Azure Automation-Worker zugewiesen. Wenngleich Worker von mehreren Azure-Konten gemeinsam genutzt werden, sind die Aufträge von verschiedenen Automation-Konten voneinander isoliert. Sie können nicht steuern, welcher Worker die Anforderung für Ihren Auftrag verarbeitet. Für ein einzelnes Runbook können mehrere Aufträge gleichzeitig ausgeführt werden. Wenn Sie die Liste der Runbooks im Azure-Portal anzeigen, wird der Status des letzten Auftrags aufgelistet, der für jedes Runbook gestartet wurde. Sie können die Liste der Aufträge für jedes Runbook anzeigen, um den Status der einzelnen Aufträge nachzuverfolgen. Eine Beschreibung der verschiedenen Auftragsstatusangaben finden Sie unter [Auftragsstatuswerte](#job-statuses).

Das folgende Diagramm zeigt den Lebenszyklus eines Runbookauftrags für [grafische Runbooks](automation-runbook-types.md#graphical-runbooks) und [PowerShell-Workflow-Runbooks](automation-runbook-types.md#powershell-workflow-runbooks).

![Auftragsstatus – PowerShell-Workflow](./media/automation-runbook-execution/job-statuses.png)

Das folgende Diagramm zeigt den Lebenszyklus eines Runbookauftrags für [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks).

![Auftragsstatus – PowerShell-Skript](./media/automation-runbook-execution/job-statuses-script.png)

Ihre Aufträge können auf Ihre Azure-Ressourcen zugreifen, indem sie eine Verbindung mit Ihrem Azure-Abonnement herstellen. Sie besitzen nur Zugriff auf Ressourcen in Ihrem Datencenter, wenn auf diese Ressourcen aus der öffentlichen Cloud zugegriffen werden kann.

## Auftragsstatus
Die folgende Tabelle beschreibt die verschiedenen Status, die für einen Auftrag möglich sind.

| Status | Beschreibung |
|:--- |:--- |
| Abgeschlossen |Der Auftrag wurde erfolgreich abgeschlossen. |
| Fehler |Bei [grafischen und PowerShell-Workflow-Runbooks](automation-runbook-types.md) ist die Kompilierung des Runbooks fehlgeschlagen. Bei [PowerShell-Skript-Runbooks](automation-runbook-types.md) konnte das Runbook nicht gestartet werden, oder bei dem Auftrag ist eine Ausnahme aufgetreten. |
| Fehler, auf Ressourcen wird gewartet |Beim Auftrag ist ein Fehler aufgetreten, da der Auftrag dreimal den Grenzwert für die [gleichmäßige Verteilung](#fairshare) erreicht hat und jedes Mal vom gleichen Prüfpunkt oder vom Anfang des Runbooks gestartet wurde. |
| In Warteschlange |Der Auftrag wartet darauf, dass Ressourcen für einen Automation-Worker verfügbar werden, damit er gestartet werden kann. |
| Wird gestartet |Der Auftrag wurde einem Worker zugewiesen, und das System ist in Begriff, ihn zu starten. |
| Wird fortgesetzt |Das System ist in Begriff, den Auftrag fortzusetzen, nachdem er angehalten wurde. |
| Wird ausgeführt |Der Auftrag wird ausgeführt. |
| Wird ausgeführt, auf Ressourcen wird gewartet |Der Auftrag wurde entladen, da er den Grenzwert für die [gleichmäßige Verteilung](#fairshare) erreicht hat. Er wird in Kürze vom letzten Prüfpunkt wiederaufgenommen. |
| Beendet |Der Auftrag wurde vom Benutzer beendet, bevor er abgeschlossen wurde. |
| Wird beendet |Das System ist in Begriff, den Auftrag zu beenden. |
| Ausgesetzt |Der Auftrag wurde vom Benutzer, vom System oder von einem Befehl im Runbook angehalten. Ein Auftrag, der angehalten wurde, kann erneut gestartet werden und wird vom letzten Prüfpunkt oder vom Anfang des Runbooks fortgesetzt, wenn er keine Prüfpunkte besitzt. Das Runbook wird nur im Fall einer Ausnahme vom System angehalten. Standardmäßig ist "ErrorActionPreference" auf **Continue** festgelegt. Dies bedeutet, dass der Auftrag bei einem Fehler weiterhin ausgeführt wird. Wenn diese Einstellungsvariable auf **Stop** festgelegt wird, wird der Auftrag bei einem Fehler angehalten. Gilt nur für [grafische und PowerShell-Workflow-Runbooks](automation-runbook-types.md). |
| Wird angehalten |Das System versucht, den Auftrag auf Anforderung des Benutzers anzuhalten. Das Runbook muss den nächsten Prüfpunkt erreichen, bevor es angehalten werden kann. Wenn der letzte Prüfpunkt bereits verstrichen ist, wird das Runbook abgeschlossen, bevor es angehalten werden kann. Gilt nur für [grafische und PowerShell-Workflow-Runbooks](automation-runbook-types.md). |

## Anzeigen des Auftragsstatus über das Azure-Verwaltungsportal
### Automation Dashboard
Das Automation Dashboard zeigt eine Zusammenfassung aller Runbooks für ein bestimmtes Automation-Konto. Es enthält auch eine Nutzungsübersicht für das Konto. Das Zusammenfassungsdiagramm zeigt die Gesamtzahl der Aufträge für alle Runbooks, die während einer bestimmten Anzahl von Tagen oder Stunden den jeweiligen Status erreicht haben. Sie können in der oberen rechten Ecke des Diagramms einen Zeitraum auswählen. Die Zeitachse des Diagramms ändert sich entsprechend der Art des ausgewählten Zeitraums. Sie können die Zeitlinie für einen bestimmten Status anzeigen, indem Sie am oberen Bildschirmrand auf den Status klicken.

Zeigen Sie das Automation Dashboard mithilfe der folgenden Schritte an.

1. Wählen Sie im Azure-Verwaltungsportal die Option **Automation**, und klicken Sie auf den Namen eines Automation-Kontos.
2. Klicken Sie auf die Registerkarte **Dashboard**.

### Runbook-Dashboard
Das Runbook-Dashboard zeigt eine Zusammenfassung für ein einzelnes Runbook. Das Zusammenfassungsdiagramm zeigt die Gesamtzahl der Aufträge für das Runbook, die während einer bestimmten Anzahl von Tagen oder Stunden den jeweiligen Status erreicht haben. Sie können in der oberen rechten Ecke des Diagramms einen Zeitraum auswählen. Die Zeitachse des Diagramms ändert sich entsprechend der Art des ausgewählten Zeitraums. Sie können die Zeitlinie für einen bestimmten Status anzeigen, indem Sie am oberen Bildschirmrand auf den Status klicken.

Zeigen Sie das Runbook-Dashboard mithilfe der folgenden Schritte an.

1. Wählen Sie im Azure-Verwaltungsportal die Option **Automation**, und klicken Sie auf den Namen eines Automation-Kontos.
2. Klicken Sie auf den Namen eines Runbooks.
3. Klicken Sie auf die Registerkarte **Dashboard**.

### API-Zusammenfassung
Sie können eine Liste aller Aufträge, die für ein bestimmtes Runbook erstellt wurden, sowie deren aktuellen Status anzeigen. Sie können diese Liste nach Auftragsstatus und dem Datumsbereich für die letzte Änderung des Auftrags filtern. Klicken Sie auf den Namen eines Auftrags, um detaillierte Informationen und die Auftragsausgabe anzuzeigen. Die Detailansicht des Auftrags enthält die Werte für die Runbookparameter, die für diesen Auftrag bereitgestellt wurden.

Zeigen Sie die Aufträge für ein Runbook mithilfe der folgenden Schritte an.

1. Wählen Sie im Azure-Verwaltungsportal die Option **Automation**, und klicken Sie auf den Namen eines Automation-Kontos.
2. Klicken Sie auf den Namen eines Runbooks.
3. Wählen Sie die Registerkarte **Aufträge** aus.
4. Klicken Sie auf die Spalte **Erstellter Auftrag**, um detaillierte Informationen und die Auftragsausgabe anzuzeigen.

## Abrufen des Auftragsstatus mithilfe von Windows PowerShell
Sie können [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) verwenden, um die für ein Runbook erstellten Aufträge und die Details zu einem bestimmten Auftrag anzuzeigen. Wenn Sie ein Runbook über Windows PowerShell mithilfe des Befehls [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx) starten, gibt er den resultierenden Auftrag zurück. Verwenden Sie [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx), um die Ausgabe eines Auftrags abzurufen.

Die folgenden Beispielbefehle rufen den letzten Auftrag für ein Beispielrunbook ab und zeigen seinen Status, die für die Runbookparameter bereitgestellten Werte und die Ausgabe des Auftrags an.

    $job = (Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" | sort LastModifiedDate –desc)[0]
    $job.Status
    $job.JobParameters
    Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

## Gleichmäßige Verteilung
Damit Ressourcen von allen Runbooks in der Cloud verwendet werden können, entlädt Azure Automation jeden Auftrag vorübergehend, nachdem er 3 Stunden lang ausgeführt wurde. [Grafische Runbooks](automation-runbook-types.md#graphical-runbooks) und [PowerShell-Workflow-Runbooks](automation-runbook-types.md#powershell-workflow-runbooks) werden ab ihrem letzten [Prüfpunkt](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) fortgesetzt. Während dieser Zeit weist der Auftrag den Status "Wird ausgeführt, auf Ressourcen wird gewartet" auf. Wenn das Runbook keine Prüfpunkte enthält oder der Auftrag den ersten Prüfpunkt vor dem Entladen nicht erreicht hat, wird der Auftrag vom Anfang neu gestartet. [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks) werden immer von Beginn an gestartet, da sie keine Prüfpunkte unterstützen.

> [!NOTE]
> Der Grenzwert für die gleichmäßige Verteilung gilt nicht für Runbookaufträge, die auf Hybrid Runbook Workern ausgeführt werden.
> 
> 

Wenn das Runbook vom gleichen Prüfpunkt aus oder vom Anfang des Runbooks drei aufeinander folgende Male neu gestartet wurde, wird es mit dem Status "Fehler, auf Ressourcen wird gewartet" beendet. Dieses Verhalten schützt davor, dass Runbooks unbegrenzt ausgeführt werden, da der nächste Prüfpunkt nicht ohne Entladen erreicht werden kann. In diesem Fall wird ein Ausnahmefehler mit der folgenden Meldung angezeigt.

*Die Ausführung des Auftrags kann nicht fortgesetzt werden, da er wiederholt am selben Prüfpunkt entfernt wurde. Stellen Sie sicher, dass Ihr Runbook Vorgänge mit langer Ausführungsdauer nicht ausführt, ohne deren Zustand dauerhaft zu speichern.*

Sorgen Sie beim Erstellen eines Runbooks dafür, dass die Zeit zum Ausführen von Aktivitäten zwischen zwei Prüfpunkten 3 Stunden nicht überschreitet. Sie müssen Ihrem Runbook ggf. Prüfpunkte hinzufügen oder Vorgänge mit langen Ausführungszeiten aufteilen, um sicherzustellen, dass dieser Grenzwert von 3 Stunden nicht erreicht wird. Angenommen, Ihr Runbook führt eine Neuindizierung für eine große SQL-Datenbank durch. Wenn dieser einzelne Vorgang nicht innerhalb des Grenzwerts für die gleichmäßige Verteilung abgeschlossen wird, wird der Auftrag entladen und vom Anfang neu gestartet. In diesem Fall sollten Sie den Neuindizierungsvorgang in mehrere Schritte unterteilen (z. B. nur jeweils eine Tabelle gleichzeitig neu indizieren) und dann einen Prüfpunkt nach jedem Vorgang einfügen, damit der Auftrag nach dem letzten Vorgang bis zum Abschluss fortgesetzt werden kann.

## Nächste Schritte
* [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md)

<!---HONumber=AcomDC_0323_2016-->