<properties
   pageTitle="Tolérance pour la récupération de perte d’une assistance technique région Azure | Microsoft Azure"
   description="L’article sur la présentation et conception d’applications à tolérance de pannes de réactifs, hautement disponibles, ainsi que la planification de reprise après sinistre"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-a-region-wide-service-disruption"></a>Guide technique de résilience Azure : restauration à partir d’une interruption de service à l’échelle de la région

Azure est divisé physiquement et logiquement en unités appelées régions. Une zone se compose d’un ou de plusieurs centres de données à proximité. Au moment de la rédaction de ce document, Azure a vingt-quatre régions du monde.

En de rares circonstances, il est possible que les installations dans une région entière peuvent devenir inaccessibles, par exemple en raison de défaillances du réseau. Ou installations peuvent être perdues entièrement, par exemple en raison d’une catastrophe naturelle. Cette section explique les fonctionnalités de Azure pour créer des applications qui sont distribuées sur les régions. Cette distribution permet de minimiser le risque qu’une défaillance dans une région peut affecter d’autres régions.

##<a name="cloud-services"></a>Services en nuage

###<a name="resource-management"></a>Gestion des ressources

Vous pouvez distribuer des instances de calcul entre les régions en créant un service cloud distinct dans chaque région cible, puis publier le package de déploiement à chaque service de cloud. Toutefois, notez que distribution du trafic sur les services en nuage dans différentes régions doit être implémenté par le développeur de l’application ou un service de gestion du trafic.

Déterminer le nombre d’instances de rôle de rechange pour déployer à l’avance pour la reprise après sinistre est un aspect important de la planification de la capacité. Un déploiement à grande échelle de secondaire permet de garantir que la capacité est déjà disponible lorsque cela est nécessaire ; Toutefois, cela double efficacement le coût. Un modèle commun est d’avoir un petit déploiement secondaire, juste assez grand pour exécuter les services critiques. Ce petit déploiement secondaire est une bonne idée, à la fois pour réserver de la capacité et pour tester la configuration de l’environnement secondaire.

>[AZURE.NOTE]Le quota des abonnements n’est pas une garantie de capacité. Le quota est simplement une limite de crédit. Pour garantir la capacité, le nombre de rôles doit être défini dans le modèle de service et les rôles doivent être déployés.

###<a name="load-balancing"></a>Équilibrage de la charge

Pour équilibrer la charge, le trafic entre les régions requiert une solution de gestion du trafic. Azure fournit [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). Vous pouvez également tirer parti des services de tiers qui fournissent des capacités de gestion de tout trafic similaire.

###<a name="strategies"></a>Stratégies de

De nombreuses stratégies alternatives sont disponibles pour la mise en œuvre de calcul distribué dans les régions. Elles doivent être adaptées aux besoins spécifiques et des circonstances de l’application. À un niveau élevé, les approches peuvent être divisés selon les catégories suivantes :

  * __Redéployer sur la reprise après sinistre__: dans cette approche de l’application est redéployée à partir de zéro au moment de la reprise après sinistre. Ceci est approprié pour les applications non critiques qui ne nécessitent pas un temps de récupération garanti.

  * __Secours à chaud (actif/passif)__: un service hébergé secondaire est créé dans une autre région, et les rôles sont déployés pour garantir la capacité minimale ; Cependant, les rôles ne reçoivent pas le trafic de production. Cette approche est utile pour les applications qui n’ont pas été conçues pour répartir le trafic entre les régions.

  * __Disque de secours (actif)__: l’application est conçue pour recevoir la charge de production dans plusieurs régions. Les services en nuage dans chaque région peuvent être configurés pour une capacité plus élevée que nécessaire pour la reprise après sinistre. Vous pouvez également les services en nuage peuvent mettre à l’échelle out en tant que de besoin, au moment de la reprise après sinistre et le basculement. Cette approche nécessite un investissement substantiel dans la conception de l’application, mais il a des avantages significatifs. Il s’agit notamment des faible et garantie reprise, en continu de tous les emplacements de récupération et l’utilisation efficace de la capacité de test.

