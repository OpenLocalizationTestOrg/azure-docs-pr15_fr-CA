<properties
    pageTitle="Modèles d’apprentissage machine d’allumage intégré de score | Microsoft Azure"
    description="Comment les modèles de formation de score qui ont été stockés dans le stockage des Blob Azure (WASB)."
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

# <a name="score-spark-built-machine-learning-models"></a>Score de modèles d’apprentissage machine d’allumage intégré 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Cette rubrique décrit comment charger les modèles ordinateur (ML) de formation qui ont été générés à l’aide de MLlib d’allumage et stockés dans le stockage de Blob Azure (WASB) et comment leur score avec des groupes de données qui ont également été stockés dans WASB. Il montre comment avant de traiter les données d’entrée, de transformer les fonctionnalités en utilisant les fonctions d’indexation et de codage dans la boîte à outils MLlib et comment créer un objet de données appelé point qui peut être utilisé comme entrée pour l’attribution de scores avec les modèles ML. Les modèles utilisés pour l’attribution de scores incluent la régression linéaire, régression logistique, modèles de forêt aléatoire et modèles d’arborescence de renforcement dégradé.


## <a name="prerequisites"></a>Conditions préalables

1. Vous avez besoin d’un compte Azure et un cluster nécessité un 1.6 d’étincelle 3.4 HDInsight allumage vous HDInsight pour effectuer cette procédure pas à pas. Consultez la [Vue d’ensemble de données scientifiques à l’aide d’allumage sur Azure HDInsight](machine-learning-data-science-spark-overview.md) pour obtenir des instructions sur la manière de satisfaire ces exigences. Cette rubrique contient également une description des données NYC 2013 Taxi utilisées ici et des instructions sur la façon d’exécuter le code à partir d’un ordinateur portable de Jupyter sur le cluster d’allumage. L’ordinateur portable **pySpark-machine-learning-data-science-spark-model-consumption.ipynb** qui contient les exemples de code dans cette rubrique est disponible dans [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark).

2. Vous devez également créer l’apprentissage modèles à noter ici par le travail par le biais de la rubrique de [l’exploration de données et modélisation avec allumage](machine-learning-data-science-spark-data-exploration-modeling.md) de l’ordinateur.   


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]
 

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Le programme d’installation : des emplacements de stockage, les bibliothèques et le contexte étincelle prédéfini

Allumage est en mesure de lire et d’écrire à un Blob de stockage Azure (WASB). Afin que vos données existantes stockées il peuvent être traitées à l’aide d’allumage et les résultats stockés à nouveau dans WASB.

Pour enregistrer les modèles ou les fichiers dans WASB, le chemin d’accès doit être correctement spécifié. Le conteneur par défaut lié au cluster étincelle peut être référencé à l’aide d’un chemin commençant par : *« wasb / / »*. L’exemple de code suivant spécifie l’emplacement des données à lire et le chemin d’accès du répertoire de stockage de modèle dans lequel la sortie de modèle est enregistrée. 


### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Définir des chemins d’accès de répertoire pour les emplacements de stockage en WASB

Les modèles sont enregistrés dans : « wasb : / / / / UtilisateurDistant/NYCTaxi/modèles utilisateur ». Si ce chemin d’accès n’est pas défini correctement, les modèles ne sont pas chargés pour l’attribution de scores.

Les résultats évaluées ont été enregistrés dans : « wasb : / / / utilisateur/UtilisateurDistant/NYCTaxi/ScoredResults ». Si le chemin d’accès au dossier est incorrecte, les résultats ne sont pas enregistrés dans ce dossier.   


>[AZURE.NOTE] Les emplacements de chemin d’accès du fichier peuvent être copiés et collés dans les espaces réservés dans ce code à partir de la sortie de la dernière cellule du bloc-notes **machine-learning-data-science-spark-data-exploration-modeling.ipynb** .   


