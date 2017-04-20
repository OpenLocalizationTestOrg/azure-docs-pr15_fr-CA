<properties
    pageTitle="Téléchargement des données de recherche Azure à l’aide de l’API REST | Microsoft Azure | Service de recherche de nuage hébergé"
    description="Apprenez à télécharger des données à un index de recherche de Azure à l’aide de l’API REST."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="upload-data-to-azure-search-using-the-rest-api"></a>Télécharger des données à la recherche d’Azure à l’aide de l’API REST
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTE](search-import-data-rest-api.md)

Cet article vous explique comment utiliser l' [API REST de recherche Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour importer des données dans un index de recherche d’Azure.

Avant de commencer cette procédure pas à pas, vous disposez déjà [créé un index de recherche d’Azure](search-what-is-an-index.md).

Pour distribuer des documents dans l’index à l’aide de l’API REST, émet une demande HTTP POST au point de terminaison URL de votre index. Le corps du corps de la demande HTTP est un objet JSON qui contient les documents pour être ajouté, modifié ou supprimé.

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>I. Identifier la clé api de l’administrateur de votre service de recherche d’Azure
Lors de l’émission de requêtes HTTP contre votre service à l’aide de l’API REST, *chaque* API demande doit inclure la clé api qui a été générée pour le service de recherche que vous mis en service. Ayant une clé valide établit l’approbation, sur une base par demande, entre l’application d’envoi de la demande et le service qui la gère.

