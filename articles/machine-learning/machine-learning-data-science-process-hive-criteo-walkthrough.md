<properties
    pageTitle="Le processus de Science de données équipe en action : à l’aide de clusters de HDInsight Hadoop sur le groupe de données 1 to Criteo | Microsoft Azure"
    description="À l’aide du processus de Science de données Team pour un scénario de bout en bout utilisant un cluster HDInsight Hadoop pour générer et déployer un modèle en utilisant un groupe de données accessible (1 To) volumineux"
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
    ms.date="09/13/2016"
    ms.author="bradsev" />

# <a name="the-team-data-science-process-in-action---using-azure-hdinsight-hadoop-clusters-on-a-1-tb-dataset"></a>Le processus de Science de données équipe en action - l’utilisation des Clusters Azure HDInsight Hadoop sur un groupe de données de 1 to

Dans cette procédure pas à pas, nous montrent à l’aide du processus de Science de données Team dans un scénario de bout en bout avec un [cluster d’Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) pour stocker, Explorer, ingénieur de fonctionnalité et vers le bas les exemples de données à partir d’un des jeux de données [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) publiquement disponible. Apprentissage automatique de Azure nous permet de créer un modèle de classification binaire sur ces données. Nous avons également montrer comment publier un de ces modèles sous la forme d’un service Web.

Il est également possible d’utiliser un ordinateur portable de IPython pour accomplir les tâches présentées dans cette procédure pas à pas. Les utilisateurs qui souhaitent utiliser cette méthode doivent consulter la rubrique [procédure pas à pas Criteo, à l’aide d’une connexion ODBC de la ruche](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) .


## <a name="dataset"></a>Description du groupe de données Criteo

