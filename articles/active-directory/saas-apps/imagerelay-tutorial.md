---
title: 'Tutorial: Azure Active Directory-Integration mit Image Relay | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Image Relay konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 0671d2f5ad4be34926c8e3d2b22711c4af5fd435
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812096"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Tutorial: Azure Active Directory-Integration mit Image Relay

In diesem Tutorial erfahren Sie, wie Sie Image Relay in Azure Active Directory (Azure AD) integrieren.

Diese Integration von Image Relay bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer auf Image Relay Zugriff hat.
- Sie können Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Image Relay anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration von Image Relay konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Image Relay-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/)eine einmonatige Testversion anfordern.

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von Image Relay aus dem Katalog
1. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-image-relay-from-the-gallery"></a>Hinzufügen von Image Relay aus dem Katalog
Zum Konfigurieren der Integration von Image Relay in Azure AD müssen Sie Image Relay aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Image Relay aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

1. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![ANWENDUNGEN][2]
    
1. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![ANWENDUNGEN][3]

1. Geben Sie im Suchfeld den Suchbegriff **Image Relay** ein.

    ![Erstellen eines Azure AD-Testbenutzers](./media/imagerelay-tutorial/tutorial_imagerelay_search.png)

1. Wählen Sie im Ergebnisbereich die Option **Image Relay** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Erstellen eines Azure AD-Testbenutzers](./media/imagerelay-tutorial/tutorial_imagerelay_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden für Azure AD mit Image Relay basierend auf einer Testbenutzerin mit dem Namen „Britta Simon“.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Image Relay als Pendant zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Image Relay muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in Image Relay den Wert für **Benutzername** in Azure AD als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Zum Konfigurieren und Testen des einmaligen Anmeldens mit Azure AD bei Image Relay müssen Sie die folgenden Bausteine ausführen:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
1. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit der Testbenutzerin Britta Simon zu testen.
1. **[Erstellen eines Image Relay-Testbenutzers](#creating-an-image-relay-test-user)**, um ein Pendant von Britta Simon in Image Relay zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Testing Single Sign-On](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer Image Relay-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Image Relay die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Image Relay** auf **Einmaliges Anmelden**.

    ![Configure single sign-on][4]

1. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_samlbase.png)

1. Führen Sie im Abschnitt **Domäne und URLs für Image Relay** die folgenden Schritte aus:

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_url.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<companyname>.imagerelay.com/`.

    b. Geben Sie im Textfeld **Bezeichner** eine URL nach folgendem Muster ein: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Clientsupportteam von Image Relay](http://support.imagerelay.com/), um diese Werte zu erhalten. 
 


1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Zertifikat (Base64)**, und speichern Sie die Zertifikatdatei auf Ihrem Computer.

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_certificate.png) 

1. Klicken Sie auf die Schaltfläche **Save** .

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_general_400.png)

1. Klicken Sie im Abschnitt **Image Relay-Konfiguration** auf **Image Relay konfigurieren**, um das Fenster **Anmeldung konfigurieren** zu öffnen. Kopieren Sie die **Dienst-URL für einmalige Abmeldung und die SAML-Dienst-URL für einmalige Anmeldung** aus dem Abschnitt **Kurzübersicht**.

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_configure.png) 

1. Melden Sie sich in einem anderen Webbrowserfenster bei Ihrer Image Relay-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf der Symbolleiste oben auf die Workload **Users & Permissions** (Benutzer und Berechtigungen).
   
    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

1. Klicken Sie auf **Create New Permission**(Neue Berechtigung erstellen).
   
    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

1. Aktivieren Sie in der Workload **Single Sign On Settings** (Einstellungen für einmaliges Anmelden) das Kontrollkästchen **This Group can only sign-in via Single Sign On** (Diese Gruppe kann sich nur per einmaligem Anmelden anmelden), und klicken Sie dann auf **Speichern**.
   
    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

1. Wechseln Sie zu **Kontoeinstellungen**.
   
    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

1. Klicken Sie auf die Workload **Single Sign On Settings** (Einstellungen für einmaliges Anmelden).
    
     ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

1. Führen Sie im Dialogfeld **SAML Settings** (SAML-Einstellungen) die folgenden Schritte aus:
    
    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. Fügen Sie in das Textfeld **Anmelde-URL** den Wert der **Dienst-URL für einmalige Anmeldung** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Fügen Sie in das Textfeld **Abmelde-URL** den Wert der **Dienst-URL für einmalige Abmeldung** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Wählen Sie unter **Name Id Format** (Format der Namens-ID) die Option **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress** aus.

    d. Wählen Sie unter **Binding Options for Requests from the Service Provider (Image Relay)** (Bindungsoptionen für Anforderungen vom Dienstanbieter (Image Relay)) die Option **POST Binding** (POST-Bindung) aus.

    e. Klicken Sie unter **x.509-Zertifikat** auf **Zertifikat aktualisieren**.

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Öffnen Sie das heruntergeladene Zertifikat im Editor, kopieren Sie den Inhalt, und fügen Sie ihn anschließend in das Textfeld „x.509-Zertifikat“ ein.

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. Aktivieren Sie im Abschnitt **Just-In-Time User Provisioning** (Just-In-Time-Benutzerbereitstellung) das Kontrollkästchen **Enable Just-In-Time User Provisioning** (Just-In-Time-Benutzerbereitstellung aktivieren).

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Wählen Sie die Berechtigungsgruppe aus (z.B. **SSO Basic** (SSO allgemein)), bei der die Anmeldung nur per einmaligem Anmelden erfolgen soll.

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Klicken Sie auf **Speichern**.

> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Dokumentation zu eingebettetem Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Portals** auf das Symbol für **Azure Active Directory**.

    ![Erstellen eines Azure AD-Testbenutzers](./media/imagerelay-tutorial/create_aaduser_01.png) 

1. Wechseln Sie zu **Benutzer und Gruppen**, und klicken Sie auf **Alle Benutzer**, um die Liste der Benutzer anzuzeigen.
    
    ![Erstellen eines Azure AD-Testbenutzers](./media/imagerelay-tutorial/create_aaduser_02.png) 

1. Klicken Sie oben im Dialogfeld auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/imagerelay-tutorial/create_aaduser_03.png) 

1. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/imagerelay-tutorial/create_aaduser_04.png) 

    a. Geben Sie in das Textfeld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie in das Textfeld **Benutzername** die **E-Mail-Adresse** von Britta Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Create**.
 
### <a name="creating-an-image-relay-test-user"></a>Erstellen eines Image Relay-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen einer Benutzerin namens Britta Simon in Image Relay.

**Um eine Benutzerin namens Britta Simon in Image Relay zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrer Image Relay-Unternehmenswebsite als Administrator an.

1. Wechseln Sie zu **Users & Permissions** (Benutzer und Berechtigungen), und klicken Sie auf **Create SSO User** (SSO-Benutzer erstellen).
   
    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

1. Geben Sie die **E-Mail-Adresse**, den **Vornamen**, den **Nachnamen** und das **Unternehmen** des bereitzustellenden Benutzers an. Wählen Sie außerdem die Berechtigungsgruppe aus (z.B. „SSO Basic“). Dies ist die Gruppe, die sich nur per einmaligem Anmelden anmelden kann.
   
    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_22.png) 

1. Klicken Sie auf **Create**.

### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Image Relay gewähren.

![Benutzer zuweisen][200] 

**Um Britta Simon Image Relay zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

1. Wählen Sie in der Anwendungsliste **Image Relay** aus.

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_app.png) 

1. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Benutzer zuweisen][202] 

1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Benutzer zuweisen][203]

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

1. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

In diesem Abschnitt soll Ihre Azure AD-Konfiguration für das einmalige Anmelden mithilfe des Zugriffsbereichs getestet werden.    

Wenn Sie im Zugriffsbereich auf die Kachel „Image Relay“ klicken, sollten Sie automatisch bei Ihrer Image Relay-Anwendung angemeldet werden.


## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/imagerelay-tutorial/tutorial_general_04.png


[100]: ./media/imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/imagerelay-tutorial/tutorial_general_201.png
[202]: ./media/imagerelay-tutorial/tutorial_general_202.png
[203]: ./media/imagerelay-tutorial/tutorial_general_203.png

