---
title: 'Tutorial: Azure Active Directory-Integration mit FilesAnywhere | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und FilesAnywhere konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: jeedes
ms.openlocfilehash: 8a08155dd67c6fcf2fb080325840bc163dc6da60
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447355"
---
# <a name="tutorial-azure-active-directory-integration-with-filesanywhere"></a>Tutorial: Azure Active Directory-Integration mit FilesAnywhere

In diesem Tutorial erfahren Sie, wie Sie FilesAnywhere in Azure Active Directory (Azure AD) integrieren.

Die Integration von FilesAnywhere in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf FilesAnywhere hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei FilesAnywhere anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Verwaltungsportal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit FilesAnywhere konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein SSO-fähiges FilesAnywhere-Abonnement


> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.


## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von FilesAnywhere aus dem Katalog
1. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## <a name="adding-filesanywhere-from-the-gallery"></a>Hinzufügen von FilesAnywhere aus dem Katalog
Zum Konfigurieren der Integration von FilesAnywhere in Azure AD müssen Sie FilesAnywhere aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um FilesAnywhere aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Verwaltungsportals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

1. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![ANWENDUNGEN][2]
    
1. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Hinzufügen**.

    ![ANWENDUNGEN][3]

1. Geben Sie im Suchfeld als Suchbegriff **FilesAnywhere** ein.

    ![Erstellen eines Azure AD-Testbenutzers](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_search.png)

