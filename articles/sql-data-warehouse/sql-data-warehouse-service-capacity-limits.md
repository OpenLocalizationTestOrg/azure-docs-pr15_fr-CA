<properties
   pageTitle="Les limites de capacité SQL Data Warehouse | Microsoft Azure"
   description="Valeurs maximales pour les connexions, les bases de données, les tables et les requêtes SQL Data Warehouse."
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
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="sql-data-warehouse-capacity-limits"></a>Limites de capacité d’entrepôt de données SQL

Les tableaux suivants contiennent les valeurs maximales autorisées pour les différents composants de l’entrepôt de données SQL Azure.


## <a name="workload-management"></a>Gestion de la charge de travail

| Catégorie            | Description                                       | Maximum            |
| :------------------ | :------------------------------------------------ | :----------------- |
| [Unités d’entrepôt de données (DWU)][]| DWU Max pour un seul Data Warehouse de SQL | 6000               |
| [Unités d’entrepôt de données (DWU)][]| DWU Max pour un seul serveur SQL         | 6000 par défaut<br/><br/> Par défaut, chaque serveur SQL (par exemple, myserver.database.windows.net) dispose d’un Quota de DTU de 45 000 qui autorise jusqu'à 6000 DWU. Ce contingent est simplement une limite de sécurité. Vous pouvez augmenter votre quota par la [Création d’un ticket de support][] et en sélectionnant des *quotas* comme le type de demande.  Pour calculer votre DTU a besoin, multipliez le 7.5 par total que DWU nécessaire. Vous pouvez afficher votre consommation de courant DTU à partir de la lame de serveur SQL dans le portail. Interruption et reprises des bases de données sont comptabilisés dans le quota DTU. |
| Connexion de base de données | Sessions simultanées ouvertes                          | 1024<br/><br/>Nous prenons en charge un maximum de 1024 des connexions actives, chacun d'entre eux peut soumettre des demandes pour une base de données SQL Data Warehouse en même temps. Notez qu’il existe des limites sur le nombre de requêtes pouvant être exécutées en fait simultanément. En cas de dépassement de la limite de l’accès concurrentiel, la demande est transmise dans une file d’attente interne où il attend d’être traitée.|
| Connexion de base de données | Quantité de mémoire maximale pour les instructions préparées            | 20 MO              |
| [Gestion de la charge de travail][] | Nombre maximal de requêtes simultané                    | 32<br/><br/> Par défaut, SQL Data Warehouse peuvent exécuter un maximum de 32 requêtes simultanées et les files d’attente restantes des requêtes.<br/><br/>Le niveau d’accès concurrentiel peut diminuer lorsque des utilisateurs sont affectés à une classe de ressource supérieure ou lorsque SQL Data Warehouse est configuré avec DWU faible. Certaines requêtes, comme les requêtes DMV, sont toujours autorisés à exécuter.|
| [Tempdb][]          | Taille max. de Tempdb                                | Go 399 par DW100. Par conséquent à DWU1000 Tempdb est dimensionné à 3,99 to |


## <a name="database-objects"></a>Objets de base de données

| Catégorie          | Description                                  | Maximum            |
| :---------------- | :------------------------------------------- | :----------------- |
| Base de données          | Taille maximale                                     | 240 to compressé sur disque<br/><br/>Cet espace est indépendant de l’espace tempdb ou le journal, et par conséquent, cet espace est dédié à des tables permanentes.  Compression de columnstore ordonné en clusters est estimée à 5 X.  Cette compression permet à la base de données atteigne environ 1 po lorsque toutes les tables sont columnstore en cluster (le type de la table par défaut).|
| Table             | Taille maximale                                     | 60 To compressé sur disque   |
| Table             | Nombre de tables par base de données                          | 2 milliards          |
| Table             | Colonnes par table                            | 1024 colonnes       |
| Table             | Octets par colonne                             | En fonction du [type de données][]de la colonne.  Limite est de 8 000 pour les types de données char, 4000 de nvarchar, ou 2 Go pour les types de données MAX.|
| Table             | Octets par ligne, taille définie                  | 8 060 octets<br/><br/>Le nombre d’octets par ligne est calculé de la même manière, comme pour SQL Server avec la compression de page. Comme SQL Server, SQL Data Warehouse prend en charge le stockage de dépassement de lignes qui permet des **colonnes de longueur variable** à être poussé hors ligne. Lorsque des lignes de longueur variable sont poussés hors ligne, uniquement 24 octets racine est stockée dans l’enregistrement principal. Pour plus d’informations, consultez l’article MSDN de [ligne-dépassement de capacité de données supérieure à 8 Ko][] .|
| Table             | Partitions par table                    | 15 000<br/><br/>Pour optimiser les performances, nous vous recommandons de réduire le nombre de partitions, vous avez besoin tout en prenant en charge des besoins de votre entreprise. Au fur et à mesure du nombre de partitions, la charge pour les opérations de langage de définition de données (DDL) et langage de Manipulation de données (DML) augmente et entraîne le ralentissement des performances.|
| Table             | Caractères par la valeur de limite de partition.| 4000 |
| Index             | Index non ordonnés en clusters par table.        | 999<br/><br/>S’applique aux tables rowstore uniquement.|
| Index             | Index ordonnés en clusters par table.            | 1<br><br/>S’applique aux tables à la fois rowstore et columnstore.|
| Index             | Taille de clé d’index.                          | 900 octets.<br/><br/>S’applique aux index rowstore uniquement.<br/><br/>Index dans des colonnes varchar avec une taille maximale de plus de 900 octets peuvent être créés si les données existantes dans les colonnes ne dépassent pas 900 octets lors de la création de l’index. Toutefois, d’insérer ou d’actions de mise à jour sur les colonnes qui provoquent la taille totale supérieure à 900 octets échouera.|
| Index             | Colonnes clés par index.                   | 16<br/><br/>S’applique aux index rowstore uniquement. Les index ordonnés en clusters columnstore incluent toutes les colonnes.|
| Statistiques        | Taille des combinaison des valeurs de colonne.      | 900 octets.         |
| Statistiques        | Colonnes par objet statistiques.           | 32                 |
| Statistiques        | Statistiques sont créées sur des colonnes par table. | 30 000            |
| Procédures stockées | Niveaux d’imbrication maximum.               | 8                 |
| Affichage              | Colonnes par vue                         | 1 024             |


