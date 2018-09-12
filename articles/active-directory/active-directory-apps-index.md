---
title: Artikelindex für die Anwendungsverwaltung in Azure Active Directory | Microsoft Azure
description: Erfahren Sie, wie Sie das Ablaufdatum für Verbundzertifikate anpassen und Zertifikate erneuern, die in Kürze ablaufen.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: cd95a1f1e0631340fa9844fd31c3d8c0af1168dd
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347051"
---
# <a name="article-index-for-application-management-in-azure-active-directory"></a>Artikelindex für die Anwendungsverwaltung in Azure Active Directory
Diese Seite enthält eine umfassende Liste aller Dokumente, die über die verschiedenen anwendungsbezogenen Funktionen in Azure Active Directory (Azure AD) geschrieben wurden.

Für jeden größeren Funktionsbereich gibt es eine kurze Einführung sowie Anleitungen dazu, welche Artikel gelesen werden sollten, wenn Sie nach bestimmten Informationen suchen.

## <a name="overview-articles"></a>Übersichtsartikel
Die folgenden Artikel sind gute Ausgangspunkte für diejenigen, die lediglich eine kurze Erläuterung zu den Funktionen der Azure AD-Anwendungsverwaltung benötigen.

| Artikelleitfaden |  |
|:---:| --- |
| Eine Einführung in die Probleme der Anwendungsverwaltung, die mit Azure AD gelöst werden |[Verwalten von Anwendungen mit Azure Active Directory (AD)](manage-apps/what-is-application-management.md) |
| Eine Übersicht über die verschiedenen Funktionen in Azure AD im Zusammenhang mit dem Aktivieren der einmaligen Anmeldung, dem Definieren des Zugriffs auf Apps sowie dem Festlegen der Startmethode für Apps |[Anwendungszugriff und einmaliges Anmelden in Azure Active Directory](manage-apps/what-is-single-sign-on.md) |
| Ein Blick auf die verschiedenen Schritte beim Integrieren von Apps in Ihr Azure AD |[Integrieren von Azure Active Directory in Anwendungen](manage-apps/plan-an-application-integration.md)<br /><br />[Aktivieren eines einmaligen Anmeldens an SaaS-Apps](manage-apps/configure-single-sign-on-portal.md)<br /><br />[Verwalten des Zugriffs auf Apps](manage-apps/what-is-access-management.md) |
| Eine technische Erklärung zur Darstellung von Apps in Azure AD |[Wie und warum werden Anwendungen zu Azure AD hinzugefügt?](active-directory-how-applications-are-added.md) |

## <a name="troubleshooting-articles"></a>Artikel zur Problembehandlung
Dieser Abschnitt bietet schnellen Zugriff auf relevante Anweisungen zur Problembehandlung. Weitere Informationen zu den einzelnen Featurebereichen finden Sie im weiteren Verlauf dieser Seite.

