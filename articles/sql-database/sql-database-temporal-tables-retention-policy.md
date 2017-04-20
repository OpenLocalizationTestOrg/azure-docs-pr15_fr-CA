<properties
   pageTitle="Gérer des données historiques dans les Tables temporelles avec la stratégie de rétention | Microsoft Azure"
   description="Découvrez comment utiliser une stratégie de rétention temporelle pour conserver des données historiques sous votre contrôle."
   services="sql-database"
   documentationCenter=""
   authors="bonova"
   manager="drasumic"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sql-database"
   ms.date="10/12/2016"
   ms.author="bonova"/>

#<a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Gérer des données historiques dans les Tables temporelles avec la stratégie de rétention

Tables temporelles peuvent augmenter la taille de la base de données, et plusieurs tableaux ordinaires, en particulier si vous conservez les données historiques pour une plus longue période de temps. Par conséquent, de rétention des données historiques est un aspect important de la planification et la gestion du cycle de vie de chaque table temporelle. Tables temporelles dans la base de données de SQL Azure sont fournis avec mécanisme de rétention de facile à utiliser qui vous permet d’accomplir cette tâche.

Conservation de l’historique temporel peut être configuré au niveau de la table individuelle, qui permet aux utilisateurs de vieillissement souple de créer des stratégies. Application de la rétention temporelle est simple : il ne nécessite qu’un seul paramètre à définir lors de la modification de schéma ou de création de table.

Après avoir défini la stratégie de rétention, base de données de SQL Azure démarrera vérifiant régulièrement s’il existe des lignes historiques qui sont éligibles pour le nettoyage automatique des données. Identification des lignes correspondantes et leur suppression de la table d’historique se produisent en toute transparence, dans la tâche d’arrière-plan qui est planifiée et exécutée par le système. Condition d’âge pour les lignes de la table historique est vérifiée en fonction de la colonne représentant la fin de la période de SYSTEM_TIME. Si la période de rétention, par exemple, est défini sur six mois, éligible pour le nettoyage des lignes de tableau remplissent la condition suivante :

````
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
````

Dans l’exemple ci-dessus nous avons supposé que **ValidTo** colonne correspond à la fin de la période de SYSTEM_TIME.

##<a name="how-to-configure-retention-policy"></a>Comment faire pour configurer la stratégie de rétention ?

Avant de configurer stratégie de rétention pour une table temporelle, vérifiez d’abord si la conservation d’historique temporelle est activé *au niveau de la base de données*.

````
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
````

Indicateur de base de données **is_temporal_history_retention_enabled** est définie à ON par défaut, mais les utilisateurs peuvent les modifier avec l’instruction ALTER DATABASE. Il est également automatiquement la valeur OFF après l’opération de [restauration instantanée](sql-database-point-in-time-restore-portal.md) . Pour activer le nettoyage de rétention de l’historique temporel de votre base de données, exécutez l’instruction suivante :

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

> [AZURE.IMPORTANT] Vous pouvez configurer la rétention des tables temporelles même si **is_temporal_history_retention_enabled** est défini sur OFF, mais le nettoyage automatique des anciennes lignes ne sera pas déclenché dans ce cas.


Stratégie de rétention est configuré lors de la création de la table en spécifiant la valeur du paramètre HISTORY_RETENTION_PERIOD :

````
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
````

Une base de données SQL Azure vous permet de spécifier la période de rétention en utilisant des unités temps différentes : jours, des semaines, des mois et des années. Si HISTORY_RETENTION_PERIOD est omis, conservation infinie est supposée. Vous pouvez également utiliser le mot-clé INFINITE explicitement.

Dans certains scénarios, vous souhaiterez peut-être configurer rétention après la création de la table, ou pour changer auparavant la valeur configurée. Dans ce cas utiliser l’instruction ALTER TABLE :

````
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
````

> [AZURE.IMPORTANT]  La définition de SYSTEM_VERSIONING OFF *ne conserve pas* de valeur période de rétention. Définir SYSTEM_VERSIONING ON sans HISTORY_RETENTION_PERIOD spécifié explicitement des résultats de la période de conservation infinie.

Pour examiner l’état actuel de la stratégie de rétention, utilisez la requête suivante qui associe l’indicateur d’activation temporelle de rétention au niveau de la base de données avec des périodes de rétention pour des tables individuelles :

