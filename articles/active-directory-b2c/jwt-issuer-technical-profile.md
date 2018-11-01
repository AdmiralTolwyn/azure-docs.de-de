---
title: Definieren eines technischen Profils für einen JWT-Tokenaussteller in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein technisches Profil für einen JWT-Tokenaussteller in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C definieren.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 25352d12e578c289ccb4ab8aab60dc55a444762e
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413501"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines technischen Profils für einen JWT-Tokenaussteller in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C stellt bei der Verarbeitung der einzelnen Authentifizierungsabläufe verschiedene Arten von Sicherheitstoken aus. Ein technisches Profil für einen JWT-Tokenaussteller gibt ein JWT-Token aus, das an die Anwendung der vertrauenden Seite zurückgegeben wird. Dieses technische Profil ist in der Regel der letzte Orchestrierungsschritt in der User Journey.

## <a name="protocol"></a>Protokoll

Das **Name**-Attribut des **Protocol**-Elements muss auf `None` festgelegt werden. Legen Sie das **OutputTokenFormat**-Element auf `JWT` fest.

Das folgende Beispiel zeigt ein technisches Profil für `JwtIssuer`:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```
 
## <a name="input-output-and-persist-claims"></a>Eingabe-, Ausgabe- und Aufbewahrungsansprüche

Die Elemente **InputClaims**, **OutputClaims** und **PersistClaims** sind leer oder fehlen. Die Elemente **InutputClaimsTransformations** und **OutputClaimsTransformations** sind ebenfalls nicht vorhanden.

## <a name="metadata"></a>Metadaten

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | JA | Der Anspruch, der in OAuth2-Autorisierungscodes und Aktualisierungstoken als Benutzeridentitätsanspruch verwendet werden soll. Standardmäßig sollten Sie dieses Attribut auf `objectId` festlegen, es sei denn, Sie möchten einen anderen SubjectNamingInfo-Anspruchstyp angeben. | 
| SendTokenResponseBodyWithJsonNumbers | Nein  | Immer auf `true` festgelegt. Legen Sie für ein altes Format, in dem numerische Werte als Zeichenfolgen anstelle von JSON-Zahlen angegeben sind, `false` fest. Dieses Attribut ist für Clients erforderlich, die eine Abhängigkeit von einer früheren Implementierung übernommen haben, bei der diese Eigenschaften als Zeichenfolgen zurückgegeben wurden. | 
| token_lifetime_secs | Nein  | Lebensdauern von Zugriffstoken. Die Lebensdauer des OAuth 2.0-Bearertokens, das zum Zugriff auf eine geschützte Ressource verwendet wird. Der Standardwert ist 3.600 Sekunden (1 Stunde). Der Mindestwert (inklusive) ist 300 Sekunden (5 Minuten). Der Höchstwert (inklusive) ist 86.400 Sekunden (24 Stunden). | 
| id_token_lifetime_secs | Nein  | Lebensdauern von ID-Token. Der Standardwert ist 3.600 Sekunden (1 Stunde). Der Mindestwert (inklusive) ist 300 Sekunden (5 Minuten). Der Höchstwert (inklusive) ist 86.400 Sekunden (24 Stunden). | 
| refresh_token_lifetime_secs | Nein  | Lebensdauern von Aktualisierungstoken. Der maximale Zeitraum, vor dessen Verstreichen ein Aktualisierungstoken verwendet werden kann, um ein neues Zugriffstoken zu erwerben, wenn Ihrer Anwendung der offline_access-Bereich gewährt wurde. Der Standardwert ist 1.209.600 Sekunden (14 Tage). Der Mindestwert (inklusive) ist 86.400 Sekunden (24 Stunden). Der Höchstwert (inklusive) ist 7.776.000 Sekunden (90 Tage). | 
| rolling_refresh_token_lifetime_secs | Nein  | Lebensdauer für gleitendes Fenster des Aktualisierungstokens. Nach Ablauf dieses Zeitraums muss der Benutzer sich erneut authentifizieren – unabhängig von der Gültigkeitsdauer des zuletzt von der Anwendung erworbenen Aktualisierungstokens. Wenn Sie keine Lebensdauer mit gleitendem Fenster erzwingen möchten, legen Sie den Wert von allow_infinite_rolling_refresh_token auf `true` fest. Der Standardwert ist 7.776.000 Sekunden (90 Tage). Der Mindestwert (inklusive) ist 86.400 Sekunden (24 Stunden). Der Höchstwert (inklusive) ist 31.536.000 Sekunden (365 Tage). | 
| allow_infinite_rolling_refresh_token | Nein  | Bei einer Festlegung auf `true` läuft die Lebensdauer für gleitendes Fenster des Aktualisierungstokens nie ab. |
| IssuanceClaimPattern | JA | Steuert den Ausstelleranspruch (iss). Einer der Werte:<ul><li>AuthorityAndTenantGuid: Der Ausstelleranspruch enthält Ihren Domänennamen, z.B. `login.microsoftonline` oder `tenant-name.b2clogin.com`, und die Mandanten-ID https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/.</li><li>AuthorityWithTfp: Der Ausstelleranspruch enthält Ihren Domänennamen, z.B. `login.microsoftonline` oder `tenant-name.b2clogin.com`, Ihre Mandanten-ID und den Richtliniennamen der vertrauenden Seite. https://login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> | 
| AuthenticationContextReferenceClaimPattern | Nein  | Steuert den Wert des `acr`-Anspruchs.<ul><li>None: Azure AD B2C stellt den acr-Anspruch nicht aus.</li><li>PolicyId: Der `acr`-Anspruch enthält den Namen der Richtlinie.</li></ul>Die Optionen zum Festlegen dieses Werts sind „TFP“ (Vertrauensframework-Richtlinie) und „ACR“ (Authentifizierungskontext-Referenz). Es wird empfohlen, diesen Wert auf „TFP“ festzulegen. Um den Wert festzulegen, stellen Sie sicher, dass das `<Item>` mit dem `Key="AuthenticationContextReferenceClaimPattern"` vorhanden ist und der Wert `None` lautet. Fügen Sie in Ihrer Richtlinie der vertrauenden Seite das <OutputClaims>-Element wie folgt hinzu: `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`. Stellen Sie außerdem sicher, dass die Richtlinie den Anspruchstyp `<ClaimType Id="trustFrameworkPolicy"> <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` enthält. | 

## <a name="cryptographic-keys"></a>Kryptografische Schlüssel

Das CryptographicKeys-Element enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| issuer_secret | JA | Das X509 Zertifikat (RSA-Schlüsselsatz) zum Signieren des JWT-Tokens. Dies ist der `B2C_1A_TokenSigningKeyContainer`-Schlüssel, den Sie in [Erste Schritte mit benutzerdefinierten Richtlinien](active-directory-b2c-get-started-custom.md) konfiguriert haben. | 
| issuer_refresh_token_key | JA | Das X509 Zertifikat (RSA-Schlüsselsatz) zum Verschlüsseln des Aktualisierungstokens. Sie haben den `B2C_1A_TokenEncryptionKeyContainer`-Schlüssel in [Erste Schritte mit benutzerdefinierten Richtlinien](active-directory-b2c-get-started-custom.md) konfiguriert. |














