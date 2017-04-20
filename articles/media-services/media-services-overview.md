<properties 
    pageTitle="Vue d’ensemble de Media Services Azure et les scénarios courants | Microsoft Azure" 
    description="Cette rubrique donne une vue d’ensemble de Azure Media Services" 
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
    ms.topic="hero-article" 
    ms.date="10/12/2016"
    ms.author="juliako;anilmur"/>

#<a name="azure-media-services-overview-and-common-scenarios"></a>Vue d’ensemble de Media Services Azure et les scénarios courants

Microsoft Azure Media Services est une plate-forme extensible basée sur le cloud qui permet aux développeurs de créer des applications de gestion et la diffusion de médias évolutifs. Media Services est basé sur les autres API qui vous permettent en toute sécurité télécharger, stocker, coder et compresser le contenu audio ou vidéo pour à la fois à la demande et en direct de diffusion en continu à des clients différents (par exemple, TV, PC et périphériques mobiles).

Vous pouvez créer des workflows de bout en bout à l’aide de Media Services entièrement. Vous pouvez également choisir d’utiliser des composants tiers pour certaines parties de votre flux de travail. Par exemple, encoder à l’aide d’un codeur de tiers. Ensuite, télécharger, protéger, package, fournir à l’aide des Services de support.

Vous pouvez choisir de flux de votre contenu live ou fournir un contenu à la demande. Cette rubrique présente des scénarios courants pour distribuer votre contenu [live](media-services-overview.md#live_scenarios) ou [à la demande](media-services-overview.md#vod_scenarios). La rubrique renvoie également à d’autres rubriques pertinentes.

## <a name="sdks-and-tools"></a>Kits de développement logiciel et les outils

Pour créer des solutions pour les Services de support, vous pouvez utiliser :

- [Media Services API REST](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- L’un des kits de développement logiciel client disponibles :
- [Azure Media Services SDK pour .NET](https://github.com/Azure/azure-sdk-for-media-services),
- [Azure SDK pour Java](https://github.com/Azure/azure-sdk-for-java),
- [Kit de développement PHP azure](https://github.com/Azure/azure-sdk-for-php),
- [Node.js Azure Media Services](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Il s’agit d’une version non Microsoft d’un kit de développement logiciel Node.js. Il est géré par une Communauté et n’a pas une couverture de 100 % des APIs AMS).
- Outils existants :
- [Azure portal](https://portal.azure.com/)
- [Azure-Support-Services-Explorateur](https://github.com/Azure/Azure-Media-Services-Explorer) (Explorateur de Services azure Media (AMSE) est un Winforms / application C# pour Windows)

##<a name="media-services-learning-paths"></a>Cursus Media Services

Vous pouvez afficher AMS cursus ici :

- [AMS Live en continu des flux de travail](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS à la demande de la diffusion en continu des flux de travail](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##<a name="prerequisites"></a>Conditions préalables

Pour démarrer les services Azure Media, vous devez avoir les éléments suivants :

3. Un compte Azure. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com).
2. Un compte Azure Media Services. Utilisez le portail Azure, .NET ou API REST pour créer compte Azure Media Services. Pour plus d’informations, consultez [Créer un compte](media-services-portal-create-account.md).
3. (Facultatif) Permet de paramétrer l’environnement de développement. Choisissez .NET ou l’API REST de votre environnement de développement. Pour plus d’informations, consultez [configuration d’environnement](media-services-dotnet-how-to-use.md).

En outre, comment se connecter par programme se [connecter](media-services-dotnet-connect-programmatically.md).
4. (Recommandé) Allouer une ou plusieurs unités de l’échelle. Il est recommandé d’allouer une ou plusieurs unités d’échelle pour les applications dans un environnement de production.   Pour plus d’informations, consultez [Gestion des points de terminaison de diffusion en continu](media-services-portal-manage-streaming-endpoints.md).

##<a name="concepts-and-overview"></a>Vue d’ensemble et concepts

Concepts d’Azure Media Services, consultez [Concepts](media-services-concepts.md).

Pour une série de procédures qui vous présente les principaux composants d’Azure Media Services, consultez les [didacticiels pas à pas des Services Azure Media](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series). Cette série a une excellente présentation des concepts et utilise l’outil AMSE pour illustrer les tâches de l’AMS. Notez que AMSE est un outil de Windows. Cet outil prend en charge la plupart des tâches que vous pouvez obtenir par programme avec [Azure SDK de PHP](https://github.com/Azure/azure-sdk-for-php), [Azure SDK pour Java](https://github.com/Azure/azure-sdk-for-java)ou [.NET SDK AMS](https://github.com/Azure/azure-sdk-for-media-services).

##<a id="vod_scenarios"></a>Livraison de Media à la demande avec Azure Media Services : scénarios courants et des tâches

Cette section décrit des scénarios courants et fournit des liens vers les rubriques correspondantes. Le diagramme suivant illustre les principales parties de la plate-forme Media Services impliqués dans la livraison de contenu à la demande. 

![Workflow de la demande (VOD)](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)


###<a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Protection des contenus dans le stockage et les médias en flux continu en clair (non crypté)

1. Télécharger un fichier de la mezzanine de haute qualité dans un actif.
    
    Il est recommandé d’appliquer l’option de cryptage du stockage à vos ressources afin de protéger votre contenu lors du téléchargement et tout en reste dans le stockage.
 
1. Coder à un ensemble de fichiers de la vitesse de transmission adaptive MP4. 

    Il est recommandé d’appliquer l’option de cryptage du stockage à l’actif de sortie afin de protéger votre contenu au repos.
    
1. Configurer la stratégie de remise d’actifs (utilisée par emballage dynamique). 
    
    Si votre actif est le stockage chiffré, vous **devez** configurer la stratégie de remise d’actifs. 

1. Publier l’actif en créant un localisateur à la demande.

    Assurez-vous d’avoir au moins une unité réservée sur le point de terminaison en continu à partir de laquelle vous souhaitez transmettre du contenu de diffusion en continu.

1. Flux de contenu publié.

###<a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Protéger le contenu dans le stockage, crypté de manière dynamique des médias en flux continu à livrer  

Pour être en mesure d’utiliser le cryptage dynamique, vous devez d’abord obtenir au moins une unité réservée en continu sur le point de terminaison en continu à partir de laquelle vous souhaitez diffuser le contenu chiffré.

1. Télécharger un fichier de la mezzanine de haute qualité dans un actif. Appliquer l’option de cryptage du stockage à l’actif.
1. Coder à un ensemble de fichiers de la vitesse de transmission adaptive MP4. Appliquer l’option de cryptage du stockage à l’actif de la sortie.
1. Créer la clé de cryptage de contenu pour l’actif que vous souhaitez crypter dynamiquement lors de la lecture.
2. Configurer la stratégie d’autorisation de clés contenu.
1. Configurer la stratégie de remise d’actifs (utilisée par emballage dynamique et le cryptage dynamique).
1. Publier l’actif en créant un localisateur à la demande.
1. Flux de contenu publié. 

###<a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Support Analytique permet de dériver des informations pratiques de vos vidéos 

Support Analytique est un ensemble de composants de la reconnaissance vocale et de la vision qui le rendent plus facile pour les organisations et aux entreprises de tirer des leçons exploitables à partir de leurs fichiers vidéo. Pour plus d’informations, consultez [Vue d’ensemble du Analytique Azure Media Services](media-services-analytics-overview.md).

1. Télécharger un fichier de la mezzanine de haute qualité dans un actif.
2. Un des services suivants Media Analytique permet de traiter vos vidéos :
    
    - **Indexeur** – [vidéos de processus avec Azure Media indexeur 2](media-services-process-content-with-indexer2.md)
    - **Hyperlapse** – [fichiers Hyperlapse Media avec Azure Media Hyperlapse](media-services-hyperlapse-content.md)
    - **Détection de mouvement** : [Détection de mouvement pour Azure Media Analytique](media-services-motion-detection.md).
    - **Détection de visage et d’émotions de Face** – [détection d’émotion pour Azure Media Analytique et Face](media-services-face-and-emotion-detection.md).
    - **Synthèse de la vidéo** : [Miniatures de vidéo Utilisez Azure Media pour créer une synthèse de la vidéo](media-services-video-summarization.md)
3. Des processeurs multimédia Media Analytique produisent les fichiers MP4 ou JSON. Si un processeur de support produit un fichier MP4, vous pouvez progressivement télécharger le fichier. Si un processeur de support produit un fichier JSON, vous pouvez télécharger le fichier du stockage blob Azure. 


###<a name="deliver-progressive-download"></a>Proposer un téléchargement progressif 

1. Télécharger un fichier de la mezzanine de haute qualité dans un actif.
1. Coder dans un seul fichier MP4.
1. Publier l’actif en créant un localisateur à la demande ou des associations de sécurité.

    Si vous utilisez le localisateur d’à la demande, assurez-vous d’avoir au moins une unité réservée sur le point de terminaison en continu à partir de laquelle vous souhaitez télécharger progressivement le contenu de diffusion en continu.

    Si vous utilisez le localisateur SAS, le contenu est téléchargé depuis le stockage blob Azure. Dans ce cas, vous n’avez pas besoin d’avoir réservé des unités de transmission en continu.
  
1. Télécharger progressivement le contenu.

##<a id="live_scenarios"></a>Remettre des événements de diffusion en continu Live avec Azure Media Services

Lorsque vous travaillez avec Live en continu les composants suivants sont généralement impliqués :

- Un appareil photo qui permet de diffuser un événement.
- Un codeur vidéo live qui convertit les signaux de la caméra dans des flux qui sont envoyés à un service de diffusion en continu live.

Le cas échéant, plusieurs codeurs direct lors de la synchronisation. Les événements qu’une disponibilité très élevée à la demande et la qualité de l’expérience, il est recommandé à employer actif des codeurs redondants avec temps de synchronizationto obtenir un basculement transparent sans perte de données en direct de certaines critiques.
- Service de diffusion en continu live qui vous permet d’effectuer les opérations suivantes :

- acquisition de contenu en direct à l’aide de différents protocoles de diffusion en continu live (par exemple RTMP ou diffusion en continu lisse),
- (le cas échéant) coder votre flux dans flux de transmission adaptive
- afficher un aperçu de votre flux en direct,
- Enregistrer et stocker le contenu ingéré pour être transmises à une version ultérieure (vidéo à la demande)
- fournir le contenu par l’intermédiaire des protocoles de diffusion courants (par exemple, MPEG tiret, lisse, TLS, HDS) directement à vos clients, ou à un réseau de livraison de contenu (CDN) distribution se poursuive.


**Microsoft Azure Media Services** (AMS) offre la possibilité d’acquisition, à coder, afficher, stocker et livrer votre contenu de diffusion en continu live.

Lors de la remise de votre contenu aux clients votre objectif est de fournir une vidéo de haute qualité à divers périphériques dans des conditions de réseau différents. Pour prendre en charge des conditions de qualité et réseau, utiliser codeurs live pour encoder votre flux de flux de données vidéo multi-vitesse de transmission (débit adaptive).  Pour prendre en charge des flux de données sur différents périphériques, permet de Media Services [packaging dynamique](media-services-dynamic-packaging-overview.md) pour dynamiquement nouveau compresser votre flux de différents protocoles. Media Services prend en charge la remise de la vitesse de transmission adaptive suivant les technologies de diffusion en continu : diffusion en continu Live HTTP (TLS), diffusion en continu lisse, MPEG tiret et HDS (pour Adobe Enforcer/accès titulaires de licence uniquement).

Dans Azure Media Services, les **canaux**, les **programmes**et les **StreamingEndpoints** gèrent toutes les fonctionnalités en continu live, y compris d’acquisition, de mise en forme, DVR, de sécurité, d’évolutivité et de redondance.

Un **canal** représente un pipeline de traitement de contenu en continu en direct. Un canal peut recevoir un live d’entrée de flux de données dans l’une des manières suivantes :

- Sur les sites live envoie de codeur multi-bitrate **RTMP** ou **Diffusion en continu lisse** (MP4 fragmentés) pour le canal est configuré pour la remise des **pass-through** . La livraison **directe** est lorsque les flux ingérés traversent le **canal**s sans traitement supplémentaire. Vous pouvez utiliser les encodeurs direct suivants que la diffusion en continu lisse de multi-débit de sortie : élémentaire, Envivio, Cisco.  Les encodeurs direct suivants de sortie RTMP : transcodeurs Adobe Flash Live Telestream Wirecast et Tricaster.  Un codeur live peut également envoyer un flux de vitesse de transmission unique à un canal qui n’est pas activé pour l ' encodage live, mais qui n’est pas recommandée. Lors d’une demande, les Services de support offre un flux.

>[AZURE.NOTE] À l’aide d’une méthode directe est le moyen le plus économique de live en continu lorsque vous effectuez plusieurs événements sur une longue période de temps, et que vous avez déjà investi dans les codeurs sur place. Consultez les détails de [tarification](/pricing/details/media-services/) .

- Un encodeur live local envoie un flux unique à vitesse de transmission à la chaîne qui est activée pour effectuer l ' encodage live avec les Services de support dans un des formats suivants : RTP (MPEG-TS), RTMP ou diffusion en continu lisse (MP4 fragmenté). Le canal a effectue ensuite l ' encodage live d’entrants seule vitesse de transmission de flux de données à un flux vidéo multi-vitesse de transmission (adaptative). Lors d’une demande, les Services de support offre un flux.


###<a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Utilisation de canaux qui reçoivent multi-vitesse de transmission de flux de codeurs de locaux (pass-through)

Le diagramme suivant illustre les principales parties de la plate-forme AMS qui sont impliqués dans le processus de **transfert** .

![Flux de travail direct][live-overview2]

Pour plus d’informations, consultez [fonctionne avec des couches multiples de cette réception-vitesse de transmission des flux direct de codeurs de local](media-services-live-streaming-with-onprem-encoders.md).

###<a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Utilisation des chaînes qui sont activés pour effectuer l ' encodage live avec Azure Media Services

Le diagramme suivant illustre les principales parties de la plate-forme AMS impliqués dans les flux de production Live en continu où un canal est activé pour effectuer l ' encodage live avec Media Services.

![Flux de travail direct][live-overview1]

Pour plus d’informations, consultez [utilisation des canaux qui sont activés pour effectuer l ' encodage Live avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).


##<a name="consuming-content"></a>Beaucoup de contenu

Azure Media Services fournit les outils nécessaires pour créer des applications de lecteur client riches et dynamiques pour la plupart des plates-formes, y compris : e/s de périphériques, des appareils Android, Windows, Windows Phone, Xbox et décodeurs boîtes. La rubrique suivante fournit des liens vers les kits de développement logiciel et les infrastructures de lecteur que vous pouvez utiliser pour développer vos propres applications clientes qui peuvent utiliser des médias en flux continu à partir des Services de support.

[Développement d’Applications de lecteur vidéo](media-services-develop-video-players.md)

##<a name="enabling-azure-cdn"></a>L’activation d’Azure CDN

Media Services prend en charge l’intégration avec Azure CDN. Pour plus d’informations sur l’activation d’Azure CDN, voir [comment gérer les points de terminaison de diffusion en continu dans un compte de Services de support](media-services-portal-manage-streaming-endpoints.md).

##<a name="scaling-a-media-services-account"></a>Mise à l’échelle d’un compte de Services de support

Vous pouvez mettre à l’échelle **Media Services** en spécifiant le nombre **d’Unités réservé de diffusion en continu** et d' **Unités réservées de codage** que vous souhaitez voir votre compte pour être mis en service avec.

Vous pouvez également mettre à l’échelle votre compte de Services de support en lui ajoutant des comptes de stockage. Chaque compte de stockage est limité à 500 To. Pour développer votre système de stockage hors des limites par défaut, vous pouvez choisir de joindre plusieurs comptes de stockage à un seul compte de Media Services.

[Cette](media-services-portal-scale-streaming-endpoints.md) rubrique des liens vers des rubriques pertinentes.

##<a name="support"></a>Prise en charge

[Azure](https://azure.microsoft.com/support/options/) offre des options de support pour Azure, y compris les Services de support.

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="service-level-agreement-sla"></a>Contrat de niveau de service (SLA)

- Pour le codage des Services de support, nous garantissons la disponibilité de 99,9 % des transactions de l’API REST.
- Pour la diffusion en continu, nous traitera correctement les demandes avec une garantie de disponibilité de 99,9 % pour un contenu de média existant lors de l’achat d’au moins une unité de diffusion en continu.
- Pour les canaux Live, nous garantissons exécutant des canaux que connectivité externe au moins 99,9 % du temps.
- Pour la Protection du contenu, nous garantissons que nous correctement répond à vos demandes de clé au moins 99,9 % du temps.
- Pour un indexeur, nous traitera correctement les demandes de tâche d’indexeur traitées avec un codage de réservé unité 99,9 % du temps.

Pour plus d’informations, consultez [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/).

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 
