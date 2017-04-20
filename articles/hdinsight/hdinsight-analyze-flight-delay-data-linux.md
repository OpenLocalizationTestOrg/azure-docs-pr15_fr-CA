<properties 
    pageTitle="Analyser les données de retard de vol avec une ruche sur basé sur Linux de HDInsight | Microsoft Azure" 
    description="Apprenez à utiliser la ruche pour analyser des données de vol sur HDInsight de basé sur Linux, puis exporter les données vers la base de données SQL à l’aide de Sqoop." 
    services="hdinsight" 
    documentationCenter="" 
    authors="Blackmist" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="larryfr"/>

#<a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analyser des données de retard de vol à l’aide de ruche dans HDInsight

Découvrez comment analyser les données de retard de vol à l’aide de la ruche sur HDInsight de basé sur Linux, puis exporter les données vers la base de données de SQL Azure en utilisant Sqoop.

> [AZURE.NOTE] Si des éléments individuels de ce document peuvent être utilisés avec les clusters basés sur Windows de HDInsight (Python et ruche par exemple), plusieurs étapes sont spécifiques aux clusters basés sur Linux. Pour les étapes qui fonctionnent avec un cluster Windows, consultez [analyse des données de retard vol à l’aide de la ruche dans HDInsight](hdinsight-analyze-flight-delay-data.md)

###<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __HDInsight d’un cluster__. Les étapes de création d’un nouveau cluster HDInsight de basé sur Linux, consultez [mise en route à l’aide d’Hadoop avec ruche dans HDInsight sous Linux](hdinsight-hadoop-linux-tutorial-get-started.md) .

- __Base de données SQL azure__. Vous utilisez une base de données SQL d’Azure comme un magasin de données de destination. Si vous n’avez pas déjà une base de données SQL, consultez [SQL didacticiel : créer une base de données SQL en quelques minutes](../sql-database/sql-database-get-started.md).

- __CLI azure__. Si vous n’avez pas installé le CLI d’Azure, reportez-vous à la section [installation et configuration de l’infrastructure du langage commun Azure](../xplat-cli-install.md) pour plus de détails.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]


##<a name="download-the-flight-data"></a>Télécharger les données de vol

1. Parcourir à la [recherche et l’Administration d’une technologie innovante, Bureau des statistiques de transport][rita-website].
2. Sur la page, sélectionnez les valeurs suivantes :

  	| Nom | Valeur |
  	| ---- | ---- |
  	| Année de filtre | 2013 |
  	| Filtre période | Janvier |
  	| Champs | Année, FlightDate, UniqueCarrier, support, FlightNum, OriginAirportID, origine, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. Désactivez tous les autres champs. |

3. Cliquez sur **Télécharger**. 

##<a name="upload-the-data"></a>Charger les données

