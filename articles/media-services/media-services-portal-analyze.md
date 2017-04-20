<properties
    pageTitle="Analyser vos médias via le portail Azure | Microsoft Azure"
    description="Cette rubrique explique comment traiter vos médias avec des processeurs multimédia Media Analytique (MPs) via le portail Azure."
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


# <a name="analyze-your-media-using-the-azure-portal"></a>Analyser vos médias via le portail Azure

> [AZURE.NOTE] Pour terminer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="overview"></a>Vue d’ensemble

Analytique de Services Azure Media est un ensemble de composants vocale et vision (à l’échelle de l’entreprise, la conformité, de sécurité et portée globale) afin de faciliter aux entreprises et aux entreprises de tirer des leçons exploitables à partir de leurs fichiers vidéo. Pour une présentation plus détaillée d’Azure Media Services Analytique, consultez [cette](media-services-analytics-overview.md) rubrique. 

Cette rubrique explique comment traiter vos médias avec des processeurs multimédia Media Analytique (MPs) via le portail Azure. Packs de gestion Analytique Media produire les fichiers MP4 ou JSON. Si un processeur de support produit un fichier MP4, vous pouvez progressivement télécharger le fichier. Si un processeur de support produit un fichier JSON, vous pouvez télécharger le fichier du stockage blob Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Choisissez un actif que vous souhaitez analyser 
 
1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Dans la fenêtre **paramètres** , sélectionnez **ressources**.  
.
    ![Analyser les vidéos](./media/media-services-portal-analyze/media-services-portal-analyze001.png)

2. Sélectionnez l’élément que vous souhaitez analyser et appuyez sur le bouton **analyser** .
        
    ![Analyser les vidéos](./media/media-services-portal-analyze/media-services-portal-analyze002.png)

3. Dans la fenêtre de la **ressource multimédia de processus avec Analytique de Media** , sélectionnez le processeur. 

    Le reste de cet article explique pourquoi et comment utiliser chaque processeur. 
   
4. Appuyez sur **créer** au début un travail.

## <a name="azure-media-indexer"></a>Indexer de Media Azure

Le processeur média **Azure Indexer de Media** vous permet de rendre le contenu et des fichiers de support avec possibilité de recherche, ainsi que de générer des pistes de sous-titrage fermés. Cette section donne des détails sur les options que vous pouvez spécifier pour ce point de gestion.

