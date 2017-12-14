---
title: 'Beziehen eines Tokens mit einer iOS-Anwendung: Azure Active Directory B2C | Microsoft-Dokumentation'
description: "In diesem Artikel erfahren Sie, wie Sie eine iOS-App erstellen, die Appauth mit Azure Active Directory B2C zum Verwalten von Benutzeridentitäten und Authentifizieren von Benutzern verwendet."
services: active-directory-b2c
documentationcenter: ios
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: d818a634-42c2-4cbd-bf73-32fa0c8c69d3
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objectivec
ms.topic: article
ms.date: 03/07/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: cc26d4d2209564fc5c994c2bc73f6a572fe87d28
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: Anmelden mit einer iOS-Anwendung

Die Microsoft Identity-Plattform nutzt offene Standards, z.B. OAuth2 und OpenID Connect. Mit einem offenen Protokoll profitieren Entwickler von einer größeren Auswahl an Bibliotheken für die Integration unserer Dienste. Wir haben diese und weitere exemplarische Vorgehensweisen bereitgestellt, um Entwickler beim Schreiben von Anwendungen zu unterstützen, die mit der Microsoft-Identitätsplattform verbunden sind. Die meisten Bibliotheken, die die [RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749)-Spezifikation implementieren, können eine Verbindung mit der Microsoft Identity-Plattform herstellen.

> [!WARNING]
> Microsoft stellt keine Fehlerbehebungen für diese Drittanbieterbibliotheken bereit und hat diese Bibliotheken keinerlei Prüfungen unterzogen. In diesem Beispiel wird eine Drittanbieterbibliothek namens AppAuth verwendet, deren Kompatibilität in grundlegenden Szenarien mit Azure AD B2C getestet wurde. Probleme und Funktionsanforderungen müssen direkt an das Open Source-Projekt der Bibliothek weitergeleitet werden. [hier finden Sie weitere Informationen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)
>
>

Wenn OAuth2 oder OpenID Connect neu für Sie ist, ergibt diese Beispielkonfiguration für Sie vielleicht noch nicht viel Sinn. Wir empfehlen Ihnen, sich eine kurze [Übersicht über das Protokoll](active-directory-b2c-reference-protocols.md)anzusehen.

## <a name="get-an-azure-ad-b2c-directory"></a>Erstellen eines Azure AD B2C-Verzeichnisses
Bevor Sie Azure AD B2C verwenden können, müssen Sie ein Verzeichnis oder einen Mandanten erstellen. Ein Verzeichnis ist ein Container für alle Benutzer, Apps, Gruppen usw. [Erstellen Sie zunächst ein B2C-Verzeichnis](active-directory-b2c-get-started.md) , sofern noch keines vorhanden ist.

## <a name="create-an-application"></a>Erstellen einer Anwendung
Als Nächstes müssen Sie in Ihrem B2C-Verzeichnis eine App erstellen. Durch die App-Registrierung werden Azure AD die Informationen bereitgestellt, die für die sichere Kommunikation mit Ihrer App erforderlich sind. Befolgen Sie zum Erstellen einer Mobile App [diese Anweisungen](active-directory-b2c-app-registration.md). Führen Sie folgende Schritte aus:

