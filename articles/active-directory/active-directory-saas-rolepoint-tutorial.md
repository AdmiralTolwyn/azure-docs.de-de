---
title: 'Tutorial: Azure Active Directory-Integration mit RolePoint | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und RolePoint konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 68d37f40-15da-45f5-a9e1-d53f78e786d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: jeedes
ms.openlocfilehash: 92c1a09558087fde2e5f8ad87227948dd3372c56
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-rolepoint"></a>Tutorial: Azure Active Directory-Integration mit RolePoint

In diesem Tutorial erfahren Sie, wie Sie RolePoint in Azure Active Directory (Azure AD) integrieren.

Die Integration von RolePoint in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf RolePoint hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei RolePoint anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit RolePoint konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein RolePoint-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/)eine einmonatige Testversion anfordern.

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptelementen:

1. Hinzufügen von RolePoint aus dem Katalog
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-rolepoint-from-the-gallery"></a>Hinzufügen von RolePoint aus dem Katalog
Zum Konfigurieren der Integration von RolePoint in Azure AD müssen Sie RolePoint aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um RolePoint aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

2. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![Anwendungen][2]
    
3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Anwendungen][3]

4. Geben Sie im Suchfeld als Suchbegriff **RolePoint** ein.

    ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_search.png)

5. Wählen Sie im Ergebnisbereich **RolePoint** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit RolePoint basierend auf einem Testbenutzer mit dem Namen Britta Simon.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in RolePoint als Gegenstück zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in RolePoint muss eine Linkbeziehung eingerichtet werden.

Diese Linkbeziehung wird hergestellt, indem Sie den **Benutzernamen** in Azure AD als **Benutzernamen** in RolePoint zuweisen.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei RolePoint müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configuring-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** – um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
3. **[Erstellen eines RolePoint-Testbenutzers](#creating-a-rolepoint-test-user)**, um eine Entsprechung von Britta Simon in RolePoint zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
4. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testing Single Sign-On](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt ermöglichen Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer RolePoint-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in RolePoint die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **RolePoint** auf **Einmaliges Anmelden**.

    ![Einmaliges Anmelden konfigurieren][4]

2. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_samlbase.png)

3. Führen Sie die folgenden Schritte auf der Seite **Domäne und URLs für RolePoint** aus:

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_url.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<subdomain>.rolepoint.com/login`.
    
    b. Geben Sie im Textfeld **Bezeichner** eine URL nach folgendem Muster ein: `https://app.rolepoint.com/<instancename>`

    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Hier empfehlen wir Ihnen, den eindeutigen Zeichenfolgenwert als Bezeichner zu verwenden. Den Wert erhalten Sie vom [RolePoint-Supportteam](mailto:info@rolepoint.com). 
 
4. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Metadaten-XML**, und speichern Sie die Metadatendatei dann auf Ihrem Computer.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_certificate.png) 

5. Klicken Sie auf die Schaltfläche **Save** .

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rolepoint-tutorial/tutorial_general_400.png)


6. Zum Konfigurieren des einmaligen Anmeldens bei **RolePoint** müssen Sie die heruntergeladene **Metadaten-XML**-Datei an das [RolePoint-Supportteam](mailto:info@rolepoint.com) senden.

> [!TIP]
> Während Sie die App einrichten, können Sie im [Azure-Portal](https://portal.azure.com) eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Eingebettete Azure AD-Dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Portals** auf das Symbol für **Azure Active Directory**.

    ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-rolepoint-tutorial/create_aaduser_01.png) 

2. Wechseln Sie zu **Benutzer und Gruppen**, und klicken Sie auf **Alle Benutzer**, um die Liste der Benutzer anzuzeigen.
    
    ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-rolepoint-tutorial/create_aaduser_02.png) 

3. Klicken Sie oben im Dialogfeld auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-rolepoint-tutorial/create_aaduser_03.png) 

4. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-rolepoint-tutorial/create_aaduser_04.png) 

    a. Geben Sie in das Textfeld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie in das Textfeld **Benutzername** die **E-Mail-Adresse** von Britta Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Erstellen**.
 
### <a name="creating-a-rolepoint-test-user"></a>Erstellen eines RolePoint-Testbenutzers

In diesem Abschnitt erstellen Sie in RolePoint einen Benutzer namens Britta Simon. Wenden Sie sich an das [Supportteam von RolePoint](mailto:info@rolepoint.com), um die Benutzer der RolePoint-Plattform hinzufügen zu lassen.

### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf RolePoint gewähren.

![Benutzer zuweisen][200] 

**Um Britta Simon zu RolePoint zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

2. Wählen Sie in der Anwendungsliste **RolePoint** aus.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-rolepoint-tutorial/tutorial_rolepoint_app.png) 

3. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Benutzer zuweisen][202] 

4. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Benutzer zuweisen][203]

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

6. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „RolePoint“ klicken, sollten Sie automatisch in Ihrer RolePoint-Anwendung angemeldet werden. 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rolepoint-tutorial/tutorial_general_203.png

