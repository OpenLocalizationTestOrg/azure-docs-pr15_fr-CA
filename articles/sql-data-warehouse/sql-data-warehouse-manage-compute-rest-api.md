<properties
   pageTitle="Gérer la puissance de calcul dans des entrepôts de données SQL (reste) de Azure | Microsoft Azure"
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
   ms.date="08/08/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-rest"></a>Gérer la puissance de calcul dans des entrepôts de données SQL (reste) de Azure

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

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Puissance de calcul d’échelle

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Pour modifier les DWUs, utilisez l’API REST de [Création ou mise à jour de base de données][] . L’exemple suivant définit l’objectif de niveau de service à la DW1000 de la base de données MySQLDW, qui est hébergé sur le serveur MyServer. Le serveur est dans un groupe de ressources Azure nommé ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/MyServer/databases/MySQLDW?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Calcul de pause

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Pour suspendre une base de données, utilisez l’API REST de [Suspendre la base de données][] . L’exemple suivant suspend une base de données nommée base de données02 hébergé sur un serveur nommé serveur01. Le serveur est dans un groupe de ressources Azure nommé ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Calcul de la reprise

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Pour démarrer une base de données, utilisez l’API REST de [Reprise de base de données][] . L’exemple suivant démarre une base de données nommée base de données02 hébergé sur un serveur nommé serveur01. Le serveur est dans un groupe de ressources Azure nommé ResourceGroup1. 

```
POST https://management.azure.com/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Étapes suivantes

Pour d’autres tâches de gestion, consultez la [vue d’ensemble de la gestion][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble de la gestion]: ./sql-data-warehouse-overview-manage.md
[Gérer la vue d’ensemble du calcul]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Base de données de pause]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[Base de données de reprise]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[Créer ou mettre à jour la base de données]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
