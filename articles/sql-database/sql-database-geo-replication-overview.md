<properties
    pageTitle="Geo-réplication Active pour la base de données SQL Azure"
    description="Geo-réplication Active vous permet de configurer 4 réplicas de votre base de données dans un des centres de données Azure."
    services="sql-database"
    documentationCenter="na"
    authors="stevestein"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="NA"
    ms.date="09/26/2016"
    ms.author="sstein" />

# <a name="overview-sql-database-active-geo-replication"></a>Vue d’ensemble : SQL de base de données Active géo-réplication

Geo-réplication Active vous permet de configurer jusqu'à quatre bases de données secondaires lisibles dans les emplacements de centre de données identiques ou différentes (zones). Bases de données secondaires sont disponibles pour l’interrogation et de basculement sur incident en cas de panne du centre de données ou l’impossibilité de se connecter à la base de données principale.

>[AZURE.NOTE] Geo-réplication Active (lisibles secondaires) est maintenant disponible pour toutes les bases de données de tous les niveaux de service. En avril 2017, est déclassé le type secondaire non lisible et bases de données non lisible passeront automatiquement à secondaires lisibles.

 Vous pouvez configurer à l’aide du [portail Azure](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md), [Transact-SQL](sql-database-geo-replication-transact-sql.md), géo-réplication Active ou la [l’API REST - création ou mise à jour de base de données](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Configurer : Portail Azure](sql-database-geo-replication-portal.md)
- [Configurer : PowerShell](sql-database-geo-replication-powershell.md)
- [Configurer : T-SQL](sql-database-geo-replication-transact-sql.md)

If pour tout motif d’échec de votre base de données principale, ou doit simplement être mis hors connexion, vous pouvez *Basculer* à une de vos bases de données secondaires. Lorsque le basculement est activé pour une base de données secondaires, tous les autres serveurs secondaires sont automatiquement liés à la nouvelle primaire.

Vous pouvez basculer vers un secondaire à l’aide d' [Azure portal](sql-database-geo-replication-failover-portal.md), [PowerShell](sql-database-geo-replication-failover-powershell.md), [Transact-SQL](sql-database-geo-replication-failover-transact-sql.md), [l’API REST - planifié le basculement](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)ou [API REST - basculement non planifié](https://msdn.microsoft.com/library/azure/mt582027.aspx).


> [AZURE.SELECTOR]
- [Basculement : Portail Azure](sql-database-geo-replication-failover-portal.md)
- [Basculement sur incident : PowerShell](sql-database-geo-replication-failover-powershell.md)
- [Basculement sur incident : T-SQL](sql-database-geo-replication-failover-transact-sql.md)

Après le basculement, assurez-vous que les exigences d’authentification de votre serveur et de base de données sont configurés sur le serveur primaire de nouveau. Pour plus d’informations, consultez [sécurité de base de données SQL après un sinistre](sql-database-geo-replication-security-config.md).


La fonctionnalité de géo-réplication Active implémente un mécanisme pour assurer la redondance de base de données dans la même région de Microsoft Azure ou dans différentes régions (redondance géographique). Geo-réplication Active en mode asynchrone réplique les transactions validées à partir d’une base de données jusqu'à quatre copies de la base de données sur des serveurs différents, à l’aide de l’isolement de capture instantanée validée (objet) pour l’isolation de lecture. Lors de la configuration de géo-réplication Active, une base de données secondaire est créée sur le serveur spécifié. La base de données d’origine devient la base de données principale. La base de données principale asynchrone réplique les transactions validées pour chaque base de données secondaires. Seules les transactions complètes sont répliquées. Tout en n’importe quel point donné, la base de données secondaire peut être légèrement derrière la base de données principale, les données secondaires sont garanties de ne jamais avoir de transactions partielles. Vous trouverez les données RPO spécifiques à la [Vue d’ensemble de continuité d’activité](sql-database-business-continuity.md).

