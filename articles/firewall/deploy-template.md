---
title: Bereitstellen von Azure Firewall mit einer Vorlage
description: Stellen Sie Azure Firewall mit einer Vorlage bereit. Das erstellte Netzwerk verfügt über ein VNet mit drei Subnetzen. Zwei virtuelle Windows Server-Computer mit zwei Kernen werden bereitgestellt.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: b9e82998283bf44eede88148a12d88aae521b1ca
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169202"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Bereitstellen von Azure Firewall mit einer Vorlage

Über die [Setupvorlage zum Erstellen einer AzureFirewall-Sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) wird eine Testnetzwerkumgebung mit einer Firewall erstellt. Das Netzwerk verfügt über ein virtuelles Netzwerk (VNET) mit drei Subnetzen: *AzureFirewallSubnet*, *ServersSubnet* und *JumpboxSubnet*. Die Subnetze *ServersSubnet* und *JumpboxSubnet* verfügen jeweils über einen virtuellen Windows Server-Computer mit zwei Kernen.

Die Firewall befindet sich im Subnetz *AzureFirewallSubnet*, und der Zugriff auf `www.microsoft.com` wird durch eine Anwendungsregelsammlung mit einer einzelnen Regel ermöglicht.

Es wird eine benutzerdefinierte Route erstellt, die den Netzwerkverkehr vom Subnetz *ServersSubnet* durch die Firewall leitet, wo die Firewallregeln angewendet werden.

Weitere Informationen zu Azure Firewall finden Sie unter [Bereitstellen und Konfigurieren von Azure Firewall über das Azure-Portal](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Verwenden der Vorlage zum Bereitstellen von Azure Firewall

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

**So verwenden Sie die Vorlage zum Installieren und Bereitstellen von Azure Firewall:**

1. Greifen Sie auf die Vorlage zu: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox).
   
1. Lesen Sie die Einführung, und wenn Sie mit der Bereitstellung beginnen möchten, klicken Sie auf **In Azure bereitstellen**.
   
1. Melden Sie sich ggf. beim Azure-Portal an. 

1. Geben Sie im Portal auf der Seite **Create a sandbox setup of AzureFirewall** (Erstellen eine Sandboxsetups von AzureFirewall) die folgenden Werte ein, oder wählen Sie diese aus:
   
   - **Ressourcengruppe**: Wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für die Ressourcengruppe an. Klicken Sie auf **OK**. 
   - **Name des virtuellen Netzwerks**: Geben Sie den Namen für das neue VNET ein. 
   - **Administratorbenutzername**: Geben Sie einen Benutzernamen für das Administratorbenutzerkonto ein.
   - **Administratorkennwort**: Geben Sie ein Administratorkennwort ein. 
   
1. Lesen Sie die Geschäftsbedingungen, und wählen Sie anschließend die Option **Ich stimme den oben genannten Geschäftsbedingungen zu** aus.
   
1. Wählen Sie die Option **Kaufen**.
   
   Die Erstellung der Ressourcen wird einige Minuten dauern. 
   
1. Untersuchen Sie zuerst die Ressourcen, die mit der Firewall erstellt wurden. 

Weitere Informationen zur JSON-Syntax und den Eigenschaften für eine Firewall in einer Vorlage finden Sie unter [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie sie nicht mehr benötigen, können Sie die Ressourcengruppe, die Firewall und alle zugehörigen Ressourcen entfernen, indem Sie den PowerShell-Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) ausführen. Zum Entfernen einer Ressourcengruppe mit dem Namen *MyResourceGroup* führen Sie diesen Befehl aus: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Entfernen Sie die Ressourcengruppe und die Firewall noch nicht, wenn Sie mit dem Tutorial zur Firewallüberwachung fortfahren möchten. 

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie die Azure Firewall-Protokolle überwachen:

> [!div class="nextstepaction"]
> [Tutorial: Überwachen von Azure Firewall-Protokollen](./tutorial-diagnostics.md)
