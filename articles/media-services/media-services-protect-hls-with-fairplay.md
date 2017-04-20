<properties 
    pageTitle="Protéger votre contenu avec Apple FairPlay et/ou Microsoft PlayReady de TLS | Microsoft Azure" 
    description="Cette rubrique donne une vue d’ensemble et montre comment utiliser Azure Media Services dynamiquement chiffrer votre contenu de diffusion en continu Live HTTP (TLS) avec FairPlay d’Apple. Il montre également comment utiliser les services de livraison licence Media pour livrer du FairPlay des licences aux clients." 
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
    ms.date="09/27/2016"
    ms.author="juliako"/>

# <a name="protect-your-hls-content-with-apple-fairplay-andor-microsoft-playready"></a>Protéger votre contenu avec Apple FairPlay et/ou Microsoft PlayReady de TLS

Azure Media Services vous permet dynamiquement chiffrer votre contenu de diffusion en continu Live HTTP (TLS) à l’aide des formats suivants :  

- **Touche effacer d’enveloppe de AES-128** 

    L’intégralité du morceau est crypté en utilisant le mode **CBC d’AES-128** . Le déchiffrement du flux est prise en charge en mode natif par e/s et le lecteur OSX. Pour plus d’informations, consultez [cet article](media-services-protect-with-aes128.md).

- **Apple FairPlay** 

    Les échantillons audio et vidéo individuels sont chiffrés en utilisant le mode **CBC d’AES-128** . **FairPlay en continu.** (IPS) est intégré dans les systèmes d’exploitation de périphérique, avec une prise en charge native sur iOS et Apple TV. Safari sur OS X permet d’images par seconde à l’aide de la prise en charge des Extensions de média crypté (EME) interface.
- **Microsoft PlayReady**

L’image suivante montre le flux de travail **TLS + FairPlay et/ou PlayReady cryptage dynamique** .

![Protection avec FairPlay](./media/media-services-content-protection-overview/media-services-content-protection-with-fairplay.png)

Cette rubrique montre comment utiliser Azure Media Services dynamiquement chiffrer votre contenu TLS avec Apple FairPlay. Il montre également comment utiliser les services de livraison licence Media pour livrer du FairPlay des licences aux clients.

>[AZURE.NOTE] Si vous souhaitez également chiffrer votre contenu TLS avec PlayReady, vous devez créer une clé de contenu commune et l’associer à vos ressources. Vous devez également configurer la stratégie d’autorisation de la clé de contenu, comme décrit dans la rubrique [cryptage de courant dynamique à l’aide de PlayReady](media-services-protect-with-drm.md) .

    
## <a name="requirements-and-considerations"></a>Configuration requise et remarques

