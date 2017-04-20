<properties
    pageTitle="Quelles sont l’échelle de l’ordinateur virtuel définit ? | Microsoft Azure"
    description="Obtenir des informations sur les jeux d’échelle de machine virtuelle."
    keywords="échelle de machine virtuelle de la machine virtuelle Linux définit" 
    services="virtual-machines-linux"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/24/2016"
    ms.author="gatneil"/>

# <a name="what-are-virtual-machine-scale-sets"></a>Quelles sont l’échelle de l’ordinateur virtuel définit ?

Jeux d’échelle de Machine virtuelle vous permettent gérer plusieurs ordinateurs virtuels comme un ensemble. À un niveau élevé, des jeux d’échelle sont les avantages et inconvénients suivants :

Professionnels de l’informatique :

1. Une haute disponibilité. Chaque jeu d’échelle met ses ordinateurs virtuels dans un ensemble de disponibilité avec des domaines d’erreur 5 (FDs) et 5 domaines de mise à jour (UDs) pour garantir la disponibilité (pour plus d’informations sur FDs et UDs, voir la [disponibilité de la machine virtuelle](./virtual-machines-linux-manage-availability.md)). 
2. Intégration facile avec Azure équilibreur de charge et de la passerelle d’application.
3. Intégration facile avec Azure Autoscale.
4. Simplifier le déploiement, la gestion et de nettoyer des ordinateurs virtuels.
5. Prend en charge les versions courantes de Windows et Linux, ainsi que des images personnalisées.

Inconvénients :

1. Impossible d’attacher des disques de données à des instances de machine virtuelle dans un jeu d’échelle. En revanche, si vous devez utiliser stockage Blob, fichiers d’Azure, Azure Tables ou autres solutions de stockage.

## <a name="quick-create-using-azure-cli"></a>Création rapide à l’aide de la CLI d’Azure

[AZURE.INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez la [page d’accueil pour les jeux de l’échelle](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Pour plus d’informations, consultez la [page de la documentation principale pour l’échelle définit](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Modèles du Gestionnaire de ressources à l’aide de jeux d’échelle, par exemple rechercher « vmss » dans [emprunteuses de github de modèles de démarrage rapide d’Azure](https://github.com/Azure/azure-quickstart-templates).

