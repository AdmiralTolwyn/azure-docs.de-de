<properties
	pageTitle="Tutorial: Azure Active Directory-Integration mit Kindling | Microsoft Azure"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Kindling konfigurieren."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/05/2016"
	ms.author="jeedes"/>


# Tutorial: Azure Active Directory-Integration mit Kindling

In diesem Tutorial erfahren Sie, wie Sie Kindling in Azure Active Directory (Azure AD) integrieren können.<br>Die Integration von Kindling in Azure AD bietet Ihnen folgende Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf Kindling hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Kindling anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im klassischen Azure-Portal. 


Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit Kindling konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Kindling-Abonnement


> [AZURE.NOTE] Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern. 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br> Das in diesem Tutorial beschriebene Szenario besteht aus zwei großen Bausteinen:

1. Hinzufügen von Kindling aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von Kindling aus dem Katalog
Zum Konfigurieren der Integration von Kindling in Azure AD müssen Sie Kindling aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Kindling aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im **klassischen Azure-Portal** im linken Navigationsbereich auf **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.<br><br> ![Anwendungen][2]<br>
4. Klicken Sie unten auf der Seite auf **Hinzufügen**.<br><br> ![Anwendungen][3]<br>
5. Klicken Sie im Dialogfeld **What do you want to do** auf **Anwendung aus dem Katalog hinzufügen**.<br><br> ![Anwendungen][4]<br>
6. Geben Sie im Suchfeld das Wort **Kindling** ein.<br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_01.png)<br>
7. Wählen Sie im Ergebnisbereich **Kindling** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_02.png)<br>

##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll anhand eines Testbenutzers namens Britta Simon veranschaulicht werden, wie das einmalige Anmelden von Azure AD in Kindling konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Kindling als Gegenbenutzer zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Kindling muss eine Linkbeziehung eingerichtet werden.<br> Diese Linkbeziehung wird hergestellt, indem Sie den **Benutzernamen** in Azure AD als Wert für den **Benutzernamen** in Kindling zuweisen.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Kindling müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD – einmaliges Anmelden](#configuring-azure-ad-single-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen eines Kindling-Testbenutzers](#creating-a-kindling-test-user)**, um eine Entsprechung von Britta Simon in Kindling zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
6. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Das Ziel dieses Abschnitts besteht darin, das einmalige Anmelden von Azure AD im klassischen Azure-Portal zu aktivieren und das einmalige Anmelden in Ihrer Kindling-Anwendung zu konfigurieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen. Falls Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o) (in englischer Sprache) weitere Informationen.

Zum Konfigurieren des einmaligen Anmeldens für Kindling benötigen Sie eine registrierte Domäne. Wenn Sie noch keine registrierte Domäne besitzen, wenden Sie sich unter [support@kindlingapp.com](mailto:support@kindlingapp.com) an das Supportteam von Kindling.



**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Kindling die folgenden Schritte aus:**

1. Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **Kindling** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen. <br><br> ![Einmaliges Anmelden konfigurieren][6] <br>

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Kindling anmelden?** die Option **Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**. <br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_03.png) <br>

3. Führen Sie auf der Dialogseite **App-Einstellungen konfigurieren** die folgenden Schritte aus: <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_04.png) <br>


    a. Geben Sie im Textfeld **Anmelde-URL** die URL ein, die die Benutzer zur Anmeldung bei der Kindling-Anwendung verwenden (z.B. *https://\<Firmenname>.kindlingapp.com/*).

    b. Wenden Sie sich unter **support@kindlingapp.com** an Ihr Kindling-Supportteam, um die Werte für [Aussteller](mailto:support@kindlingapp.com) und **Antwort-URL** zu erhalten.

    c. Geben Sie im Textfeld **Aussteller** Ihre Aussteller-URL ein.

    d. Geben Sie im Textfeld **Antwort-URL** Ihre Antwort-URL ein.
 
    e. Klicken Sie auf **Weiter**.
 
 
4. Führen Sie auf der Seite **Einmaliges Anmelden konfigurieren für Kindling** die folgenden Schritte aus: <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_05.png) <br>

    a. Klicken Sie auf **Zertifikat herunterladen** und speichern Sie die Datei auf Ihrem Computer.

    b. Wenden Sie sich an Ihr Kindling-Supportteam, und bitten Sie um die Einrichtung des einmaligen Anmeldens (SSO) für Ihre Anwendung. Sie müssen dem Supportteam Folgendes bereitstellen: das heruntergeladene Zertifikat, die **Aussteller-URL**, die der **Entitäts-ID** von Kindling zugeordnet ist, die **Dienst-URL für einmalige Anmeldung**, die der **SSO-Anmelde-URL** von Kindling zugeordnet ist, und die **Dienst-URL für einmalige Abmeldung**, die der **SSO-Abmelde-URL** von Kindling zugeordnet ist.

    c. Klicken Sie auf **Weiter**.

