---
title: 'Tutorial: Konfigurieren von Dropbox für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Dropbox für Unternehmen konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 0f3a42e4-6897-4234-af84-b47c148ec3e1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 3ae85b31e85ecc0f0df6500bc7cfda1719537f02
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814188"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Dropbox für Unternehmen für die automatische Benutzerbereitstellung

Dieses Tutorial zeigt Ihnen die Schritte, die Sie in Dropbox für Unternehmen und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in Dropbox für Unternehmen automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

*   Einen Azure Active Directory-Mandanten
*   Ein Abonnement von Dropbox für Unternehmen, mit dem das einmalige Anmelden möglich ist
*   Ein Benutzerkonto in Dropbox für Unternehmen mit Teamadministratorberechtigungen

## <a name="assigning-users-to-dropbox-for-business"></a>Zuweisen von Benutzern zu Dropbox für Unternehmen

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD die Benutzer darstellen, die Zugriff auf Ihre Dropbox für Unternehmen-App benötigen. Anschließend können Sie diese Benutzer Ihrer Dropbox für Unternehmen-App zuweisen, indem Sie diese Anweisungen befolgen:

[Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Dropbox für Unternehmen

*   Es wird empfohlen, Dropbox für Unternehmen einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

*   Wenn Sie Dropbox für Unternehmen einen Benutzer zuweisen, müssen Sie eine gültige Benutzerrolle auswählen. Die Rolle „Standardzugriff“ funktioniert nicht für die Bereitstellung.

## <a name="enable-automated-user-provisioning"></a>Aktivieren der automatisierten Benutzerbereitstellung

Dieser Abschnitt führt Sie durch das Verbinden von Azure AD mit der Dropbox für Unternehmen-API zur Bereitstellung von Benutzerkonten sowie durch das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in Dropbox für Unternehmen basierend auf der Benutzer- und Gruppenzuweisung in Azure AD.

>[!Tip]
>Optional können Sie das SAML-basierte einmalige Anmelden für Dropbox für Unternehmen aktivieren. Befolgen Sie dazu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-account-provisioning"></a>So konfigurieren Sie die automatische Bereitstellung von Benutzerkonten:

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

2. Wenn Sie Dropbox für Unternehmen bereits für einmaliges Anmelden konfiguriert haben, suchen Sie über das Suchfeld nach Ihrer Dropbox für Unternehmen-Instanz. Wählen Sie andernfalls **Hinzufügen**, und suchen Sie im Anwendungskatalog nach **Dropbox für Unternehmen**. Wählen Sie „Dropbox für Unternehmen“ in den Suchergebnissen aus, und fügen Sie es Ihrer Anwendungsliste hinzu.

3. Wählen Sie Ihre Dropbox für Unternehmen-Instanz und dann die Registerkarte **Bereitstellung** aus.

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest. 

    ![Bereitstellung](./media/dropboxforbusiness-provisioning-tutorial/provisioning.png)

5. Klicken Sie im Abschnitt **Administratoranmeldeinformationen** auf **Autorisieren**. Es wird ein Dropbox für Unternehmen-Anmeldedialogfeld in einem neuen Browserfenster geöffnet.

6. Melden Sie sich im Dialogfeld **Bei Dropbox anmelden zum Verbinden mit Azure AD** bei Ihrem Dropbox für Unternehmen-Mandanten an.

     ![Benutzerbereitstellung](./media/dropboxforbusiness-provisioning-tutorial/ic769518.png "Benutzerbereitstellung")

7. Bestätigen Sie, dass Sie Azure Active Directory die Berechtigung erteilen möchten, Änderungen an Ihrem Dropbox für Unternehmen-Mandanten vorzunehmen. Klicken Sie auf **Zulassen**.
    
      ![Benutzerbereitstellung](./media/dropboxforbusiness-provisioning-tutorial/ic769519.png "Benutzerbereitstellung")

8. Klicken Sie im Azure-Portal auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer Dropbox für Unternehmen-App herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr Dropbox für Unternehmen-Konto über Teamadministratorberechtigungen verfügt, und wiederholen Sie den Schritt zum **Autorisieren**.

9. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen.

10. Klicken Sie auf **Speichern**.

11. Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit Dropbox für Unternehmen synchronisieren** aus.

12. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Dropbox für Unternehmen synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Dropbox für Unternehmen für Updatevorgänge verwendet. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

13. Ändern Sie den **Bereitstellungsstatus** im Abschnitt „Einstellungen“ in **Ein**, um den Azure AD-Bereitstellungsdienst für Dropbox für Unternehmen zu aktivieren.

14. Klicken Sie auf **Speichern**.

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die Dropbox für Unternehmen im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Protokollen zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst in Ihrer Dropbox für Unternehmen-App ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurieren des einmaligen Anmeldens](dropboxforbusiness-tutorial.md)