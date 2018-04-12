---
title: Grundlegendes zum Zugriff auf Ressourcen in Azure | Microsoft Docs
description: In diesem Thema werden Konzepte zum Einsatz von Abonnementadministratoren erklärt, um den Zugriff auf Ressourcen im gesamten Azure-Portal zu steuern.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: curtand
ms.custom: it-pro;
ms.openlocfilehash: 621ebec898e5b345556832097b12ca9b54506e7c
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/11/2018
---
# <a name="understanding-resource-access-in-azure"></a>Grundlegendes zum Zugriff auf Ressourcen in Azure

Die Zugriffssteuerung in Azure unterliegt zunächst den Abrechnungsaspekten. Der Besitzer eines Azure-Kontos, auf das über das [Azure-Kontocenter](https://account.azure.com) zugegriffen wird, ist der Kontoadministrator (Account Administrator, AA). Abonnements dienen nicht nur als Container für die Abrechnung, sondern auch als Sicherheitsgrenze: Jedes Abonnement verfügt über einen Dienstadministrator (SA), der Azure-Ressourcen für dieses Abonnement mit dem [Azure-Portal](https://portal.azure.com/) hinzufügen, entfernen und ändern kann. Der standardmäßige Dienstadministrator eines neuen Abonnements ist der Kontoadministrator. Der Kontoadministrator kann den Dienstadministrator jedoch im Azure-Kontocenter ändern.

<br><br>![Azure-Konten][1]

Außerdem sind Abonnements einem Verzeichnis zugeordnet. Durch das Verzeichnis wird eine Gruppe von Benutzern definiert. Diese können Benutzer aus der Organisation (Unternehmen oder Schule) sein, die das Verzeichnis erstellt hat, oder externe Gastbenutzer. Auf Abonnements kann über eine Teilmenge dieser Verzeichnisbenutzer zugegriffen werden, die entweder als Dienstadministrator (SA) oder Co-Administrator (CA) zugewiesen wurden. Die einzige Ausnahme besteht darin, dass Microsoft-Konten (früher Windows Live ID) aus Gründen der Abwärtskompatibilität als SA oder CA zugewiesen werden können, ohne im Verzeichnis vorhanden zu sein.

<br><br>![Zugriffssteuerung in Azure][2]

Die Funktionalität im Azure-Portal ermöglicht es SAs, die mit einem Microsoft-Konto angemeldet sind, das Verzeichnis zu wechseln, dem ein Abonnement zugeordnet ist. Dieser Vorgang wirkt sich auf die Zugriffssteuerung dieses Abonnements aus.

<br><br>![Einfacher Benutzeranmeldungsablauf][3]

Im einfachen Fall erzwingt eine Organisation (etwa Contoso), dass die Abrechnung und die Zugriffssteuerung für dieselbe Gruppe von Abonnements erfolgen. Dies bedeutet, dass das Verzeichnis Abonnements zugeordnet ist, die sich im Besitz eines einzigen Azure-Kontos befinden. Nach der erfolgreichen Anmeldung beim Azure-Portal sehen Benutzer zwei Ressourcenauflistungen (in der Abbildung oben orangefarben dargestellt):

* Verzeichnisse, in denen ihr Benutzerkonto enthalten ist (aus dem Verzeichnis selbst oder als Fremdprinzipal hinzugefügt). Da das zum Anmelden verwendete Verzeichnis für diese Berechnung nicht relevant ist, werden Ihre Verzeichnisse immer angezeigt, unabhängig davon, wo Sie sich angemeldet haben.
* Ressourcen als Teil von Abonnements, die dem für die Anmeldung verwendeten Verzeichnis zugeordnet sind UND auf das der Benutzer (als SA oder CA) zugreifen kann.

<br><br>![Benutzer mit mehreren Abonnements und Verzeichnissen][4]

Benutzer mit Abonnements, die mehrere Verzeichnisse umfassen, können den aktuellen Kontext des Azure-Portals mithilfe des Abonnementfilters wechseln. Im Hintergrund führt dies zu einer getrennten Anmeldung bei einem anderen Verzeichnis, allerdings ist durch einmaliges Anmelden (SSO) eine nahtlose Anmeldung gewährleistet.

Vorgänge wie das Verschieben von Ressourcen zwischen Abonnements können sich aufgrund dieser Einzelverzeichnisansicht von Abonnements schwieriger gestalten. Um die Ressourcen zu übertragen, müssen Sie die Abonnements u.U. zuerst mit dem Befehl **Verzeichnis bearbeiten** unter **Einstellungen** auf der Seite „Abonnements“ demselben Verzeichnis zuordnen.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Ändern von Administratoren für ein Azure-Abonnement finden Sie unter [Hinzufügen oder Ändern von Azure-Administratorrollen](../billing/billing-add-change-azure-subscription-administrator.md)
* Weitere Informationen zur Beziehung zwischen Azure Active Directory und Ihrem Azure-Abonnement finden Sie unter [Beziehung zwischen Azure-Abonnements und Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* Informationen zum Zuweisen von Rollen in Azure AD finden Sie unter [Zuweisen von Administratorrollen in Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-understanding-resource-access/IC707931.png
[2]: ./media/active-directory-understanding-resource-access/IC707932.png
[3]: ./media/active-directory-understanding-resource-access/IC707933.png
[4]: ./media/active-directory-understanding-resource-access/IC707934.png
