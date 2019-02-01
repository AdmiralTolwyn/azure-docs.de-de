---
title: 'Tutorial: Azure Active Directory-Integration mit UserVoice | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und UserVoice konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: a2b8faaf3ee7d8f5dfca35f860dbd003f9e6072b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194337"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Tutorial: Azure Active Directory-Integration mit UserVoice

In diesem Tutorial erfahren Sie, wie Sie UserVoice in Azure Active Directory (Azure AD) integrieren.

Die Integration von UserVoice in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf UserVoice hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei UserVoice anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit UserVoice konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein UserVoice-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie eine [einmonatige Testversion anfordern](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von UserVoice aus dem Katalog
1. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-uservoice-from-the-gallery"></a>Hinzufügen von UserVoice aus dem Katalog
Zum Konfigurieren der Integration von UserVoice in Azure AD müssen Sie UserVoice aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um UserVoice aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Schaltfläche „Azure Active Directory“][1]

1. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“][2]
    
1. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“][3]

1. Geben Sie im Suchfeld **UserVoice** ein, wählen Sie im Ergebnisbereich **UserVoice** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![UserVoice in der Ergebnisliste](./media/uservoice-tutorial/tutorial_uservoice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei UserVoice basierend auf einem Testbenutzer mit dem Namen Britta Simon.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in UserVoice als Gegenstück zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in UserVoice muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in UserVoice den Wert für **Benutzername** in Azure AD als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei UserVoice müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
1. **[Erstellen eines UserVoice-Testbenutzers](#create-a-uservoice-test-user)**, um eine Entsprechung von Britta Simon in UserVoice zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer UserVoice-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei UserVoice die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **UserVoice** auf **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden][4]

1. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Dialogfeld „Einmaliges Anmelden“](./media/uservoice-tutorial/tutorial_uservoice_samlbase.png)

1. Führen Sie auf der Seite **Domäne und URLs für UserVoice** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für UserVoice](./media/uservoice-tutorial/tutorial_uservoice_url.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<tenantname>.UserVoice.com`.

    b. Geben Sie im Textfeld **Bezeichner** eine URL nach folgendem Muster ein: `https://<tenantname>.UserVoice.com`

    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Clientsupportteam von UserVoice](https://www.uservoice.com/), um diese Werte zu erhalten.

1. Kopieren Sie im Abschnitt **SAML-Signaturzertifikat** den Wert **FINGERABDRUCK** des Zertifikats.

    ![Downloadlink für das Zertifikat](./media/uservoice-tutorial/tutorial_uservoice_certificate.png) 

1. Klicken Sie auf die Schaltfläche **Save** .

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/uservoice-tutorial/tutorial_general_400.png)

1. Klicken Sie im Abschnitt **UserVoice-Konfiguration** auf **UserVoice konfigurieren**, um das Fenster **Anmeldung konfigurieren** zu öffnen. Kopieren Sie die **Abmelde-URL und die URL für den SAML-SSO-Dienst** aus dem Abschnitt **Kurzübersicht**.

    ![UserVoice-Konfiguration](./media/uservoice-tutorial/tutorial_uservoice_configure.png) 

1. Melden Sie sich in einem anderen Webbrowserfenster bei der UserVoice-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf der Symbolleiste oben auf **Settings** (Einstellungen), und wählen Sie dann aus dem Menü die Option **Web portal** aus.
   
    ![Abschnitt „Settings“ auf App-Seite](./media/uservoice-tutorial/ic777519.png "Settings")

1. Klicken Sie auf der Registerkarte **Web portal** im Abschnitt **User authentication** (Benutzerauthentifizierung) auf **Edit** (Bearbeiten), um die Dialogfeldseite **Edit User Authentication** (Benutzerauthentifizierung bearbeiten) zu öffnen.
   
    ![Registerkarte „Web portal“](./media/uservoice-tutorial/ic777520.png "Web portal")