## <a name="loads"></a>Charges

| Catégorie          | Description                                  | Maximum            |
| :---------------- | :------------------------------------------- | :----------------- |
| Charges Polybase    | Octets par ligne                                | 32 768<br/><br/>Charge de Polybase est limitées au chargement des lignes à la fois inférieure à 32 Ko et ne peut pas charger VARCHR(MAX), nvarchar (max) ou varbinary (max).  Alors que cette limite existe aujourd'hui, il sera supprimé assez rapidement.<br/><br/>


## <a name="queries"></a>Requêtes

| Catégorie          | Description                                  | Maximum            |
| :---------------- | :------------------------------------------- | :----------------- |
| Requête             | En file d’attente des requêtes sur les tables de l’utilisateur.               | 1000               |
| Requête             | Requêtes simultanées sur les vues système.          | 100                |
| Requête             | En file d’attente des requêtes sur les vues système               | 1000               |
| Requête             | Paramètres maximum                           | 2098               |
| Traitement par lots             | Taille maximale                                 | 65, 536 * 4096        |
| Résultats de la sélection    | Colonnes par ligne                              | 4096<br/><br/>Vous n’avez jamais plus de 4096 colonnes par ligne dans le résultat de SELECT. Il n’y a aucune garantie que vous pouvez toujours avoir 4096. Si le plan de requête nécessite une table temporaire, 1024 colonnes par table maximum peuvent s’appliquer.|
| SÉLECTIONNEZ            | Sous-requêtes imbriquées                            | 32<br/><br/>Vous ne pouvez jamais avoir plus de 32 sous-requêtes imbriquées dans une instruction SELECT. Il n’y a aucune garantie que vous pouvez toujours avoir 32. Par exemple, une jointure peut introduire une sous-requête dans le plan de requête. Le nombre de sous-requêtes peut aussi être limité par la mémoire disponible.|
| SÉLECTIONNEZ            | Colonnes par jointure                             | 1024 colonnes<br/><br/>Vous ne pouvez jamais avoir plus de 1024 colonnes dans la jointure. Il n’y a aucune garantie que vous pouvez toujours avoir 1024. Si le plan de la jointure requiert une table temporaire avec plus de colonnes que le résultat de la jointure, la limite de 1024 s’applique à la table temporaire. |
| SÉLECTIONNEZ            | Octets par colonnes GROUP BY.                  | 8060<br/><br/>Les colonnes dans la clause GROUP BY peuvent avoir un maximum de 8 060 octets.|
| SÉLECTIONNEZ            | Octets par colonnes ORDER BY                   | 8 060 octets.<br/><br/>Les colonnes dans la clause ORDER BY peuvent avoir un maximum de 8 060 octets.|
| Les identificateurs et les constantes par instruction | Nombre d’identificateurs référencés et des constantes. | 65 535<br/><br/>SQL Data Warehouse limite le nombre d’identificateurs et les constantes qui peuvent être contenus dans une seule expression d’une requête. Cette limite est de 65 535. Le dépassement de ce nombre se traduit par l’erreur SQL Server 8632. Pour plus d’informations, consultez [erreur interne : une limite des services d’expression a été atteint][].|


## <a name="metadata"></a>Métadonnées

| Vue système                        | Nombre maximal de lignes |
| :--------------------------------- | :------------|
| Sys.dm_pdw_component_health_alerts | 10 000       |
| Sys.dm_pdw_dms_cores               | 100          |
| Sys.dm_pdw_dms_workers             | Nombre total de travailleurs DMS pour la plus récente 1000 demandes de SQL. |
| Sys.dm_pdw_errors                  | 10 000       |
| Sys.dm_pdw_exec_requests           | 10 000       |
| Sys.dm_pdw_exec_sessions           | 10 000       |
| Sys.dm_pdw_request_steps           | Nombre total d’étapes à suivre pour les requêtes SQL 1000 plus récentes qui sont stockées dans sys.dm_pdw_exec_requests. |
| Sys.dm_pdw_os_event_logs           | 10 000       |
| Sys.dm_pdw_sql_requests            | Les 1000 SQL demandes les plus récentes qui sont stockées dans sys.dm_pdw_exec_requests. |


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez [vue d’ensemble de référence SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Unités d’entrepôt de données (DWU)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[Vue d’ensemble de référence SQL Data Warehouse]: ./sql-data-warehouse-overview-reference.md
[Gestion de la charge de travail]: ./sql-data-warehouse-develop-concurrency.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[type de données]: ./sql-data-warehouse-tables-data-types.md
[Création d’un ticket de support]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Données de dépassement de ligne supérieure à 8 Ko]: https://msdn.microsoft.com/library/ms186981.aspx
[Erreur interne : une limite des services d’expression a été atteint.]: https://support.microsoft.com/kb/913050
