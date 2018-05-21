---
title: 'Tutorial: Azure Active Directory-Integration in FirmPlay – Employee Advocacy for Recruiting | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und FirmPlay – Employee Advocacy for Recruiting konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
ms.openlocfilehash: b154901d2e31f493c32e47bd331cc2d4e9fdc1a4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Tutorial: Azure Active Directory-Integration in FirmPlay – Employee Advocacy for Recruiting

In diesem Tutorial erfahren Sie, wie Sie FirmPlay – Employee Advocacy for Recruiting in Azure Active Directory (Azure AD) integrieren.

Die Integration von FirmPlay – Employee Advocacy for Recruiting in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf FirmPlay – Employee Advocacy for Recruiting hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei FirmPlay – Employee Advocacy for Recruiting anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Verwaltungsportal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Folgendes ist erforderlich, damit Sie die Azure AD-Integration in FirmPlay – Employee Advocacy for Recruiting konfigurieren können:

- Ein Azure AD-Abonnement
- Ein FirmPlay – Employee Advocacy for Recruiting-Abonnement, für das einmaliges Anmelden aktiviert ist


> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.


## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von FirmPlay – Employee Advocacy for Recruiting aus dem Katalog
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Hinzufügen von FirmPlay – Employee Advocacy for Recruiting aus dem Katalog
Damit Sie die Integration von FirmPlay – Employee Advocacy for Recruiting in Azure AD konfigurieren können, müssen Sie FirmPlay – Employee Advocacy for Recruiting aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um FirmPlay – Employee Advocacy for Recruiting aus dem Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Verwaltungsportals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

2. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![ANWENDUNGEN][2]
    
3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Hinzufügen**.

    ![ANWENDUNGEN][3]

4. Geben Sie **FirmPlay – Employee Advocacy for Recruiting** in das Suchfeld ein.

    ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_001.png)

5. Wählen Sie im Ergebnisbereich die Option **FirmPlay – Employee Advocacy for Recruiting** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
Dieser Abschnitt veranschaulicht anhand eines Testbenutzers namens „Britta Simon“, wie das einmalige Anmelden von Azure AD in FirmPlay – Employee Advocacy for Recruiting konfiguriert und getestet wird.

Damit das einmalige Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in FirmPlay – Employee Advocacy for Recruiting als Entsprechung zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in FirmPlay – Employee Advocacy for Recruiting muss eine Linkbeziehung eingerichtet werden.

Diese Linkbeziehung wird hergestellt, indem Sie den **Benutzernamen** in Azure AD als **Benutzernamen** in FirmPlay – Employee Advocacy for Recruiting zuweisen.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei FirmPlay – Employee Advocacy for Recruiting müssen Sie die folgenden Bausteine ausführen:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit der Testbenutzerin Britta Simon zu testen.
3. **[Erstellen eines FirmPlay – Employee Advocacy for Recruiting-Testbenutzers](#creating-a-firmplay---employee-advocacy-for-recruiting-test-user)** als Entsprechung von Britta Simon in FirmPlay – Employee Advocacy for Recruiting, der mit ihrer Darstellung in Azure AD verknüpft ist.
4. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testing Single Sign-On](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden mit Azure AD im Azure-Verwaltungsportal und konfigurieren das einmalige Anmelden in Ihrer FirmPlay – Employee Advocacy for Recruiting-Anwendung.

**Führen Sie die folgenden Schritte aus, um das einmalige Anmelden mit Azure AD für FirmPlay – Employee Advocacy for Recruiting zu konfigurieren:**

1. Klicken Sie im Azure-Verwaltungsportal auf der Anwendungsintegrationsseite von **FirmPlay – Employee Advocacy for Recruiting** auf **Einmaliges Anmelden**.

    ![Configure Single Sign-On][4]

2. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_01.png)

