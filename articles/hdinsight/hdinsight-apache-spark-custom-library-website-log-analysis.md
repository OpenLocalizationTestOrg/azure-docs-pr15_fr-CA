<properties 
    pageTitle="Utiliser des bibliothèques personnalisées avec un cluster HDInsight Spark analyser les journaux du site Web | Microsoft Azure" 
    description="Utiliser des bibliothèques personnalisées avec un cluster HDInsight Spark analyser les journaux du site Web" 
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

# <a name="analyze-website-logs-using-a-custom-library-with-apache-spark-cluster-on-hdinsight-linux"></a>Analyser les journaux du site Web à l’aide d’une bibliothèque personnalisée avec cluster Apache étincelle sur HDInsight Linux

Ce bloc-notes montre comment analyser les données du journal à l’aide d’une bibliothèque personnalisée avec allumage sur HDInsight. La bibliothèque personnalisée, que nous utilisons est une bibliothèque de Python appelée **iislogparser.py**.

> [AZURE.TIP] Ce didacticiel est également disponible en tant que Jupyter bloc-notes sur un cluster d’allumage (Linux) que vous créez dans HDInsight. L’expérience de l’ordinateur portable vous permet d’exécuter des extraits de code Python à partir de l’ordinateur portable lui-même. Pour exécuter le didacticiel à partir d’un ordinateur portable, créer un cluster d’allumage, de lancer un portable Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), puis exécutez le bloc-notes **ouvre une analyse avec allumage à l’aide d’un library.ipynb personnalisé** sous le dossier **PySpark** .

**Conditions préalables :**

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster d’allumage d’Apache sur HDInsight Linux. Pour obtenir des instructions, consultez [créer un allumage Apache des clusters dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Enregistrer les données brutes sous un RDD

Dans cette section, nous utilisons le bloc-notes [Jupyter](https://jupyter.org) associé à un cluster Apache étincelle dans HDInsight pour exécuter les tâches de traitent vos données exemple brut et l’enregistrement sous la forme d’une table de la ruche. L’exemple de données est un fichier .csv (hvac.csv) disponible sur tous les clusters par défaut.

Une fois que vos données sont enregistrées sous la forme d’une table de la ruche, dans la section suivante, nous se connecte à la table de la ruche à l’aide d’outils BI tels que BI de puissance et de Tableau.

1. À partir du [Portail Azure](https://portal.azure.com/), à partir de la startboard, cliquez sur la mosaïque pour votre cluster allumage (si vous l’épinglé à la startboard). Vous pouvez également accéder à votre cluster sous **Parcourir tous les** > **HDInsight Clusters**.   

2. À partir de la blade de cluster étincelle, cliquez sur le **Tableau de bord de Cluster**, puis cliquez sur **Bloc-notes de Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administration pour le cluster.

    > [AZURE.NOTE] Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l’URL suivante dans votre navigateur. Remplacez __NOM_CLUSTER__ par le nom de votre cluster :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un nouveau bloc-notes. Cliquez sur **Nouveau**, puis cliquez sur **PySpark**.

    ![Créer un nouveau bloc-notes de Jupyter] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.createnotebook.png "Créer un nouveau bloc-notes de Jupyter")

3. Un nouvel ordinateur portable est créé et ouverte avec le nom Untitled.pynb. Cliquez sur le nom du bloc-notes dans la partie supérieure et entrez un nom convivial.

    ![Fournir un nom pour l’ordinateur portable] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.notebook.name.png "Fournir un nom pour l’ordinateur portable")

4. Parce que vous avez créé un bloc-notes à l’aide de la PySpark de noyau, vous n’avez pas besoin créer les contextes explicitement. Les contextes d’allumage et ruche seront automatiquement créés pour vous lors de l’exécution de la première cellule de code. Vous pouvez commencer par importer les types qui sont requis pour ce scénario. Collez l’extrait de code suivant dans une cellule vide et appuyez sur **MAJ + ENTRÉE**.


        from pyspark.sql import Row
        from pyspark.sql.types import *


5. Créer un RDD avec les données journal déjà disponibles sur le cluster. Vous pouvez accéder aux données du compte de stockage par défaut associé au cluster à **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**.


        logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


6. Récupérer un exemple de journal afin de vérifier que la valeur de l’étape précédente s’est terminée correctement.

        logs.take(5)

    Vous devriez voir une sortie semblable à la suivante :

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analyser les données du journal à l’aide d’une bibliothèque personnalisée de Python

7. Dans la sortie ci-dessus, les lignes deux incluent les informations d’en-tête et chaque ligne restante correspond le schéma décrit dans cet en-tête. L’analyse de ces journaux peut être complexe. Ainsi, nous utilisons une bibliothèque Python personnalisée (**iislogparser.py**) qui facilite l’analyse des ces journaux beaucoup plus faciles. Par défaut, cette bibliothèque est incluse dans votre cluster allumage de HDInsight à **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

    Toutefois, cette bibliothèque n’est pas dans le `PYTHONPATH` afin que nous ne pouvons pas en utilisant une instruction d’importation comme `import iislogparser`. Pour utiliser cette bibliothèque, nous devons distribuer à tous les nœuds du travailleur. Exécutez l’extrait de code suivant.


        sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


9. `iislogparser`Fournit une fonction de `parse_log_line` qui renvoie `None` si une ligne de journal est une ligne d’en-tête et retourne une instance de le `LogLine` de classe si elle rencontre une ligne de journal. Utilisez le `LogLine` classe pour extraire uniquement les lignes de journal à partir de la RDD :

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()


10. Récupérer quelques lignes extraites du journal afin de vérifier que l’étape s’est terminée correctement.

        logLines.take(2)

    La sortie doit être similaire à la suivante :

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]


