---
title: Gemeinsame Nutzung von Konten mit Azure AD | Microsoft Docs
description: "Beschreibt, wie es Azure Active Directory Organisationen ermöglicht, Konten für lokale Apps und für Verbraucherclouddienste auf sichere Weise gemeinsam zu nutzen."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e2d77104-d978-46a3-bfea-03ffdf3b61e6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 76f1251dc6abae6f0faadb171d87c23607b03ae1
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2017
---
# <a name="sharing-accounts-with-azure-ad"></a>Freigeben von Konten in Azure AD
## <a name="overview"></a>Übersicht
Es kann vorkommen, dass in Organisationen ein Benutzername und ein Kennwort für mehrere Personen verwendet werden müssen. Dies passiert normalerweise in den folgenden beiden Fällen:

* Beim Zugreifen auf Anwendungen, für die ein eindeutiger Benutzername und ein Kennwort für jeden Benutzer erforderlich sind (hier kann es sich um lokale Apps oder Clouddienste für Verbraucher handeln, z.B. Unternehmenskonten für Social Media).
* Beim Erstellen von Mehrbenutzerumgebungen. Beispielsweise verfügen Sie ggf. über ein einzelnes lokales Konto mit erhöhten Rechten, das für die grundlegende Einrichtung sowie für Verwaltungs- und Wiederherstellungsaktivitäten verwendet wird. Ein Beispiel hierfür ist das lokale Konto „Globaler Administrator“ für Office 365 oder das Stammkonto in Salesforce.

Normalerweise werden diese Konten gemeinsam genutzt, indem die Anmeldeinformationen (Benutzername und Kennwort) an geeignete Personen verteilt oder an gemeinsam genutzten Orten aufbewahrt werden, auf die vertrauenswürdige Personen Zugriff haben.

Das herkömmliche Modell der gemeinsamen Nutzung hat mehrere Nachteile:

* Das Bereitstellen des Zugriffs auf neue Anwendungen erfordert die Verteilung von Anmeldeinformationen an jeden, der Zugriff benötigt.
* Jede freigegebene Anwendung erfordert möglicherweise einen eigenen, eindeutigen Satz gemeinsam genutzter Anmeldeinformationen, sodass die Benutzer mehrere verschiedene Anmeldeinformationen kennen müssen. Wenn Benutzer sich viele Anmeldeinformationen merken müssen, steigt das Risiko, dass auf risikoreiche Methoden zurückgegriffen wird (z.B. das Aufschreiben von Kennwörtern).
* Es ist nicht erkennbar, wer genau Zugriff auf eine Anwendung hat.
* Es ist nicht erkennbar, wer auf eine Anwendung *zugegriffen* hat.
* Wenn Sie den Zugriff auf eine Anwendung entfernen möchten, müssen die Anmeldeinformationen aktualisiert und erneut an alle Benutzer verteilt werden, die Zugriff benötigen.

## <a name="azure-active-directory-account-sharing"></a>Azure Active Directory-Kontofreigabe
Azure AD bietet einen neuen Ansatz für die Verwendung von gemeinsam genutzten Konten, der diese Nachteile beseitigt.

Der Azure AD-Administrator konfiguriert, auf welche Anwendungen ein Benutzer zugreifen kann, indem er den Zugriffsbereich verwendet und die Art von einmaligem Anmelden auswählt, die für diese Anwendung am besten geeignet ist. Einer dieser Typen, die *kennwortbasierte einmalige Anmeldung*, ermöglicht Azure AD, während des Anmeldevorgangs für die Anwendung als eine Art Zwischenhändler (Broker) aufzutreten.

Benutzer melden Sie sich einmal mit ihrem Organisationskonto an. Dieses Konto ist dasselbe Konto, das Benutzer regelmäßig zum Zugreifen auf den Desktop oder ihre E-Mails verwenden. Sie können nur diejenigen Anwendungen ermitteln und auf sie zugreifen, die ihnen zugewiesen wurden. Bei gemeinsam genutzten Konten kann diese Liste von Anwendungen beliebig viele gemeinsam genutzte Anmeldeinformationen enthalten. Endbenutzer müssen sich die unterschiedlichen Konten nicht mehr merken oder notieren.

