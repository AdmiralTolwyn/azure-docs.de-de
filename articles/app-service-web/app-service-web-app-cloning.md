---
title: Klonen einer Web-App mithilfe von PowerShell
description: Erfahren Sie, wie Sie Ihre Web-Apps mit PowerShell in neue Web-Apps klonen.
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: aelnably
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: 3b82f352f89a7857bfb42be6cee136f245fbd5fa
ms.contentlocale: de-de
ms.lasthandoff: 09/07/2017

---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Klonen der Azure App Service-App mit PowerShell
Mit der Veröffentlichung von Microsoft Azure PowerShell Version 1.1.0 wurde New-AzureRMWebApp eine neue Option hinzugefügt. Diese ermöglicht es dem Benutzer, eine vorhandene Web-App in eine neu erstellte App in einer anderen Region oder der gleichen Region zu klonen. Dadurch können Kunden eine Reihe von Apps in unterschiedlichen Regionen bereitstellen.

Das Klonen einer App wird zurzeit nur im Premium-Tarif der App Service-Pläne unterstützt. Das neue Feature verwendet die gleichen Einschränkungen wie das Web-Apps-Sicherungsfeature. Informationen dazu finden Sie unter [Sichern von Web-Apps in Azure App Service](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>Klonen einer vorhandenen App
Das Szenario: Der Benutzer einer vorhandenen Web-App im Süden der USA möchte den Inhalt in eine neue Web-App im Norden der USA klonen. Dies kann mithilfe der Azure Resource Manager-Version des PowerShell-Cmdlets zum Erstellen einer neuen Web-App mit der Option „-SourceWebApp“ erfolgen.

Wenn wir den Namen der Ressourcengruppe kennen, die die Web-App der Quelle enthält, können wir den folgenden PowerShell-Befehl verwenden, um die Informationen der Web-App der Quelle zu erhalten (in diesem Fall mit der Bezeichnung Quellen-Web-App):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Um einen neuen App-Service-Plan zu erstellen, können wir den New-AzureRmAppServicePlan-Befehl wie in dem folgenden Beispiel verwenden:

    New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

Mit dem Befehl New-AzureRmWebApp können wir die neue Web-App in der nördlichen Region erstellen und diese an einen vorhandenen App-Service-Plan im Premiumtarif binden. Darüber hinaus können wir die gleiche Ressourcengruppe wie die Quellen-Web-App verwenden oder eine neue Ressourcengruppe definieren, wie in dem folgenden Beispiel veranschaulicht wird:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

Um eine bereits existierende Web-App zu klonen, einschließlich aller zugeordneten Bereitstellungsslots, muss der Benutzer den IncludeSourceWebAppSlots-Parameter verwenden. Der folgende PowerShell-Befehl zeigt den Gebrauch dieses Parameters mit dem Befehl New-AzureRmWebApp:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots

Um eine vorhandene Web-App innerhalb derselben Region zu klonen, muss der Benutzer eine neue Ressourcengruppe und einen neuen App-Service-Plan in derselben Region erstellen und dann den folgenden PowerShell-Befehl zum Klonen der Web-App benutzen:

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Klonen einer vorhandenen App in einer App Service-Umgebung
Das Szenario: Der Benutzer einer vorhandene Web-App im Süden der USA möchte den Inhalt in eine neue Web-App in eine bereits existierenden App Service-Umgebung (ASE) klonen.

Wenn wir den Namen der Ressourcengruppe kennen, die die Web-App der Quelle enthält, können wir den folgenden PowerShell-Befehl verwenden, um die Informationen der Web-App der Quelle zu erhalten (in diesem Fall mit der Bezeichnung Quellen-Web-App):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Wenn der Benutzer den Namen der ASE sowie den Namen der Ressourcengruppe kennt, zu der die ASE gehört, kann er den New-AzureRmWebApp-Befehl benutzen, um die neue Web-App in der bereits existierenden ASE zu erstellen wie die folgende Grafik zeigt:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

Der Speicherort-Parameter ist aus Legacy-Gründen erforderlich Beim Erstellen einer App in einer ASE wird er jedoch ignoriert. 

## <a name="cloning-an-existing-app-slot"></a>Klonen eines vorhandenen App-Slots
Szenario: Der Benutzer möchte einen vorhandenen Web-App-Slot entweder in eine neue Web-App oder in einen neuen Web-App-Slot klonen. Die neue Web-App kann in der gleichen Region wie der ursprüngliche Web-App-Slot oder in einer anderen Region sein.

Wenn wir den Namen der Ressourcengruppe kennen, welche die Quellen-Web-App enthält, können wir den folgenden PowerShell-Befehl verwenden, um die Informationen des Web-App-Slots der Quelle zu erhalten (in diesem Fall mit der Bezeichnung Quellen-Web-App-Slot), der an die Web-App Quellen-Web-App gebunden ist:

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

Das folgende Beispiel zeigt, wie man einen Klon der Quellen-Web-App in einer neuen Web-App erstellt:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## <a name="configuring-traffic-manager-while-cloning-a-app"></a>Konfigurieren des Traffic Managers beim Klonen einer App
Die Erstellung von Web-Apps für mehrere Regionen und die Konfiguration von Azure Traffic Manager zum Leiten von Datenverkehr an alle diese Web-Apps sind wichtig, um sicherzustellen, dass die Apps der Kunden hoch verfügbar sind. Beim Klonen einer vorhandenen Web-App haben Sie die Option, beide Web-Apps entweder mit einem neuen Traffic Manager-Profil oder mit einem vorhandenen Profil zu verbinden. Beachten Sie jedoch, dass nur die Azure Resource Manager-Version von Traffic Manager unterstützt wird.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-a-app"></a>Erstellen eines neuen Traffic Manager-Profils während eine App geklont wird
Das Szenario: Der Benutzer möchte eine Web-App in eine andere Region klonen, während er gleichzeitig ein Azure Resource Manager-Traffic Manager-Profil anlegt, das beide Web-Apps enthält. Das folgende Beispiel zeigt, wie man einen Klon der Quellen-Web-App in einer neuen Web-App erstellt und gleichzeitig ein neues Traffic Manager-Profil erstellt:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Hinzufügen neuer geklonter Web-Apps zu einem vorhandenen Traffic Manager-Profil
Szenario: Der Benutzer besitzt bereits ein Azure Resource Manager-Traffic Manager-Profil, dem er beide Web-Apps als Endpunkte hinzufügen möchte. Zu diesem Zweck müssen wir zuerst die vorhandene Traffic Manager-Profil-ID zusammenstellen. Dazu benötigen wir die Abonnement-ID, den Namen der Ressourcengruppe und den vorhandenen Traffic Manager-Profilnamen.

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

Das folgende Beispiel zeigt, wie man, nach der Zusammenstellung der Traffic Manager-ID, einen Klon der Quellen-Web-App in einer neuen Web-App erstellt und gleichzeitig ein neues Traffic Manager-Profil erstellt:

    $destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## <a name="current-restrictions"></a>Aktuelle Einschränkungen
Dieses Feature ist zurzeit in der Vorschau. Wir arbeiten daran, neue Funktionen im Laufe der Zeit hinzuzufügen. In der folgenden Liste sind die bekannten Einschränkungen für die aktuelle Version zum Klonen einer App aufgeführt:

* Einstellungen für automatische Skalierung werden nicht geklont
* Einstellungen des Sicherungszeitplans werden nicht geklont
* VNET-Einstellungen werden nicht geklont
* App Insights werden nicht automatisch auf der Ziel-Web-App eingerichtet
* Easy Auth-Einstellungen werden nicht geklont
* Kudu-Erweiterungen werden nicht geklont
* TiP-Regeln werden nicht geklont
* Datenbankinhalte werden nicht geklont

### <a name="references"></a>Referenzen
* [Klonen einer Web-App über das Azure-Portal](app-service-web-app-cloning-portal.md)
* [Sichern von Web-Apps in Azure App Service](web-sites-backup.md)
* [Unterstützung des Azure Resource Manager für Azure Traffic Manager – Vorschau](../traffic-manager/traffic-manager-powershell-arm.md)
* [Einführung in die App Service-Umgebung](app-service-app-service-environment-intro.md)
* [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](../azure-resource-manager/powershell-azure-resource-manager.md)


