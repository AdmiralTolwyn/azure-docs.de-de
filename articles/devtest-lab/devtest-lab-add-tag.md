---
title: "Hinzufügen von Tags zu einem Lab in Azure DevTest Labs | Microsoft-Dokumentation"
description: "Informationen zum Hinzufügen eines Tags zu einem Lab in Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: v-craic
ms.openlocfilehash: 38b6612bc33326b758b357dca09081c02d294e5d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/02/2018
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Hinzufügen von Tags zu einem Lab in Azure DevTest Labs

Sie können benutzerdefinierte Tags erstellen und auf Ihre DevTest Labs-Ressourcen anwenden, um Ressourcen logisch zu kategorisieren. Später können Sie schnell und einfach alle Ressourcen in Ihrem Abonnement anzeigen, die mit diesem Tag versehen sind. Tags sind besonders hilfreich, wenn Sie Ressourcen zur Abrechnung oder Verwaltung organisieren müssen.

Von Tags unterstützte Ressourcen:

* Compute-VMs
* NICs
* IP-Adressen
* Load Balancer
* Speicherkonten
* Verwaltete Datenträger

Sie können Tags beim [Erstellen eines Labs](devtest-lab-create-lab.md) anwenden und später auf dem Blatt „Tags“ unter „Konfiguration und Einstellungen“ verwalten.

Jedes Tag besteht aus einem **Name**/**Wert**-Paar. Sie können beispielsweise ein Tag mit dem Namen *costcenter* und dem Wert *34543* erstellen. Anhand dieses Tags können Sie später Labressourcen ermitteln, die für diesen speziellen Bereich Ihrer Organisation abgerechnet werden. Sie können Namen und Werte auswählen, die sich für die Organisation Ihres Abonnements eignen.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Schritte zur Verwaltung von Tags in einem vorhandenen Lab

1. Melden Sie sich auf dem [Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)an.
1. Wählen Sie ggf. **Weitere Dienste** und dann in der Liste **DevTest Labs** aus. Ihr Lab wird unter Umständen bereits auf dem Dashboard unter **Alle Ressourcen** angezeigt.
1. Wählen Sie in der Liste der Labs das Lab aus, in dem Sie Tags hinzufügen oder verwalten möchten.  
1. Wählen Sie im Bereich **Übersicht** des Labs die Option **Konfiguration und Richtlinien** aus.  

    ![Schaltfläche „Konfiguration und Richtlinien“](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. Wählen Sie links unter **VERWALTEN** die Option **Tags**.
1. Geben Sie zum Erstellen eines neuen Tags für dieses Lab ein **Name**/**Wert**-Paar ein, und wählen Sie **Speichern**. Sie können auch ein vorhandenes Tag aus der Liste auswählen, um die mit diesem Tag verknüpften Ressourcen anzuzeigen oder zu verwalten.

    ![Verwalten von Tags](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

## <a name="understanding-limitations-to-tags"></a>Grundlegendes zu Einschränkungen für Tags

Für Tags gelten folgende Einschränkungen:

* Jede Ressource oder Ressourcengruppe kann maximal 15 Tagname-Wert-Paare besitzen. Diese Einschränkung gilt nur für Tags, die direkt auf die Ressourcengruppe oder die Ressource angewendet werden. Eine Ressourcengruppe kann zahlreiche Ressourcen mit jeweils 15 Tagname-Wert-Paaren enthalten. 
* Der Tagname ist auf 512 Zeichen beschränkt und der Tagwert auf 256 Zeichen. Bei Speicherkonten ist der Tagname auf 128 Zeichen beschränkt und der Tagwert auf 256 Zeichen.
* Auf die Ressourcengruppe angewendete Tags werden nicht von den in dieser Ressourcengruppe enthaltenen Ressourcen geerbt.

Der Artikel [Verwenden von Tags zum Organisieren von Azure-Ressourcen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) enthält ausführlichere Informationen zur Verwendung von Tags in Azure, u.a. zur Verwaltung von Tags mit PowerShell oder der Azure CLI.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nächste Schritte
* Sie können mithilfe benutzerdefinierter Richtlinien Einschränkungen und Konventionen für Ihr Abonnement festlegen. Bei einer von Ihnen definierten Richtlinie kann es erforderlich sein, dass alle Ressourcen über einen Wert für ein bestimmtes Tag verfügen. Weitere Informationen finden Sie unter [Verwalten aller Richtlinien für ein Lab in Azure DevTest Labs](devtest-lab-set-lab-policy.md).
* Erkunden Sie den [DevTest Labs-Azure Resource Manager-Katalog mit Schnellstartvorlagen](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
