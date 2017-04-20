<properties
    pageTitle="Science de données à l’aide de Scala et allumage sur Azure | Microsoft Azure"
    description="L’utilisation de Scala pour les tâches d’apprentissage machine contrôlés avec les packages étincelle les MLlib ML d’allumage évolutives sur un cluster Azure HDInsight allumage."  
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
    ms.date="08/01/2016"
    ms.author="bradsev;deguhath"/>


# <a name="data-science-using-scala-and-spark-on-azure"></a>Science de données à l’aide de Scala et allumage sur Azure

Cet article vous montre comment utiliser Scala pour les tâches d’apprentissage machine contrôlés avec les packages étincelle les MLlib ML d’allumage évolutives sur un cluster Azure HDInsight allumage. Il vous guide dans les tâches qui constituent le [processus de données Science](http://aka.ms/datascienceprocess): réception de données et exploration, visualisation, ingénierie de fonctionnalité, la modélisation et la consommation du modèle. Les modèles dans l’article : régression logistique et linéaire, aléatoires, arborescences et des forêts amplifiée de dégradé (GBTs), en plus de deux tâches d’apprentissage machine de mise sous contrôle commun

- Problème de régression : prévision du Conseil montant de ($) pour un voyage de taxi
- Classement binaire : prédiction de l’info-bulle ou aucune info-bulle (1/0) pour un voyage de taxi

Le processus de modélisation nécessite de formation et évaluation sur un ensemble de données de test et les métriques de précision appropriée. Dans cet article, vous pouvez apprendre comment stocker ces modèles dans le stockage Azure Blob et score et d’évaluer leurs performances prédictive. Cet article traite également des rubriques les plus avancées de l’optimisation des modèles à l’aide de balayage de validation croisée et paramètre hyper. Les données utilisées sont un échantillon de la 2013 NYC taxi voyage et tarif de données disponible sur GitHub.

[Scala](http://www.scala-lang.org/), un langage basé sur la machine virtuelle Java, intègre les concepts de langage fonctionnel et orienté objet. Il est un langage évolutif est adapté au traitement distribué dans le nuage et s’exécute sur Azure érosion clusters.

[Allumage](http://spark.apache.org/) est une infrastructure de traitement parallèle d’open source qui prend en charge le traitement en mémoire pour augmenter les performances des applications analytique de données volumineuses. Le moteur de traitement et est conçu pour la vitesse, facilité d’utilisation et analytique sophistiquées. Calcul distribué de mémoire fonctionnalités d’allumage rendre un bon choix pour les algorithmes itératifs dans les calculs de formation et graphique de machine. Le package [spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) fournit un ensemble uniforme de l’API de haut niveau basé sur les données de cadres qui peuvent vous aider à créer et analyser les pratique machine pipelines d’apprentissage. [MLlib](http://spark.apache.org/mllib/) est la bibliothèque d’apprentissage automatique évolutive d’allumage, qui apporte des fonctions de modélisation à cet environnement distribué.

[HDInsight allumage](../hdinsight/hdinsight-apache-spark-overview.md) est l’offre Azure hébergé de l’allumage de l’open source. Il inclut la prise en charge pour les ordinateurs portables de Jupyter Scala sur le cluster d’allumage et vous pouvez exécuter des requêtes d’interactive SQL d’allumage pour la transformation, de filtrer et de visualiser des données stockées dans le stockage des objets Blob Azure. Scala extraits de code de cet article fournissent les solutions et afficher les placettes pertinentes pour visualiser les données s’exécutent dans les portables Jupyter installés sur des clusters de l’allumage. Les étapes de modélisation dans ces rubriques ont un code qui montre comment former, évaluer, enregistrer et utiliser chaque type de modèle.

Les étapes de l’installation et le code dans cet article sont pour Azure HDInsight 3.4 étincelle 1.6. Toutefois, le code dans cet article et dans le [Carnet de Jupyter de Scala](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) sont générique et devrait fonctionner sur n’importe quel cluster d’allumage. Les étapes de configuration et la gestion de cluster peuvent être légèrement différents de celui présenté dans cet article si vous n’utilisez pas HDInsight Spark.

> [AZURE.NOTE] Pour un sujet qui vous montre comment utiliser les Python, au lieu de Scala pour effectuer les tâches d’un processus de Science de données de bout en bout, consultez [Science de données à l’aide d’allumage sur Azure HDInsight](machine-learning-data-science-spark-overview.md).


## <a name="prerequisites"></a>Conditions préalables

-   Vous devez avoir un abonnement Azure. Si vous ne figure pas un, [Obtenez un essai gratuit Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

-   Vous avez besoin d’un cluster Azure HDInsight 3.4 étincelle 1.6 pour effectuer les procédures suivantes. Pour créer un cluster, consultez les instructions de [mise en route : créer des étincelle Apache sur Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Définir le type de cluster et la version dans le menu de **Sélection du Type de Cluster** .

![Configuration de type cluster HDInsight](./media/machine-learning-data-science-process-scala-walkthrough/spark-cluster-on-portal.png)


>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Pour une description des données de voyage NYC taxi et des instructions sur la façon d’exécuter le code à partir d’un ordinateur portable de Jupyter sur le cluster d’allumage, consultez les sections correspondantes dans la [Vue d’ensemble de données scientifiques à l’aide d’allumage sur Azure HDInsight](machine-learning-data-science-spark-overview.md).  


## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Exécuter le code de Scala à partir d’un ordinateur portable de Jupyter sur le cluster d’allumage

Vous pouvez lancer un bloc-notes de Jupyter à partir du portail Azure. Trouver le cluster étincelle sur votre tableau de bord, puis cliquez sur pour accéder à la page de gestion de votre cluster. Ensuite, cliquez sur **Tableaux de bord de Cluster**, puis cliquez sur **Jupyter bloc-notes** pour ouvrir le bloc-notes associé au cluster d’allumage.

![Tableau de bord de cluster et les ordinateurs portables de Jupyter](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-on-portal.png)

Vous pouvez également accéder à Jupyter des portables à https://&lt;clustername&gt;.azurehdinsight.net/jupyter. Remplacez *Nom_Cluster* par le nom de votre cluster. Vous devez le mot de passe pour votre compte d’administrateur accéder à la Jupyter de portables.

![Accédez aux ordinateurs portables de Jupyter en utilisant le nom de cluster](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-notebook.png)

Sélectionnez **Scala** pour afficher un répertoire contenant des exemples de préemballages portables qui utilisent l’API PySpark. La modélisation de l’Exploration et le score à l’aide du bloc-notes de Scala.ipynb qui contient les exemples de code pour cet ensemble de rubriques d’allumage est disponible sur [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).


Vous pouvez télécharger le bloc-notes directement à partir de GitHub sur le serveur Jupyter portable sur votre cluster de l’explosion. Sur votre page d’accueil Jupyter, cliquez sur le bouton **Télécharger** . Dans l’Explorateur de fichiers, collez l’URL (contenu brut) de GitHub de l’ordinateur portable de Scala, puis cliquez sur **Ouvrir**. Le bloc-notes de Scala est disponible à l’adresse suivante :

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Le programme d’installation : Contextes étincelle de paramètre prédéfini et la ruche magics d’allumage et bibliothèques d’allumage

### <a name="preset-spark-and-hive-contexts"></a>Contextes d’étincelle et ruche prédéfinis

    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


Les noyaux d’allumage qui sont fournis avec les ordinateurs portables Jupyter ont prédéfini contextes. Vous n’avez pas besoin de définir explicitement l’allumage ou contextes de ruche avant de commencer à travailler avec l’application que vous développez. Les contextes prédéfinis sont :

- `sc`pour SparkContext
- `sqlContext`pour HiveContext


### <a name="spark-magics"></a>Magics d’allumage

Le noyau d’allumage fournit certaines prédéfinis « magics », qui sont des commandes spéciales que vous pouvez appeler avec `%%`. Deux de ces commandes sont utilisées dans les exemples de code suivants.

- `%%local`Spécifie que le code dans les lignes suivantes sera exécuté localement. Le code doit être le code valide de Scala.
- `%%sql -o <variable name>`exécute une requête de ruche sur `sqlContext`. Si le `-o` paramètre est passé, le résultat de la requête est rendue persistante dans le `%%local` contexte de Scala sous la forme d’une trame de données allumage.

Pour plus d’informations sur les noyaux pour ordinateurs portables de Jupyter et leurs prédéfini « magics » que vous appelez avec `%%` (par exemple, `%%local`), consultez les [noyaux disponibles pour les ordinateurs portables de Jupyter avec les clusters Linux d’allumage HDInsight sur HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


### <a name="import-libraries"></a>Bibliothèques d’importation

Importer l’allumage, MLlib et autres bibliothèques que vous aurez besoin en utilisant le code suivant.

    # IMPORT SPARK AND JAVA LIBRARIES
    import org.apache.spark.sql.SQLContext
    import org.apache.spark.sql.functions._
    import java.text.SimpleDateFormat
    import java.util.Calendar
    import sqlContext.implicits._
    import org.apache.spark.sql.Row

    # IMPORT SPARK SQL FUNCTIONS
    import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
    import org.apache.spark.sql.functions.rand

    # IMPORT SPARK ML FUNCTIONS
    import org.apache.spark.ml.Pipeline
    import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
    import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
    import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
    import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
    import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

    # IMPORT SPARK MLLIB FUNCTIONS
    import org.apache.spark.mllib.linalg.{Vector, Vectors}
    import org.apache.spark.mllib.util.MLUtils
    import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
    import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
    import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
    import org.apache.spark.mllib.tree.configuration.BoostingStrategy
    import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
    import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

    # SPECIFY SQLCONTEXT
    val sqlContext = new SQLContext(sc)


## <a name="data-ingestion"></a>Réception de données

La première étape dans le processus de données scientifiques est de réceptionner les données que vous souhaitez analyser. Vous mettez les données à partir de sources externes ou des systèmes où il se trouve dans votre environnement de modélisation et d’exploration de données. Dans cet article, les données que vous d’acquisition sont un exemple de 0,1 % de joints du fichier voyage et tarif taxi (stocké sous forme de fichier .tsv). L’environnement de modélisation et d’exploration de données est à allumage commandé. Cette section contient le code pour terminer la série suivante de tâches :

1. Définir les chemins d’accès de répertoire pour le stockage de données et le modèle.
2. Lire dans le jeu de données d’entrée (stocké sous forme de fichier .tsv).
3. Définir un schéma pour les données et nettoyer les données.
4. Créer un bloc de données nettoyées et mettre en cache dans la mémoire.
5. Enregistrer les données sous la forme d’une table temporaire dans SQLContext.
6. Requête sur la table et importer les résultats dans une trame de données.


### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Définir des chemins d’accès de répertoire pour les emplacements de stockage dans le stockage Azure Blob

Étincelle peut lire et écrire dans le stockage des objets Blob Azure. Vous pouvez utiliser d’allumage pour traiter vos données existantes et puis stocker les résultats à nouveau dans le stockage Blob.

Pour enregistrer les modèles ou les fichiers dans le stockage Blob, vous devez correctement spécifier le chemin d’accès. Référencer le conteneur par défaut lié au cluster allumage à l’aide d’un chemin d’accès commençant par `wasb:///`. Référencer d’autres emplacements à l’aide de `wasb://`.

L’exemple de code suivant spécifie l’emplacement des données d’entrée pour la lecture et le chemin d’accès au stockage Blob qui est joint au cluster étincelle où le modèle doit être enregistré.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importer des données, créer un RDD et définir une trame de données en fonction du schéma

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
    val sqlContext = new SQLContext(sc)
    val taxi_schema = StructType(
        Array(
            StructField("medallion", StringType, true),
            StructField("hack_license", StringType, true),
            StructField("vendor_id", StringType, true),
            StructField("rate_code", DoubleType, true),
            StructField("store_and_fwd_flag", StringType, true),
            StructField("pickup_datetime", StringType, true),
            StructField("dropoff_datetime", StringType, true),
            StructField("pickup_hour", DoubleType, true),
            StructField("pickup_week", DoubleType, true),
            StructField("weekday", DoubleType, true),
            StructField("passenger_count", DoubleType, true),
            StructField("trip_time_in_secs", DoubleType, true),
            StructField("trip_distance", DoubleType, true),
            StructField("pickup_longitude", DoubleType, true),
            StructField("pickup_latitude", DoubleType, true),
            StructField("dropoff_longitude", DoubleType, true),
            StructField("dropoff_latitude", DoubleType, true),
            StructField("direct_distance", StringType, true),
            StructField("payment_type", StringType, true),
            StructField("fare_amount", DoubleType, true),
            StructField("surcharge", DoubleType, true),
            StructField("mta_tax", DoubleType, true),
            StructField("tip_amount", DoubleType, true),
            StructField("tolls_amount", DoubleType, true),
            StructField("total_amount", DoubleType, true),
            StructField("tipped", DoubleType, true),
            StructField("tip_class", DoubleType, true)
            )
        )

    # CAST VARIABLES ACCORDING TO THE SCHEMA
    val taxi_temp = (taxi_train_file.map(_.split("\t"))
                            .filter((r) => r(0) != "medallion")
                            .map(p => Row(p(0), p(1), p(2),
                                p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                                p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                                p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                                p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


    # CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
    val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
            .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
            .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
            .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
            .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
            .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
            .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
            .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
            .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

    # CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Sortie :**

Heure d’exécution de la cellule : 8 secondes.


### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Requête sur la table et importer les résultats dans une trame de données

Ensuite, interrogez la table pour les prix, les passagers et les données du Conseil ; filtrer des données endommagées et excentrées ; et l’impression de plusieurs lignes.

    # QUERY THE DATA
    val sqlStatement = """
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train
        WHERE passenger_count > 0 AND passenger_count < 7
        AND fare_amount > 0 AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 AND tip_amount < 25
    """
    val sqlResultsDF = sqlContext.sql(sqlStatement)

    # SHOW ONLY THE TOP THREE ROWS
    sqlResultsDF.show(3)

**Sortie :**

fare_amount|passenger_count|tip_amount|Bonhomme de neige
-----------|---------------|----------|------
       13,5|            1.0|       2.9|   1.0
       16.0|            2.0|       3.4|   1.0
       10.5|            2.0|       1.0|   1.0


## <a name="data-exploration-and-visualization"></a>Visualisation et exploration de données

Une fois que vous importez les données dans étincelle, l’étape suivante du processus de données scientifiques est d’obtenir une compréhension plus approfondie des données grâce à l’exploration et de visualisation. Dans cette section, vous examinez les données de taxi en utilisant des requêtes SQL. Importez ensuite les résultats dans un bloc de données à tracer les variables de la cible et les fonctionnalités potentiels pour l’inspection visuelle à l’aide de la fonctionnalité de visualisation automatique de Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Permet de tracer les données locales et magic SQL

Par défaut, la sortie d’un extrait de code que vous exécutez à partir d’un ordinateur portable de Jupyter est disponible dans le contexte de la session est conservée sur les nœuds du travailleur. Si vous souhaitez enregistrer un voyage vers les nœuds de traitement pour chaque calcul, et si toutes les données que vous avez besoin pour votre calcul est disponible localement sur le nœud du serveur Jupyter (qui est le nœud de tête), vous pouvez utiliser la `%%local` magique pour exécuter l’extrait de code sur le serveur Jupyter.

- **Magic SQL** (`%%sql`). Le noyau HDInsight Spark prend en charge les requêtes inline simple HiveQL SQLContext. La (`-o VARIABLE_NAME`) argument persiste la sortie de la requête SQL sous la forme d’une trame de données Pandas sur le serveur Jupyter. Cela signifie que ce sera disponible en mode local.
- `%%local`**magic**. Le `%%local` magique le code est exécuté localement sur le serveur Jupyter, qui est le nœud principal du cluster HDInsight. En général, vous utilisez `%%local` magique en conjonction avec le `%%sql` magique avec le `-o` paramètre. Le `-o` paramètre soit persistant de la sortie de la requête SQL local, puis `%%local` magique déclenche l’ensemble suivant d’extrait de code pour exécuter localement sur la sortie des requêtes SQL qui est conservée localement.

### <a name="query-the-data-by-using-sql"></a>Interroger les données à l’aide de SQL
Cette requête extrait les voyages de taxi par le montant du prix de la course, nombre de passagers et le montant de l’info-bulle.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

Dans le code suivant, le `%%local` magique crée un frame de données locales, sqlResults. Vous pouvez utiliser sqlResults pour tracer à l’aide de matplotlib.

> [AZURE.TIP] Magic local est utilisé plusieurs fois dans cet article. Si votre jeu de données est volumineux, veuillez un exemple pour créer un bloc de données qui peut tenir dans la mémoire locale.

### <a name="plot-the-data"></a>Tracer les données

Vous pouvez tracer à l’aide de code Python après que la trame de données est dans le contexte local sous la forme d’une trame de données Pandas.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 Le noyau d’allumage visualise automatiquement la sortie de requêtes SQL (HiveQL) une fois que vous exécutez le code. Vous pouvez choisir entre plusieurs types de visualisations :
 
- Table
- Graphique en secteurs
- Ligne
- Zone
- Barre

Voici le code pour tracer les données :

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # PLOT TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 80, -2, 20])
    plt.show()


**Sortie :**

![Histogramme de montant de pointe](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-histogram.png)

![Conseil le montant en fonction du nombre de passagers](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Montant par le montant des frais de Conseil](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-fare-amount.png)


## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Créer des fonctions et fonctionnalités de transformation puis préparer les données pour l’entrée en fonctions de modélisation

Pour les fonctions de modélisation basés sur l’arborescence d’allumage ML et MLlib, vous devez préparer les cibles et les fonctionnalités à l’aide d’une variété de techniques, telles que le groupement, d’indexation, à chaud un codage et vectorisation. Voici les procédures à suivre dans cette section :

1. Créer une fonction de **groupement** des heures dans les plages de temps de trafic.
2. Appliquer **l’indexation et à chaud un codage** aux fonctionnalités par catégorie.
3. **Exemple et fractionner le jeu de données** en fractions de formation et de test.
4. **Variable de formation de spécifier et de fonctionnalités**et puis créer ou non indexés à chaud un codé formation et test d’entrée point étiquetée résilient distribué des groupes de données (RDDs) ou des trames de données.
5. Automatiquement **classer et vectoriser des cibles et des fonctionnalités** à utiliser en tant qu’entrées pour les modèles de formation de machine.


### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Créer une nouvelle fonction de mise au rebus des heures dans les plages de temps de trafic

Ce code vous montre comment créer une nouvelle fonction de mise au rebus des heures dans les plages de temps de trafic et comment mettre en cache la trame de données qui en résulte dans la mémoire. Lorsque les images RDDs et les données sont utilisées à plusieurs reprises, la mise en cache aboutissent à des temps d’exécution améliorées. En conséquence, vous devez mettre en cache RDDs et les trames de données à plusieurs étapes dans les procédures suivantes.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    val sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train
    """
    val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>L’indexation et à chaud un codage des fonctionnalités par catégorie

La modélisation et prédire les fonctions de MLlib nécessitent des fonctionnalités avec des données d’entrée par catégorie pour être indexé ou codées avant d’être utilisés. Cette section vous indique comment indexer ou coder les fonctionnalités par catégorie pour les entrées dans les fonctions de modélisation.

Vous devez indexer ou coder vos modèles de différentes manières, selon le modèle. Par exemple, les modèles de la régression logistique et linéaire nécessitent à chaud un codage. Par exemple, une fonction avec trois catégories peut être développée en trois colonnes de fonctionnalité. Chaque colonne va contenir 0 ou 1 selon la catégorie d’une observation. MLlib fournit la fonction [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) à chaud un codage. Cet encodeur mappe une colonne d’index de l’étiquette d’une colonne de vecteurs binaires avec au plus un seul une valeur. Avec ce codage, les algorithmes qui attendent des fonctions à valeurs numériques, comme une régression logistique, peuvent être appliqués à des fonctions par catégorie.

Ici, vous transformez uniquement quatre variables pour afficher des exemples, qui sont des chaînes de caractères. Vous pouvez également indexer d’autres variables, telles que les jours de la semaine, représentés par des valeurs numériques, en tant que variables par catégorie.

Pour l’indexation, vous devez utiliser `StringIndexer()`et à chaud un codage, utilisez `OneHotEncoder()` fonctions de MLlib. Voici le code pour indexer et coder les fonctionnalités par catégorie :


    # CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # INDEX AND ENCODE VENDOR_ID
    val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
    val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
    val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
    val encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
    val indexed = stringIndexer.transform(encoded1)
    val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
    val encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
    val indexed = stringIndexer.transform(encoded2)
    val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
    val encoded3 = encoder.transform(indexed)

    # INDEX AND TRAFFIC TIME BINS
    val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
    val indexed = stringIndexer.transform(encoded3)
    val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
    val encodedFinal = encoder.transform(indexed)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Sortie :**

Heure d’exécution de la cellule : 4 secondes.



### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Exemple et fractionner le jeu de données en fractions de formation et de test

Ce code crée un échantillonnage aléatoire des données (dans cet exemple, 25 %). Bien qu’il n’est pas obligatoire dans cet exemple, en raison de la taille de l’ensemble de données, l’article vous explique comment vous pouvez échantillonner afin que vous sachiez comment l’utiliser pour vos propres problèmes lorsque cela est nécessaire. Lorsque des échantillons sont de grande taille, cela permet de gagner beaucoup de temps lorsque vous affinez les modèles. Ensuite, diviser l’échantillon dans une partie de la formation (dans cet exemple, 75 %) et une partie de test (25 % dans cet exemple) pour l’utiliser dans la classification et la modélisation de régression.

Ajouter un nombre aléatoire (entre 0 et 1) à chaque ligne (dans une colonne « rand ») qui peut être utilisé pour sélectionner des plis de validation croisée pendant la formation.


    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    val samplingFraction = 0.25;
    val trainingFraction = 0.75;
    val testingFraction = (1-trainingFraction);
    val seed = 1234;
    val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
    val sampledDFcount = encodedFinalSampledTmp.count().toInt

    val generateRandomDouble = udf(() => {
        scala.util.Random.nextDouble
    })

    # ADD A RANDOM NUMBER FOR CROSS-VALIDATION
    val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

    # SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
    # INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
    val trainData = splits(0)
    val testData = splits(1)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Sortie :**

Heure d’exécution de la cellule : 2 secondes.


### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Spécifier les fonctionnalités et la variable de la formation et créez ou non indexés à chaud un codé de formation et de test d’entrée intitulée RDDs de point ou de données images

Cette section contient le code qui vous montre comment indexer les données catégoriques de texte sous la forme d’un type de données de point étiquetées et comment l’encoder afin que vous puissiez à la formation et de test de régression logistique de MLlib et d’autres modèles de classification. Objets point étiquetées sont RDDs au format d’une façon qui est requis par la plupart des algorithmes dans MLlib de formation de machine en tant que données d’entrée. Une [étiquette de point](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) est à un vecteur local, dense ou clairsemée, associé à une étiquette/réponse.

Dans ce code, vous spécifiez la variable (dépendants) cible et les fonctions à utiliser pour l’apprentissage des modèles. Ensuite, vous créez ou non indexés à chaud un codé de formation et de test d’entrée intitulée trames RDDs de point ou de données.


    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
    val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
    val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

    # SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
    val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
    val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

    # CREATE INDEXED LABELED POINT RDD OBJECTS
    val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

    # CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
    val indexedTESTbinaryDF = indexedTESTbinary.toDF()
    val indexedTRAINregDF = indexedTRAINreg.toDF()
    val indexedTESTregDF = indexedTESTreg.toDF()

    # CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
    val OneHotTRAIN = assemblerOneHot.transform(trainData)
    val OneHotTEST = assemblerOneHot.transform(testData)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Sortie :**

Heure d’exécution de la cellule : 4 secondes.


### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Classer automatiquement et de vectoriser des fonctionnalités et des cibles à utiliser comme entrées d’apprentissage des modèles de l’ordinateur

Allumage ML permet de classer la cible et les fonctions à utiliser dans les fonctions de modélisation basés sur l’arborescence. Le code s’exécute deux tâches :

-   Crée une cible binaire pour la classification en assignant une valeur de 0 ou de 1 à chaque point de données compris entre 0 et 1 à l’aide d’une valeur de seuil de 0.5.
- Pour classer automatiquement les fonctionnalités. Si le nombre de valeurs distinctes de numérique pour toute fonctionnalité est inférieure à 32, cette fonctionnalité est classée par catégorie.

Voici le code pour ces deux tâches.

    # CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
    # CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINregDF)
    val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Modèle de classification binaire : prévoir si une info-bulle doit être payée.

Dans cette section, vous créez trois types de modèles de classification binaire pour prévoir ou non une info-bulle doit être payée :

- Un **modèle de régression logistique** à l’aide de l’allumage ML `LogisticRegression()` fonction
- Un **modèle de classification de forêt aléatoire** à l’aide de l’allumage ML `RandomForestClassifier()` fonction
- Un **modèle de classification d’une arborescence d’amplification dégradé** à l’aide de la MLlib `GradientBoostedTrees()` fonction

### <a name="create-a-logistic-regression-model"></a>Créer un modèle de régression logistique

Ensuite, créez un modèle de régression logistique à l’aide de l’allumage ML `LogisticRegression()` fonction. Vous créez le modèle de construction de code dans une série d’étapes :

1. Les données **d’apprentissage du modèle** avec un paramètre défini.
2. **Évaluer le modèle** sur un ensemble de données test avec des mesures.
3. **Enregistrer le modèle** dans le stockage Blob de consommation future.
4. **Score du modèle** par rapport à des données de test.
5. **Tracer les résultats** avec le récepteur les courbes caractéristiques (ROC).

Voici le code de ces procédures :

    # CREATE A LOGISTIC REGRESSION MODEL
    val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
    val lrModel = lr.fit(OneHotTRAIN)

    # PREDICT ON THE TEST DATA SET
    val predictions = lrModel.transform(OneHotTEST)

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)

    # SAVE THE MODEL
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LogisticRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

Charger, score et enregistrer les résultats.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
    val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON THE TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
    predictions.registerTempTable("testResults")

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC RESULTS
    println("ROC on test data = " + ROC)


**Sortie :**

LUNE DÉCROISSANTE sur les données de test = 0.9827381497557599


Utilisez les Python sur des trames de données Pandas locales pour tracer la courbe ROC.


    # QUERY THE RESULTS
    %%sql -q -o sqlResults
    SELECT tipped, probability from testResults


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
    predictions_pddf = sqlResults[["tipped","probFloat"]]

    # PREDICT THE ROC CURVE
    # predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
    prob = predictions_pddf["probFloat"]
    fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT THE ROC CURVE
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


**Sortie :**

![Info-bulle ou aucune courbe ROC du Conseil](./media/machine-learning-data-science-process-scala-walkthrough/plot-roc-curve-tip-or-not.png)


### <a name="create-a-random-forest-classification-model"></a>Créer un modèle de classification aléatoire

Ensuite, créez un modèle de classification aléatoire à l’aide de l’allumage ML `RandomForestClassifier()` fonction et évalue ensuite le modèle de données de test.


    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE THE RANDOM FOREST CLASSIFIER MODEL
    val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

    # FIT THE MODEL
    val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
    val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

    # EVALUATE THE MODEL
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(predictions)
    println("F1 score on test data: " + Test_f1Score);

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)


**Sortie :**

LUNE DÉCROISSANTE sur les données de test = 0.9847103571552683


### <a name="create-a-gbt-classification-model"></a>Créer un modèle de classification GBT

Ensuite, créez un modèle de classification GBT à l’aide de MLlib `GradientBoostedTrees()` fonction et évalue ensuite le modèle de données de test.


    # TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE GBT CLASSIFICATION MODEL
    val boostingStrategy = BoostingStrategy.defaultParams("Classification")
    boostingStrategy.numIterations = 20
    boostingStrategy.treeStrategy.numClasses = 2
    boostingStrategy.treeStrategy.maxDepth = 5
    boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    # TRAIN THE MODEL
    val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

    # SAVE THE MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "GBT_Classification__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    gbtModel.save(sc, filename);

    # EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
    val labelAndPreds = indexedTESTbinary.map { point =>
      val prediction = gbtModel.predict(point.features)
      (point.label, prediction)
    }
    val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
    //println("Learned classification GBT model:\n" + gbtModel.toDebugString)
    println("Test Error = " + testErr)

    # USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
    val metrics = new MulticlassMetrics(labelAndPreds)
    println(s"Precision: ${metrics.precision}")
    println(s"Recall: ${metrics.recall}")
    println(s"F1 Score: ${metrics.fMeasure}")

    val metrics = new BinaryClassificationMetrics(labelAndPreds)
    println(s"Area under PR curve: ${metrics.areaUnderPR}")
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC METRIC
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**Sortie :**

Zone sous la courbe ROC : 0.9846895479241554


## <a name="regression-model-predict-tip-amount"></a>Modèle de régression : prévoir le montant du pourboire

Dans cette section, vous créez deux types de modèles de régression pour prévoir la quantité de l’info-bulle :

- Un **être régularisée modèle de régression linéaire** à l’aide de l’allumage ML `LinearRegression()` fonction. Vous allez enregistrer le modèle et d’évaluer le modèle de données de test.
- Un **modèle de régression arborescence de renforcement de dégradé** à l’aide de l’allumage ML `GBTRegressor()` fonction.


### <a name="create-a-regularized-linear-regression-model"></a>Créer un modèle de REGULARISES régression linéaire

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
    val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

    # FIT THE MODEL BY USING DATA FRAMES
    val lrModel = lr.fit(OneHotTRAIN)
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
    val trainingSummary = lrModel.summary
    println(s"numIterations: ${trainingSummary.totalIterations}")
    println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
    trainingSummary.residuals.show()
    println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
    println(s"r2: ${trainingSummary.r2}")

    # SAVE THE MODEL IN AZURE BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LinearRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

    # PRINT THE COEFFICIENTS
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = lrModel.transform(OneHotTEST)

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Sortie :**

Heure d’exécution de la cellule : 13 secondes.


    # LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
    val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
    predictions.registerTempTable("testResults")

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("R-sqr on test data = " + r2)


**Sortie :**

R-sqr sur les données de test = 0.5960320470835743


Ensuite, les résultats des tests de requête sous la forme d’une trame de données et utiliser des AutoVizWidget et matplotlib pour le visualiser.


    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

Le code crée une trame de données local à partir du résultat de la requête et trace les données. Le `%%local` magique crée un bloc de données locales, `sqlResults`, que vous pouvez utiliser pour tracer avec matplotlib.

>[AZURE.NOTE] Ce magic étincelle est utilisé plusieurs fois dans cet article. Si la quantité de données est volumineuse, vous devez exemple pour créer un bloc de données qui peut tenir dans la mémoire locale.

Créer des tracés à l’aide de Python matplotlib.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    sqlResults
    %matplotlib inline
    import numpy as np

    # PLOT THE RESULTS
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    #ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 8])
    plt.show(ax)

**Sortie :**

![Montant de Conseil : réelle ou prévue](./media/machine-learning-data-science-process-scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)


### <a name="create-a-gbt-regression-model"></a>Créer un modèle de régression GBT

Créer un modèle de régression GBT à l’aide de l’allumage ML `GBTRegressor()` fonction et évalue ensuite le modèle de données de test.

[Arborescences d’amplifiée de dégradé](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) sont des ensembles d’arbres de décision. GBTs formation des arbres de décision itérative pour minimiser une fonction de perte. Vous pouvez utiliser GBTs pour la classification et de régression. Ils peuvent gérer les fonctionnalités par catégorie, ne nécessitent pas de mise à l’échelle en fonction et peuvent capturer des non-linéarités et des interactions de la fonctionnalité. Vous pouvez également utiliser les dans un paramètre de classification de multiclass.


    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # TRAIN A GBT REGRESSION MODEL
    val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
    val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

    # MAKE PREDICTIONS
    val predictions = gbtModel.transform(indexedTESTwithCatFeat)

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(predictions)


    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("Test R-sqr is: " + Test_R2);


**Sortie :**

Test R-sqr est : 0.7655383534596654



## <a name="advanced-modeling-utilities-for-optimization"></a>Utilitaires de modélisation avancées pour l’optimisation

Dans cette section, vous utilisez les utilitaires d’apprentissage machine que les développeurs utilisent fréquemment pour l’optimisation du modèle. En particulier, vous pouvez optimiser les modèles d’apprentissage machine trois manières différentes à l’aide de balayage de paramètre et de validation croisée :

-   Fractionner les données en ensembles de train et validation, optimiser le modèle à l’aide du paramètre hyper balayage sur un ensemble de formation et d’évaluer sur un ensemble de validation (régression linéaire)
-   Optimiser le modèle à l’aide de validation croisée et hyper-paramètre de balayage à l’aide CrossValidator fonction d’allumage ML (classification binaire)
-   Optimiser le modèle à l’aide de code personnalisé de validation croisée et paramètre-balayage à utiliser n’importe quelle machine de formation d’ensemble de la fonction et de paramètre (régression linéaire)


**Validation croisée** est une technique qui évalue le degré généralise un modèle formé d’un ensemble connu de données afin de prévoir les fonctionnalités d’ensembles de données à laquelle il n'a pas été configuré. L’idée générale de cette technique est qu’un modèle est formé d’un ensemble de données de données connues, et puis l’exactitude de ses prévisions est testée par rapport à un ensemble de données indépendant. Une implémentation commune consiste à diviser un jeu de données en *Ko*-prise en charge et ensuite former le modèle de façon alternée sur tous sauf un, des plis.

**Optimisation de Hyper-le paramètre** est le problème du choix d’un jeu de paramètres hyper-pour un algorithme d’apprentissage, généralement dans le but de l’optimisation d’une mesure des performances de l’algorithme sur un jeu de données indépendant. Un hyper-le paramètre est une valeur que vous devez spécifier en dehors de la procédure de formation de modèle. Hypothèse sur les valeurs de paramètre hyper peut affecter la souplesse et la précision du modèle. Arborescences de décision ont hyper-paramètres, par exemple, telles que la profondeur et le nombre de feuilles dans l’arborescence. Vous devez définir un terme de pénalité de mauvaise classification pour une machine à vecteurs supports (SVM).

Une méthode courante pour effectuer l’optimisation du paramètre hyper est d’utiliser une recherche de grille, également appelée un **balayage de paramètre**. Dans une recherche de la grille, une recherche exhaustive est effectuée sur les valeurs d’un sous-ensemble spécifié de l’espace de paramètre hyper pour un algorithme d’apprentissage. Validation croisée peut fournir une métrique de performances pour trier les résultats optimaux produites par l’algorithme de recherche de grille. Si vous utilisez le balayage hyper-le paramètre de validation croisée, vous pouvez aider les problèmes de limite overfitting d’un modèle de données d’apprentissage. De cette façon, le modèle conserve la capacité à appliquer à l’ensemble général de données à partir de laquelle les données de formation a été extrait.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Optimisation d’un modèle de régression linéaire avec hyper-le paramètre balayage

Ensuite, fractionnez les données en ensembles de train et validation, utilisez hyper-paramètre balayage sur un ensemble de formations pour optimiser le modèle, évalue sur un ensemble de validation (régression linéaire).

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # RENAME `tip_amount` AS A LABEL
    val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    OneHotTRAINLabeled.cache()
    OneHotTESTLabeled.cache()

    # DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
    val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

    # DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
    val trainPct = 0.75
    val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = trainValidationSplit.fit(OneHotTRAINLabeled)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    println("Test R-sqr is: " + Test_R2);


**Sortie :**

Test R-sqr est : 0.6226484708501209


### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optimiser le modèle de classification binaire à l’aide de balayage de validation croisée et paramètre hyper

Cette section vous indique comment optimiser un modèle de classification binaire à l’aide de balayage de validation croisée et paramètre hyper. Cette méthode utilise l’allumage ML `CrossValidator` fonction.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
    val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    indexedTRAINwithCatFeatBinTargetRF.cache()
    indexedTESTwithCatFeatBinTargetRF.cache()

    # DEFINE THE ESTIMATOR FUNCTION
    val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

    # SPECIFY THE NUMBER OF FOLDS
    val numFolds = 3

    # DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
    val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

    # COMPUTE THE TEST F1 SCORE
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Sortie :**

Heure d’exécution de la cellule : 33 secondes.


### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Optimiser le modèle de régression linéaire à l’aide de code personnalisé de validation croisée et paramètre-balayage

Ensuite, optimiser le modèle à l’aide de code personnalisé et identifier les meilleurs paramètres de modèle à l’aide du critère de haute précision. Ensuite, créez le modèle final, évaluer le modèle de données de test et enregistrer le modèle dans le stockage Blob. Enfin, charger le modèle de score des données de test et évaluer la précision.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

    val nFolds = 3
    val numModels = paramGrid.size
    val numParamsinGrid = 2

    # SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
    val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    var maxDepth = -1
    var numTrees = -1
    var param = ""
    var paramval = -1
    var validateLB = -1.0
    var validateUB = -1.0
    val h = 1.0 / nFolds;
    val RMSE  = Array.fill(numModels)(0.0)

    # CREATE K-FOLDS
    val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


    # LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
    for (i <- 0 to (nFolds-1)) {
        validateLB = i * h
        validateUB = (i + 1) * h
        val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
        val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
        val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        validationLabPt.cache()
        trainCVLabPt.cache()

        for (nParamSets <- 0 to (numModels-1)) {
            for (nParams <- 0 to (numParamsinGrid-1)) {
                param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
                paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
                if (param == "maxDepth") {maxDepth = paramval}
                if (param == "numTrees") {numTrees = paramval}
            }
            val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=numTrees, maxDepth=maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)
            val labelAndPreds = validationLabPt.map { point =>
                                                     val prediction = rfModel.predict(point.features)
                                                     ( prediction, point.label )
                                                    }
            val validMetrics = new RegressionMetrics(labelAndPreds)
            val rmse = validMetrics.rootMeanSquaredError
            RMSE(nParamSets) += rmse
        }
        validationLabPt.unpersist();
        trainCVLabPt.unpersist();
    }
    val minRMSEindex = RMSE.indexOf(RMSE.min)

    # GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
    var best_maxDepth = -1
    var best_numTrees = -1
    for (nParams <- 0 to (numParamsinGrid-1)) {
        param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
        paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
        if (param == "maxDepth") {best_maxDepth = paramval}
        if (param == "numTrees") {best_numTrees = paramval}
    }

    # CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
    val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)

    # SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "BestCV_RF_Regression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    best_rfModel.save(sc, filename);

    # PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = best_rfModel.predict(point.features)
                                            ( prediction, point.label )
                                           }

    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


    # LOAD THE MODEL
    val savedRFModel = RandomForestModel.load(sc, filename)

    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = savedRFModel.predict(point.features)
                                            ( prediction, point.label )
                                           }
    # TEST THE MODEL
    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**Sortie :**

Heure d’exécution de la cellule : 61 secondes.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Consommer des modèles d’apprentissage machine d’allumage intégré automatiquement avec Scala

Pour une vue d’ensemble des rubriques qui vous guident à travers les tâches qui constituent le processus de données scientifiques dans Azure, consultez [Processus d’équipe données scientifiques](http://aka.ms/datascienceprocess).

[Équipe de données Science processus procédures pas à pas](data-science-process-walkthroughs.md) décrit les autres procédures pas à pas à-bout illustrant les étapes du processus de Science de données équipe pour des scénarios spécifiques. Les procédures pas à pas illustrent également comment combiner les outils nuage et sur site et des services dans un flux de travail ou d’un pipeline pour créer une application intelligente.

[Machine d’allumage intégré score modèles de formation](machine-learning-data-science-spark-model-consumption.md) vous montre comment utiliser le code de Scala pour charger automatiquement et score de nouveaux jeux de données avec des modèles d’apprentissage automatique intégré étincelle et enregistrées dans le stockage des objets Blob Azure. Vous pouvez suivre les instructions fournies et simplement remplacer le code Python code Scala dans cet article pour la consommation automatique.
