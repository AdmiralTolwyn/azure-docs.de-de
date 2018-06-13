---
title: 'Tutorial: Azure Active Directory-Integration mit SmartRecruiters | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SmartRecruiters konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: e96aeecd-e113-454e-89c3-58c9f44cfd4c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: jeedes
ms.openlocfilehash: 3c455b6dee2f935ed4551b0a4f905688fc192168
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352394"
---
# <a name="tutorial-azure-active-directory-integration-with-smartrecruiters"></a>Tutorial: Azure Active Directory-Integration mit SmartRecruiters

In diesem Tutorial erfahren Sie, wie Sie SmartRecruiters in Azure Active Directory (Azure AD) integrieren.

Die Integration von SmartRecruiters in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf SmartRecruiters hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei SmartRecruiters anzumelden (Single Sign-On, SSO; einmaliges Anmelden).
- Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit SmartRecruiters konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein SmartRecruiters-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie eine [einmonatige Testversion anfordern](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von SmartRecruiters aus dem Katalog
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-smartrecruiters-from-the-gallery"></a>Hinzufügen von SmartRecruiters aus dem Katalog
Zum Konfigurieren der Integration von SmartRecruiters in Azure AD müssen Sie SmartRecruiters aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um SmartRecruiters aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Schaltfläche „Azure Active Directory“][1]

2. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“][2]
    
3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“][3]

4. Geben Sie im Suchfeld **SmartRecruiters** ein, wählen Sie im Ergebnisbereich **SmartRecruiters** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![SmartRecruiters in der Ergebnisliste](./media/active-directory-saas-smartrecruiters-tutorial/tutorial_smartrecruiters_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei SmartRecruiters mithilfe eines Testbenutzers mit dem Namen Britta Simon.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in SmartRecruiters als Pendant zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SmartRecruiters muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in SmartRecruiters den Wert für **Benutzername** in Azure AD als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit SmartRecruiters zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
3. **[Erstellen eines SmartRecruiters-Testbenutzers](#create-a-smartrecruiters-test-user)**, um ein Pendant von Britta Simon in SmartRecruiters zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer SmartRecruiters-Anwendung.

**Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit SmartRecruiters zu konfigurieren:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SmartRecruiters** auf **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden][4]

2. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Dialogfeld „Einmaliges Anmelden“](./media/active-directory-saas-smartrecruiters-tutorial/tutorial_smartrecruiters_samlbase.png)

3. Führen Sie im Abschnitt **Domäne und URLs für SmartRecruiters** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten Modus** konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für SmartRecruiters](./media/active-directory-saas-smartrecruiters-tutorial/tutorial_smartrecruiters_url.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL nach folgendem Muster ein: `https://www.smartrecruiters.com/web-sso/saml/<companyname>`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: `https://www.smartrecruiters.com/web-sso/saml/<companyname>/callback`

4. Aktivieren Sie **Erweiterte URL-Einstellungen anzeigen**, und führen Sie die folgenden Schritte aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für SmartRecruiters](./media/active-directory-saas-smartrecruiters-tutorial/tutorial_smartrecruiters_url1.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://www.smartrecruiters.com/web-sso/saml/<companyname>/login`.
     
    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner, die Antwort-URL und die Anmelde-URL. Wenden Sie sich an das [Supportteam für den SmartRecruiters-Client](https://www.smartrecruiters.com/about-us/contact-us/), um diese Werte zu erhalten. 

5. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Zertifikat (Base64)**, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](./media/active-directory-saas-smartrecruiters-tutorial/tutorial_smartrecruiters_certificate.png) 

6. Klicken Sie auf die Schaltfläche **Save** .

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_400.png)
    
7. Klicken Sie im Abschnitt **SmartRecruiters-Konfiguration** auf **SmartRecruiters konfigurieren**, um das Fenster **Anmeldung konfigurieren** zu öffnen. Kopieren Sie die **URL für den SAML-SSO-Dienst** aus dem Abschnitt **Kurzübersicht**.

    ![SmartRecruiters-Konfiguration](./media/active-directory-saas-smartrecruiters-tutorial/tutorial_smartrecruiters_configure.png) 

