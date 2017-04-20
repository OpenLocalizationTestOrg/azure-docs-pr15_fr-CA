<properties
   pageTitle="La continuité d’activité en nuage - récupération - de la base de données SQL de la base de données | Microsoft Azure"
   description="Découvrez comment prend en charge la base de données de SQL Azure cloud une continuité d’activité et de récupération de la base de données et vous permet de conserve les applications en nuage critiques en cours d’exécution."
   keywords="la continuité d’activité, continuité d’activité de nuage, récupération d’urgence de base de données, récupération de la base de données"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Vue d’ensemble de la continuité d’activité avec une base de données de SQL Azure

Cette présentation décrit les fonctionnalités de base de données de SQL Azure fournit pour la continuité d’activité et de reprise après sinistre. Il fournit des options, des conseils et des didacticiels de récupération à partir des interruptions qui pourraient entraîner une perte de données ou provoquer votre base de données et l’application ne sont plus disponibles. La discussion inclut la procédure à suivre lorsqu’un utilisateur ou erreur d’application affecte l’intégrité des données, une zone Azure a une panne ou votre application requiert une maintenance. 

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Fonctionnalités de base de données SQL que vous pouvez utiliser pour fournir une continuité d’activité

Base de données de SQL fournit plusieurs fonctionnalités de continuité d’activité commerciale, y compris les sauvegardes automatisées et la réplication de la base de données facultatif. Chacun a des caractéristiques différentes pour le temps de reprise estimé (insérer) et la perte de données potentielle pour les transactions récentes. Une fois que vous comprenez ces options, vous pouvez choisir parmi les - et, dans la plupart des scénarios, les utiliser conjointement à des scénarios différents. Lorsque vous développez votre plan de continuité de service, vous devez comprendre le maximum de temps acceptable avant que l’application est entièrement récupère après l’événement d’interruption de service - il s’agit de votre objectif de temps de récupération (RTO). Vous devez également connaître la quantité maximale de données mises à jour récentes (intervalle) l’application peut tolérer perdre lors de la récupération après l’événement d’interruption de service : l’objectif de point de récupération (RPO). 

Le tableau suivant compare le RPO et le convertir pour les trois scénarios les plus courants.

| Capacité |  Couche de base | Couche standard  | Niveau de prime |
|---|---|---|---|
| Point dans le temps restaurer à partir de la sauvegarde | Aucun point de restauration dans les 7 jours   | Aucun point de restauration au sein de 35 jours  | Aucun point de restauration au sein de 35 jours |
Geo-restaurer à partir de sauvegardes de réplication géographique | Insér < 12h, RPO < 1h   | Insér < 12h, RPO < 1h   | Insér < 12h, RPO < 1h |
|Geo-réplication Active | Insér < 30 s, RPO < 5 s   | Insér < 30 s, RPO < 5 s | Insér < 30 s, RPO < 5 s |


### <a name="use-database-backups-to-recover-a-database"></a>Les sauvegardes de base de données permet de récupérer une base de données

Base de données de SQL exécute automatiquement une combinaison de sauvegardes complètes toutes les semaines, différentielles de base de données des sauvegardes toutes les heures et sauvegardes de journal des transactions toutes les cinq minutes pour protéger votre entreprise contre la perte de données. Ces sauvegardes sont stockées dans le stockage redondant localement 35 jours pour les bases de données dans les niveaux de service Standard et Premium et sept jours pour les bases de données dans la couche de service de base - voir les [niveaux de service](sql-database-service-tiers.md) pour plus d’informations sur les niveaux de service. Si la période de rétention pour votre niveau de service ne répond pas aux besoins de votre entreprise, vous pouvez augmenter la période de rétention en [modifiant le niveau de service](sql-database-scale-up.md). La base de données complète et différentiel, les sauvegardes sont également répliqués sur un [Centre de données associés](../best-practices-availability-paired-regions.md) de protection contre une panne du centre de données - voir [les sauvegardes automatiques de la base de données](sql-database-automated-backups.md) pour plus de détails.

Vous pouvez utiliser ces sauvegardes automatiques de la base de données pour restaurer une base de données à partir de divers événements d’interruption de service, au sein de votre centre de données et à un autre centre de données. À l’aide des sauvegardes automatiques de la base de données, le temps estimé de la restauration dépend de plusieurs facteurs, notamment le nombre total de bases de données de récupération dans la même région en même temps, la taille de la base de données, la taille du journal des transactions et la bande passante du réseau. Dans la plupart des cas, le temps de récupération est de moins de 12 heures. Lors de la récupération d’une autre région de données, la perte de données potentielle est limitée à 1 heure par le stockage redondant géographique des sauvegardes de base de données différentielle toutes les heures. 

