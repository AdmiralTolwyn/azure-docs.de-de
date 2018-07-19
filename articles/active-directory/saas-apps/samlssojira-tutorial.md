---
title: 'Tutorial: Azure Active Directory-Integration mit SAML SSO for Jira by resolution GmbH | Microsoft-Dokumentation'
description: In diesem Artikel erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAML SSO for Jira by resolution GmbH konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: 05a91e66d046bb7869179175c3a7d0b13b1942e4
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042189"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Tutorial: Azure Active Directory-Integration mit SAML SSO for Jira by resolution GmbH

In diesem Tutorial erfahren Sie, wie Sie SAML SSO for Jira by resolution GmbH in Azure Active Directory (Azure AD) integrieren.

Die Integration von SAML SSO for Jira by resolution GmbH in Azure AD bietet Ihnen folgende Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf SAML SSO for Jira by resolution GmbH hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei SAML SSO for Jira by resolution GmbH anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit SAML SSO for Jira by resolution GmbH konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Abonnement, das für das einmalige Anmelden bei SAML SSO for Jira by resolution GmbH aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/)eine einmonatige Testversion anfordern.

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von SAML SSO for Jira by resolution GmbH aus dem Katalog
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Hinzufügen von SAML SSO for Jira by resolution GmbH aus dem Katalog
Um die Integration von SAML SSO for Jira by resolution GmbH in Azure AD zu konfigurieren, müssen Sie SAML SSO for Jira by resolution GmbH aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

**Um SAML SSO for Jira by resolution GmbH aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte durch:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

2. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![ANWENDUNGEN][2]
    
3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![ANWENDUNGEN][3]

4. Geben Sie in das Suchfeld **SAML SSO for Jira by resolution GmbH** ein.

    ![Erstellen eines Azure AD-Testbenutzers](./media/samlssojira-tutorial/tutorial_samlssojira_search.png)

