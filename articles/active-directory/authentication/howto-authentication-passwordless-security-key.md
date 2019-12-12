---
title: Kennwortlose Anmeldung mit Sicherheitsschlüsseln (Vorschau) – Azure Active Directory
description: Aktivieren der kennwortlosen Anmeldung mit FIDO2-Sicherheitsschlüsseln bei Azure AD (Vorschauversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 826b8e923575db3d6c6aee7ead230f87f1efb50e
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848441"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln (Vorschauversion)

Sicherheitsschlüssel bieten Unternehmen, die aktuell Kennwörter verwenden und über eine freigegebene PC-Umgebung verfügen, eine nahtlose Möglichkeit für Mitarbeiter, sich ohne Eingabe eines Benutzernamens oder Kennworts zu authentifizieren. Sicherheitsschlüssel ermöglichen den Mitarbeitern eine bessere Produktivität und bieten eine höhere Sicherheit.

In diesem Dokument liegt der Schwerpunkt auf der Aktivierung der auf Sicherheitsschlüsseln basierenden kennwortlosen Authentifizierung. Am Ende dieses Artikels können Sie sich mit Ihrem Azure AD-Konto mithilfe eines FIDO2-Sicherheitsschlüssels bei webbasierten Anwendungen anmelden.

|     |
| --- |
| FIDO2-Sicherheitsschlüssel sind eine öffentliche Previewfunktion von Azure Active Directory. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="requirements"></a>Requirements (Anforderungen)

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [Kombinierte Registrierung von Sicherheitsinformationen (Vorschauversion)](concept-registration-mfa-sspr-combined.md)
- Kompatible [FIDO2-Sicherheitsschlüssel](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN erfordert Windows 10, Version 1809 oder höher**

Um Sicherheitsschlüssel für die Anmeldung bei Web-Apps und-Diensten verwenden zu können, benötigen Sie einen Browser, der das WebAuthN-Protokoll unterstützt. Hierzu zählen Microsoft Edge, Chrome, Firefox und Safari.

## <a name="prepare-devices-for-preview"></a>Vorbereiten von Geräten für die Vorschauversion

Auf den von Ihnen verwendeten Geräten muss Windows 10 (Version 1809 oder höher) ausgeführt werden. Hierfür eignet sich besonders Windows 10 (Version 1903 oder höher).

## <a name="enable-passwordless-authentication-method"></a>Aktivieren von Methoden zur kennwortlosen Authentifizierung

### <a name="enable-the-combined-registration-experience"></a>Aktivieren der kombinierten Registrierung

Die Registrierungsfunktionen für Methoden zur kennwortlosen Authentifizierung sind von der Vorschauversion der kombinierten Registrierung abhängig. Führen Sie die im Artikel [Aktivieren der kombinierten Registrierung von Sicherheitsinformationen (Vorschauversion)](howto-registration-mfa-sspr-combined.md) aufgeführten Schritte aus, um die Vorschauversion der kombinierten Registrierung zu aktivieren.

### <a name="enable-fido2-security-key-method"></a>Aktivieren der Methode „FIDO2-Sicherheitsschlüssel“

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Authentifizierungsmethoden** > **Authentifizierungsmethodenrichtlinie (Vorschau)** .
1. Wählen Sie unter der Methode **FIDO2-Sicherheitsschlüssel** die folgenden Optionen aus:
   1. **Aktivieren**: „Ja“ oder „Nein“
   1. **Ziel**: „Alle Benutzer“ oder „Benutzer auswählen“
1. **Speichern** Sie die Konfiguration.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Benutzerregistrierung und Verwaltung von FIDO2-Sicherheitsschlüsseln

1. Navigieren Sie zu [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Melden Sie sich an, falls Sie dies noch nicht getan haben.
1. Klicken Sie auf **Sicherheitsinformation**.
   1. Wenn der Benutzer bereits mindestens eine mehrstufige Authentifizierungsmethode registriert hat, kann er sofort einen FIDO2-Sicherheitsschlüssel registrieren.
   1. Wenn der Benutzer keine mehrstufige Authentifizierungsmethode registriert hat, muss er eine hinzufügen.
1. Fügen Sie einen FIDO2-Sicherheitsschlüssel hinzu, indem Sie auf **Methode hinzufügen** klicken und dann **Sicherheitsschlüssel** auswählen.
1. Wählen Sie **USB-Gerät** oder **NFC-Gerät** aus.
1. Halten Sie Ihren Schlüssel bereit, und wählen Sie **Weiter** aus.
1. Im daraufhin angezeigten Feld wird der Benutzer aufgefordert, eine PIN für den Sicherheitsschlüssel zu erstellen/einzugeben und dann die erforderliche Geste für den Schlüssel biometrisch oder per Touch auszuführen.
1. Der Benutzer gelangt wieder zurück zur Oberfläche für die kombinierte Registrierung und wird aufgefordert, einen aussagekräftigen Namen für den Schlüssel anzugeben, damit der Benutzer diesen von den anderen unterscheiden kann. Klicken Sie auf **Weiter**.
1. Klicken Sie auf **Fertig**, um den Vorgang abzuschließen.

## <a name="sign-in-with-passwordless-credential"></a>Anmelden mit kennwortlosen Anmeldeinformationen

Im nachfolgenden Beispiel hat der Benutzer bereits einen FIDO2-Sicherheitsschlüssel bereitgestellt. Der Benutzer kann sich im Web mit seinem FIDO2-Sicherheitsschlüssel in einem unterstützten Browser unter Windows 10 (Version 1809 oder höher) anmelden.

![Anmeldung mit Sicherheitsschlüssel in Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Problembehandlung und Feedback

Wenn Sie Feedback geben möchten oder Probleme beim Anzeigen der Vorschau dieses Features auftreten, teilen Sie uns dies über die Windows-Feedback-Hub-App mit.

1. Starten Sie **Feedback-Hub**, und stellen Sie sicher, dass Sie angemeldet sind.
1. Senden Sie Ihr Feedback unter der folgenden Kategorisierung:
   1. Kategorie: Sicherheit und Datenschutz
   1. Unterkategorie: FIDO
1. Wenn Sie Protokolle erfassen möchten, verwenden Sie die folgende Option: **Problem reproduzieren**

## <a name="known-issues"></a>Bekannte Probleme

### <a name="security-key-provisioning"></a>Bereitstellung von Sicherheitsschlüsseln

Die Administratorbereitstellung und Bereitstellungsaufhebung von Sicherheitsschlüsseln ist in der öffentlichen Vorschauversion nicht verfügbar.

### <a name="upn-changes"></a>Änderungen des Benutzerprinzipalnamens

Wenn der Benutzerprinzipalname eines Benutzers geändert wird, können Sie die FIDO2-Sicherheitsschlüssel nicht mehr ändern, um dies zu berücksichtigen. Die Lösung besteht darin, das Gerät zurückzusetzen, und der Benutzer muss seine FIDO2-Sicherheitsschlüssel erneut registrieren.

## <a name="next-steps"></a>Nächste Schritte

[Anmelden mit FIDO2-Sicherheitsschlüsseln bei Windows 10-Geräten](howto-authentication-passwordless-security-key-windows.md)

[Aktivieren der FIDO2-Authentifizierung für lokale Ressourcen](howto-authentication-passwordless-security-key-on-premises.md)

[Erfahren Sie mehr über die Geräteregistrierung](../devices/overview.md)

[Erfahren Sie mehr über Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
