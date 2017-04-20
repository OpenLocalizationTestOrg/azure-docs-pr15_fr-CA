<properties
   pageTitle="Gestion d’accès concurrentiel et la charge de travail dans l’entrepôt de données SQL | Microsoft Azure"
   description="Comprendre la gestion d’accès concurrentiel et la charge de travail dans l’entrepôt de données SQL Azure pour le développement de solutions."
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
   ms.date="09/27/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>Gestion d’accès concurrentiel et la charge de travail dans l’entrepôt de données SQL

Pour fournir des performances prévisibles à grande échelle, SQL Data Warehouse Microsoft Azure permet vous contrôlez les niveaux d’accès concurrentiel et les affectations de ressources comme la mémoire et de la priorité de l’unité centrale. Cet article présente les concepts de gestion d’accès concurrentiel et la charge de travail, expliquant comment ces deux fonctionnalités ont été implémentées et comment vous pouvez les contrôler dans le data warehouse. Gestion de la charge de travail d’entrepôt de données SQL est là pour vous aider à prendre en charge des environnements multi-utilisateurs. Il n’est pas destinée à être partagées de charges de travail.

## <a name="concurrency-limits"></a>Limites d’accès concurrentiel

SQL Data Warehouse permet jusqu'à 1 024 connexions simultanées. Toutes les connexions de 1 024 peuvent soumettre des requêtes simultanément. Toutefois, pour optimiser le débit, SQL Data Warehouse peut en file d’attente des requêtes pour vous assurer que chaque requête reçoit une allocation de mémoire minimale. Queuing se produit au moment de l’exécution de requête. Par les files d’attente des requêtes lorsque les limites d’accès concurrentiel sont atteintes, SQL Data Warehouse peut augmenter débit total en garantissant que l’accès aux ressources de mémoire nécessaires sont-ils requêtes actives.  

Limites d’accès concurrentiel sont régies par les deux concepts : *requêtes simultanées* et *les emplacements d’accès concurrentiel*. Pour une requête à exécuter, il doit être exécuté dans les deux requêtes d’accès concurrentiel et la répartition des créneaux d’accès concurrentiel.

