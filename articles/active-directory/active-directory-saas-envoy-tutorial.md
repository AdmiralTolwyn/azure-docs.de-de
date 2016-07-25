<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Envoy | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Envoy mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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
    ms.date="07/09/2016" 
    ms.author="jeedes" />

#Tutorial: Azure Active Directory-Integration mit Envoy
  
In diesem Tutorial wird die Integration von Azure und Envoy erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Einen Envoy-Mandanten
  
Nach Abschluss dieses Lernprogramms können sich die Envoy zugewiesenen Azure AD-Benutzer mittels einmaligen Anmeldens auf Ihrer Envoy-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können sie auch die [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md) nutzen.
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Envoy
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-envoy-tutorial/IC776759.png "Szenario")
##Aktivieren der Anwendungsintegration für Envoy
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Envoy aktivieren.

###So aktivieren Sie die Anwendungsintegration für Envoy

1.  Klicken Sie im klassischen Azure-Portal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-envoy-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-envoy-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-envoy-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **Envoy** ein.

    ![Anwendungskatalog](./media/active-directory-saas-envoy-tutorial/IC776760.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Envoy** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoy")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Envoy zu authentifizieren. Zum Konfigurieren des einmaligen Anmeldens für Envoy müssen Sie einen Fingerabdruckwert aus einem Zertifikat abrufen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI) weitere Informationen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **Envoy** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden aktivieren](./media/active-directory-saas-envoy-tutorial/IC776778.png "Einmaliges Anmelden aktivieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Envoy anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-envoy-tutorial/IC776779.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Envoy-Anmelde-URL** die URL im Format „*https://\<Mandantenname>.Envoy.com*“ ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-envoy-tutorial/IC776780.png "App-URL konfigurieren")

4.  Klicken Sie zum Herunterladen des Zertifikats auf der Seite **Einmaliges Anmelden konfigurieren für Envoy** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal unter **c:\\Envoy.cer**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-envoy-tutorial/IC776781.png "Einmaliges Anmelden konfigurieren")

5.  Melden Sie sich in einem anderen Webbrowserfenster bei der Envoy-Unternehmenswebsite als Administrator an.

6.  Klicken Sie oben auf der Symbolleiste auf **Einstellungen**.

    ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoy")

7.  Klicken Sie auf **Unternehmen**.

    ![Unternehmen](./media/active-directory-saas-envoy-tutorial/IC776783.png "Unternehmen")

8.  Klicken Sie auf **SAML**.

    ![SAML](./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")

9.  Führen Sie im Konfigurationsabschnitt für die **SAML-Authentifizierung** die folgenden Schritte aus:

    ![SAML-Authentifizierung](./media/active-directory-saas-envoy-tutorial/IC776785.png "SAML-Authentifizierung")

    >[AZURE.NOTE] Der Wert für die HQ-Speicherort-ID wird automatisch von der Anwendung generiert.

    1.  Kopieren Sie den **Fingerabdruckwert** aus dem exportierten Zertifikat, und fügen Sie ihn in das Textfeld **Fingerabdruck** ein.

        >[AZURE.TIP] Weitere Informationen finden Sie unter [Abrufen des Fingerabdruckwerts eines Zertifikats](http://youtu.be/YKQF266SAxI).

    2.  Kopieren Sie im klassischen Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Envoy** den Wert der **SAML-SSO-URL**, und fügen Sie ihn in das Textfeld **Identity Provider HTTP SAML URL** ein.
    3.  Klicken Sie auf **Änderungen speichern**.

10. Wählen Sie im klassischen Azure-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-envoy-tutorial/IC776786.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Für das Konfigurieren der Benutzerbereitstellung in Envoy steht kein Aktionselement zur Verfügung. Wenn ein zugewiesener Benutzer versucht, sich über den Zugriffsbereich bei Envoy anzumelden, überprüft Envoy, ob der Benutzer vorhanden ist. Ist noch kein Benutzerkonto verfügbar, wird es von Envoy automatisch erstellt.
##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Envoy Benutzer zu

1.  Erstellen Sie im klassischen Azure-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **Envoy** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-envoy-tutorial/IC776787.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-envoy-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->