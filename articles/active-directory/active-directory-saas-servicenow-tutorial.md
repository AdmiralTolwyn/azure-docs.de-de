<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit ServiceNow | Microsoft Azure" 
    description="Erfahren Sie, wie Sie ServiceNow mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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

#Tutorial: Azure Active Directory-Integration mit ServiceNow
  
In diesem Tutorial wird die Integration von Azure und ServiceNow erläutert. Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Mandanten in ServiceNow, mindestens Version Calgary
-   Für den ServiceNow-Mandanten muss das [SSO-Plug-In für mehrere Anbieter](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) aktiviert sein. Dazu kann eine Serviceanfrage unter https://hi.service-now.com/ übermittelt werden.
  
Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie ServiceNow zugewiesen haben, mittels einmaliger Anmeldung auf der ServiceNow-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Sie können aber auch den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Tutorial beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für ServiceNow
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-servicenow-tutorial/IC769496.png "Szenario")
##Aktivieren der Anwendungsintegration für ServiceNow
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für ServiceNow aktivieren.

###So aktivieren Sie die Anwendungsintegration für ServiceNow

1.  Klicken Sie im klassischen Azure-Portal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-servicenow-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-servicenow-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-servicenow-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** den Text **ServiceNow** ein.

    ![Anwendungskatalog](./media/active-directory-saas-servicenow-tutorial/IC701016.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **ServiceNow** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei ServiceNow zu authentifizieren.

Im Rahmen dieses Verfahrens müssen Sie ein Base-64-codiertes Zertifikat in Ihren Dropbox für Unternehmen-Mandanten hochladen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (in englischer Sprache) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im klassischen Azure AD-Portal auf der Anwendungsintegrationsseite für **ServiceNow** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configure single sign-on")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei ServiceNow anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configure single sign-on")

3.  Führen Sie auf der Seite **App-Einstellungen konfigurieren** die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC769497.png "App-URL konfigurieren")

    a. Geben Sie im Textfeld **ServiceNow-Anmelde-URL** die URL ein, die von Ihren Benutzern zum Anmelden bei Ihrer ServiceNow-Anwendung verwendet wird (z. B.: *https://\<Instanzname>.service-now.com*).

    b. Geben Sie im Textfeld **Aussteller-URL** die von Ihren Benutzern für die Anmeldung bei Ihrer ServiceNow-Anwendung verwendete URL ein (z.B. *https://\<Instanzname>.service-now.com*).

    c. Klicken Sie auf **Weiter**.

4.  Damit Azure AD ServiceNow automatisch für die SAML-basierte Authentifizierung konfiguriert, geben Sie den Instanznamen, den Administratorbenutzernamen und das Administratorkennwort für ServiceNow in das Formular **Einmaliges Anmelden automatisch konfigurieren** ein, und klicken Sie auf *Konfigurieren*. Beachten Sie, dass dem angegebenen Administratorbenutzernamen in ServiceNow die Rolle **security\_admin** zugewiesen sein muss, damit dies funktioniert. Um ServiceNow manuell für die Verwendung von Azure AD als SAML-Identitätsanbieter zu konfigurieren, klicken Sie auf **Diese Anwendung manuell für das einmalige Anmelden konfigurieren** und anschließend auf **Weiter**, und führen Sie die folgenden Schritte aus.

    ![App-URL konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "App-URL konfigurieren")



5.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für ServiceNow** auf **Zertifikat herunterladen**, speichern Sie das Zertifikat lokal auf Ihrem Computer, und klicken Sie dann auf **Weiter**.

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configure single sign-on")

1. Melden Sie sich bei Ihrer ServiceNow-Anwendung als Administrator an.

2. Klicken Sie im Navigationsbereich auf der linken Seite auf **Properties**.

    ![App-URL konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC7694980.png "App-URL konfigurieren")


3. Führen Sie im Dialogfeld **Multiple Provider SSO Properties** die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "App-URL konfigurieren")

    a. Bei **Enable multiple provider SSO** wählen Sie **Yes** aus.

    b. Bei **Enable debug logging got the multiple provider SSO integration** wählen Sie **Yes** aus.

    c. Im Textfeld **The field on the user table that...** geben Sie **user\_name** ein.

    d. Klicken Sie auf **Speichern**.



1. Klicken Sie im Navigationsbereich auf der linken Seite auf **x509 Certificates**.

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694973.png "Configure single sign-on")


1. Klicken Sie im Dialogfeld **X.509 Certificates** auf **New**.

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configure single sign-on")


1. Führen Sie im Dialogfeld **X.509 Certificates** die folgenden Schritte aus:

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configure single sign-on")

    a. Klicken Sie auf **Neu**.

    b. Geben Sie im Textfeld **Name** einen Namen für Ihre Konfiguration ein (z.B. **TestSAML2.0**).

    c. Wählen Sie **Aktiv**.

    d. Wählen Sie unter **Format** das Format **PEM** aus.

    e. Wählen Sie unter **Type** den Typ **Trust Store Cert** aus.

    f. Erstellen Sie eine Base64-codierte Datei aus Ihrem heruntergeladene Zertifikat.
   
	> [AZURE.NOTE] Weitere Informationen finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (in englischer Sprache).
    
    g. Öffnen Sie das Base64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **PEM Certificate** ein.

    h. Klicken Sie auf **Aktualisieren**.


1. Klicken Sie im Navigationsbereich auf der linken Seite auf **Identity Providers**.

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694976.png "Configure single sign-on")

1. Klicken Sie im Dialogfeld **Identity Providers** auf **New**:

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configure single sign-on")


