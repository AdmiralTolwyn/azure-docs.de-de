---
title: Azure Automation-Runbooktypen | Microsoft Docs
description: "Beschreibt die verschiedenen Runbooktypen, die Sie in Azure Automation verwenden können, sowie Aspekte, die Sie bei der Wahl des geeigneten Typs berücksichtigen sollten. "
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 9265c975-4281-4819-a84f-d86641277f36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/01/2017
ms.author: bwren
ms.openlocfilehash: e4a8ab0e68d6614fea1b44f0115a1c633f145277
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2017
---
# <a name="azure-automation-runbook-types"></a>Azure Automation-Runbooktypen
Azure Automation unterstützt verschiedene Runbooktypen, die in der folgenden Tabelle kurz beschrieben werden.  Die folgenden Abschnitte bieten weitere Informationen zu den einzelnen Typen und deren Einsatzbereichen.

| Typ | Beschreibung |
|:--- |:--- |
| [Grafisch](#graphical-runbooks) |Basiert auf Windows PowerShell und wird vollständig im grafischen Editor im Azure-Portal erstellt und bearbeitet. |
| [PowerShell-Workflow, grafisch](#graphical-runbooks) |Basiert auf Windows PowerShell-Workflow und wird vollständig im grafischen Editor im Azure-Portal erstellt und bearbeitet. |
| [PowerShell](#powershell-runbooks) |Textrunbook, das auf einem Windows PowerShell-Skript basiert. |
| [PowerShell-Workflow](#powershell-workflow-runbooks) |Textrunbook, das auf einem Windows PowerShell-Workflow basiert. |
| [Python](#python-runbooks) |Auf Python basierendes Textrunbook |

## <a name="graphical-runbooks"></a>Grafische Runbooks
[Grafische](automation-runbook-types.md#graphical-runbooks) und grafische PowerShell-Workflow-Runbooks werden im grafischen Editor im Azure-Portal erstellt und bearbeitet.  Sie können in eine Datei exportiert und anschließend in ein anderes Automation-Konto importiert, aber nicht mit einem anderen Tool erstellt oder bearbeitet werden.  Grafische Runbooks generieren PowerShell-Code, aber Sie können den Code nicht direkt anzeigen oder ändern. Grafische Runbooks können weder in eines der [Textformate](automation-runbook-types.md)konvertiert werden, noch kann ein Textrunbook in das Grafikformat konvertiert werden. Grafische Runbooks können während des Imports in grafische PowerShell-Workflow-Runbooks konvertiert werden und umgekehrt.

### <a name="advantages"></a>Vorteile
* Visuelles Erstellungsmodell zum Einfügen/Verknüpfen/Konfigurieren  
* Schwerpunkt auf Datenfluss über das Gateway  
* Visuelle Darstellung von Verwaltungsprozessen  
* Einschließen weiterer Runbooks als untergeordnete Runbooks, um übergeordnete Workflows zu erstellen  
* Vereinfacht modulare Programmierung  


### <a name="limitations"></a>Einschränkungen
* Runbook kann nicht außerhalb des Azure-Portals bearbeitet werden.
* Erfordert möglicherweise Aktivität mit PowerShell-Code, um komplexe Logik auszuführen.
* Der vom grafischen Workflow erstellte PowerShell-Code kann nicht angezeigt oder direkt bearbeitet werden. Beachten Sie, dass Sie den erstellten Code in allen Codeaktivitäten anzeigen können.

## <a name="powershell-runbooks"></a>PowerShell-Runbooks
PowerShell-Runbooks basieren auf Windows PowerShell.  Sie bearbeiten den Code des Runbooks direkt mit dem Text-Editor im Azure-Portal.  Sie können auch einen beliebigen Offline-Texteditor verwenden und das [Runbook in Azure Automation importieren](http://msdn.microsoft.com/library/azure/dn643637.aspx) .

### <a name="advantages"></a>Vorteile
* Implementierung der gesamten komplexen Logik mit PowerShell-Code ohne die zusätzliche Komplexität des PowerShell-Workflows. 
* Runbook startet schneller als PowerShell-Workflow-Runbooks, da es nicht vor der Ausführung kompiliert werden muss.

### <a name="limitations"></a>Einschränkungen
* Autor muss mit PowerShell-Skripts vertraut sein.
* Eine [parallele Verarbeitung](automation-powershell-workflow.md#parallel-processing) zum gleichzeitigen Ausführen mehrerer Aktionen ist nicht möglich.
* Die Verwendung von [Prüfpunkten](automation-powershell-workflow.md#checkpoints) zum Fortsetzen des Runbooks im Fall eines Fehlers ist nicht möglich.
* PowerShell-Workflow- und grafische Runbooks können nur mithilfe des Cmdlets "Start-AzureAutomationRunbook" einbezogen werden, wodurch ein neuer Auftrag erstellt wird.

### <a name="known-issues"></a>Bekannte Probleme
Im Folgenden sind aktuell bekannte Probleme mit PowerShell-Runbooks aufgeführt.

* PowerShell-Runbooks können keine unverschlüsselten [Variablenassets](automation-variables.md) mit einem NULL-Wert abrufen.
* PowerShell-Runbooks können keine [Variablenassets](automation-variables.md) abrufen, deren Name *~* enthält.
* "Get-Process" in einer Schleife in einem PowerShell-Runbook kann nach etwa 80 Iterationen zum Absturz führen. 
* Ein PowerShell-Runbook kann einen Fehler verursachen, wenn es versucht, eine sehr große Datenmenge auf einmal in den Ausgabestream zu schreiben.   Sie können dieses Problem in der Regel vermeiden, indem Sie bei der Arbeit mit großen Objekten nur die benötigten Informationen ausgeben.  Statt beispielsweise *Get-Process* auszugeben, können Sie nur die erforderlichen Felder mit *Get-Process | Select ProcessName, CPU* ausgeben.

## <a name="powershell-workflow-runbooks"></a>PowerShell-Workflow-Runbooks
PowerShell-Workflow-Runbooks sind Textrunbooks, die auf einem [Windows PowerShell-Workflow](automation-powershell-workflow.md)basieren.  Sie bearbeiten den Code des Runbooks direkt mit dem Text-Editor im Azure-Portal.  Sie können auch einen beliebigen Offline-Texteditor verwenden und das [Runbook in Azure Automation importieren](http://msdn.microsoft.com/library/azure/dn643637.aspx) .

### <a name="advantages"></a>Vorteile
* Implementierung der gesamten komplexen Logik mit PowerShell-Workflowcode.
* Verwendung von [Prüfpunkten](automation-powershell-workflow.md#checkpoints) zum Fortsetzen des Runbooks im Fall eines Fehlers.
* Verwendung der [parallelen Verarbeitung](automation-powershell-workflow.md#parallel-processing) , um mehrere Aktionen gleichzeitig auszuführen.
* Kann andere grafische und PowerShell-Workflow-Runbooks als untergeordnete Runbooks integrieren, um übergeordnete Workflows zu erstellen.

### <a name="limitations"></a>Einschränkungen
* Autor muss mit PowerShell-Workflow vertraut sein.
* Die zusätzliche Komplexität des PowerShell-Workflows, z.B. [deserialisierte Objekte](automation-powershell-workflow.md#code-changes), müssen vom Runbook verarbeitet werden.
* Das Starten des Runbooks dauert länger als bei PowerShell-Runbooks, da es vor der Ausführung kompiliert werden muss.
* PowerShell-Runbooks können nur als untergeordnete Runbooks mithilfe des Cmdlets "Start-AzureAutomationRunbook" einbezogen werden, wodurch ein neuer Auftrag erstellt wird.

## <a name="python-runbooks"></a>Python-Runbooks
Python-Runbooks werden unter Python-2 kompiliert.  Sie können den Code des Runbooks direkt mit einem Text-Editor im Azure-Portal bearbeiten oder einen beliebigen Text-Editor offline verwenden und [das Runbook in Azure Automation importieren](http://msdn.microsoft.com/library/azure/dn643637.aspx).

### <a name="advantages"></a>Vorteile
* Nutzen Sie die stabile Standardbibliothek von Python.

### <a name="limitations"></a>Einschränkungen
* Sie müssen mit Python-Skripts vertraut sein.
* Derzeit wird nur Python 2 unterstützt, die spezifischen Funktionen von Python 3 sind also nicht nutzbar.

### <a name="known-issues"></a>Bekannte Probleme
Im Folgenden sind aktuell bekannte Probleme mit Python-Runbooks aufgeführt.

* Um Bibliotheken von Drittanbietern nutzen zu können, muss das Runbook auf einem [Windows-Hybrid Runbook Worker](https://docs.microsoft.com/azure/automation/automation-windows-hrw-install) oder [Linux-Hybrid Runbook Worker](https://docs.microsoft.com/azure/automation/automation-linux-hrw-install) mit den bereits auf dem Computer installierten Bibliotheken ausgeführt werden, bevor das Runbook gestartet wird.

## <a name="considerations"></a>Überlegungen
Wenn Sie festlegen, welchen Typ Sie für ein bestimmtes Runbook verwenden möchten, sollten Sie außerdem Folgendes berücksichtigen.

* Runbooks können nicht aus einem grafischen in einen textbasierten Typ oder umgekehrt konvertiert werden.
* Es gibt Einschränkungen bei der Verwendung von Runbooks verschiedener Typen als untergeordnete Runbooks.  Weitere Informationen finden Sie unter [Untergeordnete Runbooks in Azure Automation](automation-child-runbooks.md) .

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Erstellen von grafischen Runbooks finden Sie unter [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md)
* Informationen zu den Unterschieden zwischen PowerShell- und PowerShell-Workflow-Runbooks finden Sie unter [Grundlagen des Windows PowerShell-Workflows](automation-powershell-workflow.md)
* Weitere Informationen zum Erstellen oder Importieren eines Runbooks finden Sie unter [Erstellen oder Importieren eines Runbooks](automation-creating-importing-runbook.md)