Gemeinsam genutzte Konten verbessern nicht nur den Überblick, sondern auch die Sicherheit. Benutzer mit Berechtigungen zur Verwendung der Anmeldeinformationen sehen das gemeinsame Kennwort nicht, sondern erhalten vielmehr die Berechtigung, es als Teil des orchestrierten Authentifizierungsablaufs nutzen zu dürfen. Bei einigen Kennwort-SSO-Anwendungen haben Sie die Möglichkeit, Azure AD zum Durchführen eines regelmäßigen Rollovers (Aktualisierung) von Kennwörtern zu verwenden. Für das System werden lange, komplexe Kennwörter genutzt, um die Kontosicherheit zu erhöhen. Der Administrator kann Zugriffsrechte für bestimmte Anwendungen mühelos gewähren oder widerrufen. Er weiß, wer Zugriff auf das Konto hat und wer bereits darauf zugegriffen hat.

Azure AD unterstützt freigegebene Konten für alle lizenzierten Benutzer von Enterprise Mobility Suite (EMS) Premium oder Basic für alle Arten des einmaligen Anmeldens per Kennwort. Sie können Konten für Tausende im Katalog befindliche, bereits integrierte Anwendungen gemeinsam nutzen und mithilfe [benutzerdefinierter SSO-Apps](active-directory-enterprise-apps-manage-sso.md)eigene Anwendungen mit Kennwortauthentifizierung hinzufügen.

Zu den Azure AD-Features zur gemeinsamen Nutzung von Konten gehören folgende Funktionen:

* [Einmaliges Anmelden per Kennwort](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Agent für einmaliges Anmelden per Kennwort
* [Gruppenzuweisung](active-directory-accessmanagement-self-service-group-management.md)
* Apps mit benutzerdefinierten Kennwörtern
* [Dashboard für Anwendungsnutzung/Berichte](active-directory-passwords-get-insights.md)
* Zugriffsportale für Endbenutzer
* [Anwendungsproxy](active-directory-application-proxy-get-started.md)
* [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Gemeinsames Nutzen eines Kontos
Folgende Schritte sind erforderlich, um Azure AD zum Freigeben eines Kontos zu verwenden:

* Hinzufügen einer Anwendung aus dem [App-Katalog](https://azure.microsoft.com/marketplace/active-directory/) oder einer [benutzerdefinierten Anwendung](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)
* Konfigurieren der Anwendung für einmaliges Anmelden per Kennwort (SSO)
* Verwenden einer [gruppenbasierten Zuweisung](active-directory-accessmanagement-group-saasapps.md) mit der Option „Gemeinsam genutzte Anmeldeinformationen“
* Optional: In einigen Anwendungen wie Facebook, Twitter oder LinkedIn lässt sich die Option zur automatisierten Kennwortaktualisierung aktivieren: [Azure AD automated password roll-over for Facebook, Twitter and LinkedIn now in preview!](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)

Mithilfe von Azure AD lassen sich gemeinsam genutzte Konten durch Multi-Factor Authentication (MFA) sicherer gestalten (siehe [Sichern von Anwendungen mit Azure AD](../multi-factor-authentication/multi-factor-authentication-get-started.md)), und Sie können die Verwaltungsrechte über den Zugriff auf die Anwendung mithilfe der [Azure AD Self-Service](active-directory-accessmanagement-self-service-group-management.md)-Gruppenverwaltung delegieren.

## <a name="related-articles"></a>Verwandte Artikel
* [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)
* [Schützen von Apps durch bedingten Zugriff](active-directory-conditional-access-azure-portal.md)
* [Self-Service-Gruppenverwaltung/SSAA](active-directory-accessmanagement-self-service-group-management.md)

