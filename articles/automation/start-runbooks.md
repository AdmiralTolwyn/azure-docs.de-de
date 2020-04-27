---
title: Starten eines Runbooks in Azure Automation
description: Bietet eine Übersicht über die verschiedenen Methoden, die zum Starten eines Runbooks in Azure Automation verwendet werden können, und stellt Informationen zur Verwendung des Azure-Portals sowie von Windows PowerShell bereit.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 7f2c0dda952959db3bffba6016f48b986016c19e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679466"
---
# <a name="start-a-runbook-in-azure-automation"></a>Starten eines Runbooks in Azure Automation

Die folgende Tabelle hilft Ihnen dabei herauszufinden, welche Methode zum Starten eines Runbooks in Azure Automation sich am besten für Ihr jeweiliges Szenario eignet. Dieser Artikel enthält Details zum Starten eines Runbooks über das Azure-Portal oder mit Windows PowerShell. Details zu den anderen Methoden werden in anderen Artikeln bereitgestellt, auf die Sie über unten stehende Links zugreifen können.

| **Methode** | **Merkmale** |
| --- | --- |
| [Azure portal](#start-a-runbook-with-the-azure-portal) |<li>Einfachste Methode mit interaktiver Benutzeroberfläche.<br> <li>Formular zum Bereitstellen von einfachen Parameterwerten.<br> <li>Einfaches Nachverfolgen des Auftragsstatus.<br> <li>Authentifizierter Zugriff über Azure-Anmeldung. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Aufruf über Befehlszeile mit Windows PowerShell-Cmdlets.<br> <li>Kann in eine automatisierte Lösung mit mehreren Schritten eingebunden werden.<br> <li>Anforderung wird über Zertifikat oder OAuth-Benutzerprinzipal/-Dienstprinzipal authentifiziert.<br> <li>Bereitstellen von einfachen und komplexen Parameterwerten.<br> <li>Nachverfolgen des Auftragsstatus.<br> <li>Client erforderlich zur Unterstützung der PowerShell-Befehle. |
| [Azure Automation-API](/rest/api/automation/) |<li>Flexibelste Methode, jedoch auch sehr komplex.<br> <li>Aufruf aus jedem benutzerdefinierten Code, der HTTP-Anforderungen ausführen kann.<br> <li>Anforderung wird über Zertifikat oder OAuth-Benutzerprinzipal/-Dienstprinzipal authentifiziert.<br> <li>Bereitstellen von einfachen und komplexen Parameterwerten. *Wenn Sie über die API ein Python-Runbook aufrufen, muss die JSON-Nutzlast serialisiert werden.*<br> <li>Nachverfolgen des Auftragsstatus. |
| [Webhooks](automation-webhooks.md) |<li>Starten eines Runbooks über eine einzelne HTTP-Anforderung.<br> <li>Authentifizierung über Sicherheitstoken in der URL.<br> <li>Ein Client kann keine Parameterwerte überschreiben, die beim Erstellen des Webhooks festgelegt wurden. Ein Runbook kann einen einzelnen Parameter definieren, der mit den Details zur HTTP-Anforderung aufgefüllt wird.<br> <li>Keine Möglichkeit, den Auftragsstatus über die Webhook-URL nachzuverfolgen. |
| [Reagieren auf eine Azure-Warnung](../log-analytics/log-analytics-alerts.md) |<li>Starten Sie ein Runbook als Reaktion auf eine Azure-Warnung.<br> <li>Konfigurieren eines Webhooks für Runbook und Link zum Warnen.<br> <li>Authentifizierung über Sicherheitstoken in der URL. |
| [Zeitplan](automation-schedules.md) |<li>Automatisches Starten eines Runbooks: stündlich, täglich, wöchentlich oder monatlich.<br> <li>Bearbeiten des Zeitplans über das Azure-Portal, PowerShell-Cmdlets oder die Azure-API.<br> <li>Bereitstellen von Parameterwerten, die mit dem Zeitplan verwendet werden sollen. |
| [Über ein anderes Runbook](automation-child-runbooks.md) |<li>Verwenden eines Runbooks als Aktivität in einem anderen Runbook.<br> <li>Nützlich bei Funktionen, die von mehreren Runbooks verwendet werden.<br> <li>Bereitstellen von Parameterwerten für untergeordnete Runbooks und Verwenden der Ausgabe im übergeordneten Runbook. |

Die folgende Abbildung veranschaulicht ausführlich die Prozessschritte im Lebenszyklus eines Runbooks. Dies umfasst verschiedene Arten, auf die ein Runbook in Azure Automation gestartet werden kann, sowie Komponenten, die erforderlich sind, damit ein Hybrid Runbook Worker Azure Automation-Runbooks ausführen und Interaktionen zwischen verschiedenen Komponenten unterstützen kann. Weitere Informationen zum Ausführen von Automation-Runbooks in Ihrem Rechenzentrum finden Sie unter [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)

![Runbook-Architektur](media/automation-starting-runbook/runbooks-architecture.png)

>[!NOTE]
>Dieser Artikel wurde aktualisiert und beinhaltet jetzt das neue Az-Modul von Azure PowerShell. Sie können das AzureRM-Modul weiterhin verwenden, das bis mindestens Dezember 2020 weiterhin Fehlerbehebungen erhält. Weitere Informationen zum neuen Az-Modul und zur Kompatibilität mit AzureRM finden Sie unter [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Einführung in das neue Az-Modul von Azure PowerShell). Installationsanweisungen für das Az-Modul auf Ihrem Hybrid Runbook Worker finden Sie unter [Installieren des Azure PowerShell-Moduls](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). In Ihrem Automation-Konto können Sie die Module mithilfe der Informationen unter [Aktualisieren von Azure PowerShell-Modulen in Azure Automation](automation-update-azure-modules.md) auf die neueste Version aktualisieren.

## <a name="runbook-parameters"></a>Runbookparameter

Wenn Sie ein Runbook über das Azure-Portal oder mit Windows PowerShell starten, wird die Anweisung über den Azure Automation-Webdienst gesendet. Dieser Dienst unterstützt keine Parameter mit komplexen Datentypen. Wenn Sie einen Wert für einen komplexen Parameter bereitstellen müssen, müssen Sie diesen inline aus einem anderen Runbook aufrufen, wie unter [Untergeordnete Runbooks in Azure Automation](automation-child-runbooks.md)beschrieben.

Der Azure Automation-Webdienst bietet spezielle Funktionen für Parameter, die bestimmte Datentypen verwenden, wie in den folgenden Abschnitten beschrieben:

### <a name="named-values"></a>Benannte Werte

Wenn der Parameter den Datentyp „[object]“ enthält, können Sie das folgende JSON-Format verwenden, um eine Liste benannter Werte an den Parameter zu senden: *{Name1:Wert1, Name2:Wert2, Name3:Wert3}* . Bei den Werten muss es sich um einfache Typen handeln. Das Runbook empfängt den Parameter als [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) mit Eigenschaften, die den einzelnen benannten Werten entsprechen.

Betrachten Sie das folgende Testrunbook, das einen Parameter namens "user" akzeptiert.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

Für den Parameter "user" könnte folgender Text verwendet werden.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Dadurch wird die folgende Ausgabe zurückgegeben:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Arrays

Wenn es sich bei dem Parameter um ein Array wie beispielsweise [array] oder [string[]] handelt, können Sie das folgende JSON-Format verwenden, um eine Liste von Werten an den Parameter zu senden: *[Wert1,Wert2,Wert3]* . Bei den Werten muss es sich um einfache Typen handeln.

Betrachten Sie das folgende Testrunbook, das einen Parameter namens *user*akzeptiert.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

Für den Parameter "user" könnte folgender Text verwendet werden.

```input
["Joe","Smith",2,true]
```

Dadurch wird die folgende Ausgabe zurückgegeben:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Anmeldeinformationen

Wenn der Parameter den Datentyp `PSCredential` aufweist, können Sie den Namen eines Azure Automation-[Anmeldeinformationsobjekts](automation-credentials.md) bereitstellen. Das Runbook ruft die Anmeldeinformationen mit dem von Ihnen angegebenen Namen ab. Das folgende Testrunbook akzeptiert einen Parameter namens `credential`.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Für den Parameter „user“ kann der folgende Text verwendet werden, vorausgesetzt, es ist ein Anmeldeinformationsobjekt namens `My Credential` vorhanden.

```input
My Credential
```

Unter der Voraussetzung, dass der Benutzername in den Anmeldeinformationen `jsmith` lautet, wird folgende Ausgabe angezeigt.

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Starten eines Runbooks mit dem Azure-Portal

1. Wählen Sie im Azure-Portal die Option **Automation**, und klicken Sie auf den Namen eines Automation-Kontos.
2. Wählen Sie im Menü „Hub“ die Option **Runbooks** aus.
3. Wählen Sie auf der Seite „Runbook“ ein Runbook aus, und klicken Sie dann auf **Starten**.
4. Wenn das Runbook Parameter enthält, werden Sie aufgefordert, für jeden Parameter einen Wert in ein Textfeld einzugeben. Weitere Informationen zu Parametern finden Sie unter [Runbookparameter](#runbook-parameters).
5. Im Bereich „Auftrag“ können Sie den Status des Runbookauftrags anzeigen.

## <a name="start-a-runbook-with-powershell"></a>Starten eines Runbooks mit PowerShell

Sie können das Cmdlet [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) verwenden, um ein Runbook mit Windows PowerShell zu starten. Der folgende Beispielcode startet ein Runbook namens **Test-Runbook**.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook` gibt ein Auftragsobjekt zurück, mit dem Sie den Status nachverfolgen können, sobald das Runbook gestartet wurde. Sie können dieses job-Objekt mit [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) verwenden, um den Status des Auftrags zu ermitteln, und [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjoboutput?view=azps-3.7.0), um seine Ausgabe abzurufen. Das folgende Beispiel startet ein Runbook namens **Test-Runbook**, wartet, bis die Ausführung beendet ist, und zeigt anschließend die Runbookausgabe an.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Wenn das Runbook Parameter erfordert, müssen Sie diese als [Hashtabelle](https://technet.microsoft.com/library/hh847780.aspx) bereitstellen. Der Schlüssel der Hashtabelle muss dem Parameternamen entsprechen, und der Wert ist der Parameterwert. Das folgende Beispiel zeigt das Starten eines Runbooks mit zwei Zeichenfolgenparametern "FirstName" und "LastName", einem ganzzahligen Wert namens "RepeatCount" und einem booleschen Parameter namens "Show". Weitere Informationen zu Parametern finden Sie unter [Runbookparameter](#runbook-parameters).

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Ausführen von Automation-Runbooks in Ihrem Rechenzentrum finden Sie unter [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).
* Weitere Informationen zum Erstellen von modularen Runbooks, die von anderen Runbooks für spezifische oder allgemeine Funktionen verwendet werden, finden Sie unter [Untergeordnete Runbooks](automation-child-runbooks.md).
* Weitere Informationen zu PowerShell, einschließlich Sprachreferenz und Lernmodulen, finden Sie in der [PowerShell-Dokumentation](https://docs.microsoft.com/powershell/scripting/overview).
