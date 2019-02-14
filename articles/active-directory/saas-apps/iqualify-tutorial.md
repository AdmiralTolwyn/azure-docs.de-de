---
title: 'Tutorial: Azure Active Directory-Integration mit iQualify LMS | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und iQualify LMS konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25711bd09adf17fa82f9177f4badad723e590b12
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56184192"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Tutorial: Azure Active Directory-Integration mit iQualify LMS

In diesem Tutorial erfahren Sie, wie Sie iQualify LMS in Azure Active Directory (Azure AD) integrieren.

Die Integration von iQualify LMS in Azure AD hat folgende Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf iQualify LMS haben soll.
- Sie können es Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei iQualify LMS anzumelden (Single Sign-On, SSO; einmaliges Anmelden).
- Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren der Azure AD-Integration mit iQualify LMS benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein SSO-fähiges iQualify LMS-Abonnement

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie eine [einmonatige Testversion anfordern](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von iQualify LMS über den Katalog
1. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-iqualify-lms-from-the-gallery"></a>Hinzufügen von iQualify LMS über den Katalog
Um die Integration von iQualify LMS in Azure AD zu konfigurieren, müssen Sie iQualify LMS über den Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um iQualify LMS über den Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Schaltfläche „Azure Active Directory“][1]

1. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“][2]
    
1. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“][3]

1. Geben Sie im Suchfeld die Zeichenfolge **iQualify LMS** ein, wählen Sie im Ergebnisbereich **iQualify LMS** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![iQualify LMS in der Ergebnisliste](./media/iqualify-tutorial/tutorial_iqualify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei iQualify LMS mithilfe eines Testbenutzers namens Britta Simon.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in iQualify LMS als Gegenstück zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in iQualify LMS muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in iQualify LMS den Wert für **Benutzername** aus Azure AD als Wert für **Benutzername** zu, um die Linkbeziehung herzustellen.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit iQualify LMS zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
1. **[Erstellen eines iQualify LMS-Testbenutzers](#create-an-iqualify-lms-test-user)**, um in iQualify LMS eine Entsprechung von Britta Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren es in Ihrer iQualify LMS-Anwendung.

**Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit iQualify LMS zu konfigurieren:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **iQualify LMS** auf **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden][4]

1. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Dialogfeld „Einmaliges Anmelden“](./media/iqualify-tutorial/tutorial_iqualify_samlbase.png)

1. Führen Sie im Abschnitt **Domäne und URLs für iQualify LMS** die folgenden Schritte aus, wenn Sie die Anwendung im IDP-initiierten Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für iQualify LMS](./media/iqualify-tutorial/tutorial_iqualify_url.png)

    a. Geben Sie im Textfeld **Identifier** (Bezeichner) eine URL nach folgendem Muster ein: 
    | |
    |--|--|
    | Produktionsumgebung: `https://<yourorg>.iqualify.com/`|
    | Testumgebung: `https://<yourorg>.iqualify.io`|
    
    b. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: 
    | |
    |--|--|
    | Produktionsumgebung: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Testumgebung: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

1. Aktivieren Sie **Erweiterte URL-Einstellungen anzeigen**, und führen Sie die folgenden Schritte aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für iQualify LMS](./media/iqualify-tutorial/tutorial_iqualify_url1.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein:
    | |
    |--|--|
    | Produktionsumgebung: `https://<yourorg>.iqualify.com/login` |
    | Testumgebung: `https://<yourorg>.iqualify.io/login` |
     
    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner, die Antwort-URL und die Anmelde-URL. Wenden Sie sich an das [Supportteam für den iQualify LMS-Client](https://www.iqualify.com), um diese Werte zu erhalten. 

1. Die iQualify LMS-Anwendung erwartet, dass die SAML-Assertionen (Security Assertion Markup Language) in einem bestimmten Format angezeigt werden. Konfigurieren Sie die Ansprüche, und verwalten Sie die Werte der Attribute im Abschnitt **Benutzerattribute** der Seite für die Integration der iQualify-Anwendung. Dies ist im folgenden Screenshot dargestellt:
    
    ![Configure single sign-on](./media/iqualify-tutorial/atb.png)

1. Führen Sie im Dialogfeld **Einmaliges Anmelden** im Abschnitt **Benutzerattribute** für jede in der folgenden Tabelle aufgeführte Zeile die folgenden Schritte aus:
    
    | Attributname | Attributwert |
    | --- | --- |    
    | E-Mail | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | „Ihr Attribut“ | 

    a. Klicken Sie auf **Attribut hinzufügen**, um das Dialogfeld **Benutzerattribut hinzufügen** zu öffnen.

    ![Configure single sign-on](./media/iqualify-tutorial/atb2.png)

    ![Configure single sign-on](./media/iqualify-tutorial/atb3.png)
    
    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.
    
    c. Geben Sie in der Liste **Wert** den für diese Zeile angezeigten Wert ein.
    
    d. Klicken Sie auf **OK**.

    e. Wiederholen Sie die Schritte a bis d für die nächsten Tabellenzeilen. 

    > [!Note]
    > Für das Attribut **person_id** ist die Wiederholung der Schritte a bis d **optional**.

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Zertifikat (Base64)**, und speichern Sie die Zertifikatdatei auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](./media/iqualify-tutorial/tutorial_iqualify_certificate.png) 

1. Klicken Sie auf die Schaltfläche **Save** .

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/iqualify-tutorial/tutorial_general_400.png)
    
