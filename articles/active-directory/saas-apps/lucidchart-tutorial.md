---
title: 'Tutorial: Azure Active Directory-Integration mit Lucidchart | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Lucidchart konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: 45dbf350bc874d48b077ba8f7d67819eff741df2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448039"
---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Tutorial: Azure Active Directory-Integration mit Lucidchart

In diesem Tutorial erfahren Sie, wie Sie Lucidchart in Azure Active Directory (Azure AD) integrieren.

Die Integration von Lucidchart in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer auf Lucidchart Zugriff hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Lucidchart anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Lucidchart konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Lucidchart-Software-Abonnement, für das das einmalige Anmelden aktiviert ist.

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/)eine einmonatige Testversion anfordern.

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von Lucidchart aus dem Katalog
1. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-lucidchart-from-the-gallery"></a>Hinzufügen von Lucidchart aus dem Katalog
Zum Konfigurieren der Integration von Lucidchart in Azure AD müssen Sie Lucidchart aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Lucidchart aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

1. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![ANWENDUNGEN][2]
    
1. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![ANWENDUNGEN][3]

1. Geben Sie im Suchfeld **Lucidchart** ein.

    ![Erstellen eines Azure AD-Testbenutzers](./media/lucidchart-tutorial/tutorial_lucidchart_search.png)

1. Wählen Sie im Ergebnisbereich die Option **Lucidchart** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Erstellen eines Azure AD-Testbenutzers](./media/lucidchart-tutorial/tutorial_lucidchart_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Lucidchart basierend auf einem Testbenutzer mit dem Namen Britta Simon.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Lucidchart als Entsprechung für einen Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Lucidchart muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in Lucidchart den Wert für **Benutzername** in Azure AD als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Lucidchart müssen Sie die folgenden Bausteine ausführen:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
1. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit der Testbenutzerin Britta Simon zu testen.
1. **[Erstellen eines Lucidchart-Testbenutzers](#creating-a-lucidchart-test-user)**, um eine Entsprechung von Britta Simon in Lucidchart zu erhalten, die mit ihrer Repräsentation in Azure AD verknüpft ist.
1. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Testing Single Sign-On](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt ermöglichen Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer Lucidchart-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Lucidchart die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Lucidchart** auf **Einmaliges Anmelden**.

    ![Configure single sign-on][4]

1. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Configure single sign-on](./media/lucidchart-tutorial/tutorial_lucidchart_samlbase.png)

1. Führen Sie auf der Seite **Domäne und URLs für Lucidchart** die folgenden Schritte aus:

    ![Configure single sign-on](./media/lucidchart-tutorial/tutorial_lucidchart_url.png)

    Geben Sie im Textfeld **Anmelde-URL** die URL folgendermaßen ein: `https://chart2.office.lucidchart.com/saml/sso/azure`.

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Metadaten-XML**, und speichern Sie die Metadatendatei dann auf Ihrem Computer.

    ![Configure single sign-on](./media/lucidchart-tutorial/tutorial_lucidchart_certificate.png) 

1. Klicken Sie auf die Schaltfläche **Save** .

    ![Configure single sign-on](./media/lucidchart-tutorial/tutorial_general_400.png)

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Lucidchart-Unternehmenswebsite als Administrator an.

1. Klicken Sie im oberen Menü auf **Team**.
   
    ![Team](./media/lucidchart-tutorial/ic791190.png "Team")

1. Klicken Sie auf **Anwendungen \> SAML verwalten**.
   
    ![SAML verwalten](./media/lucidchart-tutorial/ic791191.png "SAML verwalten")

1. Führen Sie auf der Dialogseite **SAML-Authentifizierungseinstellungen** die folgenden Schritte aus:
   
    a. Wählen Sie **SAML-Authentifizierung aktivieren** aus, und klicken Sie dann auf **Optional**.

    ![SAML-Authentifizierungseinstellungen](./media/lucidchart-tutorial/ic791192.png "SAML-Authentifizierungseinstellungen")
 
    b. Geben Sie im Textfeld **Domäne** Ihre Domäne ein, und klicken Sie auf **Zertifikat ändern**.

    ![Zertifikat ändern](./media/lucidchart-tutorial/ic791193.png "Zertifikat ändern")
 
    c. Öffnen Sie die heruntergeladene Metadatendatei, kopieren Sie den Inhalt, und fügen Sie ihn in das Textfeld **Metadaten hochladen** ein.

    ![Metadaten hochladen](./media/lucidchart-tutorial/ic791194.png "Metadaten hochladen")
 
    d. Wählen Sie **Automatisch neue Benutzer zum Team hinzufügen** aus, und klicken Sie auf **Änderungen speichern**.

    ![Änderungen speichern](./media/lucidchart-tutorial/ic791195.png "Änderungen speichern")

> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Eingebettete Azure AD-Dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Portals** auf das Symbol für **Azure Active Directory**.

    ![Erstellen eines Azure AD-Testbenutzers](./media/lucidchart-tutorial/create_aaduser_01.png) 

1. Wechseln Sie zu **Benutzer und Gruppen**, und klicken Sie auf **Alle Benutzer**, um die Liste der Benutzer anzuzeigen.
    
    ![Erstellen eines Azure AD-Testbenutzers](./media/lucidchart-tutorial/create_aaduser_02.png) 

1. Klicken Sie oben im Dialogfeld auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/lucidchart-tutorial/create_aaduser_03.png) 

1. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/lucidchart-tutorial/create_aaduser_04.png) 

    a. Geben Sie in das Textfeld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie in das Textfeld **Benutzername** die **E-Mail-Adresse** von Britta Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Create**.
 
### <a name="creating-a-lucidchart-test-user"></a>Erstellen eines Lucidchart-Testbenutzers

Für das Konfigurieren der Benutzerbereitstellung in Lucidchart steht kein Aktionselement zur Verfügung.  Wenn sich ein zugewiesener Benutzer über den Zugriffsbereich bei Lucidchart anmelden möchte, überprüft Lucidchart, ob der Benutzer vorhanden ist.  

Ist noch kein Benutzerkonto verfügbar, wird es von Lucidchart automatisch erstellt.

### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf Lucidchart gewähren.

![Benutzer zuweisen][200] 

**Um Britta Simon Lucidchart zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

1. Wählen Sie in der Anwendungsliste **Lucidchart** aus.

    ![Configure single sign-on](./media/lucidchart-tutorial/tutorial_lucidchart_app.png) 

1. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Benutzer zuweisen][202] 

1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Benutzer zuweisen][203]

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

1. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Lucidchart“ klicken, sollten Sie automatisch in Ihrer Lucidchart-Anwendung angemeldet werden.
Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/lucidchart-tutorial/tutorial_general_01.png
[2]: ./media/lucidchart-tutorial/tutorial_general_02.png
[3]: ./media/lucidchart-tutorial/tutorial_general_03.png
[4]: ./media/lucidchart-tutorial/tutorial_general_04.png

[100]: ./media/lucidchart-tutorial/tutorial_general_100.png

[200]: ./media/lucidchart-tutorial/tutorial_general_200.png
[201]: ./media/lucidchart-tutorial/tutorial_general_201.png
[202]: ./media/lucidchart-tutorial/tutorial_general_202.png
[203]: ./media/lucidchart-tutorial/tutorial_general_203.png

