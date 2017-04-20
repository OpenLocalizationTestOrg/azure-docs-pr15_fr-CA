<properties
   pageTitle="Comment enregistrer les recherches et l’épingler les ressources de données | Microsoft Azure"
   description="Article d’apprentissage mise en surbrillance des fonctionnalités dans le catalogue de données Azure pour l’enregistrement des sources de données et les données de l’entreprise pour une réutilisation ultérieure."
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
   ms.date="10/10/2016"
   ms.author="maroche"/>

# <a name="how-to-save-searches-and-pin-data-assets"></a>Comment enregistrer les recherches et l’épingler les ressources de données

## <a name="introduction"></a>Introduction

Catalogue de données de Microsoft Azure fournit des fonctionnalités pour la découverte de source de données. Les utilisateurs peuvent rapidement rechercher et filtrer le catalogue pour localiser des sources de données et de comprendre le but recherché, rendant plus facile à trouver les données de droite pour le travail à portée de main.

Mais qu’en est-il lorsque les utilisateurs doivent régulièrement fonctionne avec les mêmes données ? Que se passe-t-il lorsque les utilisateurs contribuent régulièrement leurs connaissances aux mêmes sources de données dans le catalogue ? Dans ces situations, avoir à émettre de façon répétée les mêmes recherches peut s’avérer inefficace – il s’agit de la recherche enregistrée et épinglés données actifs peuvent aider.

## <a name="saved-searches"></a>Recherches enregistrées

Une recherche enregistrée dans le catalogue de données Azure est une définition de recherche réutilisable, par utilisateur. Une fois qu’un utilisateur a défini une recherche, y compris les termes de recherche, de balises et d’autres filtres, il peut l’enregistrer pour une utilisation ultérieure. La définition de la recherche enregistrée peut ensuite être exécutée de nouveau à une date ultérieure, pour renvoyer des actifs de données qui correspondent à ses critères de recherche.

### <a name="creating-a-saved-search"></a>Création d’une recherche enregistrée

Pour créer une recherche enregistrée, d’abord entrer les critères de recherche pour être réutilisés. Cliquez ensuite sur le lien « Enregistrer » dans la zone de recherche « en cours » dans le portail du catalogue de données Azure.

 ![Sélectionnez « Enregistrer » pour enregistrer les paramètres actuels de la recherche](./media/data-catalog-how-to-save-pin/01-save-option.png)

Lorsque vous y êtes invité, entrez un nom pour la recherche enregistrée. Choisissez un nom qui est significatif et descriptif des actifs de données qui seront renvoyés par la recherche.

 ![Fournissez un nom pour la recherche enregistrée](./media/data-catalog-how-to-save-pin/02-name.png)

### <a name="managing-saved-searches"></a>Gestion des recherches enregistrées

Une fois qu’un utilisateur a enregistré une ou plusieurs recherches, une option « Recherches enregistrées » apparaît dans le portail du catalogue de données Azure sous la zone de recherche « en cours ». Lorsqu’elle est développée, la liste complète des recherches d’enregistrements s’affichera.

 ![Liste des recherches enregistrées](./media/data-catalog-how-to-save-pin/03-list.png)

Sélection d’une recherche enregistrée à partir de la liste entraîne la recherche à exécuter.

Sélectionnez le menu déroulant fournit un ensemble d’options de gestion :

 ![Options de gestion des recherches enregistrées](./media/data-catalog-how-to-save-pin/04-managing.png)

En sélectionnant « Renommer » invitera l’utilisateur à entrer un nouveau nom pour la recherche enregistrée. La définition de la recherche ne changera pas.

Sélectionnez « Supprimer » invitera l’utilisateur à confirmer et supprime ensuite la recherche enregistrée à partir de la liste de l’utilisateur.

Sélectionnez « Enregistrer en tant que valeur par défaut » marque recherche enregistré comme la recherche par défaut pour l’utilisateur sélectionné. Si l’utilisateur effectue une recherche « vide » à partir de la page d’accueil de catalogue de données Azure, recherche par défaut de l’utilisateur est exécutée. En outre, la recherche marqués par défaut s’affiche en haut de la liste de recherche enregistrée.

### <a name="organizational-saved-searches"></a>Recherches enregistrées d’organisation

Chaque utilisateur peut enregistrer les recherches pour leur propre usage. Les administrateurs de catalogue de données peuvent également enregistrer les recherches pour tous les utilisateurs au sein de l’organisation. Lorsque vous enregistrez une recherche, les administrateurs sont présentées avec une option permettant de partager la recherche enregistrée au sein de la société. Si cette option est sélectionnée, la recherche enregistrée sera incluse dans la liste des recherches disponibles pour tous les utilisateurs.

 ![Recherches enregistrées d’organisation](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)


## <a name="pinned-data-assets"></a>Ressources de données épinglées

Recherches enregistrées permettent aux utilisateurs d’enregistrer et de réutiliser des définitions de recherche ; les éléments de données retournés par la recherche peuvent changer à mesure que le contenu de la modification du catalogue. Épinglage d’éléments de données permet aux utilisateurs d’identifier les actifs de données spécifiques pour faciliter leur accès sans avoir à utiliser une recherche de manière explicite.

Épinglage d’un élément de données est simple : les utilisateurs suffit de cliquer sur l’icône « épingler » de l’actif de données pour l’ajouter à leur liste des programmes affichés. Cette icône s’affiche dans le coin de la mosaïque de l’immobilisation dans l’affichage en mosaïque et dans la colonne la plus à gauche dans la liste sur le portail du catalogue de données Azure.

![Épinglage d’un élément de données](./media/data-catalog-how-to-save-pin/05-pinning.png)

Désépingler une immobilisation est tout aussi simple : utilisateurs cliquez simplement sur l’icône « épingler » pour désactiver le paramètre pour l’actif sélectionné.

![Désépingler un actif de données](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="my-assets"></a>« Mes ressources »
La page d’accueil portail de catalogue de données Azure inclut une section « Mes ressources » qui affiche des éléments d’intérêt pour l’utilisateur actuel. Cette section inclut les deux immobilisations épinglées et recherches enregistrées.

![« Mes ressources » sur la page d’accueil](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Résumé
Catalogue de données Azure fournit des fonctionnalités qui le rendent plus facile pour les utilisateurs à découvrir les sources de données que dont ils ont besoin, et permet de consacrer moins de temps à la recherche de données et plus de temps avec lui. Les recherches enregistrées et épinglés données actifs reposent sur ces fonctionnalités de base afin que les utilisateurs peuvent identifier facilement des sources de données avec laquelle ils peuvent fonctionner à plusieurs reprises.
