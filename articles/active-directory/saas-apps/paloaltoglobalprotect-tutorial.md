---
title: 'Tutorial: Azure Active Directory-Integration mit Palo Alto Networks - GlobalProtect | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Palo Alto Networks - GlobalProtect konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 03bef6f2-3ea2-4eaa-a828-79c5f1346ce5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: e840a8cbcbf03bf6a658047686f4d4a29994af09
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36219933"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---globalprotect"></a>Tutorial: Azure Active Directory-Integration mit Palo Alto Networks - GlobalProtect

In diesem Tutorial erfahren Sie, wie Sie Palo Alto Networks - GlobalProtect in Azure Active Directory (Azure AD) integrieren.

Die Integration von Palo Alto Networks - GlobalProtect in Azure AD bietet Ihnen folgende Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf Palo Alto Networks - GlobalProtect hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Palo Alto Networks - GlobalProtect anzumelden (Single Sign-On, SSO; einmaliges Anmelden).
- Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Palo Alto Networks - GlobalProtect konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Abonnement für Palo Alto Networks - GlobalProtect, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie eine [einmonatige Testversion anfordern](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von Palo Alto Networks - GlobalProtect aus dem Katalog
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Hinzufügen von Palo Alto Networks - GlobalProtect aus dem Katalog
Zum Konfigurieren der Integration von Palo Alto Networks - GlobalProtect in Azure AD müssen Sie Palo Alto Networks - GlobalProtect aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie zum Hinzufügen von Palo Alto Networks - GlobalProtect aus dem Katalog die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Schaltfläche „Azure Active Directory“][1]

2. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“][2]
    
3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“][3]