6. Wählen Sie im klassischen Azure-Portal die Bestätigung zur Konfiguration der einmaligen Anmeldung aus und klicken Sie dann auf **Weiter**. <br><br>![Azure AD – einmaliges Anmelden][10]<br>

7. Klicken Sie auf der Seite **Bestätigung zur einmaligen Anmeldung** auf **Fertig stellen**. <br><br>![Azure AD – einmaliges Anmelden][11]




### Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im klassischen Azure-Portal.<br> Wählen Sie in der Benutzerliste **Britta Simon** aus.<br><br>![Azure AD-Benutzer erstellen][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im **klassischen Azure-Portal** im linken Navigationsbereich auf **Active Directory**. <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-kindling-tutorial/create_aaduser_09.png)<br> 

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü oben auf **Benutzer**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-kindling-tutorial/create_aaduser_03.png) <br>
 
4. Um das Dialogfeld **Benutzer hinzufügen** zu öffnen, klicken Sie auf der Symbolleiste unten auf **Benutzer hinzufügen**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-kindling-tutorial/create_aaduser_04.png) <br>

5. Führen Sie auf der Dialogfeldseite **Informationen über diesen Benutzer** die folgenden Schritte aus: <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-kindling-tutorial/create_aaduser_05.png) <br>

    a. Wählen Sie als „Benutzertyp“ die Option „Neuer Benutzer in Ihrer Organisation“ aus.

    b. Geben Sie in das Textfeld **Benutzername** den Text **BrittaSimon** ein.

    c. Klicken Sie auf **Weiter**.

6.  Führen Sie auf der Dialogfeldseite **Benutzerprofil** die folgenden Schritte aus: <br><br>![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-kindling-tutorial/create_aaduser_06.png) <br>
 
    a. Geben Sie in das Textfeld **Vorname** den Namen **Britta** ein.

    b. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein.

    c. Geben Sie in das Textfeld **Anzeigename** den Namen **Britta Simon** ein.

    d. Wählen Sie in der Liste **Rolle** die Rolle **Benutzer** aus. e. Klicken Sie auf **Weiter**.

7. Klicken Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** auf **Erstellen**. <br><br> ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-kindling-tutorial/create_aaduser_07.png) <br>
 
8. Führen Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** die folgenden Schritte aus: <br><br>![Erstellen einen Azure AD-Testbenutzers](./media/active-directory-saas-kindling-tutorial/create_aaduser_08.png) <br>
  
    a. Notieren Sie den Wert von **Neues Kennwort**.

    b. Klicken Sie auf **Fertig stellen**.

  
 
### Erstellen eines Kindling-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in Kindling. Kindling unterstützt die Just-in-Time-Bereitstellung. Sie haben sie bereits unter [Konfigurieren der einmaligen Anmeldung in Azure AD](#configuring-azure-ad-single-single-sign-on) aktiviert.

Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung.




### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts besteht darin, Britta Simon die Verwendung des einmaligen Anmeldens bei Azure zu ermöglichen, indem sie Zugriff auf Kindling erhält. <br><br>![Benutzer zuweisen][200] <br>

**Um Britta Simon Kindling zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie zum Öffnen der Anwendungsansicht im klassischen Azure-Portal in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**. <br><br>![Benutzer zuweisen][201] <br>

2. Wählen Sie in der Anwendungsliste **Kindling** aus. <br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_50.png) <br>

1. Klicken Sie im oberen Menü auf **Benutzer**. <br><br>![Benutzer zuweisen][203] <br>

1. Wählen Sie in der Benutzerliste **Britta Simon** aus.

2. Klicken Sie auf der Symbolleiste unten auf **Zuweisen**. <br><br>![Benutzer zuweisen][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Zugriffsbereichs.<br> Wenn Sie im Zugriffsbereich auf die Kachel „Kindling“ klicken, sollten Sie automatisch bei Ihrer Kindling-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0406_2016-->