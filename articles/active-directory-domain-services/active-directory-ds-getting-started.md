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
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 16b206b97f71eeffbb4fddb9e447649034998672
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2017
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Aktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals
Dieser Artikel zeigt, wie Azure Active Directory Domain Services (Azure AD DS) mithilfe des Azure-Portals aktiviert werden kann.

Um den Assistenten zum **Aktivieren von Azure AD Domain Services** zu starten, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Klicken Sie im linken Bereich auf **Neu**.
3. Geben Sie auf dem Blatt **Neu** in der Suchleiste **Domain Services** ein.

    ![Suche nach Domänendiensten](./media/getting-started/search-domain-services.png)

4. Klicken Sie auf **Azure AD Domain Services** in der Liste der Suchvorschläge. Klicken Sie auf der Seite **Azure AD Domain Services** auf die Schaltfläche **Erstellen**.

    ![Domain Services-Ansicht](./media/getting-started/domain-services-blade.png)

5. Der Assistent zum **Aktivieren von Azure AD Domain Services** wird gestartet.


## <a name="task-1-configure-basic-settings"></a>Aufgabe 1: Grundeinstellungen konfigurieren
Auf der Seite **Grundlagen** des Assistenten können Sie den DNS-Domänennamen für die verwaltete Domäne angeben. Sie können ebenfalls die Ressourcengruppe und den Azure-Standort angeben, für die die verwaltete Domäne bereitgestellt werden soll.

![Konfigurieren von Grundlagen](./media/getting-started/domain-services-blade-basics.png)

1. Wählen Sie den **DNS-Domänennamen** für Ihre verwaltete Domäne.

   > [!NOTE]
   > **Richtlinien für das Auswählen eines DNS-Domänennamens**
   > * **Integrierter Domänenname:** Standardmäßig legt der Assistent den standardmäßigen/integrierten Domänennamen des Verzeichnisses für Sie fest (mit einem **.onmicrosoft.com**-Suffix). Wenn Sie den sicheren LDAP-Zugriff auf die verwaltete Domäne über das Internet aktivieren, können Sie davon ausgehen, dass beim Erstellen eines öffentlichen DNS-Eintrags oder Abrufen eines sicheren LDAP-Zertifikats von einer öffentlichen Zertifizierungsstelle für diesen Domänennamen Probleme auftreten. Microsoft besitzt die *. onmicrosoft.com*-Domäne, und CAs werden keine Zertifikate ausstellen, die für diese Domäne bürgen.
   * **Benutzerdefinierte Domänennamen**: Sie können auch einen benutzerdefinierten Domänennamen eingeben. In diesem Beispiel lautet der benutzerdefinierte Domänenname *contoso100.com*.
   * **Nicht routingfähige Domänensuffixe**: Nicht routingfähige Domänensuffixe sollten Sie im Allgemeinen vermeiden. Beispielsweise ist es besser, das Erstellen einer Domäne mit dem DNS-Domänennamen „contoso.local“ zu vermeiden. Das DNS-Suffix „.local“ ist nicht routingfähig und kann zu Problemen mit der DNS-Auflösung führen.
   * **Einschränkungen des Domänenpräfixes**: Das Präfix des angegebenen Domänennamens (beispielsweise *contoso100* im Domänennamen *contoso100.com*) darf maximal 15 Zeichen lang sein. Sie können keine verwaltete Domäne mit einem Präfix, das länger als 15 Zeichen ist, erstellen.
   * **Netzwerknamenskonflikte**: Stellen Sie sicher, dass der DNS-Domänenname, den Sie für die verwaltete Domäne ausgewählt haben, nicht bereits im virtuellen Netzwerk vorhanden ist. Überprüfen Sie insbesondere, ob Folgendes zutrifft:
       * Im virtuellen Netzwerk ist bereits eine Active Directory-Domäne mit dem gleichen DNS-Domänennamen vorhanden.
       * Das virtuelle Netzwerk, in dem Sie die verwaltete Domäne aktivieren möchten, hat eine VPN-Verbindung mit Ihrem lokalen Netzwerk. In diesem Szenario stellen Sie sicher, dass Sie keine Domäne mit demselben DNS-Domänennamen in Ihrem lokalen Netzwerk haben.
       * Im virtuellen Netzwerk ist bereits ein Clouddienst mit diesem Namen vorhanden.
    >

2. Wählen Sie das **Azure-Abonnement**, in dem Sie die verwaltete Domäne erstellen möchten.

3. Wählen Sie die **Ressourcengruppe**, zu der die verwaltete Domäne gehören soll. Wählen Sie entweder die Option **Create new** (Neu erstellen) oder **Use existing** (Vorhandene verwenden), um die Ressourcengruppe auszuwählen.

4. Wählen Sie den **Azure-Speicherort**, in dem die verwaltete Domäne erstellt werden soll. Auf der Seite **Netzwerk** des Assistenten sehen Sie nur virtuelle Netzwerke, die zu dem von Ihnen gewählten Standort gehören.

5. Wenn Sie fertig sind, klicken Sie auf **OK**, um zur Seite **Netzwerk** des Assistenten zu gelangen.


## <a name="next-step"></a>Nächster Schritt
[Aufgabe 2: Konfigurieren der Netzwerkeinstellungen](active-directory-ds-getting-started-network.md)
