---
title: "Übermitteln von DRM-Lizenzen oder AES-Schlüsseln mithilfe von Azure Media Services"
description: "In diesem Artikel wird beschrieben, wie Sie Azure Media Services (AMS) verwenden können, um PlayReady- und/oder Widevine-Lizenzen und AES-Schlüssel zu übermitteln, aber die restlichen Aufgaben (Codierung, Verschlüsselung, Streaming) mithilfe Ihrer lokalen Server ausführen."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 8546c2c1-430b-4254-a88d-4436a83f9192
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: juliako
ms.openlocfilehash: 263a381dc72105eea60ad9b39434599ff04a4531
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/29/2017
---
# <a name="use-azure-media-services-to-deliver-drm-licenses-or-aes-keys"></a>Übermitteln von DRM-Lizenzen oder AES-Schlüsseln mithilfe von Azure Media Services
Azure Media Services (AMS) ermöglicht Ihnen das Erfassen, Codieren, Hinzufügen von Inhaltsschutz und Streamen Ihrer Inhalte (Details finden Sie in [diesem](media-services-protect-with-drm.md) Artikel). Es gibt jedoch Kunden, die AMS nur zum Übermitteln von Lizenzen und/oder Schlüsseln nutzen und die Codierung, Verschlüsselung und das Streaming mithilfe ihrer lokalen Server durchführen möchten. Dieser Artikel beschreibt, wie Sie mit AMS PlayReady- und/oder Widevine-Lizenzen bereitstellen und die restlichen Aufgaben auf Ihren lokalen Servern ausführen können. 

## <a name="overview"></a>Übersicht
Media Services bietet einen Dienst zum Übermitteln von PlayReady- und Widevine-DMR-Lizenzen und AES-128-Schlüsseln. Mithilfe der von Media Services bereitgestellten APIs können Sie die Rechte und Einschränkungen konfigurieren, die für die DRM-Laufzeit erzwungen werden sollen, wenn ein Benutzer mit DRM geschützte Inhalte wiedergibt. Wenn ein Benutzer die geschützten Inhalte anfordert, fordert die Playeranwendung eine Lizenz vom AMS-Lizenzdienst an. Der AMS-Lizenzdienst stellt dem Player die Lizenz aus, sofern dieser autorisiert ist. PlayReady- und Widevine-Lizenzen enthalten den Entschlüsselungsschlüssel, der vom Clientplayer zum Entschlüsseln und Streamen des Inhalts verwendet werden kann.

Media Services unterstützt mehrere Möglichkeiten zur Autorisierung von Benutzern, die Lizenzen oder Schlüssel anfordern. Sie konfigurieren die Autorisierungsrichtlinie für Inhaltsschlüssel, die eine oder mehrere Einschränkungen aufweisen kann: offen oder Tokeneinschränkung. Die durch Token eingeschränkte Richtlinie gilt nur zusammen mit einem Token, das von einem Secure Token Service (STS) ausgestellt wurde. Media Services unterstützt Token im Simple Web Tokens (SWT)-Format und JSON Web Token (JWT)-Format.

Das folgende Diagramm zeigt die wichtigsten Schritte, die erfolgen müssen, damit AMS zum Übermitteln von PlayReady- und/oder Widevine-Lizenzen genutzt werden kann, die restlichen Aufgaben aber auf Ihren lokalen Servern erfolgen können.

![Schützen mit PlayReady](./media/media-services-deliver-keys-and-licenses/media-services-diagram1.png)

