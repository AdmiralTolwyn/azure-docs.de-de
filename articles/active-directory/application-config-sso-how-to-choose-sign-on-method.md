---
title: Bestimmen der zu verwendenden Methode für das einmalige Anmelden | Microsoft-Dokumentation
description: Erhalten Sie Informationen zu den von Azure AD unterstützten Modi für das einmalige Anmelden, und erfahren Sie, wie Sie herausfinden, welchen Modus Sie für Ihre Anwendung auswählen sollten.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 336aa4f671e6d86684664fa5e5d15a03a4beff23
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366288"
---
# <a name="how-to-determine-what-single-sign-on-method-to-use"></a>Bestimmen der zu verwendenden Methode für das einmalige Anmelden

In diesem Artikel erhalten Sie Informationen zu den von Azure AD unterstützten Modi für das einmalige Anmelden, und Sie erfahren, wie Sie herausfinden, welchen Modus Sie für Ihre Anwendung auswählen sollten.

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Von bestimmten Anwendungstypen unterstützte Modi für das einmalige Anmelden und die Bereitstellung

Die folgende Tabelle beschreibt die verschiedenen Modi für das einmalige Anmelden und die Bereitstellung, die von den vorstehenden Anwendungstypen unterstützt werden. In dieser Tabelle erfahren Sie, welche Anwendung Sie hinzufügen müssen, um ein bestimmtes Ziel zu erreichen.

  ![Tabelle der Anwendungstypen](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Auswählen eines Modus für das einmalige Anmelden

Nachfolgend werden die unterstützten Modi für das **einmalige Anmelden** für Azure AD-Anwendungen aufgeführt.

-   **Azure AD-SSO deaktiviert**: Wählen Sie diesen **Modus für das einmalige Anmelden** aus, wenn Sie noch nicht dazu bereit sind, diese Anwendung in das einmalige Anmelden über Azure AD zu integrieren, oder wenn Sie die Anwendung einfach nur testen möchten.

-   **Anmeldung über Link**: Wählen Sie [Anmeldung über Link](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) als **Modus für das einmalige Anmelden** aus, wenn Ihre Anwendung bereits mit einer Lösung für das einmalige Anmelden verknüpft ist oder wenn Sie einfach nur einen Link im [Anwendungszugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) oder im [Startprogramm für Office 365-Anwendungen](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none) für Ihre Benutzer veröffentlichen möchten.

-   **Kennwortbasierte Anmeldung**: Wählen Sie [Kennwortbasierte Anmeldung](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) als **Modus für das einmalige Anmelden** aus, wenn Ihre Anwendung HTML-basierte Felder für Benutzername und Kennwort generiert und Sie diese Informationen sicher speichern möchten, damit die Felder für Benutzername und Kennwort in der Anwendung später automatisch ausgefüllt werden können.

-   **SAML-basierte Anmeldung**: Wählen Sie [SAML-basierte Anmeldung](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) als Modus für das einmalige Anmelden aus, wenn Ihre Anwendung die Protokolle SAML oder OpenID Connect unterstützt, oder wenn Sie Benutzer basierend auf Regeln, die Sie in Ihrem SAML-Ansprüchen definieren, zu bestimmten Anwendungsrollen zuordnen möchten. *(**Hinweis:** Diese Option ist nicht verfügbar, wenn für eine Anwendung der Anwendungsproxy konfiguriert ist.)*

-   **Headerbasierte Anmeldung**: Wählen Sie [Headerbasierte Anmeldung](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) als Modus für das einmalige Anmelden aus, wenn das einmalige Anmelden bei einer Anwendung mit PingAccess-Funktion erfolgen soll, die die HTTP-Header-basierte Authentifizierung unterstützt. *(**Hinweis:** Diese Option ist nur verfügbar, wenn für eine Anwendung der Anwendungsproxy sowie PingAccess konfiguriert sind.)*

-   **Integrierte Windows-Authentifizierung**: Wählen Sie [Integrierte Windows-Authentifizierung](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) als Modus für das einmalige Anmelden aus, wenn Sie eine lokale WIA-Anwendung verfügbar machen, bei der das einmalige Anmelden erfolgen soll. *(**Hinweis:** Diese Option ist nur verfügbar, wenn für eine Anwendung der Anwendungsproxy konfiguriert ist.)*

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Modi für das einmalige Anmelden für benutzerdefiniert entwickelte Anwendungen

[Benutzerdefiniert entwickelte Anwendungen](#_Custom-Developed_Applications) unterstützen weitere Modi für das einmalige Anmelden, die oben nicht aufgeführt sind. Hierzu zählen:

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)-basiertes Anmelden

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)-basiertes Anmelden

-   [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)-basiertes Anmelden

-   [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)-basiertes Anmelden

Weitere Informationen zum Erstellen einer benutzerdefiniert entwickelten Anwendung, die diese Modi für das einmalige Anmelden unterstützt, finden Sie im [Entwicklerhandbuch zu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Festlegen des Modus für das einmalige Anmelden für eine Anwendung

Um den **Modus für das einmalige Anmelden** für eine Anwendung festzulegen, folgen Sie diesen Anweisungen:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5.  Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6.  Wählen Sie die Anwendung aus, für die Sie das einmalige Anmelden konfigurieren möchten.

7.  Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Einmaliges Anmelden**.

## <a name="next-steps"></a>Nächste Schritte
[Bereitstellen von einmaligem Anmelden bei Ihren Apps mit dem Anwendungsproxy](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

