<properties
    pageTitle="Instructions d’ordinateurs virtuels Linux | Microsoft Azure"
    description="En savoir plus sur les instructions de conception et implémentation de clé de déploiement d’ordinateurs virtuels Linux dans Azure"
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

# <a name="virtual-machines-guidelines"></a>Instructions de machines virtuelles

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

Cet article se concentre sur les étapes de planification requis pour créer et gérer des machines virtuelles (VM) dans votre environnement Azure la compréhension.

## <a name="implementation-guidelines-for-vms"></a>Instructions d’implémentation pour les machines virtuelles
Décisions :

- Le nombre de machines virtuelles vous avez besoin pour les divers niveaux de l’application et les composants de votre infrastructure ?
- Les ressources processeur et mémoire a besoin de chaque machine virtuelle, et quelles sont les exigences de stockage ?

Tâches :

- Permet de définir les charges de travail de votre application et les ressources qui que nécessitent des ordinateurs virtuels.
- Aligner les demandes de ressources pour chaque ordinateur virtuel avec le type approprié de taille et de stockage de machine virtuelle.
- Définissez vos groupes de ressources pour les différentes couches et composants de votre infrastructure.
- Définir votre convention d’affectation de noms de machine virtuelle.
- Créer vos ordinateurs virtuels à l’aide de la CLI d’Azure, web portail, ou avec les modèles du Gestionnaire de ressources.

## <a name="virtual-machines"></a>Ordinateurs virtuels

Un des principaux composants de votre environnement Azure est susceptible de VM. C’est lorsque vous exécutez vos applications, les bases de données, les services d’authentification, les etc..

Il est important de comprendre les [différentes tailles VM](virtual-machines-linux-sizes.md) à dimensionner correctement votre environnement à partir des performances et de coûts. Si vos ordinateurs virtuels ne sont pas suffisamment coeurs de processeur ou la mémoire, les performances de votre application sont amoindries, quelle que soit la façon dont il est conçu et développé. Passez en revue les charges de travail proposés pour chaque série de la machine virtuelle comme point de départ lorsque vous décidez de la taille à utiliser pour chaque composant de votre infrastructure virtuelle. Vous pouvez [Modifier la taille d’un ordinateur virtuel](virtual-machines-linux-change-vm-size.md) après le déploiement.

Stockage joue un rôle essentiel dans les performances de la machine virtuelle. Vous pouvez utiliser le stockage Standard, qui utilise des disques de filature, ou le stockage de la prime pour les charges d’e/s élevées et des performances de pointe, utilisant des disques SSD. Comme avec la taille de la mémoire virtuelle, des coûts sont les considérations à sélectionner le support de stockage. Vous pouvez lire l' [article de directives infrastructure de stockage](virtual-machines-linux-infrastructure-storage-solutions-guidelines.md) afin de comprendre la conception du stockage approprié pour optimiser les performances de vos ordinateurs virtuels.


## <a name="resource-groups"></a>Groupes de ressources
Les composants tels que les ordinateurs virtuels sont logiquement regroupés afin de faciliter la gestion et la maintenance à l’aide de [Groupes de ressources Azure](../azure-resource-manager/resource-group-overview.md). À l’aide de groupes de ressources, vous pouvez créer, gérer et contrôler toutes les ressources qui constituent une application donnée. Vous pouvez également implémenter des [contrôles d’accès basés sur les rôles](../active-directory/role-based-access-control-what-is.md) pour accorder l’accès à d’autres personnes au sein de votre équipe pour que les ressources que dont ils ont besoin. Prendre le temps de planifier vos groupes de ressources et les affectations de rôles. Il existe différentes approches pour réellement concevoir et implémenter des groupes de ressources, veillez à lire l' [article de directives de groupes de ressources](virtual-machines-linux-infrastructure-resource-groups-guidelines.md) pour comprendre la meilleure façon de générer vos ordinateurs virtuels.


## <a name="templates"></a>Modèles 
Vous pouvez créer des modèles, définis par les fichiers JSON déclaratives, pour créer vos ordinateurs virtuels. Modèles en général également générer le stockage requis, mise en réseau, interfaces réseau, l’adressage IP, etc. ainsi que les ordinateurs virtuels eux-mêmes. Vous pouvez utiliser des modèles pour créer des environnements cohérentes et reproductibles de développement et de test répliquer facilement les environnements de production et vice versa. Vous pouvez en savoir plus sur [Création et l’utilisation des modèles](../azure-resource-manager/resource-group-overview.md#template-deployment) pour comprendre comment vous pouvez les utiliser pour la création et le déploiement de vos ordinateurs virtuels.


## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 