---
title: Ändern der Registrierung in benutzerdefinierten Richtlinien und Konfigurieren des selbstbestätigten Anbieters | Microsoft-Dokumentation
description: Hier finden Sie eine exemplarische Vorgehensweise zum Hinzufügen von Ansprüchen zum Registrieren und Konfigurieren der Benutzereingabe.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2989af12407bdddf6e55e8967a0a574fff690208
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55179207"
---
# <a name="azure-active-directory-b2c-modify-sign-up-to-add-new-claims-and-configure-user-input"></a>Azure Active Directory B2C: Ändern der Registrierung zum Hinzufügen von neuen Ansprüchen und Konfigurieren der Benutzereingabe

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In diesem Artikel fügen Sie der User Journey für die Registrierung eine neue Benutzereingabe (einen Anspruch) hinzu.  Sie konfigurieren die Eingabe als Dropdownelement und legen fest, ob es erforderlich ist.

## <a name="prerequisites"></a>Voraussetzungen

* Führen Sie die Schritte im Artikel [Erste Schritte mit benutzerdefinierten Richtlinien](active-directory-b2c-get-started-custom.md) aus.  Testen Sie die User Journey für die Registrierung bzw. Anmeldung, um ein neues lokales Konto zu registrieren, bevor Sie fortfahren.


Die Sammlung der Anfangsdaten von Ihren Benutzern wird per Registrierungs- bzw. Anmeldevorgang erreicht.  Weitere Ansprüche können später mit User Journeys für die Profilbearbeitung gesammelt werden. Jedes Mal, wenn Azure AD B2C auf interaktive Weise Informationen direkt vom Benutzer erfasst, wird vom Identity Experience Framework der selbstbestätigte Anbieter (`selfasserted provider`) verwendet. Die unten angegebenen Schritte gelten für jede Nutzung dieses Anbieters.


## <a name="define-the-claim-its-display-name-and-the-user-input-type"></a>Definieren des Anspruchs, des Anzeigenamens und des Benutzereingabetyps
Wir fragen vom Benutzer den Ort ab.  Fügen Sie dem `<ClaimsSchema>`-Element in der TrustFrameworkBase-Richtliniendatei das folgende Richtlinienelement hinzu:

```xml
<ClaimType Id="city">
  <DisplayName>city</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```
Sie können hier auch weitere Optionen auswählen, um den Anspruch anzupassen.  Ein vollständiges Schema finden Sie in der **Technischen Referenz zu Identity Experience Framework**.  Dieses Handbuch wird in Kürze im Abschnitt mit den Referenzen veröffentlicht.

* `<DisplayName>` ist eine Zeichenfolge, mit der die *Bezeichnung* für den Benutzer definiert wird.

* Mithilfe von `<UserHelpText>` kann der Benutzer verstehen, was erforderlich ist.

* Für `<UserInputType>` sind die folgenden vier Optionen hervorgehoben:
    * `TextBox`
```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

    * `RadioSingleSelectduration`: erzwingt eine einzelne Auswahl
```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

    * `DropdownSingleSelect`: ermöglicht die Auswahl des einzigen gültigen Werts

![Screenshot der Dropdownoption](./media/active-directory-b2c-configure-signup-self-asserted-custom/dropdown-menu-example.png)


```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```


* `CheckboxMultiSelect`: Ermöglicht die Auswahl von mindestens einem Wert.

![Screenshot der Option für die Mehrfachauswahl](./media/active-directory-b2c-configure-signup-self-asserted-custom/multiselect-menu-example.png)


