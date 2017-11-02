---
title: "Bedingter Zugriff auf lokale Apps – Azure AD | Microsoft-Dokumentation"
description: "Stellt Informationen dazu bereit, wie der bedingte Zugriff für veröffentlichte Anwendungen eingerichtet wird, auf die mit dem Azure AD-Anwendungsproxy remote zugegriffen wird."
services: active-directory
documentationcenter: 
author: LizCasey
manager: femila
ms.assetid: 2e97722b-eb4e-4078-b607-9fed210d8a0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: e0d44c34a3aa631984f2a7cbdb03e19759b5ccfc
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/31/2017
---
# <a name="working-with-conditional-access-in-azure-ad-application-proxy"></a>Arbeiten mit bedingtem Zugriff im Azure AD-Anwendungsproxy

>[!NOTE]
>Dieser Artikel gilt für das klassische Azure-Portal, das in Zukunft nicht mehr verwendet wird. Wir empfehlen die Verwendung des [Azure-Portals](https://portal.azure.com). Im Azure-Portal bieten Anwendungsproxy-Apps die gleichen Funktionen für den bedingten Zugriff wie alle anderen SaaS-Apps. Weitere Informationen zum bedingten Zugriff finden Sie unter [Erste Schritte mit dem bedingten Zugriff in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Sie können Zugriffsregeln konfigurieren, um den bedingten Zugriff auf Anwendungen zu gewähren, die mit dem Anwendungsproxy veröffentlicht werden. Hierdurch wird Folgendes ermöglicht:

* Erfordern einer Multi-Factor Authentication pro Anwendung
* Erfordern einer Multi-Factor Authentication nur dann, wenn die Benutzer nicht am Arbeitsplatz sind
* Blockieren des Zugriffs der Benutzer auf Anwendungen, wenn die Benutzer nicht am Arbeitsplatz sind

Diese Regeln können auf alle Benutzer und Gruppen oder lediglich auf bestimmte Benutzer und Gruppen angewendet werden. Standardmäßig gilt die Regel für alle Benutzer, die Zugriff auf die Anwendung haben. Die Regel kann jedoch auch auf Benutzer beschränkt werden, die Mitglied der angegebenen Sicherheitsgruppen sind.  

Zugriffsregeln werden ausgewertet, wenn ein Benutzer auf eine Verbundanwendung zugreift, die OAuth 2.0, OpenID Connect, SAML oder WS-Federation verwendet. Darüber hinaus werden Zugriffsregeln mit OAuth 2.0 und OpenID Connect ausgewertet, wenn ein Aktualisierungstoken zum Abrufen eines Zugriffstokens verwendet wird.

## <a name="conditional-access-prerequisites"></a>Voraussetzungen für den bedingten Zugriff
* Azure Active Directory Premium-Abonnement
* Ein Verbundmandant oder verwalteter Azure Active Directory-Mandant
* Verbundmandanten erfordern Multi-Factor Authentication (MFA).  
    ![Konfigurieren von Zugriffsregeln – Erfordern von Multi-Factor Authentication](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## <a name="configure-per-application-multi-factor-authentication"></a>Konfigurieren von Multi-Factor Authentication pro Anwendung
1. Melden Sie sich als Administrator beim klassischen Azure-Portal an.
2. Gehen Sie zu Active Directory, und wählen Sie das Verzeichnis aus, in dem Sie den Anwendungsproxy aktivieren möchten.
3. Klicken Sie auf **Anwendungen**, und scrollen Sie nach unten zum Abschnitt **Zugriffsregeln**. Der Abschnitt mit den Zugriffsregeln wird nur für Anwendungen angezeigt, die mit dem Anwendungsproxy veröffentlicht wurden und die Verbundauthentifizierung nutzen.
4. Aktivieren Sie die Regeln, indem Sie **Zugriffsregeln aktivieren** auf **Ein** festlegen.
5. Geben Sie die Benutzer und Gruppen an, auf die die Regeln angewendet werden sollen. Wählen Sie über die Schaltfläche **Gruppe hinzufügen** eine oder mehrere Gruppen aus, für die die Zugriffsregel gelten soll. Dieses Dialogfeld kann auch zum Entfernen ausgewählter Gruppen verwendet werden.  Wenn die Regeln auf Gruppen angewendet werden sollen, werden die Zugriffsregeln nur für Benutzer erzwungen, die einer der angegebenen Sicherheitsgruppen angehören.  

   * Um Sicherheitsgruppen ausdrücklich von der Regel auszuschließen, aktivieren Sie die Option **Ausgenommen** und geben eine oder mehrere Gruppen an. Benutzer, die Mitglieder einer der in der Ausnahmeliste aufgeführten Gruppen sind, müssen keine Multi-Factor Authentication durchführen.  
   * Wenn ein Benutzer unter Verwendung des Features Multi-Factor Authentication auf Benutzerebene konfiguriert wurde, hat diese Einstellung Vorrang vor den Multi-Factor Authentication-Regeln auf Anwendungsebene. Benutzer, die auf Benutzerebene für die Multi-Factor Authentication konfiguriert wurden, müssen auch dann eine Multi-Factor Authentication durchlaufen, wenn sie auf Anwendungsebene von den Multi-Factor Authentication-Regeln ausgenommen wurden. Hier erfahren Sie mehr über [Multi-Factor Authentication und benutzerspezifische Einstellungen](../multi-factor-authentication/multi-factor-authentication.md).
6. Wählen Sie die Zugriffsregel, die Sie festlegen möchten:

   * **Mehrstufige Authentifizierung anfordern:** Benutzer, auf die Zugriffsregeln angewendet werden, müssen eine Multi-Factor Authentication durchlaufen, bevor sie auf die Anwendung zugreifen können, für die die Regel gilt.
   * **Erfordert mehrstufige Authentifizierung, wenn nicht bei der Arbeit**: Benutzer, die über eine vertrauenswürdige IP-Adresse auf die Anwendung zugreifen, werden nicht zur Ausführung einer mehrstufigen Authentifizierung aufgefordert. Die Bereiche für vertrauenswürdige IP-Adressen können auf den Einstellungsseiten für die mehrstufige Authentifizierung konfiguriert werden.
   * **Zugriff blockieren, wenn nicht gearbeitet wird**: Benutzer, die versuchen, sich von außerhalb des Unternehmensnetzwerks zu verbinden, können nicht auf die Anwendung zugreifen.

## <a name="configuring-mfa-for-federation-services"></a>Konfigurieren von MFA für Verbunddienste
Für Verbundmandanten kann Multi-Factor Authentication (MFA) von Azure Active Directory oder vom lokalen AD FS-Server durchgeführt werden. Standardmäßig wird MFA auf allen Seiten durchgeführt, die von Azure Active Directory gehostet werden. Führen Sie zur lokalen Konfiguration von MFA Windows PowerShell aus, und verwenden Sie die SupportsMFA-Eigenschaft zum Festlegen des Azure AD-Moduls.

Im folgenden Beispiel wird veranschaulicht, wie lokale MFA mithilfe des [Set-MsolDomainFederationSettings-Cmdlets](https://msdn.microsoft.com/library/azure/dn194088.aspx) auf dem Mandanten „contoso.com“ aktiviert wird: `Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

Zusätzlich zum Festlegen dieses Kennzeichens muss die AD FS-Instanz des Verbundmandanten für die Ausführung der mehrstufigen Authentifizierung konfiguriert werden. Folgen Sie hierzu den Anweisungen unter [Lokales Bereitstellen von Microsoft Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="see-also"></a>Weitere Informationen
* [Arbeiten mit Anwendungen, die Ansprüche unterstützen](active-directory-application-proxy-claims-aware-apps.md)
* [Veröffentlichen von Anwendungen mit dem Anwendungsproxy](active-directory-application-proxy-publish.md)
* [Aktivieren der einmaligen Anmeldung](active-directory-application-proxy-sso-using-kcd.md)
* [Veröffentlichen von Anwendungen mit Ihrem eigenen Domänennamen](active-directory-application-proxy-custom-domains.md)

Aktuelle Neuigkeiten und Updates finden Sie im [Blog zum Anwendungsproxy](http://blogs.technet.com/b/applicationproxyblog/)
