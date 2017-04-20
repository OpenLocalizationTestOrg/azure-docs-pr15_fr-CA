<properties
   pageTitle="Applets de commande PowerShell pour l’entrepôt de données SQL Azure"
   description="Rechercher les applets de commande PowerShell supérieure pour l’entrepôt de données de SQL Azure notamment comment suspendre et reprendre une base de données."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess;mausher"/>

# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Applets de commande PowerShell et le reste des API pour l’entrepôt de données SQL

De nombreuses tâches d’administration SQL Data Warehouse peuvent être gérés à l’aide des applets de commande PowerShell d’Azure, ou autres API.  Voici quelques exemples d’utilisation des commandes PowerShell pour automatiser les tâches courantes dans votre magasin de données SQL.  Pour obtenir des exemples reste bonne, voir [l’évolutivité de gérer avec le reste][]de l’article.

> [AZURE.NOTE]  Pour utiliser PowerShell d’Azure avec SQL Data Warehouse, vous devez Azure PowerShell version 1.0.3 ou supérieur.  Vous pouvez vérifier votre version en exécutant **Get-Module - ListAvailable-nom Azure**.  La version la plus récente peut être installée à partir de [Microsoft Web Platform Installer][].  Pour plus d’informations sur l’installation de la version la plus récente, consultez [comment installer et configurer Azure PowerShell][].

## <a name="get-started-with-azure-powershell-cmdlets"></a>Commencer à utiliser des applets de commande PowerShell de Azure

1. Ouvrez Windows PowerShell. 
2. À l’invite de PowerShell, exécutez ces commandes pour vous connecter au Gestionnaire de ressources Azure et sélectionnez votre abonnement.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Exemple d’entrepôt de données suspendre SQL

Suspendre une base de données nommée « base » de données02 hébergé sur un serveur nommé « Server01 ».  Le serveur est dans un groupe de ressources Azure nommé « ResourceGroup1 ». 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Une variation, cet exemple pipes l’objet récupéré pour [Suspension-AzureRmSqlDatabase][].  Par conséquent, la base de données est suspendue. La commande finale montre les résultats.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Démarrez l’exemple d’entrepôt de données SQL

Opération de reprise d’une base de données nommée « base » de données02 hébergé sur un serveur nommé « Server01 ». Le serveur se trouve dans un groupe de ressources nommé « ResourceGroup1 ».

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Une variation, cet exemple récupère une base de données nommée base de « données02 » à partir d’un serveur nommé serveur01 « » qui est contenue dans un groupe de ressources nommé « ResourceGroup1 ». Il transfère l’objet récupéré pour [Reprendre-AzureRmSqlDatabase][].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [AZURE.NOTE] Notez que si votre serveur est foo.database.windows.net, utilisez « foo » comme le nom du serveur - dans les applets de commande PowerShell.

## <a name="frequently-used-powershell-cmdlets"></a>Applets de commande PowerShell d’utilisés fréquemment

Ces applets de commande PowerShell sont souvent utilisés avec l’entrepôt de données SQL Azure.

- [Get-AzureRmSqlDatabase][]
- [Get-AzureRmSqlDeletedDatabaseBackup][]
- [Get-AzureRmSqlDatabaseRestorePoints][]
- [Nouvelle-AzureRmSqlDatabase][]
- [Supprimer-AzureRmSqlDatabase][]
- [Restauration-AzureRmSqlDatabase][] 
- [Resume-AzureRmSqlDatabase][]
- [Sélectionnez-AzureRmSubscription][]
- [Ensemble-AzureRmSqlDatabase][]
- [Suspension-AzureRmSqlDatabase][]

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir d’autres exemples de PowerShell, voir :

- [Créez un entrepôt de données SQL à l’aide de PowerShell][]
- [Restauration de la base de données][]

Pour obtenir une liste de toutes les tâches qui peuvent être automatisées avec PowerShell, voir les [Applets de commande de base de données de SQL Azure][].  Pour obtenir la liste des tâches qui peuvent être automatisées avec reste, consultez [opérations de bases de données SQL Azure][].

<!--Image references-->

<!--Article references-->
[Comment faire pour installer et configurer Azure PowerShell]: ./powershell-install-configure.md
[Créez un entrepôt de données SQL à l’aide de PowerShell]: ./sql-data-warehouse-get-started-provision-powershell.md
[Restauration de la base de données]: ./sql-data-warehouse-restore-database-powershell.md
[Gérer l’évolutivité avec le reste]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Applets de commande de base de données SQL Azure]: https://msdn.microsoft.com/library/mt574084.aspx
[Opérations de bases de données SQL Azure]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[Nouvelle-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Supprimer-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restauration-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Sélectionnez-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Ensemble-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspension-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
