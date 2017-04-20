<properties
    pageTitle="Exploration de données et modélisation avec allumage | Microsoft Azure"
    description="Présente les capacités de modélisation et l’exploration de données de la boîte à outils MLlib d’allumage."
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
    ms.date="10/07/2016"
    ms.author="deguhath;bradsev;gokuma" />

# <a name="data-exploration-and-modeling-with-spark"></a>Exploration de données et modélisation avec allumage

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Cette procédure pas à pas utilise HDInsight Spark faire d’exploration de données et classification binaire et les tâches de modélisation sur un échantillon de la NYC de régression taxi voyage 2013 dataset du prix.  Il vous guide à travers les étapes du [Processus de données scientifiques](http://aka.ms/datascienceprocess), de bout en bout, à l’aide de cluster un HDInsight Spark pour le traitement et Azure BLOB pour stocker les données et les modèles. Le processus explore et visualise des données importées à partir d’un Blob de stockage Azure, puis prépare les données pour construire des modèles prédictifs. Ces modèles sont build à l’aide de la boîte à outils MLlib d’allumage d’effectuer des tâches de modélisation de régression et classification binaire.

- La tâche de **classification binaire** est de prédire si une info-bulle est payée pour le voyage. 
- La tâche de **régression** doit prévoir la quantité de l’info-bulle basé sur d’autres fonctionnalités de pointe. 

Les modèles que nous utilisons : régression logistique et linéaire, aléatoires, arborescences et des forêts dégradés amplifiées

- [Régression linéaire avec SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) est un modèle de régression linéaire qui utilise une méthode de descente du Gradient stochastique (SGD) et pour l’optimisation et la fonctionnalité de mise à l’échelle afin de prévoir les montants d’info-bulle payé. 
- Régression de [la régression logistique avec LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) ou « logit », est un modèle de régression qui peut être utilisé lorsque la variable dépendante est catégorique de faire de la classification des données. LBFGS est un algorithme d’optimisation quasi Newton qui est une approximation de l’algorithme de Broyden-Fletcher-Goldfarb-Shanno (BFGS) à l’aide d’une quantité limitée de mémoire de l’ordinateur et qui est largement utilisé dans l’apprentissage automatique.
- [Les forêts aléatoire](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) sont des ensembles d’arbres de décision.  Ils associent plusieurs arborescences de décision pour réduire le risque de dépassement de l’ajustement. Forêts aléatoires sont utilisés pour la classification et de régression et peuvent gérer les fonctions par catégorie et peuvent être étendus à la valeur de classification de multiclass. Ils ne nécessitent pas de mise à l’échelle d’une fonctionnalité et sont en mesure de capturer la non linéarité et la fonctionnalité des interactions. Forêts aléatoires sont une de l’ordinateur plus de succès apprentissage des modèles pour la classification et la régression.
- [Dégradé amplifiée arborescences](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) sont des ensembles d’arbres de décision. GBTs formation des arbres de décision itérative pour minimiser une fonction de perte. GBTs sont utilisés pour la classification et de régression peut gérer les fonctions par catégorie, ne nécessitent pas de mise à l’échelle d’une fonctionnalité et sont en mesure de capturer la non linéarité et la fonctionnalité des interactions. Il peuvent également servir dans un paramètre de classification de multiclass.

Les étapes de modélisation également contient du code montrant comment former, évaluer et enregistrer chaque type de modèle. Python a été utilisé pour la solution de code et afficher les placettes pertinentes.   


>[AZURE.NOTE] Bien que le Kit d’outils MLlib d’allumage est conçu pour fonctionner sur grands ensembles de données, un échantillon relativement faible (~ 30 Mo 170K lignes, environ 0,1 % le dataset d’origine NYC) est utilisé ici pour plus de commodité. L’exercice ici s’exécute efficacement (en 10 minutes environ) sur un cluster HDInsight avec 2 nœuds du travailleur. Le même code, avec des modifications mineures, peut être utilisé pour traiter des jeux de données volumineux avec les modifications nécessaires pour la mise en cache des données dans la mémoire et la modification de la taille de cluster.

## <a name="prerequisites"></a>Conditions préalables

Vous avez besoin d’un compte Azure et un cluster nécessité un 1.6 d’étincelle 3.4 HDInsight allumage vous HDInsight pour effectuer cette procédure pas à pas. Consultez la [Vue d’ensemble de données scientifiques à l’aide d’allumage sur Azure HDInsight](machine-learning-data-science-spark-overview.md) pour obtenir des instructions sur la manière de satisfaire ces exigences. Cette rubrique contient également une description des données NYC 2013 Taxi utilisées ici et des instructions sur la façon d’exécuter le code à partir d’un ordinateur portable de Jupyter sur le cluster d’allumage. L’ordinateur portable **pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb** qui contient les exemples de code dans cette rubrique est disponible dans [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark). 


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Le programme d’installation : des emplacements de stockage, les bibliothèques et le contexte étincelle prédéfini

Allumage est en mesure de lire et écrire dans Azure stockage Blob (également connu sous le nom WASB). Afin que vos données existantes stockées il peuvent être traitées à l’aide d’allumage et les résultats stockés à nouveau dans WASB.

Pour enregistrer les modèles ou les fichiers dans WASB, le chemin d’accès doit être correctement spécifié. Le conteneur par défaut lié au cluster étincelle peut être référencé à l’aide d’un chemin commençant par : « wasb : / / / ». Autres emplacements référencés par « wasb : / / ».


### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Définir des chemins d’accès de répertoire pour les emplacements de stockage en WASB

L’exemple de code suivant spécifie l’emplacement des données à lire et le chemin d’accès du répertoire de stockage de modèle dans lequel la sortie de modèle est enregistrée :


    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Bibliothèques d’importation

Configuration requiert également l’importation de bibliothèques nécessaires. Définir le contexte d’allumage et d’importer les bibliothèques nécessaires par le code suivant :


    # IMPORT LIBRARIES
    import pyspark
    from pyspark import SparkConf
    from pyspark import SparkContext
    from pyspark.sql import SQLContext
    import matplotlib
    import matplotlib.pyplot as plt
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    import atexit
    from numpy import array
    import numpy as np
    import datetime


### <a name="preset-spark-context-and-pyspark-magics"></a>Contexte d’allumage prédéfini et PySpark magics

Les noyaux de PySpark qui sont fournis avec les ordinateurs portables de Jupyter ont un contexte prédéfini. Par conséquent, vous n’avez pas besoin de définir l’allumage ou contextes de ruche explicitement avant de commencer à travailler avec l’application que vous développez. Les contextes sont disponibles pour vous par défaut. Les contextes sont :

- SC - pour l’allumage 
- sqlContext - pour ruche

Le noyau PySpark fournit certaines prédéfinis « magics », qui sont des commandes spéciales que vous pouvez appeler avec %%. Il existe deux ces commandes sont utilisées dans ces exemples de code.

- **%% local** Spécifie que le code dans les lignes suivantes doit être exécuté localement. Code doit être le code valide de Python.
- **%%sql -o <variable name>** Exécute une requête de ruche contre le sqlContext. Si le paramètre -o est passé, le résultat de la requête est rendue persistante dans le %% contexte de Python local sous la forme d’un DataFrame de Pandas.
 

Pour plus d’informations sur les noyaux pour prédéfinis et les ordinateurs portables de Jupyter « magics » qui leur fournissent, consultez [noyaux disponibles pour les ordinateurs portables de Jupyter avec les clusters Linux d’allumage HDInsight sur HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).
 

## <a name="data-ingestion-from-public-blob"></a>Réception de données à partir de l’objet blob public

La première étape du processus de données science est envoyer les données à analyser à partir de sources où est réside dans votre environnement de modélisation et d’exploration de données. L’environnement est une étincelle dans cette procédure pas à pas. Cette section contient le code pour effectuer une série de tâches :

- l’exemple de données à modéliser d’acquisition
- lire dans le dataset d’entrée (stocké sous forme de fichier .tsv)
- mettre en forme et de nettoyer les données
- créer et mettre en cache dans la mémoire des objets (RDDs ou données des trames)
- Enregistrer sous la forme d’une table temporaire dans le contexte de SQL.

Voici le code pour la réception de données.

    # INGEST DATA

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_train_file = sc.textFile(taxi_train_file_loc)
    
    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_train_file.first()
    fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
    fields[7].dataType = IntegerType() #Pickup hour
    fields[8].dataType = IntegerType() # Pickup week
    fields[9].dataType = IntegerType() # Weekday
    fields[10].dataType = IntegerType() # Passenger count
    fields[11].dataType = FloatType() # Trip time in secs
    fields[12].dataType = FloatType() # Trip distance
    fields[19].dataType = FloatType() # Fare amount
    fields[20].dataType = FloatType() # Surcharge
    fields[21].dataType = FloatType() # Mta_tax
    fields[22].dataType = FloatType() # Tip amount
    fields[23].dataType = FloatType() # Tolls amount
    fields[24].dataType = FloatType() # Total amount
    fields[25].dataType = IntegerType() # Tipped or not
    fields[26].dataType = IntegerType() # Tip class
    taxi_schema = StructType(fields)
    
    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
    taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
    
    
    # CREATE DATA FRAME
    taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)
    
    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()
    
    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SORTIE :**

