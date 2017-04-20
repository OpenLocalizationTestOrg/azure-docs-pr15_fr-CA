<properties
    pageTitle="Votre Index de recherche Azure à l’aide de l’API REST de requête | Microsoft Azure | Service de recherche de nuage hébergé"
    description="Créer une requête de recherche dans recherche Azure et utilisez les paramètres de recherche pour filtrer et trier les résultats de recherche."
    services="search"
    documentationCenter=""
    manager="jhubbard"
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="query-your-azure-search-index-using-the-rest-api"></a>Interroger votre index de recherche d’Azure à l’aide de l’API REST
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-query-overview.md)
- [Portail](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTE](search-query-rest-api.md)

Cet article vous explique comment interroger un index à l’aide de l' [API REST de recherche Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Avant de commencer cette procédure pas à pas, vous devez déjà avoir [créé un index de recherche d’Azure](search-what-is-an-index.md) et [rempli avec les données](search-what-is-data-import.md).

## <a name="i-identify-your-azure-search-services-query-api-key"></a>I. Identifier la clé de votre service de recherche d’Azure requête api
Un composant clé de chaque opération de recherche par rapport à l’API REST de recherche Azure est la *clé d’api* qui a été généré pour le service que vous mis en service. Ayant une clé valide établit l’approbation, sur une base par demande, entre l’application d’envoi de la demande et le service qui la gère.

1. Pour trouver les touches de votre service api vous devez vous connecter au [Portail Azure](https://portal.azure.com/)
2. Atteindre les lames de votre service de recherche d’Azure
3. Cliquez sur l’icône « Clés »

Votre service aura les *clés de l’administration* et de *requête*.

 - Votre principal et secondaire *admin clés* accorder tous les droits sur toutes les opérations, y compris la possibilité de gérer le service, de créer et de supprimer des index, des indexeurs et des sources de données. Il existe deux clés afin que vous puissiez continuer à utiliser la clé secondaire si vous décidez de régénérer la clé primaire et vice versa.
 - Vos *clés de requête* accorder l’accès en lecture seule aux index et aux documents et sont en général distribuées aux applications émettant des requêtes de recherche.

Aux fins de l’interrogation d’un index, vous pouvez utiliser une des clés de votre requête. Vos clés d’administration peuvent également servir pour les requêtes, mais vous devez utiliser une clé de la requête dans le code de votre application comme cela suit mieux le [principe du moindre privilège](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="ii-formulate-your-query"></a>II. Formulation de votre requête.
Il existe deux manières de [Rechercher votre index à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/dn798927.aspx). La première consiste à émettre une demande HTTP POST où vos paramètres de requête seront définis dans un objet JSON dans le corps de la demande. L’autre méthode consiste à émettre une demande HTTP GET lorsque vos paramètres de requête seront définis dans l’URL de requête. Notez que POST a plus [souple des limites](https://msdn.microsoft.com/library/azure/dn798927.aspx) de la taille des paramètres de requête à GET. Pour cette raison, nous vous recommandons d’à l’aide de POST à moins d’avoir des circonstances particulières où obtenir serait plus pratique.

Pour les méthodes POST et GET, vous devez fournir votre *nom de service*, *nom de l’index*et la bonne *version de l’API* (la version actuelle de l’API est `2015-02-28` au moment de la publication de ce document) dans l’URL de la demande. Pour l’obtenir, la *chaîne de requête* à la fin de l’URL sera où vous fournissez les paramètres de la requête. Voir ci-dessous le format de l’URL :

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28

Le format de publication est le même, mais uniquement avec api-version dans les paramètres de chaîne de requête.



#### <a name="example-queries"></a>Exemples de requêtes

Voici quelques exemples de requêtes sur un index nommé « hôtels ». Ces requêtes sont affichés au format GET et POST.

La totalité de l’index de recherche sur le terme « budget » et retourner uniquement le `hotelName` champ :

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "budget",
    "select": "hotelName"
}
```

Appliquer un filtre à l’index pour rechercher des hôtels moins cher que 150 $ par nuit et retourner le `hotelId` et `description`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

Rechercher la totalité de l’index, ordre par un champ spécifique (`lastRenovationDate`) dans l’ordre décroissant, les deux premiers résultats, de n’afficher que `hotelName` et `lastRenovationDate`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## <a name="iii-submit-your-http-request"></a>III. Envoyez votre demande HTTP
Maintenant que vous avez formulées votre requête en tant que partie du corps (POST) ou URL de demande HTTP (pour obtenir), vous pouvez définir les en-têtes de demande et soumettre votre requête.

#### <a name="request-and-request-headers"></a>Les en-têtes de demande et de demande
Vous devez définir deux en-têtes de demande de GET, ou trois POST :
1. Le `api-key` en-tête doit être défini pour la clé de requête que vous avez trouvé à l’étape I ci-dessus. Notez que vous pouvez également utiliser une clé admin comme le `api-key` en-tête, mais il est recommandé d’utiliser une clé de requête car elle accorde uniquement un accès en lecture seule aux index et aux documents.
2. Le `Accept` en-tête doit être défini sur `application/json`.
3. Pour valider uniquement, le `Content-Type` en-tête doit également être défini sur `application/json`.

Voir ci-dessous pour une demande HTTP GET rechercher l’index « hôtels » à l’aide de l’API REST de recherche Azure, à l’aide d’une requête simple qui recherche le terme « hôtels » :

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2015-02-28
Accept: application/json
api-key: [query key]
```

Voici la même requête de l’exemple, cette fois à l’aide de HTTP POST :

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

Une demande de requête réussie génère un Code d’état de `200 OK` et les résultats de la recherche sont retournés au format JSON dans le corps de réponse. Voici ce que les résultats de l’aspect de requête ci-dessus, en supposant l’index « hôtels » est rempli avec les données de l' [Importation de données dans la recherche de Azure à l’aide de l’API REST](search-import-data-rest-api.md) (Notez que le JSON a été mis en forme pour plus de clarté).

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

Pour plus d’informations, consultez la section « Réponse » de [Rechercher des Documents](https://msdn.microsoft.com/library/azure/dn798927.aspx). Pour plus d’informations sur les autres codes d’état HTTP qui peut être retourné en cas de défaillance, consultez [codes d’état HTTP (recherche Azure)](https://msdn.microsoft.com/library/azure/dn798925.aspx).