- Requêtes simultanées sont les requêtes en cours d’exécution en même temps. SQL Data Warehouse prend en charge jusqu'à 32 requêtes simultanées sur les grandes tailles DWU.
- Les emplacements d’accès concurrentiel sont allouées en fonction de DWU. Chaque DWU 100 fournit 4 emplacements d’accès concurrentiel. Par exemple, un DW100 alloue 4 emplacements d’accès concurrentiel et DW1000 alloue 40. Chaque requête utilise un ou plusieurs accès concurrentiel emplacements, dépendants de la [classe de ressource](#resource-classes) de la requête. Requêtes en cours d’exécution dans la classe de la ressource smallrc consomment un emplacement d’accès concurrentiel. Requêtes exécutées dans une classe de ressource plus élevée consomment plus d’emplacements d’accès concurrentiel.

Le tableau suivant décrit les limites pour les demandes simultanées et les créneaux horaires de concurrence d’accès dans les différentes tailles DWU.

### <a name="concurrency-limits"></a>Limites d’accès concurrentiel

|  DWU   | Requêtes simultanées de max  | Emplacements d’accès concurrentiel alloués |
| :----  | :---------------------: | :-------------------------: |
| DW100  |            4            |                4            |
| DW200  |            8            |                8            |
| DW300  |           12            |               12            |
| DW400  |           16            |               16            |
| DW500  |           20            |               20            |
| DW600  |           24            |               24            |
| DW1000 |           32            |               40            |
| DW1200 |           32            |               48            |
| DW1500 |           32            |               60            |
| DW2000 |           32            |               80            |
| DW3000 |           32            |              120            |
| DW6000 |           32            |              240            |

Lorsque l’une de ces seuils est remplie, les nouvelles requêtes sont en file d’attente et exécutées sur une base premier sorti, first-in.  Comme des requêtes se termine et le nombre de requêtes et d’emplacements inférieure à la limite, les requêtes en file d’attente sont libérées. 

> [AZURE.NOTE]  *Sélectionnez* les requêtes en cours d’exécution exclusivement sur les vues de gestion dynamique (DMV) ou les vues de catalogue ne sont pas régies par les limites d’accès concurrentiel. Vous pouvez surveiller le système, quel que soit le nombre de requêtes en cours d’exécution dessus.

## <a name="resource-classes"></a>Classes de ressources

Ressources des classes vous permettent de que contrôler l’allocation de mémoire et de cycles de processeur données à une requête. Vous pouvez affecter quatre classes de ressource à un utilisateur dans l’écran des *rôles de base de données*. Les classes de quatre ressources sont **smallrc**, **mediumrc**, **largerc**et **xlargerc**. Smallrc les utilisateurs bénéficient d’une plus petite quantité de mémoire et peuvent tirer parti de concurrence plus élevé. En revanche, affectées à xlargerc des utilisateurs disposent de grandes quantités de mémoire, et par conséquent moins de leurs requêtes peuvent s’exécuter simultanément.

Par défaut, chaque utilisateur est un membre de la classe de ressource de petite taille, smallrc. La procédure `sp_addrolemember` est utilisé pour augmenter la classe de ressource, et `sp_droprolemember` permet de réduire la classe de ressource. Par exemple, cette commande augmenterait la classe de ressource de loaduser à largerc :

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

Une bonne pratique doit définitivement affecter des utilisateurs à une classe de ressources plutôt que de modifier leurs classes de ressource. Par exemple, des charges à des tables en cluster columnstore créer des index de qualité supérieure lorsque plus de mémoire allouée. Pour vous assurer que les charges ont accès à une mémoire, créez un utilisateur en particulier pour le chargement des données et définitivement affecter l’utilisateur à une classe de ressource plus élevée.

Il existe quelques types de requêtes qui ne bénéficient pas d’une allocation de mémoire supérieure. Le système ignore leur allocation de classe de ressource et toujours exécuter ces requêtes dans la classe de ressource de petite taille, à la place. Si ces requêtes sont toujours exécutent dans la classe de ressource de petite taille, ils peuvent exécuter lorsque les emplacements d’accès concurrentiel sont sous pression et ne consomment plus d’emplacements que nécessaire. Pour plus d’informations, reportez-vous à la section [exceptions de classe de ressource](#query-exceptions-to-concurrency-limits) .

Un peu plus sur la classe de ressource :

- Autorisation de *Modifier le rôle* est nécessaire pour modifier la classe de ressource d’un utilisateur.  
- Bien que vous pouvez ajouter un utilisateur à un ou plusieurs des classes de ressource plus élevés, les utilisateurs aura les attributs de la classe de ressources le plus élevée auquel ils sont affectés. Autrement dit, si un utilisateur est affecté à la fois mediumrc et largerc, la classe de ressource supérieure (largerc) est la classe de ressource qui sera respectée.  
- La classe de ressource de l’administrateur système ne peut pas être modifiée.

Pour un exemple détaillé, consultez [exemple de classe de ressources variables utilisateur](#changing-user-resource-class-example).

## <a name="memory-allocation"></a>Allocation de mémoire

Il y a des avantages et inconvénients à l’augmentation de la classe de ressource d’un utilisateur. Augmentation d’une classe de ressource pour un utilisateur donnera leurs requêtes aient accès à davantage de mémoire, ce qui peut signifier que les requêtes s’exécutent plus rapidement.  Toutefois, les catégories de ressources supérieures également de réduire le nombre de requêtes simultanées qui peuvent s’exécuter. C’est le compromis entre les allouer de grandes quantités de mémoire pour une seule requête ou autoriser d’autres requêtes, ce qui a également besoin des allocations de mémoire pour s’exécuter simultanément. Si un utilisateur est élevées allocations de mémoire pour une requête, autres utilisateurs n’auront pas accès à cette même mémoire pour exécuter une requête.

Le tableau suivant mappe la mémoire allouée à chaque distribution par classe de ressources et de DWU.

### <a name="memory-allocations-per-distribution-mb"></a>Allocations de mémoire par distribution (Mo)

|  DWU   | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100  |   100   |    100   |    200  |    400   |
| DW200  |   100   |    200   |    400  |    800   |
| DW300  |   100   |    200   |    400  |    800   |
| DW400  |   100   |    400   |    800  |  1 600   |
| DW500  |   100   |    400   |    800  |  1 600   |
| DW600  |   100   |    400   |    800  |  1 600   |
| DW1000 |   100   |    800   |  1 600  |  3 200   |
| DW1200 |   100   |    800   |  1 600  |  3 200   |
| DW1500 |   100   |    800   |  1 600  |  3 200   |
| DW2000 |   100   |  1 600   |  3 200  |  6,400   |
| DW3000 |   100   |  1 600   |  3 200  |  6,400   |
| DW6000 |   100   |  3 200   |  6,400  |  12,800  |

Dans le tableau précédent, vous pouvez voir qu’une requête s’exécutant sur un DW2000 dans la classe de la ressource xlargerc aurait accès à 6 400 Mo de mémoire pour chacune des bases de données distribuées 60.  SQL Data Warehouse, il y a 60 distributions. Par conséquent, pour calculer l’allocation de la mémoire totale d’une requête dans une classe de ressource donné, les valeurs ci-dessus doivent multipliés par 60.

### <a name="memory-allocations-system-wide-gb"></a>Mémoire allocations au niveau du système (Go)

|  DWU   | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100  |    6    |    6     |    12   |    23    |
| DW200  |    6    |    12    |    23   |    47    |
| DW300  |    6    |    12    |    23   |    47    |
| DW400  |    6    |    23    |    47   |    94    |
| DW500  |    6    |    23    |    47   |    94    |
| DW600  |    6    |    23    |    47   |    94    |
| DW1000 |    6    |    47    |    94   |   188    |
| DW1200 |    6    |    47    |    94   |   188    |
| DW1500 |    6    |    47    |    94   |   188    |
| DW2000 |    6    |    94    |   188   |   375    |
| DW3000 |    6    |    94    |   188   |   375    |
| DW6000 |    6    |   188    |   375   |   750    |

À partir de cette table d’allocations de mémoire à l’échelle du système, vous pouvez voir qu’une requête s’exécutant sur un DW2000 dans la classe de la ressource xlargerc est allouée à un total de 375 Go de mémoire (6 400 Mo * 60 distributions / 1024 pour convertir en Go) sur l’intégralité de votre magasin de données SQL.

## <a name="concurrency-slot-consumption"></a>Consommation d’emplacement d’accès concurrentiel

SQL Data Warehouse accorde plus de mémoire pour les requêtes en cours d’exécution dans les catégories de ressources supérieures. La mémoire est une ressource fixe.  Par conséquent, plus la mémoire allouée par la requête, les moins de requêtes simultanées peuvent exécuter. Le tableau suivant réitère tous les concepts précédentes dans une seule vue qui affiche le nombre d’emplacements de concurrence d’accès disponibles par DWU et les emplacements utilisés par chaque classe de la ressource.

### <a name="allocation-and-consumption-of-concurrency-slots"></a>Allocation et la consommation de créneaux horaires d’accès concurrentiel

|  DWU   | Nombre maximal de requêtes simultané  | Emplacements d’accès concurrentiel alloués | Emplacements utilisés par smallrc |  Emplacements utilisés par mediumrc |  Emplacements utilisés par largerc |  Emplacements utilisés par xlargerc |
| :----  | :---------------------: | :-------------------------: | :-----: | :------: | :-----: | :------: |
| DW100  |            4            |                4            |    1    |     1    |    2    |    4     |
| DW200  |            8            |                8            |    1    |     2    |    4    |    8     |
| DW300  |           12            |               12            |    1    |     2    |    4    |    8     |
| DW400  |           16            |               16            |    1    |     4    |    8    |   16     |
| DW500  |           20            |               20            |    1    |     4    |    8    |   16     |
| DW600  |           24            |               24            |    1    |     4    |    8    |   16     |
| DW1000 |           32            |               40            |    1    |     8    |   16    |   32     |
| DW1200 |           32            |               48            |    1    |     8    |   16    |   32     |
| DW1500 |           32            |               60            |    1    |     8    |   16    |   32     |
| DW2000 |           32            |               80            |    1    |    16    |   32    |   64     |
| DW3000 |           32            |              120            |    1    |    16    |   32    |   64     |
| DW6000 |           32            |              240            |    1    |    32    |   64    |  128     |


Dans ce tableau, vous pouvez voir exécutant SQL Data Warehouse comme DW1000 alloue un maximum de 32 requêtes simultanées et un total de 40 slots d’accès concurrentiel. Si tous les utilisateurs sont en cours d’exécution dans smallrc, 32 requêtes simultanées seraient autorisées car chaque requête consommerait 1 emplacement d’accès concurrentiel. Si tous les utilisateurs d’un DW1000 en cours d’exécution dans mediumrc, chaque requête aurait affecté des 800 Mo par distribution pour une allocation de la mémoire totale de 47 Go par requête et concurrence d’accès est limité à 5 utilisateurs (40 slots de simultanéité / 8 emplacements par utilisateur de mediumrc).

## <a name="query-importance"></a>Importance de la requête

SQL Data Warehouse implémente des classes de ressource à l’aide de groupes de travail. Il y a un total de huit groupes de charges de travail qui contrôlent le comportement des classes de ressource sur les différentes tailles DWU. Pour les DWU, SQL Data Warehouse n'utilise que quatre des groupes de travail de huit. Cela semble logique, car chaque groupe de charge de travail est affecté à l’une des quatre classes de ressources : smallrc, mediumrc, largerc, ou xlargerc. Important de comprendre les groupes de la charge de travail est que certains de ces groupes de charges de travail sont une *importance*supérieure. Importance est utilisé pour le processeur de planification. Requêtes exécutées avec une importance haute obtenez trois fois plus de cycles processeur que ceux avec une importance moyenne. Par conséquent, mappages d’emplacement d’accès concurrentiel déterminent également la priorité de l’UC. Lorsqu’une requête utilise des emplacements de 16 ans ou plus, qu’il s’exécute comme très important.

Le tableau suivant montre les mappages d’importance pour chaque groupe de charge de travail.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Mappages de groupes de travail pour les emplacements d’accès concurrentiel et l’importance

| Groupes de charges de travail | Mappage d’emplacement d’accès concurrentiel | Mo / Distribution | Mappage d’importance |
| :-------------- | :----------------------: | :---------------: | :----------------- |
| SloDWGroupC00   |            1             |         100       |       Support       |
| SloDWGroupC01   |            2             |         200       |       Support       |
| SloDWGroupC02   |            4             |         400       |       Support       |
| SloDWGroupC03   |            8             |         800       |       Support       |
| SloDWGroupC04   |           16             |       1 600       |       Élevé         |
| SloDWGroupC05   |           32             |       3 200       |       Élevé         |
| SloDWGroupC06   |           64             |       6,400       |       Élevé         |
| SloDWGroupC07   |          128             |      12,800       |       Élevé         |

À partir du plan de **répartition et consommation de créneaux horaires d’accès concurrentiel** , vous pouvez voir qu’un DW500 utilise 1, 4, 8 ou les emplacements d’accès concurrentiel 16 pour smallrc, mediumrc, largerc et xlargerc, respectivement. Vous pouvez rechercher ces valeurs dans le graphique précédent pour rechercher l’importance pour chaque classe de la ressource.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Mise en correspondance des classes de ressource à l’importance DW500

| Classe de ressource | Groupe de charge de travail | Emplacements d’accès concurrentiel utilisées | Mo / Distribution | Importance |
| :------------- | :------------- | :--------------------: | :---------------: | :--------- |
| smallrc        | SloDWGroupC00  |           1            |         100       |   Support   |
| mediumrc       | SloDWGroupC02  |           4            |         400       |   Support   |
| largerc        | SloDWGroupC03  |           8            |         800       |   Support   |
| xlargerc       | SloDWGroupC04  |          16            |        1 600      |   Élevé     |


Vous pouvez utiliser la requête suivante de la DMV d’examiner les différences dans l’allocation des ressources mémoire en détail dans la perspective du gouverneur de ressources, ou pour analyser l’utilisation active et historique des groupes de charges de travail lors de la résolution.

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                      AS node_type
    ,pn.pdw_node_id                 AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                  AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                     AS group_max_dop
    ,wg.effective_max_dop               AS group_effective_max_dop
    ,wg.total_request_count             AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON  wg.pdw_node_id  = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,   group_request_max_memory_grant_pcnt
,   group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>Requêtes qui respectent les limites d’accès concurrentiel

La plupart des requêtes sont régies par les classes de ressource. Ces requêtes doivent tenir dans les deux simultanées de requêtes et de simultanéité emplacement seuils. Un utilisateur ne peut pas choisir d’exclure d’une requête à partir du modèle d’emplacement d’accès concurrentiel.

Pour rappel, les instructions suivantes respectent les classes de ressource :

- INSERT-SELECT
- MISE À JOUR
- SUPPRIMER
- Sélectionnez (lors de l’interrogation de tables de l’utilisateur)
- ALTER INDEX REBUILD
- ALTER INDEX REORGANIZE
- ALTER TABLE RECONSTRUCTION
- CRÉATION D’INDEX
- CRÉER LES INDEX ORDONNÉS EN CLUSTERS COLUMNSTORE
- CRÉER LA TABLE EN TANT QUE SÉLECTION (SACT)
- Chargement de données
- Opérations de déplacement de données effectuées par le Service de mouvement de données (DMS)

## <a name="query-exceptions-to-concurrency-limits"></a>Exceptions de requête pour les limites d’accès concurrentiel

Certaines requêtes ne tiennent pas compte de la classe de la ressource à laquelle l’utilisateur est affecté. Ces exceptions dans les limites d’accès concurrentiel sont effectuées lorsque les ressources nécessaires pour une commande particulière sont faibles, souvent parce que la commande est une opération sur les métadonnées. L’objectif de ces exceptions est d’éviter les allocations de mémoire plus importantes pour les requêtes qui n’en ont jamais besoin. Dans ces cas, la classe de ressource de petite taille par défaut (smallrc) est toujours utilisée indépendamment de la classe de ressource réelle affectée à l’utilisateur. Par exemple, `CREATE LOGIN` , pourront toujours exécuter dans smallrc. Les ressources requises pour exécuter cette opération étant très faibles, il est inutile d’inclure la requête dans le modèle d’emplacement d’accès concurrentiel.  Ces requêtes ne sont pas aussi limités par la limite de la concurrence d’accès 32 utilisateur, un nombre illimité de ces requêtes peut s’exécutent dans la limite de 1 024 sessions de la session.

Les instructions suivantes ne tiennent pas compte des classes de ressource :

- CRÉER ou supprimer une TABLE
- ALTER TABLE... COMMUTATEUR, fractionner ou fusionner une PARTITION
- ALTER INDEX DÉSACTIVER
- SUPPRIMER L’INDEX
- CRÉER, mettre à jour ou DROP STATISTICS
- TRUNCATE TABLE
- MODIFIER L’AUTORISATION
- CRÉER UNE CONNEXION
- CREATE, ALTER ou DROP USER
- CREATE, ALTER ou DROP PROCEDURE
- CRÉER ou supprimer une vue
- INSÉRER DES VALEURS
- SÉLECTIONNER à partir des vues système et DMV
- EXPLIQUEZ
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="change-a-user-resource-class-example"></a>Modifier un exemple de classe de ressource utilisateur

1. **Create login :** Ouvrir une connexion à votre base de données **master** sur le serveur SQL qui héberge la base de données SQL Data Warehouse et exécuter les commandes suivantes.

    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```

    > [AZURE.NOTE] Il est conseillé de créer un utilisateur dans la base de données master pour les utilisateurs de magasin de données SQL Azure. Création d’un utilisateur dans master permet à un utilisateur pour la connexion à l’aide d’outils tels que SSMS sans spécifier un nom de base de données.  Il leur permet également d’utiliser l’Explorateur d’objets pour afficher toutes les bases de données sur un serveur SQL.  Pour plus d’informations sur la création et la gestion des utilisateurs, reportez-vous à [sécuriser une base de données SQL Data Warehouse][].

2. **Utilisateur de créer un entrepôt de données de SQL :** Ouvrir une connexion à la base de données **SQL Data Warehouse** et exécuter la commande suivante.

    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```

3. **Autorisations :** L’exemple suivant accorde `CONTROL` sur la base de données **SQL Data Warehouse** . `CONTROL`à la base de données au niveau équivaut à db_owner dans SQL Server.

    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```

4. **Augmenter la classe de ressource :** Utilisez la requête suivante pour ajouter un utilisateur à un rôle de gestion de la charge de travail supérieur.

    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```

5. **Diminuer la classe de ressource :** La requête suivante permet de supprimer un utilisateur d’un rôle de gestion de la charge de travail.

    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```

    > [AZURE.NOTE] Il n’est pas possible de supprimer un utilisateur de smallrc.

## <a name="queued-query-detection-and-other-dmvs"></a>Détection de la requête en file d’attente et autres DMV

Vous pouvez utiliser la `sys.dm_pdw_exec_requests` DMV pour identifier les requêtes en attente dans une file d’attente d’accès concurrentiel. Requêtes en attente d’un emplacement d’accès concurrentiel aura le statut **suspendu**.

```sql
SELECT   r.[request_id]              AS Request_ID
        ,r.[status]              AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]              AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

Les rôles de gestion de la charge de travail peuvent être affichés avec `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

La requête suivante montre quel rôle est affecté à chaque utilisateur.

```sql
SELECT   r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id     = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id   = m.principal_id
WHERE   r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL Data Warehouse dispose des types d’attente suivants :

- **LocalQueriesConcurrencyResourceType**: les requêtes qui se trouvent en dehors de l’infrastructure d’emplacement d’accès concurrentiel. Requêtes DMV et système de fonctions telles que `SELECT @@VERSION` sont des exemples de requêtes locales.
- **UserConcurrencyResourceType**: les requêtes qui se trouvent à l’intérieur de l’infrastructure d’emplacement d’accès concurrentiel. Requêtes sur les tables de l’utilisateur final représentent des exemples de ce type de ressource.
- **DmsConcurrencyResourceType**: attente résultant d’opérations de déplacement de données.
- **BackupConcurrencyResourceType**: cette attente indique qu’une base de données est sauvegardé. La valeur maximale pour ce type de ressource est 1. Si plusieurs sauvegardes ont été demandés en même temps, les autres sont en file d’attente.

Le `sys.dm_pdw_waits` DMV peut être utilisé pour voir quelles ressources attend une demande.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,   SESSION_ID()            AS Current_session
,   s.[status]          AS Session_status
,   s.[login_name]
,   s.[query_count]
,   s.[client_id]
,   s.[sql_spid]
,   r.[command]         AS Request_command
,   r.[label]
,   r.[status]          AS Request_status
,   r.[submit_time]
,   r.[start_time]
,   r.[end_compile_time]
,   r.[end_time]
,   DATEDIFF(ms,r.[submit_time],r.[start_time])     AS Request_queue_time_ms
,   DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,   DATEDIFF(ms,r.[end_compile_time],r.[end_time])      AS Request_execution_time_ms
,   r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE   w.[session_id] <> SESSION_ID();
```

Le `sys.dm_pdw_resource_waits` DMV ne montre que les attentes de ressources consommées par une requête donnée. Temps d’attente de ressources ne mesure que le temps d’attente pour les ressources à fournir, par opposition aux temps d’attente de signal, qui est le temps que nécessaire pour les serveurs SQL sous-jacente planifier la requête sur le processeur.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE   [session_id] <> SESSION_ID();
```

Le `sys.dm_pdw_wait_stats` DMV peut être utilisé pour l’analyse des tendances historiques des attentes.

```sql
SELECT  w.[pdw_node_id]
,       w.[wait_name]
,       w.[max_wait_time]
,       w.[request_count]
,       w.[signal_time]
,       w.[completed_count]
,       w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la gestion de sécurité et les utilisateurs de la base de données, reportez-vous à [sécuriser une base de données SQL Data Warehouse][]. Pour plus d’informations sur la ressource la plus importante, classes peut améliorer la qualité des index columnstore ordonné en clusters, voir [reconstruction d’index pour améliorer la qualité du segment].

<!--Image references-->

<!--Article references-->
[Sécuriser une base de données SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Reconstruction des index pour améliorer la qualité de segment]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Sécuriser une base de données SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
