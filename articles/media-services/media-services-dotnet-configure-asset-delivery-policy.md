<properties 
    pageTitle="Configurer des stratégies de remise d’actifs avec le Kit de développement .NET | Microsoft Azure" 
    description="Cette rubrique indique comment configurer les stratégies de réception d’immobilisation différente avec Azure Media Services .NET SDK." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako;mingfeiy"/>

#<a name="configure-asset-delivery-policies-with-net-sdk"></a>Configurer des stratégies de remise d’actifs avec le Kit de développement .NET
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

##<a name="overview"></a>Vue d’ensemble

Si vous prévoyez d’actifs de livraison chiffré, l’une des étapes du workflow de distribution de contenu Media Services est configuration des stratégies de livraison pour les immobilisations. La stratégie de livraison actif indique aux Services de support comment vous souhaitez pour votre capital à livrer : dans les flux de protocole doit vos ressources dynamiquement conditionné (par exemple, MPEG tiret, TLS, diffusion en continu lisse ou tous), vous souhaitez dynamiquement crypter votre actif ou non et comment (cryptage commun ou enveloppe).

Cette rubrique explique pourquoi et comment créer et configurer des stratégies de livraison des actifs.

>[AZURE.NOTE]Pour être en mesure d’utiliser l’emballage dynamique et le cryptage dynamique, il se peut que vous devez vous assurer d’avoir au moins une unité d’échelle (également connu sous le nom de diffusion en continu d’unité). Pour plus d’informations, consultez [Comment faire évoluer un Service de support](media-services-portal-manage-streaming-endpoints.md).
>
>En outre, votre actif doit contenir un ensemble de fichiers de diffusion en continu lisse de débit adaptatif ou de vitesse de transmission adaptive MP4s.

Vous pouvez appliquer des stratégies différentes au même actif. Par exemple, vous pouvez appliquer le chiffrement PlayReady pour le cryptage AES enveloppe et de diffusion en continu lisse MPEG tiret et TLS. Tous les protocoles qui ne sont pas définis dans une stratégie de remise (par exemple, vous ajoutez une stratégie unique qui spécifie uniquement TLS comme protocole) ne pourront pas en continu. L’exception à cette règle est que si vous n’avez aucune stratégie de livraison actif défini à tout. Ensuite, tous les protocoles peuvent être utilisés en clair.

Si vous souhaitez fournir une gestion du stockage crypté, vous devez configurer la stratégie de livraison de l’actif. Avant votre actif peut être diffusé en continu, le serveur de diffusion supprime le cryptage de stockage et flux de votre contenu à l’aide de la stratégie de livraison spécifiée. Par exemple, pour remettre vos ressources cryptées avec la clé de chiffrement Advanced Encryption Standard (AES) enveloppe, définissez le type de stratégie à **DynamicEnvelopeEncryption**. Pour supprimer le cryptage de stockage et flux de l’actif en clair, définissez le type de stratégie à **NoDynamicEncryption**. Exemples qui montrent comment configurer ces types de stratégie suivent.

En fonction de la configuration de la stratégie de livraison des actifs vous pourrez dynamiquement package dynamiquement crypter et transmettre les protocoles de diffusion en continu suivants : flux de diffusion en continu lisse, TLS, MPEG tiret et HDS.

La liste suivante affiche les formats que vous utilisez pour diffuser en continu lisse, TLS, tiret et HDS.

Diffusion en continu lisse :

