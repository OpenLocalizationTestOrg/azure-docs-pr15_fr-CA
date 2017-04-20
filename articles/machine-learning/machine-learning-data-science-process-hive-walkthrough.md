<properties
    pageTitle="Le processus de Science de données équipe en action : clusters d’utilisation Hadoop | Microsoft Azure"
    description="À l’aide du processus de Science de données équipe pour un scénario de bout en bout utilisant un cluster HDInsight Hadoop pour générer et déployer un modèle à l’aide d’un groupe de données accessible au public."
    services="machine-learning,hdinsight"
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
    ms.author="hangzh;bradsev" />


# <a name="the-team-data-science-process-in-action-using-hdinsight-hadoop-clusters"></a>Le processus de Science de données équipe en action : l’utilisation de clusters de HDInsight Hadoop

Dans cette procédure pas à pas, nous utilisons l' [Équipe données Science processus (TDSP)](data-science-process-overview.md) dans un scénario de bout en bout à l’aide d’un [cluster d’Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) pour stocker, explorez et fonctionnalité données ingénieur du DataSet [NYC Taxi voyages](http://www.andresmh.com/nyctaxitrips/) disponibles publiquement et vers le bas les données d’exemple. Modèles de données sont générées avec formation de Machine Azure pour traiter les binaires et multiclass tâches prédictives de classification et de régression.

Pour une procédure pas à pas qui montre comment gérer un groupe de données (1 téraoctet) plus importante d’un scénario similaire à l’aide des clusters de HDInsight Hadoop pour le traitement de données, consultez [Processus d’équipe données Science - utilisation des Clusters Azure HDInsight Hadoop sur un groupe de données de 1 to](machine-learning-data-science-process-hive-criteo-walkthrough.md).

Il est également possible d’utiliser un ordinateur portable de IPython pour accomplir les tâches de présentation de la procédure pas à pas utilisant le groupe de données de 1 To. Les utilisateurs qui souhaitent utiliser cette méthode doivent consulter la rubrique [procédure pas à pas Criteo, à l’aide d’une connexion ODBC de la ruche](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) .


## <a name="dataset"></a>Description du Dataset de voyages NYC Taxi

Les données de voyage de Taxi NYC sont environ 20 Go sur les fichiers compressés valeurs séparées par des virgules (CSV) (Go ~ 48 non compressé), comprenant les tarifs et les voyages individuels de plus de 173 millions payé pour chaque séjour. Chaque enregistrement voyage comprend un emplacement de prélèvement et de remise et de temps, hack rendues anonymes (pilote) numéro de licence et nombre de medallion (numéro d’identification du taxi unique). Les données couvre tous les déplacements de l’année 2013 et sont fournies dans les deux jeux de données suivantes pour chaque mois :

1. Les fichiers CSV de 'trip_data' contient les détails du voyage, comme nombre de passagers, capteur et les points de cette chute, la durée du voyage et longueur du voyage. Voici quelques exemples d’enregistrements :

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Les fichiers CSV de 'trip_fare' contient des détails sur le prix payé pour chaque voyage, telles que le type de paiement, tarif, surcharge et taxes, conseils et péages et le montant total payé. Voici quelques exemples d’enregistrements :

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Clé unique pour joindre le voyage\_données et voyage\_tarif est composée des champs : medallion, hack\_certificat et capteur\_datetime.

Pour obtenir tous les détails pertinents pour un voyage particulier, il suffit de joindre avec trois clés : « medallion », « hack\_licence » et « collecte\_datetime ».

Nous décrire certains détails plus des données lorsque nous les stocker dans les tables de la ruche peu de temps.

## <a name="mltasks"></a>Exemples de tâches de prévision
Lorsque approche des données, en déterminant le type de prévisions que vous voulez rendre selon son analyse permet de clarifier les tâches que vous devez inclure dans votre processus.
Voici trois exemples de problèmes de prévision qui nous adresse dans cette procédure pas à pas dont la formulation est basée sur la *Conseil\_volume*:

1. **Classement binaire**: prévoir ou non une info-bulle a été payée pour un voyage, c'est-à-dire un *Conseil\_montant* qui est supérieur à 0 f est un exemple positif, tout en un *Conseil\_montant* $ 0 est un exemple négatif.

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0

2. **Multiclass classification**: pour prédire la plage de rémunération de pointe pour le voyage. On divise la *Conseil\_montant* dans cinq emplacements ou les classes :

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. **Tâche de régression**: pour prévoir la quantité de l’info-bulle à payer pour un trajet.  


## <a name="setup"></a>Configurer un cluster HDInsight Hadoop pour analytique avancée

