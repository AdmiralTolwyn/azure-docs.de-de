---
title: Anwendungstypen für v2. 0 | Azure
description: App- und Szenariotypen, die vom Azure Active Directory v2.0-Endpunkt unterstützt werden.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: 94a072d3c8bde9e4fb16342db1e0705bfc22cf77
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539584"
---
# <a name="application-types-for-v20"></a>Anwendungstypen für v2. 0

Der Azure Active Directory (Azure AD) v2.0-Endpunkt unterstützt die Authentifizierung für eine Vielzahl moderner App-Architekturen, die alle auf den branchenüblichen Standardprotokollen [OAuth 2.0 oder OpenID Connect](active-directory-v2-protocols.md) basieren. Dieser Artikel beschreibt die App-Typen, die Sie unabhängig von der bevorzugten Sprache oder Plattform mithilfe von Azure AD v2.0 erstellen können. Die Informationen in diesem Artikel dienen Ihrem Verständnis der allgemeinen Szenarios, bevor Sie [mit Code arbeiten](v2-overview.md#getting-started).

> [!NOTE]
> Der v2.0-Endpunkt unterstützt nicht alle Szenarios und Funktionen von Azure Active Directory. Lesen Sie die Informationen zu den [Einschränkungen des v2.0-Endpunkts](active-directory-v2-limitations.md), um herauszufinden, ob Sie den v2.0-Endpunkt verwenden sollten.

## <a name="the-basics"></a>Grundlagen

Sie müssen jede App, die den v2.0-Endpunkt verwendet, im [Microsoft-Portal für die Anwendungsregistrierung](https://apps.dev.microsoft.com) registrieren. Beim App-Registrierungsvorgang werden die folgenden Werte für die App erfasst und zugewiesen:

* Eine **Anwendungs-ID**, die Ihre App eindeutig identifiziert
* Ein **Umleitungs-URI**, der zum Umleiten von Antworten zurück an die App verwendet werden kann
* Einige andere szenariospezifische Werte

Nähere Informationen finden Sie im Artikel zum [Registrieren einer App](quickstart-v2-register-an-app.md).

Nachdem die App registriert ist, erfolgt die Kommunikation zwischen der App und Azure AD durch Senden von Anforderungen an den Azure AD v2.0-Endpunkt. Wir stellen Open-Source-Frameworks und -Bibliotheken bereit, über die die Details dieser Anforderungen verarbeitet werden. Zudem können Sie auch selbst die Authentifizierungslogik implementieren, indem Sie Anforderungen an diese Endpunkte erstellen:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Single-Page-Apps (JavaScript)

Viele moderne Apps besitzen ein Single-Page-App-Front-End, das in erster Linie in JavaScript geschrieben ist. Häufig wird dazu ein Framework verwendet, z.B. AngularJS, Ember.js oder Durandal.js. Der Azure AD v2.0-Endpunkt unterstützt diese Apps mithilfe des [impliziten OAuth 2.0-Flusses](v2-oauth2-implicit-grant-flow.md).

Bei diesem Ablauf empfängt die App Token direkt vom v2.0-Autorisierungsendpunkt ohne jegliche Server-zu-Server-Kommunikation. Die gesamte Authentifizierungslogik und Sitzungsverarbeitung erfolgt ohne zusätzliche Seitenumleitungen vollständig im JavaScript-Client.

![Impliziter Authentifizierungsablauf](./media/v2-app-types/convergence_scenarios_implicit.png)

Um dieses Szenario in Aktion zu sehen, testen Sie eines der Codebeispiele für Single-Page-Apps im Abschnitt [Erste Schritte mit v2.0](v2-overview.md#getting-started).

## <a name="web-apps"></a>Web-Apps

Bei Web-Apps (.NET, PHP, Java, Ruby, Python, Node), auf die der Benutzer über einen Browser zugreift, können Sie für die Benutzeranmeldung [OpenID Connect](active-directory-v2-protocols.md) verwenden. In OpenID Connect empfängt die Web-App ein ID-Token. Ein ID-Token ist ein Sicherheitstoken, das die Identität des Benutzers überprüft und Informationen über den Benutzer in Form von Ansprüchen enthält:

```
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Weitere Informationen zu verschiedenen Tokentypen, die im v2.0-Endpunkt verwendet werden, finden Sie in der Referenz zu [Zugriffstoken](access-tokens.md) und zu [`id_token`](id-tokens.md).

In Webserver-Apps werden beim Authentifizierungsablauf für die Anmeldung folgende allgemeine Schritte ausgeführt:

![Authentifizierungsablauf für Web-Apps](./media/v2-app-types/convergence_scenarios_webapp.png)

Sie können die Identität des Benutzers validieren, indem Sie das ID-Token mit einem öffentlichen Signaturschlüssel überprüfen, der vom v2.0-Endpunkt empfangen wird. Ein Sitzungscookie wird festgelegt, das zur Identifizierung des Benutzers in nachfolgenden Seitenanforderungen verwendet werden kann.

Um dieses Szenario in Aktion zu sehen, können Sie eines der Web-App-Codebeispiele für die Anmeldung im Abschnitt [Erste Schritte mit v2.0](v2-overview.md#getting-started) testen.

Neben der einfachen Anmeldung benötigt eine Webserver-App möglicherweise Zugriff auf einen anderen Webdienst, z.B. auf eine REST-API. In diesem Fall wird die Webserver-App in einem kombinierten OpenID Connect- und OAuth 2.0-Vorgang ausgeführt, indem der [OAuth 2.0-Autorisierungscodefluss](active-directory-v2-protocols.md) verwendet wird. Weitere Informationen zu diesem Szenario finden Sie in den [ersten Schritten mit Web-Apps und Web-APIs](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Web-APIs

Mit dem v2.0-Endpunkt können Sie Webdienste schützen, z.B. die RESTful-Web-API Ihrer App. Anstelle von ID-Token und Sitzungscookies verwendet eine Web-API ein OAuth 2.0-Zugriffstoken zum Sichern der zugehörigen Daten und zum Authentifizieren eingehender Anforderungen. Der Aufrufer einer Web-API fügt wie folgt ein Zugriffstoken im Autorisierungsheader einer HTTP-Anforderung an:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Die Web-API verwendet das Zugriffstoken zum Überprüfen der Identität des API-Aufrufers und zum Extrahieren von Informationen über den Aufrufer aus Ansprüchen, die im Zugriffstoken codiert sind. Weitere Informationen zu verschiedenen Tokentypen, die im v2.0-Endpunkt verwendet werden, finden Sie in der Referenz zu [Zugriffstoken](access-tokens.md) und zu [`id_token`](id-tokens.md).

Eine Web-API kann Benutzern die Möglichkeit geben, sich für oder gegen bestimmte Funktionen oder Daten zu entscheiden, indem sie Berechtigungen erhalten, die auch [Bereiche](v2-permissions-and-consent.md) genannt werden. Damit eine aufrufende App Berechtigungen für einen Bereich erhält, muss der Benutzer während eines Ablaufs seine Zustimmung für den Bereich erteilen. Der v2.0-Endpunkt fragt die Zustimmung des Benutzers ab und zeichnet dann die Berechtigungen in allen Zugriffstoken auf, die die Web-API empfängt. Die Web-API überprüft das Zugriffstoken, das bei jedem Aufruf empfangen wird, und führt Autorisierungsprüfungen durch.

Eine Web-API kann Zugriffstoken von allen App-Typen empfangen, z.B. von Webserver-Apps, Desktop-Apps, mobilen Apps, Single Page-Apps, serverseitigen Daemons und selbst von anderen Web-APIs. Der allgemeine Ablauf für eine Web-API sieht wie folgt aus:

![Authentifizierungsablauf für eine Web-API](./media/v2-app-types/convergence_scenarios_webapi.png)

Informationen zum Schützen einer Web-API mithilfe von OAuth2-Zugriffstoken finden Sie in den Web-API-Codebeispielen im Abschnitt [Erste Schritte mit v2.0](v2-overview.md#getting-started).

Oftmals müssen Web-APIs auch ausgehende Anforderungen an nachgelagerte Web-APIs stellen, die von Azure Active Directory geschützt werden. Zu diesem Zweck können Web-APIs den Fluss **Im Namen von** von Azure AD nutzen, der der Web-API ermöglicht, ein eingehendes Zugriffstoken gegen ein anderes Zugriffstoken zu tauschen, das in ausgehenden Anforderungen verwendet werden soll. Der Fluss „Im Namen von“ des v2.0-Endpunkts wird [hier detailliert beschrieben](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobile und native Apps

Auf Geräten installierte Apps, z.B. mobile Apps und Desktop-Apps, benötigen häufig Zugriff auf Back-End-Dienste oder Web-APIs, die im Auftrag eines Benutzers Daten speichern und Funktionen ausführen. Diese Apps können sich mithilfe des [OAuth 2.0-Autorisierungscodeflusses](v2-oauth2-auth-code-flow.md) bei Back-End-Diensten anmelden und die Autorisierung hinzufügen.

Bei diesem Ablauf empfängt die App bei der Anmeldung des Benutzers einen Autorisierungscode vom v2.0-Endpunkt. Der Autorisierungscode stellt die Berechtigung der App zum Aufrufen von Back-End-Diensten im Namen des angemeldeten Benutzers dar. Die App kann den Autorisierungscode im Hintergrund gegen ein OAuth 2.0-Zugriffstoken und ein Aktualisierungstoken austauschen. Die App kann mithilfe des Zugriffstokens Web-APIs in HTTP-Anforderungen authentifizieren und mithilfe des Aktualisierungstokens neue Zugriffstoken abrufen, wenn die älteren Zugriffstoken abgelaufen sind.

![Authentifizierungsablauf für native Apps](./media/v2-app-types/convergence_scenarios_native.png)

## <a name="daemons-and-server-side-apps"></a>Daemons und serverseitige Apps

Apps, die lang andauernde Prozesse enthalten oder ohne Benutzereingriff ausgeführt werden, benötigen auch die Möglichkeit, auf sichere Ressourcen wie Web-APIs zuzugreifen. Diese Apps können mithilfe der App-Identität (anstelle der delegierten Benutzeridentität) über den OAuth 2.0-Ablauf für Clientanmeldeinformationen die Authentifizierung durchführen und Token abrufen. Sie können die Identität der App mit einem geheimen Clientschlüssel oder einem Zertifikat nachweisen. Weitere Informationen finden Sie unter [Authentifizierung bei Azure AD in Daemon-Apps mit Zertifikaten](https://azure.microsoft.com/resources/samples/active-directory-dotnet-daemon-certificate-credential/).

Bei diesem Ablauf erhält die App Token durch die direkte Interaktion mit dem `/token`-Endpunkt:

![Authentifizierungsablauf für Daemon-Apps](./media/v2-app-types/convergence_scenarios_daemon.png)

Informationen zum Erstellen einer Daemon-App finden Sie in der [Dokumentation zu Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md). Sie können aber auch eine [.NET-Beispiel-App](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2) testen.
