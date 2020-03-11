---
title: Definieren eines technischen RESTful-Profils in einer benutzerdefinierten Richtlinie
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie ein technisches RESTful-Profil in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C definieren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4638b5bfc3ff31d0d2149e7ee227c46d3360a306
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254987"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines technischen RESTful-Profils in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) bietet Unterstützung für Ihren eigenen RESTful-Dienst. Azure AD B2C sendet Daten an den RESTful-Dienst in einer Sammlung von Eingabeansprüchen und erhält Daten in einer Sammlung von Ausgabeansprüchen zurück. Mit der RESTful-Dienstintegration haben Sie folgende Möglichkeiten:

- **Überprüfen von Benutzereingabedaten:** verhindert, dass falsch formatierte Daten in Azure AD B2C beibehalten werden. Wenn der Wert des Benutzers nicht gültig ist, gibt Ihr RESTful-Dienst eine Fehlermeldung zurück, in der der Benutzer angewiesen wird, einen Eintrag anzugeben. Beispielsweise können Sie überprüfen, ob die vom Benutzer angegebene E-Mail-Adresse in Ihrer Kundendatenbank vorhanden ist.
- **Überschreiben von Eingabeansprüchen:** ermöglicht das Neuformatieren von Werten in Eingabeansprüchen. Wenn z.B. ein Benutzer den Vornamen vollständig in Kleinbuchstaben oder Großbuchstaben eingibt, können Sie den Namen so formatieren, dass nur der erste Buchstabe groß geschrieben wird.
- **Erweitern von Benutzerdaten:** ermöglicht eine stärkere Integration in die Branchenanwendungen eines Unternehmens. Ihr RESTful-Dienst kann z.B. die E-Mail-Adresse des Benutzers empfangen, die Kundendatenbank abfragen und die Treuenummer des Benutzers an Azure AD B2C zurückgeben. Die Rückgabeansprüche können gespeichert, in den nächsten Orchestrierungsschritten ausgewertet oder in das Zugriffstoken eingebunden werden.
- **Ausführen von benutzerdefinierter Geschäftslogik:** Sie können Pushbenachrichtigungen senden, Unternehmensdatenbanken aktualisieren, einen Benutzermigrationsprozess durchführen, Berechtigungen verwalten, Datenbanken überwachen und andere Aktionen ausführen.

Die Richtlinie kann Eingabeansprüche an Ihre REST-API senden. Die REST-API kann auch Ausgabeansprüche zurückgeben, die Sie später in Ihrer Richtlinie verwenden können, oder sie kann eine Fehlermeldung ausgeben. Sie können die Integration in die RESTful-Dienste auf folgende Weise entwerfen:

- **Technisches Validierungsprofil:** ruft den RESTful-Dienst auf. Mit dem technischen Validierungsprofil werden die vom Benutzer bereitgestellten Daten überprüft, bevor die User Journey fortgesetzt wird. Mit dem technischen Validierungsprofil wird eine Fehlermeldung auf einer Seite mit Selbstbestätigung angezeigt und in Ausgabeansprüche zurückgegeben.
- **Anspruchsaustausch:** Aufrufe an den RESTful-Dienst erfolgen über einen Orchestrierungsschritt. In diesem Szenario gibt es keine Benutzeroberfläche zum Darstellen der Fehlermeldung. Wenn Ihre REST-API einen Fehler zurückgibt, wird der Benutzer zusammen mit der Fehlermeldung zurück zur Anwendung der vertrauenden Seite umgeleitet.

## <a name="protocol"></a>Protocol

Das **Name**-Attribut des **Protocol**-Elements muss auf `Proprietary` festgelegt werden. Das **handler**-Attribut muss den vollqualifizierten Namen der Protokollhandlerassembly, die von Azure AD B2C verwendet wird, enthalten: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Das folgende Beispiel zeigt ein technisches RESTful-Profil:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Eingabeansprüche

Das **InputClaims**-Element enthält eine Liste von Ansprüchen, die an die REST-API gesendet werden sollen. Sie können auch den Namen Ihres Anspruchs dem in der REST-API definierten Namen zuordnen. Das folgende Beispiel zeigt die Zuordnung zwischen Ihrer Richtlinie und der REST-API. Der **givenName**-Anspruch wird als **firstName** und der **surname**-Anspruch wird als **lastName** an die REST-API gesendet. Der **email**-Anspruch wird unverändert festgelegt.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

Das **InputClaimsTransformations**-Element darf eine Sammlung von **InputClaimsTransformation**-Elementen, die zum Ändern der Eingabeansprüche oder zum Generieren neuer verwendet werden, enthalten, bevor es an die REST-API gesendet wird.

## <a name="send-a-json-payload"></a>Senden einer JSON-Nutzlast

