<properties
    pageTitle="Règles des groupes de ressources | Microsoft Azure"
    description="Obtenir des informations sur les instructions clés de conception et d’implémentation pour le déploiement de groupes de ressources dans les services d’infrastructure Azure."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="azure-resource-group-guidelines"></a>Instructions de groupe de ressources Azure

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

Cet article se concentre sur la compréhension de la build, votre environnement de manière logique et tous les composants dans les groupes de ressources de groupe.


## <a name="implementation-guidelines-for-resource-groups"></a>Instructions d’implémentation pour les groupes de ressources

Décisions :

- Vous allez créer des groupes de ressources par les composants d’infrastructure principaux, ou par le déploiement de l’application complète ?
- Vous devez limiter l’accès aux groupes de ressources à l’aide de contrôles d’accès basés sur les rôles ?

Tâches :

- Définir les composants de l’infrastructure de base et dédié des groupes de ressources dont vous avez besoin.
- Examinez comment implémenter le Gestionnaire de ressources des modèles pour des déploiements cohérents, reproductibles.
- Définir les rôles d’utilisateur d’accès dont vous avez besoin pour contrôler l’accès aux groupes de ressources.
- Créez le jeu de groupes de ressources à l’aide de votre convention d’affectation de noms. Vous pouvez utiliser la CLI d’Azure ou un portail.


## <a name="resource-groups"></a>Groupes de ressources

Dans Azure, vous regroupez logiquement des ressources associées, comme des comptes de stockage, des réseaux virtuels et des machines virtuelles (VM) pour déployer, de gérer et de les gérer comme une seule entité. Cette approche facilite le pour déployer les applications tout en conservant toutes les ressources connexes d’ensemble en matière de gestion, ou à l’accès d’autres utilisateurs à ce groupe de ressources. Pour une présentation plus complète des groupes de ressources, vous pouvez lire la [Présentation du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).

Une fonctionnalité clé pour les groupes de ressources est la possibilité de créer votre environnement à l’aide d’un fichier JSON qui déclare le stockage en réseau, et les ressources de calcul. Vous pouvez également définir des scripts personnalisés liés ou des configurations à appliquer. À l’aide de ces modèles JSON, vous créez des déploiements cohérents, reproductibles pour vos applications. Cette approche vous permet de créer un environnement de développement et ensuite utiliser ce même modèle pour créer un déploiement de production, ou vice versa. Pour mieux comprendre à propos de l’utilisation des modèles, consultez [la procédure pas à pas le modèle](../resource-manager-template-walkthrough.md) qui vous guide à travers chaque étape de la création d’un modèle JSON.

Il existe deux approches différentes, que vous pouvez effectuer lors de la conception de votre environnement avec les groupes de ressources :

- Les groupes de ressources pour chaque déploiement de l’application qui associe les comptes de stockage, des réseaux virtuels et des sous-réseaux, des machines virtuelles, de charger équilibreurs, etc..
- Groupes de ressources centralisées qui contiennent vos comptes réseau et de sous-réseaux ou de stockage virtuels principal. Vos applications sont ensuite dans leurs propres groupes de ressources qui contiennent uniquement des ordinateurs virtuels, les équilibreurs de charge, les interfaces réseau, etc..

Comme vous mise à l’échelle, centralisée des groupes de ressources de votre réseau et de sous-réseaux virtuel rend plus facile à build coexistence connexion réseau pour les options de connectivité hybride. L’autre approche est pour chaque application de disposer de leur propre réseau virtuel qui requiert la configuration et la maintenance. [Contrôles d’accès basés sur les rôles](../active-directory/role-based-access-control-what-is.md) fournissent granulaire permet de contrôler l’accès aux groupes de ressources. Pour les applications de production, vous pouvez contrôler les utilisateurs qui peuvent accéder à ces ressources, ou pour les ressources d’infrastructure de base, vous pouvez limiter seulement les ingénieurs d’infrastructure pour travailler avec eux. Les propriétaires des applications ont seulement accès aux composants de l’application au sein de leur groupe de ressources et pas au cœur de l’infrastructure Azure de votre environnement. Lorsque vous concevez votre environnement, envisagez les utilisateurs qui ont besoin d’accéder aux ressources et concevoir vos groupes de ressources en conséquence. 


## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 