Temps nécessaire pour exécuter au-dessus de la cellule : 51.72 secondes


## <a name="data-exploration--visualization"></a>Visualisation et exploration de données

Une fois que les données a été mises en étincelle, l’étape suivante du processus de données science est d’obtenir une connaissance plus approfondie des données grâce à l’exploration et de visualisation. Dans cette section, nous examiner les données de taxi à l’aide de requêtes SQL et les variables de la cible et les fonctionnalités potentiels pour l’inspection visuelle de traçage. En particulier, nous tracer la fréquence des décomptes de passagers dans les voyages de taxi, la fréquence des montants de l’info-bulle, et comment les conseils varient selon le type et le montant du paiement.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Tracer un histogramme des fréquences de nombre de passagers dans l’échantillon de trajets de taxi

Ce code et les extraits de code suivants utilisent magique SQL pour interroger l’échantillon et le local magique pour tracer les données.

- **Magique SQL (`%%sql`)** Le noyau HDInsight PySpark prend en charge les requêtes de HiveQL d’inline simple contre le sqlContext. Le (-o nom_variable) argument persiste la sortie de la requête SQL sous la forme d’un DataFrame de Pandas sur le serveur Jupyter. Cela signifie qu’il est disponible en mode local.
- La ** `%%local` magique** est utilisée pour exécuter localement le code sur le serveur Jupyter, ce qui est de la headnode du cluster HDInsight. En général, vous utilisez `%%local` magique en conjonction avec le `%%sql` magique avec le paramètre -o. Le paramètre-o soit persistant de la sortie de la requête SQL local puis %% magic local déclenche l’ensemble suivant d’extrait de code pour exécuter localement sur la sortie des requêtes SQL qui est conservée localement

