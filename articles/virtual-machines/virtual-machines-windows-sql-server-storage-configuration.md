<properties
    pageTitle="Configuration du stockage pour les machines virtuelles de SQL Server | Microsoft Azure"
    description="Cette rubrique décrit comment Azure configure le stockage pour les machines virtuelles de SQL Server lors de la configuration (modèle de déploiement Gestionnaire de ressources). Il explique également comment vous pouvez configurer le stockage pour vos ordinateurs virtuels de de SQL Server existant."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="ninarn"
    manager="jhubbard"    
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/04/2016"
    ms.author="ninarn" />

# <a name="storage-configuration-for-sql-server-vms"></a>Configuration du stockage pour les machines virtuelles de SQL Server

Lorsque vous configurez une image de machine virtuelle SQL Server dans Azure, le portail permet d’automatiser la configuration de votre stockage. Cela inclut l’association de stockage de la machine virtuelle, effectue ce stockage accessible à SQL Server et à le configurer pour optimiser pour vos besoins spécifiques en performances.

Cette rubrique explique comment Azure configure stockage pour vos ordinateurs virtuels de SQL Server à la fois au cours de la mise en service et pour les machines virtuelles existantes. Cette configuration est basée sur les [meilleures pratiques de performances](virtual-machines-windows-sql-performance.md) pour les machines virtuelles d’Azure exécutant SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

## <a name="prerequisites"></a>Conditions préalables
Pour utiliser les paramètres de configuration de stockage automatisé, votre machine virtuelle nécessite les caractéristiques suivantes :