```xml
<ClaimType Id="city">
  <DisplayName>Receive updates from which cities?</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-the-claim-to-the-sign-upsign-in-user-journey"></a>Hinzufügen des Anspruchs zur User Journey für die Registrierung/Anmeldung

1. Fügen Sie den Anspruch als `<OutputClaim ClaimTypeReferenceId="city"/>` dem TechnicalProfile `LocalAccountSignUpWithLogonEmail` hinzu (in der TrustFrameworkBase-Richtliniendatei enthalten).  Beachten Sie, dass für dieses TechnicalProfile der SelfAssertedAttributeProvider verwendet wird.

  ```xml
  <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
    <DisplayName>Email signup</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
      <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
      <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
      <Item Key="language.button_continue">Create</Item>
    </Metadata>
    <CryptographicKeys>
      <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
    </CryptographicKeys>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
      <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
      <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" />
      <OutputClaim ClaimTypeReferenceId="newUser" />
      <!-- Optional claims, to be collected from the user -->
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surName" />
      <OutputClaim ClaimTypeReferenceId="city"/>
    </OutputClaims>
    <ValidationTechnicalProfiles>
      <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
    </ValidationTechnicalProfiles>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
  </TechnicalProfile>
  ```

2. Fügen Sie den Anspruch dem AAD-UserWriteUsingLogonEmail-Element als `<PersistedClaim ClaimTypeReferenceId="city" />` hinzu, um den Anspruch nach der Erfassung vom Benutzer in das AAD-Verzeichnis zu schreiben. Sie können diesen Schritt überspringen, wenn Sie es vorziehen, den Anspruch im Verzeichnis nicht zur späteren Verwendung beizubehalten.

  ```xml
  <!-- Technical profiles for local accounts -->
  <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
    <Metadata>
      <Item Key="Operation">Write</Item>
      <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    </Metadata>
    <IncludeInSso>false</IncludeInSso>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" Required="true" />
    </InputClaims>
    <PersistedClaims>
      <!-- Required claims -->
      <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
      <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password" />
      <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
      <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
      <!-- Optional claims. -->
      <PersistedClaim ClaimTypeReferenceId="givenName" />
      <PersistedClaim ClaimTypeReferenceId="surname" />
      <PersistedClaim ClaimTypeReferenceId="city" />
    </PersistedClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
      <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
      <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    </OutputClaims>
    <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
  </TechnicalProfile>
  ```

3. Hinzufügen des Anspruchs zum TechnicalProfile, das für das Verzeichnis einen Lesevorgang durchführt, wenn sich ein Benutzer als `<OutputClaim ClaimTypeReferenceId="city" />` anmeldet

  ```xml
  <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
    <Metadata>
      <Item Key="Operation">Read</Item>
      <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">An account could not be found for the provided user ID.</Item>
    </Metadata>
    <IncludeInSso>false</IncludeInSso>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames" Required="true" />
    </InputClaims>
    <OutputClaims>
      <!-- Required claims -->
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
      <!-- Optional claims -->
      <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="otherMails" />
      <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  </TechnicalProfile>
  ```

4. Fügen Sie `<OutputClaim ClaimTypeReferenceId="city" />` der RP-Richtliniendatei „SignUporSignIn.xml“ hinzu, damit dieser Anspruch nach einer erfolgreichen User Journey an die Anwendung im Token gesendet wird.

  ```xml
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ```

## <a name="test-the-custom-policy-using-run-now"></a>Testen der benutzerdefinierten Richtlinie mit „Jetzt ausführen“

1. Öffnen Sie das Blatt **Azure AD B2C**, und navigieren Sie zu **Identity Experience Framework > Benutzerdefinierte Richtlinien**.
2. Wählen Sie die benutzerdefinierte Richtlinie aus, die hochgeladen werden soll, und klicken Sie auf die Schaltfläche **Jetzt ausführen**.
3. Sie sollten sich mit einer E-Mail-Adresse registrieren können.

Der Registrierungsbildschirm sollte im Testmodus etwa wie folgt aussehen:

![Screenshot der geänderten Registrierungsoption](./media/active-directory-b2c-configure-signup-self-asserted-custom/signup-with-city-claim-dropdown-example.png)

  Das Token zum Zurücksenden an Ihre Anwendung enthält jetzt wie unten dargestellt den `city`-Anspruch.
```json
{
  "exp": 1493596822,
  "nbf": 1493593222,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "9c2a3a9e-ac65-4e46-a12d-9557b63033a9",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustf_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1493593222,
  "auth_time": 1493593222,
  "email": "joe@outlook.com",
  "given_name": "Joe",
  "family_name": "Ras",
  "city": "Bellevue",
  "name": "unknown"
}
```

## <a name="optional-remove-email-verification-from-signup-journey"></a>Optional: Entfernen der E-Mail-Überprüfung aus der Journey für die Registrierung

Um die E-Mail-Verifizierung zu überspringen, kann sich der Ersteller der Richtlinie für das Entfernen von `PartnerClaimType="Verified.Email"` entscheiden. Die E-Mail-Adresse ist erforderlich, aber sie wird nur verifiziert, wenn „Required“ = true entfernt wird.  Überlegen Sie sich gründlich, ob diese Option für Ihre Anwendungsfälle geeignet ist!

Im `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">`-Element in der TrustFrameworkBase-Richtliniendatei des Starter Packs ist die Verifizierung der E-Mail standardmäßig aktiviert:
```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihre Richtlinie Social Media-Konten unterstützt, fügen Sie den neuen Anspruch dem Ablauf für die Anmeldung an Social Media-Konten hinzu, indem Sie die unten aufgeführten technischen Profile ändern. Diese Ansprüche werden von der Anmeldung bei Social Media-Konten zum Erfassen und Schreiben von Daten vom Benutzer verwendet.

1. Suchen Sie das technische Profil **SelfAsserted-Social**, und fügen Sie den Ausgabeanspruch hinzu. Die Reihenfolge der Ansprüche in **OutputClaims** bestimmt die Reihenfolge, in der Azure AD B2C die Ansprüche berechnet und auf dem Bildschirm ausgibt. Beispiel: `<OutputClaim ClaimTypeReferenceId="city" />`.
2. Suchen Sie das technische Profil **AAD-UserWriteUsingAlternativeSecurityId**, und fügen Sie den Persistenzanspruch hinzu. Beispiel: `<PersistedClaim ClaimTypeReferenceId="city" />`.
3. Suchen Sie das technische Profil **AAD-UserReadUsingAlternativeSecurityId**, und fügen Sie den Ausgabeanspruch hinzu. Beispiel: `<OutputClaim ClaimTypeReferenceId="city" />`.
