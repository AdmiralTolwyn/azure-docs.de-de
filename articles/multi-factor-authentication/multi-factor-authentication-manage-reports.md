---
title: "Zugriffs- und Nutzungsberichte für Azure MFA| Microsoft Docs"
description: "Beschreibt, wie Sie das Berichte-Feature für Multi-Factor Authentication verwenden."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: joflore
ms.reviewer: alexwe
ms.openlocfilehash: 77d6742faadfaf3d7afccfbe888b910c80278737
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Berichte in Azure Multi-Factor Authentication

Azure Multi-Factor Authentication bietet verschiedene Berichte, die Sie und Ihre Organisation verwenden können. Auf diese Berichte können Sie über das Multi-Factor Authentication-Verwaltungsportal zugreifen. In der folgenden Tabelle sind die verfügbaren Berichte aufgeführt:

| Bericht | Beschreibung |
|:--- |:--- |
| Verwendung |Die Nutzungsberichte geben Informationen zur Gesamtnutzung, Benutzerzusammenfassungen und Benutzerdetails an. |
| Serverstatus |Dieser Bericht zeigt den Status von Multi-Factor Authentication-Servern an, die mit Ihrem Konto verknüpft sind. |
| Verlauf – gesperrte Benutzer |Dieser Bericht zeigt den Verlauf von Anforderungen zum Sperren oder Entsperren von Benutzern an. |
| Verlauf – Umgangene Benutzer |Dieser Bericht zeigt den Verlauf von Anforderungen an, die mehrstufige Authentifizierung für die Telefonnummer des Benutzers zu umgehen. |
| Betrugswarnung |Dieser Bericht zeigt den Verlauf von Betrugswarnungen an, die im angegebenen Zeitraum übermittelt wurden. |
| In Warteschlange |Hier werden Berichte aufgelistet, die zur Verarbeitung und aufgrund ihres Status in der Warteschlange sind. Nach Abschluss des Berichts wird ein Link zum Herunterladen oder Anzeigen des Berichts bereitgestellt. |

## <a name="view-reports"></a>Anzeigen von Berichten

1. Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com)an.
2. Wählen Sie im linken Bereich "Active Directory" aus.
3. Gehen Sie nach einer der beiden folgenden Optionen vor, abhängig davon, ob Sie Authentifizierungsanbieter nutzen:
   * **Option 1**: Klicken Sie auf die Registerkarte mit den Multi-Factor Authentication-Anbietern. Wählen Sie Ihren MFA-Anbieter, und klicken Sie auf die Schaltfläche **Verwalten** am unteren Rand.
   * **Option 2**: Wählen Sie Ihr Verzeichnis aus, und klicken Sie auf die Registerkarte **Konfigurieren**. Klicken Sie im Abschnitt „Multi-Factor Authentication“ auf „ **Diensteinstellungen verwalten**“. Klicken Sie am unteren Rand der Seite mit den Einstellungen für den MFA-Dienst auf den Link „Portal aufrufen“.
4. Wählen Sie im Azure Multi-Factor Authentication-Verwaltungsportal auf der linken Seite im Bereich **Einen Bericht anzeigen** den gewünschten Berichtstyp aus.

<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>

## <a name="powershell-reporting"></a>PowerShell-Berichterstellung

Identifizieren Sie Benutzer, die sich für MFA registriert haben, mithilfe des folgenden PowerShell-Befehls.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifizieren Sie Benutzer, die sich nicht für MFA registriert haben, mithilfe des folgenden PowerShell-Befehls.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

**Weitere Ressourcen**

* [Für Benutzer](end-user/multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication bei MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
