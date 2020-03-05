---
title: REST-API-Anspruchsaustausch als Validierung
titleSuffix: Azure AD B2C
description: Eine exemplarische Vorgehensweise zum Erstellen einer Azure AD B2C User Journey, die mit RESTful-Diensten interagiert.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7100498d99068941bcd7ca48b6cbcaa271fbb095
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189071"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Exemplarische Vorgehensweise: Integrieren von REST-API-Anspruchsaustauschvorgängen in Ihre Azure AD B2C-User Journey als Validierung der Benutzereingabe

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Das Framework für die Identitätsfunktion (Identity Experience Framework, IEF), das Azure Active Directory B2C (Azure AD B2C) zugrunde liegt, ermöglicht dem Identitätsentwickler die Integration einer Interaktion mit einer RESTful-API in eine User Journey.

Am Ende dieser exemplarischen Vorgehensweise sind Sie in der Lage, eine Azure AD B2C User Journey zu erstellen, die mit RESTful-Diensten interagiert.

Das IEF sendet Daten in Ansprüchen und erhält Daten in Ansprüchen zurück. Die Interaktion mit der API bietet Folgendes:

- Kann als Austausch von REST-API-Ansprüchen oder als Validierungsprofil entworfen werden, der bzw. das innerhalb eines Orchestrierungsschritts erfolgt.
- Normalerweise wird die Eingabe des Benutzers validiert. Wenn der Wert des Benutzers abgelehnt wird, kann er erneut versuchen, einen gültigen Wert einzugeben, wodurch eventuell eine Fehlermeldung zurückgegeben werden kann.

Sie können die Interaktion auch als Orchestrierungsschritt entwerfen. Weitere Informationen finden Sie unter [Exemplarische Vorgehensweise: Integrieren von REST-API-Anspruchsaustausch-Vorgängen in Ihre Azure AD B2C User Journey als Orchestrierungsschritt](custom-policy-rest-api-claims-exchange.md)

Für das Beispiel für das Validierungsprofil verwenden wir die User Journey der Profilbearbeitung in der Starter Pack-Datei „ProfileEdit.xml“.

Wir können sicherstellen, dass der Name, der vom Benutzer bei der Profilbearbeitung angegeben wird, nicht Teil einer Ausschlussliste ist.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure AD B2C-Mandant ist zur Durchführung der Registrierung/Anmeldung für ein lokales Konto konfiguriert, wie unter [Erste Schritte](custom-policy-get-started.md) beschrieben.
- Ein REST-API-Endpunkt, mit dem interagiert werden kann. In dieser exemplarischen Vorgehensweise haben wir mit einem REST-API-Dienst die Demowebsite [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) eingerichtet.

## <a name="step-1-prepare-the-rest-api-function"></a>Schritt 1: Vorbereiten der REST-API-Funktion

> [!NOTE]
> Die Einrichtung von REST-API-Funktionen wird in diesem Artikel nicht beschrieben. Für [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) gibt es ein hervorragendes Toolkit zur Erstellung von RESTful-Diensten in der Cloud.

Wir haben eine Azure-Funktion erstellt, die einen als `playerTag` erwarteten Anspruch empfängt. Die Funktion überprüft, ob dieser Anspruch vorhanden ist. Sie können auf [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples) auf den gesamten Azure-Funktionscode zugreifen.

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

Das IEF erwartet den Anspruch `userMessage`, der die Azure-Funktion zurückgibt. Dieser Anspruch wird dem Benutzer als Zeichenfolge dargestellt, wenn die Überprüfung fehlschlägt, z.B., wenn im vorherigen Beispiel die Fehlermeldung 409 bezüglich eines Statuskonflikts zurückgegeben wird.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Schritt 2: Konfigurieren des RESTful-API-Anspruchsaustauschs als technisches Profil in der Datei „TrustFrameworkExtensions.xml“

Ein technisches Profil umfasst die vollständige Konfiguration des Austauschs, der für den RESTful-Dienst gewünscht ist. Öffnen Sie die Datei „TrustFrameworkExtensions.xml“, und fügen Sie den folgenden XML-Ausschnitt in das Element `<ClaimsProviders>` ein.