| Featurebereich |  |
|:---:| --- |
| Einmalige Verbundanmeldung |[Problembehandlung bei SAML-basiertem einmaligem Anmelden](develop/howto-v1-debug-saml-sso-issues.md) |
| Kennwortbasiertes einmaliges Anmelden |[Problembehandlung in der Zugriffsbereichserweiterung für Internet Explorer](manage-apps/manage-access-panel-browser-extension.md) |
| Anwendungsproxy |[Handbuch zur Problembehandlung beim App-Proxy](manage-apps/application-proxy-troubleshoot.md) |
| Einmaliges Anmelden zwischen lokalem AD und Azure AD |[Problembehandlung bei der Kennworthashsynchronisierung](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md#troubleshoot-password-hash-synchronization)<br /><br />[Problembehandlung beim Kennwortrückschreiben](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Dynamische Gruppenmitgliedschaften |[Problembehandlung bei dynamischen Mitgliedschaften in Gruppen](users-groups-roles/groups-troubleshooting.md) |

## <a name="single-sign-on-sso"></a>Einmaliges Anmelden (Single Sign-On, SSO)
### <a name="federated-single-sign-on-sign-into-many-apps-using-one-identity"></a>Einmaliges Anmelden im Verbund: Anmelden bei vielen Apps mit einer einzigen Identität
Durch einmaliges Anmelden können Benutzer mit nur einem Satz von Anmeldeinformationen auf eine Reihe von Apps und Diensten zugreifen. Der Verbund ist eine Methode, mit der Sie einmaliges Anmelden aktivieren können. Wenn Benutzer versuchen, sich bei Verbund-Apps anzumelden, werden sie auf die offizielle, von Azure Active Directory dargestellte Anmeldeseite der Organisation umgeleitet. Nach erfolgreicher Authentifizierung werden sie wieder an die Anwendung umgeleitet.

| Artikelleitfaden |  |
|:---:| --- |
| Eine Einführung in den Verbund und andere Arten von Anmeldungen |[Einmaliges Anmelden mit Azure AD](manage-apps/what-is-single-sign-on.md) |
| Tausende von SaaS-Apps, die bereits in Azure AD integriert sind, mit vereinfachten Schritten zur Konfiguration der einmaligen Anmeldung |[Erste Schritte mit dem Azure AD-Anwendungskatalog](manage-apps/what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[Vollständige Liste der vorab integrierten Apps, die den Verbund unterstützen (in englischer Sprache)](saas-apps/tutorial-list.md)<br /><br />[Hinzufügen Ihrer App zum Azure AD-App-Katalog](develop/howto-app-gallery-listing.md) |
| Mehr als 150 App-Tutorials zur Konfiguration des einmaligen Anmeldens für Apps wie [Salesforce](saas-apps/salesforce-tutorial.md), [ServiceNow](saas-apps/servicenow-tutorial.md), [Google Apps](saas-apps/google-apps-tutorial.md), [Workday](saas-apps/workday-tutorial.md) und viele mehr |[Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](saas-apps/tutorial-list.md) |
| Vorgehensweise zum manuellen Einrichten und Anpassen Ihrer Konfiguration für einmaliges Anmelden |[Konfigurieren des einmaligen Anmeldens für Anwendungen, die nicht im Azure Active Directory-Anwendungskatalog enthalten sind](manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)<br /><br />[Anpassen ausgestellter Ansprüche im SAML-Token für vorintegrierte Apps](active-directory-saml-claims-customization.md) |
| Handbuch zur Problembehandlung für Verbund-Apps, die das SAML-Protokoll verwenden |[Problembehandlung bei SAML-basiertem einmaligem Anmelden](develop/howto-v1-debug-saml-sso-issues.md) |
| Konfigurieren des Ablaufdatums für das Zertifikat Ihrer App und Erneuern von Zertifikaten |[Verwalten von Zertifikaten für die einmalige Verbundanmeldung in Azure Active Directory](manage-apps/manage-certificates-for-federated-single-sign-on.md) |

Einmaliges Anmelden im Verbund steht für alle Editionen von Azure AD für bis zu zehn Apps pro Benutzer zur Verfügung. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) unterstützt eine unbegrenzte Anzahl von Anwendungen. Wenn Ihre Organisation [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) oder [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) verwendet, können Sie [den Zugriff auf Verbundanwendungen mithilfe von Gruppen zuweisen](#managing-access-to-applications).

### <a name="password-based-single-sign-on-account-sharing-and-sso-for-non-federated-apps"></a>Kennwortbasiertes einmaliges Anmelden: gemeinsame Nutzung von Konten und SSO für nicht im Verbund befindliche Apps
Um das einmalige Anmelden für Anwendungen zu ermöglichen, die den Verbund nicht unterstützen, bietet Azure AD Funktionen zur Kennwortverwaltung, mit denen Kennwörter für SaaS-Apps sicher gespeichert und die Benutzer bei diesen Apps angemeldet werden können. Sie können problemlos Anmeldeinformationen für neu erstellte Konten verteilen und Teamkonten für mehrere Personen freigeben. Benutzer müssen nicht unbedingt die Anmeldeinformationen für die Konten kennen, für die Sie Zugriff erhalten.

| Artikelleitfaden |  |
|:---:| --- |
| Eine Einführung in die Funktionsweise des kennwortbasierten einmaligen Anmeldens und eine kurze technische Übersicht |[Kennwortbasiertes einmaliges Anmelden mit Azure AD](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on) |
| Eine Übersicht über die Szenarien im Zusammenhang mit der Kontofreigabe und Problemlösungen mit Azure AD |[Freigeben von Konten in Azure AD](active-directory-sharing-accounts.md) |
| Automatisches Ändern des Kennworts für bestimmte Apps in regelmäßigen Intervallen |[Automatisiertes Kennwortrollover (Vorschau)](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Handbücher zur Bereitstellung und Problembehandlung für die Internet Explorer-Version der Azure AD-Erweiterung zur Kennwortverwaltung |[How to Deploy the Access Panel Extension for Internet Explorer using Group Policy](manage-apps/deploy-access-panel-browser-extension.md)<br /><br />[Problembehandlung in der Zugriffsbereichserweiterung für Internet Explorer](manage-apps/manage-access-panel-browser-extension.md) |

Das kennwortbasierte einmalige Anmelden steht für alle Editionen von Azure AD für bis zu zehn Apps pro Benutzer zur Verfügung. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) unterstützt eine unbegrenzte Anzahl von Anwendungen. Wenn Ihre Organisation [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) oder [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) verwendet, können Sie [den Zugriff auf Anwendungen mithilfe von Gruppen zuweisen](#managing-access-to-applications). Automatisiertes Kennwortrollover ist ein Feature von [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) .

### <a name="app-proxy-single-sign-on-and-remote-access-to-on-premises-applications"></a>App-Proxy: Einmaliges Anmelden und Remotezugriff auf lokale Anwendungen
Wenn Sie in Ihrem privaten Netzwerk Anwendungen verwenden, die für Benutzer und Geräte außerhalb des Netzwerks zugänglich sein müssen, können Sie einen sicheren Remotezugriff auf diese Apps über einen Azure AD-Anwendungsproxy gewährleisten.

| Artikelleitfaden |  |
|:---:| --- |
| Übersicht über den Azure AD-Anwendungsproxy und seine Funktionsweise |[Bereitstellen eines sicheren Remotezugriffs auf lokale Anwendungen](manage-apps/application-proxy.md) |
| Tutorials zum Konfigurieren des Anwendungsproxys und zum Veröffentlichen Ihrer ersten App |[Aktivieren des Anwendungsproxys über das Azure-Portal](manage-apps/application-proxy-enable.md)<br /><br />[Installieren des Azure AD-Anwendungsproxyconnectors im Hintergrund](manage-apps/application-proxy-register-connector-powershell.md)<br /><br />[Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy](manage-apps/application-proxy-publish-azure-portal.md)<br /><br />[Verwenden eines eigenen Domänennamens](manage-apps/application-proxy-configure-custom-domain.md) |
| Aktivieren des einmaligen Anmeldens und des bedingten Zugriffs für Apps, die mit dem App-Proxy veröffentlicht werden |[Einmaliges Anmelden mit Anwendungsproxy](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)<br /><br />[Bedingter Zugriff und Anwendungsproxy](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| Anleitung zum Verwenden des Anwendungsproxys für die folgenden Szenarien |[Aktivieren von nativen Client-Apps für die Interaktion mit Proxyanwendungen](manage-apps/application-proxy-configure-native-client-application.md)<br /><br />[Arbeiten mit Ansprüche unterstützenden Apps im Anwendungsproxy](manage-apps/application-proxy-configure-for-claims-aware-applications.md)<br /><br />[Unterstützung für in getrennten Netzwerken und an getrennten Orten veröffentlichte Anwendungen](manage-apps/application-proxy-connector-groups.md) |
| Handbuch zur Problembehandlung beim Anwendungsproxy |[Handbuch zur Problembehandlung beim App-Proxy](manage-apps/application-proxy-troubleshoot.md) |

Der Anwendungsproxy steht für alle Editionen von Azure AD für bis zu zehn Apps pro Benutzer zur Verfügung. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) unterstützt eine unbegrenzte Anzahl von Anwendungen. Wenn Ihre Organisation [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) oder [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) verwendet, können Sie [den Zugriff auf Anwendungen mithilfe von Gruppen zuweisen](#managing-access-to-applications).

Möglicherweise interessieren Sie sich auch für die [Azure Active Directory-Domänendienste](../active-directory-domain-services/active-directory-ds-overview.md), die Ihnen das Migrieren Ihrer lokalen Anwendungen zu Azure ermöglichen und gleichzeitig die Identitätsanforderungen dieser Anwendungen erfüllen.

### <a name="enabling-single-sign-on-between-azure-ad-and-on-premises-ad"></a>Aktivieren des einmaligen Anmeldens zwischen Azure AD und lokalem AD
Wenn Ihr Unternehmen eine lokale Version von Windows Server Active Directory parallel zu Ihrem Azure Active Directory in der Cloud einsetzt, möchten Sie wahrscheinlich das einmalige Anmelden zwischen den beiden Systemen ermöglichen. Azure AD Connect (das Tool, das diese beiden Systeme ineinander integriert) bietet mehrere Optionen zum Einrichten des einmaligen Anmeldens: Einrichten eines Verbunds mit AD FS bzw. einem anderen Verbundanbieter oder Aktivieren der Kennwortsynchronisierung.

| Artikelleitfaden |  |
|:---:| --- |
| Eine Übersicht über die Optionen für das einmalige Anmelden in Azure AD Connect sowie Informationen zur Verwaltung von Hybridumgebungen |[Azure AD Connect-Optionen für die Benutzeranmeldung](active-directory-aadconnect-user-signin.md) |
| Allgemeine Richtlinien für die Verwaltung von Umgebungen mit lokalem Active Directory und Azure Active Directory |[Überlegungen zum Entwurf der Azure AD-Hybrididentität](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md) |
| Anleitung zur Verwendung der Kennwortsynchronisierung zum Aktivieren von SSO |[Implementieren der Kennwortsynchronisierung mit der Azure AD Connect-Synchronisierung](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)<br /><br />[Troubleshoot Password Synchronization (Problembehandlung bei der Kennwortsynchronisierung)](https://support.microsoft.com/kb/2855271) |
| Anleitung zur Verwendung des Kennwortrückschreibens zum Aktivieren von SSO |[Erste Schritte mit der Kennwortverwaltung](authentication/quickstart-sspr.md)<br /><br />[Problembehandlung beim Kennwortrückschreiben](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Anleitung zur Verwendung externer Identitätsanbieter zum Aktivieren von SSO |[Liste der kompatiblen externen Identitätsanbieter zum Aktivieren der einmaligen Anmeldung](https://aka.ms/ssoproviders) |
| Genießen der Vorteile des einmaligen Anmeldens über Azure AD Join für Windows 10-Benutzer |[Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-overview.md) |

Azure AD Connect steht für [alle Editionen von Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)zur Verfügung. Die Azure AD-Self-Service-Kennwortzurücksetzung ist in [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) und [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) verfügbar. Das Kennwortrückschreiben in das lokale Active Directory ist ein Feature von [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) .

### <a name="conditional-access-enforce-additional-security-requirements-for-high-risk-apps"></a>Bedingter Zugriff: Erzwingen zusätzlicher Sicherheitsanforderungen für Apps mit hohem Risiko
Sobald Sie das einmalige Anmelden für Ihre Apps und Ressourcen eingerichtet haben, können Sie sensible Anwendungen weiter absichern, indem Sie für jede Anmeldung bei der App bestimmte Sicherheitsanforderungen durchsetzen. Beispielsweise können Sie mit Azure AD erzwingen, dass alle Zugriffe auf eine bestimmte App immer eine mehrstufige Authentifizierung erfordern, unabhängig davon, ob die App diese Funktionalität immanent unterstützt. Ein weiteres häufiges Beispiel für den bedingten Zugriff ist die Anforderung, dass Benutzer mit dem vertrauenswürdigen Netzwerk der Organisation verbunden sein müssen, um auf eine besonders sensible Anwendung zuzugreifen.

| Artikelleitfaden |  |
|:---:| --- |
| Eine Einführung in die Funktionen für bedingten Zugriff, die über Azure AD, Office 365 und Intune hinweg bereitgestellt werden |[Verwalten von Risiken mit bedingtem Zugriff](active-directory-conditional-access-azure-portal.md) |
| Aktivieren des bedingten Zugriffs für die folgenden Ressourcentypen |[Bedingter Zugriff für SaaS-Apps](conditional-access/app-based-conditional-access.md)<br /><br />[Bedingter Zugriff für Office 365-Dienste](active-directory-conditional-access-device-policies.md)<br /><br />[Bedingter Zugriff für lokale Anwendungen](active-directory-conditional-access-azure-portal.md)<br /><br />[Bedingter Zugriff für lokale, über den Azure AD-Anwendungsproxy veröffentlichte Anwendungen](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| Registrieren von Geräten bei Azure Active Directory zum Aktivieren gerätebasierter Richtlinien für den bedingten Zugriff |[Erste Schritte bei der Azure Active Directory-Geräteregistrierung](active-directory-conditional-access-device-registration-overview.md)<br /><br />[Automatische Geräteregistrierung bei Azure Active Directory für in Domänen eingebundene Windows-Geräte](active-directory-conditional-access-automatic-device-registration.md)<br />– [Schritte für Windows 8.1-Geräte](active-directory-conditional-access-automatic-device-registration-setup.md)<br />– [Schritte für Windows 7-Geräte](active-directory-conditional-access-automatic-device-registration-setup.md) |

| Vorgehensweise: Verwenden der Microsoft Authenticator-App für die zweistufige Überprüfung | [Microsoft Authenticator](user-help/microsoft-authenticator-app-how-to.md) |

Bedingter Zugriff ist ein [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) -Feature.

## <a name="apps--azure-ad"></a>Apps und Azure AD
### <a name="cloud-discovery-find-which-saas-apps-are-being-used-in-your-organization"></a>Cloud Discovery: Herausfinden der in Ihrer Organisation verwendeten SaaS-Apps
Cloud Discovery analysiert Ihre Datenverkehrsprotokolle in Bezug auf Cloud-App-Kataloge von Microsoft Cloud App Security von mehr als 16.000 Cloud-Apps, die abhängig von mehr als 70 Risikofaktoren klassifiziert und bewertet werden, um Ihnen laufend Einblicke in die Cloudnutzung, Schatten-IT und das Risiko der Schatten-IT für Ihre Organisation zu gewähren.

| Artikelleitfaden |  |
|:---:| --- |
| Allgemeiner Überblick über die Funktionsweise |[Einrichten von Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) |


### <a name="automatically-provision-and-deprovision-user-accounts-in-saas-apps"></a>Automatisches Bereitstellen und Entziehen von Benutzerkonten in SaaS-Apps
Automatisieren Sie das Erstellen, Warten und Entfernen von Benutzeridentitäten in SaaS-Anwendungen, wie z. B. Dropbox, Salesforce, ServiceNow und weiteren. Gleichen Sie vorhandene Identitäten zwischen Azure AD und Ihren SaaS-Apps ab, und synchronisieren Sie sie. Steuern Sie außerdem den Zugriff durch automatisches Deaktivieren von Konten, wenn Benutzer die Organisation verlassen.

| Artikelleitfaden |  |
|:---:| --- |
| Weitere Informationen zur Funktionsweise und Antworten auf häufig gestellte Fragen |[Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](manage-apps/user-provisioning.md) |
| Konfigurieren der Zuordnung von Informationen zwischen Azure AD und Ihrer SaaS-App |[Anpassen von Attributzuordnungen](manage-apps/customize-application-attributes.md)<br><br>[Schreiben von Ausdrücken für Attributzuordnungen](manage-apps/functions-for-customizing-application-data.md) |
| Aktivieren der automatisierten Bereitstellung in jeder App, die das SCIM-Protokoll unterstützt |[Einrichten der automatischen Bereitstellung von Benutzern für SCIM-fähige Apps](manage-apps/use-scim-to-provision-users-and-groups.md) |
| Berichterstellung zu und Problembehandlung bei Benutzerbereitstellungen |[Berichterstellung zur automatischen Benutzerbereitstellung](manage-apps/check-status-user-account-provisioning.md)<br><br>[Problembehandlung bei Benutzerbereitstellungen](active-directory-application-provisioning-content-map.md) |
| Einschränken der für eine Anwendung bereitgestellten Benutzer basierend auf deren Attributwerten |[Bereichsfilter](manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) |

Die automatisierte Benutzerbereitstellung steht für alle Editionen von Azure AD für bis zu zehn Apps pro Benutzer zur Verfügung. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) unterstützt eine unbegrenzte Anzahl von Anwendungen. Wenn Ihre Organisation [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) oder [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) verwendet, können Sie [die Benutzerbereitstellung anhand von Gruppen verwalten](#managing-access-to-applications).

### <a name="building-applications-that-integrate-with-azure-ad"></a>Erstellen von in Azure AD integrierbaren Anwendungen
Wenn Ihre Organisation Line-of-Business-Anwendungen (LoB) entwickelt oder verwaltet oder wenn Sie ein App-Entwickler sind, dessen Kunden Azure Active Directory einsetzen, unterstützten die folgenden Tutorials Sie beim Integrieren Ihrer Anwendungen in Azure AD.

| Artikelleitfaden |  |
|:---:| --- |
| Anleitung für IT-Experten und Anwendungsentwickler zur Integration von Apps in Azure AD |[Azure AD und Anwendungen: Entwickeln von Branchen-Apps](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[Entwicklerhandbuch zu Azure Active Directory](develop/azure-ad-developers-guide.md) |
| Vorgehensweise für Anwendungshersteller zum Hinzufügen von Apps zum Azure AD-App-Katalog |[Ihre Anwendung im Azure Active Directory-Anwendungskatalog auflisten](develop/howto-app-gallery-listing.md) |
| Verwalten des Zugriffs auf entwickelte Anwendungen mithilfe von Azure Active Directory |[Azure AD und Clientanwendungen: Erfordern der Benutzerzuweisung](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[Azure AD und Anwendungen: Zuweisen von Benutzern zu einer Anwendung](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[Zuweisen von Gruppen zu Ihrer App](active-directory-applications-guiding-developers-assigning-groups.md) |

Wenn Sie kundenorientierte Anwendungen entwickeln, interessieren Sie sich möglicherweise für die Verwendung von [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) , damit Sie für die Verwaltung Ihrer Benutzer nicht Ihr eigenes Identitätssystem entwickeln müssen. [Weitere Informationen](../active-directory-b2c/active-directory-b2c-overview.md).

## <a name="managing-access-to-applications"></a>Verwalten des Zugriffs auf Anwendungen
### <a name="using-groups-and-self-service-to-manage-who-has-access-to-which-apps"></a>Verwenden von Gruppen und Self-Service zum Verwalten des Zugriffs auf bestimmte Apps
Um Sie bei der Verwaltung des Zugriffs auf Ressourcen zu unterstützen, ermöglicht Azure Active Directory Ihnen das umfassende Festlegen von Zuweisungen und Berechtigungen mithilfe von Gruppen. Die IT-Abteilung kann nach Belieben Self-Service-Funktionen aktivieren, sodass Benutzer einfach eine Berechtigung anfordern können, wenn sie sie benötigen.

| Artikelleitfaden |  |
|:---:| --- |
| Übersicht über Azure AD-Features für die Zugriffsverwaltung |[Einführung in die Verwaltung des Zugriffs auf Apps](manage-apps/what-is-access-management.md)<br /><br />[Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](fundamentals/active-directory-manage-groups.md)<br /><br />[Verwenden von Gruppen zum Verwalten des Zugriffs auf SaaS-Anwendungen](users-groups-roles/groups-saasapps.md) |
| Aktivieren der Self-Service-Verwaltung von Apps und Gruppen |[Self-Service-Anwendungszugriff und delegierte Verwaltung mit Azure Active Directory](active-directory-self-service-application-access.md)<br /><br />[Self-Service-Gruppenverwaltung](users-groups-roles/groups-self-service-management.md) |
| Anleitungen zum Einrichten von Gruppen in Azure AD |[Verwalten von Gruppen in Azure Active Directory](fundamentals/active-directory-groups-create-azure-portal.md)<br /><br />[Verwalten von Besitzern einer Gruppe](fundamentals/active-directory-accessmanagement-managing-group-owners.md)<br /><br />[Verwenden der Gruppe „Alle Benutzer“](active-directory-accessmanagement-dedicated-groups.md) |
| Verwenden dynamischer Gruppen zum automatischen Auffüllen der Gruppenmitgliedschaft mithilfe von attributbasierten Mitgliedschaftsregeln |[Verwenden von Attributen zum Erstellen erweiterter Regeln](active-directory-groups-dynamic-membership-azure-portal.md)<br /><br />[Problembehandlung bei dynamischen Mitgliedschaften in Gruppen](users-groups-roles/groups-troubleshooting.md) |

Gruppenbasierte Anwendungszugriffsverwaltung steht für [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) und [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) zur Verfügung. Self-Service-Gruppenverwaltung, Self-Service-Anwendungsverwaltung und dynamische Gruppen sind Features von [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) .

### <a name="b2b-collaboration-enable-partner-access-to-applications"></a>B2B-Zusammenarbeit: Aktivieren des Partnerzugriffs auf Anwendungen
Wenn Ihr Unternehmen eine Partnerschaft mit anderen Unternehmen eingegangen ist, müssen Sie wahrscheinlich den Partnerzugriff auf Ihre Unternehmensanwendungen verwalten. Die Azure Active Directory B2B-Zusammenarbeit bietet eine einfache und sichere Möglichkeit zum Teilen Ihrer Apps mit Partnern.

| Artikelleitfaden |  |
|:---:| --- |
| Überblick über die verschiedenen Azure AD-Features zur Verwaltung externer Benutzer wie Partner, Kunden usw. |[Vergleich von Funktionen zum Verwalten externer Identitäten in Azure AD](active-directory-b2b-compare-external-identities.md) |
| Einführung in die B2B-Zusammenarbeit und erste Schritte |[Vorschau der Azure AD B2B-Zusammenarbeit: Einfache, sichere Integration von Partnern in der Cloud](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Azure Active Directory B2B-Zusammenarbeit](active-directory-b2b-collaboration-overview.md) |
| Tiefer gehende Details zur Azure AD B2B-Zusammenarbeit und deren Verwendung |[Vorschau der Azure AD B2B-Zusammenarbeit: Funktionsweise](active-directory-b2b-how-it-works.md)<br /><br />[Aktuelle Einschränkungen der Azure AD B2B-Zusammenarbeit](active-directory-b2b-current-limitations.md)<br /><br />[Ausführliche exemplarische Vorgehensweise zur Verwendung der Azure AD B2B-Zusammenarbeit](active-directory-b2b-detailed-walkthrough.md) |
| Referenzartikel mit technischen Details zur Funktionsweise der Azure AD B2B-Zusammenarbeit |[Vorschau der Azure AD B2B-Zusammenarbeit: CSV-Dateiformat](active-directory-b2b-references-csv-file-format.md)<br /><br />[Vorschau der Azure AD B2B-Zusammenarbeit: Objektattributänderungen für externe Benutzer](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[Benutzertokenformat für Partnerbenutzer](active-directory-b2b-references-external-user-token-format.md) |

Die B2B-Zusammenarbeit ist zurzeit für [alle Editionen von Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) verfügbar.

### <a name="access-panel-a-portal-for-accessing-apps-and-self-service-features"></a>Zugriffsbereich: Ein Portal für den Zugriff auf Apps und Self-Service-Funktionen
Der Azure AD-Zugriffsbereich ist der Bereich, in dem Endbenutzer ihre Apps starten und auf die Self-Service-Funktionen zugreifen können, die ihnen das Verwalten ihrer Apps und Gruppenmitgliedschaften erlauben. Zusätzlich zum Zugriffsbereich sind in der folgenden Liste weitere Optionen für den Zugriff auf SSO-fähige Apps enthalten.

| Artikelleitfaden |  |
|:---:| --- |
| Vergleich der verschiedenen verfügbaren Optionen zum Bereitstellen von Apps für einmaliges Anmelden für Benutzer |[Bereitstellen von in Azure AD integrierten Anwendungen für Benutzer](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users) |
| Übersicht über den Zugriffsbereich und das mobile Äquivalent MyApps |[Einführung in den Zugriffsbereich](user-help/active-directory-saas-access-panel-introduction.md)<br />— [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| Zugriff auf Azure AD-Apps über die Office 365-Website |[Lernen Sie das Office 365-App-Startfeld kennen](https://support.office.com/en-us/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| Zugriff auf Azure AD-Apps über die mobile App Intune Managed Browser |[Verwalten des Internetzugriffs mittels Richtlinien für Managed Browser mit Microsoft Intune](https://technet.microsoft.com/library/dn878029.aspx)<br />— [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| Zugreifen auf Azure AD-Apps mit Deep-Links zum Initiieren des einmaligen Anmeldens |[Abrufen von Links zur direkten Anmeldung bei Ihren Apps](manage-apps/what-is-single-sign-on.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

Der Zugriffsbereich steht für [alle Editionen von Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)zur Verfügung.

### <a name="reports-easily-audit-app-access-changes-and-monitor-sign-ins-to-apps"></a>Berichte: Problemloses Überwachen von Änderungen am App-Zugriff und von Anmeldungen bei Apps
Azure Active Directory bietet mehrere Berichte und Warnungen, mit denen Sie den Zugriff Ihrer Organisation auf Anwendungen überwachen können. Sie können Warnungen für anomale Anmeldungen bei Ihren Apps erhalten und zudem nachverfolgen, wann und warum sich der Zugriff eines Benutzers auf eine Anwendung geändert hat.

| Artikelleitfaden |  |
|:---:| --- |
| Überblick über die Berichtsfunktionen in Azure Active Directory |[Erste Schritte mit Azure Active Directory-Berichten](active-directory-reporting-getting-started.md) |
| Überwachen der Anmeldungen und der App-Nutzung Ihrer Benutzer |[Anzeigen Ihrer Zugriffs- und Nutzungsberichte](active-directory-view-access-usage-reports.md) |
| Nachverfolgen von Änderungen am Zugriff auf eine bestimmte Anwendung |[Azure Active Directory-Überwachungsberichtsereignisse](active-directory-reporting-audit-events.md) |
| Exportieren der Daten dieser Berichte in Ihre bevorzugten Tools mit der Berichterstellungs-API |[Erste Schritte mit der Azure AD Reporting-API](active-directory-reporting-api-getting-started.md) |

Um herauszufinden, welche Berichte in verschiedenen Editionen von Azure Active Directory enthalten sind, [klicken Sie hier](active-directory-view-access-usage-reports.md).

## <a name="see-also"></a>Weitere Informationen
[Was ist Azure Active Directory?](fundamentals/active-directory-whatis.md)

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)

[Azure Active Directory-Domänendienste](https://azure.microsoft.com/services/active-directory-ds/)

[Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/)
