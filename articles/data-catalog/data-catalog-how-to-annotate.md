<properties
   pageTitle="Comment annoter des sources de données | Microsoft Azure"
   description="Article d’apprentissage comment annoter les éléments de données dans le catalogue de données Azure, y compris les experts, les balises, les descriptions et les noms conviviaux de mise en surbrillance."
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
   ms.date="09/21/2016"
   ms.author="maroche"/>


# <a name="how-to-annotate-data-sources"></a>Comment annoter des sources de données

## <a name="introduction"></a>Introduction
**Catalogue de données de Microsoft Azure** est un service en nuage entièrement géré qui sert d’un système d’enregistrement et système de détection des sources de données d’entreprise. En d’autres termes, le catalogue de données consiste à aider les utilisateurs à découvrir, à comprendre et à utiliser des sources de données et aider les entreprises à tirer davantage de valeur à partir de leurs données. Lorsqu’une source de données est enregistrée avec les données de catalogue, ses métadonnées sont copiée et par le service d’indexation, mais l’article ne se termine pas il. Catalogue de données permet aux utilisateurs de fournir leurs propres métadonnées descriptives, telles que les descriptions et balises – compléter les métadonnées extraites de la source de données et la source de données plus compréhensibles à d’autres personnes.

## <a name="annotation-and-crowdsourcing"></a>Annotation et marketing participatif
Tout le monde dispose d’un avis. Et c’est une bonne chose.
Catalogue de données reconnaît que différents utilisateurs ont différentes perspectives sur les sources de données d’entreprise, et que chacune de ces perspectives peut vous être utile. Envisagez le scénario suivant :

* L’administrateur système connaît l’entente de niveau de service pour les serveurs ou les services qui hébergent la source de données.
* L’administrateur de base de données sait que la planification de sauvegarde pour chaque base de données et les fenêtres de traitement de ETL autorisées.
* Le propriétaire du système connaît le processus permettant aux utilisateurs de demander l’accès à la source de données.
* Le Gestionnaire de données sait comment les actifs et les attributs dans la source de données correspondent au modèle de données d’entreprise.
* L’analyste sait comment les données sont utilisées dans le cadre du processus d’entreprise qu’il prend en charge.

Chacun de ces perspectives est précieux et catalogue de données utilise une approche de marketing participatif de métadonnées qui permettent à chacun d'entre eux d’être capturé et utilisé pour fournir une image complète des sources de données enregistrées. À l’aide du portail de catalogue de données, chaque utilisateur peut ajouter et de modifier ses propres annotations, tout en étant en mesure d’afficher les annotations fournies par d’autres utilisateurs.

## <a name="different-types-of-annotations"></a>Différents types d’annotations
Catalogue de données prend en charge les types d’annotations suivants :

| Annotation     | Notes                                                                                                                                                                                                                                                                                                                                                           |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nom convivial  | Noms conviviaux peuvent être fournies au niveau des actifs de données, pour rendre les actifs de données plus faciles à comprendre. Les noms conviviaux sont particulièrement utiles lorsque le nom de l’objet sous-jacent est énigmatique, abrégé ou sinon non significative pour les utilisateurs.                                                                                                                            |
| Description    | Des descriptions peuvent être fournies à l’actif de données et l’attribut / niveaux de colonne. Les descriptions sont des annotations de texte en forme libre court décrivant l’utilisateur perspective sur l’élément de données ou de son utilisation.                                                                                                                                                              |
| Balises (balises utilisateur)          | Les balises peuvent être fournies à l’actif de données et l’attribut / niveaux de colonne. Balises de l’utilisateur sont les étiquettes définies par l’utilisateur qui peuvent être utilisés pour classer les données de l’entreprise ou des attributs.                                                                                                                                                                                                    |
| Balises (balises glossaire)          | Les balises peuvent être fournies à l’actif de données et l’attribut / niveaux de colonne. Balises du glossaire sont définies de manière centralisée de glossaire peut être utilisée pour classer les données de l’entreprise ou des attributs à l’aide d’une taxonomie d’entreprise commune. Pour plus d’informations, consultez [comment configurer le glossaire Business pour le marquage de régie](data-catalog-how-to-business-glossary.md)                                                                                                                                                                                                    |
| Experts        | Les experts peuvent être fournies au niveau de la ressource de données. Experts identifient les utilisateurs ou les groupes avec experts perspectives sur les données et peuvent servir de point de contact pour les utilisateurs de découvrir les sources de données enregistrées et ont des questions qui ne sont pas traitées par les annotations existantes.  |
| Demander l’accès | Informations de demande d’accès peuvent être fournies au niveau de la ressource de données. Cette information est pour les utilisateurs de découvrir une source de données qu’ils n’ont pas encore des autorisations d’accès à. Les utilisateurs peuvent saisir l’adresse e-mail de l’utilisateur ou le groupe qui accorde l’accès, l’URL de l’outil que les utilisateurs doivent avoir accès, ou le processus ou le processus lui-même en tant que texte. |
| Documentation | Documentation peut être fournie au niveau de la ressource de données. Documentation de l’immobilisation est des informations de texte enrichi qui peut inclure des liens et des images, et qui peut fournir des informations ne pas acheminées par l’intermédiaire des descriptions et des balises. |


## <a name="annotating-multiple-assets"></a>Annotation de plusieurs actifs
Lorsque vous sélectionnez plusieurs ressources de données dans le portail du catalogue de données, les utilisateurs peuvent annoter tous les actifs sélectionnés en une seule opération. Annotations seront appliquent à tous les actifs sélectionnés, rendant facile de sélectionner et de fournir une description cohérente et ensembles de balises et d’experts pour les actifs de données connexes.

> [AZURE.NOTE] Les balises et les experts peuvent également être fournis lorsque l’outil d’inscription de la source inscription des ressources de données en utilisant les données de catalogue de données.

Lorsque la sélection de plusieurs tables et vues, seules les colonnes que toutes activées actifs ont en commun de données apparaît dans le portail du catalogue de données. Cela permet aux utilisateurs de fournir des balises et des descriptions de toutes les colonnes portant le même nom pour tous les actifs sélectionnés.

## <a name="annotations-and-discovery"></a>Découverte et annotations
Comme les métadonnées extraites de la source de données lors de l’enregistrement sont ajoutée à l’index de recherche de catalogue de données, fournies par l’utilisateur des métadonnées sont également indexée. Cela signifie que non seulement faire annotations facilitent les utilisateurs à comprendre les données qu'ils découvrent, annotations permettent également aux utilisateurs de découvrir les ressources de données annoté en effectuant une recherche en utilisant les termes ayant un sens pour les.

## <a name="summary"></a>Résumé
Enregistrement d’une source de données avec le catalogue de données rend ces données détectable en copiant les métadonnées structurelles et descriptifs à partir de la source de données dans le service de catalogue. Une fois qu’une source de données a été enregistrée, les utilisateurs peuvent fournir des annotations pour les rendre plus faciles à découvrir et de comprendre à partir du portail de catalogue de données.

## <a name="see-also"></a>Voir aussi
- Didacticiel de [Mise en route de catalogue de données Azure](data-catalog-get-started.md) pour plus d’informations détaillées sur la façon d’annoter des sources de données.
