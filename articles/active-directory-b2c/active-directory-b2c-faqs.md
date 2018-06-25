---
title: Häufig gestellte Fragen (FAQ) zu Azure Active Directory B2C | Microsoft-Dokumentation
description: Häufig gestellte Fragen (FAQ) zu Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1b56cdd119bac28eec819dbf38afcd2be5aaf907
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34710731"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure Active Directory B2C: Häufig gestellte Fragen 
Auf dieser Seite werden häufig gestellte Fragen zu Azure Active Directory (Azure AD) B2C beantwortet. Die Seite wird bei Bedarf aktualisiert.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Kann ich Azure AD B2C-Features in meinem vorhandenen mitarbeiterbasierten Azure AD Directory-Mandanten nutzen?
Azure AD und Azure AD B2C sind separate Produktangebote, die nicht zusammen unter demselben Mandanten verwendet werden können.  Ein Azure AD-Mandant repräsentiert eine Organisation.  Ein Azure AD B2C-Mandant repräsentiert eine Sammlung mit Identitäten, die mit Anwendungen der vertrauenden Seite verwendet werden sollen.  Mit benutzerdefinierten Richtlinien (öffentliche Vorschauversion) kann Azure AD B2C einen Verbund mit Azure AD herstellen, um die Authentifizierung von Mitarbeitern einer Organisation zu ermöglichen.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Kann ich Azure AD B2C zum Bereitstellen der Anmeldung für soziale Netzwerke (Facebook und Google+) in Office 365 verwenden?
Azure AD B2C kann nicht verwendet werden, um Benutzer für Microsoft Office 365 zu authentifizieren.  Azure AD ist die Microsoft-Lösung zum Verwalten des Mitarbeiterzugriffs auf SaaS-Apps. Sie verfügt über Features, die für diesen Zweck konzipiert wurden, z.B. Lizenzierung und bedingter Zugriff.  Azure AD B2C stellt eine Plattform für die Identitäts- und Zugriffsverwaltung bereit, die zum Erstellen von Webanwendungen und mobilen Anwendungen verwendet werden kann.  Wenn Azure AD B2C für den Verbund mit einem Azure AD-Mandanten konfiguriert ist, verwaltet der Azure AD-Mandant den Mitarbeiterzugriff auf Anwendungen, die auf Azure AD B2C basieren.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Was sind lokale Konten in Azure AD B2C? Wie unterscheiden sie sich von Geschäfts-, Schul- oder Unikonten in Azure AD?
Bei einem Azure AD-Mandanten melden sich Benutzer, die zum Mandanten gehören, mit einer E-Mail-Adresse der Form `<xyz>@<tenant domain>` an.  `<tenant domain>` (Mandantendomäne) ist eine der überprüften Domänen des Mandanten oder die Anfangsdomäne `<...>.onmicrosoft.com`. Dieser Kontotyp ist ein Geschäfts-, Schul- oder Unikonto.

Bei einem Azure AD B2C-Mandanten kann sich der Benutzer in den meisten Apps mit einer beliebigen E-Mail-Adresse anmelden (also beispielsweise mit joe@comcast.net, bob@gmail.com, sarah@contoso.com oder jim@live.com). Diese Art von Konto ist ein lokales Konto.  Wir unterstützen auch beliebige Benutzernamen als lokale Konten (z.B. joe, bob, sarah oder jim). Sie können einen dieser beiden lokalen Kontotypen verwenden, wenn Sie im Azure-Portal Identitätsanbieter für Azure AD B2C konfigurieren. Klicken Sie in Ihrem Azure AD B2C-Mandanten auf **Identitätsanbieter**, und wählen Sie anschließend unter „Lokale Konten“ die Option **Benutzername** aus. 

Benutzerkonten für Anwendungen müssen immer über eine Registrierungsrichtlinie, über eine Registrierungs- oder Anmelderichtlinie oder unter Verwendung der Azure AD Graph-API erstellt werden. Über das Azure-Portal erstellte Benutzerkonten werden nur zur Mandantenverwaltung verwendet.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Welche Identitätsanbieter aus sozialen Netzwerken werden derzeit unterstützt? Welche sollen in Zukunft unterstützt werden?
Wir unterstützen derzeit Facebook, Google+, LinkedIn, Amazon, Twitter (Vorschau), WeChat (Vorschau), Weibo (Vorschau) und QQ (Vorschau). Die Unterstützung für andere beliebte soziale Identitätsanbieter wird je nach Kundennachfrage hinzugefügt.

Azure AD B2C bietet auch zusätzliche Unterstützung für [benutzerdefinierte Richtlinien](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom).  Durch derartige [benutzerdefinierte Richtlinien](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom) können Entwickler eigene Richtlinien mit einem beliebigen Identitätsanbieter erstellen, der [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) oder SAML unterstützt. 

