---
title: 'Azure Active Directory B2C: Benutzerdefinierte Richtlinien | Microsoft-Dokumentation'
description: Ein Thema zu benutzerdefinierten Azure Active Directory B2C-Richtlinien
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/04/2017
ms.author: davidmu
ms.openlocfilehash: 22d34ac4128da1d1a9f20619aec2aaccc2425a21
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32138916"
---
# <a name="azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C: Benutzerdefinierte Richtlinien

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>Was sind benutzerdefinierte Richtlinien?

Benutzerdefinierte Richtlinien sind Konfigurationsdateien, die das Verhalten Ihres Azure AD B2C-Mandanten definieren. Während **integrierte Richtlinien** im Azure AD B2C-Portal für die gängigsten Identitätsaufgaben vordefiniert sind, können benutzerdefinierte Richtlinien zur Durchführung einer nahezu unbegrenzten Anzahl von Aufgaben vollumfänglich von einem Identitätsentwickler bearbeitet werden. Lesen Sie weiter, um festzustellen, ob benutzerdefinierte Richtlinien für Sie und Ihr Identitätsszenario geeignet sind.

## <a name="comparing-built-in-policies-and-custom-policies"></a>Vergleich von integrierten und benutzerdefinierten Richtlinien

| | Integrierte Richtlinien | Benutzerdefinierte Richtlinien |
|-|-------------------|-----------------|
|Zielbenutzer | Alle App-Entwickler mit oder ohne Identitätskenntnissen | Identitätsexperten: Systemintegratoren, Berater und interne Identitätsteams. Sie sind mit OpenIDConnect-Flows sowie Identitätsanbietern und anspruchsbasierten Authentifizierungen vertraut. |
|Konfigurationsmethode | Azure-Portal mit übersichtlicher Benutzeroberfläche | Bearbeiten Sie direkt XML-Dateien, und laden Sie sie anschließend in das Azure-Portal hoch. |
|Anpassung der Benutzeroberfläche | Vollständige Anpassung der Benutzeroberfläche, einschließlich Unterstützung für HTML, CSS und Javascript (erfordert benutzerdefinierte Domänen)<br><br>Unterstützung mehrerer Sprachen mit benutzerdefinierten Zeichenfolgen | identisch |
| Anpassung von Attributen | Standard- und benutzerdefinierte Attribute | identisch |
|Token- und Sitzungsverwaltung | Benutzerdefinierte Token und mehrere Sitzungsoptionen | identisch |
|Identitätsanbieter| **Heute**: vordefinierter lokaler, Social Media-Anbieter<br><br>**Zukünftig**: standardbasierte OIDC-, SAML-, OAuth-Protokolle | **Heute**: standardbasierte OIDC-, OAUTH-, SAML-Protokolle<br><br>**Zukunft**: WsFed |
|Identitätsaufgaben (Beispiele) | Registrierung oder Anmeldung mit lokalen Konten und vielen Konten für soziale Netzwerke<br><br>Self-Service-Kennwortzurücksetzung<br><br>Profilbearbeitung<br><br>Multi-Factor Authentication-Szenarien<br><br>Anpassung von Tokens und Sitzungen<br><br>Zugriffstoken-Flows | Führen Sie dieselben Aufgaben wie bei integrierten Richtlinien mit benutzerdefinierten Identitätsanbietern durch, oder verwenden Sie benutzerdefinierte Bereiche.<br><br>Stellen Sie Benutzer bei der Registrierung in einem anderen System bereit.<br><br>Senden Sie mit Ihrem eigenen E-Mail-Dienstanbieter eine Willkommens-E-Mail.<br><br>Verwenden Sie einen Benutzerspeicher außerhalb von B2C.<br><br>Überprüfen Sie die von Benutzern bereitgestellten Informationen mit einem vertrauenswürdigen System über eine API. |

## <a name="policy-files"></a>Richtliniendateien

Eine benutzerdefinierte Richtlinie wird als eine oder mehrere XML-formatierte Dateien dargestellt, die aufeinander in einer hierarchischen Kette verweisen. Die XML-Elemente definieren u.a. Anspruchsschema, Anspruchstransformationen, Inhaltsdefinitionen, Anspruchsanbieter oder technische Profile und Schritte zur Orchestrierung von User Journeys.

Wir empfehlen, drei Typen von Richtliniendateien zu verwenden:

