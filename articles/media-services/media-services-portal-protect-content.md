<properties 
    pageTitle="Configuration des stratégies de protection du contenu en utilisant le portail Azure | Microsoft Azure" 
    description="Cet article explique comment utiliser le portail Azure pour configurer des stratégies de protection du contenu. L’article indique également comment activer le cryptage dynamique pour vos ressources." 
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

# <a name="configuring-content-protection-policies-using-the-azure-portal"></a>Configuration des stratégies de protection du contenu en utilisant le portail Azure

> [AZURE.NOTE] Pour terminer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="overview"></a>Vue d’ensemble

Microsoft Azure Media Services (AMS) vous permet de sécuriser vos médias à partir de la que laisse votre ordinateur par le biais de stockage, de traitement et de distribution. Media Services vous permet de remettre votre contenu crypté dynamiquement avec chiffrement AES (Advanced Standard) (à l’aide de clés de cryptage à 128 bits), chiffrement commune (CENC) à l’aide de PlayReady et/ou Widevine DRM et FairPlay d’Apple. 

AMS fournit un service de distribution de licences DRM et AES effacer les clés aux clients autorisés. Le portail Azure vous permet de créer une **clé/licence de stratégie d’autorisation** pour tous les types de cryptages.

Cet article explique comment configurer les stratégies de protection du contenu avec le portail Azure. L’article indique également comment appliquer le cryptage dynamique pour vos ressources.

> [AZURE.NOTE]  Si vous avez utilisé le portail classique Azure pour créer des stratégies de protection, les stratégies n’apparaisse pas dans le [portail Azure](https://portal.azure.com/). Toutefois, toutes les stratégies de l’ancienne existent toujours. Vous pouvez les examiner à l’aide d’Azure Media Services .NET SDK ou l’outil [Azure-Support-Services-Explorateur](https://github.com/Azure/Azure-Media-Services-Explorer/releases) (pour afficher les stratégies, cliquez du bouton droit sur l’actif -> affichage d’informations (F4) -> cliquez sur l’onglet contenu clés -> cliquez sur la clé). 
> 
> Si vous souhaitez crypter votre actif à l’aide de nouvelles stratégies, les configurer avec le portail Azure, cliquez sur Enregistrer et appliquer de nouveau cryptage dynamique. 

## <a name="start-configuring-content-protection"></a>Démarrer la configuration de la protection de contenu

Pour utiliser le portail pour démarrer la configuration de la protection de contenu, global à votre compte AMS, effectuez les opérations suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Sélectionnez **paramètres** > **protection du contenu**.

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection001.png)
 

## <a name="keylicense-authorization-policy"></a>Stratégie d’autorisation de clé/licence

AMS prend en charge plusieurs méthodes d’authentification des utilisateurs qui effectuent des demandes de licence ou de clé. La stratégie d’autorisation de clés contenu doit être configurée par vous et remplie par votre client dans l’ordre de la clé/licence pour être delived au client. La stratégie d’autorisation de clés contenu peut avoir une ou plusieurs restrictions d’autorisation : restriction **d’Ouvrir** ou de **jeton** .

Le portail Azure vous permet de créer une **clé/licence de stratégie d’autorisation** pour tous les types de cryptages.

###<a name="open"></a>Ouvrir 

Ouvrir restriction signifie que le système doit apporter la clé à toute personne qui fait une demande de clé. Cette restriction peut être utile à des fins de test. 

### <a name="token"></a>Jeton

La stratégie de jeton restreint doit être accompagnée d’un jeton émis par un Service (jeton de sécurité). Media Services prend en charge les jetons dans les formats de jetons de Web Simple (SWT) et JSON Web jeton (JWT). Media Services ne fournit pas de Services de jeton sécurisée. Vous pouvez créer un STS personnalisé ou exploiter Microsoft Azure ACS aux jetons de problème. Le SJS doit être configuré pour créer un jeton de signature avec les revendications spécifiées de clé et le problème que vous avez spécifié dans la configuration du jeton de restriction. Les services de livraison clé Media renvoie la clé demandée (ou licence) pour le client si le jeton est valide et les revendications dans la jeton correspondance ceux configurés pour la clé (ou licence).

