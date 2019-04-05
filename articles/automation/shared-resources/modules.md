---
title: Verwenden von Modulen in Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie Module in Azure Automation verwalten.
services: automation
ms.service: automation
ms.subservice: shared-resources
author: georgewallace
ms.author: gwallace
ms.date: 03/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fa7f5d3fb38eb1dbca51dec9b73dca3c998436aa
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/14/2019
ms.locfileid: "57905325"
---
# <a name="manage-modules-in-azure-automation"></a>Verwenden von Modulen in Azure Automation

Azure Automation bietet die Möglichkeit, PowerShell-Module für die Verwendung durch PowerShell-basierte Runbooks in Ihr Automation-Konto zu importieren. Bei diesen Module kann es sich um benutzerdefinierte, von Ihnen erstellte Module, Module aus dem PowerShell-Katalog oder die AzureRM- und AZ-Module für Azure handeln.

## <a name="import-modules"></a>Importieren von Modulen

Sie haben mehrere Möglichkeiten, um ein Modul in Ihr Automation-Konto zu importieren: In den folgenden Abschnitte zeigen wir Ihnen verschiedene Möglichkeiten zum Importieren von Modulen.

> [!NOTE]
> Die maximale Pfadlänge von Dateien in einem Modul in Azure Automation beträgt 140 Zeichen. Pfade mit einer Länge von mehr als 140 Zeichen können nicht mit `Import-Module` in die PowerShell-Sitzung importiert werden.

### <a name="powershell"></a>PowerShell

Sie können das Cmdlet [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) zum Importieren von Modulen in Ihr Automation-Konto verwenden. Das Cmdlet benötigt eine URL zu einem Modul-ZIP-Paket.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Azure-Portal

Navigieren Sie im Azure-Portal zu Ihrem Automation-Konto, und wählen Sie unter **Module** die Option **Freigegebene Ressourcen** aus. Klicken Sie auf **+ Modul hinzufügen**. Wählen Sie eine **ZIP**-Datei mit Ihrem Modul aus, und klicken Sie auf **OK**, um mit dem Importieren zu beginnen.

### <a name="powershell-gallery"></a>PowerShell-Katalog

