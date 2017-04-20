<properties
    pageTitle="Vue d’ensemble de l’Analytique des Services multimédia pour Azure | Microsoft Azure"
    description="Azure Media Services propose la pré-version publique d’Azure Media Analytique, un ensemble de services de vision de la reconnaissance vocale et l’ordinateur à l’échelle de l’entreprise, de la conformité, de sécurité et de portée. Les services de support Analytique Azure reposent sur les composants de la plateforme Azure Media Services principaux et sont donc prêts à traiter les supports de traitement à grande échelle sur le premier jour. "
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/24/2016"   
    ms.author="milanga;juliako;johndeu"/>

# <a name="azure-media-services-analytics-overview"></a>Vue d’ensemble de l’Analytique des Services multimédia pour Azure

##<a name="overview"></a>Vue d’ensemble

Plusieurs organisations et entreprises adoptent vidéo comme support préféré pour former leurs employés, engager les clients et les fonctions métier de document. Cloud computing, il est efficace de stocker, de diffuser et d’accéder à ces fichiers multimédias volumineux, mais que les entreprises développent leur bibliothèque de contenu vidéo, ils doivent avoir un moyen également efficace pour extraction de nouvelles idées à partir de la vidéo afin de créer un plus significatif, personnalisés des interactions avec leurs audiences et prendre leurs activités au niveau suivant.

Pour répondre à ce besoin croissant du marché, Azure Media Services offre Media Analytique, une collection de composants vocale et vision (à l’échelle de l’entreprise, la conformité, de sécurité et portée globale) afin de faciliter aux entreprises et aux entreprises de tirer des leçons exploitables à partir de leurs fichiers vidéo. Les services de support Analytique Azure reposent sur les composants de la plateforme Azure Media Services principaux et sont donc prêts à traiter les supports de traitement à grande échelle sur le premier jour.

Azure Media Analytique permettent aux développeurs de rapidement mise en route avec les fonctions de vision pour la vidéo à une échelle limitée et mettre cette fonctionnalité avancée dans des applications. Azure Media Analytique est créé pour être utilisé par les environnements d’entreprise avec la pleine échelle, la conformité, la sécurité et la portée requise par les organisations de grande taille.

Le diagramme suivant illustre les **Médias Analytique** et autres parties principales de la plate-forme de Services de support. 

![Workflow de la demande (VOD)](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)

Des processeurs multimédia Media Analytique produisent les fichiers MP4 ou JSON. Si un processeur de support produit un fichier MP4, vous pouvez progressivement télécharger le fichier. Si un processeur de support produit un fichier JSON, vous pouvez télécharger le fichier du stockage blob Azure. 

## <a name="azure-media-analytics-services"></a>Services de support Analytique Azure

- **Indexeur** – Azure Indexer de Media vous permet de rendre le contenu disponible pour la recherche, ainsi que de générer des pistes de sous-titrage fermés. Azure Media Services publié **Azure Media indexeur 2 Preview** prenant en charge le plus rapide d’indexation et plus large. Langues prises en charge incluent l’anglais, espagnol, Français, allemand, italien, chinois, portugaise et arabe. Pour obtenir des informations détaillées et des exemples, voir les [vidéos du processus avec Azure Media indexeur 2](media-services-process-content-with-indexer2.md)
 
- **Hyperlapse** – Microsoft Hyperlapse est le résultat de la recherche de vision d’ordinateur à Microsoft Research (MSR), combinant la stabilisation video et la caducité de temps pour créer des vidéos rapides, consommables beau à partir de votre formulaire long contenu de plus de 20 ans. Outre des délais, vous pouvez également utiliser Hyperlapse pour créer des vidéos stables à partir de tremblante vidéos capturées à l’aide des caméscopes et des téléphones cellulaires. Pour obtenir des informations détaillées et des exemples, consultez la [Hyperlapse des fichiers de support avec Azure Media Hyperlapse](media-services-hyperlapse-content.md)
 
- **Détection de mouvement** , vous pouvez utiliser ce service pour la détection de mouvement dans une vidéo avec un arrière-plan de papier à lettres. Cela est idéal pour les clients qui souhaitent vérifier de faux positifs sur les événements de mouvement détectés par les caméras de surveillance sur les flux de vidéo surveillance. Pour des informations détaillées et des exemples, consultez [Détection de mouvement pour Azure Media Analytique](media-services-motion-detection.md).
 
- **Détection de visage et d’émotions de Face** – à l’aide de ce service, vous pouvez détecter les faces de personnes et leurs émotions, y compris le bonheur, tristesse, surprise, colère, contempt, crainte, sentira et indifférent/neutre. Cela a plusieurs utile applications de l’industrie, décrites ci-dessous, y compris agrégation et l’analyse des réactions de personnes participant à un événement. Pour obtenir des informations détaillées et des exemples, consultez [détection d’émotion pour Azure Media Analytique et Face](media-services-face-and-emotion-detection.md).
 
