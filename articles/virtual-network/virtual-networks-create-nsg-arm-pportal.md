---
title: "Erstellen von NSGs im ARM-Modus über das Azure-Portal | Microsoft Docs"
description: "Erfahren Sie, wie Sie NSGs im ARM-Modus über das Azure-Portal erstellen und bereitstellen."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5bc8fc2e-1e81-40e2-8231-0484cd5605cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7c00b45be99d254c1967bff8a1150ad4c1eaab6d


---
# <a name="how-to-manage-nsgs-using-the-azure-portal"></a>Verwalten von NSGs mithilfe des Azure-Portals
[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell. Sie haben auch die Möglichkeit, [NSGs im klassischen Bereitstellungsmodell zu erstellen](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Die folgenden Beispielbefehle für PowerShell setzen voraus, dass bereits eine einfache Umgebung erstellt wurde, die auf dem zuvor beschriebenen Szenario basiert. Wenn Sie die Befehle so ausführen möchten, wie sie in diesem Dokument angezeigt werden, erstellen Sie zunächst die Testumgebung durch Bereitstellen [dieser Vorlage](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd). Klicken Sie auf **In Azure bereitstellen**, ersetzen Sie bei Bedarf die Standardparameterwerte, und befolgen Sie dann die Anweisungen im Portal. In den Schritten unten wird **RG-NSG** als Name der Ressourcengruppe verwendet, für die die Vorlage bereitgestellt wurde.

## <a name="create-the-nsg-frontend-nsg"></a>Erstellen der Netzwerksicherheitsgruppe „NSG-FrontEnd“
Führen Sie die nachstehenden Schritte aus, um die Netzwerksicherheitsgruppe **NSG-FrontEnd** wie im obigen Szenario gezeigt zu erstellen.

1. Navigieren Sie in einem Browser zu http://portal.azure.com, und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
2. Klicken Sie auf **Durchsuchen >** > **Netzwerksicherheitsgruppen**.
   
    ![Azure-Portal – NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. Klicken Sie auf dem Blatt **Netzwerksicherheitsgruppen** auf **Hinzufügen**.
   
    ![Azure-Portal – NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. Erstellen Sie im Blatt **Netzwerksicherheitsgruppe erstellen** in der Ressourcengruppe *RG-NSG* eine NSG mit dem Namen *NSG-FrontEnd*, und klicken Sie dann auf **Erstellen**.
   
    ![Azure-Portal – NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Erstellen von Regeln in einer vorhandenen NSG
Führen Sie die nachstehenden Schritte aus, um im Azure-Portal Regeln in einer vorhandenen NSG zu erstellen.

1. Klicken Sie auf **Durchsuchen >** > **Netzwerksicherheitsgruppen**.
2. Klicken Sie in der Liste der NSGs auf **NSG-FrontEnd** > **Eingehende Sicherheitsregeln**
   
    ![Azure-Portal – NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. Klicken Sie in der Liste **Eingangssicherheitsregeln** auf **Hinzufügen**.
   
    ![Azure-Portal – Regel hinzufügen](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. Erstellen Sie im Blatt **Eingangssicherheitsregeln hinzufügen** eine Regel mit dem Namen *web-rule* mit der Priorität *200*, und lassen Sie den Zugriff über *TCP* und Port *80* auf alle virtuellen Computer von allen Quellen zu. Klicken Sie dann auf **OK**. Beachten Sie, dass die meisten dieser Einstellungen bereits Standardwerte sind.
   
    ![Azure-Portal – Regeleinstellungen](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. Nach einigen Sekunden wird die neue Regel in der NSG angezeigt.
   
    ![Azure-Portal – Neue Regel](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. Wiederholen Sie die Schritte bis Schritt 6, um eine eingehende Regel namens *RDP-Regel* mit einer Priorität von *250* zu erstellen, und erlauben Sie damit den Zugriff auf einen beliebigen virtuellen Computer per *TCP* auf Port *3389* von einer beliebigen Quelle aus.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Zuordnen der NSG zum FrontEnd-Subnetz
1. Klicken Sie auf **Durchsuchen >** > **Ressourcengruppen** > **RG-NSG**.
2. Klicken Sie auf dem Blatt **RG-NSG** auf **...** > **TestVNet**.
   
    ![Azure-Portal – TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. Klicken Sie auf dem Blatt **Einstellungen** auf **Subnetze** > **FrontEnd** > **Netzwerksicherheitsgruppe** > **NSG-FrontEnd**.
   
    ![Azure-Portal – Subnetzeinstellungen](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. Klicken Sie auf dem Blatt **FrontEnd** auf **Speichern**.
   
    ![Azure-Portal – Subnetzeinstellungen](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Erstellen der Netzwerksicherheitsgruppe „NSG-BackEnd“
Führen Sie die folgenden Schritte aus, um die Netzwerksicherheitsgruppe **NSG-BackEnd** zu erstellen und sie dem Subnetz **BackEnd** zuzuordnen.

1. Wiederholen Sie die Schritte unter [Erstellen der Netzwerksicherheitsgruppe „NSG-FrontEnd“](#Create-the-NSG-FrontEnd-NSG) zum Erstellen einer NSG namens *NSG-BackEnd*
2. Wiederholen Sie die Schritte unter [Erstellen von Regeln in einer vorhandenen NSG](#Create-rules-in-an-existing-NSG) zum Erstellen der **eingehenden** Regeln in der folgenden Tabelle.
   
   | Eingehende Regel | Ausgehende Regel |
   | --- | --- |
   | ![Azure-Portal – Eingehende Regel](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Azure-Portal – Ausgehende Regel](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. Wiederholen Sie die Schritte in [Zuordnen der NSG zum FrontEnd-Subnetz](#Associate-the-NSG-to-the-FrontEnd-subnet), um die **NSG-BackEnd**-NSG dem **BackEnd**-Subnetz zuzuordnen.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie Sie [vorhandene NSGs verwalten](virtual-network-manage-nsg-arm-portal.md)
* [Aktivieren Sie die Protokollierung](virtual-network-nsg-manage-log.md) für NSGs.




<!--HONumber=Nov16_HO3-->


