---
title: Überprüfen der Konfiguration des Azure Automation-Kontos
description: In diesem Artikel wird beschrieben, wie Sie überprüfen, ob die Konfiguration Ihres Automation-Kontos richtig eingerichtet ist.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: af1d05c171eb5544104b12aebb6c7be937061f6a
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437177"
---
# <a name="test-azure-automation-run-as-account-authentication"></a>Testen der Authentifizierung für das ausführende Azure Automation-Konto
Nachdem die erfolgreiche Erstellung eines Automation-Kontos abgeschlossen ist, können Sie einen einfachen Test durchführen. Auf diese Weise können Sie bestätigen, dass die Authentifizierung für die Azure Resource Manager- oder klassische Azure-Bereitstellung möglich ist, indem Sie Ihr neu erstelltes oder aktualisiertes ausführendes Automation-Konto verwenden.    

## <a name="automation-run-as-authentication"></a>Authentifizierung mit ausführendem Automation-Konto
Verwenden Sie den unten angegebenen Beispielcode zum [Erstellen eines PowerShell-Runbooks](automation-creating-importing-runbook.md), um die Authentifizierung mithilfe des ausführenden Kontos zu überprüfen, und verwenden Sie ihn auch in Ihren benutzerdefinierten Runbooks, um Resource Manager-Ressourcen mit Ihrem Automation-Konto zu authentifizieren und zu verwalten.

    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

        "Logging in to Azure..."
        Connect-AzureRmAccount `
           -ServicePrincipal `
           -TenantId $servicePrincipalConnection.TenantId `
           -ApplicationId $servicePrincipalConnection.ApplicationId `
           -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
       if (!$servicePrincipalConnection)
       {
          $ErrorMessage = "Connection $connectionName not found."
          throw $ErrorMessage
      } else{
          Write-Error -Message $_.Exception
          throw $_.Exception
      }
    }

    #Get all ARM resources from all resource groups
    $ResourceGroups = Get-AzureRmResourceGroup 

    foreach ($ResourceGroup in $ResourceGroups)
    {    
       Write-Output ("Showing resources in resource group " + $ResourceGroup.ResourceGroupName)
       $Resources = Find-AzureRmResource -ResourceGroupNameContains $ResourceGroup.ResourceGroupName | Select ResourceName, ResourceType
       ForEach ($Resource in $Resources)
       {
          Write-Output ($Resource.ResourceName + " of type " +  $Resource.ResourceType)
       }
       Write-Output ("")
    } 

Beachten Sie, dass das zur Authentifizierung im Runbook verwendete Cmdlet **Connect-AzureRmAccount** den Parametersatz *ServicePrincipalCertificate* verwendet.  Die Authentifizierung wird nicht mit Anmeldeinformationen vorgenommen, sondern mit einem Dienstprinzipalzertifikat.  

> [!IMPORTANT]
> **Add-AzureRmAccount** ist jetzt ein Alias für **Connect-AzureRMAccount**. Sollte **Connect-AzureRMAccount** beim Durchsuchen der Bibliothekselemente nicht angezeigt werden, können Sie **Add-AzureRmAccount** verwenden oder die Module in Ihrem Automation-Konto aktualisieren.

Wenn Sie das [Runbook ausführen](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal), um Ihr ausführendes Konto zu überprüfen, wird ein [Runbookauftrag](automation-runbook-execution.md) erstellt, die Seite „Auftrag“ wird geöffnet, und auf der Kachel **Auftragszusammenfassung** wird der Auftragsstatus angezeigt. Der Auftrag besitzt zunächst den Status *In Warteschlange* , um anzugeben, dass der Auftrag darauf wartet, dass in der Cloud ein Runbook Worker verfügbar wird. Wird der Auftrag von einem Worker übernommen, wechselt der Status zu *Wird gestartet* und anschließend zu *Wird ausgeführt*, wenn die Ausführung des Runbooks tatsächlich gestartet wurde.  Wenn der Runbook-Auftrag abgeschlossen ist, sollte der Status **Abgeschlossen** lauten.

Um die detaillierten Ergebnisse des Runbooks anzuzeigen, klicken Sie auf die Kachel **Ausgabe** .  Auf der Seite **Ausgabe** wird angezeigt, dass die Authentifizierung erfolgreich war, und es wird eine Liste mit allen Ressourcen in allen Ressourcengruppen Ihres Abonnements zurückgegeben.  

Vergessen Sie beim Zurücksetzen des Codes für Ihre Runbooks nicht, den Codeblock zu entfernen, der mit dem Kommentar `#Get all ARM resources from all resource groups` beginnt.

## <a name="classic-run-as-authentication"></a>Authentifizierung mit einem klassischen ausführenden Konto
Verwenden Sie den unten angegebenen Beispielcode zum [Erstellen eines PowerShell-Runbooks](automation-creating-importing-runbook.md), um die Authentifizierung mithilfe des klassischen ausführenden Kontos zu überprüfen, und verwenden Sie ihn auch in Ihren benutzerdefinierten Runbooks, um Ressourcen im klassischen Bereitstellungsmodell zu authentifizieren und zu verwalten.  

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID
    
    #Get all VMs in the subscription and return list with name of each
    Get-AzureVM | ft Name

Wenn Sie das [Runbook ausführen](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal), um Ihr ausführendes Konto zu überprüfen, wird ein [Runbookauftrag](automation-runbook-execution.md) erstellt, die Seite „Auftrag“ wird geöffnet, und auf der Kachel **Auftragszusammenfassung** wird der Auftragsstatus angezeigt. Der Auftrag besitzt zunächst den Status *In Warteschlange* , um anzugeben, dass der Auftrag darauf wartet, dass in der Cloud ein Runbook Worker verfügbar wird. Wird der Auftrag von einem Worker übernommen, wechselt der Status zu *Wird gestartet* und anschließend zu *Wird ausgeführt*, wenn die Ausführung des Runbooks tatsächlich gestartet wurde.  Wenn der Runbook-Auftrag abgeschlossen ist, sollte der Status **Abgeschlossen** lauten.

Um die detaillierten Ergebnisse des Runbooks anzuzeigen, klicken Sie auf die Kachel **Ausgabe** .  Auf der Seite **Ausgabe** wird angezeigt, dass die Authentifizierung erfolgreich war, und es wird eine nach „VMName“ sortierte Liste mit allen virtuellen Azure-Computern zurückgegeben, die in Ihrem Abonnement bereitgestellt sind.  

Vergessen Sie nicht, das Cmdlet **Get-AzureVM** zu entfernen, wenn Sie den Code für Ihre Runbooks wiederverwenden.

## <a name="next-steps"></a>Nächste Schritte
* Erste Schritte mit PowerShell-Runbooks werden unter [Mein erstes PowerShell-Runbook](automation-first-runbook-textual-powershell.md) beschrieben.
* Weitere Informationen zur grafischen Inhaltserstellung finden Sie unter [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md).
