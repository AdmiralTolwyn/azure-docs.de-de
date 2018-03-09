---
title: Vergleichen von B2B-Zusammenarbeit und B2C in Azure Active Directory | Microsoft-Dokumentation
description: Was ist der Unterschied zwischen Azure Active Directory B2B-Zusammenarbeit und Azure AD B2C?
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
ms.openlocfilehash: ae3ebdceb65c04b98965f81f52997da457bd7845
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/02/2018
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Vergleichen von B2B-Zusammenarbeit und B2C in Azure Active Directory

Sowohl Azure Active Directory (Azure AD) B2B-Zusammenarbeit als auch Azure AD B2C ermöglichen Ihnen die Arbeit mit externen Benutzern in Azure AD. Aber worin bestehen die Unterschiede?


Funktionen von B2B-Zusammenarbeit |     Eigenständiges Azure AD B2C-Angebot
-------- | --------
Vorgesehen für: Organisationen, die Benutzer einer Partnerorganisation unabhängig vom Identitätsanbieter authentifizieren möchten. | Vorgesehen für: Einladungen für Kunden Ihrer mobilen Apps und Web-Apps (Einzelpersonen, Institutionen oder Organisationen) zu Ihrem Azure AD.
Unterstützte Identitäten: Mitarbeiter mit Geschäfts-, Schul- oder Unikonten, Partner mit Geschäfts-, Schul- oder Unikonten oder beliebige E-Mail-Adressen. Direkter Verbund wird in Kürze unterstützt.  | Unterstützte Identitäten: Endbenutzer mit lokalen Anwendungskonten (E-Mail-Adresse oder Benutzername) oder eine beliebige unterstützte soziale Identität mit direktem Verbund.
Verzeichnis für die Partnerbenutzer: Partnerbenutzer aus der externen Organisation werden in demselben Verzeichnis wie Mitarbeiter verwaltet, aber speziell gekennzeichnet. Sie können auf die gleiche Weise wie Mitarbeiter verwaltet werden, denselben Gruppen hinzugefügt werden usw.  | Verzeichnis für die Kundenbenutzerentitäten: Anwendungsverzeichnis. Wird getrennt vom Verzeichnis der Organisationsmitarbeiter und Partner (sofern vorhanden) verwaltet.
Einmaliges Anmelden (SSO) bei allen verbundenen Azure AD-Apps wird unterstützt. Beispielsweise können Sie den Zugriff auf Office 365 oder lokale Apps und auf andere SaaS-Apps wie z.B. Salesforce oder Workday bereitstellen.  |  Einmaliges Anmelden bei Kunden-Apps innerhalb der Azure AD B2C-Mandanten wird unterstützt. Einmaliges Anmelden bei Office 365 oder anderen Microsoft- und Nicht-Microsoft-SaaS-Apps wird nicht unterstützt.
Partnerlebenszyklus: Wird von der Hostorganisation/einladenden Organisation verwaltet.  | Kundenlebenszyklus: Self-Service oder von der Anwendung verwaltet.
Sicherheitsrichtlinie und Konformität: Wird von der Hostorganisation/einladenden Organisation verwaltet.  | Sicherheitsrichtlinie und Konformität: Wird von der Anwendung verwaltet.
Branding: Das Branding der Hostorganisation/einladenden Organisation wird verwendet.  |    Branding: Wird von der Anwendung verwaltet. Normalerweise wird eher das Produktbranding verwendet, und die Organisation tritt in den Hintergrund.
Weitere Informationen: [Blogbeitrag](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Dokumentation](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | Weitere Informationen: [Produktseite](https://azure.microsoft.com/en-us/services/active-directory-b2c/), [Dokumentation](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zur Azure AD B2B-Zusammenarbeit:

* [Was ist die Azure AD B2B-Zusammenarbeit?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Eigenschaften von B2B-Zusammenarbeitsbenutzern](active-directory-b2b-user-properties.md)
* [Hinzufügen eines B2B-Zusammenarbeitsbenutzers zu einer Rolle](active-directory-b2b-add-guest-to-role.md)
* [Delegieren von Einladungen zur B2B-Zusammenarbeit](active-directory-b2b-delegate-invitations.md)
* [Dynamische Gruppen und B2B-Zusammenarbeit](active-directory-b2b-dynamic-groups.md)
* [Konfigurieren von SaaS-Apps für die B2B-Zusammenarbeit](active-directory-b2b-configure-saas-apps.md)
* [Benutzertoken für die B2B-Zusammenarbeit](active-directory-b2b-user-token.md)
* [Zuordnen von Benutzeransprüchen für die B2B-Zusammenarbeit](active-directory-b2b-claims-mapping.md)
* [Externe Office 365-Freigaben](active-directory-b2b-o365-external-user.md)
* [Aktuelle Einschränkungen der B2B-Zusammenarbeit](active-directory-b2b-current-limitations.md)
* [Support für die B2B-Zusammenarbeit](active-directory-b2b-support.md)