Lors de la configuration du jeton restreint la stratégie, vous devez spécifier la clé de vérification principal, émetteur et les paramètres de l’audience. La clé primaire de vérification contient le jeton a été signé avec la clé, l’émetteur est le service de jeton de sécurité qui émet le jeton. L’audience (parfois appelée étendue) décrit l’objectif du jeton ou la ressource autorise l’accès au jeton. Les services de livraison clé Media valide que ces valeurs dans le jeton correspondent aux valeurs dans le modèle.

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-rights-template"></a>Modèle de droits PlayReady

Pour obtenir des informations détaillées sur le modèle de droits PlayReady, consultez [Vue d’ensemble du modèle de licence PlayReady Media Services](media-services-playready-license-template-overview.md).

### <a name="non-persistent"></a>Non persistant

Si vous configurez la licence comme non persistants, il est seulement conservée en mémoire pendant que le lecteur est à l’aide de la licence.  

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Permanente

Si vous configurez la licence comme permanente, il est enregistré dans un stockage persistant sur le client.

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-rights-template"></a>Modèle de droits Widevine

Pour obtenir des informations détaillées sur le modèle de droits Widevine, consultez [Vue d’ensemble des modèles de licence Widevine](media-services-widevine-license-template-overview.md).

### <a name="basic"></a>Base

Lorsque vous sélectionnez de **base**, le modèle sera créé avec toutes les valeurs par défaut.

### <a name="advanced"></a>Avancé

Pour obtenir une explication détaillée sur option avance de Widevine configurations, consultez [cette](media-services-widevine-license-template-overview.md) rubrique.

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Configuration de FairPlay

Pour activer le cryptage de FairPlay, vous devez fournir le certificat de l’application et la clé de Secret d’Application (demander) par le biais de l’option de Configuration de FairPlay. Pour plus d’informations sur la configuration de FairPlay et de la configuration requise, consultez [cet](media-services-protect-hls-with-fairplay.md) article.

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Appliquer le cryptage dynamique pour vos ressources

Pour tirer parti de chiffrement dynamiques, vous devez effectuer les opérations suivantes :

- Coder votre fichier source dans un ensemble de fichiers MP4 de vitesse de transmission adaptative.
- Obtenir au moins une unité de transmission en continu à la demande pour le point de terminaison en continu à partir de laquelle vous souhaitez livrer votre contenu. Pour plus d’informations, consultez [comment mettre à l’échelle d’unités réservées de diffusion en continu à la demande](media-services-portal-manage-streaming-endpoints.md).

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Sélectionnez une immobilisation que vous souhaitez crypter

Pour voir tous vos actifs, sélectionnez **paramètres** > **actifs**.

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Crypter avec AES ou DRM

Une fois que vous appuyez sur **crypter** sur une immobilisation, vous avez deux choix : **AES** ou **DRM**. 

#### <a name="aes"></a>AES

Effacer le chiffrement à clé est activé sur tous les protocoles de diffusion en continu de AES : diffusion en continu lisse, TLS et MPEG-tiret.

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM

Lorsque vous sélectionnez l’onglet DRM, vous sont présentées avec des stratégies de protection du contenu de différentes manières (que vous devez configurer à présent) + un ensemble de protocoles de diffusion en continu.

- **PlayReady et Widevine avec MPEG-tiret** - chiffre dynamiquement votre flux MPEG-tiret PlayReady et Widevine DRMs.
- **PlayReady et Widevine avec MPEG-tiret + FairPlay avec TLS** - dynamiquement chiffre vous flux MPEG-tiret PlayReady et Widevine DRMs. Vont également crypter votre flux TLS avec FairPlay.
- **PlayReady uniquement avec la diffusion en continu lisse, TLS et MPEG-tiret** - dynamiquement chiffrera Smooth Streaming, TLS, flux MPEG-tiret avec PlayReady DRM.
- **Widevine uniquement avec MPEG-tiret** - chiffre dynamiquement vous MPEG-tiret avec Widevine DRM.
- **FairPlay uniquement avec TLS** - chiffre dynamiquement votre flux TLS avec FairPlay.

Pour activer le cryptage de FairPlay, vous devez fournir le certificat de l’application et la clé de Secret d’Application (demander) par le biais de l’option de Configuration de FairPlay de la lame de paramètres de Protection du contenu.

![Protéger le contenu](./media/media-services-portal-content-protection/media-services-content-protection009.png)

Une fois que vous apportez à la sélection de cryptage, cliquez sur **Appliquer**.

##<a name="next-steps"></a>Étapes suivantes

Passez en revue Media Services, voies de formation.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