Das technische REST-API-Profil ermöglicht es Ihnen, eine komplexe JSON-Nutzlast an einen Endpunkt zu senden.

So senden Sie eine komplexe JSON-Nutzlast:

1. Erstellen Sie die JSON-Nutzlast mit der [GenerateJson](json-transformations.md)-Anspruchstransformation.
1. Gehen Sie im technische REST-API-Profil folgendermaßen vor:
    1. Fügen Sie eine Eingabeanspruchstransformation mit einem Verweis auf die `GenerateJson`-Anspruchstransformation hinzu.
    1. Legen Sie die `SendClaimsIn`-Metadatenoption auf `body` fest.
    1. Legen Sie die `ClaimUsedForRequestPayload`-Metadatenoption auf den Namen des Anspruchs fest, der die JSON-Nutzlast enthält.
    1. Fügen Sie im Eingabeanspruch einen Verweis auf den Eingabeanspruch hinzu, der die JSON-Nutzlast enthält.

Im folgenden `TechnicalProfile`-Beispiel wird eine Überprüfungs-E-Mail mit einem Drittanbieter-E-Mail-Dienst (in diesem Fall SendGrid) gesendet.

```XML
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>Ausgabeansprüche

Das **OutputClaims**-Element enthält eine Liste von Ansprüchen, die von der REST-API zurückgegeben wurden. Sie müssen den Namen des Anspruchs, der in Ihrer Richtlinie definiert ist, dem Namen, der in der REST-API definiert wurde, zuordnen. Sie können auch Ansprüche, die nicht vom Identitätsanbieter der REST-API zurückgegeben wurden, einfügen, sofern Sie das `DefaultValue`-Attribut festlegen.

Das **OutputClaimsTransformations**-Element darf eine Sammlung von **OutputClaimsTransformation**-Elementen, die zum Ändern der Ausgabeansprüche oder zum Generieren neuer verwendet werden, enthalten.

Im folgenden Beispiel wird der von der REST-API zurückgegebene Anspruch gezeigt:

- Der **MembershipId**-Anspruch wird dem Namen des **loyaltyNumber**-Anspruchs zugeordnet.

Das technische Profil gibt auch Ansprüche zurück, die vom Identitätsanbieter nicht zurückgegeben werden:

- Der **loyaltyNumberIsNew**-Anspruch hat den Standardwert `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadaten

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ServiceUrl | Ja | Die URL des REST-API-Endpunkts. |
| AuthenticationType | Ja | Der Typ der Authentifizierung, die vom RESTful-Anspruchsanbieter ausgeführt wird. Mögliche Werte: `None`, `Basic`, `Bearer` oder `ClientCertificate`. Der Wert `None` gibt an, dass die REST-API nicht anonym ist. Der Wert `Basic` gibt an, dass die REST-API mit HTTP-Standardauthentifizierung geschützt ist. Nur verifizierte Benutzer, einschließlich Azure AD B2C, haben Zugriff auf Ihre API. Der (empfohlene) Wert `ClientCertificate` gibt an, dass die REST-API den Zugriff mithilfe von Clientzertifikatauthentifizierung einschränkt. Nur Dienste mit den richtigen Zertifikaten (z.B. Azure AD B2C) erhalten Zugriff auf Ihre API. Der Wert `Bearer` gibt an, dass die REST-API den Zugriff mithilfe des OAuth2-Bearertokens des Clients beschränkt. |
| AllowInsecureAuthInProduction| Nein| Gibt an, ob der `AuthenticationType` in der Produktionsumgebung auf `none` festgelegt werden kann (`DeploymentMode` von [TrustFrameworkPolicy](trustframeworkpolicy.md) ist auf `Production` festgelegt oder nicht angegeben). Mögliche Werte: TRUE oder FALSE (Standard). |
| SendClaimsIn | Nein | Gibt an, wie Eingabeansprüche an den RESTful-Anspruchsanbieter gesendet werden. Mögliche Werte: `Body` (Standard), `Form`, `Header` oder `QueryString`. Der Wert `Body` ist der Eingabeanspruch, der im Anforderungstext im JSON-Format gesendet wird. Der Wert `Form` ist der Eingabeanspruch, der im Anforderungstext in einem durch kaufmännische Und-Zeichen (&) getrenntes Schlüssel-Wert-Format gesendet wird. Der Wert `Header` ist der Eingabeanspruch, der im Anforderungsheader gesendet wird. Der Wert `QueryString` ist der Eingabeanspruch, der in der Abfragezeichenfolge der Anforderung gesendet wird. Die jeweils aufgerufenen HTTP-Verben lauten wie folgt:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: GET</li><li>`QueryString`: GET</li></ul> |
| ClaimsFormat | Nein | Derzeit nicht verwendet, kann ignoriert werden. |
| ClaimUsedForRequestPayload| Nein | Der Name eines Zeichenfolgenanspruchs, der die an die REST-API zu sendende Nutzlast enthält. |
| DebugMode | Nein | Führt das technische Profil im Debugmodus aus. Mögliche Werte sind `true` oder `false` (Standardwert). Im Debugmodus kann die REST-API mehr Informationen zurückgeben. Die entsprechenden Informationen finden Sie im Abschnitt [Zurückgegebene Fehlermeldung](#returning-error-message). |
| IncludeClaimResolvingInClaimsHandling  | Nein | Gibt bei Eingabe- und Ausgabeansprüchen an, ob die [Anspruchsauflösung](claim-resolver-overview.md) im technischen Profil enthalten ist. Mögliche Werte sind `true` oder `false` (Standardwert). Wenn Sie im technischen Profil eine Anspruchsauflösung verwenden möchten, legen Sie für diese Einstellung den Wert `true` fest. |
| ResolveJsonPathsInJsonTokens  | Nein | Gibt an, ob das technische Profil JSON-Pfade auflöst. Mögliche Werte sind `true` oder `false` (Standardwert). Verwenden Sie diese Metadaten, um Daten aus einem geschachtelten JSON-Element zu lesen. Legen Sie in einem Ausgabeanspruch ([OutputClaim](technicalprofiles.md#outputclaims)) den Partneranspruchstyp (`PartnerClaimType`) auf das auszugebende JSON-Pfadelement fest. Beispiel: `firstName.localized` oder `data.0.to.0.email`|

## <a name="cryptographic-keys"></a>Kryptografische Schlüssel

Wenn als Typ der Authentifizierung `None` festgelegt ist, wird das **CryptographicKeys**-Element nicht verwendet.

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Wenn als Typ der Authentifizierung `Basic` festgelegt ist, enthält das **CryptographicKeys**-Element die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Ja | Der zur Authentifizierung verwendete Benutzername. |
| BasicAuthenticationPassword | Ja | Das zur Authentifizierung verwendete Kennwort. |

Das folgende Beispiel zeigt ein technisches Profil mit Standardauthentifizierung:

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Wenn als Typ der Authentifizierung `ClientCertificate` festgelegt ist, enthält das **CryptographicKeys**-Element das folgende Attribut:

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| ClientCertificate | Ja | Das X509 Zertifikat (RSA-Schlüsselsatz) für die Authentifizierung. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

Wenn als Typ der Authentifizierung `Bearer` festgelegt ist, enthält das **CryptographicKeys**-Element das folgende Attribut:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | Nein | Das OAuth 2.0-Bearertoken. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>Zurückgegebene Fehlermeldung

Ihre REST-API muss möglicherweise eine Fehlermeldung zurückgeben (z.B. „Der Benutzer wurde nicht im CRM-System gefunden“). Wenn ein Fehler auftritt, sollte die REST-API einen HTTP-Fehler mit dem Antwort- oder Statuscode „409 Conflict“ (Konflikt) mit folgenden Attributen zurückgeben:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| version | Ja | 1.0.0 |
| status | Ja | 409 |
| code | Nein | Ein Fehlercode vom RESTful-Endpunktanbieter, der angezeigt wird, wenn `DebugMode` aktiviert ist. |
| requestId | Nein | Eine Anforderungs-ID vom RESTful-Endpunktanbieter, die angezeigt wird, wenn `DebugMode` aktiviert ist. |
| userMessage | Ja | Eine Fehlermeldung, die dem Benutzer angezeigt wird. |
| developerMessage | Nein | Die ausführliche Beschreibung des Problems und Informationen zur Behebung, die angezeigt werden, wenn `DebugMode` aktiviert ist. |
| moreInfo | Nein | Ein URI, der auf zusätzliche Informationen verweist, die angezeigt werden, wenn `DebugMode` aktiviert ist. |

Das folgende Beispiel zeigt eine REST-API, die eine JSON-formatierte Fehlermeldung zurückgibt:

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

Das folgende Beispiel zeigt eine C#-Klasse, die eine Fehlermeldung zurückgibt:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln finden Sie Beispiele für die Verwendung eines technischen RESTful-Profils:

- [Integrieren von REST-API-Anspruchsaustauschvorgängen in Ihre Azure AD B2C-User Journey als Validierung der Benutzereingabe](rest-api-claims-exchange-dotnet.md)
- [Schützen Ihrer RESTful-Dienste unter Verwendung der HTTP-Standardauthentifizierung](secure-rest-api-dotnet-basic-auth.md)
- [Schützen Ihres RESTful-Diensts mit Clientzertifikaten](secure-rest-api-dotnet-certificate-auth.md)
- [Exemplarische Vorgehensweise: Integrieren von REST-API-Anspruchsaustauschvorgängen in Ihre Azure AD B2C-User Journey als Validierung der Benutzereingabe](custom-policy-rest-api-claims-validation.md)
