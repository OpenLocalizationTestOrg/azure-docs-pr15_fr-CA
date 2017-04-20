<properties
    pageTitle="L’utilisation de Fiddler pour évaluer et tester les API de reste de recherche Azure | Microsoft Azure | Service de recherche de nuage hébergé"
    description="Utilisez Fiddler pour une approche sans code de vérification de la disponibilité de la recherche d’Azure et essayer de l’API du reste."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="10/17/2016"
    ms.author="heidist"/>

# <a name="use-fiddler-to-evaluate-and-test-azure-search-rest-apis"></a>Utilisez Fiddler pour évaluer et tester Azure recherche reste API
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-query-overview.md)
- [Explorateur de recherche](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [RESTE](search-query-rest-api.md)

Cet article explique comment utiliser Fiddler, disponible en [téléchargement gratuit à partir de Telerik](http://www.telerik.com/fiddler), problème les demandes HTTP vers et afficher les réponses à l’aide de l’API REST de recherche Azure, sans avoir à écrire de code. Recherche Azure est entièrement géré, hébergé le service de recherche de nuage de Microsoft Azure, facilement programmable via les API .NET et autres. Le service de recherche d’Azure reste API sont documentées sur le [site MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Dans les étapes suivantes, vous allez créer un index, télécharger des documents, interroger l’index et ensuite interroger le système pour des informations sur le service.

Pour effectuer ces étapes, vous aurez besoin d’un service de recherche d’Azure et `api-key`. Pour obtenir des instructions sur la mise en route, voir [Création d’un service de recherche d’Azure dans le portail](search-create-service-portal.md) .

## <a name="create-an-index"></a>Créer un index

1. Démarrez Fiddler. Dans le menu **fichier** , désactivez le **Capturer le trafic** à masquer l’activité HTTP superflue qui n’est pas liée à la tâche en cours.

3. Sous l’onglet **Composer** , vous devez formuler une requête qui ressemble à la capture d’écran suivante.

    ![][1]

2. Sélectionnez **placer**.

3. Entrez une URL qui indique l’URL du service, les attributs de la demande et la version de l’api. Quelques éléments à garder à l’esprit :
   + Utiliser le protocole HTTPS comme préfixe.
   + Attribut de requête est « hôtels/index / ». Cette option indique à la recherche pour créer un index nommé « hôtels ».
   + Version de l’API est en minuscule, spécifié sous la forme « ?-version de l’api = 2015-02-28 ». Versions de l’API sont importantes, car Azure recherche déploie les mises à jour régulièrement. Dans de rares occasions, un service de mise à jour peut introduire une modification avec rupture à l’API. Pour cette raison, recherche d’Azure nécessite une version api sur chaque demande afin que vous vous trouvez dans un contrôle total sur celui qui est utilisé.

    L’URL complète doit être similaire à l’exemple suivant.

            https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.  Spécifier l’en-tête de la demande, remplacement de l’hôte et la clé d’api avec les valeurs qui sont valides pour votre service.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

5.  Dans le corps de la demande, coller dans les champs qui composent la définition d’index.
            
             {
            "name": "hotels",  
            "fields": [
              {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
              {"name": "baseRate", "type": "Edm.Double"},
              {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
              {"name": "hotelName", "type": "Edm.String"},
              {"name": "category", "type": "Edm.String"},
              {"name": "tags", "type": "Collection(Edm.String)"},
              {"name": "parkingIncluded", "type": "Edm.Boolean"},
              {"name": "smokingAllowed", "type": "Edm.Boolean"},
              {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
              {"name": "rating", "type": "Edm.Int32"},
              {"name": "location", "type": "Edm.GeographyPoint"}
             ]
            }

6.  Cliquez sur **exécuter**.

En quelques secondes, vous devez voir une réponse HTTP 201 dans la liste des sessions, en indiquant que l’index a été créé avec succès.

Si vous obtenez HTTP 504, vérifiez que l’URL spécifie l’HTTPS. Si vous voyez HTTP 400 ou 404, vérifiez le corps de la demande pour vérifier qu'aucune erreur de copier-coller. Un HTTP 403 indique généralement un problème avec la clé api (une clé non valide ou un problème de syntaxe avec la façon dont la clé de l’api est spécifiée).

## <a name="load-documents"></a>Charger des documents

Sous l’onglet **éditeur** , votre demande pour valider des documents apparaîtra comme suit. Le corps de la requête contient les données de recherche pour 4 hôtels.

   ![][2]

1. Sélectionnez **Valider**.

2.  Entrez une URL qui commence par HTTPS, suivie par l’URL de votre service, suivi par « /indexes/ <'indexname ' >/docs/index ?-version de l’api = 2015-02-28 ». L’URL complète doit être similaire à l’exemple suivant.

            https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.  En-tête de demande doit être le même qu’avant. N’oubliez pas que vous avez remplacé l’hôte et la clé d’api avec les valeurs qui sont valides pour votre service.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

4.  Le corps de demande contient quatre documents à ajouter à l’index des hôtels.

            {
            "value": [
            {
                "@search.action": "upload",
                "hotelId": "1",
                "baseRate": 199.0,
                "description": "Best hotel in town",
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
                "@search.action": "upload",
                "hotelId": "3",
                "baseRate": 279.99,
                "description": "Surprisingly expensive",
                "hotelName": "Dew Drop Inn",
                "category": "Bed and Breakfast",
                "tags": ["charming", "quaint"],
                "parkingIncluded": true,
                "smokingAllowed": false,
                "lastRenovationDate": null,
                "rating": 4,
                "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
              },
              {
                "@search.action": "upload",
                "hotelId": "4",
                "baseRate": 220.00,
                "description": "This could be the one",
                "hotelName": "A Hotel for Everyone",
                "category": "Basic hotel",
                "tags": ["pool", "wifi"],
                "parkingIncluded": true,
                "smokingAllowed": false,
                "lastRenovationDate": null,
                "rating": 4,
                "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
              }
             ]
            }

8.  Cliquez sur **exécuter**.

En quelques secondes, vous devez voir une réponse HTTP 200 dans la liste des sessions. Cela indique que les documents ont été créés avec succès. Si vous obtenez un 207, au moins un document téléchargement a échoué. Si vous obtenez une erreur 404, vous avez une erreur de syntaxe dans l’en-tête ou le corps de la demande.

## <a name="query-the-index"></a>Requête de l’index

Maintenant que les index et les documents sont chargés, vous pouvez émettre des requêtes sur les.  Sous l’onglet **Composer** , une commande **GET** qui interroge le service de recherche semblable à la capture d’écran suivante.

   ![][3]

1.  Sélectionnez **obtenir**.

2.  Entrez une URL qui commence par HTTPS, suivie par l’URL de votre service, suivi par « /indexes/ <'indexname ' > / docs ? », suivi des paramètres de la requête. À titre d’exemple, utilisez l’URL suivante, en remplaçant le nom d’hôte exemple par un vaut pour votre service.
    
            https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    Cette requête recherche sur le terme « hôtels » et extrait des catégories de facette pour le classement.

3.  En-tête de demande doit être le même qu’avant. N’oubliez pas que vous avez remplacé l’hôte et la clé d’api avec les valeurs qui sont valides pour votre service.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

Le code de réponse doit être de 200, et la sortie de la réponse doit ressembler à la capture d’écran suivante.

   ![][4]

Exemple de requête ci-dessous est à partir de l' [opération d’Index de recherche (API de recherche Azure)](http://msdn.microsoft.com/library/dn798927.aspx) sur MSDN. La plupart des requêtes d’exemple dans cette rubrique incluent des espaces, qui ne sont pas autorisés dans Fiddler. Remplacer chaque espace avec caractère + avant de la coller dans la requête de chaîne avant d’essayer de la requête de Fiddler.

**Avant que les espaces sont remplacés :**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**Une fois les espaces sont remplacés par + :**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## <a name="query-the-system"></a>Le système de requête

Vous pouvez également interroger le système pour obtenir le nombre de documents et de la consommation du stockage. Sous l’onglet **éditeur** , votre demande sera similaire à ce qui suit, et la réponse retourne un décompte du nombre de documents et d’espace utilisé.

 ![][5]

1.  Sélectionnez **obtenir**.

2.  Entrez une URL qui inclut l’URL de votre service, suivi de « / hôtels/index/statistiques ?-version de l’api = 2015-02-28 » :

            https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28

3.  Spécifier l’en-tête de la demande, remplacement de l’hôte et la clé d’api avec les valeurs qui sont valides pour votre service.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

4.  Rien dans le corps de la demande.

5.  Cliquez sur **exécuter**. Vous devriez voir un code d’état HTTP 200 dans la liste des sessions. Sélectionnez l’entrée de validation de votre commande.

6.  Cliquez sur l’onglet **d’inspecteurs** et cliquez sur l’onglet **en-têtes** , puis sélectionnez le format JSON. Vous devez voir le document count et stockage la taille (en Ko).

## <a name="next-steps"></a>Étapes suivantes

Voir [Gérer votre service de recherche sur Azure](search-manage.md) pour un code non-approche de la gestion et l’utilisation de recherche d’Azure.


<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
