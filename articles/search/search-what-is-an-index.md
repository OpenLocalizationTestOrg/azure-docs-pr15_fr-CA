<properties
    pageTitle="Créer un index de recherche d’Azure | Microsoft Azure | Service de recherche de nuage hébergé"
    description="Ce qu’est un index de recherche d’Azure et comment l’utiliser ?"
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

# <a name="create-an-azure-search-index"></a>Créer un index de recherche d’Azure
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-what-is-an-index.md)
- [Portail](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTE](search-create-index-rest-api.md)

## <a name="what-is-an-index"></a>Ce qui est un index ?

Un *index* est un magasin persistant de *documents* et d’autres constructions utilisées par un service de recherche d’Azure. Un document est une unité de données de recherche dans l’index. Par exemple, un revendeur de commerce électronique peut avoir un document pour chaque élément qu’ils vendent, une organisation de médias peut avoir un document pour chaque article, les etc.. Mappage de ces concepts plus familiers équivalents de base de données : un *index* est conceptuellement semblable à une *table*, et *les documents* sont à peu près équivalent à des *lignes* dans une table.

Lorsque vous ajoutez/téléchargement des documents, envoyez des requêtes de recherche d’Azure, vous soumettez vos demandes à un index spécifique dans votre service de recherche.

## <a name="field-types-and-attributes-in-an-azure-search-index"></a>Types de champ et les attributs d’un index de recherche d’Azure

Lorsque vous définissez votre schéma, vous devez spécifier le nom, type et les attributs de chaque champ dans l’index. Le type de champ classifie les données qui sont stockées dans ce champ. Pour spécifier la façon dont le champ est utilisé, les attributs sont définis sur des champs individuels. Les tableaux suivants d’énumérer les types et les attributs que vous pouvez spécifier.


### <a name="field-types"></a>Types de champ
|Type de|Description|
|------------|-----------|
|*Edm.String*|Texte qui peut éventuellement être sous forme de jetons pour la recherche de texte intégral (césure de mots, recherche de radical, etc.).|
|*Collection(EDM.String)*|Une liste de chaînes qui peuvent éventuellement être sous forme de jetons pour la recherche de texte intégral. Il n’existe aucune limite théorique supérieure sur le nombre d’éléments dans une collection, mais la limite supérieure de 16 Mo sur la taille de la charge s’applique aux collections.|
|*Edm.Boolean*|Contient des valeurs true/false.|
|*Edm.Int32*|valeurs de l’entier de 32 bits.|
|*Edm.Int64*|valeurs de l’entier 64 bits.|
|*Edm.Double*|Double précision des données numériques.|
|*Edm.DateTimeOffset*|Les valeurs de temps représentées dans le format OData V4 de date (par exemple, `yyyy-MM-ddTHH:mm:ss.fffZ` ou `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`).|
|*Edm.GeographyPoint*|Un point représentant un emplacement géographique de la planète.|

Vous trouverez des informations plus détaillées à propos [prise en charge des types de données sur MSDN de la recherche d’Azure](https://msdn.microsoft.com/library/azure/dn798938.aspx).



### <a name="field-attributes"></a>Attributs de champ
|Attribut|Description|
|------------|-----------|
|*Clé*|Chaîne qui fournit l’ID unique de chaque document, utilisé pour la recherche de document. Chaque index doit avoir une clé. Seul un champ peut être la clé et son type doit être défini sur Edm.String.|
|*Récupérables*|Spécifie si un champ peut être retourné dans un résultat de recherche.|
|*Filtrables*|Permet le champ à utiliser dans des requêtes de filtre.|
|*Peut être trié*|Permet à une requête pour trier les résultats de la recherche à l’aide de ce champ.|
|*Facetable*|Permet à un champ à utiliser pour le filtrage des utilisateur autonome dans une structure de [navigation à facettes](search-faceted-navigation.md) . Les champs contenant des valeurs répétitives que vous pouvez utiliser pour regrouper plusieurs documents (par exemple, plusieurs documents qui relèvent d’un catégorie de service ou d’une marque unique) fonctionnent généralement mieux en tant que facettes.|
|*Recherche*|Marque le champ de texte intégral avec possibilité de recherche.|

Vous trouverez des informations plus détaillées sur les [attributs d’index sur le site MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx)de la recherche d’Azure.



## <a name="guidance-for-defining-an-index-schema"></a>Conseils pour la définition d’un schéma d’index

Lorsque vous concevez votre index, prenez votre temps dans la phase de planification pour y réfléchir chaque décision. Il est important de le maintenir votre recherche utilisateur expérience et des besoins à l’esprit lorsque vous concevez votre index comme les [attributs corrects](https://msdn.microsoft.com/library/azure/dn798941.aspx)doit être affecté à chaque champ. Modification d’un index après le déploiement implique la reconstruction et de rechargement des données.


Si les besoins de stockage de données changent avec le temps, vous pouvez augmenter ou diminuer la capacité en ajoutant ou supprimant des partitions. Pour plus d’informations, consultez [Gérer votre service de recherche dans Azure](search-manage.md) ou des [Limites de Service](search-limits-quotas-capacity.md).
