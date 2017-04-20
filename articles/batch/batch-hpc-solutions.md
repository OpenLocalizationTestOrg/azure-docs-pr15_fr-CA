<properties
   pageTitle="Solutions de traitement par lots et HPC dans le nuage | Microsoft Azure"
   description="En savoir plus sur le traitement par lots et les scénarios de haute performance computing (HPC et calculer volumineux) et les solutions proposées dans Azure"
   services="batch, virtual-machines, cloud-services"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="07/27/2016"
   ms.author="danlep"/>

# <a name="batch-and-hpc-solutions-in-the-azure-cloud"></a>Solutions HPC et du lot dans le nuage Azure

Azure propose des solutions de nuage et évolutive par lots et de l’informatique hautes performances (HPC) - également appelées *Calcul grande*. Découvrez ici les charges de travail de calcul importante et services de Azure prendre en charge les, ou accéder directement à des [scénarios de solution](#scenarios) plus loin dans cet article. Cet article est principalement destiné aux décideurs techniques, les responsables informatiques et les éditeurs de logiciels indépendants, mais les autres développeurs et les professionnels de l’informatique permet de se familiariser avec ces solutions.

Les organisations ont des problèmes informatiques à grande échelle : ingénierie de conception et analyse, rendu d’image, modélisation complexe, simulations Monte Carlo, les calculs de risques financiers et autres. Azure aide les entreprises à résoudre ces problèmes avec les ressources, une évolutivité et une planification que dont ils ont besoin. Avec Azure, les entreprises peuvent :

* Créez des solutions hybrides, extension des clusters HPC sur site pour décharger les charges de pointe vers le nuage

* Exécuter des outils du cluster HPC et les charges de travail entièrement dans Azure

* Des services Azure évolutives et gérées comme un [traitement par lots](https://azure.microsoft.com/documentation/services/batch/) permet d’exécuter des charges de travail intensives sans avoir à déployer et à gérer l’infrastructure de calcul

Bien qu’au-delà de la portée de cet article, Azure fournit aux développeurs et partenaires un ensemble complet de fonctionnalités, des choix d’architecture et des outils de développement de créer des workflows à grande échelle et personnalisés de calcul volumineux. Et un écosystème de partenaires en pleine croissance est prêt à vous aider à améliorer la productivité de vos charges de travail de calcul volumineux dans le nuage Azure.


## <a name="batch-and-hpc-applications"></a>Applications de traitement par lots et HPC

Contrairement aux web applications et de nombreuses applications du secteur d’activité, lot et HPC ont un défini de début et de fin, et ils peuvent s’exécuter selon une planification ou à la demande, parfois des heures ou plus. La plupart se répartissent en deux catégories principales : *intrinsèquement parallèle* (parfois appelé « excessivement parallèle », car les problèmes qu’ils résoudre se prêtent à l’exécution en parallèle sur plusieurs processeurs ou ordinateurs) et *étroitement couplée*. Pour plus d’informations sur ces types d’applications, consultez le tableau suivant. Une solution Azure proche de vos besoins pour un type ou à l’autre.

>[AZURE.NOTE] Dans les solutions de traitement par lots et HPC, une instance d’une application est généralement appelée un *travail*, et chaque projet peut obtenir divisé en *tâches*. Et les ressources en clusters compute de l’application sont souvent appelées *nœuds de calcul*.

Type de | Caractéristiques | Exemples
------------- | ----------- | ---------------
**Intrinsèquement parallèle**<br/><br/>![Intrinsèquement parallèle][parallel] |• Exécutés la logique de l’application indépendamment des ordinateurs individuels<br/><br/> • Ajout ordinateurs permet à l’application à l’échelle et de réduire les temps de calcul<br/><br/>• Application se compose d’exécutables séparés ou est divisée en un groupe de services appelé par un client (SOA, application ou une architecture orientée services) |• La modélisation des risques financiers<br/><br/>• Rendu d’image et de traitement d’image<br/><br/>• Le codage de média et de transcodage<br/><br/>• Les simulations Monte Carlo<br/><br/>• Tests de logiciels
**Étroitement couplés**<br/><br/>![Étroitement couplés][coupled] |• Application nécessite des nœuds de calcul pour interagir ou échanger des résultats intermédiaires<br/><br/>• Les nœuds de calcul peuvent communiquer à l’aide de l’Interface MPI (Message Passing), un protocole de communication commun pour l’informatique parallèle<br/><br/>• L’application est sensible à la bande passante et la latence du réseau<br/><br/>• Performances de l’application peuvent être améliorées à l’aide des technologies de réseau à grande vitesse comme InfiniBand et l’accès direct à la mémoire à distance (RDMA) |Modélisation de réservoir de pétrole et de gaz •<br/><br/>• Ingénierie analyser la conception et, par exemple des fluides informatisée<br/><br/>• Les simulations physiques tels que les pannes de voiture et réactions nucléaires<br/><br/>Prévisions météorologiques de •

### <a name="considerations-for-running-batch-and-hpc-applications-in-the-cloud"></a>Considérations pour l’exécution du lot et applications HPC dans le nuage

Vous pouvez facilement migrer de nombreuses applications sont conçues pour fonctionner dans les clusters HPC sur site pour Azure, ou un environnement hybride (cross-site). Toutefois, il peut y avoir certaines limitations ou les considérations, y compris :


* **Disponibilité des ressources de cloud** - selon le type de ressources de calcul cloud que vous en utilisez un, vous ne peut-être pouvoir compter sur la disponibilité de la machine en continu pendant l’exécution d’une tâche. Gestion d’état et de progression vérifient pointant vers des techniques courantes pour gérer les défaillances transitoires possibles et plus nécessaire lors de l’utilisation des ressources de cloud.


* **Accès aux données** : techniques d’accès aux données généralement disponibles dans les clusters de l’entreprise, tels que NFS, peuvent nécessiter une configuration spéciale dans le nuage. Ou bien, vous devrez peut-être adopter des modèles pour le nuage et méthodes d’accès de données différentes.

* **Déplacement de données** - pour les applications qui traitent de grandes quantités de données, les stratégies sont nécessaires pour déplacer les données dans le stockage en nuage et de ressources de calcul. Vous devrez peut-être à grande vitesse coexistence mise en réseau tels que les [ExpressRoute d’Azure](https://azure.microsoft.com/services/expressroute/). Pensez également à juridiques, réglementaires ou les limites de la politique de stockage ou en accédant aux données.


* Vérification de **licence** - avec le fournisseur de n’importe quelle application commerciale pour les restrictions de licences ou autres pour l’exécution dans le nuage. Pas de tous les fournisseurs offrent de paiement à l’utilisation de la licence. Vous devrez peut-être prévoir un serveur de licences dans le nuage pour votre solution, ou se connecter à un serveur de licences sur site.


### <a name="big-compute-or-big-data"></a>Grand calcul ou données volumineuses ?

La ligne de séparation entre les applications de calcul importante et données volumineuses n’est pas toujours claire, et certaines applications peuvent avoir des caractéristiques des deux. Ils concernent tous deux en cours d’exécution des calculs à grande échelle, généralement sur les clusters d’ordinateurs. Mais les approches de solutions et les outils de prise en charge peuvent différer.

• **Calculer big** a tendance à impliquent des applications qui s’appuient sur la puissance du processeur et de mémoire, telles que l’ingénierie des simulations, les risques financiers de modélisation et rendu numérique. L’infrastructure pour une solution de calcul volumineux peut inclure des ordinateurs avec des processeurs multicœurs spécialisés pour effectuer le calcul brute et spécialisé, à grande vitesse de matériel réseau pour connecter les ordinateurs.

• **Données big** résout les problèmes d’analyse de données qui impliquent de grandes quantités de données qui ne peuvent pas être gérées par un système de gestion de base de données ou d’ordinateur unique. Exemples d’importants volumes de journaux web ou autres données business intelligence. Données a tendance à se baser davantage sur la capacité des disques et des performances d’e/s que sur la puissance du processeur. Il existe également des outils spécialisés de données volumineuses telles que Hadoop Apache pour gérer le cluster et les données de partition. (Pour plus d’informations sur les HDInsight d’Azure et d’autres solutions d’Hadoop d’Azure, consultez [Hadoop](https://azure.microsoft.com/solutions/hadoop/).)

## <a name="compute-management-and-job-scheduling"></a>Calculer la gestion et la planification des tâches

Exécution d’applications de traitement par lots et HPC souvent inclut un *Gestionnaire de cluster* et un *Planificateur de tâches* pour aider à gérer les ressources de cluster de calcul et les allouer aux applications qui s’exécutent les tâches. Ces fonctions peuvent être exécutées par les outils distincts, un outil intégré ou service.

* Le **Gestionnaire du cluster de** -dispositions, mises à jour et administre les ressources de calcul (ou nœuds). Un gestionnaire de cluster peut automatiser l’installation d’images de système d’exploitation et applications sur des nœuds de calcul, mettre à l’échelle en fonction des demandes des ressources de calcul et de surveiller les performances des nœuds.

* **Planificateur de tâches** - spécifie les ressources (telles que les processeurs ou la mémoire) une application besoins, ainsi que les conditions lors de l’exécution. Un planificateur de tâches gère une file d’attente des travaux et alloue des ressources basée sur une priorité affectée ou d’autres caractéristiques.

Outils pour les clusters Windows et Linux d’ordonnancement et de la gestion de clusters peuvent migrer bien vers Azure. Par exemple, [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029), solution de cluster de calcul gratuites de Microsoft pour les charges de travail Windows et Linux HPC, propose plusieurs options pour exécuter dans Azure. Vous pouvez également créer des clusters Linux pour exécuter les outils open source tels que couple et SLURM. Vous pouvez également apporter des solutions de grille commercial à Azure, tels que [TIBCO DataSynapse GridServer](http://www.tibco.com/company/news/releases/2016/tibco-to-accelerate-cloud-adoption-of-banking-and-capital-markets-customers-via-microsoft-collaboration), [IBM Symphony](http://www-01.ibm.com/support/docview.wss?uid=isg3T1023592)et [Moteur de grille Univa](http://www.univa.com/products/grid-engine).

Comme indiqué dans les sections suivantes, vous pouvez également tirer parti des services Azure pour gérer les ressources de calcul et de planifier des outils de gestion de cluster traditionnel de travaux sans (ou en plus).


## <a name="scenarios"></a>Scénarios

Voici trois scénarios courants pour exécuter des charges de travail de calcul volumineux dans Azure à l’aide des solutions de cluster HPC existantes, services Azure ou une combinaison des deux. Considérations relatives à la clé pour le choix de chaque scénario sont répertoriées, mais ne sont pas exhaustives. Plus sur les services Azure disponibles que vous pouvez utiliser dans votre solution sont plus loin dans l’article.

  | Scénario | Pourquoi choisir ?
------------- | ----------- | ---------------
**En mode rafale des clusters HPC vers Azure**<br/><br/>[! [Rafale de cluster] [burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> Pour en savoir plus :<br/>• [Rafale aux instances de travailleur Azure avec le HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)<br/><br/>• [Configurer un hybride compute cluster HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)<br/><br/>• [Burst au lot Azure avec le HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)<br/><br/>|• Combiner votre [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) ou autre cluster local avec d’autres ressources Azure dans une solution hybride.<br/><br/>• Développez vos charges de travail de calcul volumineux à exécuter sur la plate-forme en tant que Service (PaaS), instances de machine virtuelle (actuellement Windows Server uniquement).<br/><br/>• Accéder à un magasin de données ou de serveur de licence sur site à l’aide d’un réseau virtuel Azure facultatif|• Vous avez un cluster HPC existant et que vous avez besoin de davantage de ressources <br/><br/>• Vous ne souhaitez pas acheter et gérer une autre infrastructure de cluster HPC<br/><br/>• Vous avez transitoire périodes de pointe à la demande ou des projets spéciaux
**Créer un cluster HPC entièrement dans Azure**<br/><br/>[! [Cluster dans IaaS] [iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>Pour en savoir plus :<br/>• [Les solutions de cluster HPC dans Azure](./big-compute-resources.md)<br/><br/>|• Rapidement et de manière cohérente déployer vos applications et vos outils de cluster sur l’infrastructure de Windows ou Linux standard ou personnalisé sous la forme d’une virtualisation de service (IaaS).<br/><br/>• Exécutez les différentes charges de travail de calcul volumineux à l’aide de la solution de votre choix de planification des tâches.<br/><br/>• Utilisez des services Azure supplémentaires, y compris la mise en réseau et stockage pour créer des solutions complètes de nuage. |• Vous ne souhaitez pas acheter et gérer une autre infrastructure de cluster Linux ou Windows HPC<br/><br/>• Vous avez transitoire périodes de pointe à la demande ou des projets spéciaux<br/><br/>• Vous devez supplémentaires pendant une période de cluster mais ne souhaitez pas investir dans des ordinateurs et de l’espace pour le déployer<br/><br/>• Vous voulez décharger votre application de calcul intensif s’il s’exécute comme un service entièrement dans le nuage
**Déployer une application parallèle vers Azure**<br/><br/>[! [Lot azure] [batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>Pour en savoir plus :<br/>• [Principes de base du lot Azure](./batch-technical-overview.md)<br/><br/>• [Mise en route avec la bibliothèque de commandes d’Azure pour .NET](./batch-dotnet-get-started.md)|• Développer avec [Azure lot](https://azure.microsoft.com/documentation/services/batch/) mise à l’échelle des différentes charges de travail de calcul volumineux à exécuter sur les pools, les machines virtuelles Windows ou Linux.<br/><br/>• Utilisez un service de la plateforme Azure pour gérer autoscaling de machines virtuelles, planification de tâche, de reprise après sinistre, déplacement des données, gestion des dépendances et déploiement de l’application et de déploiement.|• Vous ne souhaitez pas gérer compute de ressources ou un planificateur de tâches ; au lieu de cela, vous souhaitez vous concentrer sur l’exécution de vos applications<br/><br/>• Vous voulez décharger votre application de calcul intensif s’il s’exécute comme un service dans le nuage<br/><br/>• Vous voulez redimensionner automatiquement vos ressources informatiques afin de correspondre à la charge de travail de calcul


## <a name="azure-services-for-big-compute"></a>Services Azure Compute Big

Voici plus d’informations sur le calcul, données, réseau et vous pouvez combiner les services connexes pour les solutions de calcul volumineux et des flux de travail. Pour obtenir des instructions détaillées sur les services Azure, consultez la [documentation](https://azure.microsoft.com/documentation/)de services Azure. Les [scénarios](#scenarios) précédemment dans cet article indiquent simplement quelques méthodes d’utilisation de ces services.

>[AZURE.NOTE] Azure présente régulièrement de nouveaux services qui peuvent s’avérer utiles pour votre scénario. Si vous avez des questions, contactez un [partenaire Azure](https://pinpoint.microsoft.com/en-US/search?keyword=azure) ou l’e-mail *bigcompute@microsoft.com*.

### <a name="compute-services"></a>Services de traitement

Services d’Azure compute sont au cœur d’une solution de calcul volumineux et les avantages d’offre de services compute différents pour différents scénarios. À un niveau basique, ces services offrent des modes différents pour des applications à exécuter sur les instances de calcul machine virtuelle Azure fournit à l’aide de la technologie Hyper-V de Windows Server. Ces instances peuvent s’exécuter des systèmes d’exploitation Linux et Windows et les outils standard et personnalisés. Azure vous donne un choix de [tailles d’instance](../virtual-machines/virtual-machines-windows-sizes.md) avec différentes configurations de cœurs du processeur, mémoire, disque et d’autres caractéristiques. Selon vos besoins, vous pouvez mettre à l’échelle les instances à des milliers de cœurs et puis échelle lorsque vous avez besoin de moins de ressources.

>[AZURE.NOTE] Tirer parti des instances intensives Azure pour améliorer les performances et l’évolutivité des charges de travail HPC, y compris des applications MPI parallèles nécessitant un faible temps de latence et un débit élevé application. Voir la rubrique [About H-series et calcul intensif VM d’une série](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).  

Service | Description
------------- | -----------
**[Ordinateurs virtuels](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Fournir calculer l’infrastructure en tant que service (IaaS) à l’aide de la technologie Microsoft Hyper-V<br/><br/>• Vous permettre flexible de provisionner et de gérer des ordinateurs de nuage persistants à partir d’images standard de serveur Windows ou Linux à partir des disques de données que vous fournissez ou images et [Azure Marketplace](https://azure.microsoft.com/marketplace/),<br/><br/>• Peuvent être déployés et gérés en tant que [Jeux d’échelle de machine virtuelle](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/) pour créer des services à grande échelle dans des machines virtuelles identiques, avec autoscaling pour augmenter ou diminuer automatiquement de capacité<br/><br/>Outils de cluster et des applications entièrement dans le nuage de calcul • exécuté sur site<br/><br/>
**[Services en nuage](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• Pouvant exécuter des applications de calcul grande dans le travail des instances de rôle, qui sont des ordinateurs virtuels exécutant une version de Windows Server et sont entièrement gérés par Azure<br/><br/>• Permettre aux applications évolutif et fiables, à faible surcharge administrative, en cours d’exécution sur une plate-forme en tant que service (PaaS) modèle<br/><br/>• Peut nécessiter des outils supplémentaires ou développement d’intégrer les solutions de cluster HPC sur site
**[Traitement par lots](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• Exécute des charges de travail parallèle et traitement par lots à grande échelle dans un service entièrement géré.<br/><br/>• Fournit une planification des tâches et autoscaling d’un pool géré de machines virtuelles<br/><br/>• Permet aux développeurs de créer et exécuter des applications en tant que service ou activer le cloud des applications existantes<br/>

### <a name="storage-services"></a>Services de stockage

Une solution de calcul volumineux en général fonctionne sur un jeu de données d’entrée et génère des données de ses résultats. Parmi les services de stockage Azure utilisés dans les solutions de calcul volumineux :

* [Blob, table et le stockage de la file d’attente](https://azure.microsoft.com/documentation/services/storage/) - gérer de grandes quantités de données non structurées, les données NoSQL et les messages de flux de travail et de la communication, respectivement. Par exemple, vous pouvez utiliser le stockage blob pour les grands ensembles de données techniques ou pour les images d’entrée ou votre processus d’application des fichiers de support. Vous pouvez utiliser des files d’attente pour la communication asynchrone dans une solution. Consultez [Introduction au stockage de Microsoft Azure](../storage/storage-introduction.md).

* [Stockage azure](https://azure.microsoft.com/services/storage/files/) - partage de fichiers communs et les données dans Azure utilisant le protocole SMB standard, ce qui est nécessaire pour certaines solutions de cluster HPC.

* [Magasin de données lac](https://azure.microsoft.com/services/data-lake-store/) - fournit un système de fichiers distribués d’Apache Hadoop hyperscale pour le cloud, utile pour le traitement par lots, en temps réel et l’analytique interactive.

### <a name="data-and-analysis-services"></a>Services de données et d’analyse

Certains scénarios de calcul volumineux impliquent des flux de données à grande échelle, ou génèrent des données qui a besoin d’un traitement ultérieur ou analyse. Azure offre plusieurs services de données et d’analyse, y compris :

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) - Builds contrôlée par les données des flux de travail (pipelines) cette jointure, agrégat et transformer des données à partir de locaux, basée sur le cloud et les magasins de données Internet.

* [De la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/) - offre les fonctionnalités d’un système de gestion de base de données relationnelle Microsoft SQL Server dans un service managé.

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) - déploie et dispositions des clusters Windows Server ou Hadoop de Apache basé sur Linux dans le nuage à gérer, d’analyser et de générer des rapports sur des données volumineuses.

* [Apprentissage automatique](https://azure.microsoft.com/documentation/services/machine-learning/) - vous permet de créer, tester, fonctionner et gérer des solutions analytiques prédictives dans un service entièrement géré.

### <a name="additional-services"></a>Services supplémentaires

Votre solution de calcul importante peut être nécessaire d’autres services Azure pour vous connecter à des ressources sur site ou dans d’autres environnements. Voici quelques exemples :

* [Réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) - crée une section isolée de façon logique dans Azure pour se connecter à des ressources Azure entre eux ou à votre centre de données sur site. Avec un réseau virtuel de coexistence, calculer Big les applications peuvent accéder aux données de locaux, les services Active Directory et les serveurs de licences

* [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) - crée une connexion privée entre les centres de données de Microsoft et de l’infrastructure qui est local ou dans un environnement de colocalisation. ExpressRoute fournit une plus grande sécurité, plus de fiabilité, vitesses plus rapides et des latences plus faible que les connexions classiques sur Internet.

* [Bus de Service](https://azure.microsoft.com/documentation/services/service-bus/) - fournit plusieurs mécanismes pour les applications de communiquer ou d’échanger des données, si elles se trouvent sur Azure, sur une autre plate-forme de nuage, ou dans un centre de données.

## <a name="next-steps"></a>Étapes suivantes

* Consultez les [Ressources techniques pour le traitement par lots et HPC](big-compute-resources.md) pour trouver des conseils techniques pour générer votre solution.

* Discutez de vos options Azure avec partenaires, y compris le Cycle informatique et UberCloud.

* Découvrez Azure Big calculer des solutions fournies par les [Tours Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222), [Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/), [ANSYS](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)et [d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088).

* Pour les dernières annonces, consultez [Microsoft HPC et le blog de l’équipe par lots](http://blogs.technet.com/b/windowshpc/) et le [blog Azure](https://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png
