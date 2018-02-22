---
title: Erste Schritte mit Azure Active Directory Domain Services | Microsoft-Dokumentation
description: Aktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: maheshu
ms.openlocfilehash: 7c84ac3318bbd63129b04711c62dc441b9d35285
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2018
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Aktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals


## <a name="before-you-begin"></a>Voraussetzungen
Informationen finden Sie unter [Netzwerkaspekte für die Azure Active Directory Domain Services](active-directory-ds-networking.md).


## <a name="task-2-configure-network-settings"></a>Aufgabe 2: Konfigurieren der Netzwerkeinstellungen
Die nächste Konfigurationsaufgabe besteht in der Erstellung eines virtuellen Azure-Netzwerks und eines dedizierten Subnetzes. Azure Active Directory Domain Services wird dann in diesem Subnetz Ihres virtuellen Netzwerks aktiviert. Sie können auch ein bestehendes virtuelles Netzwerk auswählen, in dem Sie das dedizierte Subnetz erstellen.

1. Klicken Sie auf **Virtual Network** (Virtuelles Netzwerk), um ein virtuelles Netzwerk auszuwählen.
    > [!NOTE]
    > **Klassische virtuelle Netzwerke werden für neue Bereitstellungen nicht unterstützt.** Klassische virtuelle Netzwerke werden für neue Bereitstellungen nicht unterstützt. Vorhandene verwaltete Domänen, die in klassischen virtuellen Netzwerken bereitgestellt wurden, werden weiterhin unterstützt. Wir werden bald ermöglichen, eine vorhandene verwaltete Domäne aus einem klassischen virtuellen Netzwerk zu einem virtuellen Resource Manager-Netzwerk zu migrieren.
    >

2. Auf der Seite **Virtuelles Netzwerk wählen** sehen Sie alle bestehenden virtuellen Netzwerke. Sie können nur die virtuellen Netzwerke sehen, die zu der Ressourcengruppe und dem Azure-Speicherort gehören, die Sie auf der Seite **Grundlagen** des Assistenten ausgewählt haben.
3. Wählen Sie dann das virtuelle Netzwerk aus, in dem Azure AD Domain Services aktiviert werden sollen. Sie können ein vorhandenes virtuelles Netzwerk auswählen oder ein neues erstellen.

  > [!TIP]
  > **Nach dem Aktivieren der Azure AD Domain Services können Sie Ihre verwaltete Domäne nicht mehr in ein anderes virtuelles Netzwerk verschieben.** Wählen Sie das rechte virtuelle Netzwerk aus, um Ihre verwaltete Domäne zu aktivieren. Nach der Erstellung einer verwalteten Domäne können Sie sie nicht in ein anderes virtuelles Netzwerk verschieben, ohne die verwaltete Domäne zu löschen. Bevor Sie fortfahren, sollten Sie den Artikel [Netzwerkaspekte für die Azure AD Domain Services](active-directory-ds-networking.md) lesen.  
  >

4. **Neues virtuelles Netzwerk erstellen:** Klicken Sie auf **Neu erstellen**, um ein neues virtuelles Netzwerk zu erstellen. Es wird dringend empfohlen, ein dediziertes Subnetz für Azure AD Domain Services zu verwenden. Erstellen Sie zum Beispiel ein Subnetz mit dem Namen „DomainServices“, um es für andere Administratoren verständlich zu machen, was innerhalb des Subnetzes bereitgestellt wird. Wenn Sie fertig sind, klicken Sie auf **OK**.

    ![Auswählen eines virtuellen Netzwerks](./media/getting-started/domain-services-blade-network-pick-vnet.png)

  > [!WARNING]
  > Stellen Sie sicher, dass Sie einen Adressraum auswählen, der in dem privaten IP-Adressraum liegt. IP-Adressen, die Sie nicht besitzen und die sich im öffentlichen Adressraum befinden, führen zu Fehlern in Azure AD Domain Services.

5. **Bestehendes virtuelles Netzwerk:** Wenn Sie planen, ein bestehendes virtuelles Netzwerk auszuwählen, [erstellen Sie ein dediziertes Subnetz mithilfe der Erweiterung für virtuelle Netzwerke](../virtual-network/virtual-networks-create-vnet-arm-pportal.md), und wählen Sie dieses Subnetz anschließend aus. Klicken Sie auf **Virtuelles Netzwerk**, um ein virtuelles Netzwerk auszuwählen. Klicken Sie auf **Subnetz**, um das dedizierte Subnetz in dem bestehenden virtuellen Netzwerk auszuwählen, in dem Ihre neue verwaltete Domäne aktiviert werden soll. Wenn Sie fertig sind, klicken Sie auf **OK**.

    ![Auswählen eines Subnetzes innerhalb des virtuellen Netzwerks](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **Richtlinien für das Auswählen eines Subnetzes**
  > 1. Verwenden Sie ein dediziertes Subnetz für Azure AD Domain Services. Stellen Sie für dieses Subnetz keine weiteren virtuellen Computer bereit. Diese Konfiguration ermöglicht es Ihnen, Netzwerksicherheitsgruppen (NSGs) für Ihre Workloads bzw. virtuellen Computer zu konfigurieren, ohne Ihre verwaltete Domäne zu unterbrechen. Einzelheiten dazu finden Sie unter [networking considerations for Azure Active Directory Domain Services (Netzwerkaspekte für Azure Active Directory Domain Services)](active-directory-ds-networking.md).
  2. Wählen Sie für die Bereitstellung von Azure AD Domain Services nicht das Gatewaysubnetz, weil es sich dabei um eine nicht unterstützte Konfiguration handelt.
  3. Stellen Sie sicher, dass das von Ihnen ausgewählte Subnetz über einen ausreichend verfügbaren Adressraum von mindestens 3-5 verfügbaren IP-Adressen verfügt und im privaten IP-Adressraum liegt.
  >

6. Wenn Sie fertig sind, klicken Sie auf **OK**, um zur Seite **Administratorgruppe** des Assistenten zu gelangen.


## <a name="next-step"></a>Nächster Schritt
[Task 3: configure administrative group and enable Azure AD Domain Services (Aufgabe 3: Konfigurieren einer administrativen Gruppe und Aktivieren von Azure AD Domain Services)](active-directory-ds-getting-started-admingroup.md)
