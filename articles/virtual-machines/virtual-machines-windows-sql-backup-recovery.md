<properties
    pageTitle="Sauvegarde et restauration pour SQL Server | Microsoft Azure"
    description="Décrit les considérations de sauvegarde et de restauration pour les bases de données SQL Server en cours d’exécution sur Azure Virtual Machines."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth" />

# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Sauvegarde et restauration de SQL Server sur des Machines virtuelles Azure

## <a name="overview"></a>Vue d’ensemble

Sauvegarde des données dans les bases de données SQL Server est une partie importante de la stratégie de protection contre la perte de données en raison d’erreurs d’application ou de l’utilisateur. Cela est également vrai pour SQL Server en cours d’exécution sur les ordinateurs virtuels Azure (VMs).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Pour de SQL Server en cours d’exécution dans Azure VM, vous pouvez utiliser sauvegarde en mode natif et restaurer les techniques utilisant des disques connectés à la destination des fichiers de sauvegarde. Toutefois, est une limite au nombre de disques que vous pouvez attacher à une machine virtuelle Azure, en fonction de la [taille de la machine virtuelle](virtual-machines-linux-sizes.md). Il existe également la charge de la gestion des disques à prendre en compte.

À partir de SQL Server 2014, vous pouvez sauvegarder et restaurer pour le stockage des objets Blob Azure Microsoft. SQL Server 2016 fournit également des améliorations pour cette option. En outre, pour les fichiers de base de données stockés dans le stockage Blob Azure de Microsoft, SQL Server 2016 fournit une option de sauvegardes quasi instantanées et de restaurations rapides à l’aide de snapshots Azure. Cet article fournit une vue d’ensemble de ces options et vous trouverez plus d’informations sur [SQL Server sauvegarde et restauration avec le Service de stockage de Microsoft Azure Blob](https://msdn.microsoft.com/library/jj919148.aspx).

>[AZURE.NOTE] Pour une description des options de sauvegarde des bases de données très volumineuses, voir [Stratégies de sauvegarde de base de données de plusieurs téraoctets SQL Server pour Azure Virtual Machines](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx).

Les sections suivantes incluent des informations spécifiques sur les différentes versions de SQL Server prises en charge dans une machine virtuelle Azure.

## <a name="sql-server-virtual-machines"></a>Machines virtuelles de SQL Server

Lorsque votre instance de SQL Server est en cours d’exécution sur une Machine virtuelle de Azure, vos fichiers de base de données se trouvent déjà sur les disques de données sur Azure. Ces disques dynamiques dans le stockage des objets Blob Azure. Les raisons de la sauvegarde de votre base de données et les approches vous prennent maintenant modifier légèrement. Considérez les points suivants. 

- Vous n’avez plus besoin d’effectuer des sauvegardes de base de données pour offrir une protection contre les pannes de matériel ou de support, car Microsoft Azure fournit cette protection en tant que partie du service Microsoft Azure.

- Vous devez toujours effectuer des sauvegardes de base de données pour offrir une protection contre les erreurs de l’utilisateur, ou pour des besoins d’archivage, des raisons réglementaires ou à des fins administratives.

- Vous pouvez stocker le fichier de sauvegarde directement dans Azure. Pour plus d’informations, consultez les sections suivantes qui fournissent des conseils pour les différentes versions de SQL Server.

## <a name="sql-server-2016"></a>SQL Server 2016

2016 de Microsoft SQL Server prend en charge les fonctionnalités de [sauvegarde et restauration de BLOB Azure](https://msdn.microsoft.com/library/jj919148.aspx) dans SQL Server 2014. Mais il inclut également les améliorations suivantes :

| Amélioration de 2016               | Détails                          |
|---------------------|-------------------------------|
| **Agrégat par bandes**              | Lorsque vous sauvegardez dans le stockage blob de Microsoft Azure, SQL Server 2016 prend en charge la sauvegarde sur plusieurs BLOB pour permettre la sauvegarde des bases de données volumineuses, jusqu'à un maximum de 12,8 To.      |
| **Sauvegarde de snapshots**                | À l’aide de snapshots Azure, sauvegarde instantanée-fichier de SQL Server fournit des sauvegardes quasi instantanées et des restaurations rapides pour les fichiers de base de données stockées en utilisant le service de stockage Azure Blob. Cette fonctionnalité vous permet de simplifier la sauvegarde et la restauration des stratégies. Sauvegarde de l’instantané de fichiers prend également en charge le point de restauration dans le temps. Pour plus d’informations, voir [Les sauvegardes de capture instantanée pour les fichiers de base de données dans Azure](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx).   |
| **Gérer la planification des sauvegardes**            | SQL Server gérés sauvegarde vers Azure prend désormais en charge les plannings personnalisés. Pour plus d’informations, consultez [SQL Server des sauvegardes gérées par Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx).   |

Pour un didacticiel sur les fonctionnalités de SQL Server 2016 lors de l’utilisation de stockage des objets Blob Azure, consultez [didacticiel : à l’aide du service de stockage de Microsoft Azure Blob avec les bases de données SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx).

## <a name="sql-server-2014"></a>SQL Server 2014

2014 de SQL Server inclut les améliorations suivantes :

1. **Sauvegarde et restauration pour Azure**:

 - *Sauvegarde de SQL Server à URL* prend désormais en charge dans SQL Server Management Studio. L’option de sauvegarde vers Azure est maintenant disponible lors de l’utilisation de la tâche de sauvegarde ou de restauration, ou Assistant plan de maintenance dans SQL Server Management Studio. Pour plus d’informations, voir [Sauvegarde de SQL Server à l’URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
 - *SQL Server gérés sauvegarde vers Azure* a une nouvelle fonctionnalité qui permet la gestion automatique de la sauvegarde. Ceci est particulièrement utile pour l’automatisation de la gestion des sauvegardes pour les instances de SQL Server 2014 s’exécutant sur une Machine d’Azure. Pour plus d’informations, consultez [SQL Server des sauvegardes gérées par Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx).
 - *Automatisée de sauvegarde* fournit une automatisation supplémentaire pour activer automatiquement le *SQL Server gérés sauvegarde vers Azure* sur toutes les bases de données existants et nouveaux d’un ordinateur virtuel de SQL Server dans Azure. Pour plus d’informations, voir [Sauvegarde automatisée de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).
 - Pour une vue d’ensemble de toutes les options de sauvegarde 2014 Azure SQL Server, consultez [SQL Server sauvegarde et restauration avec le Service de stockage de Microsoft Azure Blob](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).

1. **Cryptage**: SQL Server 2014 prend en charge le cryptage des données lors de la création d’une sauvegarde. Il prend en charge plusieurs algorithmes de cryptage et de l’utilisation osf un certificat ou une clé asymétrique. Pour plus d’informations, consultez [Cryptage de la sauvegarde](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx).

## <a name="sql-server-2012"></a>SQL Server 2012

Pour plus d’informations sur SQL Server sauvegarde et restauration de SQL Server 2012, voir [sauvegarde et restauration de SQL Server bases de données (2012 de SQL Server)](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx).

À partir de SQL Server 2012 SP1 Cumulative Update 2, vous pouvez sauvegarder et restaurer à partir du service de stockage des objets Blob Azure. Cette amélioration peut être utilisée pour sauvegarder des bases de données SQL Server sur un SQL Server s’exécutant sur une Machine virtuelle de Azure ou une instance locale. Pour plus d’informations, consultez [SQL Server sauvegarde et restauration avec le Service de stockage de Blob Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Certains des avantages de l’utilisation du service de stockage Azure Blob incluent la possibilité de contourner la limite de 16 disques pour les disques attachés, facilité de gestion, la disponibilité directe du fichier de sauvegarde à une autre instance de l’instance de SQL Server s’exécutant sur une machine virtuelle Azure, ou à une instance locale de migration ou de reprise après sinistre. Pour obtenir une liste complète des avantages à l’utilisation d’un service de stockage blob Azure pour les sauvegardes SQL Server, consultez la section *avantages* de [SQL Server sauvegarde et restauration avec le Service de stockage de Blob Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Pour des recommandations de meilleures pratiques et des informations de dépannage, voir [sauvegarde et restauration conseillées (Blob Azure Storage Service)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx).

## <a name="sql-server-2008"></a>SQL Server 2008

Pour SQL Server sauvegarde et restauration dans SQL Server 2008 R2, voir [sauvegarde et restauration de bases de données SQL Server (SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx).

Pour SQL Server sauvegarde et restauration de SQL Server 2008, voir [sauvegarde et restauration de bases de données SQL Server (2008 de SQL Server)](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx).

## <a name="next-steps"></a>Étapes suivantes

Si vous planifiez votre déploiement de SQL Server dans un ordinateur virtuel d’Azure, vous trouverez des conseils de mise en service dans le didacticiel suivant : [une machine de virtuelle de SQL Server sur Azure avec le Gestionnaire de ressources Azure](virtual-machines-windows-portal-sql-server-provision.md).

Bien que la sauvegarde et restauration peuvent être utilisés pour migrer vos données, il existe des chemins de migration de données potentiellement plus faciles pour SQL Server sur un ordinateur virtuel d’Azure. Pour une description complète des options de migration et des recommandations, reportez-vous à la section [migration d’une base de données SQL Server sur un ordinateur virtuel d’Azure](virtual-machines-windows-migrate-sql.md).

Vérifiez les autres [ressources pour l’exécution de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).
