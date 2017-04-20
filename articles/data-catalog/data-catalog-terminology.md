<properties
   pageTitle="Terminologie de catalogue de données Azure | Microsoft Azure"
   description="Cet article fournit une introduction aux concepts et termes utilisés dans la documentation du catalogue de données Azure."
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

# <a name="azure-data-catalog-terminology"></a>Terminologie de catalogue de données Azure

## <a name="catalog"></a>Catalogue

Le catalogue de données Azure est un référentiel de métadonnées basées sur le nuage dans lequel les données sources et des données peut être enregistrées. Le catalogue est utilisé comme un emplacement de stockage central pour les metadata structurelles extraites de sources de données et des métadonnées descriptives ajoutées par les utilisateurs.

## <a name="data-source"></a>Source de données

Une source de données est un système ou un conteneur qui gère les données de l’entreprise. Exemples, bases de données SQL Server, bases de données Oracle, bases de données SQL Server Analysis Services (tabulaires ou multidimensionnels) et les serveurs SQL Server Reporting Services.

## <a name="data-asset"></a>Ressources de données

Ressources de données sont des objets contenus dans les sources de données qui peuvent être enregistrés avec le catalogue. Les exemples incluent les tables SQL Server et vues, tables Oracle et vues, SQL Server Analysis Services mesures, dimensions et indicateurs de performance clés et rapports SQL Server Reporting Services.

## <a name="data-asset-location"></a>Emplacement de données actifs

Le catalogue stocke l’emplacement d’une source de données ou un élément de données, qui peut être utilisé pour se connecter à la source à l’aide d’une application cliente. Le format et les détails de l’emplacement varient en fonction du type de source de données. Par exemple, une table de SQL Server peut être identifiée par son nom de quatre parties : nom du serveur, nom de la base de données, schéma, nom d’objet – pendant un rapport SQL Server Reporting Services peut être identifié par son URL.

## <a name="structural-metadata"></a>Metadata structurelles

Des metadata structurelles est les métadonnées extraites d’une source de données qui décrit la structure d’un élément de données. Cela inclut l’emplacement de l’actif, son nom de l’objet et le type et des caractéristiques supplémentaires spécifiques au type. Par exemple, les métadonnées de structure des tables et des vues incluent les noms et les types de données pour les colonnes de l’objet.

## <a name="descriptive-metadata"></a>Métadonnées descriptives