Module aus dem PowerShell-Katalog können entweder direkt über den [PowerShell-Katalog](https://www.powershellgallery.com) oder über Ihr Automation-Konto importiert werden.

Suchen Sie unter https://www.powershellgallery.com das gewünschte Modul, wenn Sie ein Modul über den PowerShell-Katalog importieren möchten. Klicken Sie auf der Registerkarte **Azure Automation** unter **Installation Options** (Installationsoptionen) auf **Deploy to Azure Automation** (In Azure Automation bereitstellen). Das Azure-Portal wird geöffnet. Wählen Sie auf der Seite **Importieren** Ihr Automation-Konto aus, und klicken Sie auf **OK**.

![PowerShell-Katalog – Importmodul](../media/modules/powershell-gallery.png)

Sie können Module auch direkt in Ihrem Automation-Konto Module aus dem PowerShell-Katalog importieren. Wählen Sie in Ihrem Automation-Konto unter **Freigegebene Ressourcen** die Option **Module** aus. Klicken Sie auf der Seite „Module“ auf **Katalog durchsuchen**. Die Seite **Katalog durchsuchen** wird geöffnet. Sie können auf dieser Seite ein Modul im PowerShell-Katalog suchen. Wählen Sie das gewünschte Modul aus, und klicken Sie auf **Importieren**. Klicken Sie auf der Seite **Importieren** auf **OK**, um den Importvorgang zu starten.

![PowerShell-Katalog – Import aus Azure-Portal](../media/modules/gallery-azure-portal.png)

## <a name="internal-cmdlets"></a>Interne Cmdlets

Nachfolgend finden Sie eine Liste der Cmdlets im internen `Orchestrator.AssetManagement.Cmdlets`-Modul, das in jedes Automation-Konto importiert wird. Diese Cmdlets sind in Ihren Runbooks und DSC-Konfigurationen verfügbar und ermöglichen Ihnen die Interaktion mit den Ressourcen in Ihrem Automation-Konto. Zudem ermöglichen die internen-Cmdlets das Abrufen von Geheimnissen aus verschlüsselten **Variablenwerten**, **Anmeldeinformationen** und verschlüsselten **Verbindungsfeldern**. Die Azure PowerShell-Cmdlets können diese Geheimnisse nicht abrufen. Wenn Sie diese Cmdlets verwenden, müssen Sie keine implizite Verbindung Azure herstellen. Dies ist nützlich in Szenarien, in denen Sie eine Verbindung, z. B. ein ausführendes Konto, zum Authentifizieren bei Azure benötigen.

|NAME|BESCHREIBUNG|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Hinzufügen eines Verbindungstyps zu Ihrem Modul

Sie können einen benutzerdefinierten [Verbindungstyp](../automation-connections.md) für Ihr Automation-Konto festlegen, indem Sie Ihrem Modul eine optionale Datei hinzufügen. Mit dieser Metadatendatei legen Sie einen Azure Automation-Verbindungstyp fest, den Sie mit dem Cmdlets des Moduls in Ihrem Automation-Konto verwenden möchten. Dazu müssen Sie ein PowerShell-Modell erstellen. Weitere Informationen zum Erstellen von Modulen finden Sie unter [How to Write a PowerShell Script Module](/powershell/developer/module/how-to-write-a-powershell-script-module) (Schreiben von PowerShell-Skriptmodulen).

![Verwenden einer benutzerdefinierten Verbindung im Azure-Portal](../media/modules/connection-create-new.png)

Wenn Sie einen Azure Automation-Verbindungstyp hinzufügen möchten, muss Ihr Modul eine Datei mit dem Namen `<ModuleName>-Automation.json` enthalten, mit dem die Eigenschaften des Verbindungstyps angegeben werden. Hierbei handelt es sich um eine JSON-Datei, die sich im Modulordner Ihrer komprimierten ZIP-Datei befindet. Diese Datei enthält die Felder einer Verbindung, die zum Herstellen einer Verbindung mit dem entsprechenden System oder Dienst des Moduls erforderlich ist. Durch diese Konfiguration wird ein Verbindungstyp in Azure Automation erstellt. Mit dieser Datei können Sie für den Verbindungstyp des Moduls die Feldnamen und -typen festlegen und angeben, ob die Felder verschlüsselt oder optional sein sollen. Das folgende Beispiel ist eine Vorlage im JSON-Dateiformat, das einen Benutzernamen und eine Kennworteigenschaft definiert:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="module-best-practices"></a>Modul – Best Practices

Diese PowerShell-Module können in Azure Automation importiert werden, damit ihre Cmdlets in Runbooks und ihre DSC-Ressourcen in DSC-Konfigurationen verwendet werden können. Azure Automation speichert diese Module im Hintergrund und lädt sie während der Ausführung der Runbook- und DSC-Kompilierungsaufträge in die Azure Automation-Sandboxes, wo Runbooks ausgeführt und DSC-Konfigurationen kompiliert werden. DSC-Ressourcen in Modulen werden ebenfalls automatisch auf dem Automation DSC-Pullserver gespeichert. Sie können von Computern gepullt werden, wenn sie DSC-Konfigurationen anwenden.

Beachten Sie folgende Punkte, wenn Sie ein PowerShell-Modul für die Verwendung in Azure Automation erstellen:

* Fügen Sie eine Zusammenfassung, eine Beschreibung und einen Hilfe-URI für jedes Cmdlet im Modul ein. In PowerShell können Sie mit dem Cmdlet **Get-Help** bestimmte Hilfeinformationen für Cmdlets definieren, um den Benutzern bei der Verwendung der Cmdlets zu helfen. Im folgenden Beispiel sehen Sie, wie Sie eine Zusammenfassung und einen Hilfe-URI für eine Moduldatei vom Typ „.psm1“ erstellen:

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Durch die Angabe dieser Informationen kann diese Hilfe über das Cmdlet **Get-Help** in der PowerShell-Konsole angezeigt werden. Diese Beschreibung wird auch im Azure-Portal angezeigt.

  ![Hilfe zu Integrationsmodulen](../media/modules/module-activity-description.png)

* Wenn das Modul eine Verbindung mit einem externen Dienst herstellt, sollte sie einen [Verbindungstyp](#add-a-connection-type-to-your-module) enthalten. Jedes Cmdlet im Modul sollte ein Verbindungsobjekt (eine Instanz dieses Verbindungstyps) als Parameter laden können. Dadurch können Benutzer die Parameter des Verbindungsobjekts bei jedem Aufruf eines Cmdlets den entsprechenden Parametern des Cmdlets zuordnen. Im obigen Runbook-Beispiel wird ein Verbindungsobjekt namens ContosoConnection verwendet, um auf Contoso-Ressourcen zuzugreifen und Daten vom externen Dienst zurückzugeben.

  Im folgenden Beispiel werden die Felder den Eigenschaften „UserName“ und „Password“ eines Objekts vom Typ `PSCredential` zugeordnet und dann an das Cmdlet übergeben.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Eine einfachere und bessere Herangehensweise für dieses Verhalten besteht jedoch darin, das Verbindungsobjekt direkt an das Cmdlet zu übergeben:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Sie können das Verhalten aus dem vorherigen Beispiel für Ihre Cmdlets aktivieren, indem Sie zulassen, dass ein Verbindungsobjekt direkt als Parameter akzeptiert wird (nicht nur Verbindungsfelder für Parameter). In der Regel empfiehlt sich jeweils ein Parametersatz, damit ein Benutzer, der Azure Automation nicht benutzt, Ihre Cmdlets aufrufen kann, ohne eine Hashtabelle erstellen zu müssen, die als Verbindungsobjekt dient. Mit dem Parametersatz `UserAccount` werden die Eigenschaften des Verbindungsfelds übergeben. Mit `ConnectionObject` können Sie die Verbindung direkt übergeben.

* Definieren Sie den Ausgabetyp für alle Cmdlets im Modul. Wenn Sie einen Ausgabetyp für ein Cmdlet definieren, können Sie bei der Erstellung mithilfe von IntelliSense die Ausgabeeigenschaften des Cmdlets festlegen, die während der Erstellung verwendet werden sollen. Dies ist bei der grafischen Erstellung von Automation-Runbooks hilfreich, da Kenntnisse zum Zeitpunkt des Entwurfs von zentraler Bedeutung sind, um eine hohe Benutzerfreundlichkeit Ihres Moduls zu gewährleisten.

  Fügen Sie dann `[OutputType([<MyOutputType>])]` hinzu, wenn MyOutputType ein gültiger Typ ist. Weitere Informationen zu OutputType finden Sie unter [About Functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute) (Informationen zu Functions: OutputTypeAttribute). Der folgende Code ist ein Beispiel, wie Sie `OutputType` einem Cmdlet hinzufügen:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Ausgabetyp für grafische Runbooks](../media/modules/runbook-graphical-module-output-type.png)

  Dieses Verhalten ähnelt der Textvervollständigung der Ausgabe eines Cmdlets in der PowerShell ISE, ohne dass diese ausgeführt werden muss.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

* Legen Sie alle Cmdlets im Modul als statusfrei fest. Mehrere Runbookaufträge können gleichzeitig in derselben Anwendungsdomäne, im gleichen Prozess und in der gleichen Sandbox ausgeführt werden. Wenn diese Ebenen den gleichen Status verwenden, können die Aufträge sich gegenseitig beeinflussen. Dieses Verhalten kann zu vorübergehenden und schwierig zu diagnostizierenden Problemen führen.  Hier sehen Sie ein Beispiel für eine falsche Vorgehensweise:

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

* Das Modul sollte vollständig in einem Xcopy-fähigen Paket enthalten sein. Azure Automation-Module werden an die Automation-Sandboxes verteilt, wenn Runbooks ausgeführt werden müssen. Die Module müssen unabhängig von dem Host funktionieren, auf dem sie ausgeführt werden. Es muss daher möglich sein, ein Modulpaket zu komprimieren, zu verschieben und nach dem Import in die PowerShell-Umgebung eines anderen Hosts ganz normal zu verwenden. Hierzu darf das Modul nicht von anderen Dateien außerhalb des Modulordners abhängig sein. Dieser Ordner wird komprimiert, wenn das Modul in Azure Automation importiert wird. Darüber hinaus darf das Modul nicht von spezifischen Registrierungseinstellungen auf einem Host abhängig sein (etwa von den Einstellungen, die bei der Installation eines Produkts festgelegt werden). Alle Dateien im Modul sollten einen Pfad mit weniger als 140 Zeichen aufweisen. Pfade mit mehr 140 Zeichen führen zu Problemen beim Importieren Ihres Runbooks. Andernfalls kann das Modul in Azure Automation nicht verwendet werden.  

* Falls Sie in Ihrem Modul auf [Azure PowerShell Az-Module](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) verweisen, achten Sie darauf, dass Sie auch auf `AzureRM` verweisen. Das `Az`-Modul kann nicht zusammen mit `AzureRM`-Modulen verwendet werden. `Az` wird in Runbooks zwar unterstützt, aber standardmäßig nicht importiert. Weitere Informationen zu `Az`-Modulen sowie wichtige Aspekte, die es zu berücksichtigen gilt, finden Sie unter [Az module support in Azure Automation](../az-modules.md) (Unterstützung des Az-Moduls in Azure Automation).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Erstellen von PowerShell-Modulen finden Sie unter [Writing a Windows PowerShell Module](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)