- Les éléments suivants sont requis lors de l’utilisation d’AMS de TLS cryptée avec FairPlay de livrer et de fournir des licences de FairPlay.

    - Un compte Azure. Pour plus d’informations, reportez-vous à la section [D’essai Azure](/pricing/free-trial/?WT.mc_id=A261C142F).
    - Un compte de Services de support. Pour créer un compte de Services de support, consultez [Créer un compte](media-services-portal-create-account.md).
    - Inscrivez-vous avec le [Programme de développement d’Apple](https://developer.apple.com/).
    - Apple nécessite que le propriétaire du contenu obtenir le [package de déploiement](https://developer.apple.com/contact/fps/). État de la demande que vous déjà implémenté KSM (Module de sécurité clé) avec Azure Media Services et que vous demandez le package final i/s. Il y aura des instructions contenues dans le package final i/s pour générer de certification et d’obtenir des ASK, que vous utiliserez pour configurer les FairPlay. 

    - Azure Media Services .NET SDK de version **3.6.0** ou ultérieure.

- Les éléments suivants doivent être définis sur côté de livraison clé AMS :
    - **Application de certificat (AC)** - fichier .pfx contenant la clé privée. Ce fichier est créé par le client et crypté avec un mot de passe par le même client. 
        
        Lorsque le client configure une stratégie de distribution de clés, ils doivent fournir ce mot de passe et le .pfx dans format base64.

        Les étapes suivantes décrivent comment générer un certificat pfx pour FairPlay.
        
        1. Installer OpenSSL à partir de https://slproweb.com/products/Win32OpenSSL.html
        
            Accédez au dossier dans lequel le certificat de FairPlay et d’autres fichiers fournis par Apple.
        
        2. Ligne de commande pour convertir la zone cer pem :
        
            « C:\OpenSSL-Win32\bin\openssl.exe » x509-informer der-dans la fairplay.cer-out fairplay-out.pem
        
        3. Ligne de commande pour convertir pem pfx avec la clé privée (le mot de passe du fichier pfx est alors invité par OpenSSL).
        
            « C:\OpenSSL-Win32\bin\openssl.exe » pkcs12-exportez - fairplay-out.pfx-privatekey.pem inkey-dans la fairplay-out.pem - passin file:privatekey-pem-pass.txt 
        
    - **Certificat de l’application de mot de passe** - mot de passe du client pour créer le fichier .pfx.
    - **Code de mot de passe de certificat de l’application** - le client doit télécharger le mot de passe similaire à comment ils téléchargent d’autres clés de l’AMS et à l’aide de la valeur d’énumération **ContentKeyType.FairPlayPfxPassword** . Par conséquent, ils obtiendront id AMS que c’est ce qu’ils doivent utiliser l’option de la stratégie de distribution de clés.
    - **iv** - valeur aléatoire de 16 octets, doit correspondre le vecteur d’initialisation dans la stratégie de livraison des actifs. Client génère le vecteur d’initialisation et la place dans les deux emplacements : actif stratégie et la clé livraison stratégie option de remise. 
    - **ASK** - confirmation de la réception (clé secrète de l’Application) lorsque vous générez la certification à l’aide du portail de développement d’Apple. Chaque équipe de développement recevront une confirmation unique. Enregistrez une copie de la confirmation et stockez-la dans un endroit sûr. Vous devez configurer le demander comme FairPlayAsk pour Azure Media Services ultérieurement. 
    -  **Demander un ID** - est obtenue lorsque le client télécharge ASk dans AMS. Le client doit télécharger le demander à l’aide de la valeur d’énumération **ContentKeyType.FairPlayASk** . Par conséquent, l’id de l’AMS est retourné, et c’est ce qui doit être utilisé lors de la définition de l’option de la stratégie de distribution de clés.

- Les éléments suivants doivent être définis par le client IPS :
    - **Application de certificat (AC)** - fichier.cer/.der contenant la clé publique que le système d’exploitation utilise pour crypter une charge utile. AMS doit savoir car il est requis par le lecteur. Le service de distribution de clés décrypte à l’aide de la clé privée correspondante.

- Pour lire un flux de données chiffré de FairPlay, vous devez obtenir la première de poser de réel et de générer un certificat réel. Ce processus crée tous les 3 parties :

    -  .der, 
    -  .pfx et 
    -  le mot de passe pour le .pfx.
 
- Les clients qui prennent en charge TLS avec le cryptage **AES-128 CBC** : Safari sur OS X, Apple TV, iOS.

##<a name="steps-for-configuring-fairplay-dynamic-encryption-and-license-delivery-services"></a>Étapes de configuration de FairPlay dynamique cryptage et licence services de livraison

Voici les étapes générales que vous auriez à effectuer lors de la protection de vos ressources avec FairPlay, en utilisant les services de livraison licence Media, ainsi que le cryptage dynamique.

1. Créer un actif et de télécharger des fichiers dans l’immobilisation. 
1. Coder l’actif contenant le fichier à la vitesse de transmission adaptive que MP4 définie.
1. Créer une clé de contenu et l’associer à l’actif codé.  
1. Configurer la stratégie d’autorisation de la clé de contenu. Lors de la création de la stratégie d’autorisation de clés contenu, vous devez spécifier les éléments suivants : 
    
    - mode de remise (dans ce cas, FairPlay), 
    - Configuration des options stratégie FairPlay. Pour plus d’informations sur la configuration de FairPlay, consultez la méthode ConfigureFairPlayPolicyOptions() dans l’exemple ci-dessous.
    
        >[AZURE.NOTE] En règle générale, vous pouvez configurer les options de stratégie de FairPlay qu’une seule fois, dans la mesure où vous n’avez un ensemble de certification et demander.
-restrictions (ouvrir ou jetons), - et les informations spécifiques au type de distribution de clés qui définit comment la clé est envoyée au client. 
    
2. Configurer la stratégie de remise d’actifs. Configuration de la stratégie de livraison comprend : 

    - protocole de livraison (TLS), 
    - le type de cryptage dynamique (chiffrement CBC de commune), 
    - URL d’acquisition de licence. 
    
    >[AZURE.NOTE]Si vous souhaitez fournir un flux qui est chiffré avec FairPlay + un autre DRM, vous devez configurer les stratégies de remise distinct 
    >
    >- Une IAssetDeliveryPolicy configuration tiret avec CENC (PlayReady + WideVine) et lisse avec PlayReady. 
    >- IAssetDeliveryPolicy un autre à configurer FairPlay pour TLS

1. Créer un localisateur à la demande pour obtenir une URL de transmission en continu.

##<a name="using-fairplay-key-delivery-by-playerclient-apps"></a>À l’aide de la distribution de clés de FairPlay par les applications client/lecteur

Clients peuvent développer des applications de lecteur à l’aide du SDK d’iOS. Les clients doivent mettre en œuvre le protocole d’échange de licence pour pouvoir lire le contenu de FairPlay. Le protocole d’échange de licence n’est pas spécifié par Apple. C’est à chaque application de l’envoi de demandes de livraison de clé. Les services de livraison clé AMS FairPlay attend le SPC à venir en tant que message post codées de www-form-url sous la forme suivante : 

    spc=<Base64 encoded SPC>

>[AZURE.NOTE] Azure Media Player ne prend pas en charge la lecture de FairPlay prêts à l’emploi. Les clients ont besoin obtenir le lecteur de l’exemple d’un compte de développeur d’Apple pour obtenir FairPlay lecture sur MAC OSX. 
 
##<a name="streaming-urls"></a>URL de la diffusion en continu

Si votre bien a été chiffré avec plusieurs DRM, vous devez utiliser une balise de cryptage dans l’URL en continu : (format = 'm3u8-aapl', cryptage = 'xxx').

Les considérations suivantes s’appliquent :

- Peut être spécifié uniquement zéro ou un type de cryptage.
- Type de chiffrement ne doit être spécifié dans l’url, si seul un cryptage a été appliqué à l’actif.
- Type de cryptage est la casse.
- Vous pouvez spécifier les types de cryptage suivants :  
    - **cenc**: cryptage commun (Playready ou Widevine)
    - **liste des CBC-aapl**: Fairplay
    - **CBC**: le cryptage AES enveloppe.


##<a name="net-example"></a>Exemple .NET


L’exemple suivant montre la fonctionnalité introduite dans Azure Media Services SDK pour .net-Version 3.6.0 (la capacité d’utiliser Azure Media Services pour fournir le contenu chiffré avec FairPlay). La commande de package Nuget suivante a été utilisée pour installer le package :

    PM> Install-Package windowsazure.mediaservices -Version 3.6.0


1. Créez un projet de Console.
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
            
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
        using Newtonsoft.Json;
        using System.Security.Cryptography.X509Certificates;
        
        namespace DynamicEncryptionWithFairPlay
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
        
                    IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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
        
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);
        
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
        
                static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
                {
                    // Create HLS SAMPLE AES encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryptionCbcs);
        
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
        
        
                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
        
                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                        ContentKeyDeliveryType.FairPlay,
                        restrictions,
                        FairPlayConfiguration);
        
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
        
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
        
                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
        
        
                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                               ContentKeyDeliveryType.FairPlay,
                               restrictions,
                               FairPlayConfiguration);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
        
                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
        
                    return tokenTemplateString;
                }
        
                private static string ConfigureFairPlayPolicyOptions()
                {
                    // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK. 
                    // However, for production you must use a real ASK from Apple bound to a real prod certificate.
                    byte[] askBytes = Guid.NewGuid().ToByteArray();
                    var askId = Guid.NewGuid();
                    // Key delivery retrieves askKey by askId and uses this key to generate the response.
                    IContentKey askKey = _context.ContentKeys.Create(
                                            askId,
                                            askBytes,
                                            "askKey",
                                            ContentKeyType.FairPlayASk);
        
                    //Customer password for creating the .pfx file.
                    string pfxPassword = "<customer password for creating the .pfx file>";
                    // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
                    var pfxPasswordId = Guid.NewGuid();
                    byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
                    IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                                            pfxPasswordId,
                                            pfxPasswordBytes,
                                            "pfxPasswordKey",
                                            ContentKeyType.FairPlayPfxPassword);
        
                    // iv - 16 bytes random value, must match the iv in the asset delivery policy.
                    byte[] iv = Guid.NewGuid().ToByteArray();
        
                    //Specify the .pfx file created by the customer.
                    var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);
        
                    string FairPlayConfiguration =
                        Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                            appCert,
                            pfxPassword,
                            pfxPasswordId,
                            askId,
                            iv);
        
                    return FairPlayConfiguration;
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
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();
        
                    var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);
        
                    FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);
        
                    // Get the FairPlay license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);
        
                    // The reason the below code replaces "https://" with "skd://" is because
                    // in the IOS player sample code which you obtained in Apple developer account, 
                    // the player only recognizes a Key URL that starts with skd://. 
                    // However, if you are using a customized player, 
                    // you can choose whatever protocol you want. 
                    // For example, "https". 

                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                            {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
                        };
        
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
                        AssetDeliveryProtocol.HLS,
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


##<a name="next-steps-media-services-learning-paths"></a>Les étapes suivantes : Media Services, voies de formation

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
