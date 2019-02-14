---
title: Aufbewahrungsrichtlinien für Azure Active Directory-Berichte | Microsoft Docs
description: Aufbewahrungsrichtlinien für Berichtdaten in Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c7b8489f3c90652de7a7d8e03c7293c3fbfab6b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56206615"
---
# <a name="azure-active-directory-report-retention-policies"></a>Aufbewahrungsrichtlinien für Azure Active Directory-Berichte

Dieser Artikel enthält Informationen zu den Datenaufbewahrungsrichtlinien für die unterschiedlichen Aktivitätsberichte in Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Wann beginnt Azure AD mit der Datensammlung?

| Azure AD-Edition | Start der Erfassung |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Beim Registrieren für ein Abonnement |
| Azure AD Free <br /> Azure AD Basic | Beim ersten Öffnen des Blatts [Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) oder bei der ersten Verwendung der [Berichterstellungs-APIs](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Wann stehen die Aktivitätsdaten im Azure-Portal zur Verfügung?

- **Sofort**, wenn Sie bereits Berichte im Azure-Portal verwendet haben.
- **Innerhalb von 2 Stunden**, wenn Sie die Berichterstellung im Azure-Portal nicht aktiviert haben.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Wie schnell werden nach Erwerb einer Premium-Lizenz Daten zu Aktivitäten angezeigt?

Wenn Ihnen bereits mit einer kostenlosen Lizenz Daten zu Aktivitäten angezeigt werden, können Sie diese sofort nach dem Upgrade sehen. Wenn Sie noch keine Daten haben, dauert es ein bis zwei Tage, bis die Daten in den Berichten nach dem Upgrade auf eine Premium-Lizenz angezeigt werden.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Werden nach Erwerb einer Azure AD Premium-Lizenz die Daten des letzten Monats angezeigt?

Wenn Sie vor Kurzem zu einer Premium-Version (einschließlich einer Testversion) gewechselt sind, werden Ihnen anfänglich Daten von bis zu 7 Tagen angezeigt. Wenn sich Daten ansammeln, können Sie die Daten der letzten 30 Tage sehen.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Wann beginnt Azure AD mit der Sammlung von Sicherheitssignaldaten?  

Die Erfassung von Sicherheitssignalen beginnt, wenn Sie sich für die Verwendung von **Identity Protection Center** entscheiden. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Wie lange speichert Azure AD die Daten?

**Aktivitätsberichte**    

| Bericht                 | Azure AD Free | Azure AD Basic | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--            | :--                 | :--                 |
| Verzeichnisprüfbericht        | 7 Tage        |  7 Tage        | 30 Tage             | 30 Tage             |
| Benutzeranmeldeaktivität       | –           |  –           | 30 Tage             | 30 Tage             |
| Azure MFA-Nutzung        | 30 Tage       |  30 Tage       | 30 Tage             | 30 Tage             |

Sie können die Aktivitätsdaten zu Überwachung und Anmeldung länger als den oben beschriebenen Standardaufbewahrungszeitraum speichern, indem Sie sie mit Azure Monitor an ein Azure Storage-Konto weiterleiten. Weitere Informationen finden Sie unter [Archivieren von Azure AD-Protokollen in einem Azure Storage-Konto](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Sicherheitssignale**

| Bericht         | Azure AD Free | Azure AD Basic | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--            | :--                 | :--                 |
| Gefährdete Benutzer  | 7 Tage        | 7 Tage         | 30 Tage             | 90 Tage             |
| Riskante Anmeldungen | 7 Tage        | 7 Tage         |  30 Tage            | 90 Tage             |

---
