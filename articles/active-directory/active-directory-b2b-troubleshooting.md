---
title: "Problembehandlung für die Azure Active Directory B2B-Zusammenarbeit | Microsoft-Dokumentation"
description: "Lösungen für häufig auftretende Probleme bei der Azure Active Directory B2B-Zusammenarbeit"
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/25/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 588e154d35fda539ac6ee8803ed96e6cd9a3d1df
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/02/2018
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Problembehandlung für die Azure Active Directory B2B-Zusammenarbeit

Hier finden Sie Lösungen für häufig auftretende Probleme bei der Azure Active Directory B2B-Zusammenarbeit.


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Ich habe einen externen Benutzer hinzugefügt, dieser wird aber im globalen Adressbuch oder in der Personenauswahl nicht angezeigt.

Falls externe Benutzer in der Liste nicht angezeigt werden: Es kann einige Minuten dauern, bis das Objekt repliziert wurde.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Ein B2B-Gastbenutzer wird in der SharePoint Online/OneDrive-Personenauswahl nicht angezeigt. 
 
Die Möglichkeit, in der SharePoint Online-Personenauswahl (SPO) nach vorhandenen Gastbenutzern zu suchen, ist dem Verhalten in älteren Versionen entsprechend standardmäßig deaktiviert.

Sie können dieses Feature mit der Einstellung „ShowPeoplePickerSuggestionsForGuestUsers“ auf Mandanten- und Websitesammlungsebene aktivieren. Sie können dieses Feature mithilfe der Cmdlets „Set-SPOTenant“ und „Set-SPOSite“ festlegen, damit Mitglieder das Verzeichnis nach allen vorhandenen Gastbenutzern durchsuchen können. Änderungen im Mandantenbereich wirken sich nicht auf bereits bereitgestellte SPO-Websites aus.

## <a name="invitations-have-been-disabled-for-directory"></a>Einladungen wurden für das Verzeichnis deaktiviert.

Wenn Sie darüber informiert werden, dass Sie nicht über die Berechtigung verfügen, Benutzer einzuladen, überprüfen Sie unter „Benutzereinstellungen“, ob Ihr Benutzerkonto für die Einladung externer Benutzer autorisiert ist.

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

Wenn Sie diese Einstellungen kürzlich geändert oder einem Benutzer die Rolle „Gasteinladender“ zugewiesen haben, kann es 15–60 Minuten dauern, bis die Änderungen wirksam werden.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>Der von mir eingeladene Benutzer erhält beim Einlösen eine Fehlermeldung.

Häufige Fehler sind z.B. folgende:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>Der Administrator des Eingeladenen hat die Option deaktiviert, Benutzer im Mandanten zu erstellen, die per E-Mail verifiziert werden.

Dieser Fehler kann auftreten, wenn Benutzer eingeladen werden, deren Organisation Azure Active Directory verwenden, das Konto des entsprechenden Benutzers aber dort nicht vorhanden ist (Benutzer ist z.B. in Azure AD für contoso.com nicht vorhanden). Der Administrator von contoso.com hat möglicherweise eine Richtlinie festgelegt, die das Erstellen von Benutzern verhindert. Der Benutzer muss sich bei seinem Administrator informieren, ob externe Benutzer zulässig sind. Der Administrator des externen Benutzers muss ggf. per E-Mail verifizierte Benutzer in seiner Domäne zulassen (Informationen dazu finden Sie in diesem [Artikel](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) zum Zulassen per E-Mail verifizierter Benutzer).

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Ein Benutzer ist in einer Verbunddomäne noch nicht vorhanden.

Wenn Sie die Verbundauthentifizierung verwenden und der Benutzer noch nicht in Azure Active Directory vorhanden ist, kann dieser Benutzer nicht eingeladen werden.

Um dieses Problem zu lösen, muss der Administrator des externen Benutzers das Benutzerkonto mit Azure Active Directory synchronisieren.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Wie wird „\#“ – normalerweise ein ungültiges Zeichen – in Azure AD synchronisiert?

„\#“ ist ein reserviertes Zeichen in UPNs für Azure AD B2B-Zusammenarbeit oder externe Benutzer, da das eingeladene Konto user@contoso.com zu „user_contoso.com#EXT@fabrikam.onmicrosoft.com“ wird. Aus diesem Grund dürfen sich lokale UPNs mit \# nicht im Azure-Portal anmelden. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Ich erhalte eine Fehlermeldung, wenn ich externe Benutzer zu einer synchronisierten Gruppe hinzufüge.

Externe Benutzer können nur zu „zugewiesenen“ Gruppen oder „Sicherheitsgruppen“ hinzugefügt werden, nicht zu lokal verwalteten Gruppen.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Mein externer Benutzer hat keine E-Mail zur Einlösung erhalten.

Der Eingeladene sollte seinen Spamfilter überprüfen oder sich mit seinem ISP in Verbindung setzen, um sicherzustellen, dass die folgende Adresse zulässig ist: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Die benutzerdefinierte Nachricht wird nicht immer in Einladungsnachrichten eingefügt.

Zur Einhaltung von Datenschutzgesetzen fügen unsere APIs in den folgenden Situationen keine benutzerdefinierten Meldungen in die E-Mail-Einladung ein:

- Der einladende Benutzer besitzt keine E-Mail-Adresse im einladenden Mandanten.
- Ein AppService-Prinzipal sendet die Einladung.

Wenn dies für Sie ein wichtiges Szenario ist, können Sie das Senden von E-Mail-Einladungen durch unsere API unterdrücken und sie über ein E-Mail-Verfahren Ihrer Wahl senden. Vergewissern Sie sich unbedingt bei der Rechtsabteilung Ihrer Organisation, dass auf diese Weise gesendete E-Mail-Nachrichten auch den Datenschutzgesetzen entsprechen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zur Azure AD B2B-Zusammenarbeit:

* [Was ist die Azure AD B2B-Zusammenarbeit?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Wie fügen Azure Active Directory-Administratoren B2B-Zusammenarbeitsbenutzer hinzu?](active-directory-b2b-admin-add-users.md)
* [Wie fügen Information-Worker B2B-Zusammenarbeitsbenutzer hinzu?](active-directory-b2b-iw-add-users.md)
* [Die Elemente der Einladungs-E-Mail von B2B-Zusammenarbeit](active-directory-b2b-invitation-email.md)
* [B2B-Zusammenarbeit: Einlösen von Einladungen](active-directory-b2b-redemption-experience.md)
* [Lizenzierung der Azure AD B2B-Zusammenarbeit](active-directory-b2b-licensing.md)
* [Häufig gestellte Fragen zur Azure Active Directory B2B-Zusammenarbeit](active-directory-b2b-faq.md)
* [Azure Active Directory B2B-Zusammenarbeit: API und Anpassung](active-directory-b2b-api.md)
* [Multi-Factor Authentication für Benutzer der B2B-Zusammenarbeit](active-directory-b2b-mfa-instructions.md)
* [Hinzufügen von Benutzern der B2B-Zusammenarbeit ohne Einladung](active-directory-b2b-add-user-without-invite.md)
* [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)
