---
title: Verwalten des Benutzerzugriffs mit Azure AD-Zugriffsüberprüfungen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Benutzerzugriff als Mitgliedschaft in einer Gruppe oder als Zuweisung zu einer Anwendung mit Azure Active Directory-Zugriffsüberprüfungen verwalten.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: bf91b21f803628bbcab3d3fd50c1af4b4bd40a64
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445896"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Verwalten des Benutzerzugriffs mit Azure AD-Zugriffsüberprüfungen

Mit Azure Active Directory (Azure AD) können Sie ganz einfach den entsprechenden Zugriff von Benutzern sicherstellen. Hierzu können Sie die Benutzer selbst oder einen Entscheidungsträger bitten, an einer Zugriffsüberprüfung teilzunehmen und den Zugriff des Benutzers erneut zu zertifizieren (oder zu „attestieren“). Basierend auf Vorschlägen von Azure AD können die Prüfer die Notwendigkeit des weiteren Zugriffs der einzelnen Benutzer abwägen. Nach Abschluss einer Zugriffsüberprüfung können Sie dann Änderungen vornehmen und Zugriffsrechte für Benutzer entfernen, die diese nicht mehr benötigen.

> [!NOTE]
> Wenn Sie nur den Zugriff von Gastbenutzern und nicht aller Benutzertypen überprüfen möchten, finden Sie entsprechende Informationen unter [Verwalten des Gastzugriffs mit Azure AD-Zugriffsüberprüfungen](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). Wenn Sie die Benutzermitgliedschaft in Administratorrollen wie „Globaler Administrator“ überprüfen möchten, finden Sie entsprechende Informationen unter [Starten einer Zugriffsüberprüfung in Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Voraussetzungen 


Zugriffsüberprüfungen sind mit der Premium P2-Edition von Azure AD (in Microsoft Enterprise Mobility + Security E5 enthalten) verfügbar. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md). Jeder Benutzer, der mit diesem Feature interagiert (einschließlich Erstellen oder Ausfüllen einer Überprüfung bzw. Bestätigen des Zugriffs), benötigt eine Lizenz. 

## <a name="create-and-perform-an-access-review"></a>Erstellen und Durchführen einer Zugriffsüberprüfung

Sie können einen oder mehrere Benutzer als Prüfer einer Zugriffsüberprüfung festlegen.  

1. Wählen Sie eine Gruppe in Azure AD mit mindestens einem Mitglied aus. Oder wählen Sie eine mit Azure AD verbundene Anwendung aus, der mindestens ein Benutzer zugewiesen ist. 

2. Legen Sie fest, ob jeder Benutzer seinen eigenen Zugriff oder ob ein oder mehrere Benutzer den Zugriff aller Benutzer überprüfen soll/sollen.

3. Aktivieren Sie die Zugriffsüberprüfungen, sodass sie in den Zugriffsbereichen der Prüfer angezeigt werden. Als globaler Administrator oder Benutzerkontoadministrator navigieren Sie zur Seite [Zugriffsüberprüfungen](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Starten Sie die Zugriffsüberprüfung. Weitere Informationen finden Sie unter [Erstellen einer Zugriffsüberprüfung von Gruppenmitgliedern oder dem Anwendungszugriff mit Azure AD](active-directory-azure-ad-controls-create-access-review.md).

5. Bitten Sie die Prüfer, ihre Einschätzung abzugeben. Standardmäßig erhalten alle Prüfer eine E-Mail von Azure AD mit einem Link zu dem Zugriffsbereich, in dem sie [die Zugriffsüberprüfung durchführen](active-directory-azure-ad-controls-perform-access-review.md).

6. Wenn die Prüfer keine Angaben gemacht haben, kann Azure AD ihnen eine Erinnerung senden. Standardmäßig sendet Azure AD automatisch eine Erinnerung, wenn die Prüfer nach der Hälfte der Zeit noch nicht reagiert haben.

7. Nachdem die Prüfer ihre Einschätzung abgegeben haben, können Sie die Zugriffsüberprüfung beenden und die Änderungen anwenden. Weitere Informationen finden Sie unter [Durchführen einer Zugriffsüberprüfung von Mitgliedern einer Gruppe oder des Benutzerzugriffs auf eine Anwendung mit Azure AD](active-directory-azure-ad-controls-complete-access-review.md).


## <a name="next-steps"></a>Nächste Schritte

[Erstellen einer Zugriffsüberprüfung für Mitglieder einer Gruppe oder den Zugriff auf eine Anwendung](active-directory-azure-ad-controls-create-access-review.md)