La sortie est automatiquement affichée une fois que vous exécutez le code.

Cette requête extrait les sorties en fonction du nombre de passagers. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Ce code crée une trame de données locale à partir du résultat de la requête et trace les données. Le `%%local` magique crée une trame de données locale, `sqlResults`, qui peut être utilisée pour le traçage avec matplotlib. 

>[AZURE.NOTE] Ce magic PySpark est utilisé plusieurs fois dans cette procédure pas à pas. Si la quantité de données est volumineuse, vous devez exemple pour créer une trame de données qui peut tenir dans la mémoire locale.

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local
    
    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Voici le code pour tracer les sorties en nombre de passagers

    # PLOT PASSENGER NUMBER VS. TRIP COUNTS
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline
    
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**SORTIE :**

![Fréquence de déclenchement en fonction du nombre de passagers](./media/machine-learning-data-science-spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Vous pouvez sélectionner parmi les différents types de visualisations (Table, secteur, ligne, zone ou barre) en utilisant les boutons de menu de **Type** dans le bloc-notes. Le tracé de la barre s’affiche ici.
    
### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Tracer un histogramme des montants de l’info-bulle et le montant du pourboire varie en fonction du montants count et le tarif de passager.

Utiliser une requête SQL pour des exemples de données.

    #PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE
    
    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped 
    FROM taxi_train 
    WHERE passenger_count > 0 
    AND passenger_count < 7 
    AND fare_amount > 0 
    AND fare_amount < 200 
    AND payment_type in ('CSH', 'CRD') 
    AND tip_amount > 0 
    AND tip_amount < 25


Cette cellule de code utilise la requête SQL pour créer des données de trois tracés.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local
    
    # HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()
    
    # TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()
    
    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 100, -2, 20])
    plt.show()


