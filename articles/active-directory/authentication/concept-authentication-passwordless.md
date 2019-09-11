---
title: Kennwortlose Azure Active Directory-Anmeldung (Vorschauversion)
description: Kennwortloses Anmelden bei Azure AD mithilfe von FIDO2-Sicherheitsschlüsseln oder der Microsoft Authenticator-App (Vorschauversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc633780d8b816d8fc2e313bb1955a5719979efe
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240870"
---
# <a name="what-is-passwordless"></a>Was bedeutet „kennwortlos“?

Die Multi-Factor Authentication (MFA) ist eine großartige Möglichkeit, Ihre Organisation zu schützen, aber den Benutzer ist es lästig, sich auch noch ihre Kennwörter merken zu müssen. Kennwortlose Authentifizierungsmethoden sind bequemer, da das Kennwort entfällt und durch etwas ersetzt wird, das Sie haben, plus etwas, das Sie sind oder das Sie wissen.

|   | Etwas, das Sie haben | Etwas, das Sie wissen |
| --- | --- | --- |
| Kennwortlos | Telefon- oder Sicherheitsschlüssel | Biometrische Daten oder PIN |

Jede Organisation hat unterschiedliche Anforderungen in Bezug auf die Authentifizierung. Für Windows-PCs bietet Microsoft derzeit Windows Hello. Wir fügen den kennwortlosen Optionen die Microsoft Authenticator-App und FIDO2-Sicherheitsschlüssel hinzu.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator-App

Ermöglichen Sie, dass die Telefone Ihrer Mitarbeiter in die kennwortlose Authentifizierungsmethode integriert werden. Möglicherweise verwenden Sie bereits die Microsoft Authenticator-App als komfortable MFA-Option zusätzlich zu einem Kennwort. Aber nun ist sie als kennwortlose Option verfügbar.

![Anmelden bei Microsoft Edge mit der Microsoft Authenticator-App](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Sie verwandelt jedes iOS- oder Android-Handy in eine starke, kennwortlose Anmeldeinformation, indem sie Benutzern ermöglicht, sich bei jeder Plattform oder jedem Browser anzumelden, indem sie eine Benachrichtigung auf ihrem Handy erhalten, eine auf dem Bildschirm angezeigte Nummer mit der auf ihrem Handy abgleichen und dann ihre biometrischen Daten (Berührung oder Gesicht) oder die PIN zur Bestätigung verwenden.

## <a name="fido2-security-keys"></a>FIDO2-Sicherheitsschlüssel

FIDO2-Sicherheitsschlüssel sind eine Phishing-resistente, standardbasierte Methode zur kennwortlosen Authentifizierung, die in jedem Formfaktor verfügbar sein kann. Fast Identity Online (FIDO) ist ein offener Standard für die kennwortlose Authentifizierung. Damit können Benutzer und Organisationen den Standard nutzen, um sich ohne Benutzername oder Kennwort mit einem externen Sicherheitsschlüssel oder einem in ein Gerät integrierten Plattformschlüssel bei ihren Ressourcen anzumelden.

In der Public Preview können sich Mitarbeiter mit externen Sicherheitsschlüsseln bei ihren Azure Active Directory Joined Windows 10-Computern (mit Version 1809 oder höher) anmelden und mit einmaligem Anmelden auf ihre Cloudressourcen zugreifen. Sie können sich auch bei unterstützten Browsern anmelden.

![Anmelden bei Microsoft Edge mit einem Sicherheitsschlüssel](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Obwohl viele Schlüssel von der FIDO Alliance FIDO2-zertifiziert sind, verlangt Microsoft vom Hersteller die Implementierung einiger optionaler Erweiterungen der FIDO2 CTAP-Spezifikation, um maximale Sicherheit und ein optimales Benutzererlebnis zu gewährleisten.

Ein Sicherheitsschlüssel **MUSS** die folgenden Features und Erweiterungen aus dem FIDO2 CTAP-Protokoll implementieren, damit er mit Microsoft kompatibel ist:

| # | Funktion/Erweiterte Vertrauenswürdigkeit | Warum ist diese Funktion oder Erweiterung erforderlich? |
| --- | --- | --- |
| 1 | Residenter Schlüssel | Diese Funktion ermöglicht, dass der Sicherheitsschlüssel portabel ist, wobei Ihre Anmeldeinformationen auf dem Sicherheitsschlüssel gespeichert sind. |
| 2 | Client-PIN | Diese Funktion ermöglicht Ihnen, Ihre Anmeldeinformationen mit einem zweiten Faktor zu schützen und gilt für Sicherheitsschlüssel, die keine Benutzeroberfläche haben. |
| 3 | hmac-secret | Diese Erweiterung stellt sicher, dass Sie sich bei Ihrem Gerät anmelden können, wenn es sich im Offline- oder Flugmodus befindet. |
| 4 | Mehrere Konten per RP | Diese Funktion stellt sicher, dass Sie den gleichen Sicherheitsschlüssel für mehrere Dienste wie Microsoft Account und Azure Active Directory verwenden können. |

Die folgenden Anbieter bieten FIDO2-Sicherheitsschlüssel in verschiedenen Formfaktoren an, die bekanntermaßen mit der kennwortlosen Benutzererfahrung kompatibel sind. Microsoft empfiehlt den Kunden, die Sicherheitseigenschaften dieser Schlüssel zu bewerten, indem sie sich an den Hersteller und die FIDO Alliance wenden.

| Anbieter | Kontakt |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://ensurity.com/contact-us.html](https://ensurity.com/contact-us.html) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

Wenn Sie ein Anbieter sind und möchten, dass Ihr Gerät auf diese Liste gesetzt wird, wenden Sie sich an [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com).

FIDO2-Sicherheitsschlüssel sind eine gute Option für Unternehmen, die sehr sicherheitsbewusst sind, oder deren Mitarbeiter nicht bereit oder in der Lage sind, ihr Telefon als zweiten Faktor zu verwenden, oder bei denen andere entsprechende Szenarien vorliegen.

## <a name="what-scenarios-work-with-the-preview"></a>Welche Szenarien sind für die Vorschau geeignet?

- Administratoren können kennwortlose Authentifizierungsmethoden für ihre Mandanten aktivieren
- Administratoren können die einzelnen Methoden für alle Benutzer oder ausgewählte Benutzer/Gruppen in ihrem Mandanten anwenden
- Endbenutzer können diese kennwortlosen Authentifizierungsmethoden in ihrem Kontoportal registrieren und verwalten
- Endbenutzer können sich mit diesen kennwortlosen Authentifizierungsmethoden anmelden
   - Microsoft Authenticator-App: Funktioniert in Szenarien, in denen die Azure AD-Authentifizierung verwendet wird, einschließlich aller Browser, während der Einrichtung der Windows 10-Out-of-Box-Experience (OOBE) und mit integrierten mobilen Apps unter jedem Betriebssystem.
   - Sicherheitsschlüssel: Funktioniert auf Sperrbildschirm für Windows 10 Version 1809 oder höher und im Internet in unterstützten Browsern wie Microsoft Edge.

## <a name="next-steps"></a>Nächste Schritte

[Aktivieren der kennwortlosen Anmeldung mit FIDO2-Sicherheitsschlüsseln für Ihre Organisation](howto-authentication-passwordless-security-key.md)

[Aktivieren der kennwortlosen Anmeldung per Telefon für Ihre Organisation](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Externe Links

[FIDO Alliance](https://fidoalliance.org/)

[FIDO2 CTAP-Spezifikation](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
