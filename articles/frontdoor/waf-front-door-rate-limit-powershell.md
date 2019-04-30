---
title: Konfigurieren einer Web Application Firewall-Regel zur Ratenbegrenzung für Front Door – Azure PowerShell
description: Erfahren Sie, wie Sie eine Ratenbegrenzungsregel für einen vorhandenen Front Door-Endpunkt konfigurieren.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2019
ms.author: kumud;tyao
ms.openlocfilehash: e0ad1e85a4cd47de823bc4f224b5a8834b1068b9
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59686130"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Konfigurieren einer Web Application Firewall-Regel zur Ratenbegrenzung mit Azure PowerShell
Die Azure-WAF-Ratenbegrenzungsregel (Web Application Firewall) für Azure Front Door steuert die Anzahl der Anforderungen, die von einer einzelnen Client-IP-Adresse in einem Zeitraum von einer Minute zulässig sind.
Dieser Artikel zeigt das Konfigurieren einer WAF-Ratenbegrenzungsregel mithilfe von Azure PowerShell, die steuert, wie viele Anforderungen von einem einzelnen Client an eine Webanwendung, die */promo* in der URL enthält, zulässig sind.

> [!IMPORTANT]
> Die WAF-Ratenbegrenzungsfunktion für Azure Front Door befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit der Einrichtung einer Richtlinie für ein Ratenlimit beginnen, müssen Sie zunächst Ihre PowerShell-Umgebung einrichten und ein Front Door-Profil erstellen.
### <a name="set-up-your-powershell-environment"></a>Einrichten Ihrer PowerShell-Umgebung
Azure PowerShell bietet eine Reihe von Cmdlets, die das [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-Modell für die Verwaltung von Azure-Ressourcen verwenden. 

Sie können [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) auf Ihrem lokalen Computer installieren und in einer beliebigen PowerShell-Sitzung nutzen. Befolgen Sie die Anweisungen auf der Seite, um sich mit Ihren Azure-Anmeldeinformationen anzumelden und das Azure PowerShell-Modul zu installieren.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Herstellen einer Verbindung mit Azure über einen interaktiven Anmeldedialog
```
Connect-AzAccount

```
Stellen Sie vor dem Installieren des Front Door-Moduls sicher, dass Sie die aktuelle Version von PowerShellGet installiert haben. Führen Sie den folgenden Befehl aus, und öffnen Sie PowerShell erneut.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Installieren des Moduls „Az.FrontDoor“ 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Erstellen eines Front Door-Profils
Gehen Sie zum Erstellen eines Front Door-Profils gemäß den Anweisungen unter [Schnellstart: Erstellen einer Front Door-Instanz für eine hoch verfügbare globale Webanwendung](quickstart-create-front-door.md) vor.

## <a name="define-url-match-conditions"></a>Definieren von URL-Übereinstimmungsbedingungen
Definieren Sie eine URL-Übereinstimmungsbedingung (URL enthält „/promo“) mit [New-AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject).
Das folgende Beispiel überprüft Übereinstimmungen mit */promo* im Wert der Variable *RequestUri*:

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Erstellen einer benutzerdefinierten Ratenbegrenzungsregel
Legen Sie eine Ratenbegrenzung mit [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-AzFrontDoorCustomRuleObject) fest. Im folgenden Beispiel wird die Begrenzung auf 1.000 festgelegt. Anforderungen über 1.000 von einem Client an die Aktionsseite („/promo“) in einem Zeitraum von einer Minute werden blockiert, bis die nächste Minute beginnt.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Konfigurieren einer Sicherheitsrichtlinie

Suchen Sie mithilfe von `Get-AzureRmResourceGroup` nach dem Namen der Ressourcengruppe, die das Front Door-Profil enthält. Konfigurieren Sie als Nächstes mithilfe von [New-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy) eine Sicherheitsrichtlinie mit einer Ratenbegrenzungsregel in der angegebenen Ressourcengruppe, die das Front Door-Profil enthält.

Im folgenden Beispiel wird der Ressourcengruppenname *myResourceGroupFD1* verwendet. Dabei wird davon ausgegangen, dass Sie das Front Door-Profil gemäß der Anleitung im Artikel [Schnellstart: Erstellen einer Front Door-Instanz für eine hoch verfügbare globale Webanwendung](quickstart-create-front-door.md) erstellt haben.

 Dies erfolgt unter Verwendung von [New-AzFrontDoorFireWallPolicy](/powershell/module/Az.FrontDoor/New-AzFrontDoorFireWallPolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorFireWallPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Verknüpfen der Richtlinie mit einem Front Door-Front-End-Host
Verknüpfen Sie das Sicherheitsrichtlinienobjekt mit einem vorhandenen Front Door-Front-End-Host, und aktualisieren Sie die Front Door-Eigenschaften. Rufen Sie zunächst mithilfe des Befehls [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) das Front Door-Objekt ab.
Legen Sie dann mit dem Befehl [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) die Eigenschaft *WebApplicationFirewallPolicyLink* des Front-Ends auf die *Ressourcen-ID* des im vorherigen Schritt erstellten „$ratePolicy“-Objekts fest. 

Im folgenden Beispiel wird der Ressourcengruppenname *myResourceGroupFD1* verwendet. Dabei wird davon ausgegangen, dass Sie das Front Door-Profil gemäß der Anleitung im Artikel [Schnellstart: Erstellen einer Front Door-Instanz für eine hoch verfügbare globale Webanwendung](quickstart-create-front-door.md) erstellt haben. Ersetzen Sie im folgenden Beispiel „$frontDoorName“ durch den Namen Ihres Front Door-Profils. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Die Eigenschaft *WebApplicationFirewallPolicyLink* muss nur einmal festgelegt werden, um eine Sicherheitsrichtlinie mit einem Front Door-Front-End-Host zu verknüpfen. Nachfolgende Richtlinienaktualisierungen werden automatisch auf das Front-End angewandt.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Front Door](front-door-overview.md) 


