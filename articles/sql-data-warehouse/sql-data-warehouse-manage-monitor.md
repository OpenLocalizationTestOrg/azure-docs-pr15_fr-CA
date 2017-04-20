<properties
   pageTitle="Surveillez votre charge de travail à l’aide de la DMV | Microsoft Azure"
   description="Apprenez à surveiller votre charge de travail à l’aide de la DMV."
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
   ms.date="10/08/2016"
   ms.author="sonyama;barbkess"/>

# <a name="monitor-your-workload-using-dmvs"></a>Surveillez votre charge de travail à l’aide de la DMV

Cet article décrit comment utiliser des vues de gestion dynamique (DMV) pour surveiller votre charge de travail et examiner l’exécution des requêtes dans l’entrepôt de données SQL Azure.

## <a name="permissions"></a>Autorisations

Pour interroger les DMV dans cet article, vous devez disposer de l’autorisation de l’état d’affichage de base de données ou le contrôle. L’autorisation VIEW DATABASE STATE est généralement l’autorisation par défaut car il est beaucoup plus restrictive.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Surveillance des connexions

Toutes les connexions à SQL Data Warehouse sont enregistrées dans [sys.dm_pdw_exec_sessions][].  Cette DMV contient les 10 000 dernières connexions.  L’ID de session est la clé primaire et assignés de façon séquentielle pour chaque nouvelle ouverture de session.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Exécution de la requête moniteur

Toutes les requêtes exécutées sur le magasin de données SQL sont enregistrés dans [sys.dm_pdw_exec_requests][].  Cette DMV contient 10 000 dernières requêtes exécutées.  Request_id identifie chaque requête et est la clé primaire de cette vue.  Request_id assignés de façon séquentielle pour chaque nouvelle requête et est préfixé avec QID, qui signifie ID de requête.  Interroger cette DMV pour un ID de session donné affiche toutes les requêtes pour une connexion donnée.

>[AZURE.NOTE] Les procédures stockées utilisent plusieurs ID de demande.  ID de la demande sont affectés dans l’ordre séquentiel. 

Voici les étapes à suivre pour étudier les plans d’exécution des requêtes et des heures pour une requête particulière.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>Etape 1 : Identifier la requête que vous souhaitez examiner

```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

Dans les résultats de la requête précédente, **Notez l’ID de demande** de la requête que vous souhaitez examiner.

Requêtes en mode **suspension** sont en file d’attente en raison des limites d’accès concurrentiel. Ces requêtes apparaissent également dans la requête attend de sys.dm_pdw_waits avec un type de UserConcurrencyResourceType. Pour plus d’informations sur les limites d’accès concurrentiel, consultez [Gestion d’accès concurrentiel et la charge de travail][] . Les requêtes peuvent également attendre pour d’autres raisons, telles que les verrous d’objet.  Si votre requête est en attente d’une ressource, consultez [Investigating les requêtes en attente de ressources][] plus loin dans cet article.

Pour simplifier la recherche d’une requête dans la table sys.dm_pdw_exec_requests, utilisez [l’étiquette][] pour affecter un commentaire à votre requête peut être recherché dans la vue sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>ÉTAPE 2 : Étudier le plan de requête

L’ID de demande permet de récupérer le plan de requête distribuée SQL (DSQL) à partir de [sys.dm_pdw_request_steps][].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Lorsqu’un plan DSQL est plu longue que prévu, la cause peut être un plan complexe avec de nombreuses étapes DSQL ou qu’une seule étape prend beaucoup de temps.  Si le plan est le nombre d’étapes à plusieurs opérations de déplacement, envisagez d’optimiser vos distributions de table pour réduire le déplacement des données. La [distribution de tables][] explique pourquoi les données doivent être déplacées pour résoudre une requête et explique certaines stratégies de distribution pour réduire le déplacement des données.

Pour approfondir les détails sur une seule étape, la colonne *type_opération* de l’étape de requête longue et notez l' **Index de l’étape**:

- Passez à l’étape 3 a pour **les opérations SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Passez à l’étape 3 b pour les **opérations de déplacement de données**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>ÉTAPE 3 : étudier SQL sur les bases de données distribuées

Utilisez l’ID de demande et de l’Index de l’étape pour récupérer des informations à partir de [sys.dm_pdw_sql_requests][], qui contient des informations de l’exécution de l’étape de requête sur l’ensemble des bases de données distribuées.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Lors de l’étape de la requête est en cours d’exécution, [DBCC PDW_SHOWEXECUTIONPLAN][] permet d’extraire un plan estimé de SQL Server à partir du cache de plan SQL Server pour l’étape en cours d’exécution sur une distribution particulière.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>ÉTAPE 3 b : étudier le déplacement des données sur les bases de données distribuées

Utilisez l’ID de demande et de l’Index de l’étape pour récupérer des informations sur une étape de déplacement de données en cours d’exécution sur chaque distribution à partir de [sys.dm_pdw_dms_workers][].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- Consultez la colonne *total_elapsed_time* pour voir si une distribution particulière prend beaucoup plus de temps que d’autres pour le déplacement des données.
- Pour la distribution de la durée d’exécution longue, vérifiez la colonne *rows_processed* pour voir si le nombre de lignes en cours de déplacement à partir de cette distribution est nettement plus volumineux que d’autres personnes. Si tel est le cas, cela peut indiquer inclinaison des données sous-jacentes.

Si la requête est en cours d’exécution, [DBCC PDW_SHOWEXECUTIONPLAN][] permet d’extraire un plan estimé de SQL Server à partir du cache de plan SQL Server pour l’étape de SQL en cours d’exécution au sein d’une distribution particulière.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## <a name="monitor-waiting-queries"></a>Surveiller les requêtes en attente

Si vous découvrez que votre requête ne progresse pas car il est en attente d’une ressource, voici une requête qui affiche toutes les ressources de qu'une requête est en attente pour.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Si la requête est en attente activement sur les ressources à partir d’une autre requête, l’état sera **AcquireResources**.  Si la requête possède toutes les ressources nécessaires, l’état sera **autorisé**.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les DMV, consultez [vues système][] .
Consultez les [méthodes conseillées de SQL Data Warehouse][] pour plus d’informations sur les meilleures pratiques

<!--Image references-->

<!--Article references-->
[Manage overview]: ./sql-data-warehouse-overview-manage.md
[Meilleures pratiques de SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[Vues système]: ./sql-data-warehouse-reference-tsql-system-views.md
[Distribution de tables]: ./sql-data-warehouse-tables-distribute.md
[Gestion d’accès concurrentiel et la charge de travail]: ./sql-data-warehouse-develop-concurrency.md
[Analyse de requêtes en attente de ressources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[Sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[Sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[Sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[Sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[Sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[ÉTIQUETTE]: https://msdn.microsoft.com/library/ms190322.aspx
