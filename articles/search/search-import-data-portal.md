<properties
    pageTitle="Importer des données à la recherche d’Azure à l’aide d’indexeurs dans le portail Azure | Microsoft Azure | Service de recherche de nuage hébergé"
    description="Utilisez Azure recherche Assistant Importer les données dans le portail Azure à des données d’analyse de stockage, stockage de table, de la base de données SQL et SQL Server sur Azure VM Azure Blob."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="Azure Portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="heidist"/>

# <a name="import-data-to-azure-search-using-the-portal"></a>Importer des données à la recherche d’Azure via le portail

Le portail Azure fournit un Assistant **Importer des données** dans le tableau de bord de recherche d’Azure pour le chargement des données dans un index. 

  ![Importer des données dans la barre de commande][1]

En interne, l’Assistant configure et appelle un *indexeur*, d’automatisation de plusieurs étapes du processus d’indexation : 

- Se connecter à une source de données externe dans l’abonnement Azure en cours
- Génère automatiquement un schéma d’index en fonction de la structure des données source
- Créer des documents basés sur un jeu de lignes récupéré à partir de la source de données
- Télécharger des documents dans l’index dans votre service de recherche

Vous pouvez essayer ce flux de travail à l’aide d’exemples de données dans DocumentDB. Pour obtenir des instructions, consultez [mise en route avec la recherche dans le portail Azure Azure](search-get-started-portal.md) .

## <a name="data-sources-supported-by-the-import-data-wizard"></a>Sources de données prises en charge par l’Assistant Importation de données

L’Assistant Importation de données prend en charge les sources de données suivantes : 

- Base de données SQL Azure
- Données relationnelles de SQL Server sur un ordinateur virtuel d’Azure
- DocumentDB Azure
- Stockage de Blob Azure (en aperçu)
- Stockage par Table Azure (en aperçu)

Un dataset aplati est une entrée requise. Vous ne pouvez importer à partir d’une seule table, une vue de base de données ou une structure de données équivalent. Vous devez créer cette structure de données avant d’exécuter l’Assistant.

Notez que quelques unes des indexeurs sont toujours en mode Aperçu, ce qui signifie que la définition de l’indexeur est soutenue par la version d’évaluation de l’API. Pour plus d’informations et des liens, consultez [vue d’ensemble de l’indexeur](search-indexer-overview.md) .

## <a name="connect-to-your-data"></a>Se connecter à vos données

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et tableau de bord de service en cours. Vous pouvez cliquer sur les **services de recherche** dans la barre de raccourcis pour afficher les services existants de l’abonnement en cours. 

2. Cliquez sur **Importer des données** dans la barre de commande pour la diapositive, ouvrez la blade d’importer des données.  

3. Cliquez sur **se connecter à vos données** afin de spécifier une définition de source de données utilisée par un indexeur. Pour les sources de données intra-abonnement, l’Assistant peut généralement détecter et lire les informations de connexion, en réduisant les exigences de configuration globale.

| | |
|--------|------------|
|**Source de données existante** | Si vous avez déjà des indexeurs définies dans votre service de recherche, vous pouvez sélectionner une définition de source de données existante pour importer un autre.|
|**Base de données SQL Azure** | Nom du service, les informations d’identification d’un utilisateur de base de données avec l’autorisation de lecture et un nom de base de données peuvent être spécifié sur la page, ou via une chaîne de connexion ADO.NET. Choisissez l’option de chaîne de connexion pour afficher ou personnaliser les propriétés. <br/><br/>La table ou la vue qui fournit l’ensemble de lignes doit être spécifié sur la page. Cette option s’affiche une fois la connexion établie, en donnant une liste déroulante de sorte que vous pouvez effectuer une sélection.|
|**SQL Server sur une machine virtuelle Azure** | Spécifiez un nom complet, l’ID utilisateur et mot de passe et base de données sous la forme d’une chaîne de connexion. Pour utiliser cette source de données, vous devez avoir préalablement installé un certificat dans le magasin local qui crypte la connexion. <br/><br/>La table ou la vue qui fournit l’ensemble de lignes doit être spécifié sur la page. Cette option s’affiche une fois la connexion établie, en donnant une liste déroulante de sorte que vous pouvez effectuer une sélection.
|**DocumentDB** |Exigences incluent le compte, la base de données et la collection. Tous les documents dans la collection doit être inclus dans l’index. Vous pouvez définir une requête pour uniformiser ou filtrer l’ensemble de lignes, ou pour détecter les documents modifiés pour les opérations d’actualisation de données ultérieures.|
|**Stockage des objets Blob Azure** | Pré-requis du compte de stockage et d’un conteneur. Le cas échéant, si les noms de blob suivent une convention d’affectation de noms virtuelle à des fins de regroupement, vous pouvez spécifier la partie répertoire virtuel, du nom sous la forme d’un dossier sous le conteneur. Pour plus d’informations, reportez-vous à la section [Indexation du stockage Blob (aperçu)](search-howto-indexing-azure-blob-storage.md) . |
|**Stockage par Table Azure** | Pré-requis du compte de stockage et un nom de table. Si vous le souhaitez, vous pouvez spécifier une requête pour extraire un sous-ensemble des tables. Pour plus d’informations, consultez [Stockage de Table d’indexation (aperçu)](search-howto-indexing-azure-tables.md) . |

