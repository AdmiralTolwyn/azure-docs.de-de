<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Box | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Box mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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
    ms.date="07/13/2016" 
    ms.author="jeedes" />




#Tutorial: Azure Active Directory-Integration mit Box


  
In diesem Tutorial wird die Integration von Azure und Box erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein Testmandant bei Box
  
Nach Abschluss dieses Tutorials können sich die Azure AD-Benutzer, die Sie Box zugewiesen haben, mithilfe der einmaligen Anmeldung auf der Box-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Sie können aber auch den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für Box
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzer- und Gruppenbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-box-tutorial/IC769537.png "Szenario")



##Aktivieren der Anwendungsintegration für Box
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für Box aktivieren.

###So aktivieren Sie die Anwendungsintegration für Box

1.  Klicken Sie im klassischen Azure-Portal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-box-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-box-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-box-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** als Suchbegriff **Box** ein.

    ![Anwendungskatalog](./media/active-directory-saas-box-tutorial/IC701023.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **Box** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![Box](./media/active-directory-saas-box-tutorial/IC701024.png "Box")



##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei Box zu authentifizieren. Im Rahmen dieses Verfahrens müssen Sie Metadaten auf Box.com hochladen.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **Box** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-box-tutorial/IC769538.png "Einmaliges Anmelden konfigurieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Box anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-box-tutorial/IC769539.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **Box-Mandanten-URL** die URL Ihres Box-Mandanten ein (z. B. https://<mydomainname>.box.com), und klicken Sie auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-box-tutorial/IC669826.png "App-URL konfigurieren")

4.  Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Box** auf **Metadaten herunterladen**, und speichern Sie die Datendatei lokal auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-box-tutorial/IC669824.png "Einmaliges Anmelden konfigurieren")

5.  Leiten Sie die Metadatendatei an das Supportteam von Box weiter. Das Supportteam muss ein einmaliges Anmelden für Sie konfigurieren.

6.  Wählen Sie die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-box-tutorial/IC769540.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Box aktivieren.

###So konfigurieren Sie einmaliges Anmelden

1. Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **Box** auf **Benutzerbereitstellung konfigurieren**, um das Dialogfeld **Benutzerbereitstellung konfigurieren** zu öffnen.

	![Automatische Benutzerbereitstellung aktivieren](./media/active-directory-saas-box-tutorial/IC769541.png "Automatische Benutzerbereitstellung aktivieren")

2. Klicken Sie auf der Dialogfeldseite **Benutzerbereitstellung aktivieren für Box** auf **Benutzerbereitstellung aktivieren**.

	![Automatische Benutzerbereitstellung aktivieren](./media/active-directory-saas-box-tutorial/IC769544.png "Automatische Benutzerbereitstellung aktivieren")

3. Stellen Sie auf der Seite **Anmelden, um Zugriff auf Box zu gewähren** die erforderlichen Anmeldeinformationen bereit, und klicken Sie dann auf **Autorisieren**.

	![Automatische Benutzerbereitstellung aktivieren](./media/active-directory-saas-box-tutorial/IC769546.png "Automatische Benutzerbereitstellung aktivieren")


4. Klicken Sie auf **Zugriff gewähren auf Box**, um diesen Vorgang zu autorisieren und zum klassischen Azure-Portal zurückzukehren.

	![Automatische Benutzerbereitstellung aktivieren](./media/active-directory-saas-box-tutorial/IC769549.png "Automatische Benutzerbereitstellung aktivieren")


5. Auf der Seite **Bereitstellungsoptionen** können Sie mit den Kontrollkästchen **Bereitzustellende Objekttypen** auswählen, ob neben Benutzerobjekten auch Gruppenobjekte in Box bereitgestellt werden. Weitere Informationen finden Sie weiter unter im Abschnitt „Zuweisen von Benutzern und Gruppen“.


6. Klicken Sie zum Abschließen der Konfiguration auf die Schaltfläche „Abschließen“.

	![Automatische Benutzerbereitstellung aktivieren](./media/active-directory-saas-box-tutorial/IC769551.png "Automatische Benutzerbereitstellung aktivieren")



