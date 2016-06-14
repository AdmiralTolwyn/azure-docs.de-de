<properties
	pageTitle="Tutorial: Azure Active Directory-Integration mit DocuSign | Microsoft Azure"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und DocuSign konfigurieren."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/26/2016"
	ms.author="jeedes"/>


# Tutorial: Azure Active Directory-Integration mit DocuSign

In diesem Tutorial wird die Integration von Azure und DocuSign erläutert. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

- Ein gültiges Azure-Abonnement
- Einen Mandanten in DocuSign



Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1. [Aktivieren der Anwendungsintegration für DocuSign](#enabling-the-application-integration-for-docusign) 


2. [Konfigurieren der einmaligen Anmeldung](#configuring-single-sign-on)


3. [Konfigurieren der Kontobereitstellung](#configuring-account-provisioning)


4. [Zuweisen von Benutzern](#assigning-users)




![Konfigurieren der einmaligen Anmeldung][0]


 

## Aktivieren der Anwendungsintegration für DocuSign

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für DocuSign aktivieren.

### Führen Sie die folgenden Schritte aus, um die Anwendungsintegration für DocuSign zu aktivieren:

1. Klicken Sie im klassischen Azure-Portal im linken Navigationsbereich auf **Active Directory**.

	![Konfigurieren der einmaligen Anmeldung][1]

2. Wählen Sie in der Liste Verzeichnis das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

	![Konfigurieren der einmaligen Anmeldung][2]

4. Klicken Sie unten auf der Seite auf **Hinzufügen**.

	![Anwendungen][3]

5. Klicken Sie im Dialogfeld „Was möchten Sie tun?“ auf **Anwendung aus dem Katalog hinzufügen**.

	![Konfigurieren der einmaligen Anmeldung][4]


6. Geben Sie im Suchfeld das Wort **DocuSign** ein.

	![Konfigurieren der einmaligen Anmeldung][5]

7. Wählen Sie im Ergebnisbereich **DocuSign** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

	![Konfigurieren der einmaligen Anmeldung][6]




## Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei DocuSign zu authentifizieren.


### So konfigurieren Sie einmaliges Anmelden

1. Klicken Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für **DocuSign** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld „Einmaliges Anmelden konfigurieren“ zu öffnen.

	![Konfigurieren der einmaligen Anmeldung][7]

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei DocuSign anmelden?** die Option **Microsoft Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf „Weiter“.

	![Konfigurieren der einmaligen Anmeldung][8]

3. Führen Sie auf der Seite **App-Einstellungen konfigurieren** die folgenden Schritte aus:

	![Konfigurieren der einmaligen Anmeldung][9]

	a. Geben Sie im Textfeld **Anmelde-URL** die URL Ihres DocuSign-Mandanten im folgenden Format ein: `https://<company name>.docusign.net/Member/MemberLogin.aspx?ssoname=<SSO instance name>`

	b. Klicken Sie auf **Weiter**.


    > [AZURE.TIP] Falls Sie die App-URL für Ihren Mandanten nicht kennen, können Sie sich unter SSOSetup@Docusign.com an DocuSign wenden, um die vom Service Provider initiierte SSO-URL für den Mandanten zu erfragen.
 

4. Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für DocuSign** auf **Zertifikat herunterladen**, und speichern Sie die Zertifikatsdatei lokal auf Ihrem Computer.

	![Konfigurieren der einmaligen Anmeldung][10]


5. Melden Sie sich in einem anderen Webbrowserfenster bei der **DocuSign**-Unternehmenswebsite als Administrator an.


6. Erweitern Sie oben im Menü das Benutzermenü, klicken Sie auf **Einstellungen**, und erweitern Sie dann links im Navigationsbereich die Option **Kontenverwaltung**. Klicken Sie anschließend auf **Features**.

	![Konfigurieren der einmaligen Anmeldung][11]

7. Klicken Sie auf **SAML-Konfiguration** und dann auf den Link **SAML-Konfiguration**.



8. Führen Sie im Abschnitt **SAML 2.0-Konfiguration** die folgenden Schritte aus:

	![Konfigurieren der einmaligen Anmeldung][13]


    a. Kopieren Sie im klassischen Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für DocuSign** den Wert für „Aussteller-URL**“, und fügen Sie ihn in das Textfeld **Endpunkt-URL für Identitätsanbieter-Aussteller** ein.

    > [AZURE.IMPORTANT] Wenn diese Konfigurationsoption nicht verfügbar ist, können Sie sich an Ihren Ansprechpartner bei DocuSign oder per E-Mail an das SSO-Supportteam wenden ([SSOSetup@docusign.com](mailto:SSOSetup@docusign.com)).
 
    b. Klicken Sie auf **Durchsuchen**, um das heruntergeladene Zertifikat hochzuladen.


    c. Wählen Sie die Option **Vorname, Nachname und E-Mail-Adresse aktivieren**.


    d. Klicken Sie auf **Speichern**.


9. Wählen Sie im klassischen Azure-Portal die **Bestätigung zur Konfiguration des einmaligen Anmeldens** aus, und klicken Sie dann auf **Weiter**.

	![Anwendungen][14]

10. Klicken Sie auf der Seite **Bestätigung zur einmaligen Anmeldung** auf **Fertig stellen**.

	![Anwendungen][15]


 

## Konfigurieren der Kontobereitstellung

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für DocuSign aktivieren.

### So konfigurieren Sie die Benutzerbereitstellung

1. Klicken Sie im **klassischen Azure-Portal** auf der **Anwendungsintegrationsseite für DocuSign** auf **Kontobereitstellung konfigurieren**, um das Dialogfeld „Benutzerbereitstellung konfigurieren“ zu öffnen.

	![Konfigurieren der Kontobereitstellung][30]
 

2. Geben Sie auf der Seite **Einstellungen und Administrator-Anmeldeinformationen** zum Aktivieren der automatischen Benutzerbereitstellung die Anmeldeinformationen eines DocuSign-Kontos mit ausreichenden Rechten ein, und klicken Sie auf **Weiter**.

	![Konfigurieren der Kontobereitstellung][31]

3. Klicken Sie im Dialogfeld **Verbindung testen** auf **Test starten**, und klicken Sie auf **Weiter**, wenn der Test erfolgreich ist.

	![Konfigurieren der Kontobereitstellung][32]

3. Klicken Sie auf der Bestätigungsseite auf **Abschließen**.

![Konfigurieren der Kontobereitstellung][33]
 

## Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

### Führen Sie die folgenden Schritte aus, um DocuSign Benutzer zuzuweisen:

1. Erstellen Sie im **klassischen Azure-Portal** ein Testkonto.

2. Klicken Sie auf der **Anwendungsintegrationsseite für DocuSign** auf **Benutzer zuweisen**.

	![Zuweisen von Benutzern][40]
 

3. Wählen Sie den Testbenutzer aus, klicken Sie auf **Zuweisen** und anschließend auf **Ja**, um die Zuweisung zu bestätigen.

	![Zuweisen von Benutzern][41]


Nach 10 Minuten können Sie überprüfen, ob das Konto mit DocuSign synchronisiert wurde.

Als ersten Überprüfungsschritt können Sie den Bereitstellungsstatus überprüfen, indem Sie im klassischen Azure-Portal auf der Anwendungsintegrationsseite für DocuSign auf „Dashboard“ klicken.

![Zuweisen von Benutzern][42]

Ein erfolgreich abgeschlossener Benutzerbereitstellungszyklus wird durch einen entsprechenden Status angezeigt:

![Zuweisen von Benutzern][43]


Wenn Sie die Einstellungen für einmaliges Anmelden testen möchten, öffnen Sie den Zugriffsbereich.

Weitere Informationen zum Zugriffsbereich finden Sie unter „Einführung in den Zugriffsbereich“.





## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_06.png
[11]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_07.png

[13]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_09.png
[14]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_18.png

<!---HONumber=AcomDC_0601_2016-->