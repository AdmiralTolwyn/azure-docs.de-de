<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Projectplace | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Projectplace mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />


#<a name="tutorial:-azure-active-directory-integration-with-projectplace"></a>Tutorial: Azure Active Directory-Integration mit Projectplace
  
In diesem Tutorial wird die Integration von Azure und Projectplace erläutert.  
Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Projectplace-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Tutorials können sich die Projectplace zugewiesenen Azure AD-Benutzer mittels einmaliger Anmeldung auf Ihrer Projectplace-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Projectplace
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-projectplace-tutorial/IC790217.png "Scenario")
##<a name="enabling-the-application-integration-for-projectplace"></a>Aktivieren der Anwendungsintegration für Projectplace
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Projectplace aktivieren.

###<a name="to-enable-the-application-integration-for-projectplace,-perform-the-following-steps:"></a>So aktivieren Sie die Anwendungsintegration für Projectplace

1.  Klicken Sie im klassischen Azure-Portal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-projectplace-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen** .

    ![Anwendungen](./media/active-directory-saas-projectplace-tutorial/IC700994.png "Applications")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen** .

    ![Anwendung hinzufügen](./media/active-directory-saas-projectplace-tutorial/IC749321.png "Add application")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-projectplace-tutorial/IC749322.png "Add an application from gallerry")

6.  Geben Sie im **Suchfeld** das Wort **Projectplace** ein.

    ![Anwendungskatalog](./media/active-directory-saas-projectplace-tutorial/IC790218.png "Application Gallery")

7.  Wählen Sie im Ergebnisbereich **Projectplace** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Projectplace](./media/active-directory-saas-projectplace-tutorial/IC790219.png "ProjectPlace")
##<a name="configuring-single-sign-on"></a>Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Projectplace zu authentifizieren.

###<a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **Projectplace** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-projectplace-tutorial/IC790220.png "Configure Single SignOn")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Projectplace anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-projectplace-tutorial/IC790221.png "Configure Single SignOn")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Projectplace-Anmelde-URL** die URL Ihres Projectplace-Mandanten ein (z.B. *http://company.projectplace.com*), und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-projectplace-tutorial/IC790222.png "Configure App URL")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Projectplace** auf **Metadaten herunterladen**, und speichern Sie die Metadatendatei auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-projectplace-tutorial/IC790223.png "Configure Single SignOn")

5.  Senden Sie die Metadatendatei an das Supportteam von Projectplace.

    >[AZURE.NOTE] Einmaliges Anmelden muss vom Supportteam von Projectplace konfiguriert werden. Sie erhalten eine Benachrichtigung, sobald die Konfiguration abgeschlossen ist.

6.  Bestätigen Sie im klassischen Azure-Portal die Konfiguration der einmaligen Anmeldung, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-projectplace-tutorial/IC790227.png "Configure Single SignOn")
##<a name="configuring-user-provisioning"></a>Konfigurieren der Benutzerbereitstellung
  
Damit sich Azure AD-Benutzer bei Projectplace anmelden können, müssen sie in Projectplace bereitgestellt werden.  
Im Fall von Projectplace ist die Bereitstellung eine manuelle Aufgabe.

###<a name="to-provision-a-user-accounts,-perform-the-following-steps:"></a>Führen Sie zum Bereitstellen von Benutzerkonten die folgenden Schritte aus:

1.  Melden Sie sich bei der **Projectplace** -Unternehmenswebsite als Administrator an.

2.  Wechseln Sie zu **Personen**, und klicken Sie auf **Mitglieder**.

    ![Personen](./media/active-directory-saas-projectplace-tutorial/IC790228.png "People")

3.  Klicken Sie auf **Mitglied hinzufügen**.

    ![Mitglieder hinzufügen](./media/active-directory-saas-projectplace-tutorial/IC790232.png "Add Members")

4.  Führen Sie im Abschnitt **Mitglied hinzufügen** die folgenden Schritte aus:

    ![Neue Mitglieder](./media/active-directory-saas-projectplace-tutorial/IC790233.png "New Members")

    1.  Geben Sie im Textfeld **Neue Mitglieder** die E-Mail-Adresse eines gültigen AAD-Benutzerkontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.
    2.  Klicken Sie unten auf der Seite auf **Senden**

        >[AZURE.NOTE] An den Besitzer des Azure Active Directory-Kontos wird eine E-Mail mit einem Link zur Bestätigung des Kontos gesendet, bevor es aktiv wird.
    
>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Projectplace-Benutzerkonten oder mithilfe der von Projectplace bereitgestellten APIs erstellen.

##<a name="assigning-users"></a>Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###<a name="to-assign-users-to-projectplace,-perform-the-following-steps:"></a>So weisen Sie Projectplace Benutzer zu

1.  Erstellen Sie im klassischen Azure-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Projectplace** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-projectplace-tutorial/IC790234.png "Assign Users")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-projectplace-tutorial/IC767830.png "Yes")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


<!--HONumber=Oct16_HO2-->


