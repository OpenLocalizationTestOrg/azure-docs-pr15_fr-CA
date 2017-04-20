<properties
   pageTitle="Comment enregistrer des sources de données | Microsoft Azure"
   description="Article d’apprentissage comment enregistrer des sources de données avec le catalogue de données Azure, y compris les champs de métadonnées extraites lors de l’inscription de la mise en surbrillance."
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


# <a name="how-to-register-data-sources"></a>Comment enregistrer des sources de données

## <a name="introduction"></a>Introduction
**Catalogue de données de Microsoft Azure** est un service en nuage entièrement géré qui sert d’un système d’enregistrement et système de détection des sources de données d’entreprise. En d’autres termes, le **Catalogue de données Azure** consiste à aider les utilisateurs à découvrir, à comprendre et à utiliser des sources de données et aider les entreprises à tirer davantage de valeur à partir de leurs données. Et la première étape de la fabrication d’une source de données détectable via le **Catalogue de données Azure** doit inscrire cette source de données.
## <a name="registering-data-sources"></a>Enregistrement de sources de données
L’inscription est le processus d’extraction de métadonnées à partir de la source de données et copie les données dans le service de **Catalogue de données Azure** . Les données restent où il actuellement réside et reste sous le contrôle des administrateurs et des stratégies du système en cours.

Pour enregistrer une source de données, tout simplement lancez l’outil d’inscription de **Catalogue de données Azure** données source à partir du portail de **Catalogue de données Azure** . Ouvrez une session avec votre travail scolaire compte (les mêmes informations Azure Active Directory vous utilisez pour vous connecter au portail) et puis sélectionnez la source de données que vous souhaitez enregistrer.
Consultez le didacticiel [Mise en route de catalogue de données Azure](data-catalog-get-started.md) pour plus d’informations détaillées.

Une fois la source de données a été enregistrée, le catalogue effectue le suivi de son emplacement et indexe ses métadonnées, afin que les utilisateurs peuvent rechercher, Parcourir, découvrir la source de données et son emplacement permet de se connecter à l’aide de l’application ou leur choix.

## <a name="sources-supported"></a>Sources prises en charge
Pour la liste des sources de données actuellement pris en charge, reportez-vous à [Données catalogue DSR](data-catalog-dsr.md) .
<br/>


## <a name="structural-metadata"></a>Metadata structurelles
Lorsque vous vous inscrivez une source de données, extrait l’outil d’inscription d’informations sur la structure des objets que vous sélectionnez – il s’agit de métadonnées au structurels comme.

Pour tous les objets, ces métadonnées structure inclut l’emplacement de l’objet, afin que les utilisateurs de découvrir les données peuvent utiliser ces informations pour se connecter à l’objet dans les outils clients de leur choix. Autres métadonnées structurelles incluent le type et le nom de l’objet et attribut/nom de colonne et type de données.

## <a name="descriptive-metadata"></a>Métadonnées descriptives
En plus des métadonnées de base structurelle extraites de la source de données, l’outil de l’enregistrement de source de données extrait également les métadonnées descriptives ainsi. Pour SQL Server Analysis Services et SQL Server Reporting Services, cela provient des propriétés Description exposées par ces services. Pour SQL Server, les valeurs fournies à l’aide de la propriété étendue de ms_description seront extraits. Pour la base de données Oracle, l’outil de l’enregistrement de source de données extrait la colonne commentaires à partir de la vue ALL_TAB_COMMENTS.

En plus des métadonnées descriptives extraites de la source de données, ils peuvent également entrer des métadonnées descriptives à l’aide de l’outil de l’enregistrement de source de données. Les utilisateurs peuvent ajouter des balises et peuvent identifier des experts pour les objets en cours d’enregistrement. Toutes les métadonnées de cette description est copiée dans le service de **Catalogue de données Azure** avec les métadonnées structurelles.

## <a name="including-previews"></a>Y compris les aperçus

Par défaut, uniquement des métadonnées sont extraite à partir de sources de données et copiées vers le service de **Catalogue de données Azure** , mais comprendre qu'une source de données est souvent facilitée par voir un échantillon des données qu’il contient.

L’outil de l’enregistrement de source de données **Catalogue de données Azure** permet également inclure un aperçu instantané des données dans chaque table et vue enregistrée. Si l’utilisateur opte pour inclure des aperçus lors de l’inscription, l’outil d’inscription inclut jusqu'à 20 enregistrements à partir de chaque table et vue. Cet instantané est ensuite copié dans le catalogue avec les métadonnées structurelles et descriptif.


> [AZURE.NOTE]  Avec un grand nombre de colonnes de tables larges peuvent avoir moins de 20 enregistrements inclus dans leur Aperçu.


## <a name="including-data-profiles"></a>Y compris les profils de données

Tout comme les aperçus y compris peuvent fournir le contexte précieux pour les utilisateurs de rechercher des sources de données dans le **Catalogue de données Azure**, notamment dans un profil de données peut facilitent également de comprendre les sources de données de découverte.

L’outil de l’enregistrement de source de données **Catalogue de données Azure** permet aux utilisateurs d’y inclure un profil de données pour chaque table et vue enregistrée. Si l’utilisateur choisit d’inclure un profil de données lors de l’inscription, l’outil d’inscription inclut des statistiques sur les données dans chaque table et vue, y compris :

* Le nombre de lignes et la taille des données dans l’objet
* La date de la dernière mise à jour des données et le schéma de l’objet
* Le nombre de valeurs distinctes pour les colonnes et les enregistrements null
* Les valeurs minimale, maximale, moyenne et écart-type de colonnes

Ces statistiques sont ensuite copiées dans le catalogue avec les métadonnées structurelles et descriptif.

> [AZURE.NOTE]  Les colonnes de texte et de la date n’incluent pas les statistiques de moyenne ou l’écart-type dans leur profil de données.

## <a name="updating-registrations"></a>Mise à jour d’enregistrements

Enregistrement d’une source de données va le rendre détectable dans le **Catalogue de données Azure** à l’aide de métadonnées et facultatif preview extraites lors de l’inscription. Si la source de données doit être mis à jour dans le catalogue (par exemple, si le schéma d’un objet a changé, ou exclus à l’origine de tables doivent être inclus, ou un utilisateur souhaite mettre à jour les données incluses dans les aperçus) l’outil de l’enregistrement de source de données peut être ré-exécuté.

Inscrit à nouveau une source déjà inscrite de données effectue une opération de fusion « upsert » : les objets existants seront mis à jour, alors que les nouveaux objets seront créés. Toutes les métadonnées fournies par les utilisateurs via le portail de **Catalogue de données Azure** seront conservée.

## <a name="summary"></a>Résumé
Enregistrement d’une source de données avec le **Catalogue de données Azure** facilite cette source de données à découvrir et à comprendre, en copiant les métadonnées structurelles et descriptifs à partir de la source de données dans le service de catalogue. Une fois qu’une source de données a été enregistrée, il peut ensuite être annotée, gérés et découverts à l’aide du portail de **Catalogue de données Azure** .

## <a name="see-also"></a>Voir aussi
- Didacticiel de [Mise en route de catalogue de données Azure](data-catalog-get-started.md) pour plus d’informations détaillées sur la façon d’enregistrer les sources de données.
