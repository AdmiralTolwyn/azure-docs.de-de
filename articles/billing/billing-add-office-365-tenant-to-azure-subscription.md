---
title: Verwenden eines Office 365-Mandanten mit einem Azure-Abonnement | Microsoft-Dokumentation
description: "Erfahren Sie, wie Sie ein Office 365-Verzeichnis (Mandant) einem Azure-Abonnement hinzufügen."
services: 
documentationcenter: 
author: JiangChen79
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: cc9c57c6-7bfd-4dea-9027-c75ef3737589
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/13/2017
ms.author: cjiang
ms.openlocfilehash: e6300932d044ec9a4f88eb5bd5977220ed11d513
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="link-an-office-365-tenant-to-an-azure-subscription"></a>Verknüpfen eines Office 365-Mandanten mit einem Azure-Abonnement
Verknüpfen Sie Ihre separaten Azure- und Office 365-Abonnements, sodass Sie von Ihrem Azure-Abonnement aus auf den Office 365-Mandanten zugreifen können. Um Ihre Abonnements zu verknüpfen, melden Sie sich bei Azure mit dem Azure-Dienstadministratorkonto an, fügen Sie ein Verzeichnis hinzu, und fügen Sie dann die Office 365 Geschäfts-, Schul- oder Unikonten dem Azure Active Directory-Mandanten hinzu.

**Möchten Sie Ihr bestehendes Azure-Abonnement auf Ihr Office 365 Geschäfts-, Schul- oder Unikonto übertragen?** Wenn Sie sich bei Azure mit einem persönlichen Microsoft-Konto registriert haben und Sie sich bei Ihrem Office 365-Konto anmelden möchten, wird dringend empfohlen, das Abonnement zu übertragen. Weitere Informationen finden Sie unter [Übertragen des Besitzes eines Azure-Abonnements auf ein anderes Konto](billing-subscription-transfer.md). 

**Möchten Sie sich bei Azure mithilfe von Office 365 registrieren?** Weitere Informationen finden Sie unter [Registrieren bei Azure mit einem Office 365-Konto](billing-use-existing-office-365-account-azure-subscription.md). 

