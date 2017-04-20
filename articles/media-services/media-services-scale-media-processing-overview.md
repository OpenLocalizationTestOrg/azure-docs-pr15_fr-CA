<properties
    pageTitle="Mise à l’échelle de vue d’ensemble du traitement des médias | Microsoft Azure"
    description="Cette rubrique est une vue d’ensemble de la mise à l’échelle de traitement multimédia avec Azure Media Services."
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
    ms.date="08/29/2016"
    ms.author="juliako"/>


# <a name="scaling-media-processing-overview"></a>Mise à l’échelle de vue d’ensemble de traitement de support

Cette page fournit une vue d’ensemble de la procédure et les raisons à l’échelle de transformation de media. 

## <a name="overview"></a>Vue d’ensemble

Un compte de Services de support est associé à un Type d’unité réservé, qui détermine la vitesse avec laquelle vos tâches de traitement de médias sont traitées. Vous pouvez choisir entre les types d’unité réservés suivants : **S1**, **S2**ou **S3**. Par exemple, la même tâche de codage s’exécute plus rapidement lorsque vous utilisez la comparaison de type unité **S2** réservés au type de **S1** . Pour plus d’informations, consultez les [Types d’unité réservé](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

En plus de spécifier le type d’unité réservé, vous pouvez spécifier pour configurer votre compte avec unités réservées. Le nombre d’unités de réservés mis en service détermine le nombre de tâches de support pouvant être traités simultanément dans un compte donné. Par exemple, si votre compte comporte cinq unités réservées, puis les tâches de cinq support exécutent simultanément tant que sont les tâches de traitement. Le reste des tâches dans la file d’attente et seront soient reprises pour le traitement séquentiel lorsqu’une tâche en cours d’exécution se termine. Si un compte ne dispose pas de toutes les unités réservées mis en service, puis tâches seront reprises dans l’ordre. Dans ce cas, le temps d’attente entre une tâche de fin et de début des dépend de la disponibilité des ressources dans le système.

## <a name="choosing-between-different-reserved-unit-types"></a>Choix entre les types d’unité réservé différente

Le tableau suivant vous aidera à prendre les décision lors du choix entre différentes vitesses de codage. Aussi, il fournit quelques cas de banc d’essai et des associations de sécurité URL que vous pouvez utiliser pour télécharger les vidéos sur lesquels vous pouvez réaliser vos propres tests :

Scénarios|**S1**|**S2**|**S3**|
----------|------------|----------|------------
Cas d’usage| Codage de la vitesse de transmission unique. <br/>Pas de temps à SD ou en dessous les résolutions, les fichiers sensibles, faible coût.|Vitesse de transmission unique et plusieurs encodage du débit.<br/>Utilisation normale de codage d’un à la fois SD et HD. |Vitesse de transmission unique et plusieurs encodage du débit.<br/>Vidéos de résolution HD et 4K complets. Temps de codage des délais sensibles, plus rapide. 
Banc d’essai|[Le fichier d’entrée : 5 minutes de longs 640x360p à 29,97 images/seconde](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>Codage d’un fichier MP4, à la même résolution, de vitesse de transmission unique prend environ 11 minutes.|[Fichier d’entrée : 5 minutes de longs 1280x720p à 29,97 images/seconde](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>Codage avec « Débit unique H264 720p » prédéfinie prend environ 5 minutes.<br/><br/>Codage avec « H264 débit plusieurs 720p » préréglage prend environ 11,5 minutes.|[Le fichier d’entrée : 5 minutes de longs 1920x1080p à 29,97 images/seconde](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>Codage avec « Débit unique H264 1080p » prédéfinie prend environ 2,7 minutes.<br/><br/>Codage avec « H264 débit plusieurs 1080p » préréglage prend environ 5,7 minutes.

##<a name="considerations"></a>Considérations relatives à la

>[AZURE.IMPORTANT] Consultez les remarques décrites dans cette section.  

- Réservée d’unités de travail pour la parallélisation de tous les traitements de support, y compris l’indexation des tâches à l’aide de Indexer de Media Azure.  Toutefois, contrairement à l’encodage, tâches d’indexation ne sont pas traités plus rapidement avec des unités réservées plus rapidement.

- Si vous utilisez le pool partagé, c'est-à-dire sans n’importe quelle unité réservée, puis vos tâches d’encodage offrent les mêmes performances comme avec RUs de S1. Il existe toutefois aucune limite supérieure à la durée de vos tâches peuvent passer dans l’état en file d’attente et à un moment donné, une tâche est en cours d’exécution.

- Les centres de données suivants ne proposent pas le type d’unité **S2** réservé : sud du Brésil, ouest de l’Inde, l’Inde centrale et du Sud de l’Inde.

- Les centres de données suivants ne proposent pas le type d’unité **S3** réservé : Brésil, ouest de l’Inde, l’Inde Sud.

- Le plus grand nombre d’unités spécifiées pour la période de 24 heures est utilisé dans le calcul du coût.


##<a name="quotas-and-limitations"></a>Quotas et limitations

Pour plus d’informations sur les quotas et les limitations et l’ouverture d’un ticket de support, consultez [des Quotas et des limitations](media-services-quotas-and-limitations.md).

##<a name="next-step"></a>Étape suivante

Réalisation de la tâche de traitement de support mise à l’échelle avec l’une de ces technologies : 

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [Portail](media-services-portal-scale-media-processing.md)
- [RESTE](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
