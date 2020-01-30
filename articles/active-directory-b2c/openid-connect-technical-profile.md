---
title: Definieren eines technischen OpenID Connect-Profils in einer benutzerdefinierten Richtlinie
titleSuffix: Azure AD B2C
description: Hier erfahren Sie, wie Sie ein technisches OpenID Connect-Profil in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C definieren.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8bda1d3bcce37cbb7b5306d460bddd4652349fe9
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840348"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines technischen OpenID Connect-Profils in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) unterstützt den Identitätsanbieter für das [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/)-Protokoll. OpenId Connect 1.0 definiert eine Identitätsebene über OAuth 2.0 und stellt den Stand der Technik bei modernen Authentifizierungsprotokollen dar. Mit einem technischen OpenID Connect-Profil können Sie einen Verbund mit einem OpenID Connect-basierten Identitätsanbieter wie Azure AD erstellen. Über einen Verbund mit einem Identitätsanbieter können sich Benutzer mit ihren vorhandenen Identitäten aus sozialen Netzwerken oder Unternehmen anmelden.

## <a name="protocol"></a>Protocol

Das **Name**-Attribut des **Protocol**-Elements muss auf `OpenIdConnect` festgelegt werden. Das Protokoll für das technische Profil **MSA-OIDC** ist z.B. `OpenIdConnect`:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>Eingabeansprüche

Die Elemente **InputClaims** und **InputClaimsTransformations** sind nicht erforderlich. Sie können diese zusätzlichen Parameter aber an Ihren Identitätsanbieter senden. Im folgenden Beispiel wird der Autorisierungsanforderung der Parameter **domain_hint** der Abfragezeichenfolge mit dem Wert `contoso.com` hinzugefügt.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Ausgabeansprüche

Das Element **OutputClaims** enthält eine Liste mit Ansprüchen, die vom OpenID Connect-Identitätsanbieter zurückgegeben wurden. Sie müssen den Namen des Anspruchs, der in Ihrer Richtlinie definiert ist, dem Namen, der für den Identitätsanbieter definiert wurde, zuordnen. Sie können auch Ansprüche, die nicht vom Identitätsanbieter zurückgegeben wurden, einfügen, sofern Sie das `DefaultValue`-Attribut festlegen.

Das **OutputClaimsTransformations**-Element darf eine Sammlung von **OutputClaimsTransformation**-Elementen, die zum Ändern der Ausgabeansprüche oder zum Generieren neuer verwendet werden, enthalten.

Das folgende Beispiel zeigt die Ansprüche, die vom Identitätsanbieter Microsoft-Konto zurückgegeben wurden:

- Der Anspruch **sub** wird dem Anspruch **issuerUserId** zugeordnet.
- Der Anspruch **name** wird dem Anspruch **displayName** zugeordnet.
- Dem Anspruch **email** wird kein Name zugeordnet.

Das technische Profil gibt auch Ansprüche zurück, die vom Identitätsanbieter nicht zurückgegeben werden:

- Der Anspruch **identityProvider** enthält den Namen des Identitätsanbieters.
- Der Anspruch **authenticationSource** enthält als Standardwert **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metadaten

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| client_id | Ja | Die Anwendungs-ID des Identitätsanbieters. |
| IdTokenAudience | Nein | Die Zielgruppe von id_token. Wenn eine Angabe erfolgt, überprüft Azure AD B2C, ob das Token in einem Anspruch, der vom Identitätsanbieter zurückgegeben wurde, enthalten und mit dem angegebenen Token identisch ist. |
| METADATA | Ja | Eine URL, die auf ein JSON-Konfigurationsdokument, das gemäß der OpenId Connect Discovery-Spezifikation formatiert ist, verweist und auch als OpenId-Konfigurationsendpunkt bezeichnet wird. |
| ProviderName | Nein | Der Name des Identitätsanbieters. |
| response_types | Nein | Der Antworttyp gemäß der OpenId Connect Core 1.0-Spezifikation. Mögliche Werte: `id_token`, `code` oder `token`. |
| response_mode | Nein | Die Methode, die der Identitätsanbieter verwendet, um das Ergebnis zurück an Azure AD B2C zu senden. Mögliche Werte: `query`, `form_post` (Standard) oder `fragment`. |
| scope | Nein | Der Bereich für die Anforderung gemäß der OpenId Connect Core 1.0-Spezifikation. Beispiele: `openid`, `profile` und `email`. |
| HttpBinding | Nein | Die erwartete HTTP-Bindung an die Endpunkte für Zugriffs- und Anspruchstoken. Mögliche Werte: `GET` oder `POST`.  |
| ValidTokenIssuerPrefixes | Nein | Ein Schlüssel, der für die Anmeldung bei den einzelnen Mandanten verwendet werden kann, wenn ein mehrinstanzenfähiger Identitätsanbieter wie Azure Active Directory verwendet wird. |
| UsePolicyInRedirectUri | Nein | Gibt an, ob beim Erstellen des Umleitungs-URI eine Richtlinie verwendet werden soll. Wenn Sie Ihre Anwendung im Identitätsanbieter konfigurieren, müssen Sie den Umleitungs-URI angeben. Der Umleitungs-URI verweist auf Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`.  Bei Angabe von `false` müssen Sie einen Umleitungs-URI für jede verwendete Richtlinie hinzufügen. Beispiel: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Nein | Gibt an, ob eine Anforderung an einen externen Dienst als fehlerhaft gekennzeichnet werden soll, wenn der HTTP-Statuscode im Bereich 5xx liegt. Der Standardwert lautet `false`. |
| DiscoverMetadataByTokenIssuer | Nein | Gibt an, ob die OIDC-Metadaten mithilfe des Ausstellers im JWT-Token ermittelt werden sollen. |

## <a name="cryptographic-keys"></a>Kryptografische Schlüssel

Das **CryptographicKeys**-Element enthält das folgende Attribut:

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| client_secret | Ja | Der geheime Clientschlüssel der Anwendung des Identitätsanbieters. Der kryptografische Schlüssel ist nur erforderlich, wenn die **response_type**-Metadaten auf `code` festgelegt sind. Azure AD B2C führt in diesem Fall einen weiteren Aufruf zum Austauschen des Autorisierungscode gegen ein Zugriffstoken durch. Wenn die Metadaten auf `id_token` festgelegt wurden, können Sie den kryptografischen Schlüssel weglassen.  |

## <a name="redirect-uri"></a>Umleitungs-URI

Wenn Sie den Umleitungs-URI Ihres Identitätsanbieters konfigurieren, geben Sie `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` an. Ersetzen Sie `{your-tenant-name}` durch Ihren Mandantennamen. Der Umleitungs-URI muss klein geschrieben sein.

Beispiele:

- [Hinzufügen des Microsoft-Kontos (Microsoft Account, MSA) als Identitätsanbieter mithilfe benutzerdefinierter Richtlinien](identity-provider-microsoft-account-custom.md)
- [Anmelden mithilfe von Azure AD-Konten](identity-provider-azure-ad-single-tenant-custom.md)
- [Zulassen, dass Benutzer sich mithilfe von benutzerdefinierten Richtlinien bei einem mehrinstanzenfähigen Azure AD-Identitätsanbieter anmelden](identity-provider-azure-ad-multi-tenant-custom.md)