## <a name="before-you-begin"></a>Voraussetzungen
* Sie benötigen die Anmeldeinformationen des Azure-Abonnementdienstadministrators. Mit Co-Administratorkonten können einige der Schritte in diesem Artikel nicht ausgeführt werden. Informationen zum Ändern des Dienstadministrators finden Sie unter [Hinzufügen oder Ändern von Azure-Administratorrollen](billing-add-change-azure-subscription-administrator.md#change-service-administrator-for-a-subscription).
* Sie benötigen die Anmeldeinformationen eines globalen Administrators des Office 365-Mandanten.
* Die E-Mail-Adresse des Dienstadministrators darf nicht im Office 365-Mandanten enthalten sein.
* Die E-Mail-Adresse des Dienstadministrators darf nicht mit der E-Mail-Adresse eines globalen Administrators des Office 365-Mandanten identisch sein.
* Wenn Sie eine E-Mail-Adresse verwenden, bei der es sich gleichzeitig um ein Microsoft-Konto und ein Organisationskonto handelt, legen Sie fest, dass der Dienstadministrator Ihres Azure-Abonnements vorübergehend ein anderes Microsoft-Konto verwendet. Sie können auf der [Seite für die Registrierung eines Microsoft-Kontos](https://signup.live.com/) ein Konto erstellen.

## <a name="link-office-365-tenant-to-azure-subscription"></a>Verknüpfen des Office 365-Mandanten mit dem Azure-Abonnement
Führen Sie zum Zuordnen des Office 365-Mandanten zum Azure-Abonnement die folgenden Schritte aus:

### <a name="step-1-add-office-365-tenant-to-your-azure-subscription"></a>Schritt 1: Hinzufügen des Office 365-Mandanten zu Ihrem Azure-Abonnement

1. Melden Sie sich mit den Anmeldeinformationen des Dienstadministrators beim [klassischen Azure-Portal](https://manage.windowsazure.com/) an.

    ![Screenshot zur Anmeldung bei Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
2. Wählen Sie im linken Bereich **ACTIVE DIRECTORY** aus. Der Office 365-Mandant sollte nicht angezeigt werden. Wenn er angezeigt wird, fahren Sie mit [Schritt 2: Ändern des Verzeichnisses, das mit dem Azure-Abonnement verknüpft ist](#Step2) fort.
   
   ![Screenshot des Active Directory-Eintrags](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)
3. Wählen Sie **NEU** > **VERZEICHNIS** > **BENUTZERDEFINIERT ERSTELLEN**.
   
    ![Screenshot der benutzerdefinierten Erstellung in Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
4. Wählen Sie auf der Seite **Verzeichnis hinzufügen** unter **VERZEICHNIS** die Option **Vorhandenes Verzeichnis verwenden** aus. Wählen Sie dann **Ich bin jetzt für die Abmeldung bereit** und anschließend **Fertig stellen** ![Symbol zum Fertigstellen](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
   
    ![Screenshot der Option „Vorhandenes Verzeichnis verwenden“](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
5. Nachdem Sie abgemeldet wurden, melden Sie sich mit den Anmeldeinformationen des globalen Administrators des Office 365-Mandanten an.
   
    ![Screenshot zur Anmeldung des globalen Administrators bei Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
6. Wählen Sie **Weiter**.
   
    ![Screenshot der Überprüfung](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
7. Wählen Sie **Jetzt abmelden**.
   
    ![Screenshot zum Abmeldevorgang](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
8. Melden Sie sich mit den Anmeldeinformationen des Dienstadministrators beim [klassischen Azure-Portal](https://manage.windowsazure.com/) an.
   
    ![Screenshot zur Anmeldung bei Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
9. Ihr Office 365-Mandant sollte auf dem Dashboard angezeigt werden.
   
    ![Screenshot des Dashboards](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

### <a name="Step2"></a>Schritt 2: Ändern des Verzeichnisses, das mit dem Azure-Abonnement verknüpft ist
   
1. Wählen Sie **Settings**aus.
   
    ![Screenshot des Einstellungssymbols für das klassische Azure-Portal](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
2. Wählen Sie Ihr Azure-Abonnement und anschließend **VERZEICHNIS BEARBEITEN**.

    ![Screenshot der Option zum Bearbeiten des Verzeichnisses im Azure-Abonnement](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
3. Wählen Sie **Weiter** ![Weiter-Symbol](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).
   
    ![Screenshot der Option „Zugeordnetes Verzeichnis ändern“](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
4. Überprüfen Sie die betroffenen Konten. Alle Co-Administratoren und [RBAC](../active-directory/role-based-access-control-configure.md)-Benutzer (Role-Based Access Control, rollenbasierte Zugriffssteuerung) mit zugewiesenem Zugriff in den vorhandenen Ressourcengruppen werden entfernt. In der Warnung wird nur auf die Entfernung der Co-Administratoren hingewiesen.
      
    ![Screenshot, der die zu entfernenden Co-Administratorkonten zeigt.](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
    ![Screenshot, der ein Beispiel eines zu entfernenden Benutzerkontos zeigt.](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
5. Wählen Sie **Fertig stellen** ![Symbol zum Fertigstellen](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

### <a name="step-3-add-your-office-365-organizational-accounts-as-admins-to-the-azure-subscription"></a>Schritt 3: Hinzufügen Ihrer Office 365-Organisationskonten als Administratoren zum Azure-Abonnement
   
Informationen zum Hinzufügen eines Administrators zu Ihrem Azure-Abonnement finden Sie unter [Hinzufügen oder Ändern von Azure-Administratorrollen, die das Abonnement oder die Dienste verwalten](billing-add-change-azure-subscription-administrator.md).

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Wenden Sie sich an den Support.

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.

