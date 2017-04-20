<properties
    pageTitle="Gérer et dépanner l’étirement de base de données | Microsoft Azure"
    description="Découvrez comment gérer et dépanner l’étirement de base de données."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="douglasl"/>

# <a name="manage-and-troubleshoot-stretch-database"></a>Gérer et dépanner l’étirement de base de données

Pour gérer et résoudre les problèmes de base de données de l’échelle, utilisez les outils et les méthodes décrites dans cette rubrique.

## <a name="manage-local-data"></a>Gérer des données locales

### <a name="LocalInfo"></a>Obtenir des informations sur les bases de données locales et les tables activées pour la base de données d’étirement
Ouvrir les vues de catalogue **sys.databases** et **sys.tables** pour afficher des informations sur Étirer\-activé des tables et des bases de données SQL Server. Pour plus d’informations, consultez [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) et [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

Pour voir la quantité d’espace à un étirement de\-à l’aide de table activée dans SQL Server, exécutez l’instruction suivante.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## <a name="manage-data-migration"></a>Gérer la migration des données

### <a name="check-the-filter-function-applied-to-a-table"></a>Vérifiez la fonction de filtre appliquée à un tableau
Ouvrir la vue de catalogue **sys.remote\_données\_archive\_tableaux** et vérifiez la valeur de la **filtre\_prédicat** colonne pour identifier la fonction à l’aide de base de données de l’étirement pour sélectionner les lignes à migrer. Si la valeur est null, toute la table est susceptible d’être migrés. Pour plus d’informations, consultez [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx) et [Sélectionner les lignes à migrer à l’aide d’une fonction de filtre](sql-server-stretch-database-predicate-function.md).

### <a name="Migration"></a>Vérifier l’état de la migration des données
Sélectionnez les tâches **| Étirement | Moniteur** pour une base de données dans SQL Server Management Studio pour contrôler la migration des données dans le moniteur de base de données d’étirement. Pour plus d’informations, consultez [moniteur et résoudre les problèmes de migration de données (Database Stretch)](sql-server-stretch-database-monitor.md).

Ou bien, ouvrez la vue de gestion dynamique **sys.dm\_db\_rda\_migration\_état** pour voir combien de lots et de lignes de données ont été migrées.

### <a name="Firewall"></a>Résoudre les problèmes de migration de données
Pour les suggestions de dépannage, reportez-vous à la section [moniteur et résoudre les problèmes de migration de données (Database Stretch)](sql-server-stretch-database-monitor.md).

## <a name="manage-remote-data"></a>Gérer des données à distance

### <a name="RemoteInfo"></a>Obtenir des informations sur les bases de données distantes et les tables utilisées par étirement de base de données
Ouvrir les vues de catalogue **sys.remote\_données\_archive\_bases de données** et **sys.remote\_données\_archive\_tableaux** pour afficher des informations sur les bases de données distantes et les tables dans lequel sont stockées les données migrées. Pour plus d’informations, consultez [sys.remote_data_archive_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) et [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

Pour voir la quantité d’espace utilise une table prenant en charge l’étirement dans Azure, exécutez l’instruction suivante.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### <a name="delete-migrated-data"></a>Supprime les données migrées  
Si vous souhaitez supprimer des données qui a déjà été migrées vers Azure, suivez la procédure décrite dans [sys.sp_rda_reconcile_batch](https://msdn.microsoft.com/library/mt707768.aspx).

## <a name="manage-table-schema"></a>Gérer le schéma de la table

### <a name="dont-change-the-schema-of-the-remote-table"></a>Ne modifiez pas le schéma de la table distante
Ne modifiez pas le schéma d’une table distante Azure qui est associé à une table SQL Server configurée pour l’extension de base de données. En particulier, ne modifiez pas le nom ou le type de données d’une colonne. La fonctionnalité de base de données de l’échelle permet d’hypothèses diverses sur le schéma de la table distante en ce qui concerne le schéma de la table SQL Server. Si vous modifiez le schéma distant, base de données de l’étirement de cesse de fonctionner pour la table modifiée.

### <a name="reconcile-table-columns"></a>Rapprocher les colonnes de la table  
Si vous avez accidentellement supprimé les colonnes de la table distante, exécutez **sp_rda_reconcile_columns** pour ajouter des colonnes à la table distante qui existent dans l’étirement de la\-activé table de SQL Server, mais pas dans la table distante. Pour plus d’informations, consultez [sys.sp_rda_reconcile_columns](https://msdn.microsoft.com/library/mt707765.aspx).  

  > [!IMPORTANT] Lorsque **sp_rda_reconcile_columns** recrée les colonnes que vous avez accidentellement supprimé à partir de la table distante, il ne restaure pas les données qui étaient précédemment dans les colonnes supprimées.

**sp_rda_reconcile_columns** ne supprime pas les colonnes de la table distante qui existent dans la table distante mais pas dans l’étirement de la\-activé table de SQL Server. S’il existe des colonnes dans la table Azure distante qui n’existent plus dans l’étirement de la\-activé SQL Server table, ces colonnes supplémentaires n’empêchent pas les base de données de l’étirement de fonctionner normalement. Vous pouvez éventuellement supprimer manuellement les colonnes supplémentaires.  

## <a name="manage-performance-and-costs"></a>Gérer les performances et les coûts  

### <a name="troubleshoot-query-performance"></a>Résoudre les problèmes de performances des requêtes
Requêtes qui incluent l’étirement\-tables activées devront exécuter plus lentement que c’était le cas avant que les tables soient activés pour l’étirement. Si les performances se dégradent sensiblement, passez en revue les problèmes suivants.

-   Est votre serveur Azure dans une région géographique autre que votre SQL Server ? Configurez votre serveur Azure pour être dans la même région géographique de votre SQL Server afin de réduire la latence du réseau.

-   Les conditions de votre réseau peuvent avoir diminué. Contactez votre administrateur réseau pour obtenir des informations sur les problèmes récents ou pannes.

### <a name="increase-azure-performance-level-for-resource-intensive-operations-such-as-indexing"></a>Augmenter le niveau de performance Azure pour ressources\-opérations intensives telles que l’indexation
Lorsque vous générez, reconstruisez ou réorganisez un index sur une table de grande taille qui est configuré pour l’extension de base de données, et vous prévoyez l’interrogation importante des données migrées dans Azure pendant ce temps, envisagez d’augmenter le niveau de performance de la base de Azure distante correspondante pour la durée de l’opération. Pour plus d’informations sur les niveaux de performances et de prix, consultez [Tarification de base de données SQL Server Stretch](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="you-cant-pause-the-sql-server-stretch-database-service-on-azure"></a>Vous ne pouvez pas interrompre le service de base de données de SQL Server étirer sur Azure  
 Assurez-vous que vous sélectionnez les performances approprié et le niveau de prix. Si vous augmentez le niveau de performance temporairement d’une ressource\-opération intensive, restaurer au niveau précédent, une fois l’opération terminée. Pour plus d’informations sur les niveaux de performances et de prix, consultez [Tarification de base de données SQL Server Stretch](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).  

## <a name="change-the-scope-of-queries"></a>Modifier l’étendue des requêtes  
 Requêtes sur étirement\-tables activées retournent des données en local et à distance par défaut. Vous pouvez modifier l’étendue des requêtes pour toutes les requêtes par tous les utilisateurs ou uniquement pour une seule requête par un administrateur.  

### <a name="change-the-scope-of-queries-for-all-queries-by-all-users"></a>Modifier l’étendue des requêtes pour toutes les requêtes par tous les utilisateurs  
 Pour modifier la portée de toutes les requêtes par tous les utilisateurs, exécutez la procédure stockée **sys.sp_rda_set_query_mode**. Vous pouvez réduire la portée pour que les données locales de la requête, toutes les requêtes de désactiver ou restaurer le paramètre par défaut. Pour plus d’informations, consultez [sys.sp_rda_set_query_mode](https://msdn.microsoft.com/library/mt703715.aspx).  

### <a name="queryHints"></a>Modifier l’étendue des requêtes pour une seule requête par un administrateur  
 Pour modifier l’étendue d’une seule requête par un membre du rôle db_owner, vous devez ajouter la * *WITH \( REMOTE_DATA_ARCHIVE_OVERRIDE = *valeur* \)** indicateur de requête de l’instruction SELECT. L’indicateur de requête REMOTE_DATA_ARCHIVE_OVERRIDE peut avoir les valeurs suivantes.  
 -   **LOCAL_ONLY**. Interroger les données locales uniquement.  

 -   **REMOTE_ONLY**. Demander des données à distance uniquement.  

 -   **STAGE_ONLY**. Interroger uniquement les données dans le tableau où les étapes de base de données de l’étirement de lignes éligibles à la migration et conserve les lignes migrées de la période spécifiée après la migration. Cet indicateur de requête est le seul moyen pour interroger la table intermédiaire.  

Par exemple, la requête suivante renvoie des résultats locaux uniquement.  

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="adminHints"></a>Rendre les suppressions et les mises à jour administratives  
 Par défaut, vous ne pouvez pas mettre à jour ou supprimer des lignes qui sont éligibles à la migration ou les lignes qui ont déjà été migrés, dans un étirement\-activé la table. Lorsque vous devez résoudre un problème, un membre du rôle db_owner peut exécuter une opération de mise à jour ou de suppression en ajoutant le * *WITH \( REMOTE_DATA_ARCHIVE_OVERRIDE = *valeur* \)** indicateur de requête pour l’instruction. L’indicateur de requête REMOTE_DATA_ARCHIVE_OVERRIDE peut avoir les valeurs suivantes.  
 -   **LOCAL_ONLY**. Mettre à jour ou supprimer des données locales uniquement.  

 -   **REMOTE_ONLY**. Mettre à jour ou supprimer des données à distance uniquement.  

 -   **STAGE_ONLY**. Mettre à jour ou supprimer uniquement les données de la table où les étapes de base de données de l’étirement de lignes éligibles à la migration et conserve les lignes migrées de la période spécifiée après la migration.  

## <a name="see-also"></a>Voir aussi

[Base de données étirement du moniteur](sql-server-stretch-database-monitor.md)

[Bases de données prenant en charge l’étirement de la sauvegarde](sql-server-stretch-database-backup.md)

[Restaurer les bases de données prenant en charge l’étirement](sql-server-stretch-database-restore.md)
