---
title: Hinzufügen von Authentifizierung unter Android mit Mobile Apps | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie das Mobile Apps-Feature in Azure App Service verwenden, um die Benutzer Ihrer Android-App über verschiedene Identitätsanbieter wie Google, Facebook, Twitter und Microsoft zu authentifizieren.
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
editor: ''
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: f138911510db4e6839ff96317fa6004e449e58be
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443596"
---
# <a name="add-authentication-to-your-android-app"></a>Hinzufügen der Authentifizierung zu Ihrer Android-App
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Im Rahmen von Visual Studio App Center wird in neue und integrierte Dienste investiert, die für die Entwicklung mobiler Apps von zentraler Bedeutung sind. Entwickler können **Build**-, **Test**- und **Verteilungs**dienste nutzen, um eine Pipeline für Continuous Integration und Delivery einzurichten. Nach der Bereitstellung der App können Entwickler den Status und die Nutzung ihrer App mithilfe der **Analyse**- und **Diagnose**dienste überwachen und mit Benutzern über den **Push**dienst interagieren. Entwickler können auch den **Authentifizierung**sdienst nutzen, um ihre Benutzer zu authentifizieren, und den **Daten**dienst, um App-Daten dauerhaft in der Cloud zu speichern und zu synchronisieren. Besuchen Sie noch heute das [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-android-get-started-users).
>

## <a name="summary"></a>Zusammenfassung
In diesem Tutorial verwenden Sie einen unterstützten Identitätsanbieter, um dem Aufgabenlisten-Schnellstartprojekt unter Android eine Authentifizierung hinzuzufügen. Dieses Tutorial baut auf dem Tutorial [Erste Schritte mit mobilen Apps] auf, das Sie zuerst abschließen müssen.

## <a name="register"></a>Registrieren Ihrer App für die Authentifizierung und Konfigurieren von Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Hinzufügen Ihrer App zu den zulässigen externen Umleitungs-URLs

Eine sichere Authentifizierung erfordert, dass Sie ein neues URL-Schema für Ihre App definieren. Dies ermöglicht dem Authentifizierungssystem die erneute Umleitung an Ihre App, sobald der Authentifizierungsprozess abgeschlossen ist. In diesem Tutorial verwenden wir ausschließlich das URL-Schema _appname_. Sie können jedoch ein beliebiges URL-Schema auswählen und verwenden. Es sollte für Ihre mobile Anwendung eindeutig sein. So aktivieren Sie die Umleitung auf der Serverseite

1. Wählen Sie im [Azure-Portal] App Service aus.

2. Klicken Sie auf die Menüoption **Authentifizierung/Autorisierung**.

3. Geben Sie in **Zulässige externe Umleitungs-URLs** `appname://easyauth.callback` ein.  Das _appname_-Element in dieser Zeichenfolge ist das URL-Schema für Ihre mobile Anwendung.  Es sollte der normalen URL-Spezifikation für ein Protokoll folgen (nur aus Buchstaben und Zahlen bestehen und mit einem Buchstaben beginnen).  Notieren Sie sich die gewählte Zeichenfolge, da Sie später Ihren mobilen Anwendungscode mehrfach mit dem URL-Schema anpassen müssen.

4. Klicken Sie auf **OK**.

5. Klicken Sie auf **Speichern**.

## <a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Öffnen Sie in Android Studio das Projekt, das Sie im Tutorial [Erste Schritte mit mobilen Apps] erstellt haben. Klicken Sie im Menü **Ausführen** auf **App ausführen**. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) ausgelöst wird.

     Diese Ausnahme wird ausgelöst, weil die App als nicht authentifizierter Benutzer versucht, auf das Back-End zuzugreifen, aber die *TodoItem*-Tabelle nun eine Authentifizierung erfordert.

Aktualisieren Sie nun die App, um Benutzer vor dem Anfordern von Ressourcen des Mobile Apps-Back-Ends zu authentifizieren.

## <a name="add-authentication-to-the-app"></a>Hinzufügen von Authentifizierung zur App
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Zwischenspeichern von Authentifizierungstoken auf dem Client
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie dieses einfache Tutorial zur Authentifizierung abgeschlossen haben, können Sie mit den folgenden Tutorials fortfahren:

* [Hinzufügen von Pushbenachrichtigungen zu Ihrer Android-App](app-service-mobile-android-get-started-push.md):
  Hier erfahren Sie, wie Sie Ihr Mobile Apps-Back-End für die Verwendung von Azure Notification Hubs zum Senden von Pushbenachrichtigungen konfigurieren.
* [Aktivieren der Offlinesynchronisierung für Ihre Android-App](app-service-mobile-android-get-started-offline-data.md):
  Hier erfahren Sie, wie Sie die Offlineunterstützung mit einem Mobile Apps-Back-End zu Ihrer App hinzufügen. Die Offlinesynchronisierung ermöglicht Endbenutzern die Interaktion mit einer mobilen App (also das Anzeigen, Hinzufügen oder Ändern von Daten) auch ohne bestehende Netzwerkverbindung.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Erste Schritte mit mobilen Apps]: app-service-mobile-android-get-started.md
[Azure-Portal]: https://portal.azure.com/