````
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
````


##<a name="how-sql-database-deletes-aged-rows"></a>La base de données de SQL supprime âgés de lignes ?

Le processus de nettoyage dépend de la mise en page de l’index de la table de l’historique. Il est important de noter *seules les tables historique avec un index ordonné en clusters (B-tree ou columnstore) peuvent avoir fini de rétention configurée*. Une tâche d’arrière-plan est créée pour effectuer le nettoyage des anciennes données pour toutes les tables temporelles avec la période de rétention finie.
La logique de nettoyage de l’index ordonné en clusters rowstore (B-tree) supprime les anciennes lignes en segments plus petits (jusqu'à 10 K) en réduisant la pression exercée sur le journal de la base de données et le sous-système d’e/s. Bien que la logique de nettoyage utilise les index B-tree requis, ordre des suppressions de lignes plus anciennes que la période de rétention ne peut pas être garantie fermement. Par conséquent, *ne prennent pas toute dépendance vis-à-vis de l’ordre de nettoyage dans vos applications*.

La tâche de nettoyage de la columnstore en cluster supprime ensemble de [groupes de lignes](https://msdn.microsoft.com/library/gg492088.aspx) à la fois (en général contient 1 million de lignes chaque), qui est très efficace, particulièrement lors de la génération des données historiques à un rythme élevé.

![Rétention de cluster columnstore](./media/sql-database-temporal-tables-retention-policy/cciretention.png)


La compression des données excellente et facilite la conservation efficace nettoyage index ordonné en cluster columnstore une solution idéale pour les scénarios lorsque votre charge de travail génère rapidement la grande quantité de données historiques. Ce modèle est généralement utilisé pour beaucoup de [traitement transactionnel des charges de travail qui utilisent des tables temporelles](https://msdn.microsoft.com/library/mt631669.aspx) pour le suivi des modifications et d’audit, d’analyse des tendances ou réception de données IoT.

##<a name="index-considerations"></a>Remarques sur les index

La tâche de nettoyage pour les tables avec un index ordonné en clusters de rowstore nécessite index commence, la colonne correspondante de la fin de la période SYSTEM_TIME. Si ce index n’existe pas, vous ne pourrez pas configurer la période de rétention finie :

*Msg 13765, niveau 16, état 1 <br> </br> période de rétention finie Echec de la définition de la table système version temporelle 'temporalstagetestdb.dbo.WebsiteUserInfo' car la table historique de 'temporalstagetestdb.dbo.WebsiteUserInfoHistory' ne contient-elle pas d’index ordonné en clusters requis. Envisagez la création d’un columnstore en cluster ou un index B-tree commençant par la colonne qui correspond à la fin de SYSTEM_TIME période, sur la table de l’historique.*

Il est important de noter que la table d’historique par défaut déjà créée par base de données de SQL Azure a clustered index, qui est conforme pour la stratégie de rétention. Si vous essayez de supprimer cet index sur une table avec la période de rétention finie, l’opération échoue avec l’erreur suivante :

*Msg 13766, niveau 16, état 1 <br> </br> Impossible de supprimer l’index cluster 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' car il est utilisé pour le nettoyage automatique des données anciennes. Envisagez de définir HISTORY_RETENTION_PERIOD à l’infini sur la table de version système temporelle correspondante si vous avez besoin de supprimer cet index.*

Nettoyage de l’index ordonné en clusters columnstore fonctionne au mieux si les lignes d’historiques sont insérés dans l’ordre croissant (commandé par la fin de la période de la colonne), qui est toujours le cas lorsque la table de l’historique est remplie exclusivement par le mécanisme SYSTEM_VERSIONIOING. Si les lignes de la table d’historique ne sont pas commandés en fin de période de la colonne (ce qui est peut-être le cas si vous avez migré des données historiques existantes), vous devez recréer les index columnstore ordonné en clusters sur les index B-tree rowstore correctement triée, pour obtenir des performances optimales.

Éviter la reconstruction des index columnstore ordonné en clusters sur la table d’historique avec la période de rétention finie, car il peut changer le classement dans les groupes de lignes naturellement imposées par l’opération du système-contrôle de version. Si vous devez reconstruire l’index columnstore ordonné en clusters sur la table de l’historique, faire en recréant sur compatible index B-tree, conservant l’ordre dans la rowgroups nécessaires pour le nettoyage de données régulières. La même approche convient si vous créez une table temporelle avec le tableau historique existant qui a clustered index de colonne sans ordre de garantie des données :

````
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
````

Lors de la période de rétention finie est configuré pour la table d’historique avec l’index columnstore ordonné en clusters, vous ne pouvez pas créer des index B-tree supplémentaires non ordonné en clusters sur cette table :

````
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
````

Une tentative d’exécution précédemment instruction échoue avec l’erreur suivante :

*Msg 13772, niveau 16, état 1 <br> </br> Impossible de créer index non ordonné en clusters sur une table d’historique temporel 'WebsiteUserInfoHistory' dans la mesure où il a une période limitée et des index columnstore ordonné en clusters défini.*

##<a name="querying-tables-with-retention-policy"></a>Interrogation de tables comportant la stratégie de rétention

Toutes les requêtes sur la table temporelle filtrer automatiquement les lignes historiques correspondant de rétention finie, afin d’éviter des résultats imprévisibles et incohérentes, étant donné que les anciennes lignes peuvent être supprimées par la tâche de nettoyage, *à n’importe quel point dans le temps et dans un ordre arbitraire*.

L’illustration suivante montre le plan de requête pour une requête simple :

````
SELECT * FROM dbo.WebsiteUserInfo FROM SYSTEM_TIME ALL;
````

Le plan de requête inclut un filtre supplémentaire appliqué à la fin de la période de la colonne (ValidTo) dans l’opérateur « Clustered Index Scan » sur la table de l’historique (en surbrillance). Cet exemple suppose que cette période de rétention de 1 mois a été définie sur la table de WebsiteUserInfo.

![Filtre de requête de rétention](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Cependant, si vous interrogez directement des table d’historique, vous pouvez voir les lignes qui sont plus anciens que rétention spécifiée période, mais sans aucune garantie de résultats de la requête reproductibles. L’illustration suivante montre le plan d’exécution de la requête sur la table d’historique sans appliquer des filtres supplémentaires :

![Historique des requêtes sans filtre de rétention](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Ne comptez pas votre logique métier sur la lecture de la table d’historique au-delà de la période de rétention que vous pouvez obtenir des résultats incohérents ou inattendus. Il est recommandé d’utiliser des requêtes temporelles avec clause de pour SYSTEM_TIME d’analyse de données dans les tables temporelles.

##<a name="point-in-time-restore-considerations"></a>Point de restauration considérations sur les heures

Lorsque vous créez la nouvelle base de données en [restaurant la base de données existante à un point spécifique dans le temps](sql-database-point-in-time-restore-portal.md), il a désactivé au niveau de la base de données de rétention temporelle. (indicateur de**is_temporal_history_retention_enabled** définie sur OFF). Cette fonctionnalité vous permet d’examiner toutes les lignes d’historiques lors de la restauration, sans craindre que les anciennes lignes sont supprimés avant que vous les interroger. Vous pouvez l’utiliser pour *inspecter les données historiques au-delà de la période de rétention configurée*.

Dire qu’une table temporelle rétention mois une période spécifiée. Si votre base de données a été créée dans la couche de Service Premium, vous ne pourrez pas créer de copie de base de données avec l’état de la base de données jusqu'à 35 jours dans le passé. Qui efficacement afin de pouvoir analyser les lignes historiques qui ont jusqu'à 65 jours en interrogeant directement de la table de l’historique.

Si vous souhaitez activer le nettoyage de rétention temporelle, exécutez l’instruction Transact-SQL suivante après le point de restauration dans le temps :

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

##<a name="next-steps"></a>Étapes suivantes

Pour apprendre à utiliser les Tables temporelles dans vos applications consultez [Mise en route avec les Tables temporelles dans la base de données de SQL Azure](sql-database-temporal-tables.md).

Visitez le site Channel 9 pour entendre un [témoignage de client réel temporelle implémentation](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) et regarder une [démonstration temporelle en direct](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Pour obtenir des informations détaillées sur les Tables temporelles, consultez la [documentation MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