- **Eine BASISDATEI**, die den Großteil der Definitionen enthält und für die Azure ein vollständiges Beispiel zur Verfügung stellt.  Es wird empfohlen, dass Sie eine minimale Anzahl von Änderungen an dieser Datei vornehmen, um die Problembehandlung und eine langfristige Verwaltung Ihrer Richtlinien zu unterstützen.
- **Eine Erweiterungsdatei**, die eindeutige Konfigurationsänderungen für Ihren Mandanten enthält.
- **Eine Datei der vertrauenden Seite**, die die einzige aufgabenorientierte Datei darstellt, die direkt von der Anwendung oder dem Dienst (auch als „vertrauenden Seite“ bezeichnet) aufgerufen wird.  Weitere Informationen finden Sie im Artikel zu Richtliniendateidefinitionen.  Jede eindeutige Aufgabe erfordert eine eigene Datei der vertrauenden Seite und die Anzahl kann abhängig von den Branding-Anforderungen die Gesamtanzahl der Anwendungen multipliziert mit der Gesamtanzahl von Anwendungsfällen betragen.


Integrierte Richtlinien in Azure AD B2C folgen dem oben dargestellten 3-Dateien-Muster, der Entwickler sieht jedoch nur die Datei der vertrauenden Seite, während das Portal Änderungen an der Erweiterungsdatei im Hintergrund vornimmt.

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>Wichtige Konzepte für die Verwendung benutzerdefinierter Richtlinien

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

CIAM-Dienst (Customer Identity and Access Management) von Azure. Der Dienst umfasst Folgendes:

1. Ein Benutzerverzeichnis in Form eines speziellen Azure Active Directory, das über Microsoft Graph aufgerufen werden kann und Benutzerdaten für lokale und Verbundkonten enthält. 
2. Zugriff auf das **Identity Experience Framework**, die die Vertrauensstellung zwischen Benutzern und Entitäten koordiniert und Ansprüche zwischen ihnen zur Durchführung von Identitäts-/Zugriffsverwaltungsaufgaben weiterleitet 
3. Ein Sicherheitstokendienst (STS), der ID-Tokens ausstellt, Tokens aktualisiert, auf Tokens (und entsprechende SAML-Assertionen) zugreift und diese zum Schutz von Ressourcen überprüft.

Azure AD B2C interagiert nacheinander mit Identitätsanbietern, Benutzern, anderen Systemen und dem lokalen Benutzerverzeichnis zur Durchführung einer Identitätsaufgabe (z.B. Anmelden eines Benutzers, Registrieren eines neuen Benutzers, Zurücksetzen von Kennwörtern). Die zugrunde liegende Plattform, die die Vertrauensstellung zwischen mehreren Seiten herstellt und diese Schritte ausführt, wird als Identity Experience Framework bezeichnet. Eine Richtlinie (auch als „User Journey“ oder „Vertrauensframeworkrichtlinie“ bezeichnet) definiert explizit die Akteure, Aktionen, Protokolle und die Abfolge der durchzuführenden Schritte.

### <a name="identity-experience-framework"></a>Identity Experience Framework

Dies ist eine vollständig konfigurierbare, richtliniengesteuerte und cloudbasierte Azure-Plattform, die die Vertrauensstellung zwischen Entitäten (allgemein Anspruchsanbietern) in Standardprotokollformaten (z.B. OpenIDConnect, OAuth, SAML, WSFed) sowie einigen nicht standardmäßigen Formaten (z.B. REST-API-basierter Austausch von Systemansprüchen) koordiniert. Die I2E sorgt für benutzerfreundliche White-Label-Erfahrungen, die HTML, CSS und JavaScript unterstützen.  Derzeit ist das Identity Experience Framework nur im Rahmen des Azure AD B2C-Diensts verfügbar und mit Prioritäten für Aufgaben in Zusammenhang mit CIAM versehen.

### <a name="built-in-policies"></a>Integrierte Richtlinien

Dies sind vordefinierte Konfigurationsdateien, die das Verhalten von Azure AD B2C zur Durchführung der am häufigsten verwendeten Identitätsaufgaben (d.h. Registrierung von Benutzern, Anmeldungen, Zurücksetzung von Kennwörtern) steuern und mit vertrauenswürdigen Empfängern interagieren, deren Beziehung ebenfalls in Azure AD B2C vordefiniert ist (z.B. Facebook-Identitätsanbieter, LinkedIn, Microsoft-Konto, Google-Konten).  In Zukunft können integrierte Richtlinien auch die Anpassung von Identitätsanbietern ermöglichen, die sich in der Regel im Enterprise-Bereich wie Azure Active Directory Premium, Active Directory/AD FS, Salesforce-ID-Anbieter etc. befinden.


### <a name="custom-policies"></a>Benutzerdefinierte Richtlinien

