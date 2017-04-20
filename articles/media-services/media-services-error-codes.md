<properties
    pageTitle="Les codes d’erreur Azure Media Services | Microsoft Azure"
    description="La rubrique fournit une vue d’ensemble des codes d’erreur Azure Media Services."
    authors="Juliako"
    manager="erikre"
    editor=""
    services="media-services"
    documentationCenter=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016" 
    ms.author="juliako"/>

# <a name="azure-media-services-error-codes"></a>Codes d’erreur Azure Media Services

Lorsque vous utilisez les Services de support Microsoft Azure, vous pouvez recevoir des codes d’erreur HTTP à partir du service en fonction des problèmes tels que les jetons d’authentification sans date d’expiration pour les actions qui ne sont pas pris en charge dans les Services de support. Voici une liste des **codes d’erreur HTTP** qui peuvent être retournés par les Services de support et les causes possibles pour eux.  
  
## <a name="400-bad-request"></a>400 demande incorrecte

La demande contient des informations non valides et est rejetée en raison d’une des raisons suivantes :

- Une version non prise en charge de l’API est spécifiée. Pour la version la plus récente, consultez [le programme d’installation pour le développement d’API Media Services reste](media-services-rest-how-to-use.md).
- La version de l’API Media Services n’est pas spécifiée. Pour plus d’informations sur la spécification de la version de l’API, consultez [connexion à des Services multimédias avec l’API REST de Services multimédia](media-services-rest-connect-programmatically.md). 
   
    >[AZURE.NOTE] Si vous utilisez .NET ou Java SDK pour se connecter à des Services multimédias, la version de l’API est spécifiée pour vous chaque fois que vous essayez et effectuez une action par rapport aux Services de support.
