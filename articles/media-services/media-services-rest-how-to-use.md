<properties 
    pageTitle="Vue d’ensemble de l’API REST de Services média | Microsoft Azure" 
    description="Vue d’ensemble de l’API REST de Services multimédia" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="juliako"/>


# <a name="media-services-rest-api-overview"></a>Vue d’ensemble de l’API REST de Services multimédia 

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Microsoft Azure Media Services est un service qui accepte les demandes HTTP basé sur OData et peuvent répondre en clair JSON ou atom + pub. Étant donné que Media Services est conforme aux directives de conception : bleu azur, est un ensemble d’en-têtes HTTP nécessaires que chaque client doit utiliser lors de la connexion à des Services multimédias, ainsi que d’un jeu d’en-têtes facultatifs qui peuvent être utilisés. Les sections suivantes décrivent les en-têtes et les verbes HTTP que vous pouvez utiliser lorsque création de demandes et de recevoir des réponses à partir des Services de support.

##<a name="considerations"></a>Considérations relatives à la 

Les considérations suivantes s’appliquent lors de l’utilisation de reste.

- Lors de l’interrogation des entités, il existe une limite de 1000 entités retournées en une seule fois car public reste v2 limite les résultats de la requête de résultats de 1000. Vous devez utiliser les **Ignorer** et **prendre** (.NET) / **haut** (reste) comme indiqué dans [Cet exemple .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) et [exemple de cette API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 

- Lors de l’utilisation de JSON et la spécification pour utiliser le mot clé de **__metadata** dans la demande (par exemple, à fait référence à un objet lié) vous devez définir l’en-tête **Accept** au [format JSON détaillé](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (voir l’exemple suivant). OData ne comprend pas la propriété **__metadata** dans la demande, sauf si vous la définissez explicite.  

        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.11
        Authorization: Bearer <token> 
        Host: media.windows.net
        
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 
        

## <a name="standard-http-request-headers-supported-by-media-services"></a>En-têtes de demande HTTP standard pris en charge par les Services de support

Pour chaque appel de que vous rendre dans les Services de support, il existe un ensemble d’en-têtes requis, que vous devez inclure dans votre demande, et un jeu d’en-têtes facultatifs, vous pouvez également inclure. Le tableau suivant répertorie les en-têtes requis :


En-tête|Type de|Valeur
---|---|---
Autorisation|PORTEUR|PORTEUR est le mécanisme d’autorisation d’accepté uniquement. La valeur doit également inclure le jeton d’accès fourni par l’ACS.
x-ms-version|Décimal|2.11
DataServiceVersion|Décimal|3.0
MaxDataServiceVersion|Décimal|3.0



>[AZURE.NOTE] Étant donné que les Services de support utilise OData pour exposer son référentiel de métadonnées actif sous-jacent via les API du reste, les en-têtes de DataServiceVersion et MaxDataServiceVersion doivent être inclus dans toute demande ; Toutefois, si elles ne le sont pas, puis actuellement Media Services suppose que la valeur de DataServiceVersion en cours d’utilisation est 3.0.

Voici un ensemble d’en-têtes facultatifs :

En-tête|Type de|Valeur
---|---|---
Date|Date RFC 1123|Horodatage de la demande
Accepter|Type de contenu|Le type de contenu demandé pour la réponse semblable à la suivante :<p> -application/json ; odata = verbose<p> -application/atom + xml<p> Réponses peut-être à un autre type de contenu, par exemple une extraction blob, où une réponse correcte contient le flux de données blob en tant que la charge utile.
Accepter de codage|Gzip, compressé|GZIP et DEFLATE, codage, le cas échéant. Remarque : Pour les ressources de grande taille, Media Services peut ignorer cet en-tête et retourner des données non compressées.
Accept-Language|« en », « es » et ainsi de suite.|Spécifie la langue par défaut pour la réponse.
Accepter-Charset|Type de jeu de caractères comme « UTF-8 »|Valeur par défaut est UTF-8.
X-HTTP-méthode|Méthode HTTP|Autorise les clients ou les pare-feu qui ne prennent pas en charge les méthodes HTTP telles que PUT ou DELETE à utiliser ces méthodes, envoyé par tunnel via un appel GET.
Type de contenu|Type de contenu|Type de corps de la demande dans les requêtes PUT ou de publication de contenu.
id de demande client|Chaîne|Une valeur définie par l’appelant qui identifie la demande donnée. Si spécifié, cette valeur est incluse dans le message de réponse comme un moyen pour traiter la requête. <p><p>**Important**<p>Les valeurs doivent être plafonnées à 2096b (2k).

## <a name="standard-http-response-headers-supported-by-media-services"></a>En-têtes de réponse HTTP standard pris en charge par les Services de support

Voici un jeu d’en-têtes qui peuvent être retournés pour vous en fonction de la ressource de que votre demande et l’action que vous souhaitez effectuer.


En-tête|Type de|Valeur
---|---|---
id de la demande|Chaîne|Un identificateur unique pour l’opération actuelle, générés par le service.
id de demande client|Chaîne|Un identificateur spécifié par l’appelant dans la demande d’origine, le cas échéant.
Date|Date RFC 1123|La date à laquelle la demande a été traitée.
Type de contenu|Varie|Le type de contenu du corps de la réponse.
Codage de contenu|Varie|Gzip ou compressé, le cas échéant.


## <a name="standard-http-verbs-supported-by-media-services"></a>Verbes HTTP standard pris en charge par les Services de support

Voici une liste complète des verbes HTTP qui peut être utilisé lors de la demande de fabrication de HTTP :


Verbe|Description
---|---
Télécharger|Retourne la valeur actuelle d’un objet.
Publier|Crée un objet basé sur les données fournies, ou envoie une commande.
PUT|Remplace un objet, ou crée un objet nommé (le cas échéant).
SUPPRIMER|Supprime un objet.
FUSION|Met à jour un objet existant avec les modifications de la propriété nommée.
TÊTE|Retourne les métadonnées d’un objet d’une réponse GET.

##<a name="limitation"></a>Limitation

Lors de l’interrogation des entités, il existe une limite de 1000 entités retournées en une seule fois car public reste v2 limite les résultats de la requête de résultats de 1000. Vous devez utiliser les **Ignorer** et **prendre** (.NET) / **haut** (reste) comme indiqué dans [Cet exemple .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) et [exemple de cette API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 


## <a name="discovering-media-services-model"></a>Modèle de Media Services de découverte

Pour rendre les entités de Media Services plus identifiables, l’opération $metadata peut être utilisée. Il vous permet de récupérer tous les types d’entité valide, les propriétés d’entité, les associations, fonctions, actions et ainsi de suite. L’exemple suivant montre comment construire l’URI : métadonnées de $ https://media.windows.net/API/.

Vous devez ajouter « ? version=2.x de l’api » à la fin de l’URI, si vous souhaitez afficher les métadonnées dans un navigateur, ou n’incluez pas l’en-tête x-ms-version dans votre demande.



##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





 
