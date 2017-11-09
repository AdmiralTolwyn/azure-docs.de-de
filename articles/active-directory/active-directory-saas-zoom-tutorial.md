---
title: 'Tutorial: Azure Active Directory-Integration mit Zoom | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Zoom konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: b8d902b9170fee47afb038df739bbca6d2d025e0
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Tutorial: Azure Active Directory-Integration mit Zoom

In diesem Tutorial erfahren Sie, wie Sie Zoom in Azure Active Directory (Azure AD) integrieren.

Die Integration von Zoom in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf Zoom hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Zoom anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten: im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Zoom konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Zoom-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie eine [einmonatige Testversion anfordern](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptelementen:

1. Hinzufügen von Zoom aus dem Katalog
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-zoom-from-the-gallery"></a>Hinzufügen von Zoom aus dem Katalog
Zum Konfigurieren der Integration von Zoom in Azure AD müssen Sie Zoom aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Zoom aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Schaltfläche „Azure Active Directory“][1]

2. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“][2]
    
3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“][3]

4. Geben Sie im Suchfeld **Zoom** ein, wählen Sie im Ergebnisbereich **Zoom** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Zoom in der Ergebnisliste](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Zoom basierend auf einem Testbenutzer mit dem Namen Britta Simon.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Zoom als Gegenstück zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zoom muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in Zoom den Wert für **Benutzername** in Azure AD als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Zoom müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
3. **[Erstellen eines Zoom-Testbenutzers](#create-a-zoom-test-user)**, um eine Entsprechung von Britta Simon in Zoom zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer Zoom-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Zoom die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Zoom** auf **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden][4]

2. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Dialogfeld „Einmaliges Anmelden“](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_samlbase.png)

3. Führen Sie auf der Seite **Domäne und URLs für Zoom** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Zoom](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_url.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<companyname>.zoom.us`.

    b. Geben Sie im Textfeld **Bezeichner** eine URL nach folgendem Muster ein: `<companyname>.zoom.us`

    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Clientsupportteam von Zoom](https://support.zoom.us/hc), um diese Werte zu erhalten. 
 
4. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Zertifikat (Base64)**, und speichern Sie die Zertifikatdatei auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_certificate.png) 

5. Klicken Sie auf die Schaltfläche **Save** .

    ![Schaltfläche „Einmaliges Anmelden konfigurieren“](./media/active-directory-saas-zoom-tutorial/tutorial_general_400.png)

6. Klicken Sie im Abschnitt **Zoom-Konfiguration** auf **Zoom konfigurieren**, um das Fenster **Anmeldung konfigurieren** zu öffnen. Kopieren Sie die **Abmelde-URL, die SAML-Entitäts-ID und die URL für den SAML-SSO-Dienst** aus dem Abschnitt **Kurzübersicht**.

    ![Zoom-Konfiguration](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_configure.png) 

7. Melden Sie sich in einem anderen Webbrowserfenster bei der Zoom-Unternehmenswebsite als Administrator an.

8. Klicken Sie auf die Registerkarte **Einmaliges Anmelden** .
   
    ![Registerkarte für einmaliges Anmelden](./media/active-directory-saas-zoom-tutorial/IC784700.png "Einmaliges Anmelden")

9. Klicken Sie auf die Registerkarte **Sicherheitskontrollen**, und navigieren Sie dann zu den Einstellungen für **Einmaliges Anmelden**.

10. Führen Sie im Abschnitt „Einmaliges Anmelden“ die folgenden Schritte aus:
   
    ![Abschnitt für einmaliges Anmelden](./media/active-directory-saas-zoom-tutorial/IC784701.png "Einmaliges Anmelden")
   
    a. Fügen Sie in das Textfeld **URL der Anmeldeseite** den Wert der **SAML-Dienst-URL für einmaliges Anmelden** ein, den Sie aus dem Azure-Portal kopiert haben.
   
    b. Fügen Sie in das Textfeld **URL der Abmeldeseite** den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
     
    c. Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Zertifikat des Identitätsanbieters** ein.

    d. Fügen Sie in das Textfeld **Aussteller** den Wert der **SAML-Entitäts-ID** ein, den Sie aus dem Azure-Portal kopiert haben. 

    e. Klicken Sie auf **Speichern**.

> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Eingebettete Azure AD-Dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

   ![Erstellen eines Azure AD-Testbenutzers][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Bereich des Azure-Portals auf die Schaltfläche **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](./media/active-directory-saas-zoom-tutorial/create_aaduser_01.png)

2. Navigieren Sie zu **Benutzer und Gruppen**, und klicken Sie dann auf **Alle Benutzer**, um die Liste mit den Benutzern anzuzeigen.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](./media/active-directory-saas-zoom-tutorial/create_aaduser_02.png)

3. Klicken Sie oben im Dialogfeld **Alle Benutzer** auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.

    ![Schaltfläche „Hinzufügen“](./media/active-directory-saas-zoom-tutorial/create_aaduser_03.png)

4. Führen Sie im Dialogfeld **Neuer Benutzer** die folgenden Schritte aus:

    ![Dialogfeld „Benutzer“](./media/active-directory-saas-zoom-tutorial/create_aaduser_04.png)

    a. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie im Feld **Benutzername** die E-Mail-Adresse des Benutzers Britta Simon ein.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    d. Klicken Sie auf **Erstellen**.
 
### <a name="create-a-zoom-test-user"></a>Erstellen eines Zoom-Testbenutzers

Damit sich Azure AD-Benutzer bei Zoom anmelden können, müssen sie in Zoom bereitgestellt werden. Im Fall von Zoom ist die Bereitstellung eine manuelle Aufgabe.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:

1. Melden Sie sich bei der **Zoom** -Unternehmenswebsite als Administrator an.
 
2. Klicken Sie auf die Registerkarte **Kontenverwaltung** und anschließend auf **Benutzerverwaltung**.

3. Klicken Sie im Abschnitt „Benutzerverwaltung“ auf **Benutzer hinzufügen**.
   
    ![Benutzerverwaltung](./media/active-directory-saas-zoom-tutorial/IC784703.png "Benutzerverwaltung")

4. Führen Sie auf der Seite **Benutzer hinzufügen** die folgenden Schritte aus:
   
    ![Benutzer hinzufügen](./media/active-directory-saas-zoom-tutorial/IC784704.png "Benutzer hinzufügen")
   
    a. Wählen Sie als **Benutzertyp** die Option **Basic** aus.

    b. Geben Sie im Textfeld **Emails** die E-Mail-Adresse eines gültigen Azure AD-Benutzerkontos ein, das Sie bereitstellen möchten.

    c. Klicken Sie auf **Hinzufügen**.

> [!NOTE]
> Sie können Azure Active Directory-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Zoom-Benutzerkonten oder mithilfe der von Zoom bereitgestellten APIs erstellen.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zoom gewähren.

![Zuweisen der Benutzerrolle][200] 

**Um Britta Simon Zoom zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

2. Wählen Sie in der Anwendungsliste **Zoom** aus.

    ![Zoom-Link in der Anwendungsliste](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_app.png)  

3. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Link „Benutzer und Gruppen“][202]

4. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“][203]

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

6. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Zoom“ klicken, sollten Sie automatisch bei Ihrer Zoom-Anwendung angemeldet.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_203.png

