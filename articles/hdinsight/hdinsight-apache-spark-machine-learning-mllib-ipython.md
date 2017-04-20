<properties 
    pageTitle="Permet de créer des applications de formation de machine sur HDInsight Apache étincelle | Microsoft Azure" 
    description="Instructions détaillées sur l’utilisation des ordinateurs portables avec Apache allumage pour créer des applications de formation de machine" 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="machine-learning-predictive-analysis-on-food-inspection-data-using-mllib-with-apache-spark-cluster-on-hdinsight-linux"></a>Machine d’apprentissage : analyse prédictive de DENREES ALIMENTAIRES de cluster à l’aide de MLlib avec Apache étincelle sur HDInsight Linux

> [AZURE.TIP] Ce didacticiel est également disponible en tant que Jupyter bloc-notes sur un cluster d’allumage (Linux) que vous créez dans HDInsight. L’expérience de l’ordinateur portable vous permet d’exécuter des extraits de code Python à partir de l’ordinateur portable lui-même. Pour exécuter le didacticiel à partir d’un ordinateur portable, créer un cluster d’allumage, de lancer un portable Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), puis exécutez le bloc-notes **étincelle, Machine d’apprentissage - analyse prédictive de données d’inspection alimentaires à l’aide de MLLib.ipynb** sous le dossier **Python** .


Cet article montre comment utiliser des **MLLib**, ordinateurs intégrés à allumage bibliothèques, de formation pour effectuer une simple analyse prédictive sur un groupe de données ouverte. MLLib est une bibliothèque d’allumage fondamental qui fournit un certain nombre d’utilitaires qui sont utiles pour les tâches d’apprentissage machine, y compris les utilitaires qui conviennent pour :

* Classification

* Régression

* Gestion de clusters

* Modélisation de la rubrique

* Décomposition de valeur singulière (MVP) et d’une analyse par composantes principales (PCA)

* Hypothèse de test et de calcul des statistiques de l’échantillon

Cet article présente une approche simple de *classification* de régression logistique.

## <a name="what-are-classification-and-logistic-regression"></a>Quelles sont la classification et régression logistique ?

*Classification*, une machine très courante, tâche, de la formation est le processus de tri des données d’entrée en catégories. Il s’agit de la tâche d’un algorithme de classification à déterminer comment affecter « labels » d’entrée de données que vous fournissez. Par exemple, vous pourriez utiliser un algorithme d’apprentissage machine qui accepte les informations boursières en tant qu’entrée et divise le brut en deux catégories : qui vous devez vendre et des stocks que vous devez conserver.