> [AZURE.IMPORTANT] Pour récupérer à l’aide de sauvegardes automatisées, vous devez être un membre du rôle Collaborateur de SQL Server ou le propriétaire de l’abonnement - voir [RBAC : rôles intégrés](../active-directory/role-based-access-built-in-roles.md). Vous pouvez récupérer à l’aide de l’API REST, PowerShell ou l’Azure portal. Vous ne pouvez pas utiliser Transact-SQL.

Utilisez des sauvegardes automatisées comme mécanisme de continuité d’activité et de récupération business si votre application :

- Ne fait pas mission critique.
- N’a pas un SLA de liaison par conséquent le temps d’inactivité de 24 heures ou plus n’entraînent pas de responsabilité financière.
- A un faible taux de modification des données (faibles transactions par heure) et de perdre jusqu'à une heure de modification est une perte de données acceptable. 
- Tient compte des coûts. 

Si vous avez besoin d’une récupération plus rapide, utilisez [Géo-réplication Active](sql-database-geo-replication-overview.md) (étudié ci-après). Si vous avez besoin de pouvoir récupérer des données d’une période de plus de 35 jours, pensez à l’archivage de votre base de données régulièrement à un fichier de type sac à DOS (un fichier compressé contenant votre schéma de base de données et les données associées) stockés dans le stockage blob Azure ou dans un autre emplacement de votre choix. Pour plus d’informations sur la façon de créer une archive de base de données cohérentes, consultez [créer une copie de la base de données](sql-database-copy.md) et [Exporter la copie de la base de données](sql-database-export.md). 

### <a name="use-active-geo-replication-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Geo-réplication Active permet de réduire le temps de récupération et de limiter les pertes de données associés à une récupération

En plus d’utiliser des sauvegardes de base de données pour la récupération de la base de données dans le cas d’une interruption de service, vous pouvez utiliser [Géo-réplication Active](sql-database-geo-replication-overview.md) pour configurer une base de données pour que les bases de données secondaires lisibles jusqu'à quatre dans les régions de votre choix. Ces bases de données secondaires sont synchronisés avec la base de données principale à l’aide d’un mécanisme de réplication asynchrone. Cette fonctionnalité est utilisée pour se protéger contre l’interruption de service en cas de panne du centre de données ou au cours d’une mise à niveau de l’application. Geo-réplication Active permet également à fournir les meilleures performances de requête pour les requêtes en lecture seule aux utilisateurs géographiquement dispersés.

Si la base de données primaire est déconnecté de manière inattendue ou si vous devez déconnecter pour des activités de maintenance, vous pouvez rapidement promouvoir secondaire pour devenir la principale (également appelée un basculement) et configurer les applications pour se connecter à primaire nouvellement promu. Avec un basculement planifié, il n’existe aucune perte de données. Avec un basculement non planifié, il peut exister une certaine perte de données pour les transactions très récentes en raison de la nature de la réplication asynchrone. Après un basculement, vous pouvez la restauration ultérieure - selon un plan ou lorsque le centre de données revient en ligne. Dans tous les cas, les utilisateurs de rencontrer une petite quantité de temps d’arrêt et doivent se reconnecter. 

> [AZURE.IMPORTANT] Pour utiliser géo-réplication Active, vous devez être le propriétaire ou disposer des autorisations d’administration dans SQL Server. Vous pouvez configurer et basculement sur le portail Azure, PowerShell ou l’API REST, à l’aide des autorisations sur l’abonnement ou à l’aide de Transact-SQL à l’aide d’autorisations dans SQL Server.

Utilisez géo-réplication Active si votre application répond à un de ces critères :

- Est la mission critique.
- A un contrat de niveau de service (SLA) qui n’autorise pas de 24 heures ou plus de temps d’arrêt.
- Temps d’inactivité entraîne la responsabilité financière.
- A un taux élevé de données modification est élevée et la perte d’une heure de données n’est pas acceptable.
- Le coût supplémentaire de géo-réplication active est inférieur à la responsabilité financière et entraîne une perte de l’entreprise.

## <a name="recover-a-database-after-a-user-or-application-error"></a>Restaurer une base de données après une erreur de l’utilisateur ou l’application

* Personne n’est parfait ! Un utilisateur peut supprimer accidentellement des données, accidentellement supprimer une table importante ou même supprimer une base de données entière. Ou bien, une application peut remplacer accidentellement les données avec des données erronées en raison d’un défaut d’application. 