La Criteo de données sont un groupe de données cliquez sur prévision environ 370 Go de fichiers TSV de gzip compressées (non compressé ~1.3TB), comprenant plusieurs 4.3 milliards d’enregistrements. Il provient du 24 jours de sur les données rendues disponibles par [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Pour la commodité des chercheurs de données, nous avons décompressé les données dont nous disposons pour expérimenter.

Chaque enregistrement de ce groupe de données contient 40 colonnes :

- la première colonne est une colonne de l’étiquette qui indique si un utilisateur clique sur un **Ajout** (valeur 1) ou ne clique pas une (valeur 0)
- ensuite 13 colonnes sont numériques, et
- dernière 26 sont des colonnes catégorielles

Les colonnes sont rendues anonymes et utiliser une série de noms énumérés : « Col1 » (pour la colonne libellé) pour « Col40 » (pour la dernière colonne par catégorie).            

Voici un extrait des 20 premières colonnes des deux observations (lignes) à partir de ce groupe de données :

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10   Col11   Col12   Col13   Col14   Col15           Col16           Col17           Col18           Col19       Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

Il existe des valeurs manquantes dans les colonnes numériques et par catégorie dans ce groupe de données. Nous décrire une méthode simple pour gérer les valeurs manquantes. Des détails supplémentaires des données sont décrites lorsque nous les stocker dans les tables de la ruche.

**Définition :** *Taux de clic (CTR) :* C’est le pourcentage de clics dans les données. Dans ce groupe de données Criteo, le taux de clic est environ 3,3 % ou 0.033.

## <a name="mltasks"></a>Exemples de tâches de prévision
Cette procédure pas à pas aborde les problèmes de prévision deux exemples :

1. **Classement binaire**: permet de prévoir si un utilisateur a cliqué sur un module complémentaire :
    - Classe 0 : Aucun clic
    - Classe 1 : cliquez sur

2. **Régression**: prévoit la probabilité d’un clic sur les publicités à partir des fonctionnalités de l’utilisateur.


## <a name="setup"></a>Cluster d’un HDInsight Hadoop de science de données

**Remarque :** Il s’agit généralement d’une tâche **d’administration** .

Configurer votre environnement de Science de données Azure pour créer des solutions d’analytique prédictive avec des clusters de HDInsight en trois étapes :

1. [Créer un compte de stockage](../storage/storage-create-storage-account.md): ce compte de stockage est utilisé pour stocker les données dans le stockage Blob Azure. Les données utilisées dans les clusters de HDInsight sont stockées ici.

2. [Personnaliser des Clusters de Hadoop Azure HDInsight pour les données scientifiques](machine-learning-data-science-customize-hadoop-cluster.md): cette étape crée un cluster Azure HDInsight Hadoop avec 64 bits Anaconda Python 2.7 est installé sur tous les nœuds. Il existe deux étapes importantes (décrites dans cette rubrique) pour terminer lors de la personnalisation du cluster HDInsight.

    * Vous devez lier le compte de stockage créé à l’étape 1 avec votre cluster HDInsight lorsqu’il est créé. Ce compte de stockage est utilisé pour accéder aux données qui peuvent être traitées au sein du cluster.

    * Vous devez activer l’accès distant pour le nœud de tête du cluster après sa création. Gardez à l’esprit les informations d’identification d’accès distant vous spécifiez ici (diffère de celui spécifié pour le cluster lors de sa création) : vous en avez besoin pour effectuer les procédures suivantes.

3. [Créer un espace de travail Azure ML](machine-learning-create-workspace.md): espace de travail de Machine d’Azure cet apprentissage est utilisé pour créer des modèles d’apprentissage machine après une exploration de données initiales et vers le bas d’échantillonnage sur le cluster de HDInsight.

## <a name="getdata"></a>Obtenir et utiliser des données provenant d’une source publique

Le groupe de données [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) sont accessibles en cliquant sur le lien et accepter les conditions d’utilisation qui fournit un nom. Voici une capture instantanée de cet aspect :

![Accepter les termes du contrat de Criteo](./media/machine-learning-data-science-process-hive-criteo-walkthrough/hLxfI2E.png)

Cliquez sur **Continuer pour téléchargement** pour en savoir plus sur le groupe de données et sa disponibilité.

Les données résident dans un lieu public de [Azure stockage blob](../storage/storage-dotnet-how-to-use-blobs.md) : wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. Le « wasb » fait référence à l’emplacement de stockage des objets Blob Azure. 

1. Les données de ce stockage blob publique se composent de trois sous-dossiers de données décompressées.

    1. Le sous-dossier *nombre brut / /* contient les 21 premiers jours de données, à partir du jour\_00 à jour\_20
    2. Le sous-dossier *brut-train/* se compose d’un seul jour de données, jour\_21
    3. Le sous-dossier *brut/test/* se compose de deux jours de données, jour\_22 et jour\_23

2. Pour ceux qui souhaitent démarrer avec les données brutes gzip, elles sont également disponibles dans le dossier principal *brut /* sous la forme day_NN.gz, où NN va de 00 à 23.

Une autre approche pour accéder, Explorer et le modèle de ces données, qui ne requièrent aucun téléchargement local sont expliquées plus loin dans cette procédure pas à pas, lorsque nous créons des tables de la ruche.

## <a name="login"></a>Se connecter à la headnode de cluster

Pour vous connecter à la headnode du cluster, utilisez le [portail Azure](https://ms.portal.azure.com) pour trouver le cluster. Cliquez sur l’icône d’éléphant HDInsight sur la gauche et puis double-cliquez sur le nom de votre cluster. Accédez à l’onglet **Configuration** , double-cliquez sur l’icône de connexion en bas de la page et entrez vos informations d’identification de l’accès à distance lorsque vous y êtes invité. Vous accédez alors à la headnode du cluster.

Voici à quoi ressemble un journal première typique dans le headnode de cluster :

![Connectez-vous au cluster](./media/machine-learning-data-science-process-hive-criteo-walkthrough/Yys9Vvm.png)


Sur la gauche, nous voyons la « Hadoop ligne de commande », qui est notre produit phare pour l’exploration de données. Nous constatons également deux URL utiles - « État de fils Hadoop » et « Nœud de nom Hadoop ». L’URL d’état fils affiche la progression du projet et l’URL du nœud nom donne des détails sur la configuration du cluster.

Maintenant nous sont configurés et prêts à commencer la première partie de la procédure pas à pas : exploration de données à l’aide de la ruche et la préparation des données pour l’apprentissage de Machine Azure.

## <a name="hive-db-tables"></a>Créer des tables et base de données de la ruche

Pour créer des tables de ruche pour notre dataset Criteo, ouvrir la ***ligne de commande Hadoop*** sur le bureau du nœud principal, entrez le répertoire de la ruche en entrant la commande

    cd %hive_home%\bin

>[AZURE.NOTE] Exécuter toutes les commandes de la ruche dans cette procédure pas à pas à partir de l’emplacement de la ruche / invite du répertoire. Cette méthode s’occupe de tout problème de chemin d’accès automatiquement. Nous utilisons les termes « Interroger l’annuaire ruche », « ruche bin / invite du répertoire » et « ligne de commande Hadoop » indifféremment.

>[AZURE.NOTE]  Pour exécuter des requêtes de ruche, un peut toujours utiliser les commandes suivantes :

        cd %hive_home%\bin
        hive

Une fois la réplication de la ruche s’affiche avec une « ruche > « signer, simplement coupez et collez la requête pour l’exécuter.

Le code suivant crée une base de données « criteo », puis génère des tables de 4 :


* une *table pour la génération de nombres* basé sur jour des jours\_00 à jour\_20,
* une *table utilisée en tant que dataset train* basé sur jour\_21, et
* deux *tables pour servir les jeux de données de test* basé sur les jours\_22 et jour\_23 respectivement.

Nous séparons notre dataset de test dans deux tables différentes, car un jour est un jour férié, et nous voulons déterminer si le modèle peut détecter les différences entre un jour férié et non à partir du taux de clics.

Le script [exemple & #95 ; ruche & 95 # ; créer & #95 ; criteo & #, 95 ; base de données & #, 95 ; et & #95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) s’affiche ici pour plus de commodité :

    CREATE DATABASE IF NOT EXISTS criteo;
    DROP TABLE IF EXISTS criteo.criteo_count;
    CREATE TABLE criteo.criteo_count (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

    DROP TABLE IF EXISTS criteo.criteo_train;
    CREATE TABLE criteo.criteo_train (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

    DROP TABLE IF EXISTS criteo.criteo_test_day_22;
    CREATE TABLE criteo.criteo_test_day_22 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

    DROP TABLE IF EXISTS criteo.criteo_test_day_23;
    CREATE TABLE criteo.criteo_test_day_23 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Nous Notez que toutes ces tables externes que nous indiquons simplement des emplacements de stockage des objets Blob Azure (wasb).

**Il existe deux façons d’exécuter la requête tout de la ruche qui nous parler maintenant.**

1. **À l’aide de la réplication de ruche de ligne de commande**: la première consiste à émettre une commande de « ruche » et le copier et coller une requête à la réplication de ruche de ligne de commande. Pour ce faire, effectuez :

        cd %hive_home%\bin
        hive

    À la ligne de commande de réplication, couper et coller la requête s’exécute maintenant il.

2. **Enregistrement de requêtes dans un fichier et l’exécution de la commande**: la seconde consiste à enregistrer les requêtes dans un fichier .hql ([exemple & #95 ; ruche & 95 # ; créer & #95 ; criteo & #, 95 ; base de données & #, 95 ; et & #95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) et puis exécutez la commande suivante pour exécuter la requête :

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql


### <a name="confirm-database-and-table-creation"></a>Confirmer la création de la base de données et de table

Ensuite, nous confirmer la création de la base de données avec la commande suivante à partir de l’emplacement de la ruche / invite du répertoire :

        hive -e "show databases;"

Cela donne :

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Cette opération confirme la création de la nouvelle base de données « criteo ».

Pour voir quelles tables que nous avons créé, nous créons simplement la commande à partir de l’emplacement de la ruche / invite du répertoire :

        hive -e "show tables in criteo;"

Puis, nous voir la sortie suivante :

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

##<a name="exploration"></a>Exploration des données de ruche

Nous sommes maintenant prêts à effectuer une exploration de données de base dans la ruche. Nous commencer en comptant le nombre d’exemples dans le train et tester les tables de données.

### <a name="number-of-train-examples"></a>Nombre d’exemples de train

Le contenu de [l’exemple & #95 ; ruche & #95 ; count & #95 ; train & #95 ; table & #95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) est présenté ici :

        SELECT COUNT(*) FROM criteo.criteo_train;

Cela donne :

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Sinon, un peut également délivrer la commande suivante à partir de l’emplacement de la ruche / invite du répertoire :

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Nombre d’exemples de test dans les deux jeux de données de test

Nous avons maintenant le nombre d’exemples dans les deux jeux de données de test. Le contenu de [exemple & #95 ; ruche & #95 ; count & #95 ; criteo & #95 ; test & #95 ; jour & #95 ; 22 & #95 ; table & #95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) sont ici :

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Cela donne :

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Comme d’habitude, nous pouvons également appeler le script à partir de l’emplacement de la ruche / répertoire d’invite de commandes, en émettant la commande :

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Enfin, nous allons examiner le nombre d’exemples de test dans le groupe de données de test en fonction des jours\_23.

La commande est semblable à celle ci-dessus (voir [exemple & #95 ; ruche & #95 ; count & #95 ; criteo & #95 ; test & #95 ; jour & #95 ; 23 & #95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)) :

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Cela donne :

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Distribution d’étiquette dans le dataset de train

La distribution de l’étiquette dans le dataset de train est intéressante. Pour cela, nous afficher le contenu des [exemples & #95 ; ruche & #95 ; criteo & #95 ; étiquette & #95 ; distribution & #95 ; train & #95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Cela donne la distribution de l’étiquette :

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Notez que le pourcentage d’étiquettes positifs est d’environ 3,3 % (cohérent avec le dataset d’origine).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Distributions d’histogramme de certaines variables numériques dans le dataset de train

Nous pouvons utiliser natif de la ruche « histogramme\_numérique « fonction pour savoir à quoi ressemble la distribution des variables numériques. Voici le contenu de [l’exemple & #95 ; ruche & #95 ; criteo & #95 ; histogramme & #95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Cela génère la sortie suivante :

        26      155878415
        2606    92753
        6755    22086
        11202   6922
        14432   4163
        17815   2488
        21072   1901
        24113   1283
        27429   1225
        30818   906
        34512   723
        38026   387
        41007   290
        43417   312
        45797   571
        49819   428
        53505   328
        56853   527
        61004   160
        65510   3446
        Time taken: 317.851 seconds, Fetched: 20 row(s)

AFFICHAGE - efforts transversaux éclater la combinaison a ruche pour produire une sortie de type SQL au lieu de la liste habituelle. Notez que dans cette table, la première colonne correspond au centre de l’emplacement et le second à la fréquence de l’emplacement.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Centiles approximatives de certaines variables numériques dans le dataset de train

Également d’intérêt avec des variables numériques est le calcul de centiles approximatives. Ruche de natif « centile\_environ » fait cela pour nous. Le contenu de [exemple & #95 ; ruche & #95 ; criteo & #95 ; approximative & #95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) sont :

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Cela donne :

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

Nous avons Remarque que la distribution de centiles est étroitement liée à la distribution de l’histogramme d’une variable numérique en général.        

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Rechercher le nombre de valeurs uniques pour certaines colonnes catégorielles dans le dataset de train

Continuer l’exploration de données, nous maintenant de trouver, pour certaines colonnes catégorielles, le nombre de valeurs uniques qu’ils prennent. Pour ce faire, nous afficher le contenu de [exemple & #95 ; ruche & #95 ; criteo & #95 ; unique & #95 ; valeurs & #95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Cela donne :

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Nous Notez que Col15 a des valeurs uniques de 19M ! À l’aide de techniques naïve comme « à chaud un codage » pour coder ces variables par catégorie haute-dimensions est impossible. En particulier, nous expliquer et montrer une technique puissante et robuste appelée [Apprentissage avec compte](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) pour contrer ce problème efficacement.

Nous mettre fin à cette partie en examinant le nombre de valeurs uniques pour certains autres colonnes catégorielles ainsi. Le contenu de [exemple & #95 ; ruche & #95 ; criteo & #95 ; unique & #95 ; valeurs & #95 ; plusieurs & #95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) sont :

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Cela donne :

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

À nouveau, nous voyons que, à l’exception de Col20, toutes les autres colonnes peuvent de valeurs uniques.

### <a name="co-occurence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>CO-occurrence compte de paires de variables par catégorie dans le dataset de train

Le nombre d’une occurrence de paires de variables par catégorie est aussi intéressante. Cela peut être déterminé en utilisant le code dans [exemple & #95 ; ruche & #95 ; criteo & #95 ; appariés & #95 ; catégorique & #95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Nous inverse commander le nombre par leur occurrence et en haut 15 dans ce cas. Cela nous donne :

        ad98e872        cea68cd3        8964458
        ad98e872        3dbb483e        8444762
        ad98e872        43ced263        3082503
        ad98e872        420acc05        2694489
        ad98e872        ac4c5591        2559535
        ad98e872        fb1e95da        2227216
        ad98e872        8af1edc8        1794955
        ad98e872        e56937ee        1643550
        ad98e872        d1fade1c        1348719
        ad98e872        977b4431        1115528
        e5f3fd8d        a15d1051        959252
        ad98e872        dd86c04a        872975
        349b3fec        a52ef97d        821062
        e5f3fd8d        a0aaffa6        792250
        265366bf        6f5c7c41        782142
        Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a>Exemple de jeux de données pour l’apprentissage de Machine Azure vers le bas

Ayant examiné les groupes de données et démontré comment nous pouvons faire ce type d’exploration pour toutes les variables (y compris les combinaisons), nous avons désormais vers le bas exemple les ensembles de données afin que nous pouvons créer des modèles dans l’apprentissage automatique de Azure. Rappelez-vous que le problème que nous concentrer sur : étant donné un ensemble d’attributs de l’exemple (valeurs de fonction de Col2 - Col40), nous prédire si Col1 est 0 (aucun clic) ou 1 (clic).

Pour le bas exemple notre train et test des groupes de données à 1 % de la taille d’origine, nous utilisons la fonction RAND() native de la ruche. Le script suivant, [exemple & #95 ; ruche & #95 ; criteo & #95 ; sous-échantillonner & #95 ; train & #95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) effectue cette opération pour le dataset du train :

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Cela donne :

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

Le script [exemple & #95 ; ruche & #95 ; criteo & #95 ; sous-échantillonner & #95 ; test & #95 ; jour & #95 ; 22 & #95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) le fait pour les données de test, jour\_22 :

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Cela donne :

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Enfin, le script [exemple & #95 ; ruche & #95 ; criteo & #95 ; sous-échantillonner & #95 ; test & #95 ; jour & #95 ; 23 & #95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) le fait pour les données de test, jour\_23 :

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Cela donne :

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Nous sommes prêts à utiliser notre train échantillonnée bas et tester des jeux de données pour créer des modèles dans la formation de Machine Azure.

Il est un composant important final avant de continuer à formation de Machine Azure, qui concerne la table count. Dans la section suivante, nous aborderons cela en détail.

##<a name="count"></a>Une brève discussion sur la table de comptage

Comme nous l’avons vu, plusieurs variables par catégorie ont une dimensionnalité très élevée. Dans notre procédure pas à pas, nous vous présentons une technique puissante, appelée [Apprentissage avec compte](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) pour coder ces variables de manière efficace et fiable. Plus d’informations sur cette technique est dans le lien fourni.

**Remarque :** Dans cette procédure pas à pas, nous concentrer sur l’utilisation de tables de nombre pour produire des représentations compacte de grande dimension fonctionnalités par catégorie. Il ne s’agit pas de la seule façon de coder des fonctions par catégorie ; Pour plus d’informations sur d’autres techniques, les utilisateurs concernés peuvent extraire [un hot-codage](http://en.wikipedia.org/wiki/One-hot) et la [fonction de hachage](http://en.wikipedia.org/wiki/Feature_hashing).

Pour créer des tables d’inventaire sur les données du compteur, nous utilisons les données dans le brut/nombre de dossiers. Dans la section modélisation, nous montrer les utilisateurs créer ces tables de nombre des fonctionnalités par catégorie de toutes pièces, ou vous pouvez également utiliser un tableau de nombre prédéfinis pour leurs explorations. Dans ce qui suit, lorsque nous faisons référence aux « tables de nombre prédéfinis », nous entendons les tables nombre que nous fournissons. Pour obtenir des instructions détaillées sur la façon d’accéder à ces tables sont fournies dans la section suivante.

## <a name="aml"></a>Créer un modèle avec formation de Machine Azure

Notre modèle de création de processus d’apprentissage d’ordinateur Azure procédez comme suit :

1. [Obtenir les données des tables de la ruche dans la formation de Machine Azure](#step1)
2. [Créez l’expérience : nettoyer les données, choisissez un stagiaire featurize avec les tables d’inventaire](#step2)
3. [Apprentissage du modèle](#step3)
4. [Score du modèle de données de test](#step4)
5. [Évaluer le modèle](#step5)
6. [Publier le modèle comme un service web à consommer](#step6)

Nous sommes maintenant prêts à créer des modèles dans un studio de formation de Machine Azure. Nos données échantillonnées bas sont enregistrées en tant que tables de ruche du cluster. Le module d’apprentissage de Machine d’Azure, **Importer des données** nous permet de lire ces données. Les informations d’identification pour accéder au compte de stockage de ce cluster sont fournies dans ce qui suit.

### <a name="step1"></a>Étape 1 : Obtenir des données à partir des tables de la ruche en utilisant le module Importation de données de formation de Machine Azure et sélectionnez-le pour une machine d’essai de formation

Commencez par sélectionner un **+ Nouveau** -> **expérience** -> **Essai à blanc**. Puis, à partir de la zone de **recherche** dans le coin supérieur gauche, recherchez « Importer des données ». Glisser-déplacer le module **d’Importation de données** à la zone d’essai (la partie centrale de l’écran) pour utiliser le module pour accéder aux données.

Voici à quoi ressemble l' **Importation de données** lors de l’obtention de données à partir de la table de la ruche :

![Importation de données obtient des données](./media/machine-learning-data-science-process-hive-criteo-walkthrough/i3zRaoj.png)

Pour le module **d’Importation de données** , les valeurs des paramètres qui sont fournis dans le graphique sont quelques exemples de l’ordre de tri des valeurs, que vous devez fournir. Voici quelques conseils généraux sur la façon de remplir le paramètre défini pour le module **d’Importation de données** .

1. Choisissez « Ruche query » pour la **Source de données**
2. Dans la zone **requête de base de données de la ruche** , un simple SELECT * FROM < votre\_base de données\_name.your\_table\_nom >-est suffisant.
3. **URI du serveur Hcatalog**: Si votre cluster est « abc », alors que cette situation est : https://abc.azurehdinsight.net
4. **Nom du compte utilisateur Hadoop**: le nom d’utilisateur choisi au moment de la mise en service de cluster. (Pas le nom des accès à distance utilisateur !)
5. **Mot de passe utilisateur Hadoop**: le mot de passe pour le nom d’utilisateur choisi au moment de la mise en service de cluster. (Pas l’accès à distance mot de passe !)
6. **Emplacement des données de sortie**: choisissez « Azure »
7. **Nom du compte de stockage Azure**: le compte de stockage associé au cluster
8. **Clé de compte de stockage Azure**: la clé du compte de stockage associé au cluster.
9. **Nom du conteneur d’Azure**: si le nom du cluster est « abc », puis simplement « abc », qui est généralement.


Une fois l' **Importation des données** a terminé, l’obtention de données (vous verrez la coche verte sur le Module), enregistrer ces données sous la forme d’un groupe de données (avec un nom de votre choix). Cela ressemble à :

![Enregistrement les données d’importer des données](./media/machine-learning-data-science-process-hive-criteo-walkthrough/oxM73Np.png)

Cliquez droit sur le port de sortie du module **Importation de données** . Ceci fait apparaître une option **d’enregistrement en tant que groupe de données** et une option de **visualiser** . L’option **visualiser** , si vous cliquez dessus, affiche 100 lignes de données, avec un panneau de droite qui est utile pour certaines statistiques de résumé. Pour enregistrer les données, sélectionnez **Enregistrer en tant que groupe de données** et suivez les instructions.

Pour sélectionner le groupe de données enregistré pour une utilisation dans une expérience d’apprentissage automatique, recherchez les groupes de données à l’aide de la zone de **recherche** indiquée dans la figure suivante. Tapez simplement le nom de vous avez donné le dataset partiellement à y accéder et de faire glisser le groupe de données du panneau principal. Déposant dans le panneau principal, il sélectionne pour une utilisation dans la modélisation d’apprentissage machine.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/cl5tpGw.png)

>[AZURE.NOTE] Pour ce faire, le train et les jeux de données de test. En outre, n’oubliez pas d’utiliser le nom de la base de données et les noms de table que vous avez donné à cet effet. Les valeurs utilisées dans l’illustration sont uniquement d’illustration purposes.* *

### <a name="step2"></a>Étape 2 : Créer une expérience simple dans l’apprentissage automatique de Azure pour prévoir les clics / aucun clic

Notre expérience d’Azure ML ressemble à ceci :

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/xRpVfrY.png)

Examinons maintenant les composants clés de cette expérience. En guise de rappel, nous devons faire glisser notre train enregistrée et tester d’abord des groupes de données à notre canevas de l’expérience.

#### <a name="clean-missing-data"></a>Nettoyer les données manquantes

Le module de **Nettoyage de données manquante** est ce que son nom suggère : il nettoie les données manquantes dans des méthodes qui peuvent être spécifiées par l’utilisateur. Dans ce module, nous voir ceci :

![Nettoyage données manquantes](./media/machine-learning-data-science-process-hive-criteo-walkthrough/0ycXod6.png)

Ici, nous avons choisi de remplacer toutes les valeurs manquantes par un 0. D’autres options sont également, qui peuvent être vus en examinant les menus déroulants dans le module.

#### <a name="feature-engineering-on-the-data"></a>Ingénierie de fonctionnalité sur les données

Il peut y avoir des millions de valeurs uniques pour certaines fonctionnalités par catégorie de grands ensembles de données. L’utilisation de méthodes naïve comme à chaud un codage pour la représentation de ces fonctionnalités par catégorie 3D haute est totalement impossible. Dans cette procédure pas à pas, nous vous montrer comment utiliser les fonctions de nombre à l’aide des modules de formation de Machine Azure intégrés pour générer des représentations compacte de ces variables par catégorie de grande dimension. Le résultat final est un petit modèle, les durées d’apprentissage plus rapides et de mesures de performances qui sont tout à fait comparables à l’utilisation d’autres techniques.

##### <a name="building-counting-transforms"></a>Création de transformations de comptage

Pour générer des fonctionnalités de nombre, nous utilisons le module de **Génération de comptage transformer** qui est disponible dans l’apprentissage automatique de Azure. Le module se présente comme suit :


![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/e0eqKtZ.png)
![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/OdDN0vw.png)


**Remarque importante** : dans la zone **nombre de colonnes** , nous entrez que nous souhaitons effectuer nombre de ces colonnes. En général, il s’agit (comme indiqué) haute-dimensional colonnes catégorielles. Au début, nous l’avons mentionné que le groupe de données Criteo a 26 colonnes catégorielles : à partir de Col15 à Col40. Ici, nous avons compter sur chacun d'entre eux et leurs index (à partir de 15 à 40, séparés par des virgules, comme indiqué).

Pour utiliser le module en mode MapReduce (appropriées pour les grands ensembles de données), nous ont besoin d’accéder à un cluster HDInsight Hadoop (celui utilisé pour l’exploration de la fonction peut être réutilisé à cet effet ainsi) et ses informations d’identification. Les figures précédentes, les renseignés valeurs ressemble (remplacer les valeurs fournies à titre d’illustration avec celles adaptées à votre propre cas d’utilisation).

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/05IqySf.png)

Dans la figure ci-dessus, nous montrons à entrer l’emplacement de l’objet blob d’entrée. Cet emplacement a réservée pour créer des tables de nombre données.


Après la fin de ce module, nous pouvons enregistrer la transformation pour plus tard en cliquant le module et en sélectionnant l’option **Enregistrer comme transformation** :

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/IcVgvHR.png)

Dans notre architecture d’essai ci-dessus, le groupe de données « ytransform2 » correspond précisément à une transformation de compte enregistré. Pour le reste de cette expérience, nous supposons que le lecteur utilisé un module de **Génération de comptage de transformer** des données pour générer des nombres et pouvez ensuite utiliser ces nombres nombre des fonctionnalités dans le train de générer et de tester des groupes de données.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Choisir le nombre de fonctionnalités pour inclure dans le cadre du train et test des groupes de données

Une fois que nous disposons d’un nombre de transformer prêts à l’emploi, l’utilisateur peut choisir les fonctionnalités à inclure dans leur train et l’utilisation du module de **Modifier des paramètres de Table nombre** de groupes de données de test. Nous ne montrons que ce module ici par souci d’exhaustivité, mais dans un souci de simplification n’utilisant pas il dans notre expérience.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/PfCHkVg.png)

Dans ce cas, comme vous pouvez le constater, nous avons choisi d’utiliser simplement le journal à cote et ignorer l’interruption de la colonne. Nous pouvons également définir des paramètres tels que le seuil d’emplacement garbage, le nombre des exemples pseudo-aléatoires préalables à ajouter pour le lissage et s’il faut utiliser les bruits LAPLACIEN ou non. Tous ces sont des fonctionnalités avancées et il est à noter que les valeurs par défaut sont un bon point de départ pour les utilisateurs qui apprennent à ce type de génération de la fonctionnalité.

##### <a name="data-transformation-before-generating-the-count-features"></a>Transformation de données avant de générer les fonctionnalités d’inventaire

Maintenant nous concentrer sur un point important sur la transformation de notre train et données de test avant qui génèrent réellement de fonctionnalités de décompte. Notez qu’il y a deux modules **d’Exécuter un Script R** avant de nous appliquer la transformation de nombre à nos données.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/aF59wbc.png)

Voici le script premier R :

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/3hkIoMx.png)


Dans ce script R, nous donner nos colonnes noms « Col1 » à « Col40 ». C’est parce que la transformation de nombre attend des noms de ce format.

Dans le deuxième script R, nous équilibrer la distribution entre classes positives et négatives (classes 1 et 0 respectivement) par la classe négatif de sous-échantillonnage. Le script de R ici montre comment procéder :

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/91wvcwN.png)

Dans ce simple script R, nous utilisons « pos\_neg\_rapport » pour définir le montant du solde entre la positive et les classes négatifs. Ceci est important puisque l’amélioration généralement de déséquilibre de la classe a des avantages de performance pour les problèmes de classification où la distribution de la classe est incliné (n’oubliez pas que dans notre cas, nous avons classes positif de 3,3 % et % de 96,7 négatif).

##### <a name="applying-the-count-transformation-on-our-data"></a>Application de la transformation de nombre de nos données

Enfin, nous pouvons utiliser le module **d’Appliquer une Transformation** à appliquer les nombre de transformations dans notre train et des groupes de données de test. Ce module prend la transformation nombre enregistré comme une entrée et jeux de données en tant que l’autre entrée train ou de test et renvoie des données avec des fonctionnalités de nombre. Il est indiqué ici :

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Un extrait de ce que le nombre de fonctionnalités l’aspect

Il est utile de voir à quoi ressemblent les fonctions count dans notre cas. Vous pouvez voir ici un extrait de ce :

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/FO1nNfw.png)

Dans cet extrait, nous montrent que pour les colonnes qui nous comptées sur, nous obtenir le nombre et connecter des chances en plus de toute backoffs pertinentes.

Nous sommes maintenant prêts à construire un modèle d’apprentissage automatique de Azure à l’aide de ces ensembles de données transformées. Dans la section suivante, nous montrons comment cela peut être fait.

#### <a name="azure-machine-learning-model-building"></a>Construction d’un modèle Azure apprentissage automatique

##### <a name="choice-of-learner"></a>Choix de l’étudiant

Tout d’abord, nous devons choisir un étudiant. Nous allons utiliser un arbre de décision des deux classe amplifiée comme notre étudiant. Voici les options par défaut pour cet utilisateur :

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/bH3ST2z.png)

Pour notre test, nous allons choisir les valeurs par défaut. Vous constatez que les valeurs par défaut sont généralement explicites et un bon moyen pour obtenir des configurations de référence rapides sur les performances. Vous pouvez améliorer sur les performances en balayant les paramètres si vous voulez une fois que vous avez une ligne de base.

#### <a name="train-the-model"></a>Apprentissage du modèle

Pour la formation, nous avons simplement les appeler un module du **Modèle de Train** . Les deux entrées sont au stagiaire de la classe de deux amplifiée un arbre de décision et notre dataset de train. Cela est illustré ici :

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/2bZDZTy.png)


#### <a name="score-the-model"></a>Score du modèle

Une fois que nous avons un modèle formé, nous sommes prêts pour évaluer le score du groupe de données de test et d’évaluer ses performances. Pour ce faire, nous utilisant le module du **Modèle de Score** illustré dans la figure suivante, ainsi que d’un module **d’Évaluer le modèle** :

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/fydcv6u.png)


### <a name="step5"></a>Étape 5 : Évaluer le modèle

Enfin, nous souhaiterions analyser les performances du modèle. En règle générale, pour les deux problèmes de classification (binaire) de classe, une bonne mesure est le AUC. Pour représenter ceci, nous raccorder le module du **Modèle de Score** à un module **d’Évaluer le modèle** pour cela. En cliquant sur **visualiser** sur le module **d’Évaluer le modèle** génère un graphique comme la suivante :

![Évaluation du module BDT modèle](./media/machine-learning-data-science-process-hive-criteo-walkthrough/0Tl0cdg.png)

Binaire (ou deux de classe) des problèmes de classification, une bonne mesure de précision de prévision est la zone sous courbe (AUC). Dans ce qui suit, nous afficher nos résultats sur notre dataset de test à l’aide de ce modèle. Pour obtenir cette information, cliquez sur le port de sortie du module **d’Évaluer le modèle** , puis **visualiser**.

![Visualiser le module du modèle d’évaluation](./media/machine-learning-data-science-process-hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step6"></a>Étape 6 : Publier le modèle comme un service Web
La possibilité de publier un modèle de formation de Machine Azure en tant que services web avec un minimum de complications est une fonctionnalité utile pour rendre largement accessible. Une fois cette opération effectuée, tout le monde peut effectuer des appels au service web avec des données d’entrée dont ils ont besoin des prévisions pour que le service web utilise le modèle pour renvoyer ces prévisions.

Pour ce faire, nous avons d’abord enregistrer notre modèle formé sous la forme d’un objet de modèle formé. Pour ce faire droit le module du **Modèle de Train** et à l’aide de l’option **Enregistrer en tant que modèle formé** .

Ensuite, nous devons créer l’entrée et la sortie des ports pour notre service web :

* un port d’entrée prend les données dans la même forme que les données dont nous avons besoin pour des prévisions
* un port de sortie renvoie obtenu étiquettes associées des probabilités.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Sélectionnez quelques lignes de données pour le port d’entrée

Il est pratique d’utiliser un module de **Transformation de SQL s’appliquent** sur seulement 10 lignes comme les données du port d’entrée. Sélectionnez simplement ces lignes de données pour notre port d’entrée à l’aide de la requête SQL illustrée ici :

![Données du port d’entrée](./media/machine-learning-data-science-process-hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Service Web
Maintenant, nous sommes prêts à exécuter une petite expérience qui peut être utilisée pour publier de notre service web.

#### <a name="generate-input-data-for-webservice"></a>Générer des données d’entrée pour le service Web

Comme une étape placée, étant donné que le tableau de nombre est grand, nous prendre quelques lignes de données de test et générer des données de sortie à partir de celui-ci avec des fonctionnalités de nombre. Cela peut être le format des données d’entrée pour notre webservice. Cela est illustré ici :

![Créer les données d’entrée BDT](./media/machine-learning-data-science-process-hive-criteo-walkthrough/OEJMmst.png)

>[AZURE.NOTE] Pour le format d’entrée des données, nous utilisons maintenant la sortie du module **Featurizer du nombre** . Une fois la fin de cette expérience, enregistrer la sortie dans le module **Featurizer de nombre** sous la forme d’un groupe de données. Ce groupe de données est utilisé pour les données d’entrée dans le service Web.

#### <a name="scoring-experiment-for-publishing-webservice"></a>Expérience de score pour le service de publication Web

Tout d’abord, nous montrons à quoi il ressemble. La structure essentielle est un module du **Modèle de Score** qui accepte de notre objet modèle formé et quelques lignes de données d’entrée que nous avons généré dans les étapes précédentes en utilisant le module **Featurizer du nombre** . Nous utilisons des « Sélectionner les colonnes dans Dataset » pour projeter les étiquettes Scored et les probabilités de Score.

![Sélectionner des colonnes dans un groupe de données](./media/machine-learning-data-science-process-hive-criteo-walkthrough/kRHrIbe.png)

Notez comment le module de **Sélectionner des colonnes dans un groupe de données** peut être utilisé pour le « filtrage » données à partir d’un groupe de données. Nous afficher le contenu ici :

![Filtrage avec la sélection des colonnes dans le module du groupe de données](./media/machine-learning-data-science-process-hive-criteo-walkthrough/oVUJC9K.png)

Pour obtenir le bleue ports entrée et sortie, vous cliquez sur **préparer webservice** en bas à droite. L’exécution de cette expérience nous permet également de publier le service web : cliquez sur le **SERVICE WEB de publication** en bas à droite, illustré ici :

![Service Web de publication](./media/machine-learning-data-science-process-hive-criteo-walkthrough/WO0nens.png)


Une fois le service Web est publié, nous avons souvent redirigés vers une page qui se présente ainsi :

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/YKzxAA5.png)

Nous voir deux liens de services Web sur le côté gauche :

* Le Service de **Demande/réponse** (ou RR) sont destinés aux prévisions unique et sont ce que nous allons utiliser dans cet atelier.
* Le Service **D’exécution du traitement par lots** (BES) est utilisé pour les prévisions de lot et nécessite que les données d’entrée utilisées pour effectuer des prévisions se trouvent dans le stockage Blob Azure.

En cliquant sur le lien de **Que demande/réponse** nous mène à une page qui nous donne conserves préalable de code en C#, python et R. Ce code peut être utilisé pour effectuer des appels vers le service Web. Notez que la clé d’API sur cette page doit être utilisé pour l’authentification.

Il est pratique de copier ce code python sur une nouvelle cellule dans le bloc-notes de IPython.

Ici, nous affichons un segment de code python avec la clé API correcte.

![Code Python](./media/machine-learning-data-science-process-hive-criteo-walkthrough/f8N4L4g.png)


Notez que nous avons remplacé la clé API par défaut avec la clé de l’API de nos webservices. En cliquant sur **exécuter** sur cette cellule dans un bloc-notes IPython donne la réponse suivante :

![Réponse de IPython](./media/machine-learning-data-science-process-hive-criteo-walkthrough/KSxmia2.png)

Nous constatons que pour les deux exemples de test que nous posées (dans le cadre du script python JSON), nous obtenons les réponses sous la forme « Scored Labels, les probabilités de Scored ». Notez que dans ce cas, nous avons choisi les valeurs par défaut que le code prédéfini fournit (0 pour toutes les colonnes de type numérique et la chaîne « value » pour toutes les colonnes par catégorie).

Ceci conclut notre procédure pas à pas fin bout à bout illustrant la gestion de groupe de données à grande échelle à l’aide de la formation de Machine Azure. Nous avons démarré avec un téraoctet de données, construit un modèle de prévision et déployé comme un service web dans le nuage.
