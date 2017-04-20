<properties 
    pageTitle="Modifier le niveau de performances et le niveau de service d’une base de données Azure SQL à l’aide de PowerShell | Microsoft Azure" 
    description="Modifier le niveau de service et le niveau de performances d’une base de données Azure SQL indique comment mettre à l’échelle votre base de données SQL vers le haut ou vers le bas avec PowerShell. Modification du niveau de tarification d’une base de données Azure SQL avec PowerShell." 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-with-powershell"></a>Modifier le niveau du service niveau et les performances (niveau de tarification) d’une base de données SQL avec PowerShell


> [AZURE.SELECTOR]
- [Azure portal](sql-database-scale-up.md)
- [**PowerShell**](sql-database-scale-up-powershell.md)


Les niveaux de service et de performances décrivent les fonctionnalités et les ressources disponibles pour votre base de données SQL et peuvent être mis à jour selon les besoins de votre application. Pour plus d’informations, consultez [Niveaux de Service](sql-database-service-tiers.md).

Notez que la modification de la couche de service et/ou le niveau de performances d’une base de données crée une réplique de la base de données d’origine dans le nouveau niveau de performances et puis de passer les connexions au réplica. Pendant la brève intervention lorsque nous basculer vers le réplica, les connexions à la base de données sont désactivées, et certaines opérations en vol peuvent être annulées, mais aucune donnée n’est perdue au cours de ce processus. Cette fenêtre varie, mais est en moyenne moins de 4 secondes et dans plus de 99 % des cas est de moins de 30 secondes. Très rarement, en particulier si grand nombre de transactions à la volée sur les connexions du moment est désactivé, cette fenêtre peut être plus longue.  

La durée de la totalité du processus l’extrapolation dépend de la couche service et taille de la base de données avant et après la modification. Par exemple, une base de données de 250 Go est la modification de vers, d’ou au sein d’un niveau de service Standard, doit se terminer dans les 6 heures. Pour une base de données de la même taille que la modification des niveaux de performances dans le niveau de service Premium est, il doit se terminer dans les 3 heures.


- Pour mettre à niveau une base de données, la base de données doit être inférieure à la taille maximale autorisée de la couche de service cible. 
- Lors de la mise à niveau d’une base de données avec [Réplication géographique](sql-database-geo-replication-portal.md) est activée, vous devez d’abord passer ses bases de données secondaires vers le niveau de performance souhaité avant la mise à niveau de la base de données principale.
- Lors de la mise à niveau à partir d’un niveau de service Premium, vous devez tout d’abord terminer toutes les relations de réplication géographique. Vous pouvez suivre les étapes décrites dans la rubrique [restaurer en cas de panne](sql-database-disaster-recovery.md) pour arrêter le processus de réplication entre le serveur principal et les bases de données secondaires actives.
- Les offres de service de restauration sont différentes pour les divers niveaux de service. Si la mise à niveau vous peuvent perdre la possibilité de restaurer à un point dans le temps, ou ont une période de rétention de sauvegarde inférieure. Pour plus d’informations, voir [restauration et sauvegarde de base de données SQL Azure](sql-database-business-continuity.md).
- Les nouvelles propriétés de la base de données ne sont pas appliquées tant que les modifications sont terminées.



**Pour terminer cet article, vous devez les éléments suivants :**

- Un abonnement Azure. Si vous avez besoin d’un abonnement Azure simplement cliquez sur **Compte gratuit** en haut de cette page et puis de revenir à la fin de cet article.
- Une base de données SQL d’Azure. Si vous ne disposez pas d’une base de données SQL, créez-en un en suivant les étapes décrites dans cet article : [créer votre première base de données de SQL Azure](sql-database-get-started.md).
- PowerShell Azure.


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]



## <a name="change-the-service-tier-and-performance-level-of-your-sql-database"></a>Modifier le niveau de performances et de la couche de service de votre base de données SQL

Exécutez [Set-AzureRmSqlDatabase] (applet de commande https://msdn.microsoft.com/library/azure/mt619433(v=azure.300\).aspx) et définir le **RequestedServiceObjectiveName -** pour le niveau de performance de la couche de tarification souhaité ; par exemple *S0*, *S1*, *S2*, *S3*, *P1*, *P2*,...

```
$ResourceGroupName = "resourceGroupName"
    
$ServerName = "serverName"
$DatabaseName = "databaseName"

$NewEdition = "Standard"
$NewPricingTier = "S2"

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```

  

   


## <a name="sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database"></a>Exemple de script PowerShell pour modifier le niveau de performances et de la couche de service de votre base de données SQL

Remplacer ```{variables}``` avec vos valeurs (n’incluez pas les accolades).

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"
    
$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"
    
$ServerName = "{server}"
$DatabaseName = "{database}"
    
$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"
    
Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId
    
Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```
        


## <a name="next-steps"></a>Étapes suivantes

- [Mettre à l’échelle et dans](sql-database-elastic-scale-get-started.md)
- [Se connecter et d’interroger une base de données SQL avec SSMS](sql-database-connect-query-ssms.md)
- [Exportation d’une base de données SQL d’Azure](sql-database-export-powershell.md)

## <a name="additional-resources"></a>Ressources supplémentaires

- [Vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)
- [Documentation de la base de données de SQL](http://azure.microsoft.com/documentation/services/sql-database/)
- [Applets de commande de base de données SQL azure] (http://msdn.microsoft.com/library/azure/mt574084https  :/ / msdn.microsoft.com/bibliothèque/azure/mt619433(v=azure.300\).aspx.aspx)