Dans ce scénario, il s’agit de vos options de récupération.

### <a name="perform-a-point-in-time-restore"></a>Effectuer une restauration de point-à-temps

Vous pouvez utiliser les sauvegardes automatisées pour récupérer une copie de votre base de données à un bon point connu dans le temps, sous réserve que la durée est comprise dans la période de rétention de base de données. Une fois la base de données est restaurée, vous pouvez remplacer la base de données d’origine avec la base de données restaurée ou copier les données nécessaires à partir des données restaurées dans la base de données d’origine. Si la base de données utilise géo-réplication Active, nous vous recommandons de copier les données requises à partir de la copie restaurée dans la base de données d’origine. Si vous remplacez la base de données d’origine avec la base de données restaurée, vous devrez reconfigurer et resynchroniser géo-réplication Active (ce qui peut prendre un certain temps pour une base de données de grande taille). 

Pour plus d’informations et pour obtenir la procédure détaillée pour la restauration d’une base de données à un point dans le temps à l’aide du portail Azure ou à l’aide de PowerShell, voir [restauration de point-à-temps](sql-database-recovery-using-backups.md#point-in-time-restore). Vous ne pouvez pas récupérer à l’aide de Transact-SQL.

### <a name="restore-a-deleted-database"></a>Restaurer une base de données supprimé

Si la base de données est supprimée, mais le serveur logique n’a pas été supprimé, vous pouvez restaurer la base de données supprimé au point auquel il a été supprimé. Cela permet de restaurer une sauvegarde de base de données sur le même serveur SQL logique à partir de laquelle il a été supprimé. Vous pouvez restaurer le nom original ou fournir un nouveau nom ou la base de données restaurée.

Pour plus d’informations et pour obtenir la procédure détaillée pour la restauration d’une base de données supprimé via le portail d’Azure, ou à l’aide de PowerShell, consultez [restauration d’une base de données supprimée](sql-database-recovery-using-backups.md#deleted-database-restore). Vous ne pouvez pas restaurer à l’aide de Transact-SQL.

> [AZURE.IMPORTANT] Si le serveur logique est supprimé, vous ne pouvez pas récupérer une base de données supprimée. 

### <a name="import-from-a-database-archive"></a>Importer à partir d’une archive de base de données

Si la perte de données s’est produite en dehors de la période de rétention en cours de sauvegardes automatiques et vous avez été l’archivage de la base de données, vous pouvez [Importer un fichier de type sac à DOS archivé](sql-database-import.md) vers une nouvelle base de données. À ce stade, vous pouvez remplacer la base de données d’origine avec la base de données importé ou copier les données nécessaires à partir des données importées dans la base de données d’origine. 

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Restaurer une base de données dans une autre région à partir d’une panne du centre des données régionales Azure

<!-- Explain this scenario -->

Bien que rares, un centre de données Azure peut avoir une panne. En cas de panne, il provoque une interruption de service qui peut-être durer seulement quelques minutes ou peut durer des heures. 

- Une option consiste à attendre pour être remise en ligne lorsque la panne du centre de données se trouve sur votre base de données. Cela fonctionne pour les applications qui peuvent se permettre de voir la base de données en mode hors connexion. Par exemple, un projet de développement ou de la version d’évaluation gratuite vous n’avez pas besoin de travailler en permanence. Lorsqu’un centre de données a une panne, vous ne saurez pas combien de temps dure la panne, afin que cette option fonctionne uniquement si vous ne devez pas votre base de données pendant un certain temps.
- Une autre option consiste à un basculement vers une autre région de données si vous utilisez géo-réplication Active ou la restauration, l’utilisation de sauvegardes de base de données redondante géo (Geo-restauration). Basculement sur incident ne prend que quelques secondes, alors que la récupération à partir de sauvegardes prend des heures.

Quand vous prenez l’action et le temps nécessaire pour récupérer la quantité perte de données que vous encourez en cas de panne du centre de données dépend de comment vous décidez d’utiliser les fonctionnalités de continuité d’activité d’entreprise ci-dessus dans votre application. En effet, vous pouvez choisir d’utiliser une combinaison de sauvegardes de base de données et géo-réplication Active en fonction des besoins de votre application. Pour une discussion sur les considérations de conception d’applications de bases de données autonomes et de pools élastiques à l’aide de ces fonctionnalités de continuité d’activité, consultez [Création d’une application de reprise après sinistre de nuage](sql-database-designing-cloud-solutions-for-disaster-recovery.md) et les [stratégies de récupération d’urgence Pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Les sections suivantes fournissent une vue d’ensemble des étapes pour récupérer à l’aide de sauvegardes de base de données ou de géo-réplication Active. Pour obtenir la procédure détaillée, y compris la planification des besoins, valider les étapes de récupération et d’informations sur comment simuler une panne pour effectuer un exercice de restauration après sinistre, consultez [restauration d’une base de données SQL en cas de panne](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Préparer une panne

Quelle que soit la fonctionnalité de continuité d’activité d’entreprise que vous utilisez, vous devez :

- Identifier et préparer le serveur cible, y compris les règles de pare-feu au niveau du serveur, les connexions et les autorisations de niveau base de données master.
- Déterminer comment faire pour rediriger les clients et les applications du client vers le nouveau serveur
- Document d’autres dépendances, comme l’audit des paramètres et des alertes 
 
Si vous ne pas planifier et préparer correctement, mettre vos applications en ligne après qu’un basculement ou une restauration prend plus de temps et probablement nécessitent également de résolution des problèmes liés à la fois de stress - une combinaison incorrecte.

### <a name="failover-to-a-geo-replicated-secondary-database"></a>Basculement vers une base de données secondaire géo-réplication 

Si vous utilisez géo-réplication Active comme mécanisme de récupération, [forcer un basculement vers le secondaire géo-répliquées](sql-database-disaster-recovery.md#failover-to-geo-replicated-secondary-database). Quelques secondes, l’image secondaire est promu pour devenir la nouvelle primaire et est prêt à enregistrer de nouvelles transactions et à répondre à des requêtes - avec seulement quelques secondes de perte de données pour les données qui n’ont pas encore été répliquées. Pour plus d’informations sur l’automatisation du processus de basculement, voir [Création d’une application de reprise après sinistre de nuage](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [AZURE.NOTE] Lorsque le centre de données revient en ligne, vous pouvez la restauration automatique vers le serveur principal d’origine (ou non).

### <a name="perform-a-geo-restore"></a>Effectuer une restauration de Geo 

Si vous utilisez automatisée des sauvegardes avec la réplication de stockage redondant géographique comme mécanisme de récupération, [initier une récupération de base de données à l’aide de géo-restauration](sql-database-disaster-recovery.md#recover-using-geo-restore). Récupération a généralement lieu dans les 12 heures, avec une perte de données jusqu'à une heure déterminée par lorsque la dernière sauvegarde différentielle horaire avec prises et répliquées. Jusqu'à ce que la récupération est terminée, la base de données ne peut pas enregistrer toutes les transactions ou de répondre à toutes les requêtes. 

> [AZURE.NOTE] Si le centre de données est remis en ligne avant de passer votre application la base de données récupérée, vous pouvez annuler simplement la récupération.  

### <a name="perform-post-failover--recovery-tasks"></a>Exécuter après basculement sur incident / tâches de récupération 

Après la récupération d’un mécanisme de récupération, vous devez effectuer les tâches supplémentaires suivantes avant que les utilisateurs et les applications sont de nouveau en cours d’exécution :

- Rediriger les clients et les applications clientes sur le nouveau serveur et base de données restaurée
- Assurez-vous que les règles de pare-feu de niveau serveur appropriées sont en place pour les utilisateurs à se connecter (ou utiliser des [pare-feux au niveau de base de données](sql-database-firewall-configure.md#creating-database-level-firewall-rules))
- Vérifiez les connexions appropriées et les autorisations de niveau base de données master sont en place (ou utiliser le [contenu des utilisateurs](https://msdn.microsoft.com/library/ff929188.aspx))
- Configurer l’audit, le cas échéant
- Configurer des alertes, selon le cas

## <a name="upgrade-an-application-with-minimal-downtime"></a>Mise à niveau d’une application avec un temps d’arrêt minimal

Parfois, une application doit être mise hors connexion en raison d’une maintenance planifiée comme une mise à niveau de l’application. [Mises à niveau des applications de gestion](sql-database-manage-application-rolling-upgrade.md) décrit comment géo-réplication Active permet d’activer les mises à niveau de votre application en nuage pour minimiser les interruptions lors des mises à niveau et de fournir un chemin de récupération en cas de dysfonctionnement. Cet article présente deux méthodes différentes d’orchestrer le processus de mise à niveau et explique les avantages et les inconvénients de chaque option.

## <a name="next-steps"></a>Étapes suivantes

Pour une discussion sur les considérations de conception d’application pour les bases de données autonomes et pour les pools élastiques, consultez [Création d’une application de reprise après sinistre de nuage](sql-database-designing-cloud-solutions-for-disaster-recovery.md) et les [stratégies de récupération d’urgence Pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).






