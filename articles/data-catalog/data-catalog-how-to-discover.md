<properties
   pageTitle="Comment découvrir les sources de données | Microsoft Azure"
   description="Article d’apprentissage mise en surbrillance de la façon de découvrir les ressources de données enregistré avec le catalogue de données Azure, y compris la recherche et le filtrage à l’aide de l’accès de mise en surbrillance des fonctionnalités du portail du catalogue de données Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/04/2016"
   ms.author="maroche"/>

# <a name="how-to-discover-data-sources"></a>Comment découvrir les sources de données

## <a name="introduction"></a>Introduction
**Catalogue de données de Microsoft Azure** est un service en nuage entièrement géré qui sert d’un système d’enregistrement et système de détection des sources de données d’entreprise. En d’autres termes, le **Catalogue de données Azure** consiste à aider les utilisateurs à découvrir, à comprendre et à utiliser des sources de données et aider les entreprises à tirer davantage de valeur à partir de leurs données. Une fois qu’une source de données a été enregistrée avec le **Catalogue de données Azure**, ses métadonnées sont indexée par le service, afin que les utilisateurs peuvent facilement rechercher pour découvrir les données que dont ils ont besoin.

## <a name="searching-and-filtering"></a>Recherche et filtrage

Recherche dans le **Catalogue de données Azure** utilise deux mécanismes principaux : recherche et le filtrage.

La recherche est conçue pour être intuitive et puissante – par défaut, les termes de recherche sont comparés à une propriété dans le catalogue, y compris les annotations fourni par l’utilisateur.

Le filtrage est conçu pour compléter la recherche. Les utilisateurs peuvent sélectionner des caractéristiques spécifiques telles que les experts, type de source de données, type d’objet et les balises, pour afficher uniquement les ressources de données correspondante et pour limiter les résultats de recherche correspondant ainsi que les actifs.

À l’aide d’une combinaison de la recherche et le filtrage, les utilisateurs peuvent naviguer rapidement les sources de données qui ont été enregistrés avec le **Catalogue de données Azure** pour découvrir les sources de données que dont ils ont besoin.

## <a name="search-syntax"></a>Syntaxe de recherche

Bien que la recherche en texte libre par défaut est simple et intuitive, les utilisateurs permet également syntaxe de recherche de **Catalogue de données Azure**du pour mieux contrôler les résultats de la recherche. Recherche dans le **Catalogue de données Azure** prend en charge les techniques suivantes :

| Technique                 | Utilisation                                                                                                                                     | Exemple                                                   |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| Recherche de base              | Recherche de base à l’aide d’un ou plusieurs termes de recherche. Les résultats sont des actifs qui correspondent à n’importe quelle propriété avec un ou plusieurs des mentions visées. | données de vente                                                |
| Propriété étendue          | Retourner uniquement les sources de données où le terme de recherche est mis en correspondance avec la propriété spécifiée                                                   | nom : finance                                              |
| Opérateurs booléens         | Élargir ou affiner une recherche à l’aide d’opérations booléennes                                                                                     | intérêts non d’entreprise                                     |
| Avec la parenthèse de regroupement | Utilisez des parenthèses pour des articles de groupe de la requête pour isoler logiquement, en particulier en association avec les opérateurs booléens              | nom : intérêts et (balises : Q1 ou balises : 2e trimestre) |
| Opérateurs de comparaison      | Utiliser des comparaisons de l’égalité pour les propriétés qui ont des types de données numérique et date                                                | modifiedTime > « 05/11/2014 »                                 |

Pour plus d’informations sur la recherche dans le **Catalogue de données Azure** , consultez [https://msdn.microsoft.com/library/azure/mt267594.aspx](https://msdn.microsoft.com/library/azure/mt267594.aspx).

## <a name="hit-highlighting"></a>Mise en évidence de positionnement
Lorsque vous affichez les résultats de la recherche, les propriétés affichées correspondant aux termes de recherche spécifié : le nom de la ressource, description, des balises – seront mise en surbrillance pour le rendre plus facile à identifier la cause d’une ressource donnée a été retournée par une recherche donnée.

> [AZURE.NOTE] Les utilisateurs peuvent transformer d’accès mise en surbrillance désactiver si vous le souhaitez, en utilisant le commutateur « Sélection » dans le **Catalogue de données Azure** portal.

Lors de l’affichage des résultats de la recherche, il ne peut pas toujours être évident pourquoi un élément de données est inclus, même avec la mise en surbrillance activé. Parce que toutes les propriétés sont recherchées par défaut, un élément de données peut être renvoyé en raison d’une correspondance sur une propriété au niveau des colonnes. Et étant donné que plusieurs utilisateurs peuvent annoter des ressources avec leurs propres balises et descriptions de, pas toutes les métadonnées peuvent être affiché dans la liste des résultats de la recherche.

Affichage en mosaïque dans la valeur par défaut, chaque mosaïque affiché dans les résultats de la recherche inclura une icône « Affichage terme recherché correspond au », ce qui permet à l’utilisateur pour afficher rapidement le nombre de correspondances et leur emplacement et d’y accéder si vous le souhaitez.

 ![Mise en évidence de positionnement et de recherche dans le portail du catalogue de données Azure](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Résumé
Enregistrement d’une source de données avec le **Catalogue de données Azure** facilite cette source de données à découvrir et à comprendre, en copiant les métadonnées structurelles et descriptifs à partir de la source de données dans le service de catalogue. Une fois qu’une source de données a été enregistrée, les utilisateurs peuvent découvrir à l’aide de filtrage et de recherche à partir du portail de **Catalogue de données Azure** .

## <a name="see-also"></a>Voir aussi
- Didacticiel de [Mise en route de catalogue de données Azure](data-catalog-get-started.md) pour plus d’informations détaillées sur la façon de découvrir les sources de données.
