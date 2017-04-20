<properties
    pageTitle=" Échelle transmission en continu de points de terminaison avec le portail Azure | Microsoft Azure"
    description="Ce didacticiel vous guide à travers les étapes de mise à l’échelle des points de terminaison en continu avec le portail Azure."
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


# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Mettre à l’échelle des points de terminaison en continu avec le portail Azure

##<a name="overview"></a>Vue d’ensemble

> [AZURE.NOTE] Pour terminer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/). 

Lorsque vous travaillez avec Azure Media Services est un des scénarios plus courants est offrant des performances vidéo via adaptive vitesse de transmission en continu à vos clients. Media Services prend en charge la vitesse de transmission adaptive suivant les technologies de diffusion en continu : diffusion en continu Live HTTP (TLS), diffusion en continu lisse, MPEG tiret et HDS (pour Adobe Enforcer/accès titulaires de licence uniquement).

Media Services fournit l’emballage dynamique qui vous permet de remettre votre vitesse de transmission adaptive contenu MP4 encodé en continu des formats pris en charge par les Services de support (MPEG tiret, TLS, diffusion en continu lisse, HDS) juste-à-temps, sans avoir à stocker des versions intégrées de chacun de ces formats de transmission en continu.

Pour tirer parti de l’emballage dynamique, vous devez effectuer les opérations suivantes :

- Coder votre fichier mezzanine (source) dans un ensemble de fichiers MP4 de débit adaptatif (les étapes de codage sont expliqués plus loin dans ce didacticiel).  
- Créer au moins une unité de transmission en continu pour le *point de terminaison de la diffusion en continu* à partir de laquelle vous envisagez de livraison votre contenu. Les étapes ci-dessous montrent comment modifier le nombre d’unités de transmission en continu.

Avec emballage dynamique vous suffit de stocker et de payer les fichiers au format de stockage unique et Media Services pour la génération et fournir la réponse appropriée en fonction de la demande du client.

En outre, vous pouvez contrôler la capacité du service de diffusion en continu le point de terminaison pour gérer les besoins croissants de la bande passante en réglant les unités de transmission en continu. Il est recommandé d’allouer une ou plusieurs unités d’échelle pour les applications dans un environnement de production. Unités de transmission en continu fournissent des capacités dédiés de sortie qui peuvent être achetées par incréments de 200 Mbits/s et des fonctionnalités supplémentaires qui inclut les fonctionnalités qui : [emballage dynamique](media-services-dynamic-packaging-overview.md), l’intégration du CDN et configuration avancée. Pour plus d’informations, consultez [Gestion des points de terminaison en continu avec le portail Azure](media-services-portal-manage-streaming-endpoints.md).

## <a name="scale-streaming-endpoints"></a>Mettre à l’échelle des points de terminaison en continu

Pour créer et modifier le nombre d’unités réservées de diffusion en continu, effectuez les opérations suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Dans la fenêtre **paramètres** , sélectionnez les **points de terminaison de diffusion en continu**.
3. Cliquez sur le point de terminaison en continu que vous souhaitez mettre à l’échelle. 
4. Déplacez le curseur pour spécifier le nombre d’unités de transmission en continu
 
![Point de terminaison de la diffusion en continu](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

Les considérations suivantes s’appliquent :

- L’allocation de toutes nouvelles unités de transmission en continu peut prendre 20 minutes environ. 
- Actuellement, l’intention à partir de n’importe quelle valeur positive de diffusion en continu des unités à aucun, peut désactiver à la demande en continu jusqu'à une heure.
- Le plus grand nombre d’unités spécifiées pour la période de 24 heures est utilisé dans le calcul du coût. Pour plus d’informations sur les détails de la tarification, voir les [Détails de la tarification de Services média](http://go.microsoft.com/fwlink/?LinkId=275107).

##<a name="next-steps"></a>Étapes suivantes

Passez en revue Media Services, voies de formation.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


