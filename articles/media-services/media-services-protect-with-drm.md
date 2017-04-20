<properties
    pageTitle="En utilisant le cryptage commun dynamique PlayReady et/ou Widevine | Microsoft Azure"
    description="Microsoft Azure Media Services vous permet de livrer des flux MPEG-tiret, diffusion en continu lisse et Http-Live-diffusion en continu (TLS) protégés par Microsoft PlayReady DRM. Il vous permet également de livraison tiret crypté avec Widevine DRM. Cette rubrique indique comment chiffrer dynamiquement avec PlayReady et Widevine DRM."
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="09/27/2016"
    ms.author="juliako"/>


#<a name="using-playready-andor-widevine-dynamic-common-encryption"></a>En utilisant le cryptage commun dynamique PlayReady et/ou Widevine

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-drm.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

Microsoft Azure Media Services vous permet de livrer des flux MPEG-tiret, diffusion en continu lisse et HTTP-Live-diffusion en continu (TLS) protégés par [DRM de Microsoft PlayReady](https://www.microsoft.com/playready/overview/). Il vous permet également de fournir des flux de tiret cryptés avec Widevine DRM licences. PlayReady et Widevine sont cryptés par la spécification de chiffrement commune (CENC 23001-7 de norme ISO/CEI). Vous pouvez utiliser [AMS.NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (en commençant par la version 3.5.1) ou autres API pour configurer votre AssetDeliveryConfiguration pour utiliser Widevine.

Media Services propose un service de distribution des licences PlayReady et Widevine DRM. Media Services fournit également des API qui vous permettent de configurer les droits et restrictions que vous souhaitez pour le runtime PlayReady ou Widevine DRM appliquer lorsqu’un utilisateur lit du contenu protégé. Lorsqu’un utilisateur demande un contenu protégé par DRM, l’application de lecteur demande une licence à partir du service de licence AMS. Le service de licence AMS émet une licence pour le lecteur s’il est autorisé. Une licence PlayReady ou Widevine contient la clé de déchiffrement qui peut être utilisée par le lecteur du client afin de déchiffrer et le contenu de flux.


Vous pouvez également utiliser les partenaires AMS suivants pour vous aider à obtenir les licences de Widevine : [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). Pour plus d’informations, reportez-vous à la section : intégration avec [Axinom](media-services-axinom-integration.md) et [castLabs](media-services-castlabs-integration.md).

Media Services prend en charge plusieurs méthodes d’autorisation des utilisateurs qui effectuent des demandes de clés. La stratégie d’autorisation de clés contenu peut avoir une ou plusieurs restrictions d’autorisation : ouvrir ou jeton de restriction. La stratégie de jeton restreint doit être accompagnée d’un jeton émis par un Service (jeton de sécurité). Media Services prend en charge les jetons dans les formats de [Jetons de Web Simple](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) et [JSON Web jeton](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Pour plus d’informations, consultez Configuration de la stratégie d’autorisation de la clé de contenu.

Pour tirer parti de chiffrement dynamiques, vous devez avoir un actif qui contient un ensemble de fichiers MP4 de multi-bitrate ou des fichiers source de diffusion en continu lisse multi-bitrate. Vous devez également configurer les stratégies de livraison de l’immobilisation (décrite plus loin dans cette rubrique). Puis, selon le format spécifié dans l’URL en continu, le serveur de diffusion en continu à la demande veillera à ce que le flux est remis dans le protocole que vous avez choisi. Par conséquent, il vous suffit de stocker et de payer pour les fichiers dans un format de stockage unique et Media Services pour la génération et servir la réponse HTTP appropriée en fonction de chaque demande d’un client.

Cette rubrique peut être utile pour les développeurs qui travaillent sur les applications qui offrent des médias protégés par plusieurs DRMs, des Widevine PlayReady. La rubrique vous indique comment configurer le service de remise de licence PlayReady avec des stratégies d’autorisation afin que seuls les clients autorisés peuvent recevoir des licences PlayReady ou Widevine. Il montre également comment utiliser le chiffrement de cryptage dynamique avec PlayReady ou Widevine DRM sur le tiret.

>[AZURE.NOTE]Pour utiliser le chiffrement dynamiques, vous devez d’abord obtenir au moins une unité d’échelle (également appelé une unité en continu). Pour plus d’informations, consultez [Comment faire évoluer un Service de support](media-services-portal-manage-streaming-endpoints.md).


##<a name="download-sample"></a>Télécharger l’exemple

Vous pouvez télécharger l’exemple décrit dans cet article à partir [d’ici](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm).

##<a name="configuring-dynamic-common-encryption-and-drm-license-delivery-services"></a>Configuration de cryptage commun dynamique et des Services de livraison de licence DRM

Voici les étapes générales que vous auriez à effectuer lors de la protection de vos ressources sur PlayReady, en utilisant les services de livraison licence Media, ainsi que le cryptage dynamique.

1. Créer un actif et de télécharger des fichiers dans l’immobilisation.
1. Coder l’actif contenant le fichier à la vitesse de transmission adaptive que MP4 définie.
1. Créer une clé de contenu et l’associer à l’actif codé. Dans les Services de support, la clé de contenu contient la clé de cryptage de l’actif.
1. Configurer la stratégie d’autorisation de la clé de contenu. La stratégie d’autorisation de clés contenu doit être configurée par vous et remplie par le client dans l’ordre de la clé de contenu à livrer au client.

Lors de la création de la stratégie d’autorisation de clés contenu, vous devez spécifier les informations suivantes : méthode (PlayReady ou Widevine), les restrictions de remise (en cours ou jetons), les informations spécifiques au type de distribution de clés qui définit comment la clé est envoyée au client (modèle de licence[PlayReady](media-services-playready-license-template-overview.md) ou [Widevine](media-services-widevine-license-template-overview.md) ).
1. Configurer la stratégie de la livraison d’un actif. Configuration de la stratégie de livraison comprend : protocole de livraison (par exemple, MPEG tiret, TLS, HDS, diffusion en continu lisse ou tous), le type de cryptage dynamique (par exemple, cryptage commun), PlayReady ou URL d’acquisition de licence Widevine.

Vous pouvez appliquer une stratégie différente pour chaque protocole sur le même actif. Par exemple, vous pouvez appliquer le chiffrement PlayReady lisse/tiret et enveloppe AES pour TLS. Tous les protocoles qui ne sont pas définis dans une stratégie de remise (par exemple, vous ajoutez une stratégie unique qui spécifie uniquement TLS comme protocole) ne pourront pas en continu. L’exception à cette règle est que si vous n’avez aucune stratégie de livraison actif défini à tout. Ensuite, tous les protocoles peuvent être utilisés en clair.
1. Créer un localisateur à la demande afin d’obtenir une URL de transmission en continu.

Vous trouverez un exemple complet de .NET à la fin de la rubrique.

L’image suivante montre le flux de travail décrit ci-dessus. Ici, le jeton est utilisé pour l’authentification.

![Protéger avec PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

Le reste de cette rubrique fournit des explications détaillées, des exemples de code et des liens vers des rubriques qui vous expliquent comment réaliser les tâches décrites ci-dessus.

##<a name="current-limitations"></a>Limitations actuelles

Si vous ajoutez ou mise à jour d’une stratégie de remise d’actifs, vous devez supprimer le repère associé (le cas échéant) et créer une nouvelle adresse.

Limitation lors du cryptage avec Widevine avec Azure Media Services : actuellement, plusieurs clés de contenu ne sont pas pris en charge.

##<a name="create-an-asset-and-upload-files-into-the-asset"></a>Créer un actif et de télécharger des fichiers dans des immobilisations

Pour gérer, encoder et diffuser vos vidéos, vous devez d’abord télécharger votre contenu dans Microsoft Azure Media Services. Une fois téléchargé, votre contenu est stocké de manière sécurisée dans le nuage pour le traitement et la diffusion en continu.

Pour plus d’informations, consultez [Télécharger les fichiers dans un compte de Services de support](media-services-dotnet-upload-files.md).

##<a name="encode-the-asset-containing-the-file-to-the-adaptive-bitrate-mp4-set"></a>Codage de l’élément contenant le fichier à la vitesse de transmission adaptive que MP4 définie

Avec cryptage dynamique vous suffit pour créer une immobilisation qui contient un ensemble de fichiers MP4 de multi-bitrate ou des fichiers source de diffusion en continu lisse multi-bitrate. Puis, selon le format spécifié dans la demande de manifeste et fragment, le serveur de diffusion en continu à la demande veillera à ce que vous recevez le flux du protocole que vous avez choisi. Par conséquent, il vous suffit de stocker et de payer pour les fichiers au format de stockage unique et service Media Services pour la génération et fournir la réponse appropriée en fonction de la demande du client. Pour plus d’informations, consultez la rubrique de [Vue d’ensemble dynamique des emballages](media-services-dynamic-packaging-overview.md) .

Pour obtenir des instructions sur la manière d’encoder, consultez [comment coder une immobilisation à l’aide de Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).


##<a id="create_contentkey"></a>Créer une clé de contenu et l’associer à l’actif codé

Dans les Services de support, la clé de contenu contient la clé que vous souhaitez crypter une immobilisation.

Pour plus d’informations, consultez [créer clé de contenu](media-services-dotnet-create-contentkey.md).


##<a id="configure_key_auth_policy"></a>Configurer la stratégie d’autorisation de la clé de contenu

Media Services prend en charge plusieurs méthodes d’authentification des utilisateurs qui effectuent des demandes de clés. La stratégie d’autorisation de clés contenu doit être configurée par vous et remplie par le client (lecteur) dans l’ordre de la clé pour être remis au client. La stratégie d’autorisation de clés contenu peut avoir une ou plusieurs restrictions d’autorisation : ouvrir ou jeton de restriction.

Pour plus d’informations, consultez [Configurer une stratégie contenu clé d’autorisation](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption).

##<a id="configure_asset_delivery_policy"></a>Configurer la stratégie de remise d’actifs 

Configurer la stratégie de livraison de votre actif. Certaines choses que la configuration de stratégie de remise actif inclut :

- L’URL d’acquisition de licence DRM. 
- Le protocole de livraison de la ressource (par exemple, MPEG tiret, TLS, HDS, diffusion en continu lisse ou tous). 
- Le type de cryptage dynamique (dans ce cas, il s’agit de cryptage commun). 

Pour plus d’informations, consultez [stratégie de livraison de configuration actif ](media-services-rest-configure-asset-delivery-policy.md).

##<a id="create_locator"></a>Créer une localisateur de diffusion en continu afin d’obtenir une URL de transmission en continu à la demande

Vous devez fournir aux utilisateurs l’URL de diffusion en continu lisse, tiret ou TLS.

>[AZURE.NOTE]Si vous ajoutez ou mettre à jour la stratégie de livraison de vos ressources, vous devez supprimer un localisateur existant (le cas échéant) et créer une nouvelle adresse.

Pour obtenir des instructions sur la façon de publier un actif et de générer une URL de transmission en continu, consultez [créer une URL de transmission en continu](media-services-deliver-streaming-content.md).

##<a name="get-a-test-token"></a>Obtenir un test de jeton

Obtenir un test de jeton basé sur la restriction de jeton qui a été utilisée pour la stratégie d’autorisation clé.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
    
    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

    
Vous pouvez utiliser le [Lecteur de l’AMS](http://amsplayer.azurewebsites.net/azuremediaplayer.html) pour tester votre flux de données.

##<a id="example"></a>Exemple


L’exemple suivant montre la fonctionnalité introduite dans Azure Media Services SDK pour .net-Version 3.5.2 (en particulier, la possibilité de définir un modèle de licence de Widevine et de demander une licence Widevine Azure Media Services). La commande de package Nuget suivante a été utilisée pour installer le package :

    PM> Install-Package windowsazure.mediaservices -Version 3.5.2


1. Créez un nouveau projet de Console.
1. Utilisez NuGet pour installer et ajouter Azure Media Services .NET SDK.
2. Ajouter des références : System.Configuration.
2. Ajoutez le fichier de configuration qui contient le nom de compte et les informations de clé :
    
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
              <appSettings>
            
                <add key="MediaServicesAccountName" value="AccountName"/>
                <add key="MediaServicesAccountKey" value="AccountKey"/>
            
                <add key="Issuer" value="http://testacs.com"/>
                <add key="Audience" value="urn:test"/>
              </appSettings>
        </configuration>

1. Obtenir au moins une unité de transmission en continu pour le point de terminaison en continu à partir de laquelle vous envisagez de livraison votre contenu. Pour plus d’informations, reportez-vous à la section : [configurer des points de terminaison en continu](media-services-dotnet-get-started.md#configure-streaming-endpoint-using-the-portal).

1. Remplacer le code dans le fichier Program.cs par le code illustré dans cette section.
    
    Veillez à mettre à jour les variables pour pointer vers les dossiers où se trouvent vos fichiers d’entrée.
        
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
        
        namespace DynamicEncryptionWithDRM
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
        
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
        
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
        
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateCommonTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
                    Console.WriteLine();
        
                    if (tokenRestriction)
                        tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
                    else
                        AddOpenAuthorizationPolicy(key);
        
                    Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
                    Console.WriteLine();
        
                    CreateAssetDeliveryPolicy(encodedAsset, key);
                    Console.WriteLine("Created asset delivery policy. \n");
                    Console.WriteLine();
        
                    if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
                    {
                        // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                        // back into a TokenRestrictionTemplate class instance.
                        TokenRestrictionTemplate tokenTemplate =
                            TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
        
                        // Generate a test token based on the the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey, 
                                                                                DateTime.UtcNow.AddDays(365));
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
        
                    // You can use the http://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
                    // Note that DASH works on IE 11 (via PlayReady), Edge (via PlayReady), Chrome (via Widevine).
                     
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);
        
                    Console.ReadLine();
                }
        
        
                static public IAsset UploadFileAndCreateAsset(string singleFilePath)
                {
                    if (!File.Exists(singleFilePath))
                    {
                        Console.WriteLine("File does not exist.");
                        return null;
                    }
        
                    var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);
        
                    var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));
        
                    Console.WriteLine("Created assetFile {0}", assetFile.Name);
        
                    var policy = _context.AccessPolicies.Create(
                                            assetName,
                                            TimeSpan.FromDays(30),
                                            AccessPermissions.Write | AccessPermissions.List);
        
                    var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
        
                    Console.WriteLine("Upload {0}", assetFile.Name);
        
                    assetFile.Upload(singleFilePath);
                    Console.WriteLine("Done uploading {0}", assetFile.Name);
        
                    locator.Delete();
                    policy.Delete();
        
                    return inputAsset;
                }
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
                {
                    var encodingPreset = "H264 Multiple Bitrate 720p";
            
                    IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                                            inputAsset.Name,
                                            encodingPreset));
            
                    var mediaProcessors =
                        _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();
            
                    var latestMediaProcessor =
                        mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();
            
                    ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
                    encodeTask.InputAssets.Add(inputAsset);
                    encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset),    AssetCreationOptions.StorageEncrypted);
            
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
            
                    return job.OutputMediaAssets[0];
                }
        
        
                static public IContentKey CreateCommonTypeContentKey(IAsset asset)
                {
                    
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryption);
        
                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);
        
                    return key;
                }
        
                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {
        
                    // Create ContentKeyAuthorizationPolicy with Open restrictions 
                    // and create authorization policy          
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
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
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
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
        
                    //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
                    //It contains a field for a custom data string between the license server and the application 
                    //(may be useful for custom app logic) as well as a list of one or more license templates.
                    PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
        
                    // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
                    // to be returned to the end users. 
                    //It contains the data on the content key in the license and any rights or restrictions to be 
                    //enforced by the PlayReady DRM runtime when using the content key.
                    PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
                    //Configure whether the license is persistent (saved in persistent storage on the client) 
                    //or non-persistent (only held in memory while the player is using the license).  
                    licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
        
                    // AllowTestDevices controls whether test devices can use the license or not.  
                    // If true, the MinimumSecurityLevel property of the license
                    // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
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
        
                    //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
                    // If the output protections are configured too restrictive, 
                    // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.
        
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
                                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
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
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    // Get the PlayReady license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
                
                    // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
                    // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
                    // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption 
                    // to append /? KID =< keyId > to the end of the url when creating the manifest.
                    // As a result Widevine license acquisition URL will have KID appended twice, 
                    // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.
            
                    Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
                    UriBuilder uriBuilder = new UriBuilder(widevineUrl);
                    uriBuilder.Query = String.Empty;
                    widevineUrl = uriBuilder.Uri;
        
                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                            {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}
        
                        };
        
                    // In this case we only specify Dash streaming protocol in the delivery policy,
                    // All other protocols will be blocked from streaming.
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryption,
                        AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);
        
        
                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);
        
                }
        
                /// <summary>
                /// Gets the streaming origin locator.
                /// </summary>
                /// <param name="assets"></param>
                /// <returns></returns>
                static public string GetStreamingOriginLocator(IAsset asset)
                {
        
                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 
        
                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                 EndsWith(".ism")).
                                                 FirstOrDefault();
        
                    // Create a 30-day readonly access policy. 
                    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
                        TimeSpan.FromDays(30),
                        AccessPermissions.Read);
        
                    // Create a locator to the streaming content on an origin. 
                    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
                        policy,
                        DateTime.UtcNow.AddMinutes(-5));
        
                    // Create a URL to the manifest file. 
                    return originLocator.Path + assetFile.Name;
                }
        
                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
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


## <a name="next-step"></a>Étape suivante

Passez en revue Media Services, voies de formation.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Voir aussi

[CENC avec Multi-DRM et de contrôle d’accès](media-services-cenc-with-multidrm-access-control.md)

[Configurer Widevine emballage avec AMS](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

[Annonce de services de livraison de licence Google Widevine dans Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
