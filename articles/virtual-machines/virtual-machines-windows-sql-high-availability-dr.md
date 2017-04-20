<properties
    pageTitle="Haute disponibilité et reprise après sinistre pour SQL Server | Microsoft Azure"
    description="Une description des différents types de stratégies HADR de SQL Server en cours d’exécution dans Azure Virtual Machines."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/20/2016"
    ms.author="MikeRayMSFT" />

# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Haute disponibilité et reprise après sinistre pour SQL Server dans Azure Virtual Machines

## <a name="overview"></a>Vue d’ensemble

Microsoft Azure, machines virtuelles (VM) avec SQL Server peut aider à réduire le coût d’une solution de base de données haute disponibilité et reprise après sinistre de (HADR). La plupart des solutions HADR de SQL Server sont pris en charge dans Azure des machines virtuelles, à la fois comme Azure uniquement et les solutions hybrides. Dans une solution Azure uniquement, l’ensemble du système HADR s’exécute dans Azure. Dans une configuration hybride, la partie de la solution s’exécute dans Azure et l’autre partie s’exécute sur site de votre organisation. La flexibilité de l’environnement Azure vous permet de faire partiellement ou complètement vers Azure aux exigences budgétaires et HADR de vos systèmes de base de données SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="understanding-the-need-for-an-hadr-solution"></a>Comprendre la nécessité d’une solution HADR

C’est à vous assurer que votre système de base de données possède les capacités HADR nécessitant l’accord de niveau de service (SLA). Le fait que Azure fournit des mécanismes de haute disponibilité, comme service de cicatrisation pour les services en nuage et de détection de récupération de défaillance pour les ordinateurs virtuels, ne pas lui-même garantit que vous permettent de respecter le SLA de votre choix. Ces mécanismes de protègent de la haute disponibilité des ordinateurs virtuels, mais pas la haute disponibilité de SQL Server en cours d’exécution sur les ordinateurs virtuels. Il est possible que l’instance de SQL Server pendant que l’ordinateur virtuel est en ligne et en bon état. En outre, même la haute disponibilité de mécanismes fournis par Azure autorisent pour les interruptions de service des ordinateurs virtuels en raison d’événements tels que la récupération à partir des logiciels ou des pannes matérielles et des mises à niveau du système d’exploitation.

