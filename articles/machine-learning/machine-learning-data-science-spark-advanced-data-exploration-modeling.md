<properties
    pageTitle="Avancées d’exploration de données et modélisation avec allumage | Microsoft Azure"
    description="HDInsight Spark permet de faire d’exploration de données et former les modèles binaires de classification et de régression en utilisant l’optimisation de validation croisée et hyperparameter."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="deguhath;bradsev;gokuma" />

# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Exploration de données et modélisation avec allumage 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Cette procédure pas à pas utilise HDInsight Spark pour faire d’exploration de données et de former la classification binaire et modèles de régression à l’aide de validation croisée et l’optimisation de hyperparameter sur un échantillon de la NYC taxi voyage 2013 dataset du prix. Il vous guide à travers les étapes du [Processus de données scientifiques](http://aka.ms/datascienceprocess), de bout en bout, à l’aide de cluster un HDInsight Spark pour le traitement et Azure BLOB pour stocker les données et les modèles. Le processus explore et visualise des données importées à partir d’un Blob de stockage Azure, puis prépare les données pour construire des modèles prédictifs. Python a été utilisé pour la solution de code et afficher les placettes pertinentes. Ces modèles sont build à l’aide de la boîte à outils MLlib d’allumage d’effectuer des tâches de modélisation de régression et classification binaire. 

- La tâche de **classification binaire** est de prédire si une info-bulle est payée pour le voyage. 
- La tâche de **régression** doit prévoir la quantité de l’info-bulle basé sur d’autres fonctionnalités de pointe. 

Les étapes de modélisation également contient du code montrant comment former, évaluer et enregistrer chaque type de modèle. La rubrique traite au sol de même que le sujet de [l’exploration de données et modélisation avec allumage](machine-learning-data-science-spark-data-exploration-modeling.md) . Mais il est plus « avancé » dans la mesure où il utilise également validation croisée conjointement avec hyperparameter à balayage pour former des modèles de classification et de régression précises de façon optimale. 

**Validation croisée (VC)** est une technique qui évalue le degré un modèle formé d’un ensemble connu de données généralise pour prédire les fonctionnalités des groupes de données à laquelle il n'a pas été configuré. L’idée générale de cette technique est qu’un modèle est exercé sur un dataset de données connues sur et puis l’exactitude de ses prévisions est testée par rapport à un groupe de données indépendante. Une implémentation commune utilisée ici consiste à diviser un groupe de données en Ko pliures exercerez ensuite le modèle de façon alternée sur tous sauf un, des plis. 

**Optimisation de Hyperparameter** est le problème du choix d’un jeu de hyperparameters pour un algorithme d’apprentissage, généralement dans le but de l’optimisation d’une mesure des performances de l’algorithme sur un jeu de données indépendant. **Hyperparameters** sont des valeurs qui doivent être spécifiées en dehors de la procédure de formation de modèle. Hypothèses à propos de ces valeurs peuvent influer sur la flexibilité et la précision des modèles. Arborescences de décision ont hyperparameters, par exemple, telles que la profondeur et le nombre de feuilles dans l’arborescence. Machines à vecteurs de support (SVMs) nécessitent la définition d’un terme de pénalité de mauvaise classification. 

Une méthode courante pour effectuer l’optimisation de hyperparameter utilisée ici est une recherche de grille ou un **balayage de paramètre**. Il s’agit d’effectuer une recherche exhaustive sur les valeurs d’un sous-ensemble spécifié de l’espace hyperparameter pour un algorithme d’apprentissage. Validation croisée peut fournir une métrique de performances pour trier les résultats optimaux produites par l’algorithme de recherche de grille. CV avec hyperparameter balayant les problèmes limite permet d’overfitting un modèle de données d’apprentissage afin que le modèle conserve la capacité à appliquer à l’ensemble général de données à partir de laquelle les données de formation a été extrait.

Les modèles que nous utilisons : régression logistique et linéaire, aléatoires, arborescences et des forêts dégradés amplifiées