Métadonnées descriptives sont des métadonnées qui décrivent l’objectif ou l’objectif d’un actif de données. En général les métadonnées descriptives sont ajoutée par catalogue les utilisateurs sur le portail du catalogue de données Azure, mais elle peut également être extraites à partir de la source de données lors de l’inscription. Par exemple, l’outil d’inscription de catalogue de données Azure extrait des descriptions de la propriété Description dans SQL Server Analysis Services et SQL Server Reporting Services et de la [propriété étendue de ms_description](https://technet.microsoft.com/library/ms190243.aspx) dans les bases de données SQL Server, si ces propriétés contiennent les valeurs.

## <a name="request-access"></a>Demander l’accès

Les métadonnées descriptives d’un actif de données peuvent inclure des informations sur la façon de demander l’accès à la ressource de données ou la source de données. Cette information est présentée avec l’emplacement actif de données et peut inclure un ou plusieurs des options suivantes :

- L’adresse e-mail de l’utilisateur ou l’équipe responsable de l’octroi de l’accès à la source de données.
- L’URL du processus documenté que les utilisateurs doivent suivre pour accéder à la source de données.
- L’URL d’un outil de gestion des identités et des accès (par exemple Microsoft Identity Manager) qui peut être utilisé pour accéder à la source de données.
- Une entrée de texte libre qui décrit comment les utilisateurs peuvent accéder à la source de données.

## <a name="preview"></a>Aperçu

Un aperçu dans le catalogue de données Azure est un instantané de jusqu'à 20 enregistrements qui peuvent être extraites de la source de données lors de l’inscription et stockés dans le catalogue avec les métadonnées de ressources de données. L’aperçu peut aider les utilisateurs en découverte un élément de données est mieux comprennent sa fonction et son objectif. En d’autres termes, voir les exemples de données peut être plus intéressant de voir uniquement les noms de colonne et les types de données.
Aperçus sont pris en charge uniquement pour les tables et les vues et doivent être explicitement activées par l’utilisateur lors de l’inscription.

## <a name="data-profile"></a>Profilage des données

Un profil des données de catalogue de données Azure est un instantané des métadonnées au niveau table et au niveau des colonnes sur un actif inscrit des données qui peut être extraites de la source de données lors de l’inscription et stocké dans le catalogue avec les métadonnées d’actifs de données. Le profil de données peut aider les utilisateurs en découverte un élément de données est mieux comprennent sa fonction et son objectif. Comme pour les prévisualisations, profils de données doivent être explicitement sélectionnés par l’utilisateur lors de l’inscription.

> [AZURE.NOTE] Extraire un profil de données peut être une opération coûteuse pour les vues et les tables de grande taille et peut augmenter considérablement le temps nécessaire pour enregistrer une source de données.

## <a name="user-perspective"></a>Point de vue utilisateur

Dans le catalogue de données Azure, n’importe quel utilisateur peut fournir des métadonnées descriptives d’un actif de données enregistré. Chaque utilisateur dispose d’un point de vue distinct sur les données et de son utilisation. Par exemple, l’administrateur chargé d’un serveur peut fournir les détails de son contrat de niveau de service (SLA) ou les fenêtres de sauvegarde ; un gestionnaire de données peut fournir des liens vers la documentation de l’entreprise traite le prend en charge des données ; et un analyste peut fournir une description dans les conditions qui correspondent le mieux aux autres analystes, et qui peuvent être plus utiles aux utilisateurs qui ont besoin de découvrir et de comprendre les données.

Chacun de ces perspectives sont, par nature, précieuses, et avec le catalogue de données Azure chaque utilisateur peut fournir les informations significatives pour eux, tandis que tous les utilisateurs peuvent utiliser ces informations pour comprendre les données et son objectif.

## <a name="expert"></a>Expert

Un expert est un utilisateur qui a été identifié comme ayant une perspective informée « experte » pour un élément de données. Tout utilisateur peut ajouter eux-mêmes ou par un autre utilisateur en tant qu’expert pour un actif. Qui est répertorié en tant qu’expert ne donne pas d’aucun privilège supplémentaire dans le catalogue de données Azure ; Il permet aux utilisateurs de localiser facilement les perspectives les plus susceptibles d’être utiles lors de l’examen des métadonnées d’un actif.

## <a name="owner"></a>Propriétaire

Un propriétaire est un utilisateur qui dispose des privilèges supplémentaires pour la gestion d’un élément de données dans le catalogue de données Azure. Utilisateurs peuvent prendre possession des actifs de données enregistrées, et les propriétaires peuvent ajouter d’autres utilisateurs en tant que propriétaires de collègues. Pour plus d’informations, voir [comment gérer les ressources de données](data-catalog-how-to-manage.md)  
> [AZURE.NOTE] Propriété et la gestion sont uniquement disponibles dans l’Édition Standard de Azure données catalogue.

## <a name="registration"></a>Inscription

L’inscription consiste à extraire des métadonnées des actifs de données à partir d’une source de données et en le copiant vers le service de catalogue de données Azure. Ressources de données qui ont été enregistrés pouvant être annotés et découvert.

## <a name="see-also"></a>Voir aussi

- [Quel est le catalogue de données Azure ?](data-catalog-what-is-data-catalog.md) -Cet article fournit une vue d’ensemble du service de catalogue de données Azure, les avantages et les scénarios que pris en charge.

- [Mise en route de catalogue de données Azure](data-catalog-get-started.md) : cet article propose un didacticiel de bout en bout qui vous montre comment utiliser le catalogue de données Azure pour la découverte de source de données.  
