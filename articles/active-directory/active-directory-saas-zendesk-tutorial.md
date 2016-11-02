<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Zendesk | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Zendesk mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/09/2016" 
    ms.author="jeedes" />


#<a name="tutorial:-azure-active-directory-integration-with-zendesk"></a>Tutorial: Azure Active Directory-Integration mit Zendesk
  
In diesem Tutorial wird die Integration von Azure und Zendesk erläutert.  
Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Zendesk-Mandanten
  
Nach Abschluss dieses Tutorials können sich die Zendesk zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf Ihrer Zendesk-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Zendesk
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-zendesk-tutorial/IC773083.png "Scenario")

##<a name="enabling-the-application-integration-for-zendesk"></a>Aktivieren der Anwendungsintegration für Zendesk
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Zendesk aktivieren.

###<a name="to-enable-the-application-integration-for-zendesk,-perform-the-following-steps:"></a>So aktivieren Sie die Anwendungsintegration für Zendesk

1.  Klicken Sie im linken Navigationsbereich des Azure-Verwaltungsportals auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen** .

    ![Anwendungen](./media/active-directory-saas-zendesk-tutorial/IC700994.png "Applications")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen** .

    ![Anwendung hinzufügen](./media/active-directory-saas-zendesk-tutorial/IC749321.png "Add application")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-zendesk-tutorial/IC749322.png "Add an application from gallerry")

6.  Geben Sie im **Suchfeld** den Suchbegriff **Zendesk** ein.

    ![Anwendungskatalog](./media/active-directory-saas-zendesk-tutorial/IC773084.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich die Option **Zendesk** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Zendesk](./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

##<a name="configuring-single-sign-on"></a>Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Zendesk zu authentifizieren.  
Zum Konfigurieren des einmaligen Anmeldens für Zendesk müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen.  
Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI)weitere Informationen.

###<a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite für **Zendesk** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden](./media/active-directory-saas-zendesk-tutorial/IC773086.png "Single sign-on")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Zendesk anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Configure single sign-on](./media/active-directory-saas-zendesk-tutorial/IC773087.png "Configure single sign-on")

3.  Führen Sie auf der Seite **App-URL konfigurieren** die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configure app URL")
  
    a. Geben Sie im Textfeld **Zendesk-Anmelde-URL** die URL in folgendem Format ein: `https://<tenant-name>.zendesk.com`.

    b. Klicken Sie auf **Weiter**.



4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Zendesk** auf **Zertifikat herunterladen**, und speichern Sie das Zertifikat lokal auf Ihrem Computer.

    ![Configure single sign-on](./media/active-directory-saas-zendesk-tutorial/IC777534.png "Configure single sign-on")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Zendesk-Unternehmenswebsite als Administrator an.

6.  Klicken Sie auf **Administrator**.

7.  Klicken Sie im linken Navigationsbereich auf **Einstellungen** und anschließend auf **Sicherheit**.

    ![Sicherheit](./media/active-directory-saas-zendesk-tutorial/IC773089.png "Security")

8.  Klicken Sie auf der Seite **Sicherheit** auf die Registerkarte **Administratoren und Agents**.

9.  Wählen Sie **Einmaliges Anmelden (SSO) und SAML** und anschließend **SAML** aus.

10. Kopieren Sie im Azure AD-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Zendesk** den Wert der **SAML-SSO-URL** aus, und fügen Sie ihn in das Textfeld **SAML-SSO-URL** ein.

11. Kopieren Sie im Azure AD-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Zendesk** den Wert der **Remoteabmelde-URL**, und fügen Sie ihn in das Textfeld **Remoteabmelde-URL** ein.

    ![Einmaliges Anmelden](./media/active-directory-saas-zendesk-tutorial/IC773090.png "Single sign-on")

12. Kopieren Sie den Wert für **Fingerabdruck** aus dem exportierten Zertifikat, und fügen Sie ihn in das Textfeld **Fingerabdruck des Zertifikats** ein.

    >[AZURE.TIP] Weitere Informationen finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI)

13. Klicken Sie auf **Speichern**.

14. Bestätigen Sie im Azure AD-Portal die Konfiguration der einmaligen Anmeldung, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Configure single sign-on](./media/active-directory-saas-zendesk-tutorial/IC773093.png "Configure single sign-on")

##<a name="configuring-user-provisioning"></a>Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei **Zendesk** anmelden können, müssen sie in **Zendesk** bereitgestellt werden.  
Im Fall von **Zendesk**ist die Bereitstellung eine manuelle Aufgabe.

###<a name="to-provision-a-user-account-to-zendesk,-perform-the-following-steps:"></a>Führen Sie zum Bereitstellen von Benutzerkonten in Zendesk die folgenden Schritte aus:

1.  Melden Sie sich bei Ihrem **Zendesk** -Mandanten an.

2.  Wählen Sie die Registerkarte **Kundenliste** .

3.  Wählen Sie die Registerkarte **Benutzer** aus, und klicken Sie auf **Hinzufügen**.

    ![Benutzer hinzufügen](./media/active-directory-saas-zendesk-tutorial/IC773632.png "Add user")

4.  Geben Sie die E-Mail-Adresse eines vorhandenen Azure AD-Kontos ein, das Sie bereitstellen möchten, und klicken Sie auf **Speichern**.

    ![Neuer Benutzer](./media/active-directory-saas-zendesk-tutorial/IC773633.png "New user")

>[AZURE.NOTE] Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Zendesk-Benutzerkonten oder mithilfe der von Zendesk bereitgestellten APIs erstellen.

##<a name="assigning-users"></a>Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###<a name="to-assign-users-to-zendesk,-perform-the-following-steps:"></a>So weisen Sie Zendesk Benutzer zu

1.  Erstellen Sie im Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Zendesk** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-zendesk-tutorial/IC773094.png "Assign users")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-zendesk-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).



<!--HONumber=Oct16_HO2-->