Dies sind Konfigurationsdateien, die das Verhalten des Identity Experience Framework in Ihrem Azure AD B2C-Mandanten definieren. Eine benutzerdefinierte Richtlinie kann als eine oder mehrere XML-Dateien aufgerufen werden (siehe Richtliniendateidefinitionen), die vom Identity Experience Framework beim Aufrufen durch eine vertrauende Seite (z.B. eine Anwendung) ausgeführt wird bzw. werden. Benutzerdefinierte Richtlinien können direkt von einem Identitätsentwickler bearbeitet werden, um eine nahe unbegrenzte Anzahl von Aufgaben durchzuführen. Entwickler, die benutzerdefinierte Richtlinien konfigurieren, müssen die vertrauenswürdigen Beziehungen im Detail definieren, um Metadatenendpunkte und genaue Anspruchsaustauschdefinitionen einzuschließen und bei Bedarf zudem Geheimnisse, Schlüssel und Zertifikate durch die einzelnen Identitätsanbieter zu konfigurieren.

## <a name="policy-file-definitions-for-identity-experience-framework-trustframeworks"></a>Richtliniendateidefinitionen für Identity Experience Framework-Vertrauensframeworks

### <a name="policy-files"></a>Richtliniendateien

Eine benutzerdefinierte Richtlinie wird als eine oder mehrere XML-formatierte Dateien dargestellt, die aufeinander in einer hierarchischen Kette verweisen. Die XML-Elemente definieren u.a. Anspruchsschema, Anspruchstransformationen, Inhaltsdefinitionen, Anspruchsanbieter oder technische Profile und Schritte zur Orchestrierung von User Journeys.  Wir empfehlen, drei Typen von Richtliniendateien zu verwenden:

- **Eine BASISDATEI**, die den Großteil der Definitionen enthält und für die Azure ein vollständiges Beispiel zur Verfügung stellt.  Es wird empfohlen, dass Sie eine minimale Anzahl von Änderungen an dieser Datei vornehmen, um die Problembehandlung und eine langfristige Verwaltung Ihrer Richtlinien zu unterstützen.
- **Eine Erweiterungsdatei**, die eindeutige Konfigurationsänderungen für Ihren Mandanten enthält.
- **Eine Datei der vertrauenden Seite**, die die einzige aufgabenorientierte Datei darstellt, die direkt von der Anwendung oder dem Dienst (auch als „vertrauende Seite“ bezeichnet) aufgerufen wird.  Weitere Informationen finden Sie im Artikel zu Richtliniendateidefinitionen.  Jede eindeutige Aufgabe erfordert eine eigene Datei der vertrauenden Seite und die Anzahl kann abhängig von den Branding-Anforderungen die Gesamtanzahl der Anwendungen multipliziert mit der Gesamtanzahl von Anwendungsfällen betragen.

![Richtliniendateitypen](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| Typ der Richtliniendatei | Beispieldateiname | Empfohlene Verwendung | Geerbt von |
|---------------------|--------------------|-----------------|---------------|
| BASIS |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | Enthält das Hauptanspruchsschema, Anspruchstransformationen, Anspruchsanbieter und von Microsoft konfigurierte User Journeys<br><br>Ermöglicht minimale Änderungen an dieser Datei | Keine |
| Erweiterung (EXT) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | Konsolidiert Ihre Änderungen an der BASISDATEI hier<br><br>Geänderte Anspruchsanbieter<br><br>Geänderte User Journeys<br><br>Ihre eigenen benutzerdefinierten Schemadefinitionen | BASISDATEI |
| Vertrauende Seite | B2C_1A_sign_up_sign_in.xml| Tokenform und Sitzungseinstellungen hier ändern| Erweiterungsdatei (EXT) |

### <a name="inheritance-model"></a>Vererbungsmodell

Wenn eine Anwendung die Richtliniendatei der vertrauenden Seite aufruft, fügt das Identity Experience Framework in B2C alle Elemente der BASISDATEI, dann die der ERWEITERUNGEN und zuletzt die der Richtliniendatei der vertrauenden Seite hinzu, um die aktuelle Richtlinie wirksam zusammenzustellen.  Elemente desselben Typs und mit demselben Namen in der Datei der vertrauenden Seite überschreiben jene in den ERWEITERUNGEN, während ERWEITERUNGEN wiederum BASISDATEIEN überschreiben.

**Integrierte Richtlinien** in Azure AD B2C folgen dem oben dargestellten 3-Dateien-Muster, der Entwickler sieht jedoch nur die Datei der vertrauenden Seite, während das Portal Änderungen an der Erweiterungsdatei im Hintergrund vornimmt.  Alle Azure AD B2C nutzen eine gemeinsame BASIS-Richtliniendatei, die unter der Kontrolle des Azure B2C-Teams steht und häufig aktualisiert wird.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte mit benutzerdefinierten Richtlinien](active-directory-b2c-get-started-custom.md)
