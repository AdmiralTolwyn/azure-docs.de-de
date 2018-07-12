---
title: Aktivieren oder Deaktivieren einer Rolle | Microsoft Docs
description: Erfahren Sie, wie Sie mit der Anwendung Azure Privileged Identity Management Rollen für privilegierte Identitäten aktivieren.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: bc4280d6e0ac362712d3b406e2e32c42cf4a9be2
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952680"
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Aktivieren oder Deaktivieren von Rollen in Azure AD Privileged Identity Management
Azure Active Directory (AD) Privileged Identity Management (PIM) vereinfacht die Art und Weise, in der Unternehmen den privilegierten Zugriff auf Ressourcen in Azure AD und anderen Onlinediensten von Microsoft wie Office 365 oder Microsoft Intune verwalten.  

Wenn Sie für eine Administratorrolle berechtigt sind, können Sie diese Rolle aktivieren, um privilegierte Aufgaben durchzuführen. Wenn Sie z. B. gelegentlich Office 365-Funktionen verwalten, dürfen Ihre privilegierte Rollenadministratoren Ihrer Organisation Sie nicht als permanenten globalen Administrator festlegen, da sich diese Rolle auf andere Dienste auswirkt. Stattdessen gewähren sie Ihnen Berechtigungen für Azure AD-Rollen wie z. B. Exchange Online-Administrator. Sie können eine Aktivierung dieser Rolle anfordern, wenn Sie diese Berechtigungen benötigen, und verfügen damit für einen bestimmten Zeitraum über die administrative Kontrolle.

Dieser Artikel richtet sich an Administratoren, die ihre Rolle in Azure AD Privileged Identity Management aktivieren müssen. Der Artikel erläutert schrittweise, wie Sie eine Rolle aktivieren, wenn Sie die Berechtigungen benötigen, und wie Sie die Rolle wieder deaktivieren, wenn Sie die Berechtigungen nicht mehr benötigen. Darüber hinaus können Administratoren für privilegierte Rollen eine Genehmigung benötigen, um eine Rolle zu aktivieren (Vorschauversion). Erfahren Sie hier mehr über [PIM-Genehmigungsworkflows](./privileged-identity-management/azure-ad-pim-approval-workflow.md).

## <a name="add-the-privileged-identity-management-application"></a>Hinzufügen der Anwendung Privileged Identity Management
Verwenden Sie die Anwendung Azure AD Privileged Identity Management im [Azure-Portal](https://portal.azure.com/) , um eine Rollenaktivierung anzufordern, auch wenn Sie in einem anderen Portal oder in PowerShell arbeiten werden. Wenn sich die Anwendung Azure AD Privileged Identity Management nicht in Ihrem Azure-Portal befindet, führen Sie die folgenden Schritte aus, um zu beginnen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie in der oberen rechten Ecke des Azure-Portals Ihren Benutzernamen, und wählen Sie das Verzeichnis aus, in dem Sie arbeiten möchten.
3. Wählen Sie **Alle Dienste** aus, und verwenden Sie das Textfeld „Filter“, um nach **Azure AD Privileged Identity Management** zu suchen.
4. Aktivieren Sie das Kontrollkästchen **An Dashboard anheften**, und klicken Sie dann auf **Erstellen**. Die Anwendung Privileged Identity Management wird geöffnet.

## <a name="activate-a-role"></a>Aktivieren einer Rolle
Wenn Sie eine Rolle übernehmen müssen, können Sie die Aktivierung über die Navigationsmenüoption **My Roles** (Meine Rollen) in der linken Navigationsspalte der Anwendung „Azure AD Privileged Identity Management“ anfordern.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und wählen Sie die Kachel „Azure AD Privileged Identity Management“ aus.
2. Wählen Sie **My Roles** (Meine Rollen) aus. Eine Liste Ihrer zugewiesenen berechtigten Rollen wird in der Gruppierung am oberen Rand der Seite angezeigt.
3. Wählen Sie eine Rolle aus, um diese zu aktivieren.
4. Wählen Sie **Aktivieren**aus. Das Blatt **Rollenaktivierung anfordern** wird angezeigt.
5. Für einige Rollen ist Multi-Factor Authentication (MFA) erforderlich, bevor die Rolle aktiviert werden kann. Sie müssen sich nur einmal pro Sitzung authentifizieren.
   
    ![Überprüfen mit MFA vor der Rollenaktivierung – Screenshot](./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png)
6. Geben Sie im Textfeld den Grund für die Aktivierungsanforderung ein.  Für einige Rollen müssen Sie eine Ticketnummer zur Problembehebung angeben.
7. Klicken Sie auf **OK**.  Wenn für die Rolle keine Genehmigung erforderlich ist, ist sie jetzt aktiviert und die Rolle wird in der Liste der aktiven Rollen (direkt unterhalb der Liste der berechtigten Rollenzuweisungen) angezeigt. Wenn für die Aktivierung der [Rolle eine Genehmigung erforderlich ist](./privileged-identity-management/azure-ad-pim-approval-workflow.md), wird eine Popupbenachrichtigung kurz in der oberen rechten Ecke des Browsers angezeigt, die Sie darüber informiert, dass die Genehmigung der Anforderung aussteht.

    ![Benachrichtigung über ausstehende Anforderung – Screenshot](./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Toast2.png)

## <a name="deactivate-a-role"></a>Deaktivieren einer Rolle
Wenn eine Rolle aktiviert wurde, wird sie nach Erreichen des Zeitlimits (berechtigte Dauer) automatisch deaktiviert.

Wenn Sie vorher mit Ihren Admin-Aufgaben fertig sind, können Sie die Rolle in der Anwendung Azure AD Privileged Identity Management auch manuell deaktivieren.  Wählen Sie **My Roles** (Meine Rollen) aus, wählen Sie die Rolle, die Sie nicht mehr benötigen unter der Gruppierung **Aktive Rollenzuweisungen** aus, und klicken Sie auf **Deaktivieren**.  

## <a name="cancel-a-pending-request"></a>Abbrechen einer ausstehenden Anforderung
Sollten Sie die Aktivierung einer Rolle, für die eine Genehmigung erforderlich ist, nicht benötigen, können Sie eine ausstehende Anforderung jederzeit abbrechen. Wählen Sie einfach die Navigationsmenüoption **My Roles** (Meine Rollen) in der linken Navigationsspalte der Anwendung „Azure AD Privileged Identity Management“ aus.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und wählen Sie die Kachel „Azure AD Privileged Identity Management“ aus.
2. Wählen Sie **My Roles** (Meine Rollen) aus. Eine Liste Ihrer zugewiesenen berechtigten Rollen wird in der Gruppierung am oberen Rand der Seite angezeigt.
3. Wählen Sie eine Rolle aus.
4. Wählen Sie den Banner **Activation is pending approval** (Genehmigung der Aktivierung steht aus) auf dem Blatt „Details zur Rollenaktivierung“ aus.
5. Wählen Sie **Abbrechen** am oberen Rand des Blatts **Ausstehende Genehmigung** aus.

   ![Abbrechen der ausstehenden Anforderung – Screenshot](./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen über Azure AD Privileged Identity Management finden Sie unter folgenden Links.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