**SORTIE :** 

![Conseil la distribution du montant](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-distribution.png)

![Conseil le montant en fonction du nombre de passagers](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Montant par le montant des frais de Conseil](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-fare-amount.png)


## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Préparation de l’ingénierie, de transformation et de données pour la modélisation des fonctionnalités
Cette section décrit et fournit le code pour les procédures utilisées pour préparer des données à utiliser dans la modélisation de ML. Il montre comment effectuer les tâches suivantes :

- Créer une nouvelle fonction de mise au rebus des heures dans les plages de temps de trafic
- Index et coder les fonctionnalités par catégorie
- Créer des objets point étiquetée pour l’entrée en fonctions de ML
- Créer un sondage secondaire des données et la diviser en formation et de test des jeux
- Mise à l’échelle d’une fonction
- Objets cache en mémoire


### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Créer une nouvelle fonction de mise au rebus des heures dans les plages de temps de trafic

Ce code montre comment créer une nouvelle fonction de mise au rebus des heures dans les plages de temps le trafic, puis comment mettre en cache la trame de données qui en résulte dans la mémoire. Lorsque résilient distribué des groupes de données (RDDs) et les données des trames sont utilisés à plusieurs reprises, la mise en cache conduit à des temps d’exécution améliorées. En conséquence, en cache RDDs et les données des trames à différentes étapes de la procédure pas à pas. 

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train 
    """
    taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    # THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
    # MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()

**SORTIE :** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Index et coder les fonctionnalités par catégorie pour l’entrée en fonctions de modélisation

Cette section indique comment indexer ou coder les fonctionnalités par catégorie pour les entrées dans les fonctions de modélisation. La modélisation et prédire les fonctions de MLlib nécessitent des fonctionnalités avec des données d’entrée par catégorie pour être indexé ou codées avant d’être utilisés. Selon le modèle, vous devez indexer ou de les coder de différentes façons :  

- **Basée sur l’arbre de modélisation** nécessite des catégories à coder en tant que valeurs numériques (par exemple, une fonction avec trois catégories susceptibles d’être codée avec 0, 1, 2). Ceci est fourni en fonction de [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) de MLlib. Cette fonction encode une colonne de type chaîne d’étiquettes à une colonne d’indices d’étiquette qui sont commandés par les fréquences de l’étiquette. Bien qu’indexée avec des valeurs numériques pour l’entrée et la gestion des données, les algorithmes basés sur arborescence peuvent être spécifiés pour les traiter de manière appropriée en tant que catégories. 

- **Modèles de logistique et de la droite de régression linéaire** nécessitent à chaud un codage, où, par exemple, une fonction avec trois catégories peut être développée en trois colonnes de fonctionnalité, avec chaque contenant 0 ou 1 selon la catégorie d’une observation. MLlib fournit la fonction [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) pour faire à chaud un codage. Cet encodeur mappe une colonne d’index de l’étiquette d’une colonne de vecteurs binaires, avec au plus un seul une valeur. Ce codage permet d’algorithmes qui attendent des fonctionnalités à valeurs numériques, tels que de la régression logistique, à appliquer aux fonctionnalités par catégorie.

Voici le code pour indexer et coder les fonctionnalités par catégorie :


    # INDEX AND ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES    
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer
    
    # INDEX AND ENCODE VENDOR_ID
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_train_with_newFeatures)
    encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
    encoded1 = encoder.transform(indexed)
    
    # INDEX AND ENCODE RATE_CODE
    stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
    model = stringIndexer.fit(encoded1)
    indexed = model.transform(encoded1)
    encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
    encoded2 = encoder.transform(indexed)
    
    # INDEX AND ENCODE PAYMENT_TYPE
    stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
    model = stringIndexer.fit(encoded2)
    indexed = model.transform(encoded2)
    encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
    encoded3 = encoder.transform(indexed)
    
    # INDEX AND TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE :**

Temps nécessaire pour exécuter au-dessus de la cellule : 1,28 secondes

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Créer des objets point étiquetée pour l’entrée en fonctions de ML

Cette section contient le code qui indique comment les données catégoriques de texte sous la forme d’un type de données appelé point d’index et comment l’encoder afin qu’il peut être utilisé pour former et test de régression logistique de MLlib et d’autres modèles de classification. Objets point étiquetées sont résilient distribué des groupes de données (RDD) au format d’une façon qui est requis par la plupart des algorithmes ML dans MLlib comme données d’entrée. Une [étiquette de point](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) est à un vecteur local, dense ou clairsemée, associé à une étiquette/réponse.  

Cette section contient le code qui indique comment les données catégoriques de texte sous la forme d’un type de données [nommé point](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) d’index et comment l’encoder afin qu’il peut être utilisé pour former et test de régression logistique de MLlib et d’autres modèles de classification. Objets point étiquetées sont résilient distribué des groupes de données (RDD) qui se compose d’une étiquette (variable cible/réponse) et d’un vecteur de fonctionnalité. Ce format est nécessaire en tant qu’entrée par de nombreux algorithmes ML dans MLlib.

Voici le code pour indexer et coder les fonctionnalités de texte pour la classification binaire.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


Voici le code permettant de coder et d’indexer des fonctionnalités par catégorie de texte pour une analyse de régression linéaire.

    # FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])

        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt
    
    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Créer un sondage secondaire des données et la diviser en formation et de test des jeux

Ce code crée un échantillonnage aléatoire des données (25 % est utilisé ici). Bien qu’il ne soit pas obligatoire dans cet exemple, en raison de la taille du groupe de données, nous vous montrer comment vous pouvez échantillonner ici afin de savoir comment l’utiliser pour votre propre problème lorsque cela est nécessaire. Lorsque les échantillons sont volumineuses, cela permet de gagner beaucoup de temps lors de modèles de formation. Ensuite, nous fractionner l’exemple dans une partie de la formation (75 % ici) et une partie de test (25 % ici) à utiliser dans la classification et la modélisation de régression.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.sql.functions import rand

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)
    
    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
    indexedTESTbinary = testData.map(parseRowIndexingBinary)
    oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
    oneHotTESTbinary = testData.map(parseRowOneHotBinary)
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg = trainData.map(parseRowIndexingRegression)
    indexedTESTreg = testData.map(parseRowIndexingRegression)
    oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
    oneHotTESTreg = testData.map(parseRowOneHotRegression)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE :**

Temps nécessaire pour exécuter au-dessus de la cellule : pas de 0,24 secondes


### <a name="feature-scaling"></a>Mise à l’échelle d’une fonction

Fonctionnalité mise à l’échelle, également appelé normalisation des données, ainsi que les fonctionnalités avec des valeurs d’exécution largement sont pas donné excessive Peser dans la fonction de l’objectif. Le code de mise à l’échelle d’une fonctionnalité utilise le [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) à l’échelle les fonctionnalités à l’écart de l’unité. Il est fourni par MLlib pour une utilisation dans la droite de régression linéaire avec stochastique dégradé descente (SGD), un algorithme populaire de formation d’une large gamme d’autre apprentissage des modèles de régressions de REGULARISES ou de machines vectorielles (SVM) de l’ordinateur.

>[AZURE.NOTE] Nous avons trouvé l’algorithme LinearRegressionWithSGD à être sensibles à la fonctionnalité de mise à l’échelle.

Voici le code à l’échelle des variables pour une utilisation avec l’algorithme SGD linéaire REGULARISES.

    # FEATURE SCALING

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    
    # SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
    label = oneHotTRAINreg.map(lambda x: x.label)
    features = oneHotTRAINreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
    
    label = oneHotTESTreg.map(lambda x: x.label)
    features = oneHotTESTreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE :**

Temps nécessaire pour exécuter au-dessus de la cellule : 13.17 secondes


### <a name="cache-objects-in-memory"></a>Objets cache en mémoire

La durée de la formation et de test des algorithmes de ML peut être réduite par la mise en cache de la trame de données d’entrée les objets utilisés pour la classification, régression et à l’échelle les fonctionnalités.

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.cache()
    indexedTESTbinary.cache()
    oneHotTRAINbinary.cache()
    oneHotTESTbinary.cache()
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.cache()
    indexedTESTreg.cache()
    oneHotTRAINreg.cache()
    oneHotTESTreg.cache()
    
    # SCALED FEATURES
    oneHotTRAINregScaled.cache()
    oneHotTESTregScaled.cache()
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE :** 

Temps nécessaire pour exécuter au-dessus de la cellule : 0,15 secondes


## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Prévoir ou non une info-bulle est réglée au moyen de modèles de classification binaire

Cette section indique comment utiliser trois modèles pour la tâche de classification binaire permettant de prévoir une info-bulle est payer pour un trajet de taxi ou non. Les modèles présentés sont :

- REGULARISES de régression logistique 
- Modèle de forêt aléatoire
- Arborescences d’amplification dégradés

Chaque modèle de création de section de code est divisé en étapes : 

1. Données de **formation de modèle** avec un jeu de paramètres
2. **Évaluation du modèle** sur un ensemble de données test avec des mesures
3. **Enregistrer le modèle** dans l’objet blob de consommation future

### <a name="classification-using-logistic-regression"></a>Classification à l’aide de la régression logistique

Le code de cette section montre comment former, évaluer et enregistrer un modèle de régression logistique avec [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) qui le prévoit ou non une info-bulle est payée pour un voyage dans le dataset de voyage et les frais de taxi NYC.

**Apprentissage du modèle de régression logistique à l’aide de VC et balayage de hyperparameter**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics
    
    
    # CREATE MODEL WITH ONE SET OF PARAMETERS
    logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                                   regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                                   tolerance=0.0001, validateData=True, numClasses=2)
    
    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitModel.weights))
    print("Intercept: " + str(logitModel.intercept))
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE :** 

Les coefficients : [0.0082065285375,-0.0223675576104,-0.0183812028036, - 3.48124578069e-05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Ordonnée à l’origine :-0.0111216486893

Temps nécessaire pour exécuter au-dessus de la cellule : 14.43 secondes

**Évaluer le modèle de la classification binaire avec des mesures standard**

    #EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # PREDICT ON TEST DATA WITH MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))
    
    # INSTANTIATE METRICS OBJECT
    metrics = BinaryClassificationMetrics(predictionAndLabels)

    # AREA UNDER PRECISION-RECALL CURVE
    print("Area under PR = %s" % metrics.areaUnderPR)

    # AREA UNDER ROC CURVE
    print("Area under ROC = %s" % metrics.areaUnderROC)
    metrics = MulticlassMetrics(predictionAndLabels)

    # OVERALL STATISTICS
    precision = metrics.precision()
    recall = metrics.recall()
    f1Score = metrics.fMeasure()
    print("Summary Stats")
    print("Precision = %s" % precision)
    print("Recall = %s" % recall)
    print("F1 Score = %s" % f1Score)


    ## SAVE MODEL WITH DATE-STAMP
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;
    logitModel.save(sc, dirfilename);
    
    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SORTIE :** 

Zone sous PR = 0.985297691373

Zone sous la lune décroissante = 0.983714670256

Statistiques récapitulatives

Précision = 0.984304060189

Rappeler = 0.984304060189

F1 Score = 0.984304060189

Temps nécessaire pour exécuter au-dessus de la cellule : 57.61 secondes

**Tracer la courbe ROC.**

Le *predictionAndLabelsDF* est enregistré sous la forme d’une table, *tmp_results*, dans la cellule précédente. *tmp_results* peut servir à faire des requêtes et des résultats de sortie dans la trame de données sqlResults pour le tracé. Voici le code.


    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Voici le code pour effectuer des prévisions et tracer la courbe de ROC.

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    # MAKE PREDICTIONS
    predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVE
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()
    

**SORTIE :**

![Curve.png ROC de régression logistique](./media/machine-learning-data-science-spark-data-exploration-modeling/logistic-regression-roc-curve.png)


### <a name="random-forest-classification"></a>Classification de forêt aléatoire

Le code de cette section montre comment former, évaluer et enregistrer un modèle de forêt aléatoire qui le prévoit ou non une info-bulle est payée pour un voyage dans le dataset de voyage et les frais de taxi NYC.
    
    #PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics
    
    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    # TRAIN RANDOMFOREST MODEL
    rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                           categoricalFeaturesInfo=categoricalFeaturesInfo,
                                           numTrees=25, featureSubsetStrategy="auto",
                                           impurity='gini', maxDepth=5, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRINT TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())
    
    # PREDICT ON TEST DATA AND EVALUATE
    predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
    
    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)
    
    # PERSIST MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp;
    dirfilename = modelDir + rfclassificationfilename;
    
    rfModel.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE :**

Zone sous la lune décroissante = 0.985297691373

Temps nécessaire pour exécuter au-dessus de la cellule : 31.09 secondes


### <a name="gradient-boosting-trees-classification"></a>Dégradé classification arborescences AMPLIFICATION

Le code de cette section montre comment former, évaluer et enregistrer un modèle arborescences AMPLIFICATION dégradé qui le prévoit ou non une info-bulle est payée pour un voyage dans le voyage de taxi NYC et prix du dataset.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    
    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())
    
    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
    
    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)
    
    # PERSIST MODEL IN A BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
    dirfilename = modelDir + btclassificationfilename;
    
    gbtModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE :**

Zone sous la lune décroissante = 0.985297691373

Temps nécessaire pour exécuter au-dessus de la cellule : 19.76 secondes


## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Prévoir des montants de conseil des ADPIC taxi avec les modèles de régression

Cette section montre comment les trois modèles d’utilisation de la tâche de régression de prédiction de la quantité de l’info-bulle payer pour un trajet de taxi basé sur d’autres fonctionnalités de pointe. Les modèles présentés sont :

- REGULARISES de régression linéaire
- Forêt aléatoire
- Arborescences d’amplification dégradés

Ces modèles ont été décrites dans l’introduction. Chaque modèle de création de section de code est divisé en étapes : 

1. Données de **formation de modèle** avec un jeu de paramètres
2. **Évaluation du modèle** sur un ensemble de données test avec des mesures
3. **Enregistrer le modèle** dans l’objet blob de consommation future

### <a name="linear-regression-with-sgd"></a>Régression linéaire avec SGD 

Le code de cette section montre comment utiliser les fonctionnalités de mise à l’échelle pour entraîner une régression linéaire utilise la descente de gradient stochastique (SGD) pour l’optimisation et comment le score, d’évaluer et d’enregistrer le modèle dans le stockage des Blob Azure (WASB).

>[AZURE.TIP] Dans notre expérience, il peut y avoir des problèmes avec la convergence des modèles de LinearRegressionWithSGD, et les paramètres doivent être modifiés/optimisé avec soin pour obtenir un modèle valide. Mise à l’échelle des variables considérablement aide de convergence. 


    #PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
    from pyspark.mllib.evaluation import RegressionMetrics
    from scipy import stats
    
    # USE SCALED FEATURES TO TRAIN MODEL
    linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(linearModel.weights))
    print("Intercept: " + str(linearModel.intercept))
    
    # SCORE ON SCALED TEST DATA-SET & EVALUATE
    predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
    testMetrics = RegressionMetrics(predictionAndLabels)
    
    # PRINT TEST METRICS
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;
    
    linearModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE :**

Les coefficients : [0.00457675809917,-0.0226314167349,-0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981,-0.000987181489428,-0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995,-0.00990211159703,-0.00637410344522, 0.545083566179,-0.536756072402, 0.0105762393099,-0.0130117577055, 0.0129304737772,-0.00171065945959]

Intercepter : 0.853872718283

RMSE = 1.24190115863

Sqr-R = 0.608017146081

Temps nécessaire pour exécuter au-dessus de la cellule : 58,42 secondes


### <a name="random-forest-regression"></a>Régression de Random forêt

Le code de cette section montre comment former, évaluer et enregistrer une régression aléatoire qui prévoit le montant de pointe pour les données de voyage de taxi NYC.


    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    
    
    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())
    
    ## PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp;
    dirfilename = modelDir + rfregressionfilename;
    
    rfModel.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE :**

RMSE = 0.891209218139

Sqr-R = 0.759661334921

Temps nécessaire pour exécuter au-dessus de la cellule : 49.21 secondes


### <a name="gradient-boosting-trees-regression"></a>Dégradé régression arborescences AMPLIFICATION

Le code de cette section montre comment former, évaluer et enregistrer un modèle arborescences AMPLIFICATION dégradé qui prévoit le montant de pointe pour les données de voyage de taxi NYC.

**Formation et évaluation**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils
    
    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)
    
    ## EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)
    
    # CONVER RESULTS TO DF AND REGISER TEMP TABLE
    test_predictions = sqlContext.createDataFrame(predictionAndLabels)
    test_predictions.registerTempTable("tmp_results");
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE :**

RMSE = 0.908473148639

Sqr-R = 0.753835096681

Temps nécessaire pour exécuter au-dessus de la cellule : 34.52 secondes

**Traçage**

*tmp_results* est enregistré sous la forme d’une table de la ruche dans la cellule précédente. Résultats de la table sont consignés dans la trame de données *sqlResults* pour le tracé. Voici le code

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Voici le code pour tracer les données à l’aide du serveur Jupyter.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    import numpy as np

    # PLOT 
    ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
    plt.axis([-1, 20, -1, 20])
    plt.show(ax)
    

**SORTIE :**

![Réel-vs-prévue-Conseil-montants](./media/machine-learning-data-science-spark-data-exploration-modeling/actual-vs-predicted-tips.png)

    
## <a name="clean-up-objects-from-memory"></a>Nettoyer les objets de la mémoire

Utilisez `unpersist()` pour supprimer les objets mis en cache dans la mémoire.
        
    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.unpersist()
    indexedTESTbinary.unpersist()
    oneHotTRAINbinary.unpersist()
    oneHotTESTbinary.unpersist()
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.unpersist()
    indexedTESTreg.unpersist()
    oneHotTRAINreg.unpersist()
    oneHotTESTreg.unpersist()
    
    # SCALED FEATURES
    oneHotTRAINregScaled.unpersist()
    oneHotTESTregScaled.unpersist()


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Enregistrement des emplacements de stockage des modèles de consommation et d’attribution de scores

Pour consommer et le score d’un groupe de données indépendant décrit dans la [Score et évaluer des modèles d’apprentissage automatique d’allumage intégré](machine-learning-data-science-spark-model-consumption.md) rubrique, vous devez copier et coller ces noms de fichier contenant les modèles enregistrés créés ici dans le carnet de Jupyter de la consommation. Voici le code pour imprimer les chemins d’accès aux fichiers de modèle dont vous avez besoin il.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**SORTIE**

logisticRegFileLoc = modelDir + « LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568 »

linearRegFileLoc = modelDir + « LinearRegressionWithSGD_2016-05-0317_05_21.577773 »

randomForestClassificationFileLoc = modelDir + « RandomForestClassification_2016-05-0317_04_11.950206 »

randomForestRegFileLoc = modelDir + « RandomForestRegression_2016-05-0317_06_08.723736 »

BoostedTreeClassificationFileLoc = modelDir + « GradientBoostingTreeClassification_2016-05-0317_04_36.346583 »

BoostedTreeRegressionFileLoc = modelDir + « GradientBoostingTreeRegression_2016-05-0317_06_51.737282 »


## <a name="whats-next"></a>Quel est l’avenir ?

Maintenant que vous avez créé des modèles de classification et de régression avec la MlLib d’allumage, vous êtes prêt à apprendre de score et l’évaluation de ces modèles. Bloc-notes de modélisation et de l’exploration de données avancées explore plus profonds, y compris la validation croisée, hyper-paramètre de balayage et le modèle d’évaluation. 

**Consommation de modèle :** Pour savoir comment évaluer et évaluer les modèles de classification et de régression créés dans cette rubrique, consultez [Score et l’évaluation des modèles de formation intégré d’allumage de machine](machine-learning-data-science-spark-model-consumption.md).

**Balayage de hyperparameter et validation croisée**: voir [avancées d’exploration de données et modélisation avec allumage](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) sur comment les modèles peuvent être formés à l’aide de balayage de validation croisée et paramètre hyper