## <a name="customize-target-index"></a>Personnaliser les index de la cible

Un index préliminaire est généralement déduit à partir du groupe de données. Ajouter, modifier ou supprimer des champs pour terminer le schéma. En outre, définir des attributs au niveau des champs pour déterminer le comportement de recherche ultérieures.

1. **Index cible de personnaliser**, spécifiez le nom et une **clé** utilisée pour identifier de façon unique chaque document. La clé doit être une chaîne. Si les valeurs de champ comprennent des espaces ou des tirets, veillez à définir les options avancées dans l' **importation de vos données** afin de supprimer le contrôle de validation pour ces caractères.

2. Vérifiez et modifiez les champs restants. Type et nom de champ sont généralement remplis pour vous. Vous pouvez modifier le type de données.

3. Définir les attributs d’index pour chaque champ :

 - Récupérables retourne le champ dans les résultats de la recherche.
 - Le champ d’être référencés dans les expressions de filtre permet de filtrables.
 - Triable permet le champ à utiliser dans un tri.
 - Facetable Active le champ pour la navigation à facettes.
 - Possibilité de recherche permet la recherche de texte intégral.
  
4. Si vous souhaitez spécifier un analyseur de langage au niveau des champs, cliquez sur l’onglet **Analyseur** . Analyseurs de langage uniquement peuvent être spécifiés pour l’instant. À l’aide d’un analyseur personnalisé ou un analyseur de langage non comme mot-clé, modèle et ainsi de suite, nécessitera le code.

 - Cliquez sur la **possibilité de recherche** pour désigner la recherche de texte intégral sur le champ et activer la liste déroulante d’analyseur.
 - Choisissez l’analyseur que vous souhaitez. Pour plus d’informations, consultez [Création d’un index des documents dans plusieurs langues](search-language-support.md) .

5. Cliquez sur le **Suggester** pour activer les suggestions de saisie semi-automatique de requête sur les champs sélectionnés.


## <a name="import-your-data"></a>Importation de vos données

1. Dans **importation de vos données**, fournir un nom pour l’indexeur. Rappelez-vous que le produit de l’Assistant Importation de données est un indexeur. Ultérieurement, si vous souhaitez afficher ou le modifier, vous devez le sélectionner à partir du portail au lieu en exécutant de nouveau l’Assistant. 

2. Spécifiez la planification, qui est basée sur le fuseau horaire régional dans lequel le service est mis en service.

3. Définir des options avancées pour spécifier des seuils sur l’indexation si puisse continuer si un document est supprimé. En outre, vous pouvez spécifier si les champs de **clé** sont autorisés à contenir des espaces et des barres obliques.  

## <a name="edit-an-existing-indexer"></a>Modifier un indexeur existant

Dans le tableau de bord de service, double-cliquez sur la mosaïque de l’indexeur à la diapositive une liste de tous les indexeurs créé pour votre abonnement. Double-cliquez sur un des indexeurs pour exécuter, modifier ou le supprimer. Vous pouvez remplacer l’index existant par un autre, modifier la source de données et définir les options de seuils d’erreur pendant l’indexation.

## <a name="edit-an-existing-index"></a>Modifier un index existant

Dans Azure, recherche, mises à jour de structurels à un index nécessite une reconstruction d’index, qui se compose de la suppression de l’index, recréation de l’index et de rechargement des données. Les mises à jour structurels comprennent les modification d’un type de données et de renommer ou de supprimer un champ.

Les modifications qui ne nécessitent pas une reconstruction comprennent l’ajout d’un nouveau champ, modification des profils de score, suggesters la modification ou la modification des analyseurs de langage. Pour plus d’informations, consultez [Index de mise à jour](https://msdn.microsoft.com/library/azure/dn800964.aspx) .

## <a name="next-step"></a>Étape suivante

Passez en revue ces liens pour en savoir plus sur les indexeurs :

- [L’indexation de la base de données SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB d’indexation](../documentdb/documentdb-search-indexer.md)
- [L’indexation de stockage des objets Blob (aperçu)](search-howto-indexing-azure-blob-storage.md)
- [Stockage de Table d’indexation (aperçu)](search-howto-indexing-azure-tables.md)



<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

