<properties
    pageTitle="Disponibilité des instructions de jeux | Microsoft Azure"
    description="Obtenir des informations sur les instructions clés de conception et d’implémentation pour le déploiement de disponibilité définit dans les services d’infrastructure Azure."
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="availability-sets-guidelines"></a>Définit des consignes de disponibilité

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

Cet article se concentre sur les étapes de planification requis pour les jeux de disponibilité garantir vos applications accessibles au cours d’événements planifiés ou non la compréhension.

## <a name="implementation-guidelines-for-availability-sets"></a>Instructions d’implémentation pour les jeux de disponibilité

Décisions :

- Le nombre de jeux de disponibilité avez-vous besoin pour les différents rôles et niveaux de votre infrastructure de l’application ?

Tâches :

- Définir le nombre d’ordinateurs virtuels dans chaque couche application que vous avez besoin.
- Déterminer si vous devez ajuster le nombre de pannes ou de mettre à jour des domaines à utiliser pour votre application.
- Définir les ensembles de disponibilité requis à l’aide de votre convention d’affectation de noms et les ordinateurs virtuels résident dans les. Une machine virtuelle ne peut résider que dans la disponibilité d’un jeu. 

## <a name="availability-sets"></a>Jeux de disponibilité

Dans Azure, les machines virtuelles (VM) peut être placés dans un groupement logique qui appelé un jeu de disponibilité. Lorsque vous créez des ordinateurs virtuels dans un ensemble de disponibilité, la plateforme Azure répartit le placement de ces ordinateurs virtuels sur l’infrastructure sous-jacente. Il doit y avoir un événement d’entretien planifié pour la plateforme Azure ou un matériel sous-jacent / panne d’infrastructure, l’utilisation de jeux de disponibilité garantit qu’au moins un ordinateur virtuel reste en cours d’exécution.

Pour obtenir les meilleurs résultats, les applications ne doivent pas résider sur un ordinateur virtuel unique. Un jeu de disponibilité qui contient un ordinateur virtuel unique ne prendre aucune protection à partir d’événements planifiés ou non au sein de la plateforme Azure. Le [Contrat SLA d’Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines) requiert deux ou plusieurs des ordinateurs virtuels au sein d’une disponibilité définie pour autoriser la distribution des ordinateurs virtuels au sein de l’infrastructure sous-jacente.

L’infrastructure sous-jacente dans Azure est divisé en mise à jour des domaines et des domaines d’erreur. Ces domaines sont définis par les hôtes partagent un cycle de mise à jour commune ou infrastructure physique de partage similaires telles que la mise en réseau et d’alimentation. Azure distribue automatiquement vos ordinateurs virtuels au sein d’une disponibilité définie sur plusieurs domaines pour garantir une disponibilité et une tolérance de panne. Selon la taille de votre application et le nombre de machines virtuelles au sein d’un ensemble de disponibilité, vous pouvez ajuster le nombre de domaines que vous souhaitez utiliser. Vous pouvez en savoir plus sur la [gestion de disponibilité et d’utilisation des domaines de panne et de la mise à jour](virtual-machines-windows-manage-availability.md).

Lors de la conception de votre infrastructure d’application, vous devez également planifier les niveaux de l’application que vous utilisez. Ordinateurs virtuels de groupe qui remplissent les mêmes fonctions de disponibilité définit, par exemple une disponibilité pour vos ordinateurs de virtuels frontaux exécutant IIS. Créer une disponibilité distincte pour vos ordinateurs virtuels de back-end SQL Server en cours d’exécution. L’objectif est de s’assurer que chaque composant de votre application est protégé par un ensemble de disponibilité, et au moins une fois instance reste toujours en cours d’exécution.

Équilibreurs de charge peuvent être utilisés en face de chaque couche de l’application pour travailler avec un ensemble de disponibilité et de garantir le trafic peut toujours être routé vers une instance en cours d’exécution. Sans équilibreur de charges, vos ordinateurs virtuels peuvent continuer à s’exécuter tout au long des maintenances planifiées et non planifiées, mais vos utilisateurs finaux n’est peut-être pas en mesure de les résoudre si l’ordinateur virtuel principal n’est pas disponible.


## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 