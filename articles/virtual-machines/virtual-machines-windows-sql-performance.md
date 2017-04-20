<properties
    pageTitle="Méthodes conseillées en matière de performances pour SQL Server | Microsoft Azure"
    description="Décrit les méthodes conseillées pour l’optimisation des performances de SQL Server dans Microsoft Azure VM."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/07/2016"
    ms.author="jroth" />

# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Méthodes conseillées en matière de performances pour SQL Server dans Azure Virtual Machines

## <a name="overview"></a>Vue d’ensemble

Cette rubrique fournit des recommandations pour l’optimisation des performances de SQL Server dans Microsoft Azure Virtual Machine. Lors de l’exécution de SQL Server dans Azure Virtual Machines, nous vous recommandons de continuer en utilisant la même les options qui s’applique à SQL Server dans un environnement de serveur local de réglage des performances de base de données. Toutefois, les performances d’une base de données relationnelle dans un cloud public dépendant de nombreux facteurs tels que la taille d’une machine virtuelle et la configuration des disques de données.

Lors de la création d’images de SQL Server, [envisagez la mise en service de vos ordinateurs virtuels dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md). Machines virtuelles de SQL Server mise en service dans le portail avec le Gestionnaire de ressources implémente toutes ces meilleures pratiques, y compris la configuration du stockage.

Cet article se concentre sur l’obtention de *meilleures* performances pour SQL Server sur Azure VM. Si votre charge de travail est d’alléger la charge, vous pouvez être amené pas chaque optimisation répertoriée ci-dessous. Prendre en compte vos besoins de performance et les modèles de charge de travail lors de l’évaluation de ces recommandations.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="quick-check-list"></a>Liste de vérification rapide

Voici une liste de vérification rapide pour optimiser les performances de SQL Server Azure machines virtuelles en fonctionnement :