- [Régression linéaire avec SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) est un modèle de régression linéaire qui utilise une méthode de descente du Gradient stochastique (SGD) et pour l’optimisation et la fonctionnalité de mise à l’échelle afin de prévoir les montants d’info-bulle payé. 
- Régression de [la régression logistique avec LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) ou « logit », est un modèle de régression qui peut être utilisé lorsque la variable dépendante est catégorique de faire de la classification des données. LBFGS est un algorithme d’optimisation quasi Newton qui est une approximation de l’algorithme de Broyden-Fletcher-Goldfarb-Shanno (BFGS) à l’aide d’une quantité limitée de mémoire de l’ordinateur et qui est largement utilisé dans l’apprentissage automatique.
- [Les forêts aléatoire](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) sont des ensembles d’arbres de décision.  Ils associent plusieurs arborescences de décision pour réduire le risque de dépassement de l’ajustement. Forêts aléatoires sont utilisés pour la classification et de régression et peuvent gérer les fonctions par catégorie et peuvent être étendus à la valeur de classification de multiclass. Ils ne nécessitent pas de mise à l’échelle d’une fonctionnalité et sont en mesure de capturer la non linéarité et la fonctionnalité des interactions. Forêts aléatoires sont une de l’ordinateur plus de succès apprentissage des modèles pour la classification et la régression.
- [Dégradé amplifiée arborescences](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) sont des ensembles d’arbres de décision. GBTs formation des arbres de décision itérative pour minimiser une fonction de perte. GBTs sont utilisés pour la classification et de régression peut gérer les fonctions par catégorie, ne nécessitent pas de mise à l’échelle d’une fonctionnalité et sont en mesure de capturer la non linéarité et la fonctionnalité des interactions. Il peuvent également servir dans un paramètre de classification de multiclass.

