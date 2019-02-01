---
title: 'Tutorial: Azure Active Directory-Integration mit Inkling | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Inkling konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 64c7ee45-ee8a-42f7-bf04-fd0e00833ea9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
ms.openlocfilehash: 83412b180c71b3c4deb67db4d76c277be312b7ab
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175195"
---
# <a name="tutorial-azure-active-directory-integration-with-inkling"></a>Tutorial: Azure Active Directory-Integration mit Inkling

In diesem Tutorial erfahren Sie, wie Sie Inkling in Azure Active Directory (Azure AD) integrieren.

Die Integration von Inkling in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf Inkling hat.
- Sie können Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Inkling anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Verwaltungsportal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Inkling konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Inkling-Abonnement, für das einmaliges Anmelden aktiviert ist


> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/)eine einmonatige Testversion anfordern.


## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von Inkling über den Katalog
1. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## <a name="adding-inkling-from-the-gallery"></a>Hinzufügen von Inkling über den Katalog
Zum Konfigurieren der Integration von Inkling in Azure AD müssen Sie Inkling aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Inkling aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Verwaltungsportals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

1. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![ANWENDUNGEN][2]
    
1. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Hinzufügen**.

    ![ANWENDUNGEN][3]

1. Geben Sie im Suchfeld als Suchbegriff **Inkling** ein.

    ![Erstellen eines Azure AD-Testbenutzers](./media/inkling-tutorial/tutorial_inkling_001.png)

1. Wählen Sie im Ergebnisbereich die Option **Inkling** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Erstellen eines Azure AD-Testbenutzers](./media/inkling-tutorial/tutorial_inkling_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Inkling mithilfe einer Testbenutzerin namens Britta Simon.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Inkling als Gegenpart für einen Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Inkling muss eine Linkbeziehung eingerichtet werden.

Diese Linkbeziehung wird hergestellt, indem Sie den **Benutzernamen** in Azure AD als **Benutzernamen** in Inkling zuweisen.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Inkling müssen Sie die folgenden Bausteine ausführen:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
1. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit der Testbenutzerin Britta Simon zu testen.
1. **[Erstellen eines Inkling-Testbenutzers](#creating-an-inkling-test-user)**, um ein Gegenstück zu Britta Simon in Inkling zu erhalten, das mit ihrer Repräsentation in Azure AD verknüpft ist.
1. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Testing Single Sign-On](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden mit Azure AD im Azure-Verwaltungsportal und konfigurieren das einmalige Anmelden in Ihrer Inkling-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Inkling die folgenden Schritte aus:**

1. Klicken Sie im Azure-Verwaltungsportal auf der Anwendungsintegrationsseite für **Inkling** auf **Einmaliges Anmelden**.

    ![Configure single sign-on][4]

1. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Configure single sign-on](./media/inkling-tutorial/tutorial_general_300.png)
    
1. Führen Sie die folgenden Schritte auf der Seite **Domäne und URLs für Inkling** aus:
    
    ![Configure single sign-on](./media/inkling-tutorial/tutorial_inkling_01.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL nach folgendem Muster ein: `https://api.inkling.com/saml/v2/metadata/<user-id>`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: `https://api.inkling.com/saml/v2/acs/<user-id>`

    > [!NOTE] 
    > Hinweis: Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL aktualisieren. Wenden Sie sich an das [Supportteam von Inkling](mailto:press@inkling.com), um diese Werte zu erhalten.

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Neues Zertifikat erstellen**.

    ![Configure single sign-on](./media/inkling-tutorial/tutorial_general_400.png)  

1. Klicken Sie im Dialogfeld **Neues Zertifikat erstellen** auf das Kalendersymbol, und wählen Sie ein **Ablaufdatum** aus. Klicken Sie auf die Schaltfläche **Speichern**.

    ![Configure single sign-on](./media/inkling-tutorial/tutorial_general_500.png)

1. Wählen Sie im Abschnitt **SAML-Signaturzertifikat** die Option **Make new certificate active** (Neues Zertifikat zum aktiven Zertifikat machen), und klicken Sie auf die Schaltfläche **Speichern**.

    ![Configure single sign-on](./media/inkling-tutorial/tutorial_inkling_02.png)

1. Klicken Sie im Popupfenster **Rolloverzertifikat** auf **OK**.

    ![Configure single sign-on](./media/inkling-tutorial/tutorial_general_600.png)

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Metadaten-XML**, und speichern Sie die Metadatendatei dann auf Ihrem Computer.

    ![Configure single sign-on](./media/inkling-tutorial/tutorial_inkling_03.png) 

1. Wenden Sie sich an das [Supportteam von Inkling](mailto:press@inkling.com), um SSO für Ihre Anwendung konfigurieren zu lassen, und stellen Sie die heruntergeladenen **Metadaten** bereit. 


### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
In diesem Abschnitt wird im Azure-Verwaltungsportal eine Testbenutzerin namens Britta Simon erstellt.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf das Symbol für **Azure Active Directory**.

    ![Erstellen eines Azure AD-Testbenutzers](./media/inkling-tutorial/create_aaduser_01.png) 

1. Wechseln Sie zu **Benutzer und Gruppen**, und klicken Sie auf **Alle Benutzer**, um die Liste der Benutzer anzuzeigen.
    
    ![Erstellen eines Azure AD-Testbenutzers](./media/inkling-tutorial/create_aaduser_02.png) 

1. Klicken Sie oben im Dialogfeld auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/inkling-tutorial/create_aaduser_03.png) 

1. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/inkling-tutorial/create_aaduser_04.png) 

    a. Geben Sie in das Textfeld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie in das Textfeld **Benutzername** die **E-Mail-Adresse** von Britta Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Create**. 



### <a name="creating-an-inkling-test-user"></a>Erstellen eines Inkling-Testbenutzers

In diesem Abschnitt erstellen Sie in Inkling eine Benutzerin namens Britta Simon. Lassen Sie sich beim Hinzufügen der Benutzer ggf. vom [Inkling-Supportteam](mailto:press@inkling.com) unterstützen.


### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Inkling gewähren.

![Benutzer zuweisen][200] 

**Um Britta Simon Inkling zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Verwaltungsportal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

1. Wählen Sie in der Anwendungsliste **Inkling** aus.

    ![Configure single sign-on](./media/inkling-tutorial/tutorial_inkling_50.png) 

1. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Benutzer zuweisen][202] 

1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Benutzer zuweisen][203]

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

1. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    


### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Inkling“ klicken, sollten Sie automatisch bei Ihrer Inkling-Anwendung angemeldet werden.


## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/inkling-tutorial/tutorial_general_01.png
[2]: ./media/inkling-tutorial/tutorial_general_02.png
[3]: ./media/inkling-tutorial/tutorial_general_03.png
[4]: ./media/inkling-tutorial/tutorial_general_04.png

[100]: ./media/inkling-tutorial/tutorial_general_100.png

[200]: ./media/inkling-tutorial/tutorial_general_200.png
[201]: ./media/inkling-tutorial/tutorial_general_201.png
[202]: ./media/inkling-tutorial/tutorial_general_202.png
[203]: ./media/inkling-tutorial/tutorial_general_203.png
