---
title: 'Tutorial: Azure Active Directory-Integration mit Collaborative Innovation | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Collaborative Innovation konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bba95df3-75a4-4a93-8805-b3a8aa3d4861
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: jeedes
ms.openlocfilehash: 897db5a4d794c22f436fa074ec2c359edc5b590c
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225057"
---
# <a name="tutorial-azure-active-directory-integration-with-collaborative-innovation"></a>Tutorial: Azure Active Directory-Integration mit Collaborative Innovation

In diesem Tutorial erfahren Sie, wie Sie Collaborative Innovation in Azure Active Directory (Azure AD) integrieren.

Die Integration von Collaborative Innovation in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf Collaborative Innovation hat.
- Sie können Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Collaborative Innovation anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Collaborative Innovation konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Collaborative Innovation-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/)eine einmonatige Testversion anfordern.

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von Collaborative Innovation aus dem Katalog
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-collaborative-innovation-from-the-gallery"></a>Hinzufügen von Collaborative Innovation aus dem Katalog
Zum Konfigurieren der Integration von Collaborative Innovation in Azure AD müssen Sie Collaborative Innovation aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um Collaborative Innovation aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

2. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![ANWENDUNGEN][2]
    
3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![ANWENDUNGEN][3]

4. Geben Sie in das Suchfeld **Collaborative Innovation** ein.

    ![Erstellen eines Azure AD-Testbenutzers](./media/collaborativeinnovation-tutorial/tutorial_collaborativeinnovation_search.png)