Régression logistique est l’algorithme que vous utilisez pour la classification. Régression logistique d’allumage API est utile pour la *classification binaire*ou classer les données d’entrée dans un des deux groupes. Pour plus d’informations sur les régressions logistiques, consultez [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

En résumé, le processus de régression logistique crée une *fonction logistique* qui peut être utilisée pour prédire la probabilité qu’un vecteur d’entrée appartient dans un groupe ou à l’autre.  

## <a name="what-are-we-trying-to-accomplish-in-this-article"></a>Ce que nous essayons d’accomplir dans cet article ?

Vous allez utiliser l’allumage pour effectuer une analyse prédictive de données d’inspection alimentaires (**Food_Inspections1.csv**) qui a été acquis par le biais du [portail des données Ville de Chicago](https://data.cityofchicago.org/). Ce groupe de données contient des informations concernant les inspections de denrées alimentaires qui ont été réalisés à Chicago, y compris des informations sur chaque établissement alimentaire qui a été l’objet d’une inspection, les violations trouvées (le cas échéant) et les résultats de l’inspection. Le fichier de données CSV est déjà disponible dans le compte de stockage associé au cluster à **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

Dans les étapes suivantes, vous développez un modèle pour voir ce qu’il faut en réussite ou échec d’une inspection des denrées alimentaires. 

## <a name="start-building-a-machine-learning-application-using-spark-mllib"></a>Démarrer la création d’une application d’apprentissage machine à l’aide d’allumage MLlib

1. À partir du [Portail Azure](https://portal.azure.com/), à partir de la startboard, cliquez sur la mosaïque pour votre cluster allumage (si vous l’épinglé à la startboard). Vous pouvez également accéder à votre cluster sous **Parcourir tous les** > **HDInsight Clusters**.   

2. À partir de la blade de cluster étincelle, cliquez sur le **Tableau de bord de Cluster**, puis cliquez sur **Bloc-notes de Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administration pour le cluster.

    > [AZURE.NOTE] Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l’URL suivante dans votre navigateur. Remplacez __NOM_CLUSTER__ par le nom de votre cluster :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un nouveau bloc-notes. Cliquez sur **Nouveau**, puis cliquez sur **PySpark**.

    ![Créer un nouveau bloc-notes de Jupyter] (./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.createnotebook.png "Créer un nouveau bloc-notes de Jupyter")

3. Un nouvel ordinateur portable est créé et ouverte avec le nom Untitled.pynb. Cliquez sur le nom du bloc-notes dans la partie supérieure et entrez un nom convivial.

    ![Fournir un nom pour l’ordinateur portable] (./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.notebook.name.png "Fournir un nom pour l’ordinateur portable")

3. Parce que vous avez créé un bloc-notes à l’aide de la PySpark de noyau, vous n’avez pas besoin créer les contextes explicitement. Les contextes d’allumage et ruche seront automatiquement créés pour vous lors de l’exécution de la première cellule de code. Vous pouvez commencer à créer de votre ordinateur, application de formation en important les types requis pour ce scénario. Pour ce faire, placez le curseur dans la cellule et appuyez sur **MAJ + ENTRÉE**.


        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
        from pyspark.sql.functions import UserDefinedFunction
        from pyspark.sql.types import *

## <a name="construct-an-input-dataframe"></a>Construction d’un dataframe d’entrée

Nous pouvons utiliser `sqlContext` pour effectuer des transformations sur les données structurées. La première tâche consiste à charger les données de l’échantillon ((**Food_Inspections1.csv**)) dans une étincelle SQL *dataframe*. 

1. Étant donné que les données brutes sont dans un format CSV, nous devons utiliser le contexte de l’allumage pour extraire chaque ligne du fichier en mémoire sous forme de texte non structurée ; Ensuite, vous utilisez bibliothèque CSV de Python à analyser chaque ligne individuellement. 


        def csvParse(s):
            import csv
            from StringIO import StringIO
            sio = StringIO(s)
            value = csv.reader(sio).next()
            sio.close()
            return value
        
        inspections = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                        .map(csvParse)


2. Nous avons maintenant le fichier CSV comme un RDD. Nous extraient une seule ligne à partir de la RDD pour comprendre le schéma des données.


        inspections.take(1)


    Vous devez voir une sortie comme suit :

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [['413707',
          'LUNA PARK INC',
          'LUNA PARK  DAY CARE',
          '2049789',
          "Children's Services Facility",
          'Risk 1 (High)',
          '3250 W FOSTER AVE ',
          'CHICAGO',
          'IL',
          '60625',
          '09/21/2010',
          'License-Task Force',
          'Fail',
          '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
          '41.97583445690982',
          '-87.7107455232781',
          '(41.97583445690982, -87.7107455232781)']]


3. La sortie ci-dessus nous donne une idée du schéma du fichier d’entrée ; le fichier comprend le nom de chaque établissement, le type d’établissement, l’adresse, les données des contrôles et l’emplacement, entre autres choses. Nous allons sélectionner quelques colonnes qui seront utiles pour notre analyse prédictive et regrouper les résultats sous la forme d’un dataframe, qui nous permet de créer une table temporaire.


        schema = StructType([
        StructField("id", IntegerType(), False), 
        StructField("name", StringType(), False), 
        StructField("results", StringType(), False), 
        StructField("violations", StringType(), True)])

        df = sqlContext.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
        df.registerTempTable('CountResults')

4. Nous disposons désormais d’un *dataframe*, `df` à laquelle nous pouvons effectuer notre analyse. Nous avons également une table temporaire d’appeler **CountResults**. Nous avons inclus les 4 colonnes d’intérêt dans le dataframe : **id**, **nom**, **résultats**et **violations**. 
    
    Nous allons apprendre un petit échantillon des données :

        df.show(5)

    Vous devez voir une sortie comme suit :

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        +------+--------------------+-------+--------------------+
        |    id|                name|results|          violations|
        +------+--------------------+-------+--------------------+
        |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
        |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
        |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
        |413708|BENCHMARK HOSPITA...|   Pass|                    |
        |413722|           JJ BURGER|   Pass|                    |
        +------+--------------------+-------+--------------------+

## <a name="understand-the-data"></a>Comprendre les données

1. Commençons à avoir une idée de ce que notre groupe de données contient. Par exemple, quelles sont les différentes valeurs dans la colonne de **résultats** ?


        df.select('results').distinct().show()

    
    Vous devez voir une sortie comme suit :

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
    
        +--------------------+
        |             results|
        +--------------------+
        |                Fail|
        |Business Not Located|
        |                Pass|
        |  Pass w/ Conditions|
        |     Out of Business|
        +--------------------+
    
2. Une visualisation rapide peut nous aider à motif sur la distribution de ces résultats. Nous avons déjà les données dans une table temporaire **CountResults**. Vous pouvez exécuter la requête SQL suivante sur la table pour obtenir une meilleure compréhension de la façon dont les résultats sont distribués.

        %%sql -o countResultsdf
        SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results

    Le `%%sql` magique suivie par `-o countResultsdf` garantit que le résultat de la requête est conservé localement sur le serveur Jupyter (en général, le headnode du cluster). La sortie est rendu persistant sous la forme d’un dataframe de [Pandas](http://pandas.pydata.org/) avec le nom spécifié **countResultsdf**.
    
    Vous devez voir une sortie comme suit :
    
    ![Résultat de la requête SQL] (./media/hdinsight-apache-spark-machine-learning-mllib-ipython/query.output.png "Résultat de la requête SQL")

    Pour plus d’informations sur le `%%sql` magic, ainsi que d’autres magics disponibles avec le noyau PySpark, reportez-vous à la section [noyaux disponibles sur les ordinateurs portables de Jupyter avec HDInsight d’érosion clusters](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

3. Vous pouvez également utiliser Matplotlib, une bibliothèque utilisée pour construire la visualisation de données, pour créer un tracé. Dans la mesure où le tracé doit être créé à partir de la dataframe conservé localement **countResultsdf** , l’extrait de code doit commencer par le `%%local` magique. Cela garantit que le code est exécuté localement sur le serveur Jupyter.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        
        labels = countResultsdf['results']
        sizes = countResultsdf['cnt']
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')

    Vous devez voir une sortie comme suit :

    ![Sortie des résultats :](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_13_1.png)


4. Vous pouvez voir qu’il y a 5 résultats distincts ayant une inspection :
    
    * Entreprises que ne se trouve ne pas 
    * Échec
    * Passer
    * Support technique avec les conditions, et
    * Activités 

    Nous développer un modèle qui peut deviner à l’issue d’une inspection alimentaires, étant donné les violations. Régression logistique étant une méthode de classification binaire, il est judicieux de regrouper les données en deux catégories : **Échec** et **passer**. Un « passer avec Conditions » est toujours un passage, afin que, lorsque nous formons le modèle, nous allons étudier les résultats de deux équivalents. Données avec les autres résultats (« Entreprise ne se trouve pas », « Out of Business ») ne sont pas utiles pour nous les supprimons de notre ensemble de formation. Étant donné que ces deux catégories constituent un très faible pourcentage des résultats malgré tout, cela devrait être OK.

5. Nous continuez et convertir notre dataframe existant (`df`) dans une nouvelle dataframe où chaque inspection est représentée sous la forme d’une paire de violations de l’étiquette. Dans notre cas, une étiquette de `0.0` représente un échec, une étiquette de `1.0` représente une réussite et une étiquette de `-1.0` représente certains résultats en plus de ces deux. Il filtre les autres résultats lors du calcul de la nouvelle image de données.


        def labelForResults(s):
            if s == 'Fail':
                return 0.0
            elif s == 'Pass w/ Conditions' or s == 'Pass':
                return 1.0
            else:
                return -1.0
        label = UserDefinedFunction(labelForResults, DoubleType())
        labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')


    Nous allons extraire une ligne à partir des données étiquetées pour voir à quoi il ressemble.


        labeledData.take(1)


    Vous devez voir une sortie comme suit :
    
        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
    
        [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]


## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Créer un modèle de régression logistique à partir du dataframe d’entrée

La dernière tâche consiste à convertir des données étiquetées dans un format qui peut être analysé par régression logistique. L’entrée à un algorithme de régression logistique doit être à un ensemble de *paires de vecteur de fonctionnalité d’étiquette*, où le vecteur « fonction » est un vecteur de nombres qui représente le point d’entrée d’une certaine façon. Nous devons donc un moyen de convertir la colonne « infractions », semi-structurées et contient un grand nombre de commentaires dans le texte libre, dans un tableau de nombres réels qui comprend un ordinateur facilement. 

Un ordinateur standard approche pour le traitement de langage naturel d’apprentissage est pour affecter chaque mot distinct un « index », puis passer un vecteur à la machine algorithme d’apprentissage tels que la valeur de chaque index contient la fréquence relative de ce mot dans la chaîne de texte. 

MLLib est un moyen simple d’effectuer cette opération. Tout d’abord, nous allons « réinitialiser » chaque chaîne de violations pour obtenir les mots individuels dans chaque chaîne et puis nous allons utiliser une `HashingTF` pour convertir chaque jeu de jetons en un vecteur de fonctionnalité qui peut ensuite être passé à l’algorithme de régression logistique pour construire un modèle. Nous allons effectuer toutes ces étapes à l’aide d’un « pipeline ».


    tokenizer = Tokenizer(inputCol="violations", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    
    model = pipeline.fit(labeledData)


## <a name="evaluate-the-model-on-a-separate-test-dataset"></a>Évaluer le modèle sur un groupe de données de test distinct

Nous pouvons utiliser le modèle que nous avons créé précédemment pour *prédire* quels sont les résultats de nouvelles inspections sont, aux violations qui ont été observées. Nous avons formé ce modèle sur le groupe de données **Food_Inspections1.csv**. Utilisons un second groupe de données, **Food_Inspections2.csv**, pour *évaluer* la puissance de ce modèle sur de nouvelles données. Ce second ensemble de données (**Food_Inspections2.csv**) doit déjà exister dans le conteneur de stockage par défaut associé au cluster.

1. L’extrait de code ci-dessous crée un nouveau dataframe, **predictionsDf** qui contient la prédiction générée par le modèle. L’extrait de code crée également une table temporaire **que prévisions** basées sur le dataframe.


        testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                 .map(csvParse) \
                 .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
        testDf = sqlContext.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
        predictionsDf = model.transform(testDf)
        predictionsDf.registerTempTable('Predictions')
        predictionsDf.columns


    Vous devez voir une sortie comme suit :
    
        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
        
        ['id',
         'name',
         'results',
         'violations',
         'words',
         'features',
         'rawPrediction',
         'probability',
         'prediction']

2. Observez une des prévisions. Exécutez cet extrait de code :

        predictionsDf.take(1)

    Vous verrez la prédiction à la première entrée dans le jeu de données de test.

3. Le `model.transform()` méthode appliquer la même transformation à toutes les nouvelles données avec le même schéma et parvenir à une prévision de la manière de classer les données. Nous pouvons faire des statistiques simples pour avoir une idée de la précision ont été de nos prévisions :


        numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR 
                                              (prediction = 1 AND (results = 'Pass' OR 
                                                                   results = 'Pass w/ Conditions'))""").count()
        numInspections = predictionsDf.count()
        
        print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
        print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"

    La sortie ressemble à ceci :
    
        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
    
        There were 9315 inspections and there were 8087 successful predictions
        This is a 86.8169618894% success rate


    À l’aide de la régression logistique avec allumage nous fournit un modèle précis de la relation entre une entreprise donnée peut passer ou échouer une inspection des denrées alimentaires et des descriptions de violations en anglais. 

## <a name="create-a-visual-representation-of-the-prediction"></a>Créer une représentation visuelle de la prévision

Nous pouvons maintenant construire une visualisation finale pour nous aider à raison sur les résultats de ce test. 

1. Nous allons commencer en extrayant les prévisions différentes et les résultats de la table temporaire **Predictions** créée précédemment. Les requêtes suivantes séparent la sortie en tant que *true_positive*, *false_positive*, *true_negative*et *false_negative*. Dans les requêtes ci-dessous, nous désactiver la visualisation à l’aide de `-q` et la sortie de l’enregistrement (à l’aide de `-o`) en tant que dataframes qui peut être ensuite utilisé avec la `%%local` magique. 

        %%sql -q -o true_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'

        %%sql -q -o false_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')

        %%sql -q -o true_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'

        %%sql -q -o false_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions') 

2. Enfin, utilisez l’extrait suivant pour générer le tracé à l’aide de **Matplotlib**.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        labels = ['True positive', 'False positive', 'True negative', 'False negative']
        sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')
    
    Vous devez voir la sortie suivante.
    
    ![Sortie de prévision](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_26_1.png)


    Dans ce graphique, un résultat « positif » fait référence à l’inspection des denrées alimentaires ayant échoué, tandis qu’un résultat négatif une inspection passée.

## <a name="shut-down-the-notebook"></a>Arrêt de l’ordinateur portable

Lorsque vous avez terminé l’exécution de l’application, vous devez arrêter l’ordinateur portable pour libérer les ressources. Pour ce faire, dans le menu **fichier** du bloc-notes, cliquez sur **Fermer et arrêter**. Cette va s’arrêter, puis fermez le bloc-notes.


## <a name="seealso"></a>Voir aussi


* [Vue d’ensemble : Allumage commandé de Apache sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios

* [Allumage avec BI : effectuer l’analyse interactive des données à l’aide d’étincelle dans HDInsight avec les outils d’analyse Décisionnelle](hdinsight-apache-spark-use-bi-tools.md)

* [Allumage avec apprentissage automatique : allumage d’utilisation dans les HDInsight d’analyse de température de construction à l’aide des données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Diffusion en continu de l’allumage : Allumage d’utilisation dans HDInsight pour générer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

* [Analyse de journal de site Web à l’aide d’étincelle dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Créer et exécuter des applications

* [Créez une application autonome à l’aide de Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Exécuter des tâches à distance sur un cluster d’allumage à l’aide de Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions

* [Plug-in d’outils HDInsight idée de IntelliJ permet de créer et soumettre des applications d’allumage Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Plug-in d’outils HDInsight IntelliJ idée permet de déboguer des applications d’allumage commandé à distance](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utilisez les portables Zeppelin avec un cluster d’allumage sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Noyaux disponibles pour le cluster d’allumage pour HDInsight ordinateur portable Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utilisez les lots externes avec les ordinateurs portables de Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installez Jupyter sur votre ordinateur et vous connecter à un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources

* [Gérer les ressources du cluster Apache étincelle dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tâches de suivi et de débogage en cours d’exécution sur un cluster Apache étincelle dans HDInsight](hdinsight-apache-spark-job-debugging.md)