Une description complète de conception distribuée est en dehors de la portée de ce document. Pour plus d’informations, voir [récupération d’urgence et de haute disponibilité pour les Applications Azure](https://aka.ms/drtechguide).

##<a name="virtual-machines"></a>Ordinateurs virtuels

Récupération de l’infrastructure comme un machines virtuelles de service (IaaS) (VMs) est similaire à la plate-forme que service (PaaS) calculer la récupération à bien des égards. Il existe des différences importantes, cependant, dû au fait qu’un VM IaaS se compose de la machine virtuelle et le disque de la machine virtuelle.

  * __Sauvegarde Azure pour créer des sauvegardes de région croisée qui sont d’application cohérente__.
  [Sauvegarde d’Azure](https://azure.microsoft.com/services/backup/) permet aux clients de créer des sauvegardes cohérentes d’application sur plusieurs disques VM et prend en charge la réplication des sauvegardes sur plusieurs régions. Pour cela, en choisissant de géo-répliquer le coffre-fort de sauvegarde au moment de la création. Notez que la réplication de la sauvegarde en chambre forte doit être configuré au moment de la création. Il ne peut pas être défini plus loin. En cas de perte d’une région, Microsoft rend les sauvegardes disponibles pour les clients. Les clients seront en mesure de restaurer à un de leurs points de restauration configurée.

  * __Séparer le disque de données à partir du disque du système d’exploitation__. Une considération importante pour les machines virtuelles de IaaS est que vous ne pouvez pas modifier le disque du système d’exploitation sans créer à nouveau la machine virtuelle. Si votre stratégie de récupération est de redéployer après sinistre, il ne s’agit pas d’un problème. Toutefois, il peut être un problème si vous utilisez l’approche de rechange à chaud pour réserver de la capacité. Pour implémenter correctement, vous devez disposer du disque du système d’exploitation correct déployé sur les deux sites primaire et secondaire, et les données d’application doivent être stockées sur un lecteur distinct. Si possible, utilisez une configuration de système d’exploitation standard qui peut être fournie sur les deux emplacements. Après un basculement, vous devez ensuite attacher le lecteur de données pour IaaS à vos machines virtuelles existantes dans le contrôleur de domaine secondaire. Utilisez AzCopy pour copier des snapshots de disques de données sur un site distant.

  * __Être conscient des problèmes potentiels de cohérence après géo-basculement de plusieurs disques VM__. Disques VM sont implémentés comme des objets BLOB de stockage Azure et présenter la même caractéristique de réplication géographique. À moins d’utiliser [Sauvegarde d’Azure](https://azure.microsoft.com/services/backup/) , ne sont aucune garantie de cohérence sur disques, car géo-réplication est asynchrone et la réplique de manière indépendante. Des disques VM individuelles sont garantis dans un incident cohérent état après un basculement de zone géographique, mais n’est pas cohérent sur plusieurs disques. Cela pourrait provoquer des problèmes dans certains cas (par exemple, en cas d’entrelacement de disques).

##<a name="storage"></a>Stockage

###<a name="recovery-by-using-geo-redundant-storage-of-blob-table-queue-and-vm-disk-storage"></a>Restauration en utilisant le stockage redondant Geo de blob, table, file d’attente et le stockage sur disque VM

Dans Azure, BLOB, des tables, des files d’attente et des VM disques sont tous géo-répliquées par défaut. On parle alors en tant que stockage redondant Geo (GRS). GRS réplique les données de stockage pour un centre de données de paires des centaines de miles séparés au sein d’une zone géographique spécifique. GRS est conçu pour fournir une durabilité supplémentaire en cas de sinistre majeur. Les contrôles de Microsoft lors du basculement et le basculement est limitée à des catastrophes majeures dans lequel l’emplacement principal d’origine est considérée comme non récupérable dans un laps de temps raisonnable. Dans certains scénarios, cela peut être de plusieurs jours. Les données sont généralement répliquées dans quelques minutes, bien que l’intervalle de synchronisation n’est pas encore couvert par un contrat de niveau de service.

En cas de basculement géo, il n’y aura aucun changement dans le mode d’accès du compte (la clé de l’URL et le compte ne changera pas). Le compte de stockage, cependant, sera dans une région différente après le basculement. Cela peut affecter les applications qui requièrent une affinité régionale avec leur compte de stockage. Même pour les services et les applications qui ne requièrent pas un compte de stockage dans le même centre de données, la latence de cross-centre de données et les frais de bande passante peuvent être raisons impérieuses pour atteindre le trafic de la région de basculement temporairement. Cela pourrait factoriser dans une stratégie globale de récupération après sinistre.

En plus du basculement automatique fournie par GRS, Azure a introduit un service qui vous offre un accès en lecture à la copie de vos données dans l’emplacement de stockage secondaire. Il s’agit de stockage Geo redondant d’accès en lecture (RA-GRS).

Pour plus d’informations sur le stockage GRS et de RA-GRS, voir [réplication de stockage Azure](../storage/storage-redundancy.md).

###<a name="geo-replication-region-mappings"></a>Mappages de région géographique-réplication :

Il est important de savoir où vos données sont répliquées géo, pour savoir où déployer les autres instances de vos données qui nécessitent une affinité régionale avec votre système de stockage. Le tableau suivant indique les appariements d’emplacement principal et secondaire :

[AZURE.INCLUDE [paired-region-list](../../includes/paired-region-list.md)]

###<a name="geo-replication-pricing"></a>Geo-réplication de tarification :

Geo-réplication est incluse dans le prix actuel de stockage Azure. Il s’agit de stockage redondant Geo (GRS). Si vous ne souhaitez pas que vos données répliquées géo vous pouvez désactiver géo-réplication pour votre compte. Cela est appelé localement le stockage redondant et il est facturé à un prix réduit par rapport aux GRS.

###<a name="determining-if-a-geo-failover-has-occurred"></a>Déterminer si un géo-basculement

Si un géo-basculement, validé pour la [Santé du tableau de bord Service Azure](https://azure.microsoft.com/status/). Les applications peuvent implémenter un moyen automatisé de détecter, toutefois, en surveillant la région géographique pour leur compte de stockage. Cela peut servir à déclencher d’autres opérations de restauration, tels que l’activation des ressources de calcul dans la région géographique où leur stockage déplacé vers. Vous pouvez effectuer une requête pour cela à partir de l’API, de gestion de service à l’aide [d’Obtenir les propriétés de compte de stockage](https://msdn.microsoft.com/library/ee460802.aspx). Les propriétés pertinentes sont les suivantes :

    <GeoPrimaryRegion>primary-region</GeoPrimaryRegion>
    <StatusOfPrimary>[Available|Unavailable]</StatusOfPrimary>
    <LastGeoFailoverTime>DateTime</LastGeoFailoverTime>
    <GeoSecondaryRegion>secondary-region</GeoSecondaryRegion>
    <StatusOfSecondary>[Available|Unavailable]</StatusOfSecondary>

###<a name="vm-disks-and-geo-failover"></a>Disques VM et géo-basculement

Comme expliqué dans la section sur les disques de l’ordinateur virtuel, n’existe aucune garantie de la cohérence des données entre les disques de l’ordinateur virtuel après un basculement. Pour garantir l’exactitude des sauvegardes, un logiciel de sauvegarde tel que Data Protection Manager doit être utilisé pour sauvegarder et restaurer des données d’application.

##<a name="database"></a>Base de données

###<a name="sql-database"></a>Base de données SQL

Une base de données SQL Azure fournit deux types de récupération : géo-restauration et géo-réplication Active.

####<a name="geo-restore"></a>Geo-restauration

[Geo-restauration](../sql-database/sql-database-recovery-using-backups.md#geo-restore) est également disponible avec les bases de données Basic, Standard et Premium. Il offre l’option de récupération par défaut lorsque la base de données n’est pas disponible en raison d’un incident dans la région où votre base de données est hébergée. Similaire à la restauration de Point-à-temps, géo-restauration s’appuie sur des sauvegardes de base de données dans le stockage Azure geo redondant. Il restaure à partir de la copie de sauvegarde de réplication géographique et par conséquent résiste aux interruptions de stockage dans la région principale. Pour plus d’informations, consultez [restaurer une base de données de SQL Azure ou le basculement vers le secondaire](../sql-database/sql-database-disaster-recovery.md).

####<a name="active-geo-replication"></a>Geo-réplication Active

[Geo-réplication Active](../sql-database/sql-database-geo-replication-overview.md) n’est disponible pour tous les niveaux de base de données. Il est conçu pour les applications ayant des exigences de récupération plus agressifs que géo-restauration peut offrir. Geo-réplication Active, vous pouvez créer jusqu'à quatre secondaires lisibles sur des serveurs dans différentes régions. Vous pouvez déclencher le basculement des copies secondaires. En outre, géo-réplication Active peut servir pour les scénarios de mise à niveau ou de déplacement d’applications de prendre en charge, ainsi que l’équilibrage des charges de travail en lecture seule de charge. Pour plus d’informations, voir [configurer la réplication de la zone géographique](../sql-database/sql-database-geo-replication-portal.md) et de [Basculer vers la base de données secondaire](../sql-database/sql-database-geo-replication-failover-portal.md). Pour plus d’informations sur la conception et les applications de mise en œuvre et de mise à niveau d’applications sans interruption de service, reportez-vous [à l’aide de la conception d’une application de reprise après sinistre de nuage géo-réplication Active dans la base de données de SQL](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md) et de [mises à niveau propagées à gestion des applications en nuage à l’aide de SQL Active géo-réplication](../sql-database/sql-database-manage-application-rolling-upgrade.md) .

###<a name="sql-server-on-virtual-machines"></a>SQL Server sur des Machines virtuelles

De nombreuses options sont disponibles pour la restauration et de haute disponibilité pour SQL Server 2012 (et versions ultérieures) dans Azure Machines virtuelles en fonctionnement. Pour plus d’informations, reportez-vous à la section [haute disponibilité et reprise après sinistre pour SQL Server dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

##<a name="other-azure-platform-services"></a>Autres services de la plateforme Azure

Lorsque vous tentez d’exécuter votre service cloud dans plusieurs régions Azure, vous devez considérer les implications pour chacun de vos dépendances. Dans les sections suivantes, les instructions spécifiques au service part du principe que vous devez utiliser le même service d’Azure dans un autre centre de données Azure. Ceci implique la configuration et les tâches de réplication des données.

>[AZURE.NOTE]Dans certains cas, ces étapes peuvent aider à atténuer une interruption de service spécifique au lieu d’un événement de centre de données entier. Du point de vue de l’application, une panne de service spécifiques peut être aussi limiter et nécessiterait temporairement la migration le service vers une autre zone d’Azure.

###<a name="service-bus"></a>Bus des services

Bus des services Azure utilise un espace de noms unique qui ne couvre pas les régions Azure. La première exigence consiste à configurer les espaces de noms du bus de service nécessaires dans la région de remplacement. Toutefois, il existe également des considérations pour la durabilité des messages en file d’attente. Il existe plusieurs stratégies pour la réplication des messages entre les régions Azure. Pour plus d’informations sur ces stratégies de réplication et d’autres stratégies de récupération d’urgence, consultez [méthodes conseillées pour l’isolation des applications contre les interruptions de Service Bus et les sinistres](../service-bus-messaging/service-bus-outages-disasters.md). D’autres considérations relatives à la disponibilité, consultez [Le Bus de Service (disponibilité)](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

###<a name="app-service"></a>Service d’application

Pour migrer une application de Service d’application Azure, tels que des applications Web ou applications Mobile, à une région Azure secondaire, vous devez disposer d’une sauvegarde du site Web disponible pour la publication. Si la panne n’implique pas la totalité du datacenter Azure, il est parfois possible d’utiliser FTP pour télécharger une sauvegarde récente du contenu du site. Puis créez une nouvelle application dans la région de remplacement, sauf si vous l’avez déjà fait pour réserver de la capacité. Publiez le site sur la nouvelle zone et apportez les modifications de configuration nécessaires. Ces modifications peuvent inclure les chaînes de connexion de base de données ou d’autres paramètres spécifiques à une région. Si nécessaire, ajoutez le certificat de site SSL et modifier l’enregistrement DNS CNAME afin que le nom de domaine personnalisé pointe vers l’URL d’application Web Azure redéployé.

###<a name="hdinsight"></a>HDInsight

Les données associées à HDInsight sont stockées par défaut dans le stockage Blob Azure. HDInsight requiert qu’un cluster Hadoop MapReduce travaux de traitement doit coexister dans la même région que le compte de stockage contenant les données en cours d’analyse. Si vous utilisez la fonctionnalité de réplication géographique disponible pour le stockage Azure, vous pouvez accéder à vos données dans la zone secondaire où les données a été répliquées si pour une raison quelconque la région principale n’est plus disponible. Vous pouvez créer un nouveau cluster Hadoop dans la région où les données ont été répliquées et poursuivre son traitement. Pour les autres considérations relatives à la disponibilité, consultez [HDInsight (disponibilité)](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

###<a name="sql-reporting"></a>Création de rapports SQL

À ce stade, la récupération de la perte d’une région Azure exige plusieurs instances de SQL Reporting dans différentes régions d’Azure. Ces instances SQL Reporting doivent accéder aux mêmes données et ces données doivent avoir son propre récupération plan en cas de sinistre. Vous pouvez également conserver des copies de sauvegarde externes du fichier RDL pour chaque rapport.

###<a name="media-services"></a>Services de support

Azure Media Services a une approche de restauration différentes pour le codage et diffusion en continu. En règle générale, la diffusion est plus critique suite à une panne régionale. Pour préparer, vous devez disposer d’un compte de Services de support dans deux différentes régions Azure. Le contenu codé doit se trouver dans les deux zones. Lors d’une défaillance, vous pouvez rediriger le trafic de diffusion en continu à la région de remplacement. Codage peut être effectuée dans n’importe quelle région Azure. Si le codage dépend du temps, par exemple pendant le traitement de l’événement en direct, vous devez être préparé à soumettre des tâches à un autre centre de données lors de défaillances.

###<a name="virtual-network"></a>Réseau virtuel

Fichiers de configuration fournissent le moyen le plus rapide pour configurer un réseau virtuel dans une autre région d’Azure. Après avoir configuré le réseau virtuel dans la région Azure primaire, [exportez les paramètres de réseau virtuel](../virtual-network/virtual-networks-create-vnet-classic-portal.md) pour le réseau en cours vers un fichier de configuration réseau. Dans le cas d’une panne dans la région principale, [restaurer le réseau virtuel](../virtual-network/virtual-networks-create-vnet-classic-portal.md) à partir du fichier de configuration stockée. Puis configurer les autres services en nuage, les ordinateurs virtuels ou les paramètres de coexistence pour travailler avec le nouveau réseau virtuel.

##<a name="checklists-for-disaster-recovery"></a>Listes de vérification pour la reprise après sinistre

##<a name="cloud-services-checklist"></a>Liste de contrôle de Services cloud

  1. Consultez la section Services de nuage de ce document.
  2. Créer une stratégie de récupération d’urgence de cross-région.
  3. Comprendre les compromis de réservation de capacité dans les autres régions.
  4. Utiliser des outils d’acheminement du trafic, tels que le Gestionnaire de trafic Azure.

##<a name="virtual-machines-checklist"></a>Liste de contrôle de Machines virtuelles

  1. Consultez la section ordinateurs virtuels de ce document.
  2. [Sauvegarde d’Azure](https://azure.microsoft.com/services/backup/) permet de créer des sauvegardes cohérentes d’application entre les régions.

##<a name="storage-checklist"></a>Liste de contrôle de stockage

  1. Consultez la section de stockage de ce document.
  2. Ne désactivez pas la réplication géographique des ressources de stockage.
  3. Comprendre l’autre région géographique-réplication en cas de basculement.
  4. Créer des stratégies de sauvegarde personnalisés pour les stratégies de basculement de contrôlées par l’utilisateur.

##<a name="sql-database-checklist"></a>Liste de vérification de la base de données SQL

  1. Consultez la section de la base de données SQL de ce document.
  2. Utilisez [Géo-restauration](../sql-database/sql-database-recovery-using-backups.md#geo-restore) ou [Réplication géographique](../sql-database/sql-database-geo-replication-overview.md) selon le cas.

##<a name="sql-server-on-virtual-machines-checklist"></a>SQL Server dans la liste de contrôle de Machines virtuelles

  1. Passez en revue le SQL Server sur des Machines virtuelles des section de ce document.
  2. Utilisez les groupes de disponibilité AlwaysOn cross-région ou de la mise en miroir de base de données.
  3. Vous pouvez également utiliser la sauvegarde et restauration du stockage BLOB.

##<a name="service-bus-checklist"></a>Liste de contrôle de Bus des services

  1. Consultez la section Service Bus de ce document.
  2. Configurer le Bus de Service d’un espace de noms dans une autre région.
  3. Considérer les stratégies de réplication personnalisée de messages entre les régions.

##<a name="app-service-checklist"></a>Liste de contrôle de Service de l’application

  1. Consultez la section Service de l’application de ce document.
  2. Tenir à jour les sauvegardes de sites à l’extérieur de la zone primaire.
  3. Si la panne est partielle, essayez de récupérer le site actuel avec le protocole FTP.
  4. Envisagez de déployer le site sur un site nouveau ou existant dans une autre région.
  5. Planifier les modifications de configuration pour les applications et les enregistrements DNS CNAME.

##<a name="hdinsight-checklist"></a>Liste de contrôle HDInsight

  1. Consultez la section HDInsight de ce document.
  2. Créer un nouveau cluster Hadoop dans la région de données répliquées.

##<a name="sql-reporting-checklist"></a>Liste de contrôle de génération d’états SQL

  1. Consultez la section Création de rapports SQL de ce document.
  2. Tenir à jour une autre instance de SQL Reporting dans une région différente.
  3. Tenir à jour un plan différent pour répliquer la cible à cette région.

##<a name="media-services-checklist"></a>Liste de contrôle des Services de support

  1. Consultez la section Services de support de ce document.
  2. Créez un compte de Services de support dans une autre région.
  3. Coder le même contenu dans les deux zones pour prendre en charge le basculement de transmission en continu.
  4. Soumettre les tâches de codage à une autre région dans le cas d’une interruption de service.

##<a name="virtual-network-checklist"></a>Liste de contrôle de réseau virtuel

  1. Consultez la section réseau virtuel de ce document.
  2. Utilisez exporté les paramètres de réseau virtuel pour recréer dans une autre région.

##<a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série de [conseils techniques de résilience Azure](./resiliency-technical-guidance.md). Le prochain article de cette série met l’accent sur la [récupération à partir d’un centre de données local pour Azure](./resiliency-technical-guidance-recovery-on-premises-azure.md).
