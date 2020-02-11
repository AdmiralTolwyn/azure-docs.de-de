---
title: 'Azure VMware Solutions (AVS): Einrichten von Firewalltabellen und -regeln'
description: Es wird beschrieben, wie Firewalltabellen und -regeln für die private AVS-Cloud eingerichtet werden, um den Datenverkehr für Subnetze und VLANs einzuschränken.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d133f4d0ac8cc8b70060563ad07da35e9fdf2d37
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025281"
---
# <a name="set-up-firewall-tables-and-rules-for-avs-private-clouds"></a>Einrichten von Firewalltabellen und -regeln für private AVS-Clouds

Mit Firewalltabellen und den zugehörigen Regeln können Sie Einschränkungen für Datenverkehr festlegen, die auf bestimmte Subnetze und VLANs angewendet werden sollen.

* Ein Subnetz kann einer Firewalltabelle zugeordnet werden.
* Eine Firewalltabelle kann mehreren Subnetzen zugeordnet werden.

## <a name="add-a-new-firewall-table"></a>Hinzufügen einer neuen Firewalltabelle

1. [Greifen Sie auf das AVS-Portal zu](access-cloudsimple-portal.md), und klicken Sie im seitlichen Menü auf **Network** (Netzwerk).
2. Wählen Sie **Firewall Tables** (Firewalltabellen) aus.
3. Wählen Sie **Create Firewall Tables** (Firewalltabellen erstellen) aus.

    ![Seite „VLAN/Subnetz“](media/firewall-tables-page.png)

4. Geben Sie einen Namen für die Tabelle ein.
5. Eine Standardregel für die Tabelle wird aufgeführt. Klicken Sie auf **Create New Rule** (Neue Regel erstellen), um eine zusätzliche Regel zu erstellen. Details dazu finden Sie im folgenden Verfahren.
6. Klicken Sie zum Speichern der Firewalltabelle auf **Done** (Fertig).

> [!IMPORTANT]
> Sie können bis zu zwei Firewalltabellen pro privater Cloud erstellen.

## <a name="firewall-rules"></a>Firewallregeln

Firewallregeln legen fest, wie die Firewall bestimmte Arten von Datenverkehr behandelt. Auf der Registerkarte **Rules** (Regeln) für eine ausgewählte Firewalltabelle werden alle zugehörigen Regeln aufgelistet.

![Firewallregeltabelle](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Erstellen einer Firewallregel

1. Zeigen Sie die Einstellungen zum Erstellen einer Firewallregel auf eine der folgenden Weisen an:
    * Klicken Sie beim Erstellen einer Firewalltabelle auf **Add Rule** (Regel hinzufügen).
    * Wählen Sie auf der Seite **Network (Netzwerk) > Firewall Tables (Firewalltabellen)** eine bestimmte Firewalltabelle aus, und klicken Sie dann auf **Create new firewall rule** (Neue Firewallregel erstellen).
2. Richten Sie die Regel wie folgt ein:
    * **Name**: Geben Sie der Regel einen Namen.
    * **Priorität**. Weisen Sie der Regel eine Priorität zu. Regeln mit niedrigeren Werten werden zuerst ausgeführt.
    * **Traffic Type (Datenverkehrstyp)** . Wählen Sie aus, ob die Regel für die private AVS-Cloud, das Internet oder VPN-Datenverkehr (zustandslos) bzw. für eine öffentliche IP-Adresse (zustandsbehaftet) gilt.
    * **Protokoll**. Wählen Sie das Protokoll aus, das von der Regel abgedeckt wird (TCP, UDP oder ein beliebiges anderes Protokoll).
    * **Direction (Richtung)** . Wählen Sie aus, ob die Regel für ein- oder ausgehenden Datenverkehr gilt. Sie müssen separate Regeln für eingehenden und ausgehenden Datenverkehr definieren.
    * **Action (Aktion)** . Wählen Sie die Aktion aus, die ausgeführt werden soll, wenn die Regel übereinstimmt (zulassen oder verweigern).
    * **Source** (Quelle): Geben Sie die von der Regel abgedeckten Quellen an (CIDR-Block, intern oder beliebige Quelle).
    * **Source port range (Quellportbereich)** . Geben Sie den Bereich der Ports an, die der Regel unterliegen.
    * **Direction (Richtung)** . Wählen Sie „inbound“ (eingehend) oder „outbound (ausgehend) aus.
    * **Destination (Ziel)** . Geben Sie die von der Regel abgedeckten Ziele an (CIDR-Block, intern oder beliebige Quelle).
    * **Source port range (Quellportbereich)** . Geben Sie den Bereich der Ports an, die der Regel unterliegen.

    ![Firewall table add rule (Firewalltabelle, Regel hinzufügen)](media/firewall-rule-create.png)

3. Klicken Sie auf **Done** (Fertig), um die Regel zu speichern und sie der Liste der Regeln für die Firewalltabelle hinzuzufügen.

> [!IMPORTANT]
> Jede Firewalltabelle kann bis zu 10 eingehende und 20 ausgehende Regeln aufweisen. Diese Grenzwerte können durch [Kontaktaufnahme mit dem Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) erhöht werden.

## <a name="attach-vlanssubnets"></a>Attach VLANs/Subnets (VLANs/Subnetze anzufügen)

Nachdem Sie eine Firewalltabelle definiert haben, können Sie die Subnetze angeben, die den Regeln in der Tabelle unterliegen.

1. Wählen Sie auf der Seite **Network (Netzwerk)**  > **Firewall Tables (Firewalltabellen)** eine Firewalltabelle aus.
2. Öffnen Sie die Registerkarte **Attached VLANs/Subnet** (Angefügte(s) VLANs/Subnetz).
3. Klicken Sie auf **Attach to a VLAN/Subnet** (An ein VLAN/Subnetz anfügen).
4. Wählen Sie die private AVS-Cloud und das VLAN aus. Der zugehörige Subnetzname und der CIDR-Block werden angezeigt.
5. Klicken Sie auf **Submit**(Senden).