1. Führen Sie auf der Dialogseite **Benutzerauthentifizierung bearbeiten** die folgenden Schritte aus:
   
    ![Benutzerauthentifizierung bearbeiten](./media/uservoice-tutorial/ic777521.png "Benutzerauthentifizierung bearbeiten")
   
    a. Klicken Sie auf **Einmaliges Anmelden (SSO)**.
 
    b. Fügen Sie den Wert der **SAML-Dienst-URL für einmaliges Anmelden**, den Sie aus dem Azure-Portal kopiert haben, in das Textfeld **SSO Remote Sign-In** (SSO-Remoteanmeldung) ein.

    c. Fügen Sie den Wert der **Abmelde-URL**, den Sie aus dem Azure-Portal kopiert haben, in das Textfeld **SSO Remote Sign-Out** (SSO-Remoteabmeldung) ein.
 
    d. Fügen Sie in das Textfeld  **Current certificate SHA1 fingerprint (SHA1-Fingerabdruck des aktuellen Zertifikats)** den Wert für **Fingerabdruck** aus dem Azure-Portal ein.
    
    e. Klicken Sie auf **Authentifizierungseinstellungen speichern**.

> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Dokumentation zu eingebettetem Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

   ![Erstellen eines Azure AD-Testbenutzers][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Bereich des Azure-Portals auf die Schaltfläche **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](./media/uservoice-tutorial/create_aaduser_01.png)

1. Navigieren Sie zu **Benutzer und Gruppen**, und klicken Sie dann auf **Alle Benutzer**, um die Liste mit den Benutzern anzuzeigen.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](./media/uservoice-tutorial/create_aaduser_02.png)

1. Klicken Sie oben im Dialogfeld **Alle Benutzer** auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.

    ![Schaltfläche „Hinzufügen“](./media/uservoice-tutorial/create_aaduser_03.png)

1. Führen Sie im Dialogfeld **Neuer Benutzer** die folgenden Schritte aus:

    ![Dialogfeld „Benutzer“](./media/uservoice-tutorial/create_aaduser_04.png)

    a. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie im Feld **Benutzername** die E-Mail-Adresse des Benutzers Britta Simon ein.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    d. Klicken Sie auf **Create**.
 
### <a name="create-a-uservoice-test-user"></a>Erstellen eines UserVoice-Testbenutzers

Damit sich Azure AD-Benutzer bei UserVoice anmelden können, müssen sie in UserVoice bereitgestellt werden. Im Fall von UserVoice ist die Bereitstellung eine manuelle Aufgabe.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:
1. Melden Sie sich bei Ihrem **UserVoice** -Mandanten an.

1. Wechseln Sie zu **Einstellungen**.
   
    ![Einstellungen](./media/uservoice-tutorial/ic777811.png "Einstellungen")

1. Klicken Sie auf **Allgemein**.

1. Klicken Sie auf **Agents und Berechtigungen**.
   
    ![Agents und Berechtigungen](./media/uservoice-tutorial/ic777812.png "Agents und Berechtigungen")

1. Klicken Sie auf **Administratoren hinzufügen**.
   
    ![Administratoren hinzufügen](./media/uservoice-tutorial/ic777813.png "Administratoren hinzufügen")

1. Führen Sie im Dialogfeld **Benutzer einladen** die folgenden Schritte aus:
   
    ![Administratoren einladen](./media/uservoice-tutorial/ic777814.png "Administratoren einladen")
   
    a. Geben Sie in das Textfeld „E-Mails“ die E-Mail-Adresse des Azure AD-Kontos ein, das Sie bereitstellen möchten, und klicken Sie auf **Hinzufügen**.
   
    b. Klicken Sie auf **Einladen**.

> [!NOTE]
> Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von UserVoice-Benutzerkonten oder mithilfe der von UserVoice bereitgestellten APIs erstellen.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf UserVoice gewähren.

![Zuweisen der Benutzerrolle][200] 

**Um Britta Simon UserVoice zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

1. Wählen Sie in der Anwendungsliste **UserVoice** aus.

    ![UserVoice-Link in der Anwendungsliste](./media/uservoice-tutorial/tutorial_uservoice_app.png)  

1. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Link „Benutzer und Gruppen“][202]

1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“][203]

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

1. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „UserVoice“ klicken, sollten Sie automatisch bei Ihrer UserVoice-Anwendung angemeldet.
Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/uservoice-tutorial/tutorial_general_01.png
[2]: ./media/uservoice-tutorial/tutorial_general_02.png
[3]: ./media/uservoice-tutorial/tutorial_general_03.png
[4]: ./media/uservoice-tutorial/tutorial_general_04.png

[100]: ./media/uservoice-tutorial/tutorial_general_100.png

[200]: ./media/uservoice-tutorial/tutorial_general_200.png
[201]: ./media/uservoice-tutorial/tutorial_general_201.png
[202]: ./media/uservoice-tutorial/tutorial_general_202.png
[203]: ./media/uservoice-tutorial/tutorial_general_203.png

