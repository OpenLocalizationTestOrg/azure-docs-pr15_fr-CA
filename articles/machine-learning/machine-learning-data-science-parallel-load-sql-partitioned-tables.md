<properties 
    pageTitle="Importation de données en bloc à l’aide de Tables de Partition SQL en parallèle | Microsoft Azure" 
    description="Importation de données en bloc parallèles à l’aide de Tables de Partition SQL" 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev"
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="bradsev" /> 

# <a name="parallel-bulk-data-import-using-sql-partition-tables"></a>Importation de données en bloc parallèles à l’aide de Tables de Partition SQL

Ce document décrit comment créer des tables partitionnées pour l’importation de bloc parallèle rapide de données à une base de données SQL Server. Grand chargement/transfert des données vers une base de données SQL, importation de données dans la base de données SQL et les requêtes suivantes peut être améliorée à l’aide de _vues et Tables partitionnées_. 


## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Créer une nouvelle base de données et un ensemble de groupes de fichiers

- [Créer une nouvelle base de données](https://technet.microsoft.com/library/ms176061.aspx) (si elle n’existe pas)
- Ajouter des groupes de fichiers de base de données à la base de données qui contiendra les fichiers physiques partitionnées

  Remarque : Ceci est possible avec la [Création de la base de données](https://technet.microsoft.com/library/ms176061.aspx) si elle est nouvelle ou [ALTER DATABASE](https://msdn.microsoft.com/library/bb522682.aspx) si la base de données existe déjà

- Ajouter un ou plusieurs fichiers (le cas échéant) pour chaque groupe de fichiers de base de données

 > [AZURE.NOTE] Spécifier le groupe de fichiers cible qui conserve les données de cette partition et les noms de fichiers de base de données physique où les groupe de fichiers données seront stockées.
 
L’exemple suivant crée une nouvelle base de données avec trois groupes de fichiers que le serveur principal et les groupes contenant un fichier physique dans chaque journal. Les fichiers de base de données sont créés dans le dossier de données de SQL Server par défaut, tel que configuré dans l’instance de SQL Server. Pour plus d’informations sur les emplacements de fichier par défaut, voir [Emplacements de fichiers par défaut et nommé les Instances de SQL Server](https://msdn.microsoft.com/library/ms143547.aspx).

    DECLARE @data_path nvarchar(256);
    SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);
    
    EXECUTE ('
        CREATE DATABASE <database_name>
        ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ), 
        LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')
    
## <a name="create-a-partitioned-table"></a>Créez une table partitionnée

Créez les tables partitionnées en fonction du schéma de données, mappé vers les groupes de fichiers de base de données créée à l’étape précédente. Lorsque les données sont importée dans les tables partitionnées de bloc, les enregistrements seront distribués entre les groupes de fichiers en fonction d’un schéma de partition, comme décrit ci-dessous.

**Pour créer une table de partition, vous devez :**

- [Créer une fonction de partition](https://msdn.microsoft.com/library/ms187802.aspx) qui définit la plage de valeurs/des limites pour être inclus dans chaque table de partition individuelle, par exemple, pour limiter les partitions par mois (certains\_datetime\_champ) de l’année 2013 :

        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

- [Créer un schéma de partition](https://msdn.microsoft.com/library/ms179854.aspx) qui mappe de chaque plage de partition dans la fonction de partition à un groupe de fichiers physique, par exemple :

        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )

  Conseil : Pour vérifier les plages en vigueur dans chaque partition selon le schéma/de fonction, exécutez la requête suivante :

        SELECT psch.name as PartitionScheme,
            prng.value AS ParitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

- [Créer une table partitionnée](https://msdn.microsoft.com/library/ms174979.aspx) (s) en fonction de votre schéma de données, et de spécifier le champ de jeu et contrainte de partition utilisé pour partitionner la table, par exemple :

        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

Pour plus d’informations, consultez [Création de Tables partitionnées et les index](https://msdn.microsoft.com/library/ms188730.aspx).


## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Les données pour chaque table de partition individuelle d’importation en bloc

- Vous pouvez utiliser BCP, BULK INSERT ou autres méthodes, telles que [l’Assistant de Migration SQL Server](http://sqlazuremw.codeplex.com/). L’exemple fourni utilise la méthode BCP.

- Pour modifier le modèle de journalisation des transactions à journalisé en masse afin de réduire les frais d’enregistrement, par exemple, [Modifier la base de données](https://msdn.microsoft.com/library/bb522682.aspx) :

        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED

- Pour accélérer le processus de chargement des données, de lancer les opérations d’importation en bloc en parallèle. Pour savoir comment accélérer l’importation en bloc de données dans les bases de données SQL Server, consultez [chargement de 1 To en moins de 1 heure](http://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx).

Le script PowerShell suivant est un exemple parallèles de chargement de données à l’aide de BCP.

    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0
    
    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>
       
    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"
   
    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir
      
    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }
    
    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }
    
    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }
    
    Get-Job
    
    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Créer des index pour optimiser les performances de la requête et de jointures

- Si vous extraira les données pour la modélisation à partir de plusieurs tables, créer des index sur les clés de jointure pour améliorer les performances de jointure.

- [Créer index](https://technet.microsoft.com/library/ms188783.aspx) (en cluster ou non cluster) ciblant le même groupe de fichiers pour chaque partition, par exemple pour :

        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
ou,

        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)

 > [AZURE.NOTE] Vous pouvez choisir de créer les index avant en bloc de l’importation des données. Création d’index avant l’importation en bloc ralentit le chargement des données.


## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Les processus Analytique avancée et technologie dans exemple d’Action

Pour obtenir un exemple de procédure pas à pas de bout en bout à l’aide de la procédure Analytique de Cortana avec un groupe de données publique, consultez [processus Analytique de Cortana en Action : à l’aide de SQL Server](machine-learning-data-science-process-sql-walkthrough.md).
 
