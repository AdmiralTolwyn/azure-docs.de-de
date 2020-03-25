---
title: Registrieren für Office 365 mit einem Azure-Konto
description: Erfahren Sie, wie Sie mit einem Azure-Konto ein Office 365-Abonnement erstellen
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: banders
ms.openlocfilehash: 736009a88f309e0a38402222668a9609f844552f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79204816"
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Registrieren für ein Office 365-Abonnement mit Ihrem Azure-Konto
Wenn Sie Azure-Abonnent sind, können Sie zum Registrieren für ein Office 365-Abonnement Ihr Azure-Konto verwenden. Wenn Sie Mitglied einer Organisation sind, die über ein Azure-Abonnement verfügt, können Sie Office 365-Abonnements für Benutzer in der vorhandenen Azure Active Directory-Instanz (Azure AD) erstellen. Registrieren Sie sich für Office 365 mit einem Konto mit der Berechtigung „Globaler Administrator“ oder „Abrechnungsadministrator“ in Ihrem Azure Active Directory-Mandanten. Weitere Informationen finden Sie unter [Überprüfen meiner Kontoberechtigungen in Azure AD](#RoleInAzureAD) und [Zuweisen von Administratorrollen in Azure Active Directory](../../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Wenn Sie bereits über ein Office 365-Konto und ein Azure-Abonnement verfügen, können Sie [einen Office 365-Mandanten zu einem Azure-Abonnement zuordnen](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Erhalten eines Office 365-Abonnements mithilfe Ihres Azure-Kontos

1. Wechseln Sie zur [Office 365-Produktseite](https://products.office.com/business), und wählen Sie einen Plan aus.
2. Klicken Sie oben rechts auf der Seite auf **Anmelden** .

    ![Screenshot der Seite für die Office 365-Testversion](./media/azure-account-for-office-365-subscription/12-office-365-trial-page.png)
3. Melden Sie sich mit Ihren Azure-Kontoanmeldeinformationen an. Wenn Sie ein Abonnement für Ihre Organisation erstellen, verwenden Sie ein Azure-Konto, das Mitglied der Rolle „Globaler Administrator“ oder „Abrechnungsadministrator“ auf Ihrem Azure Active Directory-Mandanten ist.

    ![Screenshot der Office 365-Anmeldung](./media/azure-account-for-office-365-subscription/13-office-365-sign-in.png)
4. Klicken auf **Jetzt ausprobieren**.

    ![Screenshot der Bestätigung Ihrer Bestellung von Office 365](./media/azure-account-for-office-365-subscription/14-office-365-confirm-your-order.png)
5. Klicken Sie auf der Bestellbestätigungsseite auf **Weiter**.

    ![Screenshot der Office 365-Bestellbestätigung](./media/azure-account-for-office-365-subscription/15-office-365-order-receipt.png)

Der Vorgang ist nun abgeschlossen.
Wenn Sie das Office 365-Abonnement für Ihre Organisation erstellt haben, führen Sie die folgenden Schritte aus, um zu überprüfen, ob die Azure AD-Benutzer jetzt Office 365-Benutzer sind.

1. Öffnen Sie das Microsoft 365 Admin Center.
2. Erweitern Sie **BENUTZER**, und klicken Sie dann auf **Aktive Benutzer**.

    ![Screenshot der Benutzer im Microsoft 365 Admin Center](./media/azure-account-for-office-365-subscription/16-microsoft-365-admin-center-users.png)

Nach der Registrierung wird das Office 365-Abonnement der gleichen Azure Active Directory-Instanz hinzugefügt, der Ihr Azure-Abonnement angehört. Weitere Informationen finden Sie unter [Hintergrundinformationen zu Azure- und Office 365-Abonnements](office-365-account-for-azure-subscription.md#more-about-subs) und [Beziehung zwischen Azure-Abonnements und Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="check-my-account-permissions-in-azure-ad"></a><a id="RoleInAzureAD"></a>Überprüfen meiner Kontoberechtigungen in Azure AD
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie auf **Alle Dienste**, und suchen Sie nach **Active Directory**.

    ![Screenshot von Active Directory im Azure-Portal](./media/azure-account-for-office-365-subscription/billing-more-services-active-directory.png)
3. Klicken Sie auf **Benutzer und Gruppen** > **Alle Benutzer**.
4. Wählen Sie den Benutzernamen aus.

    ![Screenshot der Azure Active Directory-Benutzer](./media/azure-account-for-office-365-subscription/billing-users-groups.png)

5. Klicken Sie auf **Verzeichnisrolle**.

    ![Screenshot der Verzeichnisrolle im Azure-Portal](./media/azure-account-for-office-365-subscription/billing-user-directory-role.png)
6.  Um ein Office 365-Abonnement für Benutzer im vorhandenen Azure Active Directory zu erstellen, ist die Rolle **Globaler Administrator** oder **Eingeschränkter Administrator** > **Rechnungsadministrator** erforderlich.

    ![Screenshot der Verzeichnisrolle „Rechnungsadministrator“ im Azure-Portal](./media/azure-account-for-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).