Voici le code pour définir des chemins d’accès : 

    # LOCATION OF DATA TO BE SCORED (TEST DATA)
    taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";
    
    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 
    
    # SET SCORDED RESULT DIRECTORY PATH
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 
    
    # FILE LOCATIONS FOR THE MODELS TO BE SCORED
    logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
    linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
    randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
    randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
    BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
    BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

    # RECORD START TIME
    import datetime
    datetime.datetime.now()

**SORTIE :**

DateTime.DateTime (2016, 4, 25, 23, 56, 19, 229403)


### <a name="import-libraries"></a>Bibliothèques d’importation

Définir le contexte d’allumage et d’importer les bibliothèques nécessaires par le code suivant

    #IMPORT LIBRARIES
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

Les noyaux de PySpark qui sont fournis avec les ordinateurs portables de Jupyter ont un contexte prédéfini. Par conséquent, vous n’avez pas besoin de définir l’allumage ou contextes de ruche explicitement avant de commencer à travailler avec l’application que vous développez. Ils sont disponibles pour vous par défaut. Les contextes sont :

- SC - pour l’allumage 
- sqlContext - pour ruche

Le noyau PySpark fournit certaines prédéfinis « magics », qui sont des commandes spéciales que vous pouvez appeler avec %%. Il existe deux ces commandes sont utilisées dans ces exemples de code.

- **%% local** Spécifier que le code dans les lignes suivantes est exécuté localement. Code doit être le code valide de Python.
- **%% -o sql<variable name>** 
- Exécute une requête de ruche contre le sqlContext. Si le paramètre -o est passé, le résultat de la requête est rendue persistante dans le %% contexte de Python local sous la forme d’un dataframe de Pandas.
 

Pour plus d’informations sur les noyaux pour prédéfinis et les ordinateurs portables de Jupyter « magics » qui leur fournissent, consultez [noyaux disponibles pour les ordinateurs portables de Jupyter avec les clusters Linux d’allumage HDInsight sur HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Acquisition de données et créer une trame de données nettoyées

Cette section contient le code d’une série de tâches requises pour ajouter des données à un score. Format à lire dans un échantillon de 0,1 % de joints du fichier voyage et tarif taxi (stocké sous forme de fichier .tsv), les données et crée une trame de données minimal.

Les fichiers de voyage et les frais de taxi y ont été joints en fonction de la procédure fournie dans la : [le processus Science données d’équipe en action : l’utilisation de clusters de HDInsight Hadoop](machine-learning-data-science-process-hive-walkthrough.md) rubrique.

    # INGEST DATA AND CREATE A CLEANED DATA FRAME

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # IMPORT FILE FROM PUBLIC BLOB
    taxi_test_file = sc.textFile(taxi_test_file_loc)
    
    # GET SCHEMA OF THE FILE FROM HEADER
    taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)
    
    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
        
    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_test_file.first()
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
    
    # CREATE DATA FRAME
    taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)
    
    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )
    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_cleaned.cache()
    taxi_df_test_cleaned.count()
    
    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_test_cleaned.registerTempTable("taxi_test")
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE :**

Temps nécessaire pour exécuter au-dessus de la cellule : 46.37 secondes


## <a name="prepare-data-for-scoring-in-spark"></a>Préparer les données pour la notation de l’allumage 

Cette section indique comment indexer, de coder et de mettre à l’échelle les fonctionnalités par catégorie pour les préparer pour une utilisation dans des algorithmes de formation de MLlib contrôlé pour la classification et de régression.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Fonction de transformation : index et coder les fonctionnalités par catégorie pour les entrées dans des modèles de score 

Cette section indique comment les données catégoriques à l’aide de l’index une `StringIndexer` et coder les fonctionnalités avec `OneHotEncoder` d’entrée dans les modèles.

Le [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) encode une colonne de type chaîne d’étiquettes à une colonne d’index de l’étiquette. Les index sont triés par les fréquences de l’étiquette. 

[OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) mappe une colonne d’index de l’étiquette pour une colonne de vecteurs binaires, avec au plus un seul une valeur. Ce codage permet d’algorithmes qui attendent les fonctions à valeurs continues, comme une régression logistique, à appliquer aux fonctionnalités par catégorie.
    
    #INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer
    
    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_test 
    """
    taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)
    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_with_newFeatures.cache()
    taxi_df_test_with_newFeatures.count()
    
    # INDEX AND ONE-HOT ENCODING
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_test_with_newFeatures)
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
    
    # INDEX AND ENCODE TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE :**

Temps nécessaire pour exécuter au-dessus de la cellule : 5.37 secondes


### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Créer des objets de la RDD avec baies de fonctionnalité pour une entrée dans les modèles

Cette section contient le code qui montre comment indexer les données catégoriques de texte en tant qu’objet RDD et à chaud un Encoder afin qu’il puisse être utilisé pour former et test de régression logistique des MLlib et des modèles basés sur l’arborescence. Les données indexées sont stockées dans des objets [Dataset distribuée robuste (RDD)](http://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) . Il s’agit de l’abstraction de base de l’explosion. Un objet RDD représente une collection immuable, partitionnée d’éléments qui peuvent être traitées en parallèle avec l’explosion.

Il contient également le code qui indique comment mettre à l’échelle des données avec le `StandardScalar` fournie par MLlib pour une utilisation dans la droite de régression linéaire avec stochastique dégradé descente (SGD), un algorithme populaire pour la formation d’un large éventail de modèles de formation des machines. Le [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) est utilisé à l’échelle les fonctionnalités à l’écart de l’unité. Fonctionnalité mise à l’échelle, également appelé normalisation des données, ainsi que les fonctionnalités avec des valeurs d’exécution largement sont pas donné excessive Peser dans la fonction de l’objectif. 


    # CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    from numpy import array
    
    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features
    
    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
    oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)
    
    # FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
    indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
    oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)
    
    # SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
    scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
    oneHotTESTregScaled = scaler.transform(oneHotTESTreg)
    
    # CACHE RDDS IN MEMORY
    indexedTESTbinary.cache();
    oneHotTESTbinary.cache();
    indexedTESTreg.cache();
    oneHotTESTreg.cache();
    oneHotTESTregScaled.cache();
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SORTIE :**

Temps nécessaire pour exécuter au-dessus de la cellule : 11.72 secondes


## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Score avec le modèle de régression logistique et enregistrer la sortie de blob

Le code de cette section montre comment charger un modèle de régression logistique qui a été enregistré dans le stockage blob Azure et l’utiliser pour prévoir ou non une info-bulle est payée sur un trajet de taxi, il score avec des mesures de classification standard, puis enregistrer et tracer les résultats pour le stockage des objets blob. Les résultats évaluées sont stockés dans les objets de la RDD. 


    # SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # IMPORT LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel
    
    ## LOAD SAVED MODEL
    savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
    predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))
    
    ## SAVE SCORED RESULTS (RDD) TO BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
    dirfilename = scoredResultDir + logisticregressionfilename;
    predictions.saveAsTextFile(dirfilename)
    
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SORTIE :**

Temps nécessaire pour exécuter au-dessus de la cellule : 19.22 secondes


## <a name="score-a-linear-regression-model"></a>Score d’un modèle de régression linéaire

Nous avons utilisé [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) pour former un modèle de régression linéaire à l’aide de stochastique dégradé descente (SGD) pour l’optimisation de prédire le montant du pourboire. 

Le code de cette section montre comment charger un modèle de régression linéaire depuis le stockage blob Azure, score à l’aide de variables à l’échelle et puis enregistrer les résultats dans le blob.

    #SCORE LINEAR REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    #LOAD LIBRARIES
    from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel
    
    # LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
    savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
    predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = scoredResultDir + linearregressionfilename;
    predictions.saveAsTextFile(dirfilename)
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SORTIE :**

Temps nécessaire pour exécuter au-dessus de la cellule : 16.63 secondes


## <a name="score-classification-and-regression-random-forest-models"></a>Score de classification et régression aléatoire modèles de forêt

Le code de cette section montre comment charger la classification enregistrée et modèles de forêt aléatoire de régression enregistrées dans le stockage blob Azure score leurs performances avec classifieur standard et des mesures de régression et puis enregistrez les résultats au stockage d’objets blob.

[Les forêts aléatoire](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) sont des ensembles d’arbres de décision.  Ils associent plusieurs arborescences de décision pour réduire le risque de dépassement de l’ajustement. Forêts aléatoires peuvent gérer les fonctions par catégorie, étendre le paramètre de classification multiclass, ne nécessitent pas de mise à l’échelle d’une fonctionnalité et sont en mesure de capturer la non linéarité et la fonctionnalité des interactions. Forêts aléatoires sont une de l’ordinateur plus de succès apprentissage des modèles pour la classification et la régression.

[Spark.mllib](http://spark.apache.org/mllib/) prend en charge des forêts aléatoires de classification binaire et de multiclass et de régression, à l’aide de fonctionnalités continues et par catégorie. 

    # SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES 
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    
    
    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfclassificationfilename;
    predictions.saveAsTextFile(dirfilename)
    

    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
    predictions = savedModel.predict(indexedTESTreg)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SORTIE :**

Temps nécessaire pour exécuter au-dessus de la cellule : 31.07 secondes


## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Score de classification et régression dégradé augmentation de modèles d’arborescence

Le code de cette section montre comment charger la classification et régression dégradé augmentation de modèles d’arborescence depuis le stockage blob Azure, évaluer leurs performances avec classifieur standard et des mesures de régression et puis enregistrez les résultats au stockage d’objets blob. 

**Spark.mllib** prend en charge les GBTs de classification binaire et de régression, à l’aide de fonctionnalités continues et par catégorie. 

[Arborescences d’amplification dégradés](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) sont des ensembles d’arbres de décision. GBTs formation des arbres de décision itérative pour minimiser une fonction de perte. GBTs peut gérer les fonctions par catégorie, ne nécessitent pas de mise à l’échelle d’une fonctionnalité et sont en mesure de capturer la non linéarité et la fonctionnalité des interactions. Il peuvent également servir dans un paramètre de classification de multiclass.


    # SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    
    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    #LOAD AND SCORE THE MODEL
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btclassificationfilename;
    predictions.saveAsTextFile(dirfilename)
    

    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    # LOAD AND SCORE MODEL 
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
    predictions = savedModel.predict(indexedTESTreg)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
    
**SORTIE :**

Temps nécessaire pour exécuter au-dessus de la cellule : 14.6 secondes


## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Nettoyer les objets de la mémoire et d’imprimer les emplacements de fichier évaluées

    # UNPERSIST OBJECTS CACHED IN MEMORY
    taxi_df_test_cleaned.unpersist()
    indexedTESTbinary.unpersist();
    oneHotTESTbinary.unpersist();
    indexedTESTreg.unpersist();
    oneHotTESTreg.unpersist();
    oneHotTESTregScaled.unpersist();


    # PRINT OUT PATH TO SCORED OUTPUT FILES
    print "logisticRegFileLoc: " + logisticregressionfilename;
    print "linearRegFileLoc: " + linearregressionfilename;
    print "randomForestClassificationFileLoc: " + rfclassificationfilename;
    print "randomForestRegFileLoc: " + rfregressionfilename;
    print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
    print "BoostedTreeRegressionFileLoc: " + btregressionfilename;


**SORTIE :**

logisticRegFileLoc : LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc : LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc : RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc : RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc : GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc : GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt



## <a name="consume-spark-models-through-a-web-interface"></a>Utiliser des modèles d’allumage via une interface web

Allumage fournit un mécanisme pour envoyer à distance des traitements par lots ou requêtes interactives via une interface reste avec un composant appelé Livy. Livy est activé par défaut sur votre cluster HDInsight Spark. Pour plus d’informations sur Livy voir : [allumage de soumettre les tâches à distance à l’aide de Livy](../hdinsight/hdinsight-apache-spark-livy-rest-interface.md). 

Vous pouvez utiliser Livy à distance soumettre une tâche de traitement par lots des scores un fichier qui est stocké dans un blob Azure et puis écrit les résultats dans un autre blob. Pour ce faire, vous téléchargez le script de Python à partir de  
[Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) pour le blob du cluster allumage. Vous pouvez utiliser un outil tel que **Microsoft Azure Storage Explorer** ou **AzCopy** pour copier le script dans le blob de cluster. Dans notre cas, nous avons téléchargé le script à ***wasb:///example/python/ConsumeGBNYCReg.py***.   


>[AZURE.NOTE] Les touches d’accès rapide que vous avez besoin sont accessibles sur le portail pour le compte de stockage associé au cluster d’allumage. 


Une fois chargé à cet emplacement, ce script s’exécute au sein du cluster d’étincelle dans un contexte distribué. Il charge le modèle et exécuter des prévisions sur les fichiers d’entrée basés sur le modèle.  

Vous pouvez appeler ce script à distance en effectuant une demande HTTPS/reste simple sur Livy.  Voici une commande curl pour construire la requête HTTP pour appeler le script Python à distance. Remplacez les valeurs appropriées pour votre cluster étincelle CLUSTERLOGIN, CLUSTERPASSWORD, NOM_CLUSTER.


    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Vous pouvez utiliser n’importe quel langage sur le système distant pour invoquer le travail de l’allumage par Livy par un simple appel HTTPS avec authentification de base.   


>[AZURE.NOTE] Il est pratique d’utiliser la bibliothèque de Python demandes lors de cet appel HTTP, mais il n’est pas installé par défaut dans les fonctions d’Azure. Afin que les anciennes bibliothèques HTTP sont utilisés à la place.   


Voici le code Python pour l’appel HTTP :

    #MAKE AN HTTPS CALL ON LIVY. 

    import os

    # OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILBLE BY DEFAULT
    import httplib, urllib, base64
    
    # REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
    host = '<spark cluster name>.azurehdinsight.net:443'
    username='<username>'
    password='<password>'
    
    #AUTHORIZATION
    conn = httplib.HTTPSConnection(host)
    auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
    headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}
    
    # SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
    # IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
    r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
    response = conn.getresponse().read()
    print(response)
    conn.close()


Vous pouvez également ajouter ce code Python aux [Fonctions d’Azure](https://azure.microsoft.com/documentation/services/functions/) pour déclencher une soumission de projet étincelle que scores un blob en fonction de différents événements comme une horloge, la création ou la mise à jour d’un objet blob. 

Si vous préférez une expérience client libre de code, utilisez [Les applications Azure logique](https://azure.microsoft.com/documentation/services/app-service/logic/) pour appeler le lot d’allumage de notation en définissant une action HTTP dans le **Concepteur d’applications logique** et en définissant ses paramètres. 

- À partir du portail Azure, créer une nouvelle application logique en sélectionnant **+ Nouveau** -> **Web + Mobile** -> **Logique d’application**. 
- Pour afficher le **Concepteur d’applications logique**, entrez le nom de la logique d’application et le Plan de Service d’application.
- Sélectionnez une action de HTTP et entrez les paramètres indiqués dans la figure suivante :

![](./media/machine-learning-data-science-spark-model-consumption/spark-logica-app-client.png)


## <a name="whats-next"></a>Quel est l’avenir ? 

**Balayage de hyperparameter et validation croisée**: voir [avancées d’exploration de données et modélisation avec allumage](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) sur comment les modèles peuvent être formés à l’aide de balayage de validation croisée et paramètre hyper.
