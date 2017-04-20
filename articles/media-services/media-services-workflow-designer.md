<properties 
    pageTitle="Créer des Workflows de codage avancés avec le Concepteur de flux de travail | Microsoft Azure" 
    description="Découvrez comment créer des workflows de codage avancés avec le Concepteur de flux de travail." 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016"
    ms.author="juliako;johndeu;anilmur"/>


#<a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Créer des Workflows de codage avancés avec le Concepteur de flux de travail

##<a name="overview"></a>Vue d’ensemble

Le **Concepteur de flux de travail** est un outil de bureau Windows qui est utilisé pour concevoir et créer des flux de travail personnalisés pour le codage avec le **Workflow de prime Media Encoder**.
À l’aide de la puissance de l’outil Concepteur de flux de travail, vous pouvez concevoir et créer des flux de travail complexes qui s’exécuteront dans **Media Encoder Premium**.  

Les workflows peuvent inclure la logique de décision de client et de branchement basée sur les propriétés du fichier source d’entrée. Vous pouvez créer des workflows avec des propriétés substituables et les valeurs dynamiques pour faciliter encore les tâches de codage plus complexes à répéter et à personnaliser dans le nuage.

Exemple de workflows que vous pouvez créer est les suivantes :

- Décision en fonction des flux de travail qui inspecte le contenu source pour la résolution et de code uniquement les pistes de sortie souhaité.  Il s’agit d’helfpul en éliminant les pistes perdus qui seraient générés par interpolation le par inadvertance de contenu source.
- Plusieurs fichiers d’entrée peuvent être utilisés pour prendre en charge des légendes, des superpositions et SUTURE contenu ensemble. 

Cet outil peut également être utilisé pour modifier un de nos [publié des flux de travail](media-services-workflow-designer.md#existing_workflows). 

>[AZURE.NOTE]Pour obtenir une copie de l’outil Workflow Designer, veuillez contacter mepd@microsoft.com.


Une fois un fichier de flux de travail est créé, il peut être téléchargé en tant qu’actif et ensuite être utilisé pour le codage des fichiers multimédias. Pour plus d’informations sur la façon de coder avec le **Workflow de prime Media Encoder** à l’aide de **.NET**, reportez-vous à la section [avancée de codage avec le Workflow de prime Media Encoder](media-services-encode-with-premium-workflow.md).

##<a id="existing_workflows"></a>Modifier le flux de travail existant

La par défaut [publié des flux de travail](media-services-workflow-designer.md#existing_workflows) peut être modifié à l’aide de l’outil de conception. Vous pouvez obtenir la valeur par défaut des fichiers de flux de travail [ici](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Le dossier contient également la description de ces fichiers.

Les vidéos suivantes montrent comment utiliser le concepteur.

###<a name="day-1--getting-started"></a>Jour 1 – mise en route

Vidéo du jour 1 couvre :

- Vue d’ensemble du Concepteur
- Workflows de base – « Hello World »
- Création de plusieurs fichiers MP4 pour une utilisation avec Azure Media Services de diffusion en continu de sortie

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-1]

###<a name="day-2"></a>Jour 2

Jour 2 vidéo couvre :

- Divers scénarios de fichier Source – gestion audio
- Flux de travail avec une logique avancée
- Étapes de graphique

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-2]

###<a name="day-3"></a>Jour 3

Vidéo jour 3 couvre :

- Dans les projets de flux de travail/de script
- Restrictions avec le codeur en cours
- Q & r
 
> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-3]


## <a name="next-step"></a>Étape suivante

Passez en revue Media Services, voies de formation.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


Si vous devez prendre en charge ou avez des questions à propos de la création de workflows personnalisés dans l’outil Concepteur de flux de travail, envoyez un courrier électronique à mepd@microsoft.com.

##<a name="see-also"></a>Voir aussi

[Vidéos de formation Concepteur de flux de travail de codeur Azure Premium](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)