Balayage modélisation des exemples d’utilisation de CV et Hyperparameter sont affichées pour le problème de la classification binaire. Des exemples plus simples (sans le paramètre balayage) sont présentés dans la rubrique principale pour les tâches de la régression. Mais dans l’appendice, validation à l’aide de net élastique de la régression linéaire VC avec le paramètre balayage à l’aide de régression de la forêt aléatoires est également présentée. **Net élastique** est une méthode de régression REGULARISES pour l’adaptation des modèles de régression linéaire qui linéairement combine les métriques L1 et L2 comme des sanctions, des méthodes et [FAITAGE](https://en.wikipedia.org/wiki/Tikhonov_regularization) de [lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) .   



>[AZURE.NOTE] Bien que le Kit d’outils MLlib d’allumage est conçu pour fonctionner sur grands ensembles de données, un échantillon relativement faible (~ 30 Mo 170K lignes, environ 0,1 % le dataset d’origine NYC) est utilisé ici pour plus de commodité. L’exercice ici s’exécute efficacement (en 10 minutes environ) sur un cluster HDInsight avec 2 nœuds du travailleur. Le même code, avec des modifications mineures, peut être utilisé pour traiter des jeux de données volumineux avec les modifications nécessaires pour la mise en cache des données dans la mémoire et la modification de la taille de cluster.


## <a name="prerequisites"></a>Conditions préalables

Vous avez besoin d’un compte Azure et un cluster nécessité un 1.6 d’étincelle 3.4 HDInsight allumage vous HDInsight pour effectuer cette procédure pas à pas. Consultez la [Vue d’ensemble de données scientifiques à l’aide d’allumage sur Azure HDInsight](machine-learning-data-science-spark-overview.md) pour obtenir des instructions sur la manière de satisfaire ces exigences. Cette rubrique contient également une description des données NYC 2013 Taxi utilisées ici et des instructions sur la façon d’exécuter le code à partir d’un ordinateur portable de Jupyter sur le cluster d’allumage. L’ordinateur portable **pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb** qui contient les exemples de code dans cette rubrique est disponible dans [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark).


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
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**SORTIE**

DateTime.DateTime (2016, 4, 18, 17, 36, 27, 832799)


### <a name="import-libraries"></a>Bibliothèques d’importation

Importer des bibliothèques nécessaires par le code suivant :

    # LOAD PYSPARK LIBRARIES
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


## <a name="data-ingestion-from-public-blob"></a>Réception de données à partir de l’objet blob public : 

La première étape du processus de données science est envoyer les données à analyser à partir de sources où il se trouve dans votre exploration de données et un environnement de modélisation. Cet environnement est une étincelle dans cette procédure pas à pas. Cette section contient le code pour effectuer une série de tâches :

- l’exemple de données à modéliser d’acquisition
- lire dans le dataset d’entrée (stocké sous forme de fichier .tsv)
- mettre en forme et de nettoyer les données
- créer et mettre en cache dans la mémoire des objets (RDDs ou données des trames)
- Enregistrer sous la forme d’une table temporaire dans le contexte de SQL.

Voici le code pour la réception de données.


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
    
    # CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()
    
    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE**

Temps nécessaire pour exécuter au-dessus de la cellule : 276.62 secondes


## <a name="data-exploration--visualization"></a>Visualisation et exploration de données 

Une fois que les données a été mises en étincelle, l’étape suivante du processus de données science est d’obtenir une connaissance plus approfondie des données grâce à l’exploration et de visualisation. Dans cette section, nous examiner les données de taxi à l’aide de requêtes SQL et les variables de la cible et les fonctionnalités potentiels pour l’inspection visuelle de traçage. En particulier, nous tracer la fréquence des décomptes de passagers dans les voyages de taxi, la fréquence des montants de l’info-bulle, et comment les conseils varient selon le type et le montant du paiement.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Tracer un histogramme des fréquences de nombre de passagers dans l’échantillon de trajets de taxi

Ce code et les extraits de code suivants utilisent magique SQL pour interroger l’échantillon et le local magique pour tracer les données.

- **Magique SQL (`%%sql`)** Le noyau HDInsight PySpark prend en charge les requêtes de HiveQL d’inline simple contre le sqlContext. Le (-o nom_variable) argument persiste la sortie de la requête SQL sous la forme d’un DataFrame de Pandas sur le serveur Jupyter. Cela signifie qu’il est disponible en mode local.
- La ** `%%local` magique** est utilisée pour exécuter localement le code sur le serveur Jupyter, ce qui est de la headnode du cluster HDInsight. En général, vous utilisez `%%local` magique en conjonction avec le `%%sql` magique avec le paramètre -o. Le paramètre-o soit persistant de la sortie de la requête SQL local puis %% magic local déclenche l’ensemble suivant d’extrait de code pour exécuter localement sur la sortie des requêtes SQL qui est conservée localement

La sortie est automatiquement affichée une fois que vous exécutez le code.

Cette requête extrait les sorties en fonction du nombre de passagers. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Ce code crée une trame de données locale à partir du résultat de la requête et trace les données. Le `%%local` magique crée une trame de données locale, `sqlResults`, qui peut être utilisée pour le traçage avec matplotlib. 

>[AZURE.NOTE] Ce magic PySpark est utilisé plusieurs fois dans cette procédure pas à pas. Si la quantité de données est volumineuse, vous devez exemple pour créer une trame de données qui peut tenir dans la mémoire locale.


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local
    
    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Voici le code pour tracer les sorties en nombre de passagers

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline
    
    # PLOT PASSENGER NUMBER VS TRIP COUNTS
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**SORTIE**

![Fréquence des voyages en fonction du nombre de passagers](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Vous pouvez sélectionner parmi les différents types de visualisations (Table, secteur, ligne, zone ou barre) en utilisant les boutons de menu de **Type** dans le bloc-notes. Le tracé de la barre s’affiche ici.


### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Tracer un histogramme des montants de l’info-bulle et le montant du pourboire varie en fonction du montants count et le tarif de passager.

Utiliser une requête SQL pour des exemples de données...
    
    # SQL SQUERY
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

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    
    # TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()
    
    # TIP BY PASSENGER COUNT
    ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount ($) by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()
    
    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
    ax.set_title('Tip amount by Fare amount ($)')
    ax.set_xlabel('Fare Amount')
    ax.set_ylabel('Tip Amount')
    plt.axis([-2, 120, -2, 30])
    plt.show()
    

**SORTIE :** 

![Conseil la distribution du montant](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Conseil le montant en fonction du nombre de passagers](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Conseil le montant par le montant de frais](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)


## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Préparation de l’ingénierie, de transformation et de données pour la modélisation des fonctionnalités

Cette section décrit et fournit le code pour les procédures utilisées pour préparer des données à utiliser dans la modélisation de ML. Il montre comment effectuer les tâches suivantes :

- Créer une nouvelle fonction de mise au rebus des heures dans les plages de temps de trafic
- Index et chaud à coder des fonctionnalités par catégorie
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

**SORTIE**

126050


### <a name="index-and-one-hot-encode-categorical-features"></a>L’index et à chaud un coder les fonctionnalités par catégorie

Cette section indique comment indexer ou coder les fonctionnalités par catégorie pour les entrées dans les fonctions de modélisation. La modélisation et prédire les fonctions de MLlib nécessitent des fonctionnalités avec des données d’entrée par catégorie pour être indexé ou codées avant d’être utilisés. 

Selon le modèle, vous devez indexer ou de les coder de différentes manières. Par exemple, les modèles de logistique et de la droite de régression linéaire nécessitent à chaud un codage, où, par exemple, une fonction avec trois catégories peut être développée en trois colonnes de fonctionnalité, avec chaque contenant 0 ou 1 selon la catégorie d’une observation. MLlib fournit la fonction [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) pour faire à chaud un codage. Cet encodeur mappe une colonne d’index de l’étiquette d’une colonne de vecteurs binaires, avec au plus un seul une valeur. Ce codage permet d’algorithmes qui attendent des fonctionnalités à valeurs numériques, tels que de la régression logistique, à appliquer aux fonctionnalités par catégorie.

Voici le code pour indexer et coder les fonctionnalités par catégorie :


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer
    
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


**SORTIE**

Temps nécessaire pour exécuter au-dessus de la cellule : 3.14 secondes


### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Créer des objets point étiquetée pour l’entrée en fonctions de ML

Cette section contient le code qui indique comment les données catégoriques de texte sous la forme d’un type de données appelé point d’index et comment l’encoder afin qu’il peut être utilisé pour former et test de régression logistique de MLlib et d’autres modèles de classification. Objets point étiquetées sont résilient distribué des groupes de données (RDD) au format d’une façon qui est requis par la plupart des algorithmes ML dans MLlib comme données d’entrée. Une [étiquette de point](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) est à un vecteur local, dense ou clairsemée, associé à une étiquette/réponse.

Voici le code pour indexer et coder les fonctionnalités de texte pour la classification binaire.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                             line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                   line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
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
    
    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    from pyspark.sql.functions import rand
    
    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)
    
    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);
    
    # CACHE TRAIN AND TEST DATA
    trainData.cache()
    testData.cache()
    
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

