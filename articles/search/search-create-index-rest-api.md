<properties
    pageTitle="Créer un index de recherche d’Azure à l’aide de l’API REST | Microsoft Azure | Service de recherche de nuage hébergé"
    description="Créer un index dans le code à l’aide de l’API REST de Azure recherche HTTP."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index-using-the-rest-api"></a>Créer un index de recherche d’Azure à l’aide de l’API REST
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-what-is-an-index.md)
- [Portail](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTE](search-create-index-rest-api.md)


Cet article vous guidera tout au long du processus de création d’une recherche d’Azure d' [index](https://msdn.microsoft.com/library/azure/dn798941.aspx) à l’aide de l’API REST de recherche Azure.

Avant de suivre ce guide et création d’un index, vous devez avoir déjà [créé un service de recherche d’Azure](search-create-service-portal.md).

Pour créer un index de recherche d’Azure à l’aide de l’API REST, émet une requête HTTP POST au point de terminaison de votre service de recherche d’Azure URL. Votre définition d’index figure dans le corps de la demande en tant que contenu JSON bien formé.


## <a name="i-identify-your-azure-search-services-admin-api-key"></a>I. Identifier la clé api de l’administrateur de votre service de recherche d’Azure
Maintenant que vous avez configuré un service de recherche d’Azure, vous pouvez émettre des requêtes HTTP contre le point de terminaison URL de votre service à l’aide de l’API REST. Toutefois, les demandes de *toutes les* API doivent inclure la clé api qui a été générée pour le service de recherche que vous mis en service. Ayant une clé valide établit l’approbation, sur une base par demande, entre l’application d’envoi de la demande et le service qui la gère.

1. Pour trouver les touches de votre service api vous devez vous connecter au [Portail Azure](https://portal.azure.com/)
2. Atteindre les lames de votre service de recherche d’Azure
3. Cliquez sur l’icône « Clés »

Votre service aura les *clés de l’administration* et de *requête*.

 - Votre principal et secondaire *admin clés* accorder tous les droits sur toutes les opérations, y compris la possibilité de gérer le service, de créer et de supprimer des index, des indexeurs et des sources de données. Il existe deux clés afin que vous puissiez continuer à utiliser la clé secondaire si vous décidez de régénérer la clé primaire et vice versa.
 - Vos *clés de requête* accorder l’accès en lecture seule aux index et aux documents et sont en général distribuées aux applications émettant des requêtes de recherche.

Pour les besoins de la création d’un index, vous pouvez utiliser votre clé d’administrateur principal ou secondaire.

## <a name="ii-define-your-azure-search-index-using-well-formed-json"></a>II. Définir votre index de recherche d’Azure à l’aide de JSON bien formé
Une demande HTTP POST unique à votre service créera votre index. Le corps de votre demande HTTP POST contient un seul objet JSON qui définit votre index de recherche d’Azure.

1. La première propriété de cet objet JSON est le nom de l’index.
2. La deuxième propriété de cet objet JSON est un tableau JSON nommé `fields` qui contient un objet JSON distinct pour chaque champ dans l’index. Chacun de ces objets JSON contiennent plusieurs paires nom/valeur pour chacun des attributs d’un champ, y compris le « nom », « type », etc..

Il est important de le maintenir votre recherche utilisateur expérience et des besoins à l’esprit lorsque vous concevez votre index comme les [attributs corrects](https://msdn.microsoft.com/library/azure/dn798941.aspx)doit être affecté à chaque champ. Ces contrôles d’attributs qui recherche les fonctionnalités (filtrage, faceting, tri de recherche de texte intégral, etc.) s’appliquent à des champs. Pour tout attribut que vous ne spécifiez pas, la valeur par défaut sera pour activer la fonctionnalité de recherche correspondante, sauf si vous la désactivez spécifiquement.

Dans notre exemple, nous avons nommé notre index « hôtels » et défini nos champs comme suit :

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Nous avons choisi avec précaution d’indexer les attributs de chaque champ basé sur la manière dont nous ils seront utilisés dans une application. Par exemple, `hotelId` est une clé unique que vous recherchez probablement des hôtels ne saura pas, donc nous désactiver la recherche de texte intégral de ce champ en définissant les gens `searchable` à `false`, qui économise de l’espace dans l’index.

Veuillez noter qu’exactement un champ dans l’index de type `Edm.String` doit être le désigné comme champ de « clé ».

La définition d’index utilise un analyseur de langage personnalisé pour le `description_fr` , car il est conçu pour stocker du texte Français. Consultez [la langue prise en charge de la rubrique sur le site MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx) , ainsi que le correspondant [le billet de blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) pour plus d’informations sur les analyseurs de langage.

## <a name="iii-issue-the-http-request"></a>III. Émettre la demande HTTP
1. À l’aide de votre définition de l’index en tant que corps de la demande, d’émettre une demande HTTP POST à votre URL de point de terminaison de service de recherche d’Azure. Dans l’URL, veillez à utiliser le nom de votre service en tant que le nom d’hôte et mettez appropriés `api-version` comme paramètre de chaîne de requête (la version actuelle de l’API est `2015-02-28` au moment de la publication de ce document).
2. Dans les en-têtes de demande, spécifiez la `Content-Type` en tant que `application/json`. Vous devrez également fournir la clé d’administration de votre service que vous avez identifié à l’étape I dans le `api-key` en-tête.


Vous devez fournir votre propre clé d’api et le nom de service pour émettre la demande ci-dessous :


    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [api-key]


Pour une demande réussie, vous devriez voir le code d’état 201 (créé). Pour plus d’informations sur la création d’un index via l’API REST, consultez la référence des API sur [MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx). Pour plus d’informations sur les autres codes d’état HTTP qui peut être retourné en cas de défaillance, consultez [codes d’état HTTP (recherche Azure)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

Lorsque vous avez terminé avec un index et que vous souhaitez supprimer, génère une demande HTTP DELETE. Par exemple, c’est comment nous supprime l’index de « hôtels » :

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2015-02-28
    api-key: [api-key]


## <a name="next"></a>Suivant
Création d’un index de recherche d’Azure, vous serez prêt à [charger votre contenu dans l’index](search-what-is-data-import.md) afin que vous pouvez lancer la recherche de vos données.
