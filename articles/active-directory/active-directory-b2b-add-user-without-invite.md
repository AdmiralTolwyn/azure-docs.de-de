---
title: "Hinzufügen von Benutzern der B2B-Zusammenarbeit zu Azure Active Directory ohne Einladung | Microsoft-Dokumentation"
description: "Sie können festlegen, dass ein Gastbenutzer Ihrem Azure AD andere Gastbenutzer hinzufügen kann, ohne dass eine Einladung in der Azure Active Directory B2B-Zusammenarbeit erforderlich ist."
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
ms.date: 03/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 68d828b8fb23f7074be5fe691a4250d0fa653ab7
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/02/2018
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Hinzufügen von Gastbenutzern der B2B-Zusammenarbeit ohne Einladung

Sie können festlegen, dass ein Benutzer, z.B. ein Mitarbeiter einer Partnerorganisation, Ihrer Organisation Benutzer der Partnerorganisation hinzufügen kann, ohne dass dazu Einladungen erforderlich sind. Dazu müssen Sie dem entsprechenden Benutzer lediglich in dem Verzeichnis, das Sie für die Partnerorganisation verwenden, Enumerationsberechtigungen zuweisen. 

Die Erteilung dieser Berechtigungen empfiehlt sich in den folgenden Fällen:

1. Ein Benutzer in der Hostorganisation (z.B. WoodGrove) lädt einen Benutzer aus der Partnerorganisation (z.B. Sam@litware.com) als Gast ein.
2. Der Administrator der Hostorganisation legt Richtlinien fest, die es Sam erlauben, weitere Benutzer aus der Partnerorganisation (Litware) zu identifizieren und hinzuzufügen.
3. Jetzt kann Sam dem Verzeichnis sowie den Gruppen und Anwendungen von WoodGrove weitere Benutzer von Litware hinzufügen, ohne dass Einladungen eingelöst werden müssen. Wenn Sam über entsprechende Enumerationsberechtigungen in Litware verfügt, erfolgt dies automatisch.

### <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zur Azure AD B2B-Zusammenarbeit:

* [Was ist die Azure AD B2B-Zusammenarbeit?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Wie fügen Azure Active Directory-Administratoren B2B-Zusammenarbeitsbenutzer hinzu?](active-directory-b2b-admin-add-users.md)
* [Wie fügen Information-Worker B2B-Zusammenarbeitsbenutzer hinzu?](active-directory-b2b-iw-add-users.md)
* [Die Elemente der Einladungs-E-Mail von B2B-Zusammenarbeit](active-directory-b2b-invitation-email.md)
* [B2B-Zusammenarbeit: Einlösen von Einladungen](active-directory-b2b-redemption-experience.md)
* [Lizenzierung der Azure AD B2B-Zusammenarbeit](active-directory-b2b-licensing.md)
* [Problembehandlung für die Azure Active Directory B2B-Zusammenarbeit](active-directory-b2b-troubleshooting.md)
* [Häufig gestellte Fragen zur Azure Active Directory B2B-Zusammenarbeit](active-directory-b2b-faq.md)
* [Azure Active Directory B2B-Zusammenarbeit: API und Anpassung](active-directory-b2b-api.md)
* [Multi-Factor Authentication für Benutzer der B2B-Zusammenarbeit](active-directory-b2b-mfa-instructions.md)
* [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)