   <properties
   pageTitle="Charger des données dans l’entrepôt de données SQL Azure | Microsoft Azure"
   description="Découvrez les scénarios courants pour les données de chargement dans le Data Warehouse de SQL. Celles-ci incluent l’utilisation de la PolyBase, stockage blob Azure, fichiers plats et l’expédition du disque. Vous pouvez également utiliser des outils tiers."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/12/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="load-data-into-azure-sql-data-warehouse"></a>Charger des données dans l’entrepôt de données SQL Azure

Un résumé des options de scénario et les recommandations pour le chargement de données dans l’entrepôt de données SQL.

Les plus difficiles de chargement des données sont généralement préparer les données pour la charge. Azure simplifie le chargement en utilisant le stockage blob Azure sous la forme d’une banque de données commune pour la plupart des services et Azure Data Factory pour orchestrer la communication et déplacement des données entre des services Azure. Ces processus sont intégrés avec la technologie de PolyBase qui utilise le traitement massivement parallèle (MPP) pour charger les données en parallèle depuis le stockage blob Azure dans SQL Data Warehouse. 

Pour des didacticiels qui chargent des bases de données exemples, voir [charger les bases de données exemple][].

## <a name="load-from-azure-blob-storage"></a>Charger depuis le stockage blob Azure
Le moyen le plus rapide pour importer des données dans Data Warehouse de SQL consiste à utiliser des PolyBase pour charger des données depuis le stockage blob Azure. PolyBase utilise de SQL Data Warehouse traitement massivement parallèle (MPP) pour charger des données en parallèle à partir de stockage blob Azure. Pour utiliser PolyBase, vous pouvez utiliser les commandes T-SQL ou un pipeline Azure Data Factory.

### <a name="1-use-polybase-and-t-sql"></a>1. Utilisez le PolyBase et T-SQL

Résumé du processus de chargement :

2. Mettre en forme vos données au format UTF-8 depuis PolyBase ne prend pas en charge UTF-16.
2. Déplacer vos données dans le stockage blob Azure et le stocker dans des fichiers texte.
3. Configurer des objets externes dans l’entrepôt de données SQL pour définir l’emplacement et le format des données
4. Exécuter une commande T-SQL pour charger les données en parallèle dans une nouvelle table de la base de données.

<!-- 5. Schedule and run a loading job. --> 

Pour un didacticiel, consultez [chargement de données depuis le stockage blob Azure à SQL Data Warehouse (PolyBase)][].

### <a name="2-use-azure-data-factory"></a>2. utiliser le Factory de données Azure

Pour un moyen plus simple d’utiliser PolyBase, vous pouvez créer un pipeline Azure Data Factory qui utilise PolyBase pour charger des données depuis le stockage blob Azure dans l’entrepôt de données SQL. Il est rapide à configurer dans la mesure où vous n’êtes pas obligé de définir les objets T-SQL. Si vous avez besoin interroger les données externes sans les importer, utilisez T-SQL. 

Résumé du processus de chargement :

2. Mettre en forme vos données au format UTF-8 depuis PolyBase ne prend pas en charge UTF-16.
2. Déplacer vos données dans le stockage blob Azure et le stocker dans des fichiers texte.
3. Créer un pipeline Azure Data Factory pour réceptionner les données. Utilisez l’option PolyBase.
4. Planifier et exécuter le pipeline.

Pour un didacticiel, consultez [chargement de données depuis le stockage blob Azure à SQL Data Warehouse (Azure Data Factory)][].


## <a name="load-from-sql-server"></a>Charger à partir de SQL Server
Pour charger des données à partir de SQL Server vers SQL Data Warehouse, vous pouvez utiliser Integration Services (SSIS), transférer des fichiers à plat ou disques de navire à Microsoft. Lecture à voir un résumé des différents du chargement de processus et des liens vers des didacticiels.

Pour planifier une migration complète des données à partir de SQL Server vers SQL Data Warehouse, consultez la [vue d’ensemble de la Migration][]. 

### <a name="use-integration-services-ssis"></a>Utiliser Integration Services (SSIS)
Si vous utilisez déjà des packages Integration Services (SSIS) pour charger dans SQL Server, vous pouvez mettre à jour vos packages afin d’utiliser SQL Server comme source et SQL Data Warehouse comme destination. C’est rapide et facile à faire, et est un bon choix si vous ne voulez pas migrer votre processus de chargement pour utiliser déjà des données dans le nuage. Le compromis est que la charge sera plus lente qu’avec PolyBase, car cette SSIS n’effectue pas le chargement en parallèle.

Résumé du processus de chargement :

1. Réviser votre package Integration Services pour pointer vers l’instance de SQL Server pour la source et la base de données SQL Data Warehouse de destination.
2. Migrer votre schéma d’entrepôt de données SQL, si elle n'existe pas déjà.
3. Modifier le mappage dans vos packages d’utiliser uniquement les types de données pris en charge par le magasin de données SQL.
3. Planifier et exécuter le package.

