<properties
    pageTitle="Comment modéliser les types de données complexes dans Azure recherche | Recherche de Microsoft Azure"
    description="Imbriqués ou des structures de données hiérarchiques peuvent être modélisées dans un index de recherche d’Azure à l’aide du jeu de lignes aplati et type de données de Collections."
    services="search"
    documentationCenter=""
    authors="LiamCa"
    manager="pablocas"
    editor=""
    tags="complex data types; compound data types; aggregate data types"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="09/07/2016"
    ms.author="liamca"
/>

# <a name="how-to-model-complex-data-types-in-azure-search"></a>Comment les types de données complexes dans Azure recherche de modèle

Groupes de données externes utilisées pour remplir un index de recherche d’Azure parfois incluent sous-structures hiérarchiques ou imbriqués qui ne pas cassant soigneusement dans un ensemble de lignes tabulaire. Exemples de telles structures peuvent inclure plusieurs emplacements et numéros de téléphone pour un seul client, plusieurs couleurs et les tailles pour une référence unique, plusieurs auteurs d’un livre unique et ainsi de suite. En termes de modélisation, vous pouvez voir ces structures appelés *types de données complexes*, *composés des types de données*, *types de données composites*ou *regrouper des types de données*, pour citer que quelques-uns.

Types de données complexes ne sont pas pris en charge en mode natif dans recherche d’Azure, mais une solution éprouvée inclut un processus en deux étapes de mise à plat de la structure et en utilisant un type de données de **Collection** pour reconstituer la structure intérieure. La technique décrite dans cet article permet le contenu à rechercher, à facettes, filtrés et triés.

## <a name="example-of-a-complex-data-structure"></a>Exemple de structure de données complexes

En règle générale, les données en question se trouvent sous la forme d’un ensemble de documents JSON ou XML, ou en tant qu’éléments dans un magasin de NoSQL comme DocumentDB. Structurellement, le défi provient d’avoir plusieurs éléments enfants qui doivent être recherchés et filtrés.  Comme point de départ pour illustrer la solution de contournement, prenez le document suivant de JSON qui répertorie un ensemble de contacts, par exemple :

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Les champs nommés 'id', 'nom' et 'société' peuvent facilement être mappés un à un en tant que champs dans un index de recherche d’Azure, le champ « lieux » contient un tableau d’emplacements, ayant à la fois un ensemble de codes d’emplacement, ainsi que des descriptions d’emplacement. Étant donné que la recherche d’Azure n’a pas un type de données qui prend en charge cela, nous devons différent pour ce modèle de recherche d’Azure. 

> [AZURE.NOTE] Cette technique est également décrite par Kirk Evans dans un billet de blog [DocumentDB de l’indexation de recherche d’Azure](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), qui affiche une technique appelée « mise à plat de données », dans laquelle vous aurait un champ appelé `locationsID` et `locationsDescription` qui sont des [collections](https://msdn.microsoft.com/library/azure/dn798938.aspx) (ou un tableau de chaînes).   

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Partie 1 : Aplatir le tableau en champs individuels

Pour créer un index de recherche d’Azure qui s’adapte à ce groupe de données, créer des champs pour la sous-structure imbriquée : `locationsID` et `locationsDescription` avec un type de données de [collections](https://msdn.microsoft.com/library/azure/dn798938.aspx) (ou un tableau de chaînes). Ces champs vous souhaitez indexer les valeurs '1' et '2' dans le `locationsID` champ de valeurs '3' & '4' et John Smith dans le `locationsID` champ Jen Campbell.  

Vos données dans Azure recherche ressemblerait à ceci : 

![exemple de données 2 lignes](./media/search-howto-complex-data-types/sample-data.png)


## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Partie 2 : Ajouter un champ de la collection dans la définition d’index

Dans le schéma d’index, les définitions de champ peuvent ressembler à cet exemple.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Valider les comportements de recherche et vous pouvez également étendre l’index

Si la création de l’index et de chargement des données, vous pouvez à présent tester la solution pour vérifier l’exécution des requêtes de recherche sur le groupe de données. Chaque champ de la **collection** doit être **indexé**, **filtrable** et **facetable**. Vous serez en mesure d’exécuter les requêtes comme :

* Trouver toutes les personnes qui travaillent au siège Adventureworks' '.
* Obtenir le nombre de personnes qui travaillent dans un bureau à domicile' '.  
* Des personnes qui travaillent dans un bureau d’accueil, afficher les autres bureaux qu’ils fonctionnent avec un nombre de personnes dans chaque emplacement.  

Où cette technique réduit à néant est lorsque vous devez effectuer une recherche qui combine le code d’emplacement ainsi que la description de l’emplacement. Par exemple :

* Trouver toutes les personnes où ils disposent d’un bureau à domicile et a un code d’emplacement de 4.  

Si vous vous rappelez le contenu d’origine ressemblait à ceci :

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

Toutefois, maintenant que nous avons séparé les données dans des champs distincts, nous ne pouvons pas if de connaître le Bureau à domicile pour Jen Campbell concerne `locationsID 3` ou `locationsID 4`.  

Pour gérer ce cas, définissez un autre champ dans l’index qui combine toutes les données en une collection unique.  Dans notre exemple, nous appellerons ce champ `locationsCombined` et nous allons séparer le contenu avec un `||` vous pouvez choisir n’importe quel séparateur que vous pensez serait un jeu unique de caractères pour votre contenu. Par exemple : 

![exemple de données 2 lignes avec séparateur](./media/search-howto-complex-data-types/sample-data-2.png)

L’utilisation de cette `locationsCombined` champ, nous pouvons maintenant prendre en charge davantage de requêtes, telles que :

* Afficher un nombre de personnes qui travaillent dans un « bureau à domicile » avec l’Id d’emplacement '4'.  
* Rechercher des personnes qui travaillent dans un bureau de maison avec emplacement Id '4'. 

## <a name="limitations"></a>Limitations

Cette technique est utile pour un certain nombre de scénarios, mais il n’est pas applicable dans tous les cas.  Par exemple :

1. Si vous n’avez pas un ensemble statique de champs dans le type de données complexe et il n’a aucun moyen de mapper tous les types possibles pour un seul champ. 
2. Les objets imbriqués de mise à jour nécessite un surcroît de travail pour déterminer exactement ce qui doit être mis à jour dans l’index de recherche d’Azure

## <a name="sample-code"></a>Exemples de code

Vous pouvez voir un exemple sur la manière d’un ensemble de données JSON complexe d’index en recherche d’Azure et d’effectuer un certain nombre de requêtes sur ce groupe de données à cette [mis en pension de GitHub](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Étape suivante

[Votez pour une prise en charge native pour les types de données complexes](https://feedback.azure.com/forums/263029-azure-search) sur les UserVoice de recherche Azure page et fournir une entrée supplémentaire que vous souhaitez prendre en compte concernant la mise en œuvre de la fonctionnalité. Vous pouvez également atteindre me directement sur Twitter à @liamca.


 