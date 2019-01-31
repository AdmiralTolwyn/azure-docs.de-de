---
title: 'Tutorial: Azure Active Directory-Integration mit CloudPassage | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und CloudPassage konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: d8bddc309db3ede67586a2067fcf57fbede7d6b3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152789"
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Tutorial: Azure Active Directory-Integration mit CloudPassage

In diesem Tutorial erfahren Sie, wie Sie CloudPassage in Azure Active Directory (Azure AD) integrieren.

Diese Integration bietet folgende Vorteile:

- Sie können in Azure AD steuern, wer auf CloudPassage Zugriff hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei CloudPassage anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit CloudPassage konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein CloudPassage-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/)eine einmonatige Testversion anfordern.

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von CloudPassage aus dem Katalog
1. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-cloudpassage-from-the-gallery"></a>Hinzufügen von CloudPassage aus dem Katalog
Zum Konfigurieren der Integration von CloudPassage in Azure AD müssen Sie CloudPassage aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um CloudPassage aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

1. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![ANWENDUNGEN][2]
    
1. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![ANWENDUNGEN][3]

1. Geben Sie im Suchfeld die Zeichenfolge **CloudPassage**ein.

    ![Erstellen eines Azure AD-Testbenutzers](./media/cloudpassage-tutorial/tutorial_cloudpassage_search.png)

