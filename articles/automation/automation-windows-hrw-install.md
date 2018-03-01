---
title: Azure Automation-Hybrid Runbook Worker (Windows) | Microsoft-Dokumentation
description: "Dieser Artikel enthält Informationen zum Installieren eines Azure Automation-Hybrid Runbook Workers, mit dem Sie Runbooks auf Windows-basierten Computern in Ihrem lokalen Datencenter oder einer Cloudumgebung ausführen können."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: 4232634f57f9650a35c40ee769cbeb0a3e009dfb
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/13/2018
---
# <a name="how-to-deploy-a-windows-hybrid-runbook-worker"></a>Bereitstellen eines Windows-Hybrid Runbook Workers

Runbooks in Azure Automation können nicht auf Ressourcen in anderen Clouds oder in Ihrer lokalen Umgebung zugreifen, da sie in der Azure-Cloud ausgeführt werden.  Die Azure Automation-Funktion „Hybrid Runbook Worker“ ermöglicht das Ausführen von Runbooks direkt auf dem Computer, der die Rolle hostet, und außerhalb für Ressourcen in der Umgebung zur Verwaltung dieser lokalen Ressourcen. Runbooks werden in Azure Automation gespeichert und verwaltet und an einen oder mehrere dafür vorgesehene Computer übermittelt.  

Diese Funktionalität wird in der folgenden Abbildung veranschaulicht:<br>

