<properties
   pageTitle="Gérer la puissance de calcul dans des entrepôts de données SQL (reste) de Azure | Microsoft Azure"
   description="Tâches (T-SQL) de Transact-SQL pour des performances évolutives en ajustant les DWUs. Réduire les coûts par la mise à l’échelle pendant les heures creuses."
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
   ms.date="08/08/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>Gérer la puissance de calcul dans l’entrepôt de données SQL Azure (T-SQL)

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-data-warehouse-manage-compute-overview.md)
- [Portail](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTE](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Adapter les performances en faisant évoluer calculer des ressources et mémoire pour répondre aux besoins changeants de votre charge de travail. Économies en ressources d’arrière-plan mise à l’échelle pendant les heures creuses ou compute suspension complètement. 

Cet ensemble de tâches utilise T-SQL pour :

- Afficher les paramètres en cours de DWU
- Modification des ressources de calcul en ajustant DWUs

Pour suspendre ou reprendre une base de données, choisissez une des autres options de plate-forme en haut de cet article.

Pour plus d’informations, consultez [vue d’ensemble de la puissance de calcul de gérer][].

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>Afficher les paramètres en cours de DWU

Pour afficher les paramètres actuels de la DWU de vos bases de données :

1. Ouvrez l’Explorateur d’objets SQL Server dans Visual Studio 2015.
2. Se connecter à la base de données maître associé avec le serveur SQL de base de données logique.
2. Sélectionnez dans la vue de gestion dynamique sys.database_service_objectives. Voici un exemple : 

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Calcul de l’échelle

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Pour modifier les DWUs :


1. Se connecter à la base de données maître, associé à votre serveur SQL de base de données logique.
2. Utilisez l’instruction TSQL de [ALTER DATABASE][] . L’exemple suivant définit l’objectif de niveau de service à DW1000 pour la MySQLDW de la base de données. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Étapes suivantes

Pour d’autres tâches de gestion, consultez la [vue d’ensemble de la gestion][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Vue d’ensemble de la gestion]: ./sql-data-warehouse-overview-manage.md
[Gérer la vue d’ensemble de puissance de calcul]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[MODIFIER LA BASE DE DONNÉES]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