**SORTIE**

Temps nécessaire pour exécuter au-dessus de la cellule : 0,31 secondes


### <a name="feature-scaling"></a>Mise à l’échelle d’une fonction

Fonctionnalité mise à l’échelle, également appelé normalisation des données, ainsi que les fonctionnalités avec des valeurs d’exécution largement sont pas donné excessive Peser dans la fonction de l’objectif. Le code de mise à l’échelle d’une fonctionnalité utilise le [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) à l’échelle les fonctionnalités à l’écart de l’unité. Il est fourni par MLlib pour une utilisation dans la droite de régression linéaire avec stochastique dégradé descente (SGD), un algorithme populaire de formation d’une large gamme d’autre apprentissage des modèles de régressions de REGULARISES ou de machines vectorielles (SVM) de l’ordinateur.   

>[AZURE.TIP] Nous avons trouvé l’algorithme LinearRegressionWithSGD à être sensibles à la fonctionnalité de mise à l’échelle.   

Voici le code à l’échelle des variables pour une utilisation avec l’algorithme SGD linéaire REGULARISES.

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

**SORTIE**

Temps nécessaire pour exécuter au-dessus de la cellule : 11.67 secondes


### <a name="cache-objects-in-memory"></a>Objets cache en mémoire

La durée de la formation et de test des algorithmes de ML peut être réduite par la mise en cache de l’image d’entrée de données objets utilisés pour la classification, régression et, à l’échelle les fonctionnalités.

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

**SORTIE** 

Temps nécessaire pour exécuter au-dessus de la cellule : 0,13 secondes


## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Prévoir ou non une info-bulle est réglée au moyen de modèles de classification binaire

Cette section indique comment utiliser trois modèles pour la tâche de classification binaire permettant de prévoir une info-bulle est payer pour un trajet de taxi ou non. Les modèles présentés sont :

- Régression logistique 
- Forêt aléatoire
- Arborescences d’amplification dégradés

