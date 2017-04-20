<properties 
    pageTitle="Questions fréquentes | Microsoft Azure" 
    description="Questions fréquemment posées (FAQ)" 
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
    ms.date="09/19/2016" 
    ms.author="juliako"/>


#<a name="frequently-asked-questions"></a>Forum aux questions

##<a name="general-ams-faqs"></a>Forum aux questions de la MGS général

Q : comment évoluer l’indexation ?

R : les unités réservées sont les mêmes pour les tâches de codage et de l’indexation. Suivez les instructions sur la [manière d’unités réservées de codage l’échelle](media-services-scale-media-processing-overview.md). **Notez** que la performance de l’indexeur n’est pas affecté par le Type d’unité réservé.

Q : vous avez téléchargé, codé et publié une vidéo. Quelle est la raison pour laquelle la vidéo n’est pas lu lorsque j’essaie de l’y ?

R : l’une des raisons plus courantes est que vous n’avez pas au moins une unité de transmission en continu réservée allouée sur le point de terminaison en continu à partir duquel vous tentez pour la lecture.  Suivez les instructions affichées sur [l’échelle transmission en continu des unités réservé](media-services-portal-scale-streaming-endpoints.md).

Q : est-il possible de faire la composition sur un flux en direct ?

R : la composition sur un flux en direct n’est pas actuellement disponible dans Azure Media Services, et vous devez donc à la précomposition sur votre ordinateur.

Q : puis-je utiliser Azure CDN avec Live en continu ?

R : Media Services prend en charge l’intégration avec Azure CDN (pour plus d’informations, voir [comment gérer les points de terminaison de diffusion en continu dans un compte de Services de support](media-services-portal-manage-streaming-endpoints.md)).  Vous pouvez utiliser Live en continu avec le CDN. Azure Media Services fournit des sorties de diffusion en continu, TLS et MPEG-tiret lisse. Ces formats utilisent HTTP pour le transfert de données et obtenir les avantages de la mise en cache HTTP. Dans la diffusion en continu live réel les données audio/vidéo sont divisées de fragments et cette fragments individuels mis en cache dans le CDN. Uniquement de données doit être actualisé le manifeste donnée. Paie Canada-Fichier actualise régulièrement les données de manifeste.

Q : les services de support de Azure est prise en charge des images de stockage ?

R : Si vous recherchez seulement pour le stockage des images JPEG ou PNG, vous devez conserver celles dans le stockage Blob Azure. Il n’y a aucun avantage à les placer dans votre compte de Services de support, sauf si vous souhaitez les conserver associé à vos vidéo ou Audio. Ou, si vous pouvez avoir besoin d’utiliser les images comme les superpositions de l’encodeur vidéo. Media Encoder Standard prend en charge la superposition d’images sur les vidéos, et c’est ce qu’il répertorie JPEG et PNG pris en charge d’entrée formats. Pour plus d’informations, consultez [Création de superpositions](media-services-custom-mes-presets-with-dotnet.md#overlay).

Q : Comment puis-je copier des ressources à partir d’un compte de Services de support à un autre.

R : pour copier des ressources à partir d’un compte de Services de support à un autre à l’aide de .NET, d’utiliser le méthode d’extension [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponible dans le référentiel de [Azure Media Services.NET SDK Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions/) . Pour plus d’informations, consultez [thread de forum](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) .

Q : quels sont les caractères pris en charge pour la dénomination des fichiers lors de l’utilisation de l’AMS ?

R : Media Services utilise la valeur de la propriété IAssetFile.Name lors de la génération d’URL pour le contenu de transmission en continu (par exemple, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters). Pour cette raison, codage de pourcentage n’est pas autorisée. La valeur de la propriété **Name** ne peut comporter aucun des [caractères %-codage-réservés](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)suivants : !*'();:@&=+$,/?%#[]". En outre, il ne peut exister un '.' pour l’extension de nom de fichier.


Q : comment faire pour se connecter à l’aide d’autres ?

R : après avoir connecté avec succès à https://media.windows.net, vous recevrez un réacheminement 301 spécifiant un autre URI de Services média. Vous devez faire d’autres appels à l’URI de nouveau comme décrit dans la [connexion à des Services de support à l’aide des API REST](media-services-rest-connect-programmatically.md). 


Q : Comment puis-je pour tourner une vidéo pendant le processus de codage.

A : [Standard de Media Encoder](media-services-dotnet-encode-with-media-encoder-standard.md) prend en charge la rotation par les angles de 90/180/270. Le comportement par défaut est « Auto », où il essaie de détecter la rotation de métadonnées dans le fichier MP4/MOV entrant et compensation. Inclure des **Sources de** l’élément suivant à l’un du json Présélections défini [ici](http://msdn.microsoft.com/library/azure/mt269960.aspx):
    
    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [
    
    ...




##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