{en flux continu du point de terminaison nom-media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

TLS :

{en flux continu du point de terminaison nom-media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

TIRET DE MPEG

{en flux continu du point de terminaison nom-media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

HDS

{en flux continu du point de terminaison nom-media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

Pour obtenir des instructions sur la façon de publier un actif et de générer une URL de transmission en continu, consultez [créer une URL de transmission en continu](media-services-deliver-streaming-content.md).

##<a name="considerations"></a>Considérations relatives à la

- Vous ne pouvez pas supprimer un AssetDeliveryPolicy associés lié une ressource alors qu’une recherche à la demande (en continu) existe pour cet actif. Il est recommandé de supprimer la stratégie de l’actif avant de supprimer la stratégie.
- Impossible de créer un localisateur de diffusion en continu sur un élément chiffré de stockage lorsque aucune stratégie de livraison des actifs est définie.  Si l’immobilisation n’est pas le stockage crypté, le système vous permettra de créer un localisateur et flux de l’actif en clair sans stratégie de livraison de l’actif.
- Vous pouvez avoir plusieurs stratégies de livraison actif associés à un actif unique, mais vous ne pouvez spécifier qu’un seul moyen de gérer un AssetDeliveryProtocol donné.  C'est-à-dire que si vous essayez de lier deux stratégies de livraison qui spécifient le protocole AssetDeliveryProtocol.SmoothStreaming qui provoque une erreur car le système ne sait pas que celui que vous voulez qu’elle s’applique lorsqu’un client effectue une demande de diffusion en continu lisse.
- Si vous avez une immobilisation avec un localisateur de diffusion existant, vous ne pouvez pas lier une nouvelle stratégie à l’actif (vous pouvez supprimer le lien d’une stratégie existante de l’actif, ou mettre à jour une stratégie de livraison associés liée la ressource).  Vous devez d’abord supprimer le localisateur de transmission en continu, ajuster les stratégies, puis recréez le localisateur de transmission en continu.  Vous pouvez utiliser le même locatorId lorsque vous recréez le localisateur de transmission en continu, mais vous devez vous assurer que n’engendre pas les problèmes pour les clients étant donné que le contenu peut être mis en cache par l’origine ou à un réseau CDN en aval.


##<a name="clear-asset-delivery-policy"></a>Stratégie de livraison clair actif

La méthode **ConfigureClearAssetDeliveryPolicy** suivante spécifie de ne pas appliquer le cryptage dynamique et fournir le flux dans un des protocoles suivants : protocoles MPEG tiret, TLS et diffusion en continu lisse. Vous pouvez souhaiter appliquer cette stratégie à vos ressources de stockage crypté.

Pour plus d’informations sur les valeurs que vous pouvez spécifier lors de la création d’un AssetDeliveryPolicy, reportez-vous à la section [Types utilisés lors de la définition de AssetDeliveryPolicy](#types) .

static public void ConfigureClearAssetDeliveryPolicy (IAsset actif) {IAssetDeliveryPolicy stratégie = _context. AssetDeliveryPolicies.Create ("Stratégie claire", AssetDeliveryPolicyType.NoDynamicEncryption, AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null) ;

actif. DeliveryPolicies.Add(policy) ; }

##<a name="dynamiccommonencryption-asset-delivery-policy"></a>Stratégie de livraison de DynamicCommonEncryption actif


La méthode **CreateAssetDeliveryPolicy** suivante crée **AssetDeliveryPolicy** qui est configuré pour appliquer le chiffrement commun dynamique (**DynamicCommonEncryption**) à un protocole de diffusion en continu lisse (autres protocoles ne pourront pas en continu). La méthode accepte deux paramètres : **actif** (l’actif auquel vous souhaitez appliquer la stratégie de remise) et **IContentKey** (la clé de contenu du type **CommonEncryption** , pour plus d’informations, reportez-vous à la section : [Création d’une clé de contenu](media-services-dotnet-create-contentkey.md#common_contentkey)).

Pour plus d’informations sur les valeurs que vous pouvez spécifier lors de la création d’un AssetDeliveryPolicy, reportez-vous à la section [Types utilisés lors de la définition de AssetDeliveryPolicy](#types) .


CreateAssetDeliveryPolicy public static void (IAsset actif, clé de IContentKey) {Uri acquisitionUrl = clé. GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense) ;

AssetDeliveryPolicyConfiguration de Dictionary < AssetDeliveryPolicyConfigurationKey, string > = nouveau dictionnaire < AssetDeliveryPolicyConfigurationKey, string > {{AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()}} ;

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.SmoothStreaming,
            assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
    }

Azure Media Services vous permet également d’ajouter un cryptage Widevine. L’exemple suivant illustre à la fois PlayReady et Widevine à la stratégie de livraison actif.


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
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }

>[AZURE.NOTE]Lors du chiffrement avec Widevine, vous serait uniquement en mesure de livrer à l’aide de tirets. Veillez à spécifier un tiret dans le protocole de livraison des actifs.


##<a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Stratégie de livraison de DynamicEnvelopeEncryption actif 

La méthode **CreateAssetDeliveryPolicy** suivante crée **AssetDeliveryPolicy** qui est configuré pour appliquer le cryptage enveloppe dynamique (**DynamicEnvelopeEncryption**) pour les protocoles de diffusion en continu lisse, TLS et tiret (si vous décidez de ne pas spécifier certains protocoles, ils ne pourront pas en continu). La méthode accepte deux paramètres : **actif** (l’actif auquel vous souhaitez appliquer la stratégie de remise) et **IContentKey** (la clé de contenu du type **EnvelopeEncryption** , pour plus d’informations, reportez-vous à la section : [Création d’une clé de contenu](media-services-dotnet-create-contentkey.md#envelope_contentkey)).


Pour plus d’informations sur les valeurs que vous pouvez spécifier lors de la création d’un AssetDeliveryPolicy, reportez-vous à la section [Types utilisés lors de la définition de AssetDeliveryPolicy](#types) .   

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        
        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
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
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


##<a id="types"></a>Types utilisés lors de la définition de AssetDeliveryPolicy

###<a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

###<a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }

###<a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


