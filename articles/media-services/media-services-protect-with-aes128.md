<properties
    pageTitle="À l’aide du service de distribution de clés et de cryptage dynamique de AES-128 | Microsoft Azure"
    description="Microsoft Azure Media Services vous permet de fournir le contenu chiffré avec les clés de cryptage AES 128 bits. Media Services fournit également le service de remise de la clé qui fournit des clés de cryptage à des utilisateurs autorisés. Cette rubrique montre comment crypter avec AES-128 et utiliser le service de distribution de clés de manière dynamique."
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
    ms.date="10/24/2016"
    ms.author="juliako"/>

#<a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>À l’aide du service de distribution de clés et de cryptage dynamique de AES-128

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-aes128.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="overview"></a>Vue d’ensemble

>[AZURE.NOTE] Consultez [cette](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption) vidéo pour obtenir une vue d’ensemble de la façon de protéger votre contenu multimédia avec le chiffrement AES.

Microsoft Azure Media Services vous permet de remettre Http Live-diffusion en continu (TLS) et bon flux de données cryptées avec le cryptage AES (Advanced Standard) (à l’aide de clés de cryptage à 128 bits). Media Services fournit également le service de remise de la clé qui fournit des clés de cryptage à des utilisateurs autorisés. Si vous souhaitez que pour les Services de support crypter une immobilisation, vous devez associer une clé de cryptage à l’actif et également de configurer les stratégies d’autorisation pour la clé. Lorsqu’un flux est demandé par un lecteur, Media Services utilise la clé spécifiée pour dynamiquement chiffrer votre contenu à l’aide du cryptage AES. Pour déchiffrer le flux de données, le lecteur demande la clé à partir du service de distribution de clés. Pour décider ou non de l’utilisateur est autorisé à obtenir la clé, le service évalue les stratégies d’autorisation que vous avez spécifiée pour la clé.

