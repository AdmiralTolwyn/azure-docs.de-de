---
title: Bereitstellen von VM-Skalierungsgruppen in Azure Stack | Microsoft-Dokumentation
description: "Erfahren Sie, wie Cloudbediener VM-Skalierungsgruppen zum Azure Stack-Marketplace hinzufügen können."
services: azure-stack
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: brenduns
ms.reviewer: anajod
keywords: 
ms.openlocfilehash: cb8ac5435b7a5c6deb9d4571696c79b2ed15c93a
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/02/2018
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Bereitstellen von VM-Skalierungsgruppen in Azure Stack

*Gilt für: integrierte Azure Stack-Systeme und Azure Stack Development Kit*

VM-Skalierungsgruppen sind eine Azure Stack-Computeressource. Verwenden Sie sie zum Bereitstellen und Verwalten eines Satzes identischer virtueller Computer. Da alle virtuellen Computer gleich konfiguriert sind, ist bei Skalierungsgruppen keine Vorabbereitstellung der virtuellen Computer erforderlich. Es ist einfacher, umfangreiche Dienste zu erstellen, die auf hohe Rechenleistung, Big Data und Workloads in Containern ausgelegt sind.

Dieser Artikel beschreibt, wie Skalierungsgruppen im Azure Stack-Marketplace verfügbar gemacht werden. Wenn Sie dieses Verfahren abgeschlossen haben, können Ihre Benutzer VM-Skalierungsgruppen zu ihren Abonnements hinzufügen.

VM-Skalierungsgruppen in Azure Stack sind wie VM-Skalierungsgruppen in Azure. Weitere Informationen finden Sie in den folgenden Videos:
* [Mark Russinovich spricht über Azure-Skalierungsgruppen](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [VM-Skalierungsgruppen mit Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

In Azure Stack unterstützen VM-Skalierungsgruppen keine automatische Skalierung. Sie können einer Skalierungsgruppe mithilfe des Azure Stack-Portals, Resource Manager-Vorlagen oder PowerShell weitere Instanzen hinzufügen.

## <a name="prerequisites"></a>Voraussetzungen
* **PowerShell und Tools**

   PowerShell muss installiert und für Azure Stack und die Azure Stack-Tools konfiguriert sein. Weitere Informationen finden Sie unter [Einrichten von PowerShell in Azure Stack](azure-stack-powershell-configure-quickstart.md).

   Importieren Sie nach der Installation der Azure Stack-Tools das folgende PowerShell-Modul (Pfad relativ zum Ordner „.\ComputeAdmin“ im Ordner „AzureStack-Tools-master“):

        Import-Module .\AzureStack.ComputeAdmin.psm1

* **Betriebssystemimage**

   Wenn Sie Ihrem Azure Stack-Marketplace kein Betriebssystemimage hinzugefügt haben, finden Sie unter [Add the Windows Server 2016 VM image to the Azure Stack marketplace](azure-stack-add-default-image.md) (Hinzufügen des VM-Images für Windows Server 2016 zum Azure Stack-Marketplace) weitere Informationen.

   Wenn Sie Unterstützung für Linux benötigen, laden Sie Ubuntu Server 16.04 herunter, und fügen Sie es mithilfe von ```Add-AzsVMImage``` mit den folgenden Parametern hinzu: ```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```.

## <a name="add-the-virtual-machine-scale-set"></a>Hinzufügen der VM-Skalierungsgruppe

Bearbeiten Sie das folgende PowerShell-Skript für Ihre Umgebung, und führen Sie es anschließend aus, um eine VM-Skalierungsgruppe zu Ihrem Azure Stack-Marketplace hinzuzufügen. 

``$User`` ist das Konto, das zum Herstellen einer Verbindung mit dem Administratorportal verwendet wird. Beispiel: serviceadmin@contoso.onmicrosoft.com.

```
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Add-AzureRmAccount -Environment $AzsEnv -Credential $Creds
Select-AzureRmProfile -Profile $AzsEnvContext

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="remove-a-virtual-machine-scale-set"></a>Entfernen einer VM-Skalierungsgruppe

Führen Sie zum Entfernen eines Katalogelements einer VM-Skalierungsgruppe den folgenden PowerShell-Befehl aus:

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> Das Katalogelement wird unter Umständen nicht sofort entfernt. Möglicherweise müssen Sie das Portal mehrmals aktualisieren, bevor das Element aus dem Marketplace angezeigt wird.


## <a name="next-steps"></a>Nächste Schritte
[Häufig gestellte Fragen zu Azure Stack](azure-stack-faq.md)