>[AZURE.NOTE] Il s’agit généralement d’une tâche **d’administration** .

Vous pouvez configurer un environnement Azure pour analytique avancée qui utilise un cluster de HDInsight en trois étapes :

1. [Créer un compte de stockage](../storage/storage-create-storage-account.md): ce compte de stockage est utilisé pour stocker des données dans le stockage Blob Azure. Les données utilisées dans les clusters de HDInsight se trouvent également ici.

2. [Clusters de personnaliser les Hadoop d’Azure HDInsight pour l’avancée des processus Analytique et la technologie](machine-learning-data-science-customize-hadoop-cluster.md). Cette étape crée un Hadoop de HDInsight Azure cluster avec 2.7 de Python Anaconda 64 bits est installé sur tous les nœuds. Il y a deux étapes importantes à retenir lors de la personnalisation de votre cluster de HDInsight.

    * N’oubliez pas que lier le compte de stockage créé à l’étape 1 avec votre cluster HDInsight lors de sa création. Ce compte de stockage est utilisé pour accéder aux données traitées au sein du cluster.

    * Après la création du cluster, activer l’accès distant pour le nœud principal du cluster. Accédez à l’onglet **Configuration** , cliquez sur **Activer à distance**. Cette étape spécifie les informations d’identification d’utilisateur utilisées pour la connexion d’accès distante.

3. [Créer un espace de travail de Machine d’apprentissage Azure](machine-learning-create-workspace.md): espace de travail de Machine d’Azure cet apprentissage est utilisé pour construire des modèles d’apprentissage machine. Cette tâche est adressée à une exploration de données initial à la fin et vers le bas d’échantillonnage utilisant le cluster du HDInsight.

## <a name="getdata"></a>Obtenir les données d’une source publique

>[AZURE.NOTE] Il s’agit généralement d’une tâche **d’administration** .

Pour obtenir le groupe de données [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) à partir de son emplacement public, vous pouvez utiliser une des méthodes décrites dans le [Déplacement des données vers et depuis le stockage Blob Azure](machine-learning-data-science-move-azure-blob.md) pour copier les données sur votre ordinateur.

Nous décrivons ici comment utiliser AzCopy pour transférer les fichiers contenant des données. Pour télécharger et installer AzCopy suivez les instructions de la section [Mise en route avec l’utilitaire de ligne de commande de AzCopy](../storage/storage-use-azcopy.md).

1. À partir d’une fenêtre d’invite de commandes, exécutez les commandes AzCopy suivantes, en remplaçant *< path_to_data_folder >* avec la destination souhaitée :


        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

2. Une fois la copie terminée, un total de 24 fichiers compressés sont dans le dossier de données choisi. Décompressez les fichiers téléchargés dans le même répertoire sur votre ordinateur local. Prenez note du dossier où se trouvent les fichiers non compressés. Ce dossier sera appelé la *< chemin d’accès\_à\_unzipped_data\_fichiers\> * est ce qui suit.


## <a name="upload"></a>Télécharger les données vers le conteneur par défaut du cluster d’Azure HDInsight Hadoop

>[AZURE.NOTE] Il s’agit généralement d’une tâche **d’administration** .

Dans les commandes AzCopy suivantes, remplacez les paramètres suivants avec les valeurs réelles que vous avez spécifié lors de la création du cluster Hadoop et décompresser les fichiers de données.

* ***& #60 ; path_to_data_folder >*** le répertoire (et le chemin d’accès) sur votre ordinateur qui contiennent les fichiers de données décompressés  
* ***& #60 ; le nom du compte de stockage de cluster d’Hadoop >*** le compte de stockage associé à votre cluster HDInsight
* ***& #60 ; conteneur par défaut du cluster d’Hadoop >*** le conteneur par défaut utilisé par le cluster. Notez que le nom du conteneur par défaut est généralement le même nom que le cluster lui-même. Par exemple, si le cluster est appelé « abc123.azurehdinsight.net », le conteneur par défaut est abc123.
* ***& #60 ; clé de compte de stockage >*** la clé pour le compte de stockage utilisé par votre cluster

À partir d’une invite de commandes ou une fenêtre Windows PowerShell sur votre ordinateur, exécutez les deux commandes suivantes dans la AzCopy.

Cette commande transfère les données de voyage au répertoire ***nyctaxitripraw*** dans le conteneur par défaut du cluster Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Cette commande transfère les données de prix au répertoire ***nyctaxifareraw*** dans le conteneur par défaut du cluster Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Les données doivent désormais dans le stockage Blob Azure et prêts à être consommés à l’intérieur du cluster HDInsight.

