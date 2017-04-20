<properties 
    pageTitle="Comment faire pour créer un processeur de support | Microsoft Azure" 
    description="Apprenez à créer un composant du processeur de média pour encoder, convertir le format, crypter ou décrypter le contenu multimédia pour Azure Media Services. Exemples de code sont écrits en C# et utilisent le Kit de développement de Services de support pour .NET." 
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
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="how-to-get-a-media-processor-instance"></a>Comment : obtenir une Instance de processeur média

> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [RESTE](media-services-rest-get-media-processor.md)


##<a name="overview"></a>Vue d’ensemble

Dans Media Services, qu'un processeur de média est un composant qui gère une tâche de traitement spécifiques, telles que le codage, format de conversion, de leur chiffrement ou de déchiffrement de contenu multimédia. Vous créez en général un processeur multimédia lorsque vous créez une tâche pour coder, de crypter ou de convertir le format de contenu multimédia.

Le tableau suivant fournit le nom et la description de chaque processeur de média disponible.

Nom du processeur|Description|Plus d’informations
---|---|---
Standard de codage de média|Fournit des fonctionnalités standard de codage de la demande. |[Vue d’ensemble et comparaison d’Azure sur codeurs de Media à la demande](media-services-encode-asset.md)
Flux de travail Media Encoder Premium|Vous permet d’exécuter les tâches d’encodage à l’aide de Media Encoder Premium Workflow.|[Vue d’ensemble et comparaison d’Azure sur codeurs de Media à la demande](media-services-encode-asset.md)
Indexer de Media Azure| Vous permet de rendre le contenu et des fichiers de support avec possibilité de recherche, ainsi que de générer des mots clés et les pistes de sous-titrage fermés.|[Indexer de Media Azure](media-services-index-content.md)
Hyperlapse de médias Azure (aperçu)|Vous permet de lisser les chocs « » dans votre vidéo avec stabilisation vidéo. Vous permet également d’accélérer votre contenu dans un élément consommable.|[Azure Media Hyperlapse](media-services-hyperlapse-content.md)
Azure Media Encoder|Amorti
Déchiffrement du stockage| Amorti|
Le Gestionnaire de package de Media Azure|Amorti|
Azure Media chiffreur|Amorti|

##<a name="get-media-processor"></a>Obtenir le processeur média

La méthode suivante indique comment obtenir une instance de processeur média. L’exemple de code suppose qu’une variable de niveau module nommée **_context** pour référencer le contexte du serveur, comme décrit dans la section [Comment : se connecter à Media Services par programme](media-services-dotnet-connect-programmatically.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
        return processor;
    }


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment obtenir une instance de processeur média, consultez la rubrique [comment coder un actif](media-services-dotnet-encode-with-media-encoder-standard.md) qui vous indique comment utiliser le Standard Media Encoder pour encoder un actif.