Media Services prend en charge plusieurs méthodes d’authentification des utilisateurs qui effectuent des demandes de clés. La stratégie d’autorisation de clés contenu peut avoir une ou plusieurs restrictions d’autorisation : ouvrir ou jeton de restriction. La stratégie de jeton restreint doit être accompagnée d’un jeton émis par un Service (jeton de sécurité). Media Services prend en charge les jetons dans les formats de [Jetons de Web Simple](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) et [JSON Web jeton](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Pour plus d’informations, consultez [configuration de la stratégie d’autorisation de la clé de contenu](media-services-protect-with-aes128.md#configure_key_auth_policy).

Pour tirer parti de chiffrement dynamiques, vous devez avoir un actif qui contient un ensemble de fichiers MP4 de multi-bitrate ou des fichiers source de diffusion en continu lisse multi-bitrate. Vous devez également configurer la stratégie de livraison de l’immobilisation (décrite plus loin dans cette rubrique). Puis, selon le format spécifié dans l’URL en continu, le serveur de diffusion en continu à la demande veillera à ce que le flux est remis dans le protocole que vous avez choisi. Par conséquent, il vous suffit de stocker et de payer pour les fichiers au format de stockage unique et service Media Services pour la génération et fournir la réponse appropriée en fonction de la demande du client.

Cette rubrique peut être utile pour les développeurs qui travaillent sur les applications qui offrent des médias protégés. La rubrique vous indique comment configurer le service de distribution de clés avec les stratégies d’autorisation afin que seuls les clients autorisés pourraient recevoir les clés de cryptage. Il montre également comment utiliser le cryptage dynamique.

>[AZURE.NOTE]Pour utiliser le chiffrement dynamiques, vous devez d’abord obtenir au moins une unité d’échelle (également appelé une unité en continu). Pour plus d’informations, consultez [Comment faire évoluer un Service de support](media-services-portal-manage-streaming-endpoints.md).

##<a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Le cryptage AES-128 dynamique et Workflow Service de distribution de clés

Voici les étapes générales que vous auriez à effectuer lors du chiffrement de vos ressources avec AES, en utilisant les services de livraison clé Media, ainsi que le cryptage dynamique.

1. [Créer un actif et de télécharger des fichiers dans l’immobilisation](media-services-protect-with-aes128.md#create_asset).
1. [Coder l’actif contenant le fichier à la vitesse de transmission adaptive que MP4 définie](media-services-protect-with-aes128.md#encode_asset).
1. [Créer une clé de contenu et y associer l’immobilisation codée](media-services-protect-with-aes128.md#create_contentkey). Dans les Services de support, la clé de contenu contient la clé de cryptage de l’actif.
1. [Configurer la stratégie d’autorisation de la clé de contenu](media-services-protect-with-aes128.md#configure_key_auth_policy). La stratégie d’autorisation de clés contenu doit être configurée par vous et remplie par le client dans l’ordre de la clé de contenu à livrer au client.
1. [Configurer la stratégie de la livraison d’un actif](media-services-protect-with-aes128.md#configure_asset_delivery_policy). La configuration de la stratégie de livraison comprend : la clé URL d’acquisition et le vecteur d’initialisation (IV) (AES 128 nécessite le même vecteur d’initialisation à fournir lors du chiffrement et de déchiffrement), protocole de livraison (par exemple, MPEG tiret, TLS, HDS, diffusion en continu lisse ou tous), le type de cryptage dynamique (par exemple, enveloppe ou aucun cryptage dynamique).

Vous pouvez appliquer une stratégie différente pour chaque protocole sur le même actif. Par exemple, vous pouvez appliquer le chiffrement PlayReady lisse/tiret et enveloppe AES pour TLS. Tous les protocoles qui ne sont pas définis dans une stratégie de remise (par exemple, vous ajoutez une stratégie unique qui spécifie uniquement TLS comme protocole) ne pourront pas en continu. L’exception à cette règle est que si vous n’avez aucune stratégie de livraison actif défini à tout. Ensuite, tous les protocoles peuvent être utilisés en clair.

1. [Créer un localisateur à la demande](media-services-protect-with-aes128.md#create_locator) afin d’obtenir une URL de transmission en continu.

La rubrique indique également [comment une application cliente peut demander une clé à partir du service de distribution de clés](media-services-protect-with-aes128.md#client_request).

Vous trouverez un.NET [exemple](media-services-protect-with-aes128.md#example) complet à la fin de la rubrique.

L’image suivante montre le flux de travail décrit ci-dessus. Ici, le jeton est utilisé pour l’authentification.

![Protéger avec AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Le reste de cette rubrique fournit des explications détaillées, des exemples de code et des liens vers des rubriques qui vous expliquent comment réaliser les tâches décrites ci-dessus.

##<a name="current-limitations"></a>Limitations actuelles

Si vous ajoutez ou mettre à jour la stratégie de livraison de vos ressources, vous devez supprimer un localisateur existant (le cas échéant) et créer une nouvelle adresse.

##<a id="create_asset"></a>Créer un actif et de télécharger des fichiers dans des immobilisations

Pour gérer, encoder et diffuser vos vidéos, vous devez d’abord télécharger votre contenu dans Microsoft Azure Media Services. Une fois téléchargé, votre contenu est stocké de manière sécurisée dans le nuage pour le traitement et la diffusion en continu. 

Pour plus d’informations, consultez [Télécharger les fichiers dans un compte de Services de support](media-services-dotnet-upload-files.md).

##<a id="encode_asset"></a>Codage de l’élément contenant le fichier à la vitesse de transmission adaptive que MP4 définie

Avec cryptage dynamique vous suffit pour créer une immobilisation qui contient un ensemble de fichiers MP4 de multi-bitrate ou des fichiers source de diffusion en continu lisse multi-bitrate. Puis, selon le format spécifié dans la demande de manifeste ou d’un fragment, la diffusion en continu de la demande serveur garantissent que le flux du protocole que vous avez choisi. Par conséquent, il vous suffit de stocker et de payer pour les fichiers au format de stockage unique et service Media Services pour la génération et fournir la réponse appropriée en fonction de la demande du client. Pour plus d’informations, consultez la rubrique de [Vue d’ensemble dynamique des emballages](media-services-dynamic-packaging-overview.md) .

Pour obtenir des instructions sur la manière d’encoder, consultez [comment coder une immobilisation à l’aide de Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

##<a id="create_contentkey"></a>Créer une clé de contenu et l’associer à l’actif codé

Dans les Services de support, la clé de contenu contient la clé que vous souhaitez crypter une immobilisation.

Pour plus d’informations, consultez [créer clé de contenu](media-services-dotnet-create-contentkey.md).

##<a id="configure_key_auth_policy"></a>Configurer la stratégie d’autorisation de la clé de contenu

Media Services prend en charge plusieurs méthodes d’authentification des utilisateurs qui effectuent des demandes de clés. La stratégie d’autorisation de clés contenu doit être configurée par vous et remplie par le client (lecteur) dans l’ordre de la clé pour être remis au client. La stratégie d’autorisation de clés contenu peut avoir une ou plusieurs restrictions d’autorisation : ouvert, le jeton de restriction ou de restriction IP.

Pour plus d’informations, consultez [Configurer une stratégie contenu clé d’autorisation](media-services-dotnet-configure-content-key-auth-policy.md).

##<a id="configure_asset_delivery_policy"></a>Configurer la stratégie de remise d’actifs 

Configurer la stratégie de livraison de votre actif. Certaines choses que la configuration de stratégie de remise actif inclut :

- L’URL d’acquisition de la clé. 
- Le vecteur d’initialisation (IV) à utiliser pour le cryptage de l’enveloppe. AES 128 nécessite le même vecteur d’initialisation à fournir lors du chiffrement et de déchiffrement. 
- Le protocole de livraison de la ressource (par exemple, MPEG tiret, TLS, HDS, diffusion en continu lisse ou tous).
- Le type de cryptage dynamique (par exemple, les enveloppes AES) ou aucun chiffrement dynamiques. 

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

##<a id="client_request"></a>Comment votre client peut demander une clé à partir du service de distribution de clés ?

À l’étape précédente, vous avez construit l’URL qui pointe vers un fichier de manifeste. Votre client a besoin d’extraire les informations nécessaires à partir des fichiers manifeste en continu pour faire une demande au service de distribution de clés.

###<a name="manifest-files"></a>Fichiers manifeste

Le client doit extraire l’URL (qui contient également la clé de contenu Id (kid)) la valeur à partir du fichier manifeste. Puis, le client essaiera d’obtenir la clé de cryptage à partir du service de distribution de clés. Le client doit également extraire la valeur de VI et l’utilisation qu’il de déchiffrer le flux. Le montre l’extrait de code suivant le <Protection> élément du manifeste de diffusion en continu lisse.

    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>

Dans le cas de TLS, le manifeste de la racine est divisé en fichiers de segment. 

Par exemple, le manifeste de la racine est : http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl) et il contient une liste de noms de fichier de segment.
    
    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Si vous ouvrez un fichier de segment dans un éditeur de texte (par exemple, http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels (514369)/Manifest(video,format=m3u8-aapl), il doit contenir #EXT-X-clé qui indique que le fichier est crypté.
    
    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST
    
###<a name="request-the-key-from-the-key-delivery-service"></a>Demander la clé à partir du service de distribution de clés

Le code suivant montre comment envoyer une requête au service de livraison clé des Services de support à l’aide d’une clé livraison Uri (qui a été extrait à partir du manifeste) et un jeton (cette rubrique ne communique pas avec sur l’obtention des jetons Web Simple à partir d’un Service de jeton de Secure).

    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);
                
        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;
    
        var response = request.GetResponse();
     
        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }
    
        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();
    
        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
    
##<a id="example"></a>Exemple

1. Créez un nouveau projet de Console.
1. Utilisez NuGet pour installer et ajouter les Extensions .NET SDK Azure Media Services. L’installation de ce package, également installe le Kit de développement logiciel Media Services .NET et ajoute toutes les autres dépendances requises.
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

1. Remplacer le code dans le fichier Program.cs par le code illustré dans cette section.
    
    Veillez à mettre à jour les variables pour pointer vers les dossiers où se trouvent vos fichiers d’entrée.
            
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;
        
        namespace AESDynamicEncryptionAndKeyDeliverySvc
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                // A Uri describing the issuer of the token.  
                // Must match the value in the token for the token to be considered valid.
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
                // The Audience or Scope of the token.  
                // Must match the value in the token for the token to be considered valid.
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
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateEnvelopeTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
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
        
                        // Generate a test token based on the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
        
                        //The GenerateTestToken method returns the token without the word “Bearer” in front
                        //so you have to add it in front of the token string. 
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
        
                    // You can use the bit.ly/aesplayer Flash player to test the URL 
                    // (with open authorization policy). 
                    // Paste the URL and click the Update button to play the video. 
                    //
                    string URL = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Smooth Streaming Url: {0}/manifest", URL);
                    Console.WriteLine();
                    Console.WriteLine("HLS Url: {0}/manifest(format=m3u8-aapl)", URL);
                    Console.WriteLine();
        
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
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.StorageEncrypted);
        
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
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Create a task with the encoding details, using a string preset.
                    // In this case "H264 Multiple Bitrate 720p" preset is used.
                    ITask task = job.Tasks.AddNew("My encoding task",
                        processor,
                        "H264 Multiple Bitrate 720p",
                        TaskOptions.None);
        
                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.StorageEncrypted);
        
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
                    return job.OutputMediaAssets[0];
                }
        
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
                    return processor;
                }
        
                static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
                {
                    // Create envelope encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.EnvelopeEncryption);
        
                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);
        
                    return key;
                }
        
                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {
                    // Create ContentKeyAuthorizationPolicy with Open restrictions 
                    // and create authorization policy             
                    IContentKeyAuthorizationPolicy policy = _context.
                                            ContentKeyAuthorizationPolicies.
                                            CreateAsync("Open Authorization Policy").Result;
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                        new List<ContentKeyAuthorizationPolicyRestriction>();
        
                    ContentKeyAuthorizationPolicyRestriction restriction =
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "HLS Open Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                            Requirements = null // no requirements needed for HLS
                                };
        
                    restrictions.Add(restriction);
        
                    IContentKeyAuthorizationPolicyOption policyOption =
                        _context.ContentKeyAuthorizationPolicyOptions.Create(
                        "policy",
                        ContentKeyDeliveryType.BaselineHttp,
                        restrictions,
                        "");
        
                    policy.Options.Add(policyOption);
        
                    // Add ContentKeyAutorizationPolicy to ContentKey
                    contentKey.AuthorizationPolicyId = policy.Id;
                    IContentKey updatedKey = contentKey.UpdateAsync().Result;
                    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
                }
        
                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();
        
                    IContentKeyAuthorizationPolicy policy = _context.
                                            ContentKeyAuthorizationPolicies.
                                            CreateAsync("HLS token restricted authorization policy").Result;
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                            new List<ContentKeyAuthorizationPolicyRestriction>();
        
                    ContentKeyAuthorizationPolicyRestriction restriction =
                            new ContentKeyAuthorizationPolicyRestriction
                            {
                                Name = "Token Authorization Policy",
                                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                                Requirements = tokenTemplateString
                            };
        
                    restrictions.Add(restriction);
        
                    //You could have multiple options 
                    IContentKeyAuthorizationPolicyOption policyOption =
                        _context.ContentKeyAuthorizationPolicyOptions.Create(
                            "Token option for HLS",
                            ContentKeyDeliveryType.BaselineHttp,
                            restrictions,
                            null  // no key delivery data is needed for HLS
                            );
        
                    policy.Options.Add(policyOption);
        
                    // Add ContentKeyAutorizationPolicy to ContentKey
                    contentKey.AuthorizationPolicyId = policy.Id;
                    IContentKey updatedKey = contentKey.UpdateAsync().Result;
                    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
        
                    return tokenTemplateString;
                }
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        
                    string envelopeEncryptionIV = Convert.ToBase64String(GetRandomBuffer(16));
            
                    // When configuring delivery policy, you can choose to associate it
                    // with a key acquisition URL that has a KID appended or
                    // or a key acquisition URL that does not have a KID appended  
                    // in which case a content key can be reused. 

                    // EnvelopeKeyAcquisitionUrl:  contains a key ID in the key URL.
                    // EnvelopeBaseKeyAcquisitionUrl:  the URL does not contains a key ID

                    // The following policy configuration specifies: 
                    // key url that will have KID=<Guid> appended to the envelope and
                    // the Initialization Vector (IV) to use for the envelope encryption.
                    
                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                    {
                                {AssetDeliveryPolicyConfigurationKey.EnvelopeKeyAcquisitionUrl, keyAcquisitionUri.ToString()}
                    };
        
                    IAssetDeliveryPolicy assetDeliveryPolicy =
                        _context.AssetDeliveryPolicies.Create(
                                    "AssetDeliveryPolicy",
                                    AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                                    AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                                    assetDeliveryPolicyConfiguration);
        
                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);
                    Console.WriteLine();
                    Console.WriteLine("Adding Asset Delivery Policy: " +
                        assetDeliveryPolicy.AssetDeliveryPolicyType);
                }
        
                static public string GetStreamingOriginLocator(IAsset asset)
                {
        
                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 
        
                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                EndsWith(".ism")).
                                                FirstOrDefault();
        
                    // Create a 30-day readonly access policy. 
                    // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
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
        
                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }
        
                static private byte[] GetRandomBuffer(int size)
                {
                    byte[] randomBytes = new byte[size];
                    using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(randomBytes);
                    }
        
                    return randomBytes;
                }
            }
        }


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