En outre, géo redondants stockage (GRS) dans Azure, qui est implémentée avec une fonctionnalité appelée géo-réplication, peut-être pas une solution de reprise après sinistre adéquate pour vos bases de données. Car géo-réplication envoie les données de façon asynchrone, les mises à jour récentes peuvent être perdues en cas de sinistre. Plus d’informations sur les limitations de réplication géographique sont traités dans la section [géo-réplication non pris en charge pour les données et les fichiers journaux sur des disques distincts](#geo-replication-support) .

## <a name="hadr-deployment-architectures"></a>Architectures de déploiement HADR

Les technologies de HADR de SQL Server qui sont prises en charge dans Azure sont les suivants :

- [Toujours sur les groupes de disponibilité](https://technet.microsoft.com/library/hh510230.aspx)
- [La mise en miroir de base de données](https://technet.microsoft.com/library/ms189852.aspx)
- [Envoi de journaux](https://technet.microsoft.com/library/ms187103.aspx)
- [Sauvegarde et restauration avec le Service de stockage de Blob Azure](https://msdn.microsoft.com/library/jj919148.aspx)
- [Toujours sur les Instances de Cluster de basculement](https://technet.microsoft.com/library/ms189134.aspx)

Il est possible de combiner les technologies pour implémenter une solution SQL Server qui possède à la fois haute disponibilité et reprise après sinistre. En fonction de la technologie que vous utilisez, un déploiement hybride peut nécessiter un tunnel VPN sur le réseau virtuel Azure. Les sections suivantes vous présentent certaines des architectures de déploiement d’exemple.

## <a name="azure-only-high-availability-solutions"></a>Azure uniquement : solutions de haute disponibilité

Vous pouvez avoir une solution de haute disponibilité pour vos bases de données SQL Server dans Azure toujours sur les groupes de disponibilité ou la mise en miroir de la base de données.

| Technologie                               | Exemples d’architecture                    |
| ---------------------------------------- | ---------------------------------------- |
| **Toujours sur les groupes de disponibilité**        | Tous les réplicas de disponibilité en cours d’exécution dans Azure VM pour une haute disponibilité au sein de la même région. Vous devez configurer un ordinateur virtuel de contrôleur de domaine dans la mesure où le Clustering de basculement serveur Windows (WSFC) requiert un domaine Active Directory.<br/> ![Toujours sur les groupes de disponibilité](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Pour plus d’informations, voir [Configurer toujours sur les groupes de disponibilité dans Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Toujours sur les Instances de Cluster de basculement** | Basculement Cluster Instances (FCI), qui nécessitent un stockage partagé, peut être créé de 2 façons différentes.<br/><br/>1. un FCI sur un WSFC de deux nœuds exécutant dans Azure VM avec stockage pris en charge par une solution de clustering tierce. Pour obtenir un exemple spécifique qui utilise SIÔS DataKeeper, reportez-vous à la section [haute disponibilité pour un partage de fichiers à l’aide de WSFC et logiciels de tiers 3e SIÔS Datakeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>2. un FCI sur un WSFC de deux nœuds en cours d’exécution dans Azure VM avec stockage de blocs partagé de cible iSCSI à distance via ExpressRoute. Par exemple, le stockage privé de NetApp (NPS) expose une cible iSCSI via ExpressRoute avec Equinix pour Azure VM.<br/><br/>Pour le stockage partagé de tiers et les solutions de réplication des données, vous devez contacter le fournisseur pour tous les problèmes relatifs à l’accès aux données lors d’un basculement.<br/><br/>Notez que l’aide FCI sur [stockage Azure](https://azure.microsoft.com/services/storage/files/) n'est pas encore prise en charge, dans la mesure où cette solution n’utilise pas de stockage de la prime. Nous travaillons pour prendre en charge cela plus rapidement. |

## <a name="azure-only-disaster-recovery-solutions"></a>Azure uniquement : les solutions de reprise après sinistre

Vous pouvez avoir une solution de reprise après sinistre pour vos bases de données SQL Server dans Azure toujours sur les groupes de disponibilité, la mise en miroir de la base de données ou sauvegarde et restaurer de BLOB de stockage.

| Technologie                               | Exemples d’architecture                    |
| ---------------------------------------- | ---------------------------------------- |
| **Toujours sur les groupes de disponibilité**        | Réplicas de disponibilité en cours d’exécution sur plusieurs centres de données dans Azure VM de reprise après sinistre. Cette solution de cross-région protège contre la panne de site complète. <br/> ![Toujours sur les groupes de disponibilité](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>Au sein d’une zone, tous les réplicas doivent être dans le même service de cloud et le même VNet. Étant donné que chaque région aura une VNet distinct, ces solutions nécessitent VNet VNet la connectivité. Pour plus d’informations, consultez [configurer une connexion VPN de Site à Site dans Azure portal classique](../vpn-gateway/vpn-gateway-site-to-site-create.md). |
| **La mise en miroir de base de données**                   | Capital et mise en miroir et des serveurs dans différents centres de données pour la reprise après sinistre. Vous devez le déployer à l’aide de certificats de serveur, car un domaine active directory ne peut pas s’étendre sur plusieurs centres de données.<br/>![La mise en miroir de base de données](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Sauvegarde et restauration avec le Service de stockage de Blob Azure** | Dans les bases de données de production sauvegardées directement au stockage d’objets blob dans un autre centre de données pour la reprise après sinistre.<br/>![Sauvegarde et restauration](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Pour plus d’informations, voir [sauvegarde et restauration pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |

## <a name="hybrid-it-disaster-recovery-solutions"></a>INFORMATIQUES hybrides : Solutions de reprise après sinistre

Vous pouvez avoir une solution de reprise après sinistre pour vos bases de données SQL Server dans un environnement informatique hybride utilisant toujours des groupes de disponibilité, la mise en miroir de base de données, l’envoi de journaux et sauvegarde et restauration avec le stockage Azure blog.

| Technologie                               | Exemples d’architecture                    |
| ---------------------------------------- | ---------------------------------------- |
| **Toujours sur les groupes de disponibilité**        | Certains réplicas de disponibilité en cours d’exécution dans Azure VM et les autres réplicas en cours d’exécution sur le site de reprise après sinistre de site à site. Le site de production peut être soit sur site ou dans un centre de données Azure.<br/>![Toujours sur les groupes de disponibilité](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Étant donné que tous les réplicas de disponibilité doivent être dans le même cluster WSFC, le cluster WSFC doit s’étendre sur les deux réseaux (un cluster de WSFC de sous-réseaux multiples). Cette configuration exige une connexion VPN entre Azure et le réseau local.<br/><br/>De reprise après sinistre réussie de vos bases de données, vous devez également installer un contrôleur de domaine de réplica sur le site de reprise.<br/><br/>Il est possible d’utiliser l’Assistant Ajout d’un réplica dans SSMS pour ajouter un réplica Azure vers un existant toujours sur le groupe de disponibilité. Pour plus d’informations, consultez Didacticiel : étendre votre toujours sur le groupe de disponibilité pour Azure. |
| **La mise en miroir de base de données**                   | Un partenaire en cours d’exécution dans une machine virtuelle d’Azure et l’autre sur site pour la reprise entre sites à l’aide de certificats de serveur. Les partenaires n’avez pas besoin d’être dans le même domaine Active Directory, et aucune connexion VPN n’est nécessaire.<br/>![La mise en miroir de base de données](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Un autre scénario de mise en miroir de base de données implique un partenaire s’exécutant dans un Azure VM et les autres en cours d’exécution en local dans le même domaine Active Directory pour la reprise de site à site. Une [connexion VPN entre le réseau virtuel Azure et le réseau local](../vpn-gateway/vpn-gateway-site-to-site-create.md) est requis.<br/><br/>De reprise après sinistre réussie de vos bases de données, vous devez également installer un contrôleur de domaine de réplica sur le site de reprise. |
| **Envoi de journaux**                         | Un serveur en cours d’exécution dans une machine virtuelle d’Azure et l’autre sur site de reprise après sinistre de site à site. Envoi de journaux dépend le partage de fichiers Windows, une connexion VPN entre le réseau virtuel Azure et le réseau local est requise.<br/>![Envoi de journaux](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>De reprise après sinistre réussie de vos bases de données, vous devez également installer un contrôleur de domaine de réplica sur le site de reprise. |
| **Sauvegarde et restauration avec le Service de stockage de Blob Azure** | Bases de données de production sauvegardées directement sur le stockage blob Azure pour la reprise après sinistre sur site.<br/>![Sauvegarde et restauration](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Pour plus d’informations, voir [sauvegarde et restauration pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Remarques importantes concernant le HADR de SQL Server dans Azure

Azure VM, de stockage et de réseau ont des caractéristiques opérationnelles différentes à un local, non virtualisés des infrastructures informatiques. Une implémentation réussie d’une solution de HADR SQL Server dans Azure nécessite que vous compreniez ces différences et concevez votre solution pour les prendre en charge.

### <a name="high-availability-nodes-in-an-availability-set"></a>Nœuds haute disponibilité dans un jeu de disponibilité

Jeux de disponibilité dans Azure vous permettent de placer les nœuds haute disponibilité par domaines d’erreur (FDs) et les domaines de mise à jour (UDs) distinctes. Pour les machines virtuelles de Azure être placé dans le même jeu de disponibilité, vous devez les déployer dans le même service de cloud. Seuls les nœuds dans le même service de cloud peuvent participer le même ensemble de disponibilité. Pour plus d’informations, voir [Gérer la disponibilité des ordinateurs virtuels](virtual-machines-windows-manage-availability.md).

### <a name="wsfc-cluster-behavior-in-azure-networking"></a>Comportement de cluster WSFC réseau Azure

Le service DHCP non conforme à RFC dans Azure peut entraîner la création de certaines configurations de cluster WSFC échoue, le nom de réseau cluster assignée une adresse IP en double, comme la même adresse IP comme l’un des nœuds du cluster. Il s’agit d’un problème lorsque vous implémentez toujours sur les groupes de disponibilité, qui dépend de la fonction WSFC.

Considérez le scénario lorsqu’un cluster à deux nœuds est créé et mis en ligne :

1. Le cluster est en ligne, puis NODE1 demande une adresse IP attribuée de manière dynamique pour le nom de réseau du cluster.

2. Aucune adresse IP que l’adresse IP de NODE1 n’est donnée par le service DHCP, dans la mesure où le service DHCP reconnaît que la demande provient de NODE1 lui-même.

3. Windows détecte une adresse en double est assignée pour NODE1 et le nom de réseau du cluster, et le groupe de cluster par défaut ne parvient pas à se mettre en ligne.

4. Le groupe de cluster par défaut déplace vers Nœud2, qui traite l’adresse IP de NODE1 comme l’adresse IP du cluster et met en ligne le groupe du cluster par défaut.

5. Lorsque NODE2 tente d’établir la connectivité avec NODE1, paquets dirigés à NODE1 ne laissant jamais NODE2 car il résout l’adresse IP de NODE1 à lui-même. Impossible d’établir une connectivité avec NODE1 Node2, puis perd le quorum et le cluster s’arrête.

6. En attendant, NODE1 peut envoyer des paquets à NODE2, mais NODE2 ne peut pas répondre. Node1 perd le quorum, le cluster s’arrête.

Ce scénario peut être évité en attribuant une adresse IP statique non utilisée, par exemple une adresse lien-local comme 169.254.1.1, au nom de réseau du cluster afin de mettre en ligne le nom réseau du cluster. Pour simplifier ce processus, reportez-vous à la section [Configuration de Cluster de basculement Windows dans Azure pour toujours sur les groupes de disponibilité](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Pour plus d’informations, voir [Configurer toujours sur les groupes de disponibilité dans Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Prise en charge de disponibilité groupe écouteur

Écouteurs de groupe de disponibilité sont pris en charge sur les ordinateurs virtuels d’Azure exécutant Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 et Windows Server 2016. Cette prise en charge est rendue possible par l’utilisation de points de terminaison avec équilibrage de charge activé sur les ordinateurs virtuels Azure qui sont des nœuds de groupe de disponibilité. Vous devez suivre les étapes de configuration spéciales pour les écouteurs pour les deux applications clientes qui sont exécutent dans Azure, ainsi que ceux en cours d’exécution sur site.

Il existe deux options principales pour la configuration de votre port d’écoute : externe (publique) ou interne. L’écouteur (publique) externe utilise un équilibreur de charge d’ouvert sur internet et est associé à un public IP virtuelle qui est accessible sur internet. Un port d’écoute interne utilise un équilibreur de charge interne et prennent uniquement en charge les clients au sein du même réseau virtuel. Pour une ou l’autre de charger le type de l’équilibreur, vous devez activer directement le serveur renvoie. 

Si le groupe de disponibilité s’étend sur plusieurs sous-réseaux Azure (par exemple, un déploiement qui traverse les régions Azure), la chaîne de connexion du client doit inclure "**MultisubnetFailover = True**». Ainsi, les tentatives de connexion parallèle avec les réplicas dans des sous-réseaux différents. Pour obtenir des instructions sur la configuration d’un port d’écoute, reportez-vous à la section.

- [Configurer un écouteur ILB pour toujours sur les groupes de disponibilité dans Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
- [Configurer un port d’écoute externe pour toujours sur les groupes de disponibilité dans Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

Vous pouvez toujours vous connecter à chaque réplica de disponibilité séparément en se connectant directement à l’instance de service. En outre, toujours sur les groupes de disponibilité étant compatible avec les clients de mise en miroir de la base de données, vous pouvez vous connecter avec les réplicas de disponibilité comme la mise en miroir des partenaires tant que les réplicas sont configurés similaire à la mise en miroir de base de données de la base de données :

- Un seul réplica principal et un réplica secondaire

- Le réplica secondaire est configuré comme non lisible (**Lisible secondaire** option la valeur **non**)

Un exemple de chaîne de connexion client qui correspond à cette configuration comme la mise en miroir de base de données à l’aide de ADO.NET ou SQL Server Native Client est inférieur à :

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Pour plus d’informations sur la connectivité du client, voir :

- [À l’aide de mots clés de chaîne de connexion avec SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
- [Connecter des Clients à une base de données mise en miroir de la Session (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
- [Connexion à un écouteur de groupe de disponibilité d’informatiques hybrides](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
- [Écouteurs de groupe de disponibilité, la connectivité des clients et le basculement de l’Application (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
- [À l’aide de la mise en miroir de base de données des chaînes de connexion avec des groupes de disponibilité](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latence du réseau dans informatiques hybrides

Vous devez déployer votre solution HADR en partant du principe qu’il existe peut-être des périodes de temps avec une latence de réseau élevée entre votre réseau de locaux et la Azure. Lors du déploiement des duplications sur Azure, vous devez utiliser une validation asynchrone au lieu de validation synchrone pour le mode de synchronisation. Lorsque le déploiement de serveurs de mise en miroir de la base de données à la fois sur site et dans Azure, utilisez le mode performances élevées au lieu du mode de sécurité élevée.

### <a name="geo-replication-support"></a>Prise en charge de la réplication géographique

Geo-réplication de disques Azure ne supporte pas le fichier de données et le fichier journal de la même base de données pour être stockés sur des disques distincts. GRS réplique les modifications sur chaque disque indépendamment et de manière asynchrone. Ce mécanisme garantit l’ordre d’écriture au sein d’un seul disque sur la copie de réplication géographique, mais pas entre les copies de réplication géographique de plusieurs disques. Si vous configurez une base de données pour stocker le fichier de données et son fichier journal sur des disques distincts, les disques de récupération après un sinistre peuvent contenir une copie à jour du fichier de données que le fichier journal, ce qui rompt le journal d’écriture anticipée de SQL Server et les propriétés ACID des transactions. Si vous n’avez pas l’option pour désactiver la réplication géographique sur le compte de stockage, vous devez conserver toutes les données et fichiers journaux d’une base de données sur le même disque. Si vous devez utiliser plus d’un disque en raison de la taille de la base de données, vous devez déployer une des solutions de récupération après sinistre ci-dessus pour garantir la redondance des données.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin créer un ordinateur virtuel Azure avec SQL Server, reportez-vous à la section [mise en service d’une Machine de virtuelle de SQL Server sur Azure](virtual-machines-windows-portal-sql-server-provision.md).

Pour obtenir les meilleures performances dans SQL Server en cours d’exécution sur un ordinateur virtuel d’Azure, reportez-vous à l’aide de [Méthodes conseillées en matière de performances pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Pour les autres rubriques relatives à l’exécution de SQL Server dans Azure VM, consultez [SQL Server Azure machines virtuelles en fonctionnement](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Autres ressources

- [Installer une nouvelle forêt Active Directory dans Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
- [Créer le Cluster WSFC pour toujours sur les groupes de disponibilité dans Azure VM](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)
