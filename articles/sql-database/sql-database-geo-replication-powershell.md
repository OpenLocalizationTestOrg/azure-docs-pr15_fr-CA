<properties 
    pageTitle="Configurer géo-réplication Active pour la base de données de SQL Azure à l’aide de PowerShell | Microsoft Azure" 
    description="Configurer géo-réplication Active pour la base de données de SQL Azure à l’aide de PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
   ms.workload="NA"
    ms.date="07/14/2016"
    ms.author="sstein"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-powershell"></a>Configurer la géo-réplication de base de données SQL Azure avec PowerShell

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-geo-replication-overview.md)
- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

Cet article vous indique comment configurer Active géo-réplication de base de données SQL avec PowerShell.

Pour déclencher le basculement à l’aide de PowerShell, consultez [initier un basculement planifié ou de base de données de SQL Azure avec PowerShell](sql-database-geo-replication-failover-powershell.md).

>[AZURE.NOTE] Geo-réplication Active (lisibles secondaires) est maintenant disponible pour toutes les bases de données de tous les niveaux de service. En avril 2017 est déclassé le type secondaire non lisible et bases de données non lisible passeront automatiquement à secondaires lisibles.



Pour configurer la réplication-Geo Active à l’aide de PowerShell, vous devez disposer :

- Un abonnement Azure. 
- Une base de données Azure SQL - la base de données principale que vous souhaitez répliquer.
- PowerShell Azure 1.0 ou version ultérieure. Vous pouvez télécharger et installer les modules Azure PowerShell suivante [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).


## <a name="configure-your-credentials-and-select-your-subscription"></a>Configurer vos informations d’identification, puis sélectionnez votre abonnement

Tout d’abord, vous devez établir l’accès à votre compte Azure donc démarrer PowerShell et ensuite exécuter l’applet de commande suivant. Dans l’écran de connexion, entrez le même e-mail et mot de passe que vous utilisez pour vous connecter au portail Azure.


    Login-AzureRmAccount

Une fois connecté avec succès, vous verrez certaines informations à l’écran qui inclut l’Id vous connecté et les abonnements Azure, à que vous avez accès.


### <a name="select-your-azure-subscription"></a>Sélectionnez votre abonnement Azure

Pour sélectionner l’abonnement vous avez besoin de votre ID d’abonnement Vous pouvez copier l’Id de l’abonnement à partir des informations affichées à partir de l’étape précédente, ou si vous disposez de plusieurs abonnements et que vous avez besoin de plus de détails vous pouvez exécuter l’applet de commande **Get-AzureRmSubscription** et copier les informations d’abonnement de votre choix à partir du jeu de résultats. L’applet de commande suivante utilise l’Id de l’abonnement pour définir l’abonnement actuel :

    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Après avoir correctement exécuté **Sélectionnez-AzureRmSubscription** vous êtes renvoyé à PowerShell invite.


## <a name="add-secondary-database"></a>Ajouter une base de données secondaire


Les étapes suivantes créent une nouvelle base de données secondaire dans un partenariat de réplication géographique.  
  
Pour activer un secondaire, vous devez être le propriétaire de l’abonnement ou le copropriétaire. 

Vous pouvez utiliser l’applet de commande **New-AzureRmSqlDatabaseSecondary** pour ajouter une base de données secondaire sur un serveur partenaire pour une base de données locale sur le serveur auquel vous êtes connecté (la base de données principale). 

Cette applet de commande remplace le **AzureSqlDatabaseCopy-démarrer** avec le paramètre **-IsContinuous** .  Elle est accompagnée d’un objet **AzureRmSqlDatabaseSecondary** utilisables par d’autres cmdlets pour identifier clairement un lien de réplication spécifique. Cette applet de commande renvoie lorsque la base de données secondaire est créée et totalement amorcée. Selon la taille de la base de données peut prendre de quelques minutes à quelques heures.

