---
title: "Azure Active Directory B2C: Anpassung der Benutzeroberfläche (UI) | Microsoft Docs"
description: "Ein Thema über die Anpassungsfeatures für die Benutzeroberfläche (UI) in Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 99f5a391-5328-471d-a15c-a2fafafe233d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 74b077f6f09d53c9232e5b209a5dd811364ee3f5
ms.openlocfilehash: c995e0de46c67c5c5d243739b2d36266267bdade


---
# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Azure Active Directory B2C: Anpassen der Azure AD B2C-Benutzeroberfläche (UI)
Benutzerfreundlichkeit ist in einer kundenorientierten Anwendung entscheidend. Dies ist der Unterschied zwischen einer guten Anwendung und einer hervorragenden – und zwischen lediglich aktiven Consumern und wirklich engagierten. Azure Active Directory (Azure AD) B2C ermöglicht es Ihnen, die Seiten für die Registrierung und Anmeldung von Endkunden (*siehe Hinweis unten*), für die Profilbearbeitung und für die Kennwortzurücksetzung mit einer präzisen Kontrolle anzupassen.

> [!NOTE]
> Derzeit können Anmeldeseiten für lokale Konten, die zugehörigen Seiten für die Kennwortzurücksetzung und Überprüfungs-E-Mails nur mithilfe des [Features für Unternehmensbranding](../active-directory/active-directory-add-company-branding.md) angepasst werden. Mit den Mechanismen, die in diesem Artikel beschrieben werden, ist dies nicht möglich.
> 
> 

Dieser Artikel enthält Folgendes:

* Das Anpassungsfeature für die Seitenbenutzeroberfläche (UI).
* Ein Tool, mit dem Sie das Anpassungsfeature für die Seiten-UI mit unserem Beispielinhalt testen können.
* Die wichtigsten UI-Elemente in jedem Seitentyp.
* Bewährte Methoden beim Üben des Umgangs mit diesem Feature.

## <a name="the-page-ui-customization-feature"></a>Das Anpassungsfeature für die Seiten-UI
Mit dem Anpassungsfeature für die Seiten-UI können Sie das Aussehen und Verhalten der Seiten für die Registrierung, Anmeldung, Kennwortzurücksetzung und Profilbearbeitung von Endkunden anpassen (durch Konfigurieren von [Richtlinien](active-directory-b2c-reference-policies.md)). Beim Navigieren zwischen der Anwendung und den Seiten, die von Azure AD B2C verarbeitet werden, wird den Endkunden eine nahtlose Benutzeroberfläche angeboten.

Im Gegensatz zu anderen Diensten, in denen UI-Optionen beschränkt oder nur über APIs verfügbar sind, verwendet Azure AD B2C eine moderne (und einfachere) Vorgehensweise bei der Anpassung der Seiten-UI.