## <a name="download-sample"></a>Beispiel herunterladen
Sie können das in diesem Artikel beschriebene Beispiel [hier](https://github.com/Azure/media-services-dotnet-deliver-drm-licenses)herunterladen.

## <a name="create-and-configure-a-visual-studio-project"></a>Erstellen und Konfigurieren eines Visual Studio-Projekts

1. Richten Sie Ihre Entwicklungsumgebung ein, und füllen Sie die Datei „app.config“ mit Verbindungsinformationen, wie unter [Media Services-Entwicklung mit .NET](media-services-dotnet-how-to-use.md) beschrieben. 
2. Fügen Sie den **appSettings** in Ihrer Datei „app.config“ die folgenden Elemente hinzu:

    <add key="Issuer" value="http://testacs.com"/> <add key="Audience" value="urn:test"/>

## <a name="net-code-example"></a>Codebeispiel für .NET

Das folgende Codebeispiel zeigt, wie ein allgemeiner symmetrischer Schlüssel erstellt und Lizenzerwerbs-URLs für PlayReady und Widevine abgerufen werden. Sie müssen die folgenden Angaben von AMS abrufen und Ihren lokalen Server konfigurieren: **Inhaltsschlüssel**, **Schlüssel-ID**, **Lizenzerwerbs-URL**. Nach dem Konfigurieren Ihres lokalen Servers können Sie auf Ihrem eigenen Streamingserver das Streamen aktivieren. Da der verschlüsselte Stream auf den AMS-Lizenzserver zeigt, fordert Ihr Player eine Lizenz von AMS an. Wenn Sie die Tokenauthentifizierung wählen, überprüft der AMS-Lizenzserver das von Ihnen über HTTPS gesendete Token und übermittelt (sofern gültig) die Lizenz zurück an Ihren Player. (Das Codebeispiel in diesem Thema zeigt nur, wie ein allgemeiner Inhaltsschlüssel erstellt und Lizenzerwerbs-URLs für PlayReady und Widevine abgerufen werden. Wenn Sie AES-128 Schlüssel übermitteln möchten, müssen Sie einen Umschlagsinhaltsschlüssel erstellen und eine Schlüsselerwerbs-URL abrufen, was in [diesem](media-services-protect-with-aes128.md) Artikel gezeigt wird).

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
    using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
    using Newtonsoft.Json;

    namespace DeliverDRMLicenses
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

            private static readonly Uri _sampleIssuer =
                new Uri(ConfigurationManager.AppSettings["Issuer"]);
            private static readonly Uri _sampleAudience =
                new Uri(ConfigurationManager.AppSettings["Audience"]);

            // Field for service context.
            private static CloudMediaContext _context = null;

            static void Main(string[] args)
            {
                var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

                bool tokenRestriction = true;
                string tokenTemplateString = null;


                IContentKey key = CreateCommonTypeContentKey();

                // Print out the key ID and Key in base64 string format
                Console.WriteLine("Created key {0} with key value {1} ",
                    key.Id, System.Convert.ToBase64String(key.GetClearKeyValue()));

                Console.WriteLine("PlayReady License Key delivery URL: {0}",
                    key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));

                Console.WriteLine("Widevine License Key delivery URL: {0}",
                    key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine));

                if (tokenRestriction)
                    tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
                else
                    AddOpenAuthorizationPolicy(key);

                Console.WriteLine("Added authorization policy: {0}",
                    key.AuthorizationPolicyId);
                Console.WriteLine();
                Console.ReadLine();
            }

            static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
            {

                // Create ContentKeyAuthorizationPolicy with Open restrictions 
                // and create authorization policy          

                List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                    new List<ContentKeyAuthorizationPolicyRestriction>
                {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Open",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                            Requirements = null
                        }
                };

                // Configure PlayReady and Widevine license templates.
                string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

                string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

                IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                    _context.ContentKeyAuthorizationPolicyOptions.Create("",
                        ContentKeyDeliveryType.PlayReadyLicense,
                            restrictions, PlayReadyLicenseTemplate);

                IContentKeyAuthorizationPolicyOption WidevinePolicy =
                    _context.ContentKeyAuthorizationPolicyOptions.Create("",
                        ContentKeyDeliveryType.Widevine,
                        restrictions, WidevineLicenseTemplate);

                IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                            ContentKeyAuthorizationPolicies.
                            CreateAsync("Deliver Common Content Key with no restrictions").
                            Result;


                contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
                // Associate the content key authorization policy with the content key.
                contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                contentKey = contentKey.UpdateAsync().Result;
            }

            public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
            {
                string tokenTemplateString = GenerateTokenRequirements();

                List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                    new List<ContentKeyAuthorizationPolicyRestriction>
                {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Token Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                            Requirements = tokenTemplateString,
                        }
                };

                // Configure PlayReady and Widevine license templates.
                string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

                string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

                IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                    _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                        ContentKeyDeliveryType.PlayReadyLicense,
                            restrictions, PlayReadyLicenseTemplate);

                IContentKeyAuthorizationPolicyOption WidevinePolicy =
                    _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                        ContentKeyDeliveryType.Widevine,
                            restrictions, WidevineLicenseTemplate);

                IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                            ContentKeyAuthorizationPolicies.
                            CreateAsync("Deliver Common Content Key with token restrictions").
                            Result;

                contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);

                // Associate the content key authorization policy with the content key
                contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                contentKey = contentKey.UpdateAsync().Result;

                return tokenTemplateString;
            }

            static private string GenerateTokenRequirements()
            {
                TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

                template.PrimaryVerificationKey = new SymmetricVerificationKey();
                template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
                template.Audience = _sampleAudience.ToString();
                template.Issuer = _sampleIssuer.ToString();
                template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

                return TokenRestrictionTemplateSerializer.Serialize(template);
            }

            static private string ConfigurePlayReadyLicenseTemplate()
            {
                // The following code configures PlayReady License Template using .NET classes
                // and returns the XML string.

                //The PlayReadyLicenseResponseTemplate class represents the template 
                //for the response sent back to the end user. 
                //It contains a field for a custom data string between the license server 
                //and the application (may be useful for custom app logic) 
                //as well as a list of one or more license templates.

                PlayReadyLicenseResponseTemplate responseTemplate =
                    new PlayReadyLicenseResponseTemplate();

                // The PlayReadyLicenseTemplate class represents a license template 
                // for creating PlayReady licenses
                // to be returned to the end users. 
                // It contains the data on the content key in the license 
                // and any rights or restrictions to be 
                // enforced by the PlayReady DRM runtime when using the content key.
                PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();

                // Configure whether the license is persistent 
                // (saved in persistent storage on the client) 
                // or non-persistent (only held in memory while the player is using the license).  
                licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

                // AllowTestDevices controls whether test devices can use the license or not.  
                // If true, the MinimumSecurityLevel property of the license
                // is set to 150.  If false (the default), 
                // the MinimumSecurityLevel property of the license is set to 2000.
                licenseTemplate.AllowTestDevices = true;

                // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
                // It grants the user the ability to playback the content subject to the zero or more restrictions 
                // configured in the license and on the PlayRight itself (for playback specific policy). 
                // Much of the policy on the PlayRight has to do with output restrictions 
                // which control the types of outputs that the content can be played over and 
                // any restrictions that must be put in place when using a given output.
                // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
                //then the DRM runtime will only allow the video to be displayed over digital outputs 
                //(analog video outputs won’t be allowed to pass the content).

                // IMPORTANT: These types of restrictions can be very powerful 
                // but can also affect the consumer experience. 
                // If the output protections are configured too restrictive, 
                // the content might be unplayable on some clients. 
                // For more information, see the PlayReady Compliance Rules document.

                // For example:
                //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

                responseTemplate.LicenseTemplates.Add(licenseTemplate);

                return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
            }


            private static string ConfigureWidevineLicenseTemplate()
            {
                var template = new WidevineMessage
                {
                    allowed_track_types = AllowedTrackTypes.SD_HD,
                    content_key_specs = new[]
                    {
                            new ContentKeySpecs
                            {
                                required_output_protection =
                                    new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                                security_level = 1,
                                track_type = "SD"
                            }
                        },
                    policy_overrides = new
                    {
                        can_play = true,
                        can_persist = true,
                        can_renew = false
                    }
                };

                string configuration = JsonConvert.SerializeObject(template);
                return configuration;
            }


            static public IContentKey CreateCommonTypeContentKey()
            {
                // Create envelope encryption content key
                Guid keyId = Guid.NewGuid();
                byte[] contentKey = GetRandomBuffer(16);

                IContentKey key = _context.ContentKeys.Create(
                                        keyId,
                                        contentKey,
                                        "ContentKey",
                                        ContentKeyType.CommonEncryption);

                return key;
            }

            static private byte[] GetRandomBuffer(int length)
            {
                var returnValue = new byte[length];

                using (var rng =
                    new System.Security.Cryptography.RNGCryptoServiceProvider())
                {
                    rng.GetBytes(returnValue);
                }

                return returnValue;
            }
        }
    }

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Weitere Informationen
[Verwenden von dynamischer allgemeiner Verschlüsselung mit PlayReady und/oder Widevine](media-services-protect-with-drm.md)

[Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts](media-services-protect-with-aes128.md)

[Übermitteln von Widevine-Lizenzen an Azure Media Services mithilfe von Partnern](media-services-licenses-partner-integration.md)

