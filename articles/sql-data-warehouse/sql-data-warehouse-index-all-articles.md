<properties
    pageTitle="Toutes les rubriques pour le service d’entrepôt de données SQL | Microsoft Azure"
    description="Tableau de toutes les rubriques du service Azure nommé SQL Data Warehouse qui existent sur http://azure.microsoft.com/documentation/articles/, titre et description."
    services="sql-data-warehouse"
    documentationCenter=""
    authors="barbkess"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-data-warehouse"
    ms.workload="sql-data-warehouse"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="barbkess"/>


# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>Toutes les rubriques pour le service d’entrepôt de données SQL Azure

Cette rubrique répertorie toutes les rubriques qui s’applique directement au service **SQL Data Warehouse** d’Azure. Vous pouvez rechercher cette page Web pour les mots clés à l’aide de **Ctrl + F**, pour trouver les sujets d’intérêt en cours.




## <a name="new"></a>Nouveau

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 1 | [Sauvegardes SQL Data Warehouse](sql-data-warehouse-backups.md) | Obtenir des informations sur les sauvegardes de base de données intégrée SQL Data Warehouse qui vous permettent de restaurer un entrepôt de données de SQL Azure à un point de restauration ou d’une région géographique différente. |


## <a name="updated-articles-sql-data-warehouse"></a>Articles mis à jour, SQL Data Warehouse

Cette section répertorie les articles qui ont été mis à jour récemment, où la mise à jour a été grand ou importantes. Pour chaque article mis à jour, un extrait de code approximative du texte démarque supplémentaire s’affiche. Les articles ont été mis à jour au sein de la plage de dates de **2016-08-22** pour **2016-10-05**.

