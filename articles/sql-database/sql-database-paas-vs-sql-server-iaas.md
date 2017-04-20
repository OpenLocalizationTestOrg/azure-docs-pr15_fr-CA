<properties
    pageTitle="Base de données SQL (PaaS) par rapport à SQL Server dans le nuage sur des machines virtuelles (IaaS) | Microsoft Azure"
    description="Découvrez l’option SQL Server de nuage adaptée à votre application : de la base de données Azure SQL (PaaS) ou de SQL Server dans le nuage Azure machines virtuelles en fonctionnement."
    services="sql-database, virtual-machines"
    keywords="Nuage de SQL Server, de SQL Server dans le nuage, PaaS de base de données, SQL Server, DBaaS en nuage"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="cjgronlund"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/06/2016"
    ms.author="carlrab"/>

# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Choisir un nuage option de SQL Server : base de données d’Azure, SQL (PaaS) ou de SQL Server sur Azure VM (IaaS)

Azure dispose de deux options pour l’hébergement des charges de travail de SQL Server dans Microsoft Azure :

* [Base de données de SQL Azure](https://azure.microsoft.com/services/sql-database/): base de données de A SQL natif vers le nuage, également connu sous le nom d’une plate-forme sous la forme d’une base de données de service (PaaS) ou une base de données en tant que service (DBaaS) qui est optimisé pour le développement d’applications de logiciel en tant que service (SaaS). Il offre une compatibilité avec la plupart des fonctionnalités de SQL Server. Pour plus d’informations sur les services PaaS, consultez [PaaS](https://azure.microsoft.com/overview/what-is-paas/).
* [SQL Server Azure machines virtuelles en fonctionnement](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server installé et hébergé dans le nuage sur Windows Server Machines virtuelles (VM) exécute sur Azure, également connu sous le nom d’une infrastructure en tant que service (IaaS).
SQL Server sur des machines virtuelles Azure est optimisé pour la migration d’applications SQL Server existantes. Toutes les versions et les éditions de SQL Server sont disponibles. Il offre une compatibilité de 100 % avec SQL Server, ce qui vous permet d’héberger plusieurs bases de données sous forme de transactions de bases de données croisées nécessaires et en cours d’exécution. Il offre un contrôle total sur SQL Server et Windows.

Découvrez comment chaque option s’intègre dans la plate-forme de données Microsoft et obtenir de l’aide correspondant à l’option de droite pour les besoins de votre entreprise. Si vous modifier la priorité des économies ou très peu d’administration avant tout le reste, cet article peut vous aider à choisir l’approche qui offre par rapport aux exigences de l’entreprise que vous intéressent le plus.


## <a name="microsofts-data-platform"></a>Plate-forme de données de Microsoft

Une des premières choses à comprendre dans toute discussion de Azure par rapport à des bases de données SQL Server sur site est que vous pouvez utiliser tout. Plate-forme de données de Microsoft utilise la technologie de SQL Server et la rend disponible sur les machines physiques sur site, les environnements de cloud privé, les environnements de cloud privé hébergé de tiers et cloud public. SQL Server sur Azure mchines virtuel vous permet répondre aux besoins spécifiques et diverses grâce à une combinaison de locaux et de déploiement hébergé sur le nuage, tout en utilisant le même ensemble de produits serveur, outils de développement et le savoir-faire dans ces environnements.

   ![Les options de SQL Server en nuage : serveur SQL sur IaaS ou SaaS SQL de base de données dans le nuage.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Comme illustré dans le diagramme, chaque offre peut être caractérisée par le niveau d’administration dont vous disposez sur l’infrastructure (sur l’axe des X) et le niveau de rentabilité atteint par la consolidation au niveau de la base de données et de l’automatisation (sur l’axe des Y).

Lorsque vous créez une application, les quatre options de base sont disponibles pour l’hébergement de la partie de SQL Server de l’application :

- SQL Server sur des machines physiques non virtualisés
- SQL Server sur des machines sur site virtualisé (cloud privé)
- SQL Server dans Azure Virtual Machine (nuage public de Microsoft)
- Base de données SQL Azure (nuage public de Microsoft)

Dans les sections suivantes, vous découvrez SQL Server dans le nuage public Microsoft : base de données de SQL Azure et SQL Server sur Azure VM. En outre, vous découvrez des facteurs de motivation commerciale commune pour déterminer quelle option convient le mieux pour votre application.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Examinez de plus près SQL Server sur Azure VM et de la base de données de SQL Azure

**Base de données de SQL Azure** est un relationnel de base de données-comme-a-service (DBaaS) hébergé dans le cloud Azure qui se situe dans les catégories de l’industrie des *Logiciels en tant que-a-Service (SaaS)* et *Plate-forme-comme-a-Service (PaaS)*. [Base de données SQL](sql-database-technical-overview.md) est construit sur matériel normalisé et les logiciels qui est détenue, hébergé et géré par Microsoft. La base de données SQL, vous pouvez développer directement sur le service à l’aide des fonctionnalités et des fonctions intégrées. Lorsque vous utilisez la base de données SQL, vous le paiement à l’utilisation avec les options de mise à l’échelle d’entrée ou de sortie de puissance sans interruption.

**SQL Server sur les ordinateurs virtuels Azure (VMs)** relève de la catégorie secteur *Infrastructure-comme-a-Service (IaaS)* et vous permet d’exécuter SQL Server à l’intérieur d’une machine virtuelle dans le nuage. Similaire à SQL de base de données, il repose sur un matériel normalisé est détenu, hébergé et géré par Microsoft. Lors de l’utilisation de SQL Server sur un ordinateur virtuel, vous pouvez soit paie-que vous aller pour une licence SQL Server déjà inclus dans une image de SQL Server ou d’utiliser facilement une licence existante. Vous pouvez également facilement échelle-haut/bas et suspendre/reprendre la machine virtuelle en fonction des besoins.

En général, ces deux options SQL sont optimisées à des fins différentes :

- **De la base de données SQL** est optimisée afin de réduire les coûts globaux au minimum pour provisionner et gérer de nombreuses bases de données. Il réduit les coûts d’administration en cours car vous n’êtes pas obligé de gérer les machines virtuelles, le système d’exploitation ou le logiciel de base de données. Vous n’êtes pas obligé de gérer les mises à niveau, de haute disponibilité et de [sauvegardes](sql-database-automated-backups.md). En général, la base de données de SQL Azure peut augmenter considérablement le nombre de bases de données gérées par un seul informatique ou les ressources de développement.
- **SQL Server s’exécutant sur des machines virtuelles d’Azure** est optimisé pour la migration d’applications existantes vers Azure ou étendre des applications existantes sur site vers le nuage dans les déploiements de hybride. En outre, vous pouvez utiliser SQL Server sur un ordinateur virtuel pour développer et tester des applications traditionnelles de SQL Server. Avec les SQL Server sur Azure VM, vous avez les droits d’administration complets sur une instance dédiée de SQL Server et un ordinateur virtuel sur le nuage. Il est le choix idéal lorsqu’une organisation utilise déjà les ressources informatiques disponibles pour gérer les ordinateurs virtuels. Ces fonctionnalités vous permettent de créer un système hautement personnalisé pour répondre à des exigences de disponibilité et les performances spécifiques de votre application.

Le tableau suivant récapitule les principales caractéristiques de la base de données SQL et SQL Server sur Azure VM :

|       | Base de données SQL | SQL Server sur une Machine virtuelle Azure|
| -------------- | ------------ | ---------------------- |
| **Recommandé pour :** | Nouveau nuage-conçu les applications ayant des contraintes de temps de développement et de marketing. |Applications existantes qui requièrent une migration rapide vers le nuage avec des modifications minimes. Scénarios de développement et de test rapides lorsque vous ne souhaitez pas acheter matériel sur site hors production de SQL Server. |
|| Équipes qui ont besoin de haute disponibilité intégrée, de reprise après sinistre et de mise à niveau de la base de données. |Équipes qui peuvent configurer et gérer la haute disponibilité, reprise après sinistre et installation de correctifs pour SQL Server. Certaines fournies fonctionnalités automatisées simplifient considérablement ce. |
||Équipes qui vous ne souhaitez pas gérer les paramètres de configuration et le système d’exploitation sous-jacent.| Si vous avez besoin d’un environnement personnalisé avec les droits d’administration complets.|
||Bases de données de 1 To, ou les bases de données volumineuses peuvent être [partitionnées de horizontalement ou verticalement](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) à l’aide d’un modèle d’évolution cloisonnée.|Instances de SQL Server avec jusqu'à 64 To de stockage. L’instance peut prendre en charge autant de bases de données en fonction des besoins. |
||[Les applications de construction logiciel-comme-a-Service (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md).| La migration et la création d’applications d’entreprise et hybride.|
|||||
|**Ressources :**|Vous ne souhaitez pas utiliser les ressources informatiques pour la configuration et la gestion de l’infrastructure sous-jacente, mais que vous souhaitez vous concentrer sur la couche application.|Vous avez des ressources informatiques pour la gestion et de configuration. Certaines fournies fonctionnalités automatisées simplifient considérablement ce.|
|**Coût total de propriété :**|Élimine les coûts du matériel et réduit les coûts administratifs.|Élimine les coûts matériels.|
|**Continuité d’activité :**|En plus des capacités d’infrastructure tolérance aux pannes incorporée, base de données de SQL Azure propose des fonctionnalités, telles que les [sauvegardes automatiques](sql-database-automated-backups.md), [Restaurer de Point-à-temps](sql-database-recovery-using-backups.md#point-in-time-restore), [Géo-restauration](sql-database-recovery-using-backups.md#geo-restore)et [Géo-réplication Active](sql-database-geo-replication-overview.md) pour augmenter la continuité d’activité. Pour plus d’informations, consultez [vue d’ensemble de la continuité d’activité de professionnels de la base de données SQL](sql-database-business-continuity.md).|SQL Server sur Azure VM vous permet de configurer une solution de récupération après sinistre et de disponibilité élevée pour les besoins spécifiques de votre base de données. Par conséquent, vous avez un système qui est optimisé pour votre application. Vous pouvez tester et exécuter des basculements par vous-même lorsque cela est nécessaire. Pour plus d’informations, reportez-vous à la section [haute disponibilité et reprise après sinistre pour SQL Server Azure machines virtuelles en fonctionnement](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).|
|**Cloud hybride :**|Votre application sur site peut accéder aux données dans la base de données de SQL Azure.|Avec de SQL Server sur Azure VM, vous pouvez avoir des applications qui s’exécutent en partie dans le nuage et partiellement sur site. Par exemple, vous pouvez étendre votre réseau de locaux et de domaine Active Directory sur le cloud via un [Réseau virtuel d’Azure](../virtual-network/virtual-networks-overview.md). En outre, vous pouvez stocker des fichiers de données locaux dans le stockage Azure à l’aide de [Fichiers de données SQL Server dans Azure](http://msdn.microsoft.com/library/dn385720.aspx). Pour plus d’informations, consultez [Introduction au Cloud hybride de SQL Server 2014](http://msdn.microsoft.com/library/dn606154.aspx).|
||Prend en charge [la réplication transactionnelle de SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) en tant qu’abonné pour répliquer des données.|Prend totalement en charge [la réplication transactionnelle de SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [Groupes de disponibilité AlwaysOn](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md), Integration Services et l’envoi de journaux pour répliquer les données. En outre, les sauvegardes SQL Server traditionnelles sont entièrement pris en charge|
|||||
|||||

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Les motivations pour choisir la base de données de SQL Azure ou SQL Server sur Azure VM

### <a name="cost"></a>Coût

Que vous soyez un démarrage qui est fixé pour les espèces ou une équipe, dans une société établie qui fonctionne sous des budgets serrés, limite de financement est souvent le pilote principal lorsque vous décidez comment héberger vos bases de données. Dans cette section, vous en savoir plus sur la facturation et le Gestionnaire de licences des notions de base en ce qui concerne ces deux options de base de données relationnelle dans Azure : de base de données SQL et SQL Server sur Azure VM. Vous découvrez également calculer le coût total d’application.

#### <a name="billing-and-licensing-basics"></a>Facturation et la licence des notions de base

**De la base de données SQL** est vendu aux clients en tant que service, pas avec une licence.  [SQL Server sur Azure VM](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md) est vendu avec une licence incluse que vous payez par minute. Si vous avez une licence existante, vous pouvez également l’utiliser.  

Actuellement, **SQL de base de données** est disponible dans plusieurs niveaux de service, qui sont facturées toutes les heures à un taux fixe, en fonction du niveau de service et niveau de performance que vous choisissez. En outre, vous êtes facturé pour le trafic Internet sortant à régulière des [taux de transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/). Les niveaux de service Basique, Standard et Premium, sont conçus pour offrir des performances prévisibles avec plusieurs niveaux de performances pour répondre aux besoins des pics de votre application. Vous pouvez modifier entre les niveaux de service et de performances pour répondre aux besoins variés de débit de votre application. Si votre base de données a l’important volume de transactions et de prendre en charge plusieurs utilisateurs simultanés, nous vous recommandons de la couche de service Premium. Pour obtenir les dernières informations sur les niveaux de service pris en charge en cours, consultez [Niveaux de Service de base de données SQL Azure](sql-database-service-tiers.md). Vous pouvez également créer des [pools d’élastique de la base de données](sql-database-elastic-pool.md) pour partager les ressources de performances entre les instances de base de données.

Avec **De la base de données SQL**, le logiciel de base de données est automatiquement configuré, corrigé et mis à jour par Microsoft, ce qui réduit les coûts d’administration. En outre, ses capacités de [sauvegarde intégrée](sql-database-automated-backups.md) vous aident à réaliser des économies considérables, en particulier lorsque vous avez un grand nombre de bases de données.

Avec **SQL Server sur Azure VM**, vous pouvez utiliser une des images de SQL Server fournis par la plate-forme (qui inclut une licence) ou mettre votre licence de SQL Server. Toutes les versions de SQL Server prises en charge (2008 R2, 2012, 2014, 2016) et éditions (développeur, Express, Web, Standard, entreprise) sont disponibles. En outre, les versions de mettre votre-propriétaire-licence (BYOL) des images sont disponibles. Lorsqu’à l’aide de l’Azure fourni des images, le coût des opérations dépend de la taille de la mémoire virtuelle et l’édition de SQL Server choisie. Quelle que soit la taille de mémoire virtuelle ou l’édition de SQL Server, vous payez par minute coût de la licence de SQL Server et de Windows Server, ainsi que le coût de stockage Azure pour les disques de l’ordinateur virtuel. L’option de facturation à la minute vous permet d’utiliser SQL Server pour tant que vous devez licences sans addition d’achat de SQL Server. Si vous mettez votre propre licence de SQL Server sur Azure, vous sont facturés pour Windows Server et les coûts de stockage uniquement. Pour plus d’informations sur les licences de mettre vos propres, consultez [Mobilité licence par le biais de Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="calculating-the-total-application-cost"></a>Calcul du coût total d’application

Lorsque vous démarrez à l’aide d’une plate-forme de nuage, le coût de l’exécution de votre application comprend les coûts de développement et d’administration, ainsi que les coûts de service de plateforme de nuage public.

Voici le calcul coût détaillé de votre application en cours d’exécution dans la base de données de SQL et de SQL Server sur Azure VM :

**Lorsque vous utilisez la base de données de SQL Azure :**

*Coût total de l’application = les coûts d’administration très réduite, les coûts de développement de logiciel + coûts de service de la base de données SQL*

**Lors de l’utilisation de SQL Server sur Azure VM :**

*Coût total de l’application = coût de développement de logiciels extrêmement réduite + coûts d’administration + coûts des licences SQL Server et Windows Server + les coûts du stockage Azure*

Pour plus d’informations sur la tarification, voir les ressources suivantes :

- [Prix de base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Tarification de la Machine virtuelle](https://azure.microsoft.com/pricing/details/virtual-machines/) pour [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) et [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Azure calculateur de prix](https://azure.microsoft.com/pricing/calculator/)

> [AZURE.NOTE] Il existe un petit sous-ensemble de fonctionnalités qui ne sont pas applicables ou non disponible avec la base de données SQL sur SQL Server. Pour plus d’informations, voir [les limites générales de base de données SQL et instructions](sql-database-general-limitations.md) et [des informations de base de données de SQL Transact-SQL](sql-database-transact-sql-information.md) . Si vous déplacez une solution SQL Server existante vers le nuage, reportez-vous à la section [migration d’une base de données SQL Server pour la base de données de SQL Azure](sql-database-cloud-migrate.md). Lorsque vous migrez une application existante sur site de SQL Server de la base de données SQL, envisagez la mise à jour de l’application pour tirer parti des capacités qui offre des services en nuage. Par exemple, vous pouvez envisager d’à l’aide du [Service d’application Azure Web](https://azure.microsoft.com/services/app-service/web/) ou de [Services en nuage Azure](https://azure.microsoft.com/services/cloud-services/) pour héberger la couche application pour augmenter les économies.

### <a name="administration"></a>Administration

Pour de nombreuses entreprises, la décision de transition vers un service en nuage est beaucoup sur le déchargement de la complexité de l’administration, telle qu’elle est coût. Avec **De la base de données SQL**, Microsoft administre le matériel sous-jacent. Microsoft automatiquement réplique toutes les données pour offrir une haute disponibilité, configure et met à niveau le logiciel de base de données, gère l’équilibrage de charge et est un basculement transparent s’il existe une défaillance du serveur. Vous pouvez continuer à administrer votre base de données, mais vous n’avez plus besoin de gérer le moteur de base de données, le système d’exploitation serveur ou le matériel.  Bases de données et les connexions d’accès, index et réglage de la requête et audit et de sécurité sont des exemples d’éléments que vous pouvez continuer à administrer.

Avec **SQL Server sur Azure VM**, vous avez un contrôle total sur le système d’exploitation et la configuration de l’instance de SQL Server. Avec une machine virtuelle, c’est à vous de décider quand mettre à jour/mise à niveau du système d’exploitation et le logiciel de base de données et à installer d’autres logiciels, tels que les antivirus. Certaines fonctionnalités automatisées sont fournies afin de simplifier considérablement la gestion des correctifs, de sauvegarde et de haute disponibilité. En outre, vous pouvez contrôler la taille de la machine virtuelle, le nombre de disques et leurs configurations de stockage. Azure vous permet de modifier la taille d’une machine virtuelle, comme requis. Pour plus d’informations, voir les [tailles de Service de Cloud pour Azure et de la Machine virtuelle](../virtual-machines/virtual-machines-linux-sizes.md). 

### <a name="service-level-agreement-sla"></a>Contrat de niveau de service (SLA)

De nombreux départements informatiques, le respect des obligations de durée d’un contrat de niveau de Service (SLA) est une priorité. Dans cette section, nous examinons les SLA qui s’applique à chaque base de données, option d’hébergement.

Pour les niveaux de service **De la base de données SQL** Basic, Standard et Premium, Microsoft fournit une SLA de 99,99 % de disponibilité. Pour plus d’informations, consultez le [Contrat de niveau de Service](https://azure.microsoft.com/support/legal/sla/sql-database/). Pour obtenir les dernières informations sur les niveaux de service de la base de données SQL et les plans de continuité d’activité d’entreprise pris en charge, reportez-vous à la section [Niveaux de Service](sql-database-service-tiers.md).

Pour **SQL Server s’exécutant sur des machines virtuelles d’Azure**, Microsoft fournit une disponibilité SLA de 99,95 % qui couvre uniquement la Machine virtuelle. Ce contrat SLA ne couvre pas les processus (par exemple, de SQL Server) en cours d’exécution sur l’ordinateur virtuel et requiert que vous au moins deux instances de machine virtuelle dans un jeu de disponibilité de l’hôte. Pour plus d’informations, consultez le [Contrat SLA de machine virtuelle](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Pour une base de données haute disponibilité (HA) dans des ordinateurs virtuels, vous devez configurer une des options de prise en charge de haute disponibilité dans SQL Server, tels que des [Groupes de disponibilité AlwaysOn](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx). À l’aide d’une option de prise en charge de haute disponibilité ne fournit pas un contrat SLA supplémentaire, mais vous permet d’atteindre > disponibilité de 99,99 % de base de données.

### <a name="market"></a>Délais de commercialisation

**De la base de données SQL** est la solution idéale pour des applications conçues par le nuage lorsque la productivité des développeurs et des temps de commercialisation rapide sont critiques. Avec les fonctionnalités de programmation comme le DBA, il est idéal pour les développeurs et les architectes de nuage comme il réduit la nécessité de gérer le système d’exploitation et la base de données sous-jacente. Par exemple, vous pouvez utiliser [l’API REST](http://msdn.microsoft.com/library/azure/dn505719.aspx) et les [Applets de commande PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) pour automatiser et gérer des tâches d’administration pour des milliers de bases de données. Des fonctionnalités telles que [Les Pools élastique de base de données](sql-database-elastic-pool.md) vous permettent de vous concentrer sur la couche application et de fournir plus rapidement de votre solution sur le marché.

**SQL Server s’exécutant sur des machines virtuelles d’Azure** est parfait si vos applications existantes ou nouvelles nécessitent des bases de données volumineuses, les bases de données liées entre elles ou accès à toutes les fonctionnalités de SQL Server ou de Windows. Il est également bien adaptée lorsque vous souhaitez migrer existant sur site les applications et les bases de données Azure comme-est. Dans la mesure où vous n’avez pas besoin de modifier la présentation, application et des couches de données, vous économisez du temps et budget sur le remaniement de votre solution existante. Au lieu de cela, vous pouvez vous concentrer sur la migration de tous vos solutions Azure et effectuer certaines optimisations de performances qui peuvent être requises par la plateforme Azure. Pour plus d’informations, consultez [Méthodes conseillées en matière de performances pour SQL Server Azure machines virtuelles en fonctionnement](../virtual-machines/virtual-machines-windows-sql-performance.md).

## <a name="summary"></a>Résumé

Cet article a exploré de SQL Server et de base de données de SQL Azure machines virtuelles en fonctionnement (VMs) et décrit les facteurs de motivation commerciale commune qui peuvent influencer votre décision. Voici un résumé des suggestions à prendre en compte :

Choisissez la **base de données SQL Azure** si :

- Vous générez de nouvelles applications en nuage pour bénéficier de la réduction des coûts et de fournir des services en nuage optimisation des performances. Cette approche offre les avantages d’un service cloud entièrement géré, contribue à inférieur initial temps sur le marché et permet d’optimiser les coûts à long terme.

- Vous souhaitez demander à Microsoft de réaliser des opérations de gestion courantes sur vos bases de données et nécessitent plus forte disponibilité SLA pour les bases de données.



Choisissez **de SQL Server sur des machines virtuelles Azure** si :

- Vous avez des applications sur site existant que vous souhaitez faire migrer ou étendre vers le nuage, ou si vous souhaitez créer des applications d’entreprise supérieures à 1 To. Cette approche offre l’avantage de la compatibilité SQL de 100 %, les capacités de base de données volumineuse, un contrôle total sur SQL Server et de Windows et sécuriser le tunnel vers sur site. Cette approche réduit les coûts de développement et les modifications des applications existantes.

- Vous avez des ressources informatiques et pouvez posséder en fin de compte les correctifs, les sauvegardes et haute disponibilité de base de données. Notez que certaines fonctionnalités automatisées simplifient considérablement ces opérations. 


## <a name="next-steps"></a>Étapes suivantes
- Consultez [didacticiel de la base de données de SQL : créer une base de données SQL en minutes en utilisant le portail Azure](sql-database-get-started.md) mise en route de la base de données SQL.
- Reportez-vous à la section [prix de base de données SQL] (https://azure.microsoft.com/pricing/details/sql-database/).
- Consultez [fourniture d’une machine virtuelle de SQL Server dans Azure](../virtual-machines/virtual-machines-windows-portal-sql-server-provision.md) mise en route de SQL Server sur Azure VM.
- Voir [de SQL Server sur une Machine virtuelle Azure : chemin de formation](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/).
