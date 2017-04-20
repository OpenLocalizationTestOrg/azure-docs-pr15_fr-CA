<properties
    pageTitle="Instructions de Solutions de stockage | Microsoft Azure"
    description="Obtenir des informations sur les instructions clés de conception et d’implémentation pour le déploiement de solutions de stockage dans les services d’infrastructure Azure."
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

# <a name="storage-infrastructure-guidelines"></a>Instructions d’infrastructure de stockage

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

Cet article se concentre sur la compréhension des besoins de stockage et les considérations de design pour atteindre les performances optimales virtual machine (VM).


## <a name="implementation-guidelines-for-storage"></a>Instructions d’implémentation pour le stockage

Décisions :

- Vous devez utiliser le stockage Standard ou Premium pour votre charge de travail ?
- Vous avez besoin d’un entrelacement de disques pour créer des disques de plus de 1023 Go ?
- Vous avez besoin d’un entrelacement de disques pour optimiser les performances de votre charge de travail d’e/s ?
- Le jeu de comptes de stockage avez-vous besoin héberger votre charge de travail informatique ou votre infrastructure ?

Tâches :

- Passez en revue les demandes d’e/s des applications que vous déployez et planifiez le numéro et le type de compte de stockage.
- Créer l’ensemble des comptes de stockage à l’aide de votre convention d’affectation de noms. Vous pouvez utiliser la CLI Azure ou le portail.


## <a name="storage"></a>Stockage

Le stockage Azure est un élément clé du déploiement et de gestion des applications et des machines virtuelles (VM). Stockage Azure fournit des services pour le stockage des données de fichier, les données non structurées et les messages, et il fait également partie de l’infrastructure de prise en charge des ordinateurs virtuels.

Il existe deux types de comptes de stockage pour prendre en charge les ordinateurs virtuels :

- Stockage standard comptes vous donnent accès au stockage d’objets blob (utilisé pour le stockage des disques Azure VM), stockage de table, de stockage de la file d’attente et le stockage de fichiers.
- Comptes de [stockage de prime](../storage/storage-premium-storage.md) offrent la prise en charge de disque hautes performances et à faible latence pour les charges de travail intensives d’e/s, par exemple de cluster MongoDB Sharded. Stockage de prime prend actuellement en charge uniquement les disques Azure VM.

Azure crée des ordinateurs virtuels avec un disque du système d’exploitation, un disque temporaire et zéro, un ou plusieurs disques de données facultatif. Le disque du système d’exploitation et les disques de données sont des objets BLOB Azure page, alors que le disque temporaire est stocké localement sur le nœud dans lequel l’ordinateur réside. Prenez soin lors de la conception d’applications à utiliser uniquement ce disque temporaire pour les données non persistantes comme la machine virtuelle peut être migrée entre des hôtes pendant un événement d’entretien. Toutes les données stockées sur le disque temporaire sont perdues.

Haute disponibilité et la durabilité est fournie par l’environnement de stockage Azure sous-jacent afin de garantir que vos données restent protégées contre les défaillances de maintenance ou de matériel non planifiés. Lors de la conception de votre environnement de stockage Azure, vous pouvez choisir de répliquer de stockage de machine virtuelle :

- localement dans un centre de données Azure donné
- entre les centres de données Azure dans une région donnée
- entre les centres de données Azure dans différentes régions.

Vous pouvez lire [plus d’informations sur les options de réplication pour une haute disponibilité](../storage/storage-introduction.md#replication-for-durability-and-high-availability).

Disques du système d’exploitation et données ont une taille maximale de 1 023 gigaoctets (Go). La taille maximale d’un objet blob est 1 024 Go et qui doit contenir les métadonnées (pied de page) du fichier de disque dur virtuel (un Go est 1024<sup>3</sup> octets). Vous pouvez utiliser le Gestionnaire de Volume logique (LVM) pour dépasser cette limite en regroupant des disques de données pour présenter des volumes logiques plus de 1023 Go à votre machine virtuelle.

Il existe certaines limites à l’évolutivité lors de la conception de vos déploiements de stockage Azure - pour plus d’informations, consultez [limites de service et d’abonnement Microsoft Azure, quotas et les contraintes](azure-subscription-service-limits.md#storage-limits) . Consultez également les [cibles de performances et l’évolutivité du stockage Azure](../storage/storage-scalability-targets.md).

Pour le stockage de l’application, vous pouvez stocker les données d’objet non structurés tels que documents, images, sauvegardes, données de configuration, les journaux, etc.. à l’aide du stockage des objets blob. Au lieu de votre application écrit sur un disque virtuel est connecté à l’ordinateur virtuel, l’application peut écrire directement vers le stockage blob Azure. Stockage des objets BLOB fournit également la possibilité de [différents niveaux de stockage à chaud et intéressantes](../storage/storage-blob-storage-tiers.md) en fonction de vos besoins de disponibilité et les contraintes de coût.


## <a name="striped-disks"></a>Disques agrégés par bandes
En plus de ce qui vous permet de créer plus de 1 023 Go, dans de nombreux cas, des disques utilisant l’entrelacement de disques de données améliore les performances en autorisant plusieurs BLOB sauvegarder le stockage pour un seul volume. Avec l’agrégation par bandes, les e/s requis pour écrire et lire des données à partir d’un seul disque logique se déroule en parallèle.

Azure impose des limites sur le nombre de disques de données et la quantité de bande passante disponible, en fonction de la taille de la mémoire virtuelle. Pour plus d’informations, reportez-vous à la section [tailles pour les machines virtuelles](virtual-machines-linux-sizes.md).

Si vous utilisez l’entrelacement de disques de données Azure, tenez compte des instructions suivantes :

- Disques de données doivent toujours être la taille maximale (1023 Go).
- Joindre les disques de données maximal autorisés pour la taille de la mémoire virtuelle.
- Utilisez le LVM.
- Évitez d’utiliser le disque de données Azure options mise en cache (mise en cache de la stratégie = aucune).

Pour plus d’informations, consultez [Configuration de gestionnaire de volume logique sur un ordinateur virtuel de Linux](virtual-machines-linux-configure-lvm.md).


## <a name="multiple-storage-accounts"></a>Plusieurs comptes de stockage

Lors de la conception de votre environnement de stockage Azure, vous pouvez utiliser plusieurs comptes de stockage que le nombre d’ordinateurs virtuels, vous déployez augmente. Cette approche permet de répartir les e/s sur l’infrastructure de stockage Azure pour maintenir des performances optimales pour vos machines virtuelles et les applications. Lorsque vous créez les applications que vous déployez, prendre en compte les exigences d’e/s qu'a de chaque machine virtuelle et le solde à ces ordinateurs virtuels entre les comptes de stockage Azure. Essayez d’éviter de grouper tous les les e/s élevées exigent des ordinateurs virtuels dans seulement un ou deux comptes de stockage.

Pour plus d’informations sur les fonctionnalités d’e/s de différentes options de stockage Azure et certains vous recommandons maximums, voir des [cibles de performances et l’évolutivité du stockage Azure](../storage/storage-scalability-targets.md).


## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 