<properties 
    pageTitle="Publier du contenu Azure Media Services à l’aide de .NET" 
    description="Découvrez comment créer un localisateur qui est utilisé pour générer une URL de transmission en continu. Exemples de code sont écrits en C# et utilisent le Kit de développement de Services de support pour .NET." 
    authors="juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/30/2016"
    ms.author="juliako"/>


# <a name="publish-azure-media-services-content-using-net"></a>Publier du contenu Azure Media Services à l’aide de .NET
 
> [AZURE.SELECTOR]
- [RESTE](media-services-rest-deliver-streaming-content.md)
- [.NET](media-services-deliver-streaming-content.md)
- [Portail](media-services-portal-publish.md)

##<a name="overview"></a>Vue d’ensemble

Vous pouvez diffuser une vitesse de transmission adaptive MP4 définie par la création d’un localisateur de diffusion en continu à la demande et la création d’une URL de transmission en continu. La rubrique [codage une immobilisation](media-services-encode-asset.md) montre comment encoder dans une vitesse de transmission adaptive que MP4 définie. 

>[AZURE.NOTE]Si votre contenu est crypté, configurer stratégie de remise d’actifs (comme décrit dans [cette](media-services-dotnet-configure-asset-delivery-policy.md) rubrique) avant de créer un localisateur. 

Vous pouvez également utiliser une localisateur de diffusion en continu à la demande pour créer des URL qui pointent vers les fichiers MP4 qui peuvent être téléchargés progressivement.  

Cette rubrique montre comment créer une localisateur pour publier vos ressources et de générer une lisse, MPEG tiret et TLS URL de diffusion en continu de la diffusion en continu à la demande. Il indique également à chaud pour créer des URL de téléchargement progressif. 
     
##<a name="create-an-ondemand-streaming-locator"></a>Créer une localisateur de diffusion en continu à la demande

Pour créer la repère de diffusion en continu à la demande et d’obtenir des URL, vous devez effectuer les opérations suivantes :

   1. Si le contenu est crypté, définir une stratégie d’accès.
   2. Créer une localisateur de diffusion en continu à la demande.
   3. Si vous souhaitez diffuser, obtenir le fichier manifest en continu (.ism) dans l’immobilisation. 
        
    Si vous souhaitez télécharger progressivement, obtenir les noms des fichiers MP4 dans l’immobilisation.  
   4. Créer des URL pour le fichier manifeste ou fichiers MP4. 
   

###<a name="use-media-services-net-sdk"></a>Utiliser les Services de support .NET SDK 

Créer des URL en continu 

    private static void BuildStreamingURLs(IAsset asset)
    {
    
        // Create a 30-day readonly access policy. 
        // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);
    
        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));
    
        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();
    
        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();
        
        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }

Les sorties de code :
    
    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)
    

>[AZURE.NOTE]Vous pouvez aussi diffuser votre contenu sur une connexion SSL. Pour ce faire, assurez-vous que votre URL en continu commencent par HTTPS. 

Créer des URL de téléchargement progressif 

    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);
    
        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));
    
        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();
    
        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));
                
        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }

Les sorties de code :
    
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4
    
    . . . 

###<a name="use-media-services-net-sdk-extensions"></a>Utiliser des Extensions de Media Services .NET SDK

Le code suivant appelle les méthodes d’extensions .NET SDK créer un localisateur et génèrent la diffusion en continu lisse, TLS MPEG-tiret URL et la diffusion adaptative en continu.

    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));
    
    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();
    
    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Voir aussi

[Télécharger des ressources](media-services-deliver-asset-download.md)
[stratégie de livraison de configuration actif](media-services-dotnet-configure-asset-delivery-policy.md)
