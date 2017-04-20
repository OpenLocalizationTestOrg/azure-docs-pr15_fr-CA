<properties 
    pageTitle="Configurer la stratégie de d’autorisation clé de contenu via le portail Azure | Microsoft Azure" 
    description="Découvrez comment configurer une stratégie d’autorisation pour une clé de contenu." 
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
    ms.topic="article" 
    ms.date="10/12/2016" 
    ms.author="juliako"/>



#<a name="configure-content-key-authorization-policy"></a>Configurer la stratégie d’autorisation de clés contenu
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


##<a name="overview"></a>Vue d’ensemble

Microsoft Azure Media Services vous permet de livrer des flux MPEG-tiret, diffusion en continu lisse et HTTP-Live-diffusion en continu (TLS) protégés par [DRM de Microsoft PlayReady](https://www.microsoft.com/playready/overview/)ou de la norme AES (Advanced Encryption) (à l’aide de clés de cryptage à 128 bits). AMS vous permet également de fournir des flux de tiret cryptés avec Widevine DRM. PlayReady et Widevine sont cryptés par la spécification de chiffrement commune (CENC 23001-7 de norme ISO/CEI).

Media Services fournit également un **Service de livraison/licence de clé** à partir de laquelle les clients peuvent obtenir des licences pour lire du contenu chiffré PlayReady/Widevine ou clés AES.

Cette rubrique montre comment utiliser le portail Azure pour configurer la stratégie d’autorisation de clés contenu. La clé de servir ensuite dynamiquement chiffrer votre contenu. Formats de note actuellement peut chiffrer la diffusion en continu suivants : TLS, MPEG tiret et diffusion en continu lisse. Vous ne pouvez pas crypter HDS format de flux, ou des téléchargements progressifs.

Lorsqu’un lecteur demande un flux de données qui est défini pour être cryptés de façon dynamique, Media Services utilise la clé configurée dynamiquement chiffrer votre contenu en utilisant le chiffrement AES ou DRM. Pour déchiffrer le flux de données, le lecteur demande la clé à partir du service de distribution de clés. Pour décider ou non de l’utilisateur est autorisé à obtenir la clé, le service évalue les stratégies d’autorisation que vous avez spécifiée pour la clé.


Si vous prévoyez d’avoir plusieurs clés de contenu ou spécifier une URL de **Service de remise de clé/licence** autres que les services de livraison clé Media, utilisez Media Services .NET SDK ou des API de reste.

[Configurer la stratégie de d’autorisation clé de contenu à l’aide de Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)

[Configurer la stratégie de d’autorisation clé de contenu à l’aide des API REST de Services multimédia](media-services-rest-configure-content-key-auth-policy.md)

###<a name="some-considerations-apply"></a>Certaines considérations s’appliquent :

- Pour être en mesure d’utiliser l’emballage dynamique et le cryptage dynamique, il se peut que vous devez vous assurer d’avoir au moins une unité réservée de diffusion en continu. Pour plus d’informations, consultez [Comment faire évoluer un Service de support](media-services-portal-manage-streaming-endpoints.md).
- Votre ressource doit contenir un ensemble de fichiers de diffusion en continu lisse de débit adaptatif ou de vitesse de transmission adaptive MP4s. Pour plus d’informations, reportez-vous à [Encoder un actif](media-services-encode-asset.md).
- Le service de remise de clé met en cache le ContentKeyAuthorizationPolicy et ses objets associés (options de la stratégie et les restrictions) pendant 15 minutes.  Si vous créez un ContentKeyAuthorizationPolicy et que vous spécifiez pour utiliser une restriction « Jeton », puis le tester et ensuite mettre à jour la stratégie de restriction « Ouvrir », il prendra environ 15 minutes avant que la stratégie bascule vers la version « Ouverte » de la stratégie.


##<a name="how-to-configure-the-key-authorization-policy"></a>Comment : configurer la stratégie d’autorisation clé

Pour configurer la stratégie d’autorisation clé, sélectionnez la page de **PROTECTION du contenu** .

Media Services prend en charge plusieurs méthodes d’authentification des utilisateurs qui effectuent des demandes de clés. La stratégie d’autorisation de clés contenu peut avoir à **Ouvrir**, **jeton**ou restrictions d’autorisation **IP** (**IP** peut être configuré avec les autres ou le Kit de développement .NET).

###<a name="open-restriction"></a>Restriction Ouvrir

La restriction **d’Ouvrir** signifie que le système doit apporter la clé à toute personne qui fait une demande de clé. Cette restriction peut être utile à des fins de test.

![OpenPolicy][open_policy]

###<a name="token-restriction"></a>Restriction de jeton

Pour choisir la stratégie de jeton restreint, appuyez sur le bouton de **jeton** .

La stratégie de **jeton** restreint doit être accompagnée d’un jeton émis par un **Service de jeton de sécuriser** (STS). Media Services prend en charge les jetons dans les formats de **Jetons de Web Simple** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) et **JSON Web jeton** (JWT). Pour plus d’informations, consultez [authentification de jeton JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Media Services ne fournit pas de **Services de jeton sécurisée**. Vous pouvez créer un STS personnalisé ou exploiter Microsoft Azure ACS aux jetons de problème. Le SJS doit être configuré pour créer un jeton de signature avec les revendications spécifiées de clé et le problème que vous avez spécifié dans la configuration du jeton de restriction. Les services de livraison clé Media renvoie la clé de chiffrement pour le client si le jeton est valide et que les revendications dans le jeton correspondent à ceux configurés pour la clé de contenu. Pour plus d’informations, voir [Utiliser les ACS de Azure aux jetons de problème](http://mingfeiy.com/acs-with-key-services).

Lors de la configuration du **jeton** restreint la stratégie, vous devez définir des valeurs pour la **clé de vérification**, **émetteur** et **public**. La clé primaire de vérification contient le jeton a été signé avec la clé, l’émetteur est le service de jeton de sécurité qui émet le jeton. L’audience (parfois appelée étendue) décrit l’objectif du jeton ou la ressource autorise l’accès au jeton. Les services de livraison clé Media valide que ces valeurs dans le jeton correspondent aux valeurs dans le modèle.

###<a name="playready"></a>PlayReady

Lorsque vous protégez votre contenu avec **PlayReady**, l’une des choses que vous devez spécifier dans votre stratégie d’autorisation est une chaîne XML qui définit le modèle de licence PlayReady. Par défaut, la stratégie suivante est définie :

<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
      <LicenseTemplates>
        <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
          <ContentKey i:type="ContentEncryptionKeyFromHeader" />
          <LicenseType>Nonpersistent</LicenseType>
          <PlayRight>
            <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
          </PlayRight>
        </PlayReadyLicenseTemplate>
      </LicenseTemplates>
    </PlayReadyLicenseResponseTemplate>

Vous pouvez cliquez sur le bouton **Importer la stratégie xml** et fournir un autre XML conforme au schéma XML défini [ici](https://msdn.microsoft.com/library/azure/dn783459.aspx).


##<a name="next-step"></a>Étape suivante

Passez en revue Media Services, voies de formation.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

