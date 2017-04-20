<properties 
    pageTitle="Configuration des stratégies de réception d’immobilisation à l’aide des API de Media Services reste | Microsoft Azure" 
    description="Cette rubrique indique comment configurer les stratégies de livraison différents actifs à l’aide des API de Media Services REST." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016"  
    ms.author="juliako"/>

#<a name="configuring-asset-delivery-policies"></a>Configuration des stratégies de remise d’actifs

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Si vous projetez de diffuser des ressources dynamiquement cryptés, l’une des étapes du workflow de distribution de contenu Media Services est configuration des stratégies de livraison pour les immobilisations. La stratégie de livraison actif indique aux Services de support comment vous souhaitez pour votre capital à livrer : dans les flux de protocole doit vos ressources dynamiquement conditionné (par exemple, MPEG tiret, TLS, diffusion en continu lisse ou tous), vous souhaitez dynamiquement crypter votre actif ou non et comment (cryptage commun ou enveloppe).

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
- Si vous avez une immobilisation avec un localisateur de diffusion existant, Impossible de lier une nouvelle stratégie à l’actif, supprimez le lien d’une stratégie existante de l’actif ou mise à jour d’une stratégie de livraison associés liée la ressource.  Vous devez d’abord supprimer le localisateur de transmission en continu, ajuster les stratégies, puis recréez le localisateur de transmission en continu.  Vous pouvez utiliser le même locatorId lorsque vous recréez le localisateur de transmission en continu, mais vous devez vous assurer que n’engendre pas les problèmes pour les clients étant donné que le contenu peut être mis en cache par l’origine ou à un réseau CDN en aval.

>[AZURE.NOTE] Lorsque vous travaillez avec l’API REST de Services multimédias, les considérations suivantes s’appliquent :
>
>Lorsque vous accédez à des entités dans les Services de support, vous devez définir les valeurs et les champs d’en-tête spécifiques dans vos requêtes HTTP. Pour plus d’informations, consultez [le programme d’installation pour le développement d’API Media Services reste](media-services-rest-how-to-use.md).

>Après vous être connecté avec succès à la https://media.windows.net, vous recevrez un réacheminement 301 spécifiant un autre URI de Services multimédia. Vous devez faire d’autres appels à l’URI de nouveau comme décrit dans la [connexion à des Services de support à l’aide des API REST](media-services-rest-connect-programmatically.md).


##<a name="clear-asset-delivery-policy"></a>Stratégie de livraison clair actif

###<a id="create_asset_delivery_policy"></a>Créer la stratégie de remise d’actifs
La requête HTTP suivante crée une stratégie de remise d’actifs qui spécifie de ne pas appliquer le cryptage dynamique et fournir le flux dans un des protocoles suivants : protocoles MPEG tiret, TLS et diffusion en continu lisse. 

Pour plus d’informations sur les valeurs que vous pouvez spécifier lors de la création d’un AssetDeliveryPolicy, reportez-vous à la section [Types utilisés lors de la définition de AssetDeliveryPolicy](#types) .   


Demande de :
      
    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net
    
    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

Réponse :
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT
    
    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}
    
###<a id="link_asset_with_asset_delivery_policy"></a>Immobilisation de lien avec la stratégie de livraison des actifs

La requête HTTP suivante lie la ressource spécifiée à la stratégie de livraison des actifs pour.

Demande de :

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-3344-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net
    
    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Réponse :

    HTTP/1.1 204 No Content


##<a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Stratégie de livraison de DynamicEnvelopeEncryption actif 

###<a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Créer la clé de contenu du type EnvelopeEncryption et le lier à l’actif

Lorsque vous spécifiez la stratégie de livraison de DynamicEnvelopeEncryption, vous devez vous assurer que lier vos ressources à une clé de contenu du type EnvelopeEncryption. Pour plus d’informations, reportez-vous à la section : [Création d’une clé de contenu](media-services-rest-create-contentkey.md)).


###<a id="get_delivery_url"></a>Obtenir l’URL de la remise

Obtenir l’URL de la remise pour le mode de livraison spécifié de la clé de contenu créé à l’étape précédente. Un client utilise l’URL retournée pour demander une clé AES ou un PlayReady de licences dans l’ordre de lecture du contenu protégé.