La base de données répliquée sur le serveur secondaire aura le même nom que la base de données sur le serveur principal et aura, par défaut, le même niveau de service. La base de données secondaire peut être lisible ou illisible et peut être une seule base de données ou une base de données élastique. Pour plus d’informations, consultez [New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) et [Niveaux de Service](sql-database-service-tiers.md).
Une fois l’image secondaire est créée et amorcé, données commencera la réplication à partir de la base de données principale vers la nouvelle base de données secondaire. Les étapes ci-dessous décrivent comment effectuer cette tâche à l’aide de PowerShell pour créer secondaires non lisible et lisibles, avec une seule base de données ou une base de données élastique.

Si la base de données de partenaire existe déjà (par exemple - à la suite de terminaison d’une relation géo-réplication précédente), la commande échouera.



### <a name="add-a-non-readable-secondary-single-database"></a>Ajouter un secondaire non lisible (base de données unique)

La commande suivante crée un secondaire non lisible de la base de données « mydb » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "No"



### <a name="add-readable-secondary-single-database"></a>Ajouter lisible secondaire (base de données unique)

La commande suivante crée un secondaire lisible de la base de données « mydb » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### <a name="add-a-non-readable-secondary-elastic-database"></a>Ajouter un secondaire non lisible (élastique de base de données)

La commande suivante crée le secondaire non lisible de la base de données « mydb » dans le pool élastique de la base de données nommé « ElasticPool1 » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### <a name="add-a-readable-secondary-elastic-database"></a>Ajouter un secondaire lisible (élastique de base de données)

La commande suivante crée le secondaire de la base de données « mydb » lisible dans le pool élastique de la base de données nommé « ElasticPool1 » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## <a name="remove-secondary-database"></a>Supprimer la base de données secondaire

Utilisez l’applet de commande **Remove-AzureRmSqlDatabaseSecondary** à définitivement mettre fin au partenariat de réplication entre une base de données secondaire et principal. Après la cessation de la relation, la base de données secondaire devient une base de données en lecture-écriture. Si la connectivité à la base de données secondaire est interrompue la commande a réussi, mais l’image secondaire devient en lecture-écriture lorsque la connectivité est restaurée. Pour plus d’informations, consultez [Supprimer-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) et [Niveaux de Service](sql-database-service-tiers.md).

Cette applet de commande remplace Stop-AzureSqlDatabaseCopy pour la réplication. 

Cette suppression est l’équivalent d’un arrêt forcé qui supprime le lien de réplication et de laisser l’ancien secondaire sous la forme d’une base de données autonome qui n’est pas entièrement répliqué avant son arrêt. Toutes les données de lien seront nettoyées sur le serveur primaire antérieur et ancienne secondaire, si ou lorsqu’ils sont disponibles. Cette applet de commande renvoie lorsque le lien de réplication est supprimé. 


Pour supprimer le secondaire, les utilisateurs doivent avoir accès en écriture aux bases de données principales et secondaires RBAC. Voir contrôle d’accès basé sur les rôles pour plus de détails.

L’exemple suivant supprime le lien de réplication de base de données nommée « mydb » sur le serveur « srv2 » de la ressource groupe « rg2 ». 

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## <a name="monitor-geo-replication-configuration-and-health"></a>Surveiller la santé et la configuration de la réplication de la zone géographique

Tâches de surveillance comprennent la surveillance de la configuration de réplication géographique et la surveillance de l’état de la réplication de données.  

[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) peut être utilisé pour récupérer les informations sur les liens de réplication directe visibles dans la vue de catalogue sys.geo_replication_links.

La commande suivante récupère l’état de la liaison de réplication entre le « mydb » de la base de données primaire et secondaire sur le serveur « srv2 » de la ressource groupe « rg2 ».

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur géo-réplication Active, consultez - [Géo-réplication](sql-database-geo-replication-overview.md) Active
- Pour une vue d’ensemble de la continuité de l’activité commerciale et scénarios, consultez [vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)

