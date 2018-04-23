---
title: Verwalten des Zugriffs auf Azure-Ressourcen mit Azure Active Directory
description: Hier erfahren Sie mehr über die Methoden zum Verwalten des Zugriffs auf Azure-Ressourcen mit verschiedenen Azure Active Directory-Features.
services: active-directory
documentationcenter: ''
author: skwan
manager: mtillman
editor: bryanla
ms.assetid: f66abf54-3809-436c-92b6-018e1179780e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2017
ms.author: skwan
ms.openlocfilehash: f8f8af1380dc47a4a97845d9d47ac17bd4bba3e0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2018
---
# <a name="manage-access-to-azure-resources-with-azure-active-directory"></a>Verwalten des Zugriffs auf Azure-Ressourcen mit Azure Active Directory

Die Identitäts- und Zugriffsverwaltung für Cloudressourcen ist eine wichtige Funktion für jede Organisation, die die Cloud nutzt. Azure Active Directory (Azure AD) ist das Identitäts- und Zugriffssystem für Microsoft Azure.  

Sehen Sie sich das folgende Video an, bevor Sie die praktischen Featurebereiche von Azure AD erkunden: Locking down access to the Azure Cloud using SSO, Roles Based Access Control, and Conditional (Sperren des Zugriffs auf die Azure-Cloud mithilfe von SSO, rollenbasierter Zugriffssteuerung und bedingtem Zugriff). In diesem Video erhalten Sie Informationen zu folgenden Punkten:

- Bewährte Methoden für das Konfigurieren des einmaligen Anmeldens beim Azure-Portal mit lokaler Active Directory-Instanz
- Verwenden von Azure RBAC für differenzierte Zugriffssteuerung auf Ressourcen in Abonnements
- Erzwingen sicherer Authentifizierungsregeln mit bedingtem Azure AD-Zugriff
- Konzept der verwalteten Dienstidentität, mit der Azure-Ressourcen automatisch für Azure-Dienste authentifiziert werden können und Entwickler keine API-Schlüssel oder -Geheimnisse verarbeiten müssen

> [!VIDEO https://www.youtube.com/embed/FKBoWWKRnvI]

## <a name="feature-areas"></a>Featurebereiche
Azure AD bietet die folgenden Funktionen für die Verwaltung des Zugriffs auf Azure-Ressourcen:

|||
|---|---|
| [Beziehung zwischen Azure AD-Mandanten und -Abonnements](rbac-and-directory-admin-roles.md) | Erfahren Sie mehr darüber, warum Azure AD die vertrauenswürdige Quelle von Benutzern und Gruppen für ein Azure-Abonnement ist. |
| [Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)](overview.md) | Ermöglichen Sie über die Benutzern, Gruppen oder Dienstprinzipalen zugeordneten Rollen eine differenzierte Zugriffsverwaltung. |
| [Privileged Identity Management mit RBAC](pim-azure-resource.md) | Steuern Sie umfassende Berechtigungen durch die Just-In-Time-Zuweisung privilegierter Rollen. |
| [Bedingter Zugriff für die Azure-Verwaltung](conditional-access-azure-management.md) | Richten Sie Richtlinien für bedingten Zugriff ein, um den Zugriff auf Azure-Verwaltungsendpunkte zuzulassen oder zu blockieren. |
| [Verwaltete Dienstidentität (Managed Service Identity, MSI)](../active-directory/pp/msi-overview.md) | Geben Sie Azure-Ressourcen wie virtuellen Computern eine eigene Identität, damit Sie keine Anmeldeinformationen in Ihren Code aufnehmen müssen. |

 