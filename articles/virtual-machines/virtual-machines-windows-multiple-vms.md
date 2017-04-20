<properties
    pageTitle="Créer plusieurs machines virtuelles | Microsoft Azure"
    description="Options de création de machines virtuelles sous Windows"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="guybo"/>

# <a name="create-multiple-azure-virtual-machines"></a>Créer plusieurs machines virtuelles Azure

Nombreux sont les scénarios où vous devez créer un grand nombre de machines virtuelles similaires (VMs). Voici quelques exemples d’informatique hautes performances (HPC), analyse des données à grande échelle, souvent sans état et évolutives des serveurs intermédiaires ou back-end (par exemple, des serveurs Web) et bases de données distribuées.

Cet article décrit les options disponibles pour créer plusieurs ordinateurs virtuels dans Azure. Ces options dépassent les cas simples où vous créez manuellement une série d’ordinateurs virtuels. Pour créer un grand nombre de machines virtuelles, les processus que vous utilisez habituellement ne sont pas évolutifs et si vous avez besoin créer plus d’un petit nombre de machines virtuelles.

Permet de créer plusieurs machines virtuelles de similaires est d’utiliser la construction d’Azure le Gestionnaire de ressources des _boucles de la ressource_.

## <a name="resource-loops"></a>Boucles de ressource

Boucles de ressource sont un raccourci syntaxique dans les modèles du Gestionnaire de ressources Azure. Boucles de ressource peuvent créer un ensemble de ressources de configuration similaire dans une boucle. Boucles de ressources vous permet de créer plusieurs comptes de stockage, les interfaces réseau ou les machines virtuelles. Pour plus d’informations sur les boucles de ressources, reportez-vous à [créer les ordinateurs virtuels de disponibilité définit à l’aide de boucles de la ressource](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/).

## <a name="challenges-of-scale"></a>Défis de l’échelle

Bien que les boucles de ressource en plus facile de créer une infrastructure de cloud à grande échelle et de produire des modèles plus concis, certains défis restent à relever. Par exemple, si une boucle de ressources vous permet de créer des machines virtuelles 100, vous devez mettre en corrélation des contrôleurs d’interface réseau (NIC) avec les ordinateurs virtuels correspondants et les comptes de stockage. Étant donné que le nombre d’ordinateurs virtuels est susceptible d’être différent du nombre de comptes de stockage, vous devrez traiter avec des tailles de boucle de ressource différents, trop. Il s’agit de problèmes peut être résolus, mais la complexité augmente considérablement avec échelle.

Un autre défi se produit lorsque vous avez besoin d’une infrastructure qui met à l’échelle ELASTIQUEMENT. Par exemple, vous souhaitez une infrastructure autoscale automatiquement augmente ou diminue le nombre de machines virtuelles en réponse à une charge de travail. Ordinateurs virtuels ne fournissent pas un mécanisme intégré pour faire varier en nombre (mise à l’échelle et l’échelle dans). Si vous mettre à l’échelle en supprimant les ordinateurs virtuels, il est difficile de garantir une disponibilité élevée en s’assurant que les ordinateurs virtuels soient équilibrées dans les domaines de mise à jour et les pannes.

Enfin, lorsque vous utilisez une boucle de ressource, les appels multiples pour créer des ressources atteindre du tissu sous-jacent. Lorsque plusieurs appels créent des ressources similaires, Azure a la possibilité d’améliorer cette conception et optimiser les performances et la fiabilité du déploiement implicite. Il s’agit _d’échelle de l’ordinateur virtuel définit_ interviennent.

## <a name="virtual-machine-scale-sets"></a>Jeux d’échelle de machine virtuelle

Jeux d’échelle de machine virtuelle sont une ressource Azure Compute à déployer et à gérer un ensemble d’ordinateurs virtuels identiques. Avec tous les ordinateurs virtuels configurés sur l’échelle de machine virtuelle même, les jeux sont faciles à faire évoluer et évoluer. Vous changez simplement le nombre d’ordinateurs virtuels dans le jeu. Vous pouvez également configurer des ensembles d’échelle de machine virtuelle à l’échelle automatique en fonction des besoins de la charge de travail.

Pour les applications qui doivent mettre à l’échelle les ressources de calcul et dans, les opérations d’échelle sont implicitement équitablement entre les domaines de panne et de la mise à jour.

Au lieu de la mise en corrélation de plusieurs ressources, telles que les cartes réseau et des ordinateurs virtuels, un ensemble d’échelle de machine virtuelle a réseau, stockage, machines virtuelles et les propriétés d’extension que vous pouvez configurer de manière centralisée.

Pour une introduction aux ensembles d’échelle de machine virtuelle, reportez-vous à l' [échelle de l’ordinateur virtuel définit la page du produit](https://azure.microsoft.com/services/virtual-machine-scale-sets/). Pour plus d’informations, accédez à l' [échelle de machines virtuelles définit la documentation](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/).
