---
title: Erste Schritte mit Azure AD AngularJS | Microsoft-Dokumentation
description: "Hier erfahren Sie, wie eine einseitige AngularJS-Anwendung erstellt wird, die für die Anmeldung in Azure AD integriert wird und über OAuth per Azure AD geschützte APIs aufruft."
services: active-directory
documentationcenter: 
author: jmprieur
manager: mtillman
editor: 
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 51734f5b39e9ca8af65048f2a7efd5c61cd53204
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/21/2018
---
# <a name="azure-ad-angularjs-getting-started"></a>Azure AD AngularJS – Erste Schritte

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Mit Azure Active Directory (Azure AD) ist es einfach und problemlos möglich, Ihren einseitigen Apps die Anmeldung, die Abmeldung und sichere OAuth-API-Aufrufe hinzuzufügen.  Ihre Apps können Benutzer dann über ihre Windows Server Active Directory-Konten authentifizieren und jede per Azure AD geschützte Web-API nutzen, z. B. die Office 365-APIs oder die Azure-API.

Für JavaScript-Anwendungen, die in einem Browser ausgeführt werden, stellt Azure AD die Active Directory Authentication Library( ADAL) bzw. „adal.js“ bereit. Die einzige Aufgabe von „adal.js“ besteht darin, Ihrer App das Abrufen von Zugriffstoken zu erleichtern. Um Ihnen zu zeigen, wie einfach es geht, erstellen wir hier eine AngularJS-Anwendung mit einer Aufgabenliste, mit der folgende Aktionen ausgeführt werden können:

* Anmelden von Benutzern an der App mit Azure AD als Identitätsanbieter

* Anzeigen einiger Informationen zum Benutzer
* Sicheres Aufrufen der Aufgabenlisten-API einer App mit Bearertoken aus Azure AD
* Abmelden von Benutzern von der App

Zur Entwicklung der vollständigen Arbeitsanwendung müssen Sie folgende Schritte ausführen:

1. Registrieren Sie Ihre App in Azure AD.
2. Installieren Sie die ADAL, und konfigurieren Sie die einseitige Anwendung.
3. Verwenden Sie die ADAL, um Seiten in der einseitigen Anwendung zu schützen.