So funktioniert es: Azure AD B2C führt Code im Browser des Consumers aus und verwendet einen modernen Ansatz namens [Cross-Origin Resource Sharing (CORS)](http://www.w3.org/TR/cors/) zum Laden des Inhalts von einer URL, die Sie in einer Richtlinie angeben. Sie können verschiedene URLs für unterschiedliche Seiten angeben. Der Code führt die UI-Elemente aus Azure AD B2C mit dem Inhalt, der von der URL geladen wird, zusammen und zeigt die Seite für den Endkunden an. Sie müssen nur Folgendes durchführen:

1. Erstellen Sie wohlgeformten HTML5-Inhalt mit einem `<div id="api"></div>`-Element (muss ein leeres Element sein) an einer beliebigen Stelle in `<body>`. Dieses Element markiert die Stelle, an der der Azure AD B2C-Inhalt eingefügt wird.
2. Hosten Sie diese Inhalte auf einem HTTPS-Endpunkt (auf dem CORS zulässig ist). Beachten Sie, dass Sie bei der Konfiguration von CORS die Anforderungsmethoden GET und OPTIONS aktivieren müssen.
3. Formatieren Sie die Elemente der Benutzeroberfläche, die von Azure AD B2C einfügt werden.

## <a name="test-out-the-ui-customization-feature"></a>Testen des Anpassungsfeatures für die Benutzeroberfläche (UI)
Wenn Sie das UI-Anpassungsfeature anhand der HTML- und CSS-Beispielinhalte ausprobieren möchten, können Sie unser [einfaches Hilfsprogramm](active-directory-b2c-reference-ui-customization-helper-tool.md) zum Hochladen und Konfigurieren der Beispielinhalte in Ihren Azure-Blobspeicher verwenden.

> [!NOTE]
> Sie können Ihre UI-Inhalte überall hosten: auf Webservern, CDNs, AWS S3, Dateifreigabesystemen usw. Solange die Inhalte auf einem öffentlich verfügbaren HTTPS-Endpunkt gehostet werden (mit Zulassung von CORS), ist alles in Ordnung. Wir verwenden den Azure-Blobspeicher nur zur Veranschaulichung.
> 
> 

### <a name="the-most-basic-html-content-for-testing"></a>Grundlegende HTML-Inhalte für Tests
Unten sind grundlegende HTML-Inhalte dargestellt, die Sie zum Testen dieser Funktion verwenden können. Nutzen Sie dasselbe Hilfsprogramm wie oben, um diese Inhalte in den Azure-Blobspeicher hochzuladen und zu konfigurieren. So können Sie sicherstellen, dass die grundlegenden unformatierten Schaltflächen und Formularfelder auf jeder Seite angezeigt werden und funktionieren.

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>    <!-- IMP: This element is intentionally empty; don't enter anything here -->
    </body>
</html>

```

## <a name="the-core-ui-elements-in-each-type-of-page"></a>Die wichtigsten UI-Elemente in den einzelnen Seitentypen
In den folgenden Abschnitten finden Sie Beispiele für HTML5-Fragmente, die Azure AD B2C im `<div id="api"></div>` -Element in Ihrem Inhalt zusammenführt. **Fügen Sie diese Fragmente nicht in Ihren HTML5-Inhalt ein.** Sie werden vom Azure AD B2C-Dienst zur Laufzeit eingefügt. Verwenden Sie diese Beispiele, um Ihre eigenen Stylesheets zu entwerfen.

### <a name="azure-ad-b2c-content-inserted-into-the-identity-provider-selection-page"></a>Auf der „Auswahlseite für Identitätsanbieter“ eingefügte Azure AD B2C-Inhalte
Diese Seite enthält eine Liste der Identitätsanbieter, aus denen der Benutzer bei der Registrierung oder Anmeldung auswählen kann. Dabei handelt es sich entweder um Identitätsanbieter in Form von sozialen Netzwerken wie Facebook und Google+ oder um lokale Konten (basierend auf E-Mail-Adressen oder Benutzernamen).

```HTML

<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-local-account-sign-up-page"></a>Auf der „Registrierungsseite für lokales Konto“ eingefügte Azure AD B2C-Inhalte
Diese Seite enthält ein Registrierungsformular, das der Benutzer bei der Registrierung für ein lokales Konto ausfüllen muss, das auf einer E-Mail-Adresse oder einem Benutzernamen basiert.  Das Formular kann verschiedene Eingabesteuerelemente enthalten, z. B. Texteingabefelder, Kennworteingabefelder, Optionsfelder, Dropdownfelder mit einer Auswahlmöglichkeit und Kontrollkästchen mit mehreren Optionen.

```HTML

<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-social-account-sign-up-page"></a>Auf der „Registrierungsseite für Konten sozialer Netzwerke“ eingefügte Azure AD B2C-Inhalte
Diese Seite enthält ein Registrierungsformular, das der Consumer ausfüllen muss, wenn die Registrierung mit einem vorhandenen Konto bei einem sozialen Netzwerk wie Facebook oder Google+ als Identitätsanbieter erfolgt. Diese Seite ähnelt der Registrierungsseite für lokale Konten (siehe Abschnitt oben), eine Ausnahme bilden die Kennworteingabefelder.

### <a name="azure-ad-b2c-content-inserted-into-the-unified-sign-up-or-sign-in-page"></a>Auf der „Seite für einheitliche Registrierung oder Anmeldung“ eingefügte Azure AD B2C-Inhalte
Diese Seite verarbeitet sowohl die Registrierung als auch die Anmeldung von Consumern. Diese können als Identitätsanbieter soziale Netzwerke wie z.B. Facebook oder Google+ oder lokale Konten verwenden.

```HTML

<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-multi-factor-authentication-page"></a>Auf der „Seite für die mehrstufige Authentifizierung“ eingefügte Azure AD B2C-Inhalte
Auf dieser Seite können Benutzer während der Registrierung oder Anmeldung ihre Telefonnummern überprüfen (per SMS oder Sprachnachricht).

```HTML

<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-error-page"></a>Auf der „Fehlerseite“ eingefügte Azure AD B2C-Inhalte
```HTML

<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>

```

## <a name="things-to-remember-when-building-your-own-content"></a>Wichtige Aspekte beim Erstellen eigener Inhalte
Wenn Sie das Anpassungsfeature für die Seiten-UI verwenden möchten, beachten Sie die folgenden bewährten Methoden:

* Kopieren und ändern Sie den Azure AD B2C-Standardinhalt nicht. Es wird empfohlen, eigene HTML5-Inhalte von Grund auf neu zu erstellen und den Standardinhalt als Referenz zu verwenden.
* Auf allen Seiten (mit Ausnahme der Fehlerseiten), die von den Richtlinien für die Registrierung, Anmeldung und Profilbearbeitung bereitgestellt werden, müssen die von Ihnen angegebenen Stylesheets die Standard-Stylesheets außer Kraft setzen, die wir auf diesen Seiten in den <head> -Fragmenten hinzufügen. Für alle Seiten, die von den Richtlinien für die Registrierung, Anmeldung und Kennwortzurücksetzung bereitgestellt werden, und für die Fehlerseiten aller Richtlinien müssen Sie die gesamte Formatierung selbst angeben.
* Aus Gründen der Sicherheit ist es nicht zulässig, JavaScript in Ihren Inhalt aufzunehmen. Das meiste, was Sie benötigen, sollte im Programmumfang enthalten sein. Falls nicht, fordern Sie über [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) neue Funktionen an.
* Unterstützte Browserversionen:
  * Internet Explorer 11, 10, Edge
  * Eingeschränkte Unterstützung für Internet Explorer 9, 8
  * Google Chrome 42.0 und höher
  * Mozilla Firefox 38.0 und höher



<!--HONumber=Feb17_HO2-->


