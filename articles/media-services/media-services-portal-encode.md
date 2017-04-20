<properties
    pageTitle="Coder une immobilisation à l’aide de Media Encoder Standard avec le portail Azure | Microsoft Azure"
    description="Ce didacticiel vous guide à travers les étapes de codage d’un actif à l’aide de Media Encoder Standard avec le portail Azure."
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


# <a name="encode-an-asset-using-media-encoder-standard-with-the-azure-portal"></a>Coder une immobilisation à l’aide de Media Encoder Standard avec le portail Azure

> [AZURE.NOTE] Pour terminer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/). 

Lorsque vous travaillez avec Azure Media Services est un des scénarios plus courants remet adaptive vitesse de transmission en continu à vos clients. Media Services prend en charge la vitesse de transmission adaptive suivant les technologies de diffusion en continu : diffusion en continu Live HTTP (TLS), diffusion en continu lisse, MPEG tiret et HDS (pour Adobe Enforcer/accès titulaires de licence uniquement). Pour préparer vos vidéos pour la diffusion en continu de la vitesse de transmission adaptative, vous devez encoder votre vidéo source dans des fichiers multi-bitrate. Vous devez utiliser l’encodeur **Standard de Media Encoder** pour encoder votre vidéo.  

Media Services fournit également l’emballage dynamique qui vous permet de remettre votre MP4s multi-bitrate dans les formats de diffusion en continu suivants : MPEG tiret, TLS, diffusion en continu lisse ou HDS, sans avoir à ré-package dans ces formats de transmission en continu. Avec emballage dynamique vous suffit de stocker et de payer les fichiers au format de stockage unique et Media Services pour la génération et fournir la réponse appropriée en fonction de la demande du client.

Pour tirer parti de l’emballage dynamique, vous devez effectuer les opérations suivantes :

- Coder votre fichier source dans un ensemble de fichiers MP4 de multi-bitrate (les étapes de codage sont expliqués plus loin dans cette section).
- Obtenir au moins une unité de transmission en continu pour le point de terminaison en continu à partir de laquelle vous envisagez de livraison votre contenu. Pour plus d’informations, consultez [configuration de points de terminaison en continu](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

Pour faire évoluer le traitement de media, consultez la rubrique [suivante](media-services-portal-scale-media-processing.md) .

## <a name="encode-with-the-azure-portal"></a>Coder avec le portail Azure

Cette section décrit les étapes à que suivre pour coder votre contenu avec Media Encoder Standard.

1.  Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2.  Dans la fenêtre **paramètres** , sélectionnez **ressources**.  
2.  Dans la fenêtre **d’actifs** , sélectionnez l’élément que vous voulez encoder.
3.  Appuyez sur le bouton **Encoder** .
4.  Dans la fenêtre de **coder un actif** , sélectionnez le processeur « Media Encoder Standard » et un paramètre prédéfini. Par exemple, si vous connaissez votre vidéo d’entrée a une résolution de 1920 x 1080 pixels, vous pouvez utiliser le « H264 débit plusieurs 1080p » prédéfini. Pour plus d’informations sur les présélections, voir [cet](https://msdn.microsoft.com/library/azure/mt269960.aspx) article – il est important de sélectionner le paramètre prédéfini qui convient le mieux pour votre vidéo d’entrée. Si vous avez une vidéo de basse résolution (640 x 360), puis vous ne devez pas utiliser la valeur par défaut « H264 débit plusieurs 1080p » prédéfini.
    
    Pour faciliter la gestion, vous pouvez modifier le nom de l’élément de sortie et le nom de la tâche.
        
    ![Coder des actifs](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Appuyez sur **créer**.


##<a name="next-step"></a>Étape suivante

Vous pouvez surveiller la progression du projet codage avec le portail Azure, comme décrit dans [cet](media-services-portal-check-job-progress.md) article.  

##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