3. Geben Sie im Abschnitt **FirmPlay – Employee Advocacy for Recruiting Domain and URLs** (Domäne und URLs für FirmPlay – Employee Advocacy for Recruiting) im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<your-subdomain>.firmplay.com/`

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_02.png)

    > [!NOTE] 
    > Hinweis: Hierbei handelt es sich um einen Beispielwert. Sie müssen den Wert mit der richtigen Anmelde-URL aktualisieren. Wenden Sie sich an das [Supportteam von FirmPlay – Employee Advocacy for Recruiting](mailto:engineering@firmplay.com), um diesen Wert zu erhalten. 

4. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Neues Zertifikat erstellen**.

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_03.png)   

5. Klicken Sie im Dialogfeld **Neues Zertifikat erstellen** auf das Kalendersymbol, und wählen Sie ein **Ablaufdatum** aus. Klicken Sie auf die Schaltfläche **Speichern**.

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_general_300.png)

6. Wählen Sie im Abschnitt **SAML-Signaturzertifikat** die Option **Make new certificate active** (Neues Zertifikat zum aktiven Zertifikat machen), und klicken Sie auf die Schaltfläche **Speichern**.

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_04.png)

7. Klicken Sie im Popupfenster **Rolloverzertifikat** auf **OK**.

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_general_400.png)

8. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Zertifikat (base64)**, und speichern Sie die Zertifikatdatei auf Ihrem Computer. 

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_05.png) 

9. Klicken Sie im Abschnitt **FirmPlay - Employee Advocacy for Recruiting Configuration** (FirmPlay – Employee Advocacy for Recruiting-Konfiguration) auf **Configure FirmPlay - Employee Advocacy for Recruiting** (FirmPlay – Employee Advocacy for Recruiting konfigurieren), um das Dialogfeld **Anmeldung konfigurieren** zu öffnen.

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_06.png) 

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_07.png)

10. Wenden Sie sich an das [Supportteam von FirmPlay – Employee Advocacy for Recruiting](mailto:engineering@firmplay.com), um SSO für Ihre Anwendung konfigurieren zu lassen, und stellen Sie Folgendes bereit: 

    •  Die heruntergeladene **Zertifikatsdatei**

    •  Die **SAML-Dienst-URL für einmaliges Anmelden**

    •  Die **SAML-Entitäts-ID**

    •  Die **Abmelde-URL**
  

### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
In diesem Abschnitt wird im Azure-Verwaltungsportal eine Testbenutzerin namens Britta Simon erstellt.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf das Symbol für **Azure Active Directory**.

    ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-firmplay-tutorial/create_aaduser_01.png) 

2. Wechseln Sie zu **Benutzer und Gruppen**, und klicken Sie auf **Alle Benutzer**, um die Liste der Benutzer anzuzeigen.
    
    ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-firmplay-tutorial/create_aaduser_02.png) 

3. Klicken Sie oben im Dialogfeld auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-firmplay-tutorial/create_aaduser_03.png) 

4. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-firmplay-tutorial/create_aaduser_04.png) 

    a. Geben Sie in das Textfeld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie in das Textfeld **Benutzername** die **E-Mail-Adresse** von Britta Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Create**. 



### <a name="creating-a-firmplay---employee-advocacy-for-recruiting-test-user"></a>Erstellen eines FirmPlay – Employee Advocacy for Recruiting-Testbenutzers

In diesem Abschnitt erstellen Sie in FirmPlay – Employee Advocacy for Recruiting einen Benutzer namens „Britta Simon“. Fügen Sie die Benutzer zusammen mit dem [Supportteam von FirmPlay – Employee Advocacy for Recruiting](mailto:engineering@firmplay.com) der FirmPlay – Employee Advocacy for Recruiting-Plattform hinzu.


### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf FirmPlay – Employee Advocacy for Recruiting gewähren.

![Benutzer zuweisen][200] 

**Führen Sie die folgenden Schritte aus, um Britta Simon zu FirmPlay – Employee Advocacy for Recruiting zuzuweisen:**

1. Öffnen Sie im Azure-Verwaltungsportal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

2. Wählen Sie in der Liste der Anwendungen **FirmPlay – Employee Advocacy for Recruiting** aus.

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_50.png) 

3. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Benutzer zuweisen][202] 

4. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Benutzer zuweisen][203]

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

6. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    


### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „FirmPlay – Employee Advocacy for Recruiting“ klicken, werden Sie automatisch bei Ihrer FirmPlay – Employee Advocacy for Recruiting-Anwendung angemeldet.


## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_203.png