---
title: Azure Active Directory B2C | Microsoft Docs
description: "Hier erfahren Sie, wie Sie Apps direkt mit den von Azure Active Directory B2C unterstützten Protokollen erstellen."
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 5e407d0a-73a2-4d74-ac81-3aa6c31ddcee
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 24e86b2a0c47576eba67f74c88bf9b9a42394653
ms.openlocfilehash: 556da1513345b3cadf4fda622bb351835c114845


---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: Authentifizierungsprotokolle
Azure Active Directory (Azure AD) B2C bietet Identity-as-a-Service (IDaaS) für Ihre Apps durch die Unterstützung zweier gängiger Standardprotokolle: OpenID Connect und OAuth 2.0. Auch wenn der Dienst standardkonform ist, kann es feine Unterschiede zwischen zwei Implementierungen dieser Protokolle geben.  Die in diesem Leitfaden bereitgestellten Informationen sind nützlich, wenn Sie Code direkt durch Senden und Verarbeiten von HTTP-Anforderungen schreiben, anstatt eine unserer Open Source-Bibliotheken zu nutzen. Es wird empfohlen, diese Seite zu lesen, bevor Sie sich mit den Details der spezifischen Protokolle beschäftigen. Aber wenn Sie bereits mit Azure AD B2C vertraut sind, können Sie direkt zu den [Protokollreferenzhandbüchern](#protocols)wechseln.

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Grundlagen
Jede App, die Azure AD B2C verwendet, muss im B2C-Verzeichnis im [Azure-Portal](https://portal.azure.com)registriert werden. Beim App-Registrierungsprozess werden einige Werte gesammelt und der App zugewiesen:

* Eine **Anwendungs-ID** , die Ihre App eindeutig identifiziert.
* Einen **Umleitungs-URI** oder **Paketbezeichner**, der zum Umleiten von Antworten zurück an die App verwendet werden kann.
* Einige andere szenariospezifische Werte. Weitere Informationen finden Sie im Abschnitt [zum Registrieren Ihrer Anwendung](active-directory-b2c-app-registration.md).

Nach dem Registrieren Ihrer App kommuniziert diese mit Azure AD, indem sie Anforderungen an den v2.0-Endpunkt sendet:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

In fast allen OAuth- und OpenID Connect-Abläufen gibt es beim Austausch vier Beteiligte:

![OAuth 2.0-Rollen](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

* Der **Autorisierungsserver** ist der Azure AD v2.0-Endpunkt. Er kümmert sich um die sichere Verwaltung rund um Benutzerinformationen und Zugriff. Zudem verarbeitet er die Vertrauensstellungen zwischen den Parteien in einem Ablauf. Er ist verantwortlich für das Überprüfen der Identität des Benutzers, das Erteilen und Widerrufen des Zugriffs auf Ressourcen und das Ausstellen von Token. Er wird auch als Identitätsanbieter bezeichnet.
* Beim **Ressourcenbesitzer** handelt es sich normalerweise um den Endbenutzer. Diese Person besitzt die Daten und hat die Möglichkeit, Dritten den Zugriff auf die Daten oder die Ressource zu gewähren.
* Der **OAuth-Client** ist Ihre App. Sie wird durch die Anwendungs-ID identifiziert. Sie ist normalerweise die Komponente, mit der die Endbenutzer interagieren. Sie fordert auch Token vom Autorisierungsserver an. Der Ressourcenbesitzer muss dem Client die Berechtigung für den Zugriff auf die Ressource erteilen.
* Der **Ressourcenserver** ist der Ort, an dem die Ressource oder die Daten abgelegt sind. Er vertraut auf die sichere Authentifizierung und Autorisierung des OAuth-Clients durch den Autorisierungsserver. Außerdem verwendet er Bearerzugriffstoken, um sicherzustellen, dass der Zugriff auf eine Ressource gewährt werden kann.

## <a name="policies"></a>Richtlinien
Die Richtlinien in Azure AD B2C sind wohl das wichtigste Feature des Diensts. Azure AD B2C erweitert die Standardprotokolle OAuth 2.0 und OpenID Connect durch die Einführung von Richtlinien. Diese ermöglichen Azure AD B2C viel mehr als nur die einfache Authentifizierung und Autorisierung. Richtlinien beschreiben Benutzeroberflächen im Zusammenhang mit der Endkundenidentität, einschließlich Registrierung, Anmeldung und Profilbearbeitung. Richtlinien können in einer Administratorbenutzeroberfläche definiert werden. Sie können mithilfe eines speziellen Abfrageparameters in HTTP-Authentifizierungsanforderungen ausgeführt werden. Richtlinien sind kein Standardfeature von OAuth 2.0 und OpenID Connect. Sie sollten sich also die Zeit nehmen, sich damit vertraut zu machen. Weitere Informationen finden Sie im [Referenzhandbuch zu Azure AD B2C-Richtlinien](active-directory-b2c-reference-policies.md).

## <a name="tokens"></a>Token
Die Implementierung von OAuth 2.0 und OpenID Connect in Azure AD B2C macht ausgiebig Gebrauch von Bearertoken (auch in Form von JSON-Webtoken, JWTs). Ein Bearertoken ist ein einfaches Sicherheitstoken, das dem Inhaber, dem „Bearer“, den Zugriff auf eine geschützte Ressource ermöglicht. Der Bearer ist jede beliebige Partei, die das Token vorweisen kann. Azure AD muss eine Partei zunächst authentifizieren, damit sie ein Bearertoken erhalten kann. Falls jedoch nicht die erforderlichen Schritte ausgeführt werden, um das Token bei der Übertragung und Speicherung zu schützen, kann das Token von einer unbefugten Partei abgefangen und verwendet werden.

Einige Sicherheitstoken verfügen über einen integrierten Mechanismus, der eine Verwendung durch nicht autorisierte Parteien verhindert. Bearertoken besitzen diesen Mechanismus jedoch nicht. Sie müssen über einen sicheren Kanal wie etwa Transport Layer Security (HTTPS) übertragen werden. Wird ein Bearertoken außerhalb eines sicheren Kanals gesendet, kann eine böswillige Partei das Token mithilfe eines Man-in-the-Middle-Angriffs abrufen und damit unautorisiert auf eine geschützte Ressource zugreifen. Die gleichen Sicherheitsprinzipien gelten für die (Zwischen-)Speicherung von Bearertoken zur späteren Verwendung. Stellen Sie daher sicher, dass Ihre App Bearertoken stets auf sichere Weise überträgt und speichert.

Weitere Überlegungen zur Sicherheit von Bearertoken finden Sie unter [RFC 6750, Abschnitt 5](http://tools.ietf.org/html/rfc6750).

Weitere Informationen zu verschiedenen Tokentypen, die in Azure AD B2C verwendet werden, finden Sie in der [Referenz zu Azure AD-Token](active-directory-b2c-reference-tokens.md).

## <a name="protocols"></a>Protokolle
Wenn Sie bereit sind, sich einige Beispielanforderungen anzusehen, können Sie mit einem der folgenden Tutorials beginnen. Jedes Lernprogramm entspricht einem bestimmten Szenario. Wenn Sie Hilfe beim Ermitteln des richtigen Arbeitsablaufs benötigen, informieren Sie sich über die [App-Typen, die mit Azure AD B2C erstellt werden können](active-directory-b2c-apps.md).

* [Erstellen von mobilen und nativen Anwendungen mit OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
* [Erstellen von Web-Apps mithilfe von OpenID Connect](active-directory-b2c-reference-oidc.md)
* [Erstellen von Apps mit einer einzigen Seite mit dem impliziten OAuth 2.0-Fluss](active-directory-b2c-reference-spa.md)




<!--HONumber=Feb17_HO1-->