1. Klicken Sie im Abschnitt **iQualify LMS-Konfiguration** auf **iQualify LMS konfigurieren**, um das Fenster **Anmeldung konfigurieren** zu öffnen. Kopieren Sie die **Abmelde-URL und die URL für den SAML-SSO-Dienst** aus dem Abschnitt **Kurzübersicht**.

    ![iQualify LMS-Konfiguration](./media/iqualify-tutorial/tutorial_iqualify_configure.png) 

1.  Öffnen Sie ein neues Browserfenster, und melden Sie sich als Administrator bei Ihrer iQualify LMS-Umgebung an.

1. Klicken Sie nach der Anmeldung rechts oben auf Ihren Avatar und anschließend auf **Kontoeinstellungen**.

    ![Kontoeinstellungen](./media/iqualify-tutorial/setting1.png) 
1. Klicken Sie im linken Bereich der Kontoeinstellungen auf das Menüband und anschließend auf **INTEGRATIONEN**.
    
    ![INTEGRATIONEN](./media/iqualify-tutorial/setting2.png)

1. Klicken Sie unter „INTEGRATIONEN“ auf das Symbol **SAML**.

    ![SAML-Symbol](./media/iqualify-tutorial/setting3.png)

1. Führen Sie im Dialogfeld **SAML-Authentifizierungseinstellungen** die folgenden Schritte aus:

    ![SAML-Authentifizierungseinstellungen](./media/iqualify-tutorial/setting4.png)

    a. Fügen Sie im Feld **SAML SINGLE SIGN-ON SERVICE URL** (URL für den SAML-SSO-Dienst) den **Wert der URL für den SAML-SSO-Dienst** ein, den Sie im Konfigurationsfenster der Azure AD-Anwendung kopiert haben.
    
    b. Fügen Sie im Feld **SAML LOGOUT URL** (SAML-Abmelde-URL) den **Wert für die Abmelde-URL** ein, den Sie im Konfigurationsfenster der Azure AD-Anwendung kopiert haben.
    
    c. Öffnen Sie die heruntergeladene Zertifikatsdatei im Editor, kopieren Sie den Inhalt, und fügen Sie ihn anschließend in das Feld **ÖFFENTLICHES ZERTIFIKAT** ein.
    
    d. Geben Sie unter **LOGIN BUTTON LABEL** (BESCHRIFTUNG DER ANMELDESCHALTFLÄCHE) den Namen für die Schaltfläche ein, der auf der Anmeldeseite angezeigt werden soll.
    
    e. Klicken Sie auf **SPEICHERN**.

    f. Klicken Sie auf **AKTUALISIEREN**.

> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Dokumentation zu eingebettetem Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

   ![Erstellen eines Azure AD-Testbenutzers][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Bereich des Azure-Portals auf die Schaltfläche **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](./media/iqualify-tutorial/create_aaduser_01.png)

1. Navigieren Sie zu **Benutzer und Gruppen**, und klicken Sie dann auf **Alle Benutzer**, um die Liste mit den Benutzern anzuzeigen.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](./media/iqualify-tutorial/create_aaduser_02.png)

1. Klicken Sie oben im Dialogfeld **Alle Benutzer** auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.

    ![Schaltfläche „Hinzufügen“](./media/iqualify-tutorial/create_aaduser_03.png)

1. Führen Sie im Dialogfeld **Neuer Benutzer** die folgenden Schritte aus:

    ![Dialogfeld „Benutzer“](./media/iqualify-tutorial/create_aaduser_04.png)

    a. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie im Feld **Benutzername** die E-Mail-Adresse des Benutzers Britta Simon ein.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    d. Klicken Sie auf **Create**.
 
### <a name="create-an-iqualify-lms-test-user"></a>Erstellen eines iQualify LMS-Testbenutzers

In diesem Abschnitt wird in iQualify ein Benutzer namens Britta Simon erstellt. iQualify LMS unterstützt die (standardmäßig aktivierte) Just-in-Time-Bereitstellung von Benutzern.

Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Falls ein Benutzer noch nicht in iQualify vorhanden ist, wird beim Versuch, auf iQualify LMS zuzugreifen, ein neuer Benutzer erstellt.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf iQualify LMS gewähren.

![Zuweisen der Benutzerrolle][200] 

**Führen Sie die folgenden Schritte aus, um die Zuweisung von Britta Simon zu iQualify LMS durchzuführen:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

1. Wählen Sie in der Anwendungsliste **iQualify LMS**aus.

    ![iQualify LMS-Link in der Anwendungsliste](./media/iqualify-tutorial/tutorial_iqualify_app.png)  

1. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Link „Benutzer und Gruppen“][202]

1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“][203]

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

1. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „iQualify LMS“ klicken, wird die Anmeldeseite Ihrer iQualify LMS-Anwendung aufgerufen. 

   ![Anmeldeseite](./media/iqualify-tutorial/login.png) 

Klicken Sie auf die Schaltfläche **Sign in with Azure AD** (Mit Azure AD anmelden). Daraufhin sollten Sie automatisch bei Ihrer iQualify LMS-Anwendung angemeldet werden.

Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/iqualify-tutorial/tutorial_general_01.png
[2]: ./media/iqualify-tutorial/tutorial_general_02.png
[3]: ./media/iqualify-tutorial/tutorial_general_03.png
[4]: ./media/iqualify-tutorial/tutorial_general_04.png

[100]: ./media/iqualify-tutorial/tutorial_general_100.png

[200]: ./media/iqualify-tutorial/tutorial_general_200.png
[201]: ./media/iqualify-tutorial/tutorial_general_201.png
[202]: ./media/iqualify-tutorial/tutorial_general_202.png
[203]: ./media/iqualify-tutorial/tutorial_general_203.png