1. Pour télécharger le fichier zip pour le nœud de tête du cluster HDInsight, utilisez la commande suivante :

        scp FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Remplacez le __nom de fichier__ avec le nom du fichier zip. Remplacez le __nom d’utilisateur__ avec la connexion SSH pour l’HDInsight du cluster. Remplacez CLUSTERNAME par le nom du cluster HDInsight.
    
    > [AZURE.NOTE] Si vous utilisez un mot de passe pour authentifier votre connexion SSH, le système vous demandera le mot de passe. Si vous avez utilisé une clé publique, vous devez utiliser le `-i` paramètre et spécifiez le chemin d’accès à la clé privée correspondante. Par exemple `scp -i ~/.ssh/id_rsa FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Une fois le téléchargement terminé, la connexion au cluster à l’aide de SSH :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Pour plus d’informations sur l’utilisation de SSH avec HDInsight de basé sur Linux, consultez les articles suivants :
    
    * [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir d’OS X, Unix ou Linux](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
3. Une fois connecté, utilisez la suivante pour décompresser le fichier .zip :

        unzip FILENAME.zip
    
    Cela extraira le fichier .csv est d’environ 60 Mo de taille.
    
4. Utilisez ce qui suit pour créer un nouveau répertoire sur WASB (la banque de données distribuées utilisée par HDInsight) et copier le fichier :

    très dfs - mkdir -p /tutorials/flightdelays/data très dfs-placer FILENAME.csv/didacticiels/flightdelays/données /
    
##<a name="create-and-run-the-hiveql"></a>Créer et exécuter la HiveQL

Procédez comme suit pour importer des données à partir du fichier CSV dans une table de ruche nommée des __retards__.

1. Utilisez ce qui suit pour créer et modifier un nouveau fichier nommé __flightdelays.hql__:

        nano flightdelays.hql
        
    Comme le contenu de ce fichier, utilisez ce qui suit :
    
        DROP TABLE delays_raw;
        -- Creates an external table over the csv file
        CREATE EXTERNAL TABLE delays_raw (
            YEAR string,
            FL_DATE string,
            UNIQUE_CARRIER string,
            CARRIER string,
            FL_NUM string,
            ORIGIN_AIRPORT_ID string,
            ORIGIN string,
            ORIGIN_CITY_NAME string,
            ORIGIN_CITY_NAME_TEMP string,
            ORIGIN_STATE_ABR string,
            DEST_AIRPORT_ID string,
            DEST string,
            DEST_CITY_NAME string,
            DEST_CITY_NAME_TEMP string,
            DEST_STATE_ABR string,
            DEP_DELAY_NEW float,
            ARR_DELAY_NEW float,
            CARRIER_DELAY float,
            WEATHER_DELAY float,
            NAS_DELAY float,
            SECURITY_DELAY float,
            LATE_AIRCRAFT_DELAY float)
        -- The following lines describe the format and location of the file
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE
        LOCATION '/tutorials/flightdelays/data';
        
        -- Drop the delays table if it exists
        DROP TABLE delays;
        -- Create the delays table and populate it with data
        -- pulled in from the CSV file (via the external table defined previously)
        CREATE TABLE delays AS
        SELECT YEAR AS year,
            FL_DATE AS flight_date,
            substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
            substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
            substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
            ORIGIN_AIRPORT_ID AS origin_airport_id,
            substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
            substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
            substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
            DEST_AIRPORT_ID AS dest_airport_id,
            substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
            substring(DEST_CITY_NAME,2) AS dest_city_name,
            substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
            DEP_DELAY_NEW AS dep_delay_new,
            ARR_DELAY_NEW AS arr_delay_new,
            CARRIER_DELAY AS carrier_delay,
            WEATHER_DELAY AS weather_delay,
            NAS_DELAY AS nas_delay,
            SECURITY_DELAY AS security_delay,
            LATE_AIRCRAFT_DELAY AS late_aircraft_delay
        FROM delays_raw;
        
2. Utilisez __Ctrl + X__, puis __Y__ enregistrer le fichier.

3. Utilisez la suivante pour démarrer la ruche et exécutez le fichier __flightdelays.hql__ :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -f flightdelays.hql
        
    > [AZURE.NOTE] Dans cet exemple, `localhost` est utilisé car vous êtes connecté au nœud principal du cluster HDInsight, qui est où HiveServer2 est en cours d’exécution.

4. Utilisez la commande suivante pour ouvrir une session de Beeline interactive :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

5. Lorsque vous recevez le `jdbc:hive2://localhost:10001/>` invite, utilisez la suivante pour récupérer des données à partir des données importées de vol retard.

        INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        SELECT regexp_replace(origin_city_name, '''', ''),
            avg(weather_delay)
        FROM delays
        WHERE weather_delay IS NOT NULL
        GROUP BY origin_city_name;

    Cela extraire la liste des villes qui ont connu des retards de météo, ainsi que le délai moyen et enregistrez-le à `/tutorials/flightdelays/output`. Plus tard, Sqoop sera lire les données à partir de cet emplacement et l’exporter vers une base de données de SQL Azure.

6. Pour quitter le Beeline, entrez `!quit` à l’invite de commandes.

## <a name="create-a-sql-database"></a>Créer une base de données SQL

Si vous disposez déjà d’une base de données SQL, vous devez obtenir le nom du serveur. Vous le trouverez dans le [Portail Azure](https://portal.azure.com) en sélectionnant des __Bases de données SQL__, puis en filtrant sur le nom de la base de données que vous souhaitez utiliser. Le nom du serveur est répertorié dans la colonne de __serveur__ .

Si vous ne disposez pas déjà d’une base de données SQL, utilisez les informations contenues dans [didacticiel de la base de données de SQL : créer une base de données SQL en minutes](../sql-database/sql-database-get-started.md) pour en créer un. Vous devez enregistrer le nom du serveur utilisé pour la base de données.

##<a name="create-a-sql-database-table"></a>Créer une table de base de données SQL

> [AZURE.NOTE] Il existe de nombreuses façons de se connecter à la base de données SQL pour créer une table. Les étapes suivantes utilisent les [FreeTDS](http://www.freetds.org/) du cluster HDInsight.

1. Utiliser le protocole SSH pour se connecter au cluster basé sur Linux de HDInsight et exécutez les étapes suivantes à partir de la session SSH.

3. Utilisez la commande suivante pour installer le FreeTDS :

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Une fois que FreeTDS a été installé, utilisez la commande suivante pour vous connecter au serveur de base de données SQL. Remplacez __nom_serveur__ par le nom du serveur de base de données SQL. Remplacer __adminLogin__ et __adminPassword__ par la connexion de base de données de SQL. Remplacez __databaseName__ avec le nom de la base de données.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>

    Vous recevez une sortie similaire à ce qui suit :

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. À la `1>` invite, entrez les lignes suivantes :

        CREATE TABLE [dbo].[delays](
        [origin_city_name] [nvarchar](50) NOT NULL,
        [weather_delay] float,
        CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
        ([origin_city_name] ASC))
        GO

    Lors de la `GO` instruction est entrée, les instructions précédentes seront évaluées. Cela créera une nouvelle table nommée __retards__, avec un index ordonné en clusters (requis par la base de données de SQL).

    Pour vérifier que la table a été créée, utilisez ce qui suit :

        SELECT * FROM information_schema.tables
        GO

    Vous devriez voir une sortie similaire à ce qui suit :

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        databaseName       dbo     delays      BASE TABLE

8. Entrez `exit` à la `1>` invite pour quitter l’utilitaire tsql.
    
##<a name="export-data-with-sqoop"></a>Exporter des données avec Sqoop

2. Pour vérifier que les Sqoop peuvent voir votre base de données SQL, utilisez la commande suivante :

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Cela doit retourner une liste de bases de données, y compris la base de données que vous avez créée dans la table de retards précédemment.

3. Utilisez la commande suivante pour exporter des données à partir de hivesampletable vers la table mobiledata :

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir 'wasbs:///tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1

    Cette option indique à Sqoop pour vous connecter à la base de données SQL pour la base de données contenant la table des retards et d’exporter des données à partir de la wasbs : / / / didacticiels/flightdelays/output (où nous avons stocké à la sortie de la requête de la ruche précédemment,) à la table des retards.

4. Une fois la commande terminée, utilisez les éléments suivants pour vous connecter à la base de données à l’aide de TSQL :

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Une fois connecté, utilisez les instructions suivantes pour vérifier que les données ont été exportées vers la table mobiledata :
    
        SELECT * FROM delays
        GO

    Vous devez voir une liste des données de la table. Type de `exit` pour quitter l’utilitaire tsql.

##<a id="nextsteps"></a>Étapes suivantes

Maintenant vous comprenez comment télécharger un fichier pour le stockage des objets Blob Azure, comment faire pour remplir une table de la ruche en utilisant les données de stockage des objets Blob Azure, comment exécuter des requêtes de la ruche et comment utiliser Sqoop pour exporter des données à partir de très à une base de données Azure SQL. Pour plus d’informations, consultez les articles suivants :

* [Mise en route HDInsight][hdinsight-get-started]
* [Utilisez la ruche avec HDInsight][hdinsight-use-hive]
* [Utiliser Oozie avec HDInsight][hdinsight-use-oozie]
* [Utilisez Sqoop avec HDInsight][hdinsight-use-sqoop]
* [Utilisez des porcs avec HDInsight][hdinsight-use-pig]
* [Développer les programmes Java MapReduce pour HDInsight][hdinsight-develop-mapreduce]
* [Hadoop Python pour HDInsight de diffusion en continu de programmes de développement][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx


 