Chaque modèle de création de section de code est divisé en étapes : 

1. Données de **formation de modèle** avec un jeu de paramètres
2. **Évaluation du modèle** sur un ensemble de données test avec des mesures
3. **Enregistrer le modèle** dans l’objet blob de consommation future

Nous montrent comment effectuer la validation croisée (VC) avec le paramètre balayage de deux manières :

1. À l’aide d’un code personnalisé **générique** qui peut être appliqué à n’importe quel algorithme de MLlib et à n’importe quel paramètre définit un algorithme. 
1. À l’aide de la **fonction de pipeline de CrossValidator de pySpark**. Notez que bien que pratique, selon notre expérience, CrossValidator a quelques limitations d’allumage 1.5.0 : 

    - Les modèles de pipeline ne peut pas être enregistré/persistant de consommation future.
    - Ne peut pas être utilisée pour chaque paramètre dans un modèle.
    - Ne peut pas être utilisé pour chaque algorithme de MLlib.


### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Générique entre validation et balayage hyperparameter utilisé avec l’algorithme de régression logistique pour la classification binaire

Le code de cette section montre comment former, évaluer et enregistrer un modèle de régression logistique avec [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) qui le prévoit ou non une info-bulle est payée pour un voyage dans le dataset de voyage et les frais de taxi NYC. Le modèle est formé à l’aide de validation (VC) et de balayage hyperparameter mis en œuvre avec du code personnalisé qui peut être appliqué à un des algorithmes de formation dans MLlib.   

>[AZURE.NOTE] L’exécution de ce code VC personnalisé peut prendre plusieurs minutes.

**Apprentissage du modèle de régression logistique à l’aide de VC et balayage de hyperparameter**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    
    # CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
    from sklearn.grid_search import ParameterGrid
    grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
    paramGrid = list(ParameterGrid(grid))
    numModels = len(paramGrid)
    
    # SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
    nFolds = 3;
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);
    
    # BEGIN CV WITH PARAMETER SWEEP
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create LabeledPoints from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowOneHotBinary)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowOneHotBinary)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            regt = paramGrid[j]['regType']
            regp = paramGrid[j]['regParam']
            iters = paramGrid[j]['iterations']
            tol = paramGrid[j]['tolerance']
            # Train logistic regression model with hypermarameter set
            model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                      regParam=regp, tolerance = tol, intercept=True)
            predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
            # Use ROC-AUC as accuracy metrics
            validMetrics = BinaryClassificationMetrics(predictionAndLabels)
            metric = validMetrics.areaUnderROC
            metricSum[j] += metric
    
    avgAcc = metricSum / nFolds;
    bestParam = paramGrid[np.argmax(avgAcc)];
    
    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()
        
    # TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
    logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                                  iterations=bestParam['iterations'], 
                                                  regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                                  intercept=True)
    
    
    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitBest.weights))
    print("Intercept: " + str(logitBest.intercept))
    
    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE**

Les coefficients : [0.0082065285375,-0.0223675576104,-0.0183812028036, - 3.48124578069e-05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Ordonnée à l’origine :-0.0111216486893

Temps nécessaire pour exécuter au-dessus de la cellule : 14.43 secondes


**Évaluer le modèle de la classification binaire avec des mesures standard**

Le code de cette section montre comment évaluer un modèle de régression logistique par rapport à un test-jeu de données, y compris un tracé de la courbe ROC.


    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT LIBRARIES
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics
    
    # PREDICT ON TEST DATA WITH BEST/FINAL MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))
    
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
    
    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE**

Zone sous PR = 0.985336538462

Zone sous la lune décroissante = 0.983383274312

Statistiques récapitulatives

Précision = 0.984174341679

Rappeler = 0.984174341679

F1 Score = 0.984174341679

Temps nécessaire pour exécuter au-dessus de la cellule : fréquence de 2,67 secondes


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
    
    #PREDICTIONS
    predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)
    
    # PLOT ROC CURVES
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
    

**SORTIE**

![Courbe ROC de régression logistique pour l’approche générique](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)


**Conserver le modèle dans un objet blob de consommation future**

Le code de cette section montre comment enregistrer le modèle de régression logistique destinés à la consommation.

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel
    
    # PERSIST MODEL
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;
    
    logitBest.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";