1. Klicken Sie im Dialogfeld **Identity Providers** auf **SAML2 Update1?**:

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configure single sign-on")


1. Führen Sie im Dialogfeld „SAML2 Update1 Properties“ folgende Schritte aus:

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configure single sign-on")


    a. Geben Sie im Textfeld **Name** einen Namen für Ihre Konfiguration ein (z.B. **SAML 2.0**).

    b. Geben Sie im Textfeld **User Field** den Wert **email** oder **user\_id** ein, abhängig davon, welches Feld für die eindeutige Identifizierung von Benutzern in Ihrer ServiceNow-Bereitstellung verwendet wird.
    
    **Hinweis:** Sie können Azure AD so konfigurieren, dass entweder die Azure AD-Benutzer-ID (Benutzerprinzipalname) oder die E-Mail-Adresse als eindeutiger Bezeichner im SAML-Token ausgegeben wird. Wechseln Sie dazu im klassischen Azure-Portal zum Abschnitt **ServiceNow > Attribute > Einmaliges Anmelden**, und weisen Sie dem **nameidentifier**-Attribut das gewünschte Feld zu. Der gespeicherte Wert für das ausgewählte Attribut in Azure AD (z.B. Benutzerprinzipalname) muss dem in ServiceNow gespeicherten Wert für das eingegebene Feld (z.B. user\_id) entsprechen.

    c. Kopieren Sie im klassischen Azure AD-Portal den Wert für **Identitätsanbieter-ID**, und fügen Sie ihn in das Textfeld **Identity Provider URL** ein.

    d. Kopieren Sie im klassischen Azure AD-Portal den Wert für **Authentifizierungsanforderungs-URL**, und fügen Sie ihn in das Textfeld **Identity Provider's AuthnRequest** ein.

    e. Kopieren Sie im klassischen Azure AD-Portal den Wert für **Dienst-URL für einmaliges Abmelden**, und fügen Sie ihn in das Textfeld **Identity Provider‘s SingleLogoutRequest** ein.

    f. Geben Sie im Textfeld **ServiceNow Homepage** die URL zur Homepage Ihrer ServiceNow-Instanz ein.

    > [AZURE.NOTE] Die URL zur Homepage der ServiceNow-Instanz ist eine Verkettung Ihrer **ServiceNow-Mandanten-URL** und **/navpage.do** (z.B. *https://fabrikam.service-now.com/navpage.do*).
 

    g. Geben Sie im Textfeld **Entity ID / Issuer** die URL Ihres ServiceNow-Mandaten ein.

    h. Geben Sie im Textfeld **Audience URL** die URL Ihres ServiceNow-Mandanten ein.

    i. Geben Sie im Textfeld **Protocol Binding for the IDP's SingleLogoutRequest** die Zeichenfolge **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect** ein.

    j. Geben Sie im Textfeld „NameID Policy“ die Zeichenfolge **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified** ein.

    k. Deaktivieren Sie **Create an AuthnContextClass**.

    l. In **AuthnContextClassRef Method** geben Sie **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password** ein.

    m. Im Textfeld **Clock Skew** geben Sie **60** ein.

    n. Als **Single Sign On Script** wählen Sie **MultiSSO\_SAML2\_Update1** aus.

    o. Als **x509 Certificate** wählen Sie das im vorherigen Schritt erstellte Zertifikat aus.

    p. Klicken Sie auf **Submit**.



6. Wählen Sie im klassischen Azure AD-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Weiter**.

    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configure single sign-on")

7. Klicken Sie auf der Seite **Bestätigung zur einmaligen Anmeldung** auf **Fertig stellen**.
 
    ![Configure single sign-on](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configure single sign-on")



##Konfigurieren der Benutzerbereitstellung
  
In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für ServiceNow aktivieren.


### So konfigurieren Sie die Benutzerbereitstellung

1. Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **ServiceNow** auf **Benutzerbereitstellung konfigurieren**.

	![Benutzerbereitstellung](./media/active-directory-saas-servicenow-tutorial/IC769498.png "Benutzerbereitstellung")


2. Stellen Sie auf der Seite **Geben Sie Ihre ServiceNow-Anmeldeinformationen ein, um die automatische Benutzerbereitstellung zu aktivieren** die folgenden Konfigurationseinstellungen bereit: Benutzerbereitstellung konfigurieren

     a. Geben Sie im Textfeld **ServiceNow-Instanzname** den Namen des ServiceNow-Instanz ein.

     b. Geben Sie im Textfeld **Administratorbenutzername für ServiceNow** den Namen des ServiceNow-Administratorkontos ein.

     c. Geben Sie im Textfeld **ServiceNow-Administratorkennwort** das Kennwort für dieses Konto ein.

     d. Klicken Sie auf **Überprüfen**, um die Konfiguration zu überprüfen.

     e. Klicken Sie auf **Weiter**, um die Seite **Nächste Schritte** zu öffnen.

     f. Wenn Sie alle Benutzer für diese Anwendung bereitstellen möchten, wählen Sie **Dieser Anwendung automatisch alle Benutzerkonten im Verzeichnis bereitstellen**.

	![Nächste Schritte](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Nächste Schritte")

     g. Klicken Sie auf der Seite **Nächste Schritte** auf **Abgeschlossen**, um die Konfiguration zu speichern.











##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie ServiceNow Benutzer zu

1.  Erstellen Sie im klassischen Azure AD-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **ServiceNow** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-servicenow-tutorial/IC769499.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-servicenow-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).


## Weitere Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0928_2016-->