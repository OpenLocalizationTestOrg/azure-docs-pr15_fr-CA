<properties 
   pageTitle="Copier des données entre un magasin de données LAC et base de données Azure SQL à l’aide de Sqoop | Microsoft Azure"
   description="Permet de copier des données entre la base de données de SQL Azure et le magasin de données lac Sqoop" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>Copier des données entre un magasin de données LAC et base de données Azure SQL à l’aide de Sqoop

Découvrez comment utiliser Apache Sqoop pour importer et exporter des données entre la base de données de SQL Azure et le magasin de données lac.
 

## <a name="what-is-sqoop"></a>Quel est Sqoop ?

Les applications de données volumineuses sont un choix naturel pour le traitement des données non structurées et semi-structurées, tels que les journaux et les fichiers. Toutefois, il peut également être nécessaire de traiter les données structurées qui sont stockées dans les bases de données relationnelles.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) est un outil conçu pour transférer des données entre les bases de données relationnelles et un référentiel de données volumineuses, comme magasin de données lac. Vous pouvez l’utiliser pour importer des données à partir d’un système de gestion de base de données relationnelle (SGBDR) comme base de données de SQL Azure dans le magasin de données lac. Vous pouvez ensuite transformer et analyser les données à l’aide de charges de travail de données volumineuses et puis d’exporter les données dans un SGBDR. Dans ce didacticiel, vous utilisez une base de données de SQL Azure en tant que votre base de données relationnelle à importer/exporter à partir de.
 

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- **Activer votre abonnement Azure** pour la présentation publique de magasin de données lac. Consultez les [instructions](data-lake-store-get-started-portal.md#signup). 
- **HDInsight d’azure cluster** d’accéder à un compte de banque de données lac. Voir [Création d’un cluster de HDInsight avec le magasin de données lac](data-lake-store-hdinsight-hadoop-use-portal.md). Cet article suppose que vous disposez d’un cluster de HDInsight Linux avec accès du magasin de données lac.
- **Base de données SQL azure**. Pour obtenir des instructions sur la façon d’en créer un, voir [Création d’une base de données SQL d’Azure](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Vous découvrez rapidement avec les vidéos ?

[Regardez cette vidéo](https://mix.office.com/watch/1butcdjxmu114) sur la copie de données entre objets BLOB de stockage Azure et magasin de LAC de données à l’aide de DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Créer des exemples de tables dans la base de données de SQL Azure

1. Commencez par créer deux exemples de tables dans la base de données de SQL Azure. Pour se connecter à la base de données de SQL Azure et d’exécuter les requêtes suivantes, utilisez [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou Visual Studio.

    **Créer la Table1**

        CREATE TABLE [dbo].[Table1]( 
        [ID] [int] NOT NULL, 
        [FName] [nvarchar](50) NOT NULL, 
        [LName] [nvarchar](50) NOT NULL, 
        CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
            ( 
                [ID] ASC 
            ) 
        ) ON [PRIMARY] 
        GO

    **Créer la Table2**

        CREATE TABLE [dbo].[Table2]( 
        [ID] [int] NOT NULL, 
        [FName] [nvarchar](50) NOT NULL, 
        [LName] [nvarchar](50) NOT NULL, 
        CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
            ( 
                [ID] ASC 
            ) 
        ) ON [PRIMARY] 
        GO

2. Dans **Table1**, ajoutez des exemples de données. Renseignez la **Table2** . Nous allons importer des données à partir de **Table1** dans le magasin de données lac. Ensuite, nous va exporter des données à partir du magasin de données lac vers **Table2**. Exécutez l’extrait de code suivant.

         
        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson'); 
  

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>Sqoop d’utilisation d’un cluster de HDInsight disposant d’un accès au magasin de données lac

Un cluster HDInsight possède déjà les packages Sqoop disponibles. Si vous avez configuré le cluster HDInsight pour utiliser le magasin de données lac comme un espace de stockage supplémentaire, vous pouvez utiliser Sqoop (sans aucune modification de configuration) pour importer/exporter des données entre une base de données relationnelle (dans cet exemple, la base de données de SQL Azure) et un compte de banque de données lac. 

1. Pour ce didacticiel, nous supposons que vous avez créé un cluster Linux, vous devez utiliser SSH pour se connecter au cluster. Voir [se connecter à un cluster basé sur Linux de HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).

2. Vérifiez si vous pouvez accéder le compte de la banque de données lac du cluster. Exécutez la commande suivante à l’invite SSH :

        
        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    Il doit fournir une liste de fichiers/dossiers dans le compte de la banque de données lac.

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>Importer des données à partir de la base de données de SQL Azure dans le magasin de données lac

3. Accédez au répertoire dans lequel les packages de Sqoop sont disponibles. En général, ce sera à `/usr/hdp/<version>/sqoop/bin`. 

4. Importer les données dans le compte de la banque de données lac **Table1** . Utilisez la syntaxe suivante :

        
        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Notez que cet espace réservé **sql-base de données-server-name** représente le nom du serveur où la base de données Azure SQL est en cours d’exécution. espace réservé du **nom de base de données SQL** représente le nom de la base de données.

    Par exemple,

        
        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

5. Vérifiez que les données ont été transférées vers le compte de la banque de données lac. Exécutez la commande suivante :

        
        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Vous devez voir la sortie suivante.

        
        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Chaque **partie-m -** * fichier correspond à une ligne de la table source, * *Table1**. Vous pouvez afficher le contenu de la partie-m -* fichiers à vérifier.


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>Exporter les données de magasin de données Lake dans la base de données de SQL Azure

6. Exporter les données de compte de la banque de données LAC pour la table vide, **Table2**, dans la base de données de SQL Azure. Utilisez la syntaxe suivante.

        
        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Par exemple,

        
        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

6. Vérifiez que les données à la table de base de données SQL a été téléchargées. Utilisez [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou Visual Studio de se connecter à la base de données de SQL Azure et d’exécuter la requête suivante.

        
        SELECT * FROM TABLE2

    La sortie suivante doit être.

        ID  FName   LName
        ------------------
        1   Neal    Kell
        2   Lila    Fulton
        3   Erna    Myers
        4   Annette Simpson

## <a name="see-also"></a>Voir aussi

- [Copier des données d’objets BLOB de stockage Azure au magasin de données lac](data-lake-store-copy-data-azure-storage-blob.md)
- [Sécuriser les données dans le magasin de données lac](data-lake-store-secure-data.md)
- [Utiliser des données Azure lac Analytique avec le magasin de données lac](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilisez HDInsight Azure avec le magasin de données lac](data-lake-store-hdinsight-hadoop-use-portal.md)