- Une propriété non définie a été spécifiée. Le nom de la propriété est le message d’erreur. Seules les propriétés qui sont membres d’une entité donnée peuvent être spécifiées. Pour obtenir une liste des entités et leurs propriétés, reportez-vous à la section [Référence des API reste Azure Media Services](http://msdn.microsoft.com/library/azure/hh973617.aspx) .
- Une valeur de propriété non valide a été spécifiée. Le nom de la propriété est le message d’erreur. Cliquez sur le lien précédent pour les types de propriétés valides et leurs valeurs.
- Une valeur de propriété est manquante et est nécessaire.
- Partie de l’URL spécifiée contient une valeur incorrecte.
- Une tentative a été faite pour mettre à jour une propriété WriteOnce.
- Une tentative a été effectuée pour créer une tâche avec un élément d’entrée avec un AssetFile primaire n’a été spécifié ou n’a pas pu être déterminé..
- Une tentative a été faite pour mettre à jour d’un localisateur de SAS. Les localisateurs SAS peuvent uniquement être créés ou supprimés. Localisateurs de diffusion en continu peut être mis à jour. Pour plus d’informations, reportez-vous à la section [repères](http://msdn.microsoft.com/library/azure/hh974308.aspx).
- Une opération non prise en charge ou la requête a été soumise. 

## <a name="401-unauthorized"></a>401 non autorisé

La demande n’a pas pu être authentifiée (avant qu’il peut être autorisé) en raison d’une des raisons suivantes :

- En-tête d’authentification manquant.
- Valeur de l’en-tête d’authentification incorrecte.
    - Le jeton a expiré. Si vous utilisez les API reste directement, consultez [connexion à des Services multimédias avec l’API REST de Services média](media-services-rest-connect_programmatically.md) pour savoir comment générer un nouveau jeton d’authentification. Si vous utilisez .NET ou Java SDK, créez un objet CloudMediaContext ou MediaContract pour générer le jeton. Pour plus d’informations sur la procédure à suivre, consultez [connexion à des Services multimédias avec le Kit de développement de Services de support pour .NET](media-services-dotnet-connect-programmatically.md).
    - Le jeton contient une signature non valide.</li></ul></li></ul>

## <a name="403-forbidden"></a>403 Interdit

La demande n’est pas autorisée en raison d’une des raisons suivantes :

- Le compte de Services de support ne peut pas être trouvé ou a été supprimé.
- Le compte de Services de support est désactivé et le type de demande n’est pas HTTP GET. Opérations de service renvoie une réponse de 403.
- Le jeton d’authentification ne contient-elle pas d’informations d’identification de l’utilisateur : nom de compte et/ou le SubscriptionId. Vous pouvez trouver ces informations dans l’extension de l’interface utilisateur des Services de support pour votre compte de Services de support dans le portail de gestion Azure.
- La ressource ne sont pas accessibles.
    - Une tentative a été faite pour utiliser un MediaProcessor qui n’est pas disponible pour votre compte de Services de support.
    - Une tentative a été faite pour mettre à jour d’un modèle de tâche défini par les Services de support.
    - Une tentative a été faite pour remplacer le repère de certains autres Services Media du compte.
    - Une tentative a été faite pour remplacer ContentKey certains autres Services Media du compte.

- La ressource n’a pas pu être créée en raison d’un quota de service qui a été atteint pour le compte de Services de support. Pour plus d’informations sur les quotas du service, reportez-vous à la section [Quotas et des Limitations](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 non trouvé

La demande n’est pas autorisée sur une ressource à une des raisons suivantes :

- Une tentative a été faite pour mettre à jour une entité qui n’existe pas.
- Une tentative a été faite pour supprimer une entité qui n’existe pas.
- Une tentative a été effectuée pour créer une entité à une entité qui n’existe pas.
- Une tentative a été effectuée afin d’obtenir une entité qui n’existe pas.
- Une tentative a été effectuée pour spécifier un compte de stockage qui n’est pas associé au compte de Services de support.  

## <a name="409-conflict"></a>409 conflit

La demande n’est pas autorisée en raison d’une des raisons suivantes :

- AssetFile plus d’une porte le nom spécifié dans la ressource.
- Une tentative a été effectuée pour créer un deuxième AssetFile principal au sein de l’actif.
- Une tentative a été effectuée pour créer un ContentKey avec l’Id spécifié est déjà utilisé.
- Une tentative a été effectuée pour créer un repère avec l’Id spécifié est déjà utilisé.
- IngestManifestFile plus d’une porte le nom spécifié dans le IngestManifest.
- Une tentative a été effectuée pour lier un cryptage ContentKey stockage de la ressource de stockage crypté.
- Une tentative a été faite pour lier la même ContentKey à l’actif.
- Une tentative a été effectuée pour créer un repère à un actif dont le conteneur stockage est manquant ou n’est plus associé à l’immobilisation.
- Une tentative a été effectuée pour créer un localisateur pour une immobilisation qui possède déjà des 5 localisateurs en cours d’utilisation. (Le stockage azure applique la limite de cinq stratégies d’accès partagé dans un conteneur de stockage.)
- Liaison d’un actif, les compte de stockage pour un IngestManifestAsset n’est pas le même que le compte de stockage utilisé par le parent IngestManifest.  

## <a name="500-internal-server-error"></a>Erreur de serveur interne 500

Lors du traitement de la demande, Media Services rencontre une erreur qui empêche le traitement de se poursuivre. Cela peut être dû à l’une des raisons suivantes :

- Création d’une ressource ou une tâche échoue, car les informations de quota du compte des Services de support service sont temporairement indisponibles.
- Création d’un conteneur de stockage blob actif ou IngestManifest échoue, car les informations de compte de stockage du compte sont temporairement indisponibles.
- Autre erreur inattendue. 

## <a name="503-service-unavailable"></a>503 Service non disponible

Le serveur est actuellement incapable de recevoir des demandes. Cette erreur peut provenir de demandes trop important pour le service. Mécanisme d’accélération de Media Services restreint l’utilisation des ressources pour les applications qui font une demande excessive au service.

>[AZURE.NOTE]Vérifiez le message d’erreur et la chaîne de code d’erreur pour obtenir des informations plus détaillées sur la raison pour laquelle vous avez reçu l’erreur 503. Cette erreur ne signifie pas toujours la limitation.

Description des états possibles sont les suivantes :

- « Le serveur est occupé. Séries précédentes de ce type de demande a pris plus de {0} secondes. »
- « Le serveur est occupé. Plus de {0} demandes par seconde peuvent être limité. »
- « Le serveur est occupé. Plus que peuvent limiter les demandes de {0} dans {1} secondes. »

Pour gérer cette erreur, nous vous recommandons d’à l’aide d’exponentielle recul logique des nouvelles tentatives. Cela implique l’utilisation de plus en plus longue attend entre les nouvelles tentatives pour les réponses d’erreurs consécutives.  Pour plus d’informations, consultez le [Bloc d’Application de gestion des pannes en régime transitoire](https://msdn.microsoft.com/library/hh680905.aspx). 

>[AZURE.NOTE]Si vous utilisez [Azure Media Services SDK pour .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), la logique de nouvelle tentative pour l’erreur 503 a été implémentée par le SDK.  
  
## <a name="see-also"></a>Voir aussi  

[Codes d’erreur de gestion des Services multimédia](http://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
