<properties
   pageTitle="Comment gérer les ressources de données | Microsoft Azure"
   description="Article d’apprentissage mise en surbrillance de la façon de contrôler la visibilité et la propriété des actifs de données enregistré dans le catalogue de données Azure."
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


# <a name="how-to-manage-data-assets"></a>Comment gérer les ressources de données

## <a name="introduction"></a>Introduction

**Catalogue de données Azure** fournit des fonctionnalités pour la découverte de source de données, permettant aux utilisateurs de facilement découvrir et comprendre les sources de données que nécessaires pour effectuer une analyse et prendre des décisions. Ces fonctions de découverte avoir un impact considérable lorsque tous les utilisateurs peuvent rechercher et comprendre la large gamme de sources de données disponibles. Dans cet esprit, le comportement par défaut du catalogue de données pour toutes les sources de données enregistrées être visible – et détectable par, tous les utilisateurs du catalogue.

Catalogue de données ne donne pas aux utilisateurs l’accès aux données elles-mêmes. Accès aux données est contrôlé par le propriétaire de la source de données. Catalogue de données permet aux utilisateurs de découvrir les sources de données et afficher les métadonnées relatives aux sources inscrits dans le catalogue.

Il peut arriver, cependant, où des sources de données ne doivent être visibles à des utilisateurs spécifiques ou à des membres de groupes spécifiques. Pour ces scénarios, le catalogue de données permet aux utilisateurs de prendre possession des actifs de données enregistrées dans le catalogue, et puis contrôle la visibilité des actifs dont ils sont propriétaires.

> [AZURE.NOTE] Les fonctionnalités décrites dans cet article sont uniquement disponibles dans l’Édition Standard de Azure données catalogue. L’édition gratuite ne fournit pas de fonctionnalités de propriété et de restriction visibilité de ressource de données.

## <a name="managing-ownership-of-data-assets"></a>Gestion de la propriété des actifs de données
Par défaut, les ressources de données enregistrées dans le catalogue de données sont sans propriétaire ; tout utilisateur ayant l’autorisation d’accéder au catalogue peut découvrir et annoter ces actifs. Les utilisateurs peuvent prendre possession des données sans propriétaire et peuvent limiter la visibilité des actifs que dont ils sont propriétaires.

Quand un élément de données dans le catalogue de données appartient, seuls les utilisateurs autorisés par les propriétaires peuvent découvrir l’actif et afficher ses métadonnées, et seuls les propriétaires peuvent supprimer l’actif à partir du catalogue.

> [AZURE.NOTE] Propriété de catalogue de données affecte uniquement les métadonnées stockées dans le catalogue. Il ne confère pas d’autorisations sur la source de données sous-jacente.

### <a name="taking-ownership"></a>Prise de possession
Les utilisateurs peuvent prendre possession des ressources de données en sélectionnant l’option « Prendre possession » dans le portail du catalogue de données. Aucune autorisation spéciale n’êtes obligée de prendre possession d’une ressource de données sans propriétaire ; n’importe quel utilisateur peut prendre possession d’une ressource de données sans propriétaire.

### <a name="adding-owners-and-co-owners"></a>Ajout de propriétaires et copropriétaires
Si un élément de données est déjà propriétaire, les utilisateurs ne peuvent pas simplement Appropriation – doit être ajoutés en tant que propriétaires de collègues par un propriétaire existant. N’importe quel propriétaire peut ajouter des utilisateurs et les groupes de sécurité en tant que propriétaires de collègues.

> [AZURE.NOTE] Il est recommandé d’avoir au moins deux personnes comme propriétaires de n’importe quel élément de données propriétaire.

### <a name="removing-owners"></a>Suppression de propriétaires
Tout comme n’importe quel propriétaire de la ressource peut ajouter les copropriétaires, tout propriétaire de la ressource peut supprimer toute copropriétaire.

Si le propriétaire d’une ressource se supprime lui-même en tant que propriétaire, il ne peut plus gérer l’actif. Si le propriétaire d’une ressource supprime lui-même en tant que propriétaire et qu’aucun autres copropriétaires, l’actif revient à un état sans propriétaire.

## <a name="visibility"></a>Visibilité
Les propriétaires de données actif peuvent contrôler la visibilité des actifs de données qu’ils possèdent. Pour restreindre la visibilité par défaut – où tous les utilisateurs du catalogue de données peuvent découvrir et afficher les actifs de données – le propriétaire de la ressource peut activer/désactiver le paramètre de visibilité de « Tout le monde » à « Propriétaires et ces utilisateurs » dans les propriétés de l’actif. Les propriétaires peuvent ensuite ajouter des utilisateurs et des groupes de sécurité.

> [AZURE.NOTE] Chaque fois que possible, les autorisations relatives aux actifs des possessions et sa visibilité doit être assignée aux groupes de sécurité et non aux utilisateurs individuels.

## <a name="catalog-administrators"></a>Administrateurs de catalogue
Les administrateurs de catalogue de données sont implicitement les copropriétaires de toutes les ressources dans le catalogue. Propriétaires des ressources ne peut pas supprimer la visibilité des administrateurs de catalogue, et les administrateurs peuvent gérer les possessions et sa visibilité pour tous les actifs de données dans le catalogue.

## <a name="summary"></a>Résumé
Modèle de marketing participatif du catalogue de données de métadonnées et les données du parc autorise tous les utilisateurs de catalogue contribuer et de découverte. L’Édition Standard de catalogue de données fournit des fonctionnalités pour la propriété et la gestion pour limiter la visibilité et l’utilisation des actifs de données spécifiques.
