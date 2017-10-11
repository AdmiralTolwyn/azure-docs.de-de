---
title: Bericht zu nicht lizenzierter Nutzung | Microsoft Docs
description: Der Bericht zu nicht lizenzierter Nutzung hilft Ihnen dabei, nicht lizenzierte Benutzer zu finden, die kostenpflichtige Azure AD-Funktionen nutzen.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 92138f43-9528-4c8a-b834-66a47da476e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: markvi
ms.openlocfilehash: c0b4f455f067e825362bdecc02ea62d7984f0d31
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2017
---
# <a name="unlicensed-usage-report"></a>Bericht zu nicht lizenzierter Nutzung
Der Bericht zu nicht lizenzierter Nutzung hilft Ihnen dabei, nicht lizenzierte Benutzer zu finden, die kostenpflichtige Azure AD-Funktionen nutzen. Somit können Sie Ihre gekauften Lizenzen besser nutzen und leichter ermitteln, wann Sie weitere Lizenzen benötigen. 

Der Bericht zeigt, welche kostenpflichtigen Funktionen in den letzten 30 Tagen aktiv genutzt wurden. 

## <a name="report-structure"></a>Berichtsstruktur
| Spaltenname | Beschreibung |
|:--- |:--- |
| Nicht lizenzierter Benutzer |Name des Benutzers |
| Funktion |Name des Features. Beispiel: Bedingter Zugriff |
| Genutzte Anwendung |Der Name der Anwendung, auf die mit der Funktion zugegriffen wird. Beispiel: Office 365 SharePoint Online |

> [!NOTE]
> Wenn ein Benutzerkonto gelöscht wurde, wird eine ID wie z. B. 1003000090D8B285 in der Spalte „Nicht lizenzierte Benutzer“ angezeigt.
> 
> 

## <a name="conditional-access-feature"></a>Feature für den bedingten Zugriff
Nicht lizenzierte Benutzer werden beim Zugriff auf einen Dienst, auf den eine Richtlinie für den bedingten Zugriff angewendet wird, gekennzeichnet, wenn sie nicht über eine Azure AD Premium-Lizenz verfügen. 

Dies gilt für MFA-/Standortrichtlinien sowie Geräterichtlinien, die Intune verwenden.

## <a name="see-also"></a>Weitere Informationen
* [Verwenden von bedingtem Zugriff mit Office 365 und anderen verbundenen Azure Active Directory-Apps](active-directory-conditional-access.md)
* [Erste Schritte mit bedingtem Zugriff auf Azure AD](active-directory-conditional-access-azuread-connected-apps.md) 

