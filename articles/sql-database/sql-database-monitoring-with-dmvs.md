<properties
   pageTitle="Surveillance à l’aide des vues de gestion dynamique de la base de données SQL Azure | Microsoft Azure"
   description="Apprenez à détecter et diagnostiquer les problèmes de performances courants à l’aide des vues de gestion dynamique pour analyser la base de données SQL de Microsoft Azure."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/20/2016"
   ms.author="carlrab"/>

# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Surveillance de base de données SQL Azure à l’aide des vues de gestion dynamique

Base de données SQL de Microsoft Azure permet un sous-ensemble des vues de gestion dynamique pour diagnostiquer les problèmes de performances, qui peuvent être causés par les requêtes bloquées ou longue, des goulots d’étranglement, les plans de requête défectueux et ainsi de suite. Cette rubrique fournit des informations sur la façon de détecter des problèmes de performances courants à l’aide de vues de gestion dynamiques.

Base de données de SQL prend en charge partiellement les trois catégories de vues de gestion dynamiques :

- Vues liés à la base de données de gestion dynamique.
- Vues associées à l’exécution de gestion dynamique.
- Vues liés à la transaction de gestion dynamique.

Pour plus d’informations sur les vues de gestion dynamique, consultez [vues de gestion dynamique et fonctions (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) dans la documentation en ligne de SQL Server.

## <a name="permissions"></a>Autorisations

Dans la base de données de SQL, requête d’une vue de gestion dynamique requiert des autorisations de **L’état d’affichage de base de données** . L’autorisation **VIEW DATABASE STATE** renvoie des informations sur tous les objets dans la base de données en cours.
Pour accorder l’autorisation de **l’État d’affichage de base de données** à un utilisateur de base de données spécifique, exécutez la requête suivante :

```GRANT VIEW DATABASE STATE TO database_user; ```

Dans une instance de local SQL Server, vues de gestion dynamique renvoient des informations d’état de serveur. Dans la base de données SQL qu’ils renvoient des informations concernant votre logique base de données actuelle uniquement.

## <a name="calculating-database-size"></a>Calcul de la taille de la base de données

La requête suivante renvoie la taille de votre base de données (en mégaoctets) :

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

La requête suivante renvoie la taille des objets individuels (en mégaoctets) dans votre base de données :

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Surveillance des connexions

La vue [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) permet de récupérer des informations sur les connexions établies à un serveur spécifique de la base de données de SQL Azure et les détails de chaque connexion. En outre, la vue [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) est utile lors de la récupération des informations sur toutes les connexions d’utilisateurs actives et tâches internes.
La requête suivante extrait des informations sur la connexion en cours :

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [AZURE.NOTE] Lors de l’exécution de **l’association** et **les vues sys.dm_exec_sessions**, si vous avez l’autorisation de **l’État d’affichage de base de données** sur la base de données, vous voyez en cours d’exécution toutes les sessions sur la base de données ; dans le cas contraire, vous consultez uniquement à la session en cours.

## <a name="monitoring-query-performance"></a>Surveillance des performances des requêtes

Exécution lente ou long de requêtes peut consommer des ressources système de significatif. Cette section illustre l’utilisation des vues de gestion dynamique pour détecter quelques problèmes de performances courants de requête. Une référence plus ancienne, mais toujours utile pour le dépannage, est l’article [Résolution des problèmes de performances dans SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) sur Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Recherche de requêtes top N

L’exemple suivant renvoie des informations sur les cinq premières requêtes les classés par temps processeur moyen. Cet exemple regroupe les requêtes en fonction de leur hachage de requête, afin que les requêtes logiquement équivalentes sont regroupés par leur consommation de ressource cumulée.

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
    MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
    ((CASE statement_end_offset
        WHEN -1 THEN DATALENGTH(ST.text)
        ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
     FROM sys.dm_exec_query_stats AS QS
     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Surveillance des requêtes bloquées

Les requêtes lentes ou longues peuvent contribuer à une consommation excessive de ressources et être la conséquence de requêtes bloquées. La cause du blocage possible de conception d’application médiocre, les plans de requête incorrect, l’absence d’index utiles et ainsi de suite. Vous pouvez utiliser la vue sys.dm_tran_locks pour obtenir des informations sur l’activité de verrouillage en cours dans votre base de données de SQL Azure. Par exemple le code, consultez [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) dans la documentation en ligne de SQL Server.

### <a name="monitoring-query-plans"></a>Surveillance des plans de requête

Un plan de requête inefficace peut également augmenter la consommation de processeur. L’exemple suivant utilise la vue [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) pour déterminer quelle requête utilise l’UC plus cumulative.

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Voir aussi

[Présentation de la base de données SQL](sql-database-technical-overview.md)
