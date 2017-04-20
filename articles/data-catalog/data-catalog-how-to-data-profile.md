<properties
    pageTitle="Comment les sources de données de profil de données"
    description="Article d’apprentissage mettant en évidence comment inclure des profils de données au niveau de la table et de la colonne lors de l’enregistrement des sources de données dans le catalogue de données Azure et l’utilisation de profils de données à comprendre les sources de données."
    services="data-catalog"
    documentationCenter=""
    authors="spelluru"
    manager="NA"
    editor=""
    tags=""/>
<tags
    ms.service="data-catalog"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-catalog"
    ms.date="09/13/2016"
    ms.author="spelluru"/>

# <a name="data-profile-data-sources"></a>Sources de données de profil de données

## <a name="introduction"></a>Introduction

**Catalogue de données de Microsoft Azure** est un service en nuage entièrement géré qui sert d’un système d’enregistrement et système de détection des sources de données d’entreprise. En d’autres termes, le **Catalogue de données Azure** consiste à aider les utilisateurs à découvrir, à comprendre et à utiliser des sources de données et aider les entreprises à tirer davantage de valeur à partir de leurs données. Lorsqu’une source de données est enregistrée avec le **Catalogue de données Azure**, ses métadonnées sont copiée et par le service d’indexation, mais l’article ne se termine pas il.

La fonctionnalité de **Profilage des données** de **Catalogue de données Azure** examine les données à partir de sources de données prises en charge dans votre catalogue et collecte les statistiques et les informations relatives à ces données. Il est facile d’inclure un profil de vos données. Lorsque vous enregistrez un élément de données, cliquez sur **Inclure les profils de données** dans l’outil de l’enregistrement de source de données.

## <a name="what-is-data-profiling"></a>Quel est le profilage des données

Profilage des données examine les données dans la source de données en cours d’enregistrement et collecte les statistiques et les informations relatives à ces données. Lors de la découverte de source de données, ces statistiques peuvent vous aider à déterminer l’adéquation des données à résoudre leur problème de l’entreprise.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Sources de données suivantes prennent en charge le profilage des données :

- Les vues et les tables SQL Server (y compris la base de données SQL Azure et d’Azure SQL Data Warehouse)
- Les vues et les tables oracle
- Les vues et les tables de Teradata
- Tables de la ruche

Profils de données, y compris lors de l’enregistrement des données de l’entreprise permet aux utilisateurs de répondent aux questions concernant les sources de données, y compris :

-   Peut être utilisé pour résoudre le problème de mon entreprise ?
-   Les données sont conformes aux normes particulières ou des modèles ?
-   Quelles sont les anomalies de la source de données ?
-   Quels sont les défis possibles d’intégrer ces données dans mon application ?

> [AZURE.NOTE] Vous pouvez aussi ajouter une documentation à une immobilisation pour décrire comment les données peuvent être intégrées dans une application. Apprendre [à documenter des sources de données](data-catalog-how-to-documentation.md).


<a name="howto"/>
## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Comment inclure un profil de données lors de l’enregistrement d’une source de données

Il est facile d’inclure un profil de votre source de données. Lorsque vous enregistrez une source de données, dans le panneau **objets pour être enregistrés** de l’outil de l’enregistrement de source de données, cliquez sur **Inclure les profils de données**.

![](media\data-catalog-data-profile\data-catalog-register-profile.png)

Pour en savoir plus sur l’inscription des sources de données, reportez-vous à la section [comment enregistrer des sources de données](data-catalog-how-to-register.md) et [mise en route de catalogue de données Azure](data-catalog-get-started.md).


## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtrage sur des ressources de données qui contiennent des profils de données
Pour découvrir les ressources de données qui incluent un profil des données, vous pouvez inclure `has:tableDataProfiles` ou `has:columnsDataProfiles` comme l’un des termes de la recherche.

> [AZURE.NOTE] **Profil de données incluent** la sélection dans l’outil de l’enregistrement de source de données contient les tables et les informations de profil au niveau de la colonne. Toutefois, l’API de catalogue de données permet des actifs de données à enregistrer avec un seul jeu d’informations de profil inclus.

## <a name="viewing-data-profile-information"></a>Affichage des informations de profil de données

Une fois que vous trouvez une source de données avec un profil, vous pouvez afficher les détails de profil de données. Pour afficher les données de profil, sélectionnez un élément de données et sur les **Données de profil** dans la fenêtre de portail de catalogue de données.

![](media\data-catalog-data-profile\data-catalog-view.png)

Un profil des données de **Catalogue de données Azure** affiche la table et y compris les informations de profil de colonne :

### <a name="object-data-profile"></a>Profil de données objet

-   Nombre de lignes
-   Taille de la table
-   Lorsque l’objet a été mis à jour

### <a name="column-data-profile"></a>Profil des données de colonne

- Type de données de colonne
- Nombre de valeurs distinctes
- Nombre de lignes contenant des valeurs NULL
- Minimum, maximum, moyen et écart type des valeurs de colonne

## <a name="summary"></a>Résumé
Profilage des données fournissent des statistiques et des informations sur les éléments de données inscrits pour vous aider à déterminer l’adéquation des données pour résoudre les problèmes de l’entreprise. Annotation et documentation des sources de données, des profils de données permettent aux utilisateurs d’une bonne compréhension de vos données.


## <a name="see-also"></a>Voir aussi
-   [Comment enregistrer des sources de données](data-catalog-how-to-register.md)
-   [Mise en route de catalogue de données Azure](data-catalog-get-started.md)