##Zuweisen eines Testbenutzers
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie Box Benutzer zu

1. Erstellen Sie im klassischen Azure-Portal ein Testkonto.

2. Klicken Sie auf der Anwendungsintegrationsseite für **Box** auf **Benutzer zuweisen**.

	![Benutzer zuweisen](./media/active-directory-saas-box-tutorial/IC769552.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

	![Ja](./media/active-directory-saas-box-tutorial/IC767830.png "Ja")
  
Nach 10 Minuten können Sie überprüfen, ob das Konto mit Box synchronisiert wurde.

Als ersten Überprüfungsschritt können Sie den Bereitstellungsstatus überprüfen, indem Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für Box auf „Dashboard“ klicken.

![Dashboard](./media/active-directory-saas-box-tutorial/IC769553.png "Dashboard")

Ein erfolgreich abgeschlossener Benutzerbereitstellungszyklus wird durch einen entsprechenden Status angezeigt:

![Integrationsstatus](./media/active-directory-saas-box-tutorial/IC769555.png "Integrationsstatus")


In Ihrem Box-Mandanten werden synchronisierte Benutzer in der **Verwaltungskonsole** unter **Verwaltete Benutzer** aufgelistet.

![Integrationsstatus](./media/active-directory-saas-box-tutorial/IC769556.png "Integrationsstatus")


##Zuweisen von Benutzern und Gruppen

Auf der Registerkarte **Box > Benutzer und Gruppen** im klassischen Azure-Portal können Sie angeben, welchen Benutzern und Gruppen Zugriff auf Box gewährt werden soll. Durch die Zuweisung eines Benutzers oder einer Gruppe geschieht Folgendes:

* Azure AD ermöglicht es dem zugewiesenen Benutzer (entweder durch direkte Zuweisung oder durch eine Gruppenmitgliedschaft), sich bei Box zu authentifizieren. Wenn ein Benutzer nicht zugewiesen ist, lässt Azure AD eine Anmeldung bei Box nicht zu und gibt auf der Azure AD-Anmeldeseite einen Fehler zurück.

* Eine App-Kachel für Box wird dem [Anwendungsstartprogramm](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) des Benutzers hinzugefügt.

* Wenn die automatische Bereitstellung aktiviert ist, werden die zugewiesenen Benutzer und/oder Gruppen der Bereitstellungswarteschlange hinzugefügt, damit sie automatisch bereitgestellt werden.

    * Wenn nur Benutzerobjekte zur Bereitstellung konfiguriert wurden, werden alle direkt zugewiesenen Benutzer in der Bereitstellungswarteschlange platziert. Zudem werden alle Benutzer, die Mitglieder von zugewiesenen Gruppen sind, in die Bereitstellungswarteschlange aufgenommen.
    
    * Wenn Gruppenobjekte zur Bereitstellung konfiguriert wurden, werden alle zugewiesenen Gruppenobjekte in Box bereitgestellt. Darüber hinaus werden auch alle Benutzer bereitgestellt, die Mitglieder dieser Gruppen sind. Die Gruppen- und Benutzermitgliedschaften bleiben erhalten, nachdem sie an Box übertragen wurden.
    
Auf der Registerkarte **Attribute > Einmaliges Anmelden** können Sie konfigurieren, welche Benutzerattribute (oder Ansprüche) in Box während der SAML-basierten Authentifizierung angezeigt werden. Auf der Registerkarte **Attribute > Bereitstellung** können Sie konfigurieren, wie Benutzer- und Gruppenattribute während der Bereitstellung von Azure AD an Box übertragen werden. Weitere Informationen finden Sie in den unten aufgeführten Ressourcen.


## Zusätzliche Ressourcen

* [Anpassen ausgestellter Ansprüche im SAML-Token](active-directory-saml-claims-customization.md)
* [Bereitstellung: Anpassen von Attributzuordnungen](active-directory-saas-customizing-attribute-mappings.md)
* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0720_2016-->