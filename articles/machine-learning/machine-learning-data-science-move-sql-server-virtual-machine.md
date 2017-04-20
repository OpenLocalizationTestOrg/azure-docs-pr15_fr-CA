<properties 
    pageTitle="Déplacer les données vers SQL Server sur une machine virtuelle Azure | Azure" 
    description="Déplacer les données à partir de fichiers plats ou un de SQL Server en local pour SQL Server sur Azure VM." 
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
    ms.date="09/14/2016" 
    ms.author="bradsev" /> 

# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Déplacer les données vers SQL Server sur une machine virtuelle Azure

Cette rubrique décrit les options pour transférer les données à partir de fichiers plats (formats CSV ou TSV) ou à partir d’une de SQL Server sur site à SQL Server sur une machine virtuelle Azure. Ces tâches de déplacement des données vers le nuage font partie du processus Team données scientifiques.

Une rubrique qui décrit les options de déplacement des données vers une base de données de SQL Azure pour l’apprentissage automatique, voir [déplacer des données d’une base de données de SQL Azure pour l’apprentissage de Machine Azure](machine-learning-data-science-move-sql-azure.md).

Le **menu** ci-dessous des liens vers des rubriques qui décrivent comment faire pour ajouter des données dans les autres environnements cibles où les données peuvent être stockées et traitées pendant le processus de Science du données Team (TDSP).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


Le tableau suivant récapitule les options de déplacement des données vers SQL Server sur une machine virtuelle Azure.