1. Wählen Sie im Ergebnisbereich die Option **FilesAnywhere** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Erstellen eines Azure AD-Testbenutzers](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei FilesAnywhere mithilfe eines Testbenutzers namens Britta Simon.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in FilesAnywhere als Gegenpart zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in FilesAnywhere muss eine Linkbeziehung eingerichtet werden.

Diese Linkbeziehung wird hergestellt, indem Sie den **Benutzernamen** in Azure AD als **Benutzernamen** in FilesAnywhere zuweisen.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei FilesAnywhere müssen Sie die folgenden Bausteine ausführen:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
1. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit der Testbenutzerin Britta Simon zu testen.
1. **[Erstellen eines FilesAnywhere-Testbenutzers](#creating-a-filesanywhere-test-user)**, um in FilesAnywhere einen Gegenpart von Britta Simon zu erhalten, der mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Testing Single Sign-On](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden mit Azure AD im Azure-Verwaltungsportal und konfigurieren das einmalige Anmelden in Ihrer FilesAnywhere-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in FilesAnywhere die folgenden Schritte aus:**

1. Klicken Sie im Azure-Verwaltungsportal auf der Anwendungsintegrationsseite für **FilesAnywhere** auf **Einmaliges Anmelden**.

    ![Configure single sign-on][4]

1. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Configure single sign-on](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_samlbase.png)

1. Führen Sie im Abschnitt **Domäne und URLs für FilesAnywhere** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten Modus** konfigurieren möchten:

    ![Configure single sign-on](./media/filesanywhere-tutorial/tutorial_filesanywhere_url.png)
    
    a. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: `https://<company name>.filesanywhere.com/saml20.aspx?c=215`
> [!NOTE]
> Beachten Sie, dass der Wert **215** für **clientid** nur ein Beispiel ist. Sie müssen es durch den tatsächlichen clientid-Wert ersetzen.

1. Führen Sie im Abschnitt **Domäne und URLs für FilesAnywhere** die folgenden Schritte aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:
    
    ![Configure single sign-on](./media/filesanywhere-tutorial/tutorial_filesanywhere_url1.png)

    a. Klicken Sie auf die Option **Erweiterte URL-Einstellungen anzeigen**.

    b. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<sub domain>.filesanywhere.com/`

    > [!NOTE] 
    > Hinweis: Hierbei handelt es sich um Beispielwerte. Die Werte müssen durch die tatsächliche Anmelde-URL und die tatsächliche Antwort-URL ersetzt werden. Wenden Sie sich an das [Supportteam von FilesAnywhere](mailto:support@FilesAnywhere.com), um diese Werte zu erhalten. 

1. Ihre FilesAnywhere-Softwareanwendung erwartet die SAML-Assertions in einem bestimmten Format. Konfigurieren Sie für diese Anwendung die folgenden Ansprüche. Sie können die Werte dieser Attribute über den Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:
    
    ![Configure single sign-on](./media/filesanywhere-tutorial/tutorial_filesanywhere_attribute.png)
    
    Wenn Benutzer sich bei FilesAnywhere registrieren, erhalten sie den Wert des **clientid**-Attributs vom [FilesAnywhere-Team](mailto:support@FilesAnywhere.com). Sie müssen das clientid-Attribut mit dem eindeutigen Wert hinzufügen, der von FilesAnywhere bereitgestellt wurde. Alle oben gezeigten Attribute sind erforderlich.
    > [!NOTE] 
    > Beachten Sie, dass der Wert **2331** von **clientid** nur ein Beispiel ist. Sie müssen den tatsächlichen Wert angeben.


1. Konfigurieren Sie das SAML-Tokenattribut im Dialogfeld **Einmaliges Anmelden** im Abschnitt **Benutzerattribute**, wie im obigen Bild gezeigt, und führen Sie die folgenden Schritte aus:
    
    | Attributname | Attributwert |
    | ---------------| --------------- |    
    | clientid | *"eindeutigerWert"* |

    a. Klicken Sie auf **Attribut hinzufügen**, um das Dialogfeld **Benutzerattribut hinzufügen** zu öffnen.

    ![Configure single sign-on](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_04.png)

    ![Configure single sign-on](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_05.png)
    
    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.
    
    c. Geben Sie in der Liste **Wert** den für diese Zeile angezeigten Wert ein.
    
    d. Klicken Sie auf **OK**.

1. Klicken Sie auf die Schaltfläche **Save** .

    ![Configure single sign-on](./media/filesanywhere-tutorial/tutorial_general_400.png)

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Zertifikat (Base64)**, und speichern Sie die Zertifikatdatei auf Ihrem Computer.

    ![Configure single sign-on](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_certificate.png) 

1. Klicken Sie im Abschnitt **FilesAnywhere-Konfiguration** auf **FilesAnywhere konfigurieren**, um das Fenster **Anmeldung konfigurieren** zu öffnen.

    ![Configure single sign-on](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configure.png) 

    ![Configure single sign-on](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configuresignon.png)

1.  Um die SSO-Konfiguration für Ihre Anwendung aufseiten von FilesAnywhere abzuschließen, wenden Sie sich an das [FilesAnywhere-Supportteam](mailto:support@FilesAnywhere.com), und geben Sie das heruntergeladene SAML-Tokensignaturzertifikat und die URL für einmaliges Anmelden (SSO) an.

### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
In diesem Abschnitt wird im Azure-Verwaltungsportal eine Testbenutzerin namens Britta Simon erstellt.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf das Symbol für **Azure Active Directory**.

    ![Erstellen eines Azure AD-Testbenutzers](./media/filesanywhere-tutorial/create_aaduser_01.png) 

1. Wechseln Sie zu **Benutzer und Gruppen**, und klicken Sie auf **Alle Benutzer**, um die Liste der Benutzer anzuzeigen.
    
    ![Erstellen eines Azure AD-Testbenutzers](./media/filesanywhere-tutorial/create_aaduser_02.png) 

1. Klicken Sie oben im Dialogfeld auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/filesanywhere-tutorial/create_aaduser_03.png) 

1. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/filesanywhere-tutorial/create_aaduser_04.png) 

    a. Geben Sie in das Textfeld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie in das Textfeld **Benutzername** die **E-Mail-Adresse** von Britta Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Create**. 



### <a name="creating-a-filesanywhere-test-user"></a>Erstellen eines FilesAnywhere-Testbenutzers

Die Anwendung unterstützt die Just-in-Time-Benutzerbereitstellung. Nach der Authentifizierung werden in der Anwendung automatisch Benutzer erstellt. 


### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf FilesAnywhere gewähren.

![Benutzer zuweisen][200] 

**Um Britta Simon FilesAnywhere zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Verwaltungsportal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

1. Wählen Sie in der Anwendungsliste **FilesAnywhere** aus.

    ![Configure single sign-on](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_app.png) 

1. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Benutzer zuweisen][202] 

1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Benutzer zuweisen][203]

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

1. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    


### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „FilesAnywhere“ klicken, sollten Sie automatisch bei Ihrer FilesAnywhere-Anwendung angemeldet werden.


## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/FilesAnywhere-tutorial/tutorial_general_01.png
[2]: ./media/FilesAnywhere-tutorial/tutorial_general_02.png
[3]: ./media/FilesAnywhere-tutorial/tutorial_general_03.png
[4]: ./media/FilesAnywhere-tutorial/tutorial_general_04.png

[100]: ./media/FilesAnywhere-tutorial/tutorial_general_100.png

[200]: ./media/FilesAnywhere-tutorial/tutorial_general_200.png
[201]: ./media/FilesAnywhere-tutorial/tutorial_general_201.png
[202]: ./media/FilesAnywhere-tutorial/tutorial_general_202.png
[203]: ./media/FilesAnywhere-tutorial/tutorial_general_203.png
