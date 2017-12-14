---
title: 'Azure Active Directory B2B-Zusammenarbeit: API und Anpassung | Microsoft-Dokumentation'
description: "Die Azure Active Directory B2B-Zusammenarbeit ermöglicht Geschäftspartnern den gezielten Zugriff auf Ihre Unternehmensanwendungen und unterstützt so Ihre unternehmensübergreifenden Beziehungen."
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/11/2017
ms.author: sasubram
ms.openlocfilehash: 9e1dcd38e7c05a234e4b8d7a6190770d813a227f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory B2B-Zusammenarbeit: API und Anpassung

Viele Kunden haben uns berichtet, dass sie den Einladungsprozess auf die Anforderungen ihres Unternehmens anpassen möchten. Mit unserer API können Sie genau das tun. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Funktionen der Einladungs-API
Die API bietet die folgenden Funktionen:

1. Laden Sie einen externen Benutzer mit einer *beliebigen* E-Mail-Adresse ein.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Legen Sie fest, an welche URL Benutzer weitergeleitet werden, nachdem sie Ihre Einladung angenommen haben.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Senden Sie die Standardeinladung über uns.

    ```
    "sendInvitationMessage": true
    ```

  Fügen Sie eine anpassbare Nachricht an den Empfänger hinzu.

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. Sie können Benutzer auf Cc setzen, die Sie über die Einladung dieses Benutzers informieren möchten.

5. Sie können Ihren Einladungs- und Onboardingworkflow auch vollständig benutzerdefiniert anpassen, indem Sie Benachrichtigungen nicht über Azure AD senden.

    ```
    "sendInvitationMessage": false
    ```

  In diesem Fall gibt die API eine Einlösungs-URL zurück, die Sie in eine E-Mail-Vorlage, eine Sofortnachricht oder eine andere Verteilungsmethode Ihrer Wahl einbetten können.

6. Wenn Sie Administrator sind, können Sie den Benutzer als Mitglied einladen.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Autorisierungsmodell
Die API kann in folgenden Autorisierungsmodi ausgeführt werden:

### <a name="app--user-mode"></a>Modus „App und Benutzer“
In diesem Modus benötigt der jeweilige Benutzer der API die Berechtigungen, B2B-Einladungen zu erstellen.

### <a name="app-only-mode"></a>Reiner App-Modus
Im reinen App-Modus benötigt die App die Berechtigungsbereiche „User.ReadWrite.All“ oder „Directory.ReadWrite.All“, damit die Einladung erfolgreich ist.

Weitere Informationen finden Sie unter https://graph.microsoft.io/docs/authorization/permission_scopes.


## <a name="powershell"></a>PowerShell
Sie können jetzt PowerShell verwenden, um externe Benutzer ganz einfach in eine Organisation einzuladen und dieser hinzuzufügen. Erstellen Sie eine Einladung mit dem Cmdlet:

```
New-AzureADMSInvitation
```

Sie können die folgenden Optionen verwenden:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

Zudem können Sie sich die Referenz für die Einladungs-API unter [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) ansehen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zur Azure AD B2B-Kollaboration:

* [Was ist die Azure AD B2B-Zusammenarbeit?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Wie fügen Azure Active Directory-Administratoren B2B-Zusammenarbeitsbenutzer hinzu?](active-directory-b2b-admin-add-users.md)
* [Wie fügen Information-Worker B2B-Zusammenarbeitsbenutzer hinzu?](active-directory-b2b-iw-add-users.md)
* [Die Elemente der Einladungs-E-Mail von B2B-Zusammenarbeit](active-directory-b2b-invitation-email.md)
* [B2B-Zusammenarbeit: Einlösen von Einladungen](active-directory-b2b-redemption-experience.md)
* [Lizenzierung von Azure AD B2B-Zusammenarbeit](active-directory-b2b-licensing.md)
* [Problembehandlung für die Azure Active Directory B2B-Zusammenarbeit](active-directory-b2b-troubleshooting.md)
* [Häufig gestellte Fragen zur Azure Active Directory B2B-Zusammenarbeit](active-directory-b2b-faq.md)
* [Multi-Factor Authentication für Benutzer der B2B-Zusammenarbeit](active-directory-b2b-mfa-instructions.md)
* [Hinzufügen von Benutzern der B2B-Zusammenarbeit ohne Einladung](active-directory-b2b-add-user-without-invite.md)
* [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)
