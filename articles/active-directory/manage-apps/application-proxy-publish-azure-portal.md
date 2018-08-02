---
title: Veröffentlichen von Apps mit Azure AD-Anwendungsproxy | Microsoft Docs
description: Es wird beschrieben, wie Sie lokale Anwendungen im Azure-Portal mit dem Azure AD-Anwendungsproxy in der Cloud veröffentlichen.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 1224642bb7e6fc0c51b3f839a78449132db5b4bb
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364256"
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy

Mit dem Azure Active Directory-Anwendungsproxy (AD) können Sie Remotemitarbeiter unterstützen, indem Sie lokale Anwendungen so veröffentlichen, dass über das Internet auf sie zugegriffen werden kann. Über das Azure-Portal können Sie diese Anwendungen veröffentlichen, um sicheren Remotezugriff von außerhalb Ihres Netzwerks zu ermöglichen.

Dieser Artikel führt Sie durch die Schritte zum Veröffentlichen einer lokalen App mit dem Anwendungsproxy. Nachdem Sie diesen Artikel abgeschlossen haben, verfügen Ihre Benutzer über einen Remotezugriff auf Ihre App. Sie können zusätzliche Funktionen für die Anwendung konfigurieren, wie beispielsweise die einmalige Anmeldung, personalisierte Informationen oder Sicherheitsanforderungen.

Wenn Sie mit dem Anwendungsproxy noch nicht vertraut sind, erfahren Sie im Artikel [Bereitstellen von sicherem Remotezugriff auf lokale Anwendungen](application-proxy.md) mehr über diese Funktion.

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie bereits einen Connector installiert und registriert haben. Die dafür erforderlichen Schritte finden Sie bei Bedarf unter [Erste Schritte mit dem Anwendungsproxy und Installieren des Connectors](application-proxy-enable.md).

## <a name="publish-an-on-premises-app-for-remote-access"></a>Veröffentlichen einer lokalen App für Remotezugriff

Führen Sie diese Schritte aus, um Ihre Apps mit dem Anwendungsproxy zu veröffentlichen. Wenn Sie noch keinen Connector für Ihr Unternehmen heruntergeladen und konfiguriert haben, navigieren Sie zuerst zu [Erste Schritte mit dem Anwendungsproxy und Installieren des Connectors](application-proxy-enable.md), und veröffentlichen Sie dann Ihre App.

> [!TIP]
> Wenn Sie den Anwendungsproxy zum ersten Mal verwenden, wählen Sie eine Anwendung aus, die bereits für kennwortbasierte Authentifizierung eingerichtet ist. Der Anwendungsproxy unterstützt andere Arten von Authentifizierung, aber mit kennwortbasierten Apps ist der Einstieg am einfachsten und schnellen. 

1. Melden Sie sich als Administrator beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie **Azure Active Directory** > **Unternehmensanwendungen** > **Neue Anwendung** aus.

  ![Hinzufügen einer Unternehmensanwendung](./media/application-proxy-publish-azure-portal/add-app.png)

