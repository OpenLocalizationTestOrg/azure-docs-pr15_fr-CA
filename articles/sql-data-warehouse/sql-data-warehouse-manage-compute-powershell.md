<properties
   pageTitle="Gérer la puissance de calcul dans l’entrepôt de données SQL Azure (PowerShell) | Microsoft Azure"
   description="Pour gérer les tâches de PowerShell grande puissance de traitement. Échelle des ressources de calcul en ajustant les DWUs. Ou bien, suspendre et reprendre les ressources pour réduire les coûts de calcul."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/13/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>Gérer la puissance de calcul dans l’entrepôt de données SQL Azure (PowerShell)

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-data-warehouse-manage-compute-overview.md)
- [Portail](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTE](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Adapter les performances en faisant évoluer calculer des ressources et mémoire pour répondre aux besoins changeants de votre charge de travail. Économies en ressources d’arrière-plan mise à l’échelle pendant les heures creuses ou compute suspension complètement. 

Cet ensemble de tâches utilise le portail Azure pour :

- Calcul de l’échelle
- Calcul de pause
- Calcul de la reprise

Pour plus d’informations, consultez [Gérer compute vue d’ensemble][].


## <a name="before-you-begin"></a>Avant de commencer

### <a name="install-the-latest-version-of-azure-powershell"></a>Installez la dernière version de PowerShell d’Azure

> [AZURE.NOTE]  Pour utiliser PowerShell d’Azure avec SQL Data Warehouse, vous devez Azure PowerShell version 1.0.3 ou supérieur.  Pour vérifier votre version actuelle, exécutée la commande **Get-Module - ListAvailable-nom Azure**. Vous pouvez installer la version la plus récente de [Microsoft Web Platform Installer][].  Pour plus d’informations, consultez [comment installer et configurer Azure PowerShell][].

### <a name="get-started-with-azure-powershell-cmdlets"></a>Commencer à utiliser des applets de commande PowerShell de Azure

Mise en route :

1. Ouvrez PowerShell Azure. 
2. À l’invite de PowerShell, exécutez ces commandes pour vous connecter au Gestionnaire de ressources Azure et sélectionnez votre abonnement.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Puissance de calcul d’échelle

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Pour modifier les DWUs, utilisez l’applet de commande PowerShell [Set-AzureRmSqlDatabase][] . L’exemple suivant définit l’objectif de niveau de service à la DW1000 de la base de données MySQLDW, qui est hébergé sur le serveur MyServer. 

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Calcul de pause

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Pour suspendre une base de données, utilisez la cmdlet [Suspend-AzureRmSqlDatabase][] . L’exemple suivant suspend une base de données nommée base de données02 hébergé sur un serveur nommé serveur01. Le serveur est dans un groupe de ressources Azure nommé ResourceGroup1. 

> [AZURE.NOTE] Notez que si votre serveur est foo.database.windows.net, utilisez « foo » comme le nom du serveur - dans les applets de commande PowerShell.

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
Une variante, l’exemple suivant récupère la base de données dans l’objet $database. Puis, il transfère l’objet de [Suspension-AzureRmSqlDatabase][]. Les résultats sont stockés dans l’objet resultDatabase. La commande finale montre les résultats.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Calcul de la reprise

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Pour démarrer une base de données, utilisez la cmdlet [Resume-AzureRmSqlDatabase][] . L’exemple suivant démarre une base de données nommée base de données02 hébergé sur un serveur nommé serveur01. Le serveur est dans un groupe de ressources Azure nommé ResourceGroup1. 

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

Une variante, l’exemple suivant récupère la base de données dans l’objet $database. Il tuyaux de l’objet à [Reprendre-AzureRmSqlDatabase][] et stocke les résultats dans $resultDatabase. La commande finale montre les résultats.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Étapes suivantes

Pour d’autres tâches de gestion, consultez la [vue d’ensemble de la gestion][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Vue d’ensemble de la gestion]: ./sql-data-warehouse-overview-manage.md
[Comment faire pour installer et configurer Azure PowerShell]: ./powershell-install-configure.md
[Gérer la vue d’ensemble du calcul]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspension-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Ensemble-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/