Spécifiez le type de l’URL pour obtenir dans le corps de la demande HTTP. Si vous protégez votre contenu avec PlayReady, demander une URL d’acquisition de licence PlayReady des Services de support, à l’aide de 1 pour le keyDeliveryType : {« keyDeliveryType » : 1}. Si vous protégez votre contenu avec le cryptage de l’enveloppe, demander une URL d’acquisition de clé en spécifiant 2 pour keyDeliveryType : {« keyDeliveryType » : 2}.

Demande de :
    
    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423452029&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=IEXV06e3drSIN5naFRBdhJZCbfEqQbFZsGSIGmawhEo%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21
    
    {"keyDeliveryType":2}

Réponse :
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


###<a name="create-asset-delivery-policy"></a>Créer la stratégie de remise d’actifs

La requête HTTP suivante crée **AssetDeliveryPolicy** qui est configuré pour appliquer le cryptage enveloppe dynamique (**DynamicEnvelopeEncryption**) pour le protocole **TLS** (dans cet exemple, d’autres protocoles ne pourront pas en continu). 


Pour plus d’informations sur les valeurs que vous pouvez spécifier lors de la création d’un AssetDeliveryPolicy, reportez-vous à la section [Types utilisés lors de la définition de AssetDeliveryPolicy](#types) .   

Demande de :

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}

    
Réponse :
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


###<a name="link-asset-with-asset-delivery-policy"></a>Immobilisation de lien avec la stratégie de livraison des actifs

Voir [actifs de lien avec la stratégie de livraison des actifs](#link_asset_with_asset_delivery_policy)

##<a name="dynamiccommonencryption-asset-delivery-policy"></a>Stratégie de livraison de DynamicCommonEncryption actif 

###<a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Créer la clé de contenu du type CommonEncryption et le lier à l’actif

Lorsque vous spécifiez la stratégie de livraison de DynamicCommonEncryption, vous devez vous assurer que lier vos ressources à une clé de contenu du type CommonEncryption. Pour plus d’informations, reportez-vous à la section : [Création d’une clé de contenu](media-services-rest-create-contentkey.md)).


###<a name="get-delivery-url"></a>Obtenir l’URL de la remise

Obtenir l’URL de la remise pour le mode de livraison de PlayReady de la clé de contenu créé à l’étape précédente. Un client utilise l’URL retournée pour demander une licence PlayReady dans l’ordre de lecture du contenu protégé. Pour plus d’informations, consultez [Obtenir l’URL de remise](#get_delivery_url).

###<a name="create-asset-delivery-policy"></a>Créer la stratégie de remise d’actifs

La requête HTTP suivante crée **AssetDeliveryPolicy** qui est configuré pour appliquer le cryptage dynamique commun (**DynamicCommonEncryption**) pour le protocole de **Diffusion en continu lisse** (dans cet exemple, d’autres protocoles ne pourront pas en continu). 

Pour plus d’informations sur les valeurs que vous pouvez spécifier lors de la création d’un AssetDeliveryPolicy, reportez-vous à la section [Types utilisés lors de la définition de AssetDeliveryPolicy](#types) .   


Demande de :

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Si vous souhaitez protéger votre contenu à l’aide de Widevine DRM, mettre à jour les valeurs de AssetDeliveryConfiguration pour utiliser WidevineLicenseAcquisitionUrl (qui a la valeur 7) et spécifiez l’URL d’un service de livraison de licence. Vous pouvez utiliser les partenaires AMS suivants pour vous aider à obtenir les licences de Widevine : [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

Par exemple : 
 
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

>[AZURE.NOTE]Lors du chiffrement avec Widevine, vous serait uniquement en mesure de livrer à l’aide de tirets. Veillez à spécifier le tiret (2) dans le protocole de livraison des actifs.
  
###<a name="link-asset-with-asset-delivery-policy"></a>Immobilisation de lien avec la stratégie de livraison des actifs

Voir [actifs de lien avec la stratégie de livraison des actifs](#link_asset_with_asset_delivery_policy)


##<a id="types"></a>Types utilisés lors de la définition de AssetDeliveryPolicy

###<a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol 

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

###<a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

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

###<a name="contentkeydeliverytype"></a>ContentKeyDeliveryType


    /// <summary>
    /// Delivery method of the content key to the client.
    ///
    </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }


###<a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

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