1. Pour trouver les touches de votre service api vous devez vous connecter au [Portail Azure](https://portal.azure.com/)
2. Atteindre les lames de votre service de recherche d’Azure
3. Cliquez sur l’icône « Clés »

Votre service aura les *clés de l’administration* et de *requête*.

  - Votre principal et secondaire *admin clés* accorder tous les droits sur toutes les opérations, y compris la possibilité de gérer le service, de créer et de supprimer des index, des indexeurs et des sources de données. Il existe deux clés afin que vous puissiez continuer à utiliser la clé secondaire si vous décidez de régénérer la clé primaire et vice versa.
  - Vos *clés de requête* accorder l’accès en lecture seule aux index et aux documents et sont en général distribuées aux applications émettant des requêtes de recherche.

Aux fins de l’importation de données dans un index, vous pouvez utiliser votre clé d’administrateur principal ou secondaire.

## <a name="ii-decide-which-indexing-action-to-use"></a>II. Décidez de l’action d’indexation à utiliser
Lorsque vous utilisez l’API REST, vous émettez des requêtes HTTP POST avec un corps de demande JSON à l’URL du point de terminaison de votre index de recherche d’Azure. L’objet JSON dans le corps de la demande HTTP contient un seul tableau JSON nommé « valeur », qui contient des objets JSON représentant les documents que vous souhaitez ajouter à votre index, update ou delete.

Chaque objet JSON dans le tableau « valeur » représente un document à indexer. Chacun de ces objets contient les clé du document et spécifie l’action d’indexation souhaitée (téléchargement, fusion, suppression, etc.). En fonction de la sous actions vous choisissez, certains champs doivent être inclus pour chaque document :

@search.action | Description | Champs nécessaires pour chaque document | Notes
--- | --- | --- | ---
`upload` | Un `upload` action est similaire à un « upsert » dans lequel le document sera inséré s’il est nouveau et mis à jour/remplacé s’il existe. | touche, ainsi que tous les autres champs que vous souhaitez définir | Lors de la mise à jour/remplacement d’un document existant, un champ qui n’est pas spécifié dans la demande aura son champ la valeur `null`. Cela se produit même lorsque le champ a été précédemment défini sur une valeur non null.
`merge` | Met à jour un document existant avec les champs spécifiés. Si le document n’existe pas dans l’index, la fusion échoue. | touche, ainsi que tous les autres champs que vous souhaitez définir | N’importe quel champ que vous spécifiez dans une fusion remplacent le champ existant dans le document. Cela inclut les champs de type `Collection(Edm.String)`. Par exemple, si le document contient un champ `tags` avec la valeur `["budget"]` et que vous exécutez une fusion avec une valeur `["economy", "pool"]` de `tags`, la valeur finale de la `tags` champ sera `["economy", "pool"]`. Il ne sera pas `["budget", "economy", "pool"]`.
`mergeOrUpload` | Cette action se comporte comme `merge` si un document avec la clé spécifiée existe déjà dans l’index. Si le document n’existe pas, il se comporte comme `upload` avec un nouveau document. | touche, ainsi que tous les autres champs que vous souhaitez définir | -
`delete` | Supprime le document spécifié à partir de l’index. | clé uniquement | Tous les champs que vous spécifiez que le champ clé sera ignoré. Si vous souhaitez supprimer un champ d’un document, utilisez `merge` à la place et simplement la valeur du champ explicitement null.

## <a name="iii-construct-your-http-request-and-request-body"></a>III. Construire votre requête HTTP et le corps de requête
Maintenant que vous avez rassemblé les valeurs de champ nécessaire pour les actions de votre index, vous êtes prêt à construire de demande HTTP et corps de la demande JSON pour importer vos données.

#### <a name="request-and-request-headers"></a>Les en-têtes de demande et de demande
Dans l’URL, vous devez fournir le nom de votre service, nom de l’index (« hôtels » dans le cas présent), ainsi que la version correcte de l’API (la version actuelle de l’API est `2015-02-28` au moment de la publication de ce document). Vous devez définir la `Content-Type` et `api-key` en-têtes de demande. Pour ces derniers, utilisez une des clés d’administration de votre service.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>Corps de la demande

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

Dans ce cas, nous utilisons `upload`, `mergeOrUpload`, et `delete` en tant qu’actions de notre recherche.

Supposons que cet exemple « hôtels » index est déjà rempli avec un nombre de documents. Notez la façon dont nous ne possédait pas de spécifier tous les champs document possible lors de l’utilisation `mergeOrUpload` et comment nous uniquement la clé de document (`hotelId`) lors de l’utilisation `delete`.

Notez également que vous ne pouvez inclure documents de 1000 (ou 16 Mo) dans une seule demande d’indexation.

## <a name="iv-understand-your-http-response-code"></a>IV. Comprendre votre code de réponse HTTP
#### <a name="200"></a>200
Après la soumission d’une demande d’indexation réussie, vous recevrez une réponse HTTP avec le code d’état de `200 OK`. Le corps JSON de la réponse HTTP sera comme suit :

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
Un code d’état de `207` sera retournée lorsqu’au moins un article n’a pas été indexé. Le corps JSON de la réponse HTTP contient les informations relatives aux documents ayant échouées.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [AZURE.NOTE] Cela signifie souvent que la charge sur votre service de recherche a atteint un point où l’indexation des demandes commencera à retourner `503` les réponses. Dans ce cas, nous hautement est recommandé que votre code client hors tension et nous attendre avant le renouvellement. Cela donnera le système peu de temps à restaurer, augmenter les chances de réussite de futures demandes. Tentatives de rapidement vos demandes seront prolonger uniquement la situation.

#### <a name="429"></a>429
Un code d’état de `429` sont retournées lorsque vous avez dépassé votre quota sur le nombre de documents par index.

#### <a name="503"></a>503
Un code d’état de `503` sera retourné si aucun des articles de la demande ont été indexés. Cette erreur signifie que le système est sous une charge importante et si votre demande ne peut pas être traitée pour l’instant.

> [AZURE.NOTE] Dans ce cas, nous hautement est recommandé que votre code client hors tension et nous attendre avant le renouvellement. Cela donnera le système peu de temps à restaurer, augmenter les chances de réussite de futures demandes. Tentatives de rapidement vos demandes seront prolonger uniquement la situation.

Pour plus d’informations sur les actions de document et les réponses de réussite / d’erreur, reportez-vous à [Ajout, mise à jour ou supprimer les Documents](https://msdn.microsoft.com/library/azure/dn798930.aspx). Pour plus d’informations sur les autres codes d’état HTTP qui peut être retourné en cas de défaillance, consultez [codes d’état HTTP (recherche Azure)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

## <a name="next"></a>Suivant
Après avoir rempli votre index de recherche d’Azure, vous serez prêt à commencer l’exécution de requêtes pour rechercher des documents. Pour plus d’informations, consultez [Index de recherche Azure votre requête](search-query-overview.md) .
