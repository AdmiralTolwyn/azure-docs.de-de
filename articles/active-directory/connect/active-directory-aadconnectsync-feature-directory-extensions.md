---
title: 'Azure AD Connect Sync: Verzeichniserweiterungen | Microsoft Docs'
description: Dieses Thema beschreibt das Verzeichniserweiterungsfeature in Azure AD Connect.
services: active-directory
documentationcenter: 
author: AndKjell
manager: mtillman
editor: 
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 3ab8b02ad30315de23e5d8e7370cc385a53ecf3a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect Sync: Verzeichniserweiterungen
Verzeichniserweiterungen ermöglichen Ihnen das Erweitern des Schemas in Azure AD mit Ihren eigenen Attributen aus dem lokalen Active Directory. Dank dieses Features können Sie Branchen-Apps erstellen, die weiterhin lokal verwaltete Attribute nutzen. Diese Attribute können über [Azure AD Graph-Verzeichniserweiterungen](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) oder über [Microsoft Graph](https://graph.microsoft.io/) genutzt werden. Sie können die verfügbaren Attribute je nach Lösung mithilfe von [Azure AD Graph-Explorer](https://graphexplorer.azurewebsites.net/) oder [Microsoft Graph-Explore](https://developer.microsoft.com/en-us/graph/graph-explorer)r anzeigen.

Derzeit können diese Attribute nicht von Office 365-Workloads genutzt werden.

Sie konfigurieren im Installations-Assistenten im Pfad der benutzerdefinierten Einstellungen, welche zusätzlichen Attribute synchronisiert werden sollen.
![Schemaerweiterungs-Assistent](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  
Bei der Installation werden folgenden Attribute als zulässige Kandidaten angezeigt:

* Benutzer- und Gruppenobjekttypen
* Einwertige Attribute: Zeichenfolge, Boolescher Wert, ganze Zahl, Binärwert
* Mehrwertige Attribute: Zeichenfolge, Binärwert


>[!NOTE]
> Azure AD Connect unterstützt zwar das Synchronisieren mehrwertiger AD-Attribute für Azure AD als mehrwertige Verzeichniserweiterungen, doch gibt es derzeit keine Funktionen in Azure AD, die die Verwendung mehrwertiger Verzeichniserweiterungen unterstützen.

Die Attributliste wird aus dem Schemacache gelesen, der im Zuge der Installation von Azure AD Connect erstellt wurde. Wenn Sie das Active Directory-Schema um zusätzliche Attribute erweitert haben, ist eine [Aktualisierung des Schemas erforderlich](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema), damit die neuen Attribute angezeigt werden.

Ein Objekt in Azure AD kann bis zu 100 Attribute für Verzeichniserweiterungen aufweisen. Die maximale Länge beträgt 250 Zeichen. Längere Attributwerte werden vom Synchronisierungsmodul gekürzt.

Während der Installation von Azure AD Connect wird eine Anwendung registriert, in der diese Attribute verfügbar sind. Diese Anwendung wird im Azure-Portal angezeigt.  
![Schemaerweiterungs-App](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

Diese Attribute sind jetzt über Graph verfügbar:   
![Graph](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Die Attribute haben das Präfix „extension\_{AppClientId}\_“. Die AppClientId besitzt für alle Attribute in Ihrem Azure AD-Mandanten den gleichen Wert.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](active-directory-aadconnectsync-whatis.md) .

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).