| &nbsp; | Article | Texte mis à jour, l’extrait de code | Mise à jour lorsque |
| --: | :-- | :-- | :-- |
| 2 | [Charger des données depuis le stockage blob Azure dans l’entrepôt de données SQL (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | /-Pour suivre les octets et les fichiers r.command SELECT, s.request_id, r.status, count (distinct input_name) en tant que nbr_files, sum (s.bytes_processed) / 1024/1024 en tant que gb_processed de sys.dm_pdw_exec_requests r jointure interne sys.dm_pdw_dms_external_work s sur r.request_id = s.request_id où r. Label = ' SACT : charge cso. DimProduct ' ou r. Label = ' SACT : charge cso. GROUP BY r.command des FactOnlineSales, s.request_id, r.status ORDER BY nbr_files desc, gb_processed, desc ;  | 2016-09-07 |
| 3 | [Restauration du magasin de données SQL](sql-data-warehouse-restore-database-overview.md) | **Est-il possible de restaurer un entrepôt de données en pause ?** Pour restaurer un data warehouse qui est suspendu, vous devez tout d’abord le mettre en ligne. Une fois que l’entrepôt de données est en ligne, vous avez des points de restauration de sept jours. **Restaurer à une région géographique redondant** Si vous utilisez le stockage redondant géographique, vous pouvez restaurer l’entrepôt de données pour votre centre de données associés dans une région géographique différente. L’entrepôt de données est restaurée à partir de la dernière sauvegarde quotidienne. **Restaurer la barre de planning** Vous pouvez restaurer une base de données à n’importe quel point de restauration dans les sept derniers jours. Des snapshots toutes les quatre à huit heures et sont disponibles pendant sept jours. Lorsqu’une capture instantanée est plue de sept jours, elle expire et son point de restauration n’est plus disponible. **Les coûts de restauration** Les frais de stockage pour l’entrepôt de données restaurées sont facturé au taux de prime le stockage Azure. Si vous suspendez un entrepôt de données restaurées, vous sont facturés pour le stockage au taux de prime le stockage Azure. L’avantage de la suspension est que vous ne sont pas des frais | 2016-09-29 |





## <a name="get-started"></a>Mise en route

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 4 | [Authentification à l’entrepôt de données SQL Azure](sql-data-warehouse-authentication.md) | Authentification Active Directory (DAS) et SQL Server Azure à l’entrepôt de données SQL Azure. |
| 5 | [Meilleures pratiques pour l’entrepôt de données SQL Azure](sql-data-warehouse-best-practices.md) | Recommandations et méthodes conseillées que vous devez savoir que vous développent des solutions pour l’entrepôt de données SQL Azure. Ils vous aideront à réussir. |
| 6 | [Pilotes pour l’entrepôt de données SQL Azure](sql-data-warehouse-connection-strings.md) | Chaînes de connexion et des pilotes pour l’entrepôt de données SQL |
| 7 | [Se connecter au magasin de données SQL Azure](sql-data-warehouse-connect-overview.md) | Comment faire pour rechercher le serveur de connexion et le nom de chaîne pour votre à l’entrepôt de données SQL Azure |
| 8 | [Analyser des données avec Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) | Pour créer une machine prédictive apprentissage du modèle basé sur les données stockées dans l’entrepôt de données de SQL Azure, utilisez Azure Machine d’apprentissage. |
| 9 | [Interroger le magasin de données SQL Azure (sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) | Interrogation d’entrepôt de données de SQL Azure avec l’utilitaire de ligne de commande de sqlcmd. |
| 10 | [Créer une base de données SQL Data Warehouse à l’aide de Transact-SQL (TSQL).](sql-data-warehouse-get-started-create-database-tsql.md) | Apprenez à créer un entrepôt de données de SQL Azure avec TSQL |
| 11 | [Comment faire pour créer un ticket de support pour l’entrepôt de données SQL](sql-data-warehouse-get-started-create-support-ticket.md) | Procédure de création d’un ticket de support dans l’entrepôt de données SQL Azure. |
| 12 | [Charger les données avec Azure Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md) | Apprenez à charger les données avec Azure Data Factory |
| 13 | [Charger les données avec PolyBase dans le magasin de données SQL](sql-data-warehouse-get-started-load-with-polybase.md) | Découvrez les nouveautés de PolyBase et son utilisation pour les scénarios de Data warehousing. |
| 14 | [Créer un entrepôt de données SQL Azure](sql-data-warehouse-get-started-provision.md) | Apprenez à créer un entrepôt de données SQL Azure dans le portail Azure |
| 15 | [Créer SQL Data Warehouse à l’aide de PowerShell](sql-data-warehouse-get-started-provision-powershell.md) | Créer un magasin de données SQL à l’aide de PowerShell |
| 16 | [Visualiser les données avec alimentation BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) | Visualiser les données de magasin de données SQL avec alimentation BI |
| 17 | [Requête SQL Azure Data Warehouse (Visual Studio)](sql-data-warehouse-query-visual-studio.md) | Requête SQL Data Warehouse avec Visual Studio. |



## <a name="develop"></a>Développer

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 18 | [Optimisation des transactions pour SQL Data Warehouse](sql-data-warehouse-develop-best-practices-transactions.md) | Méthodes recommandées sur l’écriture de mises à jour de transaction efficace dans l’entrepôt de données SQL Azure |
| 19 | [Gestion d’accès concurrentiel et la charge de travail dans l’entrepôt de données SQL](sql-data-warehouse-develop-concurrency.md) | Comprendre la gestion d’accès concurrentiel et la charge de travail dans l’entrepôt de données SQL Azure pour le développement de solutions. |
| 20 | [Créer la Table comme Select (SACT) dans le magasin de données SQL](sql-data-warehouse-develop-ctas.md) | Conseils de codage avec la créer une table en tant qu’instruction select de (SACT) dans le magasin de données SQL Azure pour le développement de solutions. |
| 21 | [SQL dynamique dans l’entrepôt de données SQL](sql-data-warehouse-develop-dynamic-sql.md) | Conseils pour l’utilisation de SQL dynamique dans l’entrepôt de données SQL Azure pour le développement de solutions. |
| 22 | [Regrouper par options SQL Data Warehouse](sql-data-warehouse-develop-group-by-options.md) | Conseils pour l’implémentation de groupe par des options dans l’entrepôt de données SQL Azure pour le développement de solutions. |
| 23 | [Utiliser des étiquettes pour les requêtes d’instrument dans le magasin de données SQL](sql-data-warehouse-develop-label.md) | Conseils pour l’utilisation d’étiquettes aux requêtes d’instrument dans le magasin de données SQL Azure pour le développement de solutions. |
| 24 | [Boucles dans l’entrepôt de données SQL](sql-data-warehouse-develop-loops.md) | Conseils pour les boucles de Transact-SQL et les curseurs de remplacement dans l’entrepôt de données SQL Azure pour le développement de solutions. |
| 25 | [Procédures stockées dans l’entrepôt de données SQL](sql-data-warehouse-develop-stored-procedures.md) | Conseils pour l’implémentation de procédures stockées dans l’entrepôt de données SQL Azure pour le développement de solutions. |
| 26 | [Transactions dans le magasin de données SQL](sql-data-warehouse-develop-transactions.md) | Conseils pour l’implémentation des transactions dans le magasin de données SQL Azure pour le développement de solutions. |
| 27 | [Schémas définis par l’utilisateur dans le magasin de données SQL](sql-data-warehouse-develop-user-defined-schemas.md) | Conseils d’utilisation des schémas de Transact-SQL dans le magasin de données SQL Azure pour le développement de solutions. |
| 28 | [Assigner des variables SQL Data Warehouse](sql-data-warehouse-develop-variable-assignment.md) | Conseils pour l’affectation des variables Transact-SQL dans le magasin de données SQL Azure pour le développement de solutions. |
| 29 | [Vues SQL Data Warehouse](sql-data-warehouse-develop-views.md) | Conseils d’utilisation des vues de Transact-SQL dans le magasin de données SQL Azure pour le développement de solutions. |
| 30 | [Décisions de conception et les techniques de codage pour l’entrepôt de données SQL](sql-data-warehouse-overview-develop.md) | Concepts de développement, des décisions de conception, des recommandations et des techniques de codage pour l’entrepôt de données SQL. |



## <a name="manage"></a>Gérer les

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 31 | [Gérer la puissance de calcul dans l’entrepôt de données SQL Azure (présentation)](sql-data-warehouse-manage-compute-overview.md) | Échelle de performances des fonctionnalités dans l’entrepôt de données SQL Azure. Évoluer en ajustant la DWUs ou de suspendre et de reprendre des ressources informatiques pour réduire les coûts. |
| 32 | [Gérer la puissance de calcul dans l’entrepôt de données SQL Azure (Azure portal)](sql-data-warehouse-manage-compute-portal.md) | Tâches du portail Azure pour gérer grande puissance de traitement. Échelle des ressources de calcul en ajustant les DWUs. Ou bien, suspendre et reprendre les ressources pour réduire les coûts de calcul. |
| 33 | [Gérer la puissance de calcul dans l’entrepôt de données SQL Azure (PowerShell)](sql-data-warehouse-manage-compute-powershell.md) | Pour gérer les tâches de PowerShell grande puissance de traitement. Échelle des ressources de calcul en ajustant les DWUs. Ou bien, suspendre et reprendre les ressources pour réduire les coûts de calcul. |
| 34 | [Gérer la puissance de calcul dans des entrepôts de données SQL (reste) de Azure](sql-data-warehouse-manage-compute-rest-api.md) | Pour gérer les tâches de PowerShell grande puissance de traitement. Échelle des ressources de calcul en ajustant les DWUs. Ou bien, suspendre et reprendre les ressources pour réduire les coûts de calcul. |
| 35 | [Gérer la puissance de calcul dans l’entrepôt de données SQL Azure (T-SQL)](sql-data-warehouse-manage-compute-tsql.md) | Tâches (T-SQL) de Transact-SQL pour des performances évolutives en ajustant les DWUs. Réduire les coûts par la mise à l’échelle pendant les heures creuses. |
| 36 | [Surveillez votre charge de travail à l’aide de la DMV](sql-data-warehouse-manage-monitor.md) | Apprenez à surveiller votre charge de travail à l’aide de la DMV. |
| 37 | [Gérer les bases de données dans l’entrepôt de données SQL Azure](sql-data-warehouse-overview-manage.md) | Vue d’ensemble de la gestion des bases de données SQL Data Warehouse. Inclut des outils de gestion, de DWUs et des performances évolutives, résolution des problèmes de performances des requêtes, établissement de stratégies de sécurité et de la restauration d’une base de données à partir de la corruption des données ou d’une panne régionale. |
| 38 | [Surveiller les requêtes de l’utilisateur dans le magasin de données SQL Azure](sql-data-warehouse-overview-manage-user-queries.md) | Vue d’ensemble des considérations, meilleures pratiques et des tâches pour surveiller les requêtes de l’utilisateur dans le magasin de données SQL Azure |
| 39 | [Restauration du magasin de données SQL](sql-data-warehouse-restore-database-overview.md) | Vue d’ensemble des options de restauration de base de données pour la récupération d’une base de données dans l’entrepôt de données SQL Azure. |
| 40 | [Restauration d’un entrepôt de données SQL Azure (portail)](sql-data-warehouse-restore-database-portal.md) | Tâches du portail Azure pour la restauration d’un entrepôt de données SQL Azure. |
| 41 | [Restauration d’un entrepôt de données SQL Azure (PowerShell)](sql-data-warehouse-restore-database-powershell.md) | Tâches de PowerShell pour la restauration d’un entrepôt de données SQL Azure. |
| 42 | [Restauration d’un entrepôt de données SQL Azure (API REST)](sql-data-warehouse-restore-database-rest-api.md) | Tâches d’API REST de restauration d’un entrepôt de données SQL Azure. |



## <a name="tables-and-indexes"></a>Tables et index

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 43 | [Types de données pour les tables SQL Data Warehouse](sql-data-warehouse-tables-data-types.md) | Mise en route avec les types de données pour les tables de l’entrepôt de données SQL Azure. |
| 44 | [Distribution de tables SQL Data Warehouse](sql-data-warehouse-tables-distribute.md) | Mise en route de la distribution de tables dans l’entrepôt de données SQL Azure. |
| 45 | [L’indexation des tables SQL Data Warehouse](sql-data-warehouse-tables-index.md) | Mise en route de la table d’indexation dans le magasin de données SQL Azure. |
| 46 | [Vue d’ensemble des tables SQL Data Warehouse](sql-data-warehouse-tables-overview.md) | Mise en route avec les Tables de Data Warehouse SQL Azure. |
| 47 | [Partitionnement de tables SQL Data Warehouse](sql-data-warehouse-tables-partition.md) | Mise en route de partitionnement de table dans le magasin de données SQL Azure. |
| 48 | [Gestion des statistiques sur les tables SQL Data Warehouse](sql-data-warehouse-tables-statistics.md) | Mise en route avec les statistiques sur les tables dans le magasin de données SQL Azure. |
| 49 | [Tables temporaires de SQL Data Warehouse](sql-data-warehouse-tables-temporary.md) | Mise en route avec des tables temporaires dans l’entrepôt de données SQL Azure. |



## <a name="integrate"></a>Intégrer

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 50 | [Utiliser le Factory de données Azure avec SQL Data Warehouse](sql-data-warehouse-integrate-azure-data-factory.md) | Conseils pour l’utilisation de fabrique de données Azure (CAD) à l’entrepôt de données SQL Azure pour le développement de solutions. |
| 51 | [Utiliser l’apprentissage automatique Azure avec SQL Data Warehouse](sql-data-warehouse-integrate-azure-machine-learning.md) | Didacticiel pour l’utilisation de formation de Machine Azure avec l’entrepôt de données SQL Azure pour le développement de solutions. |
| 52 | [Utiliser les flux Azure Analytique avec SQL Data Warehouse](sql-data-warehouse-integrate-azure-stream-analytics.md) | Conseils pour l’utilisation d’Azure flux Analytique avec l’entrepôt de données SQL Azure pour le développement de solutions. |
| 53 | [Utiliser la BI de puissance avec SQL Data Warehouse](sql-data-warehouse-integrate-power-bi.md) | Conseils pour l’utilisation de puissance BI à l’entrepôt de données SQL Azure pour le développement de solutions. |
| 54 | [Tirer parti des autres services avec SQL Data Warehouse](sql-data-warehouse-overview-integrate.md) | Les outils et les partenaires grâce à des solutions qui s’intègrent avec SQL Data Warehouse.  |



## <a name="load"></a>Charge

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 55 | [Charger des données depuis le stockage blob Azure dans l’entrepôt de données SQL Azure (Azure Data Factory)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) | Apprenez à charger les données avec Azure Data Factory |
| 56 | [Charger des données depuis le stockage blob Azure dans l’entrepôt de données SQL (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | Découvrez comment utiliser PolyBase pour charger des données depuis le stockage blob Azure dans l’entrepôt de données SQL. Charger les quelques tables de données publiques dans le schéma d’entrepôt de données de vente au détail de Contoso. |
| 57 | [Charger des données à partir de SQL Server dans l’entrepôt de données SQL Azure (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) | Utilise l’utilitaire bcp pour exporter des données à partir de SQL Server vers des fichiers plats, AZCopy pour importer des données dans le stockage blob Azure et PolyBase pour recevoir les données dans l’entrepôt de données SQL Azure. |
| 58 | [Charger des données à partir de SQL Server dans l’entrepôt de données SQL Azure (fichiers plats)](sql-data-warehouse-load-from-sql-server-with-bcp.md) | Pour une taille de données de petite taille, utilise bcp pour exporter des données à partir de SQL Server pour les fichiers plats et importer les données directement dans le magasin de données SQL Azure. |
| 59 | [Charger des données à partir de SQL Server dans des entrepôts de données SQL (SSIS) de Azure](sql-data-warehouse-load-from-sql-server-with-integration-services.md) | Vous montre comment créer un package SQL Server Integration Services (SSIS) pour déplacer des données à partir d’un large éventail de sources de données SQL Data Warehouse. |
| 60 | [Charger les données avec PolyBase dans le magasin de données SQL](sql-data-warehouse-load-from-sql-server-with-polybase.md) | Utilise l’utilitaire bcp pour exporter des données à partir de SQL Server vers des fichiers plats, AZCopy pour importer des données dans le stockage blob Azure et PolyBase pour recevoir les données dans l’entrepôt de données SQL Azure. |
| 61 | [Guide d’utilisation de PolyBase dans l’entrepôt de données SQL](sql-data-warehouse-load-polybase-guide.md) | Instructions et recommandations sur l’utilisation de PolyBase dans les scénarios de Data Warehouse de SQL. |
| 62 | [Charger des exemples de données dans Data Warehouse de SQL](sql-data-warehouse-load-sample-databases.md) | Charger des exemples de données dans Data Warehouse de SQL |
| 63 | [Charger les données avec bcp](sql-data-warehouse-load-with-bcp.md) | Découvrez ce que bcp et comment l’utiliser pour les scénarios de Data warehousing. |
| 64 | [Charger des données dans l’entrepôt de données SQL Azure](sql-data-warehouse-overview-load.md) | Découvrez les scénarios courants pour les données de chargement dans le Data Warehouse de SQL. Celles-ci incluent l’utilisation de la PolyBase, stockage blob Azure, fichiers plats et l’expédition du disque. Vous pouvez également utiliser des outils tiers. |



## <a name="migrate"></a>Effectuer la migration

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 65 | [Migration du code SQL pour SQL Data Warehouse](sql-data-warehouse-migrate-code.md) | Conseils pour la migration de votre code SQL pour l’entrepôt de données SQL Azure pour le développement de solutions. |
| 66 | [Migration de vos données](sql-data-warehouse-migrate-data.md) | Conseils pour la migration de vos données de l’entrepôt de données SQL Azure pour le développement de solutions. |
| 67 | [Utilitaire de Migration de magasin de données (aperçu)](sql-data-warehouse-migrate-migration-utility.md) | Migrer vers le magasin de données SQL. |
| 68 | [Migrer votre schéma d’entrepôt de données SQL](sql-data-warehouse-migrate-schema.md) | Conseils pour la migration de votre schéma d’entrepôt de données SQL Azure pour le développement de solutions. |
| 69 | [Migration de votre solution d’entrepôt de données de SQL](sql-data-warehouse-overview-migrate.md) | Guide de migration pour mettre votre solution à la plate-forme de l’entrepôt de données SQL Azure. |



## <a name="partners"></a>Partenaires

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 70 | [Partenaires de SQL Data Warehouse business intelligence](sql-data-warehouse-partner-business-intelligence.md) | Listes des tiers intelligence les partenaires grâce à des solutions prenant en charge SQL Data Warehouse. |
| 71 | [Partenaires d’intégration de données de magasin de données SQL](sql-data-warehouse-partner-data-integration.md) | Listes de partenaires tiers avec les solutions d’intégration de données qui prennent en charge le magasin de données SQL Azure. |
| 72 | [Partenaires de gestion de données SQL Data Warehouse](sql-data-warehouse-partner-data-management.md) | Liste des partenaires de gestion de données tiers grâce à des solutions prenant en charge SQL Data Warehouse. |



## <a name="reference"></a>Référence

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 73 | [Rubriques de référence pour l’entrepôt de données SQL](sql-data-warehouse-overview-reference.md) | Liens de contenu de référence pour l’entrepôt de données SQL. |
| 74 | [Applets de commande PowerShell et le reste des API pour l’entrepôt de données SQL](sql-data-warehouse-reference-powershell-cmdlets.md) | Rechercher les applets de commande PowerShell supérieure pour l’entrepôt de données de SQL Azure notamment comment suspendre et reprendre une base de données. |
| 75 | [Éléments de langage](sql-data-warehouse-reference-tsql-language-elements.md) | Liste de liens vers le contenu de référence pour les éléments du langage Transact-SQL utilisée pour l’entrepôt de données SQL. |
| 76 | [Rubriques de Transact-SQL](sql-data-warehouse-reference-tsql-statements.md) | Liens vers le contenu de référence pour les rubriques de Transact-SQL utilisé par SQL Data Warehouse. |
| 77 | [Vues système](sql-data-warehouse-reference-tsql-system-views.md) | Des liens vers le système affiche le contenu de l’entrepôt de données SQL. |



## <a name="security"></a>Sécurité

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 78 | [SQL Data Warehouse - prend en charge les clients de bas niveau pour les données d’audit et dynamiques de masquage](sql-data-warehouse-auditing-downlevel-clients.md) | En savoir plus sur la prise en charge de clients de bas niveau SQL Data Warehouse pour l’audit des données |
| 79 | [L’audit dans l’entrepôt de données SQL Azure](sql-data-warehouse-auditing-overview.md) | Mise en route de l’audit dans l’entrepôt de données SQL Azure |
| 80 | [Mise en route avec des Transparent données cryptage (TDE) dans le magasin de données SQL](sql-data-warehouse-encryption-tde.md) | Chiffrement transparent des données (TDE) dans le magasin de données SQL |
| 81 | [Mise en route avec le chiffrement de données Transparent (TDE)](sql-data-warehouse-encryption-tde-tsql.md) | Chiffrement transparent des données (TDE) dans le magasin de données SQL (T-SQL) |
| 82 | [Sécuriser une base de données SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md) | Conseils sur la sécurisation d’une base de données dans l’entrepôt de données SQL Azure pour le développement de solutions. |



## <a name="miscellaneous"></a>Divers

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 83 | [Installer Visual Studio 2015 et SSDT pour l’entrepôt de données SQL](sql-data-warehouse-install-visual-studio.md) | Installation de Visual Studio et les outils de développement SQL Server (SSDT) pour l’entrepôt de données SQL Azure |
| 84 | [Migration vers les détails de stockage Premium](sql-data-warehouse-migrate-to-premium-storage.md) | Instructions pour la migration d’un entrepôt de données SQL existant pour le stockage de la prime |
| 85 | [Mise en route de la détection des menaces](sql-data-warehouse-security-threat-detection.md) | La mise en route avec la détection de la menace |
| 86 | [Limites de capacité d’entrepôt de données SQL](sql-data-warehouse-service-capacity-limits.md) | Valeurs maximales pour les connexions, les bases de données, les tables et les requêtes SQL Data Warehouse. |
| 87 | [Dépannage de SQL Azure Data Warehouse](sql-data-warehouse-troubleshoot.md) | Dépannage de SQL Azure Data Warehouse. |