- **Synthèse de vidéo** – synthèse de vidéo peut vous aider à créer des résumés des vidéos longues en sélectionnant automatiquement extraits intéressants à partir de la vidéo source. Cela est utile lorsque vous souhaitez fournir un aperçu rapide de ce qui se passe dans une vidéo longue. Pour obtenir des informations détaillées et des exemples, reportez-vous à la section [Utilisation Azure Media vidéo miniatures pour créer une synthèse de la vidéo](media-services-video-summarization.md)

- La **reconnaissance optique de caractères** - Azure Media Analytique OCR (reconnaissance optique de caractères) vous permet de vous permet de convertir le contenu de texte dans des fichiers vidéo en texte modifiable numérique avec possibilité de recherche. Cela vous permet d’automatiser l’extraction de métadonnées explicites de signal vidéo de votre support.
 
- **Biffure du visage évolutive** - **Redactor de support Azure** est un MP Analytique Media Azure qui offre de biffure évolutifs face dans le nuage. Biffure du visage vous permet de modifier votre vidéo pour brouiller les faces des individus sélectionnés. Voulez-vous utiliser le service de rédaction de face dans les scénarios de sécurité et de médias public. Quelques minutes de métrage qui contient plusieurs faces peuvent prendre des heures à biffer manuellement, mais grâce à ce service, le processus de rédaction de face nécessitera que quelques étapes simples. Pour plus d’informations, consultez [cet](media-services-face-redaction.md) article.

 
## <a name="common-scenarios"></a>Scénarios courants

Vous trouverez ci-dessous quelques exemples de scénarios où Azure Media Analytique peut aider les entreprises et les entreprises des secteurs glaner des nouvelles idées à partir de la vidéo pour créer plus personnalisé public et les engagements de l’employé, ainsi que gérer plus efficacement les gros volume de contenu vidéo :

- Les **centres d’appels** – même avec l’avènement des réseaux sociaux, appel client centres de facilitent encore un pourcentage important des transactions de service client. Codé dans ces données audio est une mine d’informations sur les clients qui peuvent être analysées pour améliorer les produits et également former les employés du centre d’appel pour atteindre le niveau de satisfaction client. À l’aide de Indexer de Media Azure, les clients sont en mesure d’extraire le texte et génération d’un index de recherche et de tableaux de bord pour extraire l’intelligence autour de la plus courante se plaint, source de se plaint et autres ces données pertinentes.

- **Modération du contenu généré par l’utilisateur** – à partir de prises de médias aux services de police, de nombreuses organisations possèdent des portails en vis-à-vis publics où ils acceptent UGC supports, tels que les images et les vidéos. Le volume de contenu peut grimper en raison d’événements inattendus. Dans ces scénarios, il est presque impossible de procéder à une révision manuelle efficace du contenu de manière correcte. Clients peuvent compter sur le service de modération du contenu de se concentrer sur le contenu qui convient.

- **Surveillance** - avec la croissance des caméras IP, il existe une explosion de vidéos de surveillance. Examen manuel de vidéo de surveillance est long et sujet aux erreurs humaines. Azure Media Analytique fournit de nombreux composants, tels que la détection de mouvement, la détection de visage et Hyperlapse pour rendre le processus de révision, de gestion et de création de dérivés plus facile.

## <a name="media-services-analytics-media-processors"></a>Des processeurs multimédia Analytique de Services multimédia 

Cette section répertorie tous les Media Services Analytique Media processeurs (MP) et montre comment utiliser .NET ou reste pour obtenir un objet de point de gestion.

### <a name="mp-names"></a>Noms de point de gestion


- Aperçu de l’indexeur 2 Azure Media
- Indexer de Media Azure
- Azure Media Hyperlapse
- Détecteur de Face Azure Media
- Détecteur de mouvement Azure Media
- Miniatures de vidéo Azure Media
- Reconnaissance optique de caractères Azure Media

### <a name="net"></a>.NET

La fonction suivante prend un des noms de point de gestion spécifiés et retourne un objet de point de gestion.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


## <a name="rest"></a>RESTE

Demande de :

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net
    
Réponse :
        
    . . .
    
    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

##<a name="demos"></a>Démonstrations

[Démonstrations multimédias Analytique Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

##<a name="next-steps"></a>Étapes suivantes

Passez en revue Media Services, voies de formation.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-articles"></a>Articles connexes

[Annonce de Media Services Analytique](https://azure.microsoft.com/blog/introducing-azure-media-analytics/)
  

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