Beginnen Sie, indem Sie das [Anwendungsgerüst](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) oder [das vollständige Beispiel herunterladen](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Außerdem benötigen Sie einen Azure AD-Mandanten, in dem Sie Benutzer erstellen und Ihre Anwendung registrieren können. Wenn Sie über noch keinen Mandanten verfügen, [erfahren Sie hier, wie Sie einen erhalten](active-directory-howto-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Schritt 1: Registrieren der Anwendung DirectorySearcher
Sie müssen die App zuerst in Ihrem Azure AD-Mandanten registrieren, um damit Benutzer authentifizieren und Token abrufen zu können:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wenn Sie in mehreren Verzeichnissen angemeldet sind, müssen Sie ggf. prüfen, ob Sie das richtige Verzeichnis anzeigen. Klicken Sie dazu auf der oberen Leiste auf Ihr Konto. Wählen Sie in der **Verzeichnisliste** den Azure AD-Mandanten aus, bei dem Sie Ihre Anwendung registrieren möchten.
3. Klicken Sie im linken Bereich auf **Alle Dienste**, und wählen Sie **Azure Active Directory** aus.
4. Klicken Sie auf **App-Registrierungen**, und wählen Sie **Hinzufügen** aus.
5. Folgen Sie den Bildschirmaufforderungen, und erstellen Sie eine neue Webanwendung und/oder Web-API.
  * **Name**: Beschreibt die Anwendung für Benutzer.
  * Der **Umleitungs-URI** gibt den Speicherort an, an dem Azure AD die Token zurückgibt. Der Standardspeicherort für dieses Beispiel lautet `https://localhost:44326/`.
6. Nach Abschluss der Registrierung weist Azure AD Ihrer App eine eindeutige Anwendungs-ID zu.  Diesen Wert benötigen Sie in den nächsten Abschnitten. Daher sollten Sie ihn von der Registerkarte „Anwendung“ kopieren.
7. Für „adal.js“ wird der implizite OAuth-Fluss genutzt, um mit Azure AD zu kommunizieren. Sie müssen den impliziten Fluss für Ihre Anwendung aktivieren:
  1. Klicken Sie auf die Anwendung, und wählen Sie **Manifest**, um den Inline-Manifest-Editor zu öffnen.
  2. Suchen Sie die `oauth2AllowImplicitFlow`-Eigenschaft. Legen Sie den Wert der Eigenschaft auf `true`fest.
  3. Klicken Sie auf **Speichern**, um das Manifest zu speichern.
8. Erteilen Sie in Ihrem gesamten Mandanten Berechtigungen für Ihre Anwendung. Wechseln Sie zu **Einstellungen** > **Eigenschaften** > **Erforderliche Berechtigungen**, und klicken Sie auf der oberen Leiste auf die Schaltfläche **Berechtigungen erteilen**. Klicken Sie auf **Ja**, um zu bestätigen.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Schritt 2: Installieren von ADAL und Konfigurieren der einseitigen Anwendung
Nachdem Sie nun eine Anwendung in Azure AD erstellt haben, können Sie „adal.js“ installieren und Ihren identitätsbezogenen Code schreiben.

### <a name="configure-the-javascript-client"></a>Konfigurieren des JavaScript-Clients
Beginnen Sie, indem Sie „adal.js“ über die Paket-Manager-Konsole dem Projekt mit der einseitigen Aufgabenlisten-Anwendung (TodoSPA) hinzufügen:
  1. Laden Sie [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) herunter, und fügen Sie die Datei dem Projektverzeichnis `App/Scripts/` hinzu.
  2. Laden Sie [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) herunter, und fügen Sie die Datei dem Projektverzeichnis `App/Scripts/` hinzu.
  3. Laden Sie jedes Skript vor dem Ende von `</body>` in `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Konfigurieren des Back-End-Servers
Damit die Back-End-Aufgabenlisten-API der einseitigen Anwendung Token aus dem Browser akzeptieren kann, benötigt das Back-End Konfigurationsinformationen zur App-Registrierung. Öffnen Sie `web.config`im Projekt mit der einseitigen Aufgabenlisten-Anwendung. Ersetzen Sie die Werte der Elemente im Abschnitt `<appSettings>` durch die Werte, die Sie im Azure-Portal verwendet haben. Sobald Ihr Code ADAL verwendet, verweist er auf diese Werte.
  * `ida:Tenant` ist die Domäne Ihres Azure AD-Mandanten, z. B. „contoso.onmicrosoft.com“.
  * `ida:Audience` ist die Client-ID der Anwendung, die Sie aus dem Portal kopiert haben.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Schritt 3: Verwenden von ADAL zum Schützen von Seiten in der einseitigen Anwendung
„adal.js“ wurde für die Integration in die AngularJS-Route und in HTTP-Anbieter erstellt, damit Sie einzelne Ansichten in Ihrer einseitigen Anwendung schützen können.

1. Binden Sie in `App/Scripts/app.js` das Modul „adal.js“ ein:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Initialisieren Sie `adalProvider` mit den Konfigurationswerten Ihrer Anwendungsregistrierung, ebenfalls in `App/Scripts/app.js`:

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. Zum Schützen der Ansicht `TodoList` in der App ist nur eine Codezeile erforderlich: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Zusammenfassung
Sie verfügen jetzt über eine sichere einseitige Anwendung, mit der Benutzer angemeldet und per Bearertoken geschützte Anforderungen für die Back-End-API ausgegeben werden können. Wenn ein Benutzer nun auf den Link **TodoList** klickt, erfolgt durch „adal.js“ automatisch eine Umleitung zu Azure AD, um bei Bedarf die Anmeldung auszuführen. Außerdem ordnet „adal.js“ allen AJAX-Anforderungen, die an das Back-End der Anwendung gesendet werden, automatisch ein Zugriffstoken zu.  

Die obigen Angaben beschreiben die erforderlichen Mindestvoraussetzungen zum Erstellen einer einseitigen Anwendung per „adal.js“. Einige andere Features sind jedoch in einseitigen Anwendungen nützlich:

* Zum expliziten Ausgeben von Anmeldungs- und Abmeldungsanforderungen können Sie Funktionen in Ihren Controllern definieren, mit denen „adal.js“ aufgerufen wird.  In `App/Scripts/homeCtrl.js`:

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* Möglicherweise möchten Sie in der App-UI Benutzerinformationen präsentieren. Der ADAL-Dienst wurde dem `userDataCtrl`-Controller bereits hinzugefügt, sodass Sie auf das `userInfo`-Objekt in der zugeordneten Ansicht (`App/Views/UserData.html`) zugreifen können:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* In manchen Fällen möchten Sie überprüfen, ob der Benutzer angemeldet ist. Sie können auch das `userInfo`-Objekt verwenden, um diese Informationen zu sammeln.  In `index.html` können Sie beispielsweise je nach Authentifizierungsstatus entweder die Schaltfläche **Anmelden** oder **Abmelden** anzeigen:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Ihre in Azure AD integrierte einseitige Anwendung kann Benutzer authentifizieren, das Back-End mit OAuth 2.0 sicher aufrufen und grundlegende Informationen zum Benutzer abrufen. Sofern nicht bereits geschehen, ist es nun an der Zeit, Ihren Mandanten mit Benutzern zu füllen. Führen Sie Ihre einseitige Aufgabenlistenanwendung aus, und melden Sie sich mit einem der Benutzer an. Fügen Sie der Aufgabenliste des Benutzers Aufgaben hinzu, melden Sie sich ab, und melden Sie sich dann wieder an.

Adal.js erleichtert Ihnen die Integration allgemeiner Identitätsfunktionen in Ihrer Anwendung. Es übernimmt die unangenehmen Verwaltungsarbeiten für Sie – die Cacheverwaltung, die Unterstützung des OAuth-Protokolls, die Anzeige einer Anmeldeschnittstelle für den Benutzer, die Aktualisierung abgelaufener Tokens und vieles mehr.

Das vollständige Beispiel (ohne Ihre Konfigurationswerte) steht in [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip) zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte
Sie können sich nun weiteren Szenarien zuwenden. Wie wäre es zum Beispiel mit Folgendem: [Aufrufen einer CORS-Web-API aus einer einseitigen Anwendung](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