8. Melden Sie sich in einem anderen Webbrowserfenster bei der SmartRecruiters-Unternehmenswebsite als Administrator an.

9. Navigieren Sie zu **Settings / Admin** (Einstellungen/Administrator).

    ![SmartRecruiters-Konfiguration](./media/active-directory-saas-smartrecruiters-tutorial/configure.png)

10. Klicken Sie im Abschnitt **Konfiguration** auf **Web SSO** (Web-SSO).

    ![SmartRecruiters-Konfiguration](./media/active-directory-saas-smartrecruiters-tutorial/configure1.png)

11. Schalten Sie **Enable Web SSO** (Web-SSO aktivieren) ein oder aus.

    ![SmartRecruiters-Konfiguration](./media/active-directory-saas-smartrecruiters-tutorial/configure2.png)

12. Führen Sie unter **Identitätsanbieterkonfiguration** die folgenden Schritte aus:

    ![SmartRecruiters-Konfiguration](./media/active-directory-saas-smartrecruiters-tutorial/configure4.png)

    a. Fügen Sie in das Textfeld **Identitätsanbieter-URL** den Wert für **SAML Single Sign-On Service URL** (SAML-Dienst-URL für einmaliges Anmelden) ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Öffnen Sie das **Zertifikat (Base64)**, das Sie aus dem Azure-Portal heruntergeladen haben, und fügen Sie den Wert ins Textfeld **Identitätsanbieterzertifikat** ein.

13. Klicken Sie auf **Save Web SSO configuration** (Web-SSO-Konfiguration speichern).

> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Eingebettete Azure AD-Dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

   ![Erstellen eines Azure AD-Testbenutzers][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Bereich des Azure-Portals auf die Schaltfläche **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](./media/active-directory-saas-smartrecruiters-tutorial/create_aaduser_01.png)

2. Navigieren Sie zu **Benutzer und Gruppen**, und klicken Sie dann auf **Alle Benutzer**, um die Liste mit den Benutzern anzuzeigen.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](./media/active-directory-saas-smartrecruiters-tutorial/create_aaduser_02.png)

3. Klicken Sie oben im Dialogfeld **Alle Benutzer** auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.

    ![Schaltfläche „Hinzufügen“](./media/active-directory-saas-smartrecruiters-tutorial/create_aaduser_03.png)

4. Führen Sie im Dialogfeld **Neuer Benutzer** die folgenden Schritte aus:

    ![Dialogfeld „Benutzer“](./media/active-directory-saas-smartrecruiters-tutorial/create_aaduser_04.png)

    a. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie im Feld **Benutzername** die E-Mail-Adresse des Benutzers Britta Simon ein.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    d. Klicken Sie auf **Create**.
 
### <a name="create-a-smartrecruiters-test-user"></a>Erstellen eines SmartRecruiters-Testbenutzers

In diesem Abschnitt erstellen Sie in SmartRecruiters einen Benutzer mit dem Namen Britta Simon. Lassen Sie sich beim Hinzufügen der Benutzer auf der SmartRecruiters-Plattform ggf. vom [SmartRecruiters-Supportteam](https://www.smartrecruiters.com/about-us/contact-us/) unterstützen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können. 

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SmartRecruiters gewähren.

![Zuweisen der Benutzerrolle][200] 

**Führen Sie die folgenden Schritte aus, um Britta Simon SmartRecruiters zuzuweisen:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

2. Wählen Sie in der Anwendungsliste **SmartRecruiters**  aus.

    ![SmartRecruiters-Link in der Anwendungsliste](./media/active-directory-saas-smartrecruiters-tutorial/tutorial_smartrecruiters_app.png)  

3. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Link „Benutzer und Gruppen“][202]

4. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“][203]

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

6. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SmartRecruiters“ klicken, sollten Sie automatisch bei Ihrer SmartRecruiters-Anwendung angemeldet werden.
Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-smartrecruiters-tutorial/tutorial_general_203.png