11. Le `LogLine` (classe), à son tour, a quelques méthodes fort utiles, comme `is_error()`, qui retourne une valeur si une entrée de journal possède un code d’erreur. Utilisez cette option pour calculer le nombre d’erreurs dans les lignes de journal extraits et toutes les erreurs puis ouvrez une session dans un autre fichier.

        errors = logLines.filter(lambda p: p.is_error())
        numLines = logLines.count()
        numErrors = errors.count()
        print 'There are', numErrors, 'errors and', numLines, 'log entries'
        errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

    Vous devez voir une sortie comme suit :

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        There are 30 errors and 646 log entries

12. Vous pouvez également utiliser **Matplotlib** pour construire une visualisation des données. Par exemple, si vous souhaitez isoler la cause de requêtes qui s’exécutent pendant une longue période, vous pouvez souhaiter rechercher les fichiers qui prennent le plus de temps à servir en moyenne.
L’extrait de code ci-dessous récupère les ressources de 25 supérieur qui a pris plus de temps pour répondre à une requête.

        def avgTimeTakenByKey(rdd):
            return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                    lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                    lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                      .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))
            
        avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

    Vous devez voir une sortie comme suit :

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [(u'/blogposts/mvc4/step13.png', 197.5),
         (u'/blogposts/mvc2/step10.jpg', 179.5),
         (u'/blogposts/extractusercontrol/step5.png', 170.0),
         (u'/blogposts/mvc4/step8.png', 159.0),
         (u'/blogposts/mvcrouting/step22.jpg', 155.0),
         (u'/blogposts/mvcrouting/step3.jpg', 152.0),
         (u'/blogposts/linqsproc1/step16.jpg', 138.75),
         (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
         (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
         (u'/blogposts/nested/step2.jpg', 126.0),
         (u'/blogposts/adminpack/step1.png', 124.0),
         (u'/BlogPosts/datalistpaging/step2.png', 118.0),
         (u'/blogposts/mvc4/step35.png', 117.0),
         (u'/blogposts/mvcrouting/step2.jpg', 116.5),
         (u'/blogposts/aboutme/basketball.jpg', 109.0),
         (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
         (u'/blogposts/mvc4/step12.png', 106.0),
         (u'/blogposts/linq8/step0.jpg', 105.5),
         (u'/blogposts/mvc2/step18.jpg', 104.0),
         (u'/blogposts/mvc2/step11.jpg', 104.0),
         (u'/blogposts/mvcrouting/step1.jpg', 104.0),
         (u'/blogposts/extractusercontrol/step1.png', 103.0),
         (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
         (u'/blogposts/mvcrouting/step21.jpg', 101.0),
         (u'/blogposts/mvc4/step1.png', 98.0)]


13. Vous pouvez également présenter ces informations sous la forme du tracé. La première étape consiste à créer un tracé, nous d’abord créer une table temporaire **AverageTime**. Le tableau a regroupe les journaux en temps pour voir si des pics de latence inhabituel à un moment donné.

        avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
        schema = StructType([StructField('Minutes', IntegerType(), True),
                             StructField('Time', FloatType(), True)])
                             
        avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
        avgTimeTakenByMinuteDF.registerTempTable('AverageTime')

14. Vous pouvez ensuite exécuter la requête SQL suivante pour obtenir tous les enregistrements dans la table **AverageTime** .

        %%sql -o averagetime
        SELECT * FROM AverageTime

    Le `%%sql` magique suivie par `-o averagetime` garantit que le résultat de la requête est conservé localement sur le serveur Jupyter (en général, le headnode du cluster). La sortie est rendu persistant sous la forme d’un dataframe de [Pandas](http://pandas.pydata.org/) avec nom spécifié **averagetime**.

    Vous devez voir une sortie comme suit :

    ![Résultat de la requête SQL] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/sql.output.png "Résultat de la requête SQL")

    Pour plus d’informations sur le `%%sql` magic, ainsi que d’autres magics disponibles avec le noyau PySpark, reportez-vous à la section [noyaux disponibles sur les ordinateurs portables de Jupyter avec HDInsight d’érosion clusters](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

15. Vous pouvez maintenant utiliser Matplotlib, une bibliothèque utilisée pour construire la visualisation de données, pour créer un tracé. Dans la mesure où le tracé doit être créé à partir de la dataframe conservé localement **averagetime** , l’extrait de code doit commencer par le `%%local` magique. Cela garantit que le code est exécuté localement sur le serveur Jupyter.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
        plt.xlabel('Time (min)')
        plt.ylabel('Average time taken for request (ms)')

    Vous devez voir une sortie comme suit :

    ![Sortie de Matplotlib] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdi-apache-spark-web-log-analysis-plot.png "Sortie de Matplotlib")

16. Lorsque vous avez terminé l’exécution de l’application, vous devez arrêter l’ordinateur portable pour libérer les ressources. Pour ce faire, dans le menu **fichier** du bloc-notes, cliquez sur **Fermer et arrêter**. Cette va s’arrêter, puis fermez le bloc-notes.
    

## <a name="seealso"></a>Voir aussi


* [Vue d’ensemble : Allumage commandé de Apache sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios

* [Allumage avec BI : effectuer l’analyse interactive des données à l’aide d’étincelle dans HDInsight avec les outils d’analyse Décisionnelle](hdinsight-apache-spark-use-bi-tools.md)

* [Allumage avec apprentissage automatique : allumage d’utilisation dans les HDInsight d’analyse de température de construction à l’aide des données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Allumage avec apprentissage automatique : allumage utilisation de HDInsight pour prédire les résultats de l’inspection alimentaires](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Diffusion en continu de l’allumage : Allumage d’utilisation dans HDInsight pour générer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

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