![Hybrid-Runbook-Worker – Übersicht](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Eine technische Übersicht über die Hybrid Runbook Worker-Rolle und Berücksichtigungen bei der Bereitstellung erhalten Sie unter [Übersicht über die Automation-Architektur](automation-offering-get-started.md#automation-architecture-overview).

## <a name="hybrid-runbook-worker-groups"></a>Hybrid-Runbook-Workergruppen

Jeder Hybrid-Runbook-Worker ist ein Mitglied einer Hybrid-Runbook-Workergruppe, die Sie beim Installieren des Agents angeben.  Eine Gruppe kann einen einzelnen Agent umfassen, aber für Hochverfügbarkeit können Sie mehrere Agents installieren.

Wenn Sie ein Runbook auf einen Hybrid Runbook Worker starten, geben Sie die Gruppe an, in der das Runbook ausgeführt werden soll.  Die Mitglieder der Gruppe legen fest, welcher Worker die Anforderung verarbeitet.  Sie können keinen bestimmten Worker angeben.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Installieren der Windows Hybrid Runbook Worker 

Zum Installieren und Konfigurieren eines Windows Hybrid Runbook Worker stehen zwei Methoden zur Verfügung.  Die empfohlene Methode besteht darin, ein Automation-Runbook zu verwenden, um den Prozess, der zum Konfigurieren eines Windows-Computers erforderlich ist, vollständig zu automatisieren.  Bei der zweiten Methode wird die Rolle Schritt für Schritt manuell installiert und konfiguriert.  

> [!NOTE]
> Um die Konfiguration der Server zu verwalten, die die Hybrid Runbook Worker-Rolle mit Desired State Configuration (DSC) unterstützen, müssen Sie die Server als DSC-Knoten hinzufügen.  Weitere Informationen zu ihrer Integration für die Verwaltung durch DSC finden Sie unter [Integrieren von Computern für die Verwaltung durch Azure Automation DSC](automation-dsc-onboarding.md).        
Wenn Sie die [Lösung für die Updateverwaltung](../operations-management-suite/oms-solution-update-management.md) aktivieren, werden alle mit dem OMS-Arbeitsbereich verbundenen Windows-Computer automatisch als Hybrid Runbook Worker konfiguriert, um Runbooks zu unterstützen, die in dieser Lösung enthalten sind.  Es wird aber keine Registrierung für Hybrid Worker-Gruppen durchgeführt, die in Ihrem Automation-Konto bereits definiert wurden.  Der Computer kann einer Hybrid Runbook Worker-Gruppe in Ihrem Automation-Konto hinzugefügt werden, um Automation-Runbooks zu unterstützen, solange Sie sowohl für die Lösung als auch die Mitgliedschaft in der Hybrid Runbook Worker-Gruppe dasselbe Konto verwenden.  Diese Funktionalität wurde Version 7.2.12024.0 des Hybrid Runbook Worker hinzugefügt.  

Lesen Sie sich vor dem Bereitstellen eines Hybrid Runbook Worker die folgenden Informationen zu [Hardware- und Softwareanforderungen](automation-offering-get-started.md#hybrid-runbook-worker) und [Informationen zur Vorbereitung Ihres Netzwerks](automation-offering-get-started.md#network-planning) durch.  Lesen Sie sich nach dem erfolgreichen Bereitstellen eines Runbook Workers [Running runbooks on a Hybrid Runbook Worker (Ausführen von Runbooks auf einem Hybrid Runbook Worker)](automation-hrw-run-runbooks.md) durch, um zu erfahren, wie Sie Ihre Runbooks für die Automatisierung von Prozessen in Ihrem lokalen Rechenzentrum oder in einer anderen Cloudumgebung konfigurieren.  
 
### <a name="automated-deployment"></a>Automatisierte Bereitstellung

Führen Sie die folgenden Schritte aus, um die Installation und Konfiguration der Windows Hybrid Worker-Rolle zu automatisieren.  

1. Laden Sie das Skript *New-OnPremiseHybridWorker.ps1* aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/) direkt über den Computer herunter, auf dem die Hybrid Runbook Worker-Rolle ausgeführt wird, oder laden Sie es über einen anderen Computer in Ihrer Umgebung herunter, und kopieren sie es auf den Worker.  

    Das Skript *New-OnPremiseHybridWorker.ps1* erfordert während der Ausführung folgende Parameter:

  * *AutomationAccountName* (obligatorisch): Der Name Ihres Automation-Kontos.  
  * *ResourceGroupName* (obligatorisch): Der Name der Ressourcengruppe, die Ihrem Automation-Konto zugeordnet ist.  
  * *HybridGroupName* (obligatorisch): Der Name einer Hybrid Runbook Worker-Gruppe, die Sie als Ziel für die Runbooks angeben, die dieses Szenario unterstützen. 
  *  *SubscriptionID* (obligatorisch): Die Azure-Abonnement-ID Ihres Automation-Kontos
  *  *WorkspaceName* (optional): Der Name des OMS-Arbeitsbereichs.  Falls kein OMS-Arbeitsbereich vorhanden ist, wird dieser durch das Skript erstellt und konfiguriert.  

     > [!NOTE]
     > Derzeit werden für die Automation-OMS-Integration lediglich folgende Regionen unterstützt: **Australien, Südosten**, **USA, Osten 2**, **Asien, Südosten** und **Europa, Westen**.  Falls sich Ihr Automation-Konto nicht in einer dieser Regionen befindet, erstellt das Skript zwar einen OMS-Arbeitsbereich, weist Sie aber in einer Warnung darauf hin, dass keine Verknüpfung möglich ist.
     >
2. Starten Sie auf Ihrem Computer **Windows PowerShell** über den **Startbildschirm** im Administratormodus.  
3. Navigieren Sie über die PowerShell-Befehlszeilenshell zu dem Ordner mit dem heruntergeladenen Skript, und führen Sie es aus. Ändern Sie dabei die Werte für die Parameter *-AutomationAccountName*, *-ResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* und *-WorkspaceName*.

     > [!NOTE] 
     > Nach dem Ausführen des Skripts werden Sie aufgefordert, sich bei Azure zu authentifizieren.  Sie **müssen** sich mit einem Konto anmelden, das Mitglied der Rolle „Abonnement-Administratoren“ und Co-Administrator des Abonnements ist.  
     >  
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. Sie werden aufgefordert, der Installation von **NuGet** zuzustimmen und sich mit Ihren Azure-Anmeldeinformationen zu authentifizieren.<br><br> ![Ausführen des Skripts „New-OnPremiseHybridWorker“](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. Nach vollständiger Ausführung des Skripts werden auf der Seite „Hybrid Worker-Gruppen“ die neue Gruppe und die Anzahl von Mitgliedern angezeigt. Im Falle einer bereits vorhandenen Gruppe wird die Anzahl der Mitglieder erhöht.  Sie können die Gruppe aus der Liste auf der Seite **Hybrid Worker-Gruppen** auswählen und dann auf die Kachel **Hybrid Worker** klicken.  Auf der Seite **Hybrid Worker** werden die einzelnen Mitglieder der Gruppe aufgelistet.  

### <a name="manual-deployment"></a>Manuelle Bereitstellung 

Führen Sie die ersten beiden Schritte einmal für Ihre Automation-Umgebung durch, und wiederholen Sie dann die verbleibenden Schritte für jeden Workercomputer.

#### <a name="1-create-operations-management-suite-workspace"></a>1. Erstellen eines Operations Management Suite-Arbeitsbereichs

Sofern Sie noch nicht über einen Operations Management Suite-Arbeitsbereich verfügen, erstellen Sie diesen mithilfe der Anweisungen unter [Verwalten von Arbeitsbereichen](../log-analytics/log-analytics-manage-access.md). Wenn Sie bereits über einen Arbeitsbereich verfügen, können Sie diesen verwenden.

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2. Hinzufügen der Automation-Lösung zum Operations Management Suite-Arbeitsbereich

Die Funktionalität von Operations Management Suite kann durch Lösungen erweitert werden.  Die Automation-Lösung fügt Azure Automation Funktionalität hinzu, einschließlich der Unterstützung für Hybrid Runbook Worker.  Wenn Sie die Lösung Ihrem Arbeitsbereich hinzufügen, werden Workerkomponenten automatisch per Push auf den Agent-Computer übertragen, den Sie im nächsten Schritt installieren werden.

Befolgen Sie die Anweisungen unter [Hinzufügen einer Lösung über den Lösungskatalog](../log-analytics/log-analytics-add-solutions.md) , um Ihrem Operations Management Suite-Arbeitsbereich die Lösung **Automation** hinzuzufügen.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Installieren von Microsoft Monitoring Agent

Microsoft Monitoring Agent verbindet Computer mit Operations Management Suite.  Wenn Sie den Agent auf dem lokalen Computer installieren und mit Ihrem Arbeitsbereich verbinden, werden automatisch die erforderlichen Komponenten für Hybrid Runbook Worker heruntergeladen.

Befolgen Sie die Anweisungen unter [Verbinden von Windows-Computern mit Log Analytics](../log-analytics/log-analytics-windows-agent.md) , um den Agent auf dem lokalen Computer zu installieren.  Sie können diesen Vorgang für mehrere Computer wiederholen, um Ihrer Umgebung mehrere Worker hinzuzufügen.

Wenn der Agent erfolgreich mit Operations Management Suite verbunden ist, wird er im Operations Management Suite-Bereich **Einstellungen** auf der Registerkarte **Verbundene Datenquellen** aufgeführt.  Sie können sicherstellen, dass der Agent die Automation-Lösung ordnungsgemäß heruntergeladen hat, indem Sie prüfen, ob unter „C:\Programme\Microsoft Monitoring Agent\Agent“ ein Ordner namens **AzureAutomationFiles** vorhanden ist.  Unter „C:\Programme\Microsoft Monitoring Agent\Agent\AzureAutomation\“ im Unterordner \\*version* können Sie die Hybrid Runbook Worker-Version überprüfen.   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installieren der Runbookumgebung und Verbindungsherstellung mit Azure Automation

Wenn Sie der Operations Management Suite einen Agent hinzufügen, lädt die Automation-Lösung per Push das PowerShell-Modul **HybridRegistration** herunter, in dem das Cmdlet **Add-HybridRunbookWorker** enthalten ist.  Sie verwenden dieses Cmdlet zum Installieren der Runbookumgebung auf dem Computer und registrieren diesen bei Azure Automation.

Öffnen Sie eine PowerShell-Sitzung im Administratormodus, und führen Sie die folgenden Befehle zum Importieren des Moduls aus.

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

Führen Sie dann das Cmdlet **Add-HybridRunbookWorker** mit der folgenden Syntax aus:

    Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>

Sie finden die für dieses Cmdlet erforderlichen Informationen im Azure-Portal auf der Seite **Schlüssel verwalten**.  Öffnen Sie diese Seite, indem Sie in Ihrem Automation-Konto auf der Seite **Einstellungen** die Option **Schlüssel** auswählen.

![Hybrid-Runbook-Worker – Übersicht](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** ist der Name der Hybrid Runbook Worker-Gruppe. Wenn die Gruppe bereits im Automation-Konto vorhanden ist, wird der aktuelle Computer der Gruppe hinzugefügt.  Ist die Gruppe nicht vorhanden, wird sie hinzugefügt.
* **EndPoint** ist das Feld **URL** auf der Seite **Schlüssel verwalten**.
* **Token** ist der **primäre Zugriffsschlüssel** auf der Seite **Schlüssel verwalten**.  

Verwenden Sie den Schalter **-Verbose** mit **Add-HybridRunbookWorker**, um detaillierte Informationen über die Installation zu erhalten.

#### <a name="5-install-powershell-modules"></a>5. Installieren von PowerShell-Modulen

Runbooks können beliebige Aktivitäten und Cmdlets der Module verwenden, die Sie in Ihrer Azure Automation-Umgebung installiert haben.  Diese Module werden nicht automatisch auf den lokalen Computern bereitgestellt, sie müssen manuell installiert werden.  Eine Ausnahme stellt das standardmäßig installierte Azure-Modul dar. Es bietet Zugriff auf Cmdlets für alle Azure-Dienste und -Aktivitäten für Azure Automation.

Da der primäre Zweck der Hybrid Runbook Worker-Funktion darin besteht, lokale Ressourcen zu verwalten, müssen Sie sehr wahrscheinlich die Module zur Unterstützung dieser Ressourcen installieren.  Unter [Installieren von Modulen](http://msdn.microsoft.com/library/dd878350.aspx) finden Sie Informationen zum Installieren von PowerShell-Modulen.  Installierte Module müssen sich in einem Speicherort befinden, auf den von der PSModulePath-Umgebungsvariable verwiesen wird, damit sie vom Hybrid Worker automatisch importiert werden.  Weitere Informationen finden Sie unter [Modifying the PSModulePath Installation Path](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx) (Ändern des PSModulePath-Installationspfads). 

## <a name="troubleshooting"></a>Problembehandlung 

Der Hybrid Runbook Worker ist abhängig von Microsoft Monitoring Agent, um mit Ihrem Automation-Konto für die Registrierung des Workers zu kommunizieren, Runbook-Aufträge zu erhalten und den Status zu melden. Wenn die Registrierung des Workers fehlschlägt, gibt es mehrere mögliche Gründe dafür:  

1. Der Hybrid Worker befindet sich hinter einem Proxy oder einer Firewall.  
    Stellen Sie sicher, dass der Computer über einen ausgehenden Zugriff auf *.azure-automation.net über Port 443 verfügt.  

2. Der Computer, auf dem der Hybrid Worker ausgeführt wird, erfüllt die [Mindestanforderungen](automation-offering-get-started.md#hybrid-runbook-worker) an die Hardware nicht.  
    Computer, auf denen der Hybrid Runbook Worker ausgeführt werden soll, müssen die Mindestanforderungen an die Hardware erfüllen, damit sie als Host für dieses Feature infrage kommen. Andernfalls kommt es – je nach Ressourcennutzung durch andere Hintergrundprozesse und möglichen Konflikten bei der Runbookausführung – zu einer übermäßigen Auslastung des Computers. Dies verursacht Verzögerungen und Timeouts bei Runbookaufträgen.
   Vergewissern Sie sich, dass der für die Ausführung des Hybrid Runbook Worker-Features vorgesehene Computer die Hardwaremindestanforderungen erfüllt.  Wenn dies der Fall ist, überwachen Sie die CPU- und Arbeitsspeicherauslastung, um Zusammenhänge zwischen der Leistung der Hybrid Runbook Worker-Prozesse und Windows zu erkennen.  Falls die Leistungsgrenzen des Arbeitsspeichers oder der CPU fast erreicht werden, deutet dies u.U. darauf hin, dass Sie ein Prozessorupgrade oder weitere Prozessoren oder mehr Speicher benötigen, um den Ressourcenengpass zu beseitigen und den Fehler zu beheben. Alternativ können Sie auch eine andere Computeressource auswählen, die die Mindestanforderungen erfüllt. Bei entsprechend großem Workload skalieren Sie sie nach Bedarf.
    
3. Der Microsoft Monitoring Agent-Dienst wird nicht ausgeführt.  
    Wenn der Microsoft Monitoring Agent-Dienst von Windows nicht ausgeführt wird, verhindert dies die Kommunikation zwischen dem Hybrid Runbook Worker und Azure Automation.  Überprüfen Sie, dass der Agent ausgeführt wird, indem Sie den folgenden Befehl in PowerShell eingeben: `get-service healthservice`.  Wenn der Dienst beendet wurde, geben Sie den folgenden Befehl in PowerShell ein, um ihn wieder zu starten: `start-service healthservice`.  

4. Im Ereignisprotokoll **Anwendungs- und Dienstprotokolle\Operations Manager** wird das Ereignis 4502 angezeigt sowie ein EventMessage-Element mit **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** und folgender Beschreibung: *Das durch den Dienst „\<wsid\>.oms.opinsights.azure.com“ vorgelegte Zertifikat wurde nicht durch eine für Microsoft-Dienste verwendete Zertifizierungsstelle ausgestellt. Wenden Sie sich bitte an Ihren Netzwerkadministrator, um zu erfahren, ob ein Proxy ausgeführt wird, das die TLS/SSL-Kommunikation abfängt. Im Artikel KB3126513 sind weitere Informationen zur Behandlung von Verbindungsproblemen enthalten.*
    Dies kann daran liegen, dass Ihr Proxy oder Ihre Netzwerkfirewall die Kommunikation mit Microsoft Azure blockiert.  Stellen Sie sicher, dass der Computer über einen ausgehenden Zugriff auf *.azure-automation.net über Port 443 verfügt.

Protokolle werden lokal auf jedem Hybridworker unter "C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes" gespeichert.  Sie können überprüfen, ob Warnungen oder Fehlerereignisse in die Ereignisprotokolle **Anwendungs- und Dienstprotokolle\Microsoft-SMA\Operations** und **Anwendungs- und Dienstprotokolle\Operations Manager** eingetragen wurden, die darauf hinweisen, dass ein Verbindungsproblem oder ein anderes Problem das Onboarding der Rolle in Azure Automation beeinträchtigt oder ein Problem bei der Ausführung regulärer Vorgänge vorliegt.  

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie sich [Running runbooks on a Hybrid Runbook Worker (Ausführen von Runbooks auf einem Hybrid Runbook Worker)](automation-hrw-run-runbooks.md) durch, um zu erfahren, wie Sie Ihre Runbooks für die Automatisierung von Prozessen in Ihrem lokalen Rechenzentrum oder in einer anderen Cloudumgebung konfigurieren.
* Anweisungen zum Entfernen von Hybrid Runbook Workern finden Sie unter [Entfernen von Azure Automation-Hybrid-Runbook Workern](automation-remove-hrw.md).