**SORTIE**

Temps nécessaire pour exécuter au-dessus de la cellule : 34.57 secondes


### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Utiliser la fonction de pipeline CrossValidator de MLlib avec un modèle de régression logistique (régression élastique)

Le code de cette section montre comment former, évaluer et enregistrer un modèle de régression logistique avec [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) qui le prévoit ou non une info-bulle est payée pour un voyage dans le dataset de voyage et les frais de taxi NYC. Le modèle est formé à l’aide de validation (CV) et de balayage hyperparameter mis en œuvre avec la fonction de pipeline MLlib CrossValidator pour VC avec balayage de paramètre.   


>[AZURE.NOTE] L’exécution de ce code MLlib VC peut prendre plusieurs minutes.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    from sklearn.metrics import roc_curve,auc
    
    # DEFINE ALGORITHM / MODEL
    lr = LogisticRegression()
    
    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build()
    
    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=BinaryClassificationEvaluator(),
                        numFolds=3)
    
    # CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])
    
    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)
    

    ## PREDICT AND EVALUATE ON TEST DATA-SET

    # USE TEST DATASET FOR PREDICTION
    testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
    test_predictions = cv_model.transform(testDataFrame)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SORTIE**

Temps nécessaire pour exécuter au-dessus de la cellule : 107.98 secondes


**Tracer la courbe ROC.**

Le *predictionAndLabelsDF* est enregistré sous la forme d’une table, *tmp_results*, dans la cellule précédente. *tmp_results* peut servir à faire des requêtes et des résultats de sortie dans la trame de données sqlResults pour le tracé. Voici le code.


    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

Voici le code pour tracer la courbe ROC.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
    %%local
    from sklearn.metrics import roc_curve,auc
    
    # ROC CURVE
    prob = [x["values"][1] for x in sqlResults["probability"]]
    fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)
    
    #PLOT
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


**SORTIE**

![Courbe ROC de régression logistique à l’aide de CrossValidator de MLlib](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)



### <a name="random-forest-classification"></a>Classification de forêt aléatoire

Le code de cette section montre comment former, évaluer et enregistrer une régression aléatoire qui le prévoit ou non une info-bulle est payée pour un voyage dans le dataset de voyage et les frais de taxi NYC.


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
    ## UN-COMMENT IF YOU WANT TO PRING TREES
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


**SORTIE**

Zone sous la lune décroissante = 0.985336538462

Temps nécessaire pour exécuter au-dessus de la cellule : 26.72 secondes


### <a name="gradient-boosting-trees-classification"></a>Dégradé classification arborescences AMPLIFICATION

Le code de cette section montre comment former, évaluer et enregistrer un modèle arborescences AMPLIFICATION dégradé qui le prévoit ou non une info-bulle est payée pour un voyage dans le voyage de taxi NYC et prix du dataset.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    
    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                    numIterations=10)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())
    
    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
    
    # Area under ROC curve
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

**SORTIE**

Zone sous la lune décroissante = 0.985336538462

Temps nécessaire pour exécuter au-dessus de la cellule : 28.13 secondes


## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Prévoir le montant du pourboire avec les modèles de régression (ne pas à l’aide de VC)

Cette section montre comment les trois modèles d’utilisation de la tâche de régression de prédiction de la quantité de l’info-bulle payer pour un trajet de taxi basé sur d’autres fonctionnalités de pointe. Les modèles présentés sont :

- REGULARISES de régression linéaire
- Forêt aléatoire
- Arborescences d’amplification dégradés

Ces modèles ont été décrites dans l’introduction. Chaque modèle de création de section de code est divisé en étapes : 

1. Données de **formation de modèle** avec un jeu de paramètres
2. **Évaluation du modèle** sur un ensemble de données test avec des mesures
3. **Enregistrer le modèle** dans l’objet blob de consommation future   


>Remarque AZURE : Validation croisée ne sert pas avec les modèles de trois régression dans cette section, dans la mesure où cela a été indiqué en détail pour les modèles de régression logistique. Un exemple illustrant l’utilisation de CV avec Net élastique pour une régression linéaire est fourni dans l’annexe de cette rubrique.