4. Geben Sie im Suchfeld **Palo Alto Networks - GlobalProtect** ein, wählen Sie im Ergebnisbereich **Palo Alto Networks - GlobalProtect** aus, und klicken Sie auf **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Palo Alto Networks - GlobalProtect in der Ergebnisliste](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Palo Alto Networks - GlobalProtect mithilfe einer Testbenutzerin namens Britta Simon.

Damit das einmalige Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Palo Alto Networks - GlobalProtect als Gegenstück zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Palo Alto Networks - GlobalProtect muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in Palo Alto Networks - GlobalProtect den Wert für **Benutzername** in Azure AD als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Palo Alto Networks - GlobalProtect müssen die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
3. **[Erstellen eines Palo Alto Networks - GlobalProtect-Testbenutzers](#create-a-palo-alto-networks---globalprotect-test-user)**, um ein Pendant von Britta Simon in Palo Alto Networks - GlobalProtect zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden bei Ihrer Palo Alto Networks - GlobalProtect-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Palo Alto Networks - GlobalProtect die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Palo Alto Networks - GlobalProtect** auf **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden][4]

2. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Dialogfeld „Einmaliges Anmelden“](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_samlbase.png)

3. Führen Sie im Abschnitt **Domäne und URLs für Palo Alto Networks - GlobalProtect** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Palo Alto Networks - GlobalProtect](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_url.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<Customer Firewall URL>`.

    b. Geben Sie im Textfeld **Bezeichner** eine URL nach folgendem Muster ein: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Supportteam für den Palo Alto Networks - GlobalProtect-Client](https://support.paloaltonetworks.com/support), um diese Werte zu erhalten. 
 
4. Die Palo Alto Networks - GlobalProtect-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie für diese Anwendung die folgenden Ansprüche. Sie können die Werte dieser Attribute über den Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:
    
    ![Configure Single Sign-On](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_attribute.png)
    
5. Konfigurieren Sie das SAML-Tokenattribut im Dialogfeld **Einmaliges Anmelden** im Abschnitt **Benutzerattribute**, wie im obigen Bild gezeigt, und führen Sie die folgenden Schritte aus:
    
    | Attributname | Attributwert |
    | --- | --- |    
    | username | user.userprincipalname |

    a. Klicken Sie auf **Attribut hinzufügen**, um das Dialogfeld **Benutzerattribut hinzufügen** zu öffnen.

    ![Configure Single Sign-On](./media/paloaltoglobalprotect-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/paloaltoglobalprotect-tutorial/tutorial_attribute_05.png)
    
    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.
    
    c. Geben Sie in der Liste **Wert** den für diese Zeile angezeigten Wert ein. Wir haben den Wert als Beispiel „user.userprincipalname“ zugeordnet, Sie können jedoch Ihren entsprechenden Wert verwenden. 
    
    d. Klicken Sie auf **OK**.


6. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Metadaten-XML**, und speichern Sie die Metadatendatei dann auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_certificate.png) 

7. Klicken Sie auf die Schaltfläche **Save** .

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/paloaltoglobalprotect-tutorial/tutorial_general_400.png)

8. Öffnen Sie als Administrator die Administratoroberfläche für die Palo Alto Networks-Firewall in einem anderen Browserfenster.

9. Klicken Sie auf **Device** (Gerät).

    ![Konfigurieren des einmaligen Anmeldens für Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

10. Klicken Sie in der linken Navigationsleiste auf **SAML-Identitätsanbieter** und anschließend auf „Importieren“, um die Metadatendatei zu importieren.

    ![Konfigurieren des einmaligen Anmeldens für Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

11. Führen Sie im Fenster zum Importieren die folgenden Aktionen aus:

    ![Konfigurieren des einmaligen Anmeldens für Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Geben Sie im Textfeld **Profile Name** (Profilname) einen Namen ein, etwa „Azure AD Global Protect“.
    
    b. Klicken Sie unter **Identity Provider Metadata** (Metadaten des Identitätsanbieters) auf **Browse** (Durchsuchen), und wählen Sie die Datei „metadata.xml“ aus, die Sie aus dem Azure-Portal heruntergeladen haben.
    
    c. Klicken Sie auf **OK**

> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Eingebettete Azure AD-Dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

   ![Erstellen eines Azure AD-Testbenutzers][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Bereich des Azure-Portals auf die Schaltfläche **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](./media/paloaltoglobalprotect-tutorial/create_aaduser_01.png)

2. Navigieren Sie zu **Benutzer und Gruppen**, und klicken Sie dann auf **Alle Benutzer**, um die Liste mit den Benutzern anzuzeigen.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](./media/paloaltoglobalprotect-tutorial/create_aaduser_02.png)

3. Klicken Sie oben im Dialogfeld **Alle Benutzer** auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.

    ![Schaltfläche „Hinzufügen“](./media/paloaltoglobalprotect-tutorial/create_aaduser_03.png)

4. Führen Sie im Dialogfeld **Neuer Benutzer** die folgenden Schritte aus:

    ![Dialogfeld „Benutzer“](./media/paloaltoglobalprotect-tutorial/create_aaduser_04.png)

    a. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie im Feld **Benutzername** die E-Mail-Adresse des Benutzers Britta Simon ein.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    d. Klicken Sie auf **Create**.
 
### <a name="create-a-palo-alto-networks---globalprotect-test-user"></a>Erstellen eines Palo Alto Networks - GlobalProtect-Testbenutzers

Palo Alto Networks - GlobalProtect unterstützt die Just-In-Time-Bereitstellung von Benutzern. Daher wird ein Benutzer nach der erfolgreichen Authentifizierung automatisch im System erstellt, sofern er noch nicht vorhanden ist. Sie müssen keine Aktion ausführen. 

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf Palo Alto Networks - GlobalProtect gewähren.

![Zuweisen der Benutzerrolle][200] 

**Führen Sie zum Hinzufügen von Britta Simon zu Palo Alto Networks - GlobalProtect die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

2. Wählen Sie in der Anwendungsliste **Palo Alto Networks - GlobalProtect** aus.

    ![Der Link für Palo Alto Networks - GlobalProtect in der Anwendungsliste](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_app.png)  

3. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Link „Benutzer und Gruppen“][202]

4. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“][203]

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

6. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Palo Alto Networks - GlobalProtect“ klicken, sollten Sie automatisch bei Ihrer Palo Alto Networks - GlobalProtect-Anwendung angemeldet werden.
Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_01.png
[2]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_02.png
[3]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_03.png
[4]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_04.png

[100]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_100.png

[200]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_200.png
[201]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_201.png
[202]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_202.png
[203]: ./media/paloaltoglobalprotect-tutorial/tutorial_general_203.png

