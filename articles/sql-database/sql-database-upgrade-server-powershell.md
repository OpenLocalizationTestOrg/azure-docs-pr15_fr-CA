<properties
    pageTitle="Mise à niveau V12 de base de données SQL Azure à l’aide de PowerShell | Microsoft Azure"
    description="Explique comment mettre à niveau vers V12 de base de données SQL Azure, y compris la mise à niveau des bases de données Web et de l’entreprise et comment mettre à niveau un serveur V11 migrer ses bases de données directement dans un pool d’élasticité de la base de données à l’aide de PowerShell."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="sstein"/>

# <a name="upgrade-to-azure-sql-database-v12-using-powershell"></a>Mise à niveau V12 de base de données SQL Azure à l’aide de PowerShell


> [AZURE.SELECTOR]
- [Azure portal](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


V12 de base de données SQL est la version la plus récente mise à niveau vers V12 de base de données SQL est recommandé.
V12 de base de données SQL a notamment de nombreux [avantages par rapport à la version précédente](sql-database-v12-whats-new.md) :

- Compatibilité accrue avec SQL Server.
- Les performances améliorées de prime et nouveaux niveaux de performances.
- [Les pools élastique de la base de données](sql-database-elastic-pool.md).

Cet article fournit des instructions relatives à la mise à niveau de bases de données et les serveurs SQL de base de données V11 existants vers V12 de base de données SQL.

Au cours du processus de mise à niveau vers V12, mise à niveau des bases de données Web et de l’entreprise à un nouveau niveau de service afin que les instructions de mise à niveau des bases de données Web et de l’entreprise sont incluses.

En outre, la migration vers un [pool d’élastique de la base de données](sql-database-elastic-pool.md) peut être plus efficace que la mise à niveau vers des niveaux de performances individuelles (tarification niveaux) pour les bases de données unique. Pools simplifient également la gestion de la base de données car vous n’avez besoin gérer les paramètres de performances pour le pool, plutôt que de gestion distincte des niveaux de performances des bases de données. Si vous avez des bases de données sur plusieurs serveurs, envisagez de les déplacer dans le même serveur et en tirant profit de les placer dans un pool.

Vous pouvez suivre les étapes décrites dans cet article afin de migrer facilement des bases de données à partir de serveurs de V11 directement dans des pools d’élasticité de la base de données.

Notez que vos bases de données va rester en ligne et continuer à travailler pendant toute l’opération de mise à niveau. Au moment de la transition réelle pour le nouveau niveau de performances temporaire de suppression des connexions à la base de données peut se produire pour une durée de très petite taille qui est généralement environ 90 secondes, mais peut être autant que 5 minutes. Si votre application a les [pannes transitoires pour les interruptions de connexion](sql-database-connectivity-issues.md) qu’il est suffisant pour se protéger contre les pertes de connexion à la fin de la mise à niveau.

Mise à niveau vers V12 de base de données SQL ne peut pas être annulée. Après une mise à niveau, le serveur ne peut pas être annulée en V11.

Après la mise à niveau à V12, [recommandations de niveau de service](sql-database-service-tier-advisor.md) et les [recommandations de pools élastique](sql-database-elastic-pool-create-portal.md) pas seront immédiatement disponibles jusqu'à ce que le service de temps pour évaluer vos charges de travail sur le nouveau serveur. Historique de recommandation de serveur V11 ne s’applique pas aux serveurs de V12 afin qu’il n’est pas conservé.  

## <a name="prepare-to-upgrade"></a>Préparer la mise à niveau

- **Mise à niveau de toutes les bases de données Web et de l’entreprise**: utiliser le portail, ou utiliser [PowerShell pour mettre à niveau les bases de données et le serveur](sql-database-upgrade-server-powershell.md).
- **Examen et suspendre la réplication géo**: Si votre base de données Azure SQL est configuré pour la réplication de la zone géographique, vous devez documenter sa configuration actuelle et [Arrêter la réplication de la zone géographique](sql-database-geo-replication-portal.md#remove-secondary-database). Une fois la mise à niveau terminée reconfigurer votre base de données pour la réplication de la zone géographique.
- **Ouverture de ces ports si vous avez des clients sur un ordinateur virtuel d’Azure**: Si votre programme client se connecte à SQL de base de données V12 alors que votre client s’exécute sur une machine virtuelle Azure (VM), vous devez ouvrir des plages de ports 11999-11000 et 14000-14999 sur l’ordinateur virtuel. Pour plus d’informations, reportez-vous à la section [Ports pour V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


## <a name="prerequisites"></a>Conditions préalables

Pour mettre à niveau un serveur V12 avec PowerShell, vous devez disposer de la dernière PowerShell Azure installé et en cours d’exécution. Pour plus d’informations, consultez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).


## <a name="configure-your-credentials-and-select-your-subscription"></a>Configurer vos informations d’identification, puis sélectionnez votre abonnement

Pour exécuter les applets de commande PowerShell sur votre abonnement Azure vous devez d’abord établir l’accès à votre compte Azure. Exécutez la commande suivante et vous obtenez un écran de connexion pour entrer vos informations d’identification. Utilisez le même e-mail et mot de passe que vous utilisez pour vous connecter au portail Azure.

    Add-AzureRmAccount

Une fois connecté avec succès, vous devez voir des informations sur écran qui inclut l’Id vous connecté et les abonnements Azure, à que vous avez accès.

Pour sélectionner l’abonnement que vous souhaitez travailler avec vous avez besoin de votre Id (**-SubscriptionId**) de l’abonnement ou l’abonnement nom (**-SubscriptionName**). Vous pouvez le copier à partir de l’étape précédente, ou si vous disposez de plusieurs abonnements, vous pouvez exécuter l’applet de commande **Get-AzureRmSubscription** et copier les informations d’abonnement de votre choix à partir du jeu de résultats.

Avec vos informations d’abonnement pour définir votre abonnement en cours, exécutez l’applet de commande suivante :

    Set-AzureRmContext -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Les commandes suivantes seront exécutées sur l’abonnement que vous avez sélectionnées juste au-dessus.

## <a name="get-recommendations"></a>Obtenir des recommandations

Pour obtenir la recommandation pour la mise à niveau de serveur exécuter l’applet de commande suivante :

    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1”

Pour plus d’informations, consultez [créer un pool de la base de données élastique](sql-database-elastic-pool-create-portal.md) et [recommandations de la couche de base de données de SQL Azure tarification](sql-database-service-tier-advisor.md).



## <a name="start-the-upgrade"></a>Démarrer la mise à niveau

Pour démarrer la mise à niveau du serveur, exécutez l’applet de commande suivante :

    Start-AzureRmSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Lorsque vous exécutez le processus de mise à niveau de cette commande commence. Vous pouvez personnaliser la sortie de la recommandation et fournir la recommandation modifiée à cette applet de commande.


## <a name="upgrade-a-server"></a>Mise à niveau d’un serveur


    # Adding the account
    #
    Add-AzureRmAccount

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION'
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP'
    $ServerName = 'YOUR_SERVER'

    # Selecting the right subscription
    #
    Set-AzureRmContext -SubscriptionName $SubscriptionName

    # Getting the upgrade recommendations
    #
    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName

    # Starting the upgrade process
    #
    Start-AzureRmSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## <a name="custom-upgrade-mapping"></a>Mappage de la mise à niveau personnalisée

Si les recommandations ne sont pas appropriées pour votre serveur et l’étude de cas, vous pouvez choisir comment vos bases de données sont mis à niveau et les mapper aux bases de données simples ou élastiques.

ElasticPoolCollection et sur DatabaseCollection sont facultatives :

    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100
    $elasticPool.DatabaseDtuMin = 0
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”)
    $elasticPool.Name = "elasticpool_1"
    $elasticPool.StorageMb = 800

    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap1.Name = "DBMain1"
    $databaseMap1.TargetEdition = "Standard"
    $databaseMap1.TargetServiceLevelObjective = "S0"

    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap2.Name = "DBMain2"
    $databaseMap2.TargetEdition = "Standard"
    $databaseMap2.TargetServiceLevelObjective = "S2"

    # Starting the upgrade
    #
    Start-AzureRmSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -ServerVersion 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool)



## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>Surveiller les bases de données après la mise à niveau vers V12 de base de données SQL


Après la mise à niveau, il est recommandé de surveiller la base de données active pour assurer les applications sont en cours d’exécution lors de l’exécution de votre choix et optimiser l’utilisation, le cas échéant.

En outre, à la surveillance des bases de données vous pouvez surveiller des pools élastique de la base de données [à l’aide du portail](sql-database-elastic-pool-manage-portal.md) ou avec [PowerShell](sql-database-elastic-pool-manage-powershell.md)


**Données de consommation de ressources :** Basique, Standard et Premium, des bases de données de consommation de ressources sont disponibles par le biais de la [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV dans la base de données de l’utilisateur. Cette vue offre près des informations de consommation des ressources en temps réel à 15 deuxième niveau de granularité pour l’heure précédente de l’opération. La consommation de pourcentage DTU pour un intervalle est calculée comme la consommation de pourcentage maximal des dimensions UC, d’e/s et le journal. Voici une requête pour calculer la consommation de pourcentage DTU moyenne sur la dernière heure :

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Informations supplémentaires :

- [Guide de performances de base de données de SQL azure des bases de données unique](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Considérations de prix et de performances pour un pool d’élasticité de la base de données](sql-database-elastic-pool-guidance.md).
- [Surveillance de base de données SQL Azure à l’aide des vues de gestion dynamique](sql-database-monitoring-with-dmvs.md)



**Alertes :** Configurer « Alertes » dans le portail Azure afin de vous avertir lorsque certain niveau élevé est proche de la consommation de DTU pour une base de données mis à niveau. Alertes de la base de données permet de configurer dans Azure portal pour diverses métriques de performances telles que DTU, UC, d’e/s et journal. Recherchez votre base de données, puis sélectionnez les **règles d’alerte** de la lame de **paramètres** .

Par exemple, vous pouvez définir jusqu'à un message d’alerte sur le « Pourcentage de DTU » si la valeur de pourcentage DTU moyenne est supérieure à 75 % sur au cours des 5 dernières minutes. Pour en savoir plus sur la configuration des notifications d’alerte, reportez-vous à [recevoir des notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) .



## <a name="next-steps"></a>Étapes suivantes

- [Créer un pool d’élasticité de la base de données](sql-database-elastic-pool-create-portal.md) et ajouter tout ou partie des bases de données dans le pool.
- [Modifier le niveau de performances et de la couche de service de votre base de données](sql-database-scale-up.md).



## <a name="related-information"></a>Informations connexes

- [Get-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Démarrer-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)