Un des principaux avantages de géo-réplication Active est de fournir une solution de récupération après incident de niveau de base de données avec un temps de récupération faible. Lorsque vous placez la base de données secondaire sur un serveur dans une autre région, vous ajoutez une résistance maximale à votre application. Redondance de cross-région permet aux applications de récupérer d’une perte permanente d’un centre de données entier ou des parties d’un centre de données provoquée par des catastrophes naturelles, les erreurs humaines catastrophiques ou actes malveillants. La figure suivante illustre qu'un exemple de géo-réplication Active est configuré avec un serveur principal dans la région du Nord centrale nous et secondaire dans la région Sud.

![Relation de géo-réplication](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Un autre avantage est que les bases de données secondaires sont lisibles et peuvent être utilisés pour décharger les charges de travail en lecture seule telles que les travaux de création de rapports. Si vous souhaitez uniquement utiliser la base de données secondaire pour l’équilibrage de la charge, vous pouvez le créer dans la même région, comme le serveur principal. Création secondaire dans la même région, n’augmente pas la tolérance de l’application aux défaillances irrémédiables.  

Autres scénarios où géo-réplication Active peut être utilisée :

- **Migration de la base de données**: vous pouvez utiliser géo-réplication Active pour migrer une base de données d’un serveur à un autre en ligne avec un temps d’arrêt minimum.
- **Mises à niveau de l’application**: vous pouvez créer un secondaire supplémentaire comme copie basculement back lors de mises à niveau de l’application.

Pour obtenir la continuité d’activité réel, la redondance entre les centres de données de base de données n'est qu’une partie de la solution. Récupération d’une application (service) de bout en bout après qu’une panne grave requiert la récupération de tous les composants qui constituent le service et tous les services dépendants. Le DNS, serveurs web frontaux, de stockage et le logiciel client (par exemple, un navigateur avec un code JavaScript personnalisé) sont des exemples de ces composants. Il est essentiel que tous les composants sont résistants aux mêmes défaillances et deviennent disponibles dans l’objectif de temps de récupération (RTO) de votre application. Par conséquent, vous devez identifier tous les services dépendants et comprendre les garanties et les fonctionnalités qu’ils fournissent. Ensuite, vous devez prendre des mesures adéquates pour que vos fonctions de service pendant le basculement des services dont il dépend. Pour plus d’informations sur la conception de solutions de reprise après sinistre, consultez [Création de Solutions de Cloud de reprise après sinistre à l’aide de Active géo-réplication](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Fonctions de réplication géographique actives
La fonctionnalité de géo-réplication Active fournit les capacités essentielles suivantes :

- **La réplication asynchrone automatique**: vous ne pouvez créer qu’une base de données secondaire en ajoutant à une base de données existante. Le serveur secondaire ne peut pas être créé dans un autre serveur de base de données de SQL Azure. Une fois créée, la base de données secondaire est remplie avec les données copiées à partir de la base de données principale. Ce processus est appelé amorçage. Une fois la base de données secondaire a été créé et alimenté, mises à jour de la base de données principal sont répliquées de manière asynchrone à la base de données secondaire automatiquement. Réplication asynchrone signifie que les transactions sont validées sur la base de données principale avant qu’elles sont répliquées vers la base de données secondaire. 

- **Plusieurs bases de données secondaires**: deux ou plusieurs bases de données secondaires augmentent la redondance et le niveau de protection de la base de données principale et l’application. Si plusieurs bases de données secondaires existent, l’application reste protégée, même si une base de données secondaires échoue. S’il existe uniquement une base de données secondaire, et qu’il tombe en panne, l’application est exposée à des risques plus élevés jusqu'à ce qu’une nouvelle base de données secondaire est créée.

- **Les bases de données secondaires lisibles**: une application peut accéder à une base de données secondaire pour les opérations en lecture seule à l’aide d’entités de sécurité identiques ou différents utilisées pour accéder à la base de données principale. Les bases de données secondaires fonctionnent en mode d’isolation de capture instantanée pour garantir la réplication des mises à jour de l’ordinateur principal (la relecture du journal) n’est pas retardée par des requêtes exécutées sur le serveur secondaire.

>[AZURE.NOTE] La relecture du journal est différée pour la base de données secondaire si le schéma des mises à jour qu’il reçoit du site principal qui nécessitent un verrou de schéma sur la base de données secondaire.

- **Geo-réplication Active des bases de données de pool élastique**: géo-réplication Active peut être configurée pour une base de données dans n’importe quel pool élastique de la base de données. La base de données secondaire peut être dans un autre pool élastique de la base de données. Pour les bases de données régulières, secondaire peut être un versa de pool et inversement élastique de la base de données tant que les niveaux de service sont les mêmes. 

- **Niveau de performance configurables de la base de données secondaire**: une base de données secondaire peut être créé avec un niveau de performances inférieur que la principale. Les bases de données principales et secondaires doivent avoir le même niveau de service. Cette option n’est pas recommandée pour les applications avec l’activité d’écriture de base de données haute car le retard de réplication accrue augmente le risque de perte de données après un basculement. En outre, après le basculement des performances de l’application sont concerné jusqu'à ce que la nouvelle primaire est mis à niveau vers un niveau de performances plus élevé. Le graphique de pourcentage journal e/s sur Azure portal fournit un bon moyen d’estimer le niveau de performances minimal du secondaire qui est nécessaire pour supporter la charge de réplication. Par exemple, si votre base de données principale est P6 (1000 DTU) et son journal % d’e/s est le serveur secondaire doit être au moins de 50 % P4 (500 DTU). Vous pouvez également récupérer les données d’e/s du journal à l’aide des vues de base de données [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) ou [sys.dm_db_resource_stats]( https://msdn.microsoft.com/library/dn800981.aspx) .  Pour plus d’informations sur les niveaux de performances de la base de données SQL, consultez les [performances et les options de base de données SQL](sql-database-service-tiers.md). 

- **Retour arrière et basculement de contrôlées par l’utilisateur**: une base de données secondaire peut explicitement activé pour le rôle principal à tout moment par l’application ou l’utilisateur. Suite à une panne réelle l’option « non planifiée » doit servir, qui promeut immédiatement secondaire pour être le serveur principal. Lorsque le principal défaillant récupère et est à nouveau disponible, le système marque principale récupérée comme secondaire et mettre à jour avec la nouvelle primaire automatiquement. En raison de la nature asynchrone de la réplication, une petite quantité de données peut être perdue durant un basculement non planifié si un serveur principal tombe en panne avant elle réplique les modifications les plus récentes sur le site secondaire. Lorsqu’un serveur principal avec plusieurs zones secondaires échoue, le système a automatiquement reconfigure les relations de réplication et lie les zones secondaires restantes au principal nouvellement promu sans aucune intervention de l’utilisateur. Après que la panne qui a provoqué le basculement est atténuée, il peut être souhaitable pour renvoyer la région principale de l’application. Pour ce faire, la commande de basculement doit être appelée avec l’option « planifiée ». 

- **Conserver les informations d’identification et de synchronisation des règles de pare-feu**: nous vous recommandons d’à l’aide de [règles de pare-feu de base de données](sql-database-firewall-configure.md) de réplication géographique bases de données ainsi, ces règles peut être répliquée avec la base de données pour toutes les bases de données secondaires ont les mêmes règles de pare-feu que le serveur principal. Cette approche élimine la nécessité pour les clients à configurer manuellement et mettre à jour les règles de pare-feu sur les serveurs qui hébergent les bases de données principales et secondaires. De même, à l’aide de [contenus aux utilisateurs de base de données](sql-database-manage-logins.md) pour l’accès aux données permet de bases de données primaires et secondaires ont toujours le même utilisateur les informations d’identification, lors d’un basculement, il n’y a aucune interruption en raison des incompatibilités avec des noms d’accès et des mots de passe. Avec l’ajout [d’Azure Active Directory](../active-directory/active-directory-whatis.md), les clients peuvent gérer l’accès des utilisateurs aux bases de données primaires et secondaires et en éliminant le besoin de gestion des informations d’identification dans totalement de bases de données.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Mise à niveau ou la mise à niveau d’une base de données primaire
Vous pouvez mettre à niveau ou mettre à niveau une base de données primaire à un niveau de performance (dans le même niveau de service) sans déconnecter les bases de données secondaires. Lors de la mise à niveau, nous vous recommandons d’abord de mettre à niveau la base de données secondaire et de mettre à niveau le serveur principal. Lors d’un déclassement, inverser l’ordre : rétrograder le serveur principal de tout d’abord, puis rétrograder le serveur secondaire. 

La base de données secondaire doit être dans le même niveau de service en tant que principal, afin que la migration de votre base de données principale vers un niveau différent de service vous oblige à terminer le lien de réplication géographique et renommez la base de données secondaire ou simplement le faire glisser. Migrer le serveur principal pour le nouveau niveau de service, puis reconfigurer la réplication de la zone géographique. Votre nouvelle secondaire sera créé automatiquement avec le même niveau de performances que le principal par défaut.

## <a name="preventing-the-loss-of-critical-data"></a>Prévenir la perte de données critiques
En raison de la latence élevée de réseaux étendus, copie en continu utilise un mécanisme de réplication asynchrone. Réplication asynchrone permet une perte de données inévitable si une défaillance se produit. Toutefois, certaines applications ne peuvent nécessiter aucune perte de données. Pour protéger ces mises à jour critiques, un développeur d’applications peut appeler la procédure système [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx) immédiatement après la validation de la transaction. L’appel de **sp_wait_for_database_copy_sync** bloque le thread appelant jusqu'à ce que la dernière transaction validée qui a été répliquée dans la base de données secondaire. La procédure attend jusqu'à ce que toutes les transactions en file d’attente ont été reconnues dans la base de données secondaire. **sp_wait_for_database_copy_sync** limitée à un lien spécifique copie en continu. Tout utilisateur possédant les droits de connexion à la base de données principale peut appeler cette procédure.

>[AZURE.NOTE] Le délai provoqué par un appel de procédure **sp_wait_for_database_copy_sync** peut être significatif. Le délai dépend de la taille de la longueur du journal des transactions au moment et que cet appel ne retourne pas jusqu'à ce que la totalité du journal est répliqué. Évitez d’appeler cette procédure, sauf si cela est absolument nécessaire.

## <a name="programmatically-managing-active-geo-replication"></a>Gestion par programme de géo-réplication Active

Comme indiqué précédemment, les géo-réplication Active peut également gérée par programme à l’aide de PowerShell d’Azure et l’API REST. Les tableaux suivants décrivent l’ensemble des commandes disponibles.

- **API du Gestionnaire de ressources Azure et basée sur les rôles de sécurité**: géo-réplication Active inclut un ensemble [d’API du Gestionnaire de ressources Azure]( https://msdn.microsoft.com/library/azure/mt163571.aspx) pour la gestion, y compris les [applets de commande PowerShell de basé sur le Gestionnaire de ressources Azure](sql-database-geo-replication-powershell.md). Ces API nécessite l’utilisation de groupes de ressources et la prise en charge de la sécurité basée sur les rôles (RBAC). Pour plus d’informations sur la façon d’implémenter l’accès aux rôles voir [Contrôle d’accès Azure Role-Based](../active-directory/role-based-access-control-configure.md).

>[AZURE.NOTE] De nombreuses nouvelles fonctionnalités de géo-réplication Active sont uniquement pris en charge à l’aide de [Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md) en fonction [API REST de SQL Azure](https://msdn.microsoft.com/library/azure/mt163571.aspx) et les [applets de commande PowerShell de base de données SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx). L’API REST (classique)] (https://msdn.microsoft.com/library/azure/dn505719.aspx) et les [applets de commande de base de données de SQL Azure (classique)](https://msdn.microsoft.com/library/azure/dn546723.aspx) sont pris en charge pour la compatibilité descendante à l’aide de l’API Gestionnaire de ressources Azure sont recommandés. 


### <a name="transact-sql"></a>Transact-SQL

|Commande|Description|
|-------|-----------|
|[MODIFIER la base de données (base de données SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx)|Argument d’ajouter un serveur secondaire ON permet de créer une base de données secondaire pour une base de données existante et le démarre la réplication des données|
|[MODIFIER la base de données (base de données SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx)|Permet de passer d’une base de données secondaire pour être primaire pour déclencher le basculement basculement ou FORCE_FAILOVER_ALLOW_DATA_LOSS
|[MODIFIER la base de données (base de données SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx)|Permet de supprimer le serveur secondaire sur Mettre fin à une réplication de données entre une base de données SQL et la base de données secondaire spécifié.|
|[Sys.geo_replication_links (de base de données SQL Azure)](https://msdn.microsoft.com/library/mt575501.aspx)|Renvoie des informations sur tous les liens de réplication existants pour chaque base de données sur le serveur logique de base de données de SQL Azure.|
|[Sys.dm_geo_replication_link_status (de base de données SQL Azure)](https://msdn.microsoft.com/library/mt575504.aspx)|Obtient l’heure de la dernière réplication, dernier décalage de réplication et d’autres informations sur le lien de réplication pour une base de données SQL.|
|[Sys.dm_operation_status (de base de données SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx)|Affiche l’état de toutes les opérations de base de données, y compris de l’état des liens de réplication.|
|[sp_wait_for_database_copy_sync (de base de données SQL Azure)](https://msdn.microsoft.com/library/dn467644.aspx)|force l’application à attendre jusqu'à ce que toutes les transactions validées sont répliquées et reconnues par la base de données secondaire active.|
||||

### <a name="powershell"></a>PowerShell

|Applet de commande|Description|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx)|Obtient une ou plusieurs bases de données.|
|[Nouvelle-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx)|Crée une base de données secondaire pour une base de données existante et démarre la réplication de données.|
|[Ensemble-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/en-us/library/mt619393.aspx)|Une base de données secondaire pour être primaire pour déclencher le basculement des commutateurs.|
|[Supprimer-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/en-us/library/mt603457.aspx)|Met fin à la réplication des données entre une base de données SQL et la base de données secondaire spécifié.|
|[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx)|Obtient les liaisons de géo-réplication entre une base de données de SQL Azure et un groupe de ressources ou de SQL Server.|
||||

### <a name="rest-api"></a>API REST

|API|Description|
|---|-----------|
|[Création ou mise à jour de base de données (mode de création = restauration)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Crée ou met à jour restaure un principal ou une base de données secondaire.|
|[Obtenir de créer ou de mettre à jour le statut de la base de données](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Retourne l’état au cours d’une opération de création.|
|[Base de données secondaire à définir comme principale (basculement planifié)](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)|Promouvoir une base de données secondaire dans un partenariat de réplication géographique pour devenir la nouvelle base de données principale.|
|[Base de données secondaire à définir comme principale (basculement non planifié)](https://msdn.microsoft.com/library/azure/mt582027.aspx)|Pour forcer un basculement de la base de données secondaire et définir l’image secondaire en tant que principal.|
|[Obtenir des liens de réplication](https://msdn.microsoft.com/library/azure/mt600929.aspx)|Obtient tous les liens de réplication pour une base de données SQL dans un partenariat de réplication géographique. Il récupère les informations visibles dans la vue de catalogue sys.geo_replication_links.|
|[Obtenir le lien de réplication](https://msdn.microsoft.com/library/azure/mt600778.aspx)|Obtient un lien de réplication spécifique pour une base de données SQL dans un partenariat de réplication géographique. Il récupère les informations visibles dans la vue de catalogue sys.geo_replication_links.|
||||



## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la continuité de l’activité commerciale et scénarios, consultez [vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)
- Pour obtenir des informations sur les sauvegardes de base de données SQL Azure automatisé, consultez [SQL de base de données des sauvegardes automatisées](sql-database-automated-backups.md).
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [restauration d’une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md).
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, voir [copie de la base de données](sql-database-copy.md).
- Pour en savoir plus sur les conditions d’authentification requises pour un nouveau serveur principal et la base de données, consultez [sécurité de base de données SQL après un sinistre](sql-database-geo-replication-security-config.md).
