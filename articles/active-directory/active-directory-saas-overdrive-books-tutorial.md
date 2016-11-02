<properties 
    pageTitle="Tutorial: Azure Active Directory-Integration mit Overdrive Books | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie Overdrive Books mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen." 
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
    ms.date="07/08/2016" 
    ms.author="jeedes" />

#Tutorial: Azure Active Directory-Integration mit Overdrive Books
  
In diesem Tutorial wird die Integration von Azure und OverDrive erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

-   Ein gültiges Azure-Abonnement
-   Ein OverDrive-Abonnement, für das einmaliges Anmelden aktiviert ist
  
Nach Abschluss dieses Lernprogramms können sich die Azure AD-Benutzer, die Sie OverDrive zugewiesen haben, mittels einmaliger Anmeldung auf Ihrer OverDrive-Unternehmenswebsite bei der Anwendung anmelden (durch den Dienstanbieter initiierte Anmeldung). Alternativ können Sie den Zugriffsbereich nutzen (siehe [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md)).
  
Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1.  Aktivieren der Anwendungsintegration für OverDrive
2.  Konfigurieren der einmaligen Anmeldung
3.  Konfigurieren der Benutzerbereitstellung
4.  Zuweisen von Benutzern

![Szenario](./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "Szenario")
##Aktivieren der Anwendungsintegration für OverDrive
  
In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für OverDrive aktivieren.

###So aktivieren Sie die Anwendungsintegration für OverDrive

1.  Klicken Sie im klassischen Azure-Portal im linken Navigationsbereich auf **Active Directory**.

    ![Active Directory](./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "Active Directory")

2.  Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.  Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

    ![Anwendungen](./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "Anwendungen")

4.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

    ![Anwendung hinzufügen](./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Anwendung hinzufügen")

5.  Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

    ![Anwendung aus dem Katalog hinzufügen](./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "Anwendung aus dem Katalog hinzufügen")

6.  Geben Sie im **Suchfeld** das Wort **OverDrive** ein.

    ![Anwendungskatalog](./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "Anwendungskatalog")

7.  Wählen Sie im Ergebnisbereich **OverDrive** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

    ![OverDrive](./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "OverDrive")
##Konfigurieren der einmaligen Anmeldung
  
In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei OverDrive zu authentifizieren.

###So konfigurieren Sie einmaliges Anmelden

1.  Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **OverDrive** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

    ![Einmaliges Anmelden aktivieren](./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "Einmaliges Anmelden aktivieren")

2.  Wählen Sie auf der Seite **Wie sollen sich Benutzer bei OverDrive anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "Einmaliges Anmelden konfigurieren")

3.  Geben Sie auf der Seite **App-URL konfigurieren** im Textfeld für die **OverDrive-Anmelde-URL** Ihre URL im Format „*http://mslibrarytest.libraryreserve.com*" ein, und klicken Sie dann auf **Weiter**.

    ![App-URL konfigurieren](./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "App-URL konfigurieren")

4.  Laden Sie auf der Seite **Einmaliges Anmelden konfigurieren für OverDrive** die Metadatendatei herunter, und senden Sie sie an das Supportteam von OverDrive.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "Einmaliges Anmelden konfigurieren")

    >[AZURE.NOTE]Das Supportteam von OverDrive konfiguriert einmaliges Anmelden für Sie. Sie erhalten eine Benachrichtigung, wenn die Konfiguration abgeschlossen ist.

5.  Wählen Sie im klassischen Azure-Portal die Bestätigung zur Konfiguration des einmaligen Anmeldens aus, und klicken Sie dann auf **Abschließen**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu schließen.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "Einmaliges Anmelden konfigurieren")
##Konfigurieren der Benutzerbereitstellung
  
Für das Konfigurieren der Benutzerbereitstellung in OverDrive steht kein Aktionselement zur Verfügung. Wenn ein zugewiesener Benutzer versucht, sich bei OverDrive anzumelden, wird ggf. automatisch ein OverDrive-Konto erstellt.

>[AZURE.NOTE]Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von OverDrive-Benutzerkonten oder mithilfe der von OverDrive bereitgestellten APIs erstellen.

##Zuweisen von Benutzern
  
Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

###So weisen Sie OverDrive Benutzer zu

1.  Erstellen Sie im klassischen Azure-Portal ein Testkonto.

2.  Klicken Sie auf der Anwendungsintegrationsseite für **OverDrive** auf **Benutzer zuweisen**.

    ![Benutzer zuweisen](./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "Benutzer zuweisen")

3.  Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

    ![Ja](./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "Ja")
  
Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->