5. Wählen Sie im Ergebnisbereich die Option **Collaborative Innovation** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Erstellen eines Azure AD-Testbenutzers](./media/collaborativeinnovation-tutorial/tutorial_collaborativeinnovation_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Collaborative Innovation basierend auf einer Testbenutzerin mit dem Namen Britta Simon.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, wer das Pendant in Collaborative Innovation zu einem Benutzer in Azure AD ist. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Collaborative Innovation muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in Collaborative Innovation den Wert des **Benutzernamens** in Azure AD als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Collaborative Innovation müssen Sie die folgenden Schritte ausführen:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit der Testbenutzerin Britta Simon zu testen.
3. **[Erstellen eines Collaborative Innovation-Testbenutzers](#creating-a-collaborative-innovation-test-user)**, um ein Pendant von Britta Simon in Collaborative Innovation zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
4. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testing Single Sign-On](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden mit Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer Collaborative Innovation-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Collaborative Innovation die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Collaborative Innovation** auf **Einmaliges Anmelden**.

    ![Configure Single Sign-On][4]

2. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Configure Single Sign-On](./media/collaborativeinnovation-tutorial/tutorial_collaborativeinnovation_samlbase.png)

3. Führen Sie im Abschnitt **Domäne und URLs für Collaborative Innovation** die folgenden Schritte aus:

    ![Configure Single Sign-On](./media/collaborativeinnovation-tutorial/tutorial_collaborativeinnovation_url.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<instancename>.foundry.<companyname>.com/`.

    b. Geben Sie im Textfeld **Bezeichner** eine URL nach folgendem Muster ein: `https://<instancename>.foundry.<companyname>.com`
    
    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Clientsupportteam von Collaborative Innovation](https://www.unilever.com/contact/), um diese Werte zu erhalten.  

4. Die Collaborative Innovation-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie für diese Anwendung die folgenden Ansprüche. Sie können die Werte dieser Attribute über den Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:
    
    ![Configure Single Sign-On](./media/collaborativeinnovation-tutorial/attribute.png)
    
5. Aktivieren Sie im Abschnitt **Benutzerattribute** das Kontrollkästchen **Alle weiteren Benutzerattribute anzeigen und bearbeiten**, um die Attribute zu erweitern. Führen Sie die folgenden Schritte für jedes der angezeigten Attribute aus:

    | Attributname | Attributwert |
    | ---------------| --------------- |    
    | givenname | user.givenname |
    | surname | user.surname |
    | emailaddress | user.userprincipalname |
    | name | user.userprincipalname |

    a. Klicken Sie auf das Attribut, um das Fenster **Attribut bearbeiten** zu öffnen.

    ![Configure Single Sign-On](./media/collaborativeinnovation-tutorial/url_update.png)

    b. Löschen Sie den URL-Wert aus dem Feld **Namespace**.
    
    c. Klicken Sie auf **OK**, um die Einstellung zu speichern.

6. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Metadaten-XML**, und speichern Sie die Metadatendatei dann auf Ihrem Computer.

    ![Configure Single Sign-On](./media/collaborativeinnovation-tutorial/tutorial_collaborativeinnovation_certificate.png) 

7. Klicken Sie auf die Schaltfläche **Save** .

    ![Configure Single Sign-On](./media/collaborativeinnovation-tutorial/tutorial_general_400.png)

8. Zum Konfigurieren des einmaligen Anmeldens bei **Collaborative Innovation** müssen Sie die heruntergeladene **Metadaten-XML**-Datei an das [Collaborative Innovation-Supportteam](https://www.unilever.com/contact/) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Eingebettete Azure AD-Dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Portals** auf das Symbol für **Azure Active Directory**.

    ![Erstellen eines Azure AD-Testbenutzers](./media/collaborativeinnovation-tutorial/create_aaduser_01.png) 

2. Wechseln Sie zu **Benutzer und Gruppen**, und klicken Sie auf **Alle Benutzer**, um die Liste der Benutzer anzuzeigen.
    
    ![Erstellen eines Azure AD-Testbenutzers](./media/collaborativeinnovation-tutorial/create_aaduser_02.png) 

3. Klicken Sie oben im Dialogfeld auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/collaborativeinnovation-tutorial/create_aaduser_03.png) 

4. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/collaborativeinnovation-tutorial/create_aaduser_04.png) 

    a. Geben Sie in das Textfeld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie in das Textfeld **Benutzername** die **E-Mail-Adresse** von Britta Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Create**.
 
### <a name="creating-a-collaborative-innovation-test-user"></a>Erstellen eines Collaborative Innovation-Testbenutzers

Um Azure AD-Benutzern die Anmeldung bei Collaborative Innovation zu ermöglichen, müssen sie in Collaborative Innovation bereitgestellt werden.  

In diesem Fall erfolgt die Anwendungsbereitstellung automatisch, da die Anwendung Just-in-Time-Benutzerbereitstellung unterstützt. Hier müssen also keine Schritte ausgeführt werden.

### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Collaborative Innovation gewähren.

![Benutzer zuweisen][200] 

**Um Britta Simon Collaborative Innovation zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

2. Wählen Sie in der Liste der Anwendungen **Collaborative Innovation**.

    ![Configure Single Sign-On](./media/collaborativeinnovation-tutorial/tutorial_collaborativeinnovation_app.png) 

3. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Benutzer zuweisen][202] 

4. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Benutzer zuweisen][203]

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

6. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Collaborative Innovation“ klicken, sollte die Anmeldeseite Ihrer Collaborative Innovation-Anwendung angezeigt werden.
Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/collaborativeinnovation-tutorial/tutorial_general_01.png
[2]: ./media/collaborativeinnovation-tutorial/tutorial_general_02.png
[3]: ./media/collaborativeinnovation-tutorial/tutorial_general_03.png
[4]: ./media/collaborativeinnovation-tutorial/tutorial_general_04.png

[100]: ./media/collaborativeinnovation-tutorial/tutorial_general_100.png

[200]: ./media/collaborativeinnovation-tutorial/tutorial_general_200.png
[201]: ./media/collaborativeinnovation-tutorial/tutorial_general_201.png
[202]: ./media/collaborativeinnovation-tutorial/tutorial_general_202.png
[203]: ./media/collaborativeinnovation-tutorial/tutorial_general_203.png