Machen Sie sich durch das [Starter Pack für benutzerdefinierte Richtlinien](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack) mit benutzerdefinierten Richtlinien vertraut.

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Kann ich Bereiche konfigurieren, um von verschiedenen Identitätsanbietern aus sozialen Netzwerken mehr Informationen zu Endkunden zu erhalten?
Nein, aber dieses Feature ist geplant. Die Standardbereiche für unsere unterstützten sozialen Identitätsanbieter sind:

* Facebook: email
* Google+: email
* Microsoft-Konto: openid email profile
* Amazon: profile
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Muss meine Anwendung unter Azure ausgeführt werden, damit sie mit Azure AD B2C funktioniert?
Nein, Sie können Ihre Anwendung überall hosten (in der Cloud oder lokal). Für die Interaktion mit Azure AD B2C muss nur die Fähigkeit zum Senden und Empfangen von HTTP-Anforderungen über öffentlich zugängliche Endpunkte möglich sein.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Ich habe mehrere Azure AD B2C-Mandanten. Wie kann ich sie im Azure-Portal verwalten?
Vor dem Öffnen von „Azure AD B2C“ im Menü auf der linken Seite im Azure-Portal müssen Sie zum Verzeichnis wechseln, das Sie verwalten möchten.  Wechseln Sie Verzeichnisse, indem Sie rechts oben im Azure-Portal auf Ihre Identität klicken und dann ein Verzeichnis in der Dropdownliste auswählen, die angezeigt wird.  Eine detaillierte Anleitung mit Abbildungen finden Sie unter [Navigieren zu Azure AD B2C-Einstellungen](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Wie kann ich Bestätigungs-E-Mails anpassen (Inhalt und das Feld „Von:“), die von Azure AD B2C gesendet werden?
Sie können das [Feature für Unternehmensbranding](../active-directory/customize-branding.md) verwenden, um den Inhalt von Bestätigungs-E-Mails anzupassen. Insbesondere diese beiden Elemente einer E-Mail können angepasst werden:

* **Bannerlogo**: Wird unten rechts angezeigt.
* **Hintergrundfarbe**: Wird oben angezeigt.

    ![Screenshot einer angepassten Bestätigungs-E-Mail](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

Die E-Mail-Signatur enthält den Namen des Azure AD B2C-Mandanten, den Sie bei der ursprünglichen Erstellung des Azure AD B2C-Mandanten angegeben haben. Sie können den Namen mit folgenden Schritten ändern:

1. Melden Sie sich als globaler Administrator beim [Azure-Portal](https://portal.azure.com/) an.
1. Öffnen Sie das Blatt **Azure Active Directory**.
1. Klicken Sie auf die Registerkarte **Eigenschaften**.
1. Ändern Sie das Feld **Name**.
1. Klicken Sie oben auf der Seite auf **Speichern**.

Zurzeit besteht keine Möglichkeit, das Feld „Von:“ der E-Mail zu ändern. Sie können unter [feedback.azure.com](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails) Ihre Stimme abgeben, wenn Sie an der Anpassung des Texts der Überprüfungs-E-Mail interessiert sind.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Wie kann ich meine vorhandenen Benutzernamen, Kennwörter und Profile von meiner Datenbank zu Azure AD B2C migrieren?
Sie können Ihr Migrationstool mithilfe der Azure AD Graph-API schreiben. Weitere Informationen finden Sie im [Handbuch für die Benutzermigration](active-directory-b2c-user-migration.md).

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Welche Kennwortrichtlinie wird für lokale Konten in Azure AD B2C verwendet?
Die Kennwortrichtlinie von Azure AD B2C für lokale Konten basiert auf der Kennwortrichtlinie von Azure AD. Die Azure AD B2C-Richtlinien zur Registrierung, Anmeldung und Kennwortrücksetzung verwenden sichere Kennwörter, und die Kennwörter laufen nicht ab. Weitere Informationen finden Sie in der [Kennwortrichtlinie von Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) .

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Kann ich Azure AD Connect zum Migrieren von Endkundenidentitäten, die in meinem lokalen Active Directory gespeichert sind, zu Azure AD B2C verwenden?
Nein. Azure AD Connect ist nicht für die Verwendung mit Azure AD B2C ausgelegt. Sie können erwägen, die [Graph-API](active-directory-b2c-devquickstarts-graph-dotnet.md) für die Benutzermigration zu verwenden.  Weitere Informationen finden Sie im [Handbuch für die Benutzermigration](active-directory-b2c-user-migration.md).

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Kann meine App Azure AD B2C-Seiten in einem iFrame öffnen?
Nein. Aus Sicherheitsgründen können Azure AD B2C-Seiten nicht in einem iFrame geöffnet werden.  Unser Dienst kommuniziert mit dem Browser, um die Verwendung von iFrames zu unterbinden.  In allgemeinen Sicherheitskreisen und in der OAUTH2-Spezifikation wird davon abgeraten, iFrames für Identitätszwecke zu verwenden, weil dies mit einem Clickjacking-Risiko verbunden ist.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Funktioniert Azure AD B2C in Verbindung mit CRM-Systemen wie Microsoft Dynamics?
Die Integration in das Microsoft Dynamics 365-Portal ist möglich.  Siehe [Konfigurieren des Dynamics 365-Portals für die Authentifizierung mit Azure AD B2C](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Funktioniert Azure AD B2C in Verbindung mit SharePoint On-Premises 2016 oder früher?
Azure AD B2C ist nicht für das SharePoint-Freigabeszenario für externe Partner vorgesehen. Informationen hierzu finden Sie unter [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx).

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Sollte ich Azure AD B2C oder B2B zum Verwalten externer Identitäten verwenden?
Lesen Sie diesen Artikel über [externe Identitäten](../active-directory/active-directory-b2b-compare-external-identities.md) , um mehr über das Anwenden der entsprechenden Features in Ihren Szenarien mit externen Identitäten zu erfahren.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Welche Funktionen für Berichterstellung und Überwachung bietet Azure AD B2C? Sind die Funktionen mit denen in Azure AD Premium identisch?
Nein. Azure AD B2C unterstützt nicht den gleichen Berichtssatz wie Azure AD Premium. Es gibt aber viele Gemeinsamkeiten:

* **Anmeldeberichte** sind nur im Azure-Portal (Azure Active Directory > Aktivität > Anmeldungen) und nicht über die Graph-API verfügbar. Sie enthalten einen Eintrag für jede Anmeldung mit reduzierten Details.
* **Prüfberichte** sind nur im Azure-Portal (Azure Active Directory > Aktivität > Überwachungsprotokolle) und nicht über die Graph-API verfügbar. Sie enthalten die Administratoraktivität sowie die Anwendungsaktivität. 
* **Verwendungsberichte** stehen nur über die [Verwendungsberichts-API](active-directory-b2c-reference-usage-reporting-api.md) und nicht über das Azure-Portal zur Verfügung. Sie enthalten die Anzahl von Benutzern, die Anzahl von Anmeldungen und den MFA-Umfang. 

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Kann ich die Benutzeroberfläche von Seiten lokalisieren, die von Azure AD B2C bereitgestellt werden? Welche Sprachen werden unterstützt?
Ja.  Lesen Sie die Informationen zur [Sprachanpassung](active-directory-b2c-reference-language-customization.md). Diese Funktion befindet sich in der öffentlichen Vorschauphase.  Wir bieten Übersetzungen für 36 Sprachen an, und Sie können alle Zeichenfolgen gemäß Ihren Anforderungen außer Kraft setzen und anpassen.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Kann ich auf meinen von Azure AD B2C bereitgestellten Seiten für die Registrierung und Anmeldung meine eigenen URLs verwenden? Kann ich beispielsweise die URL von „login.microsoftonline.com“ in „login.contoso.com“ ändern?
Derzeit ist dies nicht möglich. Dieses Feature ist aber geplant. Sie erreichen dieses Ziel nicht, indem Sie Ihre Domäne auf der Registerkarte **Domänen** im Azure-Portal überprüfen.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Wie lösche ich meinen Azure AD B2C-Mandanten?
Führen Sie die folgenden Schritte aus, um Ihren Azure AD B2C-Mandanten zu löschen:

1. Führen Sie die folgenden Schritte aus, um im Azure-Portal zu den [Azure AD B2C-Einstellungen](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) zu wechseln.
1. Navigieren Sie zu **Anwendungen**, **Identitätsanbieter** und **Alle Richtlinien**, und löschen Sie jeweils alle Einträge.
1. Melden Sie sich nun beim [Azure-Portal](https://portal.azure.com/) als Abonnementadministrator an. (Verwenden Sie dasselbe Geschäfts-, Schul- oder Unikonto bzw. dasselbe Microsoft-Konto, mit dem Sie sich bei Azure registriert haben.)
1. Wechseln Sie zu dem Azure AD B2C-Mandanten, den Sie löschen möchten.
2. Navigieren Sie zum Active Directory-Menü auf der linken Seite.
3. Wählen Sie **Benutzer und Gruppen**.
4. Wählen Sie nacheinander die einzelnen Benutzer aus – mit Ausnahme des Abonnementadministrator-Benutzers, als der Sie gerade angemeldet sind. Klicken Sie im unteren Bereich der Seite auf **Löschen**, und klicken Sie auf **Ja**, wenn Sie zur Bestätigung aufgefordert werden.
5. Klicken Sie auf **App-Registrierungen**.
6. Wählen Sie die Anwendung **b2c-extensions-app** aus. Klicken Sie auf **Löschen** und dann bei der Frage auf **Ja**.
7. Wählen Sie **Übersicht**.
8. Klicken Sie auf **Verzeichnis löschen**. Folgen Sie den Anweisungen auf dem Bildschirm, um den Vorgang abzuschließen.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Kann ich Azure AD B2C als Teil der Enterprise Mobility Suite erwerben?
Nein. Azure AD B2C ist ein Azure-Dienst mit nutzungsbasierter Bezahlung und nicht Teil der Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Wie melde ich Probleme mit Azure AD B2C?
Siehe [Senden von Supportanfragen für Azure Active Directory B2C](active-directory-b2c-support.md).
