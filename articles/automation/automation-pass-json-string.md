---
title: Übergeben eines JSON-Objekts an ein Azure Automation-Runbook
description: Übergeben von Parametern an ein Runbook als JSON-Objekt
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: PowerShell, Runbook, JSON, Azure Automation
ms.openlocfilehash: 5e1ab8d6bd2de24251851cfc60d270a2fef4090d
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918817"
---
# <a name="pass-a-json-object-to-an-azure-automation-runbook"></a>Übergeben eines JSON-Objekts an ein Azure Automation-Runbook

Es kann hilfreich sein, Daten, die Sie an ein Runbook übergeben möchten, in einer JSON-Datei zu speichern.
Sie können z.B. eine JSON-Datei erstellen, die alle Parameter enthält, die an ein Runbook übergeben werden sollen.
Zu diesem Zweck müssen Sie den JSON-Code in eine Zeichenfolge und diese dann in ein PowerShell-Objekt konvertieren, dessen Inhalt Sie anschließend an ein Runbook übergeben.

In diesem Beispiel erstellen wir ein PowerShell-Skript, das [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) aufruft, um ein PowerShell-Runbook zu starten, mit dem der Inhalt der JSON-Datei an das Runbook übergeben wird.
Das PowerShell-Runbook startet einen virtuellen Azure-Computer und ruft die Parameter für den virtuellen Computer aus der übergebenen JSON-Datei ab.

## <a name="prerequisites"></a>Voraussetzungen
Für dieses Tutorial benötigen Sie Folgendes:

* Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich für ein [kostenloses Konto registrieren](https://azure.microsoft.com/free/).
* [Automation-Konto](automation-sec-configure-azure-runas-account.md) dient zur Aufbewahrung des Runbooks und zur Authentifizierung gegenüber Azure-Ressourcen.  Dieses Konto muss über die Berechtigung zum Starten und Beenden des virtuellen Computers verfügen.
* Einen virtuellen Azure-Computer. Da dieser Computer gestartet und beendet wird, sollte es sich nicht um einen virtuellen Computer in der Produktionsumgebung handeln.
* Eine Installation von Azure PowerShell auf einem lokalen Computer. Weitere Informationen zum Erhalt von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0).

## <a name="create-the-json-file"></a>Erstellen der JSON-Datei

Geben Sie den folgenden Test in eine Textdatei ein, und speichern Sie diese als `test.json` auf dem lokalen Computer.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

## <a name="create-the-runbook"></a>Erstellen des Runbooks

Erstellen Sie ein neues PowerShell-Runbook mit dem Namen „Test-Json“ in Azure Automation.
Weitere Informationen zum Erstellen eines PowerShell-Runbooks finden Sie unter [Mein erstes PowerShell-Runbook](automation-first-runbook-textual-powershell.md).

Damit die JSON-Daten akzeptiert werden, muss das Runbook ein Objekt als Eingabeparameter annehmen.

Das Runbook kann dann die in der JSON-Datei definierten Eigenschaften verwenden.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account   
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
 ```

 Speichern und veröffentlichen Sie dieses Runbook in Ihrem Automation-Konto.

## <a name="call-the-runbook-from-powershell"></a>Aufrufen des Runbooks in PowerShell

Jetzt können Sie das Runbook auf dem lokalen Computer mithilfe von Azure PowerShell aufrufen.
Führen Sie die folgenden PowerShell-Befehle aus:

1. Anmelden bei Azure:
   ```powershell
   Connect-AzureRmAccount
   ```
    Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** ist jetzt ein Alias für **Connect-AzureRMAccount**. Sollte **Connect-AzureRMAccount** beim Durchsuchen der Bibliothekselemente nicht angezeigt werden, können Sie **Add-AzureRmAccount** verwenden oder die Module in Ihrem Automation-Konto aktualisieren.

1. Rufen Sie den Inhalt der JSON-Datei ab, und konvertieren Sie ihn in eine Zeichenfolge:
    ```powershell
    $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
    ```
    `JsonPath` ist der Pfad, unter dem Sie die JSON-Datei gespeichert haben.
1. Konvertieren des Zeichenfolgeninhalts von `$json` in ein PowerShell-Objekt:
   ```powershell
   $JsonParams = @{"json"=$json}
   ```
1. Erstellen einer Hashtabelle der Parameter für `Start-AzureRmAutomationRunbook`:
   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```
   Beachten Sie, dass Sie den Wert von `Parameters` auf das PowerShell-Objekt festlegen, das die Werte aus der JSON-Datei enthält. 
1. Starten des Runbooks
   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

Das Runbook verwendet die Werte aus der JSON-Datei, um einen virtuellen Computer zu starten.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Bearbeiten von PowerShell- und PowerShell-Workflow-Runbooks mit einem Text-Editor finden Sie unter [Bearbeiten von Textrunbooks in Azure Automation](automation-edit-textual-runbook.md) 
* Weitere Informationen zum Erstellen und Importieren eines Runbooks finden Sie unter [Erstellen oder Importieren eines Runbooks in Azure Automation](automation-creating-importing-runbook.md).


