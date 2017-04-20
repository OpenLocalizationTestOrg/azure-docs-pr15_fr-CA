<properties 
    pageTitle="Permet de fournir des licences DRM ou clés AES Azure Media Services" 
    description="Cet article décrit comment vous pouvez utiliser Azure Media Services (AMS) à livrer PlayReady et/ou les licences de Widevine et de clés AES mais ne le reste (codage, cryptage, en flux continu) à l’aide de vos serveurs sur site." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


#<a name="use-azure-media-services-to-deliver-drm-licenses-or-aes-keys"></a>Permet de fournir des licences DRM ou clés AES Azure Media Services

Azure Media Services (AMS) vous permet d’acquisition, coder, ajouter la protection du contenu et le contenu de flux (consultez [cet](media-services-protect-with-drm.md) article pour plus de détails). Toutefois, il existe des utilisateurs que vous souhaitez uniquement utiliser AMS pour fournir des licences et des clés de codage, cryptage et en continu à l’aide de leurs serveurs sur site. Cet article décrit comment vous pouvez utiliser AMS à fournir des licences PlayReady et/ou Widevine mais faire le reste avec vos serveurs locaux. 


## <a name="overview"></a>Vue d’ensemble

Media Services fournit des services pour fournir PlayReady et Widevine DRM licences et clés de AES-128. Media Services fournit également des API qui vous permettent de configurer les droits et restrictions que vous souhaitez pour le runtime DRM à appliquer lorsqu’un utilisateur lit la DRM du contenu protégé. Lorsqu’un utilisateur demande le contenu protégé, l’application de lecteur demande une licence à partir du service de licence AMS. Le service de licence AMS émet la licence pour le lecteur (s’il est autorisé). Les licences PlayReady et Widevine contiennent la clé de déchiffrement qui peut être utilisée par le lecteur du client afin de déchiffrer et le contenu de flux.

Media Services prend en charge plusieurs méthodes d’autorisation des utilisateurs qui effectuent des demandes de clés ou de licence. Vous configurez la stratégie d’autorisation de la clé de contenu et de la stratégie peut avoir une ou plusieurs restrictions : ouvrir ou jeton de restriction. La stratégie de jeton restreint doit être accompagnée d’un jeton émis par un Service (jeton de sécurité). Media Services prend en charge les jetons dans les formats de jetons de Web Simple (SWT) et JSON Web jeton (JWT).


Le diagramme suivant illustre les principales étapes à suivre pour AMS permet de fournir des licences PlayReady et/ou Widevine mais faire le reste avec vos serveurs locaux.

![Protéger avec PlayReady](./media/media-services-deliver-keys-and-licenses/media-services-diagram1.png)

##<a name="download-sample"></a>Télécharger l’exemple

Vous pouvez télécharger l’exemple décrit dans cet article à partir [d’ici](https://github.com/Azure/media-services-dotnet-deliver-drm-licenses).

##<a name="net-code-example"></a>Exemple de code .NET

L’exemple de code dans cette rubrique montre comment créer une clé de contenu commune et obtenir l’URL d’acquisition licence PlayReady ou Widevine. Vous devez obtenir les éléments suivants des informations à partir de l’AMS et configurer votre serveur local : **clé de contenu**, **identificateur de clé**, **URL d’acquisition de licence**. Une fois que vous configurez votre serveur local, vous pourriez transmettre en continu à partir de votre propre serveur de diffusion. Dans la mesure où les points de flux de données chiffré à l’AMS de licence serveur, votre lecteur demande une licence à partir de l’AMS. Si vous choisissez l’authentification de jeton, le serveur de licences AMS validera le jeton que vous avez envoyé via HTTPS et (s’il est valide) fournira la licence sur votre lecteur. (L’exemple de code montre comment créer une clé de contenu commune et obtenir l’URL d’acquisition licence PlayReady ou Widevine d’uniquement. Si vous souhaitez les clés de livraison AES-128, vous devez créer une enveloppe contenu clé et obtenir une URL d’acquisition de clé et [cet](media-services-protect-with-aes128.md) article explique comment faire).
    
    
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Threading;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
    using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
    using Newtonsoft.Json;
    
    
    namespace DeliverDRMLicenses
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
            private static readonly Uri _sampleIssuer =
                new Uri(ConfigurationManager.AppSettings["Issuer"]);
            private static readonly Uri _sampleAudience =
                new Uri(ConfigurationManager.AppSettings["Audience"]);
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
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
    

##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Voir aussi

[En utilisant le cryptage commun PlayReady et/ou Widevine dynamique](media-services-protect-with-drm.md)

[À l’aide du Service de distribution de clés et de cryptage dynamique de AES-128](media-services-protect-with-aes128.md)

[L’utilisation de partenaires pour fournir des licences de Widevine pour Azure Media Services](media-services-licenses-partner-integration.md)