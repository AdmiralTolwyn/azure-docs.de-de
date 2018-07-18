---
title: 'Tutorial: Azure Active Directory-Integration in Zscaler Two | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Zscaler Two konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1fd8a940-7320-47e0-a176-2dd4eeca6db2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 066581456b0f3dcbe4793c95cdb511e52413d1fb
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211569"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Tutorial: Azure Active Directory-Integration in Zscaler Two

In diesem Tutorial erfahren Sie, wie Sie Zscaler Two in Azure Active Directory (Azure AD) integrieren.

Die Integration von Zscaler Two in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf Zscaler Two hat.
- Sie können Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Zscaler Two (Single Sign-On, SSO; einmaliges Anmelden) anzumelden.
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Zscaler Two konfigurieren zu können, ist Folgendes erforderlich:

- Ein Azure AD-Abonnement
- Ein Zscaler Two-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie hier eine einmonatige Testversion anfordern: [Testversion](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von Zscaler Two aus dem Katalog
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-zscaler-two-from-the-gallery"></a>Hinzufügen von Zscaler Two aus dem Katalog
Zum Konfigurieren der Integration von Zscaler Two in Azure AD müssen Sie Zscaler Two aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Zscaler Two aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

2. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![ANWENDUNGEN][2]
    
3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![ANWENDUNGEN][3]

4. Geben Sie im Suchfeld den Namen **Zscaler Two** ein.

    ![Erstellen eines Azure AD-Testbenutzers](./media/zscaler-two-tutorial/tutorial_zscalertwo_search.png)

5. Wählen Sie im Ergebnisbereich die Option **Zscaler Two** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Erstellen eines Azure AD-Testbenutzers](./media/zscaler-two-tutorial/tutorial_zscalertwo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Zscaler Two mithilfe einer Testbenutzerin namens Britta Simon.

Damit das einmalige Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Zscaler Two als Pendant zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zscaler Two muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in Zscaler Two den Wert für **Benutzername** in Azure AD als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Zscaler Two müssen die folgenden Schritte ausgeführt werden:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Konfigurieren von Proxyeinstellungen](#configuring-proxy-settings)** zum Konfigurieren der Proxyeinstellungen in Internet Explorer
3. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit der Testbenutzerin Britta Simon zu testen.
4. **[Erstellen eines Zscaler Two-Testbenutzers](#creating-a-zscaler-two-test-user)**, um ein Pendant von Britta Simon in Zscaler Two zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
6. **[Testing Single Sign-On](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer Zscaler Two-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Zscaler Two die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Zscaler Two** auf **Einmaliges Anmelden**.

    ![Configure Single Sign-On][4]

2. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Configure Single Sign-On](./media/zscaler-two-tutorial/tutorial_zscalertwo_samlbase.png)

3. Führen Sie im Abschnitt **Domäne und URLs für Zscaler Two** die folgenden Schritte aus:

    ![Configure Single Sign-On](./media/zscaler-two-tutorial/tutorial_zscalertwo_url.png)

   Geben Sie im Textfeld „Anmelde-URL“ die von Ihren Benutzern für die Anmeldung bei Ihrer Zscaler Two-Anwendung verwendete URL ein.

    > [!NOTE] 
    > Sie müssen den Wert mit der richtigen Anmelde-URL aktualisieren. Wenden Sie sich an das [Supportteam für den Zscaler Two-Client](https://www.zscaler.com/company/contact), um diese Werte zu erhalten.

4. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Zertifikat (Base64)**, und speichern Sie die Zertifikatdatei auf Ihrem Computer.

    ![Configure Single Sign-On](./media/zscaler-two-tutorial/tutorial_zscalertwo_certificate.png) 

5. Klicken Sie auf die Schaltfläche **Save** .

    ![Configure Single Sign-On](./media/zscaler-two-tutorial/tutorial_general_400.png)

6. Klicken Sie im Abschnitt **Zscaler Two-Konfiguration** auf **Zscaler Two konfigurieren**, um das Fenster **Anmeldung konfigurieren** zu öffnen. Kopieren Sie die **URL für den SAML-SSO-Dienst** aus dem Abschnitt **Kurzübersicht**.

    ![Configure Single Sign-On](./media/zscaler-two-tutorial/tutorial_zscalertwo_configure.png) 

7. Melden Sie sich in einem anderen Webbrowserfenster bei der Zscaler Two-Unternehmenswebsite als Administrator an.

8. Klicken Sie oben im Menü auf **Verwaltung**.
   
    ![Verwaltung](./media/zscaler-two-tutorial/ic800206.png "Verwaltung")

9. Klicken Sie unter **Administratoren & Rollen verwalten** auf **Benutzer & Authentifizierung verwalten**.   
            
    ![Benutzer &amp; Authentifizierung verwalten](./media/zscaler-two-tutorial/ic800207.png "Benutzer &amp; Authentifizierung verwalten")

10. Führen Sie im Abschnitt **Auswählen von Authentifizierungsoptionen für Ihre Organisation** die folgenden Schritte aus:   
                
    ![Authentifizierung](./media/zscaler-two-tutorial/ic800208.png "Authentifizierung")
   
    a. Wählen Sie **Authentifizeren mit der einmaligen Anmeldung für SAML**.

    b. Klicken Sie auf **Einzelne Parameter der einmaligen Anmeldung für SAML konfigurieren**.

11. Führen Sie auf der Dialogfeldseite **Parameter der einmaligen Anmeldung für SAML konfigurieren** die folgenden Schritte aus, und klicken Sie dann auf **Fertig**.

    ![Einmaliges Anmelden](./media/zscaler-two-tutorial/ic800209.png "des einmaligen Anmeldens")
    
    a. Fügen Sie den Wert der **SAML-Dienst-URL für einmaliges Anmelden**, den Sie aus dem Azure-Portal kopiert haben, in das Textfeld **URL des SAML-Portals, zu dem Benutzer zur Authentifizierung geleitet werden** ein.
    
    b. Geben Sie im Textfeld **Attribut mit Anmeldenamen** die **NameID** ein.
    
    c. Klicken Sie auf **Zscaler pem**, um das heruntergeladene Zertifikat hochzuladen.
    
    d. Wählen Sie **Automatische SAML-Bereitstellung aktivieren**.

12. Führen Sie auf der Dialogseite **Benutzerauthentifizierung konfigurieren** die folgenden Schritte aus:

    ![Verwaltung](./media/zscaler-two-tutorial/ic800210.png "Verwaltung")
    
    a. Klicken Sie auf **Speichern**.

    b. Klicken Sie auf **Jetzt aktivieren**.

## <a name="configuring-proxy-settings"></a>Konfigurieren von Proxyeinstellungen
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>So konfigurieren Sie die Proxyeinstellungen in Internet Explorer:

1. Starten Sie **Internet Explorer**.

2. Wählen Sie im Menü **Extras** die Option **Internetoptionen**, um das Dialogfeld **Internetoptionen** zu öffnen.   
    
     ![Internetoptionen](./media/zscaler-two-tutorial/ic769492.png "Internetoptionen")

3. Klicken Sie auf die Registerkarte **Verbindungen** .   
  
     ![Verbindungen](./media/zscaler-two-tutorial/ic769493.png "Verbindungen")

4. Klicken Sie zum Öffnen des Dialogfelds **LAN-Einstellungen** auf **LAN-Einstellungen**.

5. Führen Sie im Abschnitt "Proxyserver" die folgenden Schritte aus:   
   
    ![Proxyserver](./media/zscaler-two-tutorial/ic769494.png "Proxyserver")

    a. Wählen Sie **Proxyserver für LAN verwenden** aus.

    b. Geben Sie in das Textfeld "Adresse" **gateway.zscalertwo.net**ein.

    c. Geben Sie im Textfeld „Port“ **80**ein.

    d. Wählen Sie **Proxyserver für lokale Adressen umgehen**.

    e. Klicken Sie zum Schließen des Dialogfelds **Einstellungen für lokales Netzwerk** auf **OK**.

6. Klicken Sie zum Schließen des Dialogfelds **Internetoptionen** auf **OK**.

> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Eingebettete Azure AD-Dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Portals** auf das Symbol für **Azure Active Directory**.

    ![Erstellen eines Azure AD-Testbenutzers](./media/zscaler-two-tutorial/create_aaduser_01.png) 

2. Wechseln Sie zu **Benutzer und Gruppen**, und klicken Sie auf **Alle Benutzer**, um die Liste der Benutzer anzuzeigen.
    
    ![Erstellen eines Azure AD-Testbenutzers](./media/zscaler-two-tutorial/create_aaduser_02.png) 

3. Klicken Sie oben im Dialogfeld auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/zscaler-two-tutorial/create_aaduser_03.png) 

4. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/zscaler-two-tutorial/create_aaduser_04.png) 

    a. Geben Sie in das Textfeld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie in das Textfeld **Benutzername** die **E-Mail-Adresse** von Britta Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Create**.
 
### <a name="creating-a-zscaler-two-test-user"></a>Erstellen eines Zscaler Two-Testbenutzers

Damit sich Azure AD-Benutzer bei ZScaler Two anmelden können, müssen sie in ZScaler Two bereitgestellt werden. Im Fall von ZScaler Two ist die Bereitstellung eine manuelle Aufgabe.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>So konfigurieren Sie die Benutzerbereitstellung

1. Melden Sie sich bei Ihrem **Zscaler Two**-Mandanten an.

2. Klicken Sie auf **Verwaltung**.   
   
    ![Verwaltung](./media/zscaler-two-tutorial/ic781035.png "Verwaltung")

3. Klicken Sie auf **Benutzerverwaltung**.   
        
     ![Hinzufügen](./media/zscaler-two-tutorial/ic781036.png "Hinzufügen")

4. Klicken Sie auf der Registerkarte **Benutzer** auf **Hinzufügen**.
      
    ![Hinzufügen](./media/zscaler-two-tutorial/ic781037.png "Hinzufügen")

5. Führen Sie im Abschnitt "Benutzer hinzufügen" die folgenden Schritte aus:
        
    ![Benutzer hinzufügen](./media/zscaler-two-tutorial/ic781038.png "Benutzer hinzufügen")
   
    a. Geben Sie die entsprechenden Werte in die Felder **Benutzer-ID**, **Benutzeranzeigename**, **Kennwort** und **Kennwort bestätigen** ein, und wählen Sie dann **Gruppen** und die **Abteilung** eines gültigen Azure AD-Kontos aus, das Sie bereitstellen möchten.

    b. Klicken Sie auf **Speichern**.

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von ZScaler Two-Benutzerkonten oder mithilfe der von ZScaler Two bereitgestellten APIs erstellen.

### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zscaler Two gewähren.

![Benutzer zuweisen][200] 

**Um Britta Simon Zscaler Two zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

2. Wählen Sie in der Liste der Anwendungen **Zscaler Two** aus.

    ![Configure Single Sign-On](./media/zscaler-two-tutorial/tutorial_zscalertwo_app.png) 

3. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Benutzer zuweisen][202] 

4. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Benutzer zuweisen][203]

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

6. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Zscaler Two“ klicken, sollten Sie automatisch bei Ihrer Zscaler Two-Anwendung angemeldet werden.
Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-two-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-two-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-two-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-two-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-two-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-two-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-two-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-two-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-two-tutorial/tutorial_general_203.png