![Analyser les vidéos](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Langue

Le langage naturel à être reconnu dans le fichier multimédia. Par exemple, en anglais ou en espagnol. 

### <a name="captions"></a>Légendes

Vous pouvez choisir un format de la légende qui sera généré à partir de votre contenu. Une tâche d’indexation peut générer des fichiers de sous-titres dans les formats suivants :  

- **Lapon**
- **TTML**
- **WebVTT**

Fermeture de légende (CC) et les fichiers dans ces formats peuvent être utilisés pour rendre des fichiers audio et vidéo accessibles aux personnes atteintes de handicap de l’audition.

### <a name="aib-file"></a>Fichier AIB

Sélectionnez cette option si vous souhaitez générer le fichier Audio Index Blob pour une utilisation avec SQL Server IFilter personnalisé. Pour plus d’informations, consultez [le](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blog.

### <a name="keywords"></a>Mots clés

Sélectionnez cette option si vous souhaitez générer un fichier XML de mots clés. Ce fichier contient les mots clés de l’extrait à partir du contenu de la parole, avec la fréquence et les informations d’offset.

### <a name="job-name"></a>Nom de la tâche

Un nom convivial qui vous permet d’identifier la tâche. [Cet](media-services-portal-check-job-progress.md) article décrit comment vous pouvez surveiller la progression d’une tâche. 

### <a name="output-file"></a>Fichier de sortie

Un nom convivial qui vous permet d’identifier le contenu de sortie. 

## <a name="azure-media-hyperlapse"></a>Azure Media Hyperlapse

Hyperlapse de médias Azure est un Pack d’administration qui crée des vidéos bon temps écoulé à partir du contenu de première-personne ou l’appareil photo de l’action.  Pour plus d’informations, consultez [cette](media-services-hyperlapse-content.md) rubrique. Cette section donne des détails sur les options que vous pouvez spécifier pour ce point de gestion.

![Analyser les vidéos](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>Vitesse 

Spécifier la vitesse permettant d’accélérer la vidéo d’entrée. La sortie est un format stabilisée et temps écoulé de l’entrée vidéo.

### <a name="job-name"></a>Nom de la tâche

Un nom convivial qui vous permet d’identifier la tâche. [Cet](media-services-portal-check-job-progress.md) article décrit comment vous pouvez surveiller la progression d’une tâche. 

### <a name="output-file"></a>Fichier de sortie

Un nom convivial qui vous permet d’identifier le contenu de sortie. 

## <a name="azure-media-face-detector"></a>Détecteur de Face Azure Media

Le processeur de média **Azure Media Face détecteur** (MP) vous permet de compter, effectuer le suivi des mouvements et évaluer la même réaction via des expressions du visage et la participation du public à. Ce service contient deux fonctionnalités : 

- **Détection de visage**

    La détection de visage détecte et suit les faces humains dans une vidéo. Plusieurs faces peuvent être détectés et par la suite être suivis qu’ils sont en déplacement, avec les métadonnées de l’heure et l’emplacement retournée dans un fichier JSON. Lors du suivi, il va tenter de donner un code cohérent sur la même face alors que la personne est déplacer sur l’écran, même s’ils sont empêchés ou laissez brièvement de l’image.

    >[AZURE.NOTE]Ce service n’effectue pas la reconnaissance faciale. Une personne qui quitte le cadre ou devient masquée pour trop long aura un nouvel ID lorsqu’ils renvoient.

- **Détection d’émotion**
    
    Détection d’émotion est un composant facultatif du processeur média détection Face qui renvoie les faces détectés, y compris le bonheur, tristesse, crainte, colère et plus analyse sur plusieurs attributs émotionnels. 

![Analyser les vidéos](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Mode de détection

Un des modes suivants peut être utilisé par le processeur :

- détection de visage
- par détection d’émotion face
- détection d’émotion d’agrégation

### <a name="job-name"></a>Nom de la tâche

Un nom convivial qui vous permet d’identifier la tâche. [Cet](media-services-portal-check-job-progress.md) article décrit comment vous pouvez surveiller la progression d’une tâche. 

### <a name="output-file"></a>Fichier de sortie

Un nom convivial qui vous permet d’identifier le contenu de sortie. 

## <a name="azure-media-motion-detector"></a>Détecteur de mouvement Azure Media

Le processeur de média du **Détecteur de mouvement Azure Media** (MP) vous permet d’identifier efficacement les sections d’intérêt dans une vidéo sinon long et se déroule normalement. Détection de mouvement peut servir de métrage de caméra statique pour identifier des sections de la vidéo où se produit le mouvement. Il génère un fichier JSON qui contient une entrée de métadonnées avec l’horodatage et la région de cadre englobant dans lequel l’événement s’est produit.

Ciblant les flux vidéo de sécurité, cette technologie est en mesure de catégoriser le mouvement dans les événements pertinents et les faux positifs, tels que des ombres et des changements d’éclairage. Cela vous permet de générer des alertes de sécurité à partir de flux vidéo sans recevoir des messages indésirables avec des événements non pertinents sans fin, tout en étant en mesure d’extraire les moments d’intérêt à partir des vidéos de surveillance extrêmement long.

![Analyser les vidéos](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Miniatures de vidéo Azure Media

Ce processeur peut vous aider à créer des résumés des vidéos longues en sélectionnant automatiquement extraits intéressants à partir de la vidéo source. Cela est utile lorsque vous souhaitez fournir un aperçu rapide de ce qui se passe dans une vidéo longue. Pour obtenir des informations détaillées et des exemples, reportez-vous à la section [Utilisation Azure Media vidéo miniatures pour créer une synthèse de la vidéo](media-services-video-summarization.md)

![Analyser les vidéos](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nom de la tâche

Un nom convivial qui vous permet d’identifier la tâche. [Cet](media-services-portal-check-job-progress.md) article décrit comment vous pouvez surveiller la progression d’une tâche. 

### <a name="output-file"></a>Fichier de sortie

Un nom convivial qui vous permet d’identifier le contenu de sortie. 


##<a name="next-steps"></a>Étapes suivantes

Afficher les Services de support cursus.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