> [!NOTE]
> Im folgenden XML-Ausschnitt wird der RESTful-Anbieter `Version=1.0.0.0` als Protokoll beschrieben. Betrachten Sie diesen als Funktion, die mit dem externen Dienst interagiert. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="SendClaimsIn">Body</Item>
                <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
                <Item Key="AuthenticationType">None</Item>
                <!-- REMOVE the following line in production environments -->
                <Item Key="AllowInsecureAuthInProduction">true</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

Das `InputClaims`-Element definiert die Ansprüche, die vom IEF zum REST-Dienst gesendet werden. In diesem Beispiel wird der Inhalt des Anspruchs `givenName` als `playerTag` an den REST-Dienst gesendet. In diesem Beispiel erwartet das IEF keine Ansprüche zurück. Stattdessen wartet es auf eine Antwort des REST-Diensts und agiert basierend auf den empfangenen Statuscodes.

Die Kommentare `AuthenticationType` und `AllowInsecureAuthInProduction` oben geben Änderungen an, die Sie beim Wechsel zu einer Produktionsumgebung vornehmen sollten. Informationen zum Schützen Ihrer RESTful-APIs für die Produktionsumgebung finden Sie unter [Schützen von RESTful-APIs per Standardauthentifizierung](secure-rest-api-dotnet-basic-auth.md) und [Schützen von RESTful-APIs per Zertifikatauthentifizierung](secure-rest-api-dotnet-certificate-auth.md).

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Schritt 3: Einfügen des Austauschs von Ansprüchen des RESTful-Diensts in ein selbstbestätigtes technisches Profil zum Validieren der Benutzereingabe

Der Validierungsschritt wird am häufigsten bei der Interaktion mit einem Benutzer eingesetzt. Alle Interaktionen, bei denen vom Benutzer eine Eingabe erwartet wird, sind *selbstbestätigte technische Profile*. In diesem Beispiel fügen wir diese Validierung dem technischen Profil „Self-Asserted-ProfileUpdate“ hinzu. Dies ist das technische Profil, das die Richtliniendatei der vertrauenden Seite `Profile Edit` verwendet.

So fügen Sie dem selbstbestätigten technischen Profil den Anspruchsaustausch hinzu:

1. Öffnen Sie die Datei „TrustFrameworkBase.xml“, und suchen Sie nach `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Überprüfen Sie die Konfiguration dieses technischen Profils. Ermitteln Sie, wie der Austausch mit dem Benutzer basierend auf Ansprüchen definiert ist: Ansprüche, die vom Benutzer gefordert werden (Eingabeansprüche), und Ansprüche, die vom selbstbestätigten Anbieter zurückerwartet werden (Ausgabeansprüche).
3. Suchen Sie nach `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`. Beachten Sie, dass dieses Profil als Orchestrierungsschritt 5 von `<UserJourney Id="ProfileEdit">` aufgerufen wird.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Schritt 4: Hochladen und Testen der Richtliniendatei der vertrauenden Seite für die Profilbearbeitung

1. Laden Sie die neue Version der Richtliniendatei „TrustFrameworkExtensions.xml“ hoch.
2. Verwenden Sie die Option **Jetzt ausführen**, um die RP-Richtliniendatei für die Profilbearbeitung zu testen.
3. Testen Sie die Validierung, indem Sie einen der vorhandenen Namen (z.B. „mcvinny“) in das Feld **Angegebener Name** eingeben. Wenn alles richtig eingerichtet ist, sollten Sie eine Meldung mit dem Hinweis erhalten, dass das Playertag bereits verwendet wird.

## <a name="next-steps"></a>Nächste Schritte

[Ändern der Profilbearbeitung und der Benutzerregistrierung zum Sammeln zusätzlicher Informationen von Ihren Benutzern](custom-policy-custom-attributes.md)

[Exemplarische Vorgehensweise: Integrieren von REST-API-Anspruchsaustausch-Vorgängen in Ihre Azure AD B2C User Journey als Orchestrierungsschritt](custom-policy-rest-api-claims-exchange.md)

[Referenz: Technisches Profil „RESTful“](restful-technical-profile.md)

Informationen zum Schützen Ihrer APIs finden Sie in den folgenden Artikeln:

* [Secure your RESTful API with basic authentication (username and password)](secure-rest-api-dotnet-basic-auth.md) (Schützen Ihrer RESTful-API per Standardauthentifizierung (Benutzername und Kennwort))
* [Schützen Ihrer RESTful-API mit Clientzertifikaten](secure-rest-api-dotnet-certificate-auth.md)