* Fügen Sie einen **nativen Client** in die Anwendung ein.
* Kopieren Sie die **Anwendungs-ID** , die Ihrer App zugewiesen ist. Diese GUID benötigen Sie später noch.
* Richten Sie eine **Umleitungs-URI** mit einem benutzerdefinierten Schema ein (z.B. com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Diese URI benötigen Sie später noch.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Erstellen der Richtlinien
In Azure AD B2C wird jede Benutzererfahrung durch eine [Richtlinie](active-directory-b2c-reference-policies.md)definiert. Diese App enthält eine kombinierte Identitätsoberfläche für die Anmeldung und Registrierung. Erstellen Sie diese Richtlinie wie im [Artikel zur Richtlinienreferenz](active-directory-b2c-reference-policies.md#create-a-sign-up-policy) beschrieben. Achten Sie beim Erstellen der Richtlinie auf Folgendes:

* Wählen Sie unter **Registrierungsattribute** das Attribut **Anzeigename** aus.  Sie können auch andere Attribute auswählen.
* Wählen Sie unter **Anwendungsansprüche** die Ansprüche **Anzeigename** und **Objekt-ID des Benutzers** aus. Sie können auch andere Ansprüche auswählen.
* Notieren Sie sich die **Namen** der einzelnen Richtlinien nach ihrer Erstellung. Dem Namen der Richtlinie wird beim Speichern der Richtlinie das Präfix `b2c_1_` vorangestellt.  Sie benötigen den Richtliniennamen später noch.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Nachdem Sie die Richtlinien erstellt haben, können Sie Ihre App erstellen.

## <a name="download-the-sample-code"></a>Herunterladen des Beispielcodes
Wir haben ein funktionsfähiges Beispiel bereitgestellt, das AppAuth mit Azure AD B2C [auf GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c) verwendet. Sie können den Code herunterladen und ausführen. Folgen Sie den Anweisungen in der Datei [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md), um Ihren eigenen Azure AD B2C-Mandanten zu verwenden.

Dieses Beispiel wurde anhand der README-Anweisungen vom [iOS AppAuth Projekt auf GitHub](https://github.com/openid/AppAuth-iOS) erstellt. Weitere Informationen zur Funktionsweise des Beispiels und der Bibliothek finden Sie in der AppAuth-README auf GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Ändern Ihrer Anwendung zur Verwendung von Azure AD B2C mit AppAuth

> [!NOTE]
> AppAuth unterstützt iOS 7 und höher.  Um Anmeldeinformationen für soziale Netzwerke auf Google zu unterstützen, benötigt SFSafariViewController jedoch iOS 9 oder höher.
>

### <a name="configuration"></a>Konfiguration

Sie können die Kommunikation mit Azure AD B2C konfigurieren, indem Sie die Autorisierungsendpunkt- und die Token-Endpunkt-URIs angeben.  Zum Generieren dieser URIs benötigen Sie die folgenden Informationen:
* Mandanten-ID (z.B. contoso.onmicrosoft.com)
* Richtlinienname (z.B. B2C\_1\_SignUpIn)

Sie können die Token-Endpunkt-URI generieren, indem Sie Tenant\_ID (Mandanten-ID) und Policy\_Name (Richtlinienname) in der folgenden URL ersetzen:

```objc
static NSString *const tokenEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Sie können die Autorisierungsendpunkt-URI generieren, indem Sie Tenant\_ID (Mandanten-ID) und Policy\_Name (Richtlinienname) in der folgenden URL ersetzen:

```objc
static NSString *const authorizationEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Führen Sie den folgenden Code aus, um Ihr AuthorizationServiceConfiguration-Objekt zu erstellen:

```objc
OIDServiceConfiguration *configuration = 
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Autorisieren

Nach dem Konfigurieren oder Abrufen einer Autorisierungsdienstkonfiguration kann eine Autorisierungsanforderung erstellt werden. Zum Erstellen dieser Anforderung benötigen Sie die folgenden Informationen:  
* Client-ID (z.B. 00000000-0000-0000-0000-000000000000)
* Umleitungs-URI mit einem benutzerdefinierten Schema (z.B. com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect)

Sie sollten beide Elemente beim [Registrieren Ihrer App](#create-an-application) gespeichert haben.

```objc
OIDAuthorizationRequest *request = 
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow = 
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Um Ihre Anwendung zum Verarbeiten der Umleitung an den URI mit dem benutzerdefinierten Schema einzurichten, müssen Sie die Liste der URL-Schemas in der Datei „Info.plist“ aktualisieren:
* Öffnen Sie die Datei „Info.plist“.
* Zeigen Sie mit dem Mauszeiger auf eine Zeile wie „Bundle OS Type Code“, und klicken Sie auf das Symbol \+.
* Nennen Sie die neue Zeile „URL types“.
* Klicken Sie auf den Pfeil links neben „URL types“, um die Struktur zu öffnen.
* Klicken Sie auf den Pfeil links neben „Item 0“, um die Struktur zu öffnen.
* Benennen Sie das erste Element unterhalb von „Item 0“ in „URL Schemes“ um.
* Klicken Sie auf den Pfeil links neben „URL Schemes“, um die Struktur zu öffnen.
* In der Spalte „Value“ befindet sich links neben „Item 0“ unterhalb von „URL Schemes“ ein leeres Feld.  Legen Sie den Wert auf das eindeutige Schema Ihrer Anwendung fest.  Der Wert muss mit dem in der Umleitungs-URL beim Erstellen des OIDAuthorizationRequest-Objekts verwendeten Schema übereinstimmen.  In diesem Beispiel haben wir das Schema „com.onmicrosoft.fabrikamb2c.exampleapp“ verwendet.

Informationen zum restlichen Prozess finden Sie im [AppAuth Handbuch](https://openid.github.io/AppAuth-iOS/). Sehen Sie sich [unser Beispiel](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c) für die ersten Schritte mit einer funktionsfähigen App an. Folgen Sie den Anweisungen in der Datei [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) aus, um Ihre eigene Azure AD B2C-Konfiguration einzugeben.

Wir sind stets offen für Feedback und Vorschläge. Wenn Sie Probleme mit diesem Thema oder Vorschläge zur Verbesserung dieses Inhalts haben, würden wir uns über Ihr Feedback unten auf der Seite freuen. Anforderungen neuer Features geben Sie bitte unter [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) ein.