|Zone|Optimisations|
|---|---|
|[Taille de mémoire virtuelle](#vm-size-guidance)|[DS3](virtual-machines-windows-sizes.md#standard-tier-ds-series) ou plus pour l’édition entreprise de SQL.<br/><br/>[DS2](virtual-machines-windows-sizes.md#standard-tier-ds-series) ou supérieure pour les éditions Standard de SQL et Web.|
|[Stockage](#storage-guidance)|Utilisez le [stockage de la prime](../storage/storage-premium-storage.md). Stockage standard est recommandé uniquement pour le développement/test.<br/><br/>Conserver le [compte de stockage](../storage/storage-create-storage-account.md) et de la machine virtuelle de SQL Server dans la même région.<br/><br/>Désactiver Azure [stockage redondant geo](../storage/storage-redundancy.md) (geo-réplication) sur le compte de stockage.|
|[Disques](#disks-guidance)|Utiliser un minimum de 2 [P30 disques](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage) (1 pour les fichiers journaux ; 1 pour les fichiers de données et TempDB).<br/><br/>Évitez d’utiliser le système d’exploitation ou disques temporaires pour le stockage de base de données ou.<br/><br/>Activer la mise en cache sur le disque hébergeant les fichiers de données et TempDB de lecture.<br/><br/>N’activez pas la mise en cache sur les disques qui héberge le fichier journal.<br/><br/>Important : Arrêter le service SQL Server lorsque vous modifiez les paramètres de cache de disque Azure VM.<br/><br/>Plusieurs disques de données Azure pour obtenir d’améliorer le débit d’e/s de bande.<br/><br/>Mettre en forme avec une taille d’allocation documentées.|
|[E/S](#io-guidance)|Activer la compression de page de base de données.<br/><br/>Activer l’initialisation instantanée des fichiers pour les fichiers de données.<br/><br/>Limiter ou désactiver les paramètres d’étendue automatique sur la base de données.<br/><br/>Désactiver la réduction automatique de la base de données.<br/><br/>Déplacez toutes les bases de données vers des disques de données, y compris les bases de données système.<br/><br/>Déplacer SQL Server erreur journal et la trace de répertoires des fichiers pour les disques de données.<br/><br/>Configuration des emplacements de fichiers de sauvegarde et de la base de données par défaut.<br/><br/>Activer les pages verrouillées.<br/><br/>Appliquer les correctifs de performances de SQL Server.|
|[Fonctionnalité spécifique](#feature-specific-guidance)|Sauvegarder directement sur le stockage des objets blob.|

Pour plus d’informations sur *comment* et *Pourquoi* effectuer ces optimisations, veuillez consulter les détails et les instructions fournies dans les sections suivantes.

## <a name="vm-size-guidance"></a>Conseils de taille de machine virtuelle

Pour les applications sensibles de performances, il est recommandé d’utiliser [des tailles de machines virtuelles](virtual-machines-windows-sizes.md)suivantes :

- **SQL Server Enterprise Edition**: DS3 ou supérieur

- **SQL Server Standard et les éditions Web**: DS2 ou supérieur


## <a name="storage-guidance"></a>Conseils de stockage

Prise en charge des ordinateurs virtuels série DS (avec DSv2-séries et GS) [Premium stockage](../storage/storage-premium-storage.md). Stockage de prime est recommandée pour toutes les charges de travail.

> [AZURE.WARNING] Stockage standard possède différentes latences et la bande passante et n’est recommandé que pour les charges de travail de développement/test. Charges de travail doivent utiliser le stockage de la prime.

En outre, nous vous conseillons de créer votre compte de stockage Azure dans le même centre de données en tant que vos machines virtuelles de SQL Server afin de réduire les délais de transfert. Lors de la création d’un compte de stockage, désactivez géo-réplication comme ordre d’écriture cohérent sur plusieurs disques n’est pas garanti. À la place, envisagez alors de configurer une technologie de récupération d’urgence de SQL Server entre deux Azure centres de données. Pour plus d’informations, reportez-vous à la section [haute disponibilité et reprise après sinistre pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Conseils de disques

Il existe trois types principaux de disque sur un ordinateur virtuel d’Azure :

- **Disque du système d’exploitation**: lorsque vous créez un ordinateur virtuel de Azure, la plate-forme s’attache au moins un disque (comme le lecteur **C** ) de la machine virtuelle pour le disque de votre système d’exploitation. Ce disque est un disque dur virtuel stocké sous la forme d’un objet dans le stockage blob de page.
- **Disque temporaire**: ordinateurs virtuels Azure contiennent un autre disque, appelé le disque temporaire (étiqueté comme le **D**: lecteur). Il s’agit d’un disque sur le nœud qui peut être utilisé pour l’espace de travail.
- **Disques de données**: vous pouvez également attacher des disques supplémentaires à votre machine virtuelle en tant que disques de données, et ces seront stockées dans le stockage en tant que BLOB de la page.

Les sections suivantes décrivent des recommandations pour l’utilisation de ces disques différents.

### <a name="operating-system-disk"></a>Disque du système d’exploitation

Un disque du système d’exploitation est un disque dur virtuel que vous pouvez démarrer et monter en tant que version en cours d’exécution du système d’exploitation et est étiqueté comme lecteur **C** .

Valeur par défaut de la mise en cache de la stratégie sur le disque du système d’exploitation est **En lecture-écriture**. Pour les applications sensibles de performances, nous vous recommandons d’utiliser des disques de données au lieu du disque du système d’exploitation. Reportez-vous à la section sur les disques de données ci-dessous.

### <a name="temporary-disk"></a>Disque temporaire

Le lecteur de stockage temporaire appelé le **D**: lecteur, n’est pas rendue persistante dans le stockage blob Azure. Ne stockez pas vos fichiers de base de données utilisateur ou les fichiers journaux des transactions utilisateur sur la **D**: lecteur.

Pour la série D, série Dv2 et machines virtuelles de la série G, le lecteur temporaire sur ces ordinateurs virtuels est basée sur les SSD. Si votre charge de travail fait un usage intensif de TempDB (par exemple, pour les objets temporaires ou des jointures complexes), stockant TempDB sur le lecteur **D** peut entraîner un débit plus élevé TempDB et moins de latence de TempDB.

Pour les machines virtuelles qui prennent en charge le stockage de prime (série DS, DSv2-series et GS-series), nous vous recommandons de stocker les TempDB et/ou des Extensions de Pool de mémoire tampon sur un disque qui prend en charge le stockage de prime avec cache de lecture activé. Il existe une exception à cette recommandation ; Si l’utilisation de TempDB est gourmande en écriture, vous pouvez obtenir les meilleures performances en stockant TempDB sur le disque local **D** , qui est également basée sur SSD sur ces tailles de machine.

### <a name="data-disks"></a>Disques de données

- **Utilisez des disques de données pour les données et les fichiers journaux**: au minimum, utilisez 2 prime de stockage [P30 disques](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage) où un disque contient les ou les fichiers journaux et l’autre contient les fichiers de données et TempDB.

- **Entrelacement**: pour plus de débit, vous pouvez ajouter des disques de données supplémentaires et utiliser la répartition des disques. Pour déterminer le nombre de disques de données, vous avez besoin analyser le nombre des Ops ES/s disponibles pour vos disques de données et de journaux. Pour obtenir ces informations, consultez les tableaux sur Ops ES/s par taille de disque et de la [taille de mémoire virtuelle](virtual-machines-windows-sizes.md) dans l’article suivant : [Stockage de prime à l’aide de disques](../storage/storage-premium-storage.md). Utilisez le code suivant ces instructions :

    - Pour Windows 8 et Windows Server 2012 ou version ultérieure, utilisez des [Espaces de stockage](https://technet.microsoft.com/library/hh831739.aspx). Définir la taille de répartition de 64 Ko pour les charges de travail OLTP et 256 Ko pour les charges de gestion de magasins de données afin d’éviter l’impact sur les performances en raison d’un mauvais alignement de la partition. En outre, définir le nombre de colonnes = nombre de disques physiques. Pour configurer un espace de stockage avec des disques de plus de 8, vous devez utiliser PowerShell (pas Gestionnaire de serveur UI) pour définir explicitement le nombre de colonnes pour correspondre au nombre de disques. Pour plus d’informations sur la configuration des [Espaces de stockage](https://technet.microsoft.com/library/hh831739.aspx), reportez-vous à la section [Applets d’espaces de stockage dans Windows PowerShell](https://technet.microsoft.com/library/jj851254.aspx)

    - Pour Windows 2008 R2 ou version antérieure, vous pouvez utiliser des disques dynamiques (volumes du système d’exploitation agrégés par bande) et la taille de répartition est toujours de 64 Ko. Notez que cette option est déconseillée à Windows 8 et Windows Server 2012. Pour plus d’informations, consultez la déclaration de prise en charge au [Service de disque virtuel est en cours de transition à l’API de gestion de stockage Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

    - Si votre charge de travail n’est pas beaucoup de journal et n’a pas besoin des Ops ES/s dédiés, vous pouvez configurer un pool de stockage. Sinon, créez deux pools de stockage, un pour les fichiers journaux et un autre pool de stockage pour les fichiers de données et TempDB. Déterminer le nombre de disques associés à chaque pool de stockage en fonction de vos attentes en matière de charge. Gardez à l’esprit que les différentes tailles de machine virtuelle permettent différents numéros de disques de données attaché. Pour plus d’informations, reportez-vous à la section [tailles pour les Machines virtuelles](virtual-machines-windows-sizes.md).

    - Si vous n’utilisez pas le stockage de prime (scénarios de développement/test), la recommandation consiste à ajouter le nombre maximal de disques de données pris en charge par la [taille de mémoire virtuelle](virtual-machines-windows-sizes.md) et utilisez l’entrelacement.

- **Mise en cache de la stratégie**: activer les disques de données pour le stockage de prime, le cache de lecture sur les disques de données hébergeant les fichiers de données et TempDB uniquement. Si vous n’utilisez pas le stockage de la prime, n’activez pas toute la mise en cache sur les disques de données. Pour obtenir des instructions sur la configuration de la mise en cache du disque, consultez les rubriques suivantes : [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) et [AzureDataDisk de l’ensemble](https://msdn.microsoft.com/library/azure/jj152851.aspx).

    >[AZURE.WARNING] Arrêtez le service SQL Server lorsque vous modifiez le paramètre de cache de disques Azure VM pour éviter une altération de la base de données.

- **Taille d’unité d’allocation NTFS**: lors du formatage du disque de données, il est recommandé que vous utilisez une taille d’unité d’allocation de 64 Ko pour les données et les fichiers journaux ainsi que TempDB.

- **Meilleures pratiques de gestion de disque**: lorsque la suppression d’un disque de données ou de la modification de son type de cache, arrêtez le service de SQL Server lors de la modification. Lorsque les paramètres de mise en cache sont modifiés sur le disque du système d’exploitation, Azure arrête la machine virtuelle, modifie le type de cache et redémarrage de la machine virtuelle. Lorsque les paramètres de cache d’un disque de données sont modifiées, la machine virtuelle n’est pas arrêtée, mais le disque de données est détaché de la machine virtuelle lors de la modification, puis de les rattacher.

    >[AZURE.WARNING] Arrêter le service SQL Server au cours de ces opérations peut provoquer une corruption de base de données.

## <a name="io-guidance"></a>Conseils d’e/s

- Les meilleurs résultats avec un stockage de prime sont atteints lorsque vous parallélisez votre application et les demandes. Stockage de prime est conçu pour les scénarios où la profondeur de la file d’attente d’e/s est supérieure à 1, voir peu ou pas des gains de performances pour les demandes de série à thread unique (même si elles sont intensives de stockage). Par exemple, cela peut affecter les résultats des tests de mono-thread des outils d’analyse de performances, par exemple SQLIO.

- Envisagez d’utiliser la [compression de page de base de données](https://msdn.microsoft.com/library/cc280449.aspx) peut aider à améliorer les performances des charges de travail intensives d’e/s. Toutefois, la compression de données peut augmenter la consommation de processeur sur le serveur de base de données.

- Pensez à activer l’initialisation instantanée des fichiers afin de réduire le temps nécessaire pour l’allocation du fichier initial. Pour tirer parti de l’initialisation instantanée des fichiers, vous accordez au compte de service SQL Server (MSSQLSERVER) avec l’autorisation SE_MANAGE_VOLUME_NAME et l’ajoutez à la stratégie de sécurité **d’Effectuer des tâches de gestion des volumes** . Si vous utilisez une image de la plate-forme SQL Server pour Azure, le compte de service par défaut (NT Service\MSSQLSERVER) n’est pas ajouté à la stratégie de sécurité **d’Effectuer des tâches de gestion des volumes** . En d’autres termes, l’initialisation instantanée de fichiers n’est pas activée dans une image de la plateforme Azure de SQL Server. Après avoir ajouté le compte de service SQL Server à la stratégie de sécurité **d’Effectuer des tâches de gestion des volumes** , redémarrez le service SQL Server. Il peut y avoir des considérations de sécurité pour l’utilisation de cette fonctionnalité. Pour plus d’informations, consultez [Initialisation des fichiers de base de données](https://msdn.microsoft.com/library/ms175935.aspx).

- **croissance automatique** est considérée comme simplement une éventualité de croissance inattendue. Ne gérez pas la croissance de vos données et de journaux sur une base quotidienne avec les paramètres d’étendue automatique. Si étendue automatique est utilisé, avant d’augmenter le fichier en utilisant le commutateur de taille.

- Assurez-vous que la **réduction automatique** est désactivée pour éviter toute surcharge inutile qui peut nuire aux performances.

- Déplacez toutes les bases de données vers des disques de données, y compris les bases de données système. Pour plus d’informations, voir [Déplacer des bases de données système](https://msdn.microsoft.com/library/ms345408.aspx).

- Déplacer SQL Server erreur journal et la trace de répertoires des fichiers pour les disques de données. Cela est possible dans le Gestionnaire de Configuration SQL Server, avec le bouton droit de votre instance de SQL Server et sélectionnez Propriétés. Les paramètres du fichier journal et la trace erreur peuvent être modifiées dans l’onglet **Paramètres de démarrage** . Le répertoire de l’image est spécifié dans l’onglet **Avancé** . La capture d’écran suivante indique où rechercher le paramètre de démarrage du journal des erreurs.

    ![Capture d’écran du journal des erreurs SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

- Configuration des emplacements de fichiers de sauvegarde et de la base de données par défaut. Utilisez les recommandations de cette rubrique et apportez les modifications dans la fenêtre Propriétés du serveur. Pour plus d’informations, consultez [Afficher ou modifier les emplacements par défaut pour les données et les fichiers journaux (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). La capture d’écran suivante montre où effectuer ces modifications.

    ![Fichiers de sauvegarde et du journal de données SQL](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)

- Activer les pages verrouillées afin de réduire les e/s et toute activité de pagination. Pour plus d’informations, voir [Activer le verrouillage de Pages dans l’Option mémoire (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Si vous exécutez SQL Server 2012, installez 10 de mise à jour Cumulative de Service Pack 1. Cette mise à jour contient le correctif des mauvaises performances d’e/s lorsque vous exécutez select dans l’instruction de table temporaire dans SQL Server 2012. Pour plus d’informations, consultez cet [article de la base de connaissances](http://support.microsoft.com/kb/2958012).

- Envisagez la compression des fichiers de données lors du transfert d’entrée/sortie d’Azure.

## <a name="feature-specific-guidance"></a>Fonction des recommandations spécifiques

Certains déploiements peuvent profiter des avantages de performances supplémentaires à l’aide de techniques de configuration plus avancées. La liste suivante met en évidence certaines fonctionnalités de SQL Server qui peuvent vous aider à obtenir de meilleures performances :

- **Sauvegarde vers le stockage Azure**: lors de l’exécution des sauvegardes de SQL Server en cours d’exécution sur des machines virtuelles Azure, vous pouvez utiliser la [Sauvegarde de SQL Server à l’URL](https://msdn.microsoft.com/library/dn435916.aspx). Cette fonctionnalité est disponible à partir de SQL Server 2012 SP1 CU2 et recommandé pour une sauvegarde sur les disques de données attaché. Lorsque vous sauvegarde/restauration vers/à partir d’un stockage Azure, suivez les recommandations fournies à la [Sauvegarde de SQL Server à l’URL de meilleures pratiques et de résolution des problèmes et de restauration à partir de sauvegardes stockées dans le stockage Azure](https://msdn.microsoft.com/library/jj919149.aspx). Vous pouvez également automatiser ces sauvegardes à l’aide de la [Sauvegarde automatisée de SQL Server dans Azure Virtual Machines](virtual-machines-windows-classic-sql-automated-backup.md).

    Avant SQL Server 2012, vous pouvez utiliser [Sauvegarde de SQL Server vers l’outil d’Azure](https://www.microsoft.com/download/details.aspx?id=40740). Cet outil peut aider à augmenter le débit de sauvegarde à l’aide de plusieurs cibles de bandes de sauvegarde.

- **Fichiers de données SQL Server dans Azure**: cette nouvelle fonctionnalité, les [Fichiers de données SQL Server dans Azure](https://msdn.microsoft.com/library/dn385720.aspx), est disponible à partir de SQL Server 2014. Exécution de SQL Server avec des fichiers de données dans Azure montre les caractéristiques des performances comparables à l’aide de disques de données Azure.

## <a name="next-steps"></a>Étapes suivantes

Si vous vous intéressez dans l’exploration de SQL Server et le stockage de la prime dans plus de détails, voir l’article [Utiliser le stockage Azure Premium avec SQL Server sur des Machines virtuelles](virtual-machines-windows-classic-sql-server-premium-storage.md).

Pour les méthodes conseillées, consultez [Considérations relatives à la sécurité pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-security.md).

Consultez les autres rubriques de la Machine virtuelle de SQL Server à [SQL Server sur la vue d’ensemble des Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).