3. Wählen Sie **Alle** und dann **Lokale Anwendungen** aus.  

  ![Hinzufügen einer eigenen Anwendung](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Geben Sie die folgenden Informationen zur Anwendung an:

   - **Name**: Der Name der Anwendung, der im Zugriffsbereich und im Azure-Portal angezeigt wird. 

   - **Interne URL**: Die URL, die zu nutzen, um innerhalb Ihres privaten Netzwerks auf die Anwendung zuzugreifen. Sie können einen bestimmten Pfad auf dem Back-End-Server für die Veröffentlichung angeben, während der Rest des Servers nicht veröffentlicht wird. Auf diese Weise können Sie unterschiedliche Websites auf demselben Server als unterschiedliche Apps veröffentlichen und jeweils einen eigenen Namen und Zugriffsregeln vergeben.

     > [!TIP]
     > Stellen Sie beim Veröffentlichen eines Pfads sicher, dass er alle erforderlichen Bilder, Skripts und Stylesheets für Ihre Anwendung enthält. Wenn sich Ihre App beispielsweise unter https://yourapp/app befindet und Bilder nutzt, die unter https://yourapp/media gespeichert sind, sollten Sie https://yourapp/ als Pfad veröffentlichen. Diese interne URL verfügt nicht über die Zielseite, die den Benutzern angezeigt wird. Weitere Informationen finden Sie unter [Festlegen einer benutzerdefinierten Startseite für veröffentlichte Apps](application-proxy-configure-custom-home-page.md).

   - **Externe URL**: Die Adresse, die Ihre Benutzer aufrufen, um auf die App von außerhalb Ihres Netzwerks zuzugreifen. Wenn Sie die Anwendungsproxy-Standarddomäne nicht verwenden möchten, lesen Sie [Benutzerdefinierte Domänen im Azure AD-Anwendungsproxy](application-proxy-configure-custom-domain.md).
   - **Vorauthentifizierung**: Gibt das Verfahren an, wie der Anwendungsproxy Benutzer überprüft, bevor diese Zugriff auf Ihre Anwendung erhalten. 

     - Azure Active Directory: Der Anwendungsproxy leitet Benutzer an die Anmeldung mit Azure AD um. Hierbei werden deren Berechtigungen für das Verzeichnis und die Anwendung authentifiziert. Es wird empfohlen, diese Option als Standardwert aktiviert zu lassen, damit Sie Azure AD-Sicherheitsfunktionen wie bedingten Zugriff und Multi-Factor Authentication nutzen können.
     - Passthrough: Benutzer müssen sich nicht bei Azure Active Directory authentifizieren, um Zugriff auf die Anwendung zu erhalten. Sie können weiterhin Authentifizierungsanforderungen auf dem Back-End einrichten.
   - **Connectorgruppe**: Connectors verarbeiten den Remotezugriff auf Ihre Anwendung, und Connectorgruppen helfen Ihnen, Connectors und Apps nach Region, Netzwerk oder Zweck zu organisieren. Wenn Sie noch keine Connectorgruppen erstellt haben, wird Ihre App zu **Standard** zugewiesen.

>[!NOTE]
>Wenn für Ihre Anwendung WebSockets zum Herstellen einer Verbindung verwendet werden, sollten Sie sicherstellen, dass Sie über die Connectorversion 1.5.612.0 oder höher mit WebSocket-Unterstützung verfügen und von der zugewiesenen Connectorgruppe nur diese Connectors verwendet werden.

   ![Konfigurieren der Anwendung](./media/application-proxy-publish-azure-portal/configure-app.png)
5. Konfigurieren Sie bei Bedarf weitere Einstellungen. Für die meisten Anwendungen sollten Sie diese Einstellungen im Standardzustand belassen. 
   - **Backend Application Timeout** (Timeout der Back-End-Anwendung): Legen Sie diesen Wert nur auf **Lang** fest, wenn Ihre Anwendung bei der Authentifizierung und dem Verbinden langsam ist. 
   - **Translate URLs in Headers** (URLs in Headern übersetzen): Behalten Sie diesen Wert als **Ja** bei, wenn Ihre Anwendung den ursprünglichen Hostheader nicht in der Authentifizierungsanforderung erfordert.
   - **Translate URLs in Application Body** (URLs im Hauptteil der Anwendung übersetzen): Behalten Sie diesen Wert als **Nein** bei, wenn Sie nicht über hartcodierte HTML-Links zu anderen lokalen Anwendungen verfügen, und verwenden Sie keine benutzerdefinierten Domänen. Weitere Informationen finden Sie unter [Linkübersetzung mit dem Anwendungsproxy](application-proxy-configure-hard-coded-link-translation.md).
   
   ![Konfigurieren der Anwendung](./media/application-proxy-publish-azure-portal/additional-settings.png)

6. Wählen Sie **Hinzufügen**.


## <a name="add-a-test-user"></a>Hinzufügen eines Testbenutzers 

Um zu testen, ob Ihre App ordnungsgemäß veröffentlicht wurde, fügen Sie ein Testbenutzerkonto hinzu. Stellen Sie sicher, dass dieses Konto bereits über Berechtigungen zum Zugriff auf die App aus dem Unternehmensnetzwerk verfügt.

1. Wählen Sie auf dem Blatt „Schnellstart“ die Option **Benutzer zu Testzwecken zuweisen** aus.

  ![Zuweisen eines Benutzers zu Testzwecken](./media/application-proxy-publish-azure-portal/assign-user.png)

2. Wählen Sie auf dem Blatt „Benutzer und Gruppen“ die Option **Hinzufügen** aus.

  ![Hinzufügen eines Benutzers oder einer Gruppe](./media/application-proxy-publish-azure-portal/add-user.png)

3. Wählen Sie auf dem Blatt „Zuweisung hinzufügen“ die Option **Benutzer und Gruppen** aus, und wählen Sie dann das Konto aus, das Sie hinzufügen möchten. 
4. Wählen Sie **Zuweisen** aus.

## <a name="test-your-published-app"></a>Testen der veröffentlichten App

Navigieren Sie in Ihrem Browser zur externen URL, die Sie beim Veröffentlichen konfiguriert haben. Der Startbildschirm sollte angezeigt werden, und Sie sollten in der Lage sein, sich mit dem Testkonto anzumelden, das Sie eingerichtet haben.

![Testen der veröffentlichten App](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>Nächste Schritte
- [Laden Sie Connectors herunter](application-proxy-enable.md), und [erstellen Sie Connectorgruppen](application-proxy-connector-groups.md), um Anwendungen in getrennten Netzwerken und Standorten zu veröffentlichen.

- [Richten Sie einmaliges Anmelden](application-proxy-configure-single-sign-on-password-vaulting.md) für Ihre neu veröffentlichte App ein.