<b>SOURCE</b> |<b>DESTINATION : SQL Server sur Azure VM</b> |
------------------ |-------------------- |
<b>Fichier plat</b> |1. <a href="#insert-tables-bcp">utilitaire de copie en bloc de ligne de commande (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">en bloc de requête SQL Insert</a><br> 3. <a href="#sql-builtin-utilities">graphiques utilitaires intégrés dans SQL Server</a>
<b>Local SQL Server</b> | 1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">déployer une base de données de SQL Server à un Assistant Microsoft Azure VM</a><br> 2. <a href="#export-flat-file">Exporter vers un fichier plat</a><br> 3. <a href="#sql-migration">l’Assistant de Migration SQL de base de données</a> <br> 4. <a href="#sql-backup">de la base de données back up et de restauration</a><br>

Notez que ce document suppose que les commandes SQL sont exécutées à partir de SQL Server Management Studio ou dans l’Explorateur de base de données Visual Studio.

> [AZURE.TIP] Comme alternative, vous pouvez utiliser le [Factory de données Azure](https://azure.microsoft.com/services/data-factory/) pour créer et planifier un pipeline qui déplace des données à un ordinateur virtuel de SQL Server sur Azure. Pour plus d’informations, voir [copier des données avec Azure Data Factory (activité de copie)](../data-factory/data-factory-data-movement-activities.md).


## <a name="prereqs"></a>Conditions préalables
Ce didacticiel suppose que vous avez :

* Un **abonnement Azure**. Si vous ne disposez pas d’un abonnement, vous pouvez vous inscrire pour un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Un **compte de stockage Azure**. Vous allez utiliser un compte de stockage Azure pour stocker les données dans ce didacticiel. Si vous n’avez pas un compte de stockage Azure, voir l’article [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) . Après avoir créé le compte de stockage, vous devez obtenir la clé de compte utilisée pour accéder au stockage. Reportez-vous à la section [Affichage, de copie et de touches d’accès de stockage régénérer](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Mise en service **de SQL Server sur une machine virtuelle Azure**. Pour plus d’informations, consultez [configurer une machine virtuelle de Azure SQL Server comme serveur de IPython portable pour analytique avancée](machine-learning-data-science-setup-sql-server-virtual-machine.md).
* Installation et configuration **Azure PowerShell** localement. Pour obtenir des instructions, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md).


## <a name="filesource_to_sqlonazurevm"></a>Déplacement des données à partir d’une source de fichier plat pour SQL Server sur un ordinateur virtuel d’Azure

Si les données dans un fichier plat (organisé dans un format lignes/colonnes), elle peut être déplacée à la machine virtuelle de SQL Server sur Azure via les méthodes suivantes :

1. [Utilitaire de copie en bloc de ligne de commande (BCP)](#insert-tables-bcp) 
2. [Requête SQL d’insertion en bloc](#insert-tables-bulkquery)
3. [Graphiques utilitaires intégrés dans SQL Server (importation/exportation, SSIS)](#sql-builtin-utilities)


### <a name="insert-tables-bcp"></a>Utilitaire de copie en bloc de ligne de commande (BCP)

BCP est un utilitaire de ligne de commande installé avec SQL Server et est un des moyens plus rapides pour déplacer des données. Il fonctionne sur toutes les trois variantes de SQL Server (local SQL Server, SQL Azure et machine virtuelle de SQL Server sur Azure). 

> [AZURE.NOTE]**Où mes données doivent être pour BCP ?**  
> S’il n’est pas requis, les fichiers contenant les données source que qui se trouve sur le même ordinateur que le serveur SQL cible permet de transfert plus rapide (réseau vitesse vs disque local la vitesse d’e/s). Vous pouvez déplacer les fichiers plats contenant les données sur l’ordinateur où SQL Server est installé à l’aide d’outils tels que [AZCopy](../storage/storage-use-azcopy.md)de copie des fichiers différents, ou windows [Azure Storage Explorer](http://storageexplorer.com/) copier-coller via le protocole RDP (Remote Desktop).

1. Assurez-vous que la base de données et les tables sont créées sur la base de données de SQL Server cible. Voici un exemple de la façon de le faire en utilisant la `Create Database` et `Create Table` commandes :

        CREATE DATABASE <database_name>
    
        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        ) 
    
2. Générer le fichier de format décrivant le schéma de la table à l’aide de la commande suivante à partir de la ligne de commande de la machine où est installé le bcp.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n` 

3. Insérer les données dans la base de données à l’aide de la commande bcp comme suit. En supposant que le SQL Server est installé sur le même ordinateur, cela doit fonctionner à partir de la ligne de commande :

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Optimisation de BCP insère** Veuillez consulter l’article suivant [« Directives pour l’optimisation de l’importation en bloc »](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) pour optimiser ces insertions.


### <a name="insert-tables-bulkquery-parallel"></a>Parallélisation d’insertions pour un déplacement plus rapide des données

Si les données que vous déplacez sont volumineuse, vous pouvez accélérer choses en exécutant simultanément plusieurs commandes BCP en parallèle dans un PowerShell Script.

> [AZURE.NOTE]**Réception de données volumineuses** Pour optimiser les données de chargement pour les groupes de données volumineux et très volumineux, partitionner vos tables logiques et physiques de la base de données à l’aide de plusieurs tables de groupes de fichiers et de la partition. Pour plus d’informations sur la création et le chargement des données dans les tables de partition, consultez [Tables de Partition de SQL charge parallèle](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).


L’exemple de script PowerShell ci-dessous montrent les insertions parallèles à l’aide de bcp :
    
    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
           bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

            #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
            #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n 
      }
    

    # Background processing of all partitions
    for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
    {
      Write-Debug "Submit loading partition # $i"
      Start-Job $ScriptBlock -Arg $i      
    }
    

    # Wait for it all to complete
    While (Get-Job -State "Running")
    {
      Start-Sleep 10
      Get-Job
    }
    
    # Getting the information back from the jobs
    Get-Job | Receive-Job
    Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>Requête SQL d’insertion en bloc

[Requête de SQL Bulk Insert](https://msdn.microsoft.com/library/ms188365) peut servir à importer des données dans la base de données à partir de fichiers de base de ligne/colonne (les types pris en charge sont couverts dans la[Préparation des données pour l’exportation de bloc ou l’importation (SQL Server)](https://msdn.microsoft.com/library/ms188609)) rubrique. 

Voici quelques exemples de commandes pour l’insertion en bloc sont les suivantes :  

1. Analyser des données et définir des options personnalisées avant de l’importer pour vous assurer que la base de données SQL Server suppose que le même format pour des champs spéciaux tels que les dates. Voici un exemple de la définition du format de date année-mois-jour (si vos données contiennent la date dans le format année-mois-jour) :

        SET DATEFORMAT ymd; 
    
2. Importer des données à l’aide d’instructions d’importation en bloc :

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH 
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )
      

### <a name="sql-builtin-utilities"></a>Utilitaires intégrés dans SQL Server

Vous pouvez utiliser SQL Server Integrations Services (SSIS) pour importer des données dans la machine virtuelle de SQL Server sur Azure à partir d’un fichier plat. SSIS est disponible dans les deux environnements de studio. Pour plus d’informations, consultez [Integration Services (SSIS) et les environnements de Studio](https://technet.microsoft.com/library/ms140028.aspx):

- Pour plus d’informations sur les outils de données de SQL Server, reportez-vous à la section [Outils de données de Microsoft SQL Server](https://msdn.microsoft.com/data/tools.aspx)  
- Pour plus d’informations sur l’Assistant Importation/exportation, reportez-vous à la section [importation de SQL Server et l’Assistant Exportation](https://msdn.microsoft.com/library/ms141209.aspx)


## <a name="sqlonprem_to_sqlonazurevm"></a>Déplacement de données locale SQL Server à SQL Server sur une machine virtuelle Azure

Vous pouvez également utiliser les stratégies de migration suivantes :

1. [Déployer une base de données de SQL Server à un Assistant Microsoft Azure VM](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exporter vers un fichier plat](#export-flat-file) 
3. [Assistant de Migration de base de données SQL](#sql-migration)
4. [De la base de données back up et de restauration](#sql-backup)

Nous décrivent chacun de ces ci-dessous :

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Déployer une base de données de SQL Server à un Assistant Microsoft Azure VM

Le **déploiement d’une base de données de SQL Server à un Assistant Microsoft Azure VM** est un moyen simple et recommandé pour déplacer des données d’une instance de SQL Server sur site vers SQL Server sur un ordinateur virtuel d’Azure. Pour obtenir la procédure détaillée ainsi qu’une description des autres solutions de voir [migrer une base de données SQL Server sur un ordinateur virtuel d’Azure](../virtual-machines/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exporter vers un fichier plat

Différentes méthodes peuvent être utilisés pour exporter des données à partir d’une de SQL Server locale en procédant comme décrit dans la rubrique [importation et exportation de données (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) . Ce document couvre le programme de copie en bloc (BCP) par exemple. Une fois que les données sont exportées dans un fichier plat, il peut être importé vers un autre serveur SQL à l’aide d’une importation en bloc. 

1. Exporter les données à partir de SQL Server sur site dans un fichier à l’aide de l’utilitaire bcp comme suit.

    `bcp dbname..tablename out datafile.tsv -S  servername\sqlinstancename -T -t \t -t \n -c`

2. Créer la base de données et la table sur la machine virtuelle de SQL Server sur Azure à l’aide de la `create database` et `create table` pour le schéma de table exporté à l’étape 1.

3. Créez un fichier de format pour décrire le schéma de table de données est importé/exporté. Détails du fichier de format sont décrites dans [Création d’un fichier de Format (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Génération du fichier de format lors de l’exécution BCP à partir de l’ordinateur SQL Server 

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n 

    Génération du fichier de format lors de l’exécution BCP à distance par rapport à un SQL Server 

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
        
    
4. Pour déplacer les données dans les fichiers plats à un SQL Server, utilisez une des méthodes décrites dans la section [Déplacement de données à partir du fichier Source](#filesource_to_sqlonazurevm) .

### <a name="sql-migration"></a>Assistant de Migration de base de données SQL

[Assistant de Migration de base de données SQL Server](http://sqlazuremw.codeplex.com/) fournit une manière conviviale pour déplacer des données entre deux instances de SQL server. Il permet à l’utilisateur mapper le schéma de données entre les sources et les tables de destination, choisissez les types de colonnes et diverses autres fonctionnalités. Il utilise la copie en bloc (BCP) dans les coulisses. Voici une capture d’écran de l’écran de bienvenue de l’Assistant Migration de base de données SQL.  

![Assistant de Migration SQL Server][2]

### <a name="sql-backup"></a>De la base de données back up et de restauration

SQL Server prend en charge : 

1. [De la base de données back up et restaurer la fonctionnalité](https://msdn.microsoft.com/library/ms187048.aspx) (à la fois à un fichier local ou un sac à DOS exporter vers blob) et les [Applications de couche données](https://msdn.microsoft.com/library/ee210546.aspx) (à l’aide de sac à DOS). 
2. Possibilité de créer directement des machines virtuelles de SQL Server sur Azure avec un copiées de la base de données ou d’une copie d’une base de données SQL Azure. Pour plus d’informations, consultez [utilisation de l’Assistant copie de base de données](https://msdn.microsoft.com/library/ms188664.aspx). 

Une capture d’écran de l’arrière de la base de données/restaurer options à partir de SQL Server Management Studio est illustrée ci-dessous.

![Outil d’importation de SQL Server][1]

## <a name="resources"></a>Ressources

[Migrer une base de données de SQL Server sur une machine virtuelle Azure](../virtual-machines/virtual-machines-windows-migrate-sql.md)

[SQL Server sur la vue d’ensemble d’ordinateurs virtuels Azure](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png