5. Wählen Sie im Ergebnisbereich die Option **SAML SSO for Jira by resolution GmbH** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Erstellen eines Azure AD-Testbenutzers](./media/samlssojira-tutorial/tutorial_samlssojira_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden mit Azure AD bei SAML SSO for Jira by resolution GmbH basierend auf einem Testbenutzer mit dem Namen „Britta Simon“.

Damit das einmalige Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in SAML SSO for Jira by resolution GmbH als Gegenstück zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAML SSO for Jira by resolution GmbH muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in SAML SSO for Jira by resolution GmbH den Wert für **Benutzername** in Azure AD als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Führen Sie die folgenden Bausteine aus, um das einmalige Anmelden mit Azure AD bei SAML SSO for Jira by resolution GmbH zu konfigurieren und zu testen:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit der Testbenutzerin Britta Simon zu testen.
3. **[Erstellen eines Testbenutzers für SAML SSO for Jira by resolution GmbH](#creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user)** – um ein Gegenstück für Britta Simon in SAML SSO for Jira by resolution GmbH bereitzustellen, das mit der Azure AD-Darstellung eines Benutzers verknüpft ist.
4. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testing Single Sign-On](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden mit Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer SAML SSO for Jira by resolution GmbH-Anwendung.

**Führen Sie die folgenden Schritte durch, um das einmalige Anmelden mit Azure AD bei SAML SSO for Jira by resolution GmbH zu konfigurieren:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SAML SSO for Jira by resolution GmbH** auf **Einmaliges Anmelden**.

    ![Configure single sign-on][4]

2. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Configure single sign-on](./media/samlssojira-tutorial/tutorial_samlssojira_samlbase.png)

3. Führen Sie im Abschnitt **Domäne und URLs für SAML SSO for Jira by resolution GmbH** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    ![Configure single sign-on](./media/samlssojira-tutorial/tutorial_samlssojira_url_1.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL nach folgendem Muster ein: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: `https://<server-base-url>/plugins/servlet/samlsso`

4. Aktivieren Sie **Erweiterte URL-Einstellungen anzeigen**. Wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![Configure single sign-on](./media/samlssojira-tutorial/tutorial_samlssojira_url_2.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`.
     
    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner, die Antwort-URL und die Anmelde-URL. Diese Werte erhalten Sie vom [Supportteam für den SAML SSO for Jira by resolution GmbH-Client](https://www.resolution.de/go/support). 

5. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Metadaten-XML**, und speichern Sie die Metadatendatei dann auf Ihrem Computer.

    ![Configure single sign-on](./media/samlssojira-tutorial/tutorial_samlssojira_certificate.png) 

6. Klicken Sie auf die Schaltfläche **Save** .

    ![Configure single sign-on](./media/samlssojira-tutorial/tutorial_general_400.png)
    
7. Melden Sie sich in einem anderen Webbrowserfenster beim **SAML SSO for Jira by resolution GmbH-Verwaltungsportal** als Administrator an.

8. Fahren Sie mit dem Mauszeiger über das Zahnrad, und klicken Sie auf die **Add-Ons**.
    
    ![Configure single sign-on](./media/samlssojira-tutorial/addon1.png)

9. Sie werden zur Seite „Administratorzugriff“ umgeleitet. Geben Sie das **Kennwort** ein, und klicken Sie auf die Schaltfläche **Bestätigen**.

    ![Configure single sign-on](./media/samlssojira-tutorial/addon2.png)

10. Klicken Sie im Registerkartenabschnitt „Add-Ons“ auf **Nach neuen Add-Ons suchen**. Suchen Sie nach **Einmalige SAML-Anmeldung (SSO) für JIRA**, und klicken Sie auf die Schaltfläche **Installieren**, um das neue SAML-Plug-In zu installieren.

    ![Configure single sign-on](./media/samlssojira-tutorial/addon7.png)

11. Die Installation des Plug-Ins wird gestartet. Klicken Sie auf **Schließen**.

    ![Configure single sign-on](./media/samlssojira-tutorial/addon8.png)

    ![Configure single sign-on](./media/samlssojira-tutorial/addon9.png)

12. Klicken Sie auf **Manage**.

    ![Configure single sign-on](./media/samlssojira-tutorial/addon10.png)
    
13. Klicken Sie auf **Konfigurieren**, um das neue Plug-In zu konfigurieren.

    ![Configure single sign-on](./media/samlssojira-tutorial/addon11.png)

14. Klicken Sie auf der Seite **Konfiguration des SAML-SSO-Plug-Ins** auf die Schaltfläche **Add new IdP** (Neuen IdP hinzufügen), um die Einstellungen des Identitätsanbieters zu konfigurieren.

    ![Configure single sign-on](./media/samlssojira-tutorial/addon4.png)

15. Führen Sie auf der Seite **Choose your SAML Identity Provider** (SAML-Identitätsanbieter auswählen) die folgenden Schritte aus:

    ![Configure single sign-on](./media/samlssojira-tutorial/addon5a.png)
 
    a. Legen Sie als IdP-Typ die Option **Azure AD** fest.
    
    b. Fügen Sie den **Namen** des Identitätsanbieters (z.B. Azure AD) hinzu.
    
    c. Fügen Sie eine **Beschreibung** des Identitätsanbieters (z.B. Azure AD) hinzu.
    
    d. Klicken Sie auf **Weiter**.
    
16. Klicken Sie auf der Seite **Identitätsanbieterkonfiguration** auf die Schaltfläche **Weiter**.

    ![Configure single sign-on](./media/samlssojira-tutorial/addon5b.png)

17. Führen Sie auf der Seite **Import SAML IdP Metadata** (SAML-IdP-Metadaten importieren) die folgenden Schritte aus:

    ![Configure single sign-on](./media/samlssojira-tutorial/addon5c.png)

    a. Klicken Sie auf die Schaltfläche **Datei laden**, und wählen Sie die in Schritt 5 heruntergeladene Metadaten-XML-Datei aus.

    b. Klicken Sie auf die Schaltfläche **Importieren**.
    
    c. Warten Sie kurz, bis der Import erfolgreich ausgeführt wurde.
    
    d. Klicken Sie auf die Schaltfläche **Weiter**.
    
18. Klicken Sie auf der Seite **User ID attribute and transformation** (Benutzer-ID-Attribut und Transformation) auf die Schaltfläche **Weiter**.

    ![Configure single sign-on](./media/samlssojira-tutorial/addon5d.png)
    
19. Klicken Sie auf der Seite **User creation and update** (Benutzererstellung und -aktualisierung) auf **Save & Next** (Speichern und weiter), um die Einstellungen zu speichern.   
    
    ![Configure single sign-on](./media/samlssojira-tutorial/addon6a.png)
    
20. Klicken Sie auf der Seite **Testen Ihrer Einstellungen** auf **Skip test & configure manually** (Test überspringen und manuell konfigurieren), um den Benutzertest vorerst zu überspringen. Dieser wird im nächsten Abschnitt durchgeführt und erfordert einige Einstellungen im Azure-Portal. 
    
    ![Configure single sign-on](./media/samlssojira-tutorial/addon6b.png)
    
21. Klicken Sie im angezeigten Dialogfeld **Skipping the test means...** (Das Überspringen des Tests bedeutet...) auf **OK**.
    
    ![Configure single sign-on](./media/samlssojira-tutorial/addon6c.png)

> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Eingebettete Azure AD-Dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Portals** auf das Symbol für **Azure Active Directory**.

    ![Erstellen eines Azure AD-Testbenutzers](./media/samlssojira-tutorial/create_aaduser_01.png) 

2. Wechseln Sie zu **Benutzer und Gruppen**, und klicken Sie auf **Alle Benutzer**, um die Liste der Benutzer anzuzeigen.
    
    ![Erstellen eines Azure AD-Testbenutzers](./media/samlssojira-tutorial/create_aaduser_02.png) 

3. Klicken Sie oben im Dialogfeld auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/samlssojira-tutorial/create_aaduser_03.png) 

4. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/samlssojira-tutorial/create_aaduser_04.png) 

    a. Geben Sie in das Textfeld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie in das Textfeld **Benutzername** die **E-Mail-Adresse** von Britta Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Create**.
 
### <a name="creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Erstellen eines Testbenutzers für SAML SSO for Jira by resolution GmbH

Um Azure AD-Benutzern die Anmeldung bei SAML SSO for Jira by resolution GmbH zu ermöglichen, müssen diese in SAML SSO for Jira by resolution GmbH bereitgestellt werden.  
Die Bereitstellung in SAML SSO for Jira by resolution GmbH ist ein manueller Vorgang.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der Unternehmenswebsite von SAML SSO for Jira by resolution GmbH als Administrator an.

2. Fahren Sie mit dem Mauszeiger über das Zahnrad, und klicken Sie auf **Benutzerverwaltung**.

    ![Mitarbeiter hinzufügen](./media/samlssojira-tutorial/user1.png) 

3. Sie werden zur Seite „Administratorzugriff“ umgeleitet, um Ihr **Kennwort** einzugeben. Klicken Sie anschließend auf die Schaltfläche **Bestätigen**.

    ![Mitarbeiter hinzufügen](./media/samlssojira-tutorial/user2.png) 

4. Klicken Sie im Registerkartenabschnitt **Benutzerverwaltung** auf **Benutzer erstellen**.

    ![Mitarbeiter hinzufügen](./media/samlssojira-tutorial/user3.png) 

5. Führen Sie auf der Dialogfeldseite **Neuen Benutzer erstellen** die folgenden Schritte durch:

    ![Mitarbeiter hinzufügen](./media/samlssojira-tutorial/user4.png) 

    a. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers, z.B. Brittasimon@contoso.com, ein.

    b. Geben Sie im Textfeld **Vollständiger Name** den vollständigen Namen des Benutzers, z.B. „Britta Simon“, ein.

    c. Geben Sie im Textfeld **Benutzername** die E-Mail-Adresse des Benutzers, z.B. Brittasimon@contoso.com, ein.

    d. Geben Sie im Textfeld **Kennwort** das Kennwort des Benutzers ein.

    e. Klicken Sie auf **Benutzer erstellen**.   

### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SAML SSO for Jira by resolution GmbH gewähren.

![Benutzer zuweisen][200] 

**Um Britta Simon zu SAML SSO for Jira by resolution GmbH hinzuzufügen, führen Sie die folgenden Schritte durch:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

2. Wählen Sie in der Anwendungsliste **SAML SSO for Jira by resolution GmbH** aus.

    ![Configure single sign-on](./media/samlssojira-tutorial/tutorial_samlssojira_app.png) 

3. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Benutzer zuweisen][202] 

4. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Benutzer zuweisen][203]

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

6. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SAML SSO for Jira by resolution GmbH“ klicken, sollten Sie automatisch bei Ihrer SAML SSO for Jira by resolution GmbH-Anwendung angemeldet werden.
Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/samlssojira-tutorial/tutorial_general_01.png
[2]: ./media/samlssojira-tutorial/tutorial_general_02.png
[3]: ./media/samlssojira-tutorial/tutorial_general_03.png
[4]: ./media/samlssojira-tutorial/tutorial_general_04.png

[100]: ./media/samlssojira-tutorial/tutorial_general_100.png

[200]: ./media/samlssojira-tutorial/tutorial_general_200.png
[201]: ./media/samlssojira-tutorial/tutorial_general_201.png
[202]: ./media/samlssojira-tutorial/tutorial_general_202.png
[203]: ./media/samlssojira-tutorial/tutorial_general_203.png

