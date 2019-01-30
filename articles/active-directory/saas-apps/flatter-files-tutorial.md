---
title: 'Tutorial: Azure Active Directory-Integration mit Flatter Files | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Flatter Files konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: aaf6c6ba17e41c4d32aafa98dbd2c1dc2532e197
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808305"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Tutorial: Azure Active Directory-Integration mit Flatter Files

In diesem Tutorial erfahren Sie, wie Sie Flatter Files in Azure Active Directory (Azure AD) integrieren.

Die Integration von Flatter Files in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer auf Flatter Files Zugriff hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Flatter Files anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Flatter Files konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Flatter Files-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/)eine einmonatige Testversion anfordern.

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von Flatter Files aus dem Katalog
1. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-flatter-files-from-the-gallery"></a>Hinzufügen von Flatter Files aus dem Katalog
Zum Konfigurieren der Integration von Flatter Files in Azure AD müssen Sie Flatter Files aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Flatter Files aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

1. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![ANWENDUNGEN][2]
    
1. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![ANWENDUNGEN][3]

1. Geben Sie im Suchfeld als Suchbegriff **Flatter Files**ein.

    ![Erstellen eines Azure AD-Testbenutzers](./media/flatter-files-tutorial/tutorial_flatterfiles_search.png)

1. Wählen Sie im Ergebnisbereich die Option **Flatter Files**  aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Erstellen eines Azure AD-Testbenutzers](./media/flatter-files-tutorial/tutorial_flatterfiles_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Flatter Files basierend auf einer Testbenutzerin namens Britta Simon.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Flatter Files als Pendant zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Flatter Files muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in Flatter Files den Wert des **Benutzernamens** in Azure AD als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD mit Flatter Files müssen Sie die folgenden Bausteine ausführen:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
1. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit der Testbenutzerin Britta Simon zu testen.
1. **[Erstellen eines Flatter Files-Testbenutzers](#creating-a-flatter-files-test-user)**, um ein Pendant von Britta Simon in Flatter Files zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Testing Single Sign-On](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer Flatter Files-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens in Azure AD mit Flatter Files die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Flatter Files** auf **Einmaliges Anmelden**.

    ![Configure single sign-on][4]

1. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Configure single sign-on](./media/flatter-files-tutorial/tutorial_flatterfiles_samlbase.png)

1. Im Abschnitt **Domäne und URLs für Flatter Files** muss der Benutzer keine Schritte ausführen, da die App bereits in Azure integriert ist.

    ![Configure single sign-on](./media/flatter-files-tutorial/tutorial_flatterfiles_url.png)
 
1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Zertifikat (Base64)**, und speichern Sie die Zertifikatdatei auf Ihrem Computer.

    ![Configure single sign-on](./media/flatter-files-tutorial/tutorial_flatterfiles_certificate.png) 

1. Klicken Sie auf die Schaltfläche **Save** .

    ![Configure single sign-on](./media/flatter-files-tutorial/tutorial_general_400.png)

1. Klicken Sie im Abschnitt **Flatter Files-Konfiguration** auf **Flatter Files konfigurieren**, um das Fenster **Anmeldung konfigurieren** zu öffnen. Kopieren Sie die **URL für den SAML-SSO-Dienst** aus dem Abschnitt **Kurzübersicht**.

    ![Configure single sign-on](./media/flatter-files-tutorial/tutorial_flatterfiles_configure.png) 

1. Melden Sie sich bei Ihrer Flatter Files-Anwendung als Administrator an.

1. Klicken Sie auf **DASHBOARD**. 
   
    ![Configure single sign-on](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

1. Klicken Sie auf **Settings** (Einstellungen), und führen Sie auf der Registerkarte **Company** (Unternehmen) die folgenden Schritte aus: 
   
    ![Configure single sign-on](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Wählen Sie **Use SAML 2.0 for Authentication**aus.
    
    b. Klicken Sie auf **Configure SAML**.

1. Führen Sie im Dialogfeld **SAML Configuration** die folgenden Schritte aus: 
   
    ![Configure single sign-on](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. Geben Sie in das Textfeld für die **Domäne** die registrierte Domäne ein.
   
    >[!NOTE]
    >Wenn Sie noch keine registrierte Domäne besitzen, wenden Sie sich unter [support@flatterfiles.com](mailto:support@flatterfiles.com). 
    
    b. Fügen Sie in das Textfeld **Identitätsanbieter-URL** den Wert der **SAML-Dienst-URL für einmalige Anmeldung** ein, den Sie aus dem Azure-Portal kopiert haben.
   
    c.  Öffnen Sie das Base64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Zertifikat des Identitätsanbieters** ein.

    d. Klicken Sie auf **Aktualisieren**.

> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Dokumentation zu eingebettetem Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Portals** auf das Symbol für **Azure Active Directory**.

    ![Erstellen eines Azure AD-Testbenutzers](./media/flatter-files-tutorial/create_aaduser_01.png) 

1. Wechseln Sie zu **Benutzer und Gruppen**, und klicken Sie auf **Alle Benutzer**, um die Liste der Benutzer anzuzeigen.
    
    ![Erstellen eines Azure AD-Testbenutzers](./media/flatter-files-tutorial/create_aaduser_02.png) 

1. Klicken Sie oben im Dialogfeld auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/flatter-files-tutorial/create_aaduser_03.png) 

1. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/flatter-files-tutorial/create_aaduser_04.png) 

    a. Geben Sie in das Textfeld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie in das Textfeld **Benutzername** die **E-Mail-Adresse** von Britta Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Create**.
 
### <a name="creating-a-flatter-files-test-user"></a>Erstellen eines Flatter Files-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in Flatter Files.

**Um einen Benutzer namens Britta Simon in Flatter Files zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrer **Flatter Files** -Unternehmenswebsite als Administrator an.

1. Klicken Sie im Navigationsbereich links auf **Settings** (Einstellungen) und anschließend auf die Registerkarte **Users** (Benutzer).
   
    ![Erstellen eines Flatter Files-Benutzers](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

1. Klicken Sie auf **Benutzer hinzufügen**. 

1. Führen Sie im Dialogfeld **Add User** die folgenden Schritte aus:
   
    ![Erstellen eines Flatter Files-Benutzers](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. Geben Sie in das Textfeld **Vorname** den Namen **Britta** ein.
   
    b. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein. 
   
    c. Geben Sie im Textfeld **Email Address** die E-Mail-Adresse von Britta Simon aus dem Azure-Portal ein.
   
    d. Klicken Sie auf **Submit**.   


### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Flatter Files gewähren.

![Benutzer zuweisen][200] 

**Um Britta Simon Flatter Files zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

1. Wählen Sie in der Anwendungsliste **Flatter Files**aus.

    ![Configure single sign-on](./media/flatter-files-tutorial/tutorial_flatterfiles_app.png) 

1. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Benutzer zuweisen][202] 

1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Benutzer zuweisen][203]

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

1. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Flatter Files“ klicken, sollten Sie automatisch bei Ihrer Flatter Files-Anwendung angemeldet werden.
Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/flatter-files-tutorial/tutorial_general_04.png

[100]: ./media/flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/flatter-files-tutorial/tutorial_general_201.png
[202]: ./media/flatter-files-tutorial/tutorial_general_202.png
[203]: ./media/flatter-files-tutorial/tutorial_general_203.png