1. Wählen Sie im Ergebnisbereich die Option **CloudPassage** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Erstellen eines Azure AD-Testbenutzers](./media/cloudpassage-tutorial/tutorial_cloudpassage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei CloudPassage basierend auf einer Testbenutzerin mit dem Namen Britta Simon.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, wer das Pendant in CloudPassage zu einem Benutzer in Azure AD ist. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in CloudPassage muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in CloudPassage den Wert des **Benutzernamens** in Azure AD als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei CloudPassage müssen Sie die folgenden Bausteine ausführen:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
1. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit der Testbenutzerin Britta Simon zu testen.
1. **[Erstellen eines CloudPassage-Testbenutzers](#creating-a-cloudpassage-test-user)**, um ein Pendant von Britta Simon in CloudPassage zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Testing Single Sign-On](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer CloudPassage-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in CloudPassage die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **CloudPassage** auf **Einmaliges Anmelden**.

    ![Configure single sign-on][4]

1. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Configure single sign-on](./media/cloudpassage-tutorial/tutorial_cloudpassage_samlbase.png)

1. Führen Sie im Abschnitt **Domäne und URLs für CloudPassage** die folgenden Schritte aus:

    ![Configure single sign-on](./media/cloudpassage-tutorial/tutorial_cloudpassage_url.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://portal.cloudpassage.com/saml/init/accountid`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: `https://portal.cloudpassage.com/saml/consume/accountid`. Sie können Ihren Wert für dieses Attribut abrufen, indem Sie auf die **Dokumentation der SSO-Einrichtung** im Abschnitt mit den **Einstellungen für einmaliges Anmelden** Ihres CloudPassage-Portals klicken.

    ![Configure single sign-on](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)
     
    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit der tatsächlichen Antwort-URL und Anmelde-URL. Wenden Sie sich an das [Clientsupportteam von CloudPassage](https://www.cloudpassage.com/company/contact/), um diese Werte zu erhalten. 

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Zertifikat (Base64)**, und speichern Sie die Zertifikatdatei auf Ihrem Computer.

    ![Configure single sign-on](./media/cloudpassage-tutorial/tutorial_cloudpassage_certificate.png) 

1. Die CloudPassage-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:
   
   ![Configure single sign-on](./media/cloudpassage-tutorial/tutorial_cloudpassage_25.png) 

1. Konfigurieren Sie das SAML-Tokenattribut im Dialogfeld **Einmaliges Anmelden** im Abschnitt **Benutzerattribute**, wie im obigen Bild gezeigt, und führen Sie die folgenden Schritte aus:

    | Attributname | Attributwert |
    | --- | --- |
    | firstname |user.givenname |
    | lastname |user.surname |
    | E-Mail |user.mail |
    
    a. Klicken Sie auf **Attribut hinzufügen**, um das Dialogfeld **Benutzerattribut hinzufügen** zu öffnen.

    ![Configure single sign-on](./media/cloudpassage-tutorial/tutorial_attribute_04.png)
    
    ![Configure single sign-on](./media/cloudpassage-tutorial/tutorial_attribute_05.png)
    
    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Geben Sie in der Liste **Wert** den für diese Zeile angezeigten Wert ein.
    
    d. Klicken Sie auf **OK**.

1. Klicken Sie auf die Schaltfläche **Save** .

    ![Configure single sign-on](./media/cloudpassage-tutorial/tutorial_general_400.png)
    
1. Klicken Sie im Abschnitt **CloudPassage-Konfiguration** auf **CloudPassage konfigurieren**, um das Fenster **Anmeldung konfigurieren** zu öffnen. Kopieren Sie die **Abmelde-URL, die SAML-Entitäts-ID und die URL für den SAML-SSO-Dienst** aus dem Abschnitt **Kurzübersicht**.

    ![Configure single sign-on](./media/cloudpassage-tutorial/tutorial_cloudpassage_configure.png) 

1. Melden Sie sich in einem anderen Browserfenster bei der CloudPassage-Unternehmenswebsite als Administrator an.

1. Klicken Sie im Menü am oberen Rand auf **Einstellungen** und dann auf **Websiteverwaltung**. 
   
    ![Configure single sign-on][12]

1. Klicken Sie auf die Registerkarte **Authentifizierungseinstellungen** . 
   
    ![Configure single sign-on][13]

1. Führen Sie im Abschnitt **Einstellungen für einmaliges Anmelden** die folgenden Schritte aus: 
   
    ![Configure single sign-on][14]

    a. Aktivieren Sie das Kontrollkästchen **Einmaliges Anmelden aktivieren (SSO) (SSO-Setup-Dokumentation)**.
    
    b. Fügen Sie **SAML-Entitäts-ID** in das Textfeld **SAML-Aussteller-URL** ein.
  
    c. Fügen Sie die **SAML-Dienst-URL für einmalige Anmeldung** in das Textfeld **SAML-Endpunkt-URL** ein.
  
    d. Fügen Sie die **Abmelde-URL** in das Textfeld **Abmelde-Landing Page** ein.
  
    e. Öffnen Sie das heruntergeladene Zertifikat im Editor, kopieren Sie den Inhalt des heruntergeladenen Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **X.509-Zertifikat** ein.
  
    f. Klicken Sie auf **Speichern**.

> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Dokumentation zu eingebettetem Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Portals** auf das Symbol für **Azure Active Directory**.

    ![Erstellen eines Azure AD-Testbenutzers](./media/cloudpassage-tutorial/create_aaduser_01.png) 

1. Wechseln Sie zu **Benutzer und Gruppen**, und klicken Sie auf **Alle Benutzer**, um die Liste der Benutzer anzuzeigen.
    
    ![Erstellen eines Azure AD-Testbenutzers](./media/cloudpassage-tutorial/create_aaduser_02.png) 

1. Klicken Sie oben im Dialogfeld auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/cloudpassage-tutorial/create_aaduser_03.png) 

1. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/cloudpassage-tutorial/create_aaduser_04.png) 

    a. Geben Sie in das Textfeld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie in das Textfeld **Benutzername** die **E-Mail-Adresse** von Britta Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Create**.
 
### <a name="creating-a-cloudpassage-test-user"></a>Erstellen eines CloudPassage-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in CloudPassage.

**Um einen Benutzer namens Britta Simon in CloudPassage zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei der **CloudPassage** -Unternehmenswebsite als Administrator an. 

1. Klicken Sie auf der Symbolleiste am oberen Rand auf **Einstellungen** und dann auf **Websiteverwaltung**. 
   
   ![Erstellen eines CloudPassage-Testbenutzers][22] 

1. Klicken Sie auf die Registerkarte **Benutzer** und anschließend auf **Neuen Benutzer hinzufügen**. 
   
   ![Erstellen eines CloudPassage-Testbenutzers][23]

1. Führen Sie im Abschnitt **Neuen Benutzer hinzufügen** die folgenden Schritte aus: 
   
   ![Erstellen eines CloudPassage-Testbenutzers][24]
    
    a. Geben Sie in das Textfeld **Vorname** den Namen Britta ein. 
  
    b. Geben Sie in das Textfeld **Nachname** den Namen Simon ein.
  
    c. Geben Sie in die Textfelder **Benutzername**, **E-Mail** und **E-Mail erneut eingeben** Brittas Benutzernamen in Azure AD ein.
  
    d. Wählen Sie als **Zugriffstyp** die Option **Halo-Portalzugriff aktivieren** aus.
  
    e. Klicken Sie auf **Hinzufügen**.

### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf CloudPassage gewähren.

![Benutzer zuweisen][200] 

**Um Britta Simon CloudPassage zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

1. Wählen Sie in der Anwendungsliste die Option **CloudPassage**aus.

    ![Configure single sign-on](./media/cloudpassage-tutorial/tutorial_cloudpassage_app.png) 

1. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Benutzer zuweisen][202] 

1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Benutzer zuweisen][203]

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

1. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden (SSO) über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel "CloudPassage" klicken, sollten Sie automatisch in Ihrer CloudPassage-Anwendung angemeldet werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/cloudpassage-tutorial/tutorial_general_04.png
[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

[100]: ./media/cloudpassage-tutorial/tutorial_general_100.png

[200]: ./media/cloudpassage-tutorial/tutorial_general_200.png
[201]: ./media/cloudpassage-tutorial/tutorial_general_201.png
[202]: ./media/cloudpassage-tutorial/tutorial_general_202.png
[203]: ./media/cloudpassage-tutorial/tutorial_general_203.png

