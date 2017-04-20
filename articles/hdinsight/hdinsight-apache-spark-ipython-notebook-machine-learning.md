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


# <a name="build-machine-learning-applications-to-run-on-apache-spark-clusters-on-hdinsight-linux"></a>Créer des applications à exécuter sur les clusters d’allumage d’Apache sur HDInsight Linux apprentissage automatique

Apprenez à créer une machine de formation d’application à l’aide d’un cluster Apache étincelle dans HDInsight. Cet article explique comment utiliser le bloc-notes Jupyter disponible avec le cluster pour générer et tester l’application. L’application utilise l’exemple de données de HVAC.csv qui est disponible sur tous les clusters par défaut.

**Conditions préalables :**

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster d’allumage d’Apache sur HDInsight Linux. Pour obtenir des instructions, consultez [créer un allumage Apache des clusters dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md). 

##<a name="data"></a>Afficher les données

Avant d’entamer la création de l’application, nous de comprendre la structure des données et le type d’analyse que nous ferons sur les données. 

Dans cet article, nous utilisons l’exemple de fichier de données **HVAC.csv** qui est disponible dans le compte de stockage Azure que vous avez associés au cluster HDInsight. Dans le compte de stockage, le fichier est en **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Téléchargez et ouvrez le fichier CSV pour obtenir un instantané des données.  

![Capture instantanée de données HVAC] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.png "Capture instantanée des données HVAC")

Les données indiquent la température de la cible et la température réelle d’un bâtiment qui a installé des systèmes HVAC. Supposons que la colonne **système** représente l’ID du système et de la colonne **SystemAge** représente le nombre d’années que le système de climatisation a été en place dans le bâtiment.

Ces données nous permet de prédire si un bâtiment chaudes ou surgelé basé sur la température de cible, étant donnée un ID système et l’âge du système.

##<a name="app"></a>Écrire une application d’apprentissage machine à l’aide d’allumage MLlib

Dans cette application, nous utilisons un pipeline ML d’allumage pour effectuer une classification de document. Dans le pipeline, nous divisé le document en mots, de convertir les mots dans un vecteur de fonctionnalité numérique et enfin créer un modèle de prévision à l’aide de vecteurs de fonction et des étiquettes. Procédez comme suit pour créer l’application.

1. À partir du [Portail Azure](https://portal.azure.com/), à partir de la startboard, cliquez sur la mosaïque pour votre cluster allumage (si vous l’épinglé à la startboard). Vous pouvez également accéder à votre cluster sous **Parcourir tous les** > **HDInsight Clusters**.   

2. À partir de la blade de cluster étincelle, cliquez sur le **Tableau de bord de Cluster**, puis cliquez sur **Bloc-notes de Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administration pour le cluster.

    > [AZURE.NOTE] Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l’URL suivante dans votre navigateur. Remplacez __NOM_CLUSTER__ par le nom de votre cluster :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un nouveau bloc-notes. Cliquez sur **Nouveau**, puis cliquez sur **PySpark**.

    ![Créer un nouveau bloc-notes de Jupyter] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.createnotebook.png "Créer un nouveau bloc-notes de Jupyter")

3. Un nouvel ordinateur portable est créé et ouverte avec le nom Untitled.pynb. Cliquez sur le nom du bloc-notes dans la partie supérieure et entrez un nom convivial.

    ![Fournir un nom pour l’ordinateur portable] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.notebook.name.png "Fournir un nom pour l’ordinateur portable")

3. Parce que vous avez créé un bloc-notes à l’aide de la PySpark de noyau, vous n’avez pas besoin créer les contextes explicitement. Les contextes d’allumage et ruche seront automatiquement créés pour vous lors de l’exécution de la première cellule de code. Vous pouvez commencer par importer les types qui sont requis pour ce scénario. Collez l’extrait de code suivant dans une cellule vide et appuyez sur **MAJ + ENTRÉE**. 

        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
        
        import os
        import sys
        from pyspark.sql.types import *
        
        from pyspark.mllib.classification import LogisticRegressionWithSGD
        from pyspark.mllib.regression import LabeledPoint
        from numpy import array
        
        
     
4. Vous devez maintenant charger les données (hvac.csv), l’analyser et l’utiliser pour former le modèle. Pour ce faire, vous définissez une fonction qui vérifie si la température réelle du bâtiment est supérieure à la température de la cible. Si la température réelle est supérieure, l’immeuble est actif, indiqué par la valeur **1.0**. Si la température réelle est moindre, le bâtiment est à froid, indiqué par la valeur **0.0**. 

    Collez l’extrait de code suivant dans une cellule vide et appuyez sur **MAJ + ENTRÉE**.

        
        # List the structure of data for better understanding. Becuase the data will be
        # loaded as an array, this structure makes it easy to understand what each element
        # in the array corresponds to

        # 0 Date
        # 1 Time
        # 2 TargetTemp
        # 3 ActualTemp
        # 4 System
        # 5 SystemAge
        # 6 BuildingID

        LabeledDocument = Row("BuildingID", "SystemInfo", "label")

        # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
        
        def parseDocument(line):
            values = [str(x) for x in line.split(',')]
            if (values[3] > values[2]):
                hot = 1.0
            else:
                hot = 0.0        
    
            textValue = str(values[4]) + " " + str(values[5])
    
            return LabeledDocument((values[6]), textValue, hot)

        # Load the raw HVAC.csv file, parse it using the function
        data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
        training = documents.toDF()