Pour un didacticiel, consultez [chargement de données à partir de SQL Server à Azure SQL données entrepôt (SSIS)][].

### <a name="use-azcopy-recommended-for--10-tb-data"></a>Utilisez AZCopy (recommandé pour < 10 To de données)
Si le format de données est < 10 To, vous pouvez exporter les données à partir de SQL Server pour les fichiers plats, copiez les fichiers dans le stockage blob Azure et puis utiliser PolyBase pour charger les données dans l’entrepôt de données de SQL

Résumé du processus de chargement :

1. Utilisez l’utilitaire de ligne de commande bcp pour exporter des données à partir de SQL Server vers des fichiers plats.
2. Utilisez l’utilitaire de ligne de commande AZCopy pour copier les données de fichiers plats pour le stockage des objets blob Azure.
3. PolyBase permet de charger dans le magasin de données SQL.

Pour un didacticiel, consultez [chargement de données depuis le stockage blob Azure à SQL Data Warehouse (PolyBase)][].

### <a name="use-bcp"></a>Utilisez le programme bcp
Si vous avez une petite quantité de données vous pouvez utiliser bcp pour charger directement dans l’entrepôt de données SQL Azure.

Résumé du processus de chargement :
1. Utilisez l’utilitaire de ligne de commande bcp pour exporter des données à partir de SQL Server vers des fichiers plats.
2. Utilisez le programme bcp pour charger des données à partir de fichiers plats directement au magasin de données SQL.

Pour un didacticiel, consultez [chargement de données à partir de SQL Server vers Data Warehouse de SQL Azure (bcp)][].


### <a name="use-importexport-recommended-for--10-tb-data"></a>Utilisez Import/Export (recommandé pour > 10 To de données)
Si la taille de vos données est > 10 To et que vous souhaitez déplacer vers Azure, nous vous recommandons d’utiliser notre service [d’Importation/exportation][]d’expédition de disque. 

Résumé du processus de chargement
2. Utilisez l’utilitaire de ligne de commande bcp pour exporter des données à partir de SQL Server vers des fichiers plats sur disques transférables.
3. Expédier les disques à Microsoft.
4. Microsoft a charge les données dans l’entrepôt de données SQL

## <a name="load-from-hdinsight"></a>Charger à partir de HDInsight
SQL Data Warehouse prend en charge le chargement des données à partir de HDInsight de via PolyBase. Le processus est le même que le chargement des données depuis le stockage Blob Azure - pour se connecter à HDInsight pour charger des données à l’aide de PolyBase. 

### <a name="1-use-polybase-and-t-sql"></a>1. Utilisez le PolyBase et T-SQL

Résumé du processus de chargement :

2. Mettre en forme vos données au format UTF-8 depuis PolyBase ne prend pas en charge UTF-16.
2. Déplacer vos données dans HDInsight et les stocker dans des fichiers texte, format ORC ou Parquet.
3. Configurer des objets externes dans l’entrepôt de données SQL pour définir l’emplacement et le format des données.
4. Exécuter une commande T-SQL pour charger les données en parallèle dans une nouvelle table de la base de données.

Pour un didacticiel, consultez [chargement de données depuis le stockage blob Azure à SQL Data Warehouse (PolyBase)][].

## <a name="recommendations"></a>Recommandations

La plupart de nos partenaires ont le chargement des solutions. Pour en savoir plus, consultez une liste de nos [partenaires de solutions][]. 

Si vos données proviennent d’une source non relationnelles et que vous souhaitez charger dans SQL Data Warehouse, vous devez transformer en lignes et les colonnes avant de le charger. Les données transformées n’a pas besoin d’être stockées dans une base de données, il peut être stocké dans des fichiers texte.

Créer des statistiques sur les données qui vient d’être chargées. Entrepôt de données SQL Azure est pas encore prise en charge automatique créer ou automatique des statistiques de mise à jour.  Pour obtenir les meilleures performances de vos requêtes, il est important de créer des statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou apporter aucune modification substantielle se produire dans les données.  Pour plus d’informations, reportez-vous à la section [statistiques][].


## <a name="next-steps"></a>Étapes suivantes
Pour plus de conseils de développement, consultez la [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[Charger des données depuis le stockage blob Azure à SQL Data Warehouse (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Charger des données depuis le stockage blob Azure à SQL Data Warehouse (Azure Data Factory)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Charger des données à partir de SQL Server à Azure SQL données entrepôt (SSIS)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Charger des données à partir de SQL Server vers Data Warehouse de SQL Azure (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Charger des bases de données exemples]: ./sql-data-warehouse-load-sample-databases.md
[Vue d’ensemble de la migration]: ./sql-data-warehouse-overview-migrate.md
[partenaires de solutions]: ./sql-data-warehouse-partner-business-intelligence.md
[vue d’ensemble du développement]: ./sql-data-warehouse-overview-develop.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Importation/exportation]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/