- Configuré avec une [image de la galerie de SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#option-1-deploy-a-sql-vm-per-minute-licensing).
- Utilise le [modèle de déploiement de gestionnaire de ressources](../resource-manager-deployment-model.md).
- Utilise le [stockage de la prime](../storage/storage-premium-storage.md).

## <a name="new-vms"></a>Nouvelles machines virtuelles
Les sections suivantes décrivent comment configurer le stockage pour les machines virtuelles de SQL Server.

### <a name="azure-portal"></a>Azure Portal
Lors de la mise en service d’un Azure VM à l’aide d’une image de la galerie de SQL Server, vous pouvez choisir de configurer automatiquement le stockage pour votre nouvelle machine virtuelle. Vous spécifiez la taille de stockage, les limites de performances et type de charge de travail. La capture d’écran suivante affiche la lame de configuration de stockage utilisée SQL VM lors de la mise en service.

![Configuration de stockage de machine virtuelle de SQL Server au cours de la mise en service](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

En fonction de vos choix, Azure exécute les tâches de configuration de stockage suivantes après la création de la machine virtuelle :

- Crée et attache des disques de données de stockage de prime sur l’ordinateur virtuel.
- Configure les disques de données pour être accessible à SQL Server.
- Configure les disques de données dans un pool de stockage en fonction des taille et les performances (IOPS et débit) exigences spécifiées.
- Associe le pool de stockage à un nouveau lecteur sur l’ordinateur virtuel.
- Optimise ce nouveau lecteur, en fonction de votre type de charge de travail spécifiée (de Data warehousing, transactionnel traitement ou général).

Pour plus de détails sur comment Azure configure les paramètres de stockage, reportez-vous à la [section de configuration de stockage](#storage-configuration). Pour une procédure complète de création d’une machine virtuelle de SQL Server dans le portail Azure, consultez [le didacticiel de mise en service](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Gérer les modèles de ressources
Si vous utilisez les Gestionnaire de ressources de modèles suivants, deux disques de données premium sont joints par défaut, sans aucune configuration de pool de stockage. Toutefois, vous pouvez personnaliser ces modèles pour modifier le nombre de disques de données de prime qui sont attachés à l’ordinateur virtuel.

- [Créer un ordinateur virtuel avec sauvegarde automatisée](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
- [Créer un ordinateur virtuel avec correction automatique](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
- [Créer un ordinateur virtuel grâce à l’intégration de AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Machines virtuelles existantes
Pour les machines virtuelles SQL Server existantes, vous pouvez modifier certains paramètres de stockage dans le portail Azure. Sélectionnez votre machine virtuelle, accédez à la zone Paramètres et puis sélectionnez la Configuration de SQL Server. La lame de Configuration de SQL Server affiche l’utilisation du stockage actuelle de votre machine virtuelle. Tous les lecteurs qui existent sur votre ordinateur virtuel sont affichées dans ce graphique. Pour chaque lecteur, l’espace de stockage affiche dans quatre sections :

- Données SQL
- Journal SQL
- Autres (stockage non-SQL)
- Disponible

![Configurer le stockage pour existante de SQL Server VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Pour configurer le stockage pour ajouter un nouveau lecteur ou d’étendre un lecteur existant, cliquez sur le lien Modifier au-dessus du graphique.

Les options de configuration qui s’affiche varie selon que vous avez utilisé cette fonctionnalité avant. Lorsque vous utilisez pour la première fois, vous pouvez spécifier vos besoins de stockage pour un nouveau lecteur. Si vous utilisiez cette fonctionnalité pour créer un lecteur, vous pouvez choisir d’étendre ce d’une capacité de stockage.

### <a name="use-for-the-first-time"></a>Utilisation pour la première fois
S’il s’agit de votre première fois à l’aide de cette fonctionnalité, vous pouvez spécifier les limites de taille et les performances de stockage pour un nouveau lecteur. Cette expérience est similaire à ce que vous voyez à la mise en service de temps. La principale différence est que vous n’êtes pas autorisé à spécifier le type de charge de travail. Cette restriction empêche l’interruption des configurations existantes de SQL Server sur l’ordinateur virtuel.

![Configurer les curseurs de stockage SQL Server](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

Azure crée un nouveau lecteur, en fonction de vos spécifications. Dans ce scénario, Azure exécute les tâches de configuration de stockage suivantes :

- Crée et attache des disques de données de stockage de prime sur l’ordinateur virtuel.
- Configure les disques de données pour être accessible à SQL Server.
- Configure les disques de données dans un pool de stockage en fonction des taille et les performances (IOPS et débit) exigences spécifiées.
- Associe le pool de stockage à un nouveau lecteur sur l’ordinateur virtuel.

Pour plus de détails sur comment Azure configure les paramètres de stockage, reportez-vous à la [section de configuration de stockage](#storage-configuration).

### <a name="add-a-new-drive"></a>Ajouter un nouveau lecteur
Si vous avez déjà configuré le stockage sur votre ordinateur virtuel de SQL Server, extension de stockage affiche deux nouvelles options. La première option est d’ajouter un nouveau lecteur, ce qui peut augmenter le niveau de performance de votre machine virtuelle.

![Ajouter un nouveau lecteur à une VM de SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

Toutefois, après avoir ajouté le lecteur, vous devez effectuer une configuration manuelle supplémentaire pour obtenir l’augmentation des performances.

### <a name="extend-the-drive"></a>Étendre le lecteur
L’autre option pour le stockage de développement est d’étendre le lecteur existant. Cette option augmente la capacité de stockage de votre lecteur, mais il n’augmente pas les performances. Avec les pools de stockage, vous ne pouvez pas modifier le nombre de colonnes après la création du pool de stockage. Le nombre de colonnes détermine le nombre d’écritures parallèles, qui peuvent être réparties sur les disques de données. Par conséquent, les disques de données ajouté ne peut pas augmenter les performances. Ils ne peuvent fournir davantage de stockage que pour les données écrites. Cette limitation signifie également que, lorsque vous étendez le lecteur, le nombre de colonnes détermine le nombre minimum de disques de données que vous pouvez ajouter. Par conséquent, si vous créez un pool de stockage avec des disques de données de quatre, le nombre de colonnes est également quatre. Chaque fois que vous étendez le stockage, vous devez ajouter des disques de données d’au moins quatre.

![Étendre un lecteur pour un VM de SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Configuration du stockage
Cette section fournit une référence pour les modifications de configuration de stockage Azure s’exécute automatiquement pendant la VM SQL mise en service ou de la configuration du portail Azure.

- Si vous avez sélectionné moins de deux to de stockage de votre machine virtuelle, Azure ne crée pas un pool de stockage.
- Si vous avez sélectionné au moins deux to de stockage de votre machine virtuelle, Azure configure un pool de stockage. La section suivante de cette rubrique fournit les détails de la configuration de pool de stockage.
- Toujours à la configuration de stockage automatique utilise des disques de données de [stockage de prime](../storage/storage-premium-storage.md) P30. Il est donc un mappage 1:1 entre votre nombre de téraoctets sélectionné et le nombre de disques de données associés à votre machine virtuelle.

Informations de tarification, consultez la page [tarification de stockage](https://azure.microsoft.com/pricing/details/storage) sous l’onglet **Stockage sur disque** .

### <a name="creation-of-the-storage-pool"></a>Création d’un pool de stockage
Azure utilise les paramètres suivants pour créer le pool de stockage sur des machines virtuelles de SQL Server.

| Paramètre | Valeur |
|-----|-----|
| Taille de répartition  | 256 Ko (Data warehousing) ; 64 Ko (transactionnel) |
| Tailles de disque | 1 to |
| Mémoire cache | En lecture |
| Taille d’allocation | Taille d’unité d’allocation NTFS 64 Ko |
| Initialisation instantanée des fichiers | Activé |
| Verrouiller des pages en mémoire | Activé |
| Récupération | Récupération simple (sans tolérance) |
| Nombre de colonnes | Nombre de disques de données<sup>1</sup> |
| Emplacement de TempDB | Stockées sur les disques de données<sup>2</sup> |

<sup>1</sup> une fois le pool de stockage est créé, vous ne pouvez pas modifier le nombre de colonnes dans le pool de stockage.

<sup>2</sup> ce paramètre s’applique uniquement à la première unité que vous créez à l’aide de la fonctionnalité de configuration de stockage.

## <a name="workload-optimization-settings"></a>Paramètres d’optimisation de la charge de travail
Le tableau suivant décrit les options de type de trois charges de travail disponibles et leurs optimisations correspondantes :

| Type de charge de travail | Description | Optimisations |
|-----|-----|-----|
| **Général** | Paramètre par défaut qui prend en charge la plupart des charges de travail | Aucun |
| **Traitement transactionnel** | Optimise le stockage pour les charges de travail OLTP traditionnels de la base de données | Indicateur de trace 1117<br/>Indicateur de suivi 1118 |
| **Entreposage de données** | Optimise le stockage pour les charges de travail d’analyse et de création de rapports | Indicateur de trace 610<br/>Indicateur de trace 1117 |

>[AZURE.NOTE] Vous ne pouvez spécifier le type de charge de travail lorsque vous configurez un ordinateur virtuel SQL en le sélectionnant dans l’étape de configuration de stockage.

## <a name="next-steps"></a>Étapes suivantes
Pour les autres rubriques relatives à l’exécution de SQL Server dans Azure VM, consultez [SQL Server Azure machines virtuelles en fonctionnement](virtual-machines-windows-sql-server-iaas-overview.md).