## <a name="#download-hql-files"></a>Journal dans le nœud de tête du cluster d’Hadoop et et le préparer pour l’analyse exploratoire

>[AZURE.NOTE] Il s’agit généralement d’une tâche **d’administration** .

Pour accéder au nœud de tête du cluster pour analyse exploratoire et d’échantillonnage des données, suivez la procédure décrite dans [la tête d’Hadoop Cluster de nœuds d’accès](machine-learning-data-science-customize-hadoop-cluster.md#headnode).

Dans cette procédure pas à pas, nous utilisent principalement des requêtes écrites dans la [ruche](https://hive.apache.org/), un langage de requête de type SQL pour effectuer des explorations de données préliminaires. Les requêtes de la ruche sont stockés dans les fichiers .hql. Nous puis vers le bas un exemple de données à utiliser pour la création de modèles de formation de Machine Azure.

Pour préparer le cluster analyse exploratoire, nous télécharger les fichiers de .hql contenant des scripts ruche pertinents à partir de [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) dans un répertoire local (C:\temp) sur le nœud principal. Pour ce faire, ouvrez l' **invite de commande** à partir de dans le nœud de tête du cluster et exécutez les deux commandes suivantes :

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Ces deux commandes télécharge tous les fichiers .hql dans cette procédure pas à pas dans le répertoire local ***C:\temp & #, 92 ;*** dans le nœud de tête.

## <a name="#hive-db-tables"></a>Créer une base de données de la ruche et les tables partitionnées par mois

>[AZURE.NOTE] Il s’agit généralement d’une tâche **d’administration** .

Nous sommes maintenant prêts à créer des tables de ruche pour notre dataset de taxi NYC.
Dans le nœud de tête du cluster Hadoop, ouvrez la ***ligne de commande Hadoop*** sur le bureau du nœud principal et entrez le répertoire de la ruche en entrant la commande

    cd %hive_home%\bin

>[AZURE.NOTE] **Exécuter toutes les commandes de la ruche dans cette procédure pas à pas à partir de l’emplacement ci-dessus de la ruche / invite du répertoire. Il se charge de tout problème de chemin d’accès automatiquement. Nous utilisons les termes « Invite du répertoire ruche », « emplacement de la ruche / invite du répertoire » et « ligne de commande Hadoop » indifféremment dans cette procédure pas à pas.**

À partir de l’invite du répertoire ruche, entrez la commande suivante dans Hadoop ligne de commande du nœud principal pour soumettre la requête de la ruche pour créer des tables et base de données de la ruche :

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Voici le contenu de la ***C:\temp\sample\_ruche\_créer\_db\_et\_tables.hql*** fichier qui crée la ruche de base de données ***nyctaxidb*** et des tables de ***voyage*** et des ***frais***.

    create database if not exists nyctaxidb;

    create external table if not exists nyctaxidb.trip
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double)  
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

    create external table if not exists nyctaxidb.fare
    (
        medallion string,
        hack_license string,
        vendor_id string,
        pickup_datetime string,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double)
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Ce script de ruche crée deux tables :

* la table « déplacement » contient des détails de voyage de chaque marche (détails du pilote, heure d’extraction, distance de voyage et des heures)
* la table « tarif » contient les détails des frais (montant des frais, le montant de l’info-bulle, péages et surtaxes).

Si vous avez besoin d’assistance supplémentaire avec ces procédures ou que vous souhaitez étudier d’autres possibles, reportez-vous à la section [soumettre la ruche requêtes directement à partir de la ligne de commande de Hadoop ](machine-learning-data-science-move-hive-tables.md#submit).

## <a name="#load-data"></a>Charger des données à des tables de la ruche par les partitions

>[AZURE.NOTE] Il s’agit généralement d’une tâche **d’administration** .

Le groupe de données taxi NYC a un partitionnement naturel par mois, ce qui nous permet d’activer des temps de traitement et de la requête. Les commandes PowerShell ci-dessous (émis à partir du répertoire de la ruche à l’aide de la **ligne de commande Hadoop**) chargement les données dans les tables de ruche « voyage » et « tarif » partitionnées par mois.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

Le *exemple\_ruche\_de charger\_données\_en\_partitions.hql* fichier contient les commandes suivantes de **charger** .

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Notez qu’un certain nombre de requêtes de ruche que nous utilisons ici dans le processus d’exploration implique la recherche qu’une seule partition ou quelques partitions. Mais ces requêtes peuvent s’exécuter sur les données ensemble.

### <a name="#show-db"></a>Afficher les bases de données du cluster HDInsight Hadoop

Pour afficher les bases de données créées dans le cluster HDInsight Hadoop à l’intérieur de la fenêtre de ligne de commande Hadoop, exécutez la commande suivante depuis la ligne de commande de Hadoop :

    hive -e "show databases;"

### <a name="#show-tables"></a>Afficher les tables de la ruche dans la base de données nyctaxidb

Pour afficher les tables de la base de données nyctaxidb, exécutez la commande suivante depuis la ligne de commande de Hadoop :

    hive -e "show tables in nyctaxidb;"

Nous pouvons confirmer que les tables sont partitionnées en émettant la commande ci-dessous :

    hive -e "show partitions nyctaxidb.trip;"

Le résultat attendu est indiqué ci-dessous :

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 2.075 seconds, Fetched: 12 row(s)

De même, nous pouvons vérifier que la table de tarif est partitionnée en utilisant la commande ci-dessous :

    hive -e "show partitions nyctaxidb.fare;"

Le résultat attendu est indiqué ci-dessous :

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="#explore-hive"></a>Exploration de données et de l’ingénierie de fonctionnalité dans la ruche

>[AZURE.NOTE] Il s’agit généralement d’une tâche **Scientifique des données** .

L’exploration de données et la fonctionnalité ingénierie des tâches pour les données chargées dans les tables de la ruche peuvent être effectuées à l’aide de requêtes de la ruche. Voici des exemples de tâches telles que nous vous guider dans cette section :

- Permet d’afficher les enregistrements des 10 premiers dans les deux tables.
- Explorez les distributions de données de certains champs dans différentes fenêtres.
- Examinez la qualité des données des champs de latitude et de longitude.
- Générer des étiquettes de classification binaire et multiclass en fonction de la **Conseil\_volume**.
- Générer des fonctionnalités en calculant les distances de déplacement direct.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Exploration : Permet d’afficher les enregistrements des 10 principaux de voyage de la table

>[AZURE.NOTE] Il s’agit généralement d’une tâche **Scientifique des données** .

Pour voir à quoi ressemble les données, nous examinons les 10 enregistrements de chaque table. Exécutez les deux requêtes suivantes séparément à partir de l’invite du répertoire ruche dans la console de ligne de commande Hadoop pour inspecter les enregistrements.

Pour obtenir les premiers 10 enregistrements dans la table « déplacement » du premier mois :

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Pour obtenir les 10 premiers enregistrements dans la table « tarif » à partir du premier mois :

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Il est souvent utile d’enregistrer les enregistrements d’un fichier pour un affichage pratique. Une petite modification à la requête ci-dessus utilise pour cela :

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Exploration : Permet d’afficher le nombre d’enregistrements dans chacune des 12 partitions

>[AZURE.NOTE] Il s’agit généralement d’une tâche **Scientifique des données** .

D’intérêt est la façon dont le nombre de déplacements varie au cours de l’année civile. Regroupement par mois nous permet de voir l’aspect de cette distribution de déplacements.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Cela nous donne le résultat :

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 283.406 seconds, Fetched: 12 row(s)

La première colonne est le mois, et la seconde est le nombre de déplacements pour ce mois.

Nous pouvons également compter le nombre total d’enregistrements dans notre ensemble de données de voyage en émettant la commande suivante à l’invite du répertoire ruche.

    hive -e "select count(*) from nyctaxidb.trip;"

Cela donne :

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

À l’aide des commandes similaires à ceux indiqués pour l’ensemble de données de voyage, nous pouvons émettre des requêtes de ruche à partir de l’invite du répertoire ruche pour les données de prix définies valider le nombre d’enregistrements.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Cela nous donne le résultat :

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 253.955 seconds, Fetched: 12 row(s)

Notez que le même nombre de voyages par mois est retourné pour les deux ensembles de données. Ainsi, la première validation que les données ont été chargées correctement.

Pour compter le nombre total d’enregistrements dans le jeu de données de prix peut être effectuée à l’aide de la commande ci-dessous à partir de l’invite du répertoire ruche :

    hive -e "select count(*) from nyctaxidb.fare;"

Cela donne :

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

Le nombre total d’enregistrements dans les deux tables est également identique. Ainsi, un deuxième contrôle que les données ont été chargées correctement.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploration : Distribution de voyage par medallion

>[AZURE.NOTE] Il s’agit généralement d’une tâche **Scientifique des données** .

Cet exemple identifie la medallion (numéros de taxi) avec plus de 100 voyages dans un laps de temps donné. La requête bénéficie de l’accès à la table partitionnée dans la mesure où il est conditionné par la variable de partition **mois**. Résultats de la requête sont écrites dans un queryoutput.tsv de fichier local de `C:\temp` sur le nœud principal.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Voici le contenu de *exemple\_ruche\_voyage\_nombre\_par\_medallion.hql* fichier de contrôle.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

La medallion dans le jeu de données de taxi NYC identifie un fichier cab unique. Nous pouvons identifier les fichiers CAB est « occupé » en demandant à ceux qui supérieure à un certain nombre de voyages effectués dans une période donnée. L’exemple suivant identifie des cabines réalisées par les voyages de plus d’une centaine en premier trois mois et enregistre les résultats de requête dans un fichier local, C:\temp\queryoutput.tsv.

Voici le contenu de *exemple\_ruche\_voyage\_nombre\_par\_medallion.hql* fichier de contrôle.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

À partir de l’invite du répertoire ruche, exécutez la commande ci-dessous :

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Exploration : Distribution de voyage par medallion et hack_license

>[AZURE.NOTE] Il s’agit généralement d’une tâche **Scientifique des données** .

Lors de l’exploration d’un groupe de données, nous souhaitons fréquemment examiner le nombre de co-occurrences, des groupes de valeurs. Cette section fournit un exemple de la procédure à suivre pour les pilotes et les fichiers CAB.

Le *exemple\_ruche\_voyage\_nombre de\_par\_medallion\_license.hql* fichier regroupe le jeu de données de prix sur « medallion » et « hack_license » et renvoie le nombre de chaque combinaison. Voici son contenu.

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Cette requête renvoie le cab et combinaisons de pilote particulier commandées par nombre décroissant de déplacements.

À partir de l’invite du répertoire ruche, exécutez :

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Résultats de la requête sont écrites dans un fichier local C:\temp\queryoutput.tsv.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitudelatitude-records"></a>Exploration : Évaluation de la qualité des données en recherchant les enregistrements de longitude et latitude non valide

>[AZURE.NOTE] Il s’agit généralement d’une tâche **Scientifique des données** .

Un objectif commun d’analyse de données exploratoire est afin d’éliminer les enregistrements non valides ou incorrectes. L’exemple de cette section détermine si les champs de latitude ou de longitude contiennent une valeur en dehors de la zone NYC. Dans la mesure où il est probable que ces enregistrements ont un valeurs erronées de latitude-longitude, nous souhaitons les éliminer toutes les données qui doit être utilisé pour la modélisation.

Voici le contenu de *exemple\_ruche\_qualité\_assessment.hql* fichier de contrôle.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


À partir de l’invite du répertoire ruche, exécutez :

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

L’argument *-S* inclus dans cette commande supprime l’impression écran de statut des tâches de mappage/réduction de la ruche. Ceci est utile car elle rend l’écran imprimer la ruche du résultat de requête plus lisible.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Exploration : Distributions de classe binaire conseils de voyage

> [AZURE.NOTE] Il s’agit généralement d’une tâche **Scientifique des données** .

Pour le problème de la classification binaire décrit dans la section des [exemples de tâches de prévision](machine-learning-data-science-process-hive-walkthrough.md#mltasks) , il est utile de savoir si une info-bulle a été accordée ou non. Cette distribution de conseils est binaire :

* Conseil donné (classe 1, le Conseil\_montant > 0)  
* aucune info-bulle (classe 0, Conseil\_montant = 0 $).

Le *exemple\_ruche\_Bonhomme de neige\_frequencies.hql* fichier indiqué ci-dessous pour cela.

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

À partir de l’invite du répertoire ruche, exécutez :

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Exploration : Classe distributions dans le paramètre multiclass

> [AZURE.NOTE] Il s’agit généralement d’une tâche **Scientifique des données** .

Pour le problème de classification multiclass décrit dans la section [exemples de tâches de prévision](machine-learning-data-science-process-hive-walkthrough.md#mltasks) cet ensemble de données se prête à une classification naturelle où nous souhaiterions prévoir la quantité des conseils donnés. Nous pouvons utiliser des emplacements pour définir les plages d’info-bulle de la requête. Pour obtenir les distributions de classe pour les différents Conseil plages, nous utilisons la *exemple\_ruche\_Conseil\_plage\_frequencies.hql* fichier. Voici son contenu.

    SELECT tip_class, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount=0, 0,
            if(tip_amount>0 and tip_amount<=5, 1,
            if(tip_amount>5 and tip_amount<=10, 2,
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

Exécutez la commande suivante à partir de la console de ligne de commande Hadoop :

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-direct-distance-between-two-longitude-latitude-locations"></a>Exploration : Calculer la Distance directe entre deux emplacements de Latitude-Longitude

> [AZURE.NOTE] Il s’agit généralement d’une tâche **Scientifique des données** .

Ayant une mesure de la distance directe permet de connaître la différence entre lui et la distance réelle de voyage. Nous motiver cette fonctionnalité en expliquant qu’un passager peut être moins susceptible de Conseil Si elles déterminer que le pilote a prises intentionnellement par un itinéraire beaucoup plus de temps.

Pour voir la comparaison entre la distance de déplacement réel et la [Haversine distance](http://en.wikipedia.org/wiki/Haversine_formula) entre deux points de latitude-longitude (distance « orthodromique »), nous utilisons les fonctions trigonométriques disponibles au sein de la ruche, par conséquent :

    set R=3959;
    set pi=radians(180);

    insert overwrite directory 'wasb:///queryoutputdir'

    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
    from nyctaxidb.trip
    where month=1
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

Dans la requête ci-dessus, R est le rayon de la terre en miles et pi est convertie en radians. Notez que les points de latitude-longitude sont « filtrées » pour supprimer les valeurs qui sont loin d’être la zone NYC.

Dans ce cas, nous écrire nos résultats à un répertoire nommé « queryoutputdir ». La séquence de commandes ci-dessous crée tout d’abord ce répertoire de sortie, puis exécute la commande de la ruche.

À partir de l’invite du répertoire ruche, exécutez :

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Résultats de la requête sont écrites dans les BLOB Azure 9 ***queryoutputdir/000000\_0*** à ***queryoutputdir/000008\_0*** sous le conteneur par défaut du cluster Hadoop.

Pour afficher la taille des blobs individuels, nous exécutons la commande suivante à partir de l’invite du répertoire ruche :

    hdfs dfs -ls wasb:///queryoutputdir

Pour afficher le contenu d’un fichier donné, par exemple 000000\_0, nous utilisons d’Hadoop `copyToLocal` commande, donc.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [AZURE.WARNING] `copyToLocal`peut être très lente pour les fichiers volumineux et n’est pas recommandé pour une utilisation avec eux.  

Des principaux avantages d’avoir ces données se trouvent dans un blob Azure sont que nous pouvons Explorer les données de formation de Machine Azure en utilisant les [Données d’importation] [ import-data] module.


## <a name="#downsample"></a>Exemples de modèles de données et la génération dans l’apprentissage automatique de Azure vers le bas

> [AZURE.NOTE] Il s’agit généralement d’une tâche **Scientifique des données** .

Après la phase d’analyse exploratoire de données, nous sommes maintenant prêts à bas exemple les données pour la création de modèles dans l’apprentissage automatique de Azure. Dans cette section, nous montrons comment utiliser une requête de ruche vers le bas exemple les données, qui sont ensuite accessible à partir des [Données d’importation] [ import-data] module dans la formation de Machine Azure.

### <a name="down-sampling-the-data"></a>Vers le bas les données d’échantillonnage

Il y a deux étapes dans cette procédure. Tout d’abord nous joindre les tables **nyctaxidb.trip** et **nyctaxidb.fare** sur les trois clés qui sont présents dans tous les enregistrements : « medallion », « hack\_licence », et « collecte\_datetime ». Nous avons ensuite générer une étiquette de classement binaire **Bonhomme de neige** et une étiquette de classement de multiples **Conseil\_classe**.

Pour pouvoir utiliser le bas échantillonner les données directement à partir des [Données d’importation] [ import-data] module de formation de Machine d’Azure, il est nécessaire de stocker les résultats de la requête ci-dessus pour une table interne de la ruche. Dans ce qui suit, nous créer une table interne de la ruche et remplir son contenu avec la jointure et vers le bas des données échantillonnées.

La requête s’applique des fonctions de ruche standard directement pour générer l’heure de la journée, la semaine de l’année, jour de la semaine (1 signifie lundi, 7 socles et dimanche) à partir de la « collecte\_datetime » champ et la distance directe entre les emplacements de prélèvement et de cette chute. Les utilisateurs peuvent faire référence à [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) pour une liste complète de ces fonctions.

La requête puis vers le bas les exemples données afin que les résultats de la requête peuvent tenir dans le Studio de formation de Machine Azure. Environ 1 % du groupe de données d’origine sont importée dans le Studio.

Vous trouverez ci-dessous le contenu de *exemple\_ruche\_préparer\_de\_aml\_full.hql* fichier qui prépare les données pour le modèle de construction dans l’apprentissage automatique de Azure.

        set R = 3959;
        set pi=radians(180);

        create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\n'
        stored as textfile;

        --- now insert contents of the join into the above internal table

        insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from nyctaxidb.fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

Pour exécuter cette requête, à partir de l’invite du répertoire ruche :

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Nous disposons désormais d’une table interne de « nyctaxidb.nyctaxi_downsampled_dataset », qui est accessible à l’aide de l' [Importation de données] [ import-data] module de formation de Machine Azure. En outre, nous pouvons utiliser ce groupe de données pour la création de modèles d’apprentissage automatique.  

### <a name="use-the-import-data-module-in-azure-machine-learning-to-access-the-down-sampled-data"></a>Utiliser le module d’importation de données dans l’apprentissage automatique de Azure pour accéder aux données échantillonnées bas

Comme composants requis pour l’exécution de requêtes de ruche dans les [Données d’importation] [ import-data] module d’apprentissage de Machine d’Azure, nous devons accès à une Machine Azure, espace de travail de la formation et les informations d’identification du cluster et de son compte de stockage associé.

Pour plus d’informations sur l' [Importation de données] [ import-data] module et les paramètres d’entrée :

**URI du serveur HCatalog**: si le nom du cluster est abc123, alors il est simplement : https://abc123.azurehdinsight.net

**Nom du compte utilisateur Hadoop** : le nom d’utilisateur choisi pour le cluster (**pas** le nom d’utilisateur de l’accès à distance)

**Mot de passe du compte de ser Hadoop** : le mot de passe choisi pour le cluster (**pas** le mot de passe d’accès à distance)

**Emplacement des données de sortie** : il est choisi pour être Azure.

**Nom du compte de stockage Azure** : nom du compte de stockage par défaut associé au cluster.

**Nom du conteneur d’Azure** : Ceci est le nom de conteneur par défaut pour le cluster et est généralement le même que le nom du cluster. Pour un cluster est appelé « abc123 », il s’agit simplement abc123.

> [AZURE.IMPORTANT] **N’importe quelle table que nous souhaitons interroger à l’aide de l' [Importation des données] [ import-data] module dans la formation de Machine Azure doit être une table interne.** Voici une astuce permettant de déterminer si une table T dans un D.db de la base de données est une table interne.

À partir de l’invite du répertoire ruche, exécutez la commande :

    hdfs dfs -ls wasb:///D.db/T

Si la table est une table interne et il est rempli, son contenu doit afficher ici. Une autre façon de déterminer si une table est une table interne est d’utiliser l’Explorateur de stockage Azure. Utilisez-la pour naviguer vers le nom de conteneur par défaut du cluster et ensuite appliquer un filtre par nom de la table. Si la table et son contenu s’affiche, cela permet de confirmer qu’il est une table interne.

Voici un aperçu de la requête de la ruche et l' [Importation des données] [ import-data] module :

![](./media/machine-learning-data-science-process-hive-walkthrough/1eTYf52.png)

Notez que depuis notre vers le bas des données échantillonnées résident dans le conteneur par défaut, la requête obtenue de la ruche de Azure Machine Learning est très simple est simplement un « sélectionner * à partir de nyctaxidb.nyctaxi\_sous-échantillonnées\_données ».

Le dataset peut maintenant être utilisé comme point de départ pour la création de modèles d’apprentissage automatique.

### <a name="mlmodel"></a>Créer des modèles dans la formation de Machine Azure

Nous sommes maintenant en mesure de procéder à la construction du modèle et le modèle déploiement dans [Azure Machine Learning](https://studio.azureml.net). Les données sont prêtes pour nous permet de résoudre les problèmes de prévision identifiés ci-dessus :

**1. classification binaire**: pour prévoir ou non une info-bulle a été payée pour un voyage.

**Stagiaire utilisé :** Régression logistique de classe 2

une barre d’outils. Pour résoudre ce problème, notre étiquette cible (ou classe) est « Bonhomme de neige ». Notre dataset d’origine sous-échantillonnée a quelques colonnes qui sont des fuites de cible de cette expérimentation de la classification. En particulier : Conseil\_de classe, Conseil\_montant et total\_montant révéler d’informations sur l’étiquette de la cible qui n’est pas disponible à l’heure de test. Nous supprimer ces colonnes de compte à l’aide de la [Sélection des colonnes dans un groupe de données] [ select-columns] module.

L’instantané ci-dessous illustre notre expérience de prédire si une info-bulle a été payée pour une sortie donnée.

![](./media/machine-learning-data-science-process-hive-walkthrough/QGxRz5A.png)

b. Pour cette étude, notre distributions d’étiquette cible ont été environ 1:1.

L’instantané ci-dessous affiche les étiquettes de classe pour le problème de la classification binaire de la distribution de l’info-bulle.

![](./media/machine-learning-data-science-process-hive-walkthrough/9mM4jlD.png)

Par conséquent, nous obtenons un AUC de 0.987 comme indiqué dans la figure ci-dessous.

![](./media/machine-learning-data-science-process-hive-walkthrough/8JDT0F8.png)

**2. multiclass classification**: pour prédire la plage de rémunération de pointe pour le voyage, à l’aide de classes définies précédemment.

**Stagiaire utilisé :** Multiclass régression logistique

une barre d’outils. Pour résoudre ce problème, notre étiquette cible (ou classe) est « Conseil\_classe » qui peut prendre une des cinq valeurs (0,1,2,3,4). Comme dans le cas de la classification binaire, nous avons quelques colonnes qui sont des fuites de cible de cette expérience. En particulier : Bonhomme de neige, Conseil\_montant total\_montant révéler d’informations sur l’étiquette de la cible qui n’est pas disponible à l’heure de test. Nous supprimer ces colonnes à l’aide de la [Sélection des colonnes dans un groupe de données] [ select-columns] module.

L’instantané ci-dessous illustre notre expérience pour prévoir l’emplacement dans lequel une info-bulle est susceptible de tomber (classe 0 : Conseil = 0 $, classe 1 : Conseil > $0 et Conseil < = 5 $, classe 2 : Conseil 5 > $ et le Conseil < = 10 $, classe 3 : Conseil > $10 et Conseil < = 20, classe 4 : Conseil 20 >)

![](./media/machine-learning-data-science-process-hive-walkthrough/5ztv0n0.png)

Maintenant, nous montrer ce à quoi ressemble notre distribution de classe de test réel. Nous constatons que, tandis que la classe 0 et classe 1 sont les plus fréquentes, les autres classes sont rares.

![](./media/machine-learning-data-science-process-hive-walkthrough/Vy1FUKa.png)

b. Pour cette étude, nous utilisons une matrice de confusion à consulter nos précisions de prévision. Cela est indiqué ci-dessous.

![](./media/machine-learning-data-science-process-hive-walkthrough/cxFmErM.png)

Notez que tandis que les précisions de notre classe sur les classes et répandus est assez bonne, le modèle n’effectue pas un bon travail de « apprendre » sur les classes plus rares.


**3. tâche de régression**: prévoir le montant du pourboire pour un voyage.

**Stagiaire utilisé :** Arbre de décision amplifié

une barre d’outils. Pour résoudre ce problème, notre étiquette cible (ou classe) est « Conseil\_montant ». Les fuites de la cible sont dans ce cas : Bonhomme de neige, Conseil\_(classe), total\_montant ; toutes ces variables révèlent des informations sur le montant de l’info-bulle qui n’est généralement pas disponible à l’heure de test. Nous supprimer ces colonnes à l’aide de la [Sélection des colonnes dans un groupe de données] [ select-columns] module.

L’instantané belows illustre notre expérience pour prévoir la quantité de l’info-bulle donné.

![](./media/machine-learning-data-science-process-hive-walkthrough/11TZWgV.png)

b. Pour les problèmes de régression, nous mesurer les précisions de notre prévision en examinant l’erreur au carré dans les prévisions, le coefficient de détermination et autres. Nous présentons ces ci-dessous.

![](./media/machine-learning-data-science-process-hive-walkthrough/Jat9mrz.png)

Nous voir sur le coefficient de détermination est 0.709, ce qui implique de 71 % environ de la variance est expliqué par nos coefficients du modèle.

> [AZURE.IMPORTANT] Pour plus d’informations sur la formation de Machine Azure et comment y accéder et l’utiliser, reportez-vous à [Nouveautés d’apprentissage automatique ?](machine-learning-what-is-machine-learning.md). Une ressource très utile pour jouer avec un ensemble d’expériences d’apprentissage automatique sur Azure Machine Learning est la [Galerie d’Intelligence Cortana](https://gallery.cortanaintelligence.com/). La galerie couvre une gamme d’expériences et fournit une présentation détaillée dans la gamme des fonctionnalités d’apprentissage d’ordinateur Azure.

## <a name="license-information"></a>Informations de licence

Cette procédure pas à pas exemples et ses scripts qui l’accompagne sont partagées par Microsoft sous licence MIT. Veuillez vérifier le fichier LICENSE.txt le répertoire de l’exemple de code sur GitHub pour plus de détails.

## <a name="references"></a>Références

• La [Page de téléchargement de Monroy de Andrés NYC Taxi voyages](http://www.andresmh.com/nyctaxitrips/)  
• [Mise en échec des données de voyage du NYC Taxi par Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi briefings Commission recherche et statistiques](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
