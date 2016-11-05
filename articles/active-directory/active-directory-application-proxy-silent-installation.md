---
title: Installieren des Azure AD-Anwendungsproxyconnectors im Hintergrund | Microsoft Docs
description: Erläutert das Durchführen einer Installation des Azure AD-Anwendungsproxyconnectors im Hintergrund, um sicheren Remotezugriff auf lokale Apps zu gewähren.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2016
ms.author: kgremban

---
# Installieren des Azure AD-Anwendungsproxyconnectors im Hintergrund
Sie möchten ein Installationsskript an mehrere Windows-Server senden können oder an Windows-Server, auf denen keine Benutzeroberfläche aktiviert ist. In diesem Thema wird das Erstellen eines Windows PowerShell-Skripts erläutert, das eine unbeaufsichtigte Installation zum Installieren und Registrieren des Azure AD-Anwendungsproxyconnectors ermöglicht.

## Aktivieren des Zugriffs
Um den Anwendungsproxy nutzen zu können, müssen Sie einen als Connector bezeichneten schlanken Windows Server-Dienst in Ihrem Netzwerk installieren. Für das Funktionieren des Anwendungsproxyconnectors muss dieser im Azure AD-Verzeichnis durch einen globalen Administrator mit Kennwort registriert werden. Dieses wird normalerweise während der Installation des Connectors in einem Popupdialogfeld eingegeben. Stattdessen können Sie mit Windows PowerShell ein Anmeldeinformationsobjekt erstellen, um die Registrierungsinformationen einzugeben. Sie können jedoch auch ein eigenes Token erstellen und zur Eingabe der Registrierungsinformationen verwenden.

## Schritt 1: Installieren des Connectors ohne Registrierung
Installieren Sie die Connector-MSIs wie folgt, ohne den Connector zu registrieren:

1. Öffnen Sie eine Eingabeaufforderung.
2. Führen Sie den folgenden Befehl aus, in dem "/q" für die Installation im Hintergrund steht – bei der Installation werden Sie nicht aufgefordert, den Endbenutzer-Lizenzvertrag zu akzeptieren.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## Schritt 2: Registrieren des Connectors in Azure Active Directory
Dies lässt sich mit einer folgenden Methoden bewirken:

* Registrieren des Connectors mit einem Windows PowerShell-Anmeldeinformationsobjekts
* Registrieren des Connectors mithilfe eines offline erstellten Tokens

### Registrieren des Connectors mit einem Windows PowerShell-Anmeldeinformationsobjekts
1. Erstellen Sie das Windows PowerShell-Anmeldeinformationsobjekt durch Ausführen des folgenden Befehls, wobei <username> und <password> durch den Benutzernamen und das Kennwort für das betreffende Verzeichnis ersetzt werden muss:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Wechseln Sie zu **C:\\Programme\\Microsoft AAD App Proxy Connector**, und führen Sie das Skript mithilfe des zuvor erstellten PowerShell-Anmeldeinformationsobjekts aus, wobei „$cred“ für den Namen des erstellten PowerShell-Anmeldeinformationsobjekts steht:
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

### Registrieren des Connectors mithilfe eines offline erstellten Tokens
1. Erstellen Sie mithilfe der AuthenticationContext-Klasse ein Offlinetoken, indem Sie die Werte in dem Codeausschnitt verwenden:

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





1. Sobald das Token vorliegt, erstellen Sie einen SecureString, der das Token verwendet: <br> `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
2. Führen Sie den folgenden Windows PowerShell-Befehl aus, wobei „SecureToken“ der Name des soeben erstellten Tokens ist, und „tenantID“ die GUID des Mandanten: <br> `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`

## Weitere Informationen
* [Aktivieren des Azure AD-Anwendungsproxys](active-directory-application-proxy-enable.md)
* [Veröffentlichen von Anwendungen mit Ihrem eigenen Domänennamen](active-directory-application-proxy-custom-domains.md)
* [Aktivieren der einmaligen Anmeldung](active-directory-application-proxy-sso-using-kcd.md)
* [Problembehandlung von Anwendungsproxys](active-directory-application-proxy-troubleshoot.md)

Aktuelle Neuigkeiten und Updates finden Sie im [Blog zum Anwendungsproxy](http://blogs.technet.com/b/applicationproxyblog/).

<!---HONumber=AcomDC_0622_2016-->