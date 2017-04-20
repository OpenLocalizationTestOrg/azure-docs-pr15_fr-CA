<properties 
    pageTitle="Vue d’ensemble du contenu de protection | Microsoft Azure" 
    description="Cet article donne une vue d’ensemble de la protection du contenu avec Media Services." 
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

#<a name="protecting-content-overview"></a>Vue d’ensemble du contenu de la protection


Microsoft Azure Media Services vous permet de sécuriser vos médias à partir de la que laisse votre ordinateur par le biais de stockage, de traitement et de distribution. Media Services vous permet de remettre votre contenu en direct et sur demande crypté dynamiquement avec la norme AES (Advanced Encryption) (à l’aide de clés de cryptage à 128 bits) ou des DRMs principales : Microsoft PlayReady, Google Widevine et FairPlay d’Apple. Media Services fournit également un service de distribution de clés AES et DRM (PlayReady, Widevine et FairPlay) des licences aux clients autorisés. 

L’image suivante montre les flux de travail de protection de contenu qui prend en charge de l’AMS. 

![Protéger avec PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

>[AZURE.NOTE]Pour être en mesure d’utiliser le cryptage dynamique, vous devez d’abord obtenir au moins une unité réservée en continu sur le point de terminaison en continu à partir de laquelle vous souhaitez diffuser le contenu chiffré.

Cette rubrique explique les [concepts et la terminologie](media-services-content-protection-overview.md) pertinente pour la présentation de la protection de contenu avec l’AMS. La rubrique contient également [des liens](media-services-content-protection-overview.md#common-scenarios) vers les rubriques qui montrent comment effectuer les tâches de protection du contenu. 

##<a name="dynamic-encryption"></a>Cryptage dynamique

Microsoft Azure Media Services vous permet de fournir le contenu crypté dynamiquement avec clé d’effacement chiffrement AES ou DRM : Microsoft PlayReady, Google Widevine et FairPlay d’Apple.

Actuellement, vous pouvez crypter les formats de diffusion en continu suivants : TLS, MPEG tiret et diffusion en continu lisse. Vous ne pouvez pas crypter HDS format de flux, ou des téléchargements progressifs.

Si vous souhaitez que pour les Services de support crypter une immobilisation, vous devez associer une clé de cryptage (CommonEncryption ou EnvelopeEncryption) à votre actif et également de configurer les stratégies d’autorisation pour la clé.

Vous devez également configurer la stratégie de livraison de l’actif. Si vous souhaitez diffuser la gestion du stockage crypté, veillez à spécifier comment vous souhaitez livrer par la configuration de la stratégie de remise d’actifs.

Lorsqu’un flux est demandé par un lecteur, Media Services utilise la clé spécifiée pour dynamiquement chiffrer votre contenu à l’aide de la clé d’effacement de AES ou le cryptage de DRM. Pour déchiffrer le flux de données, le lecteur demande la clé à partir du service de distribution de clés. Pour décider ou non de l’utilisateur est autorisé à obtenir la clé, le service évalue les stratégies d’autorisation que vous avez spécifiée pour la clé.

>[AZURE.NOTE]Pour tirer parti de chiffrement dynamiques, vous devez d’abord obtenir au moins une unité de transmission en continu à la demande pour le point de terminaison en continu à partir de laquelle vous envisagez de livraison votre contenu crypté. Pour plus d’informations, consultez [comment les Services de support d’échelle](media-services-portal-manage-streaming-endpoints.md).

##<a name="storage-encryption"></a>Cryptage du stockage

Cryptage du stockage permet de chiffrer votre contenu clair localement en utilisant un cryptage AES 256 bits et ensuite de le télécharger vers le stockage Azure où il est stocké est crypté au repos. Les ressources protégées par un cryptage de stockage sont automatiquement et placés dans un système de fichiers cryptés avant le codage et non éventuellement cryptés avant de le télécharger comme une nouvelle immobilisation de sortie. Cas d’usage principal pour le cryptage du stockage est lorsque vous souhaitez sécuriser vos fichiers de média d’entrée de haute qualité avec le cryptage fort au repos sur le disque.

Afin d’offrir une gestion du stockage crypté, vous devez configurer la stratégie de livraison de l’actif afin que les Services de support sache comment vous souhaitez livrer votre contenu. Avant votre actif peut être diffusé en continu, le serveur de diffusion supprime le cryptage de stockage et flux de votre contenu à l’aide de la stratégie de remise spécifié (par exemple, AES, cryptage commun ou pas de cryptage).

## <a name="common-encryption-cenc"></a>Cryptage commun (CENC)

Cryptage commun est utilisé pour chiffrer votre contenu avec PlayReady ou / et Widewine.

## <a name="using-cbcs-aapl-encryption"></a>À l’aide de la liste des CBC-aapl cryptage

Liste des CBC-aapl est utilisée pour chiffrer votre contenu avec FairPlay.

## <a name="envelope-encryption"></a>Cryptage d’enveloppe 

Utilisez cette option si vous souhaitez protéger votre contenu avec touche AES-128. Si vous souhaitez une option plus sûre, choisissez parmi les DRMs répertoriées dans cette rubrique. 

##<a name="licenses-and-keys-delivery-service"></a>Les licences et les clés de service de livraison

Media Services fournit un service de distribution des licences DRM (PlayReady, Widevine, FairPlay) et AES effacer les clés aux clients autorisés. Pour configurer des stratégies d’authentification et d’autorisation pour les licences et les clés, vous pouvez utiliser [le portail Azure](media-services-portal-protect-content.md), API REST ou Media Services SDK pour .NET.

##<a name="token-restriction"></a>Restriction de jeton

La stratégie d’autorisation de clés contenu peut avoir une ou plusieurs restrictions d’autorisation : ouvrir ou jeton de restriction. La stratégie de jeton restreint doit être accompagnée d’un jeton émis par un Service (jeton de sécurité). Media Services prend en charge les jetons dans les formats de jetons de Web Simple (SWT) et JSON Web jeton (JWT). Media Services ne fournit pas de Services de jeton sécurisée. Vous pouvez créer un STS personnalisé ou exploiter Microsoft Azure ACS aux jetons de problème. Le SJS doit être configuré pour créer un jeton de signature avec les revendications spécifiées de clé et le problème que vous avez spécifié dans la configuration du jeton de restriction. Les services de livraison clé Media renvoie la clé demandée (ou licence) pour le client si le jeton est valide et les revendications dans la jeton correspondance ceux configurés pour la clé (ou licence).

Lors de la configuration du jeton restreint la stratégie, vous devez spécifier la clé de vérification principal, un émetteur et un paramètres d’audience. La clé primaire de vérification contient le jeton a été signé avec la clé, l’émetteur est le service de jeton de sécurité qui émet le jeton. L’audience (parfois appelée étendue) décrit l’objectif du jeton ou la ressource autorise l’accès au jeton. Les services de livraison clé Media valide que ces valeurs dans le jeton correspondent aux valeurs dans le modèle.

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

##<a name="common-scenarios"></a>Scénarios courants

Les rubriques suivantes montrent comment protéger le contenu dans le stockage, remettre crypté de manière dynamique des médias en flux continu, utilisez le service de remise de clé/licence AMS

- [Protéger avec AES](media-services-protect-with-aes128.md) 
- [Protéger à l’aide de PlayReady et/ou Widevine](media-services-protect-with-drm.md)
- [Diffuser votre contenu TLS Protected Apple FairPlay et/ou PlayReady](media-services-protect-hls-with-fairplay.md)

### <a name="additional-scenarios"></a>Scénarios supplémentaires

- [Comment intégrer le service de licences de PlayReady Azure avec votre propre serveur chiffreur/de diffusion en continu](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server).
- [L’utilisation de castLabs pour remettre les licences DRM pour Azure Media Services](media-services-castlabs-integration.md)
 
##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>Liens connexes

[Annonce de PlayReady sous la forme d’un service et un chiffrement AES, dynamique avec Azure Media Services](http://mingfeiy.com/playready)

[Tarification livraison licence Azure Media Services PlayReady expliqué](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Le débogage de flux de données chiffrée AES dans Azure Media Services](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[Authenitcation de jeton JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Intégrer Azure Media Services OWIN MVC en fonction de l’application avec Azure Active Directory et limiter la remise de clés contenu basé sur les revendications JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[ACS d’Azure utilisation de jetons de problème](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
