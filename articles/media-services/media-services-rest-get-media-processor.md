<properties 
    pageTitle="Comment faire pour créer un processeur de support | Microsoft Azure" 
    description="Apprenez à créer un composant du processeur de média pour encoder, convertir le format, crypter ou décrypter le contenu multimédia pour Azure Media Services." 
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

##<a name="get-mediaprocessor"></a>Obtenir MediaProcessor

>[AZURE.NOTE] Lorsque vous travaillez avec l’API REST de Services multimédias, les considérations suivantes s’appliquent :
>
>Lorsque vous accédez à des entités dans les Services de support, vous devez définir les valeurs et les champs d’en-tête spécifiques dans vos requêtes HTTP. Pour plus d’informations, consultez [le programme d’installation pour le développement d’API Media Services reste](media-services-rest-how-to-use.md).

>Après vous être connecté avec succès à la https://media.windows.net, vous recevrez un réacheminement 301 spécifiant un autre URI de Services multimédia. Vous devez faire d’autres appels à l’URI de nouveau comme décrit dans la [connexion à des Services de support à l’aide des API REST](media-services-rest-connect-programmatically.md). 


L’appel reste suivant montre comment obtenir une instance de processeur média par nom (dans ce cas, **Media Encoder Standard**). 



    
Demande de :

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.11
    Host: media.windows.net
    
Réponse :
        
    . . .
    
    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment obtenir une instance de processeur média, consultez la rubrique [comment coder un actif](media-services-rest-get-started.md) qui vous indique comment utiliser le Standard Media Encoder pour encoder un actif.