>Remarque AZURE : Dans notre expérience, il peut être problèmes de convergence des modèles de LinearRegressionWithSGD, et les paramètres doivent être modifiés/optimisé avec soin pour obtenir un modèle valide. Mise à l’échelle des variables considérablement aide de convergence. Régression de nette élastique, figure à l’appendice à ce sujet, peut également servir au lieu de LinearRegressionWithSGD.


### <a name="linear-regression-with-sgd"></a>Régression linéaire avec SGD

Le code de cette section montre comment utiliser les fonctionnalités de mise à l’échelle pour entraîner une régression linéaire utilise la descente de gradient stochastique (SGD) pour l’optimisation et comment le score, d’évaluer et d’enregistrer le modèle dans le stockage des Blob Azure (WASB).

>[AZURE.TIP] Dans notre expérience, il peut y avoir des problèmes avec la convergence des modèles de LinearRegressionWithSGD, et les paramètres doivent être modifiés/optimisé avec soin pour obtenir un modèle valide. Mise à l’échelle des variables considérablement aide de convergence.


    # LINEAR REGRESSION WITH SGD 

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
    
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;
    
    linearModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE**

Les coefficients : [0.0141707753435,-0.0252930927087,-0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092,-0.00456498588241,-0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632,-0.00289545676449,-0.00791124681938, 0.54396316518,-0.536293513569, 0.0119076553369,-0.0173039244582, 0.0119632796147, 0.00146764882502]

Intercepter : 0.854507624459

RMSE = 1.23485131376

Sqr-R = 0.597963951127

Temps nécessaire pour exécuter au-dessus de la cellule : 38.62 secondes


### <a name="random-forest-regression"></a>Régression de Random forêt

Le code de cette section montre comment former, évaluer et enregistrer un modèle de forêt aléatoire qui prévoit le montant de pointe pour les données de voyage de taxi NYC.   

>[AZURE.NOTE] Validation croisée avec le paramètre balayage à l’aide d’un code personnalisé est fournie dans l’annexe.


    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    
    
    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    # UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())
    
    # PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)
    
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

**SORTIE**

RMSE = 0.931981967875

Sqr-R = 0.733445485802

Temps nécessaire pour exécuter au-dessus de la cellule : 25.98 secondes


### <a name="gradient-boosting-trees-regression"></a>Dégradé régression arborescences AMPLIFICATION

Le code de cette section montre comment former, évaluer et enregistrer un modèle arborescences AMPLIFICATION dégradé qui prévoit le montant de pointe pour les données de voyage de taxi NYC.

**Formation et évaluation**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils
    
    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)
    
    # EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
    
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
    test_predictions= sqlContext.createDataFrame(predictionAndLabels)
    test_predictions_pddf = test_predictions.toPandas()
    
    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE**

RMSE = 0.928172197114

Sqr-R = 0.732680354389

Temps nécessaire pour exécuter au-dessus de la cellule : 20,9 secondes


**Traçage**
    
*tmp_results* est enregistré sous la forme d’une table de la ruche dans la cellule précédente. Résultats de la table sont consignés dans la trame de données *sqlResults* pour le tracé. Voici le code

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Voici le code pour tracer les données à l’aide du serveur Jupyter.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import numpy as np
    
    # PLOT
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 15])
    plt.show(ax)

![Réel-vs-prévue-Conseil-montants](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)


## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>L’appendice : Tâches de régression supplémentaires à l’aide de validation croisée avec les balayages de paramètre

Cette annexe contient le code illustrant comment faire CV à l’aide de net élastique pour une régression linéaire et comment VC avec balayage de paramètre à l’aide de code personnalisé pour la régression de forêt aléatoire.


### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Entre la validation à l’aide de net d’élastique pour une régression linéaire

Le code de cette section montre comment traverser de validation à l’aide de net élastique pour une régression linéaire et à évaluer le modèle par rapport à des données de test.

    ###  CV USING ELASTIC NET FOR LINEAR REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.regression import LinearRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import RegressionEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    
    # DEFINE ALGORITHM/MODEL
    lr = LinearRegression()
    
    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build() 
    
    # DEFINE PIPELINE 
    # SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
    pipeline = Pipeline(stages=[lr])
    
    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=RegressionEvaluator(),
                        numFolds=3)
    
    # CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])
    
    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)
    

    # EVALUATE MODEL ON TEST SET
    testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])
    
    # MAKE PREDICTIONS ON TEST DOCUMENTS
    # cvModel uses the best model found (lrModel).
    predictionAndLabels = cv_model.transform(testDataFrame)
    
    # CONVERT TO DF AND SAVE REGISER DF AS TABLE
    predictionAndLabels.registerTempTable("tmp_results");
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE**

