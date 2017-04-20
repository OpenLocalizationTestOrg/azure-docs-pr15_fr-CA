<properties 
    pageTitle="Comment faire pour effectuer une diffusion en continu live à l’aide d’Azure Media Services pour créer des flux de multi-bitrate avec .NET | Microsoft Azure" 
    description="Ce didacticiel vous guide tout au long des étapes de création d’un canal qui reçoit un flux en direct unique-bitrate et l’encode pour multi-vitesse de transmission de flux de données à l’aide du Kit de développement .NET." 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="10/12/2016"
    ms.author="juliako;anilmur"/>


#<a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-net"></a>Comment faire pour effectuer une diffusion en continu live à l’aide d’Azure Media Services pour créer des flux de multi-bitrate avec .NET

> [AZURE.SELECTOR]
- [Portail](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [API REST](https://msdn.microsoft.com/library/azure/dn783458.aspx)

>[AZURE.NOTE]
> Pour terminer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, reportez-vous à la section [D’essai Azure](/pricing/free-trial/?WT.mc_id=A261C142F).

##<a name="overview"></a>Vue d’ensemble

Ce didacticiel vous guide tout au long des étapes de création d’un **canal** qui reçoit un flux en direct unique-bitrate et l’encode pour multi-vitesse de transmission de flux de données.

Pour plus d’informations conceptuelles relatives aux canaux qui sont activés pour l ' encodage live, consultez [Live en continu à l’aide d’Azure Media Services pour créer des flux de données multi-bitrate](media-services-manage-live-encoder-enabled-channels.md).


##<a name="common-live-streaming-scenario"></a>Scénario classique de diffusion en continu Live

Les étapes suivantes décrivent les tâches impliquées dans la création d’applications courantes de diffusion en continu live.

>[AZURE.NOTE] Actuellement, la durée maximale recommandée pour un événement en direct est de 8 heures. Si vous avez besoin exécuter un canal pour de longues périodes, contactez amslived sur le site Microsoft.com.

1. Connectez une caméra vidéo à un ordinateur. Lancer et configurer un encodeur live local qui peut afficher un flux de vitesse de transmission unique dans un des protocoles suivants : RTMP, diffusion en continu lisse ou RTP (MPEG-TS). Pour plus d’informations, consultez [prise en charge du protocole RTMP Azure Media Services et les codeurs de Live](http://go.microsoft.com/fwlink/?LinkId=532824).

Cette étape peut également être effectuée après avoir créé votre canal.

1. Créer et démarrer un canal.

1. Récupérer la chaîne URL d’acquisition.

L’URL d’acquisition est utilisé par l’encodeur live pour envoyer le flux au canal.

1. Extraire l’URL de prévisualisation de canal.

Cette URL permet de vérifier que votre canal reçoit correctement le flux en direct.

2. Créez une immobilisation.
3. Si vous souhaitez de l’actif à être chiffré dynamiquement lors de la lecture, effectuez le des opérations suivantes :
1. Créer une clé de contenu.
1. Configurer la stratégie d’autorisation de la clé de contenu.
1. Configurer la stratégie de remise d’actifs (utilisée par emballage dynamique et le cryptage dynamique).
3. Créer un programme et spécifiez pour l’utilisation de la ressource que vous avez créé.
1. Publier l’immobilisation associée au programme par la création d’un localisateur à la demande.

Assurez-vous d’avoir au moins une unité réservée sur le point de terminaison en continu à partir de laquelle vous souhaitez transmettre du contenu de diffusion en continu.

1. Démarrez le programme lorsque vous êtes prêt à commencer la diffusion et l’archivage.
2. Le cas échéant, l’encodeur live peut être signalé pour lancer une publication. La publicité est insérée dans le flux de sortie.
1. Arrêter le programme chaque fois que vous souhaitez arrêter la diffusion et l’archivage de l’événement.
1. Supprimer le programme (et éventuellement supprimer l’actif).

## <a name="what-youll-learn"></a>Vous allez apprendre

Cette rubrique vous indique comment exécuter les différentes opérations sur les canaux et les programmes à l’aide de Media Services .NET SDK. Étant donné que de nombreuses opérations sont longues API .NET qui gèrent les longues opérations sont utilisées.

La rubrique indique comment effectuer les opérations suivantes :

1. Créer et démarrer un canal. Durée d’exécution longue API est utilisés.
1. Obtenir les canaux d’acquisition (d’entrée) du point de terminaison. Ce point de terminaison doit être fournie à l’encodeur qui peut envoyer un flux direct de vitesse de transmission unique.
1. Obtenir le point de terminaison d’aperçu. Ce point de terminaison est utilisé pour afficher un aperçu de votre flux de données.
1. Créer un actif qui sera utilisé pour stocker votre contenu. Les stratégies de livraison actif doivent être configurés ainsi, comme illustré dans cet exemple.
1. Créer un programme et spécifiez pour l’utilisation de la ressource qui a été créée précédemment. Démarrez le programme. Durée d’exécution longue API est utilisés.
1. Créer un localisateur pour l’actif, afin que le contenu est publié et peut être diffusé en continu à vos clients.
1. Afficher et masquer des tablettes tactiles. Démarrer et arrêter les publicités. Durée d’exécution longue API est utilisés.
1. Nettoyer votre canal et toutes les ressources associées.


##<a name="prerequisites"></a>Conditions préalables

La configuration suivante est requise pour ce didacticiel.

- Pour terminer ce didacticiel, vous avez besoin d’un compte Azure.

Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Vous obtenez des crédits qui peuvent être utilisés pour tester des services Azure payés. Même après que les crédits sont épuisés, vous pouvez conserver le compte et utiliser des services Azure gratuits et des fonctionnalités, telles que la fonctionnalité d’applications Web dans le Service d’application Azure.
- Un compte de Services de support. Pour créer un compte de Services de support, consultez [Créer un compte](media-services-portal-create-account.md).
- Visual Studio 2010 SP1 (Professionnel, Premium, Ultimate ou Express) ou une version ultérieure.
- Vous devez utiliser Media Services .NET SDK version 3.2.0.0 ou ultérieure.
- Une webcam et un encodeur qui peut envoyer un flux direct de vitesse de transmission unique.

##<a name="considerations"></a>Considérations relatives à la

- Actuellement, la durée maximale recommandée pour un événement en direct est de 8 heures. Si vous avez besoin exécuter un canal pour de longues périodes, contactez amslived sur le site Microsoft.com.
- Assurez-vous d’avoir au moins une unité réservée sur le point de terminaison en continu à partir de laquelle vous souhaitez transmettre du contenu de diffusion en continu.

##<a name="download-sample"></a>Télécharger l’exemple

Obtenir et exécuter un exemple à partir [d’ici](https://azure.microsoft.com/documentation/samples/media-services-dotnet-encode-live-stream-with-ams-clear/).


##<a name="set-up-for-development-with-media-services-sdk-for-net"></a>Configurer pour le développement avec Media Services SDK pour .NET

1. Créez une application console à l’aide de Visual Studio.
1. Ajoutez le Kit de développement de Services de support pour .NET dans votre application console à l’aide de package NuGet de Services multimédia.

##<a name="connect-to-media-services"></a>Se connecter à des Services multimédias
Pour obtenir les meilleurs résultats, vous devez utiliser un fichier app.config pour stocker la clé de compte et de nom de Media Services.

>[AZURE.NOTE]Pour rechercher le nom et la clé des valeurs, accédez au portail Azure et sélectionnez votre compte. La fenêtre Paramètres s’affiche sur la droite. Dans la fenêtre Paramètres, sélectionnez clés. En cliquant sur l’icône en regard de chaque zone de texte de copie la valeur dans le Presse-papiers système.

Ajoutez la section appSettings du fichier app.config et définir les valeurs de votre clé de compte et de nom de compte Media Services.


    <?xml version="1.0"?>
    <configuration>
      <appSettings>
          <add key="MediaServicesAccountName" value="YouMediaServicesAccountName" />
          <add key="MediaServicesAccountKey" value="YouMediaServicesAccountKey" />
      </appSettings>
    </configuration>
     
    
##<a name="code-example"></a>Exemple de code

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Net;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
    
    namespace EncodeLiveStreamWithAmsClear
    {
        class Program
        {
            private const string ChannelName = "channel001";
            private const string AssetlName = "asset001";
            private const string ProgramlName = "program001";
    
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
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
    
                IChannel channel = CreateAndStartChannel();
    
                // The channel's input endpoint:
                string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();
    
                Console.WriteLine("Intest URL: {0}", ingestUrl);
    
    
                // Use the previewEndpoint to preview and verify 
                // that the input from the encoder is actually reaching the Channel. 
                string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();
    
                Console.WriteLine("Preview URL: {0}", previewEndpoint);
    
                // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
                // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
                // The thumbnail is exposed via the same end-point as the Channel Preview URL.
                string thumbnailUri = new UriBuilder
                {
                    Scheme = Uri.UriSchemeHttps,
                    Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
                    Path = "thumbnails/input.jpg"
                }.Uri.ToString();
    
                Console.WriteLine("Thumbain URL: {0}", thumbnailUri);
    
                // Once you previewed your stream and verified that it is flowing into your Channel, 
                // you can create an event by creating an Asset, Program, and Streaming Locator. 
                IAsset asset = CreateAndConfigureAsset();
    
                IProgram program = CreateAndStartProgram(channel, asset);
    
                ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);
    
                // You can use slates and ads only if the channel type is Standard.  
                StartStopAdsSlates(channel);
    
                // Once you are done streaming, clean up your resources.
                Cleanup(channel);
    
            }
    
            public static IChannel CreateAndStartChannel()
            {
                var channelInput = CreateChannelInput();
                var channePreview = CreateChannelPreview();
                var channelEncoding = CreateChannelEncoding();
    
    
                ChannelCreationOptions options = new ChannelCreationOptions
                {
                    EncodingType = ChannelEncodingType.Standard,
                    Name = ChannelName,
                    Input = channelInput,
                    Preview = channePreview,
                    Encoding = channelEncoding
                };
    
                Log("Creating channel");
                IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
                string channelId = TrackOperation(channelCreateOperation, "Channel create");
    
                IChannel channel = _context.Channels.Where(c => c.Id == channelId).FirstOrDefault();
    
                Log("Starting channel");
                var channelStartOperation = channel.SendStartOperation();
                TrackOperation(channelStartOperation, "Channel start");
    
                return channel;
            }
    
            /// <summary>
            /// Create channel input, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelInput CreateChannelInput()
            {
                return new ChannelInput
                {
                    StreamingProtocol = StreamingProtocol.RTPMPEG2TS,
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelInput001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }
    
            /// <summary>
            /// Create channel preview, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelPreview CreateChannelPreview()
            {
                return new ChannelPreview
                {
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelPreview001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }
    
            /// <summary>
            /// Create channel encoding, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelEncoding CreateChannelEncoding()
            {
                return new ChannelEncoding
                {
                    SystemPreset = "Default720p",
                    IgnoreCea708ClosedCaptions = false,
                    AdMarkerSource = AdMarkerSource.Api,
                    // You can only set audio if streaming protocol is set to StreamingProtocol.RTPMPEG2TS.
                    AudioStreams = new List<AudioStream> { new AudioStream { Index = 103, Language = "eng" } }.AsReadOnly()
                };
            }
    
            /// <summary>
            /// Create an asset and configure asset delivery policies.
            /// </summary>
            /// <returns></returns>
            public static IAsset CreateAndConfigureAsset()
            {
                IAsset asset = _context.Assets.Create(AssetlName, AssetCreationOptions.None);
    
                IAssetDeliveryPolicy policy =
                    _context.AssetDeliveryPolicies.Create("Clear Policy",
                    AssetDeliveryPolicyType.NoDynamicEncryption,
                    AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
    
                asset.DeliveryPolicies.Add(policy);
    
                return asset;
            }
    
            /// <summary>
            /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
            /// however each Program must have a unique name within your Media Services account.
            /// </summary>
            /// <param name="channel"></param>
            /// <param name="asset"></param>
            /// <returns></returns>
            public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
            {
                IProgram program = channel.Programs.Create(ProgramlName, TimeSpan.FromHours(3), asset.Id);
                Log("Program created", program.Id);
    
                Log("Starting program");
                var programStartOperation = program.SendStartOperation();
                TrackOperation(programStartOperation, "Program start");
    
                return program;
            }
    
            /// <summary>
            /// Create locators in order to be able to publish and stream the video.
            /// </summary>
            /// <param name="asset"></param>
            /// <param name="ArchiveWindowLength"></param>
            /// <returns></returns>
            public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
            {
                // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
                var locator = _context.Locators.CreateLocator
                    (
                        LocatorType.OnDemandOrigin,
                        asset,
                        _context.AccessPolicies.Create
                            (
                                "Live Stream Policy",
                                ArchiveWindowLength,
                                AccessPermissions.Read
                            )
                    );
    
                return locator;
            }
    
            /// <summary>
            /// Perform operations on slates.
            /// </summary>
            /// <param name="channel"></param>
            public static void StartStopAdsSlates(IChannel channel)
            {
                int cueId = new Random().Next(int.MaxValue);
                var path = Path.GetFullPath(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, @"..\\..\\SlateJPG\\DefaultAzurePortalSlate.jpg"));
    
                Log("Creating asset");
                var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
                Log("Slate asset created", slateAsset.Id);
    
                Log("Uploading file");
                var assetFile = slateAsset.AssetFiles.Create("DefaultAzurePortalSlate.jpg");
                assetFile.Upload(path);
                assetFile.IsPrimary = true;
                assetFile.Update();
    
                Log("Showing slate");
                var showSlateOpeartion = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
                TrackOperation(showSlateOpeartion, "Show slate");
    
                Log("Hiding slate");
                var hideSlateOperation = channel.SendHideSlateOperation();
                TrackOperation(hideSlateOperation, "Hide slate");
    
                Log("Starting ad");
                var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), cueId, false);
                TrackOperation(startAdOperation, "Start ad");
    
                Log("Ending ad");
                var endAdOperation = channel.SendEndAdvertisementOperation(cueId);
                TrackOperation(endAdOperation, "End ad");
    
                Log("Deleting slate asset");
                slateAsset.Delete();
            }
    
            /// <summary>
            /// Clean up resources associated with the channel.
            /// </summary>
            /// <param name="channel"></param>
            public static void Cleanup(IChannel channel)
            {
                IAsset asset;
                if (channel != null)
                {
                    foreach (var program in channel.Programs)
                    {
                        asset = _context.Assets.Where(se => se.Id == program.AssetId)
                                                .FirstOrDefault();
    
                        Log("Stopping program");
                        var programStopOperation = program.SendStopOperation();
                        TrackOperation(programStopOperation, "Program stop");
    
                        program.Delete();
    
                        if (asset != null)
                        {
                            Log("Deleting locators");
                            foreach (var l in asset.Locators)
                                l.Delete();
    
                            Log("Deleting asset");
                            asset.Delete();
                        }
                    }
    
                    Log("Stopping channel");
                    var channelStopOperation = channel.SendStopOperation();
                    TrackOperation(channelStopOperation, "Channel stop");
    
                    Log("Deleting channel");
                    var channelDeleteOperation = channel.SendDeleteOperation();
                    TrackOperation(channelDeleteOperation, "Channel delete");
                }
            }
    
    
            /// <summary>
            /// Track long running operations.
            /// </summary>
            /// <param name="operation"></param>
            /// <param name="description"></param>
            /// <returns></returns>
            public static string TrackOperation(IOperation operation, string description)
            {
                string entityId = null;
                bool isCompleted = false;
    
                Log("starting to track ", null, operation.Id);
                while (isCompleted == false)
                {
                    operation = _context.Operations.GetOperation(operation.Id);
                    isCompleted = IsCompleted(operation, out entityId);
                    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
                }
                // If we got here, the operation succeeded.
                Log(description + " in completed", operation.TargetEntityId, operation.Id);
    
                return entityId;
            }
    
            /// <summary> 
            /// Checks if the operation has been completed. 
            /// If the operation succeeded, the created entity Id is returned in the out parameter.
            /// </summary> 
            /// <param name="operationId">The operation Id.</param> 
            /// <param name="channel">
            /// If the operation succeeded, 
            /// the entity Id associated with the sucessful operation is returned in the out parameter.</param>
            /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
            private static bool IsCompleted(IOperation operation, out string entityId)
            {
    
                bool completed = false;
    
                entityId = null;
    
                switch (operation.State)
                {
                    case OperationState.Failed:
                        // Handle the failure. 
                        // For example, throw an exception. 
                        // Use the following information in the exception: operationId, operation.ErrorMessage.
                        Log("operation failed", operation.TargetEntityId, operation.Id);
                        break;
                    case OperationState.Succeeded:
                        completed = true;
                        entityId = operation.TargetEntityId;
                        break;
                    case OperationState.InProgress:
                        completed = false;
                        Log("operation in progress", operation.TargetEntityId, operation.Id);
                        break;
                }
                return completed;
            }
    
    
            private static void Log(string action, string entityId = null, string operationId = null)
            {
                Console.WriteLine(
                    "{0,-21}{1,-51}{2,-51}{3,-51}",
                    DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
                    action,
                    entityId ?? string.Empty,
                    operationId ?? string.Empty);
            }
        }
    }   


##<a name="next-step"></a>Étape suivante

Passez en revue Media Services, voies de formation.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="looking-for-something-else"></a>Vous recherchez quelque chose d’autre ?

Si cette rubrique ne contenait pas ce que vous attendiez, il manque quelque chose, ou dans une autre façon n’a pas répondre à vos besoins, veuillez nous fournir vous commentaires à l’aide de la thread de Disqus ci-dessous.
