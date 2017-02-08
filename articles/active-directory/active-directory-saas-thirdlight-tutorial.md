---
title: 'Tutorial: Azure Active Directory-Integration mit Thirdlight | Microsoft Docs'
description: "Hier erfahren Sie, wie Sie Thirdlight mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 168aae9a-54ee-4c2b-ab12-650a2c62b901
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 49352a5e8255468bbc54c02e0cd9242d49002dbd
ms.openlocfilehash: 559c9fd7ff57eebff81da3fd491803442c6d0d9e


---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Tutorial: Azure Active Directory-Integration mit Thirdlight
In diesem Tutorial wird die Integration von Azure und Thirdlight erläutert.  
Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Ein gültiges Azure-Abonnement
* Ein Thirdlight-Software-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Tutorials können sich die Thirdlight zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf Ihrer Thirdlight-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).

Das in diesem Tutorial beschriebene Szenario besteht aus den folgenden Bausteinen:

1. Aktivieren der Anwendungsintegration für Thirdlight
2. Konfigurieren der einmaligen Anmeldung
3. Konfigurieren der Benutzerbereitstellung
4. Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-thirdlight-tutorial/IC805836.png "Szenario")

## <a name="enabling-the-application-integration-for-thirdlight"></a>Aktivieren der Anwendungsintegration für Thirdlight
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Thirdlight aktivieren.

### <a name="to-enable-the-application-integration-for-thirdlight-perform-the-following-steps"></a>So aktivieren Sie die Anwendungsintegration für Thirdlight:
1. Klicken Sie im klassischen Azure-Portal im linken Navigationsbereich auf **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-thirdlight-tutorial/IC700993.png "Active Directory")

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen** .
   
    ![Anwendungen](./media/active-directory-saas-thirdlight-tutorial/IC700994.png "Anwendungen")

4. Klicken Sie unten auf der Seite auf **Hinzufügen** .
   
    ![Anwendung hinzufügen](./media/active-directory-saas-thirdlight-tutorial/IC749321.png "Anwendung hinzufügen")

5. Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.
   
    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-thirdlight-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6. Geben Sie in das **Suchfeld****Thirdlight** ein.
   
    ![Anwendungskatalog](./media/active-directory-saas-thirdlight-tutorial/IC805837.png "Anwendungskatalog")

7. Wählen Sie im Ergebnisbereich **Thirdlight** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.
   
    ![ThirdLight](./media/active-directory-saas-thirdlight-tutorial/IC805838.png "ThirdLight")

## <a name="configuring-single-sign-on"></a>Konfigurieren der einmaligen Anmeldung
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Thirdlight zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für Thirdlight müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI)weitere Informationen.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>So konfigurieren Sie einmaliges Anmelden
1. Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **Thirdlight** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.
   
    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thirdlight-tutorial/IC805839.png "Einmaliges Anmelden konfigurieren")

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Thirdlight anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.
   
    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thirdlight-tutorial/IC805840.png "Einmaliges Anmelden konfigurieren")

3. Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Thirdlight-Anmelde-URL** die URL ein, welche die Benutzer zur Anmeldung bei Thirdlight verwenden (z.B. „*http://azuresso2.thirdlight.com/*“), und klicken Sie dann auf **Weiter**.
   
    ![App-URL konfigurieren](./media/active-directory-saas-thirdlight-tutorial/IC805841.png "App-URL konfigurieren")

4. Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Thirdlight** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.
   
    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thirdlight-tutorial/IC805842.png "Einmaliges Anmelden konfigurieren")

5. Melden Sie sich in einem anderen Webbrowserfenster bei der Thirdlight-Unternehmenswebsite als Administrator an.

6. Wechseln Sie zu **Konfiguration\> Systemadministration**, und klicken Sie dann auf **SAML2**.
   
    ![Systemadministration](./media/active-directory-saas-thirdlight-tutorial/IC805843.png "Systemadministration")

7. Führen Sie im Abschnitt „SAML2-Konfiguration“ die folgenden Schritte aus:
   
    ![Einmaliges Anmelden für SAML](./media/active-directory-saas-thirdlight-tutorial/IC805844.png "Einmaliges Anmelden für SAML")
   
    a. Wählen Sie **Einmaliges Anmelden bei SAML2 aktivieren**aus.
   
    b. Als **Quelle für IdP-Metadaten** wählen Sie **IdP-Metadaten von XML laden**.
   
    c. Öffnen Sie die heruntergeladene Metadatendatei, kopieren Sie den Inhalt, und fügen Sie ihn in das Textfeld **IdP-Metadaten XML** ein.
   
    d. Klicken Sie auf **SAML2-Einstellungen speichern**.

8. Bestätigen Sie im klassischen Azure-Portal die Konfiguration der einmaligen Anmeldung, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.
   
    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-thirdlight-tutorial/IC805845.png "Einmaliges Anmelden konfigurieren")

## <a name="configuring-user-provisioning"></a>Konfigurieren der Benutzerbereitstellung
Damit sich Azure AD-Benutzer bei Thirdlight anmelden können, müssen sie in Thirdlight bereitgestellt werden.  
Im Fall von Thirdlight ist die Bereitstellung eine manuelle Aufgabe.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>So konfigurieren Sie die Benutzerbereitstellung
1. Melden Sie sich bei der **Thirdlight** -Unternehmenswebsite als Administrator an.

2. Wechseln Sie zur Registerkarte **Benutzer** .

3. Wählen Sie **Benutzer und Gruppen**.

4. Klicken Sie auf **Neuen Benutzer hinzufügen** .

5. Machen Sie Ihre Angaben unter **Benutzernamen,  Namen oder Beschreibung, E-Mail und Voreinstellung oder Gruppe neuer Mitglieder wählen** eines gültigen AAD-Kontos, das Sie bereitstellen möchten.

6. Klicken Sie auf **Create**.

> [!NOTE]
> Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Thirdlight-Benutzerkonten oder mithilfe der von Thirdlight bereitgestellten APIs erstellen.
> 
> 

## <a name="assigning-users"></a>Zuweisen von Benutzern
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

### <a name="to-assign-users-to-thirdlight-perform-the-following-steps"></a>So weisen Sie Thirdlight Benutzer zu:
1. Erstellen Sie im klassischen Azure-Portal ein Testkonto.

2. Klicken Sie auf der Anwendungsintegrationsseite für **Thirdlight** auf **Benutzer zuweisen**.
   
    ![Zuweisen von Benutzern](./media/active-directory-saas-thirdlight-tutorial/IC805846.png "Zuweisen von Benutzern")

3. Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.
   
    ![Ja](./media/active-directory-saas-thirdlight-tutorial/IC767830.png "Ja")

Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Feb17_HO1-->


