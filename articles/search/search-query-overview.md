<properties
    pageTitle="Interroger votre Index de recherche Azure | Microsoft Azure | Service de recherche de nuage hébergé"
    description="Créer une requête de recherche dans recherche Azure et utilisez les paramètres de recherche pour filtrer et trier les résultats de recherche."
    services="search"
    manager="jhubbard"
    documentationCenter=""
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

# <a name="query-your-azure-search-index"></a>Interroger votre index de recherche d’Azure
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-query-overview.md)
- [Portail](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTE](search-query-rest-api.md)

Lors de l’envoi de demandes de recherche à la recherche d’Azure, il existe un certain nombre de paramètres qui peuvent être spécifiées avec les mots réels qui sont tapés dans la zone de recherche de votre application. Ces paramètres de requête vous permettent de vous permettent d’obtenir un contrôle plus profond de l’expérience de la recherche de texte intégral.

Vous trouverez ci-dessous une liste qui explique brièvement les utilisations courantes des paramètres de requête de recherche d’Azure. Pour obtenir une couverture complète des paramètres de requête et de leur comportement, consultez les pages détaillées pour [l’API REST](https://msdn.microsoft.com/library/azure/dn798927.aspx) et le [Kit de développement .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters_properties.aspx).

## <a name="types-of-queries"></a>Types de requêtes

Recherche Azure offre de nombreuses options pour créer des requêtes extrêmement puissantes. Les deux principaux types de requête que vous allez utiliser sont `search` et `filter`. A `search` requête recherche un ou plusieurs termes dans tous les champs de _recherche_ dans l’index et fonctionne de la manière que vous vous attendez à un moteur de recherche comme Google ou Bing pour travailler. A `filter` requête évalue une expression booléenne sur toutes les _campagnes_ champs dans un index. Contrairement à `search` requêtes, `filter` requêtes correspond à la valeur exacte d’un champ, ce qui signifie qu’ils sont sensibles à la casse pour les champs de type chaîne.

Vous pouvez utiliser des filtres et des recherches ensemble ou séparément. Si vous les utilisez conjointement, le filtre est appliqué en premier à la totalité de l’index et la recherche est effectuée sur les résultats du filtre. Les filtres peuvent donc être une technique utile pour améliorer les performances de la requête puisqu’elles réduisent l’ensemble de documents qui doit traiter la requête de recherche.

La syntaxe des expressions de filtre est un sous-ensemble du [langage de filtre OData](https://msdn.microsoft.com/library/azure/dn798921.aspx). Pour les requêtes de recherche, vous pouvez utiliser la [syntaxe simplifiée](https://msdn.microsoft.com/library/azure/dn798920.aspx) ou la [syntaxe de requête Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) qui sont présentés ci-dessous.

### <a name="simple-query-syntax"></a>Syntaxe de requête simple
La [syntaxe de requête simple](https://msdn.microsoft.com/library/azure/dn798920.aspx) est le langage de requête par défaut utilisé dans la recherche d’Azure. La syntaxe de requête simple prend en charge un certain nombre d’opérateurs de recherche commune, y compris de AND, OR, NOT, phrase, suffixe et les opérateurs de priorité.

### <a name="lucene-query-syntax"></a>Syntaxe de requête Lucene
La [syntaxe de requête Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) vous permet d’utiliser le langage de requête expressif et largement adopté développé dans le cadre de [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

À l’aide de cette syntaxe de requête vous permet d’atteindre facilement les capacités suivantes : [à portée d’un champ requêtes](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fields), [recherche floue](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fuzzy), [recherche de proximité](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_proximity), [terme dynamiser](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_termboost), [recherche d’expression régulière](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_regex), [recherche par caractères génériques](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_wildcard), [les principes de base de syntaxe](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_syntax)et [requêtes à l’aide d’opérateurs booléens](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_boolean).



## <a name="ordering-results"></a>Tri des résultats
Lors de la réception des résultats d’une requête de recherche, vous pouvez demander que Azure recherche sert les résultats triés par valeurs dans un champ spécifique. Par défaut, les commandes Azure recherche les résultats de la recherche selon le rang d’un résultat de recherche de chaque document, qui est dérivée de [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Si vous souhaitez que la recherche d’Azure pour renvoyer les résultats classés par une valeur autre que le résultat de la recherche, vous pouvez utiliser la `orderby` paramètre de recherche. Vous pouvez spécifier la valeur de la `orderby` paramètre pour inclure les noms de champ et les appels à la [ `geo.distance()` fonction](https://msdn.microsoft.com/library/azure/dn798921.aspx) pour les valeurs de géographique. Chaque expression peut être suivie par `asc` pour indiquer que les résultats sont demandés dans l’ordre croissant, et `desc` pour indiquer que les résultats sont demandés dans l’ordre décroissant. Le classement par défaut par ordre croissant.

## <a name="paging"></a>La pagination
Recherche Azure facilite l’implémenter la pagination des résultats de recherche. À l’aide de la `top` et `skip` paramètres, vous pouvez facilement émettre des requêtes de recherche qui vous permettent de recevoir l’ensemble des résultats de la recherche en sous-ensembles plus faciles à gérer, ordonnées qui permettent facilement les pratiques de l’interface utilisateur de recherche pertinents. Lors de la réception de ces plus petits sous-ensembles de résultats, vous pouvez également recevoir le nombre de documents dans l’ensemble des résultats de la recherche.

Vous pouvez en savoir plus à propos de la pagination des résultats de la recherche dans l’article de [la page résultats de recherche d’Azure](search-pagination-page-layout.md).


## <a name="hit-highlighting"></a>Mise en évidence de positionnement
Recherche d’Azure, mettant l’accent sur la portion exacte des résultats de recherche qui correspondent à la requête de recherche est très simple à l’aide de la `highlight`, `highlightPreTag`, et `highlightPostTag` paramètres. Vous pouvez spécifier les champs de _recherche_ doit avoir leur texte correspondant mis en évidence ainsi que par les balises de la chaîne exacte à ajouter pour le début et la fin du texte correspondant à cette recherche Azure renvoie.