5. Configurer les opportunités de formation de machine étincelle qui se compose de trois étapes : lr, hashingTF et Générateur de jetons. Pour plus d’informations sur ce qu’est un tuyau et son fonctionnement, voir <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">machine étincelle pipeline d’apprentissage</a>.

    Collez l’extrait de code suivant dans une cellule vide et appuyez sur **MAJ + ENTRÉE**.

        tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
        hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
        lr = LogisticRegression(maxIter=10, regParam=0.01)
        pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

6. Ajuster le pipeline pour les documents de formation. Collez l’extrait de code suivant dans une cellule vide et appuyez sur **MAJ + ENTRÉE**.

        model = pipeline.fit(training)

7. Vérifiez le document de formation à un point de contrôle de votre progression dans l’application. Collez l’extrait de code suivant dans une cellule vide et appuyez sur **MAJ + ENTRÉE**.

        training.show()

    Le résultat semblable au suivant doit s’afficher :

        +----------+----------+-----+
        |BuildingID|SystemInfo|label|
        +----------+----------+-----+
        |         4|     13 20|  0.0|
        |        17|      3 20|  0.0|
        |        18|     17 20|  1.0|
        |        15|      2 23|  0.0|
        |         3|      16 9|  1.0|
        |         4|     13 28|  0.0|
        |         2|     12 24|  0.0|
        |        16|     20 26|  1.0|
        |         9|      16 9|  1.0|
        |        12|       6 5|  0.0|
        |        15|     10 17|  1.0|
        |         7|      2 11|  0.0|
        |        15|      14 2|  1.0|
        |         6|       3 2|  0.0|
        |        20|     19 22|  0.0|
        |         8|     19 11|  0.0|
        |         6|      15 7|  0.0|
        |        13|      12 5|  0.0|
        |         4|      8 22|  0.0|
        |         7|      17 5|  0.0|
        +----------+----------+-----+


    Revenez en arrière et vérifier le résultat dans le fichier CSV brut. Par exemple, la première ligne du fichier CSV comporte les données :

    ![Capture instantanée de données HVAC] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.first.row.png "Capture instantanée des données HVAC")

    Notez que la température réelle est inférieure à la température de cible suggérant que la construction est à froid. Par conséquent, dans la sortie de la formation, la valeur de l' **étiquette** dans la première ligne est **0,0**, ce qui signifie que le bâtiment n’est pas actif.

8.  Préparer un ensemble de données pour exécuter le modèle formé contre. Pour ce faire, nous passerait sur un ID système et l’âge de système (indiquée dans la sortie de la formation comme **SystemInfo** ) et le modèle devrait prévoir si le bâtiment avec cet ID de système et l’âge du système serait plus chaud (indiquée par 1.0) ou plus froide (indiquée par 0.0).

    Collez l’extrait de code suivant dans une cellule vide et appuyez sur **MAJ + ENTRÉE**.
        
        # SystemInfo here is a combination of system ID followed by system age
        Document = Row("id", "SystemInfo")
        test = sc.parallelize([(1L, "20 25"),
                      (2L, "4 15"),
                      (3L, "16 9"),
                      (4L, "9 22"),
                      (5L, "17 10"),
                      (6L, "7 22")]) \
            .map(lambda x: Document(*x)).toDF() 

9. Enfin, effectuer des prévisions sur les données de test. Collez l’extrait de code suivant dans une cellule vide et appuyez sur **MAJ + ENTRÉE**.

        # Make predictions on test documents and print columns of interest
        prediction = model.transform(test)
        selected = prediction.select("SystemInfo", "prediction", "probability")
        for row in selected.collect():
            print row

10. Vous devriez voir une sortie semblable à la suivante :

        Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
        Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
        Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
        Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
        Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
        Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))

    À partir de la première ligne dans la prévision, vous pouvez voir que pour un système de climatisation avec l’ID 20 et l’âge de système de 25 ans, le bâtiment sera à chaud (**prévision = 1.0**). La première valeur de DenseVector (0.49999) correspond à la prédiction de 0,0 et la deuxième valeur (0.5001) correspond à la prédiction de 1.0. Dans la sortie, même si la deuxième valeur est seulement très légèrement plus élevée, le modèle affiche **prévision = 1.0**.

11. Lorsque vous avez terminé l’exécution de l’application, vous devez arrêter l’ordinateur portable pour libérer les ressources. Pour ce faire, dans le menu **fichier** du bloc-notes, cliquez sur **Fermer et arrêter**. Cette va s’arrêter, puis fermez le bloc-notes.
           

##<a name="anaconda"></a>Utiliser des scikit de Anaconda-en savoir plus de la bibliothèque pour l’apprentissage de l’ordinateur

Clusters d’allumage d’Apache sur HDInsight incluent les bibliothèques Anaconda. Cela inclut également la **scikit-en savoir** bibliothèque pour l’apprentissage de l’ordinateur. La bibliothèque inclut également différents jeux de données que vous pouvez utiliser pour générer les exemples d’applications directement à partir d’un ordinateur portable de Jupyter. Pour obtenir des exemples sur l’utilisation de la scikit-en savoir plus de la bibliothèque, consultez [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

##<a name="seealso"></a>Voir aussi

* [Vue d’ensemble : Allumage commandé de Apache sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios

* [Allumage avec BI : effectuer l’analyse interactive des données à l’aide d’étincelle dans HDInsight avec les outils d’analyse Décisionnelle](hdinsight-apache-spark-use-bi-tools.md)

* [Allumage avec apprentissage automatique : allumage utilisation de HDInsight pour prédire les résultats de l’inspection alimentaires](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

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


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md
