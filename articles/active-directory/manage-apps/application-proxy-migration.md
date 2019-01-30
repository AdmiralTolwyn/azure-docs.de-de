---
title: Durchführen eines Upgrades auf den Azure AD-Anwendungsproxy | Microsoft-Dokumentation
description: Wählen Sie aus, welche Proxylösung am besten für Sie geeignet ist, um ein Upgrade von Microsoft Forefront oder Unified Access Gateway durchzuführen.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/27/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 1517fedd4b4f8d46b0c7367fa4c1319325818b08
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54461348"
---
# <a name="compare-remote-access-solutions"></a>Vergleichen von Remotezugriffslösungen

Der Azure Active Directory-Anwendungsproxy ist eine der beiden von Microsoft angebotenen Remotezugriffslösungen. Das andere ist Webanwendungsproxy, die lokale Version. Diese beiden Lösungen ersetzen ältere Produkte, die von Microsoft angeboten wurden: Microsoft Forefront Threat Management Gateway (TMG) und Unified Access Gateway (UAG). In diesem Artikel erhalten Sie einen Vergleich dieser vier Lösungen. Benutzer, die weiterhin die veralteten Lösungen TMG oder UAG verwenden, können mithilfe dieses Artikels ihre Migration zu einem der Anwendungsproxys planen. 


## <a name="feature-comparison"></a>Funktionsvergleiche

Verwenden Sie diese Tabelle zum Vergleich von Threat Management Gateway (TMG), Unified Access Gateway (UAG), Webanwendungsproxy (WAP) und Azure AD-Anwendungsproxy (AP).

| Feature | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Zertifikatauthentifizierung | JA | JA | - | - |
| Ausgewählte Veröffentlichung von Browser-Apps | JA | Ja | Ja | JA |
| Vorauthentifizierung und einmaliges Anmelden | JA | Ja | Ja | JA | 
| Layer 2/3-Firewall | JA | JA | - | - |
| Weiterleitungs-Proxyfunktionen | JA | - | - | - |
| VPN-Funktionen | JA | JA | - | - |
| Umfangreiche Protokollunterstützung | - | JA | Ja, bei Ausführung über HTTP | Ja, bei Ausführung über HTTP oder über Remotedesktopgateway |
| Dient als AD FS-Proxyserver | - | JA | JA | - |
| Ein Portal für den Anwendungszugriff | - | JA | - | JA |
| Übersetzung von Antworttext in Link | JA | Ja | - | JA | 
| Authentifizierung mit Headern | - | JA | - | Ja, mit PingAccess | 
| Sicherheit auf Cloud-Ebene | - | - | - | JA | 
| Bedingter Zugriff | - | JA | - | JA |
| Keine Komponenten in der demilitarisierten Zone (DMZ) | - | - | - | JA |
| Keine eingehenden Verbindungen | - | - | - | JA |

In den meisten Szenarien empfiehlt sich Azure AD-Anwendungsproxy als moderne Lösung. Webanwendungsproxy wird nur in Szenarien bevorzugt, bei denen ein Proxyserver für AD FS erforderlich ist. Außerdem können Sie in Azure Active Directory keine benutzerdefinierten Domänen verwenden. 

Azure AD-Anwendungsproxy bietet besondere Vorteile im Vergleich zu ähnlichen Produkten, darunter:

- Erweitern von Azure AD um lokale Ressourcen
   - Sicherheit und Schutz auf Cloud-Ebene
   - Einfach zu aktivierende Features wie bedingter Zugriff und mehrstufige Authentifizierung
- Keine Komponenten in der demilitarisierten Zone
- Keine Notwendigkeit eingehender Verbindungen
- Ein Zugriffsbereich, in dem Ihre Benutzer alle ihre Anwendungen erreichen, darunter Office 365, in Azure AD integrierte SaaS-Apps und Ihre lokalen Web-Apps. 


## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen eines sicheren Remotezugriffs auf lokale Anwendungen mit Azure AD-Anwendungsproxy](application-proxy.md)
- [Übergang von Forefront TMG und UAG zu Anwendungsproxy](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).