Temps nécessaire pour exécuter au-dessus de la cellule : 161.21 secondes

**Évaluez avec mesure de R-SQR**

*tmp_results* est enregistré sous la forme d’une table de la ruche dans la cellule précédente. Résultats de la table sont consignés dans la trame de données *sqlResults* pour le tracé. Voici le code

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Voici le code pour calculer R-sqr.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats
    
    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**SORTIE**

Sqr-R = 0.619184907088


### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Croisement de validation avec balayage de paramètre à l’aide de code personnalisé pour la régression de forêt aléatoire

Le code de cette section montre comment ne traversent pas validation avec balayage de paramètre à l’aide de code personnalisé pour la régression de forêt aléatoire et l’évaluation du modèle avec les données de test.


    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    # GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    from sklearn.grid_search import ParameterGrid
    
    ## CREATE PARAMETER GRID
    grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
    paramGrid = list(ParameterGrid(grid))
    
    ## SPECIFY LEVELS OF CATEGORICAL VARIBLES
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    # SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
    nFolds = 3;
    numModels = len(paramGrid)
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);
    
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create labeled points from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowIndexingRegression)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowIndexingRegression)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            maxD = paramGrid[j]['maxDepth']
            numT = paramGrid[j]['numTrees']
            # Train logistic regression model with hypermarameter set
            rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=numT, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=maxD, maxBins=32)
            predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
            predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
            # Use ROC-AUC as accuracy metrics
            validMetrics = RegressionMetrics(predictionAndLabels)
            metric = validMetrics.rootMeanSquaredError
            metricSum[j] += metric
    
    avgAcc = metricSum/nFolds;
    bestParam = paramGrid[np.argmin(avgAcc)];
    
    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()
            
    ## TRAIN FINAL MODL WIHT BEST PARAMETERS
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

    # EVALUATE MODEL ON TEST DATA
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
    
    #PRINT TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE**

RMSE = 0.906972198262

Sqr-R = 0.740751197012

Temps nécessaire pour exécuter au-dessus de la cellule : 69.17 secondes


### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Nettoyer les objets de la mémoire et les emplacements du modèle d’impression

Utilisez `unpersist()` pour supprimer les objets mis en cache dans la mémoire.

    # UNPERSIST OBJECTS CACHED IN MEMORY

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    trainData.unpersist()
    trainData.unpersist()
    
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


**SORTIE**

PythonRDD [122] à RDD au PythonRDD.scala : 43


**Chemin d’accès des fichiers de modèle à utiliser dans le bloc-notes de consommation impression.** Pour consommer et le score d’un jeu de données indépendant, vous devez copier et coller ces noms de fichier dans le bloc-notes « consommation ».


    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**SORTIE**

logisticRegFileLoc = modelDir + « LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528 »

linearRegFileLoc = modelDir + « LinearRegressionWithSGD_2016-05-0316_51_28.433670 »

randomForestClassificationFileLoc = modelDir + « RandomForestClassification_2016-05-0316_50_17.454440 »

randomForestRegFileLoc = modelDir + « RandomForestRegression_2016-05-0316_51_57.331730 »

BoostedTreeClassificationFileLoc = modelDir + « GradientBoostingTreeClassification_2016-05-0316_50_40.138809 »

BoostedTreeRegressionFileLoc = modelDir + « GradientBoostingTreeRegression_2016-05-0316_52_18.827237 »

## <a name="whats-next"></a>Quel est l’avenir ?

Maintenant que vous avez créé des modèles de classification et de régression avec la MlLib d’allumage, vous êtes prêt à apprendre de score et l’évaluation de ces modèles.

**Consommation de modèle :** Pour savoir comment évaluer et évaluer les modèles de classification et de régression créés dans cette rubrique, consultez [Score et l’évaluation des modèles de formation intégré d’allumage de machine](machine-learning-data-science-spark-model-consumption.md).
