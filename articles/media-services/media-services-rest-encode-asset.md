<properties 
    pageTitle="Comment coder une immobilisation à l’aide de Media Encoder Standard | Microsoft Azure" 
    description="Apprenez à utiliser le Standard Media Encoder pour encoder le contenu multimédia sur les Services de support. Exemples de code utilisent l’API REST." 
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


#<a name="how-to-encode-an-asset-using-media-encoder-standard"></a>Comment coder une immobilisation à l’aide de Media Encoder Standard


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
- [RESTE](media-services-rest-encode-asset.md)
- [Portail](media-services-portal-encode.md)

##<a name="overview"></a>Vue d’ensemble
Pour diffuser des vidéos numériques sur internet, vous devez compresser le média. Les fichiers vidéo numériques sont très volumineux et peuvent être trop important sur internet ou des périphériques de vos clients pour s’afficher correctement. Le codage est le processus de compression audio et vidéo pour que vos clients puissent consulter vos médias.

Tâches d’encodage sont une des opérations de traitement plus courantes dans les Services de support. Vous créez les tâches d’encodage pour convertir des fichiers multimédias à partir d’un encodage à un autre. Lorsque vous codez, vous pouvez utiliser l’encodeur intégré des Services de support (Media Encoder Standard). Vous pouvez également utiliser un encodeur fourni par un partenaire de Services de support ; codeurs de tiers sont disponibles via l’Azure Marketplace. Vous pouvez spécifier les détails des tâches de codage à l’aide de chaînes prédéfinies définies pour votre serveur, ou à l’aide de fichiers de configuration prédéfinis. Pour voir les types de paramètres prédéfinis qui sont disponibles, consultez [Les préréglages de tâche pour Media Encoder Standard](http://msdn.microsoft.com/library/mt269960).

Chaque tâche peut avoir une ou plusieurs tâches en fonction du type de traitement que vous souhaitez accomplir. Par le biais de l’API REST, vous pouvez créer des tâches et leurs tâches associées dans une des deux façons :

- Les tâches peuvent être définies inline par le biais de la propriété de navigation de tâches sur les entités de la tâche, ou
- par l’intermédiaire du traitement par lots OData.


Il est recommandé de toujours coder vos fichiers mezzanine à une vitesse de transmission adaptive MP4 la valeur et le format de votre choix à l’aide de l' [Emballage dynamique](media-services-dynamic-packaging-overview.md)puis convertir le jeu. Pour tirer parti de l’emballage dynamique, vous devez d’abord obtenir au moins une unité de transmission en continu à la demande pour le point de terminaison en continu à partir de laquelle vous envisagez de livraison votre contenu. Pour plus d’informations, consultez [comment les Services de support d’échelle](media-services-portal-manage-streaming-endpoints.md).

Si votre capital de sortie est stockage crypté, vous devez configurer la stratégie de livraison d’actif. Pour plus d’informations, consultez [stratégie de livraison de configuration actif](media-services-rest-configure-asset-delivery-policy.md).


>[AZURE.NOTE]Avant de commencer à référencer des processeurs multimédia, vérifiez que vous disposez du bon média ID de processeur. Pour plus d’informations, consultez [Obtenir des processeurs multimédia](media-services-rest-get-media-processor.md).

##<a name="create-a-job-with-a-single-encoding-task"></a>Créer une tâche avec une seule tâche de codage

>[AZURE.NOTE] Lorsque vous travaillez avec l’API REST de Services multimédias, les considérations suivantes s’appliquent :
>
>Lorsque vous accédez à des entités dans les Services de support, vous devez définir les valeurs et les champs d’en-tête spécifiques dans vos requêtes HTTP. Pour plus d’informations, consultez [le programme d’installation pour le développement d’API Media Services reste](media-services-rest-how-to-use.md).

>Après vous être connecté avec succès à la https://media.windows.net, vous recevrez un réacheminement 301 spécifiant un autre URI de Services multimédia. Vous devez faire d’autres appels à l’URI de nouveau comme décrit dans la [connexion à des Services de support à l’aide des API REST](media-services-rest-connect-programmatically.md).
>
>Lors de l’utilisation de JSON et la spécification pour utiliser le mot clé de **__metadata** dans la demande (par exemple, à fait référence à un objet lié) vous devez définir l’en-tête **Accept** au [format JSON avec commentaires](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): accepter : application/json ; odata = verbose.

L’exemple suivant vous montre comment créer et publier un projet avec une que tâche définie pour encoder une vidéo à une résolution spécifique et de la qualité. Lors de l’encodage avec Media Encoder Standard, vous pouvez utiliser des préréglages de configuration de tâche spécifiés [ici](http://msdn.microsoft.com/library/mt269960).

Demande de :

POST https://media.windows.net/API/Jobs HTTP/1.1 Content-Type : application/json ; odata = accepter verbose : application/json ; odata = DataServiceVersion détaillée : MaxDataServiceVersion 3.0 : x-ms-version 3.0 : autorisation 2.11 : porteur <token value> 
 x-ms-client-demande-id : 00000000-0000-0000-0000-000000000000 hôte : media.windows.net

    
    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Multiple Bitrate 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Réponse :
    
    HTTP/1.1 201 Created

    . . . 

###<a name="set-the-output-assets-name"></a>Définir le nom de l’élément de la sortie

L’exemple suivant montre comment définir l’attribut assetName :

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

##<a name="considerations"></a>Considérations relatives à la

- TaskBody propriétés doivent utiliser le littéral XML pour définir le numéro d’entrée ou de sortie actifs qui seront utilisés par la tâche. La rubrique de la tâche contient la définition de schéma XML pour le fichier XML.
- Dans la définition de TaskBody, chaque interne de valeur pour <inputAsset> et <outputAsset> doit être défini comme JobInputAsset(value) ou JobOutputAsset(value).
- Une tâche peut avoir plusieurs capitaux de sortie. Un JobOutputAsset(x) utilisable uniquement une fois sous la forme d’une sortie d’une tâche dans un projet.
- Vous pouvez spécifier JobInputAsset ou JobOutputAsset comme un actif d’entrée d’une tâche.
- Tâches ne doivent pas former un cycle.
- Le paramètre de la valeur que vous passez à JobInputAsset ou JobOutputAsset représente la valeur d’index pour une immobilisation. Les ressources réelles sont définis dans les propriétés de navigation InputMediaAssets et OutputMediaAssets sur la définition de l’entité tâche. 
- Media Services s’appuie sur OData v3, les immobilisations individuelles dans les ensembles de propriétés de navigation InputMediaAssets et OutputMediaAssets sont référencées par un « __metadata : uri « paire nom-valeur.
- InputMediaAssets correspond à un ou plusieurs actifs que vous avez créé dans les Services de support. OutputMediaAssets sont créés par le système. Ils ne font pas référencent à une immobilisation existante.
- OutputMediaAssets peut être nommé à l’aide de l’attribut assetName. Si cet attribut n’est pas présent, alors que le nom de la OutputMediaAsset sera quelle que soit la valeur de texte interne de la <outputAsset> élément est le suffixe de la valeur du nom de la tâche, soit de la valeur de l’Id de la tâche (dans le cas où la propriété Name n’est pas définie). Par exemple, si vous définissez une valeur pour assetName de « Sample », la propriété Name de OutputMediaAsset être définie à « Sample ». Toutefois, si vous n’avez pas défini de valeur pour assetName, mais n’avez défini le nom du travail à « NewJob », alors le nom OutputMediaAsset est « _NewJob JobOutputAsset (valeur) ». 


##<a name="create-a-job-with-chained-tasks"></a>Créer un travail avec tâches chaînés

Dans de nombreux scénarios d’application, les développeurs souhaitent créer une série de tâches de traitement. Dans les Services de support, vous pouvez créer une série de tâches chaînés. Chaque tâche effectue les étapes de traitement différents et peut utiliser des supports différents processeurs. Les tâches chaînés peuvent transférer un actif d’une tâche à un autre, effectuer une séquence linéaire des tâches sur l’actif. Toutefois, les tâches effectuées dans une tâche ne doivent pas dans une séquence. Lorsque vous créez une tâche chaînée, les objets **ITask** chaînées sont créés dans un objet **IJob** .

>[AZURE.NOTE] Il existe actuellement une limite de 30 tâches par projet. Si vous avez besoin de plus de 30 tâches en chaîne, créez plusieurs tâches pour contenir les tâches.


    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


###<a name="considerations"></a>Considérations relatives à la

Pour activer le chaînage de la tâche :

- Un projet doit avoir au moins 2 tâches
- Il doit y avoir au moins une tâche dont l’entrée est la sortie d’une autre tâche dans le projet.

## <a name="use-odata-batch-processing"></a>Utilisez le traitement par lots de OData 

L’exemple suivant montre comment utiliser le traitement par lots de OData pour créer un projet et les tâches. Pour plus d’informations sur le traitement par lots, voir la rubrique [Traitement des lots d’Open Data Protocol (OData)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).
 
    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net
    
    
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d
    
    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    
    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}
    
    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    POST https://media.windows.net/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    
    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
 


## <a name="create-a-job-using-a-jobtemplate"></a>Créer une tâche à l’aide d’un modèle de tâche


Lors du traitement de plusieurs ressources à l’aide d’un jeu commun de tâches, les JobTemplates sont utiles pour spécifier les préréglages de tâche par défaut, l’ordre des tâches et ainsi de suite.

L’exemple suivant montre comment créer un modèle de tâche avec un inline TaskTemplate définie. Le TaskTemplate utilise la norme de codage Media comme le MediaProcessor pour coder le fichier actif ; Cependant, les autres MediaProcessors peut être utilisée d’également. 


    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net

    
    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]Contrairement à d’autres entités de Media Services, vous devez définir un nouvel identificateur GUID pour chaque TaskTemplate et placez-le dans le taskTemplateId et la propriété Id dans le corps de votre demande. Le schéma d’identification de contenu doit suivre le schéma décrit dans identifier les entités Azure Media Services. En outre, JobTemplates ne peut pas être mis à jour. Au lieu de cela, vous devez créer un nouveau avec vos modifications de mise à jour.
 

Si l’opération réussit, la réponse suivante est retournée :
    
    HTTP/1.1 201 Created
    
    . . .


L’exemple suivant montre comment créer un travail de référence à un Id le modèle de tâche :

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net

    
    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
     

Si l’opération réussit, la réponse suivante est retournée :
    
    HTTP/1.1 201 Created
    
    . . . 



##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-steps"></a>Étapes suivantes
Maintenant que vous savez comment créer une tâche pour coder un assset, consultez la rubrique [Comment pour vérifier la progression du projet avec les Services de support](media-services-rest-check-job-progress.md) .


##<a name="see-also"></a>Voir aussi

[Obtenir des processeurs multimédia](media-services-rest-get-media-processor.md)
