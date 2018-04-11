---
title: Installieren von PowerShell für Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie PowerShell für Azure Stack installieren.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F8D99A91-15B5-4073-BE07-A43514A6D2CF
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: mabrigg
ms.openlocfilehash: 7bf2d9b999db738007f75d72a8818ca0eb6f34ba
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/03/2018
---
# <a name="install-powershell-for-azure-stack"></a>Installieren von PowerShell für Azure Stack  

Für die Verwendung von Azure Stack sind mit Azure Stack kompatible Azure PowerShell-Module erforderlich. In diesem Handbuch werden die Schritte erläutert, die zum Installieren von PowerShell für Azure Stack erforderlich sind. Führen Sie die in diesem Artikel beschriebenen Schritte entweder über das Azure Stack Development Kit oder über einen Windows-basierten externen Client aus, wenn eine Verbindung über VPN besteht.

Dieser Artikel enthält ausführliche Anweisungen zur Installation von PowerShell für Azure Stack. Wenn Sie PowerShell jedoch schnell installieren und konfigurieren möchten, können Sie das Skript aus dem Artikel „Einrichten von PowerShell“ verwenden. 

> [!NOTE]
> Für die folgenden Schritte ist PowerShell 5.0 erforderlich. Führen Sie zum Überprüfen Ihrer Version „$PSVersionTable.PSVersion“ aus, und vergleichen Sie die Hauptversion.

PowerShell-Befehle für Azure Stack werden über den PowerShell-Katalog installiert. Zum Registrieren des PSGallery-Repositorys öffnen Sie über das Development Kit oder über einen Windows-basierten externen Client (falls eine VPN-Verbindung besteht) eine PowerShell-Sitzung mit erhöhten Rechten. Führen Sie dann den folgenden Befehl aus:

```powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

## <a name="uninstall-existing-versions-of-powershell"></a>Deinstallieren vorhandener PowerShell-Versionen

Deinstallieren Sie vor der Installation der erforderlichen Version unbedingt alle vorhandenen Azure PowerShell-Module. Verwenden Sie zum Deinstallieren eine folgenden Methoden:

* Melden Sie sich zum Deinstallieren der vorhandenen PowerShell-Module beim Development Kit an. Wenn Sie eine VPN-Verbindung herstellen möchten, melden Sie sich beim Windows-basierten externen Client an. Schließen Sie alle aktiven PowerShell-Sitzungen, und führen Sie den folgenden Befehl aus: 

   ```powershell
   Get-Module -ListAvailable | where-Object {$_.Name -like “Azure*”} | Uninstall-Module
   ```

* Melden Sie sich beim Development Kit an. Wenn Sie eine VPN-Verbindung herstellen möchten, melden Sie sich beim Windows-basierten externen Client an. Löschen Sie aus den Ordnern `C:\Program Files (x86)\WindowsPowerShell\Modules` und `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` alle Ordner, die mit „Azure“ beginnen. Durch das Löschen dieser Ordner werden alle vorhandenen PowerShell-Module aus den Benutzerbereichen „AzureStackAdmin“ und „global“ entfernt. 

In den folgenden Abschnitten werden die Schritte beschrieben, die zum Installieren von PowerShell für Azure Stack erforderlich sind. PowerShell kann in Azure Stack-Instanzen installiert werden, die in verbundenen, partiell verbundenen oder nicht verbundenen Szenarien betrieben werden. 

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>Installieren von PowerShell in einem verbundenen Szenario (mit Internetverbindung)

Mit Azure Stack kompatible AzureRM-Module werden über API-Versionsprofile installiert. Für Azure Stack ist das API-Versionsprofil **2017-03-09-profile** erforderlich, das durch Installation des AzureRM.Bootstrapper-Moduls zur Verfügung gestellt wird. Informationen zu API-Versionsprofilen und den von ihnen bereitgestellten Cmdlets finden Sie unter [Manage API version profiles in Azure Stack](azure-stack-version-profiles-powershell.md) (Verwalten von API-Versionsprofilen in Azure Stack). Zusätzlich zu den AzureRM-Modulen müssen die Azure Stack-spezifischen PowerShell-Module installiert werden. Führen Sie zum Installieren dieser Module auf der Entwicklungsarbeitsstation das folgende PowerShell-Skript aus:

> [!IMPORTANT]  
> Das Release des PowerShell-Moduls AzureRM 1.2.11 enthält eine Liste mit wichtigen Änderungen. Informationen zum Upgrade von Version 1.2.10 finden Sie im Migrationshandbuch unter [https://aka.ms/azspowershellmigration](https://aka.ms/azspowershellmigration).

  ```powershell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```

Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

  ```powershell
  Get-Module `
    -ListAvailable | where-Object {$_.Name -like “Azure*”}
  ```
  War die Installation erfolgreich, werden die AzureRM- und Azure Stack-Module in der Ausgabe angezeigt.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Installieren von PowerShell in einem nicht verbundenen oder partiell verbundenen Szenario (mit eingeschränkter Internetkonnektivität)

In einem nicht verbundenen oder einem partiell verbundenen Szenario müssen Sie zuerst die PowerShell-Module auf einen Computer mit Internetverbindung herunterladen und sie dann für die Installation in das Azure Stack Development Kit übertragen.

> [!IMPORTANT]
> Das Release des PowerShell-Moduls AzureRM 1.2.11 enthält eine Liste mit wichtigen Änderungen. Informationen zum Upgrade von Version 1.2.10 finden Sie im Migrationshandbuch unter [https://aka.ms/azspowershellmigration](https://aka.ms/azspowershellmigration).

1. Melden Sie sich bei einem Computer mit Internetverbindung an, und laden Sie mithilfe des folgenden Skripts die AzureRM- und AzureStack-Pakete auf den lokalen Computer herunter:

   ```powershell
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11 
   ```

2. Kopieren Sie die heruntergeladenen Pakete auf ein USB-Gerät.

3. Melden Sie sich beim Development Kit an, und kopieren Sie die Pakete vom USB-Gerät an einen Speicherort im Development Kit. 

4. Nun müssen Sie diesen Speicherort als Standardrepository registrieren und die AzureRM- und AzureStack-Module aus diesem Repository installieren:

   ```powershell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   ```powershell
   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="next-steps"></a>Nächste Schritte

* [Herunterladen von Azure Stack-Tools von GitHub](azure-stack-powershell-download.md)  
* [Konfigurieren der PowerShell-Umgebung des Azure Stack-Benutzers](azure-stack-powershell-configure-user.md)  
* [Verwalten von API-Versionsprofilen in Azure Stack](azure-stack-version-profiles-powershell.md)  
